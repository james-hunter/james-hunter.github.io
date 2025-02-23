---
layout: post
author: jhunter
title: Deutsche Telekom Speedport Entry 3 as a DSL modem
categories:
- networking
date: 2025-02-09
---

# Sources
[telekomhilft.telekom.de](https://telekomhilft.telekom.de/conversations/festnetz-internet/konfiguration-tipps-f%C3%BCr-cisco-876-router/668611fe4ae73561da06a47f)

[dslreports.com](https://web.archive.org/web/20150815202703/http://www.dslreports.com/faq/10952)

# Introduction
I love the way telecommunications developed into the internet. In the olden days a normal telephone connection could be used as an interface with the internet. This old (July 1982) article from the [wikipedia page for xmodem](https://en.wikipedia.org/wiki/XMODEM) gives a fun example of using hotel phones in various countries in order to set up an internet connection: [Osborne—Behind Guerrilla Lines](https://archive.org/stream/kilobaudmagazine-1982-07/Microcomputing_1982_July#page/n43/mode/2up)

Today it is not really necessary to know much about how a home router works in order to connect to the internet. In my case, I received a Speedport Entry 3 device from my internet provider Deutsche Telekom, and after powering it on, I went to it's web interface and typed in my credentials.

I hear that other home routers are personally pre-configured for each client, and you only need to connect the power.

The Speedport Entry 3 still lets me do things the hard way. In particular the device can function as a DSL modem.

The DSL modem allows me to use an external device as a point-to-point over Ethernet (PPPOE) client. Such devices include:

* cisco routers running IOS
* Windows computers

I found it a great way to learn about DSL and PPPOE, and also about how my own devices work practically.

This blog post is not intended as a complete configuration guide, but only a suggestion of what can be possible.

# Configure the Speedport Entry 3 as a DSL modem
The Speedport Entry 3 normally works as a normal home router, doing these functions:

* Telephone
* Wi-Fi
* Ethernet
* VPN, file server, print server etc...

When the DSL modem mode is activated, other functions are disabled.

First you should download the device's current configuration, under Settings, DSL modem.

Then in the same area you can click on 'Use Speedport as DSL modem'. The confguration is complete.

After a couple of moments the device will show only two white solid lights: Status and Link.

# Configuring your PPPOE client device
You need to configure your PPPOE client using the same credentials used in the Speedport Entry 3. You find this on the paperwork provided by your internet provider.

For Deutsche Telekom customers, this data looks like this:

| German | English | Format |
| :----- | :------ | :----: |
| Zugangsnummer, formally t-online number | Access number | 123456789012 |
| Ihr personliches Kenwort | Your personal password | 123456789 |
| Ihre Anschlusskunnung | Your connection number | 123456789012 |
| Mitbenutzernummer | Co-user number | 1234 |

These details make up your customer usernamelike this:

* `'connection number''access number'#'co-user number'@t-online.de`

So, my internet service provide password has the form, for example:

* 123456789012123456789012#1234@t-online.de

The password is just the personal password as above. The MTU is 1492. the communication protocol for the client is PPPOE. The authentication protocol is CHAP or PAP - I didn't yet test CHAP but PAP worked fine.

The interface that goes to the DSL modem must use VLAN 7.

There is unfortunately a fair bit more configuration to do. Above I've linked some sources which discuss configuring devices attached to DSL modems.

# Re-configuring the Speedport Entry 3 as a normal router
To set the device as normal router again, use a pencil or other thin object to hold the reset button on the back of the Speedport Entrt 3 for 5 seconds.

After some moments the lights will stop blinking.

Join to the device's web portal again and ignore the auto-configuration prompts, and go straight to uploading the saved configuration file from your computer.


