---
layout: post
author: jhunter
title: Curly brackets
categories:
- powershell
date: 2025-03-16
---

# Source
`help about_Script_Blocks`

[wikipedia.org](https://en.wikipedia.org/wiki/Bracket)

# Command
```powershell
$variable1 = { $scriptblock1 }
```

# Definition
The curly brackets `{` and `}` (also known as "braces") can be used to define a block of script inside a PowerShell variable.

This is handy if you want to:

* Build a block of script and re-use it;
* Or programmatically build a block of script inside your code.

# How to build an expression
In the PowerShell, define a block of script like in this example:

```powershell
$year = 2025
$myscriptblock1 = {get-childitem | Where-Object { (get-date($_.lastwritetime)).year -eq $year }}
```

Note that this is not a string of text. Verify this fact with the following command:

```powershell
PS C:\> $myscriptblock1 | Get-Member


   TypeName : System.Management.Automation.ScriptBlock

<output omitted>
```

# Run a script block variable
Here are two handy ways to run a block of script:

Use dot notation:
```powershell
. $myscriptblock1
```

Use the `ìnvoke-command` command:

```powershell
invoke-command -scriptblock $myscriptblock1
```

