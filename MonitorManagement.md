# Managing Monitors On Arch Linux

Multiple monitors are all but a necessity ( especially if you're t430 doesn't
have an hd screen like mine ) but luckily with a few tools and simple scripts
external monitors can be as easy as two keystrokes. 

## Arandr and You 

Arandr is a graphical tool for managing monitors on an X session. To grab it
just use ```pacman -S arandr``` and let pacman do it's thing.

Arandr is a graphical tool and includes a nice gui, but that's not what we're
here for. We want to be able to change monitor setups with a keybind. Simply set
up the monitor enviorment you want using arandr (making sure to set your
resolution) and save the shell script it creates by clicking on Layout > Save
As. I saved mine to my scripts folder at ``` ~/.scripts/MonitorOnLeft.sh"```

## The Scripts

Almost done now we just need to clean up a few things. The auto-generated file
is just a bash script shell telling xrandr what to do. This gives us a good spot
to tie up some loose ends with more than one monitor. 

### Polybar

I use [this script] (https://github.com/polybar/polybar/issues/763) which looks
for all active monitors and passes that through as a varible for polybar. 

Make a new shell script ( mine is named "launchpolybar.sh" ) and copy the code
into it. We need to kill the polybar instance we already had or you'll end up
with two polybars stacked. Adding ```killall polybar``` at the top of
launchpolybar.sh script. Make it executable with ```chmod +x launchpolybar.sh```.  

So now we have a script that might look like this 
```
#!/bin/sh
 xrandr --output LVDS1 --primary --mode 1600x900 --pos 1920x90 --rotate normal --output DP1 --off --output DP2 --off --output DP3 --off --output HDMI1 --    off --output HDMI2 --off --output HDMI3 --off --output VGA1 --mode 1920x1080 --pos 0x0 --rotate normal --output VIRTUAL1 --off 

#start polybar on each monitor
launchpolybar.sh
```
editors note: I use a seperate polybar script so I can call it in my .Xinitrc
and have polybar when I start up my xserver.

### Setting the Wallpapers
Nitrogen is an amazing tool for managing wallpapers with a gui and simple
console commands to restore wallpapers. Adding ```nitrogen --restore``` to the
end of out MonitorOnLeft.sh script will take care of wallpapers and set them to
the previous wallpapers we had. This also remembers the resolution and position
of all wallpapers.

Our end script should look something like this 
```
#!/bin/sh
xrandr --output LVDS1 --primary --mode 1600x900 --pos 1920x90 --rotate normal --output DP1 --off --output DP2 --off --output DP3 --off --output HDMI1 --    off --output HDMI2 --off --output HDMI3 --off --output VGA1 --mode 1920x1080 --pos 0x0 --rotate normal --output VIRTUAL1 --off

#start polybar on each monitor
launchpolybar.sh
 
#set wallpapers on montiors
nitrogen --restore
```

## At the Press of A Button
This script can be called through a keybind for maximum ease. I3's config
```~/.config/i3/config``` is where I'll add in the keybind. 
```
#arandr
bindsym $mod+F1 exec NoExternalMonitor.sh
bindsym $mod+F2 exec MonitorOnLeft.sh
```
To make more monitor setups simply set it up in arandr's gui, save the
auto-generated script from arandr and add the polybar and nitrogen scripts. 

And now you too can have cool, double triple monitor set ups with the press
of the windows key + F1. 
