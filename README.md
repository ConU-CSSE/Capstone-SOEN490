# **SOEN 490 Environment Access Guide**

This guide provides step-by-step instructions for SOEN 490 students to set up their NetBird VPN account, configure the VPN client, and connect to allocated server resources via SSH.

# **Table of Contents**

1. [Prerequisites](#prerequisites)  
2. [Step 1: Create Your NetBird Account](#step-1-create-your-netbird-account)  
3. [Step 2: Install and Configure NetBird VPN Client](#step-2-install-and-configure-netbird-vpn-client)  
4. [Step 3: Connect to Allocated Servers via SSH](#step-3-connect-to-allocated-servers-via-ssh)  
5. [Step 4: User Account Management (For Team Leads)](#step-4-user-account-management-for-team-leads)  
6. [Important Policies & Support](#important-policies--support)

# **Prerequisites**

Before starting, ensure you have:

* Access to your ENCS / student email inbox to receive the NetBird invite link.  
* A mobile device with an authenticator app (e.g., Google Authenticator, Authy, or 1Password) installed for Two-Factor Authentication (MFA).  
* An SSH client (Terminal on Linux/macOS, or PowerShell/WSL/Git Bash on Windows).

# **Step 1: Create Your NetBird Account**

1. **Open the Invite Link**: Check your email for the invitation link sent by `sysadmin`. Copy and paste the link into your web browser (e.g., `https://netbird.490.conu-csse.ca/invite?token=...`).  
2. **Set Your Password**: Enter and confirm a strong password that meets the security requirements:  
   * At least 8 characters  
   * At least 1 uppercase letter  
   * At least 1 lowercase letter  
   * At least 1 number  
   * At least 1 special character (`!@#$%^&*`)  
3. **Submit**: Click **Create Account**.  
4. **Sign In**: On the confirmation screen, click **Go to Login** and sign in using your email address and new password.  
5. **Configure Multi-Factor Authentication (MFA)**:  
   * Scan the displayed QR code using your mobile authenticator app.  
   * Enter the generated 6-digit one-time code.  
   * Click **Verify** to complete account setup.

# **Step 2: Install and Configure NetBird VPN Client**

## **2.1 Installation**

1. Navigate to the NetBird installation page at [https\://app.netbird.io/install](https://app.netbird.io/install) (or follow the post-registration download screen).  
2. Select your operating system (Linux, Windows, macOS, iOS, or Android) and follow the installation instructions.  

## **2.2 Profile Configuration**

1. Open the NetBird desktop client. *(Note: On Linux/macOS, opening the application may start the background daemon; click the NetBird icon in your system tray to display the GUI)*.  
2. Click the three-dot menu (**...**) in the upper right corner and select **Settings...**.  
3. Go to the **Profiles** tab and click **Add Profile**.  
4. Configure the profile details as follows:  
   * **Profile Name**: `SOEN-490` (or your preferred name)  
   * **Management Server**: Select **Self-hosted**  
   * **Server URL**: `https://netbird.490.conu-csse.ca:443`  
5. Click **Add Profile**, then close the Settings window.

## **2.3 Connect to the Network**

1. Verify that the newly created profile (`SOEN-490`) is selected at the top of the NetBird window.  
2. Toggle the connection switch from **Disconnected** to **Connected**.  
3. You will be redirected to your default browser for authentication:  
   * Enter your email address and password, then click **Sign in**.  
   * Enter your 6-digit MFA code from your authenticator app and click **Verify**.  
4. Once you see the **Login Successful** screen, close the browser tab.  
5. Return to the NetBird client. Under the **Resources** tab, verify that your allocated server resources (e.g., server `Case` at IP `10.128.108.21`) are listed as connected.

# **Step 3: Connect to Allocated Servers via SSH**

Once your VPN connection is established, you can SSH into your assigned server resources.

1. **Initial SSH Connection**:  
   * CSSE SysAdmins will provide an initial account (typically the team lead's ENCS username) and a temporary password.  
   * Open your terminal and run:

```
ssh <your_username>@10.128.108.21
```

     *(Replace `<your_username>` with your actual username and `10.128.108.21` with your assigned server IP)*.

   

2. **Host Fingerprint Verification**:  
   * On your first connection, you will be prompted to confirm the host key fingerprint. Type `yes` and press **Enter**.  
3. **Mandatory Password Reset**:  
   * Enter your temporary password when prompted.  
   * You will immediately be forced to set a new password:  
     * Enter the **Current password**.  
     * Enter your **New password**.  
     * Retype the **New password**.  
   * Upon successful password update, the initial connection will close. Re-run the SSH command to log in with your new password.

# **Step 4: User Account Management (For Team Leads)**

Team leads have `sudo` privileges on the assigned server and can create individual user accounts for team members.

## **Automated User Creation Script**

To quickly generate team accounts with sudo access and mandatory first-login password reset, create and run the following script on the server:

1. Create a script file named `create-user.sh`:

```
vim create-user.sh
```

2. Add the following code:

```
#!/bin/bash
# expects 3 parameters Firstname, Lastname, Username
if [ "$#" -lt 3 ] ; then echo "Usage: $0 <name> <surname> <username>"; exit 1; fi

rand_passwd=$(cat /dev/urandom | tr -dc 'a-zA-Z0-9' | fold -w 12 | head -n 1)
sudo useradd -m -d /home/$3 -s /bin/bash -c "$1 $2" $3
echo "$3:$rand_passwd" | sudo chpasswd
# uncomment line below to add account to sudoers 
# sudo usermod -aG sudo $3
echo "User $3 , new password is: $rand_passwd"
# uncomment line below to force password change the next login
# sudo passwd -e $3
````

3. Make the script executable and run it:
```bash
chmod +x create-user.sh
./create-user.sh John Doe johndoe
````

# **Important Policies & Support**

* **Usage Guidelines**: Allocated server and network resources are strictly for **SOEN 490 project purposes**. Responsible and ethical use is required at all times. Misuse may result in immediate access revocation.  
* **Support**: CSSE support is provided on a best-effort basis. For questions or technical assistance, contact the support team via email:  
  * **Email**: [tech.team.csse@concordia.ca](mailto:tech.team.csse@concordia.ca)
* **Support requests**: accepted only from team lead and only from a Concordia's email account.
