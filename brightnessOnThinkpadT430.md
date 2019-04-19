# Adjusting Brightness on a Thinkpad T430

After buying a Thinkpad T430 off of ebay I went straight to insalling Arch Linux. Almost everything worked out of the box including sound(!) but none of my fn layer keys seemed to work. In my attempts at trying to change my friend into the linux ways, I figured I'd document my steps and thoughts in solving this problem. Just know that I am not a master, and this is a very specific case and a very specific solution I came up with. I hope this can serve as a documentation to problem solving on linux and basic linux terminal commands.

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
It looks complicated but all it does is grab the value of the brightness file, and times it by 9/10 (bash can't handle decimals), which echos 90% of the original brightness file's value. Use > to pipe it into the brightness file again and we have a script that will decrease our brightness. I saved this file as ```~/.scripts/brightnessDown```
Now we're going to quickly make a copy, edit it to increase brightness instead of decrease and save it as brightnessDown. In a terminal : 
```
cp ~/.scripts/brightnessDown ~/.scripts/brightnessUp
vim ~/.scripts/brightnessUp

#!/bin/sh
echo $(( $< /sys/class/backlight/intel_backlight/brightness) * 11/10) > /sys/class/backlight/intel_backlight/brightness
```

We just need to make this into a executable script and bam we have our scripts in place.
```chmod +x brightnessUp brightnessDown```

## Linking the keypress to the scripts

I use i3-gaps which is a tiling window manager. Whenever I load i3 on an xserver a file called ```~/.config/i3/config``` is ran and through here I can set up my keybinds. This is going to be specific on which window manager / desktop enviorment you use but a google search should give you the right place and way to put these keybinds.

```Xev``` is a beautiful tool that will print the name of the key you just pressed into a terminal. Using this we know that our brightness up key and down key are called ```XF86MonBrightnessUp XF86MonBrightnessDown``` respectively. 
Adding that keybind to my i3 config would look something like this :
```
bindsym XF86MonBrightnessUp brightnessUp
bindsym XF86MonBrightnessDown brightnessDown
```
This will run our ```brightnessUp``` script when we press our brightness up key, and run ```brightnessDown``` when we press brightness down. 

## A few kinks

Remember when we made our brightness file editable by our user profile? Well that breaks when we restart our computer.  We know that our ```chown``` command allows us to edit the file so an easy solution is just to run that command everytime we start our computer. 

This is another distro-specific issue but for me theres a file specifically made for running commands on loading bash. I edited ```/etc/rc.local``` to simply run my ```chown``` command and was all set. 

## Summary 

These are all the steps I took to fix a simple problem and with all the googling and editing it probably took me about 30 minutes. I learned about the ```chown``` command and how to autorun commands on startup and if I had to do it again I could probably do it in just a few minutes. 

