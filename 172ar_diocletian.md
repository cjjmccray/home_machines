# Software
Name chosen: [diocletian](http://www.roman-emperors.org/dioclet.htm)


## Linux Ubuntu
User home folders *are* encrypted.


### Nov-2016
Upgraded to Ubuntu 16.10.


### 09-May-2016 - Error running sudo apt-get update

    chrism@diocletian:~$ sudo apt-get update
    Ign:1 http://dl.google.com/linux/chrome/deb stable InRelease
    Hit:2 http://security.ubuntu.com/ubuntu xenial-security InRelease   
    Hit:3 http://gb.archive.ubuntu.com/ubuntu xenial InRelease          
    Hit:4 http://dl.google.com/linux/chrome/deb stable Release          
    Hit:5 http://ppa.launchpad.net/rebuntu16/avidemux+unofficial/ubuntu xenial InRelease
    Hit:6 http://gb.archive.ubuntu.com/ubuntu xenial-updates InRelease  
    Hit:7 http://gb.archive.ubuntu.com/ubuntu xenial-backports InRelease
    Reading package lists... Done
    W: http://dl.google.com/linux/chrome/deb/dists/stable/Release.gpg: Signature by key 4CCA1EAF950CEE4AB83976DCA040830F7FAC5991 uses weak digest algorithm (SHA1)
    W: There is no public key available for the following key IDs:
    1397BC53640DB551
    chrism@diocletian:~$ 

Solution:

    chrism@diocletian:~$ sudo apt-key adv --recv-keys --keyserver keyserver.ubuntu.com 1397BC53640DB551
    Executing: /tmp/tmp.EVP1s8JWOQ/gpg.1.sh --recv-keys
    --keyserver
    keyserver.ubuntu.com
    1397BC53640DB551
    gpg: requesting key 640DB551 from hkp server keyserver.ubuntu.com
    gpg: key D38B4796: public key "Google Inc. (Linux Packages Signing Authority) <linux-packages-keymaster@google.com>" imported
    gpg: Total number processed: 1
    gpg:               imported: 1  (RSA: 1)
    chrism@diocletian:~$

From: http://ubuntuforums.org/showthread.php?t=2322420

Still getting two weak digest algorithm errors:

    W: http://dl.google.com/linux/chrome/deb/dists/stable/Release.gpg: Signature by key 4CCA1EAF950CEE4AB83976DCA040830F7FAC5991 uses weak digest algorithm (SHA1)
    W: http://dl.google.com/linux/chrome/deb/dists/stable/Release.gpg: Signature by key 3B068FB4789ABE4AEFA3BB491397BC53640DB551 uses weak digest algorithm (SHA1)

but seems to have cleared the problem.

### 09-May-2016 - Temperature sensors

First install some stuff: 

    sudo apt-get sensors-applet
    sudo apt install lm-sensors
    sudo apt-get install hddtemp

Then:

    sudo service kmod start

Finally at a terminal run `sensors`

    chrism@diocletian:~$ sensors
    acpitz-virtual-0
    Adapter: Virtual device
    temp1:        +46.0°C  (crit = +98.0°C)
    
    thinkpad-isa-0000
    Adapter: ISA adapter
    fan1:        3240 RPM
    
    coretemp-isa-0000
    Adapter: ISA adapter
    Physical id 0:  +47.0°C  (high = +86.0°C, crit = +100.0°C)
    Core 0:         +45.0°C  (high = +86.0°C, crit = +100.0°C)
    Core 1:         +46.0°C  (high = +86.0°C, crit = +100.0°C)
    
    chrism@diocletian:~$ 

But to monitor everything:

    sudo sensors-detect

    chrism@diocletian:~$ sudo sensors-detect
    # sensors-detect revision 6284 (2015-05-31 14:00:33 +0200)
    # System: LENOVO 4236D42 [ThinkPad T420] (laptop)
    # Kernel: 4.4.0-22-generic x86_64
    # Processor: Intel(R) Core(TM) i5-2520M CPU @ 2.50GHz (6/42/7)
    
    This program will help you determine which kernel modules you need
    to load to use lm_sensors most effectively. It is generally safe
    and recommended to accept the default answers to all questions,
    unless you know what you're doing.
    
    Some south bridges, CPUs or memory controllers contain embedded sensors.
    Do you want to scan for them? This is totally safe. (YES/no): YES
    Silicon Integrated Systems SIS5595...                       No
    VIA VT82C686 Integrated Sensors...                          No
    VIA VT8231 Integrated Sensors...                            No
    AMD K8 thermal sensors...                                   No
    AMD Family 10h thermal sensors...                           No
    AMD Family 11h thermal sensors...                           No
    AMD Family 12h and 14h thermal sensors...                   No
    AMD Family 15h thermal sensors...                           No
    AMD Family 16h thermal sensors...                           No
    AMD Family 15h power sensors...                             No
    AMD Family 16h power sensors...                             No
    Intel digital thermal sensor...                             Success!
        (driver `coretemp')
    Intel AMB FB-DIMM thermal sensor...                         No
    Intel 5500/5520/X58 thermal sensor...                       No
    VIA C7 thermal sensor...                                    No
    VIA Nano thermal sensor...                                  No
    
    Some Super I/O chips contain embedded sensors. We have to write to
    standard I/O ports to probe them. This is usually safe.
    Do you want to scan for Super I/O sensors? (YES/no): YES
    Probing for Super-I/O at 0x2e/0x2f
    Trying family `National Semiconductor/ITE'...               No
    Trying family `SMSC'...                                     No
    Trying family `VIA/Winbond/Nuvoton/Fintek'...               No
    Trying family `ITE'...                                      No
    Probing for Super-I/O at 0x4e/0x4f
    Trying family `National Semiconductor/ITE'...               No
    Trying family `SMSC'...                                     No
    Trying family `VIA/Winbond/Nuvoton/Fintek'...               No
    Trying family `ITE'...                                      No
    
    Some hardware monitoring chips are accessible through the ISA I/O ports.
    We have to write to arbitrary I/O ports to probe them. This is usually
    safe though. Yes, you do have ISA I/O ports even if you do not have any
    ISA slots! Do you want to scan the ISA I/O ports? (YES/no): YES
    Probing for `National Semiconductor LM78' at 0x290...       No
    Probing for `National Semiconductor LM79' at 0x290...       No
    Probing for `Winbond W83781D' at 0x290...                   No
    Probing for `Winbond W83782D' at 0x290...                   No
    
    Lastly, we can probe the I2C/SMBus adapters for connected hardware
    monitoring devices. This is the most risky part, and while it works
    reasonably well on most systems, it has been reported to cause trouble
    on some systems.
    Do you want to probe the I2C/SMBus adapters now? (YES/no): YES
    Using driver `i2c-i801' for device 0000:00:1f.3: Intel Cougar Point (PCH)
    Module i2c-i801 loaded successfully.
    
    Next adapter: i915 gmbus ssc (i2c-0)
    Do you want to scan it? (yes/NO/selectively): yes
    
    Next adapter: i915 gmbus vga (i2c-1)
    Do you want to scan it? (yes/NO/selectively): yes
    
    Next adapter: i915 gmbus panel (i2c-2)
    Do you want to scan it? (yes/NO/selectively): yes
    Client found at address 0x28
    Probing for `National Semiconductor LM78'...                No
    Probing for `National Semiconductor LM79'...                No
    Probing for `National Semiconductor LM80'...                No
    Probing for `National Semiconductor LM96080'...             No
    Probing for `Winbond W83781D'...                            No
    Probing for `Winbond W83782D'...                            No
    Probing for `Nuvoton NCT7802Y'...                           No
    Probing for `Winbond W83627HF'...                           No
    Probing for `Winbond W83627EHF'...                          No
    Probing for `Winbond W83627DHG/W83667HG/W83677HG'...        No
    Probing for `Asus AS99127F (rev.1)'...                      No
    Probing for `Asus AS99127F (rev.2)'...                      No
    Probing for `Asus ASB100 Bach'...                           No
    Probing for `Analog Devices ADM1029'...                     No
    Probing for `ITE IT8712F'...                                No
    
    Next adapter: i915 gmbus dpc (i2c-3)
    Do you want to scan it? (yes/NO/selectively): yes
    
    Next adapter: i915 gmbus dpb (i2c-4)
    Do you want to scan it? (yes/NO/selectively): yes
    
    Next adapter: i915 gmbus dpd (i2c-5)
    Do you want to scan it? (yes/NO/selectively): yes
    
    Next adapter: DPDDC-B (i2c-6)
    Do you want to scan it? (yes/NO/selectively): yes
    
    Next adapter: DPDDC-C (i2c-7)
    Do you want to scan it? (yes/NO/selectively): yes
    
    Next adapter: DPDDC-D (i2c-8)
    Do you want to scan it? (yes/NO/selectively): yes
    
    Next adapter: SMBus I801 adapter at efa0 (i2c-9)
    Do you want to scan it? (YES/no/selectively): yes
    Client found at address 0x2b
    Probing for `National Semiconductor LM78'...                No
    Probing for `National Semiconductor LM79'...                No
    Probing for `National Semiconductor LM80'...                No
    Probing for `National Semiconductor LM96080'...             No
    Probing for `Winbond W83781D'...                            No
    Probing for `Winbond W83782D'...                            No
    Probing for `Nuvoton NCT7802Y'...                           No
    Probing for `Winbond W83627HF'...                           No
    Probing for `Winbond W83627EHF'...                          No
    Probing for `Winbond W83627DHG/W83667HG/W83677HG'...        No
    Probing for `Asus AS99127F (rev.1)'...                      No
    Probing for `Asus AS99127F (rev.2)'...                      No
    Probing for `Asus ASB100 Bach'...                           No
    Probing for `Analog Devices ADM1021'...                     No
    Probing for `Analog Devices ADM1021A/ADM1023'...            No
    Probing for `Maxim MAX1617'...                              No
    Probing for `Maxim MAX1617A'...                             No
    Probing for `Maxim MAX1668'...                              No
    Probing for `Maxim MAX1805'...                              No
    Probing for `Maxim MAX1989'...                              No
    Probing for `Maxim MAX6655/MAX6656'...                      No
    Probing for `TI THMC10'...                                  No
    Probing for `National Semiconductor LM84'...                No
    Probing for `Genesys Logic GL523SM'...                      No
    Probing for `Onsemi MC1066'...                              No
    Probing for `Maxim MAX1618'...                              No
    Probing for `Maxim MAX1619'...                              No
    Probing for `National Semiconductor LM82/LM83'...           No
    Probing for `Maxim MAX6654'...                              No
    Probing for `Maxim MAX6690'...                              No
    Probing for `Maxim MAX6680/MAX6681'...                      No
    Probing for `Maxim MAX6695/MAX6696'...                      No
    Probing for `Texas Instruments TMP400'...                   No
    Probing for `National Semiconductor LM95231'...             No
    Probing for `National Semiconductor LM95233'...             No
    Probing for `National Semiconductor LM95241'...             No
    Probing for `Analog Devices ADM1029'...                     No
    Probing for `ITE IT8712F'...                                No
    Client found at address 0x50
    Probing for `Analog Devices ADM1033'...                     No
    Probing for `Analog Devices ADM1034'...                     No
    Probing for `SPD EEPROM'...                                 Yes
        (confidence 8, not a hardware monitoring chip)
    Probing for `EDID EEPROM'...                                No
    Client found at address 0x51
    Probing for `Analog Devices ADM1033'...                     No
    Probing for `Analog Devices ADM1034'...                     No
    Probing for `SPD EEPROM'...                                 Yes
        (confidence 8, not a hardware monitoring chip)
    Client found at address 0x5c
    Probing for `Analog Devices ADT7462'...                     No
    Probing for `SMSC EMC1072'...                               No
    Probing for `SMSC EMC1073'...                               No
    Probing for `SMSC EMC1074'...                               No
    
    
    Now follows a summary of the probes I have just done.
    Just press ENTER to continue: 
    
    Driver `coretemp':
      * Chip `Intel digital thermal sensor' (confidence: 9)
    
    To load everything that is needed, add this to /etc/modules:
    #----cut here----
    # Chip drivers
    coretemp
    #----cut here----
    If you have some drivers built into your kernel, the list above will
    contain too many modules. Skip the appropriate ones!
    
    Do you want to add these lines automatically to /etc/modules? (yes/NO)yes
    Successful!
    
    Monitoring programs won't work until the needed modules are
    loaded. You may want to run '/etc/init.d/kmod start'
    to load them.
    
    Unloading i2c-i801... OK
    
    chrism@diocletian:~$ 

And get it runing using:

    sudo /etc/init.d/kmod start

Last one, install psensor

    sudo apt-get install psensor

And run it as `psensor` and watch some graphs.  CPU temp around 50 degrees C.  Which is fine.  Compared to Severus in the Living room that keeps restarting as it's CPU temp hits it's auto-off point of 100 degrees C.


### Apr-2016
Ubuntu 15.04 (April/May 2015) upgraded to 15.10 (October 2015) and 16.04 (April 2016).

If prompted to run 'sudo apt autoremove' to remove old stuff, that's done as a matter of course and won't be logged.


## Slackware 14.1 (64-bit)
Kernel 3.10.17.


## Windows 7
Is installed, but it blue-screens briefly while booting then reboots immediately.  i.e. is unusable.  Recovery boot prompts for Windows 7 CD/DVD which I don't have, though could probably get from the Microsoft website easily enough.  Although the bottom of the laptop has no Windows COA licence sticker, so that might come completely unstuck.


# Hardware
[Lenovo ThinkPad T420](https://support.lenovo.com/gb/en/documents/pd015734)


# Location, IP etc...
See secure list on Evernote (search for "House Computers & Equipment").
