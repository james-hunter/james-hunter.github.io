---
layout: post
author: jhunter
title: Powershell dots and ampersands 
cnategories:
- powershell
date: 2025-01-19
---

# Sources
`help about_Operators -ShowWindow`

# Summary
This blog shows a couple of ways to use dots `.` and ampersands `&` in PowerShell.

# Dot .
## Using a dot as the present working directory
When writing te path to a file, begin the path like this to show that the item is inside the present working directory.
```powershell
.\
```
For example:
```powershell
get-childitem .\
```
## Using a dot as a sourcing operator
Run a script in the current scope.
```powershell
. C:\Temp\20250119_test.ps1
```
You can use the dot sourcing operator as an alias of `ìmport-module`, when the .ps1 file contains functions.

# Ampersand &
## Call operator
The ampersand is a call operator. When you call a script file with the ampersand the code is run in a different scope to the curent one.
``` powershell
& C:\Temp\20250119_test.ps1
```
Since the script gets executed in another scope, it doesn't make sense to use an ampersand to import a function.
# Example
I create this simple function inside the file C:\Temp\20250119_test.txt.
```powershell
function james-test{

        $x = 1
        return $x

}
```
Trying to import the script with an ampersand fails. With the dot source operator it works. I only imported the function so the $x variable is still null.
```powershell
PS C:\Temp> & .\20250119_test.ps1
PS C:\Temp> dir function:*james*
PS C:\Temp>
PS C:\Temp> $x
PS C:\Temp>
PS C:\Temp> . .\20250119_test.ps1
PS C:\Temp> dir function:*james*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Function        james-test

PS C:\Temp> $x
PS C:\Temp>
```
When I run the function with an ampersand the script runs in another scope and returns the value of $x. However, the value of $x in the current scope is unchanged.

Interestingly, if I run the function without any call operator, it is as if I used the ampersand. The function is executed but the value of $x is unchanged in the current scope.

The value of $x is changed if I run the function in the current scope with a dot.
```powershell
PS C:\Temp> & james-test
1
PS C:\Temp> $x
PS C:\Temp>
PS C:\Temp> james-test
1
PS C:\Temp> $x
PS C:\Temp>
PS C:\Temp>
PS C:\Temp> . james-test
1
PS C:\Temp> $x
1
PS C:\Temp>
```



