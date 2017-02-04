
## 04-Feb-2017
Actually carried out between Christmas 2016 and New Year 2017, shortened the antenna to 67mm and mounted (glued) onto an old coffee tub, [using these instructions](http://forum.flightradar24.com/threads/9711-Optimize-the-Whip-Antenna-Which-Is-Supplied-With-DVB-T-Dongle).



## 04-Jun-2016

Name chosen: [galerius](http://www.roman-emperors.org/galerius.htm)

IP address selected: 192.168.1.41
Gateway: 192.168.0.2
Netmask: 255.255.254.0

Issue following the usual instructions, when you come to start dropbear to do ssh, it fails silently.

Checking /var/log/dropbear.log and there's a complaint about the motd file not right - it's looking for /etc/motd.net.  /etc/motd exists so a symlink has been created from /etc/motd.net to /etc/motd

Slackware 14.1 regular setup ensued.

Swap file: 1G, Linux partition: 13.9G.

### Flightradar24
The default instaler is designed for Rasbian and instructions rely on being able to use Ubuntu-like apt-get etc...  There's a deb file which when converted using deb2tgz installed but doesn't run (complains of file not found).  Suspect there are dependencies not installed...

#### rtl-sdr
Cloned from git into /root/git

git clone git://git.osmocom.org/rtl-sdr.git

Instructions here: http://sdr.osmocom.org/trac/wiki/rtl-sdr

    cd rtl-sdr/
    mkdir build
    cd build
    cmake ../
    make
    sudo make install
    sudo ldconfig

#### dump1090
Cloned from git into /root/git

git  clone  git://github.com/MalcolmRobb/dump1090.git

Instructions here: http://forum.flightradar24.com/threads/8591-Raspberry-Pi-How-To-Install-Raspian-OS-Dump1090-FR24-Data-Feeder

    make
    ./dump1090 --interactive --net --net-http-port 8080
 
 and I did get the error message:
 
    root@galerius:~/git/dump1090# ./dump1090 --interactive --net --net-http-port 8080
     Found 1 device(s):
    0: Realtek, RTL2838UHIDIR, SN: 00000001 (currently selected)
    
    Kernel driver is active, or device is claimed by second instance of librtlsdr.
    In the first case, please either detach or blacklist the kernel module
    (dvb_usb_rtl28xxu), or enable automatic detaching at compile time.
    
    usb_claim_interface error -6
    Error opening the RTLSDR device: Device or resource busy
    root@galerius:~/git/dump1090# 

OK, dump1090 *works*:

    Hex     Mode  Sqwk  Flight   Alt    Spd  Hdg    Lat      Long   Sig  Msgs   Ti-
    -------------------------------------------------------------------------------
    AAE5A5  S     4761  N801AS   43000  478  294   52.999   -2.782    6   399    3

and earlier I was getting 3-5 planes overhead, but... the fr24feed program just refuses to start.

F**k it, switch to Raspbian...

## Raspbian
Out of the box there's one user created, 'pi' with password 'raspberry'/

First thing - get it to work over wifi with a static IP address:
http://www.modmypi.com/blog/how-to-give-your-raspberry-pi-a-static-ip-address-update

192.168.1.41 set as the wifi static address
DNS - changed the default DNS server from the last wifi box found to 192.168.0.2 and added to /etc/resolv.conf 208.67.222.222 and 208.67.220.220 (both from OpenDNS).

## 06-Jun-2016 1725h
Installed dnsutils to allow dig and nslookup to work, also was suggested to install rblcheck so did so (not sure what it does).
Installed geeknote and my geeknote_update.sh.

Hmmm... from a terminal as user 'pi' geeknote login done but though gnsync looks to be running OK, there's nothing in my Evernote.  Copied to /etc/cron.daily and also sudo'd to geeknote login.  We'll see.

Machine downed @ 1730h having been up 1day 11h43m to move.


todo
* set a different password for the "pi" user!
* set the machine name to 'galerius' (it's currently 'raspberrypi')
* get static ip to work on wifi *or* put a network wire into the attic
