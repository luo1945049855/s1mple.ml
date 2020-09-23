---
title:  "Way to uniquely identify a windows machine"
date:   2020-09-16
categories: Windows
---

## MAC address

Using MAC address to identify a computer isn't reliable. Because each computer can easily have multiple MAC address from multiple network adapters(Ethernet, Wi-Fi, Bluetooth, even virtual network adapters).

## wmic csproduct get UUID

That UUID is the best way to ID a machine, it exists in Windows, Mac and many other platforms. It is 32 characters in length, a universally unique identifier. You can run the above wmic command to get it.

But wait, that’s not always the case. Some of the motherboard vendors might not include this UUID in their motherboard, which is quite common on those non-OEM machines. In that case, when you use wmic trying to retrieve it you will get all FFFFFFFF-FFFF-FFFF-FFFF-FFFFFFFFFFFF as the ID back.

## Windows Product ID

Activation of Windows may change the value.

## Hard Drive's serial number

Using the SN of the HD volume actually works, though when testing it on various systems, I've sometimes needed more privileges, so I stopped pursuing that path. It fails when running from (Linux) Live-OS.

## System.Identity.UniqueID

[System.Identity.UniqueID](https://docs.microsoft.com/en-us/windows/win32/properties/props-system-identity-uniqueid)

not much infomations.

## CPUID

CPUID isn't unique. At least, not since last ~10 years. Intel deactivated that feature long ago. Usually some zero-ed string is returned, when someone tries to read the specific variable.

## HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Cryptography\\MachineGuid"

MachineGuid is unique unless Windows is restored from a backup or clone.

## Reference

1. [The Best Way To Uniquely Identify A Windows Machine](https://www.nextofwindows.com/the-best-way-to-uniquely-identify-a-windows-machine)
2. [Error while getting machine id in Windows 10 #2](https://github.com/denisbrodbeck/machineid/issues/2)
