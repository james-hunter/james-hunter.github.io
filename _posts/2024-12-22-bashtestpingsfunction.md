---
layout: post
author: jhunter
title: Test ping function with bash 
categories:
- bash
date: 2024-12-22
---

# Summary
Often when I set up a new linux computer in a network, I want to check the network connectivity.

There are lots of tools such as `ping`, `mtr` and `ip route`. 

I frequently just want to see if connectivity is really etablished or otherwise after making tweaks to my network.

This post presents a function in bash which lets me see if routes are really accessible from various interfaces on my computer.

# The function
```bash
#!/bin/bash

function james-invoke-ping(){

        #param1 is the sources
        #param2 is the dstinations

        local -n source1=$1
        local -n destination1=$2

        for i in ${source1[@]}
        do
                for j in ${destination1[@]}
                do
                        if [ "$(ping -I $i $j -c 1 | grep "100% packet loss")" != "" ] ; then
                                status="failed"
                        else
                                status="succeeded"
                        fi
                        echo -e "$(date)\tOn $(hostname) ping from ${i}\tto\t${j}\t${status}"
                done
        done
}

```

# Using the function
The function uses arrays of IP addresses.

The first parameter is all the IP addresses of the local computer: for example:
```bash
sources=(10.8.0.1 192.168.2.199)
```
The second parameter is all the IP addresses that I want to reach.
```bash
destinations=(10.8.0.2 192.168.186.170 192.168.2.1 192.168.186.1 8.8.8.8)
```
Here is how I might use the function:
```bash
sources=(10.8.0.1 192.168.2.199)
destinations=(10.8.0.2 192.168.186.170 192.168.2.1 192.168.186.1 8.8.8.8)
james-invoke-ping sources destinations
```
Giving this example result:
```bash
root@server1:~# james-invoke-ping sources destinations
Sun Dec 22 11:35:18 PM UTC 2024 On server1 ping from 10.8.0.1     to      10.8.0.2        succeeded
Sun Dec 22 11:35:18 PM UTC 2024 On server1 ping from 10.8.0.1     to      192.168.186.170 failed
Sun Dec 22 11:35:18 PM UTC 2024 On server1 ping from 10.8.0.1     to      192.168.2.1     succeeded
Sun Dec 22 11:35:18 PM UTC 2024 On server1 ping from 10.8.0.1     to      192.168.186.1   failed
Sun Dec 22 11:35:18 PM UTC 2024 On server1 ping from 10.8.0.1     to      8.8.8.8 succeeded
Sun Dec 22 11:35:18 PM UTC 2024 On server1 ping from 192.168.2.199        to      10.8.0.2        succeeded
Sun Dec 22 11:35:18 PM UTC 2024 On server1 ping from 192.168.2.199        to      192.168.186.170 failed
Sun Dec 22 11:35:18 PM UTC 2024 On server1 ping from 192.168.2.199        to      192.168.2.1     succeeded
Sun Dec 22 11:35:18 PM UTC 2024 On server1 ping from 192.168.2.199        to      192.168.186.1   failed
Sun Dec 22 11:35:18 PM UTC 2024 On server1 ping from 192.168.2.199        to      8.8.8.8 succeeded
```
