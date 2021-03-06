---
title: Serial Connectivity
nav_sort: 2
autotoc: true
layout: guide.hbs
---

The Dash [Quick Start Guide](/docs/guide/dash/quick-start) walks through the easiest way to establish a
serial connection between the Dash and your PC: Using USB and the Arduino IDE's
serial monitor. There may be cases where these tools aren't available or
feasible, so this guide describes some alternative methods to connect your PC
to the Dash.

### USB-to-TTL Converter

Instead of using the Dash's onboard Micro USB port, you may communicate over the TTL
UART interface through a [USB-to-TTL
converter](http://www.amazon.com/FT232RL-Serial-Adapter-Module-Arduino/dp/B00HSX3CXE/).
You may want to use this method if you plan
to interface with another device over TTL and want to verify that you can
communicate over those pins.

You must use the *Serial2* interface (UART pins RX2 and TX2) because the
*Serial1* interface is used for communicating with the Hologram Cloud. The
appropriate pins are as follows:

* **RX2**: Pin L06
* **TX2**: Pin L08

Using header hook-up wire,

* Connect the Dash's *GND* pin to the TTL converter's *GND* pin
* Connect the Dash's *RX2* (*L06*) pin to the TTL converter's
  *Tx* (or *TXD*) pin
* Connect the Dash's *TX2* (*L08*) pin to the TTL converter's
  *Rx* (or *RXI*) pin
* Connect the TTL converter to the computer using a USB cable

Note that the Dash's receiving (RX) pin connects to the converter's transmitting
(TX) pin, and vice versa. If you don't see data when you expect to, try swapping the
two connections. Sometimes TTL converters can label pins in reverse!

The completed connection should look something like:

{{{ image src="/wp-content/uploads/2015/10/konekt_dash_pro_debugging_ttl.png"
                   alt="TTL connection on Dash Pro" }}}

With the pre-loaded program, the Dash will forward any messages sent from the 
UART interface to the Hologram Cloud. When writing your own programs, make sure
to use the *Serial2* interface in your Arduino code. [This
example](https://github.com/hologram-io/hologram-dash-arduino-examples/blob/master/konekt_dash_helloworld/konekt_dash_helloworld.ino) 
illustrates sending and receiving data over the various Serial interfaces.

### Alternate Terminal Emulators

Regardless of how you physically connect the Dash to your PC, it will be
accessible as a generic USB/Serial device. This means you can use any
serial interface application (also called *terminal emulator*) you 
wish to send/receive data. The Arduino IDE's serial monitor is cross-platform
and usually the easiest to set up. But for more advanced users this guide
describes a few alternatives.

#### Screen

*Screen* is a command line Linux/Unix application that can interface with
attached serial devices. It is installed by default on MacOS and most Linux
distributions. 

First, find the device file corresponding to your Dash:

MacOS:

```bash
ls /dev/cu.usbmodem*
```

Linux:

```bash
ls /dev/ttyUSB*
ls /dev/ttyACM*
```

If there are multiple devices listed, you can disconnect the Dash and re-run the
command to see which device disappears from the list.

Open a *screen* session at the default baud rate of 9600, e.g.:

```bash
screen /dev/ttyUSB4
```

{{#callout}}
On Linux, you must ensure your user has proper permissions to access the
serial interface. On Debian-based distributions, this can be accomplished with: 

``sudo adduser `whoami` dialout``
{{/callout}}

Any data sent from the Dash will be displayed in the terminal. Anything you type
in the terminal will be sent to the Dash. 

To close the session and quit *screen*, type *Ctrl-A* followed by *k*. Then
type 'y' at the prompt to confirm.

#### PuTTY

*PuTTY* is a terminal emulator for Windows. Download the latest version
[here](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Determine the COM port associated with your Dash: Open the device manager and
expand the *Ports (COM & LPT)* section. You should see a *USB Serial Port* entry
with a COM identifier, e.g. *COM3*. Note this identifier.

Open PuTTY and configure the connection as follows:

* **Connection type:** Serial
* **Serial line:** COM identifier as noted above
* **Speed:** 9600

Click *Open* to establish a connection to the Dash. 
Any data sent from the Dash will be displayed in the terminal. Anything you type
in the terminal will be sent to the Dash. 

### Raspberry Pi and Other Embedded PCs

The Hologram Dash can be used with its default program to send messages
from an embedded PC (or any other serial device) to the Hologram Cloud.

On the Linux device, add Hologram's udev rules to enable the proper permissions
and to disable ModemManager from taking over the device:

```bash
sudo wget -O /etc/udev/rules.d/85-hologram.rules \
  https://raw.githubusercontent.com/hologram-io/hologram-dash-arduino-integration/master/85-hologram.rules
```

Using a micro USB cable, connect the Dash to one of the USB ports on your
embedded PC. The Dash's serial interface will be available as `/dev/ttyACM0`.

Send a test message from the command line:

```bash
echo "Hello, Hologram!" > /dev/ttyACM0
```

You can also use a serial library in your programming language of choice to
communicate with the device. Here's a simple Python example:

```python
import serial
import sys

try:
   port = serial.Serial("/dev/ttyACM0", baudrate=9600, timeout=1)
except:
   print("FAIL, No USB Serial connected")
   sys.exit(1)

if not port.isOpen():
   self.log("Serial port wasn't opened")
   sys.exit(1)

port.write("TEST MESSAGE\n")
```
