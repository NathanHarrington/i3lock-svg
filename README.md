
![i3lock demo](/examples/WasatchPhotonicsExample.gif "i3lock demo")


i3lock - improved screen locker
===============================
i3lock is a simple screen locker like slock. After starting it, you will
see a white screen (you can configure the color/an image). You can return
to your screen by entering your password.

Many little improvements have been made to i3lock over time:

- i3lock forks, so you can combine it with an alias to suspend to RAM
  (run "i3lock && echo mem > /sys/power/state" to get a locked screen
   after waking up your computer from suspend to RAM)

- You can specify either a background color or a PNG image which will be
  displayed while your screen is locked.

- You can specify whether i3lock should bell upon a wrong password.

- i3lock uses PAM and therefore is compatible with LDAP etc.

Requirements
------------
- pkg-config
- libxcb
- libxcb-util
- libpam-dev
- libcairo-dev
- libxcb-xinerama
- libev
- libx11-dev
- libx11-xcb-dev
- libxkbfile-dev
- libxkbcommon >= 0.5.0
- libxkbcommon-x11 >= 0.5.0
- librsvg

Compiling
---------
On Fedora Core 21:

sudo dnf -y install
    xcb-util-devel xcb-util-image-devel
    pam-devel libev-devel libxkbfile-devel
    libxkbcommon-x11-devel cairo-dock-devel


Installation
----------------------
When you first run i3lock it will always gives you the wrong password,
either install i3lock from the package manager, or update your pam
configuration as described here:
https://bbs.archlinux.org/viewtopic.php?id=170300

echo "auth include login" > /etc/pam.d/i3lock 

You must do the above or you will always get the wrong password
reported.


XFCE Configuration
------------------
On XFCE with Fedora Core 21/22, remove xscreensaver:
sudo dnf erase xscreensaver\*

Replace the xflock4 file with the contents of the i3lock program:
<path_to_i3lock-svg> -i 1920x1080-wasatchphotonics.png 
-s wasatchphotonics_prism.svg 

Update your session startup parameters to include the xautolock
configuration:
xautolock -locker xflock4 -time 10


Cinnamon Configuration
----------------------
On Cinnamon 3.0.1 on Fedora Core 23:
sudo dnf install xautolock
sudo dnf install slock

sudo mv /usr/bin/slock /usr/bin/orig.slock

Create a new file: /usr/bin/slock with the contents:
#!/bin/sh
I3PATH=/home/nharrington/projects/i3lock-svg
PNGFILE=$I3PATH/examples/3200x1800-wasatchphotonics.png
SVGFILE=$I3PATH/examples/3200x1800-wasatch_photonics_prism.svg

$I3PATH/i3lock -i $PNGFILE -s $SVGFILE


# Setup the file to be executable
sudo chmod +x /usr/bin/slock

Settings->Screensaver
Make sure these are off/disabled:
Lock the computer when put to sleep
Lock the computer when the screen turns off
Lock the computer when inactive

Settings->Startup applications
Add an entry with the name:
    xss-locker
command:
    xss-lock -- slock
comment:
    trigger manually with xset s activate

This will automatically lock the screen on suspend, and when the 
Settings->Power Management settings "turn off the screen"

That's the idea at least - currently testing with different scenarios to
see if you can get it to behave like a 'normal' locker.


Running i3lock
-------------
Simply invoke the 'i3lock' command. To get out of it, enter your password and
press enter.

Creating indicator SVGs
-----------------------
In each state a specific object id will be rendered. Used ids are:
- "idle"
- "verify"
- "fail"
- "backspace"
- "animXX" for frames of the typing animation, where XX is a ascending number
  starting at "00".
- "fg" and "bg" for foreground and background

If the id "sequential_animation" is present, animation frames will be rendered
in ascending order instead of random order.

If the id "remove_background" is present "idle", "verfiy" and "fail" won't be
rendered when drawing the animation frames.

For easier testing, uncomment lines in i3lock.c:
```
        case XKB_KEY_Escape:
            clear_password_memory();
            turn_monitors_on();
            exit(0);
```

Use caution, as this will disable all password checking and immediately
terminate i3lock when the Esc is pressed.

Running: Use -i for the background image, and -s for the svg, for
example:

./i3lock -i examples/1920x1080-wasatchphotonics.png \
         -s examples/wasatch_photonics_prism.svg 

Upstream
--------
Please submit pull requests to https://github.com/i3/i3lock
