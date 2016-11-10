## Raspberry PI TVHeadend Server
\#raspberrypi #rotary #lcd #flask #sse #tvheadend #oscam


###Introduction:

This RPI TVHeadEnd Server is a platform based on Raspberry PI hardware, which creates a headless DVB-S2 Tuner (DVBSky S960 USB Box) and streams the video to any network client. This platform supports conditional access via “oscam” server.


###Hidden Agenda - local and remote control examples
You can either recreate this to have similar platform or rip out three  “goodies” for your own Raspberry Pi projects. Respective two github repositories provide you:
Python **Rotary Encoder Knob** class based on interrupts (to preserve processor's power and save energy). Example of **simple pygame interface** using above rotary class encoder and cheap  SainSmart 1.8" TFT LCD display. All this together gives you a lean system with as little as possible components and GPIO lines to leverage your projects with powerful user control. 

Local control is good, remote control via mobile, tablet or computer is even better. There are plenty of projects connecting processes on RPI with mobile apps. Here you will be given a plain piece of python code to create an interactive HTML5 page you can open in any modern browser and control your RPI remotely.

##Quick links:
- [Go to local control via rotary and LCD](https://github.com/petervflocke/rotaryencoder_rpi) 
- [Go to web based RPI Server App](https://github.com/petervflocke/flasksse_rpi) 
- Screenshots :)
Continue reading hardware part:


##Steps to prepare the hardware platform
###1. Hardware:

####Prepare rotary switch,
for example Alps EC11 Encoder. Solder resistors and capacitors directly on the switch and protect them with heat shrink tubing. Use below wiring digram.
![rotary switch](https://raw.githubusercontent.com/petervflocke/rpitvheadend/master/rotary-switch.png  "Rotary Switch")

####Prepare display:
I used cheap [SainSmart 1.8″ Color TFT LCD Display](http://www.sainsmart.com/sainsmart-1-8-spi-lcd-module-with-microsd-led-backlight-for-arduino-mega-atmel-atmega.html) . This 1.8″ display has 128 x 160 pixels resolution and is capable of displaying 18-bit colors. Measure: 5 cm x 3.5 cm x 0,6 cm thick.
![SainSmart 1.8 ST7735R TFT LCD Module](https://raw.githubusercontent.com/petervflocke/rpitvheadend/master/display.jpg  "SainSmart 1.8 ST7735R TFT LCD Module")
My display has fixed back-light (there are versions with PWN backlight).  The Micro-SD-Card reader is left unused.
The display is driven by a ST7735R controller and is well supported by new linux kernel. Refer to the software installation part.
For display wiring refer to the picture
SainSmart 1.8″ Color TFT LCD Display wiring:
![SainSmart 1.8″ Color TFT LCD Display.](https://raw.githubusercontent.com/petervflocke/rpitvheadend/master/SaintSmartDiagram.png  "SainSmart 1.8″ Color TFT LCD Display.")
Note: When the screen is not used I can switching the power line – it still displays (magic or powering via data lines) dark screen – still testing.

####Prepare the power supply:

Let's power the RPI using dedicated pads instead via micro USB. This let us avoid cables sticking out of the box and all issues with too thin USB power cables, etc.

Following the RPI forum: https://www.raspberrypi.org/forums/viewtopic.php?f=28&t=137305

You can solder the +5V wire to the points marked PP1 or PP2 and ground wire to PP3, PP$, PP5 or PP6. That will be the same as if the power has been supplied through the micro USB.

![RPI Power](https://raw.githubusercontent.com/petervflocke/rpitvheadend/master/rpi-power.jpg  "RPI Power")

Easymouse 2 is powered via USB from RPI.

Add a switch for 5V for RPI for your convenience.

USB tuner requires external 12V, this will be switched on/off by a dedicated relay module, which I have laying around from “Arduino time”. The better way would be a switch based on mosfet transistor. 

The USB Tuner shell be switched off in case of no use/no streaming to save energy.

####Hands-on high level wiring diagram of all elements
![High Level Wiring](https://raw.githubusercontent.com/petervflocke/rpitvheadend/master/highlewelwiring.jpg  "High Level Wiring")

> **Note:**
>For your reference I used as a housing an old D-Link Di-624 Wlan Router. Power socket is cut out from DVD Player board to get a nice stable 4 pin molex / amp connector. Just cut out the size you need. De-solder unwanted elements (I left some capacitors) tracked down the paths from the molex socket and solder 12V and 5V power cables.

**Used GPIO for rotary the switch and relay**

GPIO | Header PIN | Switch
- | - | -
GPIO 0 | 11 | Rotary switch pin **A**
GPIO 1 | 12  | Relay input
GPIO 2 | 13  | Rotary switch pin **B**
GPIO 3 | 15  | Rotary push button **P1**
GND | 9 | Ground for rotary switch

**Used GPIO for the display**

GPIO | Header PIN | Display
- | - | -
3.3 VDC  | 1 | VCC
GND | 6  | GND
GPIO 14 | 23 | SCL
GPIO 12 | 19 | SDA
GPIO 5 | 18 | RS/DC
GPIO 6 | 22 | RES
GPIO 10 | 24 | CS



###2. Software: Download and install on sd card raspbian (jessie)
Connect your RPI to your network, power-on  and scan for new ip on your network to find newly booted rpi. Assuming your network segemnt is `192.168.0.0/24` you can find all ips

```sh
nmap -sn 192.168.0.0/24
ssh to pi@detdected.ip
```
finish instaltion running `raspi-config`enable SPI – will be needed by the display. After the first instllation always run 
```sh
sudo apt-get update
sudo apt-get upgrade
```
Change default name of the server from raspberry to any thing you want by modifying the files `/etc/hostname` and `/etc/hosts` and run:
```sh
sudo /etc/init.d/hostname.sh
```
Change dhcp to fixed ip (or do this on your router, servers should have fixed ip: in file `/etc/dhcpcd.conf`at the bottom add:

```
interface eth0

static ip_address=192.168.0.10/24
static routers=192.168.0.1
static domain_name_servers=192.168.0.1

interface wlan0

static ip_address=192.168.0.11/24
static routers=192.168.0.1
static domain_name_servers=192.168.0.1
```
Make the network settings corresponding to your home network!

[Install](https://www.raspberrypi.org/documentation/remote-access/ssh/passwordless.md)  if desired a passwordless ssh (this accelerate the work)

