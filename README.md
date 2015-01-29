# Servicios de Red e Internet

[https://help.ubuntu.com/12.04/serverguide/index.html](https://help.ubuntu.com/12.04/serverguide/index.html)

https://help.ubuntu.com/community/BIND9ServerHowto

## Network configuration

[https://help.ubuntu.com/12.04/serverguide/network-configuration.html](https://help.ubuntu.com/12.04/serverguide/network-configuration.html)

To see ethX interface name:
```bash
$ ifconfig -a
```
Edit configuration file:
```bash
$ sudo vim /etc/network/interfaces
```
```bash
auto eth0
iface eth0 inet static
address 10.0.0.100
netmask 255.255.255.0
dns-nameservers 10.0.0.100 8.8.8.8
gateway 10.0.0.1
```





## VSFTPD

VSFTPD

https://security.appspot.com/vsftpd.html

https://help.ubuntu.com/community/vsftpd

Clients:

c:

http://winscp.net/

https://filezilla-project.org/

Options: (/etc/vsftpd.conf)

- disable anonymous login

```
#anonymous_enable=YES
local_enable=YES
```
- chroot or jail users – limit users to only their home directory
```
#anonymous_enable=YES
local_enable=YES
chroot_local_user=YES
allow_writeable_chroot=YES
```
IF Don't work (Ubuntu 12.04 version!!!):

http://www.linuxquestions.org/questions/ubuntu-63/vsftpd-chroot_local_user%3Dyes-is-not-working-554410/

http://www.mikestechblog.com/joomla/operating-systems-section/operating-systems-ubuntu/155-500-oops-vsftpd-refusing-to-run-with-writable-root-inside-chroot.html

https://www.digitalocean.com/community/tutorials/how-to-set-up-vsftpd-on-ubuntu-12-04

http://askubuntu.com/questions/239239/500-oops-vsftpd-refusing-to-run-with-writable-root-inside-chroot-keep-user-j



Other Option, use ssh:

http://www.howtoforge.com/restricting-users-to-sftp-plus-setting-up-chrooted-ssh-sftp-debian-squeeze

https://www.linode.com/docs/tools-reference/tools/limiting-access-with-sftp-jails-on-debian-and-ubuntu

- 


#### Install / config

```
$ sudo apt-get install vsftpd
```

## Tools

wireshark: https://www.wireshark.org/

iptraf: http://iptraf.seul.org/

```
# see configuration lines removing comments
$ grep -v '^#' filename.conf
```


## VirtualBox

### Monowall

[(internal network) PC1 - PC2 - ... - Monowall][Bridge | NAT] <-> internet

http://m0n0.ch/

Use IDE - FAT32 disk to install it.
Disable "Block private networks" in: Interfaces->WAN
Then add an NAT rule for ssh. Don't forget to check the option to apply firewall rule.


### DIG

http://www.thegeekstuff.com/2012/02/dig-command-examples/

### Network NAT

https://www.virtualbox.org/manual/ch06.html#network_nat_service

[PC1 - PC2 - ... - Server][NAT] <-> internet

To create a NAT network. The router is 192.168.15.1
```
VBoxManage natnetwork add -t nat-int-network -n "192.168.15.0/24" -e
```
Port-forwarding is supported (using the "-p" switch for IPv4 and "-P" for IPv6):
```
VBoxManage natnetwork modify -t nat-int-network -p "ssh:tcp:[]:10022:[192.168.15.15]:22"
```
To see the list of registered NAT networks, use:
```
VBoxManage list natnetworks
```
Then configure VirtualBox Network to "Network NAT"


