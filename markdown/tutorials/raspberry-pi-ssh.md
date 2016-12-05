---
title: SSH into your Raspberry Pi via Cellular
nav_sort: 2
autotoc: true
layout: tutorial.hbs
preview_image: "/wp-content/uploads/2015/04/rpi.jpg"
---

### Introduction

This tutorial walks through connecting a Raspberry Pi to the Hologram network,
and logging into it via SSH from any internet-connected computer.

Before starting, make sure you have the following components:

* Raspberry Pi 3 running latest Raspbian OS
* Hologram SIM card
* Huawei E303 USB Cellular Modem

The SIM card and E303 modem are both available from the [Hologram store](/store).

### Instructions

#### Activate your SIM

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

Open the terminal application to get to a command line interface, and follow 
the *Serial PPP mode* instructions in our [E303
guide](/docs/guide/connect/e303#serial-ppp-mode-linux-).

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


#### Establish an inbound tunnel with Spacebridge

By default, the Hologram network blocks inbound connections to cellular devices.
In order to connect to your Pi with SSH, you'll need to relay the connection through
Hologram's servers using the Spacebridge service.

Enable tunneling for your SIM in the Hologram Dashboard, and use the Spacebridge
client to forward a local port to the device's port 22 (the standard SSH port).
The [Spacebridge guide](/docs/guide/cloud/spacebridge-tunnel) describes this
process in more detail.

With the Spacebridge client running, you can now SSH to your Raspberry Pi by
connecting to localhost on the port you're forwarding to (e.g. 5000).

Linux and MacOS include a built-in command line SSH client, so from a terminal
you can simply run:

```bash
ssh -p 5000 pi@localhost
```

And enter the default Raspbian password 'raspberry' when prompted.

On Windows, download
[PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) and
open a session with hostname 'localhost', and port 5000 (or the local port you
configured). When prompted, enter the username 'pi' and password 'raspberry'.

