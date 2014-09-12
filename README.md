# Servicios de Red e Internet

[https://help.ubuntu.com/12.04/serverguide/index.html](https://help.ubuntu.com/12.04/serverguide/index.html)

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
