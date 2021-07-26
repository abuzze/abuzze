---
title: "MacOS Cheatsheet"
date: "26.07.2021"
draft: false
tags: ['cheat sheet']
---
### Bonjourbrowser
```
$ dns-sd -B _afpovertcp._tcp
Browsing for _afpovertcp._tcp
DATE: ---Thu 16 Jun 2016---
 9:44:33.789  ...STARTING...
Timestamp     A/R    Flags  if Domain               Service Type         Instance Name
 9:44:33.790  Add        2   9 local.               _afpovertcp._tcp.    netsus-server
```
### speed up cifs/smb
```
	sudo sysctl -w net.inet.tcp.delayed_ack=0
```
persist it in /etc/sysctl.conf

### Launchd

alle Einträge anzeigen
```
	launchctl list
```
### netzwerkverkehr sniffen

alles auf der Netzwerkkarte
```
	sudo tcpdump -n -i en0
```
### DNS Cache löschen

Mac OS X Lion, Mountain Lion (10.7, 10.8)
```
	sudo killall -HUP mDNSResponder
```
Mac OS X Leopard, Snow Leopard (10.5, 10.6)
```
	sudo dscacheutil -flushcache
```
### versteckte Dateien im Finder an/aus
```
	defaults write com.apple.Finder AppleShowAllFiles YES
```
### Mobile Backups on Macbook Pro

	On a MPB with Lion or Mountain Lion the mobile Backups
	under /Volumes/MobileBackups can consume a lot of space.
	You can confirm this by looking into the Storage tab via about
	my mac. This backup is matched against a time maschine backup, but there is no switch in the sys prefs to disable it. But you can via terminal. Reboot afterwards and the mobile backups are gone.
```
	sudo tmutil disablelocal
```
### mySQL starten
```
	sudo /usr/local/mysql/bin/mysqld_safe
```
Alternative
```
	/usr/local/bin/mysql.server start
```
### MongoDB starten

To have launchd start mongodb at login:
```
	ln -sfv /usr/local/opt/mongodb/*.plist ~/Library/LaunchAgents
```
Then to load mongodb now:
```
    launchctl load ~/Library/LaunchAgents/homebrew.mxcl.mongodb.plist
```
Or, if you don't want/need launchctl, you can just run:
```
    mongod --config /usr/local/etc/mongod.conf
```
### Netboot

	C : Forces most Macs to boot from the CD-Rom drive instead of the internal hard drive. Only works with Apple ROM drives and with bootable CD discs.
	D : Forces the first internal hard drive to be the startup disk.
	N : Netboot (New World ROM machines only) – Looks for BOOTP or TFTP Server on the network to boot from
	R : Forces PowerBooks to reset their screen to default size (helpful if you’ve been hooked up to an external montior or projector!)
	T : Target Disk Mode (FireWire) – Puts machines with built-in FireWire into target Disk mode so a system attached with a FireWire cable will have that device show up as a hard drive on their system. Very useful for PowerBooks!
	Mouse Button Held Down : Ejects any mounted removable media.
	Shift : Disables all extensions (Mac OS 7-9), or disables Login items when using Mac OS X 10.1.3 or later. Also works when booting Classic mode up just like you were using the OS natively.
	Option : When using an Open Firmware "New World ROM" capable system, the System Picker will appear and query all mounted devices for bootable systems, returning a list of drives & what OS they have on them. On "Old World" systems the machine will simply boot into it’s default OS without any Finder windows open.
	Space bar : Brings up Apple’s Extension Manager (or Casady & Greene’s Conflict Catcher, if installed) up at startup to allow you to modify your extension set.
	Command-V : Boots Mac OS X into "Verbose Mode", reporting every console message generated during startup. Really shows what’s going on behind the scenes with your machine on startup!
	Command-S : Boots Mac OS X into "Single User Mode" – helpful to fix problems with Mac OS X, if necessary.
	Command-Option : Rebuilds the Desktop (Mac OS 7-9).
	Command-Option-P-R : Erases PRAM if held down immediately after startup tone. Your machine will chime when it’s erased the PRAM, most people will hold this combination for a total of 3 chimes to really flush the PRAM out.
	Command-Option-N-V : Erases NVRAM (Non-Volatile RAM). Used with later Power Macintosh systems mostly.
	Command-Option-O-F : Boots the machine into Open Firmware (New World ROM systems only).
	Command-Option-Shift-Delete : Forces your Mac to startup from its internal CD-ROM drive or an external hard drive. Very helpful if you have a 3rd party CD-ROM drive that is not an Apple ROM device.
	Command-Option-Shift-Delete-#(where #= a SCSI DEVICE ID) : Boot from a specific SCSI device, if you have your 3rd party CD-ROM drive set to SCSI ID 3, you would press "3" as the # in the combination.
