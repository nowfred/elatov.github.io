---
title: Installing Mediatomb on FreeBSD 9 and Connecting to it with XBMC from a Fedora 17 OS
author: Karim Elatov
layout: post
permalink: /2012/10/installing-mediatomb-on-freebsd-9-and-connecting-to-it-with-xbmc-from-a-fedora-17-os/
dsq_thread_id:
  - 1404673594
categories:
  - Home Lab
  - Networking
tags:
  - dlna
  - mediatomb
  - upnp
---
I recently got some new hardware and wanted to setup a video streaming server in my home. There are a lot of choices out there, for example check out the wiki page &#8220;<a href="http://en.wikipedia.org/wiki/List_of_streaming_media_systems" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://en.wikipedia.org/wiki/List_of_streaming_media_systems']);">List of streaming media systems</a>&#8220;. Most of those servers are for streaming media over the internet, where I was looking something more for my home network. I then came across this broadcom article entitled &#8220;<a href="http://www.broadcom.com/docs/articles/Network_Home.pdf" onclick="javascript:_gaq.push(['_trackEvent','download','http://www.broadcom.com/docs/articles/Network_Home.pdf']);">How to Select a Proper Technology for HD Video Streaming in Home Networking Environments</a>&#8220;. From that article:

> This emerging “connected home” ecosystem comprises a variety of connectivity technologies – including Wi-Fi, Ethernet and MoCA – as well as a host of different file formats and potentially incompatible software applications that can lead to consumer confusion. With potentially so many ways to implement the sharing of digital video throughout the home, there could be nearly as many ways for users to experience incompatibility and frustration. In order to reduce confusion and to enable as seamless a user experience as possible, devices need to inter-operate transparently.
> 
> The key means for enabling these connectivity technologies to work together is to bring together proven and established network and multimedia standards into a single consistent and reliable framework that serves developers, consumers and service providers alike. To this end, Digital Living Network Alliance (DLNA) has developed robust, technical guidelines defining device classes and networked home use cases which, when built into digital TVs, STBs, Blu-ray players, mobile handset, personal media players and other devices allow high-quality streaming of multimedia content over wireless and wired connections.

The paper also included a pretty good table of all the guidelines from the DLNA, from the paper:

<a href="http://virtuallyhyper.com/wp-content/uploads/2012/10/dlna_guidelines.png" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://virtuallyhyper.com/wp-content/uploads/2012/10/dlna_guidelines.png']);"><img class="alignnone size-full wp-image-4169" title="dlna_guidelines" src="http://virtuallyhyper.com/wp-content/uploads/2012/10/dlna_guidelines.png" alt="dlna guidelines Installing Mediatomb on FreeBSD 9 and Connecting to it with XBMC from a Fedora 17 OS" width="530" height="377" /></a>

DLNA uses UPnP for a lot of it&#8217;s functions, so I decided to setup a UPnP capable server to stream my media. I was then checking out the wiki page &#8220;<a href="http://en.wikipedia.org/wiki/Comparison_of_UPnP_AV_MediaServers" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://en.wikipedia.org/wiki/Comparison_of_UPnP_AV_MediaServers']);">Comparison of UPnP AV media servers</a>&#8221; to see a list of UPnP servers. Of course, I wanted to choose a media server with the most functionality. Here are the ones that stood out:

*   <a href="http://mediatomb.cc" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://mediatomb.cc']);">MediaTomb</a>
*   <a href="http://en.wikipedia.org/wiki/Plex_(software)" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://en.wikipedia.org/wiki/Plex_(software)']);" title="Plex (software)">Plex</a>
*   <a href="http://en.wikipedia.org/wiki/PS3_Media_Server" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://en.wikipedia.org/wiki/PS3_Media_Server']);" title="PS3 Media Server">PS3 Media Server</a>
*   <a href="http://sourceforge.net/projects/minidlna/" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://sourceforge.net/projects/minidlna/']);">MiniDLNA</a>

I then wanted to check out what other people were setting up, after doing some googling around, I found the following:

