# Active Directory Project

## 🎯 Objective

This project demonstrates the setup and integration of an Active Directory domain with Splunk for centralized Windows event logging, alerting, and automated incident response using Shuffle.io SOAR. The goal is to build a security monitoring pipeline that detects suspicious logins, notifies analysts, and automates threat containment by disabling compromised user accounts.

## 📚 Skills Learned

### 🔐 Active Directory Setup and Management  
Configured and managed Active Directory Domain Services, including creating a secure domain controller and managing user accounts. This is fundamental for identity and access management (IAM) in cybersecurity, controlling authentication and authorization within a network.

### 💻 Endpoint Onboarding and Integration  
Joined Windows machines to the Active Directory domain and installed/configured Splunk Universal Forwarders. This enables centralized logging and monitoring, essential for detecting and investigating security events on endpoints.

### 🐧 Linux Server Administration and SIEM Deployment  
Installed and configured Splunk Enterprise on an Ubuntu server, gaining experience with SIEM (Security Information and Event Management) platforms which aggregate and analyze security logs to detect threats.

### 📊 Log Collection and Indexing  
Used Splunk Add-ons and created custom indexes to ingest Windows event logs, enabling detailed monitoring of security-relevant events like logons and policy changes.

### 🚨 Writing Security Alert Queries  
Developed Splunk queries to detect suspicious logon activity (e.g., EventCode 4624 with unusual IPs), an important skill for threat detection and incident response.

### 🤖 SOAR Automation with Shuffle.io  
Built automated workflows that respond to security alerts by integrating Splunk with Shuffle.io SOAR, allowing orchestration of detection, investigation, and remediation processes.

### 📡 Webhook and Notification Integration  
Configured webhooks and Slack notifications to alert security analysts in real time, improving incident awareness and accelerating response times.

### 📧 Incident Response Communication  
Set up email prompts to facilitate analyst decision-making within automated workflows, enhancing collaboration and operational efficiency during investigations.

### 🛑 Automated Threat Containment  
Programmed workflows to automatically disable compromised user accounts in Active Directory, implementing immediate containment actions to prevent further compromise.

### 🔄 Verification and Monitoring  
Built mechanisms to verify that automated actions (e.g., disabling users) were successfully applied, ensuring reliability and accountability in security operations.


##  🛠️ Tools Used

- Microsoft Windows Server (Active Directory Domain Controller)  
- Windows 10/11 Test VM  
- Ubuntu Linux (Splunk Enterprise server)  
- Splunk Enterprise & Universal Forwarder  
- Splunk Add-on for Microsoft Windows  
- Shuffle.io SOAR platform  
- Slack (for alert notifications)  
- Remote Desktop Protocol (RDP) for testing logon events  

---

## Active Directory & Splunk Integration Project - Step-by-Step

1. **Initial Active Directory Setup**  
   I logged into the VM I intended to use as the domain controller (**RIO-ADDC01**). I opened **Add Roles and Features**, added the **Active Directory Domain Services** role, and proceeded with the installation.

2. **Domain Controller Configuration**  
   After installation, I selected **Promote this server to a domain controller**, created a new forest with the root domain name **RIO.LOCAL**, entered a password, and completed the setup. Once Active Directory was installed, I confirmed the configuration by searching for and opening the **Active Directory Administrative Center**.

3. **Creating a New User**  
   I opened **Active Directory Users and Computers**, navigated to the **Users** container, and created a new user named **Thomas Shelby** with the login name **T.Shelby**.

4. **Adding a Test Machine to the Domain**  
   On my test VM (**RIO-TESTMACHINE**), I navigated to **This PC > Properties > Rename this PC (Advanced)** and added it to the **RIO** domain. I used administrator credentials to authenticate the change.

5. **Domain Join Confirmation**  
   After restarting the test machine, I verified the domain join by selecting **Other User** on the login screen. The prompt **Sign in to: RIO** confirmed successful domain integration.

