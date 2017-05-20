# Software
Name chosen: [Severus](http://www.roman-emperors.org/severus.htm)


## Linux Ubuntu
User home folders are not encrypted.

### 15-May-2017
Weirdly the guest login has disappeared.  Rebooted and it's just not there.  Found instructions to remove the guest login [https://askubuntu.com/questions/451526/removing-guest-session-at-login-in-ubuntu-14-04](here) and [https://www.howtogeek.com/117994/how-to-disable-ubuntus-guest-session-account/](here).

Reversing these, created from scratch `/etc/lightdm/lightdm.conf` with:
```
[SeatDefaults]
user-session=ubuntu
greeter-session=unity-greeter
allow-guest=true
```
I also added the same section to `/etc/lightdm/users.conf` though am not sure if that was absolutely necessary.  One or both of these has restored the guest login.


### Sat 04-Feb-2017
Hard disk finally failed, replaced with 1Tb thing hanging around.  Installed Ubuntu 16.10, no Windows at all.

Assigned fixed IP, edited `/etc/network/interfaces`, `/etc/hosts`.

Installed samba and cifs-utils: `sudo apt-get install samba cifs-utils` to be able to set workgroup on Samba client.


### Sat 19-Nov-2016
Upgraded from Ubuntu 16.04 LTS to 16.10.  Had to go through System Settings / Software & Updates / Updates first and change 'Notify me of a new Ubuntu version' to 'For any version'.

Post update, the versioning stuff is now:
```
chrism@Severus:~$ cat /etc/debian_version
stretch/sid
chrism@Severus:~$ uname -a
Linux Severus 4.8.0-27-generic #29-Ubuntu SMP Thu Oct 20 21:03:13 UTC 2016 x86_64 x86_64 x86_64 GNU/Linux
chrism@Severus:~$ lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 16.10
Release:	16.10
Codename:	yakkety
chrism@Severus:~$ 
```

Unlike the other laptops upgraded, there hasn't been a similar decrease in CPU temperature.  The machine usually hovers between 75 and 85 degrees C, occasionally hitting 95 and even 97 (after which the BIOS kicks-in and halts the machine to avoid heat damage).  Other machines have all seen a 10-20 degree C drop in usual CPU temperature from upgrading to a 4.8 kernel.


### Fri 13-May-2016 - Installed Play on Linux
To run, start a terminal and type: `playonlinux` and leave the terminal window open.
http://sysads.co.uk/2014/07/install-playonlinux-4-2-4-on-ubuntu-14-04/

Used it to install Kindle Reader under Wine and it worked a treat.
http://sysads.co.uk/2014/08/install-amazon-kindle-ubuntu-14-04/

Also upgraded to wine 1.8, but playonlinux has installed wine 1.7 as well.


### Apr-2016
Ubuntu upgraded to 16.04 (April 2016).

If prompted to run 'sudo apt autoremove' to remove old stuff, that's done as a matter of course and won't be logged.


## Windows 7
Is installed, haven't booted it for a very long time (maybe not booted to Windows 7 in 2016).


# Hardware
[HP ProBook 6545b](http://h20564.www2.hp.com/hpsc/doc/public/display?docId=emr_na-c01897562)


# Location, IP etc...
See secure list on Evernote (search for "House Computers & Equipment").
