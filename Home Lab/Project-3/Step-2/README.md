Let's create a managed svc account!

<img width="658" height="312" alt="Screenshot 2026-10-25 130735" src="https://github.com/user-attachments/assets/447f3228-68d1-4d1d-9067-5a0dd3f15285" />

<img width="244" height="41" alt="Screenshot 2026-10-25 130848" src="https://github.com/user-attachments/assets/39c5b5bb-70a5-482d-b26a-3c702934719c" />



**Principle of Least Privilege; Delegate control ONLY WHERE NECESSARY - ONBOARDER**

*create, delete, manage user accts, reset passwords on user accts ONLY UNDERNEATH LabUsers OU* instead of *user under a security group with wide range of permissions*


But actually, I should redo the svc account, but not in that OU specifically because that is reserved for special MSA/gMSA objects to be referred to within Powershell scripts

<img width="159" height="35" alt="Screenshot 2026-10-25 132134" src="https://github.com/user-attachments/assets/144129b2-3a9b-4b47-8229-5a743412c061" />
<img width="378" height="115" alt="Screenshot 2026-10-25 132416" src="https://github.com/user-attachments/assets/d191b90d-8c20-4ee7-83b8-dcca20e7e232" />


Delegation done!
