---
layout: post
title:  "Repairing My Neo-Geo MVS"
date:   2022-03-13 12:16:22 -0500
categories: arcade
---
# Introduction
I recently went to boot up my Neo-Geo MVS arcade machine and was greeted with a blank screen with no image or sound. I could hear the monitor kicking on and was praying the issue had something to do with the board as it doesn’t run lethal amounts of voltage. This machine’s monitor is in amazing condition though and all the caps look great so it shouldn’t be having any issues. For context I have added a picture of the machine I’m talking about and the board. Note that these are not pictures of my cabinet and board. They are pictures I found online of the same cabinet and PCB I have at home.

<br />
<center><img src="\assets\03-13-2022-neo-geo-repair\cabinet.webp" width="200" hspace="10" /><img src="\assets\03-13-2022-neo-geo-repair\board.jpg" width="200" hspace="10" /></center>
<br />

# Testing The Board
The very first thing I wanted to do was test the board to make sure it was working. I have a little portable JAMMA test bench that I can plug boards into and hook a monitor into to test if they are working or not. I opened up the cabinet and pulled out the PCB and boom it fired right up on the test bench. At this point I decided to plug the PCB back into the cabinet, turn it on and pay close attention to the little LED light on the board that indicates it is getting power. Sure enough the monitor kicked on but the light on the PCB did not. The power that comes in from the wall into the cabinet is split, one end running to the monitor and the other to an EMI power line filter that runs into the power supply for the PCB.

# Tracking The Power Outage
Now that I knew the PCB was working when it gets power, and I have seen that in the cabinet the power LED on the PCB never comes on, I was pretty convinced I had a power issue on my hands.Upon a visual inspection the power supply definitely looked like it had seen better days and a few of the connections had some brown residue on them. Though this was a pretty good sign that my power supply had died, there is a very easy way to confirm it is the power supply and not the line filter causing the issue. The line filter malfunctioning could also cause power issues so, it can’t hurt to check. I pulled out my voltmeter and checked the voltage reading coming out of the line filter. It was reading right around 120 volts so that was looking good. I happen to already have a few sapre power supplies around so I unplugged the old power supply and plugged the input and output wires to the new power supply. One very important step here is to make sure to not turn it on for the first time with the board plugged in. You want to fire up the cabinet first with the board unplugged and calibrate the voltage coming out of the power supply to slightly over 5 volts on the 5 volt rail. I usually run about 5.2-5.3 volts. Once you know your output is within a safe range, it’s time to plug in the PCB and fire it up for a test.

# Moment Of Truth
Now that the new power supply was in and calibrated, there is only one thing left to do. I plugged it back in and flipped the cabinet on and boom, I could immediately hear the sound coming from the game booting up. At this point there was only one thing left to do. Put some time in playing. This repair ended up being a pretty easy one. I hope this article helps give some insight into how I go about troubleshooting issues in these old arcade cabinets and what you can do to try and repair them yourself at home. One thing to note when working with arcade cabinets using these old CRT monitors is that they do contain lethal amounts of voltage and can cause you serious harm if you don’t discharge them first. Be very careful not to get anywhere near your monitor when you do these repairs and when in doubt, it is better to pay a professional to repair your machine than risk taking a lethal shock.