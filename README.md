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


