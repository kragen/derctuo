I'm trying to get my Blue Pill board to run Blink, the hello-world of
embedded development.

<https://www.instructables.com/Getting-Started-With-Stm32-Using-Arduino-IDE/> says:

> 1. Launch Arduino.cc IDE. Click on "File" menu and then "Preferences". 
>
>     The "Preferences" dialog will open, then add the following link
>     to the "Additional Boards Managers URLs" field:
> 
>     "<http://dan.drown.org/stm32duino/package_STM32duino_index.json>"

But I thought maybe this might be outdated and maybe it's better to
use ST's official package.  Boy, was I ever fucking wrong.  It's a
fucking trojan horse.

<https://github.com/stm32duino/wiki/wiki/Getting-Started> says:

> 1. Launch Arduino.cc IDE. Click on "File" menu and then
> "Preferences".
> 
> The "Preferences" dialog will open, then add the following link to
> the "Additional Boards Managers URLs" field:
> 
> <https://github.com/stm32duino/BoardManagerFiles/raw/master/STM32/package_stm_index.json>

This installs, by default, version 1.9.0, which occupies tens of megs,
takes fucking forever, and then gives an error about
STM32CubeProgrammer.  What the fuck is STM32CubeProgrammer?

<https://github.com/stm32duino/wiki/wiki/Upload-methods> says

> STLink                                                                                                         
> 
> Deprecated since core version > 1.5.0 replaced by STM32CubeProgrammer (SWD)                                 
>
> Requires a ST-Link/V2 device connected to the PC over USB and to the board via the SWD interface.           

(Actually, it's not deprecated, it's fucking missing.)

> ...
> 
> STM32CubeProgrammer                                                                                            
> > Since core version > 1.5.0                                                                                  
> 
> ...
> 
>   Requirement                                                                                                  
> 
> To use those upload methods, STM32CubeProgrammer have to be
> installed manually as it is not provided through the tools packages.
> 
> ...
> In any case, if the STM32CubeProgrammer binary is not found, user will be warned like this:                 
> 
>      STM32_Programmer.sh/STM32_Programmer_CLI.exe not found.                                                       
>      Please install it or add '<STM32CubeProgrammer path>/bin' to your PATH environment:`                          
>      https://www.st.com/en/development-tools/stm32cubeprog.html`                                                   
>      Aborting!                                                                                                     

Okay, fuck, I guess I have another fucking hoop to jump through.
What's this STM32CubeProgrammer thing?  Some kind of open-source
firmware uploader?

No, that couldn't be more
wrong. <https://www.st.com/en/development-tools/stm32cubeprog.html> is
pants-shittingly menacing:

> [Please choose a sub-application] An end application is required.                                           
> 
> Nature of Business:                                                                                      
> 
> [ _______________________________] A nature of business is required.                                        
> 
> Military Related:                                                                                        
> 
> [ __] A military relation status is required.                                                               
> 
> Software Country/Region of Use:                                                                          
> 
> [ ___________________________________________] A country of use is required.                                
> Please keep me informed about future updates for this product.                                              
> [ ]                                                                                                         
> 
> Comment:                                                                                                 
> 
> _________________________________________                                                                   
> _________________________________________                                                                   
> _________________________________________                                                                   
> [ ] I accept all Terms & Conditions of the Export Control regulations Accept Terms & Conditions             
> Confirm Request Cancel                                                                                      
> 
> ...
> 
> Request for software successfully submitted. The approval process may take up to 48 hours. After you have  
> been approved, you should receive a link to the requested software via email.                              
>
> ...
> 
>  If you don't want to login now, you can download the software by
>  simply providing your name and e-mail address in the form below and
>  validating it.
>
> ...
> 
> For security / validation purposes, all software download requests
> must originate from a valid email address.
>       
>  BY INSTALLING COPYING, DOWNLOADING, ACCESSING OR OTHERWISE USING THIS
>  SOFTWARE PACKAGE OR ANY PART THEREOF (AND THE RELATED DOCUMENTATION)
>  FROM STMICROELECTRONICS INTERNATIONAL N.V, SWISS BRANCH AND/OR ITS
>  AFFILIATED COMPANIES (STMICROELECTRONICS), THE RECIPIENT, ON BEHALF
>  OF HIMSELF OR HERSELF, OR ON BEHALF OF ANY ENTITY BY WHICH SUCH
>  RECIPIENT IS EMPLOYED AND/OR ENGAGED AGREES TO BE BOUND BY THIS
>  SOFTWARE PACKAGE LICENSE AGREEMENT.
> 
>   \4. This software package or any part thereof, including
>   modifications and/or derivative works of this software package,
>   must be used and execute solely and exclusively on or in
>   combination with a microcontroller or a microprocessor devices
>   manufactured by or for STMicroelectronics.
> 
>   \9. The software package is and will remain the exclusive property of
>   STMicroelectronics and its licensors.  The recipient will not
>   take any action that jeopardizes STMicroelectronics and its
>   licensors' proprietary rights or acquire any rights in the
>   software package, except the limited rights specified hereunder.
> 
>  \10. The recipient shall comply with all applicable laws and
>   regulations affecting the use of the software package or any part
>   thereof including any applicable export control law or
>   regulation.
> 
>  \11. Redistribution and use of this software package partially or any
>  part thereof other than as permitted under this license is void
>  and will automatically terminate your rights under this license.

