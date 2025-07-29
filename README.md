<p align="center">
<img src="https://github.com/user-attachments/assets/0cdb6e65-bc1d-4080-80ea-a19b9976472a" alt="activedir-logo"/>
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

- [**Step 1 - Configure a Group Policy in Active Directory to Lockout an Employee User Account After 5 Failed Login Attempts** 🖥🔐🧑‍🧑‍🧒](https://github.com/cyberchris010/ad-accounts/blob/main/README.md#step-1---configure-a-group-policy-in-active-directory-to-lockout-an-employee-user-account-after-5-failed-login-attempts-)
- [**Step 2 - Get Locked Out of the Client VM after 6 Failed Login Attempts as an Employee User** 👤🔒🪟]()
- [**Step 3 - Unlock the Employee User Account and Reset Its Password in Active Directory** 🖥🔐🔄]()
- [**Step 4 - Log in to the Client VM with the Reset Employee User Password** 👤✅🪟]()
- [**Step 5 - Disable the Employee User Account in Active Directory** 🖥🔐🚷]()
- [**Step 6 - Log in to the Client VM with the Disabled Employee User** 👤🚫🪟]()
- [**Step 7 - Re-enable the Employee User Account in Active Directory** 🖥🔐🔄]()
- [**Step 8 - Log in to the Client VM with the Re-enabled Employee User Account** 👤✅🪟]()
- [**Step 9 - Observe the Logs in the Domain Controller VM** 🖥🖥️🪵]()
- [**Step 10 - Observe the Logs in the Client VM** 🪟🖥️🪵]()

<h2>Step 1 - Configure a Group Policy in Active Directory to Lockout an Employee User Account After 5 Failed Login Attempts 🖥🔐🧑‍🧑‍🧒</h2>

 - From a **local macOS system**, open the **Microsoft App (Remote Desktop)** and double-click the **Domain Controller VM** tile under **Saved PCs**.
 - Log-in to `dc-1` as `jane_admin` by entering `mydomain.com\jane_admin` for **Username** and the corresponding password.
 - Click **Continue**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/fbb8ae3b-2f80-4dc0-95f1-8e0839793662" alt="rdpasdomadminindc1"/>
</p>
<p>
<br />

 - When logged in to the **Domain Controller VM**, right-click the **Start** menu in the bottom left and click **Run**.
 - In the **Open:** field, enter `gpmc.msc`, then **OK** to open the **Group Policy Management Console (GPMC)**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/6b7f59d7-a838-416c-96c0-657f94825b8d" alt="rungpmc"/>
</p>
<p>
<br />

 - Edit the **Group Policy Object (GPO)**, **Default Domain Policy**:
   - In the **Group Policy Management** window, on the left, navigate to **Group Policy Management > Forest: mydomain.com > Domains > mydomain.com > Default Domain Policy**.
   - Right-click **Default Domain Policy** and select **Edit**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/c4576abe-96c6-4eaa-9a37-812c54d1fea6" alt="gpmceditgpo"/>
</p>
<p>
<br />

 - In the **Group Policy Management Editor**, on the left, navigate to **Default Domain Policy [DC-1.MYDOMAIN.COM] Policy > Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Account Lockout Policy** and select **Account Lockout Policy**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/1563da7a-c3cf-4c38-916b-36324a1cd4ff" alt="accountlockoutpolicy"/>
</p>
<p>
<br />

 - On the right, double-click **Account lockout duration**.
**Account Lockout Duration** is the time in minutes that an account remains locked before it is automatically unlocked.
 - In the **Security Policy Setting** tab, check the **Define this policy setting** box, and enter `30` in the **minutes** field
 - Click **Apply**, then click **OK** twice.
 - Now, when a user account is locked out, it will be locked out for 30 minutes before unlocking, so the user can try to reset it again.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/b999f0cd-70aa-4d23-b276-b53db127b7dd" alt="accountlockoutdurationproperties"/>
</p>
<p>
<br />

 - Next, double-click **Account lockout threshold** on the right.
 - **Account Lockout Threshold** is the number of failed logon attempts that will trigger an account lockout.
 - In the **Security Policy Setting** tab, check the **Define this policy setting** box, and leave this at the **default Account lockout threshold**, which is **5 invalid logon attempts**.
 - Click **Apply**, then **OK** twice.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/fafac092-6829-4cc3-84e4-a62f4c49b101" alt="accountlockoutthresholdprops"/>
</p>
<p>
<br />

 - The complete **Account Lockout Policy Settings** list should look like this: 
<p>
<img width="1200" src="https://github.com/user-attachments/assets/e57db590-2002-4e49-87bb-2e677d0a7b83" alt="accountlockoutpolicysettingsfinished"/>
</p>
<p>
<br />

 - Verify the edited **Account Lockout Policy** of the **Default Domain Policy**:
   - In the **Group Policy Management** window, on the left, navigate to **Group Policy Management > Forest: mydomain.com > Domains > mydomain.com > Default Domain Policy** and select **Default Domain Policy**.
   - On the right, click the **Settings** tab and click **Close** when prompted by another dialog box.
   - Scroll down to **Computer Configuration (Enabled) > Policies > Windows Settings > Security Settings > Account Policies/Account Lockout Policy** and verify the policy settings in this section.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/46d8e2cd-afde-4334-ab74-2a2304e2753b" alt="verifyaccountpoliciesaccountlockoutpolicy"/>
</p>
<p>
<br />

 - It will take 90 minutes for the **Group Policy** to propagate automatically.
 - To force an update of the **Group Policy** immediately, log in to the **Client VM** as the **domain admin user**, `jane_admin`:
   - Open the **Microsoft App (formerly Remote Desktop)** on the **local macOS system**.
   - Double-click the **Client-1 VM** tile, enter `mydomain.com\jane_admin` for **Username**, and the corresponding password for **Password**.
   - Click **Continue**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/8ca40978-92d5-4bc7-a740-196cc0374564" alt="rdpasjaneadminclientvm"/>
</p>
<p>
<br />

 - Open **Command Prompt** as **administrator**, then type `gpupdate /force` and press **Enter**.
 - This forces the **Client VM** to accept the edited **Group Policy**.
 - When the **Command Prompt** displays `Computer Policy update has completed successfully. User Policy update has completed successfully`, the **Group Policy** is updated, and the **Client VM** will lock out any user who fails to log in after more than 5 attempts.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/4eac6726-244a-4fe4-b76e-aea52da5afc9" alt="gpupdateforce"/>
</p>
<p>
<br />

 - To further verify the **Group Policy** update, invoke the `gpresult /r` command in the **Administrator Command Prompt**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/4e9300d4-edab-46d5-af38-cde1665253a8" alt="gpresultslashr"/>
</p>
<p>
<br />

<h2>Step 2 -Get Locked Out of the Client VM after 6 Failed Login Attempts as an Employee User 👤🔒🪟</h2>

 - From the **local macOS system**, open the **Microsoft App (Remote Desktop)** and under **Saved PCs**, double-click the **Client-1 VM** tile.
 - Enter the employee username, `baf.rij`, into the **Username** field in the  **domain\username** format: `mydomain.com\baf.rij`
 - Attempt to log in to the **Client VM** with an incorrect password at least 6 times.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/ec9751ab-997f-4cd0-996b-cbf4ab660e20" alt="rdprandomuserclient"/>
</p>
<p>
<br />

 - An error **code:0xd07** message displays **Unable to connect**.
 - The edited **Group Policy** has worked.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/02435c9a-6fe9-4f45-8109-4602b44f61f0" alt="unabletoconnectmsg"/>
</p>
<p>
<br />

<h2>Step 3 - Unlock the Employee User Account and Reset Its Password in Active Directory 🖥🔐🔄</h2>

 - Back in the **Domain Controller VM**, open **Active Directory Users and Computers** and under `mydomain.com` select the `_EMPLOYEES` **Organizational Unit (OU)**.
 - Double-click the employee user who is locked out, `baf.rij`.
 - Click the **Account** tab and check the box next to **Unlock account. This account is currently locked out on this Active Directory Domain Controller**.
 - Click **Apply**, then click **OK**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/8b7ecea6-f662-4e76-b06f-bfef0bce5f12" alt="unlockemployeeuseraccount"/>
</p>
<p>
<br />

<h2>Step 4 - Log in to the Client VM with the Reset Employee User Password 👤✅🪟</h2>

 - Back on the **local macOS system**, open the **Microsoft App (Remote Desktop)** and under **Saved PCs**, double-click the **Client-1 VM** tile.
 - Enter the employee username, `baf.rij`, into the **Username** field in the  **domain\username** format: `mydomain.com\baf.rij`
 - Log in to the **Client VM** with the correct password this time, `Password1`.
 - Click **Continue**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/e42d29ab-5abe-4f4d-9db5-6bf90effbc07" alt="rdprandomuserclient"/>
</p>
<p>
<br />

 - Verify the successful employee user login:
   - Open **PowerShell** and enter `whoami` in the terminal.
   - It returns the current logged-in user, `mydomain\baf.rij`.
 - Log out of the **Client VM**. 
<p>
<img width="1200" src="https://github.com/user-attachments/assets/b3356b32-3f3f-4cca-a3b0-f232d2401dcf" alt="clientvmuserloggedinwhoami"/>
</p>
<p>
<br />

<h2>Step 5 - Disable the Employee User Account in Active Directory 🖥🔐🚷</h2>

 - Back in the **Domain Controller VM**, open **Active Directory Users and Computers** as an **administrator**.
 - On the left, right-click `mydomain.com` and select **Find**.
 - In the **Find Users, Contacts, and Groups** menu, enter the employee username, `baf.rij`, in the **Name** field and click **Find Now**.
 - When the username appears in the **Search results** box at the bottom, right-click it and select **Disable Account**.
 - Then click **OK**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/ca42cddb-d9fa-40fd-8c9f-8fc5a3b894fa" alt="disableemployeeaccount"/>
</p>
<p>
<br />

<h2>Step 6 - Log in to the Client VM with the Disabled Employee User 👤🚫🪟</h2>

 - From the **local macOS system**, open the **Microsoft App (Remote Desktop)** and under **Saved PCs**, double-click the **Client-1 VM** tile.
 - Enter the employee username, `baf.rij`, into the **Username** field in the  **domain\username** format: `mydomain.com\baf.rij`
 - For **Password** enter `Password1`.
 - Attempt to log in to the **Client VM** with the **disabled** employee user account.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/871575fd-b3c0-49ec-bcea-655bbdbb9f96" alt="rdprandomuserclient"/>
</p>
<p>
<br />

 - An error **code:0xb07** message displays **Unable to connect**.
 - The edited **Group Policy** has worked.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/4d004a54-e305-49d3-ad84-cf56a68e1dfb" alt="unabletoconnectmsg2"/>
</p>
<p>
<br />

<h2>Step 7 - Re-enable the Employee User Account in Active Directory 🖥🔐🔄</h2>

 - Go back to the **Domain Controller VM** and open **Active Directory Users and Computers** as an **administrator**.
 - On the left, right-click `mydomain.com` and select **Find**.
 - In the **Find Users, Contacts, and Groups** menu, enter `baf.rij` into the **Name** field and click **Find Now**.
 - When the username appears in the box at the bottom, right-click it and select **Enable Account**.
 - Then click **OK**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/03958595-f14b-4c32-908b-eb89fdc1f971" alt="enableuseraccount"/>
</p>
<p>
<br />

<h2>Step 8 - Log in to the Client VM with the Re-enabled Employee User Account 👤✅🪟</h2>

 - Back on the **local macOS system**, open the **Microsoft App (Remote Desktop)** and under **Saved PCs**, double-click the **Client-1 VM** tile.
 - Enter the employee username, `baf.rij`, into the **Username** field in the  **domain\username** format: `mydomain.com\baf.rij`.
 - Enter `Password1` for the **Password**
 - Attempt to log in to the **Client VM** with the **Enabled** employee user account, `baf.rij`, and click **Continue**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/6feabffa-efc0-48e0-8398-9e2076d8800c" alt="rdprandomuserclient"/>
</p>
<p>
<br />

 - Verify the successful employee user login:
   - Open **PowerShell** and enter `whoami` in the terminal.
   - It returns the current logged-in user, `mydomain\baf.rij`.
 - Log out of the **Client VM**. 
<p>
<img width="1200" src="https://github.com/user-attachments/assets/b1c37e8f-b158-4cf5-9e31-37076102e6ed" alt="clientvmuserloggedinwhoami"/>
</p>
<p>
<br />

<h2>Step 9 - Observe the Logs in the Domain Controller VM 🖥🖥️🪵</h2>

 - In the **Domain Controller VM**, enter `eventvwr.msc` into the **Search** menu in the bottom left.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/43488e31-bfa2-4373-99cb-dfa95f2b07fa" alt="eventvwrmsc"/>
</p>
<p>
<br />

 - On the left, click **Windows Logs**, then right-click **Security** and select **Find**.
 - In the **Find** dialog box, enter the employee username, `baf.rij`, then click **Find Next** until the desired entry line is found for `Audit Failure`, `Credential Validation`, and username, `baf.rij`.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/2361d14c-43ae-41e0-837f-87b913cf6491" alt="dc-logs"/>
</p>
<p>
<br />

<h2>Step 10 - Observe Logs in the Client VM 🪟🖥️🪵</h2>

 - In the **Client VM**, use the **Search** bar in the bottom left and enter `eventvwr.msc` and **Run as administrator**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/a23bbcb0-9fc3-4332-a8b4-c5c7204e2dcc" alt="clienteventvwrmsc"/>
</p>
<p>
<br />

 - When prompted to enter the admin user's name and password, enter `mydomain.com\jane_admin` for the name and enter its corresponding password.
 - Click **Yes**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/5878682f-ef17-46b0-99a9-7a3d28d882d9" alt="admincreds"/>
</p>
<p>
<br />

 - In **Event Viewer**, click **Windows Logs** on the left, then right-click **Security** and select **Find**.
 - Enter the employee user, `baf.rij`, then **Find Next** until the desired entry line is found for `Audit Failure`, `Logon`, and `baf.rij`.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/d28b8b7b-9c61-49e3-8acd-36e79cdacdd1" alt="clientvmeventvwrmsc"/>
</p>
<p>
<br />
 

