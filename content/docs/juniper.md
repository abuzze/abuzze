---
title: "Juniper Cheatsheet"
date: "26.07.2021"
draft: false
tags: ['cheat sheet']
---
# Juniper Cheatsheet

### Configuration

Run a command in configure mode
```
run ping 172.20.1.1
run show interface terse
```

### copy configuration via terminal

Delete the old config first
```
delete vlans
delete interfaces
delete system
etc.
```
Copy the complete config via ctrl+c
```
load merge terminal relative
show | compare
commit and-quit
```
### Display Infos

Show all interfaces status
```
show interface terse
show interface description
```

Show all configured options as set commands
```
show configuration | display set
show configuration | display set | match irb
```
Show all vlans
```
show configuration vlans
VLAN1 {
  	vlan-id 1;
  	l3-interface irb.1;
}
VLAN5 {
  	vlan-id 5;
  	l3-interface irb.5;
}
show vlans
Routing instance        VLAN name             Tag          Interfaces
default-switch          VLAN1                 1
  	                                                       ae128.0
      	                                                   ge-0/0/0.0*
          	                                               ge-0/0/1.0
                                                         	   ge-0/0/6.0
default-switch          VLAN5                 5
  	                                                       ae128.0
      	                                                   ge-0/0/0.0*
          	                                               ge-0/0/7.0
```


List older configs
```
rollback ?
```
rollback to the last committed config and remove the candidate config
```
rollback
```
rollback to an older config
```
rollback 20
```
compare the current config to a former config
```
show | compare rollback 2
```

compare your current changes to the saved config
```
show | compare
```