6. **Splunk Setup on Ubuntu Machine**  
   I downloaded **Splunk Enterprise** on my Ubuntu Linux machine, navigated to the Splunk binary, started the service, and set a username and password. I could now access the Splunk Web Interface.

7. **Installing the Splunk Add-on for Windows**  
   From the Splunk interface, I selected **Browse More Apps**, located the **Splunk Add-on for Microsoft Windows**, and installed it.

8. **Creating a New Index**  
   I navigated to **Settings > Indexes** and created a new index named **rio-ad**.

9. **Configuring Receiving Port**  
   Under **Settings > Forwarding and Receiving**, I selected **Configure Receiving > New Receiving Port**, and set port **9997** for incoming data.

10. **Installing Splunk Universal Forwarder**  
    I installed the **Splunk Universal Forwarder** on both **RIO-ADDC01** and **RIO-TESTMACHINE** and completed the initial setup.

11. **Configuring inputs.conf for Windows Event Logs**  
    I copied the **inputs.conf** file from the default directory to the local directory in the Splunk Forwarder installation path. Using Notepad with administrative privileges, I added the following lines to the file on both machines:

    ```ini
    [WinEventLog://Security]
    index = rio-ad
    disabled = false
    ```

12. **Setting Splunk Forwarder to Run as Local System Account**  
    I opened the **Services** app with admin privileges, found **SplunkForwarder**, opened **Properties**, set it to log on as **Local System Account**, and restarted the service. This was done on both **RIO-ADDC01** and **RIO-TESTMACHINE**.

13. **Verifying Incoming Telemetry in Splunk**  
    The Splunk web interface confirmed it was receiving telemetry data. Under the field **host**, I observed two values: **RIO-ADDC01** and **RIO-TESTMACHINE**.

14. **Writing a Detection Search in Splunk**  
    I wrote a search query to detect **EventCode=4624** (successful logons) with **Logon Type 7 or 10**, excluding IP addresses starting with **125.\***. I then initiated an RDP session to **RIO-TESTMACHINE** from another computer connected via VPN to generate telemetry and verify results.

15. **Saving and Testing the Alert**  
    I saved this query as an **Alert** and navigated to **Triggered Alerts** to confirm it was working.

16. **Integrating with Shuffle.io**  
    I created a new workflow on **Shuffle.io**, added a **Webhook** trigger, copied the webhook URL, returned to Splunk Alerts, and added a webhook action using the Shuffle URL.

17. **Verifying Webhook Functionality**  
    I confirmed that the webhook was correctly receiving alerts from Splunk.

18. **Slack Notification Setup**  
    I configured the Slack app in the Shuffle workflow to send alert messages to a Slack channel named **Alerts**, containing key information such as the username and source IP address.

19. **User Input for Account Action**  
    I added a **User Input** trigger in Shuffle to send an email asking if the user account should be disabled.

20. **Automated User Disable via Active Directory**  
    I added the Active Directory app in Shuffle, authenticated it with the domain, and configured it to disable the selected user. I also added an additional Active Directory action to get user attributes for verification, along with a Repeater app to continuously check until the account was confirmed disabled. Once verified, a final Slack notification (via another Slack app) was sent to confirm user deactivation.

21. **End-to-End Workflow Test**  
    Using an attacker machine, I generated a real alert via Splunk. The Shuffle workflow successfully triggered:  
    - An analyst was notified via Slack and email.  
    - The user account **Thomas Shelby** was disabled in Active Directory (verified by the down arrow next to the username).  
    - A final Slack message confirmed the user had been disabled.

---

## Summary

This project demonstrates an end-to-end security monitoring and automated response system combining Active Directory, Splunk SIEM, and Shuffle.io SOAR. It showcases:

- Active Directory user management and domain joining.  
- Windows event log forwarding and indexing with Splunk.  
- Alerting on suspicious authentication events.  
- Automated analyst notification and decision-making via Slack and email.  
- Automatic user account disablement for threat containment.

