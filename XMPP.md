Servers:

http://xmpp.org/xmpp-software/servers/

http://www.igniterealtime.org/projects/openfire/

https://www.ejabberd.im/

http://jabberd2.org/

Clients:

http://xmpp.org/xmpp-software/clients/

https://www.pidgin.im/

http://www.igniterealtime.org/projects/spark/


## OpenFire

http://www.igniterealtime.org/projects/openfire/

https://www.thefanclub.co.za/how-to/how-setup-im-voip-server-using-openfire-ubuntu-1204

https://www.youtube.com/watch?v=BFHiRYRx79E

```
$ sudo apt-get remove --purge openjdk*

$ sudo add-apt-repository ppa:webupd8team/java
$ sudo apt-get update
$ sudo apt-get install oracle-java6-installer
$ sudo apt-get install oracle-java7-installer

(
OR openjdk? TO TEST
$ sudo apt-get install default-jre
$ sudo apt-get install default-sdk
)

$ wget openfire_3.9.3_all.deb http://www.igniterealtime.org/downloads/download-landing.jsp?file=openfire/openfire_3.9.3_all.deb
$ sudo dpkg -i openfire_X.deb
```

Admin:

```
http://[Server IP]:9090
OR
http://[Server IP]:9090/sertup/index.jsp

user:admin
password:XXXXXX

domain: [IP] or domain.name
```
####Client configuration:
First create user using Web admin tool

Spark:
user: username
Server: [IP] or domain.name

Pidgin:
username: username
Domain: [IP] or domain.name?
Advanced:Connected Server:[IP]

####Plugin
Add search plugin TODO

## eJabberd

Install:
```
$ sudo apt-get install ejabberd
```
Admin:
```
$ http://
```


## Jabberd

https://help.ubuntu.com/14.04/serverguide/jabberd2-server.html
