# KSKB-IX

## 介紹 | Introduction
一個搭在我家客廳雲的玩具IX，主要作為一個BGP實驗交流和學習的平台  

本IX支援IPv4/IPv6/MTU9000

## 接入 | Join
非商業，也禁止商業使用  
只是個玩具IX啦，不要拿營業用ASN來加喔  
SLA保證低於99%  
~~保證低於也算是一種保證~~  
VM接入/wifi接入(如果妳住我家隔壁)  
IX本身不存在IP Transit。妳可以在IX裡面自行尋找參與者索要IP Transit  
我們強制要求與RS進行BGP連接  
若沒有和RS進行BGP連接並發送至少一條路由，KSKB將在Google Chrome記憶體爆炸的時候，優先關閉妳的VM，供KSKB順順看網頁/玩遊戲  

## 要求
參與者使用的bgp daemon必須支援以下功能  
* IPv6 link local
* bgp large community
* multiprotocol BGP (IPv4)
* extended nexthop (IPv4)

## 設定 | Configure
* RS1
  * AS114514
  * [過濾規則](https://github.com/KSKBpage/KSKB-IX/blob/main/RS1.md#default-filtering-policy)
  * 普通人也能連接，RS有做過濾
  * 必須連接，並發送至少一條路由
  * [支援的Community屬性](https://github.com/KSKBpage/KSKB-IX/blob/main/RS1.md#announcement-control-via-bgp-communities)
  * 連線地址: fe80::114:514%eth1
* RS2
  * AS114514
  * 過濾規則: `import all; export all;`
  * 只有智慧之人才能連接，相信智慧之人都會自己做好過濾的，對吧?
  * 不支援任何Community屬性
  * 路由發送上限 2000，超過會被斷開連接
  * 連線地址: fe80::11:4514%eth1

## 連線 | Connectivity

IX VM 的網路連線能力

Traffic     | Connection   | MTU  | Comment                |
------------|--------------|------|------------------------|
IPv4 Input  | Hinet        | 1492 | port forward進來的連線  |
IPv4 Output | wgcf         | 1432 | 內部對外發起的連線       |
IPv6        | wg in wgcf   | 1372 | 感謝Nyaa的 HE TPE IPv6 transit |

提供udp port forward服務，port範圍 \*\*\*00~\*\*\*99，\*\*\*=VMID，共計100個port供內網隧道搭建使用  
port forward入口，由於是浮動IP，wg隧道務必加上crontab更新endpoint  

Input/Output，並不是依據封包方向。而是依據tcp/udp session建立的方向  
由防火牆conntrack模組負責追蹤( `-m conntrack --ctstate NEW ` )整個session  

可以申請dst IP白名單，供隧道搭建使用，名單內的IP不論方向，均直接走hinet出去  

## 成員 | Members
 
* [Looking Glass](https://ixlg.kskb.eu.org/summary/127.0.0.1/)
* AS-SET: [AS-KSKB-IX](https://apps.db.ripe.net/db-web-ui/lookup?source=RIPE&type=as-set&key=AS-KSKB-IX)

## 限制 | Limitations
對於KSKB提供的IX VM，僅供IX成員作為網路流量交換使用。禁止其他類型使用  
包括但不限於以下限制

* 遵守中華民國（台灣）法律，禁止做出任何可能會讓我家電腦被扣押的舉動
* 僅供個人玩玩，禁止轉讓/租借/商業使用
* 禁止主機對外、對內發包（無論是否為主動行為）, ARP 攻擊, ARP 劫持, 掃描弱密碼, 惡意窮舉，DDoS，木馬和干擾其它伺服器運行
* 禁止發送垃圾郵件、垃圾訊息, 散播木馬、病毒(包括引用其它伺服器的惡意文件)
* 禁止使用 Torrents、BT，等下載或傳播版權內容
* 禁止使用 net_speeder/finalspeed/kcptun 等干擾我家網路運作的，任何形式的多倍發包工具
* 資源合理使用。禁止長時間消耗/佔滿CPU/網路頻寬等資源，例如rclone轉存/挖礦
* 個人使用，解鎖流媒體之類的還可以。但禁止爬蟲/大量帳號註冊等，可能會使IP被標記為bot(俗稱IP被汙染)之行為
* 禁止架設耗資源的程式，如線上遊戲。或將主機當作檔案主機使用，提供公開連接，例如圖床/檔案伺服器
