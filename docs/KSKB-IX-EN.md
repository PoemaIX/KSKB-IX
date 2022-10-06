# KSKB-IX

## Introduction
It's a [Virtual IXP](https://bgp.tools/kb/virtual-ixp) located at my home, and it's hosting in a laptop at my home for a platform for BGP hobbyist to learn and expirenment networking.  
This IX provides OSI Layer 2 Ethernet Switching service. For the switching network, We call it `IX LAN`/`IX peering LAN` or `Peering LAN` in the following section.  

I host this virtal Internet eXchange point because I want to provide a platform to learn and expirenment the BGP for network engineering hobbyist enthusiast or novice.  
The idea behind the KSKB-IX project, is to enable regular end user to learn, use and play with IPv6, and BGP networking.  

This IX supports IPv4(mpbgp + extended next hop) and IPv6  
PeeringDB: [https://www.peeringdb.com/ix/3792](https://www.peeringdb.com/ix/3792)  
IXPDB: [https://ixpdb.euro-ix.net/en/ixpdb/ixp/1061/](https://ixpdb.euro-ix.net/en/ixpdb/ixp/1061/)  

Meanwhile, there is a lot of new technologies such as `IPv6 Link-local address`, `Multiprotocol BGP` and `Extended Next Hop(RFC 5549)` but there are no any IXP uses it.  
These technologies exists, but why we don't use it? So, I decide to use it ay my IXP.  

## Join

1. Join via LXC/VM
2. Join via tunnel - Taiwan only
    1. zerotier
    2. openvpn
3. Join via Wifi - Taipei only
    1. Short range
        1. 2.4GHz 802.11n
        2. 5GHz 802.11ac
    2. Long range
        1. The access point must be located within 3km of Liuzhangli MRT Station, and can be seen directly without any obstruction by buildings.
        2. I provide the top floor, you provide the construction fee of the directional wi-fi
4. Join via AX.25 radio (Considering, not implenment yet)

Commercial use is prohibited. For example using KSKB-IX to exchange commercial traffic

* Commercial use is prohibited. For example using KSKB-IX to exchange commercial traffic. Please contact us for more information 
* SLA < 99% guaranteed. ([My computer is very unstable](https://www.kskb.eu.org/2022/06/5.html), It may be a problem with the memory or the motherboard, and it will BSoD randomly) 
* If SLA >= 99%，You can get double SLA credit compensate, I will shutdown your server to fit the SLA. ~~Guaranteed less than is also a guarantee~~  
* IXP **DO NOT** provides IP Transit itself, but there are some [volunteer](#RS2) Provides IP Transit. You can ask IP transit from [IX members](../members).
* We mandatory a BGP connection with RS1 and **announce at least one IPv6 route from your internal network**.
* KSKB will shut your VM down when I feel my computer is laggy if you do not establish a bgp session to `RS1` and announce routes to release resource for KSKB surf the internet/play games smoothly.  

## Requirement
Participants must have a public network ASN and at least a /48 ipv6  
Participants should have some knowledge and experience related to the Network engineering, such as:

* Know how L2 and L3 work
* Know what eBGP/iBGP/iGP is and its differences
* Deployed more than two DN42 nodes and provided cross-node ip transit services before. Or equivalent knowledge and experience (provided similar services on other experimental networks/public networks)
* With a humble learning heart, Be nice.

The bgp daemon used by the participant must support the following functions

* IPv6 link local
* bgp large community
* multiprotocol BGP (IPv4)
* extended nexthop (IPv4)

## Configure
We have 3 Route Servers, with 3 different policies  

**Only RS1 is a regular Route Server**  
RS2 and RS3 are special RSs for experiment.  

Please first make the special settings according to the policy of RS before you connect to it.  
**If you have no time to read the rules, please connect to RS1 only**  

* RS1
    * AS114514
    * A regular route server
    * [Filtering Policy](..\RS#default-filtering-policy)
    * [Communities](..\RS#announcement-control-via-bgp-communities)
    * Nutshell: **Config the RS session as peering session**
    * You can connect to this RS without any concern, We will doing IRR and RPKI validation for you.
    * We mandatory a BGP connection with RS1 and **announce at least one IPv6 route from your internal network**.
    * IP Address(link-local mode): `fe80::114:514 % eth1`
    * IP Address(Regular mode): `2404:f4c0:f70e:1980::114:514`
* RS2<a name="RS2"></a>
    * AS114514
    * Transitable route server. This RS allow it's routes be transitted to external, and it allows transit external route to RS.
        * But you have to transit symmetrically. When you transit routes in, you have to transit our routes out.
        * You can be a voluntary **transit provider**, help us transit our routes to other place such as STUIX or he.net without setup BGP sessions for each downstream one by one.
    * Experimental RS, which mixes the peering route and the transit route in the same bgp session. Please sperate it by bgp_large_community at your filter.
        * All transit/upstream routes shall contains `(114514:65530:7)`, otherwise it's peering routes.
        * Please add `(114514:65530:7)` for all non-IX routes while sending to RS2.
        * Only **transit provider** can send external routes to RS2. Please register first.
    * Nutshell:
        * **Regular member: Set RS2 as an upstream**
        * **Transit provider: Set RS2 as an downstream**, reject all routes contains `(114514:65530:7)` and add `(114514:65530:7)` while exporting routes.
    * IP Address(link-local mode): `fe80::1145:14 % eth1`
    * IP Address(Regular mode):  `2404:f4c0:f70e:1980::1145:14`
    * Obligation of transit provider:
        * Do not transit any routes received from RS2 which contains `(114514:65530:7)` community.
        * Add community `(114514:65530:7)` before send external routes to RS2
        * Add [AS-KSKB-IX-RS2](https://apps.db.ripe.net/db-web-ui/lookup?source=RIPE&type=as-set&key=AS-KSKB-IX-RS2) to your AS-SET. It contains RS2 connected members who has <=100 routes in his AS-SET.
        * You have to transit routes symmetrically.
            * You have to transit our route out if you want to transit external route in.
            * If you want to exclude transit out for someone, you have to use [community](..\RS#announcement-control-via-bgp-communities) `Do not announce to peer` to avoid transit in to that person.
            * External route <--> you <--> RS2 route, routes must be transit symmetrically.
        * RS2 can helps you add `(114514:65530:7)` for your transit routes if you register your upstream ASN routes at my side.
        * You have to be registered as a transit provider to update the filter of RS2 to allow transit routes.
* RS3
    * AS114514
    * Do not have any filter: `import all; export all`;
    * Only man of wisdom can connect, man of wisdom will do their own filtering.
    * Nutshell: **Do whatever you want to do**
    * Do not support community attribute
    * Import limit: 2000, it will be disconnected if you exceeds
    * IP Address(link-local mode): `fe80::11:4514 % eth1`
    * IP Address(Regular mode): `2404:f4c0:f70e:1980::11:4514`

## Members

See member list: [Members](..\members)

## LXC/VM Connectivity
**IX VM Connectivity. Ignore this chapter if you are not joining via VM**

All **outbound** connections from the IX VM follows the following routing policies

Traffic        | Connection    | MTU  | Comment                                |
---------------|---------------|------|----------------------------------------|
IPv4           | wgcf          | 1432 | `dst port < 9999`                      |
IPv4           | hinet         | 1492 | `dst port >= 10000`，`icmp`            |
IPv6           | wg-wgcf-STUIX | 1372 | sponsered IP Transit `HE TPE at STUIX` |

#### Connection Service
1. port forward:
    * **\*\*\*=VMID**
    * :\[\*\*\*00~\*\*\*99\] → :\[\*\*\*00~\*\*\*99\], 100 exposed ports available for the tunnels for the building of your internal network
    * :10\*\*\* → :22, For ssh connection
    * The external IP is dynamic, please update your tunnel endpoint routinely. 

## Limitations

### IX LAN
There are some security regulations for the IX peering LAN and the IX access port (usually eth1 for the IX VM)  

* Only permitted MAC addresses will be allowed
* arp-proxy and ndp-proxy must disable on the IX port. You can only respond the Neighbor Discovery packet that send to the KSKB-IX allocated `IX LAN` IP.
* L3 joining only. Bridge out port to other switch are not allowed.
* Participants must not announce ("leak") KSKB-IX peering LAN (IPv6: 2404:f4c0:f70e:1980::/64) to other networks to redice the potential attack surface.
* KSKB-IX does NOT support trunk port or VLAN mapping to our switches.
* Ethertypes: All forwarded frames must have one of the following ether types:
    * 0x0800 - IPv4
    * 0x86dd - IPv6
* Link-local traffic: 
    * Only the following link-local traffic is allowed:
        * ICMPv6 Neighbor Solicitation / Advertisement
        * BGP session over link-local address in the IX peering LAN.
    * All other types of lick-local traffic are prohibited, including but not limited to:
        * IGP traffic (e.g. OSPF, ISIS, IGRP, EIGRP)
        * BOOTP/DHCP
        * ICMPv6 Router Advertisement
        * ICMP redirects
        * Discovery Protocol：CDP、EDP
        * VLAN/trunking protocols：VTP、DTP
* Unicast/Multicast/Broadcast:
    * Only unicast traffic is allowed.
    * Frames forwarded must not be addressed to a multicast or broadcast MAC destination address except as follows:
        * ICMPv6 Neighbor Solicitation / Advertisement
    * Multicast/broadcast packet traffic must not exceed 1kbps
* Abuse network infrastructure of IXP members is not allowed. Including but not limited to the following acts:
    *  Setup default route or unauthorized route to IXP member is prohibited
    *  Send routes that has malformed nexthop attribute such as point to other menber is prohibited.
    *  Send ICMP redirects packets to redirect your traffic to other members is prohibited.
    *  BGP hijacking without the authorization of the resource owner is prohibited. Including but not limited to the following behaviors
        * Prefix hijacking
        * ASN origin spoofing
        * AS path manipulation

### IX VM
For the IX VM provided by KSKB, as well as the IX peering LAN, IX members can use it for network traffic exchange. Other types of use are prohibited.  
Including but not limited to the following restrictions  

* Comply with the laws of the Republic of China (Taiwan) and prohibit any action that may cause my computer to be seized by the police
* Any operation involving "money" is **strictly prohibited**[^1]. For example, game point card top-up, register related account. Or account opening/operation of financial-related webpages/programs.
* Personal use only, transfer/rental/commercial use is prohibited
* Cyber attack is not allowed. Such as ARP attack, ARP hijacking, scan weak passwords, malicious exhaustion, DDoS, Trojan horses and interfere with the operation of other servers
* Spam email, spam messages, spread Trojan horses, viruses (including referencing malicious files from other servers) are not allowed
* Downloading or distributing copyrighted content using Torrents, BitTorrent, etc. is prohibited
* Prohibit the use the net_speeder/finalspeed/kcptun, etc, any form of multiple packet sending tools that may interfere my home network, or let my web browsing is very lag.
* Fair use terms applied for all resources. It is forbidden to consume/occupy CPU/network/bandwidth and other resources for a long time, such as `rclone transferring`/`crypto mining`, or any action that makes me feel my home network is very lag
* Personal use is allowed. But use it for crawler/account registration, etc., which may cause my IP to be marked as a bot is not allowed
* Must not hosting resource-consuming programs, such as online games, crypto mining. Or providing an open connection, such as a large file server.

## Contact
* mailto: ix@kskb.eu.org

## Special Thanks
We want to acknowledge the following sponsers for their sponsored resources and support toward the KSKB-IX project.  

| List                                     | Acknowledgements     |
|------------------------------------------|----------------------|
| [TOHU NET](https://as140731.bairuo.net/) | <li>Thank <ins>The BaiRuo</ins> for the IPv6 Transit to STUIX over GeekIX</li> |
| [Shizuku](https://as142553.zhiccc.net/)  | <li>Thank <ins>Shizuku</ins> for the IPv6 Transit to to STUIX over WGCF</li>   |
| [SteveYi](https://network.steveyi.net/)  | <li>Thank <ins>SteveYi</ins> for the IPv6 Transit to STUIX over <ins>SteveYi</ins>'s VM</li><li>Thank <ins>SteveYi</ins> for the Taipei VM which can reach both Hinet and STUIX with low latency to slove the `Hinet-STUIX` routing problem.</li> |
| [Gatterer Manuel](https://as204508.net/) | <li>Thank <ins>Gatterer Manuel</ins> for the germany VM for better connectivity. |

[^1]: True story: There are someone bought a swag gift card unintentionally, top-up it with a Taiwan VPN. Then the police found the top-uping IP is from a Taiwan IP. Then computer is be seized and the server holder are arrest by the police.