<p align="center">
<img src="https://github.com/user-attachments/assets/83387a1b-35c6-4c88-be82-8443f8fc1aa0" alt="activedir-logo"/>
</p>


<h1>Managing Accounts and Resetting Passwords in Active Directory</h1>
This tutorial explains how to configure accounts and group policy objects, and how to reset passwords, in Active Directory.<br />


<h2>Environments and Technologies Used 🖥️</h2>

- Microsoft Azure Compute
- Microsoft Azure Resource Groups
- Microsoft Azure Virtual Machines
- Windows App for Mac (previously Microsoft Remote Desktop)
- Active Directory Domain Services
- Active Directory Organizational Units (OUs)
- Active Directory Group Policies
- PowerShell
- Microsoft Event Viewer

<h2>Operating Systems Used 💿</h2>

- macOS Sequoia (Local)
- Windows Server 2022
- Windows 10 (21H2)

<h2>List of Prerequisites ✅</h2>
- Microsoft Account
- Microsoft Azure Subscription and Credits
- Active Directory Infrastructure in Azure
- Deployment of Active Directory Domain and Organizational Units in Azure VM
- Deployment of Active Directory Client Joined to the Domain in Azure VM
- Creation of Multiple Employee User Accounts in Active Directory Domain

<h2>High-Level Deployment and Configuration Steps 🪜</h2>

