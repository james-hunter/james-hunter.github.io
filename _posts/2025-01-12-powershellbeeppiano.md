---
layout: post
author: jhunter
title: Powershell beep piano
categories:
- powershell
date: 2025-01-12
---

# Sources
[https://stackoverflow.com](https://stackoverflow.com/questions/57650484/delay-between-consecutive-calls-to-console-beep)

[https://devblogs.microsoft.com](https://devblogs.microsoft.com/scripting/powertip-use-powershell-to-send-beep-to-console)

[https://www.youtube.com](https://www.youtube.com/watch?v=EdYzqLgMmgk)

[https://worldradiohistory.com](https://worldradiohistory.com/BOOKSHELF-ARH/Handbooks/RSGB-The-Amateur-Radio-Handbook-1940.pdf)


# Summary
This blog post presents a function I created which turns the keyboard into a very inefficient piano.

As the link from stackoverflow above explains, the console beep is not intended for making music.

Instead, the purpose of this function is to demonstrate how to make powershell react to key presses, thanks to the `$host` variable.

# Using the function
Import the function into powershell.exe or pwsh.exe (but not the ISE).

The console beep can be controlled in two ways: the frequency and the length of the beep. The function defines the frequency of middle C in Hertz. The length can be changed but defaults to 600 milliseconds.

With this function, you get one octave of musical notes. The computer keyboard keys:
```powershell
 WE TYU
ASDFGHJK
```

correspond to the piano keys:
```powershell
 CD FGA   (sharps)
CDEFGABC
```

# The function
```powershell
function james-play-piano{

    param(
        [int][Parameter(Mandatory = $false, Position=0)]$length
    )


    $middlec = 261.625565
    if(!$length){
        $length = 600
    }
    
    while ($true){
        if($Host.UI.RawUI.KeyAvailable -and ($key = $Host.UI.RawUI.ReadKey("AllowCtrlC,NoEcho,IncludeKeyUp")) ){
    
            if($key.ControlKeyState -eq "LeftCtrlPressed, NumLockOn"){
                $c = $middlec / 2
            }
            elseif($key.ControlKeyState -eq "RightCtrlPressed, NumLockOn"){
                $c = $middlec * 2
            }
            else{
                $c = $middlec
            }
    
            switch($key.VirtualKeyCode){
                81 {$play = $c} # q key on a French keyboard
                90 {$play = ($c * [math]::pow(2,(1/12)) ) }
                83 {$play = ($c * [math]::pow(2,(2/12)) ) }
                69 {$play = ($c * [math]::pow(2,(3/12)) ) }
                68 {$play = ($c * [math]::pow(2,(4/12)) ) }
                70 {$play = ($c * [math]::pow(2,(5/12)) ) }
                84 {$play = ($c * [math]::pow(2,(6/12)) ) }
                71 {$play = ($c * [math]::pow(2,(7/12)) ) }
                89 {$play = ($c * [math]::pow(2,(8/12)) ) }
                72 {$play = ($c * [math]::pow(2,(9/12)) ) }
                85 {$play = ($c * [math]::pow(2,(10/12))) }
                74 {$play = ($c * [math]::pow(2,(11/12))) }
                75 {$play = ($c * [math]::pow(2,(12/12))) }
                default {$play= $null}
            }
            
            if($play){
                Write-Host "$(get-date -format "yyyy-MM-dd HH:mm")`tPlaying key $($key.VirtualKeyCode), frequency : $($play)"
                try{
                    [console]::beep($play,$length)
                    Start-Sleep -Seconds 1
                }
                catch{
                    Write-Host "$(get-date -format "yyyy-MM-dd HH:mm")`tFailed to play tone" -ForegroundColor Red
                }
            }
        }
    }
}
```


