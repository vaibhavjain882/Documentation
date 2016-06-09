###Asterisk:
Asterisk is an open source framework for building communications applications. Asterisk turns an ordinary computer into a communications server.

###FreePBX:
FreePBX is a web-based open source GUI (graphical user interface) that controls and manages Asterisk (PBX), a voice over IP server.

####Installation And Configuration:
1. Install additional required dependencies:
```

yum install gcc gcc-c++ lynx bison mysql-devel mysql-server php php-mysql php-pear php-mbstring tftp-server httpd make ncurses-devel \
libtermcap-devel sendmail sendmail-cf caching-nameserver sox newt-devel libxml2-devel libtiff-devel audiofile-devel gtk2-devel subversion \ 
kernel-devel git subversion kernel-devel php-process crontabs cronie cronie-anacron wget vim php-xml uuid-devel libtool sqlite-devel unixODBC \
mysql-connector-odbc
```

2. Start mysql and apache.

3. Install Pear DB:
```
pear channel-update pear.php.net
pear install db-1.7.14
```

4. Install Dependencies for Google Voice:
install iksemel
```
wget https://iksemel.googlecode.com/files/iksemel-1.4.tar.gz
tar xf iksemel-*.tar.gz
cd iksemel-*
./configure
make
make install
```

5. Add the Asterisk User
```
adduser asterisk -M -c "Asterisk User"
```

6. Install and Configure Asterisk
download asterisk:
```
wget http://downloads.asterisk.org/pub/telephony/dahdi-linux-complete/dahdi-linux-complete-current.tar.gz
wget http://downloads.asterisk.org/pub/telephony/libpri/libpri-1.4-current.tar.gz
wget http://downloads.asterisk.org/pub/telephony/asterisk/asterisk-13-current.tar.gz
git clone https://github.com/akheron/jansson.git
wget http://www.pjsip.org/release/2.2.1/pjproject-2.2.1.tar.bz2
```

Compile and install DAHDI and LibPRI
```
tar xvfz dahdi-linux-complete-current.tar.gz
tar xvfz libpri-1.4-current.tar.gz
rm -f dahdi-linux-complete-current.tar.gz libpri-1.4-current.tar.gz
cd dahdi-linux-complete-*
make all
make install
make config
cd libpri-1.4.*
make
make install
```
Compile and install pjproject
```
tar -xjvf pjproject-2.2.1.tar.bz2
cd pjproject-2.2.1
CFLAGS='-DPJ_HAS_IPV6=1' ./configure --prefix=/usr --enable-shared --disable-sound\
  --disable-resample --disable-video --disable-opencore-amr --libdir=/usr/lib64
  make dep
  make
  make install
```
Note: Set --libdir=/usr/lib64 if you are using 64 bit machine.

Compile and Install jansson:
```
cd jansson
autoreconf -i
./configure --libdir=/usr/lib64
make
make install
```

Compile and install Asterisk:
```
tar xvfz asterisk-13-current.tar.gz
rm -f asterisk-13-current.tar.gz
cd asterisk-*
contrib/scripts/install_prereq install
./configure --libdir=/usr/lib64
contrib/scripts/get_mp3_source.sh
make menuselect
```

NOTE: You will be prompted at the point to pick which modules to build. Most of them will be enabled, but if you want to have MP3 support, you need to manually turn on 'format_mp3' on the first page.

then
```
make
make install
make config
ldconfig
```

Install Asterisk-Extra-Sounds:
```
mkdir -p /var/lib/asterisk/sounds
cd /var/lib/asterisk/sounds
wget http://downloads.asterisk.org/pub/telephony/sounds/asterisk-extra-sounds-en-wav-current.tar.gz
tar xfz asterisk-extra-sounds-en-wav-current.tar.gz
rm -f asterisk-extra-sounds-en-wav-current.tar.gz
# Wideband Audio download
wget http://downloads.asterisk.org/pub/telephony/sounds/asterisk-extra-sounds-en-g722-current.tar.gz
tar xfz asterisk-extra-sounds-en-g722-current.tar.gz
rm -f asterisk-extra-sounds-en-g722-current.tar.gz
```

