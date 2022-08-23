# TimeLapse

## Tools
- nmap
- smbclient
- zip2john
- john
- crackpkcs12
- evil-winrm
- Get-LAPSPasswords

## Enumeration
run Nmap

```
nmap -A 10.10.11.152
```

![](https://i.imgur.com/ZIDZdlR.png)
we're dealing with active directory.
there's some services running like kerberos and smb
let's first check if we can connect anonymously to smb and list the shares
## SMB Enumeration
we can connect anonymously and we have on accesible share for us called Shares let's dig into it
![](https://i.imgur.com/mhBvFcO.png)
we have two directories Dev and HelpDesk and they contain some intersting files let's download them to our machine
![](https://i.imgur.com/Md4Egx7.png)
## Crack Passwords
extracting the winrm_backup.zip file, it asks for a password
![](https://i.imgur.com/HH6cgoz.png)

we can [crack it with john](https://dfir.science/2014/07/how-to-cracking-zip-and-rar-protected.html), first extract the hash using
```
zip2john winrm_backup.zip > hash
```
![](https://i.imgur.com/FaoklSn.png)

crack the hash using
```
john hash --wordlist=rockyou.txt
```
![](https://i.imgur.com/Nlt81aI.png)

Inside that zip, there was a [pfx file](https://www.howtouselinux.com/post/pfx-file-with-examples)
so it basically a certificate but it contains both puplic and private key
we can [extract those](https://www.ibm.com/docs/en/arl/9.7?topic=certification-extracting-certificate-keys-from-pfx-file)

but when I try to extract the keys it asks for a passowrd.
searching for a tool to crack it I found [crackpkcs12](https://github.com/crackpkcs12/crackpkcs12)

after I ran it, I got this password.
![](https://i.imgur.com/g4LLs0Y.png)

now we can extract private and public keys
![](https://i.imgur.com/AMpSt85.png)

for PEM pass phrase just use any password you want, you'll use it when you login to the machine

after some research, I found that we can use these files in evil-winrm to connect to the machine without a username
```
evil-winrm -c publickey -k privatekey -i 10.10.11.152 -r timelapse -S
```
![](https://i.imgur.com/V3LIpqZ.png)

user flag
![](https://i.imgur.com/2lhQJQg.png)

## Privilege Escalation
searching through the machine I found this file containing creds for svc_deploy
![](https://i.imgur.com/KiHEpL1.png)

login as svc_deploy
![](https://i.imgur.com/DfIPpvD.png)


## Privilege Escalation to root
remember the folder HelpDesk we dound in the smb shares?
there were some files, all started with [LAPS](https://docs.microsoft.com/en-us/defender-for-identity/security-assessment-laps)
so it's a password manager for windows that generate randomized password upon request
so we can request a password for the administrator
we will use [Get-LAPSPasswords](https://github.com/kfosaaen/Get-LAPSPasswords/blob/master/Get-LAPSPasswords.ps1)
first upload it to the machine and run it
![](https://i.imgur.com/urcgqok.png)

now we can login as administrator
![](https://i.imgur.com/Xh1BTe4.png)

root flag, it was inside TRX's desktop
![](https://i.imgur.com/gpkStpS.png)

## Recourses
- https://dfir.science/2014/07/how-to-cracking-zip-and-rar-protected.html
- https://www.howtouselinux.com/post/pfx-file-with-examples
- https://www.ibm.com/docs/en/arl/9.7?topic=certification-extracting-certificate-keys-from-pfx-file
- https://github.com/kfosaaen/Get-LAPSPasswords/blob/master/Get-LAPSPasswords.ps1
