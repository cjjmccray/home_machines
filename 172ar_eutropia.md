# Software
Name chosen: [eutropia](http://www.roman-emperors.org/maxherc.htm#Note%201)


## [Raspbian Buster](https://www.raspberrypi.org/blog/buster-the-new-version-of-raspbian/)

Did try and install using Slackware 14.2 but ran into too many problems with ZoneMinder (see below).

### Previous install
Slackware 14.2 ([SARPi](http://sarpi.fatdog.eu/index.php?p=getslack))
User home folders are not encrypted.  Eventually this machine will be running ZoneMinder for cameras and acting as a file server accessible from outside the house (for file sharing when I'm away, as a hot-backup for some files from 7bc, and for Alma to be able to grab some stuff I place on the drive from inside the home network).


### Wed 03-Jul-2019 - Initial OS install
Regular SARPi install on 16Gb memory card. 4Tb Maxtor M3 drive added (and is powered from the RasPi without an external supply).

### Thu 04-Jul-2019 - Installing ZoneMinder
[ZoneMinder from SlackBuilds](https://slackbuilds.org/repository/14.2/system/ZoneMinder/) - wget for the pieces on the RasPi, then extract the SlackBuild tarball and run ```./SlackBuild```

Perl hit a snag:
: -- Could NOT find PerlModules (missing: PERLMODULES_DATE_MANIP_MODULE PERLMODULES_LWP_USERAGENT_MODULE PERLMODULES_SYS_MMAP_MODULE)
 
Google PERLMODULES_DATE_MANIP_MODULE and we get... ```cpan Date::Manip```

Which suggests using the following for the rest...
- ```cpan LWP::UserAgent```
- ```cpan Sys::Mmap```
  - (```SYS::MMap``` doesn't work - so they are case-sensitive)

Install successful...
- ```If this is a new installation, you will need to create a MySQL database for ZoneMinder to use. See /usr/doc/ZoneMinder-<version>/README.SLACKWARE```

Stopping at this point, as for some reason MySQL isn't running, despite setting ```/etc/rc.d/rc.mysqld``` to be executable and running it with ```start``` as the command-line option.

### Fri 05-Jul-2019 - MySQL

Following [these instructions for setting up mysql](http://www.neiland.net/blog/article/configuring-mariadb-on-slackware-14-1/) - is on Slackware 14.1, and this is Slackware 14.2 but it should be close enough.

Unable to change the mysql password...

``` root@eutropia:~# mysqladmin -u root password '{password}'
mysqladmin: connect to server at 'localhost' failed
error: 'Can't connect to local MySQL server through socket '/var/run/mysql/mysql.sock' (111)'
Check that mysqld is running and that the socket: '/var/run/mysql/mysql.sock' exists!
root@eutropia:~#
```


Checking ```/var/lib/mysql/eutropia.cjjm.eu.err``` and there are multiple issues with mysql starting up.  

Root cause (HA! GEDDIT!) is a folder ownership problem in the ```/var/lib/mysql``` folder:

```root@eutropia:/var/lib/mysql# ls -lrt
total 110672
-rw-rw---- 1 mysql mysql 50331648 Jul  5 04:08 ib_logfile1
drwx------ 2 root  root      4096 Jul  5 12:05 mysql/
drwx------ 2 root  root      4096 Jul  5 12:05 performance_schema/
drwx------ 2 root  root      4096 Jul  5 12:05 test/
-rw-rw---- 1 mysql mysql      888 Jul  5 13:40 ib_buffer_pool
-rw-rw---- 1 mysql mysql    33976 Jul  5 13:40 eutropia.cjjm.eu.err
-rw-rw---- 1 mysql mysql 50331648 Jul  5 13:40 ib_logfile0
-rw-rw---- 1 mysql mysql 12582912 Jul  5 13:40 ibdata1
-rw-rw---- 1 mysql mysql       52 Jul  5 13:40 aria_log_control
-rw-rw---- 1 mysql mysql    24576 Jul  5 13:40 aria_log.00000001
root@eutropia:/var/lib/mysql# chown -R mysql:mysql *
root@eutropia:/var/lib/mysql# 
```

and now it seems to be working:

```root@eutropia:/var/lib/mysql# ps ax | grep mysql
 1029 pts/0    S      0:00 /bin/sh /usr/bin/mysqld_safe --datadir=/var/lib/mysql --pid-file=/var/run/mysql/mysql.pid --skip-networking
 1109 pts/0    Sl     0:00 /usr/libexec/mysqld --basedir=/usr --datadir=/var/lib/mysql --plugin-dir=/usr/lib/mysql/plugin --user=mysql --skip-networking --log-error=/var/lib/mysql/eutropia.cjjm.eu.err --pid-file=/var/run/mysql/mysql.pid
 1150 pts/0    S+     0:00 grep mysql
root@eutropia:/var/lib/mysql# 
```

and the password change command works OK.

Following the suggestion in ```/etc/rc.d/rc.mysqld```, to run ```mysql_install_db --user=mysql``` but it fails with a permissions error.

Dropping back to ```mysql_install_db``` gives an error that the mysql user table already exists, and to use ```mysql_upgrade``` instead:

```mysql.user table already exists!
Run mysql_upgrade, not mysql_install_db
```

So finally... ```mysql_upgrade``` has done a pile of stuff and looks to have worked.

### Fri 05-Jul-2019 ZoneMinder

Following [these instructions from the Slackware website](https://docs.slackware.com/howtos:software:zoneminder).

Table of permissions in mysql is slightly different:
```MariaDB [(none)]> select User,Host from mysql.user;  #Check and make zmuser was created
+--------+------------------+
| User   | Host             |
+--------+------------------+
|        | eutropia.cjjm.eu |
|        | localhost        |
| root   | localhost        |
| zmuser | localhost        |
+--------+------------------+
4 rows in set (0.006 sec)

MariaDB [(none)]> quit 
```

Both zmuser and root user should have a zoneminder host as well, though that might not be a problem as we're working directly on the box.

Timezone used was Europe/London.

Last sed command didn't work, had to manually edit ```/etc/httpd/httpd.conf```.

So, last part is to start Apache and ZoneMinder. 

Apache fails to start:
```root@eutropia:~# chmod 755 /etc/rc.d/rc.httpd; /etc/rc.d/rc.httpd start
AH00526: Syntax error on line 62 of /etc/httpd/extra/zm.conf:
Invalid command 'RewriteEngine', perhaps misspelled or defined by a module not included in the server configuration
root@eutropia:~# 
```

The rewrite engine module is commented out in ```/etc/httpd/httpd.conf``` ([tip from here](https://askbot.org/en/question/10237/unable-to-start-apache-server-error-in-rewriteengine/)) - edit it, find ```rewrite_module``` and uncomment.

Now try the command to start Apache and... silent.  It's started OK.

Now try the command to start ZoneMinder:
```chmod 755 /etc/rc.d/rc.zm; /etc/rc.d/rc.zm start```
and it fails:
```root@eutropia:/etc/httpd# chmod 755 /etc/rc.d/rc.zm; /etc/rc.d/rc.zm start
Starting ZoneMinder: 07/05/2019 14:57:44.768027 zmpkg[1438].INF [main:57] [Command: start]
07/05/2019 14:57:44.828045 zmpkg[1438].INF [main:305] [Sanity checking States table...]
07/05/2019 14:57:44.847649 zmpkg[1438].INF [main:97] [Command: start]
Can't locate Sys/MemInfo.pm in @INC (you may need to install the Sys::MemInfo module) (@INC contains: /usr/local/lib/perl5 /usr/local/share/perl5 /usr/lib/perl5/vendor_perl /usr/share/perl5/vendor_perl /usr/lib/perl5 /usr/share/perl5) at /usr/bin/zmdc.pl line 158.
BEGIN failed--compilation aborted at /usr/bin/zmdc.pl line 158.
07/05/2019 14:57:45.407509 zmpkg[1438].ERR [ZoneMinder::General:174] [Unable to run "sudo -u apache /usr/bin/zmdc.pl check", output is "", status is 2]
                                                           [FAILED]
root@eutropia:/etc/httpd# 
```

Another perl module, ```Sys::MemInfo``` seems to be missing, so... ```cpan Sys::MemInfo``` and let's see what happens.

Try ZoneMinder again and...
```root@eutropia:/etc/httpd# chmod 755 /etc/rc.d/rc.zm; /etc/rc.d/rc.zm start
Starting ZoneMinder: 07/05/2019 15:00:07.345548 zmpkg[1575].INF [main:57] [Command: start]
07/05/2019 15:00:07.362355 zmpkg[1575].INF [main:305] [Sanity checking States table...]
07/05/2019 15:00:07.396653 zmpkg[1575].INF [main:97] [Command: start]
07/05/2019 15:00:10.249991 zmpkg[1575].INF [main:205] [Single server configuration detected. Starting up services.]
                                                           [  OK  ]
root@eutropia:/etc/httpd#
```

OOOH!

OK, we have a user interface on [http://eutropia/zm](http://eutropia/zm).  Configuring it to handle the camera - experimenting with rtsp/stream link and http/snapshot-file links.  None working, as yet...

Another perl module - in the ZM configuration for the camera, there's a link at the top-right for "ONVIF" which is another marque/brand for Herospeed.  It might be that the camera has been autodetected.  However, the settings fail to work, and at a command prompt, ```/usr/bin/zmonvif-probe.pl probe``` fails, with a missing perl module, ```Class::Std::Fast```. So... ```cpan Class::Std::Fast``` and try again.

And again... ```Data::Dump``` so ```cpan Data::Dump``` ...
And again... ```SOAP::WSDL::Transport::HTTP``` so ```cpan SOAP::WSDL::Transport::HTTP``` ...
And again... ```Data::UUID``` so ```cpan Data::UUID``` ...
And again... ```IO::Socket::Multicast``` so ```cpan IO::Socket::Multicast``` ...

```root@eutropia:/etc/rc.d# /usr/bin/zmonvif-probe.pl probe
Name "ZoneMinder::ONVIF::verbose" used only once: possible typo at /usr/bin/zmonvif-probe.pl line 81.
http://192.168.1.242/onvif/device_service, 1.2, (type='network_video_transmitter', type='video_encoder', type='audio_encoder', manufacturer='HeroSpeed', type='ptz', Profile='Streaming', Profile='G', Profile='G', hardware='IPCamera', name='HeroSpeed', location='Guangzhou')
root@eutropia:/etc/rc.d# 
```

Right... some more fiddling was done to enable cgi-bin...
- aaaagh! there was an awful lot of this!!!

"LoadModule cgi_module modules/mod_cgi.so or LoadModule cgi_module modules/mod_cgid.so" from [this Stackoverflow question and answer](https://stackoverflow.com/questions/7471859/cgi-script-is-not-executing)



And edited the ```/etc/rc.d/rc.zm``` file so without parameters it now does a restart (stop and start) and where the asterisk for no parameter catch was, this now is a formal 'help' parameter.

### Sat 06-Jul-2019
There's an error from [the ZMS test json structure](http://192.168.1.33/zm/api/configs/view/ZM_PATH_ZMS.json) from ZoneMinder.  Buried in the error report is "Call to undefined function apc_fetch()".  [This ZoneMinder forum discussion](https://forums.zoneminder.com/viewtopic.php?t=27454) mentions missing apcu module.  ```pecl install apcu``` to install it, and edit ```/etc/php.ini``` to add a line: ```extension=apcu.so``` (search for 'extension=' and add just after there).

Need to restart Apache etc... - easier now to do a full restart.

### Mon 08-Jul-2019
Raspbian Buster installed on 32Gb memory card. Downloaded zip image and installed balena etcher (as recommended on the Raspbian website) to "burn" the zip file onto the memory card (it creates 

- Changed Raspbian install to:
  - fixed IP address 192.168.1.33 / 255.255.254.0 / 192.168.0.2
  - boot to command line at first
  - allow SSH
  - change pi password

Following a mash-up of instructions from the ZoneMinder wiki: [here](https://wiki.zoneminder.com/Raspbian#How_to_install_ZoneMinder_1.30.4_on_Raspberry_PI_3_with_Raspbian_9_.28Stretch.29) and [here](https://wiki.zoneminder.com/Debian_9_64-bit_with_Zoneminder_1.30.4_the_Easy_Way#Debian_9_with_Zoneminder_1.30.4).

- sudo apt install apache2
- apt install php mariadb-server php-mysql libapache2-mod-php7.0
  - fails as it cannot find the libapache2-mod-php7.0
  - removed that and...
- apt install php mariadb-server php-mysql
  - installs OK
  - php is installed - type ```php -v``` at a terminal and it works OK:
```pi@eutropia:~ $ php -v
PHP 7.3.4-2 (cli) (built: Apr 13 2019 19:05:48) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.3.4, Copyright (c) 1998-2018 Zend Technologies
    with Zend OPcache v7.3.4-2, Copyright (c) 1999-2018, by Zend Technologies
pi@eutropia:~ $ 
```
- mysql_secure_installation
- sudo mv /etc/mysql/my.cnf /etc/mysql/my.cnf_old 
  - (didn't delete, just moved it out of the way)
- sudo cp /etc/mysql/mariadb.conf.d/50-server.cnf /etc/mysql/my.cnf
- sudo nano /etc/mysql/my.cnf
  - change existing lines to:
  - character-set-server = latin1
  - collation-server = latin1_swedish_ci
  - (the correct collation-server is latin1_swedish_ci, from [this page in the MySQL 5.7.2 manual](https://dev.mysql.com/doc/refman/5.7/en/charset-mysql.html)
- sudo service mariadb restart
- sudo nano /etc/apt/sources.list
  - add: deb http://www.deb-multimedia.org buster main non-free
  - (was: deb http://www.deb-multimedia.org stretch main non-free, but trying buster as that's the current version)
- sudo apt update
- wget http://www.deb-multimedia.org/pool/main/d/deb-multimedia-keyring/deb-multimedia-keyring_2016.8.1_all.deb
- sudo dpkg -i deb-multimedia-keyring_2016.8.1_all.deb
- apt install deb-multimedia-keyring
  - is already the latest version
- sudo apt update
- sudo apt upgrade
- sudo apt dist-upgrade
  - nothing to do
  - sudo apt autoremove 
  - to reveal 11Mb of space
- sudo apt install zoneminder vlc-plugin-base php7.0-gd
  - fails as cannot find php7.0-gd
  - sudo apt install zoneminder vlc-plugin-base
  - as it installed, apcu and php7.3-gd were found OK
- sudo apt install samba
  - also created user chrism, samba user chrism, edited fstab to make the 4Tb appear at boot, and configured Samba share using the old Slackware config share file (copying and pasting bits into the Raspbian smb.conf file)
- sudo chmod 740 /etc/zm/zm.conf
- sudo chown root:www-data /etc/zm/zm.conf
- sudo systemctl enable zoneminder.service
- sudo adduser www-data video
- sudo systemctl start zoneminder.service
```Job for zoneminder.service failed because the control process exited with error code.
See "systemctl status zoneminder.service" and "journalctl -xe" for details.
pi@eutropia:/etc $ sudo systemctl status zoneminder.service
● zoneminder.service - ZoneMinder CCTV recording and surveillance system
   Loaded: loaded (/lib/systemd/system/zoneminder.service; enabled; vendor prese
   Active: failed (Result: exit-code) since Mon 2019-07-08 19:44:56 BST; 9s ago
     Docs: http://zoneminder.readthedocs.org/en/latest/
  Process: 25880 ExecStart=/usr/bin/zmpkg.pl start (code=exited, status=255/EXCE

Jul 08 19:44:56 eutropia zmpkg.pl[25880]: DBI connect('database=zm;host=localhos
Jul 08 19:44:56 eutropia zmpkg.pl[25880]: Can't connect to db at /usr/share/perl
Jul 08 19:44:56 eutropia zmpkg.pl[25880]: BEGIN failed--compilation aborted at /
Jul 08 19:44:56 eutropia zmpkg.pl[25880]: Compilation failed in require at /usr/
Jul 08 19:44:56 eutropia zmpkg.pl[25880]: BEGIN failed--compilation aborted at /
Jul 08 19:44:56 eutropia zmpkg.pl[25880]: Compilation failed in require at /usr/
Jul 08 19:44:56 eutropia zmpkg.pl[25880]: BEGIN failed--compilation aborted at /
Jul 08 19:44:56 eutropia systemd[1]: zoneminder.service: Control process exited,
Jul 08 19:44:56 eutropia systemd[1]: zoneminder.service: Failed with result 'exi
Jul 08 19:44:56 eutropia systemd[1]: Failed to start ZoneMinder CCTV recording a

pi@eutropia:/etc $ 
```
- Hmmm... I've not setup MariaDB properly
  - nano .my.cnf
    - create this with the root user's name and password
  - sudo mysql < /usr/share/zoneminder/db/zm_create.sql
  - sudo mysql -e "grant select,insert,update,delete,create on zm.* to 'zmuser'@localhost identified by 'zmpass';"
  - rm .my.cnf
    - remove this as we're done with it
- sudo chmod 740 /etc/zm/zm.conf
- sudo chown root:www-data /etc/zm/zm.conf
- ln -s /etc/zm/apache.conf /etc/apache2/conf-enabled/zoneminder.conf
  - this fails as the file already exists
  - don't care about it, the one that's there should work
- sudo systemctl enable zoneminder.service
- sudo adduser www-data video
- sudo systemctl start zoneminder.service
```pi@eutropia:~ $ sudo systemctl status zoneminder.service
● zoneminder.service - ZoneMinder CCTV recording and surveillance system
   Loaded: loaded (/lib/systemd/system/zoneminder.service; enabled; vendor prese
   Active: active (running) since Mon 2019-07-08 19:59:37 BST; 6s ago
     Docs: http://zoneminder.readthedocs.org/en/latest/
  Process: 26043 ExecStart=/usr/bin/zmpkg.pl start (code=exited, status=0/SUCCES
 Main PID: 26055 (zmdc.pl)
    Tasks: 6 (limit: 2200)
   Memory: 70.9M
   CGroup: /system.slice/zoneminder.service
           ├─26055 /usr/bin/perl -wT /usr/bin/zmdc.pl startup
           ├─26082 /usr/bin/perl -wT /usr/bin/zmfilter.pl --filter_id=1 --daemon
           ├─26087 /usr/bin/perl -wT /usr/bin/zmfilter.pl --filter_id=2 --daemon
           ├─26092 /usr/bin/perl -wT /usr/bin/zmaudit.pl -c
           ├─26099 /usr/bin/perl -wT /usr/bin/zmwatch.pl
           └─26105 /usr/bin/perl -wT /usr/bin/zmstats.pl

Jul 08 19:59:36 eutropia zmdc[26092]: INF ['zmaudit.pl -c' started at 19/07/08 1
Jul 08 19:59:37 eutropia zmfilter_2[26087]: INF [Scanning for events using filte
Jul 08 19:59:37 eutropia zmfilter_2[26087]: INF [Checking filter Update DiskSpac
Jul 08 19:59:37 eutropia zmdc[26055]: INF ['zmwatch.pl' starting at 19/07/08 19:
Jul 08 19:59:37 eutropia zmdc[26099]: INF ['zmwatch.pl' started at 19/07/08 19:5
Jul 08 19:59:37 eutropia zmwatch[26099]: INF [Watchdog starting, pausing for 30 
Jul 08 19:59:37 eutropia zmdc[26055]: INF ['zmstats.pl' starting at 19/07/08 19:
Jul 08 19:59:37 eutropia zmdc[26105]: INF ['zmstats.pl' started at 19/07/08 19:5
Jul 08 19:59:37 eutropia systemd[1]: Started ZoneMinder CCTV recording and surve
Jul 08 19:59:38 eutropia zmstats[26105]: INF [Stats Daemon starting in 30 second

pi@eutropia:~ $ 
```
BOOM! It's running OK.

- sudo a2enmod cgi
- sudo a2enconf zoneminder
- sudo service apache2 restart

Now, [the ZoneMinder home page](http://eutropia/zm/index.php) is reporting an error: 
- ZoneMinder is not installed properly: php's date.timezone is not set to a valid timezone

I skipped the Cambozola configuration (as I don't use Internet Explorer).

Memory settings:
- sudo su -
- echo "kernel.shmmax = 134217728" >> /etc/sysctl.conf
- exit
- sudo su -
- echo "kernel.shmall = 2097152" >> /etc/sysctl.conf
- exit

And here's PHP's default time zone:
- sudo nano /etc/php5/apache2/php.ini
  - hmmm... php5...
  - found the php.ini file at:
  - sudo nano /etc/php/7.3/apache2/php.ini
  - go and find 'date.timezone' and uncomment and set to Europe/London
- sudo service apache2 restart
  - and we should have ZoneMinder up and running...

CHEF'S KISS! It's up.  Now to configure it...

ZoneMinder
- accept the privacy message
- change the storage default to: ```/disks/maxtor-m3/ZoneMinder/events```
- configured the Hall camera first
- the Porch camera... figured out what was wrong... drop the "http://" from the address and it works OK
- changed the permissions - took some figuring out, set the owner to www-data, group www-data, and u+rwx, go+rx on the ZoneMinder tree on the Maxtor drive and it works...

Last things...
- install new camera in the Porch, not sure what to do with the old one (junk/sell?)
- set up logrotate to archive old ZoneMinder stuff - anything over 1 month old tar/gz; anything over 1 year old delete
  - will be interesting to learn to use logrotate on this given it drives me nuts in a professional domain this isn't the norm...
- heatsink kit for the Pi or a box with a heatsink integral to the design (the lid is warm, and I've only just started getting this to do stuff)

# Hardware
[Raspberry Pi 3 model B+](https://www.raspberrypi.org/blog/raspberry-pi-3-model-bplus-sale-now-35/) bought spring 2019.


# Location, IP etc...
- IP: defined.
- Cellar cupboard
