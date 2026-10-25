*Side Note*

This took a lot of time because I realized I don't have copy and paste and that I had to configure it for that, so I was like, let me just type the entire thing, then I realized that it would take me like 20 more minutes to copy the script, so I just downloaded Guest Additions and copy and pasted the Powershell script from AI that's supposed to achieve these things


<img width="535" height="107" alt="Screenshot 2026-10-25 141847" src="https://github.com/user-attachments/assets/ba9ea8dc-f6e0-4b65-901e-73850577530e" />





## Step 1: Connects & Authenticates with Jira
It securely encodes your Atlassian account email and API Token into a Base64 HTTP Basic Authentication header.

It queries the Jira REST API v3 using a JQL (Jira Query Language) filter to search for all open tickets in your IT project where the summary contains "Onboard" and the status is not "Done".

## Step 2: Parses Jira Data & Routes Departments
Extracts the Name: It reads the ticket's Summary (e.g., "John Smith"), splits it into a first name (John) and last name (Smith), and automatically generates a standard SAM account name (jsmith) and User Principal Name (jsmith@lab.local).

Parses the Description & Routes OUs: It reads the ticket's Description looking for text like Department: Finance.

If it finds Finance, it targets OU=Finance,OU=LabUsers,DC=lab,DC=local.

If it finds IT Support, it targets OU=IT Support,OU=LabUsers,DC=lab,DC=local.

If no department is specified, it defaults safely to the parent OU=LabUsers,DC=lab,DC=local.

## Step 3: Generates Temporary Credentials
It dynamically creates a secure, random 14-character password containing uppercase, lowercase, numbers, and symbols.

It converts that password into a secure string required by Active Directory.

## Step 4: Provisions the Active Directory User
It checks if an account with that SAM account name already exists to prevent duplicate creation errors.

It executes the New-ADUser cmdlet to create the account directly inside the correct department OU, sets the secure password, enables the account, and flags it to require a password change at the next logon.

## Step 5: Closes the Loop in Jira (The Automation Feedback Loop)
Posts a Comment: It sends a formatted comment back to that specific Jira ticket containing the newly created username, UPN, target OU path, and temporary password so IT staff can securely hand it off to the new employee.

Resolves the Ticket: It queries Jira's available workflow transitions for that issue, locates the one corresponding to "Done", and automatically transitions the ticket status so it disappears from the active queue.


**This script contains variables to make it more intuitive**

$JiraDomain (e.g., your Atlassian site name)

$JiraEmail (your login email)

$ApiToken (the token you generated earlier)

$BaseParentOU (OU=LabUsers,DC=lab,DC=local)

