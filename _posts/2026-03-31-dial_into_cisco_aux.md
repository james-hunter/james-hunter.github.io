---
layout: post
author: jhunter
title: Dial into cisco AUX port
categories:
- networking
date: 2026-03-31
---

# Source 

[cisco.com](https://www.cisco.com/c/en/us/support/docs/dial-access/asynchronous-connections/10318-mod-aux-exec.html)

CCNP(TM) Remote Access Study Guide (Exam 640-505) by Syngress Media, 2000

# Summary
I passed the cisco CCNA certificate in Augut 2018. In preparation for the test I learned how to configure cisco 1921 IOS routers.

These routers have ethernet ports, console ports for configuring and also 'aux' ports. The learning materials I read never went into detail about the aux port, and I only ever used it as a secondary console port directly connected to my PC over a rollover cable.

For fun I googled what the aux port is for, and I see that you can use it to acces the cisco CLI out of band.

The sources at the top of this page explained to me how to dial into the CLI of a cisco IOS router using the aux port.

# Lab
After reading these resources, I collected these items to build a lab. The Fritz box behaves like a PSTN and includes 2 RJ11 ports. The phones on ports 1 and 2 can dial each other by dialing **1 or **2 respectively.

* cisco 1921 IOS router
* 2 * US Robotics 56k modem, each bought with DB9 (male) to DB25 (male) RS232 modem cable
* 2 * RJ11 cables
* USB to RS232 (male) cable
* FRITZ!Box Fon WLAN 7390
* A normal cat5 ethernet cable
* A cisco console rollover adapter
* A cab-25as-mmod: female rj45-to-db25 male adapter
* Plus a regular cisco rollover cable, for configuring the cisco device.

# Configurations
The modems I bought had different firmwares. I saw this using this command on both 56k modems:

ATI7

One did not include protocol v.92, so I needed to configure the other to ignore that protocol, using these commands in the modem. Cards on the table, this was the one place where AI helped me out.

```bash
AT&F
AT+MS=V34,0
AT&W
```

In the cisco router, I put in only these commands:

```bash
conf t
username jimmy privilege 15 password 0 secret1
line aux 0
 login local
 modem Dialin
 modem autoconfigure type usr_sportster
 transport input all
 stopbits 1
 speed 115200
 flowcontrol hardware
 exit
```

# Dialing-in
I plugged the list of items together. To dial into the cisco router from the computer side, I typed in this modem command:

```bash
ATDT**2
```

![image](https://james-hunter.github.io/pictures/20260331_1.jpg)

The dial-up sounds can be enjoyed at this link:
[mega.nz](https://mega.nz/embed/OKgWzZBR#fEZDfWjL3uhRDFU2cC3Oi4gyLriYwp1kCsny8957v1c)
