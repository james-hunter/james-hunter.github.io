---
layout: post
author: jhunter
title: Simple POE configuration of an HPE comware switch
categories:
- networking
date: 2025-02-16
---

# Introduction
Lots of network devices get their power over ethernet, rather than via a power plug.

I acquired a second hand from HPE which can supply POE to devices, with the model name 'HP A5120-48G-PoE+ EI Switch'.

![image](https://james-hunter.github.io/pictures/20250216_3.jpg)

This brief blog shows how to use this switch to delivery POE to a network device.

# Configuration
The HPE switch can be configured via the console port with a normal console cable.

![image](https://james-hunter.github.io/pictures/20250216_1.jpg)

I can enable POE from the console with these simple commands:

```bash
interface GigabitEthernet1/0/48
 poe enable
```

That's it, the POE is now available. However, if you foresee you will need to do lots of POE configurations, then in my opinion it is a bit easier to use the switch's web interface.

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

To enable POE on an interface:

1. On the web interface I navigate to the POE area
1. I select Port Setup.
1. I click on the interesting interface, select 'enable' from the options, and click on apply.

![image](https://james-hunter.github.io/pictures/20250216_4.jpg)

# Result
Now the POE is enabled, I can power a device which requires POE, like this cisco access point.

![image](https://james-hunter.github.io/pictures/20250216_2.jpg)
