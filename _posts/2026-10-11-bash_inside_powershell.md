---
layout: post
author: jhunter
title: bash inside powershell
categories:
- bash
- powershell
date: 2026-10-11
---

# Sources 

[stackoverflow.com](https://stackoverflow.com/questions/72151630/how-to-run-a-bash-script-on-wsl-with-powershell)

[microsoft.com](https://learn.microsoft.com/en-us/windows/wsl/install)

# Introduction

After lots of years working with PowerShell I naturally find it easier to work with PowerShell compared to bash.

The bash shell is sometimes unavoidable because linux contains tools that Windows PowerShell doesn't have.

Luckily I can run lines of code of bash using PowerShell, thanks to wsl.

# Using wsl

The linked source shows how to set up wsl on Windows 11. On my Windows 11 computer I'm using Ubuntu 24.04. 

wsl starts up when i start my computer. I can use the wsl shell simply by opening the Windows terminal and opening a wsl tab.

# Using bash with wsl and PowerShell

I got this command from the stackoverflow website linked above.

```powershell
wsl bash -c "echo hi from simple script"
```

# Taking advantage of bash and PowerShell

I quickly found that I can run commands like this in PowerShell as well. This example converts a midi file into mp3 thanks to the linux applications timidity and ffmpeg.

```powershell
wsl bash -c "timidity myfile.mid -Ow -o - | ffmpeg -i - myfile.mp3"
```

I can get files using  PowerShell and process them with bash.

This example manipulates the file name into a format that is understandable for bash.

```powershell
$myfiles = @(get-childitem *mid)
foreach($file in $myfiles){
    Write-Host "$(get-date -Format "yyyy-MM-dd HH:mm")`tConverting to mp3: $($myfiles.indexof($file) + 1 ) out of $($myfiles.count):`t$($file.fullname)"
    $source1 = $file.fullname -replace("C:","/mnt/c") -replace("\\","/") -replace(" ","\ ")
    $target1 = $file.fullname -replace("C:","/mnt/c") -replace("\\","/") -replace(" ","\ ") -replace("\.mid$",".mp3")
    wsl bash -c "timidity $source1 -Ow -o - | ffmpeg -i - -hide_banner -loglevel error $target1"
}
```

# Concluding

The linked StackOverflow gives lots more example on using bash inside PowerShell. 

It's great to be able to use the power of linux inside Windows scripting, so PowerShell is made even more powerful than ever before.


