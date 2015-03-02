## Is there a GUI for Reaver? ##

Reaver Pro is a compact embedded device customized for Reaver attacks. No more driver compatibility issues. Plug Reaver Pro into your Windows, OS X, or Linux machine and It just works!

Reaver Pro Features:
  * New easy to use GUI
  * Optimized PIN sequencing using Markov Chains based on on PINs from hundreds of actual WPS-enabled devices
  * Automatically connect to the target and send the PIN and PSK to an email address you specify

**[Buy the NEW Reaver Pro!!!](http://www.reaversystems.com)**

## Why do I get timeouts/out of order warnings? ##

This is usually due to interference or low signal strength. The AP's WPS registrar functionality could also be locked.

You may get these warnings randomly during an attack, and they may take a few minutes to clear up. This is due to the AP's WPS state machine and Reaver's getting out of sync; the AP's state machine may not be reset for ~2 minutes. After this, things should start working again.

## Why doesn't MAC spoofing work? ##

It does, but you have to make sure you are spoofing the MAC on the physical card's interface. See the [wiki](http://code.google.com/p/reaver-wps/wiki/HintsAndTips).

## WEP cracking works fine with low signal strengths, why does Reaver have problems? ##

WEP cracking usually entails re-transmitting captured packets in order to generate more responses from the AP and therefore collect IVs faster. In this situation, it does not matter if some packets get dropped or corrupted as you are simply looking for a quantity of packets.

In contrast, attacks against WPS require that specific packets must be exchanged between Reaver and the AP in specific order for each pin that is tested. If a packet gets dropped or corrupted, the transaction may need to be started all over again. This makes WPS attacks much more susceptible to failure due to poor signal strength or interference.

Another concern to keep in mind is that just because you can see the AP doesn't mean the AP can see you. You don't know what interference may be at the AP's location, nor do you know how selective the AP's RF front end is, nor how sensitive the AP's receiver is. All of this can contribute to communications problems. Thus, it is best to get the strongest signal possible before running Reaver.

## Reaver just tries the same pin over and over ##

Make sure your target AP supports WPS. Run the walsh tool to scan for WPS-enabled APs and make sure your target AP is listed.

## Help, Reaver can't associate with my AP! ##

This could be due to interference or low signal strength. It could also be a driver issue, check the supported drivers wiki.

## I get a warning "rate limiting detected", over and over again ##

This earning message means that the AP has locked WPS pin attempts. This is usually only a temporary lock (around 5 minutes typically), but in some cases may be permanent (requires administrator intervention to unlock).

There is a known bug in Reaver 1.3 that causes it to not detect when the AP has lifted the lock. Use the --ignore-locks option as a work around, or grab the latest code from the SVN trunk.

## Can I run more than one instance of Reaver against an AP? ##

Technically yes, but this is ultimately a flawed approach to increasing attack speed. The primary limitation on attack speed is the low resources (memory, CPU, etc) of the AP, so having two simultaneous attackers will result in twice the CPU load.

It is better to use Reaver's advanced options to help speed up the attack.