Regardless of whether you'd have any ethical reason to obey this, or
whether it could actually be enforced in court or not, I have a CKS32,
not an STM32, and the above is a crystal-clear threat from ST: buy our
hardware or else.  Ew ew ew ew ew.

I'm installing version 1.5.0 now, which is still literally 82 fucking
megabytes.  And still takes fucking forever.  Like on the order of
half a fucking hour.

This done, "STLink" appears as an option for "Upload method" under the
Arduino 1.8.14 "Tools" menu.  However, the "Port" submenu is grayed
out, and evidently it can't find the "STLink".  On replugging it, I
see in `dmesg`:

    [802528.168367] usb 2-1: USB disconnect, device number 6
    [802531.948113] usb 2-1: new full-speed USB device number 7 using uhci_hcd
    [802532.117227] usb 2-1: New USB device found, idVendor=0483, idProduct=3748
    [802532.117248] usb 2-1: New USB device strings: Mfr=1, Product=2, SerialNumber=3
    [802532.117261] usb 2-1: Product: STM32 STLink
    [802532.117273] usb 2-1: Manufacturer: STMicroelectronics
    [802532.117285] usb 2-1: SerialNumber: L/28S5KN

And I have a new file in /dev/char:

    lrwxrwxrwx  1 root root   18 Nov  6 19:54 189:134 -> ../bus/usb/002/007

I can read some data from it, so it's probably not a permissions
problem:

    $ < /dev/char/189:134 xxd
    00000000: 1201 0002 0000 0040 8304 4837 0001 0102  .......@..H7....
    00000010: 0301 0902 2700 0101 0080 3209 0400 0003  ....'.....2.....
    00000020: ffff ff04 0705 8102 4000 0007 0502 0240  ........@......@
    00000030: 0000 0705 8302 4000 00                   ......@..

What that means is anybody's guess.  The device disappears if I unplug
the "STLink".

In theory, according to
<https://github.com/rogerclarkmelbourne/Arduino_STM32/wiki/Programming-an-STM32F103XXX-with-a-generic-%22ST-Link-V2%22-programmer-from-Linux>,
OpenOCD should work:

    $ openocd -f /usr/share/openocd/scripts/interface/stlink-v2.cfg \
              -f /usr/share/openocd/scripts/target/stm32f1x.cfg 
    Open On-Chip Debugger 0.9.0 (2018-01-24-01:07)
    Licensed under GNU GPL v2
    For bug reports, read
            http://openocd.org/doc/doxygen/bugs.html
    Info : auto-selecting first available session transport "hla_swd". To override use 'transport select <transport>'.
    Info : The selected transport took over low-level target control. The results might differ compared to plain JTAG/SWD
    adapter speed: 1000 kHz
    adapter_nsrst_delay: 100
    none separate
    Info : Unable to match requested speed 1000 kHz, using 950 kHz
    Info : Unable to match requested speed 1000 kHz, using 950 kHz
    Info : clock speed 950 kHz
    Error: libusb_open() failed with LIBUSB_ERROR_ACCESS
    Error: open failed
    in procedure 'init' 
    in procedure 'ocd_bouncer'

    default@default-Aspire-one:~/Downloads/arduino-nightly$ sudo openocd -f /usr/share/openocd/scripts/interface/stlink-v2.cfg -f /usr/share/openocd/scripts/target/stm32f1x.cfg 
    [sudo] password for default: 
    Open On-Chip Debugger 0.9.0 (2018-01-24-01:07)
    Licensed under GNU GPL v2
    For bug reports, read
            http://openocd.org/doc/doxygen/bugs.html
    Info : auto-selecting first available session transport "hla_swd". To override use 'transport select <transport>'.
    Info : The selected transport took over low-level target control. The results might differ compared to plain JTAG/SWD
    adapter speed: 1000 kHz
    adapter_nsrst_delay: 100
    none separate
    Info : Unable to match requested speed 1000 kHz, using 950 kHz
    Info : Unable to match requested speed 1000 kHz, using 950 kHz
    Info : clock speed 950 kHz
    Info : STLINK v2 JTAG v29 API v2 SWIM v7 VID 0x0483 PID 0x3748
    Info : using stlink api v2
    Info : Target voltage: 3.139057
    Warn : UNEXPECTED idcode: 0x2ba01477
    Error: expected 1 of 1: 0x1ba01477
    in procedure 'init' 
    in procedure 'ocd_bouncer'

