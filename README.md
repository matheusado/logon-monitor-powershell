<h1>Windows Logon Monitoring PowerShell</h1>


<h2>Description</h2>
This project implements a comprehensive Windows auditing and monitoring solution that leverages Group Policy, Event Viewer, and custom PowerShell automation to track critical security events. It focuses on three key areas: successful logons (Event ID 4624), failed logons (Event IDs 4625 and 4776), and object/file access (Event ID 4663). The setup process enables advanced audit policies, configures event logging for both successes and failures, and applies targeted folder auditing for sensitive data. Custom PowerShell scripts then parse these logs, extract meaningful details (such as usernames, timestamps, IP addresses, accessed objects, and process names), and generate HTML reports for easy review in a browser. The solution can be run manually or scheduled via Task Scheduler for automated, recurring reports—helping administrators quickly detect unauthorized access attempts, investigate suspicious activity, and maintain a proactive security posture on Windows 11 Pro or Windows Server systems.
<br />


<h2>Languages and Utilities Used</h2>

- PowerShell
- HTML (for automated reports)
- Event Viewer (monitoring logon and event activity)
- Group Policy Management Editor (advanced audit policies)
- PowerShell scripts (automation of monitoring and reporting)
- Task Scheduler (optional for automation trigger)


<h2>Environments Used </h2>

- Windows 11 Pro or Windows Server (test environment)
- Local Administrative Access (for enabling policies and audit settings)
- PowerShell Console or ISE
- Web browser (for viewing HTML reports)


<h2>Step-by-Step Guide:</h2>


Configure Audit Policies for Logon Failure Analysis.

In the Start bar type “local group policy editor” and click on Edit group policy:<br/>
<img src="https://i.imgur.com/QAcGUEt.png"/>
<br />
<br />
Once the Local group policy editor is open navigate to Computer Configuration → Windows Settings → Security Settings → Local Policies → Audit Policy and double-click on Audit logon events: <br/>
<img src="https://i.imgur.com/YCZlp1C.png"/>
<br />
<br />
In the “Audit logon events Properties” window check both boxes “Success” and “Failure”, click Apply then click Ok:<br/>
<img src="https://i.imgur.com/qw5Huot.png"/>
<br />
<br />
Open Event Viewer: <br/>
<img src="https://i.imgur.com/cRrRh3A.png"/>
<br />
<br />
In Event Viewer navigate to Event Viewer Local → Windows Logs → Security:<br/>
<img src="https://i.imgur.com/lkotWSP.png"/>
<br />
<br />
On the right-side panel, click “Filter Current Log…” and in the popup window find the “Includes/Excludes Events IDs” field and type 4625 for Failed logon Attempts then click Ok:<br/>
<img src="https://i.imgur.com/nXZzrc5.png"/>
<br />
<br />
All the Failed logon attempts (Event ID 4625) will be filtered (for those events to show in your screen you need to log out your Windows and try to log in with a wrong password):<br/>
<img src="https://i.imgur.com/xNPzyhx.png"/>

Note: Repeat these steps for different types of events you want to filter.
<br />
<br />
Enable Command Logging. Start by opening Group Policy Editor navigate to Computer Configuration → Administrative Templates → System → Audit Process Creation then double-click Include command line in process creation events: <br/>
<img src="https://i.imgur.com/tkyQYE4.png"/>
<br />
<br />
In the window that opens, select Enable, click Apply then click Ok:  <br/>
<img src="https://i.imgur.com/mI48Ztn.png"/> 

Note: This step enables Windows to log the full command line of every process that is created on the system. By activating the “Include command line in process creation events” policy, administrators ensure that when a new process is started, the exact command used (including any arguments or scripts) is recorded in the event logs. This is especially useful for security auditing and threat detection, as it allows visibility into potentially malicious or suspicious commands executed by users or malware.
<br />
<br />
Navigate to Computer Configuration → Windows Settings → Security Settings → Advanced Audit Policy Configuration → System Audit Policies → Detailed Tracking then double-click Audit Process Creation:<br/>
<img src="https://i.imgur.com/VISuAap.png"/>
<br />
<br />
In the window that opens, check both Success and Failure, click Apply then click Ok:  <br/>
<img src="https://i.imgur.com/bdggTIQ.png"/>

Note: This step enables detailed auditing of process creation events in Windows by configuring the system to log both successful and failed attempts to start a process. When enabled, every time a program or script is executed, Windows will generate an event log entry (Event ID 4688) that includes valuable details such as the full command line used – thanks to the setting enabled in the previous step. This is essential for security monitoring and forensic analysis, as it allows administrators to track exactly what commands were run on the system, detect suspicious activity, and investigate potential threats or misconfigurations.
<br />
<br />
Track Successful Logons. Navigate to Computer Configuration → Windows Settings → Security Settings → Advanced Audit Policy Configuration → System Audit Policies → Logon/Logoff then double-click Audit Logon: <br/>
<img src="https://i.imgur.com/7YP7gxf.png"/>
<br />
<br />
In the window that opens check Success, Failure, click Apply then click Ok: <br/>
<img src="https://i.imgur.com/8Wch6fi.png"/>

