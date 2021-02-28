# thinkbook-14s-yoga
Some additional configuration steps and configs neccessary to run linux on the lenovo thinkbook 14s yoga perfectly.

## System

Gentoo.

## cooling problems

I have regocnized, that the thinkbook gets very hot while I've compiled my entire system.
First, install thermald
```bash
emerge -va sys-power/thermald
```
second, you should add the following config files to your /etc-folder
```bash
git clone https://github.com/ph0llux/thinkbook-14s-yoga
cp -v thinkbook-14s-yoga/etc/thermald/thermal-conf.xml /etc/thermald/
cp -v thinkbook-14s-yoga/etc/thermald/thermal-cpu-cdev-order.xml /etc/thermald/
```
...and last but not least, you should enable the service
```bash
# for systemd users:
root # systemctl enable --now thermald
# for openrc users:
root # rc-config add thermald
```