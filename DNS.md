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
www	IN	CNAME	server
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

Edit Edit /etc/bind/named.conf.local and and the following:
```
zone "1.168.192.in-addr.arpa" {
        type master;
        file "/etc/bind/db.192";
};
```
Now create the /etc/bind/db.192 file:
```
sudo cp /etc/bind/db.127 /etc/bind/db.192
```
Next edit /etc/bind/db.192 changing the basically the same options as /etc/bind/db.example.com:
```
;
; BIND reverse data file for local 192.168.1.XXX net
;
$TTL    604800
@       IN      SOA     ns.example.com. root.example.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      ns.
1       IN      PTR     ns.example.com.
```
Test:
```
C:\ nslookup

C:\ ping -a 192.168.1.1

$ dig -x 192.168.1.1
```
### Secondary Master

https://help.ubuntu.com/12.04/serverguide/dns-configuration.html#dns-secondarymaster-configuration

https://help.ubuntu.com/community/BIND9ServerHowto

http://www.barlow.eu.com/techblog/installing-master-and-slave-dns-servers-using-bind-9-on-ubuntu-12-04/

http://www.microhowto.info/howto/configure_bind_as_a_slave_dns_server.html


Master:
/etc/bind/named.conf.local
```
zone "ubuntu.zubiri" {
	type master;
	file "/etc/bind/db.ubuntu.zubiri";
	allow-transfer { 192.168.1.2; };
	notify yes;
};
```
Slave:
```
zone "ubuntu.zubiri" {
	type slave;
	file "/var/lib/bind/db.ubuntu.zubiri";
	masters { 192.168.1.1; };
};
```


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

### Multiple zones same bind server

http://ubuntuforums.org/showthread.php?t=1271929

/etc/bind/named.conf.local
```
zone "ubuntu.zubiri" {
	type master;
	file "/etc/bind/db.ubuntu.zubiri";
	allow-transfer { 192.168.1.2; };
	notify yes;
};

zone "ubuntu2.zubiri" {
	type master;
	file "/etc/bind/db.ubuntu2.zubiri";
};
```

/etc/bind/db.ubuntu2.zubiri
```
;
; BIND data file for ubuntu.zubiri
$TTL	604800
$ORIGIN ubuntu2.zubiri.
@	IN	SOA	ubuntu.zubiri. server.ubuntu.zubiri. (
			      2		; Serial
			 604800		; Refresh
			  86400		; Retry
			2419200		; Expire
			 604800 )	; Negative Cache TTL
;
@	IN	NS	ns.ubuntu.zubiri.
; A
server	IN	A	192.168.1.1
; CNAME
www	IN	CNAME	server
```
