# Task
xFusionCorp Industries has planned to set up a common email server in Stork DC. 

After several meetings and recommendations they have decided to use postfix as their mail transfer agent and dovecot as an IMAP/POP3 server. 

We would like you to perform the following steps:

1. Install and configure postfix on Stork DC mail server.

2. Create an email account ravi@stratos.xfusioncorp.com identified by LQfKeWWxWD.

3. Set its mail directory to /home/ravi/Maildir.

4. Install and configure dovecot on the same server.

# Solution
1. Install postfix
2. Add user ravi
3. In file ```/etc/postfix/main.cf```
   * Tell postfix to consider ```stratos.xfusioncorp.com```  to be itself. This way we can send email to ```ravi@stratos.xfusioncorp.com``` instead of ```ravi@stmail01.stratos.xfusioncorp.com```
     * Add ```$mydomain``` as destination ```mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain```
   * Set to use IPv4 only ```inet_protocols = ipv4```
   * Uncomment line to use ```/home/<user>/Maildir/``` instead of ```/var/spool/mail```
     * ```home_mailbox = Maildir/```
5. Reload/Start postfix
6. Install dovecot
7. Edit ```/etc/dovecot/conf.d/10-mail.conf``` and set mail location to use ```/home/<user>/Maildir/```
   * ```mail_location = maildir:~/Maildir```
8. Reload/Start dovecot
