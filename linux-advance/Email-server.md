Dovecot is used to allow users to access their email by either imap or pop protocols. We assume a domain name of example.com which should be changed by the reader and can be either a real domain name for a fully qualified email server or a fake domain name if you only wish to implement an internal mail server. We assume that the physical mail server (host) is mail.example.com and is located at the private IP address 192.168.0.1 (this should be changed to suit the readers needs). The email server will provide email accounts via standard user system accounts and users will access their email using their system account username and password. We will assume a user called John Smith who has a system account under the login name of john.

####Installation:
```
yum install postfix dovecot
yum remove sendmail
```
###Configuration:
####postfix:
vi /etc/postfix/main.cf
```
myhostname = mail.example.com
mydomain = example.com
myorigin = $mydomain
inet_interfaces = all
mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain
mynetworks = 192.168.0.0/24, 127.0.0.0/8
relay_domains =
home_mailbox = Maildir/
```
Note: Each line should start at the beginning of a new line and should not be preceded by white space or tabs. White space or tabs at the beginning of a line are treated as a continuation of the previous line, and if the previous line is a comment (#) line then the subsequent line is also treated as such. Further, inline comments should be avoided.

Now lets take a look at each setting it turn to understand what we've just done:

myhostname: is the host name of the system (i.e, the system is called mail or mail.example.com).

mydomain: is the domain name for the email server (it can be a real or fake domain name).

myorigin: is the domain name that locally-posted email appears to have come from and is delivered to.

inet_interfaces: sets the network interfaces that Postfix can receive mail on. These need to include at least localhost and the local domain.

mydestination: is the list of domains that will be delivered to (i.e, this server is the final destination for email addressed to these domains).

mynetworks: is a list of trusted IP addresses that may send or relay mail through the server. Users attempting to send email through the server originating from IP addresses not listed here will be rejected.

relay_domains: is a list of destination domains this system will relay mail to. By setting it to be blank we ensure that our mail server isn't acting as an open relay for untrusted networks. The reader is advised to test that their system isn't acting as an open relay here: http://www.abuse.net/relay.html

home_mailbox: sets the path of the mailbox relative to the users home directory and also specifies the style of mailbox to be used. Postfix supports both Maildir and mbox formats and readers are encouraged to read up on the merits of each for themselves. However, in this article we have chosen to use Maildir format (a trailing slash indicates Maildir format. To specify mbox format, the reader would use home_mailbox = Mailbox).

####Dovecot
vi /etc/dovecot.conf
```
protocols = imap imaps pop3 pop3s
mail_location = maildir:~/Maildir
pop3_uidl_format = %08Xu%08Xv
# Required on x86_64 kernels
login_process_size = 64
```
protocols: specifies the protocols available to users to access their email. Dovecot supports imap(s) and pop3(s), and any or all may be used.

mail_location: specifies the format and location of each users mailbox. Here we see we are using maildir format and each user has their mailbox located at ~/Maildir. Examples for mbox format are provided in the configuration file.

pop3_uidl_format: is required to fix a problem with Outlook 2003 accessing mailboxes via pop3 so it makes sense to set this (see the notes in the configuration file for more details).

login_process_size: The release notes for CentOS 5.1 state that "the Dovecot package on x86_64 kernels requires the parameter "login_process_size = 64" to be added to /etc/dovecot.conf after an upgrade to CentOS 5.1". 32-Bit installations are unaffected and do not require this setting.

Note: If you have any issues connecting with either imap or pop3 to dovecot, check the IMAP specific settings and POP3 specific settings sections of the dovecot.conf configuration file for workarounds. The available options mostly affect older mail clients and workarounds for Microsoft Outlook and Outlook Express.

Note on dovecot and C6: With CentOS 6 the configuration moved to /etc/dovecot/dovecot.conf. Dovecot can be started without any further changes to the configuration file and will automatically listen for connections on the ports for pop3(s) and imap(s). Changes to suite your environment will probably be required.

#### CReate Users Mailbox:
```
# Person who should get root's mail
root:           john
# User aliases
jsmith:         john
j.smith:        john
```
####Starting the mail server:
```
chkconfig --level 345 dovecot on
/etc/init.d/dovecot start
/etc/init.d/postfix start
```
Postfix is an extremely powerful and versatile mail transport agent. In this article we have seen how to implement a basic email server using postfix and dovecot for a single domain based on system user accounts. We have barely scratched the surface of the true capabilities of a postfix-based system, but hopefully have provided a solid working foundation on which new users can build.


