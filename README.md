rpilights
==========

![](misc/title.jpg)

Software for LED window display such as the one in [this video](https://www.youtube.com/watch?v=n9-j6oeJJw4).

### Hardware

Lights [here](https://www.amazon.com/Diffused-Digital.../dp/B00B4UKG2W).  This link may be
outdated at some point as the vendor likes to play
Feng Shui with his listings.  Also, be aware that the delivery times might sometimes be very long, like several months,
so plan ahead!

If the link is outdated (likely), look for these keywords: 

* WS2811
* 12mm
* Diffused
* RGB
* LED
* Round
* IP68
* DC 5V

Either mount in pegboard (holes every 2 inches drilled out to 12mm) or in 1/2 inch grid hardware cloth.

You will an external 5v power supply to power the lights if there are more than 100 or so.
I use [this one](https://www.amazon.com/dp/B00EZJFMKU).
The +5v and GND will need to be
connected to the power wires around every 200 lights due to internal resistance of the wires and circuitry.  Otherwise you will
see the intensity of the lights dim noticeably at the far end.

Also, unless you boost the Pi's GPIO 3v signal to 5v, make sure the signal wire is very short, a half foot or a foot at most.
Otherwise the lights will flash randomly.

See the "Limitations" info in the README for the rpi\_ws281x submodule -- on some Pis, the sound submodule keeps the lights from
working and needs to be disabled.

It is difficult to figure out which end of the light strand is to receive the signal.  Peer into the lights to see if you can
make out which side of the internal circuit board has the labels +5V, DIN (or DI), GND vs. +5v, DOUT (or DO), GND.  The waterproof
resin that fills each bulb is hazy and distorted, so this can be quite a challenge.  Sometimes there is an arrow that points away
from the wires that are to receive the signal.

### Software

Install into the directory /home/pi/rpilights:

	sudo apt-get update
	sudo apt-get install git
	sudo apt-get install imagemagick
	cd /home/pi
	git clone --recursive git://github.com/yggdrasilradio/rpilights.git

The command "make" should build the "rpilights" command, but it won't work properly until you configure the map.txt file
to reflect the physical layout of your LED display.

The "snow" animation vertically scrolls the graphic data in the file images/snow.gif.rgb.

The "valentines" animation horizontally scrolls the graphic data in the file images/valentines.gif.rgb.

The "stpatricks" animation horizontally scrolls the graphic data in the file images/stpatricksday.gif.rgb.

The .rgb files are generated from the script gif2rgb (requires installation
of ImageMagick) via the commands:

	gif2rgb snow.gif
	gif2rgb valentines.gif
	gif2rgb stpatricksday.gif

Make your own files with the dimensions of your display, as the existing data probably doesn't match those dimensions.

### map.txt file

The "map.txt" file describes the physical layout of your LED lights as an ASCII picture, viewed from the back of the display.
The existing map.txt file is for a complicated two-channel multi-panel display that I have in my four basement windows right
now.  Please edit it to reflect the arrangement of your lights:

	o		LED lights
	| and --	wires
	0 and 1		The first LED light on channels 0 (pin 18) and 1 (pin 19)
	.		(dot) Unused light position (gaps between windows)

For example, a simple 10x10 LED panel might look like this:

	o--o  o--o  o--o  o--o  o--o
	|  |  |  |  |  |  |  |  |  |
	o  o  o  o  o  o  o  o  o  o
	|  |  |  |  |  |  |  |  |  |
	o  o  o  o  o  o  o  o  o  o
	|  |  |  |  |  |  |  |  |  |
	o  o  o  o  o  o  o  o  o  o
	|  |  |  |  |  |  |  |  |  |
	o  o  o  o  o  o  o  o  o  o
	|  |  |  |  |  |  |  |  |  |
	o  o  o  o  o  o  o  o  o  o
	|  |  |  |  |  |  |  |  |  |
	o  o  o  o  o  o  o  o  o  o
	|  |  |  |  |  |  |  |  |  |
	o  o  o  o  o  o  o  o  o  o
	|  |  |  |  |  |  |  |  |  |
	o  o  o  o  o  o  o  o  o  o
	|  |  |  |  |  |  |  |  |  |
	0  o--o  o--o  o--o  o--o  o

The format of the map.txt file is very fussy; it can't have comments in it, blank lines at the beginning, the spacing has to be exactly like
the examples, etc.

### rpilights command

The "rpilights" command by itself with no further arguments should give a list of possible commands:

	Usage:	rpilights on		Turn lights on (show last animation)
		rpilights off		Turn lights off
		rpilights weather	Display scrolling time, date, weather
		rpilights red		Set all lights to red
		rpilights blue		Set all lights to blue
		rpilights green		Set all lights to green
		rpilights magenta	Set all lights to magenta
		rpilights yellow	Set all lights to yellow
		rpilights cyan		Set all lights to cyan
		rpilights rainbow	Display scrolling rainbow pattern
		rpilights ip		Display scrolling IP address
		rpilights pacman	Display Pacman animation
		rpilights snow		Display snow animation
		rpilights lines		Display random lines animation
		rpilights twinkle	Display twinkle lights animation
		rpilights valentines	Display Valentine's Day animation
		rpilights stpatricks	Display St Patrick's Day animation
		rpilights christmas	Display Christmas animation

### rc.local

The file misc/rc.local has suggested commands to add to your existing /etc/rc.local file.

### .messages5x8 and .messages5x5

If these files exist, messages from .messages5x8 will scroll as well as the temperature and weather in the upper line
of the weather display, and messages from .messages5x5 will scroll along with the date and time in the lower line.  If
there are multiple lines in the file each line will display in turn.

### updatewx script

Edit the file scripts/updatewx to put your zipcode in place of mine (55337).
This script gets weather info (forecast and temperature) every five minutes.
You'll want to start this script from rc.local.  If you don't run this, the 'weather' command won't work.
