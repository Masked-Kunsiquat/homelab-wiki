---
title: Postfix Configuration
description: 
published: 1
date: 2024-03-20T05:36:36.162Z
tags: 
editor: markdown
dateCreated: 2024-03-20T05:36:27.246Z
---


- [ ] Install dependencies for SMTP/postifx connection on Proxmox host.

```
apt install -y libsasl2-modules mailutils
```

- [ ] Generate an app-password to your logging-email.

*I have a specific email through Gmail for my HomeLab logging.*

- [ ]  Generate password file on Proxmox host.

```
echo "smtp.gmail.com your-email@gmail.com:YourAppPassword" > /etc/postfix/sasl_passwd
```

- [ ]  Encode the password file to the proper header format.

```
postmap hash:/etc/postfix/sasl_passwd
```

- [ ] Set the password file to the necessary permissions.

```
chmod 600 /etc/postfix/sasl_passwd
```

- [ ] Edit the postfix configuration file.

```
nano /etc/postfix/main.cf
```

- [ ] Paste the following at the bottom of the configuration file.

```
# Google Mail Configuration

relayhost = smtp.gmail.com:587
smtp_use_tls = yes
smtp_sasl_auth_enable = yes
smtp_sasl_security_options =
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_tls_CAfile = /etc/ssl/certs/Entrust_Root_Certification_Authority.pem
smtp_tls_session_cache_database = btree:/var/lib/postfix/smtp_tls_session_cache
smtp_tls_session_cache_timeout = 3600s
```

- [ ] Reload postfix.

```
postfix reload
```

- [ ] Send a test email.

```
echo "This is a test message sent from postfix on my Proxmox Server" | mail -s "Test Email from Proxmox" your-email@protonmail.com
```

### Fixing the “from” name in emails

- [ ] Install dependency.

```
apt update
apt install postfix-pcre
```

- [ ] Edit the configuration file.

```
nano /etc/postfix/smtp_header_checks
```

- [ ] Paste the following text.

```
/^From:.*/ REPLACE From: PVE-alert <pve-alert@something.com>
```

- [ ] Hash the file.

```
postmap hash:/etc/postfix/smtp_header_checks
```

- [ ] Check the contents of the file.

```
cat /etc/postfix/smtp_header_checks.db
```

- [ ] Add the module to the postfix configuration.

```
nano /etc/postfix/main.cf
```

- [ ] Paste the following to the end of the file.

```
smtp_header_checks = pcre:/etc/postfix/smtp_header_checks
```

- [ ] Reload postfix.

```
postfix reload
```