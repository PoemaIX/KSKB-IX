# Poema IX

## Introduction
Poema IX is a non-profit distributed [Virtual IXP](https://bgp.tools/kb/virtual-ixp) in Taiwan. It serves to provide BGP hobbyists with a platform to learn and experiment with real life networking.  

This IX provides OSI Layer 2 Ethernet Switching service. For the switching network, we refer to `IX LAN`, `IX peering LAN` or `Peering LAN` in the following sections.  

This IX supports IPv4 (mpbgp + extended next hop) and IPv6  
PeeringDB: [https://www.peeringdb.com/ix/3792](https://www.peeringdb.com/ix/3792)  
IXPDB: [https://ixpdb.euro-ix.net/en/ixpdb/ixp/1061/](https://ixpdb.euro-ix.net/en/ixpdb/ixp/1061/)  

Meanwhile a number of new technologies are avilable, such as `IPv6 Link-local address`, `Multiprotocol BGP` and `Extended Next Hop(RFC 5549)` but only few networks are using it.  
If those technologies are avilable, why we don't use it? So, I have decided to use it at my IXP.  

## Join

1. Join via VM (If you don't have a Network PoP in Taiwan)
2. Join via tunnel (If you have a Network PoP in Taiwan already)

Each Point of Presence (PoP) has its own management rules. Please refer to the [PoP List](/PoP-List-en/)

* Commercial use is not allowed. For example, using Poema IX to exchange commercial traffic. Please contact us for more information.
* The IXP **DOES NOT** provides IP Transit itself, but there are some [sponsors](#RS2) who provide IP Transit. Otherwise you can ask IP transit from [IX members](../members) directly.
* We require you to maintain an active BGP connection with RS1 and **announce at least one IPv6 route from your own network**.
* KSKB will disable your VM when I feel my computer is laggy and if you do not establish a bgp session to `RS1` and announce routes.  

## Requirement
Participants must have a ASN and at least a public routable /48 ipv6 subnet  
Participants needs a Telegram account to join our announcement group to get IX related announcement
Participants should have some knowledge and experience related to networking and BGP, such as:

* Know the basics about L2 and L3
* Know what eBGP/iBGP/IGP is and its differences
* Have deployed more than two DN42 nodes and have provided cross-node ip transit services before. Or equivalent knowledge and experience (have provided similar services on other experimental networks/public networks)
* Have a humble learning heart. Be nice.

The bgp daemon used by the participant must support the following functions

* IPv6 link local
* BGP large community
* Multiprotocol BGP (IPv4)
* Extended nexthop (IPv4)

## Configure
We have three route servers, with 3 different policies  

**Only `RS Regular` is a regular Route Server**  
`RS Transitable` / `RS Chaos` are special Route Servers for experimenting.  

**If you don't know what this is, please establish sessions to `RS Regular` only**  

* RS1
    * AS199594
    * A regular route server
    * [Filtering Policy](\RS#default-filtering-policy)
    * [Communities](\RS#announcement-control-via-bgp-communities)
    * In a nutshell: **Config the RS session as peering session**
    * You can connect to this RS without any concern, we will do IRR and RPKI validation for you.
    * A BGP connection with `RS Regular 1` is mandatory and you must **announce at least one IPv6 route from your own network**.
    * IP Address(Link-local mode): `fe80::1980:1:1 % eth1`
    * IP Address(Regular mode): `2404:f4c0:f70e:1980::1:1`
* RS2<a name="RS2"></a>
    * AS199594
    * "Transitable" route server.
        * You can be a voluntary **transit sponsor**, and help us to transit our routes to upstreams such as STUIX or AS6939 without setting up BGP sessions for each downstream one by one.
        * Those who need transit can connect to `RS Transitable` to get IP transit.
        * Transit Sponsors can reject to transit a specific downstream by tagging routes with `(199594:0:AS number)`.
    * Client Roles: **Downstream** and **Transit Sponsor**
        * The default role is downstream, you can't announce full table to it.
        * Once you have been registered as a transit sponsor, then you can send full table to `RS Transitable`.
        * You have to transit routes to upstreams for downstreams.
    * It's experimental since peering routes and transit routes are mixed in the same BGP session. You have to split them with bgp_large_community.
        * Routes with `(199594:65530:7)` attributes are transit routes and should be treated as upstream routes. Transit Sponsors should reject these routes.
        * Similarly, when exporting external routes into `RS Transitable`, please tag them with `(199594:65530:7)` as reference for other Transit Sponsors.
        * Only transit sponsors can export the full table into `RS Transitable`. Please refer to the `Obligation of transit sponsors` below.
    * In a nutshell:
        * **Regular member: Set `RS Transitable` as an upstream**
        * **Transit sponsor: Set `RS Transitable` as an downstream**, reject all routes contains `(199594:65530:7)` and add `(199594:65530:7)` while exporting routes.
    * IP Address(Link-local mode): `fe80::1980:2:1 % eth1`
    * IP Address(Regular mode):  `2404:f4c0:f70e:1980::2:1`
    * Obligation of transit sponsors:
        * Do not transit any routes received from `RS Transitable` which contains `(199594:65530:7)` community.
        * Add community `(199594:65530:7)` before exporting routes to RS Transitable.
        * Add [AS-KSKB-IX-RS2](https://apps.db.ripe.net/db-web-ui/lookup?source=RIPE&type=as-set&key=AS-KSKB-IX-RS2) to your AS-SET. It contains RS2 connected downstreams only.
        * Transit Sponsors can refuse transit to specific users by tagging routes with `(199594:0:AS number)` and reject corresponding routes.
* RS Chaos
    * AS199594
    * Does not have any filter: `import all; export all`;
    * Only men of wisdom can connect, men of wisdom will do their own filtering.
    * In a nutshell: **Do whatever you want to do**
    * Does not support community attributes
    * Import limit: 2000, it will be disconnected if you exceed
    * IP Address(Link-local mode): `fe80::1980:3:1 % eth1`
    * IP Address(Regular mode): `2404:f4c0:f70e:1980::3:1`

## Members

See members list: [Members](\members)

## Limitations

### IX LAN
There are some security regulations for the IX peering LAN and the IX access port (usually eth1 for the IX VM).  

* Only permitted MAC addresses will be allowed
* arp-proxy and ndp-proxy must be disabled on the IX port. You can only respond the Neighbor Discovery packet that was sent to the Poema IX allocated `IX LAN` IP.
* L3 joining only. Bridging our port to other switches are not allowed.
* Participants must not announce ("leak") Poema IX peering LAN (IPv6: 2404:f4c0:f70e:1980::/64) to other networks to reduce the potential attack surface.
* Poema IX does NOT support trunk port or VLAN mapping to our switches.
* Ethertypes: All forwarded frames must have one of the following ether types:
    * 0x0800 - IPv4
    * 0x86dd - IPv6
* Link-local traffic: 
    * Only the following link-local traffic is allowed:
        * ICMPv6 Neighbor Solicitation / Advertisement
        * BGP session over link-local address in the IX peering LAN.
    * All other types of link-local traffic are prohibited, including but not limited to:
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
* Abuse on network infrastructure of IXP members is not allowed. Including but not limited to the following acts:
    *  Setting up default route or unauthorized routes to IXP member is prohibited.
        * a.k.a Any form of **static route to members** without authorization, is **strict prohibited**.
    *  Sending routes that has malformed nexthop attribute such as point to other member is prohibited.
    *  Sending ICMP redirects packets to redirect your traffic to other members is prohibited.
    *  BGP hijacking without the authorization of the resource owner is prohibited. Including but not limited to the following behaviors
        * Prefix hijacking
        * ASN origin spoofing
        * AS path manipulation

### IX VM
Regarding the IX VM provided by KSKB, as well as the IX peering LAN itself, IX members may only use it to exchange network traffic(internal traffic/peering). Other types of use are not allowed.
Extra enforcements apply, including but not limited to:  

* Complying with the laws of the Republic of China (Taiwan) and refrain from any action that may result in the confiscation of the computer.
* Any operation involving "money" is **strictly prohibited**[^1]. For example, Game points, card top-ups, registering third-party accounts or account opening/operation of financial-related webpages/programs.
* For personal use only; transfer, rental, and commercial uses are prohibited.
* Cyber attacks are not allowed, such as ARP attack, ARP hijacking, scaning weak passwords, malicious exhaustion, DDoS, Trojan horses and interfering with the operation of other networks and servers.
* Spaming emails, spaming messages, spreading Trojans, viruses (including referencing malicious files from other servers) are not allowed.
* Commiting copyright violations using Torrents, BitTorrent, etc. is not allowed.
* Use of the net_speeder/finalspeed/kcptun, etc, and any form of packet multiplication tools that may interfere our network.
* Fair use terms applied for all resources. It is forbidden to consume/occupy CPU/network/bandwidth and other resources for a long time, such as `rclone transferring`/`crypto mining`, or any action that makes me feel my network is very laggy.
* Using it as crawler or for account registration, etc. which may cause my IP to be marked as a bot is not allowed.
* You may not run resource-consuming programs, such as online games, crypto mining.

## Contact
* mailto: ix@kskb.eu.org

## Special Thanks
We want to acknowledge the following sponsors for their sponsored resources and support toward the Poema IX project.  

| List                                     | Acknowledgements     |
|------------------------------------------|----------------------|
| [TOHU NET](https://as140731.bairuo.net/) | <li>Thank to <ins>The BaiRuo</ins> for the IPv6 Transit to STUIX over GeekIX</li> |
| [MrSheepNET LTD](https://mrsheep.io/)    | <li>Thank to <ins>MrSheepNET LTD</ins> to be a transit sponsor</li><li>Transits our routes to STUIX.</li> |
| [MoeDove LLC](https://www.moedove.com/)  | <li>Thank to <ins>MoeDove LLC</ins> to be a transit sponsor</li><li>Transits our routes to STUIX.</li> |
| [Liu Haoran](https://www.pysio.online)   | <li>Thank to <ins>Liu Haoran</ins> to be a transit sponsor</li><li>Transits our routes to STUIX and PCCWG.</li> |
| [Muilties Network](https://muilties.com/)| <li>Thank to <ins>Muilties Network</ins> to be a transit sponsor</li><li>Transits our routes to STUIX.</li> |
| [MLGT](https://as204508.net/)            | <li>Thank to <ins>Gatterer Manuel</ins> for providing the German VM for better connectivity. |

[^1]: True story: Someone bought a swag gift card unintentionally, topped it up with a Taiwan VPN. Then the police found the top-uping IP is from a Taiwan IP. Then the computer has been seized and the server holder has been arrested by the police.
