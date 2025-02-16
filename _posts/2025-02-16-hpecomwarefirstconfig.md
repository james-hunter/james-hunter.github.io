---
layout: post
author: jhunter
title: simple first configuration of an HPE comware switch
categories:
- networking
date: 2025-02-16
---

# Introduction
Lots of network devices get their power over ethernet, rather than via a power plug.

I acquired a second hand from HPE which can supply POE to devices, with the model name 'HP A5120-48G-PoE+ EI Switch'.

This brief blog shows how to use this switch to delivery POE to a network device.

# Configuration
The HPE switch can be configured via the console port with a normal console cable.

![image](https://james-hunter.github.io/pictures/20250216_1.jpg)

Enable the web console using these console commands:

```bash
system-view
int vlan 1
ip address dhcp-alloc
ip http enable
local-user james
 password simple password1
 authorization-attribute level 3
 service-type web
```

With this configuration I can connect an ethernet cable to my home router, and the switch gets an IP address.

I connect to the switch's web interface using the username and password I just defined.

To enable POE on an interface, on the web interface I navigate to the POE area, select the interesting interface, and click enable.

# Result
Now the POE is enabled, I can power a device which requires POEn like this cisco access point.

![image](https://james-hunter.github.io/pictures/20250216_2.jpg)
