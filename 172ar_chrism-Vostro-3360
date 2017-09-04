# Machine Name
Defaulted to what Ubuntu chose on install: chrism-Vostro-3360

# Hardware
Dell Vostro 3360 booting through a USB stick to Ubuntu 17.04 x64

# Configuration Changes
Standard install of Ubuntu 17.04 64-bit from one USB stick to another.  Despite being instructed not to do so, it *has* installed a grub boot loader on the main HDD.

## Real-Time Clock (RTC)
Changed Ubuntu so it treats the hardware RTC as being in local time on UTC, so hopefully when booting back to Windows the time isn't wrong and causing issues.  Details [in this page](https://askubuntu.com/questions/169376/clock-time-is-off-on-dual-boot), but in essence you just run:
```timedatectl set-local-rtc 1```
at the command prompt.
