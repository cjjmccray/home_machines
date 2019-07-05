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

```root@eutropia:~# mysqladmin -u root password '<<<password>>>'
mysqladmin: connect to server at 'localhost' failed
error: 'Can't connect to local MySQL server through socket '/var/run/mysql/mysql.sock' (111)'
Check that mysqld is running and that the socket: '/var/run/mysql/mysql.sock' exists!
root@eutropia:~# ```

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
root@eutropia:/var/lib/mysql# ```

and now it seems to be working:

```root@eutropia:/var/lib/mysql# ps ax | grep mysql
 1029 pts/0    S      0:00 /bin/sh /usr/bin/mysqld_safe --datadir=/var/lib/mysql --pid-file=/var/run/mysql/mysql.pid --skip-networking
 1109 pts/0    Sl     0:00 /usr/libexec/mysqld --basedir=/usr --datadir=/var/lib/mysql --plugin-dir=/usr/lib/mysql/plugin --user=mysql --skip-networking --log-error=/var/lib/mysql/eutropia.cjjm.eu.err --pid-file=/var/run/mysql/mysql.pid
 1150 pts/0    S+     0:00 grep mysql
root@eutropia:/var/lib/mysql# ```

and the password change command works OK.

Following the suggestion in ```/etc/rc.d/rc.mysqld```, to run ```mysql_install_db --user=mysql``` but it fails with a permissions error.

Dropping back to ```mysql_install_db``` gives an error that the mysql user table already exists, and to use ```mysql_upgrade``` instead:

```mysql.user table already exists!
Run mysql_upgrade, not mysql_install_db```

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

MariaDB [(none)]> quit ```

Both zmuser and root user should have a zoneminder host as well, though that might not be a problem as we're working directly on the box.

Timezone used was Europe/London.

Last sed command didn't work, had to manually edit ```/etc/httpd/httpd.conf```.


# Hardware
[Raspberry Pi 3 model B+](https://www.raspberrypi.org/blog/raspberry-pi-3-model-bplus-sale-now-35/) bought spring 2019.


# Location, IP etc...
tba - likely in the cellar cupboard




Now try:
cpan LWP::UserAgent

cpan Sys::Mmap

not sure if these are case sensitive
