# DO NOT USE TESTING IN PROGRESS!

# Stubby-Installer-Asuswrt-Merlin
Stubby DNS Privacy Daemon Install Script for Asuswrt-Merlin Firmware

## Description

[Stubby](https://dnsprivacy.org/wiki/display/DP/DNS+Privacy+Daemon+-+Stubby) is an application that acts as a local DNS Privacy stub resolver (using DNS-over-TLS). Stubby encrypts DNS queries sent from a client machine (desktop or laptop) to a DNS Privacy resolver increasing end user privacy.

## Requirements
1. An Asus router with  [Asuswrt-Merlin](http://asuswrt.lostrealm.ca/) firmware installed.
2. A USB drive with entware installed.  Entware can be installed using [amtm - the SNBForum Asuswrt-Merlin Terminal Menu](https://www.snbforums.com/threads/amtm-the-snbforum-asuswrt-merlin-terminal-menu.42415/)
3. Turn off **DNSSEC** on the WAN GUI.  More about this issue coming soon...

The use of Stubby on Asuswrt-Merlin is experimental and not endorsed by the firmware developer. As an extra precaution, it is highly recommended to take a back-up of the jffs partition and the firmware configuration before proceeding with the installation. You can also use this script to uninstall Stubby and remove the changes made during the installation.   

The Stubby installer script **stubby_installer.sh** will
1. Install the entware packages **stubby** and **ca-certificates**
2. Override how the firmware manages DNS  
3. Default to Cloudfare DNS 1.1.1.1. You can change to other supported DNS over TLS providers by modifying /opt/var/stubby/stubby.yml and the DNS Settings on the WAN Menu.
4. Provide the option to remove Stubby and the firmware DNS overrides.

## Installation
Copy and paste the command below into an SSH session.
```javascript
/usr/sbin/curl --retry 3 "https://raw.githubusercontent.com/Xentrk/Stubby-Installer-Asuswrt-Merlin/master/install_stubby.sh" -o /jffs/scripts/install_stubby.sh && chmod 755 /jffs/scripts/install_stubby.sh && sh /jffs/scripts/install_stubby.sh
```
## Troubleshooting
If you break out of the installation process before it can complete, you may loose internet connectivity.  To quickly resolve:

1. Remove the lines below from the files **/jffs/scripts/dnsmasq.postconf**.  If you use one or more OpenVPN clients, remove the same line from **/jffs/scripts/openvpn-event**.

        cp /jffs/configs/resolv.dnsmasq /tmp/resolv.dnsmasq

2. Remove the following lines from **/jffs/configs/dnsmasq.conf.add**:

        server=127.0.0.1#5453
        server=0::1#5453

3. Edit **/tmp/resolv.dnsmasq** and replace the existing entry with the line:

        server=1.1.1.1

4. Type the command **service restart_dnsmasq**

5. If necessary, adjust the DNS setting on the WAN GUI page and select the **Apply** button.

## Validating that Stubby is Working
Run the following commands from an SSH session to verify that stubby is working properly:

    ps | grep stubby | grep -v grep
    netstat -lnptu | grep stubby
    drill github.com (requires entware package drill)
    nslookup github.com
    echo | openssl s_client -connect '1.1.1.1:853'

Use the [Cloudfare Help Page](https://1.1.1.1/help) to validate you are connected to 1.1.1.1 and **DNS over TLS** is working.  If working properly, the page will display a **Yes** as seen in the example below:

    Connected to 1.1.1.1         Yes
    Using DNS over HTTPS (DoH)   No
    Using DNS over TLS (DoT)     Yes

## Starting, Stopping and Killing Stubby
To **(start|stop|restart|check|kill|reconfigure)** stubby, type the command below where **option** is one of the options listed in the parenthesis.

    /opt/etc/init.d/S61stubby option

## DNS Test Web Sites
1. DNSSEC Validation by dnsmasq test: (requires the firmware version 384.6 or newer for test)
  * https://rootcanary.org/test.html

2. DNS Spoof and Entrophy Test
  * https://www.grc.com/dns/dns.htm (scroll down and click on "Initiate Standard DNS Spoofability Test")
  *	https://www.dns-oarc.net/oarc/services/dnsentropy

3. DNS Leak Test

  * https://www.dnsleaktest.com/ (use Extended test)
  *	https://ipleak.net/

4. DNSSEC Test

  * http://dnssec.vs.uni-due.de/
  * http://en.conn.internet.nl/connection/

5. WebRTC Leak Test

  * https://browserleaks.com/webrtc
  * https://ip8.com/webrtc-test
  * https://ipx.ac/run
  * https://www.perfect-privacy.com/check-ip/
  * https://www.doileak.com/

## Collaborators

* [Martineau](https://www.snbforums.com/members/martineau.13215/) on snbforums.com provided the **Chk_Entware** function.

* [John9527](https://www.snbforums.com/members/john9527.27638/) is the developer of the [Asuswrt-Merlin Fork](https://github.com/john9527/asuswrt-merlin). **John9527** implemented Stubby in August 2018 and provided the **stubby.yml** configuration generated by the firmware **Asuswrt-Merlin-Fork**. The **stubby.yml** provided by **John9527** was used as a benchmark for this project.  My goal is to standardize the configurations used in the [Asuswrt-Merlin Fork](https://github.com/john9527/asuswrt-merlin) where possible.     


## Support

Support for the project is available on snbforums.com (Link Coming Soon)
