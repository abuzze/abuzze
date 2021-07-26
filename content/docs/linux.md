---
title: "Linux Cheatsheet"
date: "26.07.2021"
draft: false
tags: ['cheat sheet']
---

# Basic Linux Cheatsheet
## Commands are in random order; execute at your own risk

### setup ansible in venv
```
	easy_install pip
	pip install virtualenv virtualenvwrapper
	mkdir ~/venv/ansible_2_5_11
	virtualenv ~/venv/ansible_2_5_11
	source ~/venv/ansible_2_5_11/bin/activate
	pip install nose cryptography tornado
	pip install ansible==2.5.11
```
to deactivate the venv

	deactivate

### Sendmail testen

echo "Subject: test" | /usr/lib/sendmail -v me@domain.com



fehlender Einf. key im Midnight Commander auf OSX

	Ctrl + t markiert in mc einen einzelnen Eintrag

Prozesse die mit D markiert sind finden, um Performanceprobleme zu finden

	while true; do date; ps auxf | awk '{if($8=="D") print $0;}'; sleep 1; done

welchen dateien werden von welchen Prozess genutzt

	lsof -p 16528

Wird ein unlinked file von einem Prozess offen gehalten?

	lsof -a +L1 /dev/file

oder per User

	lsof -u www-data

### in Dateien suchen

	grep -r -i somethingtosearchfor ./
	grep -rn $'\xFEFE' * #utf8 BOM finden auch mit FEFF suchen

### Dateien suchen

	find /var/ -name '*.jpg'

### bestimmte dateien/ordner abcd in einem Unterverzeichis löschen

	find . -name abcd -type d -exec rm -rf {} \;

um zu schauen welche Files betroffen sind

	find . -name abcd -type d -exec echo {} \;

### Prozess in den Hintergrund schicken und mit disown gegen SIGHUP schützen, damit er weiterläuft und man die SSH Verbindung trotzdem beenden kann

	./long_running_script
	ctrl+z #pausiert den Prozess
	bg #schickt ihn in den Hintergrund
	disown #schütz ihn gegen den SIGHUP beim beenden der SSH Session

### Paketverwaltung

Pakete suchen mit apt-get

	apt-cache search Suchbegriff1 [Suchbegriff2]

Infos zum Paket anzeigen

	apt-cache show Paketname

Infos zu möglichen Updates anzeigen

	apt-cache policy <package name>

Version eines Pakets anzeigen

	dpkg -s <packagename>

Dateien die zu einem Paket gehören anzeigen

	dpkg -s <packagename>

PPA hinzufügen und Paket daraus installieren

	sudo add-apt-repository ppa:tuxpoldo/btsync
	sudo apt-get update
	sudo apt-get install btsync

PPA wieder entfernen

	sudo add-apt-repository --remove ppa:PPA_Name/ppa

falls add-apt-repository fehlt muss python-software-properties installiert werden

	sudo apt-get install python-software-properties

Alle Paket updaten und den Vorgang loggen (vorher apt-get update)

	sudo apt-get upgrade 2>&1 | tee ~/apt-get.log


Alte Kernel entfernen um Platz in /boot zu schaffen

Installierte Kernel anzeigen

	dpkg -l | grep linux-image

benutzten Kernel anzeigen

	uname -r

Kernel entfernen

	dpkg --remove linux-image-extra-4.2.0-15-generic


#### nur security updates installieren


sudo cp /etc/apt/sources.list /etc/apt/security.sources.list

Edit the latter to contain only security repositories, then:

sudo apt-get upgrade -o Dir::Etc::SourceList=/etc/apt/security.sources.list

### programe per source installieren


	source nach /tmp downloaden
	auspacken meist mit xfv source.tar.gz

	./configure
	make
	checkinstall -D make install    ##checkinstall muss ggfs. per apt-get installiert werden und erzeugt ein Paket für das auch eine spätere Deinstallation möglich ist.
	dpkg -i <paketname> ## installiert das Paket

	source aus tmp wieder löschen!


