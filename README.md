## Raspberry PI TVHeadend Server
\#raspberrypi #rotary #lcd #flask #sse #tvheadend #oscam


###Introduction:

This RPI TVHeadEnd Server is a platform based on Raspberry PI hardware, which creates a headless DVB-S2 Tuner and streams the video to any network client. This platform supports conditional access via “oscam” server.


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

Prepare rotary switch, for example 
![rotary switch](https://raw.githubusercontent.com/petervflocke/rpitvheadend/master/rotary-switch.png  "Rotary Switch")

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


