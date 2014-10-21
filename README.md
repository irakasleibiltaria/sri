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

https://help.ubuntu.com/community/BIND9ServerHowto

http://en.wikipedia.org/wiki/List_of_DNS_record_types


### Installation

```bash
$ sudo apt-get install bind9 
```

Change hostname for FQDN (Fully Qualified Domain Name)

```bash
$ sudo echo "server.example.com" > /etc/hostname
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

Test /etc/resolv.conf
```bash
$ cat /etc/resolv.conf
```

```bash
$ sudo vim /etc/bind/named.conf.local

zone "example.com" {
	type master;
        file "/etc/bind/db.example.com";
};
```
```bash
$ sudo cp /etc/bind/db.local /etc/bind/db.example.com
```
```bash
$ sudo vim /etc/bind/db.example.com

;
; BIND data file for example.com
;
$TTL    604800
@       IN      SOA     example.com. server.example.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
        IN      A       192.168.1.10
;
@       IN      NS      ns.example.com.
@       IN      A       192.168.1.10
@       IN      AAAA    ::1
ns      IN      A       192.168.1.10
;A
server	IN	A	192.168.1.1
test	IN	A 	192.168.1.5
;CNAME
www	IN	A	server
```
### DNS test

Windows:
```bash
c:\> nslookup
```
Linux:
[dig host](http://blog.smalleycreative.com/linux/nslookup-is-dead-long-live-dig-and-host/)
```bash
$ host
$ dig
```
Clear DNS cache:
```bash
$ ipconfig /flushdns
```

### Reverse zone

TODO

### Secondary Master

https://help.ubuntu.com/12.04/serverguide/dns-configuration.html#dns-secondarymaster-configuration

https://help.ubuntu.com/community/BIND9ServerHowto

http://www.barlow.eu.com/techblog/installing-master-and-slave-dns-servers-using-bind-9-on-ubuntu-12-04/

http://www.microhowto.info/howto/configure_bind_as_a_slave_dns_server.html

Dumping master file: permission denied: read: http://www.microhowto.info/howto/configure_bind_as_a_slave_dns_server.html
```
Dumping master file: permission denied

An error of the form:

dumping master file: /etc/bind/tmp-nIOVHD85JX: open: permission denied
on ns1 indicates that named has successfully performed a zone transfer, but was unable to write the result to a zone file because it did not have permission to write to the relevant directory.

In this particular case it has been incorrectly configured to write the zone file to /etc/bind. You should not attempt to fix this by granting write access to that directory: there are good security reasons why named should only have read access to its configuration. Instead you should write the zone file to some other location. On Debian-based systems, the appropriate location is /var/lib/bind.
```

Amazon Route 53:

http://aws.amazon.com/route53/

## APACHE

https://help.ubuntu.com/12.04/serverguide/httpd.html

https://httpd.apache.org/docs/2.4/

#### Installation
```bash
$ sudo apt-get install apache2

$ sudo /etc/init.d/apache2 restart
$ sudo apache2ctl restart
```

command line:
```
$ apache2 args
$ apache2 -l
```

http://www.ubuntugeek.com/simple-commands-to-manage-apache2-sites-and-modules.html


#### Configuration

```
/etc/apache2/
#       |-- apache2.conf
#       |       `--  ports.conf
#       |-- mods-enabled
#       |       |-- *.load
#       |       `-- *.conf
#       |-- conf-enabled
#       |       `-- *.conf
#       `-- sites-enabled
#               `-- *.conf

```

```
/etc/apache2/apache2.conf
```
Global configuration parameters:
```
Timeout 300
MaxKeepAliveRequests 100
...
ErrorLog ${APACHE_LOG_DIR}/error.log
...
IncludeOptional mods-enabled/*.load
IncludeOptional mods-enabled/*.conf

Include ports.conf

```
Change default document root



Change default listen port number:

https://httpd.apache.org/docs/2.4/bind.html
```
/etc/apache2/ports.conf
```

VirtualHosts:

http://httpd.apache.org/docs/current/mod/core.html#directory

http://httpd.apache.org/docs/2.4/mod/core.html.en#options

```
# create a new VirtualHosts
# http://docs.domain.com 
# add a CNAME record "docs" in DNS

$ sudo cp /etc/apache2/sites-available/default /etc/apache2/sites-available/mynewsite

$ sudo vim /etc/apache2/sites-available/mynewsite




$ sudo service apache2 reload
```

telnet
```
c:/> telnet www.domain.com 80

GET

```

curl
```
c:/> curl ....
```

Alias:

www.domain.com/aliasname
```
file
```



https/ssl:

https://httpd.apache.org/docs/2.4/ssl/ssl_howto.html


modules: php, ...


Security:


