==========
DISCLAIMER
==========

THE SOFTWARE IS PROVIDED *AS IS*, WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.

IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.


================
IMPORTANT NOTICE 
================
 
This script is designed to collect information that will help Microsoft Customer Support Services (CSS) troubleshoot an issue you may be experiencing with Windows Virtual Desktop.

The collected data may contain Personally Identifiable Information (PII) and/or sensitive data, such as (but not limited to) IP addresses, PC names and user names.

The script will save the collected data in a subfolder and also compress the results into a ZIP file. The folder or ZIP file are not automatically sent to Microsoft. 

You can send the ZIP file to Microsoft CSS using a secure file transfer tool - Please discuss this with your support professional and also any concerns you may have. 

Find our privacy statement here: https://privacy.microsoft.com/en-us/privacy 


==================================
How to use WVD-Collect (v201219.7)
==================================

The script must be run with elevated permissions in order to collect all required data. It works on any Windows client and Windows server OS supporting at least PowerShell 5.1.

Run the script on WVD host VMs and/or on Windows based devices from where you connect to the WVD hosts, as needed.

The script will collect a set of "default data" regardless of parameters. 
By adding one or more parameters, you can collect additional data, useful for specific troubleshooting scenarios.

The script will archive the collected data into a .zip file located in the same folder as the script itself.


Available command line parameters
---------------------------------

-Certificate = Collects Certificates related data

-Client = Collects existing RD client ETL traces and RD client upgrade log from devices running the WVD Desktop Client (the content of the "C:\Users\%username%\AppData\Local\Temp\DiagOutputDir\RdClientAutoTrace" folder)

	Important notes:
		o This "-ClientAutoTrace" parameter is useful for collecting the automatic client ETL traces, when troubleshooting WVD client connectivity or WVD client issues. 
		o Please note that the RdClientAutoTrace folder might get quite large over time. 
		o When such data is needed for troubleshooting, recommended is to first clear the content of the folder (eventually create a backup of the old content if you want), then reproduce the issue and close the client afterwards so that new traces are generated and after that run the WVD-Collect script so that only the latest, relevant traces are collected.
		o The tool will also check if "msrdc.exe" is still running and if yes, it will ask the user's confirmation to close it in oder to collect the latest client ETL trace too. If you select "Y", it will disconnect all active WVD connections on the client. If you select "N", the tool will continue without disconnecting active WVD connections, but the latest WVD Desktop client ETL trace may not be collected.

-MonTables = Collects existing converted monitoring traces from WVD hosts (.csv files converted from existing .tsf files from under "C:\Windows\System32\config\systemprofile\AppData\Roaming\Microsoft\Monitoring\Tables")

	Important notes:
		o This "-MonTables" parameter is useful for investigating issues with WVD hosts not communicating with the WVD services (Broker or Diagnostics).
		o In these scenarios Kusto/Log Analytics may not receive any data, but some traces are still available on the hosts themselves and may help identify the underlying cause.

-MSRA = Collects Remote Assistance related data

-Profiles = Collects User Profile related data (incl. FSLogix)

-Teams = Collects Teams WVD optimization related data

	Important notes:
		o To collect the proper data when having issues with Teams optimized for WVD, reproduce the issue with an affected user, press Ctrl+Alt+Shift+1 within the affected user's session while Teams is open to generate additional Teams diagnostics data and after that run the script with the "-Teams" parameter (WVD-Collect.ps1 -Teams) within this affected user's WVD session.
		o The script itself will not force generating these diagnostics files, it will only collect them if they are already available.
		o There is also an additional confirmation prompt when launching the tool with the "-Teams" parameter to get the user's confirmation that these prerequisites have been met before continuing.

-DiagOnly = When executed with this parameter (even if other parameters are also included) the script will skip ALL data collection and will ONLY run the diagnostics part. This is useful when you want to run only a quick Diag without collecting additional data.

	Important notes: 
		o "-DiagOnly" is not a replacement of a full data analysis. It is only meant to give you basic diagnostics of some common scenarios and to ease further troubleshooting. Depending on the scenario, thorough data collection and analysis without using the "-DiagOnly" will be needed.
		o To run diagnostics also for a specific scenario (like Profile troubleshooting), the corresponding command line parameter needs to be present too.
			E.g.: 
			".\WVD-Collect.ps1 -DiagOnly" 			will run only the default diagnostics
			".\WVD-Collect.ps1 -Profile -DiagOnly" 		will run the default diagnostics + "Profile"-specific diagnostics

