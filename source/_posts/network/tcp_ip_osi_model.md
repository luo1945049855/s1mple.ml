---
title:  "TCP/IP vs OSI Model"
date:   2020-10-10
categories: Network
---

## OSI Model

`The Open System Interconnection(OSI)`
defines a logical network and effectively describes computer packet transfer by using various layers of protocols.

## TCP/IP Model

TCP/IP stands for `Transmission Control Protocol/Internet Protocol`.
It is specifically designed as a model to offer highly reliable and end-to-end byte stream over an unreliable internetwork.

## Characteristic And Difference

![TCP/IPvsOSI.png](/images/TCPIPvsOSIM.png)

1. Physical: Hardware transmits signals over media. (e.g. cable, RJ45)

2. Data Link: Binary to signals and vice versa. (e.g. MAC, ARP, switches)

3. Network: Packet-Forwarding, Routing, and Addressing. (e.g. IP, DNS, routers)

4. Transport: Error-Checking and Data-Recovery. (e.g. TCP, UDP, port numbers)

5. Session: Establish and terminate connections between devices. (e.g. Syn/Ack)

6. Presentation: Translate application formatting to network formatting and vice versa. (e.g. encryption, ASCII, PNG, MIDI)

7. Application: User Interact. Browser, Skype, Outlook. (e.g. SNMP, HTTP, FTP)

One popular mnemonic, starting with Layer7, is "All People Seem To Need Data Processing".
The other one start with Layer1, is "Please Do Not Throw Sausage Pizza Away".

## Reference

1. [TCP/IP vs OSI Model: What's the Difference?](https://www.guru99.com/difference-tcp-ip-vs-osi-model.html)

2. [The Network Layers Explained](https://www.plixer.com/blog/network-layers-explained)
