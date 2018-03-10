# Software
Name chosen: [Maxentius](http://www.roman-emperors.org/maxentius.htm)


## Linux Ubuntu - 17.10
User home folders *are* encrypted.


### Sat 10-Mar-2018
#### Python (needed for Calibre install)
```sudo apt-get install build-essential checkinstall```
```sudo apt-get install libreadline-gplv2-dev libncursesw5-dev libssl-dev libsqlite3-dev tk-dev libgdbm-dev libc6-dev libbz2-dev```
```sudo apt install python```

#### Calibre
``` sudo -v && ```
```  wget -nv -O- https://raw.githubusercontent.com/kovidgoyal/calibre/master/setup/linux-installer.py | ```
```  sudo python -c "import sys; main=lambda:sys.stderr.write('Download failed\n'); exec(sys.stdin.read()); main()"```

#### [Node.js and npm](https://uk.godaddy.com/help/install-nodejs-ubuntu-17395)
Follow all the instructions especially getting nvm installed.

Node.js in ```/usr/bin``` stays out of date, set a link to the version under your profile (or copy the node file to ```/usr/bin```)
- ```ls -lrt ~/.nvm/versions/node/v9.8.0/bin/node```
  - ```-rwxr-xr-x 1 chrism chrism 35177169 Mar  7 21:40 /home/chrism/.nvm/versions/node/v9.8.0/bin/node```
I've moved the old node in /usr/bin to node-6-11-4 and set it not executable.  Symlink in place to the local copy of node.

#### [n](https://github.com/tj/n)
- Prefix the install command with ```sudo```
- Node.js version is v6.11.4 (not v9.2.0)

#### Wine, PlayOnLinux, Amazon Kindle Reader
- [Add the repo](https://www.winehq.org/pipermail/wine-devel/2017-March/117104.html)
  - Install using: ```sudo apt get update```
  - then: ```sudo apt-get install winehq-devel```  
- [Install Wine 1.7 Tricks then PlayOnLinux](https://sysads.co.uk/2014/07/23/install-playonlinux-4-2-4-on-ubuntu-14-04/)
- [Amazon Kindle](https://sysads.co.uk/2014/08/19/install-amazon-kindle-ubuntu-14-04/)
  - took about 45 minutes to setup the Windows drive
- PlayOnLinux won't install Microsoft Core Fonts - starts downloading andale32.exe then fails, and others are [having the same problem](https://www.playonlinux.com/en/topic-15164-Cannot_install_core_fonts.html)

#### get_iplayer
Following [instructions from here](https://launchpad.net/~jon-hedgerows/+archive/ubuntu/get-iplayer):
- ```sudo add-apt-repository ppa:jon-hedgerows/get-iplayer```
- ```sudo apt-get update```
- ```sudo apt-get install get-iplayer```

#### TreeStyleTab under Firefox
Latest version is compatible with Firefox 57.0+ ("Quantium") - just search for it and install.

#### Old files
Copied from Severus backup on 4Gb removable drive to same folders on Maxentius.

### Sat 03-Mar-2018
Initial commissioning.  Resized partitions from 240Gb all to Win10 to 120Gb each for Win10 and Ubuntu.  Win10 boots OK after resizing.
- youtube-dl
- Firefox
- VLC, NordVPN
  - VLC set as default player for music and video
- NordVPN
  - NordVPN connections to Iceland and Sweden setup
- Remmina
  - Installed, used to connect to the PC in the hall at 7bc OK.


## Windows 10 Pro
Is installed.  Was on the machine when purchased.  Disk partitions resized to make space for Ubuntu.

Also has a full Microsoft Office 2016 Pro install with licence.


# Hardware
[Lenovo T430](http://link.to/the-manufacturers-website-or-other-good-specification-list), with 16Gb RAM, 240Gb SSD disk, purchased 2nd hand from eBay, March 2018.


# Location, IP etc...
See secure list on Evernote (search for "House Computers & Equipment").
