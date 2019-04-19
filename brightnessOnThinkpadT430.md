# Adjusting Brightness on a Thinkpad T430

After buying a Thinkpad T430 off of ebay I went straight to insalling Arch Linux. Almost everything worked out of the box including sound(!) but none of my fn layer keys seemed to work.

## How to change brightness

Backlight brightness is controlled by a file in ```/sys/class/backlight/intel_backlight/backlight```. The file just has a number value (for me it was stuck on a max brightness of 4437) and using ```echo brightnessValue > /path/tofile ```  you can theoritcally change the brightness. 

Here's where my issues began. Instant permission denied, and even using sudo I wasn't able to edit the file. ```ls -l brightness``` gave me an out put that looked something like this 
```
-rw-r--r-- 1 root root 4096 Apr 18 23:35 brightness
```
From this we can tell that root is the owner of the file and it's editable by root only. Since I want to be able to edit this file I can simply ```chown david brightness``` to change my user to the owner of the file, which should give me sufficient privilages to edit the file. ```ls -l brightness``` now gives me 
```
-rw-r--r-- 1 david root 4096 Apr 18 23:35 brightness
```

So we've figured out how to edit the file and made it to where we can manually change the brightness value on our screen, but having to open a terminal to type that in manually each time is a pain. Which leads us to

## Making a script to adjust the brightness

We have a dedicated key to increase the brightness and when we press that key it should increase the brightness. This is where we can write a simple bash script to either add or subtract 10% of the brightness value and then write that out to the file. The ever helpful [` on the #archlinux freenode server helped me come up with this
```
echo $(( $< /sys/class/backlight/intel_backlight/brightness) * 9/10))
```
It looks complicated but all it does is grab the value of the brightness file, and times it by 9/10 (bash can't handle decimals), which echos 90% of the original brightness file's value.
