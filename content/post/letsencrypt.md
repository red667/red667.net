+++
date = "2016-05-14T21:12:33+02:00"
draft = false
title = "letsencrypt"

+++

## letsencrypt on fedora 23 for apache, postfix and dovecot
Here"s a small howto on how I set up SSL encyption for apache, postfix and dovecot with certs from [letsencrypt](https://letsencrypt.org/).

### install letsencrypt
To install letsencrypt just run:
```bash
sudo dnf install -y letsencrypt
```

### generate the certs
Since I already have an apache running with the default webroot I can run this command to generate the certs for the root domain and the sub-domains used for the mail servers:

```bash
letsencrypt --text --email red@red667.net -d red667.net -d mail.red667.net -d imap.red667.net --agree-tos --renew-by-default --webroot --webroot-path /var/www/html certonly
```

### config apache
To add the certs to apache edit following lines in `/etc/httpd/conf.d/ssl.conf`:
```
SSLCertificateFile /etc/letsencrypt/live/red667.net/cert.pem
SSLCertificateKeyFile /etc/letsencrypt/live/red667.net/privkey.pem
```

### config postfix
To add the certs to postfix edit following lines in `/etc/postfix/main.cf`:
```
smtpd_tls_key_file = /etc/letsencrypt/live/red667.net/privkey.pem
smtpd_tls_cert_file = /etc/letsencrypt/live/red667.net/fullchain.pem
smtpd_tls_CAfile = /etc/letsencrypt/live/red667.net/fullchain.pem
smtp_tls_key_file = /etc/letsencrypt/live/red667.net/privkey.pem
smtp_tls_cert_file = /etc/letsencrypt/live/red667.net/fullchain.pem
```

### config dovecot
To add th certs to dovecot edit following lines in `/etc/dovecot/conf.d/10-ssl.conf`
```
ssl_cert = </etc/letsencrypt/live/red667.net/fullchain.pem
ssl_key = </etc/letsencrypt/live/red667.net/privkey.pem
```