###Nicht mehr unterstützte Pakete (LTS) anzeigen

ubuntu-support-status --show-unsupported

### Startup / Initscripte

Damit ein Service beim hochfahren gestartet wird kann man unter Debian/Ubuntu update-rc.d nutzen. Die Init Scripte liegen unter /etc/init.d über rc1-x werden die Initscripte per symlink verlinkt. Das K(ill) oder S(tart) vor den Scriptnamen und die Zahl legen die Reihenfolge und das hoch- oder runterfahren fest.

	# update-rc.d SERVICE-NAME defaults

Um den Service wieder zu entfernen.

	# update-rc.d SERVICE-NAME remove

### Benutzer anlegen/löschen/verwalten

	useradd -m USERNAME -g sudo -s /bin/bash #mit Homeverzeichnis und gleich in die Gruppe sudo
	groups USERNAME #grp eines users anzeigen
	adduser USERNAME GROUP#user in grp adden
	addgroup GROUP #neu Gruppe
	usermod -a username -G sudo # user der gruppe Sudu hinzufügen (visible after next login)
	userdel -r username # User mit seinem Homeverzeichnis löschen
	usermod -d /path/to/new/home -m username #ändert das Homeverzeichnis und kopiert ggfs die Daten aus dem alten

###Dateien verschieben

scp file to host
	scp SourceFile user@host:directory/TargetFile

scp folder FROM host
	scp -r user@host:directory/SourceFolder TargetFolder


#netstat

offen port mit zugehöriger application
	netstat -lnptu
routen anzeigen
	netstat -r

###packen
	tar cvf backup.tar directory
	bzip2 -9 backup.tar
###auspacken
	bunzip backup.tar.bz2
	tar xfv backup.tar

