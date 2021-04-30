<a href="http://pentoo.ch"><img src="https://github.com/pentoo/pentoo-overlay/wiki/images/pentoo1.png"></a>
## Frequently Asked Questions - Pentoo
### Installation
#### Pentoo Linux Hard Disk Install
This feature is supported by the official Pentoo-installer. More info: Installation to disc page.

#### Pentoo Linux Encrypted Disk Install
How to go about it? Grimmlin took care to make our life easier and now this feature is available from the pentoo-installer.

    r5245 pentoo-installer: Added LUKS with pgp-encrypted keys support
#### Pentoo Linux Overlay Install
More info: OverlayUsage page.

### Customization
#### Can I install XXX instead of YYY?
Yes, there are number of packages which can replaced. For example:

    emerge -C wicd
    emerge -1 networkmanager
    emerge -C chromium
    emerge -1 google-chrome

#### Can I uninstall XXX? Pentoo pulls it by default
There are number of USE flags which can be disabled. For example:

    pentoo/pentoo kde -gnome -radio qemu
    pentoo/pentoo-mobile -ios
    pentoo/pentoo-wireless -drivers
    pentoo/pentoo-system -drivers -windows-compat
    pentoo/pentoo-misc -accessibility -atm -qt4 -gtk -X -office
    app-exploits/packetstormexploits -2009 -2010 -2011 -2012

    www-servers/lighttpd -mysql -php
    net-analyzer/wireshark -ares -btbb -gcrypt -geoip -kerberos -portaudio -smi libadns
    net-wireless/kismet -plugin-btscan -plugin-spectools

    net-misc/networkmanager -modemmanager
Use eix or examine each ebuild for more details

#### How to configure proxy for the overlay

Please customize the proxy variable in /etc/layman/layman.cfg
         
     proxy  : http://[user:pass@]www.my-proxy.org:3128

If unset, layman will use the http_proxy environment variable.
To use this method:

     cd /etc/env.d
     sudo touch 99local
     sudo nano 99local

and please add:

     http_proxy="http://YourProxyAddress:YourProxyPortNumber"
     https_proxy="http://YourProxyAddress:YourProxyPortNumber"
     ftp_proxy="http://YourProxyAddress:YourProxyPortNumber"

And then to apply changes to the system:

     sudo env-update && source /etc/profile

To check this variable:

     sudo echo $http_proxy

and then:

     git config --global http.proxy $http_proxy
     layman -S

If you use a local proxy such as CNTLM to authenticate through a corporate proxy server or for whatever other reason, you should be aware that you may experience a several minute delay in GIT making the first connection to the proxy. This could be caused by GIT/cURL resolving localhost to an IPv6 address first ::1 and trying to connect to that before timing out (in my case, after 2.5 minutes). After the timeout, cURL will then try the IPv4 version of 127.0.0.1. If your local proxy only listens on the IPv4 address (which seems a common occurrence), make sure you define the proxy as such:

     git config http.proxy http://127.0.0.1:8080

To troubleshoot problems with your proxy, you can set the following environment variables:

     set GIT_TRACE=1
     set GIT_CURL_VERBOSE=1

Then run the command again and you will get much more information about what GIT and the cURL library are up to.

#### Restrict Thunar from showing encrypted partitions
For some reason, Thunar will sometimes show encrypted partitions as removable devices. To restrict thunar from showing encrypted partitions:

    nano /etc/udev/rules.d/99-hide-disks.rules 
and add:

    KERNEL=="sda3", ENV{UDISKS_IGNORE}="1"
where sda3 is an encrypted partition meant to hide. Then use this command (as root) to trigger a refresh:

udevadm trigger
## Troubleshooting a Pentoo Installation
#### Compilation errors
##### Unable to compile app-exploits/packetstormexploits-meta-2000:0/0::pentoo
    cd /usr/portage/distfiles/
    wget --no-check-certificate http://dl.packetstormsecurity.net/1501-exploits/1501-exploits.tgz
    wget --no-check-certificate http://dl.packetstormsecurity.net/1502-exploits/1502-exploits.tgz
    wget --no-check-certificate http://dl.packetstormsecurity.net/1503-exploits/1503-exploits.tgz
    wget --no-check-certificate http://dl.packetstormsecurity.net/1504-exploits/1504-exploits.tgz
    wget --no-check-certificate http://dl.packetstormsecurity.net/1505-exploits/1505-exploits.tgz
    wget --no-check-certificate http://dl.packetstormsecurity.net/1412-exploits/2014-exploits.tgz
    wget --no-check-certificate http://dl.packetstormsecurity.net/1312-exploits/2013-exploits.tgz
    wget --no-check-certificate http://dl.packetstormsecurity.net/1212-exploits/2012-exploits.tgz
    wget --no-check-certificate http://dl.packetstormsecurity.net/1112-exploits/2011-exploits.tgz
    wget --no-check-certificate http://dl.packetstormsecurity.net/1012-exploits/2010-exploits.tgz
    emerge packetstormexploits-meta -1 -va
    
##### Unable to compile a kernel module
    * ERROR: sys-kernel/spl-0.6.2-r3::gentoo failed:
    * ERROR: sys-fs/zfs-kmod-0.6.2-r3::gentoo failed:
then do the same thing:

    ls -l /usr/src/linux
    lrwxrwxrwx 1 root root 12 Dec 25 07:50 /usr/src/linux -> /usr/src/linux-3.9.9-pentoo
    cd /usr/src/linux
assuming /usr/src/linux links to the 3.9.9-pentoo sources,

    zcat /proc/config.gz > .config
    make clean && make clean
    make prepare
    make modules_prepare
#### Upgrading
##### Unable to resolve blocks
Uninstall all blocks manually. For example:

    emerge -C dev-lang/vala
    emerge -C dev-libs/ecore