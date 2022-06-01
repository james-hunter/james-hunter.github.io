---
layout: post
author: jhunter
title: Using get-content
categories:
- powershell
date: 2022-06-01
---

# Sources
`get-help get-content`

# Command
`get-content`

# Alias
`more`, `gc`, and `cat`

# Definition
Read a file.

# Options

`-Tail` allows you to only see the last x lines of a file.

# Example

`Get-content` lets you simply read a file, but I tend to use it to work with a list of (a little) data that originated from outside of PowerShell.
In this example I save a list in text file, then use `get-content` to put the list into a variable.

`$list = get-content .\test.txt`

![image](https://james-hunter.github.io/pictures/20220601_1.jpg)

In this way I can loop through the data with other commands.

`foreach($pc in $list) {write-host "PC is : $pc" -ForegroundColor yellow}`

![image](https://james-hunter.github.io/pictures/20220601_2.jpg)

# Example

Here I use `-Tail` with an infinite loop to read (or "watch") a log file where I expect to see changes.

`while($true){cls ; Get-Content .\testlog.txt -Tail 5 ; Start-Sleep 5 }`

# Example

The alias `more` is not an exact alias of `get-content` because `more` will show part of the file content if that content is longer than the screen height (this is called "pagination").

In this way `more` can be used in two ways:

`get-content ./mylongfile.txt | more`

`more ./mylargefile.txt`
