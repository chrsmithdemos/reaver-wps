## Reaver Pro Hardware ##

Reaver Pro is a compact embedded device customized for Reaver attacks. No more driver compatibility issues. Plug Reaver Pro into your Windows, OS X, or Linux machine and It just works!

Reaver Pro Features:
  * New easy to use GUI
  * Optimized PIN sequencing using Markov Chains based on on PINs from hundreds of actual WPS-enabled devices
  * Automatically connect to the target and send the PIN and PSK to an email address you specify

**[Buy the NEW Reaver Pro. Now shipping WORLDWIDE!!!](http://www.reaversystems.com)**

## Prerequisites for Open Source Reaver ##

  1. You must be running Linux
  1. You must have a wireless card capable of raw injection
  1. You must put your wireless card into monitor mode. This is most easily done using airmon-ng from the aircrack-ng tool suite.

## Basic Usage ##

First, make sure your wireless card is in monitor mode:

```
# airmon-ng start wlan0
```

To run Reaver, you must specify the BSSID of the target AP and the name of the monitor mode interface (usually 'mon0', **not** 'wlan0', although this will vary based on your wireless card/drivers):

```
# reaver -i mon0 -b 00:01:02:03:04:05
```

You will probably also want to use -vv to get verbose info about Reaver's progress:

```
# reaver -i mon0 -b 00:01:02:03:04:05 -vv
```

## Speeding Up the Attack ##

By default, Reaver has a 1 second delay between pin attempts. You can disable this delay by adding '-d 0' on the command line, but some APs may not like it:

```
# reaver -i mon0 -b 00:01:02:03:04:05 -vv -d 0
```

Another option that can speed up an attack is --dh-small. This option instructs Reaver to use small diffie-hellman secret numbers in order to reduce the computational load on the target AP:

```
# reaver -i mon0 -b 00:01:02:03:04:05 -vv --dh-small
```

## MAC Spoofing ##

In some cases you may want/need to spoof your MAC address. Reaver supports MAC spoofing with the --mac option, but you must ensure that you have spoofed your MAC correctly in order for it to work.

Changing the MAC address of the virtual monitor mode interface (typically named mon0) WILL NOT WORK. **You must change the MAC address of your wireless card's physical interface**. For example:

```
# ifconfig wlan0 down
# ifconfig wlan0 hw ether 00:BA:AD:BE:EF:69
# ifconfig wlan0 up
# airmon-ng start wlan0
# reaver -i mon0 -b 00:01:02:03:04:05 -vv --mac=00:BA:AD:BE:EF:69
```

## Default Pins ##

It has been reported that some models/vendors/ISPs all come configured with a default pin. Common pins are 12345670, 00005678, 01230000, etc. Reaver attempts known default pins first.

## Errors and Warnings ##

It is not uncommon to get a few errors or warnings during the attack, usually related to receive timeouts or out of order WPS messages. You may even get these warnings for a few minutes until the pin count starts incrementing again.

However, if your pin count does not increment at all, or increments only occasionally with lots of errors/warnings, answer the following:

  1. Does the target AP support WPS and is WPS enabled?
  1. Did you put your wireless card into monitor mode?
  1. Did you specify the monitor mode interface with the -i option?
  1. Do you have a good signal from the AP?

If you still have problems, you can see if your problem is already listed in the project issue tracker. If not, create a new issue, and be sure to include:

  1. Linux distro, distro version, and architecture (32 bit or 64bit?)
  1. Wireless card and driver
  1. Pcap file demonstrating the issue, if possible