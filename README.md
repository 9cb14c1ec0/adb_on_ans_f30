# adb_on_ans_f30
How to get adb access on an ANS F30 flip phone

The ANS F30 phone is an Android based flip phone sold by US Cellular.  This phone is a particular challenge to get adb access to, as adb is disabled by default, there is no option in the settings app to enable developer settings, and the default dialer app does not have the permissions needed to send the debug secret code.

# Tools
You will need [edl.py](https://github.com/bkerler/edl) and fastboot installed on your computer.  Also, you will need to be able to mount an linux ext4 filesystem, which means you need to be running linux to follow this tutorial.

1. Clone this repo to your computer and open your terminal to the repo folder.
2. Power the phone off.  Press and hold the volume up and volume down buttons, and then immediately connect the phone to your computer via the USB cable.  This will put the phone in edl mode.
3. Run `edl rl firmware_dump_folder --genxml --loader=prog_emmc_firehose_8909.mbn` to dump the system firmware.
4. In your terminal, run this command: `mkdir ansmount`
5. Run `sudo mount -t ext4 -o loop firmware_dump_folder/system.bin ansmount`.  This mounts the system image from the phone in the ansmount folder.
6. Run `sudo nano ansmount/build.prop`.  This will open the build.prop file in the nano editor with root permissions.
7. Go the the line that says `persist.sys.usb.config=mtp` and add `,adb` to the end of it.
8. Add the following three lines: 
```
ro.adb.secure=0
ro.debuggable=1
persist.service.adb.enable=1
```
9. Press Ctrl+X to exit the file, then run `sudo umount ansmount` to unmount the system image.
10. Exit edl mode and turn the phone off by removing the battery.
11. Enter fastboot mode by holding volume down and inserting the usb cable.
12. Flash the modified system image with `fastboot flash system firmware_dump_folder/system.bin`
13. When you reboot the phone you should have adb access.

PS.  It might be a good idea to make a copy of the system.bin file before modifying it.  It never hurts to have a backup.