- [**Step 1 - Configure Group Policy in Active Directory to Lockout Employee User Account After 5 Failed Login Attempts** 🖥🔐🧑‍🧑‍🧒](https://github.com/cyberchris010/ad-accounts#step-1---configure-group-policy-in-active-directory-to-lockout-employee-user-account-after-5-failed-login-attempts-)
- [**Step 2 - Get Locked Out of Client VM with 6 Failed Employee User Login Attempts** 👤🔒🪟](https://github.com/cyberchris010/ad-accounts/blob/main/README.md#step-2---get-locked-out-of-client-vm-with-6-failed-employee-user-login-attempts-)
- [**Step 3 - Unlock Employee User Account and Reset its Password in Active Directory** 🖥🔐🔄](https://github.com/cyberchris010/ad-accounts/blob/main/README.md#step-3---unlock-employee-user-account-and-reset-its-password-in-active-directory-)
- [**Step 4 - Log-in to Client VM with Reset Employee User Password** 👤✅🪟](https://github.com/cyberchris010/ad-accounts/blob/main/README.md#step-4---log-in-to-client-vm-with-reset-employee-user-password-)
- [**Step 5 - Disable Employee User Account in Active Directory** 🖥🔐🚷](https://github.com/cyberchris010/ad-accounts/blob/main/README.md#step-5---disable-employee-user-account-in-active-directory-)
- [**Step 6 - Attempt Login to Client VM with Disabled Employee User** 👤🚫🪟](https://github.com/cyberchris010/ad-accounts/blob/main/README.md#step-6---attempt-login-to-client-vm-with-disabled-employee-user-)
- [**Step 7 - Re-enable Employee User Account in Active Directory** 🖥🔐🔄](https://github.com/cyberchris010/ad-accounts/blob/main/README.md#step-7---re-enable-employee-user-account-in-active-directory-)
- [**Step 8 - Log-in to Client VM with Re-enabled Employee User Account** 👤✅🪟](https://github.com/cyberchris010/ad-accounts/blob/main/README.md#step-8---log-in-to-client-vm-with-re-enabled-employee-user-account-)
- [**Step 9 - Observe Logs in the Domain Controller VM** 🖥🖥️🪵](https://github.com/cyberchris010/ad-accounts/blob/main/README.md#step-9---observe-logs-in-the-domain-controller-vm-%EF%B8%8F)
- [**Step 10 - Observe Logs in the Client VM** 🪟🖥️🪵](https://github.com/cyberchris010/ad-accounts/blob/main/README.md#step-10---observe-logs-in-the-client-vm-%EF%B8%8F)

<h2>Step 1 - Configure Group Policy in Active Directory to Lockout Employee User Account After 5 Failed Login Attempts 🖥🔐🧑‍🧑‍🧒</h2>

 - From a **local macOS system**, open the **Microsoft App (Remote Desktop)** and double-click the **Domain Controller VM** tile under **Saved PCs**.
 - Log-in to `dc-1` as `jane_admin` by entering `mydomain.com\jane_admin` for **Username** and the corresponding password.
 - Click **Continue**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/760f45fe-54f4-4b24-a91a-a62237a1ed13" alt="rdpasdomadminindc1"/>
</p>
<p>
<br />

 - When logged in to the **Domain Controller VM**, right-click the **Start** menu in the bottom left and click **Run**.
 - In the **Open:** field, enter `gpmc.msc` then **OK** to open the **Group Policy Management Console (GPMC)**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/9cc1a3ea-ca75-41eb-a516-dd4199d07d28" alt="rungpmc"/>
</p>
<p>
<br />

 - Edit the **Group Policy Object (GPO)**, **Default Domain Policy**:
   - In the **Group Policy Management** window, on the left, navigate to **Group Policy Management > Forest: mydomain.com > Domains > mydomain.com > Default Domain Policy**.
   - Right-click **Default Domain Policy** and select **Edit**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/f4285c4c-3e70-4e2b-87b0-08800125fdbe" alt="gpmceditgpo"/>
</p>
<p>
<br />

 - In the **Group Policy Management Editor**, on the left, navigate to **Default Domain Policy [DC-1.MYDOMAIN.COM] Policy > Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Account Lockout Policy** and select **Account Lockout Policy**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/5e0f3934-d0a5-4bc2-a2d3-9ed267e33a13" alt="accountlockoutpolicy"/>
</p>
<p>
<br />
 
 - On the right, double-click **Account lockout duration**.
**Account Lockout Duration** is the time in minutes that an account remains locked before it is automatically unlocked.
 - In the **Security Policy Setting** tab, check the **Define this policy setting** box, and enter `30` in the **minutes** field
 - Click **Apply** then **OK** twice.
 - Now when a user account is locked out, it will be locked out for 30 minutes before unlocking so the user can try to reset it again.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/fdcea0ab-fb70-493f-8dd8-511b93e97428" alt="accountlockoutdurationproperties"/>
</p>
<p>
<br />

 - Next, double-click **Account lockout threshold** on the right.
 - **Account Lockout Threshold** is the number of failed logon attempts that will trigger an account lockout.
 - In the **Security Policy Setting** tab, check the **Define this policy setting** box, and leave this at the **default Account lockout threshold**, which is **5 invalid logon attempts**.
 - Click **Apply**, then **OK** twice.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/160644e5-198a-4f15-98a7-061166ecd948" alt="accountlockoutthresholdprops"/>
</p>
<p>
<br />

 - The complete **Account Lockout Policy Settings** list should look like this: 
<p>
<img width="1200" src="https://github.com/user-attachments/assets/cbf72096-fa68-4d3f-b6c9-0fd0bce264da" alt="accountlockoutpolicysettingsfinished"/>
</p>
<p>
<br />

 - Verify the edited **Account Lockout Policy** of the **Default Domain Policy**:
   - In the **Group Policy Management** window, on the left, navigate to **Group Policy Management > Forest: mydomain.com > Domains > mydomain.com > Default Domain Policy** and select **Default Domain Policy**.
   - On the right, click the **Settings** tab and click **Close** when prompted by another dialog box.
   - Scroll down to **Computer Configuration (Enabled) > Policies > Windows Settings > Security Settings > Account Policies/Account Lockout Policy** and verify the policy settings in this section.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/328cda51-2110-49c7-86e4-b9fad9147751" alt="verifyaccountpoliciesaccountlockoutpolicy"/>
</p>
<p>
<br />

 - It will take 90 minutes for the **Group Policy** to propagate automatically.
 - To force an update of the **Group Policy** immediately, log-in to the **Client VM** as the **domain admin user**, `jane_admin`:
   - Open the **Microsoft App (formerly Remote Desktop)** on the **local macOS system**.
   - Double-click the **Client-1 VM** tile, enter `mydomain.com\jane_admin` for **Username**, and the corresponding password for **Password**.
   - Click **Continue**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/01abeb40-3304-4c73-8e01-eb00dad6eb7f" alt="rdpasjaneadminclientvm"/>
</p>
<p>
<br />

 - Open **Command Prompt** as **administrator** then type `gpupdate /force` and press **Enter**.
 - This forces the **Client VM** to accept the edited **Group Policy**.
 - When the **Command Prompt** displays `Computer Policy update has completed successfully. User Policy update has completed successfully.`, the **Group Policy** has been updated, and the **Client VM** will lockout any user who fails to login after more than 5 attempts.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/00dac73d-59e1-475a-b74d-0390649e1887" alt="gpupdateforce"/>
</p>
<p>
<br />

 - To further verify the **Group Policy** update, invoke the `gpresult /r` command in the **Administrator Command Prompt**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/7f57567d-4cc7-4750-a37e-13928e236037" alt="gpresultslashr"/>
</p>
<p>
<br />

<h2>Step 2 - Get Locked Out of Client VM with 6 Failed Employee User Login Attempts 👤🔒🪟</h2>

 - From the **local macOS system**, open the **Microsoft App (Remote Desktop)** and under **Saved PCs**, double-click the **Client-1 VM** tile.
 - Enter the employee username, `baf.rij`, into the **Username** field in the  **domain\username** format: `mydomain.com\baf.rij`
 - Attempt to log-in to the **Client VM** with an incorrect password at least 6 times.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/8b472ac6-20b0-4195-b491-c4804eb32d3d" alt="rdprandomuserclient"/>
</p>
<p>
<br />

 - An error **code:0xd07** message displays **Unable to connect**.
 - The edited **Group Policy** has worked.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/b6d1c490-5aa9-4c0a-826a-a3eca12b1d77" alt="unabletoconnectmsg"/>
</p>
<p>
<br />

<h2>Step 3 - Unlock Employee User Account and Reset its Password in Active Directory 🖥🔐🔄</h2>

 - Back in the **Domain Controller VM**, open **Active Directory Users and Computers** and under `mydomain.com` select the `_EMPLOYEES` **Organizational Unit (OU)**.
 - Double-click the employee user who is locked out, `baf.rij`.
 - Click the **Account** tab and check the box next to **Unlock account. This account is currently locked out on this Active Directory Domain Controller**.
 - Click **Apply** then **OK**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/cffa2d91-8f99-4753-ac6a-725ce22531fb" alt="unlockemployeeuseraccount"/>
</p>
<p>
<br />

<h2>Step 4 - Log-in to Client VM with Reset Employee User Password 👤✅🪟</h2>

 - Back on the **local macOS system**, open the **Microsoft App (Remote Desktop)** and under **Saved PCs**, double-click the **Client-1 VM** tile.
 - Enter the employee username, `baf.rij`, into the **Username** field in the  **domain\username** format: `mydomain.com\baf.rij`
 - Log-in to the **Client VM** with the correct password this time, `Password1`.
 - Click **Continue**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/d0e9b541-ef2a-4e15-8fe8-7fdaf16d1a10" alt="rdprandomuserclient"/>
</p>
<p>
<br />

 - Verify successful employee user login.
   - Open **PowerShell** and enter `whoami` in the terminal and it spits-out the current logged-in user, `mydomain\baf.rij`.
 - Log-out of the **Client VM**. 
<p>
<img width="1200" src="https://github.com/user-attachments/assets/41626e5a-c6c5-4b56-b7c7-1df1c7e5c00c" alt="clientvmuserloggedinwhoami"/>
</p>
<p>
<br />

<h2>Step 5 - Disable Employee User Account in Active Directory 🖥🔐🚷</h2>

 - Back in the **Domain Controller VM**, open **Active Directory Users and Computers** as **administrator**.
 - On the left, right-click `mydomain.com` and select **Find**.
 - In the **Find Users, Contacts, and Groups** menu, enter the employee username, `baf.rij` in the **Name** field and click **Find Now**.
 - When the username appears in the **Search results** box at the bottom, right-click it and select **Disable Account**.
 - Then click **OK**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/df7d0cc1-c38a-4c06-813e-b261bf551b08" alt="disableemployeeaccount"/>
</p>
<p>
<br />

<h2>Step 6 - Attempt Login to Client VM with Disabled Employee User 👤🚫🪟</h2>

 - From the **local macOS system**, open the **Microsoft App (Remote Desktop)** and under **Saved PCs**, double-click the **Client-1 VM** tile.
 - Enter the employee username, `baf.rij`, into the **Username** field in the  **domain\username** format: `mydomain.com\baf.rij`
 - For **Password** enter `Password1`.
 - Attempt to log-in to the **Client VM** with the **disabled** employee user account.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/d0e9b541-ef2a-4e15-8fe8-7fdaf16d1a10" alt="rdprandomuserclient"/>
</p>
<p>
<br />

 - An error **code:0xb07** message displays **Unable to connect**.
 - The edited **Group Policy** has worked.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/00721de0-71c6-48ff-994e-e5143d27421d" alt="unabletoconnectmsg2"/>
</p>
<p>
<br />

<h2>Step 7 - Re-enable Employee User Account in Active Directory 🖥🔐🔄</h2>

 - Go back to the **Domain Controller VM** and open **Active Directory Users and Computers** as **administrator**.
 - On the left, right-click `mydomain.com` and select **Find**.
 - In the **Find Users, Contacts, and Groups** menu, enter `baf.rij` into the **Name** field and click **Find Now**.
 - When the username appears in the box at the bottom, right-click it and select **Enable Account**.
 - Then click **OK**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/7b0fc30a-cc6f-4cc3-9889-d920765c6e66" alt="enableuseraccount"/>
</p>
<p>
<br />

<h2>Step 8 - Log-in to Client VM with Re-enabled Employee User Account 👤✅🪟</h2>

 - Back on the **local macOS system**, open the **Microsoft App (Remote Desktop)** and under **Saved PCs**, double-click the **Client-1 VM** tile.
 - Enter the employee username, `baf.rij`, into the **Username** field in the  **domain\username** format: `mydomain.com\baf.rij`.
 - Enter `Password1` for the **Password**
 - Attempt to log-in to the **Client VM** with the **Enabled** employee user account, `baf.rij`, and click **Continue**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/d0e9b541-ef2a-4e15-8fe8-7fdaf16d1a10" alt="rdprandomuserclient"/>
</p>
<p>
<br />

 - Verify successful employee user login.
   - Open **PowerShell** and enter `whoami` in the terminal and it spits-out the current logged-in user, `mydomain\baf.rij`.
 - Log-out of the **Client VM**. 
<p>
<img width="1200" src="https://github.com/user-attachments/assets/41626e5a-c6c5-4b56-b7c7-1df1c7e5c00c" alt="clientvmuserloggedinwhoami"/>
</p>
<p>
<br />

<h2>Step 9 - Observe Logs in the Domain Controller VM 🖥🖥️🪵</h2>

 - In the **Domain Controller VM**, enter `eventvwr.msc` into the **Search** menu in the bottom left.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/dcc6baf4-7183-4521-9344-89ee2c9348b7" alt="eventvwrmsc"/>
</p>
<p>
<br />

 - On the left, click **Windows Logs** then right-click **Security** and select **Find**.
 - In the **Find** dialog box, enter the employee username `baf.rij`, then click **Find Next** until desired entry line is found for `Audit Failure`, `Credential Validation`, and username `baf.rij`.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/89a4516b-480d-4aac-ae6c-cafa25ef3552" alt="dc-logs"/>
</p>
<p>
<br />

<h2>Step 10 - Observe Logs in the Client VM 🪟🖥️🪵</h2>

 - In the **Client VM**, use the **Search** bar in the bottom left and enter `eventvwr.msc` and **Run as administrator**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/b2f60a6e-769e-42b7-983b-5e24ea558d4e" alt="clienteventvwrmsc"/>
</p>
<p>
<br />

 - When prompted to enter the admin user's name and password, enter `mydomain.com\jane_admin` for the name and enter its corresponding password.
 - Click **Yes**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/02b49eb4-be34-42ed-851c-b9b1ab698edd" alt="admincreds"/>
</p>
<p>
<br />

 - In **Event Viewer**, click **Windows Logs** on the left, then right-click **Security** and select **Find**.
 - Enter employee user, `baf.rij` then **Find Next** until desired entry line is found for `Audit Failure`, `Logon`, and `baf.rij`.
 - 
<p>
<img width="1200" src="https://github.com/user-attachments/assets/2b31092f-646d-4b36-af5b-2e26ab7411f8" alt="clientvmeventvwrmsc"/>
</p>
<p>
<br />
 

