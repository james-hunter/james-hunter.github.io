---
layout: post
author: jhunter
title: Kermit on dosbox
categories:
- networking
date: 2025-04-20
---

# Source
[columbia.edu](https://www.columbia.edu/kermit/mskermit.html)

[kermitproject.org](https://www.kermitproject.org/onlinebooks/usingmsdoskermit2e.pdf)

[vogons.org](https://www.vogons.org/viewtopic.php?t=64293)

# Summary
DOS is old fashioned, but there exists old hardware which requires DOS software and a serial connection to run.

DOSBox is DOS emulator which runs in Windows11 and which can run DOS software.

Kermit is a terminal emulation software that runs on lots of platforms. 

Here I describe how to run Kermit on DOSBox and use a communication port (COM port) to talk to a real physical box.

# Topology
This blog is using this physcial topology:
* A physcial Cisco router has a serial cable connected to my Windows 11 computer.
* I use a usb-to-serial adapter on my win11 PC.

# Configuring the COM port
Inside the control panel of Windows I can see the COM port on my PC is COM10.

If I want to use the COM port inside dosbox, I need to tell DOSBox the COM port number using the DOSBox configuration file.

To open the configuraion file I access this link:

`C:\ProgramData\Microsoft\Windows\Start Menu\Programs\DOSBox-0.74-3\Options\DOSBox 0.74-3 Options.lnk`

I find the line with "serial1...." and edit it to include the COM port that I have in Windows.

Notice that the COM10 port in Windows is going to be seen as serial 1 port inside of DOSBox.

```powershell
serial1=directserial realport:COM10
```

# Making Kermit available
I download Kermit from the university of Columbia website.

I extract the zip file and put the Kermit files here on my PC:

`C:\ProgramData\dosboxc\msk316`

I open the DOSBox configuration file, and go to this section. I update the configuration file to mount the folder as the c drive inside DOSBox.

```powershell
[autoexec]
# Lines in this section will be run at startup.
# You can put your MOUNT lines here.
mount c D:\RIGAS-DATORS\ProgramData\dosboxc
c:
```

# Using Kermit in DOSBox
I start the DOSBox program and the autoexec code is executed

Inside DOSBox I navigate to the Kermit folder, and start Kermit. 

To prove that Kermit has detected the COM port, I get the COM port status with `show communications`.

```powershell
cd msk316
msk316.exe
show communications
```

![image](https://james-hunter.github.io/pictures/20250420_1.jpg)

Since there is a COM1 port (serial1 in DOSBox which is actually COM10 in Windows) Kermit detects the available port automatically.

The output shows that the COM port is already available. Still, here are some commands which you might have used to tweak the COM port:

```powershell
set port 1
set speed 9600
set flow-control off
```

But I don't need to use those; I can skip those commands and join directly to the serial port. 

I can then control the external device over the serial port.

```powershell
connect
```

![image](https://james-hunter.github.io/pictures/20250420_2.jpg)

# Tips
You can scroll the terminal. 

`Ctrl`+`PgUp` to go up one line

`Ctrl`+`PgDown` to go down one line


