# Poema IX POPs

Poema IX 由多個 PoP 組成，每個 PoP 獨立運作  
PoP 之間使用 EVPN+VXLAN 互連  
![Topology](https://i.imgur.com/Cc33xTj.png)

### KSKB-HOME

#### PoP 屬性
* 管理人: KSKB
* PoP 種類: [直營](#TYPE)
* 物理位置: KSKB 家裡的客廳雲
* SLA保證低於99% ([我家電腦很不穩](https://www.kskb.eu.org/2022/06/5.html)，可能是記憶體或是主機板有問題，會自己不定時BSoD)  
* SLA若超過99%，工單反應以後可獲得雙倍 SLA credit 補償，人工幫你關機降低 SLA 。~~保證低於也算是一種保證~~  

#### 接入方式

1. VM接入
    * 申請 IX VM 無須付費，供測試學習使用。但僅供以下目的使用
        * 練習內網和網路的相關設定(如 OSPF/eBGP/iBGP/Route Reflector)，而非其他用途比如代理，架站等等
    * 且你必須向我證明你具有下列基礎能力:
        * 知道交換機轉發封包的流程
        * 了解路由器選路過程
        * 明白 eBGP/iBGP/iGP/RR 運作的過程，以及這些技術要解決的問題
        * 其他「計算機網路」這門課期末考可能會考的東東
    * 證明方法，可能是考試/面試，或是群友內推
    * 若沒有和`RS1`進行BGP連接並**發送至少一條IPv6路由**，KSKB將在Google Chrome記憶體爆炸的時候，優先關閉妳的VM，供KSKB順順看網頁/玩遊戲  
    * 如果你是我朋友，可以繞過以上限制
2. 隧道接入
    * OpenVPN
3. Wifi接入 - Taipei
    * 短距離接入，僅限鄰居
        * 2.4GHz 802.11n
        * 5GHz 802.11ac
    * 長距離接入
        * 接入點必須位於六張犁捷運站 3km 內，並且可直接目視，無建物遮擋
        * 我提供頂樓，指向式wifi建設成本由妳提供
4. AX.25 無線電接入(正在想要不要搞)

#### VM 連線 | VM Connectivity
**IX VM 的網路連線能力。wifi 或是其他方式接入可以無視本章節**

所有從 IX VM 的**出方向**的連線，遵守以下路由策略

| Dst IP           | Dst port                | Connection                  | MTU  | Comment         |
|------------------|-------------------------|-----------------------------|------|-----------------|
| 0.0.0.0/0        | `0~9999`                | wgcf(Cloudflare)            | 1432 |                 |
| 0.0.0.0/0        | `10000~65535`<br>ICMP   | Hinet                       | 1492 |                 |
| ::/0             | Any                     | Hurricane Electric          | 1372 |                 |


##### 連線服務 | Connection Service
1. port forward服務:
    * **\*\*\*=VMID**
    * :\[\*\*\*00~\*\*\*99\] → :\[\*\*\*00~\*\*\*99\]，共計100個port供內網隧道搭建使用  
    * :10\*\*\* → :22 ，供ssh連線使用
    * port forward入口，由於是浮動IP，wg隧道務必加上crontab更新endpoint

### Jord-HOME

#### PoP 屬性
* 管理人: [@jord903](https://t.me/jord903)
* PoP 種類: [加盟](#TYPE)
* 物理位置: Jord

#### 接入方式

1. VM接入
2. 隧道接入
    1. OpenVPN

#### VM 連線 | VM Connectivity
**IX VM 的網路連線能力。wifi 或是其他方式接入可以無視本章節**

所有從 IX VM 的**出方向**的連線，遵守以下路由策略

| Dst IP           | Dst port                | Connection                  | MTU  | Comment         |
|------------------|-------------------------|-----------------------------|------|-----------------|
| 0.0.0.0/0        | `any`                   | SeedNet                     | 1500 |                 |

### Yi-VM

#### PoP 屬性
* 管理人: KSKB
* PoP 種類: [贊助](#TYPE)
* 贊助人: 小易
* 物理位置: 世方電信

#### 接入方式

1. 隧道接入
    1. OpenVPN
    2. GRETAP
    3. VxLAN
    
### ZeroTier

#### PoP 屬性
* 管理人: KSKB
* PoP 種類: [直營](#TYPE)
* 物理位置: 虛擬 PoP，無具體位置

#### 接入方式

1. 隧道接入
    1. ZeroTier
    
## 直營 vs 加盟 vs 贊助<a name="TYPE"></a>

### 直營
* 接入需要 KSKB 本人同意
* 節點設備由 KSKB 本人所有，並由 KSKB 直接管理  

### 加盟
* 接入需要該 PoP 之管理員同意，無須 KSKB 同意
* 但若發現有濫用行為，KSKB 有事後清退權
* KSKB 授權使用 「Poema IX」商標，使用 VXLAN + EVPN 接入 IX 內網  
* 節點設備以及節點清退規則由加盟人自行管理

### 贊助
* 接入需要 KSKB 本人同意
* 由贊助人贊助節點給 KSKB 作為 Poema IX 的 PoP 使用
* 節點設備由贊助者所有，但授權 KSKB 管理
