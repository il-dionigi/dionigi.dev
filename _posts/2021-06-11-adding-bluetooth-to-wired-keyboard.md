---
title: Adding BLE to a Wired Keyboard
layout: post
---

## Background

{% newthought "I've been on a crusade" %} against visible wires recently. Wires going to my modem (*why* is the internet coax cable coming out of the middle of my closet?). Wires going to my TV. And, most importantly, wires going to the peripherals on my desk.

I mean, seriously, my desk is already messy enough as it is, without the wires for my headset, mouse, and keyboard. Thankfully I was able to find a swift replacement for two of them using a Logitech MX Master 3 for the mouse and my current airpods for the headset (until I can afford a proper over-ear headset, that is). Finding a proper replacement keyboard, however, turned out to be a problem.

My ideal keyboard has the following requirements: wireless, mechanical, backlit, a good battery life (more than 2-3 days), affordable (<$200), and media controls. From what I could see online, this wasn't available. OK, so I can't buy a keyboard, but I've enjoyed using my CODE Keyboard for the past 7 years, so why not just make it into a wireless keyboard?

First thing I did was a quick google search online, which turned up a few results from others who had the same idea. One in particular -- [this album](https://imgur.com/gallery/E1NWKiG) on imgur from `u/MountainBound77` -- seemed like a good baseline. In fact, this post can ultimately be thought of as a spiritual successor to that album, since I ended up using the same bill of materials as `u/MountainBound77`; however, I went into more detail so that anyone else who wants to do this themselves has an easier time than I did.

## Parts

{% newthought "The requirements" %} for this project influence the parts list quite directly. When taking a look at what's available for <abbr title="Commercial Off-the-Shelf">COTS</abbr> solutions, there aren't many options that act as transmitters instead of receivers for existing wireless keyboards (the <abbr title="Bluetooth Low-Energy">BLE</abbr> dongles you can find of amazon, for example, are receivers{% sidenote 'sidenote-ble-dongle' "At a high level, a BLE dongle (AKA, receiver) takes incoming data sent as BLE packets and deconstructs them into binary streams to send over USB into a computer. This is similar, but unfortunately the opposite direction, as a transmitter which takes the incoming USB data from the keyboard and constructs them into BLE packets to send over radio." %}).

Luckily, the company Handheld Scientific sells a bluetooth adapter (the [BT-500](http://handheldsci.com/kb/)) which is made for our use-case and fairly cheap (< $40). We can even customize the adapter's software, not that we need to for our purposes. The BT-500 is originally bulky, but this can be amended by putting in a customer support ticket to get the <abbr title="Printed Circuit Board">PCB</abbr> without tall components (USB headers and mode-switch button) soldered on.

See below for a comparison of stock BT-500 compared to the barebones PCB version:

{% maincolumn 'assets/img/keyboard/bt500-pcb-comparison.png' "Above is an image of the BT-500 from HandheldScientific exactly as they sell it -- a bit too large for us to put in the keyboard -- while below is what they send when you request the bare PCB without headers and button attached. I labeled the relevant pins on the PCB to help compare the two. Note that we don't need the mode switch button in the middle or the data +/- pins from the male USB header." %}

{% marginfigure 'mf-battery-connections' 'assets/img/keyboard/battery-connections.png' "As a note for others, it can never hurt to verify the polarity of the battery you're using. Turns out mine had its polarity swapped, so I had to resolder the connections. And before anyone asks, I ended up redoing it after taking this photo to add shrink tubing just to be safe. :)" %}

Going from here, we need a way to power the BT-500 and recharge the power source. Lipo batteries are the standard solution for something like this, but they operate at 3V7 while the keyboard requires 5V. To get around this and the charging requirement, we can use a [PowerBoost 500 Charger](https://www.adafruit.com/product/1944) from Adafruit -- getting the PowerBoost 1000 is way over-engineered for this project since keyboards at maximum seem to draw 500mA. If you're concerned of your keyboard's current use, you can test it using one of [these](https://www.amazon.com/gp/product/B07FMQZVW2). I tested mine and found I was well below 500mA even with the brightest backlight setting.

Lastly, a nice-to-have for this project is a way to still use the keyboard wired while we're charging. This can be done with a <abbr title="Double Pole, Double Throw">DPDT</abbr> switch that is on-off-on. Using a dremel and some hot glue, the switch will be a pretty easy addition to the keyboard.

Since we'll need to understand an on-off-on DPDT switch for the wiring, here is a quick description of both the DPDT's schematic representation and its internals.

{% maincolumn 'assets/img/keyboard/dpdt-diagram.png' 'Looking at this figure, we see a DPDT switch both as its typically represented in wiring diagrams (above) and as a schematic showing the pins (below). While initially confusing, it is just two parallel on-off-on switches. Not too crazy. :)' %}

## BOM

Tallying up the prices shows that this cost slightly less than $82.

{% marginnote 'table-bom-sans-keyboard' 'Table 1: The bill of materials, taxes included. Note, DPDT switches could likely be bought for cheaper on a dedicated electronics site (e.g. Digikey). Also, be sure to double- and triple-check the battery dimensions, since last thing you want is the keyboard PCB piercing the battery -- or straight-up not fitting! -- when you close things up.' %}

|Item                    |Seller              |Price (USD)  |
|:-----------------------|:-------------------|:-----------:|
|BT-500                  |Handheld Scientific |39.95        |
|PowerBoost 500 Charger  |Adafruit            |14.95        |
|2500mAh Lip Battery     |Amazon              |14.77        |
|DPDT Switches (6 Pack)  |Amazon              |12.20        |
|                        |**Total**           |**81.87**    |

I do have a confession to make at this point, though, since this didn't end up being my actual total.

So... turns out the parts don't fit in my CODE Keyboard. Drats!

I was hopeful that I could try and squeeze things in, but after soldering everything and testing that it worked (it did!) there just wasn't enough space. There is *really* limited clearance under the PCB inside the keyboard, even near the top, so I ended up needing to buy another keyboard for this project.

Much like u/MountainBound77 from the original iteration of this project, the Durgod Taurus K320 seemed like a great fit. I did splurge for the backlit version, but testing showed that it didn't draw that much more current with the LEDs on the lowest setting, and I can always turn off the LEDs during the day.

{% marginnote 'table-bom-keyboard' "Table 2: The final bill of materials, taxes included. Note to double-check the dimensions of whatever battery/keyboard combination being used, or else you'll end up in the same situation as me." %}

|Item                    |Seller              |Price (USD)  |
|:-----------------------|:-------------------|:-----------:|
|Wireless Componenets    |See above           |81.87        |
|Durgod Taurus K320      |Amazon              |131.39       |
|                        |**Grand Total**     |**213.26**   |

## Wiring

{% newthought "First thing" %} we need to be aware of for the wiring is how the USB inside the keyboard is wired. The Durgod uses 5 wires -- black, yellow, green, white, and red -- which is pretty typical for USBs. Looking online, black and red are ground and power, respectively, while green and white are data+ and data- {% sidenote 'sidenote-data-wires' "The data+ and data- wires are there to create a [differential pair](https://en.wikipedia.org/wiki/Differential_signaling), reducing the external interference on the signal." %}. The only wire that takes a bit of testing is the yellow wire. It could be the ID pin (also called the [On-The-Go](https://en.wikipedia.org/wiki/USB_On-The-Go) wire for determining host vs device), but more likely than not it's just tied to ground. Using a voltometer, I determined that in fact, yes, the yellow is just a redundant ground!

From here, determining which side of the BT-500 to connect to D- and D+ would be the next logical step. Personally, I messed this up the first time and had to re-solder my connections, but taking a look at the [BT-500 manual](http://www.handheldsci.com/wp/wp-content/uploads/Manual_Full_v5.2.0.pdf) it's pretty clear the female side of the device gets tied to the keyboard data.

Lastly, we need to figure out how to wire the DPDT switch so we can use the keyboard wireless, or wired when charging the battery. This ended up being a little tricky to figure out, but ultimately not too bad. This is accomplished by solving two separate questions:{% sidenote 'sidenote-wiring-question' "Each question will be solved by one half of the DPDT switch, as it consists of two on-off-on switches wired in parallel. This can be seen more clearly in the figure at the end of the 'Parts' section." %} how to alternate power from the wired to wireless connection and how to disable the bluetooth.

Alternating power sources is the easy one since we just change what the keyboard's outbound Vcc is connected to. Meanwhile, to enable or disable the bluetooth we need to utilize the PowerBoost 500's enable pin. If we connect it to ground, the PowerBoost stops outputting 5V, and the BT-500 no longer receives enough power to operate. Ta-da!

Putting it all together, we get the following schematic:

{% maincolumn 'assets/img/keyboard/wiring-diagram.jpg' 'Possible status LEDs are included, but in my case were not used due to not enough space in the case.' %}

## Construction

{% newthought "Putting it together"%} isn't too bad.

I started by finding a video online for how to take apart the Durgod. My advice would be to get some sort of pry tool (mine are "triangle dissasemble blades," whatever that means) if taking apart a Durgod since the latches on the corners are especially tricky.

Just as I figured, the backlit version also has plenty of space at the top for our needs.

{% maincolumn 'assets/img/keyboard/keyboard-backplate-empty.png' 'Taking a look at the inside of the case, there were a bunch of compartments that would get in the way of placing our components. In this photo, I already got rid of the majority (those vertical lines in the top channel) with some scissors and a plier. I originally thought of leaving that compartment to the top right of the USB-C port, but ultimately also removed it so that we could put the switch on that side.' %}

Next, I used a dremel to cut a rectangle to the top right of the USB-C port for our DPDT switch. With that out of the way, all that remained was to solder everything together using the schematic as reference.

For those new to soldering, I found it helpful to tin the wires (fancy speak for adding solder) before trying connect them to the DPDT switch. Also, for safety reasons, it's never a bad idea to buy some shrink tubing so that you don't have exposed wire at the connection points. My colored wires came with colored shrink tubing, so depending on where you shop it might not even cost extra.

It might also be a good idea to add tape to the underside of the PCB where the through-hole wires might poke the battery -- we don't want a battery leak! I found that my battery just *barely* fit when the case was closed.

Anyhow, here's a picture of everything soldered and taped down:

{% maincolumn 'assets/img/keyboard/keyboard-components-added.png' "I made some wires a tad too long, but I'm overall pleased with how I laid out the componenets. The DPDT switch is on the right side to make space for the battery on the left, and everything fit snugly on the right. If I were to do it again, I'd take more time with the dremel since the DPDT switch's hole is a little sloppy. Thankfully it can't be seen, though :P" %}

## Battery Life

{% newthought "Calculating the battery" %} life takes a little bit of math and looking at specs.

To begin, I measured the current usage of the keyboard prior to modifying it, and without the backlight on it only draws around ~13.3mA. With the backlight on the minimal setting, the draw increases a tiny bit to ~17.6mA, and then increases linearly to ~121.4mA at the brightest setting nine levels up.

Meanwhile, the BT-500 draws 40mA and the PowerBoost 500 draws 5mA, as according to their spec sheets. Another thing we need to note is the operating efficiency of the PowerBoost 500, which from the specs we get as 90%+ for the range of operating current we'll be needing.

To calculate the battery life, we use the following equation that we can derive from $$t = \frac{C}{A}$$:

{% marginnote 'mn-equation-variables' "In this equation, $$C$$ is capacity, $$A$$ is current draw, and $$\eta$$ is efficiency. Likewise, subscripts $$_{BT}$$, $$_{PB}$$, and $$_{K}$$, are respectively BT-500, PowerBoost 500, and keyboard." %}

$$t_{hours} = \frac{C_{battery}}{A_{PB} + \frac{1}{\eta_{PB}^2}(A_{BT} + A_{K})}$$

Plugging in the values, we get the following for theoretical battery life.

{% marginnote 'table-battery-life' "Table3: The current draws and theoretical battery life using our components and their power consumptions, accounting for boosted voltage efficiency. Calculated for our 2500mA battery." %}

| Backlight Level | System Current Draw (mA) | Battery Life (hours) |
|:----------------|:------------------------:|:--------------------:|
| 0  (none)       | 70.80                    | 35.3                 |
| 1  (minimal)    | 76.11                    | 32.85                |
| 10 (maximal)    | 204.26                   | 12.24                |

## Conclusion

{% newthought "Success!" %} Couldn't be happier with how this turned out.

If I were to be nit-picky, I *could* say the battery life is too short, but considering that I turn the keyboard off when not in use, 30+ hours is more than enough. Having used the keyboard for the past few weeks, it easily lasts me more than 3 days since I don't use this keyboard for work -- my work laptop has bluetooth disabled, and I'm not about to use this wired after spending time on specifically making it wireless.

To anyone wanting to make their own wireless keyboard, I strongly suggest it for the learning opportunity! Personally, I was only about 50% confident in being able to pull this off, but here I am with a working wireless keyboard. :)

{% fullwidth 'assets/img/keyboard/keyboard-hello-world.png' "'Hello world!' using the keyboard -- first thing I did with it" %}
