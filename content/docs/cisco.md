---
title: "Cisco Cheatsheet"
date: "26.07.2021"
draft: false
tags: ['cheat sheet']
---
# Cisco Cheatsheet

## Enable SSH login on a Catalyst 3850
```
configure terminal
hostname name
ip domain-name name
crypto key generate rsa
ip ssh [time-out seconds | authentication-r etries integer ]
ip ssh version 2
line vty 5 15
exec-timeout 15 0
transport input ssh
access-class authorized-managers in
!
ip access-list extended authorized-managers
permit ip 172.25.0.0 0.0.255.255 any
permit ip host 172.20.3.195 any
permit ip host 172.20.1.133 any
```


## Check this when setting up new switches.

cisco password change
```
enable secret new_password
```

enable  logging to syslogserver:
```
logging ip_syslogserver
```
disable vtp mode
```
vtp off
```
If you use Portchanels, verify at least on side is active not "on"
```
channelgroup 1 mode active
```

```
conf t
interface fa 0/1
switchport access vlan 1

```

### Editing ACLs

Note the line numbering in the required access-list e.g.:
1
2
3
4
```
extended IP access-list 115
   10 access-list 115 deny ip host 222.222.222.222 any
   20 access-list 115 permit ip any any
```
Then enter config mode and insert the line you want to add, prefixing it with the appropriate number to position it where you want in the list (substitute standard for extended in the example below if you are working with a standard ACL):

1
2
3
4
5

```
conf t
ip access-list extended 115
15 deny ip host 111.111.111.111 any
end
```
## SDM
```
sdm prefer

sh plattform tcam util
```
## Spanning Tree
### STP

Changing Root Prio is possible via two ways

```
  spanning-tree vlan vlan-id priority bridge-priority
```
Changing root bridge via macro. Sets prio to 24576 if current root prio is above that value, or to 4096 if below.

```
  spanning-tree vlan vlan-id root primary
```

### Loopguard

LG prevents Loop, with unidirectional links (broken SFPs)

Rootguard cannot be enable on the same port as Loopguard. Loopguard would disable Rootguard on the port.

### UniDirectionalLinkDetection - UDLD

Detects if only one fibre strand is connected

udld aggressive mode try to reenable the link, while normal mode just err-disables the port.

Global config all ports
```
  udld enable | aggressive
```
Config per port
```
  udld port
```
Verify config
```
  show udld
```
Reset err-disabled ports
```
  udld reset
```
## VTP

Pruefungsfrage
1-1001 (VTP1,2,3)
1002-1005 FDDI/Token Ring
extended VLAN 1006-4094 (VTP3)

Unterschiede VTP Versionen
* VTP3 lernt nicht automatisch den Domainnamen
VTP Pruning sorgt dafuer das Switches, deren Mgmt VLAN verschieden ist keine VTP Daten uebertragen.

## Link Aggregation

Static und Dynamic sind miteinander nicht kompatibel

Static - On, ohne Protokoll

Dynamic

*PAgP (Cisco)
*LACP


Etherchannel > Prozess
Portchannel > Interface

Bedingung:
* gleicher Speed auf beiden Seiten
* "access <> access" oder "Trunk <> Trunk"
* gleiche VLANs

```
  sh etherchannel sum
```

## NTP

Configure the ntp on a switch. This also enables the ntp server mode on the switch. Use access lists to prevent other clients
from accessing the ntp services on the switch.
```
  ntp server ip
```
Securing NTP with access list
```
  access-list 1 permit 10.1.0.0 0.0.255.255
  ntp access-group serve-only 1
```
Configures the switch to only answer ntp sync requests from 10.1.0.0/16


Prüfung: NTP Versionen und Unterschiede!


## SNMPv3

To prevent index shuffle during reboot and software updates set
```
  snmp-server ifindex persist
```
Enable and limit the traps to cpu and port-security
```
  access-list 99 permit 10.1.1.0 0.0.0.255
  snmp-server view OPS
  snmp-server group groupZ v3 priv read OPS access 99
  snmp-server group userZ groupZ v3 auth sha secret priv aes 256 anothersecret
  snmp-server enable traps
  snmp-server host ip traps version 3 priv userZ cpu port-security
```
## IP SLA
```
measure udp-jitter, etc from a switch to another switch. Should be visible in librenms
```

## Storm control
```
  storm-control broadcast level 1.0 0.70
```
## Port-Security

enable on all access ports to prevent MAC address flooding

## DHCP Spoofing

configure ip dhcp snooping first and then ip source guard, to prevent devices from using fixed ip addresses.
```
  ip dhcp snooping
  int Te1/0/1
    desc uplink
    ip dhcp snooping trust

  ip dhcp snooping
  int gi1/0/1
    ip verify source
```
show all IP addresses on the switch ports
```
  sh ip verify source
```
### Dynamic ARP Inspection

prevents arp poisoning
```
  int Te1//0/1
    desc uplink
    ip arp inspection trust

  ip arp inspection vlan 5
```