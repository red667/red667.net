+++
date = "2016-05-14T19:05:24+02:00"
draft = false
title = "backscatter"

+++

## How to circumvent Backscatter

### what is backscatter mail?
When a spammer or an other attacker sends you an email with the sender domain being your own:

```
EHLO example.com
MAIL FROM: test@example.com
RCPT TO: test@example.com
data
...
```

### why to do something against it
I use this to send spearphishing attacks to customers when I perform social engineering email attacks. So if you leave this attack vector open I can send you email for example from your boss your it department etc.

### how to fix this in postfix
On stackoverflow I found some methods how to fix this for postfix: [Link](https://serverfault.com/questions/688758/reject-incoming-emails-that-use-your-own-domain-as-sender#comment849777_688758)

I chose the 3rd method. Since I just have my mail accounts this was the easiest one.

Add you mail addresses and logins to `/etc/postfix/smtpd_sender_login_maps`

```
test@example.com test
foo@example.com foo
```

Then load the file with the command `sudo postmap /etc/postfix/smtpd_sender_login_maps`

And finally add following lines to `/etc/postfix/main.cf`

```
smtpd_sender_restrictions = reject_unknown_sender_domain,
                            reject_authenticated_sender_login_mismatch,
                            reject_non_fqdn_sender,
                            reject_sender_login_mismatch,
                            reject_unverified_sender,
                            reject_sender_login_mismatch,
                            permit_sasl_authenticated
                            reject_unlisted_sender,
                            permit
```

Now your postfix should reject unauthenticated senders with your domain. To test this just connect to the mail server with telnet:
```
telnet example.com 25
EHLO example.com
MAIL FROM: <test@example.com>
RCPT TO: <test@example.com>
```
And it should trigger this error message:
```
553 5.7.1 <test@example.com>: Sender address rejected: not logged in
```
