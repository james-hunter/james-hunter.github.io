---
layout: post
author: jhunter
title: Changing CLI environment
categories:
- powershell
date: 2024-12-08
---

# Command
`powershell

# Definition
While working inside the terminal you can change which shell you are using. 
Simple enter the name of the shell: powershell, pwsh or cmd (or powershell.exe or pwsh.exe or cmd.exe respectively). 

Note that you can view the powershell version with the output of the variable $psversiontable. Windows 11 also shows 'PS' in the terminal to let you know that you are inside powershell or pwsh.

```powershell
PS C:\> $psversiontable

Name                           Value
----                           -----
PSVersion                      7.4.6
...
```

# Examples

## Change to powershell

```powershell
powershell
```

## Change to pwsh

```powershell
pwsh
```

## Change to cmd


```powershell
cmd
```

