### My config
Get touch driver from [onitake/gsl-firmware](https://github.com/onitake/gsl-firmware).  
Deploy driver  
`cd /lib/firmware/silead/`  
`cp gsl1680-chuwi-hi10-air.fw mssl1680.fw`  
Schedule `touch_rotation.sh` to run after log in

## Useful packages
* okular - screen reader for PDF, ebooks, with touch scrolling support
* xournal - support for stylus with draw and eraser functions
* xserver-xorg-input-wacom - Wacom tablet support for Xorg
* xserver-xorg-input-multitouch - for multi-touch input
* [touchegg](https://www.linux.com/learn/how-configure-touchscreen-linux) - gesture

## Virtual Keyboards
| Keyboard | auto hide | word completion | transparency | handwrite |
| :------- | :-------- | --------------- | :----------- | :-------- |
| [GOK](http://www.gok.ca/gok) | yes | yes | no | no |
| [Florence](http://florence.sourceforge.net/english/alternatives.html) | yes | no | yes | no |
| [Onboard](https://launchpad.net/onboard) | no | no | no | no |
| [XVKBD](http://homepage3.nifty.com/tsato/xvkbd) | no | yes | no | no |
| [Caribou](https://wiki.gnome.org/action/show/Projects/Caribou?action=show&redirect=Caribou) | ??? | ??? | ??? | no |
| [CellWriter](https://github.com/risujin/cellwriter) | ??? | ??? | ??? | yes |


---

# ORIGINAL Touchscreen configuration
From linux 4.15 on, the `silead` mainline kernel module should work ok, as long as you provide a valid firmware.
Different firmwares can be downloaded from [onitake/gsl-firmware](https://github.com/onitake/gsl-firmware)
(the ones named `firmware.fw`).

The firmware needs to be copied to `/usr/lib/firmware/silead/` with a custom name depending on the device (or `mssl1680.fw` by default). Take a look at [silead_dmi.c](https://github.com/torvalds/linux/blob/cacd9759eea2f1c7e8792ecd91ed4602f963b1a5/drivers/platform/x86/silead_dmi.c) to
see the DMI match expressions and the corresponding driver properties.


## Adapting kernel driver
It the driver doesn't work right, you can try tunning the driver parameters. This folder contains a copy
of the [silead kernel driver](https://github.com/torvalds/linux/blob/master/drivers/input/touchscreen/silead.c)
modified with some functions to tune the driver.

You will need the kernel headers installed and the `silead` module not loaded.
Then:
 - Modify the `touchscreen_properties` struct in `silead-test.c` with better values.
 - Execute `make test`.
 - Test the device.
 - Rinse and repeat.



## Calibration
Another way of calibrating is using the `libinput` driver. My previous device calibration matrix is stored in
[50-touchscreen.rules](https://raw.githubusercontent.com/danielotero/linux-on-hi10/master/touchscreen/50-touchscreen.rules),
which is an [udev rules](https://wiki.archlinux.org/index.php/Udev) configuration file that must be copied to
`/etc/udev/rules.d/`. Documentation of the specific libinput variable can be found
[here](https://wayland.freedesktop.org/libinput/doc/1.2.1/udev_config.html).

In the X server, you will need to map the touchscreen input to the output every time the later changes. Here is a
sample script to rotate the screen:
```Bash
#!/bin/bash

set -e

TOUCHSCREEN='silead_ts'
OUTPUT='DSI1'

if [ -z "$1" ]; then
  ROTATION=right
else
  ROTATION=$1
fi

xrandr --output "$OUTPUT" --rotate "$ROTATION"
xinput --map-to-output "$TOUCHSCREEN" "$OUTPUT"

```

## Calibration process
The calibration process was done manually using the description found
[here](https://wayland.freedesktop.org/libinput/doc/1.2.1/group__config.html#ga09a798f58cc601edd2797780096e9804).
For my screen the values were interpreted as follows:
`LIBINPUT_CALIBRATION_MATRIX=0 <y-scale> <y-offset> <x-scale> 0 <x-offset>`

where:

 - **`y-scale`** is vertical scaling factor
 - **`y-offset`** is the vertical offset from the bottom-left corner of the screen
 - **`x-scale`** is horizontal scaling factor
 - **`x-offset`** is the horizontal offset from the bottom-left corner of the screen