-Verbose = Displays more verbose information about the steps performed during data collection


You can also read more information about the tool and available parameters by running "Get-Help .\WVD-Collect.ps1 -Full".



Usage example without parameters (collects only default data)
--------------------------------

	.\WVD-Collect.ps1 


Usage example with parameters (collects default data + profile related information + Teams WVD optimization related data + displays more information on the performed steps):
-----------------------------

	.\WVD-Collect.ps1 -Profiles -Teams -Verbose


PowerShell ExecutionPolicy
--------------------------

If the script does not start, complaining about execution restrictions, then in an elevated PowerShell console run:

	Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Force -Scope Process

and verify with "Get-ExecutionPolicy -List" that no ExecutionPolicy with higher precedence is blocking execution of this script.
The script is digitally signed with a Microsoft Code Sign certificate.

After that run the WVD-Collect script again.

If you cannot change the ExecutionPolicy settings or a higher restriction applies that blocks execution of the script, please ask your domain admin (or the responsible team) for a temporary exemption.


Once the script has started, p​​​lease read the "IMPORTANT NOTICE" message and confirm if you agree to continue with the data collection.

Depending on the amount of data that needs to be collected, the script may need run for several minutes. Please wait until the script finishes collecting all the data.

If you are missing any of the data that the tool should normally collect, check the content of "*_WVD-Collect-Output.txt" and "*_WVD-Collect-Errors.txt" for more information. Some data may not be present during data collection and thus not picked up by the script. This should be visible in one of the two text files.



====================
Data being collected
====================

The collected data is stored in a subfolder under the same folder where the script is located and at the end of the data collection, the results are archived into a .zip file. No data is automatically uploaded to Microsoft.

The script collects the following set of "default data" (if present) regardless if command line parameters have been specified (in brackets is the folder or file that contains the collected data, with * = computer name):

• Log files:
	o C:\Packages\Plugins\Microsoft.Powershell.DSC\<version>\Status\
	o C:\Packages\Plugins\Microsoft.Compute.JsonADDomainExtension\<version>\Status\
	o C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\<version>\Status\
	o C:\Program Files\Microsoft RDInfra\AgentInstall.txt
	o C:\Program Files\Microsoft RDInfra\GenevaInstall.txt
	o C:\Program Files\Microsoft RDInfra\SXSStackInstall.txt
	o C:\Program Files\Microsoft RDInfra\WVDAgentManagerInstall.txt (when executed on Windows 7 hosts)
	o C:\Windows\debug\NetSetup.log
	o C:\Windows\Temp\ScriptLog.log
	o C:\WindowsAzure\Logs\WaAppAgent.log
	o C:\WindowsAzure\Logs\MonitoringAgent.log
	o C:\WindowsAzure\Logs\Plugins\ folder with all subfolders
• Geneva Scheduled Task information
• Local group membership information:
	o Remote Desktop Users
• Registry keys:
	o HKEY_CURRENT_USER\SOFTWARE\Microsoft\RdClientRadc
	o HKEY_CURRENT_USER\SOFTWARE\Microsoft\Remote Desktop
	o HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\DSC
	o HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\
	o HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\RDAgentBootLoader
	o HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\RDInfraAgent
	o HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\RDMonitoringAgent
	o HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WVDAgentManager (when executed on Windows 7 hosts)
	o HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Terminal Server Client
	o HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies
	o HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Terminal Server
	o HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Cryptography
	o HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\CredentialsDelegation
	o HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services
	o HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Cryptography
	o HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa
	o HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders
	o HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server
	o HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RdAgent
	o ​​​​​​​HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RDAgentBootLoader
	o ​​​​​​​HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WVDAgent (when executed on Windows 7 hosts)
	o ​​​​​​​HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WVDAgentManager (when executed on Windows 7 hosts)
	o HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\TermService
	o HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\UmRdpService
	o HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WinRM​​