####Install and configure freepbx:

Download and extract FreePBX:
```
wget http://mirror.freepbx.org/modules/packages/freepbx/freepbx-12.0-latest.tgz
tar vxfz freepbx-12-latest.tgz
```

Set ownership permissions:
```
chown asterisk. /var/run/asterisk
chown -R asterisk. /etc/asterisk
chown -R asterisk. /var/{lib,log,spool}/asterisk
chown -R asterisk. /usr/lib/asterisk
chown -R asterisk. /usr/lib64/asterisk
mkdir /var/www/html
chown -R asterisk. /var/www/
```

A few small modifications to Apache:
```
sed -i 's/\(^upload_max_filesize = \).*/\120M/' /etc/php.ini
cp /etc/httpd/conf/httpd.conf /etc/httpd/conf/httpd.conf_orig
sed -i 's/^\(User\|Group\).*/\1 asterisk/' /etc/httpd/conf/httpd.conf
service httpd restart
```

Configure Asterisk database in MYSQL:
```
export ASTERISK_DB_PW=amp109
mysqladmin -u root create asterisk 
mysqladmin -u root create asteriskcdrdb 
```

Set permissions on MYSQL database:
```
mysql -u root -e "GRANT ALL PRIVILEGES ON asterisk.* TO asteriskuser@localhost IDENTIFIED BY '${ASTERISK_DB_PW}';"
mysql -u root -e "GRANT ALL PRIVILEGES ON asteriskcdrdb.* TO asteriskuser@localhost IDENTIFIED BY '${ASTERISK_DB_PW}';"
mysql -u root -e "flush privileges;"
```

Restart Asterisk and install FreePBX:
```
./start_asterisk start
./install_amp --installdb --username=asteriskuser --password=${ASTERISK_DB_PW}
amportal chown
amportal a ma installall
amportal a reload
amportal a ma refreshsignatures
amportal chown
```

Finally, one last mod and start FreePBX:
```
ln -s /var/lib/asterisk/moh /var/lib/asterisk/mohmp3
amportal restart
```

Start FreePBX:
```
http://yourlocalipaddress/html or if you prefer http://localhost/admin
```

#### Following are the things to configure in FreePBX dashboard:

Extensions: Extensions are where you setup the extensions that you will use on your system. In this module, you’ll create an extension number and set a password for each extension and set-up voicemail (if desired). Note that this module only configures FreePBX and Asterisk. You must separately configure the phones themselves to connect to your PBX, either by configuring the phones manually, or by using the Endpoint Manager Module.

IVR: This is the module where you configure an auto attendant. This is the most complicated part. The IVR is the “Press 1 for …, Press 2 for …”. First you need to create recordings. You need to have an extension working on the system so you can ‘call’ it and record or you need to make your recordings outside of the phone system.

Ring: Ring Groups allow you to create a single extension number (the Ring Group Number) that will call more than one person. For example, you could make a Ring Group so that when any user dials extension 601, extensions 10, 11, 12, and 13 ring for 15 seconds, and then the call goes to the voicemail for extension 17.

Queue: Enhanced version of Queue.

Inbound route configuration: The Inbound Routes module is where you tell the PBX how to handle incoming calls. Typically, you tell the PBX the phone number that outside callers have called (“DID Number” or “Direct Inward Dial Number”) and then indicate which extension, Ring Group, Voicemail, or other destination the call will go to.


Outbound route configuration: Outbound Routes are how you tell your PBX which Trunks (phone lines) to use when people dial certain telephone numbers. A simple installation will tell the PBX to send all calls to a single trunk. However, a complex setup will have an outbound route for emergency calls, another outbound route for local calls, another for long distance calls, and perhaps even another for international calls. You can even create a “dead trunk” and route prohibited calls (such as international and 976 calls) to it. If you don’t add a pattern for international calls, then no one can call an international number.


For detailed configuration options refer to http://www.freepbxhosting.com/getting-started/ 



