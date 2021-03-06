
    Phishing Intelligence Engine
    LogRhythm Security Operations
    greg . foss @ logrhythm . com
    v1.1  --  February, 2018

Copyright 2017 LogRhythm Inc.   
Licensed under the MIT License. See LICENSE file in the project root for full license information.


## [About]
    
#### Invoke-O365Trace.ps1 is the core of the Phishing Intelligence Engine.

This script handles the message trace logging, dynamic analytics, and automated triage + response to phishing attacks. Additionally, this script collects Office 365 Log data via the EWS API, so that it can be aggregated in the SIEM.

##### If you're looking to deploy only message trace logging - use [Basic Message Trace Logging](/Scripts/Basic_Message-Trace-Logging/).


## [System Requirements]

    Minimum System Requirements:

        OS: Windows 2012 R2
        vCPU: 2
        RAM: 8 GB
        DISK: 100 GB

        Ideally, partition the drive out, and install PIE to its own partition


## [Install]

1) Build a Windows Server and install Microsoft Outlook. Configure Outlook with access to your defined Phishing Inbox.

2) Set up auto-response for all new messages on the Phishing inbox, and notify your users to forward suspected phishing messages here. Leave Outlook open on the server.

3) Copy the entire contents of this folder over to the directory you plan to run PIE from on the Windows server.

4) Decide how you would like to store credentials - both options have a level of risk, so ensure you monitor the PIE server appropriately!

    a) Option 1 - Generate an XML document containing the credentials for the inbox and service account. Note that this file will need to be re-generated whenever the server is rebooted!

        Set $EncodedXMLCredentials = $true

        Run the following commands, and take note of the filenames and where these files are stored:

        PS C:\> Get-Credential | Export-Clixml Service-Account_cred.xml

        Enter the full path to the XML file on line 57

    b) Option 2 - Enter plain text passwords.

        Set $PlainText = $true

        Enter your service account credentials on lines 62 and 63

5) Open the Invoke-O365Trace.ps1 script and review the contents. You will need to add credentials and API keys where desired.

    Review lines 44 through 79
    
        Add credentials under each specified section (as documented in step 4)
        Review Office 365 Connectivity and LogRhythm Case API Integration
        Define the folder where you will deploy the Invoke-O365MessageTrace.ps1 script from ($logFolder variable)

    Review Lines 82 through 139

        For each setting that you would like to enable, change the value from $false to $true
        For each enabled third party plugin, set the API key and other required paramters

6) Open the plugins directory. Within this folder, edit Case-API.ps1

    Review Line 30

        Edit the $caseFolder parameter to define where you installed PIE, per step 4. Ensure that you leave \plugins at the end.

7) Run Invoke-O365Trace.ps1 manually (no switches required) and ensure that logs are being pulled into the 'logs' directory.

    Don't worry about the warnings that are thrown - this is completely normal.

8) Configure Invoke-O365Trace.ps1 to run as a scheduled task every 5 minutes.
    
    C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe -command "& 'C:\PIE_INSTALL_DIR\Invoke-O365Trace.ps1'"

9) Configure the LogRhythm SIEM to ingest this data.

    Office 365 Message Tracking parsing - This is included in the LogRhythm Knowledgebase.

    Configure this log source and select 'BETA : LogRhythm  Default' for the Message Processing Engine (MPE) Policy

    ![Log Source Type](/images/O365-Message-Tracking_Log-Source.png)

    Next, set the time parsing under the 'Flat File Settings' tab. If this option does not exist, add the following regex:

        <M>/<d>/<yy> <h>:<m>:<s> <t>

    ![Flat File Settings](/images/O365-Message-Tracking_Time-Format.png)
    
    If you'd like to customize the parsing in any way, below is the current RegEx used to parse Office 365 Message Tracking logs:

        ^"(?<session>[^"]*)","[^"]*","(?<sender>[^"]*)","(?<recipient>[^"]*)",(?:"<dip>")?,(?:"<sip>")?,(?:"(?<subject>.*?)")?,"(?<command>(?<status>[^"]*))","(?<size>\d*)"

10) Import the PIE dashboards, and configure desired AIE alarms.

## [API Integrations]

[Cisco AMP ThreatGRID](https://panacea.threatgrid.com/login)

[Domain Tools](https://domaintools.com)

[Get Link Info](http://getlinkinfo.com/)

[LogRhythm SIEM](https://logrhythm.com/solutions/security/siem/)

[OpenDNS](https://www.opendns.com/)

[Phish Tank](http://www.phishtank.com)

[Sucuri](https://sucuri.net/)

[Screenshot Machine](http://screenshotmachine.com/)

[Swift On Security RegEx](https://github.com/SwiftOnSecurity/PhishingRegex)

[URL Void](http://api.urlvoid.com/)

[VirusTotal](https://virustotal.com)

[Wrike](https://www.wrike.com/)

And more coming soon...


## [Usage]

The /logs/ directory will populate every time the Invoke-script is run. By default, this is configured to run evey 5-minutes.

If you configure the script to handle phishing attacks, you will be presented with a 'cases' folder. Each phishing attack will be given a folder, and associated evidence will be stored within If you enable LogRhythm Case integration, new cases will be created within the SIEM, allowing for collaboration, tracking, and automated metrics.

![Case Management](/images/Case-Management.png)

Automated response actions can be configured based on a weighting system. Every positive indicator from the email analysis increments the overall threat score. So, if this is set to 5, and the site is detected as malicious in Phish Tank, and VirusTotal flags on 7 engines then the overall threat score will be 8, and auto-quarantine will initiate.

![Auto Quarantine](/images/Case-Quarantine.png)

All third party plugins are contained within the 'plugins directory' and any of the smaller functions are included directly in Invoke-O365Trace.ps1. There is no need to modify anything within this directory, unless adding integrations.

To maximize value from this script, couple this with Invoke-O365Ninja.ps1 for automated response to Phishing attacks.

Stay on top of the messages that are coming in, and develop custom AIE rules within the SIEM to detect and respond to these attacks.


## [License]

Copyright 2017 LogRhythm Inc.   
Licensed under the MIT License. See LICENSE file in the project root for full license information.
