# thinkbook-14s-yoga
Some additional configuration steps and configs neccessary to run gentoo linux on the Lenovo Thinkbook 14s yoga perfectly.

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

for audio output you need ```>=sys-firmware/sof-firmware-1.6.1```.

As time of writing this, the package is available on the portage tree, but only in version 1.5.1.
You can build your own ebuild and a local repository to install sof-firmware.
I've copied the ebuild of =sof-firmware-1.5.1 from gentoo-tree and renamed to sof-firmware-1.6.1 in my own repository to handle this.

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
