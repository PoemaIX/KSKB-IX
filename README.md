# KSKB-IX

## 介紹 | Introduction
一個搭在我家客廳雲的玩具IX，作為一個BGP實驗交流和學習的平台  

主要是給BGP配網的``業餘/有興趣/新手``玩家一個練習的地方，可以放心炸網  
還有提供一個平台，給大家熟悉IX環境。畢竟DN42和公網環境差距不只是一點點  

本IX支援IPv4(透過mpbgp+enh) 以及 IPv6  
peeringdb: https://www.peeringdb.com/ix/3792  

一般IX都不用 `link local`/`mpbgp`/`extended next hop`  
這些技術明明存在，為什麼不用呢?我很好奇!  
所以我就想來用看看了  

普通的IX也不能炸全表  
同時我也想順便開一個可以炸全表的IX  
用比較科學的方式啦  

## 接入 | Join
接入方式:
1. VM接入
2. 短距離Wifi接入(2.4GHz 802.11n/5GHz 802.11ac，如果妳住我家隔壁)
3. 長距離Wifi接入(接入點必須位於六張犁捷運站3km內，我提供頂樓，指向式wifi建設成本由妳提供)
3. AX.25 無線電接入(正在想要不要搞)

非商業，也禁止商業使用  
只是個玩具IX啦，不要拿營業用ASN來加喔  
SLA保證低於99% (我家電腦很不穩，可能是記憶體或是主機板有問題，會自己不定時BSoD)  
~~保證低於也算是一種保證~~  
IX本身不存在IP Transit。妳可以在IX裡面自行尋找參與者索要IP Transit  
我們強制要求與`RS1`進行BGP連接  
若沒有和`RS1`進行BGP連接並**發送至少一條路由**，KSKB將在Google Chrome記憶體爆炸的時候，優先關閉妳的VM，供KSKB順順看網頁/玩遊戲  

## 要求 | Requirement
參與者要有一個公網ASN，以及至少一段 /48 的ipv6

參與者要有一點網路相關知識和經驗，比如
* 知道L2和L3運作的原理
* 知道eBGP/iBGP/iGP是什麼和其差異
* 曾經配過兩個以上 DN42 節點，並提供跨節點的ip transit服務。或是與之相等的知識經驗(曾在其他實驗網/公網提供類似服務)

參與者使用的bgp daemon必須支援以下功能  
* IPv6 link local
* bgp large community
* multiprotocol BGP (IPv4)
* extended nexthop (IPv4)

