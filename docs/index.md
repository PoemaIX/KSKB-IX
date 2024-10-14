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

1. VM接入 (如果你沒有台灣 PoP)
2. 隧道接入 (如果你已經有台灣 PoP)

每個 PoP 有不同的接入方式，請參考 [PoP List](/PoP-List/)

* 非商業性質。禁止商業使用，例如使用 Poema IX 交換商業流量。請聯系我們以獲取更多信息  
* IX本身不存在IP Transit。妳也可以在IX裡面自行尋找[其他參與者](members)索要IP Transit  
* 我們強制要求與`RS Regular 1`進行BGP連接  
* 若沒有和`RS Regular 1`進行BGP連接並**發送至少一條IPv6路由**，長期不連接可能會被視為不活躍成員被清理  

## 要求 | Requirement

參與者要有一個公網ASN，以及至少一段 /48 的ipv6

參與者要有一點網路相關知識和經驗，比如

* 知道L2和L3運作的原理
* 知道eBGP/iBGP/IGP是什麼和其差異
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
    * AS199594
    * 是一個**普通RS**
    * [過濾規則](RS#default-filtering-policy)
    * [支援的Community屬性](RS#announcement-control-via-bgp-communities)
    * 普通人也能連接，RS有做過濾
    * 懶人包: **把RS 當作 peer 對象來連線**
    * 我們強制要求與 `RS Regular 1` 進行BGP連接，並發送至少一條IPv6路由
    * 連線地址(link-local 模式): `fe80::1980:1:1 % eth1`
    * 連線地址(普通模式): `2404:f4c0:f70e:1980::1:1`
* RS Transitable<a name="RS2"></a>
    * AS199594
    * Transitable route server.
        * 你可以成為 transit sponsor 幫忙把路由 transit 到 STUIX 的 HE 上游，而不需要每個下游一一設定BGP session
        * 你有 transit 需要的話，接上 `RS Transitable` 也能接到上游
        * 成為 transit sponsor 可以拒絕為特定使用者提供 transit ，只需要發路由時打上 `(199594:0:對方AS)` 即可
    * 接入有兩種身分: **Downstream** 和 **Transit Sponser**
        * 預設是 Downstream 身分接入，不能發全表
        * 成為 transit sponsor 需要登記，可以發全表
        * transit sponsor 幫忙把路由表帶到 STUIX 的上游
    * 實驗性質，因為 peering route 和 transit route 被混在同一個 bgp session 裡面了，所以要改透過 bgp_large_community 來區分
        * 有 `(199594:65530:7)` 屬性的就是 transit 路由， Transit Sponser 請拒收
        * 同理，若將外部路由倒入 `RS Transitable` ，請將外部路由打上 `(199594:65530:7)`，供其他成員參考
        * 只有提供Transit志願者可以倒全表，請參考下面的「發全表條件」
    * 懶人包:
        * **一般成員: 請將 `RS Transitable` 設定成上游，同時心中默念100遍「感謝 sponsor 幫我 transit」，即可獲得 transit**
        * **transit sponsor: 請將 `RS Transitable` 設定成下游**，發送路由打上 `(199594:65530:7)`，並拒收帶有 `(199594:65530:7)` 的路由
    * 連線地址(link-local 模式): `fe80::1980:2:1 % eth1`
    * 連線地址(普通模式): `2404:f4c0:f70e:1980::2:1`
    * 成為 transit sponsor 注意事項:
        * 首先，收路由請**務必，絕對**要過濾掉 `(199594:65530:7)` 的路由
        * 將 [AS-KSKB-IX-RS2](https://apps.db.ripe.net/db-web-ui/lookup?source=RIPE&type=as-set&key=AS-KSKB-IX-RS2) 加到自己的 AS-SET 裡面，裡面只有已和 `RS Transitable` 有 client 已經發送的(並且 AS-SET 大小必須小於100條路由)路由，每小時同步一次
        * 發全表進去要打上 `(199594:65530:7)` (其實RS會幫忙自動打上)
        * sponsor 可以拒絕為特定使用者提供 transit ，只需要發路由時打上 `(199594:0:對方AS)` 並拒絕對應的路由即可
* RS Chaos
    * AS199594
    * 過濾規則: `import all; export all`;，也就是沒有過濾
    * 只有智慧之人才能連接，智慧之人都會自己做好過濾的
    * 懶人包: **大亂鬥，有DN42漏油都不奇怪**
    * 不支援任何Community屬性
    * 路由發送上限 2000，超過會被斷開連接
    * 連線地址(link-local 模式): `fe80::1980:3:1 % eth1`
    * 連線地址(普通模式): `2404:f4c0:f70e:1980::3:1`

## 成員 | Members

See member list: [Members](members)


## 限制 | Limitations

### IX LAN
對於  IX LAN 內網，以及IX接入端口(IX VM 接入的話通常是eth1)，有以下規定

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
        * a.k.a 未經當事人授權， **static route 指向成員是嚴重違規行為**
    *  不得發送 nexthop 不是自己，而是指向其他成員的路由
    *  不得發送 ICMP redirects 封包，將自己的封包重新導向至其他成員
    *  不得盜播路由，包括但不限於以下行為，除非有獲得當事人授權
        * 盜取他人的 prefix
        * 偽造他人的 ASN origin
        * 發送造假的 AS path

### IX VM
對於接入了 Poema IX 的 IX VM，以及IX LAN ，有以下額外規定

* 禁止流量落地，僅供作為成員間內網流量交換使用
* 遵守中華民國（台灣）法律，禁止做出任何可能會讓電腦主機被扣押的舉動
* 尤其是任何涉及「錢」的操作，更是**嚴禁**[^1]。比如遊戲點卡儲值，註冊相關帳號。或是金融相關網頁/程式的帳戶開設/操作
* 僅供個人使用，禁止轉讓/租借/商業使用
* 禁止主機對外、對內發包（無論是否為主動行為）, ARP 攻擊, ARP 劫持, 掃描弱密碼, 惡意窮舉，DDoS，木馬和干擾其它伺服器運行
* 禁止發送垃圾郵件、垃圾訊息, 散播木馬、病毒(包括引用其它伺服器的惡意文件)
* 禁止使用 Torrents、BT，等下載或傳播版權內容
* 禁止使用 net_speeder/finalspeed/kcptun 等干擾網路運作的，任何形式的多倍發包工具
* 資源合理使用。禁止長時間消耗/佔滿 CPU/網路頻寬等資源，例如 rclone 轉存/挖礦，**或是讓我感覺家裡網路很卡**(這是最主要的判斷方式)
* 禁止用於爬蟲/帳號註冊等，可能會使 IP 被標記為 bot(俗稱 IP 被汙染)之行為
* 禁止架設耗資源的程式，如線上遊戲
* Poema IX 由多人運營，請遵守 [PoP 各自的規範](/PoP-List/)

## 聯絡方式 | Contact
* mailto: ix@kskb.eu.org

## 致謝 | Special Thanks
Poema IX 的正常運作，離不開下列群友的貢獻

| 名單                                      | 致謝 |
|------------------------------------------|----------|
| [TOHU NET](https://as140731.bairuo.net/) | <li>感謝<ins>白渃</ins>提供的 IPv6 Transit</li><li>走 GeekIX 去 TWDS </li> |
| [MrSheepNET LTD](https://mrsheep.io/)    | <li>感謝<ins>MrSheepNET LTD</ins>成為transit sponsor</li><li>幫助需要的人把路由 Transit 到 STUIX</li> |
| [Muilties Network](https://muilties.com/)| <li>感謝<ins>Muilties Network</ins>成為transit sponsor</li><li>幫助需要的人把路由 Transit 到 STUIX</li> |
| [MLGT](https://as204508.net/)            | <li>感謝 <ins>Gatterer Manuel</ins> 提供的德國 VM 讓我做相關實驗. |


[^1]: 真實故事: 不法分子行騙以後，贓款 GASH 點數於網路上轉賣。有人貪便宜購買贓物 GASH 以後，掛著 Hinet 出口的 VPN 儲值。警方接獲報案以後循線追蹤，因此將 VPN 主人的電腦(查到儲值 IP 是 VPN 主人的 IP)扣押進行後續調查。因此符合第一條的「可能會讓我家電腦被扣押的舉動」
