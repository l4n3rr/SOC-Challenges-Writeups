This is the PowerShell Automation Script Template

# ==============================================================================
# CONFIGURATION
# ==============================================================================
$JiraDomain    = "your-subdomain"         # e.g., "mycompany" from mycompany.atlassian.net
$JiraEmail     = "your-email@example.com"
$ApiToken      = "YOUR_SAVED_API_TOKEN"   # Paste your Atlassian API Token here
$BaseParentOU  = "OU=LabUsers,DC=lab,DC=local" # Your parent OU
$UPNDomain     = "lab.local"              # Your Active Directory domain suffix

# ==============================================================================
# AUTHENTICATION & REST API HEADERS
# ==============================================================================
$Pair = "$($JiraEmail):$($ApiToken)"
$Bytes = [System.Text.Encoding]::ASCII.GetBytes($Pair)
$Base64 = [Convert]::ToBase64String($Bytes)
$Headers = @{
    "Authorization" = "Basic $Base64"
    "Content-Type"  = "application/json"
    "Accept"        = "application/json"
}

# JQL search for open Onboard tickets not marked as Done
$JqlQuery = 'project = "IT" AND status != "Done" AND summary ~ "Onboard"'
$SearchUrl = "https://$JiraDomain.atlassian.net/rest/api/3/search?jql=$([uri]::EscapeDataString($JqlQuery))"

# Helper function to extract plain text from Jira API v3 Atlassian Document Format (ADF)
function Get-JiraText {
    param($Field)
    if (-not $Field -or -not $Field.content) { return "" }
    $texts = foreach ($block in $Field.content) {
        foreach ($sub in $block.content) { if ($sub.text) { $sub.text } }
    }
    return ($texts -join " ")
}

# ==============================================================================
# FETCH TICKETS
# ==============================================================================
try {
    $Response = Invoke-RestMethod -Uri $SearchUrl -Method Get -Headers $Headers
    $Tickets  = $Response.issues
} catch {
    Write-Error "Failed to connect to Jira API: $_"
    exit
}

if (-not $Tickets -or $Tickets.Count -eq 0) {
    Write-Host "No pending onboarding tickets found." -ForegroundColor Yellow
    exit
}

foreach ($Ticket in $Tickets) {
    $IssueKey = $Ticket.key
    $FullName = $Ticket.fields.summary.Trim()
    $RawDesc  = Get-JiraText -Field $Ticket.fields.description

    Write-Host "Processing ticket $IssueKey for $FullName..." -ForegroundColor Cyan

    # 1. Parse Name
    $NameParts = $FullName -split '\s+'
    if ($NameParts.Count -lt 2) {
        Write-Warning "Skipping $IssueKey: Summary '$FullName' does not contain a full name (First Last)."
        continue
    }

    $GivenName      = $NameParts[0]
    $Surname        = $NameParts[-1]
    $SamAccountName = ($GivenName.Substring(0,1) + $Surname).ToLower()
    $UserPrincipal  = "$SamAccountName@$UPNDomain"

    # 2. Dynamic Department & OU Routing
    $TargetOU = $BaseParentOU
    if ($RawDesc -match "Department:\s*([^\r\n]+)") {
        $Department = $Matches[1].Trim()
        Write-Host "Detected Department: $Department" -ForegroundColor DarkCyan
        
        # Route to child OU if it matches your lab structure
        if ($Department -match "Finance") {
            $TargetOU = "OU=Finance,$BaseParentOU"
        } elseif ($Department -match "IT Support" -or $Department -match "Information Technology") {
            $TargetOU = "OU=IT Support,$BaseParentOU"
        }
    }

    # 3. Generate Secure Random Password
    $TempPassword   = -join ((33..126) | Get-Random -Count 14 | ForEach-Object {[char]$_})
    $SecurePassword = ConvertTo-SecureString $TempPassword -AsPlainText -Force

    # 4. Create Active Directory Account
    try {
        if (Get-ADUser -Filter "SamAccountName -eq '$SamAccountName'" -ErrorAction SilentlyContinue) {
            Write-Warning "User $SamAccountName already exists in Active Directory."
            continue
        }

        New-ADUser -Name "$GivenName $Surname" `
                   -GivenName $GivenName `
                   -Surname $Surname `
                   -SamAccountName $SamAccountName `
                   -UserPrincipalName $UserPrincipal `
                   -Path $TargetOU `
                   -AccountPassword $SecurePassword `
                   -Enabled $true `
                   -ChangePasswordAtLogon $true `
                   -ErrorAction Stop

        Write-Host "Successfully created AD user $SamAccountName in OU: $TargetOU" -ForegroundColor Green

        # 5. Post Comment Back to Jira (Phase 4)
        $CommentBody = @{
            body = @{
                type = "doc"
                version = 1
                content = @(
                    @{
                        type = "paragraph"
                        content = @(
                            @{
                                type = "text"
                                text = "Automated AD Account Provisioned Successfully:`n- Username: $SamAccountName`n- UPN: $UserPrincipal`n- OU Path: $TargetOU`n- Temp Password: $TempPassword"
                            }
                        )
                    }
                )
            }
        } | ConvertTo-Json -Depth 5

        $CommentUrl = "https://$JiraDomain.atlassian.net/rest/api/3/issue/$IssueKey/comment"
        Invoke-RestMethod -Uri $CommentUrl -Method Post -Headers $Headers -Body $CommentBody | Out-Null

        # 6. Transition Ticket to Done (Phase 4)
        $TransUrl = "https://$JiraDomain.atlassian.net/rest/api/3/issue/$IssueKey/transitions"
        $Transitions = Invoke-RestMethod -Uri $TransUrl -Method Get -Headers $Headers
        $DoneTransition = $Transitions.transitions | Where-Object { $_.name -like "*Done*" -or $_.name -like "*Close*" -or $_.name -like "*Resolve*" } | Select-Object -First 1

        if ($DoneTransition) {
            $TransitionPayload = @{
                transition = @{ id = $DoneTransition.id }
            } | ConvertTo-Json -Depth 3

            Invoke-RestMethod -Uri $TransUrl -Method Post -Headers $Headers -Body $TransitionPayload | Out-Null
            Write-Host "Transitioned ticket $IssueKey to $($DoneTransition.name)." -ForegroundColor Green
        }

    } catch {
        Write-Error "Error provisioning user for ticket $IssueKey: $_"
    }
}