Note: This step enables auditing of user logon events by configuring Windows to log both successful and failed logon attempts. By activating the “Audit Logon” policy and selecting both “Success” and “Failure”, the system will generate event logs each time a user attempts to log in – whether the attempt was successful (Event ID 4624) or failed (Event ID 4625). This is essential for tracking user activity, identifying unauthorized access attempts, and supporting security investigations by providing clear records of who logged into the system and when.
<br />
<br />
Open Local Security Policy: <br/>
<img src="https://i.imgur.com/exe6Ydn.png"/>
<br />
<br />
Navigate to Advanced Audit Policy Configuration → System Audit Policies → Object Access then double-click Audit File System: <br/>
<img src="https://i.imgur.com/q3EKoYS.png"/>
<br />
<br />
Enable “Configure the Following audit events”, “Success” and “Failure” then click Ok: <br/>
<img src="https://i.imgur.com/1R70sSh.png"/>

Note: This step enables file system auditing in Windows by configuring the "Audit File System" policy under Object Access in the Local Security Policy. By selecting “Success” and “Failure,” Windows is instructed to log both successful and failed attempts to access files or folders. This is essential for generating Event ID 4663, which records detailed information about object access, such as which user attempted to open, modify, or delete a file. It forms the foundation for monitoring unauthorized access or suspicious activity on sensitive directories, enabling you to track these actions through audit logs and security reports.
<br />
<br />
Enable Auditing on a Specific Folder. Right-click a folder then select Properties: <br/>
<img src="https://i.imgur.com/A95tDPs.png"/>
<br />
<br />
In the window that opens go to Security then Advanced: <br/>
<img src="https://i.imgur.com/XOJTyka.png"/>
<br />
<br />
Select Auditing then click Add: <br/>
<img src="https://i.imgur.com/ZAFxxR8.png"/>
<br />
<br />
Click Select Principal: <br/>
<img src="https://i.imgur.com/lDUXTSb.png"/>
<br />
<br />
In the text box, type “Everyone” and click “Check Names” to validate the input. If “Everyone” becomes underlined, it’s valid. Then click Ok to confirm: <br/>
<img src="https://i.imgur.com/MwOpDeL.png"/>
<img src="https://i.imgur.com/fZaoPhu.png"/>
<br />
<br />
Create PowerShell Script to Parse Logon Failures. Start by opening the Notepad and paste the following Script then save it as LogonFailuresMonitor.ps1: <br/>
<img src="https://i.imgur.com/o6EzI7Q.png"/>
<img src="https://i.imgur.com/wsLsBkY.png"/>

Note: This PowerShell script retrieves the 50 most recent failed logon events (Windows Security Event ID 4625) from the system’s Security log, extracts key details such as the event time, username, and source IP address, formats this data into an HTML table, and saves the output as a report file (FailedLogons_Raw.html) inside the C:\SecurityReports folder, allowing for easy viewing of failed login attempts in a web browser.
<br />
<br />
Now Create a PowerShell Script to Parse Successful Logons. Start by opening the Notepad and paste the following Script then save it as SuccessLogonsMonitor.ps1: <br/>
<img src="https://i.imgur.com/y4GHkfX.png"/>
<img src="https://i.imgur.com/av0f8BB.png"/>

Note: This PowerShell script retrieves the 50 most recent successful logon events (Windows Security Event ID 4624) from the system’s Security log, extracts details such as the logon time, target username, IP address or workstation name, and logon type, and organizes this information into an HTML table. If the IP address is missing (common for local logons), it substitutes the workstation name instead. The formatted report is then saved as SuccessfulLogons_raw.html in the C:\SecurityReports folder, allowing the user to easily view recent successful login activity in a web browser.
<br />
<br />
Create PowerShell Script to Parse Object Access. Start by opening the Notepad and paste the following Script then save it as ObjectAccessMonitor.ps1: <br/>
<img src="https://i.imgur.com/9NnUTGQ.png"/>
<img src="https://i.imgur.com/wTNNemU.png"/>

Note: This PowerShell script collects the 50 most recent Object Access events (Windows Security Event ID 4663) from the system’s Security log, extracts details such as the event time, the user who performed the action, the object name, the process involved, and the type of access requested or performed. It uses XML parsing to reliably pull these fields by name, ensuring accuracy even if event data ordering changes. The gathered information is then formatted into an HTML table and saved as ObjectAccessReport_Raw.html in the C:\SecurityReports folder, producing a readable report of recent file or object access activity for review in a web browser.
<br />
<br />
Create the Output Folder. Before running the script, make sure the output folder exists, open the File Explorer, go to C:\ and create a new folder called SecurityReports: <br/>
<img src="https://i.imgur.com/hcIk3QI.png"/>
<br />
<br />
Manual Execution Steps for LogonFailuresMonitor.ps1. Open PowerShell as Administrator: <br/>
<img src="https://i.imgur.com/QCLYfKL.png"/>
<br />
<br />
Run the Scripts with the following commands (in this case the Script is saved on the directory C:\Scripts, replace the path with the exact location of your .ps1 file if it’s different):

