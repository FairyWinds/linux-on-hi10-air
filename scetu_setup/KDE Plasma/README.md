![Index image](img/index.png)

## What is new?
[Adventures in Linux and KDE](https://pointieststick.com/)

## Touch driver
### Get driver
Touch driver from root chuwi folder [onitake/gsl-firmware/tree/master](https://github.com/onitake/gsl-firmware/tree/master/firmware/chuwi) is not working for me, but in `driver/` is driver which works for me - taken from [onitake/gsl-firmware/blob/../gsl1680-chuwi-hi10-air.fw](https://github.com/onitake/gsl-firmware/blob/master/firmware/linux/silead/gsl1680-chuwi-hi10-air.fw)    

### Install touch driver
Install xinput of not installed

```
# pacman -Sy xorg-xinput
```

Copy to folder `/lib/firmware/silead/` with default name `mssl1680.fw`  
``` 
$ cd /lib/firmware/silead/  
# cp ~/Downloads/gsl1680-chuwi-hi10-air.fw mssl1680.fw
```

## Screen rotation
Install `iio-sensor-proxy-git` and `kded-rotation-git` from AUR. [source](https://wiki.archlinux.org/title/Tablet_PC#With_a_KDE_module)

Create `/lib/udev/hwdb.d/61-sensor-local.hwdb` with mount matrix, this works for me, adjust it as needed. 
```
# Chuwi Hi10 Air
sensor:modalias:acpi:BOSC0200*:dmi:*
 ACCEL_MOUNT_MATRIX=0, -1, 0; -1, 0, 0; 0, 0, 1

```

Now update config and trigger
```
# systemd-hwdb update 
# udevadm trigger -v -p DEVNAME=/dev/iio:device0
```

Reboot tablet and screen rotation should work

## Virtual keyboard


## Other


---
## Screenshots

---
## Helpful links
[Arch Linux Docs](https://wiki.archlinux.org/title/Tablet_PC)
[Manjaro Forum Thread](https://archived.forum.manjaro.org/t/x64-x86-tablet-howto-draft-for-a-wiki-page-help-appreciated/121979/11)