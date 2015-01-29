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

Putty (ssh, telnet, ...)

curl
```
c:/> curl ....
```


Security:
