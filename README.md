## OVERVIEW ##

> Reaver performs a brute force attack against an access point's WiFi Protected Setup pin number.
> Once the WPS pin is found, the WPA PSK can be recovered and alternately the AP's wireless settings can be
> reconfigured.

> While Reaver does not support reconfiguring the AP, this can be accomplished with wpa\_supplicant once
> the WPS pin is known.

## DESCRIPTION ##

> Reaver targets the external registrar functionality mandated by the WiFi Protected Setup specification.
> Access points will provide authenticated registrars with their current wireless configuration (including
> the WPA PSK), and also accept a new configuration from the registrar.

> In order to authenticate as a registrar, the registrar must prove its knowledge of the AP's 8-digit pin
> number. Registrars may authenticate themselves to an AP at any time without any user interaction. Because
> the WPS protocol is conducted over EAP, the registrar need only be associated with the AP and does not
> need any prior knowledge of the wireless encryption or configuration.

> Reaver performs a brute force attack against the AP, attempting every possible combination in order to
> guess the AP's 8 digit pin number. Since the pin numbers are all numeric, there are 10^8 (100,000,000)
> possible values for any given pin number. However, because the last digit of the pin is a checksum value
> which can be calculated based on the previous 7 digits, that key space is reduced to 10^7 (10,000,000)
> possible values.

> The key space is reduced even further due to the fact that the WPS authentication protocol cuts the pin in
> half and validates each half individually. That means that there are 10^4 (10,000) possible values for the
> first half of the pin and 10^3 (1,000) possible values for the second half of the pin, with the last digit
> of the pin being a checksum.

> Reaver brute forces the first half of the pin and then the second half of the pin, meaning that the entire
> key space for the WPS pin number can be exhausted in 11,000 attempts. The speed at which Reaver can test
> pin numbers is entirely limited by the speed at which the AP can process WPS requests. Some APs are fast enough
> that one pin can be tested every second; others are slower and only allow one pin every ten seconds. Statistically,
> it will only take half of that time in order to guess the correct pin number.


## INSTALLATION ##

> Reaver is only supported on the Linux platform, requires the libpcap and libsqlite3 libraries, and can be built and
> installed by running:

```
		$ ./configure
		$ make
		# make install
```

> To remove everything installed/created by Reaver:

```
		# make distclean
```

## USAGE ##

> Usually, the only required arguments to Reaver are the interface name and the BSSID of the target AP:

```
		# reaver -i mon0 -b 00:01:02:03:04:05
```

> The channel and SSID (provided that the SSID is not cloaked) of the target AP will be automatically
> identified by Reaver, unless explicitly specified on the command line:

```
		# reaver -i mon0 -b 00:01:02:03:04:05 -c 11 -e linksys
```

> By default, if the AP switches channels, Reaver will also change its channel accordingly. However,
> this feature may be disabled by fixing the interface's channel:

```
		# reaver -i mon0 -b 00:01:02:03:04:05 --fixed
```

> The default receive timeout period is 5 seconds. This timeout period can be set manually if necessary
> (minimum timeout period is 1 second):

```
		# reaver -i mon0 -b 00:01:02:03:04:05 -t 2
```

> The default delay period between pin attempts is 1 second. This value can be increased or decreased
> to any non-negative integer value. A value of zero means no delay:

```
		# reaver -i mon0 -b 00:01:02:03:04:05 -d 0
```

> Some APs will temporarily lock their WPS state, typically for five minutes or less, when "suspicious"
> activity is detected. By default when a locked state is detected, Reaver will check the state every
> 315 seconds (5 minutes and 15 seconds) and not continue brute forcing pins until the WPS state is unlocked.
> This check can be increased or decreased to any non-negative integer value:

```
		# reaver -i mon0 -b 00:01:02:03:04:05 --lock-delay=250
```

> For additional output, the verbose option may be provided. Providing the verbose option twice will
> increase verbosity and display each pin number as it is attempted:

```
		# reaver -i mon0 -b 00:01:02:03:04:05 -vv
```

> The default timeout period for receiving the M5 and M7 WPS response messages is .1 seconds. This timeout period can be set manually if necessary (max timeout period is 1 second):

```
                # reaver -i mon0 -b 00:01:02:03:04:05 -T .5
```

> Some poor WPS implementations will drop a connection on the floor when an invalid pin is supplied
> instead of responding with a NACK message as the specs dictate. To account for this, if an M5/M7 timeout
> is reached, it is treated the same as a NACK by default. However, if it is known that the target AP sends
> NACKS (most do), this feature can be disabled to ensure better reliability. This option is largely useless
> as Reaver will auto-detect if an AP properly responds with NACKs or not:

```
		# reaver -i mon0 -b 00:01:02:03:04:05 --nack
```

> While most APs don't care, sending an EAP FAIL message to close out a WPS session is sometimes necessary.
> By default this feature is disabled, but can be enabled for those APs that need it:

```
		# reaver -i mon0 -b 00:01:02:03:04:05 --eap-terminate
```

> When 10 consecutive unexpected WPS errors are encountered, a warning message will be displayed. Since this
> may be a sign that the AP is rate limiting pin attempts or simply being overloaded, a sleep can be put in
> place that will occur whenever these warning messages appear:

```
		# reaver -i mon0 -b 00:01:02:03:04:05 --fail-wait=360
```

## FILES ##

> The following are Reaver source files:

  * 80211.c	Functions for reading, sending, and parsing 802.11 management frames
  * builder.c	Functions for building packets and packet headers
  * config.h	Generated by the configure script
  * cracker.c	Core cracking functions for Reaver.
  * defs.h	        Common header with most required definitions and declarations
  * exchange.c	Functions for initiating and processing a WPS exchange
  * globule.c	Wrapper functions for accessing global settings
  * iface.c	        Network interface functions
  * init.c	        Initialization functions
  * keys.c	        Contains tables of all possible pins
  * misc.c	        Mac address conversion, debug print functions, etc
  * pins.c	        Pin generation and randomization functions
  * send.c	        Functions for sending WPS response messages
  * sigalrm.c	Functions for handling SIGALRM interrupts
  * sigint.c	        Functions for handling SIGINT interrupts
  * wpscrack.c	Main Reaver source file
  * wps.h		Includes for wps wpa\_supplicant functions
  * libwps/	Generic library code for parsing WPS information elements

> The following files have been taken from wpa\_supplicant. Some have been modified from their original sources:

  * common/
  * crypto/
  * tls/
  * utils/
  * wps/

> The lwe directory contains Wireless Tools version 29, used for interfacing with Linux Wireless Extensions.