*   <a href="http://www.makeuseof.com/tag/6-upnpdlna-servers-streaming-media-devices-crossplatform/" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://www.makeuseof.com/tag/6-upnpdlna-servers-streaming-media-devices-crossplatform/']);">6 UPnP/DLNA Servers For Streaming Media To Your Devices [Cross-Platform]</a> (+1 MediaTomb) 
*   <a href="http://www.thev.net/PaulLiu/bsd-home-server.html" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://www.thev.net/PaulLiu/bsd-home-server.html']);">Setup a Home Server with FreeBSD and ZFS</a> (+1 MediaTomb +1 minidlna) 
*   <a href="http://superuser.com/questions/288639/recommended-media-server-software-for-freebsd-for-upnp-dlna" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://superuser.com/questions/288639/recommended-media-server-software-for-freebsd-for-upnp-dlna']);">Recommended media server software for FreeBSD for UPnP/DLNA</a> (+1 MediaTomb +1 minidlna) 

I also wanted the package to be included in ports (just for management&#8217;s sake). I checked out ports:

    elatov@freebsd:/usr/ports>find . -iname 'ps3*'
    elatov@freebsd:/usr/ports>find . -iname 'plex'
    elatov@freebsd:/usr/ports>find . -iname 'mediatomb'
    ./net/mediatomb
    elatov@freebsd:/usr/ports>find . -iname 'minidlna'
    ./net/minidlna
    

I couldn&#8217;t find *ps3mediaserver*. I did find this &#8216;how to&#8217;: <a href="http://stepbystep.wikkii.com/wiki/PS3MediaServerOnFreeNAS" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://stepbystep.wikkii.com/wiki/PS3MediaServerOnFreeNAS']);">PS3MediaServerOnFreeNAS</a>, but it was a lot of work. Then checking out this FreeBSD forum: &#8220;<a href="http://forums.plexapp.com/index.php/topic/28760-plex-on-freebsd/" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://forums.plexapp.com/index.php/topic/28760-plex-on-freebsd/']);">Plex on FreeBSD?</a>&#8220;, it doesn&#8217;t looks there is support for that yet. So I was thinking either MediaTomb or miniDLNA and since MediaTomb had better transcoding capabilities, I decided to go with that. Here are the steps I took to install MediaTomb on my FreeBSD machine.

### 1. Install MediaTomb from ports

Let&#8217;s get this started:

    elatov@freebsd:~>cd /usr/ports/net/mediatomb/
    elatov@freebsd:/usr/ports/net/mediatomb>sudo make install clean
    

The compile went for a while, since it needed to install **ffmpeg**. I wanted to include every possible encoding/codec, just to make sure I can play everything. Here is how the config looked like for mediatomb:

    elatov@freebsd:/usr/ports/net/mediatomb>sudo make showconfig
    ===> The following configuration options are available for mediatomb-0.12.1_8:
         SQLITE3=on: sqlite3 support
         MYSQL=off: MySQL support
         JS=on: JavaScript (SpiderMonkey) support
         LIBEXIF=on: libexif support
         TAGLIB=on: taglib support
         FFMPEG=on: ffmpeg metadata extraction support
         FFMPEGTHUMBNAILER=on: ffmpeg thumbnailer support
         EXTERNAL_TRANSCODING=on: external transcoding support
         CURL=on: curl support
         ID3LIB=off: id3lib support
         LIBEXTRACTOR=off: libextractor support
         DEBUG=off: debug build
    ===> Use 'make config' to modify these settings
    

And here is how my **ffmpeg** config looked like:

    elatov@freebsd:/usr/ports/multimedia/ffmpeg>sudo make showconfig
    ===> The following configuration options are available for ffmpeg-0.7.13_6,1:
         AACPLUS=off: AAC support via libaacplus
         ALSA=off: ALSA audio architecture support
         AMR_NB=off: AMR Narrow Band audio support (opencore)
         AMR_WB=off: AMR Wide Band audio support (opencore)
         CELT=off: CELT audio codec support
         DEBUG=off: Install debug symbols
         DIRAC=off: Dirac codec support via libdirac
         FAAC=on: FAAC AAC encoder support
         FFSERVER=on: Build and install ffserver
         FREETYPE=on: TrueType font rendering support
         FREI0R=on: Frei0r video plugins support
         GSM=off: GSM codec support
         LAME=on: LAME MP3 audio encoder support
         OPENCV=on: OpenCV support
         OPENJPEG=on: Enhanced JPEG graphics support
         OPTIMIZED_CFLAGS=off: Use extra compiler optimizations
         RTMP=off: RTMP protocol support via librtmp
         SCHROEDINGER=on: Dirac codec support via libschroedinger
         SDL=off: Simple Direct Media Layer support
         SPEEX=off: Speex audio format support
         THEORA=on: Ogg Theora video codec support
         VAAPI=off: VAAPI (GPU video acceleration) support
         VDPAU=off: VDPAU (GPU video acceleration) support
         VORBIS=on: Ogg Vorbis audio codec support
         VO_AACENC=off: AAC audio encoding via vo-aacenc
         VO_AMRWBENC=off: AMR Wide Band encoding via vo-amrwbenc
         VPX=on: VP8 video codec support
         X11GRAB=off: Enable x11 grabbing
         X264=on: H.264 video codec support via x264
         XVID=on: Xvid MPEG-4 video codec support
    ===> Use 'make config' to modify these settings
    

The compile was successful so now it&#8217;s time to enable the service.

### 2. Enable the MediaTomb Service to Start Automatically

I added the following entries into the **/etc/rc.conf** file

    # Enable mediatomb
    mediatomb_enable="YES"
    mediatomb_flags="-i 192.168.1.101"
    

I rebooted and the MediaTomb service was up and running

    elatov@freebsd:~>ps auxww | grep media | grep -v grep
    mediatomb  1189   0.0  0.9  74732  22276  ??  Ss    9:05AM   0:00.24 /usr/local/bin/mediatomb -i 192.168.1.101 -d -c /usr/local/etc/mediatomb/config.xml -l /var/log/mediatomb.log -u mediatomb -g mediatomb -P /var/mediatomb/mediatomb.pid
    elatov@freebsd:~>sudo /usr/local/etc/rc.d/mediatomb status
    mediatomb is running as pid 1189.
    

As you can see it was running as the **mediatomb** user and group. I like security, so this was perfect. Also checking out the logs, I saw that it started up successfully:

    elatov@freebsd:/var/log>sudo tail -5 mediatomb.log
    2012-10-07 09:05:30    INFO: Configuration check succeeded.
    2012-10-07 09:05:30    INFO: Initialized port: 49152
    2012-10-07 09:05:30    INFO: Server bound to: 192.168.1.101
    2012-10-07 09:05:31    INFO: MediaTomb Web UI can be reached by following this link:
    2012-10-07 09:05:31    INFO: http://192.168.1.101:49152/
    

### 3. Go to the Web Management Interface for MediaTomb and Add a Folder Where Your Media is Stored

Open up a browser and point it to **http://IP\_OF\_MEDIATOMB_SERVER:49152**. Here is how my browser looked like:

<a href="http://virtuallyhyper.com/wp-content/uploads/2012/10/media_tomb_web_mgmt.png" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://virtuallyhyper.com/wp-content/uploads/2012/10/media_tomb_web_mgmt.png']);"><img class="alignnone size-full wp-image-4170" title="media_tomb_web_mgmt" src="http://virtuallyhyper.com/wp-content/uploads/2012/10/media_tomb_web_mgmt.png" alt="media tomb web mgmt Installing Mediatomb on FreeBSD 9 and Connecting to it with XBMC from a Fedora 17 OS" width="732" height="240" /></a>

From here you can expand your file system and add a directory which will be served by the UPnP server and you can set it be scanned for new media at your desired frequency. Here is how my folder looked like:

<a href="http://virtuallyhyper.com/wp-content/uploads/2012/10/media_tomb_add_folder.png" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://virtuallyhyper.com/wp-content/uploads/2012/10/media_tomb_add_folder.png']);"><img class="alignnone size-full wp-image-4177" title="media_tomb_add_folder" src="http://virtuallyhyper.com/wp-content/uploads/2012/10/media_tomb_add_folder.png" alt="media tomb add folder Installing Mediatomb on FreeBSD 9 and Connecting to it with XBMC from a Fedora 17 OS" width="790" height="390" /></a>

That should be it for the MediaTomb Setup.

### 4. Enable Multicast in your OS

UPnP relies on multicast and IGMP to discover devices and to serve up files. Multicast is mostly UDP so it makes sense for streaming video. From the MediaTomb <a href="http://mediatomb.cc/pages/documentation" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://mediatomb.cc/pages/documentation']);">Documentation</a>, here is what is necessary:

> **4.1. Network Setup**  
> Some systems require a special setup on the network interface. If MediaTomb exits with UPnP Error -117, or if it does not respond to M-SEARCH requests from the renderer (i.e. MediaTomb is running, but your renderer device does not show it) you should try the following settings (the lines below assume that MediaTomb is running on a Linux machine, on network interface eth1):
> 
>     # route add -net 239.0.0.0 netmask 255.0.0.0 eth1 
>     # ifconfig eth1 allmulti
>     
> 
> Those settings will be applied automatically by the init.d startup script.
> 
> You should also make sure that your firewall is not blocking port UDP port 1900 (required for SSDP) and UDP/TCP port of MediaTomb. By default MediaTomb will select a free port starting with 49152, however you can specify a port of your choice in the configuration file.

I wanted to make sure FreeBSD was capable of handling Multicast traffic. Another person was wondering the same thing. From the FreeBSD forum: <a href="http://forums.freebsd.org/showthread.php?t=13270" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://forums.freebsd.org/showthread.php?t=13270']);">UPnP Setup- ifconfig allmulti Crashes Network</a>:

> As you can see, the MULTICAST flag is set, but not the ALLMULTI flag. That&#8217;s because ALLMULTI is the linux equivalent of MULTICAST.

So if you have the MULTICAST flag set on the interface you should be all set. I checked out my interface and it indeed had the flag set:

    elatov@freebsd:~>ifconfig em0
    em0: flags=8843<up ,BROADCAST,RUNNING,SIMPLEX,MULTICAST> metric 0 mtu 1500
        options=9b<rxcsum ,TXCSUM,VLAN_MTU,VLAN_HWTAGGING,VLAN_HWCSUM>
        ether 00:c0:9f:41:c5:fa
        inet 192.168.1.101 netmask 0xffffff00 broadcast 192.168.1.255
        inet6 fe80::2c0:9fff:fe41:c5fa%em0 prefixlen 64 scopeid 0x1
        nd6 options=29<performnud ,IFDISABLED,AUTO_LINKLOCAL>
        media: Ethernet autoselect (100baseTX <full -duplex>)
        status: active
    

I also wanted to make sure I have the correct routes in place to allow for IGMP traffic and I did:

    elatov@freebsd:~>ifmcstat -i em0 -f inet
    em0:
        inet 192.168.1.101
        igmpv3 flags=0<> rv 2 qi 125 qri 10 uri 3
            group 239.255.255.250 mode exclude
                mcast-macaddr 01:00:5e:7f:ff:fa
            group 224.0.0.1 mode exclude
                mcast-macaddr 01:00:5e:00:00:01
    

### 5. Make sure the appropriate ports are open on your UPnP Server and Client

From MediaTomb <a href="http://mediatomb.cc/dokuwiki/faq:faq" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','']);">FAQ</a>:

> The UPnP protocol players use to find media servers relies on the IGMP protocol, which uses multicast. Be sure your firewall is allowing IGMP join requests from any IP address, and general IGMP traffic destined for broadcast addresses. For example, using iptables on linux and assuming eth1 is the interface MediaTomb is listening on:
> 
>     # Special case to allow 0.0.0.0 source for UPnP to use IGMP to 
>     # register clients with media servers and routers. 
>     iptables -A INPUT -i eth1 -s 0.0.0.0/32 -d 224.0.0.1/32 -p igmp -j ACCEPT 
>     # Any firewall code to limit eth1 input traffic to the appropriate IP subnets 
>     # must come AFTER the above special case. 
>     # UPnP uses IGMP multicast to find media servers. Accept IGMP broadcast packets 
>     iptables -A INPUT -i eth1 -d 239.0.0.0/8 -p igmp -j ACCEPT
>     

My FreeBSD machine had no external facing ports to the internet, so I actually didn&#8217;t have a firewall running on it. But if I did, I would run **pf** and set the following rules by converting the above **iptables** rules to pf rules:

    # allow Multicast traffic
    pass in on em0 inet proto igmp to 224.0.0.0/4 allow-opts
    pass in on em0 inet proto udp  to 224.0.0.0/4
    

Taken from &#8220;<a href="http://un.geeig.net/openbsd-vdsl.html" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://un.geeig.net/openbsd-vdsl.html']);">Using OpenBSD with VDSL</a>&#8221; and this as well:

    # allow UPnP traffic
    pass in on em0 from any to 239.0.0.0/8 keep state
    

Also from the MediaTomb <a href="http://mediatomb.cc/dokuwiki/faq:faq" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','']);">FAQ</a> page:

> make sure that your firewall is not blocking the server, port 1900 has to be open as well as the port on which the server is running (i.e. web UI port), both TCP and UDP

Again here is an example for iptables from &#8220;<a href="http://www.gentoo-wiki.info/HOWTO_Setup_UPnP_with_IPTables" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://www.gentoo-wiki.info/HOWTO_Setup_UPnP_with_IPTables']);">HOWTO&#95;Setup&#95;UPnP&#95;with&#95;IPTables</a>&#8220;

    iptables -t filter -A INPUT -i eth0 -d 239.0.0.0/8 -j ACCEPT
    iptables -t filter -A INPUT -i eth0 -p tcp --dport 49152 -j ACCEPT
    iptables -t filter -A INPUT -i eth0 -p udp --dport 1900 -j ACCEPT
    

Converting that to **pf**, we would get this:

    # Allow SSDP (used for UPnP Discovery)
    pass in on em0 proto udp from any to any port 1900
    # allow Mediatomb Web Interface
    pass in on em0 proto { tcp udp } from any to any port 49152
    

Since I didn&#8217;t have a firewall on my FreeBSD machine, I didn&#8217;t have to set that up. Next I needed to open up the necessary ports on my laptop, which was running Fedora 17. I shut down the my firewall:

    [elatov@klaptop ~]$ sudo service iptables stop
    Redirecting to /bin/systemctl stop  iptables.service
    

Then I installed **upnp-inspector**:

    [elatov@klaptop ~]$ sudo yum install upnp-inspector
    

Here is the installed package:

    [elatov@klaptop ~]$ rpm -qa | grep upnp-ins
    upnp-inspector-0.2.2-6.fc17.noarch
    

I then started **upnp-inspector** and I saw the following:

<a href="http://virtuallyhyper.com/wp-content/uploads/2012/10/upnp-inspector.png" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://virtuallyhyper.com/wp-content/uploads/2012/10/upnp-inspector.png']);"><img src="http://virtuallyhyper.com/wp-content/uploads/2012/10/upnp-inspector.png" alt="upnp inspector Installing Mediatomb on FreeBSD 9 and Connecting to it with XBMC from a Fedora 17 OS" title="upnp-inspector" width="324" height="233" class="alignnone size-full wp-image-4178" /></a>

So I was able to see the MediaTomb Server just fine. YAY! While I launched **upnp-inspector**, I also fired up **tcpdump** in back ground to see what kind of traffic was going through:

    [elatov@klaptop ~]$ sudo tcpdump -i wlan0 udp or igmp and not udp port domain -nn
    tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
    listening on wlan0, link-type EN10MB (Ethernet), capture size 65535 bytes
    13:45:42.294794 IP 192.168.1.116.55076 > 239.255.255.250.1900: UDP, length 94
    13:45:42.294854 IP 192.168.1.116.55076 > 239.255.255.250.1900: UDP, length 94
    13:45:42.295553 IP 192.168.1.116 > 224.0.0.22: igmp v3 report, 1 group record(s)
    13:45:42.296502 IP 192.168.1.101.19979 > 192.168.1.116.55076: UDP, length 291
    13:45:42.396308 IP 192.168.1.101.19979 > 192.168.1.116.55076: UDP, length 300
    13:45:42.497342 IP 192.168.1.101.19979 > 192.168.1.116.55076: UDP, length 343
    13:45:42.598989 IP 192.168.1.101.16571 > 192.168.1.116.55076: UDP, length 357
    13:45:42.699595 IP 192.168.1.101.33257 > 192.168.1.116.55076: UDP, length 355
    13:45:43.019552 IP 192.168.1.101.64740 > 192.168.1.116.55076: UDP, length 291
    13:45:43.120488 IP 192.168.1.101.64740 > 192.168.1.116.55076: UDP, length 300
    13:45:43.221411 IP 192.168.1.101.64740 > 192.168.1.116.55076: UDP, length 343
    13:45:43.322406 IP 192.168.1.101.13329 > 192.168.1.116.55076: UDP, length 357
    13:45:43.423473 IP 192.168.1.101.35252 > 192.168.1.116.55076: UDP, length 355
    13:45:43.517630 IP 192.168.1.116 > 224.0.0.22: igmp v3 report, 1 group record(s)
    13:45:46.381568 IP 192.168.1.116 > 224.0.0.22: igmp v3 report, 1 group record(s)
    13:45:47.039584 IP 192.168.1.116 > 224.0.0.22: igmp v3 report, 1 group record(s)
    ^C
    16 packets captured
    16 packets received by filter
    0 packets dropped by kernel
    

Checking out the same thing on our FreeBSD machine, I saw the following:

    elatov@freebsd:~>sudo tcpdump -i em0 -nn ip net 224.0.0.0/8 or ip net 239.0.0.0/8 or udp and not udp port domain
    tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
    listening on em0, link-type EN10MB (Ethernet), capture size 65535 bytes
    13:49:08.165626 IP 192.168.1.116.32958 > 239.255.255.250.1900: UDP, length 94
    13:49:08.165999 IP 192.168.1.116.32958 > 239.255.255.250.1900: UDP, length 94
    13:49:08.166315 IP 192.168.1.101.26832 > 192.168.1.116.32958: UDP, length 291
    13:49:08.166499 IP 192.168.1.116 > 224.0.0.22: igmp v3 report, 1 group record(s)
    13:49:08.266548 IP 192.168.1.101.26832 > 192.168.1.116.32958: UDP, length 300
    13:49:08.367545 IP 192.168.1.101.26832 > 192.168.1.116.32958: UDP, length 343
    13:49:08.468631 IP 192.168.1.101.26474 > 192.168.1.116.32958: UDP, length 357
    13:49:08.569623 IP 192.168.1.101.52540 > 192.168.1.116.32958: UDP, length 355
    13:49:11.000750 IP 192.168.1.101.30293 > 192.168.1.116.32958: UDP, length 291
    13:49:11.101542 IP 192.168.1.101.30293 > 192.168.1.116.32958: UDP, length 300
    13:49:11.202540 IP 192.168.1.101.30293 > 192.168.1.116.32958: UDP, length 343
    13:49:11.303613 IP 192.168.1.101.15855 > 192.168.1.116.32958: UDP, length 357
    13:49:11.404592 IP 192.168.1.101.62518 > 192.168.1.116.32958: UDP, length 355
    13:49:12.328086 IP 192.168.1.116 > 224.0.0.22: igmp v3 report, 1 group record(s)
    13:49:14.276460 IP 192.168.1.116 > 224.0.0.22: igmp v3 report, 1 group record(s)
    ^C
    15 packets captured
    155 packets received by filter
    0 packets dropped by kernel
    

So we can see that first, UPnP discovers on 239.255.255.250:1900 UDP (SSDP) then we get an IGMP (224.0.0.22) packet to join a group and then the UDP/multicast traffic for the rest. Once we are done, we get a IGMP packet to leave the group.

So the FreeBSD machine was handling the multicast traffic just fine. I also confirmed by checking the **netstat** command:

    elatov@freebsd:~>netstat -ss -p igmp
    igmp:
        56 messages received
        2 membership reports sent
    

We were definitely processing IGMP packets, and I wasn&#8217;t dropping any packets:

    elatov@freebsd:~>netstat -ain -f inet
    Name    Mtu Network       Address              Ipkts Ierrs Idrop    Opkts Oerrs  Coll
    em0    1500 192.168.1.0/2 192.168.1.101        23423     -     -    18012     -     -
                              239.255.255.250 
                              224.0.0.1       
    lo0   16384 127.0.0.0/8   127.0.0.1                0     -     -        0     -     -
                              224.0.0.1       
    

From the above **tcpdump** I saw that the server responds on a random UDP port above 30,000. I actually found an Fedora forum that talked about this: &#8220;<a href="http://fedoraforum.org/forum/showthread.php?t=281540" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://fedoraforum.org/forum/showthread.php?t=281540']);">allowing xbmc to discover upnp mediatomb through iptables firewall</a>&#8220;. From that forum:

> I find that xbmc upnp client needs to receive a udp reply after broadcasting ssdp to search for upnp devices. How do I limit the reply udp port or let the firewall allow it without opening all the udp ports?
> 
> I have iptables firewall on the xbmc client machine set up to only allow udp port 30000-59999:
> 
>     -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT 
>     -A INPUT -p icmp -j ACCEPT 
>     -A INPUT -m state --state NEW -m udp -p udp --dport 30000:59999 -j ACCEPT 
>     ... other rules ... 
>     -A INPUT -j DROP
>     

I started **iptables** back up and added the rule to my **iptables** rule-set:

    [elatov@klaptop ~]$ sudo service iptables start
    Redirecting to /bin/systemctl start  iptables.service
    [elatov@klaptop ~]$ sudo iptables -I INPUT 14 -s 192.168.1.0/24 -m udp -p udp --dport 30000:59999 -j ACCEPT
    

I fired up upnp-inspector and was able to see the *mediatomb* server still. I then saved the **iptables** setup:

    [elatov@klaptop ~]$ sudo /usr/libexec/iptables.init save
    iptables: Saving firewall rules to /etc/sysconfig/iptables:
    

### 6. Install XBMC and Add a UPnP Library from the MediaTomb Server

The install is pretty easy:

    [elatov@klaptop ~]$ sudo yum install xbmc
    

After it&#8217;s installed you can just launch it by running **xbmc** from the command prompt/terminal. Once it&#8217;s launched it will look something like this:

<a href="http://virtuallyhyper.com/wp-content/uploads/2012/10/screenshot000.png" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://virtuallyhyper.com/wp-content/uploads/2012/10/screenshot000.png']);"><img src="http://virtuallyhyper.com/wp-content/uploads/2012/10/screenshot000.png" alt="screenshot000 Installing Mediatomb on FreeBSD 9 and Connecting to it with XBMC from a Fedora 17 OS" title="screenshot000" width="1280" height="800" class="alignnone size-full wp-image-4180" /></a>

Next, enable the built-in UPnP client from XBMC. Go to System -> Settings -> Network -> Services and Check the box that says &#8220;Allow Control of XBMC via UPnP&#8221;. The GUI looks something like this:

<a href="http://virtuallyhyper.com/wp-content/uploads/2012/10/screenshot001.png" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://virtuallyhyper.com/wp-content/uploads/2012/10/screenshot001.png']);"><img src="http://virtuallyhyper.com/wp-content/uploads/2012/10/screenshot001.png" alt="screenshot001 Installing Mediatomb on FreeBSD 9 and Connecting to it with XBMC from a Fedora 17 OS" title="screenshot001" width="1280" height="800" class="alignnone size-full wp-image-4181" /></a>

The settings are described in &#8220;<a href="http://wiki.xbmc.org/index.php?title=Settings/Network" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://wiki.xbmc.org/index.php?title=Settings/Network']);">Settings/Network</a>&#8221; of the XMBC web site. From the site:

> **Share video and music libraries through UPnP**  
> Enables the UPnP server; this will enable you to steam media to any UPnP client.
> 
> **Allow control of XBMC via UPnP**  
> Enables the UPnP client; this will enable you to steam media from any UPnP server.

Lastly add a UPnP Share by going to Videos -> Files -> Add Videos -> Browse -> UPnP Devices. From there you will see your MediaTomb Server. It will look something like this:

<a href="http://virtuallyhyper.com/wp-content/uploads/2012/10/screenshot011.png" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://virtuallyhyper.com/wp-content/uploads/2012/10/screenshot011.png']);"><img src="http://virtuallyhyper.com/wp-content/uploads/2012/10/screenshot011.png" alt="screenshot011 Installing Mediatomb on FreeBSD 9 and Connecting to it with XBMC from a Fedora 17 OS" title="screenshot011" width="1280" height="800" class="alignnone size-full wp-image-4182" /></a>

After you have added your share, you can go back to Videos -> Files and your share will be there. There are actually many other UPnP clients out there, I just heard of XBMC before and decided to try it out and I actually really like it. Here is a <a href="http://www.makeuseof.com/tag/using-your-linux-computer-as-a-media-center-part-1/</full></performnud></rxcsum></up>&#8221;>link</a> that talks about other Media Centers.

<p class="wp-flattr-button">
  <a class="FlattrButton" style="display:none;" href="http://virtuallyhyper.com/2012/10/installing-mediatomb-on-freebsd-9-and-connecting-to-it-with-xbmc-from-a-fedora-17-os/" title=" Installing Mediatomb on FreeBSD 9 and Connecting to it with XBMC from a Fedora 17 OS" rev="flattr;uid:virtuallyhyper;language:en_GB;category:text;tags:dlna,mediatomb,upnp,blog;button:compact;">I recently got some new hardware and wanted to setup a video streaming server in my home. There are a lot of choices out there, for example check out the...</a>
</p>