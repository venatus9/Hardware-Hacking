# Hardware-Hacking-the-vodafone-THG3000
This is the documentation of my process in dumping the vodafone THG3000 router's firmware.

## Warning
If you wish to recreate any of the steps shown in this documentation, I would like to remind you that you are responsible for the damages you will incur.


## Introduction
I've always liked the idea of tinkering about with hardware, so I had acquired a Vodaphone router (THG3000 model) for cheap and took a crack at it to see what I could do.

If you have heeded my earlier warning, I would recommend you have a screwdriver kit and soldering kit handy (a multimeter is also necessary - but only if you plan on doing this to another model, I would still recommend this if you are planning to do this yourself on the same model so that you can learn along with me). Alongside this, you will be needing header pins of some kind for later when the time comes.

With that being said, lets get started!

# HACKING BEGINS

## Opening the router
The first step in hacking the router is opening the router.

Screws on the bottom panel held some promise, they can be unscrewed using a T10 tip screwdriver.

However, upon further inspection it simply removes the bottom stand.

Typical - the actual shell of the router has been glued shut. Companies that manufacture hardware such as routers generally don't want you fiddling with their internal components. As a result, sadly we have to bite the bullet and mangle the shell to get access to the circuit board.

There is a small seam at the top which can be pried with a thin object. I used a pair of scissors. Simply pry up and down the seam, and when it is fully loose, continue with the sides of the router. Finally, once that's through you should just be able to pull the front and back of the router apart (be careful as doing this too quickly or with too much strength risks tearing the antenna - which isn't all that bad in terms of firmware dumping, but causes some problems later on if you're planning to actually use the router). Be sure to carefully remove the antenna from the back panel before setting the shell aside.

If you managed to do this, congratulations! You now have complete access to the circuit board and can move on to the next step - locating the UART interface.

## UART
Behold, the UART interface in all its glory.

Looking at it you'd suspect that it would be UART judging by the four pins, shape and outline surrounding. But of course it's alwawys best to check. Looking online I was able to identify the J203 interface and...

... It is in fact UART!

Success! Now we just have to identify which pin is which.
