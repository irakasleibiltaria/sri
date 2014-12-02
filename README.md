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

######VirtualHosts:

http://httpd.apache.org/docs/current/mod/core.html#directory

http://httpd.apache.org/docs/2.4/mod/core.html.en#options

```
# create a new VirtualHosts
# http://docs.domain.com 
# add a CNAME record "docs" in DNS

$ sudo cp /etc/apache2/sites-available/default /etc/apache2/sites-available/mynewsite

$ sudo vim /etc/apache2/sites-available/mynewsite

<VirtualHost *:80>
	ServerAdmin webmaster@localhost
	
	ServerName ubuntu.zubiri
	
	ServerAlias docs.ubuntu.zubiri

	DocumentRoot /home/ubuntu

	#DirectoryIndex	index3.html

	#<Directory />
	#	Options FollowSymLinks
	#	AllowOverride None
	#</Directory>
	#<Directory /home/zubiri/>
	#	Options Indexes FollowSymLinks MultiViews
	#	AllowOverride None
	#	Order allow,deny
	#	allow from all
	#</Directory>

</VirtualHost>
```

enable mynewsite:
```
$ sudo a2ensite mynewsite
$ sudo service apache2 reload
```

disable site
```
$ sudo a2dissite mynewsite
$ sudo service apache2 reload
```

http://httpd.apache.org/docs/current/mod/core.html

######modules

enable disable modules:
```
$ sudo a2enmod ...
$ sudo a2dismon ...
```
list of enabled mudules:
```
$ apache2ctl -M
```

######.htaccess:

http://httpd.apache.org/docs/current/mod/core.html#allowoverride

http://httpd.apache.org/docs/current/howto/htaccess.html

######Authentication and Authorization
http://httpd.apache.org/docs/2.4/howto/auth.html

tutorials:

https://www.linode.com/docs/websites/authbased-access-control-with-apache

http://blog.unlugarenelmundo.es/2010/03/13/autenticacion-en-apache-basica-y-pam/

Example:

Create .htpassword-users
```
$ htpassword -c .htpassword-users username
```
add new user
```
$ htpassword .htpassword-users username2
```
Configure virtualhost:
```
	<Directory /home/ubuntu/>
		AuthType Basic
		AuthUserFile /home/ubuntu/.htpassword-users
		AuthName "only name"
		Require valid-user
	</Directory>
```


######Access control

http://httpd.apache.org/docs/current/howto/access.html

http://httpd.apache.org/docs/2.2/howto/access.html

######Authentication

http://httpd.apache.org/docs/current/mod/mod_authn_core.html

http://httpd.apache.org/docs/2.4/programs/htpasswd.html

ldap authentication:

http://httpd.apache.org/docs/current/mod/mod_authnz_ldap.html


######Alias:

http://httpd.apache.org/docs/current/mod/mod_alias.html
```
...
```

######https/ssl:

https://httpd.apache.org/docs/2.4/ssl/ssl_howto.html

https://help.ubuntu.com/14.04/serverguide/httpd.html#https-configuration

https://help.ubuntu.com/12.04/serverguide/certificates-and-security.html

Tutorial:

https://www.digitalocean.com/community/tutorials/how-to-create-a-ssl-certificate-on-apache-for-ubuntu-14-04

https://www.linode.com/docs/security/ssl/ssl-certificates-with-apache-2-on-ubuntu-10-10-maverick

Configure:
```
$ sudo a2enmod ssl
$ sudo a2ensite default-ssl
$ sudo service apache2 reload
```
To use your own certificate:
```
$ sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt
```
/etc/apache2/sites-availables/default-ssl
```
	SSLCertificateFile /..../apache.crt
	SSLCertificateKeyFile /..../apache.key
```

######Redirect:

redirect http to https (for example)

https://www.sslshopper.com/apache-redirect-http-to-https.html

https://wiki.apache.org/httpd/RedirectSSL

http://httpd.apache.org/docs/trunk/mod/mod_alias.html#redirect

Redirect from http://www.domain.com TO https://www.domain.com:
Add this in the virtualhost of http://www.domain.com
```
Redirect permanent / https://www.domain.com/
```


######modules: php, ...


######Test:

telnet
```
c:/> telnet www.domain.com 80

GET

```

curl
```
c:/> curl ....
```


Security:


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

## Mail Server

https://help.ubuntu.com/14.04/serverguide/email-services.html

