# KSKB-IX

## 介紹 | Introduction
一個搭在我家客廳雲的玩具IX，主要作為一個中立的BGP實驗交流和學習的平台  

本IX支援IPv4/IPv6  
我們支持MTU9000的巨型幀  

## 接入 | Join
僅限邀請/內部推薦  
SLA保證低於99%  
~~保證低於也算是一種保證~~  
VM接入/wifi接入(如果你住我家隔壁)  
我們強制要求與RS進行BGP連接，而且參與者必須支援ipv6 link local，以及bgp large community  
若有發送IPv4路由需求，參與者還須支援multiprotocol BGP和extended nexthop  
若沒有和RS進行BGP連接並發送路由，KSKB將在Chrome記憶體爆炸的時候，優先關閉妳的VM  

## 設定 | Configure

* RS1
  * RS1必須連接，並發送至少一條路由
  * 有過濾，AS-SET內容變動，請手動聯繫更新
  * 連線地址: fe80::114:514%eth1
* RS2
  * RS2只有智慧之人才能連接
  * `import all; export all;`
  * 連線地址: fe80::11:4514%eth1

## 連線 | Connectivity
內部向外發起的連線，走wgcf。MTU 1432  
外部向內發起的連線，走hinet。MTU 1492  
提供udp port forward服務，範圍 [VMID00~VMID99]供內網隧道連接使用  
向內/向外，並不是依據封包方向。而是依據tcp/udp session建立的方向  
由防火牆conntrack模組負責追蹤( `-m conntrack --ctstate NEW ` )整個session  
講人話: 透過port forward連進來的連線走Hinet，MTU 1492。由內部向外發起連線，走wgcf，1432  

可以申請IP白名單，供隧道搭建使用，名單內的IP不論方向，不走wgcf，直接hinet出去  
