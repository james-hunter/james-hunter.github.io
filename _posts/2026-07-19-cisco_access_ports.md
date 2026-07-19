---
layout: post
author: jhunter
title: VMPS to assign cisco switch ports
categories:
- networking
date: 2026-07-19
---

# Sources 

[networkworld.com](https://www.networkworld.com/article/765417/cisco-subnet-vmps-put-a-fork-in-it.html)

[sourceforge.net](https://sourceforge.net/projects/vmps/files/vmpsd/)

[linux.die.net](https://linux.die.net/man/1/vmpsd)

[chatgpt.com](chatgpt.com)

[gemini.google.com](gemini.google.com)

Cioara, J.D., Cavanaugh, M.J. and Krake, K.A. (2010) CCNA voice - official exam certification guide. Indianapolis, Ind: Cisco Pr. 

# Introduction

Reading older networking textbooks has lots of advantages. One reason is that old documentation lets me understand technologies that I have used for decades which is really satisfying, even if I don't use those technologies so much anymore. Recently I picked us the cisco CCNA voice guide from ebay for only 5 euros which was written around 2008-2010. I'm intending to refresh my understaning of SIP telephones.

Another reason for reading old documents is that sometimes you get lumped with old equipment and you just do not have the possibility to update its software.

I have just finished reading chapter 3 of the book which is called 'connecting IP phones to the LAN infrastructure'. Now it's obvious if you will use an IP phone on your desk that you need to connect an ethernet cable from the phone to a switch. It's less obvious how you will configure the switch's interface.

For fun and learning I have a cisco c2950 switch which works well but is stuck with an old operating system from 2002.

This little blog will describe one way of configuring a switch interface that works on my cisco switch but which is completely obsolete: VMPS.

# What is VMPS?

VMPS stands for VLAN policy membership server and it is a proprietary cisco protocol.

VMPS lets you configure VLANs of your switches' interfaces depending on the MAC address of your devices. 

VMPS is totally obsolete, for the reasons described in the Network World blog linked to the top of this page. Today we might use MAC address bypass or 802.1x but my cisco c2950 doesn't let me us MAB and my test device (a little iot thingy) doesn't speak 802.1x.

VMPS is UDP protocol, using port 1589.

# How does VMPS work?

In normal operation, a device will send ethernet frames to the connected switch. The switch uses IP to send VMPS query protocol (VQP) messages to the VMPS server. If the endpoint is known to the VMPS server, the VMPS server can respond to the switch with the VLAN that the end point should be in.

The VLAN needs to be configured in the switch already.

# cisco c2950 and VMPS

For this study, I use these computers:

* an iot device as the end point
* cisco c2950 switch with IOS 12.1
* ubuntu server 24.04

![image](https://james-hunter.github.io/pictures/20260719_1.jpg)

# cisco c2950 configuration

This configuration fragment:

* Gives the switch layer 3 communication,
* Tells the switch where the VMPS server is,
* Enables interface fa0/11 to use VMPS,
* Statically adds vlan 20.

```ios
!version 12.1
vmps server 192.168.2.195 primary
interface FastEthernet0/11
 switchport access vlan dynamic
 no ip address
 spanning-tree portfast
!
interface Vlan1
 ip address 192.168.2.7 255.255.255.0
 no ip route-cache
!
ip default-gateway 192.168.2.1
vtp domain mydomain
vlan 20
```

# VMPS server configuration

Here is how I installed this reverse engineered version of VMPS '`vmpsd`' from sourceforce.

```bash
#download and extract
wget https://downloads.sourceforge.net/project/vmps/vmpsd/1.4.06/vmpsd-1.4.06.tar.gz
tar xf vmpsd-1.4.06.tar.gz
cd vmpsd-1.4.06

#get build essentials
sudo apt update
sudo apt install build-essential

#install the code
./configure
make
sudo make install

#I add my endpoint device in the VMPS database
#the vmps domain in the vlan.db must match the vtp domain on the cisco switch.
#the MAC address should be written with small letters and use this dot format.
vi ~/vmpsd-1.4.06/vlan.db
vmps domain mydomain
address ab12.cd34.ef56 vlan-name VLAN20

#start vmpsd with maximum logging
sudo vmpsd -d -f ~/vmpsd-1.4.06/vlan.db -l 0xFFFF
```

# Troubleshooting

Without special debug commands, you will see if the VQL failed. For example, this is what happens when the MAC is unknown to the VMPS database.

```ios
#on cisco
00:06:54: %VQPCLIENT-2-DENY: Host ab12.cd34.ef56 denied on interface Fa0/11

#on vmpsd log
DENY: ab:12:cd:34:ef:56 -> NONE, switch (unknown) [192.168.2.7] port Fa0/11
```

This other example shows when the MAC exists in the VMPS database but the vlan is unknown to the cisco switch.

```ios
#cisco
00:11:25: %VQPCLIENT-3-VLANNAME: Invalid VLAN (JAMES) in response
#on vmpsd log: VMPS seems happy about it.
ALLOW: ab:12:cd:34:ef:56 -> JAMES, switch (unknown) [192.168.2.7] port Fa0/11
```

vmpsd shows me when the endpoint is denied: this example was because I had mismatched vtp domain on cisco and vmps domain in vmpsd.

```bash
DOMAIN MISMATCH: ab:12:cd:34:ef:56 -> --NONE--, switch (unknown) [192.168.2.7] port Fa0/11
```

You can take a deep dive into the debugging thanks to these commands:

```ios
#cisco
show interface status
show vmps statistics
show vtp status
show interfaces fa 0/11 swi

#show interfaces fa 0/11 swi
Name: Fa0/11
Switchport: Enabled
Administrative Mode: dynamic access
Operational Mode: dynamic access
Administrative Trunking Encapsulation: dot1q
Operational Trunking Encapsulation: native
Negotiation of Trunking: Off
Access Mode VLAN: 20 (VLAN20)
Trunking Native Mode VLAN: 1 (default)
Administrative private-vlan host-association: none
Administrative private-vlan mapping: none
Operational private-vlan: none
Trunking VLANs Enabled: ALL
Pruning VLANs Enabled: 2-1001
```

The optional logging in vmpsd will reveal much about what the server is thinking, and if things are working as expected. Here is an an example good response.

(A bugbear of mine is that they never settled on one way to write MAC addresses.)


```bash
VQP Request
Unknown: 1
Request Type: 1
Response: 0
No. Data Items: 6
Sequence No.: 54
Client IP address: 192.168.2.7
Port name: Fa0/11
Vlan name: VLAN20
Domain name: mydomain
MAC address: ab12cd34ef56
ALLOW: ab:12:cd:34:ef:56 -> VLAN20, switch (unknown) [192.168.2.7] port Fa0/11
```

# Concluding

I'm really happy that I can still get some old equipment to work.



