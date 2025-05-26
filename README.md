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
   I logged into the VM I intended to use as the domain controller (**RIO-ADDC01**). I opened **Add Roles and Features**, added the **Active Directory Domain Services** role, and proceeded with the installation.![image](https://github.com/user-attachments/assets/14abbba7-70ec-4c9a-9d59-e527276e557e)![image](https://github.com/user-attachments/assets/004ad59f-cdfa-433d-b489-bffdea220386)![image](https://github.com/user-attachments/assets/a82a4a0e-6ca7-4fd6-bbda-fac24b91d079)




2. **Domain Controller Configuration**  
   After installation, I selected **Promote this server to a domain controller**, created a new forest with the root domain name **RIO.LOCAL**, entered a password, and completed the setup. Once Active Directory was installed, I confirmed the configuration by searching for and opening the **Active Directory Administrative Center**.![image](https://github.com/user-attachments/assets/f247bdcf-0e92-4f58-bfc9-be574a815100)![image](https://github.com/user-attachments/assets/a88dc51c-5574-4841-848b-1b8af75d4df0)![image](https://github.com/user-attachments/assets/bb617382-b06c-4c6c-9957-2df5bd45b83d)![image](https://github.com/user-attachments/assets/302ef272-71ce-4bca-89e7-5f400ac06b95)





3. **Creating a New User**  
   I opened **Active Directory Users and Computers**, navigated to the **Users** container, and created a new user named **Thomas Shelby** with the login name **T.Shelby**.![image](https://github.com/user-attachments/assets/a7946e58-ba45-48aa-b724-5d28e389bb73)![image](https://github.com/user-attachments/assets/523459bc-7554-4055-84a6-bfc9b759264d)![image](https://github.com/user-attachments/assets/088be47c-1094-40c6-8563-32f78abebf0d)




4. **Adding a Test Machine to the Domain**  
   On my test VM (**RIO-TESTMACHINE**), I navigated to **This PC > Properties > Rename this PC (Advanced)** and added it to the **RIO** domain. I used administrator credentials to authenticate the change.![image](https://github.com/user-attachments/assets/bb77de00-ec4d-4fbb-8241-22833245fa67)![image](https://github.com/user-attachments/assets/b829a587-909f-49f2-98c1-abfd0b69ffd5)![image](https://github.com/user-attachments/assets/415ef44b-9778-437d-8f05-af3c721fa7dd)![image](https://github.com/user-attachments/assets/8dca4362-fc3e-4744-a6c3-b40f5b270c9b)





5. **Domain Join Confirmation**  
   After restarting the test machine, I verified the domain join by selecting **Other User** on the login screen. The prompt **Sign in to: RIO** confirmed successful domain integration.![image](https://github.com/user-attachments/assets/f40a2d8f-6ea4-4221-bd95-54dffa0bfe16)


6. **Splunk Setup on Ubuntu Machine**  
   I downloaded **Splunk Enterprise** on my Ubuntu Linux machine, navigated to the Splunk binary, started the service, and set a username and password. I could now access the Splunk Web Interface.![image](https://github.com/user-attachments/assets/d7e594bb-7ddf-4c61-af90-8bb376162dfa)![image](https://github.com/user-attachments/assets/30555ccf-41c5-4c24-8c2c-edc73c0362e6)![image](https://github.com/user-attachments/assets/bd4a9735-a7c2-4c44-b9a4-454de6eb6751)




7. **Installing the Splunk Add-on for Windows**  
   From the Splunk interface, I selected **Browse More Apps**, located the **Splunk Add-on for Microsoft Windows**, and installed it.![image](https://github.com/user-attachments/assets/79610361-72c4-466c-abd7-148b7e04d813)![image](https://github.com/user-attachments/assets/641cf507-8106-40b4-9a04-70a9d4ef7e8c)



8. **Creating a New Index**  
   I navigated to **Settings > Indexes** and created a new index named **rio-ad**.![image](https://github.com/user-attachments/assets/afd34fdd-ba50-4bea-aa92-d6b35b43978c)


9. **Configuring Receiving Port**  
   Under **Settings > Forwarding and Receiving**, I selected **Configure Receiving > New Receiving Port**, and set port **9997** for incoming data.![image](https://github.com/user-attachments/assets/077d3174-d01a-44e4-b8cf-8490801ef28a)


10. **Installing Splunk Universal Forwarder**  
    I installed the **Splunk Universal Forwarder** on both **RIO-ADDC01** and **RIO-TESTMACHINE** and completed the initial setup.![image](https://github.com/user-attachments/assets/a1bd29cf-b46a-4c2e-a69a-9d3427155d69)![image](https://github.com/user-attachments/assets/040c931c-0c0e-4c92-a435-1acaf08cc534)![image](https://github.com/user-attachments/assets/f957f2a9-5eaa-47cf-ae80-8a59212cca1c)![image](https://github.com/user-attachments/assets/f7c06b79-60bc-4458-81fc-cb314ec5b17b)





11. **Configuring inputs.conf for Windows Event Logs**  
    I copied the **inputs.conf** file from the default directory to the local directory in the Splunk Forwarder installation path. Using Notepad with administrative privileges, I added the following lines to the file on both machines:

    ```ini
    [WinEventLog://Security]
    index = rio-ad
    disabled = false
    ```
    ![image](https://github.com/user-attachments/assets/33fc540f-f850-404c-a989-2f029e2cd606)![image](https://github.com/user-attachments/assets/fc9c0822-ac17-459f-a6b4-a90325c75b5d)![image](https://github.com/user-attachments/assets/7756a8a0-b7d4-4bb9-ac36-185e5deb30df)![image](https://github.com/user-attachments/assets/2704ca4c-b891-42e8-9ff6-b0826cb83c44)






12. **Setting Splunk Forwarder to Run as Local System Account**  
    I opened the **Services** app with admin privileges, found **SplunkForwarder**, opened **Properties**, set it to log on as **Local System Account**, and restarted the service. This was done on both **RIO-ADDC01** and **RIO-TESTMACHINE**.![image](https://github.com/user-attachments/assets/aa8742be-e94d-4e16-bdbb-9bf23c72bba8)![image](https://github.com/user-attachments/assets/23b2a3cc-2f07-452b-9c4e-807bdc1061e2)



13. **Verifying Incoming Telemetry in Splunk**  
    The Splunk web interface confirmed it was receiving telemetry data. Under the field **host**, I observed two values: **RIO-ADDC01** and **RIO-TESTMACHINE**.![image](https://github.com/user-attachments/assets/62162146-ffc9-4ea5-ba06-14871e30f62f)![image](https://github.com/user-attachments/assets/c5e0f293-641f-4c49-86a8-daf96820068a)



14. **Writing a Detection Search in Splunk**  
    I wrote a search query to detect **EventCode=4624** (successful logons) with **Logon Type 7 or 10**, excluding IP addresses starting with **125.\***. I then initiated an RDP session to **RIO-TESTMACHINE** from another computer connected via VPN to generate telemetry and verify results.![image](https://github.com/user-attachments/assets/1d2ab809-2733-4e7e-8402-4d378dd512bd)![image](https://github.com/user-attachments/assets/a34dbaa5-299a-48b0-9d32-8cdffc3acec1)![image](https://github.com/user-attachments/assets/a923361e-d284-498b-8c34-be69d3b3315c)




15. **Saving and Testing the Alert**  
    I saved this query as an **Alert** and navigated to **Triggered Alerts** to confirm it was working.![image](https://github.com/user-attachments/assets/74ed4a72-c169-4fba-b188-96c91031b888)![image](https://github.com/user-attachments/assets/15ad1cac-313c-416f-a6c6-6f70653f1b8d)



16. **Integrating with Shuffle.io**  
    I created a new workflow on **Shuffle.io**, added a **Webhook** trigger, copied the webhook URL, returned to Splunk Alerts, and added a webhook action using the Shuffle URL.![image](https://github.com/user-attachments/assets/27f4fdc3-0dfa-4026-b50f-4ecb5bf15541)![image](https://github.com/user-attachments/assets/aac81755-2e38-4526-bd6a-f43f0dbc64f6)



17. **Verifying Webhook Functionality**  
    I confirmed that the webhook was correctly receiving alerts from Splunk.![image](https://github.com/user-attachments/assets/6f76d1f4-37ba-44bc-9536-53e70aa48072)![image](https://github.com/user-attachments/assets/58557901-5ac1-491d-9131-d264d926d9bc)



18. **Slack Notification Setup**  
    I configured the Slack app in the Shuffle workflow to send alert messages to a Slack channel named **Alerts**, containing key information such as the username and source IP address.![image](https://github.com/user-attachments/assets/e10a3586-9548-4151-adc7-6e14cd5350f4)![image](https://github.com/user-attachments/assets/59a7fad9-8ba7-418d-8bcf-dab91f6f4a44)



19. **User Input for Account Action**  
    I added a **User Input** trigger in Shuffle to send an email asking if the user account should be disabled.![image](https://github.com/user-attachments/assets/1fbbba40-1124-45a2-9ccf-bfba3d1b4cd0)![image](https://github.com/user-attachments/assets/93963995-2c56-4274-9831-e5635fdd2c9e)



20. **Automated User Disable via Active Directory**  
    I added the Active Directory app in Shuffle, authenticated it with the domain, and configured it to disable the selected user. I also added an additional Active Directory action to get user attributes for verification, along with a Repeater app to continuously check until the account was confirmed disabled. Once verified, a final Slack notification (via another Slack app) was sent to confirm user deactivation.![image](https://github.com/user-attachments/assets/48d8a447-0dce-4073-bd43-94de5986590d)
![image](https://github.com/user-attachments/assets/8f11fe90-819a-4b7d-87f3-e9e54e00c85f)


21. **End-to-End Workflow Test**  
    Using an attacker machine, I generated a real alert via Splunk. The Shuffle workflow successfully triggered:  
    - An analyst was notified via Slack and email.  
    - The user account **Thomas Shelby** was disabled in Active Directory (verified by the down arrow next to the username).  
    - A final Slack message confirmed the user had been disabled.![image](https://github.com/user-attachments/assets/c3d06a72-61ed-4e1d-b969-25294a6b21de)![image](https://github.com/user-attachments/assets/16574a11-adab-4b98-bb7e-e24b2c1c59e0)![image](https://github.com/user-attachments/assets/9c5f4805-83a8-44e7-85e0-1e0235b2cc09)![image](https://github.com/user-attachments/assets/836a7b71-ac8d-43d6-8f09-2a4f7d3a6e4a)





---

## Summary

This project demonstrates an end-to-end security monitoring and automated response system combining Active Directory, Splunk SIEM, and Shuffle.io SOAR. It showcases:

- Active Directory user management and domain joining.  
- Windows event log forwarding and indexing with Splunk.  
- Alerting on suspicious authentication events.  
- Automated analyst notification and decision-making via Slack and email.  
- Automatic user account disablement for threat containment.

