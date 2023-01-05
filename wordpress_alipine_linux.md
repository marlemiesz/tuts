# WP on linux alpine
Guide for installing WordPress on a Linux Alpine environment. 

## Install PHP and lighttpd
Update the packagelist:
```shell
apk update
```
Install php:
```shell
apk add php8 lighttpd
```
Install the additional packages:
```shell
apk add lighttpd php8-common php8-session php8-iconv php8-json php8-gd php8-curl php8-xml php8-mysqli php8-imap php8-cgi fcgi php8-pdo php8-pdo_mysql php8-soap php8-posix php8-gettext php8-ldap php8-ctype php8-dom php8-simplexml
```

## Configuration webserver
Edit **lighttpd.conf**:
```shell
vi /etc/lighttpd/lighttpd.conf
```
Uncomment line:
`include "mod_fastcgi.conf"`

**Optional, If you require an SSL connection, you will need to add the following text**
```text
$SERVER["socket"] == ":443" {
ssl.engine                  = "enable"
ssl.pemfile                 = "/etc/lighttpd/ssl/localhost.pem"
ssl.cipher-list 			= "EECDH+AESGCM:EDH+AESGCM:AES128+EECDH:AES128+EDH"
}
```

Edit **/etc/lighttpd/mod_fastcgi.conf**
```shell
vi /etc/lighttpd/mod_fastcgi.conf
```
Change `"bin-path"=>"/usr/bin/php-cgi"` to `"bin-path"=>"/usr/bin/php-cgi8"`

Start lighttpd service and add to needed runlevel
```shell
rc-service lighttpd start && rc-update add lighttpd default
/etc/init.d/lighttpd restart
```
## Installing WordPress
Create a directory named webapps
```shell
mkdir -p /usr/share/webapps/
```
Download the latest WordPress source files
```shell
cd /usr/share/webapps/
wget http://wordpress.org/latest.tar.gz
```
Unpack the archive and delete it afterwards
```shell
tar -xzvf latest.tar.gz
rm latest.tar.gz
```
Change the folder persmissions
```shell
chown -R lighttpd /usr/share/webapps/
```
Symlink to main www folder
```shell
rm /var/www/localhost/htdocs/ -r
ln -s /usr/share/webapps/wordpress/ /var/www/localhost/htdocs
```
Browse to http://YOUR_HOST/ and configure the WP
