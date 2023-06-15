# Poema IX POP List

## Topology

Poema IX is composed of multiple Points of Presence (PoP), each operating independently.
The PoPs are interconnected using EVPN+VXLAN.

![Alt text](./files/topology.drawio.svg)

## Node List

=== "KSKB-HOME"

    <h3>Pop Attributes</h3>
    
    * Operator: KSKB
    * Pop Type: [Direct Sales](#TYPE)
    * Physical Location: Da'an District, Taipei City, Cloud in KSKB's living room
    * No SLA guarantee
    
    <h3>Access Method</h3> 
    
        1. Tunnel
        2. VM
        3. Physical
    <h4>1. Tunnel</h4>
    
        1. OpenVPN
    <h4>2. VM</h4>
    
    === "VM Shape"
        |Virt| CPU| Mem| Disk| MRC    |
        |----|----|----|-----|--------|
        | LXC| 1C |0.5G| 8G  | Free   |
        | KVM| 1C | 1G | 8G  | NTD200 |
        | KVM| 1C | 2G | 16G | NTD350 |
        
        ??? "IX Global Rules"
            * Comply with the laws of the Republic of China (Taiwan) and refrain from any action that may result in the confiscation of the computer
            * Especially prohibited are any operations involving "money." For example, game card top-up or registration of related accounts, as well as account opening or operations related to financial web pages/programs.
            * For personal use only; transfer, rental, and commercial use are prohibited
            * Prohibited actions include sending packages from the host to the outside or inside (regardless of whether they are active), ARP attacks, ARP hijacking, scanning weak passwords, malicious enumeration, DDoS, Trojans, and interference with the operation of other servers
            * Prohibited activities include sending spam emails or messages, spreading Trojans and viruses (including malicious files referencing other servers)
            * Prohibited from using Torrents, BT, or similar methods to download or distribute copyrighted content
            * Prohibited from using tools such as net_speeder/finalspeed/kcptun, or any form of packet multiplication that interferes with network operation
            * Utilize resources reasonably. Prohibited from consuming/occupying CPU/network bandwidth for a long time, such as rclone storage transfer/mining, or making my home network feel slow (this is the primary criterion)
            * Prohibited activities such as web crawling/account registration that may result in the IP being flagged as a bot (commonly known as IP contamination)
            * Prohibited from running resource-intensive programs, such as online games
        

        ???+ PoP Specific Regulations
            KSKB provides resources for free to provide a place for individuals interested in network operation to practice network configuration. It also allows networking enthusiasts to connect, discuss, and experiment with different network architectures, collectively improving their technical skills. However, for KSKB, electricity costs (≈NT200/month), internet fees (NT1099/month), and hardware costs (≈NT25000) are expenses. KSKB does not want its funds to be used for purposes other than experimentation and practicing network configuration. Therefore, the use of VMs is subject to the following regulations:

            <h4>Usage Regulations</h4>
            * VMs are only allowed for internal network tunneling and network configuration purposes (such as OSPF/eBGP/iBGP/Route Reflector).
            * The network provided by VMs is only for member-to-member or internal network traffic exchange and should not be used as an external network.
                * The connected entities must meet the following criteria:
                   1. Other nodes within your internal network
                   2. Peering partners
                   3. Apt update/git clone traffic required for node operation
                *Prohibited actions include prolonged bandwidth occupation that affects others' usage.
            *Prohibited use for other purposes such as proxy, hosting, etc., including but not limited to:
                * Prohibited provision of public services or public connections.
                    * Looning glass or network configuration-related services are allowed.
                * Prohibited use as a file host, such as image hosting/file/web servers.
                * Prohibited use as traffic termination/VPN egress.
            * If you are a friend of KSKB, you may bypass the above restrictions with KSKB's consent.
        ???+ "VM Access Requirements"
            * You must prove to KSKB that you possess the following abilities:
                1. Understanding of packet forwarding processes in switches.
                2. Knowledge of router routing selection processes.
                3. Understanding the purpose of dynamic routing and the differences between dynamic and static routing.
                4. Familiarity with the operation of eBGP/iBGP/iGP/RR and the problems these technologies aim to solve.
                5. Must have your own internal network (tunnel-based is acceptable) and include the provided VM in the network.
                6. The internal network must have iGP and iBGP routing protocols and be able to differentiate between peer and transit tables.
                7. The network card on the IX is only used for building an internal network tunnel and not for any other services.
                8. Approach learning with a humble attitude and be nice.
            * Proof: Recommendation from group members/exams/interviews/self-provided materials.
            * If you do not establish a BGP connection with RS Regular and send at least one IPv6 route, KSKB will prioritize shutting down your VM when Google Chrome's memory explodes to allow KSKB to browse the web/play games smoothly.
           * If you are a friend of KSKB, you may bypass the above restrictions with KSKB's consent.
    === "Connectivity"
        Network Connections

        
        All **outgoing** connections from the IX VM follow the routing policies outlined below:
        
        | Dst IP           | Dst port                | Connection         | Speed     | MTU  |
        |------------------|-------------------------|--------------------|-----------|------|
        | 0.0.0.0/0        | `0~9999`                | wgcf(Cloudflare)   | 200M/200M | 1432 |
        | 0.0.0.0/0        | `10000~65535`<br>ICMP   | Hinet              | 500M/250M | 1492 |
        | ::/0             | Any                     | Hurricane Electric | 200M/200M | 1372 |
        
      | Connection Service  
        
        * Port forward:
            * 🂠🂠🂠=**VMID**
            tcp/udp通用 porforwarding, a total of 100 portsrt
                *For internal network tunnel setup  
                * 🂠🂠🂠00~🂠🂠🂠99
            * ssh port:
                Connect to port 22 of VMID machine22
                * :10🂠🂠🂠 → :2t
    <h4>3Physical物理(Wifi)</h4>
    
        Location: Taipei City北市
        Short-range access, limited to neighbors鄰居
            * 2.4GHz 802.11n
            * 5GHz 802.11ac
        Long-range access接入
            The access point must be within 3km of Liuzhangli MRT Station, Taipei, with direct line of sight and no building obstruction.遮擋
            KSKB provides the rooftop, and the directional Wi-Fi construction cost will be borne by you.提供
    <h4>4Physical (AX.25 Radio Access) (Not Implemented)))</h4>
    
        Not implemented, still considering whether to proceed.要弄

