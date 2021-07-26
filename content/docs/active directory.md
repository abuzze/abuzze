---
title: "AD Cheatsheet"
date: "26.07.2021"
draft: false
tags: ['cheat sheet', 'AD']

---

# Windows/Mac Active Directory Cheatsheet

Show Kerberos Tickets and create sessions for a user account
```
klist
kinit user@domain.tld

klist
kdestroy

```
Show AD Settings on a macbook
```
dsconfigad -show
```

[Troubleshoot](https://www.amsys.co.uk/using-dscl-to-troubleshoot-directory-issues/) Directory Service issues on a Macbook
```
dscl

ls
```
```
dsAttrTypeNative:userAccountControl: 512
```
```
change usabilitytest dsAttrTypeNative:userAccountControl 66080 512
```


Find all account computer accounts in the active directory
```
(Get-ADComputer -Filter * -Properties PasswordLastSet,LastLogonDate,OperatingSystem | ? { $_.LastLogonDate -GT $(Get-Date).AddDays(-180) } | Select Name,PasswordLastSet,LastLogonDate,Operatingsystem | ? {$_.OperatingSystem -like "MAC*"} | Sort-Object PasswordLastSet -Descending).count
```