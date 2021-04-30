---
layout: post
title: "Using USB to RS232 Cable with Mac Terminal"
date: 2016-03-16
tags: hardware
redirect_from:
  - /using-usb-to-rs232-cable-with-mac-terminal/
  - /using-usb-to-rs232-cable-with-mac-terminal
comments: true
hidden: false
---

![USB to RS232](/assets/images/2016/usb-to-rs232_1.jpg)

I don't think there is a modern laptop that has serial ports, but many networking devices still use them for local connection. It happens rarely, but I need to use console port from time to time. I knew how to use the adapter on Windows systems, but on OS X it's a little different. Here's the process:

## 1. Get USB to RS232 adapter or cable

I bought [König USB-Serial conversion cable](http://www.konigelectronic.com/en_us/computer/connectivity/678851) for 11.30 € (unfairly expensive) from local electronics store. When buying the cable, take a look at the chipset if it is supported on OS X. My cable had [PL2303TA](http://www.prolific.com.tw/US/ShowProduct.aspx?pcid=41) chip developed by Prolific and supported on most of the operating systems. Another popular choice would be to look at the cables with [FTDI](http://www.ftdichip.com/Products/Cables/USBRS232.htm) chips.

## 2. Download and install drivers
It may depend on the system you're going to use the cable, but most of the times I don't recommend using drivers from CD that came with the cable, because they may be already deprecated.

Open [this link](http://www.prolific.com.tw/US/ShowProduct.aspx?p_id=229&pcid=41) to get the newest Prolific drivers.

Installing the drivers is straight forward – open `.pkg` file and continue through the process. Installer will ask to reboot your computer, so go ahead and restart the system.

## 3. Check if the drivers are properly installed
Go to System Preferences > Network and make sure new device called USB-Serial Controller D is in the list. It should look similar to this:

![Network Preferences](/assets/images/2016/usb-to-rs232_2.png)

Now open Terminal and type in this command:

    ls /dev/cu.*

The command should list all connected CU (Call-Up) devices. Here's the result on my system:

    /dev/cu.Bluetooth-Incoming-Port	/dev/cu.usbserial

In this example serial device is identified as `/dev/cu.usbserial`.

## 4. Connect to your device
When you have the right name of serial device and checked that drivers are properly installed, you can try connecting to any router, switch or other device that uses console port.
No additional software on Mac is needed to connect to console ports, because `screen` is already installed.

Open Terminal and type in this command to connect:

    screen /dev/cu.usbserial 9600

After executing this command, I got into my HP 2920 24G switch and followed further instructions to login.

To quit `screen` press Control-\ on your Mac keyboard. For more information about `screen` usage, refer to manual by typing `man screen` in new Terminal window.
