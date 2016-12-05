---
title: Connect your Raspberry Pi to Cellular
nav_sort: 1
autotoc: true
layout: tutorial.hbs
preview_image: "/wp-content/uploads/2015/04/rpi.jpg"
---

### Introduction

This tutorial walks through connecting a Raspberry Pi to the internet via
cellular using the Huawei E303 USB modem.

{{{ image src="http://hologram.io/wp-content/uploads/2015/04/rpi.jpg"
    alt="Raspberri Pi photo" }}}

Before starting, make sure you have the following components available:

-   Raspberry Pi 2 Model B (or newer)
-   8GB or greater MicroSD card
-   2.4A USB power supply and cable
-   Huawei e303 3G USB Dongle -- available from the [Hologram Store](/store)
-   Hologram SIM -- available from the [Hologram Store](/store)
-   Monitor with HDMI input
-   USB Mouse and Keyboard

This is a simple method that will get you on the network as quickly as possible.
If you need more advanced networking capabilities such as inbound connections to
the Pi, see the tutorial [SSH into your Raspberry Pi via
Cellular](/docs/tutorials/raspberry-pi-ssh).

This tutorial requires the use of the GUI (which means monitor, keyboard,
and mouse) and cannot be easily automated.
It also requires you initially connect the Pi to the internet via Ethernet or
Wi-Fi in order to download software packages.

### Instructions

#### Activate Your SIM

{{{ include "activate-sim" }}}

#### Set up the Pi

Download and install the Raspbian image to your micro SD card
following the instructions on the [Raspberry Pi
site](https://www.raspberrypi.org/documentation/installation/installing-images/README.md).

For the initial configuration, connect the Pi directly to a keyboard and
monitor, and connect to the internet via Ethernet or Wi-Fi. After you've 
confirmed that the cellular connection is working, you can then
run it "headless" anywhere that has cellular coverage.

#### Connect with the E303 modem

Follow the instructions in our [e303 HiLink
guide](/docs/guide/connect/e303#hilink-mode-linux-) to configure the E303 modem.

After completing the setup, disable the Pi's other network interfaces to ensure
that all network communication goes over cellular:

```bash
sudo ifconfig wlan0 down
sudo ifconfig eth0 down
```

Then, test your connection by pinging hologram.io:

```bash
ping -c3 hologram.io
```

If you don't receive any errors, you are now connected to the internet via the
Hologram network!