### Möglichkeiten die Linuxdistro rausfinden
	cat /etc/*release
	cat /etc/*version
	cat /etc/issue
	cat /proc/version
	getconf LONG_BIT #64 oder 32 bit

###cert request

	openssl req -nodes -newkey rsa:4096 -keyout domainname.key -out domainname.csr

das Keyfile anschließend sofort mit chmod 600 domainname.key schützen und den Inhalt des csr
unter https://caserver/certsrv in eine cer umwandeln

Request mit alternate names erzeugt man, indem man ein conf file erstellt. Das sollte so aussehen.

[req]
distinguished_name = req_distinguished_name
req_extensions = v3_req

[req_distinguished_name]
countryName = Country Name (2 letter code)
countryName_default = DE
stateOrProvinceName             = State or Province Name (full name)
stateOrProvinceName_default     = Hamburg
localityName = Locality Name (eg, city)
localityName_default = Hamburg
organizationalUnitName = Organizational Unit Name (eg, section)
organizationalUnitName_default = IT
commonName = commonName (Hostname, etc.)
commonName_max = 64
emailAddress = E-Mailaddress
emailAddress_default = yourmailadresse@mail.com
emailAddress_max = 40

[v3_req]
keyUsage = keyEncipherment, dataEncipherment
extendedKeyUsage = serverAuth
subjectAltName = @alt_names

[alt_names]
DNS.1   = foobar.com
DNS.2   = foobar
DNS.3   = secondname.foobar.com
IP.1 = 192.168.1.100
IP.2 = 192.168.1.200

Unter alt_names kann man weitere Namen und IP Adressen hinzufügen. Die Werte unter [req_distinguished_name] müssen nicht unbedingt angepasst werden, da sie beim csr erstellen nochmal abgefragt werden. Dann muss der csr mit dem Configfile erstellt werden:

	openssl req -nodes -newkey rsa:4096 -keyout domainname.key -out domainname.csr -config configfile


### Zertifikate konvertieren

Convert a DER file (.crt .cer .der) to PEM

	openssl x509 -inform der -in certificate.cer -out certificate.pem

Convert a PEM file to DER

	openssl x509 -outform der -in certificate.pem -out certificate.der

Convert a PKCS#12 file (.pfx .p12) containing a private key and certificates to PEM

	openssl pkcs12 -in keyStore.pfx -out keyStore.pem -nodes

You can add -nocerts to only output the private key or add -nokeys to only output the certificates.

Convert a PEM certificate file and a private key to PKCS#12 (.pfx .p12)

	openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt -certfile CACert.crt


### Zertifikate auslesen

openssl x509 -in yourcert.pem -inform pem -noout -text

### Verify if key belongs to certificate

openssl x509 -noout -modulus -in wildcard.domain.net.crt | openssl md5
1f6485eac12a42c17a778f82105b268e

openssl rsa -noout -modulus -in wildcard.domain.net.key | openssl md5
1f6485eac12a42c17a778f82105b268e

### verify chain with openssl

  openssl verify -CAfile RootCert.pem -untrusted Intermediate.pem UserCert.pem

### openssl encrytion
encrypt

  $ openssl enc -aes-256-cbc -salt -pbkdf2 -iter 100000 -in file.txt -out file.txt.enc

decrypt

  $ openssl enc -aes-256-cbc -d -in file.txt.enc -out file.txt


###Ordnergröße sortiert anzeigen im aktuellen Dir
	du -hs * | sort -r


Ordnergröße aller Unterordner summieren und anzeigen

	du -cksh * |sort -g

Alle großen Dateien über 20MB finden und anzeigen

	find / -type f -size +20000k -exec ls -lh {} \; | awk '{ print $9 ": " $5 }'

###symlink
	ln -s quelle ziel


### TCP traffic Analyse
traffic from 10.5.2.3 destined for port 3389
	tcpdump -nnvvS and src 10.5.2.3 and dst port 80 and dst 172.20.1.8 -w caputurefile

	sudo tcpdump -nnvvS src 172.20.0.24 -w caputurefile
	sudo tcpdump -nnvvS port 5353
	sudo tcpdump -nnvvS -n arp

-s 0 #für vollständige packete anhängen.

### mysql service neu starten

falls service mysql restart nicht reicht, um den Prozess neu zu starten ist vielleicht upstart das Problem. Dann hilft

initctl --system stop mysql
initctl --system start mysql

###mysql User anlegen

	mysql -u root -p  #einloggen mit dem root user

alle User anzeigen

	select host, user, password from mysql.user;

neuen User anlegen

	CREATE USER 'username'@'localhost' IDENTIFIED BY 'some_pass';

Berechtigungen für den User der auf alle Datenbanken zugreifen kann:

	GRANT ALL PRIVILEGES ON *.* TO 'username'@'localhost' IDENTIFIED BY 'some_pass';

neuen User anlegen der auf eine bestimmte Datenbank zugreifen kann:

	GRANT ALL PRIVILEGES ON dbname.* TO 'username'@'localhost' IDENTIFIED BY 'some_pass';


ALL PRIVILEGES- as we saw previously, this would allow a MySQL user all access to a designated database (or if no database is selected, across the system)
CREATE- allows them to create new tables or databases
DROP- allows them to them to delete tables or databases
DELETE- allows them to delete rows from tables
INSERT- allows them to insert rows into tables
SELECT- allows them to use the Select command to read through databases
UPDATE- allow them to update table rows
GRANT OPTION- allows them to grant or remove other users privileges


You can grant multiple privileges in one command by separating them with commas: eg:

	GRANT UPDATE, SELECT ON [database name].[table name] TO ‘[username]’@‘localhost’;

Berechtigungen wieder entziehen

	REVOKE [type of permission] ON [database name].[table name] FROM ‘[username]’@‘localhost’;

reload all permissions

	FLUSH PRIVILEGES;

delete a user

	DROP USER ‘demo’@‘localhost’;

Show all Databases

	show databases;

kaputte mysql Tabelle reparieren

	mysql -uroot -p
	use databasename;
	check table tablename;
	repair table tablename;

Datenbank exportieren

	$ mysqldump --lock-tables database -u USER -p [tables] > database.sql

Datenbank importieren

	$ mysql -p dbname -u USER -p < dumpfile.sql

Datenbank auf anderen host importieren
	$ mysql -P 3310 -h 10.0.0.100 -p dbname -u USER -p < dumpfile.sql

Datenbank in Container importieren

 	$ docker exec -i <Container_ID or Container_Name mysql -u[mysql_user] -p[mysql_user_password] [database] < [database_backup_file.sql]

Ausgabe umleiten 2>&1 (hinter Snagview Checkcommands sehr praktisch)

###iptables

#show all Rules with port numbers (exept NAT Rules)

	iptables -L -n

#removes a rule at in a specific chain with a certain line number

	iptables -D INPUT <linenumber>

#accept new mysql connections from a specific ip and insert the rule in third place

	iptables -I INPUT 3 -p tcp -i eth0 -s 172.20.4.17 --dport 3306 -m state --state NEW -j ACCEPT


# Allows HTTP and HTTPS connections from anywhere (the normal ports for websites)

	iptables -A INPUT -p tcp --dport 80 -j ACCEPT

# Drops a Traffic to port 80 for a specific source ip

	iptables -A INPUT -p tcp -s <sourceip> --dport 80 -j DROP

# removes this rule

	iptables -D INPUT -p tcp -s <sourceip> --dport 80 -j DROP

iptables speichern:

	sudo sh -c "iptables-save > /etc/iptables.rules"

iptables laden:

	iptables-restore -c < /etc/iptables.rules


	Basic Rules: (iptables.rules)
	Generated by iptables-save v1.4.12 on Sun Nov 18 21:17:25 2012
	*filter
	:INPUT ACCEPT [0:0]
	:FORWARD ACCEPT [0:0]
	:OUTPUT ACCEPT [0:0]
	-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
	-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT
	-A INPUT -p tcp -m tcp --dport 22 -j ACCEPT
	-A INPUT -p icmp -j ACCEPT
	-A INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
	-A INPUT -j DROP
	-A FORWARD -j DROP
	-A OUTPUT -m conntrack --ctstate NEW,RELATED,ESTABLISHED -j ACCEPT
	-A OUTPUT -j DROP
	COMMIT
	# Completed on Sun Nov 18 21:17:25 2012

to apply iptables on startup: insert this into /etc/network/interfaces

	pre-up iptables-restore < /etc/iptables.rules


script zum iptables sichern

	#!/bin/sh
	sudo sh -c "iptables-save > /etc/iptables.rules"
	STATE=0
	if [ $? -eq 0 ] ;then
	        echo "IPTABLES saved"
	        STATE=1
	else
	        echo "something went wrong"
	fi
	exit $STATE


#### netools vs. iproute2 commands

arp -na 			ip neigh
ifconfig 			ip link
ifconfig -a 		ip addr show
ifconfig -s 		ip -s link
ifconfig eth0 up 	ip link set eth0 up
ipmaddr				ip maddr
iptunnel			ip tunnel

netstat				ss
netstat -i 			ip -s link
netstat -g 			ip maddr
netstat -l 			ss -l
netstat -r 			ip route

route add 			ip route add
route del 			ip route del
route -n 			ip route show


set the ip for eth0 to 172.16.1.1/24

    ifconfig eth0 172.16.1.1/24 up

    ip addr add 172.16.1.1/24 dev eth0

set new default route

    route add default gw 192.168.1.254 eth0

    ip route add default dev eth0 via 192.168.1.254

add/delete static route

    route add -net 192.168.1.0 netmask 255.255.255.0 gw 192.168.1.254
    route del -net 192.168.1.0 netmask 255.255.255.0 gw 192.168.1.254

    ip route add 172.16.29/24 dev eth0
    ip route del 172.16.29/24 dev eth0

auf Routern muss das IP Forwarding aktiviert werden

	sudo sysctl -w net.ipv4.ip_forward=1


### key

erzeugen

	ssh-keygen -t rsa -b 8192 -f mykeyfile_rsa

ssh key ohne ssh-copy-id deployen

	cat ~/.ssh/id_rsa.pub | ssh username@host "cat – >> ~/.ssh/authorized_keys"

alternativ mit ssh-copy-id

	ssh-copy-id -i ~/.ssh/id_rsa.pub remote-host

### Shell Magic
exitcode des letzten Befehls

	echo $?

### CPAN

*aufrufen mit:

	perl -MCPAN -e 'shell'

*Module suchen

	i /Net::SNMP/

*suchen nach dem richtigen Modulnamen und installieren

	install Net::SNMP

### package managment

show installed packages
	dpkg -l


### rsyslog

to prevent rsyslog from spamming the syslog change /etc/rsyslog.d/50-default.conf

	*.*;auth,authpriv.none,cron.none,mail.none              -/var/log/syslog
	cron.*							                          /var/log/cron.log
	mail.*                          -/var/log/mail.log

for syslog-ng uncomment this in /etc/syslog-ng/syslog-ng.conf

	destination cron { file("/var/log/cron"); };
	log { source(src); filter(f_cron); destination(cron); };

and change this line (add cron)

	filter f_messages   { not facility(news, mail,cron) and not filter(f_iptables); };

Don't forget logrotate (/etc/logrotate.d/syslog) or things get ugly


to forward syslog messages to another syslog-ng insert this into the /etc/syslog-ng/syslog-ng.conf

destination d_udp514    {udp("loghost" port (514));};
log { source(src); filter(f_messages); destination(messages); destination(d_udp514);};

for rsyslogd and syslogd add this in /etc/rsyslog.d/50-default.conf or /etc/syslog.conf

*.*   @loggingserverip_or_hostname:514


### DNS Server mit resolfconf ändern ohne Interfaceneustart

	echo "nameserver 8.8.8.8
	search example.com" | sudo resolvconf -a eth0.inet

Damit das dauerhaft greift, muss der DNS Server in /etc/network/interfaces eingetragen sein.

	dns-search example.com
 	dns-search-nameservers 8.8.8.8

### LVM mounten

Install lvm2:
	$ sudo apt-get install lvm2
Load the necessary module(s):
	$ sudo modprobe dm-mod
Scan your system for LVM volumes and identify in the output the volume group name that has your Fedora volume (mine proved to be VolGroup00):
	$ sudo vgscan
Activate the volume:
	$ sudo vgchange -ay VolGroup00
Find the logical volume that has your Fedora root filesystem (mine proved to be LogVol00):
	$ sudo lvs
Create a mount point for that volume:
	$ sudo mkdir /mnt/fcroot
Mount it:
	$ sudo mount /dev/VolGroup00/LogVol00 /mnt/fcroot -o ro,user

###mit chroot auf andere Platten zugreifen

Systen z.B. unter /mount/point einbinden

	# mount -o bind /proc /mount/point/proc
	# mount -o bind /dev /mount/point/dev
	# mount -o bind /dev/pts /mount/point/dev/pts
	# mount -o bind /sys /mount/point/sys

für DNS Auflösung

	# cp /etc/resolv.conf /mount/point/etc/resolv.conf

Shell aufmachen

	# chroot /mount/point /bin/bash


### rename an ubuntu after cloning the vm

change configs in
	/etc/hostname
	/etc/hosts
	/etc/network/interfaces
restart the interfaces
	/etc/init.d/networking restart

### insert new harddrive to vm with LVM

check the lvm and the existing hdds first

	pvdisplay
	fdisk -l

Add the hdd via the vpshere console. The new disk will show up something like this

	"Disk /dev/sdb doesn't contain a valid partition table"

Time to format the new disk and create an LVM Partition. We assume that /dev/sdb is the new drive

fdisk /dev/sdb
n
p
1
2x Enter to use the complete disk
t
8e (to change the partion to LVM)

verify the change with "p"
   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048    88080383    44039168   8e  Linux LVM

Write the new partition table with "w"
Now create a new LVM physical volume

	pvcreate /dev/sdb1

Now we add the Harddrive to our existing VG (Volume Group)

	vgextend u1404template-vg /dev/sdb1


Next we extent the size of the Logical Volume, by the size of the new HDD (42 GB in this example)

lvextend -L+42GB /dev/mapper/u1404template--vg-root

The last step is to resize the filesysem with

resize2fs /dev/mapper/u1404template--vg-root

Now you should see that the main LV is larger



### insert new harddrive in a virtual server

check the drives

	 sudo cat /proc/scsi/scsi

to rescan the drives install

	apt-get install scsitools

rescan

	rescan-scsi-bus.sh

recheck the drives

	 sudo cat /proc/scsi/scsi

list all hdds

	fdisk -l
oder

	fdisk -l /dev/sdb

Disk /dev/sdb: 1073 MB, 1073741824 bytes
255 heads, 63 sectors/track, 130 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes

Disk /dev/sdb doesn't contain a valid partition table

partion mit fdisk anlegen

alle Partitionen auf sdb anzeigen
	fdisk /dev/sdb dann p

neue Partition anlegen

I start fdisk from the shell prompt:
	fdisk /dev/hdb
which indicates that I am using the second drive on my IDE controller. (See Section 2.1.) When I print the (empty) partition table, I just get configuration information.

	Command (m for help): p

	Disk /dev/hdb: 64 heads, 63 sectors, 621 cylinders
	Units = cylinders of 4032 * 512 bytes
	I knew that I had a 1.2Gb drive, but now I really know: 64 * 63 * 512 * 621 = 1281982464 bytes. I decide to reserve 128Mb of that space for swap, leaving 1153982464. If I use one of my primary partitions for swap, that means I have three left for ext2 partitions. Divided equally, that makes for 384Mb per partition. Now I get to work.
	Command (m for help): n
	Command action
	   e   extended
	   p   primary partition (1-4)
	p
	Partition number (1-4): 1
	First cylinder (1-621, default 1):<RETURN>
	Using default value 1
	Last cylinder or +size or +sizeM or +sizeK (1-621, default 621): +384M

Next, I set up the partition I want to use for swap:

	Command (m for help): n
	Command action
	   e   extended
	   p   primary partition (1-4)
	p
	Partition number (1-4): 2
	First cylinder (197-621, default 197):<RETURN>
	Using default value 197
	Last cylinder or +size or +sizeM or +sizeK (197-621, default 621): +128M
	Now the partition table looks like this:
	   Device Boot    Start       End    Blocks   Id  System
	/dev/hdb1             1       196    395104   83  Linux
	/dev/hdb2           197       262    133056   83  Linux
I set up the remaining two partitions the same way I did the first. Finally, I make the first partition bootable:
	Command (m for help): a
	Partition number (1-4): 1
And I make the second partition of type swap:
	Command (m for help): t
	Partition number (1-4): 2
	Hex code (type L to list codes): 82
	Changed system type of partition 2 to 82 (Linux swap)
	Command (m for help): p
The end result:
	Disk /dev/hdb: 64 heads, 63 sectors, 621 cylinders
	Units = cylinders of 4032 * 512 bytes

	   Device Boot    Start       End    Blocks   Id  System
	/dev/hdb1   *         1       196    395104+  83  Linux
	/dev/hdb2           197       262    133056   82  Linux swap
	/dev/hdb3           263       458    395136   83  Linux
	/dev/hdb4           459       621    328608   83  Linux

Finally, I issue the write command (w) to write the table on the disk.

Platten formatieren z.b. mit

	mkfs.ext3 /dev/sdb1

Eintrag in /etc/fstab machen

	/dev/sdb1       /mountpoint       ext3    defaults        0       2



### programm ohne sudo mit setuid bit ausführen

Owner muss root sein
	chown root:wheel program

setuid bit setzen
	chmod u+s program


### smbshares mounten

	sudo apt-get install cifs-utils

On older systems:

	sudo apt-get install smbfs


First, let's create the mount directory. You will need a separate directory for each mount.

sudo mkdir /media/windowsshare
Then edit your /etc/fstab file (with root privileges) to add this line:

//servername/sharename  /media/windowsshare  cifs  guest,uid=1000,iocharset=utf8  0  0
Where

guest indicates you don't need a password to access the share,
uid=1000 makes the Linux user specified by the id the owner of the mounted share, allowing them to rename files,
iocharset=utf8 allows access to files with names in non-English languages. This doesn't work with shares of devices like the Buffalo Tera Station, or Windows machines that export their shares using ISO8895-15.
After you add the entry to /etc/fstab type:

sudo mount -a
This will (re)mount all entries listed in /etc/fstab.




The quickest way to auto-mounting a password-protected share is to edit /etc/fstab (with root privileges), to add this line:

//servername/sharename  /media/windowsshare  cifs  username=msusername,password=mspassword,iocharset=utf8,sec=ntlm  0  0
This is not a good idea however: /etc/fstab is readable by everyone and so is your Windows password in it. The way around this is to use a credentials file. This is a file that contains just the username and password.

Using a text editor, create a file for your remote servers logon credential:


gedit ~/.smbcredentials
Enter your Windows username and password in the file:


username=msusername
password=mspassword
Save the file, exit the editor.

Change the permissions of the file to prevent unwanted access to your credentials:


chmod 600 ~/.smbcredentials
Then edit your /etc/fstab file (with root privileges) to add this line (replacing the insecure line in the example above, if you added it):


//servername/sharename /media/windowsshare cifs credentials=/home/ubuntuusername/.smbcredentials,iocharset=utf8,sec=ntlm 0 0
Save the file, exit the editor.

Finally, test the fstab entry by issuing:


sudo mount -a
If there are no errors, you should test how it works after a reboot. Your remote share should mount automatically.


## nfs shares

### nfs share mounten

	sudo mount -t nfs host:/share /mountpoint


### nfs share über fstab mounten

	host:/share   /mountpoint   nfs    rsize=8192,wsize=8192,timeo=14,intr

## Berechtigungen

# 	Permission 								rwx
7 	read, write and execute 	111
6 	read and write 						110
5 	read and execute 					101
4 	read only 								100
3 	write and execute 				011
2 	write only 								010
1 	execute only 							001
0 	none 											000


$ chmod u=rw,g=rx,o= internalPlan.txt


/home 700
./ssh 700
authorised_key 600
startupscripte in /etc/init.d/ 755

Filepermissions oktal anzeigen

	stat -c "%a %A %n" /usr/bin/*



## umask gilt für einen Prozess und verhindert Filepermissions beim Anlagen.

$ umask          # display current value
0022
$ umask 0077     #set to prevent files from being created with any access by group members or others
$ umask          #display confirms new setting
0077
$ umask -S       # display symbolically
u=rwx,g=,o=

Octal in umask 	Prevents (if requested)
7 							read, write and execute
6 							read and write
5 							read and execute
4 							read only
3 							write and execute
2 							write only
1 							execute only
0 							no permissions

umask u-x,g=r,o+w

user hat kein execute mehr
group darf nur lesen
alle dürfen zusätzlich schreiben

umask a+r
alle dürfen lesen

###installing likewise-open

	apt-get install likewise-open
	domainjoin-cli join domainname.local domainadmin
	lwconfig homedirtemplate %H/%U
	lwconfig AssumeDefaultDomain true
	lwconfig RequireMembershipOf domain\\group^name
	lwconfig CacheEntryExpiry 30

Add the following line to /etc/sudoers:

	%group^name ALL=(ALL) ALL

### aliase

	alias ls='ls -lahn'

### User ausloggen

Damit werden alle Prozesse des Users administator beendet und er wird damit natürlich auch ausgelogt
	pkill -KILL -u administrator

### User sperren

Damit ein User sich nicht mehr einloggen kann sperrt man ihn mit passwd

	passwd -l username


## Netzwerk

### Netzwerkkarten konfigurieren

in /etc/network/interfaces
für DCHP

	auto eth0
	iface eth0 inet dhcp

für statische IP

	iface eth0 inet static
  	address 192.168.1.2
  	netmask 255.255.255.0
  	gateway 192.168.1.1
  	dns-search domain.local
  	dns-nameservers 8.8.8.8 8.8.8.8

### videodownload script

#!/bin/zsh
STATE=0
target=/var/www/sandmann
newfilename=sandmann-`date +%d-%m-%Y`.wmv
logger starte mplayer capture
mplayer -dumpstream -dumpfile $target/$newfilename  mmsh://85.239.122.162/mk3w-3faw-3rqf-enc0-kika\?MSWMExt\=.asf &
sleep 722
killall mplayer
exit $STATE

### SQLITE3 Commands

Open a database

	sqlite3 tablename

Show Tables in this database

	.tables

Show a specific tableschema

	.schema tablename

Show the content of a table

	SELECT * FROM table;

Shows the help

	.help

### autocomplete für services

alle Dienste aus /etc/init.d/ autocompleten (am besten in die .bashrc)

	complete -W "$(ls /etc/init.d/)" service


## Glance installieren (https://github.com/nicolargo/glances)

### pip installieren
wget https://bitbucket.org/pypa/setuptools/raw/bootstrap/ez_setup.py
sudo python ez_setup.py
wget https://raw.github.com/pypa/pip/master/contrib/get-pip.py
sudo python get-pip.py

### python-dev installieren für psutil (Abhängigkeit wird von Glance aufgelöst)
sudo apt-get install python-dev
sudo pip install Glances

sudo pip install psutil (falls psutil doch nicht installiert wurde)

### spezielle Version eines Pakets installieren

sudo pip install ansible==2.0.0.2

### use --user python on OSX 10.11 to bypass a rootless problem while upgrading

pip install --upgrade setuptools --user python

### Script das beim Start jeder SSH Session ausgeführt wird

/etc/ssh/sshrc

nützlich z. B. für login


### http zu https forwarden mit Apache2

zuerst: a2enmod rewrite
ggfs noch: a2enmod ssl

		RewriteEngine On
        RewriteCond %{SERVER_PORT} !^443$
        RewriteRule (.*) https://%{HTTP_HOST}$1 [R=301,L]

### Seite mit htaccess schützen

Entweder in die Directory Directive im vhost.conf oder in eine .htaccess im jeweiligen Ordner

	AuthName "Sitename or Banner"
	AuthType Basic
	AuthUserFile "/path/to/.htpasswd"
	require valid-user

# apt install apache2-utils

#neues htpasswd file
	htpasswd -c /home/pwww/.htpasswd jerry

#password für den User jerry ändern
	htpasswd /home/pwww/.htpasswd-users jerry


# tshark

apt install tshark

sudo usermod -a -G wireshark <username>
logout/login again

Filter for TLS versions

	tshark -T fields -e ssl.record.version

0x0300 SSL 3.0
0x0301 TLS 1.0
0x0302 TLS 1.1
0x0303 TLS 1.2

Filter DNS Querys

	tshark -f "src port 53"




