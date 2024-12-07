# Stunnel O365 smtp proxy | 127.0.0.1:25/Unencrypted -> smtp.office365.com:587/STARTTLS

This setup ensures Stunnel securely relays SMTP traffic to your provider with STARTTLS. The service starts automatically with the server and does not require a logged-in user.

---

### **Step 1: Download and Install Stunnel**
1. **Download Stunnel**:
   - Visit the [Stunnel website](https://www.stunnel.org/) and download the Windows installer.
2. **Install Stunnel**:
   - Run the installer and follow the prompts. Use dummy values for certificate generation.
   - By default, Stunnel installs to `C:\Program Files (x86)\stunnel`.

---

### **Step 2: Create the Configuration File**
1. **Edit the `stunnel.conf` File**:
   - Open Notepad with administrator privileges
   - Open `C:\Program Files (x86)\stunnel\config\stunnel.conf`.
   - Replace its contents with the following configuration:

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

3. **Save and Close** the configuration file.

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
   - In the Services Manager, find the stunnel service in the list.
   - Right-click the service and select Properties.

1. **Configure Automatic Startup**:
   - In the Services Manager, ensure Stunnel's startup type is set to **Automatic**: 
     - Right-click the `stunnel` service, choose `Properties`, and set **Startup type** to **Automatic**.
   - Go to the Recovery tab.
   - Set the following recovery options:
     - **First failure**: Restart the Service.
     - **Second failure**: Restart the Service.
     - **Subsequent failures**: Restart the Service.
   - In the **Restart service after** field, set the delay to **1 minute** (default is 1 minute; adjust if needed).

1. **Start the Service**:
   - From a command line with administrator privileges run:
     ```cmd
     net start stunnel
     ```


---

### **Step 4: Verify the Configuration**
1. **Check the Logs**:
   - Review the `stunnel.log` file in the installation directory for any errors or connection issues.
2. **Test SMTP Relay**:
   - Configure your application to send emails to `127.0.0.1:25` (Stunnel’s local listener).
   - Verify that emails are successfully forwarded to your SMTP provider.

---


 **Logs**: Regularly review the `stunnel.log` file for operational insights or troubleshooting.

---

