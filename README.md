This is a repository of PowerShell scripts that help you setup and configure a ConfigMgr platform. The scripts are developed and maintained by Johan Arwidmark, Johnny Radeck and Henrik Antin.

Here is a quick guide for using the script to set up a proof-of-concept ConfigMgr 2012 R2 platform. In this guide we assume you have a member server installed in a virtual machine, and that you have downloaded all scripts to C:\Setup\Scripts.

# Verify no pending reboots
Test-HYDPendingReboot

# Install Site Server PreReqs (Note:Script reboots if needed)
Install-HYDSiteServerPreReqs.ps1

# Install Windows ADK 8.1 or Windows ADK 10 (Verify log files in %temp% adk folder)
Install-HYDWindowsADK81.ps1
Install-HYDWindowsADK81.ps1

# Install SQL Server 2014 with SP1, modify SQL2014Unattend.ini to match environment (Check logs in C:\Program Files\Microsoft SQL Server\120\Setup Bootstrap\Log)
Install-HYDSQLServer2014.ps1

# Set SQL Server Memory, modify SetSQLServerMemory.sql (Close and Re-Open PowerShell prompt to "find" SQL PoSH cmdlets). If RDP:ed in, logout and login.
Invoke-Sqlcmd -InputFile C:\Setup\Scripts\Set-HYDSQLServerMemory.sql

# Pre-create ConfigMgr Database files, modify CreateConfigMgrDatabase.sql to match environment
Invoke-Sqlcmd –QueryTimeout 0 -InputFile C:\Setup\Scripts\New-HYDConfigMgrDatabase.sql

# Configure Temp DB files, modify ConfigureTempDB.sql to match environment
Invoke-Sqlcmd –QueryTimeout 0 -InputFile E:\Setup\Scripts\Set-HYDTempDB.sql

# Verify no pending reboots
Test-PendingReboot

# Install ConfigMgr 2012 SP2, modify CM2012Unattend.ini to match environment, verify by running cmtrace.
Install-ConfigMgr2012SP2.ps1

# Install ConfigMgr 2012 R2 SP1
Install-ConfigMgr2012R2SP1.ps1

# Install MDT 2013 Update 1
Install-MDT2013U1.ps1

# Configure MDT 2013 Update 1 Integration
Run Configure ConfigMgr Integration (via start screen)

# Install WSUS (add role)
Install-HYDWSUS.ps1

# Configure WSUS (post config), modify Configure-WSUS.ps1 to match environment
Set-HYDWSUS.ps1

--- SQL / ConfigMgr Extra Config ---
CAP all log files, and Configure Reporting Services for simple recovery model
Scripts will be added for this, manual process for now

Configure Backup of SQL, manual process
- Configure SQL Agent to Automatic, plus start agent
- Create SQL backup job per organization standard, select CM_PS1, msdb, SUSDB, ReportingServer, ReportingServerTempDB

# Enable Forest Discovery (If error, make sure to open/close console, and then close/open PowerShell)
Set-HYDEnableADForestDiscovery.ps1

# Enable System Discovery and add OUs to discover. Modify to match environment.
Set-HYDEnableADSystemDiscovery.ps1

Create Boundary Group manually, do not assign boundary group

Add Reporting Services Point site system role, use service account
- verify by viewing wsyncmgr.log

Add Software Update Point site system role
- verify by viewing wsyncmanager.log
- - If needed, start initial sync via WSUS console (normally not needed)
- View WSUS config by running: (Get-WSUSServer -Name WSUS01 -Port 8530).GetConfiguration()

Configure Network Access Account

