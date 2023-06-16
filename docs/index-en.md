# Poema IX

## Introduction
Poema IX is a non-profit distributed [Virtual IXP](https://bgp.tools/kb/virtual-ixp) located and running at my home in order to provide a platform to BGP hobbyist to learn and expirenment with real life networking.  

This IX provides OSI Layer 2 Ethernet Switching service. For the switching network, we refer to `IX LAN`, `IX peering LAN` or `Peering LAN` in the following sections.  

This IX supports IPv4(mpbgp + extended next hop) and IPv6  
PeeringDB: [https://www.peeringdb.com/ix/3792](https://www.peeringdb.com/ix/3792)  
IXPDB: [https://ixpdb.euro-ix.net/en/ixpdb/ixp/1061/](https://ixpdb.euro-ix.net/en/ixpdb/ixp/1061/)  

Meanwhile a number of new technologies are avilable, such as `IPv6 Link-local address`, `Multiprotocol BGP` and `Extended Next Hop(RFC 5549)` but only few networks are using it.  
If those technologies are avilable, but why we don't use it? So, I decide to use it at my IXP.  

## Join

1. Join via VM
2. Join via tunnel

Each Point of Presence (PoP) has own management rules. Please refer to the [PoP List](/PoP-List-en/)

* Commercial use is not allowed. For example using Poema IX to exchange commercial traffic. Please contact us for more information.
* The IXP **DOES NOT** provides IP Transit itself, but there are some [sponsers](#RS2) who provides IP Transit. Otherwise you can ask IP transit from [IX members](../members) directly.
* We require you to maintain an active BGP connection with RS1 and **announce at least one IPv6 route from your internal network**.
* KSKB will disable your VM when I feel my computer is laggy if you do not establish a bgp session to `RS1` and announce routes.  

## Requirement
Participants must have a ASN and at least a public routabe /48 ipv6 subent  
Participants should should have some knowledge and experience related to the networking and BGP, such as:

* Know the basics about L2 and L3
* Know what eBGP/iBGP/iGP is and its differences
* Deployed more than two DN42 nodes and provided cross-node ip transit services before. Or equivalent knowledge and experience (provided similar services on other experimental networks/public networks)
* With a humble learning heart, Be nice.

The bgp daemon used by the participant must support the following functions

* IPv6 link local
* bgp large community
* multiprotocol BGP (IPv4)
* extended nexthop (IPv4)

## Configure
We have three route servers, with 3 different policies  

**Only `RS Regular` is a regular Route Server**  
`RS Transitable` / `RS Chaos` are special Route Servers for experimenting.  

**If you don't known what this is, establish session to `RS Regular` only please**  

* RS1
    * AS199594
    * A regular route server
    * [Filtering Policy](\RS#default-filtering-policy)
    * [Communities](\RS#announcement-control-via-bgp-communities)
    * Nutshell: **Config the RS session as peering session**
    * You can connect to this RS without any concern, We do IRR and RPKI validation for you.
    * A BGP connection with `RS Regular 1` is mandatory and you must **announce at least one IPv6 route from your internal network**.
    * IP Address(link-local mode): `fe80::1980:1:1 % eth1`
    * IP Address(Regular mode): `2404:f4c0:f70e:1980::1:1`
* RS2<a name="RS2"></a>
    * AS199594
    * "Transitable" route server.
        * You can be a voluntary **transit sponsor**, help us to transit our routes to upstream such as STUIX or AS6939 without setup BGP sessions for each downstream one by one.
        * Those who need transit can connect to `RS Transitable` to get IP transit.
        * Transit Sponser can reject to transit for specific downstream by tagging routes with `(199594:0:AS number)`.
    * Client Roles: **Downstream** and **Transit Sponser**
        * The default role is downstream, you can't accounce full table into it.
        * You can registered as transit sponser, then you can send full table to `RS Transitable`.
        * You have to transit routes to upstream for downstreams.
    * It's experimental since peering routes and transit routes are mixed in the same BGP session. You have split them with bgp_large_community.
        * Routes with `(199594:65530:7)` attribute are transit routes and should be treated as upstream routes. Transit Sponser should reject these rotues.
        * Similarly, when exporting external routes into `RS Transitable`, please tag them with `(199594:65530:7)` for reference by other Transit Sponser.
        * Only transit sponser can export the full table into `RS Transitable`. Please refer to the `Obligation of transit sponsor` below.
    * Nutshell:
        * **Regular member: Set `RS Transitable` as an upstream**
        * **Transit sponsor: Set `RS Transitable` as an downstream**, reject all routes contains `(199594:65530:7)` and add `(199594:65530:7)` while exporting routes.
    * IP Address(link-local mode): `fe80::1980:2:1 % eth1`
    * IP Address(Regular mode):  `2404:f4c0:f70e:1980::2:1`
    * Obligation of transit sponsor:
        * Do not transit any routes received from `RS Transitable` which contains `(199594:65530:7)` community.
        * Add community `(199594:65530:7)` before exporting routes to RS Transitable
        * Add [AS-KSKB-IX-RS2](https://apps.db.ripe.net/db-web-ui/lookup?source=RIPE&type=as-set&key=AS-KSKB-IX-RS2) to your AS-SET. It contains RS2 connected downstream only.
        * Transit Sponser can refuse transit to specific users by tagging routes with `(199594:0:AS number)` and reject corresponding routes.
* RS Chaos
    * AS199594
    * Do not have any filter: `import all; export all`;
    * Only man of wisdom can connect, man of wisdom will do their own filtering.
    * Nutshell: **Do whatever you want to do**
    * Do not support community attribute
    * Import limit: 2000, it will be disconnected if you exceeds
    * IP Address(link-local mode): `fe80::1980:3:1 % eth1`
    * IP Address(Regular mode): `2404:f4c0:f70e:1980::3:1`

## Members

See member list: [Members](\members)

## Limitations

### IX LAN
There are some security regulations for the IX peering LAN and the IX access port (usually eth1 for the IX VM).  

* Only permitted MAC addresses will be allowed
* arp-proxy and ndp-proxy must disable on the IX port. You can only respond the Neighbor Discovery packet that send to the Poema IX allocated `IX LAN` IP.
* L3 joining only. Bridge out port to other switch are not allowed.
* Participants must not announce ("leak") Poema IX peering LAN (IPv6: 2404:f4c0:f70e:1980::/64) to other networks to redice the potential attack surface.
* Poema IX does NOT support trunk port or VLAN mapping to our switches.
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
Regarding the IX VM provided by KSKB, as well as the IX peering LAN itself, IX members may only use it to exchange network traffic(internal traffic/peering). Other types of use are not allowed.
Including but not limited to the following:  

* Comply with the laws of the Republic of China (Taiwan) and refrain from any action that may result in the confiscation of the computer
* Any operation involving "money" is **strictly prohibited**[^1]. For example, Game point, card top-up, registering third-party account or account opening/operation of financial-related webpages/programs.
* For personal use only; transfer, rental, and commercial use are prohibited
* Cyber attack are not allowed, such as ARP attack, ARP hijacking, scan weak passwords, malicious exhaustion, DDoS, Trojan horses and interfere with the operation of other networks and servers
* Spam email, spam messages, spread Trojans, viruses (including referencing malicious files from other servers) are not allowed
* Commiting copyright violations using Torrents, BitTorrent, etc. is not allowed.
* Use of the net_speeder/finalspeed/kcptun, etc, and any form of packet multiplication tools that may interfere our network
* Fair use terms applied for all resources. It is forbidden to consume/occupy CPU/network/bandwidth and other resources for a long time, such as `rclone transferring`/`crypto mining`, or any action that makes me feel my network is very lag
* Using it as crawler or for account registration, etc. which may cause my IP to be marked as a bot is not allowed
* You may not run resource-consuming programs, such as online games, crypto mining. 

## Contact
* mailto: ix@kskb.eu.org

## Special Thanks
We want to acknowledge the following sponsers for their sponsored resources and support toward the Poema IX project.  

| List                                     | Acknowledgements     |
|------------------------------------------|----------------------|
| [TOHU NET](https://as140731.bairuo.net/) | <li>Thank to <ins>The BaiRuo</ins> for the IPv6 Transit to STUIX over GeekIX</li> |
| [MrSheepNET LTD](https://mrsheep.io/)    | <li>Thank to <ins>MrSheepNET LTD</ins> to be a transit sponsor</li><li>Transit our routes to STUIX.</li> |
| [Muilties Network](https://muilties.com/)| <li>Thank to <ins>Muilties Network</ins> to be a transit sponsor</li><li>Transit our routes to STUIX.</li> |
| [SteveYi](https://network.steveyi.net/)  | <li>Thank to <ins>SteveYi</ins> for the VM, let me setup testing the EVPN-VXLAN setup.</li> |
| [MLGT](https://as204508.net/)            | <li>Thank to <ins>Gatterer Manuel</ins> for providing the Germany VM for better connectivity. |

[^1]: True story: Someone bought a swag gift card unintentionally, top-up it with a Taiwan VPN. Then the police found the top-uping IP is from a Taiwan IP. Then the computer has been seized and the server holder has been arrest by the police.