https://help.ubuntu.com/community/Postfix

https://www.linode.com/docs/email/postfix/postfix-smtp-debian7

http://www.postfix.org/

http://dovecot.org/

http://squirrelmail.org/

http://www.exim.org/

http://spamassassin.apache.org/

http://www.zimbra.com/

http://products.office.com/en-us/exchange/email

http://products.office.com/en-us/outlook/email-and-calendar-software-microsoft-outlook

https://www.mozilla.org/en-US/thunderbird/features/

http://mailchimp.com/

http://www.mailgun.com/

### DNS MX records
```
@	IN	MX	10   mail.domain.com.
; OR
; domain.com.	IN	MX	10   mail.domain.com.
mail	IN 	A	192.168.1.1
```
To test MX record:
```
$ dig -t MX domain.com
```

### M_X_A

The sender uses a Mail User Agent (MUA), or email client, to send the message through one or more Mail Transfer Agents (MTA), the last of which will hand it off to a Mail Delivery Agent (MDA) for delivery to the recipient's mailbox, from which it will be retrieved by the recipient's email client, usually via a POP3 or IMAP server.
```
MUA -(smtp)-> MTA -(smtp)-> ... -(smtp)-> MTA -(smtp)-> MDA -(pop|imap)-> MUA
```
### Postfix

```
$ sudo apt-get install postfix

Local

System mail name: server.domain.com

```
Reconfigure: 
```
$ sudo dpkg-reconfigure postfix
```

```
1. Internet
(1. Local only: if you want to use ONLY with local clients like squirrelmail)
2. domain.com
3. admin_user_name
4. domain.com, mail.example.com, localhost.localdomain, localhost
5. No
6. 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128
7. 0
8. +
9. All
```

Send a test mail:
```
sendmail recipient@elsewhere.com
From: you@example.com
Subject: Test mail
This is a test email
.
```
Now you can see the email in /var/email
If you want to change mailbox to other place:
```
$ sudo postconf -e 'home_mailbox = Maildir/'
``` 
Now, the email is stored in /home/user/Maildir

Postfix sends all log messages to /var/log/mail.log

####SSL/TLS postfix
SSL/TLS

https://help.ubuntu.com/14.04/serverguide/postfix.html#postfix-smtp-authentication

http://www.postfix.org/TLS_README.html


####Telnet smtp commands
http://www.yuki-onna.co.uk/email/smtp.html

```
telnet mail.domain.zz 25


```

### Dovecot

```
$ sudo apt-get install dovecot-imapd dovecot-pop3d
```

/etc/dovecot/dovecot.conf
```
protocols = pop3 pop3s imap imaps
```
/etc/dovecot/conf.d/10-mail.conf
```
mail_location = maildir:~/Maildir # (for maildir)
or
mail_location = mbox:~/mail:INBOX=/var/spool/mail/%u # (for mbox)
```
Restart
```
sudo service dovecot restart
```

Dovecot configuration parameters list:
```
$ doveconf -n
OR
$ doveconf -a
```

POP3 test:

http://www.anta.net/misc/telnet-troubleshooting/pop.shtml

https://workaround.org/ispmail/lenny/test-fetching-with-imap-and-pop3

POP3:
```
telnet mail.domain.com 110
(telnet mail.domain.com pop3)

user <username>
pass <password>
list
retr <num>
quit
```

IMAP:
```
telnet mail.domain.com imap


```
Errors:

Plaintext: http://www.dovecot.org/list/dovecot/2012-June/083863.html

Solution:

```
/etc/dovecot/conf.d/10-auth.conf

disable_plaintext_auth = no
```
restart:
```
sudo service dovecot restart
```

###Squirrelmail

https://help.ubuntu.com/community/Squirrelmail

http://squirrelmail.org/

```
$ sudo apt-get install php5
$ sudo apt-get install squirrelmail
```
Configure:
```
$ sudo squirrelmail-configure
...
```
Apache configuration:
```
$ sudo cp /etc/squirrelmail/apache.conf /etc/apache2/sites-available/squirrelmail.conf
$ sudo a2ensite squirrelmail.conf
$ sudo service apache2 reload
```
test:
```
http://localhost/squirrelmail
```
Admin:

http://squirrelmail.org/docs/admin/admin-5.html

TODO:

- HTTPS
- 

###Exchange 2008
TODO


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


