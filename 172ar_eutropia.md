# Software
Name chosen: [eutropia](http://www.roman-emperors.org/maxherc.htm#Note%201)


## Slackware 14.2 ([SARPi](http://sarpi.fatdog.eu/index.php?p=getslack))
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



# Hardware
[Raspberry Pi 3 model B+](https://www.raspberrypi.org/blog/raspberry-pi-3-model-bplus-sale-now-35/) bought spring 2019.


# Location, IP etc...
tba - likely in the cellar cupboard
