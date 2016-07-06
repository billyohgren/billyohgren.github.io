---
layout:     post
title:      "How to copy VMDK files from USB (vmfs) in ESXI 5.5"
subtitle:   "What sounds like an easy task"
date:       2016-07-05 06:20:00
author:     "Billy"
header-img: ""
---
## Background
One day one of my hard drives inside of my HP Microserver G8 decided to give up on me and I lost all of my virtual machines. It could have been worse though, it could have been all of my photos!
I asked myself why why why haven't I used raid? 
After a quick price watching I bought two 5TB drives (WD Blue with modified parking time to 300 seconds, just like WD Red) and decided to finally do this.
After removing the old drives, installed the new ones and switch the setting in BIOS for AHCI/RAID to RAID I was going to create a new RAID array.

ESXi booted up as normal and I created a new data store for my raid.
All good, except for my old files and virtual machines that I assumed I could just access via USB directly inside of ESXi. And once again, "assumption is the mother of all f***-ups."

## Problem
There's no way to access a USB disk directly from ESXi.

## Solution
1. Install a new VM (I called it migrate).
2. Install vmfs-tools (http://mirrors.kernel.org/ubuntu/pool/universe/v/vmfs-tools/vmfs-tools_0.2.5-1_amd64.deb).
3. Mount your USB case with your vmfs formatted drive inside.
4. Enable ssh access in ESXI.
5. Use scp to copy your folder containing the VMDK image to your ESXI host. (I wasn't able to use rsync, otherwise I would have chosen that instead).
6. Done!

## Posible solutions (I don't know if they will work)
1. The easiest would be to lend/buy an identical server, or maybe any other server running ESXi and AHCI instead of RAID.
2. Buy a SATA controller card that's supported by ESXi.
3. Install the open source vmfs driver on a windows machine and upload the files via vSphere.

## UPDATE:
I was able to scp all of my virtual machines to my ESXi data store and add them to my inventory but when I tried to send one of my 2TB vmdk files scp was just stalling. I tried a bunch of solutions I found on stack overflow without any luck.

After som googling I found this blog post about installing rsync on esxi https://damiendebin.net/blog/2013/12/06/esxi-5-dot-1-and-rsync/. I wasn't able to wget that binary inside of my ESXi machine so I downloaded it on my virtual migrate machine instead and sent it via SCP to the ESXi host.

Passing the rsync path to the host enabled me to send huge files :) ETA for a 2TB vmdk on my home network is around 3.5 hours.

rsync --rsync-path=/tmp/rsync/rsync-static -avP PATH-TO-VMDK-FOLDER-ON-MIGRATE-MACHINE USER@ESXI-HOST-IP:/vmfs/volumes/RANDOM-NUMBER-FOR-MY-DATA-STORE

That's it!