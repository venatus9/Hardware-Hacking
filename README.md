# Hardware-Hacking-the-Vodafone-THG3000 !!!WIP!!!
This is the documentation of my process in dumping the vodafone THG3000 router's firmware.

## Warning
If you wish to recreate any of the steps shown in this documentation, I would like to remind you that you are solely responsible for the damages you will incur.


## Introduction
I've always liked the idea of tinkering about with hardware, so I had acquired a Vodaphone router (THG3000 model) for cheap and took a crack at it to see what I could do.

If you have heeded my earlier warning, I would recommend you have a screwdriver kit and soldering kit handy (a multimeter is also necessary - but only if you plan on doing this to another model, I would still recommend this if you are planning to do this yourself on the same model so that you can learn along with me). Alongside this, you will be needing header pins of some kind for later when the time comes.

With that being said, lets get started!

# HACKING START

## Opening the router
The first step in hacking the router is opening the router.

Screws on the bottom panel held some promise, they can be unscrewed using a T10 tip screwdriver.

![IMG_20241102_184146__01__01.jpg](https://github.com/user-attachments/assets/8eb2bbc7-7d5f-47e9-87b7-a52be8683be8)
However, upon further inspection it simply removes the bottom stand.

Typical - the actual shell of the router has been glued shut. Companies that manufacture hardware such as routers generally don't want you fiddling with their internal components. As a result, sadly we have to bite the bullet and mangle the shell to get access to the circuit board.

There is a small seam at the top which can be pried with a thin object. I used a pair of scissors and a screwdriver. Simply pry up and down the seam, and when it is fully loose, continue with the sides of the router. Finally, once that's through you should just be able to pull the front and back of the router apart (be careful as doing this too quickly or with too much strength risks tearing the antenna - which isn't all that bad in terms of firmware dumping, but causes some problems later on if you're planning to actually use the router). Be sure to carefully remove the antenna from the back panel before setting the shell aside.

![IMG_20241102_184639.jpg](https://github.com/user-attachments/assets/4e3843d7-4614-463e-9d96-4c697f7a0965)

If you managed to do this, congratulations! You now have complete access to the circuit board and can move on to the next step - locating the UART interface. I would recommend looking for it yourself before moving on to the next section where I show you its location (that is of course if you are using the same make and model as me).

![IMG_20241102_184917.jpg](https://github.com/user-attachments/assets/8d896874-0dc1-4be9-aec6-f82d765cce0d)

## identifying UART
Behold, the suspected UART interface in all its glory.

![IMG_20241102_184917__01.jpg](https://github.com/user-attachments/assets/03fa8d1d-7d09-44bd-a757-d9d7db596155)

Looking at it you'd suspect that it would be UART judging by the four pins, shape and outline surrounding. But of course it's always best to check. Looking online for the J203 interface I was able to identify two similar interfaces labelled J203 including UART alongside a PTA interface. However, considering the fact that this interface on the board is not being visibly utilised by anything on the pins and the connection to a nearby chip on the backside of the board rather than any antenna, I concluded...

![IMG_20241102_184948__01__01.jpg](https://github.com/user-attachments/assets/0bfac6f0-8c34-432a-84a8-7b990aa2fcd8)

... This in fact should be UART!

Now we have to identify which pin is which. I hope you have a multimeter handy!

It is easiest to find the ground pin (GND) first, so set your multimeter to the below configuration - negative probe (black) to common interface (COM), positive probe (red) to the voltage, resistance and frequency terminal, finally set your multimeter to continuity mode, or to sound mode if your multimeter is like mine.

![IMG_20241105_144001.jpg](https://github.com/user-attachments/assets/59463ae4-fcd5-48f5-9ad3-73b766d78028)

Place one probe on any metal sticking out on the board, the metal cover with the heat sink should do. From here simply check each pin on the UART interface. If it beeps, that's your ground pin (GND)!

![IMG_20241105_144419__01.jpg](https://github.com/user-attachments/assets/95473afe-ae1b-4b6a-a220-ebd9c7d498eb)

We've identified the 2nd pin to be ground (GND). We could visibly suspect this due to it being visibly an odd-one-out, but now it's confirmed!

![IMG_20241102_184948__01__01__01__01.jpg](https://github.com/user-attachments/assets/d4d877b8-beb4-4bb4-aa95-16f8ab59224e)

Now it get tricker, for here comes the rest of the pins.

Set your multimeter to 20 DC volatage and turn on the router. We'll go through the rest of the pins by keeping the negative probe on the ground pin, while cycling through the rest of the pins.

UART consists of 4 pins, those being:
* Ground (GND)
* Power (VCC)
* Transmit (TX)
* Receive (RX)

We've already identified ground (GND) to be the second pin, the next order of business will be power (VCC). The way we identify this pin is through a steady voltage that doesn't change and stays around either 3.33V or 5V.
The transmit pin (TX) is usually relatively simple to identify as the voltage should frequently fluctuate. This is because on the router's startup, it transmits information through this pin. Be sure to turn your router on and off repeatedly in order to make sure that this is the case, as after a while of being on the router may stop transmitting.
The receive pin (RX) is quite similar to ground, it should come up with no voltage when checked. The reason for this is because it expects input from this pin, so it shouldn't give any recogniseable signal, and if it does it should be steady similar to power.

It is at this point that I ran into some minor trouble identifying the pins - none of the pins give an exact 3.33V or 5V, which would be a dead giveaway. Two of the pins give steady volatges - the 1st and 4th. One of these is power (VCC) and the other is receive (RX). The first gives a 3.38V/3.37V signal, and the other gives a 3.21V/3.22V signal. It is at this point we have to make an educated guess.

![IMG_20241102_184948__01__01__01__04__03.jpg](https://github.com/user-attachments/assets/f747f741-ae06-4564-a41b-3a220e648531)

Power (VCC) will have the highest voltage out of all of the pins on the UART interface, second highest belongs to the transmit pin (TX) and the lowest (apart from ground (GND) at 0) will be the receive pin (RX). From this we can safely assume that the 1st pin is power (VCC) and the 4th is receive (RX), it also makes more sense to have the power pin (VCC) next to the ground pin (GND).

![IMG_20241102_184948__01__01__01__04__01.jpg](https://github.com/user-attachments/assets/710c6cdd-b59c-4376-927f-b4ef9803fc1f)

![IMG_20241102_184948__01__01__01__04__02__01.jpg](https://github.com/user-attachments/assets/7822f516-3b04-43e0-824b-2bbb0e857b7b)

I was able to confidently identify the 3rd pin as transmit (TX) due to its variable voltage.

![IMG_20241102_184948__01__01__01__04__02__02__01.jpg](https://github.com/user-attachments/assets/cf9c3e52-9418-452f-9118-814fc7301be0)

With that done, we've mapped our UART interface!

![IMG_20241102_184948__01__01__01__04__02__02.jpg](https://github.com/user-attachments/assets/e5e9c824-4031-4b5f-ac76-dcfdfe8a43ed)

Now comes the most nerve-wracking step in this project...

... Soldering.

## Soldering Pin Headers

In order to connect to the router via the UART interface, chances are you're going to have to attach some pin headers into the board as most manufacturers remove them before shipping. If you are lucky enough to have gotten your hands on a board with pin headers already soldered on, you can skip this entire chapter.

