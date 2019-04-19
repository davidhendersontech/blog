# Adjusting Brightness on a Thinkpad T430

After buying a Thinkpad T430 off of ebay I went straight to insalling Arch Linux. Almost everything worked out of the box including sound(!) but none of my fn layer keys seemed to work.

## How to change brightness

Backlight brightness is controlled by a file in ```/sys/class/backlight/intel_backlight/backlight```. The file just has a number value (for me it was stuck on a max brightness of 4437) and using ```echo brightnessValue > /path/tofile ```  you can theoritcally change the brightness. 

Here's where my issues began. Instant permission denied, and even using sudo I wasn't able to edit the file. ```ls -l brightness``` gave me an out put that looked something like this 
```
-rw-r--r-- 1 root root 4096 Apr 18 23:35 brightness
```