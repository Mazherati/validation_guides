# IPMI/BMC Troubleshooting Tips

All commands are  run in a Linux terminal

## Check cable and switch

Check to make sure that a LAN cable is properly connected to the IPMI LAN port. 

If the LAN cable is connected, but you are not seeing the connection LEDs on the port flashing, check the Ethernet switch the cable is connected to and see if a signal is being sent out from the switch. If no signal is being sent, try using a different switch.

## Check IPMI IP and MAC address

If the IPMI LAN port is receiving a signal there are a few things we can check in the OS. 

Open up a terminal in any Linux distro and type the following command: 

    $ipmitool lan print

Use this command to make sure that there is an active IP address for your IPMI LAN.

If the IP address is showing as 0.0.0.0, check to see if the MAC address is also zeroed out. If the MAC address is zeroed out, you will have to manually enter the IPMI MAC address with the following command:

    $ipmi reset mac command xx.xx.xx.xx.xx
where xx.xx.xx.xx.xx is your system's IPMI MAC address.

The IPMI MAC address is located on a sticker on your system's motherboard, near the IPMI LAN port.

## Correct IPMI MAC address, no active IP address

If your system is displaying the correct IPMI MAC address, but there is no active IP address shown, try resetting the BMC with the following command:

    $ipmitool mc reset cold

Wait a few minutes after running this command, and then check to see if the IPMI IP and MAC address have been updated by rerunning the command:

    $ipmitool lan print

If the BMC reset doesn't work,
try resetting the BMC with the following command:
    
    $ipmi reset command

You may also try disabling and reenabling DHCP with the following commands:

    $ipmi dhcp command off
    $ipmi dhcp command on

## Reflashing IPMI firmware

If none of the prior troubleshootings steps are working, the IPMI firmware may need to be reflashed.

First try flashing using the IPMI web GUI.

If flashing through the web GUI fails, load the firmware onto a USB and insert the USB into your system. Boot into EFI shell and flash the firmware using the included firmware installer.

If the EFI shell flash also fails, then a clip must be used to flash the chip directly.

## Flashing IPMI firmware with clip

Sometimes the IPMI firmware will become corrupted, which will cause many issues such as having no active IPMI IP address.

To remedy this we will have to use a clip to flash the firmware directly onto the chip.

Look at your BMC chip and see if it is removable or not, this will tell us which flasher to use.

### Removable BMC chip

Remove the BMC chip and note the arrow or indent located at one of the corners on top of the chip. This will indicate where pin 1 is on the chip.

Take the chip over to the dedicated flashing PC setup in the lab, and grab the clip used for removable BMC chips. If you don't know which clip is which, you can ask one of the technicians in the lab.

 Insert the BMC chip into the clip, making sure to align pin 1 on the chip with the designated pin 1 marking on the clip. Then plug in the USB from the clip into the PC and start the Dediprog flashing program.

**Note: Make sure that you insert the BMC chip into the clip before plugging the clip into the PC to receive power, otherwise you may fry the chip**

Load the IPMI firmware onto the PC using a USB or downloading from online, and load the firmware into Dediprog by clicking the "Load" button.

Next, click the "Erase" button to remove the old firmware from the chip. If you suspect the old firmware is corrupted, or just to be safe, you may want to hit "Erase" twice. Corrupted firmware can sometimes leave lingering bits of data on the chip after the first erase, which will interfere with the new firmware you are going to flash.

Once you have finished erasing the old firmware, click the "Batch" button to load your new firmware onto your chip.

Once the firmware is finished installing and verifying, remove the USB connection from the clip before pulling the chip out of the clip.

Replace the BMC chip into your system's motherboard and check to see if the issues have been resolved.

If this still does not fix your issues, you will have to contact the system PM and request either a new BMC chip or motherboard.


### Non-removable BMC chip
Download the program sameprog onto your laptop. You may start the program at this time, but the necessary features will be unavailable until you connect your BMC chip.

Grab the clip used for non-removable BMC chips. If you don't know which clip is which, you can ask one of the technicians in the lab.

Locate pin 1 on the BMC chip, marked by a small indent or dot on one of the corners on the top of the chip. Attatch the clip to the BMC chip, making sure to align pin 1 on the chip with the designated pin 1 marking on the clip. Then plug the USB from the clip into your laptop.

**Note: Make sure that you connect the clip to the BMC chip before plugging the clip into the PC to receive power, otherwise you may fry the chip**

After you connect the chip and clip to the PC, run sameprog by right clicking on the icon and selecting the "Advanced Configuration" option.

Once booted into the program, load your firmware into sameprog and press the "Erase" button to remove the old firmware from the chip. If you suspect the old firmware is corrupted, or just to be safe, you may want to hit "Erase" twice. Corrupted firmware can sometimes leave lingering bits of data on the chip after the first erase, which will interfere with the new firmware you are going to flash.

Once the old firmware has finished being erased, you may flash the new firmware onto the chip. After the firmware is finished flashing onto the chip, press the "Verify" button to ensure that the flash went smoothly.

When the verification process has finished, disconnect the clip from the PC first, before removing the clip from the BMC chip.

Check the system to see if the issues have been resolved.

If this still does not fix your issues, you will have to contact the system PM and request either a new BMC chip or motherboard.