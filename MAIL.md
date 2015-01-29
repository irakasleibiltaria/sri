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

```
c:\ nslookup
server 192.168.1.1   (DNS server IP. Not required)
set q=MX
domain.com
(OR domain.com.)
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

####test:

http://www.anta.net/misc/telnet-troubleshooting/pop.shtml

https://workaround.org/ispmail/lenny/test-fetching-with-imap-and-pop3

http://wiki2.dovecot.org/TestInstallation

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
telnet mail.domain.com 143

a login "username" "password"
b select inbox
c logout
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
