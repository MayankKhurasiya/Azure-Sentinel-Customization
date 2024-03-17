<h1>Azure Sentinel Customization</h1>


<h2>Description:</h2>
Implemented custom content and performed various tasks for Azure Sentinel (SIEM) to enhance threat detection and visualization capabilities. Utilized a custom PowerShell script to extract metadata from Windows Event Viewer, forwarding it to a third-party API to derive geolocation data. Configured a Log Analytics Workspace in Azure to ingest custom logs containing geographic information such as latitude, longitude, state/province, and country. Additionally, configured Custom Fields in Log Analytics Workspace to map geo data in Azure Sentinel. Developed and configured an Azure Sentinel workbook to display global attack data, specifically RDP brute force attacks, on a world map based on physical location and magnitude of attacks.
<br />


<h2>The tools used in this project include:</h2>

- <b>Azure Sentinel: Microsoft's cloud SIEM used for threat detection and response.</b>
- <b>Log Analytics Workspace: Configured to ingest and analyze custom logs containing geographic information.</b>
- <b>PowerShell: Used to develop custom scripts for extracting metadata from Windows Event Viewer.</b>
- <b>Third-party API: Utilized to derive geolocation data from extracted metadata.</b>
- <b>Azure Sentinel Workbooks: Developed and configured to visualize attack data on a world map.</b>


<h2>Program walk-through:</h2>

<h3>Create Free Azure Account:</h3>
- Sign up: https://azure.microsoft.com/en-us/free/ <br />
- Login: https://portal.azure.com  

