---
layout: post
author: jhunter
title: Preparing a Wireguard VPN from Telekom in ubuntu server
categories:
- networking
date: 2024-12-15
---

# Sources
Manage your own Speedport Smart 3 router from Telekom:
[http://speedport.ip](http://speedport.ip/html/login/index.html)

Web addresses for your router:
[https://www.noip.com](https://www.noip.com)

For the maximum VPN clients on the Speedport Smart 3:
[https://telekomhilft.telekom.de](https://telekomhilft.telekom.de/t5/Festnetz-Internet/Max-Anzahl-Geraete-im-Wireguard-VPN/td-p/4686094)

In-depth installing wireguard client on ubuntu server
[https://serverspace.io](https://serverspace.io/support/help/how-to-install-wireguard-vpn-client-on-ubuntu-linux)

# Scope
This post describes how to prepare an ubuntu server as a client of the wireguard VPN server, which is being hosted by a Telekom Speedport Smart 3 router.

# Pre-requisite
You have a Speedport Smart 3 router.

You have spun up an ubuntu server. I am using ubuntu 24.04.1 LTS. 

The ubuntu computer can be in the LAN of the Speedport Smart 3 router from Telekom.

Alternatively the ubuntu machine can be in the WAN; in this case the ubuntu machine requires normal internet access.

# Prepare dynamic DNS
By default, the Speedport router will configure VPN clients using the router's IP address. 

Given that the public IP address of most routers changes every few days, in order to maintain conectivity, you should create a routeable web address for your Speedport router.

I used the website no-ip.com to do it. This website lets you create up to three web addresses for free.
1. Go to no-ip.com and sign in, or sign up for free if you don't already have an account.
1. Create a 'Dynamic DNS Hostnames', of the type 'A'. 
1. The hostname can be what you like. 
1. Note the hostname you create, as well of the no-ip login credentials.

# Configure dynamic DNS on the Speedport router
This section will configure the hostname you created inside the Speedport router.
1. Log into the web interface of the Speedport router. You can see your own router's admin password on the sticker on its under-side.
1. In the web interface, go to: Internet; Dynamic DNS.
1. Set these configurations:
 * Use dynamic DNS : tick
 * Provider : no-ip.com
 * Hostname : the hostname you created
 * email address or username : your email which you used for no-ip
 * Password : the password you used with no-ip.com
1. Save.
After a few moments, the world-wide DNS system will learn that your router can be accessible at the hostname address you configured.

# Prepare the VPN server
This section describes how to configure the Speedport router as the Wireguard VPN server.

The Speedport Smart 3 router can actually only have one VPN and also only one VPN client.

The router does let you download the client configuration file. This file defines the VPN client's IP address, however the client doesn't work if you try to change the client IP address within the configuration file.

The next model in the Telekom series, 'Speedport Smart 4', doesn't have this limitation and it does let you create many VPN's.

To create the VPN:
1. In the Speedport Smart 3 web interface, go to: Network; Virtual Network (VPN).
1. Click on 'Activate it now'.
1. You see the box 'Use VPN' appears and is ticked. You are invited to scan a QR code, but you will now click 'Download' to get the VPN client configuration file: Wireguard.conf.

![image](https://james-hunter.github.io/pictures/20241215_1.jpg)

# Install Wireguard on the ubuntu machine
Here are the commands to install wireguard in ubuntu server.

Optional: Update your ubuntu system.
```bash
sudo apt-get update && sudo apt-get upgrade
```
Install wireguard:
```bash
sudo apt install wireguard
```

# Configure the Wireguard VPN client
Put the Wireguard config file from the Speedport router - unchanged - here: `/etc/wireguard/Wireguard.conf`.

# Start the VPN client
Start the VPN client from linux: I think the conf file name must match this command: it's also going to be the interface name.
```bash
sudo wg-quick up Wireguard
```

# Verify the VPN is working
From the Speedport router, in the VPN section, you will see the status : `VPN access active WG-10-200-200-1 connected`.

From the ubuntu machine, you can use this command. The most interesting thing from its output is seeing: `latest handshake: 11 seconds ago` and seeing the transfer number going up: `transfer: 4.37 KiB received, 23.60 KiB sent`.
```bash
sudo wg show
```

# Shutting down the VPN client
To stop the VPN from the ubuntu client, use this command:
```bash
sudo wg-quick down Wireguard
```

# Some troubleshooting tips
## The VPN doesn't come up
These tips may help:

Run this command from the ubuntu computer to see if the hostname is correctly resolved (replace `hostname` with the hostname you created):
```bash
dig hostname
```
Check the ubuntu server has internet by doing a simply ping:
```bash
ping 9.9.9.9
```
Check the Speedport router has internet, by looking at its LEDs.

If you think the client configuration file was changed after it was downloaded, feel free at any time to delete the Wireguard VPN from the Speedport router, and start again.

If you like, you can use the exact same client configuration file within other compatible client devices. You can only do this to test your VPN, because the VPN will be unstable if two devices use the same VPN client configuration file at the same time.

# Features of the VPN
With this VPN you can use your remote ubuntu machine to communicate with devices within the LAN of the Speedport router.

Devices in the LAN will not be able to create connectons to the remote ubuntu machine.

Uses might include:
 * Joining to your personal file server.
 * Communicating to your smart home devices while you are away.
