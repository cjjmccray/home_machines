# Software
Name chosen: [Severus](http://www.roman-emperors.org/severus.htm)

## Linux Ubuntu
17.10. User home folders are not encrypted.

### 17-Nov-2017
- Firefox updated automatically to 57.0, TreeStyleTab also updated to 2.2.9
  - TreeStyleTab works differently - the core Firefox add-on platform has changed, no XUL/XPCOM elements, but web-API based things
  - Only visible difference is in the tabs appear across the top as well as down the side
  - There *is* a problem with [Scrapbook X](https://addons.mozilla.org/en-US/firefox/addon/scrapbook/) - it doesn't work at all, and might never
  - Changing to the [Pale Moon browser fork from Firefox](https://en.wikipedia.org/wiki/Pale_Moon_(web_browser)) is an option, but using a forked browser like that will break other things as well
  - Waiting *is* an option - there might be some movement in support for XUL/XPCOM or conversion of Scrapbook X to use the web API
  - Running two versions of Firefox/Pale Moon to be able to Scrapbook stuff you need to for legal/archive reasons is also a possibility (and using a PortableApps version of Firefox on Windows, say).
  - Some discussion forums have more info: [here](https://github.com/danny0838/firefox-scrapbook/issues/85), [here](https://github.com/danny0838/firefox-scrapbook/issues/162) and [here](https://blog.mozilla.org/addons/2016/11/23/add-ons-in-2017/).
- [Node.js and npm](https://uk.godaddy.com/help/install-nodejs-ubuntu-17395)
- [n](https://github.com/tj/n)
  - then moved /usr/bin/node and put a link in place to /usr/local/bin/node
  - node.js now v9.2.0
- Wine, PlayOnLinux, Amazon Kindle Reader
  - [Add the repo](https://www.winehq.org/pipermail/wine-devel/2017-March/117104.html)
  - [Install Wine 1.7 Tricks then PlayOnLinux](https://sysads.co.uk/2014/07/23/install-playonlinux-4-2-4-on-ubuntu-14-04/)
  - [Amazon Kindle](https://sysads.co.uk/2014/08/19/install-amazon-kindle-ubuntu-14-04/)
    - took about 45 minutes to setup the Windows drive
  - PlayOnLinux won't install Microsoft Core Fonts - starts downloading andale32.exe then fails, and others are [having the same problem](https://www.playonlinux.com/en/topic-15164-Cannot_install_core_fonts.html)

### 10-Nov-2017
Replaced hard disk with SSD drive (old disk crashed/broken).  Installed:
- Ubuntu 17.10
- get_iplayer
- youtube-dl
- TreeStyleTab under Firefox - had to install 1.9 version from 3+ months ago to get it to work

### 03-Jun-2017
Installed...
- `sudo apt-get install cryptsetup-bin`
  - to allow LUKS encrypted partitions to work for new 4Gb backup drive
- sudo apt install hashalot
  - turns out `sha256sum` etc... aren't installed by default as terminal programs
- added right-click file checksumming to Nautilus [from these instructions](https://solus-project.com/forums/viewtopic.php?t=2672)
  - note, use the modified script in [my repo here](https://github.com/cjjmccray/linux_shell_scripts/blob/master/Checksums) as the line-spacing is borked in the versions in that forum
- installed and uninstalled [HashChecker](http://ubuntuguide.net/md5sha256-hash-checker-gui-for-ubuntu-downloadsfiles) and [here](https://www.gnome-look.org/content/download.php?content=129309&id=1&tan=39290839) as the source code is missing from [this Dropbox folder](https://dl.dropboxusercontent.com/u/9350208/CurrentVersion/)
- Jacksum
  - installed from [here](http://jacksum.net/en/download.html)
  - manually installed into `/bin/jacksum`
- Java
  - installed the Java SE runtime, manually into `/bin/java`
  - there's a symlink from `/bin/java/jre` to the version-numbered folder, to allow for multiple versions to be handled at some point in the future
- sudo apt install cryptsetup
  - was prompted by `apt` to install this, suspect it's a recommendation after installing cryptsetup-bin

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
