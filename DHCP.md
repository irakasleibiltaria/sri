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
