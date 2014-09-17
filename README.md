# Servicios de Red e Internet

[https://help.ubuntu.com/12.04/serverguide/index.html](https://help.ubuntu.com/12.04/serverguide/index.html)

## Network configuration

[https://help.ubuntu.com/12.04/serverguide/network-configuration.html](https://help.ubuntu.com/12.04/serverguide/network-configuration.html)

```bash
auto eth0
iface eth0 inet static
address 10.0.0.100
netmask 255.255.255.0
gateway 10.0.0.1
```

## DHCP

### Ubuntu DHCP Server

[https://help.ubuntu.com/12.04/serverguide/dhcp.html](https://help.ubuntu.com/12.04/serverguide/dhcp.html)

[http://saulo.net/pub/tcpip/](http://saulo.net/pub/tcpip/)

```bash
$ sudo apt-get update
$ sudo apt-get install isc-dhcp-server
...
$ vim /etc/dhcp/dhcpd.conf
...
$ service isc-dhcp-server restart
```

Ejemplo:

```bash
subnet 192.168.1.0 netmask 255.255.255.0 {
        range 192.168.1.10   192.168.1.20;
        option routers                  192.168.1.1;
        option subnet-mask              255.255.255.0;
        option broadcast-address        192.168.1.255;
        option domain-name-servers      194.168.4.100, 194.168.4.101;

        host bla1 {
                hardware ethernet DD:GH:DF:E5:F7:D7;
                fixed-address 192.168.1.2;
        }
        host bla2 {
                hardware ethernet 00:JJ:YU:38:AC:45;
                fixed-address 192.168.1.20;
        }
}
```

Service:

```bash
$ service isc-dhcp-server start | stop | restart
```

Log errors:
```bash
$ cat /var/log/syslog
```

IPs asigned:
```bash
$ cat /var/lib/dhcpd/dhcpd.leases
```

## DNS

[https://help.ubuntu.com/12.04/serverguide/dns-installation.html](https://help.ubuntu.com/12.04/serverguide/dns-installation.html)

### Installation

```bash
$ sudo apt-get install bind9 
```

Change hostname for FQDN (Fully Qualified Domain Name)

```bash
$ sudo echo "example.com" > /etc/hostname
$ sudo service hostname restart
```
Edit hosts file
```bash
$ vim /etc/hosts

add

127.0.0.1       example.com
```

```bash
$ sudo reboot
```

```bash
$ sudo vim /etc/bind/named.conf.local
```

```bash
zone "example.com" {
	type master;
        file "/etc/bind/db.example.com";
};
```

```bash
$ sudo cp /etc/bind/db.local /etc/bind/db.example.com
```

### test
```bash
c:\> nslookup
```
```bash
$ host
```