=== "Jord-Home"

    <h3>PoP 屬性</h3>
    
    * 管理人: [@jord903](https://t.me/jord903)
    * PoP 種類: [加盟](#TYPE)
    * 物理位置: 桃園市平鎮區，Jord 家裡的客廳
    * 無SLA保證
    
    <h3>接入方式</h3> 
        
        1. 隧道
        2. VM
    <h4>1. 隧道</h4>
    
        1. OpenVPN
        2. GRETAP
        3. VXLAN
    <h4>2. VM</h4>
    
    === "VM Shape"
        |Virt| CPU| Mem| Disk| MRC    |
        |----|----|----|-----|--------|
        | LXC| 1C |512M|  8G |  Free  |
        | KVM| 1C | 1G | 16G | NTD150 |
        | KVM| 1C | 2G | 32G | NTD300 |
        更高規格請洽詢節點管理員
        
        ??? "IX 全域性規定"
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

        ???+ "VM 接入要求"
            * 任何人皆可接入，僅限教育使用，嚴禁非法行為
            
    === "Connectivity"
        網路連線
        
        所有從 IX VM 的**出方向**的連線，遵守以下路由策略
        
        | Dst IP           | Dst port                | Connection       | Speed   | MTU  |
        |------------------|-------------------------|------------------|---------|------|
        | 0.0.0.0/0        | `any`                   | SeedNet          |100M/40M | 1500 |
        
    
=== "JK-Lab"
    
    <h3>PoP 屬性</h3>
    
    * 管理人: whojk
    * PoP 種類: [加盟](#TYPE)
    * 物理位置: 台灣桃園市中壢區，中央大學
    * 無SLA保證
    
    <h3>接入方式</h3> 
        
        1. 隧道
        2. VM
    <h4>1. 隧道</h4>
    
        1. OpenVPN
    <h4>2. VM</h4>
    
    === "VM Shape"
        |Virt| CPU| Mem| Disk| MRC    |
        |----|----|----|-----|--------|
        | LXC| 1C |0.5G|8G   | Free   |

        ??? "IX 全域性規定"
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

        ???+ "VM 接入要求"
            * 僅限中央大學在職教職員/在讀學生
    === "Connectivity"
        網路連線
        
        所有從 IX VM 的**出方向**的連線，遵守以下路由策略
        
        | Dst IP           | Dst port                | Connection                 | Speed    | MTU  |
        |------------------|-------------------------|----------------------------|----------|------|
        | 0.0.0.0/0        | `any`                   | National Central University| 500M/500M | 1500 |
        

=== "Yi-VM"

    <h3>PoP 屬性</h3>
    
    * 管理人: KSKB
    * PoP 種類: [贊助](#TYPE)，贊助人: 小易
    * 物理位置: 台北市內湖區，是方電訊
    * 無SLA保證
    * 大易網維修中，暫時無法使用
    
    <h3>接入方式</h3> 
        
        1. 隧道
    <h4>1. 隧道</h4>
    
        1. OpenVPN
        2. GRETAP
        3. VxLAN

=== "Luobo-TWDSVM"

    <h3>PoP 屬性</h3>
    
    * 管理人: KSKB
    * PoP 種類: [贊助](#TYPE)，贊助人: 蘿蔔
    * 物理位置: 台北市內湖區，是方電訊
    * 無SLA保證
    
    <h3>接入方式</h3> 
        
        1. 隧道
    <h4>1. 隧道</h4>
    
        1. OpenVPN
        2. GRETAP
        3. VxLAN

=== "Zerotier"

    <h3>PoP 屬性</h3>
    
    * 管理人: KSKB
    * PoP 種類: [直營](#TYPE)
    * 物理位置: 虛擬 PoP，無具體位置
    * 無SLA保證
    
    <h3>接入方式</h3> 
            
        1. 隧道
    <h4>1. 隧道</h4>
    
        1. ZeroTier



## 直營 vs 加盟 vs 贊助<a name="TYPE"></a>

### 直營
* 節點設備由 KSKB 本人所有，並由 KSKB 直接管理
* 接入需要 KSKB 本人同意

### 加盟
* 節點設備以及節點清退規則由加盟人管理
* 接入需該 PoP 之管理員同意，無須 KSKB 同意
* 但若發現有濫用行為，KSKB 有權要求加盟主清退成員
* KSKB 授權使用 「Poema IX」商標和營運權限
* 使用 VXLAN + EVPN 接入 IX 內網

### 贊助
* 接入需要 KSKB 本人同意
* 由贊助人贊助節點給 KSKB 作為 Poema IX 的 PoP 使用
* 節點設備由贊助者所有，但授權 KSKB 管理
