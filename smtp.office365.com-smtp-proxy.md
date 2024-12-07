# Use Stunnel to relay unencrypted smtp traffic to smtp.office365.com over TCP port 587 using STARTTLS

This setup configure stunnel to act as a local smtp relay, relaying traffic over smtp to Office365 over port 587 using STARTTLS.

The service starts automatically with the machine and does not require a logged-in user.

---

### **Step 1: Download and Install Stunnel**
1. **Download Stunnel**:
   - Visit the [Stunnel website](https://www.stunnel.org/) and download the Windows installer.
2. **Install Stunnel**:
   - Run the installer
   - Choose to install for **anyone using this computer** and stick to the defaults
   - During certificate creation, any dummy values will do (the certificate will not be used in this setup)
   - **Don't** check **Start stunnel after installation** and finish installation

---

### **Step 2: Replace the configuration file**
  1. Open Notepad with administrator privileges
  2. Open `C:\Program Files (x86)\stunnel\config\stunnel.conf`.
  3. Replace its contents with the following configuration:

```ini
; Stunnel configuration file

; Debugging and logging
debug = 7
output = stunnel.log

; Service definitions
[SMTP]
client = yes
accept = 127.0.0.1:25
connect = smtp.office365.com:587
protocol = smtp

```

   - **Explanation**:
     - `client = yes`: Indicates that Stunnel operates in client mode.
     - `accept = 127.0.0.1:25`: Stunnel listens for incoming SMTP connections on port 25.
     - `connect = smtp.office365.com:587`: Forward connections to your SMTP service on port 587 using STARTTLS.

  4. **Save and Close** the configuration file.

---

### **Step 3: Setup Stunnel as a Windows Service**
1. **Install Stunnel as a Service**:
   - Open a command prompt as an administrator.
   - Install Stunnel as a Windows service:
     ```cmd
     cd "C:\Program Files (x86)\stunnel\bin"
     stunnel -install
     ```

1. **Open the Services Management Console and locate the Stunnel service**
   - Press Win + R to open the Run dialog.
   - Type services.msc and press Enter to open the Services Manager.
   - In the Services Manager, find the service **Stunnel TLS wrapper** in the list.
   - Right-click the service and select Properties.

1. **Configure Automatic Startup**:
   - Make sure the **Startup type** is set to **Automatic**.
   - Go to the **Recovery** tab.
   - Set the following recovery options:
     - **First failure**: Restart the Service.
     - **Second failure**: Restart the Service.
     - **Subsequent failures**: Restart the Service.
   - In the **Restart service after** field, set the delay to **1 minute** (default is 1 minute; adjust if needed).
   - Confirm with **Apply**

1. **Start the Service**:
   - Go to the **General** tab
   - Choose **Start**

---

### **Step 4: Verify the Configuration**
1. **Check the Logs**:
   - Review the `C:\Program Files (x86)\stunnel\config\stunnel.log` file in the installation directory for any errors or connection issues.
     
2. **Test SMTP Relay**:
   - **Using Powershell**
```powershell
# Define SMTP server settings
$smtpServer = "127.0.0.1"
$smtpPort = 25
$smtpUser = "smtp username"
$smtpPassword = "smtp password"

# Define email details
$from = "sender@example.com"      # Replace with sender email address
$to = "recipient@example.com"     # Replace with recipient email address
$subject = "STunnel Test Email from PowerShell"
$body = "This is a test email sent through STunnel using PowerShell."

# Create the message object
$message = @{
    From       = $from
    To         = $to
    Subject    = $subject
    Body       = $body
    SmtpServer = $smtpServer
    Port       = $smtpPort
}

# Send the email
Send-MailMessage @message -Credential (New-Object PSCredential($smtpUser, (ConvertTo-SecureString $smtpPassword -AsPlainText -Force)))

```

---

### **Step 5 Configure the application**
  - Configure your application to send emails to `127.0.0.1:25` (Stunnel’s local listener).
  - Make sure the proper credentials are set.
  - Verify that emails are successfully forwarded to your SMTP provider.

---

### **Step 6 Review logs**
  - Regularly review the `C:\Program Files (x86)\stunnel\config\stunnel.log` file for operational insights or troubleshooting.