<p align="left"> <br />
Login to portal.azure.com
Create Virtual Machine: <br/>
<img src="https://i.imgur.com/rBvweuY.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
<br />
Fill in the details:  <br/>
<img src="https://i.imgur.com/loHbrRN.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
<br />
Provide username and password > click next: <br/>
<img src="https://i.imgur.com/uCSOPNI.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
<br />
Click next:  <br/>
<img src="https://i.imgur.com/1Zi7JGN.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
<br />
Select Advance in NIC > click create new:  <br/>
<img src="https://i.imgur.com/0jvbSDB.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
<br />
Remove the default rule:  <br/>
<img src="https://i.imgur.com/9kHwWb2.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
<br />
Create inbound rule which will allow everyone into the VM:  <br/>
<img src="https://i.imgur.com/vCYJW0B.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
Click Add > OK <br/>
<br />
Click review+create > create (this will take a while to deploy)  <br/>
<img src="https://i.imgur.com/c5pcN87.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
<br />
VM is created (My VM):  <br/>
<img src="https://i.imgur.com/QWHB6TF.png" height="80%" width="90%" alt="Azure Sentinel"/>
<h3>Log Analytics Workspaces:</h3>
<br />
Now, search log analytics workspace:  <br/>
<img src="https://i.imgur.com/jAvDXtL.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
Create log analytics workspace:  <br/>
<img src="https://i.imgur.com/UpGTnsv.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
Click review + create > create <br/>
<img src="https://i.imgur.com/gxPV7Ar.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
<h3>Microsoft Defender for Cloud:</h3>
Search microsoft defender for cloud > select environment setting from left pane:  <br/>
<img src="https://i.imgur.com/vxHCEmV.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
Select law-honeypot > defender plans > except sql, enable all:  <br/>
<img src="https://i.imgur.com/9TSqqlA.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
From data collection select all events > save <br/>
<img src="https://i.imgur.com/BIkCIF7.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
Go to log analytics workspaces and connect to VM: <br/>
log analytics workspaces > select your VM > again select VM > click the VM > and connect  <br/>
<img src="https://i.imgur.com/D0FhdUw.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
VM is connecting:  <br/>
<img src="https://i.imgur.com/7ryDar5.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
<h3>Azure Sentinel:</h3>
Open another windows tab and open Azure Sentinel in it: (this will be used to visual the attach data)  <br/>
<img src="https://i.imgur.com/odcXldJ.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
<br />
Click create azure sentinel > add the listed VM: <br/>
<img src="https://i.imgur.com/X9rSk9h.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
Go back to previous windows tab and open your VM > copy the public IP address:  <br/>
<img src="https://i.imgur.com/IQBHnOa.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
<h3>Connecting VM via RDP:</h3>
Open the remote desktop connection (on your own computer)  <br/>
<img src="https://i.imgur.com/3j3blTa.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
Click connect > more choices > give username and password and connect: <br/>
<img src="https://i.imgur.com/eJJQbJS.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
Click start menu > event logs > windows logs > security > audit failure: evnet ID 4625 (this captures failed logon attempts along with ip addresses)  <br/>
<img src="https://i.imgur.com/8j6tSwI.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
<br />
<h3>Powershell Script:</h3>
We will use the captures ip addresses to get the details > ipgeolocation.io (we will paste the ip here and the api's will post the details)- this will be done through script <br/>

Note: turn off the firewall of your VM <br/>

Now, go to https://github.com/joshmadakor1/Sentinel-Lab/blob/main/Custom_Security_Log_Exporter.ps1 <br/>
Copy the script > open powershell ise > create new file and paste the script > save the file (log_expoter.ps1) <br/>

Replace the API key in the script (to get the API key signup into ipgeoloaction.io) <br/>
API: 4c3f2d536a234aff810e7d90******** <br/>

Run the script (this script fetch the data from audit logs > sent them to ipgeolocation.io site and get the data > insert the data into the log file (failed_rdp.log)). <br/>
<h3>Create Custom Logs in Azure:</h3>
Search log analytics > select law-honeypot > Table > create > new custom logs (MMA based) <br/>
<img src="https://i.imgur.com/NLq0ldR.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
Copy the data from failed_rdp.log (from VM), and paste the data in your local system (in notepad and name it 'failed_rdp.log') and save the file in your desktop.  <br/>
Select the file: <br/>
<img src="https://i.imgur.com/DyiGy9N.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
Click next:  <br/>
<img src="https://i.imgur.com/EqguNdL.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
Click next> select windows > give the full path of failed_rdp.log file: <br/>
<img src="https://i.imgur.com/mPzdP8e.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
Click next > give the CL name: FAILED_RDP_WITH_GEO  <br/>
<img src="https://i.imgur.com/CyLz7Lh.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
<br />
Click next > review + create > create <br/>
<img src="https://i.imgur.com/oDfnJBX.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
This will take some time to sync.<br/>
To check the data, go to logs > type SecurityEvent > Run  <br/>
<img src="https://i.imgur.com/617tC9c.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
<br />
You can give the following query: (this will fetch the data along with their placeholders)- save this query <br/>
FAILED_RDP_WITH_GEO_CL
| parse RawData with * "latitude:" Latitude ",longitude:" Longitude ",destinationhost:" DestinationHost ",username:" Username ",sourcehost:" Sourcehost ",state:" State ", country:" Country ",label:" Label ",timestamp:" Timestamp
| project Latitude, Longitude, DestinationHost, Username, Sourcehost, State, Country, Label, Timestamp <br/>
<h3>OR</h3>
FAILED_RDP_WITH_GEO_CL <br/>
 |extend username = extract(@"username:([^,]+)", 1, RawData), <br/>
         timestamp = extract(@"timestamp:([^,]+)", 1, RawData), <br/>
         latitude = extract(@"latitude:([^,]+)", 1, RawData), <br/>
         longitude = extract(@"longitude:([^,]+)", 1, RawData), <br/>
         sourcehost = extract(@"sourcehost:([^,]+)", 1, RawData), <br/>
         state = extract(@"state:([^,]+)", 1, RawData), <br/>
         label = extract(@"label:([^,]+)", 1, RawData), <br/>
         destination = extract(@"destinationhost:([^,]+)", 1, RawData), <br/>
         country = extract(@"country:([^,]+)", 1, RawData) <br/>
 |where destination != "samplehost" <br/>
 |where sourcehost != "" <br/>
 |summarize event_count=count() by timestamp, label, country, state, sourcehost, username, destination, longitude, latitude <br/>
<img src="https://i.imgur.com/uuGu01H.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
<h3>Setting up MAP in Sentinel:</h3>
Search sentinel > click law-honeypot > workbook  <br/>
<img src="https://i.imgur.com/YxRFjSI.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
Click add workbook > remove default entries > click add query > paste the query <br />
FAILED_RDP_WITH_GEO_CL 
| parse RawData with * "latitude:" Latitude ",longitude:" Longitude ",destinationhost:" DestinationHost ",username:" Username ",sourcehost:" Sourcehost ",state:" State ", country:" Country ",label:" Label ",timestamp:" Timestamp
| project Latitude, Longitude, DestinationHost, Username, Sourcehost, State, Country, Label, Timestamp <br />

From visualization > select MAP <br />
From Size > select full  <br/>
<img src="https://i.imgur.com/8yqX8hb.png" height="80%" width="90%" alt="Azure Sentinel"/>
<br />
Final Map:  <br/>
<img src="https://i.imgur.com/jGa3ec3.png" height="80%" width="90%" alt="Azure Sentinel"/>
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
