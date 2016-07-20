# Flash CHIP With an OS

You might want (or need) to completely re-flash your CHIP with a different operating system. We've made it really easy to do this by embedding the entire process in a browser-based application. Just hook CHIP up to your computer, fire up a browser, and flash your CHIP


## Things you will need

 * C.H.I.P.
 * Standard-USB to micro-USB connector
 * Thin Paper clip (a jumper wire works too)
 * Separate computer with [Chrome](https://www.google.com/chrome/browser/desktop/index.html) or [Chromium](https://www.chromium.org/getting-involved/download-chromium) browser
 
## Instructions

If you don't have the Google [Chrome](https://www.google.com/chrome/browser/desktop/index.html) or the open-source [Chromium](https://www.chromium.org/getting-involved/download-chromium) browser on your other computer, install it by following the preceding links.

Ok, good. Now that you have installed the browser, you can visit [flash.getchip.com](http://flash.getchip.com) in Chrome or Chromium and follow all the instructions. If you are using a computer with USB3 ports, it's suggested that you attach a USB2-compliant hub and connect your CHIP to the hub, instead of directly to the USB3 port.

It's possible to run the entire operation online. There are also options for downloading the OS image to your computer's harddrive, then flashing your CHIP with no additional need for internet access. 

### FEL mode 
Here is a photo (which may be clearer than the animation in the web flasher) which shows where to place your jumper wire or paper clip:

![CHIP with jumper wire attached](images/uboot_fel_jumper.jpg)

*Note: this jumper needs to be present only when you connect CHIP to power*. If for some reason the wire becomes disconnected after you have powered CHIP, there is no problem or need to panic.



For more information on OS-specific issues, [see here](#web-flasher-os-specific-issues).

## The SDK Way

You may not want to use the browser-based flashing procedure. If this is the case, or you need additional tools, you can flash using the [CHIP SDK](#flash-chip-firmware)

## Web Flasher OS-Specific Issues

Before you try anything else, try using a different USB cable. Many cables are charging-only, or do not support high bandwidth and will cause flashing to fail.

### Windows-specific 
   * You must install [drivers](https://s3-us-west-2.amazonaws.com/getchip.com/extension/drivers/windows/InstallDriver2.exe) to be able to talk with C.H.I.P.
   * Reboot after installing drivers on previous versions (<10) of Windows.
   
Unfortunately, due to the nature of how Windows manages drivers, the flashing procedure will likely fail the first time you use it. When that happens, try completely closing and reopening your browser. Depending on your version of windows, this might happen twice, once when waiting for FEL, and then again waiting for Fastboot.

#### Troubleshooting The Web Flasher
   * Try using a USB2 port (USB3 ports have issues).
   * Try passing through a USB2 hub if using a USB3 port.
   * Try a different USB cable. They often are bad.
   * Try turning off antivirus software.
   * If you get stuck "Waiting for Fastboot" and the above options don't work, you should be able to install a "headless no fastboot" image. However, it will take quite a bit longer, and the Operating System won't have a GUI.


### MacOS specific
   * There are some times where using USB3 ports will cause the flashing to fail. If you can, try using a USB2 port, not a USB3. Recent Macs have only USB3 ports. If you find yourself with a modern Mac, try using a USB2 hub in your USB3 port and plug C.H.I.P. into that.
   * If you get stuck "Waiting for Fastboot" and the above options don't work, you should be able to install a "headless no fastboot" image. However, it will take quite a bit longer, and the Operating System won't have a GUI.


### Linux-specific

Linux requires permissions to write to C.H.I.P. when its plugged into your computer. Chrome (or Chromium) does not have these permissions, so you need to explicitly create them before you'll be able to use the [web flasher](#flash-chip-with-an-os) .

#### On Ubuntu:

You need to paste the following into a terminal:

```shell
sudo usermod -a -G dialout $(logname)
sudo usermod -a -G plugdev $(logname)

# Create udev rules 
echo -e 'SUBSYSTEM=="usb", ATTRS{idVendor}=="1f3a", ATTRS{idProduct}=="efe8", GROUP="plugdev", MODE="0660" SYMLINK+="usb-chip"
SUBSYSTEM=="usb", ATTRS{idVendor}=="18d1", ATTRS{idProduct}=="1010", GROUP="plugdev", MODE="0660" SYMLINK+="usb-chip-fastboot"
SUBSYSTEM=="usb", ATTRS{idVendor}=="1f3a", ATTRS{idProduct}=="1010", GROUP="plugdev", MODE="0660" SYMLINK+="usb-chip-fastboot"
SUBSYSTEM=="usb", ATTRS{idVendor}=="067b", ATTRS{idProduct}=="2303", GROUP="plugdev", MODE="0660" SYMLINK+="usb-serial-adapter"
' | sudo tee /etc/udev/rules.d/99-allwinner.rules
sudo udevadm control --reload-rules
```

Then logout and log back in. 

For the curious:

   * logname: outputs your username 
   * dialout: gives non-root access to serial connections 
   * plugdev: allows non-root mounting with pmount 
   
The udev rules then map the usb device to the groups.

For more information, check [the systems group page on debian.org](https://wiki.debian.org/SystemGroups).

**USB3 Issues**
   * If you have any issues, try using a USB2 port and not a USB3 one, or try using a USB2 hub in your USB3 port and plug C.H.I.P. into that.
   * If you get stuck "Waiting for Fastboot" and the above options do not work, you should be able to install a "headless no fastboot" image. However, it will take quite a bit longer, and the Operating System won't have a GUI.



**Caveat**
In rare cases, you may have an issue with your [computer](http://askubuntu.com/questions/185274/how-can-i-disable-usb-autosuspend-for-a-specific-device) putting C.H.I.P. into auto-suspend mode. Here is an example on how to fix this problem:

```shell
apt-get install laptop-mode-tools
#### edit /etc/laptop-mode/conf.d/runtime-pm.conf, uncomment/change AUTOSUSPEND_RUNTIME_DEVID_BLACKLIST
#### add all fel devices to the blacklist:
AUTOSUSPEND_RUNTIME_DEVID_BLACKLIST="1f3a:efe8"
reboot
```
