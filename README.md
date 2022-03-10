# thinkbook-14s-yoga
Some additional configuration steps and configs neccessary to run gentoo linux on the Lenovo Thinkbook 14s yoga perfectly.

## a note to BIOS update 2.0

Since march 2021, there is a Bios-update to BIOS version 2.0 available. There come some issues with this update, as you can read in the [official user-to-user lenovo forum](https://forums.lenovo.com/t5/ThinkBook-Notebooks/14s-Yoga-ITL-BIOS-2-00-Update-BSOD-Nightmare/m-p/5072720).

With the BIOS V2.0 the linux system will freeze randomly (after a minute, or after two days, or [...]). On windows, you got a bluescreen with the error message WHEA_UNCORRECTABLE_ERROR.
As you can see: you shouldn't update to V2.0. But if you stay on version 1.0.7 then you have to live with some theoretical security vulnerabilities in the processor microcode (I'm not sure if they are really that relevant in practice, though).

At time of writing this (08-06-2021) the "bios update to version 2.0" is not fixed by lenovo.

## cooling / thermal problems

I have regocnized, that the thinkbook gets very hot while I've compiled my entire system.

I've used the thermald-service to handle this problem. To use this service, you have to enable some kernel options, according to the gentoo wiki (Enable additional drivers: https://wiki.gentoo.org/wiki/Power_management/Guide/de#Enabling_additional_drivers):

```
Device Drivers --->
  -*- Generic Thermal sysfs driver --->
    (0)   Emergency poweroff delay in milli-seconds                                                                              
    [*]   Expose thermal sensors as hwmon device                                                                                
    -*-   Enable writable trip points                                                                                          
          Default Thermal governor (step_wise)  --->                                                                           
    [*]   Fair-share thermal governor                                                                                           
    -*-   Step_wise thermal governor                                                                                            
    -*-   Bang Bang thermal governor                                                                                            
    -*-   User_space thermal governor                                                                                           
    [ ]   Thermal emulation mode support
    [*]   Power allocator thermal governor   
    <M>   Intel PowerClamp idle injection driver
    <M>   X86 package temperature thermal driver
    < >   Intel SoCs DTS thermal driver                                                                                          
          ACPI INT340X thermal drivers  --->                                                                                     
             <M> ACPI INT340X thermal drivers
    <M>   Intel PCH Thermal Reporting Driver
```

now, install thermald
```bash
emerge -va sys-power/thermald
```
after this, you should add the following config files to your /etc-folder
```bash
git clone https://github.com/ph0llux/thinkbook-14s-yoga
cp -v thinkbook-14s-yoga/etc/thermald/thermal-conf.xml /etc/thermald/
cp -v thinkbook-14s-yoga/etc/thermald/thermal-cpu-cdev-order.xml /etc/thermald/
```
...and last but not least, you should enable the service
```bash
# for systemd users:
systemctl enable --now thermald
# for openrc users:
rc-config add thermald
```

## no audio output

for audio output you need ```=sys-firmware/sof-firmware-1.6.1```.
The newer firmware-version 1.9.3 won't run perfectly with the thinkbook. You should mask the version:
```bash
echo ">sys-firmware/sof-firmware-1.6.1" > /etc/portage/package.mask/sof-firmware
```

Additionally (if you're using gnome or a similar DE), you should switch from pulseaudio to pipewire.
You can do this by adding the useflag ```screencast``` globally and reflecting a world update by running ```emerge -vauDN @world```.
Now, you have to start the pipewire service at the user-session and replace the pulseaudio service. You can find the appropriate commands for your init system (systemd, openRC, ...) here: https://wiki.gentoo.org/wiki/PipeWire.

There are some issue's with pulseaudio (e.g. no sound at speaker), which are not present with pipewire.

## thunderbolt

first, install ```sys-apps/bolt```:
```bash
emerge -va sys-apps/bolt
```

second, activate USB4 in kernel config.

now everything should be fine.

## touchscreen

you should install libwacom:
```bash
emerge -va dev-libs/libwacom
```

touchscreen works fine with linux >=5.10.
