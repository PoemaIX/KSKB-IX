# Poema IX

## 介紹 | Introduction
一個非營利的分部式[虛擬 IX](Virtual-IXP) ，作為一個BGP實驗交流和學習的平台  

主要是給BGP配網的`業餘/有興趣/新手`一個練習的地方，可以放心實驗  
同時提供一個平台，給大家熟悉IX環境。畢竟DN42和公網環境差距不只是一點點  

提供OSI第二層之交換服務(Switching)，模式為Ethernet Switching。針對Switching內網，以下稱呼為 **IX LAN**

本IX支援IPv4(透過mpbgp+enh) 以及 IPv6  
PeeringDB: [https://www.peeringdb.com/ix/3792](https://www.peeringdb.com/ix/3792)  
IXPDB: [https://ixpdb.euro-ix.net/en/ixpdb/ixp/1061/](https://ixpdb.euro-ix.net/en/ixpdb/ixp/1061/)  

一般IX都不用 `link local`/`mpbgp`/`extended next hop`  
這些技術明明存在，為什麼不用呢?我很好奇!  
所以我就想自己成立一個 IX ，把這些都使用上    

## 接入 | Join

主要有以下接入方式:

1. VM接入
2. 隧道接入

每個 PoP 有不同的接入方式，請參考 [PoP List](/PoP-List/)

* 非商業性質。禁止商業使用，例如使用 Poema IX 交換商業流量。請聯系我們以獲取更多信息  
* IX本身不存在IP Transit。妳也可以在IX裡面自行尋找[其他參與者](members)索要IP Transit  
* 我們強制要求與`RS Regular 1`進行BGP連接  
* 若沒有和`RS Regular 1`進行BGP連接並**發送至少一條IPv6路由**，KSKB將在Google Chrome記憶體爆炸的時候，優先關閉妳的VM，供KSKB順順看網頁/玩遊戲  

## 要求 | Requirement

參與者要有一個公網ASN，以及至少一段 /48 的ipv6

參與者要有一點網路相關知識和經驗，比如

* 知道L2和L3運作的原理
* 知道eBGP/iBGP/iGP是什麼和其差異
* 曾經配過兩個以上 DN42 節點，並提供跨節點的ip transit服務。或是與之相等的知識經驗(曾在其他實驗網/公網提供類似服務)
* 抱著謙虛學習的心，Be nice.

參與者使用的bgp daemon必須支援以下功能

* IPv6 link local
* bgp large community
* multiprotocol BGP (IPv4)
* extended nexthop (IPv4)

## 設定 | Configure
我們有以下三台Route Server，分別有著不同的策略  

**只有 `RS Regular` 是正常的 Route server**  
`RS Transitable` / `RS Chaos` 都是特殊RS，有實驗/整活的性質，請先針對RS的情況，弄好配套的特殊設定才能接入喔  
**懶得設定的話，只要連 RS Regular 就好了**

* RS Regular 1
    * AS114514
    * 是一個普通RS
    * [過濾規則](RS#default-filtering-policy)
    * [支援的Community屬性](RS#announcement-control-via-bgp-communities)
    * 普通人也能連接，RS有做過濾
    * 懶人包: **把RS 當作 peer 對象來連線**
    * 我們強制要求與RS1進行BGP連接，並發送至少一條IPv6路由
    * 連線地址(link-local 模式): `fe80::114:514 % eth1`
    * 連線地址(普通模式): `2404:f4c0:f70e:1980::114:514`
* RS Transitable<a name="RS2"></a>
    * AS114514
    * Transitable route server. 這個 RS 的路由允許被 transit 到其他地方，同時也允許把其他地方的路由 transit 進來
        * 但兩者必須同時發生。把其他地方的路由 transit 進來的同時，必須把 RS 內路由 transit 到其他地方。必須要對秤
        * 初衷是想說任何成員都可以成為志願者，一次性幫忙把 IX 裡面全部成員的路由 transit 去別的地方，而不需要每個下游一一設定BGP session。比如 STUIX ，或是 HE 上游之類
    * 實驗性質，把 peering route 和 transit route 混在同一個 bgp session 裡面，透過 bgp_large_community 來區分
        * 有 `(114514:65530:7)` 屬性的就是 transit 路由，請當成上游路由處裡。沒有的就是 peering 路由，請當成 peering 路由處理
        * 同理，若將外部路由倒入 `RS Transitable` ，請將外部路由打上 `(114514:65530:7)`，供其他成員參考
        * 只有提供Transit志願者可以倒全表，請參考下面的「發全表條件」
    * 懶人包:
        * **一般成員: 請將 `RS Transitable` 設定成上游**
        * **提供Transit志願者: 請將 `RS Transitable` 設定成下游**，發送路由打上 `(114514:65530:7)`，並拒收帶有 `(114514:65530:7)` 的路由
    * 連線地址(link-local 模式): `fe80::1145:14 % eth1`
    * 連線地址(普通模式): `2404:f4c0:f70e:1980::1145:14`
    * 發全表條件:
        * 如果你想成為志願者，想幫忙 transit `RS Transitable` 的路由去 STUIX 的話，收路由就要過濾掉 (114514:65530:7)
        * 外面收到的表要打上 `(114514:65530:7)`才能發去 `RS Transitable` (可以在我這邊登記上游ASN，RS會幫忙自動打上)
        * 將 [AS-KSKB-IX-RS2](https://apps.db.ripe.net/db-web-ui/lookup?source=RIPE&type=as-set&key=AS-KSKB-IX-RS2) 加到自己的 AS-SET 裡面，裡面只有已和 `RS Transitable` 有連線的成員(並且 AS-SET 大小必須小於100條路由)，每小時同步一次
        * 若想排除部分成員的transit，則需要使用[Community屬性](RS#announcement-control-via-bgp-communities)裡面的`Do not announce to peer`，將之從發送對象之中排除
            * 意思是若你想法全表發給A，你就得同時把A的路由發給上游。不想幫某人發上游，就不要發給他全表。必須做到對稱
        * 弄好以後即可以申請開通炸全表filter
* RS Chaos
    * AS114514
    * 過濾規則: `import all; export all`;，也就是沒有過濾
    * 只有智慧之人才能連接，智慧之人都會自己做好過濾的
    * 懶人包: **大亂鬥，有DN42漏油都不奇怪**
    * 不支援任何Community屬性
    * 路由發送上限 2000，超過會被斷開連接
    * 連線地址(link-local 模式): `fe80::11:4514 % eth1`
    * 連線地址(普通模式): `2404:f4c0:f70e:1980::11:4514`

## 成員 | Members

See member list: [Members](members)


## 限制 | Limitations

### IX LAN
對於  IX LAN 內網，以及IX接入端口(IX VM 接入的話通常是eth1)，有以下安全規範

* 只有經過申請的來源mac位址可以被使用
* 端口必須關閉 arp-proxy 和 ndp-proxy ，只能針對由 Poema IX 分配的 **IX LAN** IP 的 Neighbor Discovery 封包做出回應
* 必須 L3 接入，不得將端口橋接到其他交換機上面
* Poema IX 服務交換網段(IPv6: 2404:f4c0:f70e:1980::/64)資訊不可被轉送至客戶所轄網內或者是互聯網上任何地方以免成為受攻擊目標
* Poema IX 不支援與成員交換或者對應/修改VLAN資訊
* Ethertypes: 轉發的所有幀必須具有以下以太網類型之一:
    * 0x0800 - IPv4
    * 0x86dd - IPv6
* Link-local 流量: 
    * 只允許以下的 link-local 流量:
        * ICMPv6 Neighbor Solicitation / Advertisement
        * IX 內部使用的 BGP session over link-local address
    * 其餘種類的 lick-local 流量皆禁止，包括但不限於:
        * IGP流量 (e.g. OSPF, ISIS, IGRP, EIGRP)
        * BOOTP/DHCP
        * ICMPv6 Router Advertisement
        * ICMP redirects
        * 發現協議：CDP、EDP
        * VLAN/中繼協議：VTP、DTP
* 單播/組播/廣播: 
    * 只允許單播流量
    * 不得發送到多播或廣播的 MAC 目標地址，以下情況除外：
        * ICMPv6 Neighbor Solicitation / Advertisement
    * 組播/廣播封包流量不得超過 1kbps
* 不得濫用 IXP 成員的網路基礎設施。包括但不限於以下行為:
    *  不得將 default route 或是未授權的路由，指向IXP成員
    *  不得發送 nexthop 不是自己，而是指向其他成員的路由
    *  不得發送 ICMP redirects 封包，將自己的封包重新導向至其他成員
    *  不得盜播路由，包括但不限於以下行為，除非有獲得當事人授權
        * 盜取他人的 prefix
        * 偽造他人的 ASN origin
        * 發送造假的 AS path

### IX VM
對於接入了 Poema IX 的 IX VM，以及IX LAN ，有以下安全規定

* 遵守中華民國（台灣）法律，禁止做出任何可能會讓電腦被扣押的舉動
* 尤其是任何涉及「錢」的操作，更是**嚴禁**[^1]。比如遊戲點卡儲值，註冊相關帳號。或是金融相關網頁/程式的帳戶開設/操作
* 僅供個人使用，禁止轉讓/租借/商業使用
* 禁止主機對外、對內發包（無論是否為主動行為）, ARP 攻擊, ARP 劫持, 掃描弱密碼, 惡意窮舉，DDoS，木馬和干擾其它伺服器運行
* 禁止發送垃圾郵件、垃圾訊息, 散播木馬、病毒(包括引用其它伺服器的惡意文件)
* 禁止使用 Torrents、BT，等下載或傳播版權內容
* 禁止使用 net_speeder/finalspeed/kcptun 等干擾網路運作的，任何形式的多倍發包工具
* 資源合理使用。禁止長時間消耗/佔滿CPU/網路頻寬等資源，例如rclone轉存/挖礦，**或是讓我感覺家裡網路很卡**(這是最主要的判斷方式)
* 禁止用於爬蟲/帳號註冊等，可能會使IP被標記為bot(俗稱IP被汙染)之行為
* 禁止架設耗資源的程式，如線上遊戲
* Poema IX 由多人運營，請遵守 [PoP 各自的規範](/PoP-List/)

## 聯絡方式 | Contact
* mailto: ix@kskb.eu.org

## 致謝 | Special Thanks
Poema IX 的正常運作，離不開下列群友的貢獻

| 名單                                      | 致謝 |
|------------------------------------------|----------|
| [TOHU NET](https://as140731.bairuo.net/) | <li>感謝<ins>白渃</ins>提供的 IPv6 Transit</li><li>走GeekIX去STUIX </li> |
| [雫](https://as142553.zhiccc.net/)       | <li>感謝<ins>雫</ins>提供的 IPv6 Transit</li><li>走wgcf去STUIX</li> |
| [小易](https://network.steveyi.net/)     | <li>感謝<ins>小易</ins>提供的VM，可以直達去STUIX</li><li>解決了 Hinet 和 STUIX 互連性問題</li> |
| [MLGT](https://as204508.net/)            | <li>感謝 <ins>Gatterer Manuel</ins> 提供的德國VM讓我做相關實驗. |


[^1]: 真實故事: 不法分子行騙以後，贓款 GASH 點數於網路上轉賣。有人貪便宜購買贓物 GASH 以後，掛著 Hinet 出口的VPN儲值。警方接獲報案以後循線追蹤，因此將VPN主人的電腦(查到儲值IP是VPN主人的IP)扣押進行後續調查。因此符合第一條的「可能會讓我家電腦被扣押的舉動」