## 設定 | Configure
* RS1
  * AS114514
  * [過濾規則](https://github.com/KSKBpage/KSKB-IX/blob/main/RS1.md#default-filtering-policy)
  * [支援的Community屬性](https://github.com/KSKBpage/KSKB-IX/blob/main/RS1.md#announcement-control-via-bgp-communities)
  * 普通人也能連接，RS有做過濾
  * 連線地址: fe80::114:514%eth1
* RS2
  * AS114514
  * 規則和RS1一樣，但是可以申請炸全表
  * 連線地址: fe80::1145:14%eth1
  * 炸全表條件:
    * 先將此RS收到的成員路由transit給上游，才能將上游的全表發送至RS.
    * AS-SET: `AS-KSKB-IX`
    * 若想排除部分成員的transit，則需要使用[Community屬性](https://github.com/KSKBpage/KSKB-IX/blob/main/RS1.md#announcement-control-via-bgp-communities)裡面的`Do not announce to peer`，一併從全表發送對象之中排除
      * 意思是若你想法全表發給A，你就得同時把A的路由發給上游
      * 不想把某人路由發上游，就不要發給他全表。要做到對稱
    * 上游路由需打上 (65530:7) 屬性(可以在我這邊登記上游ASN，RS會幫忙自動打上)
    * 弄好以後即可以申請開通炸全表filter
* RS3
  * AS114514
  * 過濾規則: `import all; export all`;，也就是沒有過濾
  * 只有智慧之人才能連接，智慧之人都會自己做好過濾的
  * 不支援任何Community屬性
  * 路由發送上限 2000，超過會被斷開連接
  * 連線地址: fe80::11:4514%eth1

## 成員 | Members
* [Looking Glass](https://ixlg.kskb.eu.org/summary/127.0.0.1/)
* AS-SET: [AS-KSKB-IX](https://apps.db.ripe.net/db-web-ui/lookup?source=RIPE&type=as-set&key=AS-KSKB-IX)


## IX VM 連線 | IX VM Connectivity
**IX VM 的網路連線能力。wifi 或是其他方式接入可以無視本章節**

Traffic        | Connection    | MTU  | Comment                                |
---------------|---------------|------|----------------------------------------|
IPv4(1)        | wgcf          | 1432 | `tcp` & `udp --dport 0:9999`           |
IPv4(2)        | hinet         | 1492 | `icmp` & `udp --dport 10000:65535`     |
IPv6           | wgcf-nyaa-HE  | 1372 | 群友 Nyaa 的 HE TPE 上游(STUIX)         |
L2 to STUIX VM | yi-(your vm)  | 1432 | 走小易VM中轉                            |

提供port forward服務，port範圍 \*\*\*00~\*\*\*99，\*\*\*=VMID，共計100個port供內網隧道搭建使用  
port forward入口，由於是浮動IP，wg隧道務必加上crontab更新endpoint  


#### 連線服務 | Connection Service
1. Dst IP白名單服務(已棄用): 
    * 名單內的IP不論方向，均直接走hinet出去，供內網隧道搭建使用  
3. STUIX 隧道服務: 
    * 因為`中華電信<->STUIX`繞美，家裡剛好也是中華電信。但是不少成員同時有購買 STUIX IXVM
    * 因此我想說幫忙轉發一下，提供 L2 隧道直接通向成員的 STUIX IX VM。  
    * 注意，**並非**接入 STUIX LAN 喔，不能和 HE peer。您還是必須先購買 STUIX IX VM 才能使用喔  
    * 使用方法:
        1. 您 `STUIX 的 IX VM` 和我這邊的`小易VM`建立gretap/隧道  
        2. 我幫您 `KSKB-IX 的 IX VM` 新增虛擬網卡  
    * 由我維護網卡兩邊的隧道。兩邊端口 L2 互通  
    * MTU 1432  

## 限制 | Limitations

對於KSKB提供的IX VM，以及IX LAN，僅供IX成員作為網路流量交換使用。禁止其他類型使用  
包括但不限於以下限制

* 遵守中華民國（台灣）法律，禁止做出任何可能會讓我家電腦被扣押的舉動
* 尤其是任何涉及「錢」的操作，更是**嚴禁**。比如遊戲點卡儲值，註冊相關帳號。或是金融相關網頁/程式的帳戶開設/操作 [^1]
* 僅供個人玩玩，禁止轉讓/租借/商業使用
* 禁止主機對外、對內發包（無論是否為主動行為）, ARP 攻擊, ARP 劫持, 掃描弱密碼, 惡意窮舉，DDoS，木馬和干擾其它伺服器運行
* 禁止發送垃圾郵件、垃圾訊息, 散播木馬、病毒(包括引用其它伺服器的惡意文件)
* 禁止使用 Torrents、BT，等下載或傳播版權內容
* 禁止使用 net_speeder/finalspeed/kcptun 等干擾我家網路運作的，任何形式的多倍發包工具
* 資源合理使用。禁止長時間消耗/佔滿CPU/網路頻寬等資源，例如rclone轉存/挖礦，或是讓我感覺家裡網路很卡
* 個人使用，可以拿來解鎖流媒體之類的。但禁止用於爬蟲/帳號註冊等，可能會使IP被標記為bot(俗稱IP被汙染)之行為
* 禁止架設耗資源的程式，如線上遊戲。或將主機當作檔案主機使用，提供公開連接，例如圖床/檔案伺服器

## 致謝 | Special Thanks
* Nyaa 給我使用的 HE TPE IPv6 Transit
* 小易的轉發，解決`中華電信 <-> STUIX`繞美的問題

[^1]: 真實故事: 不法分子行騙以後，贓款 GASH 點數於網路上轉賣。民眾貪便宜購買贓物 GASH 以後儲值，警方接獲報案以後循線追蹤，因此將儲值 IP 的整台機器扣押進行後續調查。因此符合第一條的「可能會讓我家電腦被扣押的舉動」
