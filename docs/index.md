# KSKB-IX

## 介紹 | Introduction
一個搭在我家客廳雲，非營利玩具類型的[虛擬 IX](https://bgp.tools/kb/virtual-ixp) ，作為一個BGP實驗交流和學習的平台  
提供OSI第二層之交換服務(Switching)，模式為Ethernet Switching。針對Switching內網，以下稱呼為 **IX LAN**

主要是給BGP配網的``業餘/有興趣/新手``玩家一個練習的地方，可以放心炸網  
還有提供一個平台，給大家熟悉IX環境。畢竟DN42和公網環境差距不只是一點點  

本IX支援IPv4(透過mpbgp+enh) 以及 IPv6  
PeeringDB: [https://www.peeringdb.com/ix/3792](https://www.peeringdb.com/ix/3792)  

一般IX都不用 `link local`/`mpbgp`/`extended next hop`  
這些技術明明存在，為什麼不用呢?我很好奇!  
所以我就想來用看看了  

普通的IX也不能炸全表  
同時我也想順便開一個可以炸全表的IX  
用比較科學的方式啦  

## 接入 | Join

接入方式:

1. VM接入
2. Wifi接入 - Taipei
    1. 短距離接入，僅限鄰居
        1. 2.4GHz 802.11n
        2. 5GHz 802.11ac
    2. 長距離接入
        1. 接入點必須位於六張犁捷運站 3km 內，並且可直接目視，無建物遮擋
        2. 我提供頂樓，指向式wifi建設成本由妳提供
3. AX.25 無線電接入(正在想要不要搞)

只是個玩具IX啦，不要拿營業用ASN來加喔

* 非商業，也禁止商業使用。例如使用 KSKB-IX 交換商業流量，請聯系我們以獲取更多信息  
* SLA保證低於99% ([我家電腦很不穩](https://www.kskb.eu.org/2022/06/5.html)，可能是記憶體或是主機板有問題，會自己不定時BSoD)  
* SLA若超過99%，工單反應以後可獲得雙倍 SLA credit 補償，人工幫你關機降低 SLA 。~~保證低於也算是一種保證~~  
* IX本身不存在IP Transit，但是現在有[志願者](#RS2)於RS2提供IP Transit。妳也可以在IX裡面自行尋找[其他參與者](members)索要IP Transit  
* 我們強制要求與`RS1`進行BGP連接  
* 若沒有和`RS1`進行BGP連接並**發送至少一條IPv6路由**，KSKB將在Google Chrome記憶體爆炸的時候，優先關閉妳的VM，供KSKB順順看網頁/玩遊戲  

## 要求 | Requirement
參與者要有一個公網ASN，以及至少一段 /48 的ipv6

參與者要有一點網路相關知識和經驗，比如

* 知道L2和L3運作的原理
* 知道eBGP/iBGP/iGP是什麼和其差異
* 曾經配過兩個以上 DN42 節點，並提供跨節點的ip transit服務
* 或是與之相等的知識經驗(曾在其他實驗網/公網提供類似服務)
* 抱著謙虛學習的心，Be nice.

參與者使用的bgp daemon必須支援以下功能

* IPv6 link local
* bgp large community
* multiprotocol BGP (IPv4)
* extended nexthop (IPv4)

## 設定 | Configure
我們有以下三台Route Server，分別有著不同的策略  

**只有 RS1 是正常的 Route server**  
RS2 RS3 都是特殊RS，有實驗/整活的性質，請先針對RS的情況，弄好配套的特殊設定才能接入喔  
**懶得設定的話，只要連 RS1 就好了**

* RS1
    * AS114514
    * 初衷是一個普通RS
    * [過濾規則](RS#default-filtering-policy)
    * [支援的Community屬性](RS#announcement-control-via-bgp-communities)
    * 普通人也能連接，RS有做過濾
    * 懶人包: **把RS 當作 peer 對象來連線**
    * 我們強制要求與RS1進行BGP連接，並發送至少一條IPv6路由
    * 連線地址(link-local 模式): `fe80::114:514 % eth1`
    * 連線地址(普通模式): `2404:f4c0:f70e:1980::114:514`
* RS2<a name="RS2"></a>
    * AS114514
    * 規則和RS1一樣，但是可以炸全表(需手動開通炸全表filter)
        * 初衷是想說任何成員都可以成為志願者，一次性幫忙把 IX 裡面全部成員的路由 transit 去別的地方，而不需要每個下游一一設定BGP session
        * 比如 STUIX ，或是 HE 上游之類
    * 懶人包:
        * **一般成員: 請將 RS2 設定成上游**
        * **提供Transit志願者: 請將 RS2 設定成下游**，發送路由打上 `(114514:65530:7)`，並拒收帶有 `(114514:65530:7)` 的路由
    * 連線地址(link-local 模式): `fe80::1145:14 % eth1`
    * 連線地址(普通模式): `2404:f4c0:f70e:1980::1145:14`
    * 發全表條件:
        * 如果你想成為志願者，想幫忙 transit RS2 的路由去 STUIX 的話，收路由就要過濾掉 (114514:65530:7)
        * 然後STUIX收到的表要打上 (114514:65530:7)才能發去RS2
        * 將 [AS-KSKB-IX-RS2](https://apps.db.ripe.net/db-web-ui/lookup?source=RIPE&type=as-set&key=AS-KSKB-IX-RS2) 加到自己的 AS-SET 裡面，裡面只有已和RS2有連線的成員，每小時同步一次
        * 若想排除部分成員的transit，則需要使用[Community屬性](RS#announcement-control-via-bgp-communities)裡面的`Do not announce to peer`，一併從全表發送對象之中排除
            * 意思是若你想法全表發給A，你就得同時把A的路由發給上游。不想幫某人發上游，就不要發給他全表。路由上盡量做到對稱
        * 上游來，發往 RS2 的路由需打上 (114514:65530:7) 屬性(可以在我這邊登記上游ASN，RS會幫忙自動打上)
        * 弄好以後即可以申請開通炸全表filter
* RS3
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

## VM 連線 | VM Connectivity
**IX VM 的網路連線能力。wifi 或是其他方式接入可以無視本章節**

Traffic        | Connection    | MTU  | Comment                                |
---------------|---------------|------|----------------------------------------|
IPv4           | wgcf          | 1432 | `9999 以下的port`                      |
IPv4           | hinet         | 1492 | `10000 以上的port`，`icmp`             |
IPv6           | wg-wgcf-STUIX | 1372 | 群友提供的 IP Transit`HE TPE at STUIX`  |
L2 to STUIX VM | yi-(your vm)  | 1432 | 走小易VM中轉(暫時不可用)                 |

#### 連線服務 | Connection Service
1. port forward服務:
    * **\*\*\*=VMID**
    * :\[\*\*\*00~\*\*\*99\] → :\[\*\*\*00~\*\*\*99\]，共計100個port供內網隧道搭建使用  
    * :10\*\*\* → :22 ，供ssh連線使用
    * port forward入口，由於是浮動IP，wg隧道務必加上crontab更新endpoint
2. Dst IP白名單服務(已棄用): 
    * 名單內的IP不論方向，均直接走hinet出去，供內網隧道搭建使用  
3. STUIX 隧道服務(暫時不可用): 
    * 因為`中華電信<->STUIX`繞美，家裡剛好也是中華電信。但是不少成員同時有購買 STUIX IXVM
    * 因此我想說幫忙轉發一下，提供 L2 隧道直接通向成員的 STUIX IX VM。  
    * 注意，**並非**接入 STUIX LAN 喔，不能和 HE peer。您還是必須先購買 STUIX IX VM 才能使用喔  
    * 使用方法:
        1. 您 `STUIX 的 IX VM` 和我這邊的`小易VM`建立gretap/隧道  
        2. 我幫您 `KSKB-IX 的 IX VM` 新增虛擬網卡  
    * 由我維護網卡兩邊的隧道。兩邊端口 L2 互通  
    * MTU 1432  

## 限制 | Limitations

### IX LAN
對於  IX LAN 內網，以及IX接入端口(IX VM 接入的話通常是eth1)，有以下安全規範

* 只有經過申請的來源mac位址可以被使用
* 端口必須關閉 arp-proxy 和 ndp-proxy ，只能針對由 KSKB-IX 分配的 **IX LAN** IP 的 Neighbor Discovery 封包做出回應
* 必須 L3 接入，不得將端口橋接到其他交換機上面
* KSKB-IX 服務交換網段(IPv6: 2404:f4c0:f70e:1980::/64)資訊不可被轉送至客戶所轄網內或者是互聯網上任何地方以免成為受攻擊目標
* KSKB-IX 不支援與成員交換或者對應/修改VLAN資訊
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
* 單播/組播/廣播: 只允許單播流量，不得發送到多播或廣播的 MAC 目標地址
    * 以下情況除外：
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
對於KSKB提供的IX VM，以及IX LAN，僅供IX成員作為網路流量交換使用。禁止其他類型使用  
包括但不限於以下限制

* 遵守中華民國（台灣）法律，禁止做出任何可能會讓我家電腦被扣押的舉動
* 尤其是任何涉及「錢」的操作，更是**嚴禁**[^1]。比如遊戲點卡儲值，註冊相關帳號。或是金融相關網頁/程式的帳戶開設/操作
* 僅供個人玩玩，禁止轉讓/租借/商業使用
* 禁止主機對外、對內發包（無論是否為主動行為）, ARP 攻擊, ARP 劫持, 掃描弱密碼, 惡意窮舉，DDoS，木馬和干擾其它伺服器運行
* 禁止發送垃圾郵件、垃圾訊息, 散播木馬、病毒(包括引用其它伺服器的惡意文件)
* 禁止使用 Torrents、BT，等下載或傳播版權內容
* 禁止使用 net_speeder/finalspeed/kcptun 等干擾我家網路運作的，任何形式的多倍發包工具
* 資源合理使用。禁止長時間消耗/佔滿CPU/網路頻寬等資源，例如rclone轉存/挖礦，**或是讓我感覺家裡網路很卡**(最主要的判斷方式)
* 個人使用，可以拿來解鎖流媒體之類的。但禁止用於爬蟲/帳號註冊等，可能會使IP被標記為bot(俗稱IP被汙染)之行為
* 禁止架設耗資源的程式，如線上遊戲。或將主機當作檔案主機使用，提供公開連接，例如圖床/檔案伺服器

## 聯絡方式 | Contact
* mailto: ix@kskb.eu.org

## 致謝 | Special Thanks
KSKB-IX 的正常運作，離不開下列群友的貢獻

| 名單                                      | 致謝 |
|------------------------------------------|----------|
| [TOHU NET](https://as140731.bairuo.net/) | <li>感謝<ins>白渃</ins>提供的 IPv6 Transit</li><li>走GeekIX去STUIX </li> |
| [雫](https://as142553.zhiccc.net/)       | <li>感謝<ins>雫</ins>提供的 IPv6 Transit</li><li>走wgcf去STUIX</li> |
| [小易](https://network.steveyi.net/)     | <li>感謝<ins>小易</ins>提供的 IPv6 Transit</li><li>走<ins>小易</ins>提供的VM去STUIX</li><li>感謝<ins>小易</ins>提供的VM，可以同時直連Hinet和STUIX，解決`中華電信 <-> STUIX`繞美的問題</li> |

[^1]: 真實故事: 不法分子行騙以後，贓款 GASH 點數於網路上轉賣。有人貪便宜購買贓物 GASH 以後，掛著 Hinet 出口的VPN儲值。警方接獲報案以後循線追蹤，因此將VPN主人的電腦(查到儲值IP是VPN主人的IP)扣押進行後續調查。因此符合第一條的「可能會讓我家電腦被扣押的舉動」