• Event Logs:
	o Application
	o Microsoft-Windows-DSC/Operational
	o Microsoft-Windows-PowerShell/Operational
	o Microsoft-Windows-RemoteDesktopServices
	o Microsoft-Windows-RemoteDesktopServices-RdpCoreCDV/Admin
	o Microsoft-Windows-RemoteDesktopServices-RdpCoreCDV/Operational
	o Microsoft-Windows-RemoteDesktopServices-RdpCoreTS/Admin
	o Microsoft-Windows-RemoteDesktopServices-RdpCoreTS/Operational
	o Microsoft-Windows-TerminalServices-LocalSessionManager/Admin
	o Microsoft-Windows-TerminalServices-LocalSessionManager/Operational
	o Microsoft-Windows-TerminalServices-PnPDevices/Admin
	o Microsoft-Windows-TerminalServices-PnPDevices/Operational
	o Microsoft-Windows-TerminalServices-RemoteConnectionManager/Admin
	o Microsoft-Windows-TerminalServices-RemoteConnectionManager/Operational
	o Microsoft-Windows-WinRM/Operational
	o Microsoft-WindowsAzure-Diagnostics/Bootstrapper
	o Microsoft-WindowsAzure-Diagnostics/GuestAgent
	o Microsoft-WindowsAzure-Diagnostics/Heartbeat
	o Microsoft-WindowsAzure-Diagnostics/Runtime
	o Microsoft-WindowsAzure-Status/GuestAgent
	o Microsoft-WindowsAzure-Status/Plugins
	o Security
	o System
• "gpresult /h" and "gpresult /r /v" output
• "fltmc filters" output
• Details of the running processes and services
• Networking information (firewall rules, ipconfig /all, profiles, netstat -anob, proxy settings)
• Qwinsta output
• PowerShell version
• Get-Hotfix output
• Get-DscConfiguration and Get-DscConfigurationStatus output
• File versions of the currently running binaries
• File information about the WVD desktop client binaries ("msrdc.exe" and "msrdcw.exe")
• File versions of key binaries:
	o Windows\System32\*.dll
	o Windows\System32\*.exe
	o Windows\System32\*.sys
	o Windows\SysWOW64\*.dll
	o Windows\SysWOW64\*.exe
	o Windows\SysWOW64\*.sys
	o Windows\System32\drivers\*.sys
• Basic system information
• .NET Framework information
• Msinfo32 output
• WinRM configuration information
• Basic Diagnostics information (see below for more details)



When used together with the "-Certificate" command line parameter, the following data are also collected (if present):

• Certificate My store information
• Certificate thumbprint information
• Event Logs:
	o Microsoft-Windows-CAPI2/Operational



When used together with the "-Client" command line parameter, the following data are also collected (if present):

• The content of the "C:\Users\%username%\AppData\Local\Temp\DiagOutputDir\RdClientAutoTrace" folder (available on devices used as source clients to connect to WVD hosts), containing:
	o WVD remote desktop client connection ETL traces
	o WVD remote desktop client application ETL traces
	o WVD remote desktop client upgrade log (MSI.log)



When used together with the "-MonTables" command line parameter, the following steps are also performed:

• Convert existing .tsf files on WVD hosts from under "C:\Windows\System32\config\systemprofile\AppData\Roaming\Microsoft\Monitoring\Tables" into .csv files
• Collect the resulting .csv files



When used together with the "-MSRA" command line parameter, the following data are also collected (if present):

• Membership information for the following groups:
	o Distributed COM Users
	o Offer Remote Assistance Helpers
• Event Logs:
	o Microsoft-Windows-RemoteAssistance/Admin
	o Microsoft-Windows-RemoteAssistance/Operational



When used together with the "-Profiles" command line parameter, the following data are also collected (if present):

• FSLogix log files:
	o C:\ProgramData\FSLogix\Logs
• Registry keys:
	o HKEY_CURRENT_USER\SOFTWARE\Microsoft\Office
	o HKEY_CURRENT_USER\SOFTWARE\Microsoft\OneDrive
	o HKEY_CURRENT_USER\SOFTWARE\Policies\Microsoft\Office
	o HKEY_LOCAL_MACHINE\SOFTWARE\FSLogix
	o HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList
	o HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Search
	o HKEY_LOCAL_MACHINE\SOFTWARE\Policies\FSLogix
	o HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Defender\Exclusions\Extensions
	o HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Defender\Exclusions\Paths
	o HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Defender\Exclusions\Processes
