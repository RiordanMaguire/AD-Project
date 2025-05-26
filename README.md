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

## Project Steps

1. **Set up Domain Controller:**  
   Logged into the VM `RIO-ADDC01` intended as the Active Directory Domain Controller.  
   Added the **Active Directory Domain Services** role via "Add Roles and Features".  
   Promoted the server to a domain controller, creating a new forest with the root domain `RIO.LOCAL`.  
   Set the Directory Services Restore Mode (DSRM) password and completed the installation.  
   Verified installation by opening **Active Directory Administrative Center**.

2. **Create User in Active Directory:**  
   Opened **Active Directory Users and Computers**.  
   Navigated to the `Users` container and created a new user named **Thomas Shelby** with the logon name `T.Shelby`.

3. **Join Test Machine to Domain:**  
   On VM `RIO-TESTMACHINE`, accessed `This PC > Properties > Rename this PC (Advanced)`.  
   Changed the machine to join the domain `RIO`.  
   Entered admin credentials to authorize the change.  
   Restarted the VM and confirmed the domain join by checking the login screen shows **Sign in to: RIO**.

4. **Install and Configure Splunk Enterprise on Ubuntu:**  
   Downloaded and installed Splunk Enterprise on an Ubuntu VM.  
   Launched Splunk and configured an initial username and password.  
   Accessed the Splunk web interface.

5. **Extend Splunk for Windows Logs:**  
   Installed the **Splunk Add-on for Microsoft Windows** from Splunkbase.  
   Created a new index called `rio-ad`.  
   Enabled Splunk to receive data by configuring a new receiving port on `9997`.

6. **Install Splunk Universal Forwarder on Domain Controller and Test Machine:**  
   Installed the Universal Forwarder on both `RIO-ADDC01` and `RIO-TESTMACHINE`.  
   Configured forwarding settings on both machines.

7. **Configure Forwarder Inputs:**  
   Copied the `inputs.conf` from the default directory to the local directory on both machines.  
   Edited `inputs.conf` (with administrative privileges) to include:


8. **Run Splunk Forwarder as Local System Account:**  
Opened Windows Services on both machines.  
Set the SplunkForwarder service to log on as the **Local System Account**.  
Restarted the service.

9. **Verify Telemetry in Splunk:**  
Confirmed that Splunk was receiving logs from both `RIO-ADDC01` and `RIO-TESTMACHINE` by checking the hosts in Splunk web.

10. **Create Alert Query in Splunk:**  
 Wrote a search to detect successful logon events (EventCode=4624) with Logon types 7 or 10.  
 Filtered out events from approved IP ranges (e.g., starting with 125.*).

11. **Test Alert with External RDP Login:**  
 Connected to `RIO-TESTMACHINE` via RDP from a VPN-connected machine to generate suspicious telemetry.  
 Verified the alert triggered based on the query.

12. **Save Alert and Configure Actions:**  
 Saved the search as an alert.  
 Verified the alert appeared in the triggered alerts list.

13. **Create Shuffle.io Workflow:**  
 Created a new workflow on Shuffle.io.  
 Added a webhook trigger and copied the webhook URL.

14. **Connect Splunk Alert to Shuffle Workflow:**  
 Edited the Splunk alert to include the webhook action.  
 Pasted the Shuffle webhook URL into the alert action.

15. **Verify Webhook Reception:**  
 Confirmed Shuffle.io received the alerts triggered by Splunk.

16. **Add Slack Notification to Workflow:**  
 Configured the Slack app in Shuffle to send alert notifications to a Slack channel.  
 Notifications included useful details like username and source IP.

17. **Add User Input Trigger in Shuffle:**  
 Configured an email prompt asking the analyst whether to disable the suspicious user.

18. **Integrate Active Directory Actions:**  
 Added the Active Directory app to:  
 - Disable the flagged user account upon approval.  
 - Query user attributes to confirm the disablement.  
 Added a repeater to re-check user status.

19. **Send Final Slack Confirmation:**  
 Configured a Slack notification to confirm the user has been disabled in AD.

20. **Test Full Workflow:**  
 Triggered an alert using an attacker machine.  
 Verified Slack and email notifications.  
 Confirmed user **Thomas Shelby** was disabled (noted by the down-arrow icon in AD).  
 Confirmed final Slack message stating the user was disabled.

---

## Summary

This project demonstrates an end-to-end security monitoring and automated response system combining Active Directory, Splunk SIEM, and Shuffle.io SOAR. It showcases:

- Active Directory user management and domain joining.  
- Windows event log forwarding and indexing with Splunk.  
- Alerting on suspicious authentication events.  
- Automated analyst notification and decision-making via Slack and email.  
- Automatic user account disablement for threat containment.