That's wonderful!  That's precisely the error I've seen other people
report with the CKS32 devices.  So I made the relevant config change:

    $ diff -u /usr/share/openocd/scripts/target/stm32f1x.cfg ~/devel/dev3/cks32f1x.cfg
    --- /usr/share/openocd/scripts/target/stm32f1x.cfg	2018-01-23 22:08:20.000000000 -0300
    +++ /home/default/devel/dev3/cks32f1x.cfg	2020-11-06 20:46:58.606514993 -0300
    @@ -31,7 +31,7 @@
           set _CPUTAPID 0x3ba00477
        } {
           # this is the SW-DP tap id not the jtag tap id
    -      set _CPUTAPID 0x1ba01477
    +      set _CPUTAPID 0x2ba01477
        }
     }

And then OpenOCD apparently works; anyway it doesn't crash immediately
like before and the LED on the "STLink" turns from orange (?) to blue:

    $ sudo openocd -f /usr/share/openocd/scripts/interface/stlink-v2.cfg
                   -f ~/devel/dev3/cks32f1x.cfg 
    Open On-Chip Debugger 0.9.0 (2018-01-24-01:07)
    Licensed under GNU GPL v2
    For bug reports, read
            http://openocd.org/doc/doxygen/bugs.html
    Info : auto-selecting first available session transport "hla_swd". To override use 'transport select <transport>'.
    Info : The selected transport took over low-level target control. The results might differ compared to plain JTAG/SWD
    adapter speed: 1000 kHz
    adapter_nsrst_delay: 100
    none separate
    Info : Unable to match requested speed 1000 kHz, using 950 kHz
    Info : Unable to match requested speed 1000 kHz, using 950 kHz
    Info : clock speed 950 kHz
    Info : STLINK v2 JTAG v29 API v2 SWIM v7 VID 0x0483 PID 0x3748
    Info : using stlink api v2
    Info : Target voltage: 3.135959
    Info : stm32f1x.cpu: hardware has 6 breakpoints, 4 watchpoints
    ^C

So OpenOCD is successfully connecting!  But I'm still some distance
away from burning the "blink" sketch onto the hardware.

The access-denied problem that led me to try running it with sudo
straces as follows:

    open("/dev/bus/usb/002/008", O_RDWR)    = -1 EACCES (Permission denied)
    write(2, "Error: libusb_open() failed with"..., 53Error: libusb_open() failed with LIBUSB_ERROR_ACCESS

And indeed I don't have permission to write to that device, only read:

    $ ls -l /dev/bus/usb/002/008
    crw-rw-r-- 1 root plugdev 189, 135 Nov  6 20:49 /dev/bus/usb/002/008

However, I *am* in /etc/group as belonging to plugdev, I guess I just
haven't logged out and back in since then.  So I launch the arduino
IDE in the plugdev group:

    arduno-nightly$ sg plugdev ./arduino

However, that doesn't help; the "Port" submenu of "Tools" is still
grayed out.  And `sudo ./arduino` of course doesn't have the STM32
board package installed.  I copied everything in my ~/.arduino15 to
root's, and then the board package is installed, but it has the same
problem.  I tried `chmod 666 /dev/bus/usb/002/008`, and then OpenOCD
works without `sudo`, but the Arduino IDE still has a greyed-out
"Port" menu.

Next steps, I guess, are to try Arduino with the Dan Drown stm32duino
stuff (or maybe [rogerclarkemelbourne's zip
file](https://github.com/rogerclarkmelbourne/Arduino_STM32/wiki/Installation)
([actual
file](https://github.com/rogerclarkmelbourne/Arduino_STM32/archive/master.zip))?);
or to try to program it with OpenOCD; or to use OpenOCD to install
some kind of USB bootloader on it.

But I think that's about all for tonight; I've been trying things on
and off for 9 hours.  I'll see what I can manage tomorrow.  The fact
that now OpenOCD works means that success is in sight.
