---
layout: post
author: jhunter
title: Using get-wmiobject
categories:
- powershell
date: 2022-06-06
---

# Sources
`get-help get-wmiobject`

[https://docs.microsoft.com](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-wmiobject?view=powershell-5.1#:~:text=1%20Description.%20Starting%20in%20PowerShell%203.0%2C%20this%20cmdlet,are%20returned%20from%20WMI%20should%20contain%20amended%20information.)

[https://doc.microsoft.com](https://docs.microsoft.com/en-us/windows/win32/cimwin32prov/win32-usbcontrollerdevice)

[https://en.wikipedia.org](https://en.wikipedia.org/wiki/Windows_Management_Instrumentation)

# Command
`get-wmiobject`

# Alias
`gwmi`

# Definition
Get a Windows Management Instrumentation object.

In other words, get information about your computer or about a remote computer.

# Options

`-Class` lets you specify the type of information you are getting.

`-Computername` specifies the computer you are getting information from and about. By default you are getting information about the local computer.

# Examples

## Get BIOS information

```powershell

PS C:\Temp> get-wmiobject -class win32_bios

SMBIOSBIOSVersion : R01-B3
Manufacturer      : American Megatrends Inc.
Name              : R01-B3
SerialNumber      : DTBDSEF123407012343123
Version           : ACRSYS - 11300000

```

## Get the local operating system

```powershell

PS C:\Temp> Get-WmiObject -Class Win32_OperatingSystem | Format-Table Caption, ServicePackMajorVersion -AutoSize

Caption                      ServicePackMajorVersion
-------                      -----------------------
Microsoft Windows 11 Famille                       0

```

## Get information about USB devices

This is an example of where the data might be available elsewhere, for example using `Get-PnpDevice`.

```powershell

PS C:\Temp> gwmi Win32_USBControllerDevice |%{[wmi]($_.Dependent)} | Sort Manufacturer,Description,DeviceID | select  Manufacturer,Description,Service,DeviceID

Manufacturer                                    Description                                             Service  DeviceID
------------                                    -----------                                             -------  --------
(Claviers standard)                             Périphérique clavier IHM                                kbdhid   HID\VID_1234&PID_4651...

```

## Old wmic command

Before `get-wmiobject` there was the command `wmic`. Today you can use the new command.

For example, here is the old command to list brief information from a class (in this case the logicaldisk class).

```powershell

PS C:\Temp> wmic logicaldisk list brief
DeviceID  DriveType  FreeSpace     ProviderName  Size           VolumeName
C:        3          27459571712                 526838894592   Test

```

Instead you can have:

```powershell

PS C:\Temp> get-wmiobject -Class win32_logicaldisk


DeviceID     : C:
DriveType    : 3
ProviderName :
FreeSpace    : 27460636672
Size         : 526838894592
VolumeName   : Test

```

## Connecting to other computers

Sometimes certain commands don't work remotely, and this is where `get-wmiobject` can help you out.

 I was trying to get info from a remote machine and this command worked for me:

```powershell

PS C:\Temp> get-wmiobject -Class Win32_NTLogEvent -computername server02 | ? {$_.logfile -eq "application"} | select -First 1 | fl


Category         : 0
CategoryString   :
EventCode        : 15
EventIdentifier  : 15
TypeEvent        :
InsertionStrings : {Windows Defender, SECURITY_PRODUCT_STATE_ON}
LogFile          : Application
Message          : L’état Windows Defender a été mis à jour vers SECURITY_PRODUCT_STATE_ON.

```

## Finding classes

You can list all the WMI classes with this command:

```powershell

 Get-WmiObject -List | sort name | select name

```

For example:

```powershell

PS C:\Temp> Get-WmiObject -List | ? {$_.name -like "*useraccount*"}


   NameSpace : ROOT\cimv2

Name                                Methods              Properties
----                                -------              ----------
Win32_UserAccount                   {Rename}             {AccountType, Caption, Description, Disabled...}

```