• Event Logs:
	o FSLogix-Apps/Admin
	o FSLogix-Apps/Operational
	o Microsoft-Windows-SMBClient/Connectivity
	o Microsoft-Windows-SMBClient/Operational
	o Microsoft-Windows-SMBClient/Security
	o Microsoft-Windows-SMBServer/Connectivity
	o Microsoft-Windows-SMBServer/Operational
	o Microsoft-Windows-SMBServer/Security
	o Microsoft-Windows-User Profile Service/Operational
	o Microsoft-Windows-VHDMP/Operational
• FSLogix tool output:
	o frx list-redirects
	o frx list-rules



When used together with the "-Teams" command line parameter and ran within the session of an affected user that has already pressed Ctrl+Alt+Shift+1 within Teams to generate additioanl diagnostics logs, the following data are also collected:

• Teams Logs:
	o %appdata%\Microsoft\Teams\logs.txt
	o %userprofile%\Downloads\MSTeams Diagnostics Log DATE_TIME.txt
	o %userprofile%\Downloads\MSTeams Diagnostics Log DATE_TIME_calling.txt
	o %userprofile%\Downloads\MSTeams Diagnostics Log DATE_TIME_cdl.txt
	o %userprofile%\Downloads\MSTeams Diagnostics Log DATE_TIME_cdlWorker.txt
	o %userprofile%\Downloads\MSTeams Diagnostics Log DATE_TIME_chatListData.txt
	o %userprofile%\Downloads\MSTeams Diagnostics Log DATE_TIME_sync.txt
	o %userprofile%\Downloads\MSTeams Diagnostics Log DATE_TIME_vdi_partner.txt
• Registry keys:
	o HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams
	o HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RDWebRTCSvc
• DxDiag output in .txt format with no WHQL check



========
WVD-Diag
========

WVD-Collect also performs basic diagnostics for some common known issues. You can run the script also in DiagOnly mode, by specifying the "-DiagOnly" command line parameter.
With this parameter, the script will skip all data collection and run only the basic diagnostics steps.
Important: To run diagnostics also for a specific scenario (like Profile troubleshooting), the corresponding command line parameter needs to be present too.
E.g.: 
	"WVD-Collect -DiagOnly" will run only the default diagnostics
	"WVD-Collect -Profile -DiagOnly" will run the default diagnostics + "Profile"-specific diagnostics


Currently the following checks are performed:

Default diagnostics:

• Check the status of key services (RdAgent, RDAgentBootLoader, WVDAgent (Win7), WVDAgentManager (Win7), TermService, SessionEnv, UmRdpService, AppReadiness, AppXSvc, WinRM)
• Check for current and previous WVD Agent and Stack versions and their installation dates (Windows 10 and Server OS hosts)
• Check the availability and value of the reg key: 'fEnableWinStation' for host not available scenarios
• Check the availability and value of the reg key: 'DeleteUserAppContainersOnLogoff' for firewall rules bloating scenarios
• Check the availability and value of the reg key: 'SessionDirectoryListener' to better identify the WVD listener currently in use
• Check the availability and value of the reg key responsible for the 'SSL Cipher Suite Order' policy for scenarios where users cannot connect with a message containing 'security package error' or 'no available resources'
• Check for RDP ShortPath configuration (Windows 10 and Server OS hosts)
• Check for Windows Instaler service start type (If you disable Windows Installer, the service won't be able to install agent updates on your session hosts, and your session hosts won't function properly)
• Check WinRM configuration / requirements
​​​​​​​• Presence of "WinRMRemoteWMIUsers__" group
• IPv4Filter and IPv6Filter values
• Presence of firewall rules for ports 5985 and 5986


Profile specific diagnostics (when ran together with the "-Profiles" command line paramenter):

• Check the status of key services (frxsvc, frxdrv, frxccds, OneDrive Updater Service)
• Check for the presence of the recommended Windows Defender Antivirus exclusion values


The script generates a *_WVD-Diag.txt output file with the results of the above checks.



==========
Tool Owner
==========
Robert Klemencz @ Microsoft Customer Service and Support
If you have any feedback or bugs to report, please, reach out to me (Robert Klemencz) at robert.klemencz@microsoft.com

