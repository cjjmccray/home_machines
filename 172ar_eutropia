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
-- (```SYS::MMap``` doesn't work - so they are case-sensitive)



# Hardware
[Raspberry Pi 3 model B+](https://www.raspberrypi.org/blog/raspberry-pi-3-model-bplus-sale-now-35/) bought spring 2019.


# Location, IP etc...
tba - likely in the cellar cupboard




Now try:
cpan LWP::UserAgent

cpan Sys::Mmap

not sure if these are case sensitive
