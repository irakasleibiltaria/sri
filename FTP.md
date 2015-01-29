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
