# Software

Name chosen: [caracalla](http://www.roman-emperors.org/caracala.htm)

## Linux Ubuntu
User home folders *are* encrypted.

### 11-Dec-2016
VLC installed - don't think I had to add a repo for this, just ran:
```
sudo apt-get update
sudo apt-get install vlc vlc-plugin-*
```

Finally got the Bitcoin .bitcoin folder to copy across, two block files had to be copied separately as their copy jobs failed due to wifi connection timing out.
```
sudo add-apt-repository ppa:bitcoin/bitcoin 
sudo apt-get update
sudo apt-get install bitcoin-qt
```

with 3 weeks to catch-up, that took about 15 hours.

Wine installed:
```
sudo add-apt-repository ppa:ubuntu-wine/ppa
sudo apt-get update
sudo apt-get install wine1.8 winetricks
```

Version 1.8 actually results in 1.6 being installed, though I tried 2.2, 2.1, 2.0 and 1.9 first - all failed.  Still didn't make it the usually app for Windows/DOS exe files and didn't register as an app.  So...
```
chrism@caracalla:~$ sudo apt-get install wine
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Package wine is a virtual package provided by:
  wine-stable 1.8.5-1ubuntu1
  wine-development 1.9.20-1ubuntu1
You should explicitly select one to install.

E: Package 'wine' has no installation candidate
chrism@caracalla:~$ sudo apt-get install wine-stable
```

WineTricks is available as a listed/known app - ran it and it sits there with an "updating the configuration" message.  Left it to run... after about 5 minutes it finished, but doesn't seem right.


Algodoo ([Windows version](http://www.algodoo.com/download/)) - downloaded but unable to check.


### 10-Dec-2016
Ubuntu 16.10 installed successfully with encypted home folder.

Firefox add-ons:

1. [Session Manager](https://addons.mozilla.org/en-US/firefox/addon/session-manager/?src=search)
2. [Scrapbook X](https://addons.mozilla.org/en-US/firefox/addon/scrapbook-x/?src=search) in replacement and to try out an alternative to [Scrapbook](https://addons.mozilla.org/en-US/firefox/addon/scrapbook/?src=search)
3. [Tree Style Tab](https://addons.mozilla.org/en-US/firefox/addon/tree-style-tab/)


### 05-Dec-2016
Been trying to decrypt the encrypted home folder to copy all files off the drive to allow full re-install (try 16.10 first, if still fails, revert to 16.04).

1. Boot to Ubuntu 16.10 or 16.04 Live CD, to try not install.
2. Find the disk in Nautilus and click to connect to it.
3. In a terminal, go and find it properly there (you only need to do this to make sure you get the directory right in the command:
  + `ecryptfs-recover-private /media/<UUID>/home/.ecryptfs/<USERNAME>/.Private`
  + It will promt for the mount password, unlock the wrapped-passphrase and mount the directory in read only mode under /tmp/ with just single command. Use the flag `--rw` to mount the encrypted filesystem as read and write.
  + from http://askubuntu.com/questions/238047/how-do-i-mount-an-encrypted-home-directory-on-another-ubuntu-machine
  + [ecryptfs home page](http://ecryptfs.org/), [documentation](http://ecryptfs.org/documentation.html) and [man page](http://manpages.ubuntu.com/manpages/zesty/en/man1/ecryptfs-recover-private.1.html)
4. Then from either Nautilus copy the files across, or in the case of any hidden folders (.bitcoin for instance) copy them at a terminal window or rename the folders temporarily to unhide them).


### 19th November 2016
Upgraded from Ubuntu 16.04 LTS to 16.10.

Post-upgrade restart on Sunday 20th November and... no side-bar and no top-bar menus, and the background image isn't being shown.

Right-click menu from the desktop does work, and from there am able to start a terminal and reboot.  Background image seen fleetingly when shutting down.  Restarted with still the same fault.  Have tried some reinstalls of unity and others (notes to follow), but no change.

Suspect there's a service failing to start on reboot.  Having upgraded Severus at the same time (and that being OK), the "fresh from boot" `ps ax` list has been exported and [uploaded here](https://github.com/cjjmccray/home_machines/blob/master/severus_process_list.txt).


### 16th September 2016
* VLC
 * and in 'System Settings', 'Details' changed so VLC is default player for music and video.
* grsync
* get_iplayer
 * follow [these instructions](http://www.christopherdowning.co.uk/2012/01/11/a-basic-get_iplayer-tutorial/)
  * `sudo add-apt-repository ppa:jon-hedgerows/get-iplayer`
  * `sudo apt-get update`
  * `sudo apt-get install get-iplayer`
* Bitcoin
 * `sudo apt-add-repository ppa:bitcoin/bitcoin`
 * `sudo apt-get update`
 * `sudo apt-get install bitcoin-qt`


### Linux - Ubuntu 16.04 LTS
All are installed using `apt-get install`, where a different repo is needed, details will be added as well


### 9th September 2016
Tried Kubuntu to start with but discarded after a few hours - struggling to understand how to anchor icons to the taskbar along the bottom, and realised I was trying to use it like Ubuntu.
* youtube-dl
* Wine
* [Algodoo](http://www.algodoo.com/) for Windows, runs fine under Wine.  Inspired by reading about [Phun](https://en.wikipedia.org/wiki/Phun).
* Firefox plugins: Scrapbook, Session Manager, Tree Style Tab


## Windows 8.1
Hardly used... booted during late-November and early-December 2016 to force Windows updates through.


# Hardware
[Lenovo G50-45](http://shop.lenovo.com/gb/en/laptops/lenovo/g-series/g50-45/) laptop


# Location, IP etc...
See secure list on Evernote (search for "House Computers & Equipment").
