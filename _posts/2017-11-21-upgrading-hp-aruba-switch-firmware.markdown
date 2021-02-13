---
layout: post
title: "Upgrading HP Aruba Switch Firmware"
date: 2017-11-21 18:00:00 +0300
tags: networking hardware
redirect_from:
  - /upgrading-hp-aruba-switch-firmware/
  - /upgrading-hp-aruba-switch-firmware
comments: false
hidden: true
---

![HP Aruba](/assets/images/2017/aruba_1.jpg)

Guide for upgrading HP Aruba (formerly ProCurve) switch firmware.

### Requirements
- macOS
- HP Aruba Switch

### 1. Start built-in `tftp` server on your Mac
`tftp` server will enable file transfers between macOS and Aruba switch.

Run the following command in Terminal:

    sudo launchctl load -F /System/Library/LaunchDaemons/tftp.plist

Symlink `tftp` source folder to some other folder in your home directory that you have full control over:

    sudo rm -rf /private/tftpboot
    mkdir /Users/username/tftpboot
    sudo ln -s /Users/username/tftpboot /private/tftpboot

Restart `tftp` server for changes to take effect:

    sudo launchctl unload -F /System/Library/LaunchDaemons/tftp.plist
    sudo launchctl load -F /System/Library/LaunchDaemons/tftp.plist

### 2. Backup current switch configuration
Before downloading configuration from switch, you have to create empty file and give proper permissions so your switch can write the configuration over it.

Create config file:

    cd /Users/username/tftpboot
    touch switch.conf
    chmod 766 switch.conf

Save startup configuration to file:

    write memory
    copy startup-config tftp 192.168.100.100 switch.conf

Don't forget to change the permissions back after successful file transfer.

### 3. Check current firmware version
In this example, `WB.16.02.0012` firmware uses `WB.16.03` boot ROM. It is upgraded automatically from the firmware image to the version that is required.
Make sure that you are upgrading from supported version. Sometimes you may need to install incremented backups to keep the configuration intact.

Check current flash:
```
HP-2920# show flash
Image             Size (bytes) Date     Version
----------------- ------------ -------- --------------
Primary Image    :    14233321 01/26/17 WB.16.02.0012
Secondary Image  :    14233321 01/26/17 WB.16.02.0012

Boot ROM Version : WB.16.03
Default Boot     : Primary
```

### 4. Download the latest firmware from HP
Open [this](https://h10145.www1.hp.com/downloads/ProductsList.aspx?smp=1) link and type in your product name to download the newest firmware image. In this example for [Aruba 2920 24G Switch](https://h10145.www1.hp.com/downloads/SoftwareReleases.aspx?ProductNumber=J9726A&lang=&cc=&prodSeriesId=) it's `WB_16_04_0009.swi`

Copy downloaded firmware to your `tftp` folder:

    cp Downloads/WB_16_04_0009.swi /Users/username/tftpboot

### 5. Upload and flash the firmware on secondary image
Primary Image: Default storage for switch software image.

Secondary Image: Additional storage for either a redundant or an alternate switch software image.

With the Primary/Secondary flash option, you can test a new image in your system without having to replace a previously existing image.
It is recommended to upgrade secondary image while booted into primary and after the upgrade boot into secondary to test if everything works well.[^1]

Copy firmware to the secondary image:

    copy tftp flash 192.168.100.100 WB_16_04_0009.swi secondary

You should see "The Secondary OS Image will be deleted, continue [y/n]?". Respond to the warning message by pressing `y`.

Now you should see "Validating and Writing System Software to Flash..."

Wait for the process to finish and reboot the switch to the new firmware:

    boot system flash secondary

Check if everything is working well and no configuration is lost with the new firmware.

### 6. Upload and flash the firmware on primary image
Repeat the same commands from Step 5, using primary instead of secondary.

    copy tftp flash 192.168.100.100 WB_16_04_0009.swi primary
    boot system flash primary

### 7. Validate the new firmware versions

```
HP-2920# sh flash
Image             Size (bytes) Date     Version
----------------- ------------ -------- --------------
Primary Image    :    16495314 10/13/17 WB.16.04.0009        
Secondary Image  :    16495314 10/13/17 WB.16.04.0009       

Boot ROM Version
----------------
Primary Boot ROM Version   : WB.16.03
Secondary Boot ROM Version : WB.16.03

Default Boot Image   : Primary
Default Boot ROM     : Primary
```

The system has been updated to the newest version!

[^1]: [Using Primary and Secondary flash image options on HP](http://h22208.www2.hpe.com/eginfolib/networking/docs/switches/common/15-18/5998-8158_bog/content/ch04s07.html)