powershell -ExecutionPolicy Bypass -File "C:\Scripts\LogonFailuresMonitor.ps1"

powershell -ExecutionPolicy Bypass -File "C:\Scripts\SuccessLogonsMonitor.ps1"

powershell -ExecutionPolicy Bypass -File "C:\Scripts\ObjectAccessMonitor.ps1"

<img src="https://i.imgur.com/8c8GSit.png"/>
<br />
<br />
Once you run the commands it will save the results as HTML files in (C:\SecurityReports):  <br/>
<img src="https://i.imgur.com/1wtZXzD.png"/>
<br />
<br />
Automate the Script Execution (Optional). Open the Task Scheduler app as administrator: <br/>
<img src="https://i.imgur.com/LqFUbOH.png"/>
<br />
<br />
In the right panel click Create Basic Task: <br/>
<img src="https://i.imgur.com/SDtx3ex.png"/>
<br />
<br />
Give it a name, for example LogonMonitorAutomation, then click Next:  <br/>
<img src="https://i.imgur.com/iMefAqW.png"/>
<br />
<br />
Choose Trigger. In this case Daily will be selected (the Script will run once a day) then click Next:  <br/>
<img src="https://i.imgur.com/Ddq3NUS.png"/>
<br />
<br />
Leave the setting as-is or adjust the time if you want. Recur every “1” days is fine (runs every day), then click Next:  <br/>
<img src="https://i.imgur.com/kgL4nEa.png"/>
<br />
<br />
Select Start a Program and click Next:  <br/>
<img src="https://i.imgur.com/OjvEhX7.png"/>
<br />
<br />
In the Program/script field type:

powershell.exe

In Add Arguments (optional), paste this:

-ExecutionPolicy Bypass -File "C:\Scripts\LogonFailuresMonitor.ps1"

Click Next: <br />
<img src="https://i.imgur.com/ukrFAGA.png"/>
<br />
<br />
Check the box “Open the Properties dialog for this task when I click Finish” then click Finish: <br />
<img src="https://i.imgur.com/pBF0jEX.png"/>
<br />
<br />
In the Properties window that opens check “Run with highest privilegies”. Under “Configure for:” Windows 10 is selected since it’s the most compatible to Windows 11 system that’s being used in this case. 

You can select “Run whether user is logged on or not”, this is optional. Select this option if you want the script to run even when you’re not logged in. You’ll be prompted to enter your Windows password. 

Click Ok to save it: <br />
<img src="https://i.imgur.com/fsGqhAe.png"/>
<br />
<br />
Now let’s test it. In the Task Scheduler, navigate to Task Scheduler Library, right-click LogonMonitorAutomation and select Run: <br />
<img src="https://i.imgur.com/MQ4GvtN.png"/>
<br />
<br />
After running the task check if your script created an HTML or log file inside your SecurityReports directory: <br />
<img src="https://i.imgur.com/w0jifns.png"/>

Done! The Script automation is working effectively. If you want to automate the other scripts, repeat the same steps adapting for the different features.
<br />
<br />
Now test if your audit settings and PowerShell Scripts are working properly for events 4624, 4625, and 4663, follow these steps:

1. Trigger Event 4624 (Successful Logon)

•	Log out and log back into your computer using a valid user account.

•	This action generates a 4624 event in the Security Event Log. <br />
<img src="https://i.imgur.com/tl0iPlq.png"/>
<br />
<br />
2. Trigger Event 4625 (Failed Logon)

•	Press Ctrl + Alt + Del, select Switch User.

•	Try logging in with wrong credentials several times.

•	Each failed attempt generates a 4625 event. <br />
<img src="https://i.imgur.com/jw8jQvd.png"/>
<br />
<br />
3. Trigger Event 4663 (Object Access)

•	Go to the folder where you applied the auditing policy.

•	Open, modify, or delete a file inside that folder (In this case I renamed it for Lab Test folder12345 it was previously Lab Test folder): <br />
<img src="https://i.imgur.com/dUa1fv8.png"/>

In conclusion, this project successfully delivers a streamlined, Windows-native solution for monitoring and reporting on key security events—successful logons (4624), failed logons (4625/4776), and object access attempts (4663). By combining Group Policy audit configurations, Event Viewer analysis, and PowerShell automation, it enables administrators to track system access in real time, generate structured HTML reports, and maintain clear, reviewable evidence of activity. The scripts provide raw, expanded event data for accurate investigation, while the audit settings ensure that no critical activity is missed.

This setup not only improves visibility into user activity and potential intrusion attempts but also strengthens incident response readiness. Whether run manually or on a scheduled basis, the solution equips security teams with actionable insights for detecting suspicious behavior, protecting sensitive files, and maintaining compliance with security best practices on Windows 11 Pro or Windows Server environments.

<br />
<br />
</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
