Configuration of Poema IX Route Server at AS199594
======================================================================

BGP sessions default configuration
----------------------------------

* **Passive** sessions are configured toward neighbors.
* GTSM (Generalized TTL Security Mechanism - [RFC5082](//tools.ietf.org/html/rfc5082)) is **disabled** on sessions toward the neighbors.
* **ADD-PATH** capability ([RFC7911](//tools.ietf.org/html/rfc7911)) is negotiaded by default; the route server is configured as "able to send multiple paths to its peer".

Route server general behaviours
-------------------------------

* Route server ASN is **not prepended** to the AS_PATH of routes announced to clients ([RFC7947 section 2.2.2.1](https://tools.ietf.org/html/rfc7947#section-2.2.2.1)).
* Route server **does  implement** path-hiding mitigation techniques ([RFC7947 section 2.3.1](https://tools.ietf.org/html/rfc7947#section-2.3.1)).


Default filtering policy
------------------------

### NEXT_HOP attribute

* The route server verifies that the NEXT_HOP attribute of routes received from a client matches the **IP address of the client itself**.

### AS_PATH attribute

* Routes whose **AS_PATH is longer than 32** ASNs are rejected.
* The **left-most ASN** in the AS_PATH of any route announced to the route server must be the ASN of the announcing client.
* Routes whose AS_PATH contains [**private or invalid ASNs**](http://mailman.nanog.org/pipermail/nanog/2016-June/086078.html) are rejected.
* Routes with an AS_PATH containing one or more of the following **"transit-free" networks**' ASNs
are **rejected**.

  List of "transit-free" networks' ASNs:
[701](https://stat.ripe.net/AS701), [1239](https://stat.ripe.net/AS1239), [1299](https://stat.ripe.net/AS1299), [2914](https://stat.ripe.net/AS2914), [3257](https://stat.ripe.net/AS3257), [3320](https://stat.ripe.net/AS3320), [3356](https://stat.ripe.net/AS3356), [3491](https://stat.ripe.net/AS3491), [5511](https://stat.ripe.net/AS5511), [6453](https://stat.ripe.net/AS6453), [6461](https://stat.ripe.net/AS6461), [6762](https://stat.ripe.net/AS6762), [6830](https://stat.ripe.net/AS6830), [7018](https://stat.ripe.net/AS7018), [12956](https://stat.ripe.net/AS12956), [174](https://stat.ripe.net/AS174), [1273](https://stat.ripe.net/AS1273), [2828](https://stat.ripe.net/AS2828), [4134](https://stat.ripe.net/AS4134), [4809](https://stat.ripe.net/AS4809), [4637](https://stat.ripe.net/AS4637), [6939](https://stat.ripe.net/AS6939), [7473](https://stat.ripe.net/AS7473), [7922](https://stat.ripe.net/AS7922), [9002](https://stat.ripe.net/AS9002)
* Routes with an AS_PATH containing one or more **"never via route-servers" networks**' ASNs are **rejected**.

### IRRDBs prefix/origin ASN enforcement

* Origin ASN validity is **enforced**. Routes whose origin ASN is not authorized by the client's AS-SET are rejected.
* Announced prefixes validity is **enforced**. Routes whose prefix is not part of the client's AS-SET are rejected.
  Longer prefixes that are covered by one entry of the resulting route set are accepted.
* Route **validity state** is signalled to route server clients using the following **BGP communities**:


| Validity state | Standard | Extended | Large |
| --- | --- | --- | --- |
| Prefix is included in client's AS-SET | 65530:5 | None | 199594:65530:5 |
| Prefix is NOT included in client's AS-SET | 65530:6 | None | 199594:65530:6 |
| Origin ASN is included in client's AS-SET | 65530:1 | None | 199594:65530:1 |
| Origin ASN is NOT included in client's AS-SET | 65530:0 | None | 199594:65530:0 |
| AS-PATH contains transit providers | 65530:7 | None | 199594:65530:7 |
| Prefix matched by a RPKI ROA for the authorized origin ASN | 65530:2 | None | 199594:65530:2 |
| Prefix matched by an entry of the ARIN Whois DB dump | 65530:4 | None | 199594:65530:4 |
| Route authorized soley because of a client white list entry | 65530:3 | None | 199594:65530:3 |

### RPKI BGP Prefix Origin Validation


* [RPKI BGP Origin Validation](https://tools.ietf.org/html/rfc6483) of routes received by the route server is **enabled**.
* When an INVALID route is received by the route server, **it is rejected**.



### RPKI ROAs


* RPKI ROAs are fetched from the RIPE RPKI Validator format cache files at <a href="https://console.rpki-client.org/vrps.json" rel="noopener">https://console.rpki-client.org/vrps.json</a>, <a href="https://rpki.gin.ntt.net/api/export.json" rel="noopener">https://rpki.gin.ntt.net/api/export.json</a>, <a href="https://rpki-validator.ripe.net/api/export.json" rel="noopener">https://rpki-validator.ripe.net/api/export.json</a>. The following Trust Anchors are used: APNIC RPKI Root, AfriNIC RPKI Root, LACNIC RPKI Root, RIPE NCC RPKI Root, apnic, afrinic, lacnic, ripe

### Max-pref limit


* A **max-prefix limit** is enforced; when it triggers,  new routes from the announcing client are **discarded**.
* If no more specific limits exist for the client, the **general limit** of 170000 IPv4 routes and 12000 IPv6 routes is enforced.


### Min/max prefix length


* Only prefixes whose length is in the following range are accepted by the route server:
    * IPv4: 8-24
    * IPv6: 12-48


### Rejected prefixes


* The following prefixes are **unconditionally rejected**:

| Prefix | More specific | Comment |
| --- | --- | --- |
| fd00::/8 | any more specific prefix | DN42 Network |

* **Bogon prefixes** are rejected too.

| Prefix | More specific | Comment |
| --- | --- | --- |
| 0.0.0.0/0 | only the exact prefix | Default route |
| 0.0.0.0/8 | any more specific prefix | IANA - Local Identification |
| 10.0.0.0/8 | any more specific prefix | RFC 1918 - Private Use |
| 127.0.0.0/8 | any more specific prefix | IANA - Loopback |
| 169.254.0.0/16 | any more specific prefix | RFC 3927 - Link Local |
| 172.16.0.0/12 | any more specific prefix | RFC 1918 - Private Use |
| 192.0.2.0/24 | any more specific prefix | RFC 5737 - TEST-NET-1 |
| 192.88.99.0/24 | any more specific prefix | RFC 3068 - 6to4 prefix |
| 192.168.0.0/16 | any more specific prefix | RFC 1918 - Private Use |
| 198.18.0.0/15 | any more specific prefix | RFC 2544 - Network Interconnect Device Benchmark Testing |
| 198.51.100.0/24 | any more specific prefix | RFC 5737 - TEST-NET-2 |
| 203.0.113.0/24 | any more specific prefix | RFC 5737 - TEST-NET-3 |
| 224.0.0.0/3 | any more specific prefix | RFC 5771 - Multcast (formerly Class D) |
| 100.64.0.0/10 | any more specific prefix | RFC 6598 - Shared Address Space |
| ::/0 | only the exact prefix | Default route |
| ::/8 | any more specific prefix | loopback, unspecified, v4-mapped |
| 64:ff9b::/96 | any more specific prefix | RFC 6052 - IPv4-IPv6 Translation |
| 100::/8 | any more specific prefix | RFC 6666 - reserved for Discard-Only Address Block |
| 200::/7 | any more specific prefix | RFC 4048 - Reserved by IETF |
| 400::/6 | any more specific prefix | RFC 4291 - Reserved by IETF |
| 800::/5 | any more specific prefix | RFC 4291 - Reserved by IETF |
| 1000::/4 | any more specific prefix | RFC 4291 - Reserved by IETF |
| 2001::/33 | any more specific prefix | RFC 4380 - Teredo prefix |
| 2001:0:8000::/33 | any more specific prefix | RFC 4380 - Teredo prefix |
| 2001:2::/48 | any more specific prefix | RFC 5180 - Benchmarking |
| 2001:3::/32 | any more specific prefix | RFC 7450 - Automatic Multicast Tunneling |
| 2001:10::/28 | any more specific prefix | RFC 4843 - Deprecated ORCHID |
| 2001:20::/28 | any more specific prefix | RFC 7343 - ORCHIDv2 |
| 2001:db8::/32 | any more specific prefix | RFC 3849 - NON-ROUTABLE range to be used for documentation purpose |
| 2002::/16 | any more specific prefix | RFC 3068 - 6to4 prefix |
| 3ffe::/16 | any more specific prefix | RFC 5156 - used for the 6bone but was returned |
| 4000::/3 | any more specific prefix | RFC 4291 - Reserved by IETF |
| 5f00::/8 | any more specific prefix | RFC 5156 - used for the 6bone but was returned |
| 6000::/3 | any more specific prefix | RFC 4291 - Reserved by IETF |
| 8000::/3 | any more specific prefix | RFC 4291 - Reserved by IETF |
| a000::/3 | any more specific prefix | RFC 4291 - Reserved by IETF |
| c000::/3 | any more specific prefix | RFC 4291 - Reserved by IETF |
| e000::/4 | any more specific prefix | RFC 4291 - Reserved by IETF |
| f000::/5 | any more specific prefix | RFC 4291 - Reserved by IETF |
| f800::/6 | any more specific prefix | RFC 4291 - Reserved by IETF |
| fc00::/7 | any more specific prefix | RFC 4193 - Unique Local Unicast |
| fe80::/10 | any more specific prefix | RFC 4291 - Link Local Unicast |
| fec0::/10 | any more specific prefix | RFC 4291 - Reserved by IETF |
| ff00::/8 | any more specific prefix | RFC 4291 - Multicast |


* IPv6 prefixes are accepted only if part of the IPv6 Global Unicast space 2000::/3.


Blackhole filtering
-------------------


* Blackhole filtering of more specific IP prefixes can be requested by tagging them with the following **BGP communities**: 65534:0, 199594:666:0,  65535:666 ([BLACKHOLE](https://tools.ietf.org/html/rfc7999#section-5) well-known community)

* By default, routes are **propagated** to all the clients unless they have been explicitly configured to not receive them.
* IPv4 routes are propagated **unchanged** to clients.
* IPv6 routes are propagated **unchanged** to clients.
* Before being announced to clients, all the routes are tagged with the BLACKHOLE well-known community.
  The NO_EXPORT well-known community is also added.
* Blackhole filtering requests bypass any RPKI validation check and min/max length check.

Graceful BGP session shutdown
-----------------------------


* Routes tagged with the **GRACEFUL_SHUTDOWN** BGP community (65535:0) have their LOCAL_PREF attribute lowered to 0.


Announcement control via BGP communities
----------------------------------------


* Routes tagged with the **NO_EXPORT** or **NO_ADVERTISE** communities received by the route server are propagated to other clients with those communities unaltered.



| Function | Standard | Extended | Large |
| --- | --- | --- | --- |
| Do not announce to any client | None | rt:0:199594 | 199594:0:199594 |
| Announce to peer, even if tagged with the previous community | None | None | 199594:199594:peer_as |
| Do not announce to peer | 0:peer_as | rt:0:peer_as | 199594:0:peer_as |
| Prepend the announcing ASN once to peer | 65504:peer_as | rt:65504:peer_as | 199594:65504:peer_as |
| Prepend the announcing ASN twice to peer | 65505:peer_as | rt:65505:peer_as | 199594:65505:peer_as |
| Prepend the announcing ASN thrice to peer | 65506:peer_as | rt:65506:peer_as | 199594:65506:peer_as |
| Prepend the announcing ASN once to any | None | rt:65501:199594 | 199594:65501:199594 |
| Prepend the announcing ASN twice to any | None | rt:65502:199594 | 199594:65502:199594 |
| Prepend the announcing ASN thrice to any | None | rt:65503:199594 | 199594:65503:199594 |
| Add NO_EXPORT to any | None | rt:65507:199594 | 199594:65507:199594 |
| Add NO_ADVERTISE to any | None | rt:65508:199594 | 199594:65508:199594 |
| Add NO_EXPORT to peer | 65509:peer_as | rt:65509:peer_as | 199594:65509:peer_as |
| Add NO_ADVERTISE to peer | 65510:peer_as | rt:65510:peer_as | 199594:65510:peer_as |


Reject reasons
--------------

* The following values are used to identify the reason for which routes are rejected. This is mostly used for troubleshooting, internal reporting purposes, looking glasses or in the route server log files.

* Routes which are rejected are tagged with the BGP community that represents the reason for which they were discarded.

| ID | Reason | Standard | Extended | Large |
| --- | --- | --- | --- | --- |
| 0 | Generic code: the route must be treated as rejected | 65531:0 | None | 199594:65531:0 |
| 1 | Invalid AS_PATH length | 65531:1 | None | 199594:65531:1 |
| 2 | Prefix is bogon | 65531:2 | None | 199594:65531:2 |
| 3 | Prefix is in global blacklist | 65531:3 | None | 199594:65531:3 |
| 4 | Invalid AFI | 65531:4 | None | 199594:65531:4 |
| 5 | Invalid NEXT_HOP | 65531:5 | None | 199594:65531:5 |
| 6 | Invalid left-most ASN | 65531:6 | None | 199594:65531:6 |
| 7 | Invalid ASN in AS_PATH | 65531:7 | None | 199594:65531:7 |
| 8 | Transit-free ASN in AS_PATH | 65531:8 | None | 199594:65531:8 |
| 9 | Origin ASN not in IRRDB AS-SETs | 65531:9 | None | 199594:65531:9 |
| 10 | IPv6 prefix not in global unicast space | 65531:10 | None | 199594:65531:10 |
| 11 | Prefix is in client blacklist | 65531:11 | None | 199594:65531:11 |
| 12 | Prefix not in IRRDB AS-SETs | 65531:12 | None | 199594:65531:12 |
| 13 | Invalid prefix length | 65531:13 | None | 199594:65531:13 |
| 14 | RPKI INVALID route | 65531:14 | None | 199594:65531:14 |
| 15 | Never via route-servers ASN in AS_PATH | 65531:15 | None | 199594:65531:15 |
| 65535 | Unknown | 65531:65535 | None | 199594:65531:65535 |



