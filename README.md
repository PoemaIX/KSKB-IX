# KSKB-IX

## 介紹 | Introduction
一個搭在我家客廳雲的玩具IX，主要作為一個中立的BGP實驗交流和學習的平台  

本IX支援IPv4/IPv6  
我們支持MTU9000的巨型幀  

## 接入 | Join
玩具IX
SLA保證低於99%  
~~保證低於也算是一種保證~~  
VM接入/wifi接入(如果你住我家隔壁)  
我們強制要求與RS進行BGP連接  
若沒有和RS進行BGP連接並發送路由，KSKB將在Google Chrome記憶體爆炸的時候，優先關閉妳的VM  

## 要求
參與者使用的bgp daemon必須支援以下功能
* IPv6 link local
* bgp large community
* multiprotocol BGP (IPv4)
* extended nexthop (IPv4)

## 設定 | Configure
* RS1
  * AS114514
  * 普通人也能連接
  * 必須連接，並發送至少一條路由
  * [過濾規則](https://github.com/KSKBpage/KSKB-IX/blob/main/RS1.md#default-filtering-policy)
  * [支援的Community屬性](https://github.com/KSKBpage/KSKB-IX/blob/main/RS1.md#announcement-control-via-bgp-communities)
  * 連線地址: fe80::114:514%eth1
* RS2
  * AS114514
  * 只有智慧之人才能連接
  * `import all; export all;`
  * 不支援任何Community屬性
  * 路由發送上限 2000，超過會被斷開連接
  * 連線地址: fe80::11:4514%eth1

## 連線 | Connectivity
內部向外發起的連線，走wgcf。MTU 1432  
外部向內發起的連線，走hinet。MTU 1492  
提供udp port forward服務，port範圍 XXX00~XXX99，XXX=VMID。供內網隧道搭建使用  
向內/向外，並不是依據封包方向。而是依據tcp/udp session建立的方向  
由防火牆conntrack模組負責追蹤( `-m conntrack --ctstate NEW ` )整個session  
講人話: 透過port forward連進來的連線走Hinet，MTU 1492。由內部向外發起連線，走wgcf，1432  

可以申請IP白名單，供隧道搭建使用，名單內的IP不論方向，均直接走hinet出去  
