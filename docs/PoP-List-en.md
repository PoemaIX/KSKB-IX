# Poema IX POP List

## Topology

Poema IX is composed of multiple PoPs, each operating independently.
PoPs are interconnected using EVPN+VXLAN.

![Diagram](topology.d2){pad="0"}
![Diagram](legend.d2){pad="0" scale="1"}

## Node List

=== "KSKB-HOME"

    <h3>PoP Attributes</h3>
    
    * Operator: [@KSKB](https://t.me/KusakabeShi)([Direct](#TYPE))
    * Physical Location: Da'an District, Taipei City, KSKB's living room
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
        
        ??? "IX Global Rules"
            * Comply with the laws of the Republic of China (Taiwan). Any high-risk legal activity that could cause the operator's computer to be seized is prohibited.
            * Any activity involving "money" is strictly prohibited. For example: game top-ups, registering related accounts, or opening/operating accounts on financial websites/programs.
            * Sending packets to the Internet or within the IX (whether proactive or not) is prohibited, including ARP attacks, ARP hijacking, weak password scanning, malicious brute force, DDoS, trojans, and interference with other servers.
            * Sending spam email/messages or spreading trojans/viruses (including referencing malicious files from other servers) is prohibited.
            * Using Torrents/BT to download or distribute copyrighted content is prohibited.
            * Using net_speeder/finalspeed/kcptun or any form of packet multiplication tool that interferes with network operation is prohibited.
            * Fair use applies. Long-term consumption/occupation of CPU/network/bandwidth resources (e.g., rclone transfer, mining) is prohibited, **at the operator's discretion**.
            * Using it for crawling/account registration that may cause IPs to be flagged as bots is prohibited.
            * Running resource-intensive programs such as online games is prohibited.
            
        ???+ PoP Specific Rules
            KSKB provides resources for free to give people interested in networking a place to **practice network configuration** and to meet other enthusiasts to discuss/experiment with different network architectures. However, electricity (≈NT200/month), internet (NT1099/month), and hardware (≈NT25000) are real costs. KSKB does not want these funds used for purposes other than experiments or practice. Therefore, VM usage follows the rules below.
        
            <h4>Usage Regulations</h4>
            
            * VMs are only for internal network setup and networking configuration (e.g., OSPF/eBGP/iBGP/Route Reflector).
            * Personal use only; transfer/rental/commercial use is prohibited.
            * The VM-provided network is only for **member-to-member internal traffic exchange** and must not be used as an external network.
                * The connection targets must meet the following criteria:
                    1. [Other nodes in your internal network](Virtual-IXP.md#IX-VM-ETH0)
                    2. Peering partners
                    3. Traffic required to keep the node running, such as apt update/git clone
                * Long-term bandwidth occupation that affects others is prohibited.
            * Other uses such as proxying or hosting are prohibited, including but not limited to:
                * Public services or public connections are prohibited.
                    * Looking glass or networking-configuration-related services are allowed.
                * File hosting, such as image/file/web servers, is prohibited.
                * Traffic landing/VPN egress is prohibited.
            * If you are a friend of KSKB, you may bypass the above restrictions with KSKB's consent.
        
        ???+ "VM Access Requirements"
            * You must prove to KSKB that you have the following abilities:
                1. Understand packet forwarding on switches (what happens in L2 forwarding/L3 routing).
                2. Understand the purpose of dynamic routing and the difference from static routing.
                3. Understand the differences between eBGP/iBGP/IGP/RR, what problems they solve, and how they operate.
                4. Must have your own internal network (tunnel-based is acceptable) and include the provided VM in it.
                5. Your internal network must run IGP and iBGP and be able to differentiate peer and transit tables.
                6. The IX VM interface is only for internal tunnel construction and not for other services.
                7. Be humble and be nice.
            * Proof: Recommendation from group members/exams/interviews/self-provided materials.
            * If you do not establish BGP with `RS Regular` and **announce at least one IPv6 route**, KSKB will prioritize shutting down your VM when Google Chrome memory usage explodes so KSKB can browse/ play games smoothly.
            * If you are a friend of KSKB, you may bypass the above restrictions with KSKB's consent.
    === "Connectivity"
        Network Connections
        
        All **outgoing** connections from the IX VM follow the routing policies outlined below:
        
        | Dst IP           | Dst port                | Connection         | Speed     | MTU  |
        |------------------|-------------------------|--------------------|-----------|------|
        | 0.0.0.0/0        | `0~9999`                | wgcf(Cloudflare)   | 450M/450M | 1432 |
        | 0.0.0.0/0        | `10000~65535`<br>ICMP   | Hinet              | 500M/500M | 1492 |
        | ::/0             | Any                     | Hurricane Electric | 450M/450M | 1372 |
        
        Connection Service
        
        * Port forward:
            * 🂠🂠🂠=**VMID**
            * tcp/udp port forwarding, total 100 ports
                * For internal tunnel setup
                * 🂠🂠🂠00~🂠🂠🂠99
            * ssh port:
                * Connect to port 22 of VMID machine
                * :10🂠🂠🂠 → :22
            * Port-forward entry uses a floating IP; the WireGuard tunnel must update the endpoint via crontab.
    <h4>3. Physical(Wifi)</h4>
    
        * Location: Taipei City
        * Short-range access, limited to neighbors
            * 2.4GHz 802.11n
            * 5GHz 802.11ac
        * Long-range access
            * The access point must be within 3km of Liuzhangli MRT Station with direct line of sight and no building obstruction.
            * KSKB provides the rooftop; you cover the directional Wi-Fi build cost.
    <h4>4. Physical (AX.25 Radio Access) (Not Implemented)</h4>
    
        * Not implemented, still considering whether to proceed.

=== "Jord-Home"

    <h3>PoP Attributes</h3>
    
    * Operator: [Jord903](https://t.me/jord903)([Franchisee](#TYPE))
    * Physical Location: Pingzhen District, Taoyuan City, Jord's living room
    * No SLA guarantee
    
    <h3>Access Method</h3> 
    
        1. Tunnel
        2. VM
    <h4>1. Tunnel</h4>
    
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
        For larger shapes, please contact the node administrator
        
        ??? "IX Global Rules"
            * Comply with the laws of the Republic of China (Taiwan). Any high-risk legal activity that could cause the operator's computer to be seized is prohibited.
            * Any activity involving "money" is strictly prohibited. For example: game top-ups, registering related accounts, or opening/operating accounts on financial websites/programs.
            * Sending packets to the Internet or within the IX (whether proactive or not) is prohibited, including ARP attacks, ARP hijacking, weak password scanning, malicious brute force, DDoS, trojans, and interference with other servers.
            * Sending spam email/messages or spreading trojans/viruses (including referencing malicious files from other servers) is prohibited.
            * Using Torrents/BT to download or distribute copyrighted content is prohibited.
            * Using net_speeder/finalspeed/kcptun or any form of packet multiplication tool that interferes with network operation is prohibited.
            * Fair use applies. Long-term consumption/occupation of CPU/network/bandwidth resources (e.g., rclone transfer, mining) is prohibited, **at the operator's discretion**.
            * Using it for crawling/account registration that may cause IPs to be flagged as bots is prohibited.
            * Running resource-intensive programs such as online games is prohibited.

        ???+ "VM Access Requirements"
            * Anyone can access, educational use only, illegal activities are strictly prohibited.
            * Personal use only; transfer/rental/commercial use is prohibited.
            
    === "Connectivity"
        Network Connections
        
        All **outgoing** connections from the IX VM follow the routing policies outlined below:
        
        | Dst IP           | Dst port                | Connection       | Speed   | MTU  |
        |------------------|-------------------------|------------------|---------|------|
        | 0.0.0.0/0        | `1 ~ 9999`              | Cloudflare WARP  |300M/300M| 1440 |
        | 0.0.0.0/0        | `10000 ~ 65535`         | SeedNet          |300M/300M| 1500 |

        The VM is behind NAT. For security reasons, connections with destination ports less than 10000 will be redirected to Cloudflare WARP.
        
    
=== "JK-Lab"
    
    <h3>PoP Attributes</h3>
    
    * Operator: [WhoJK](https://t.me/whojk)([Franchisee](#TYPE))
    * Physical Location: Zhongli District, Taoyuan City, National Central University
    * No SLA guarantee
    
    <h3>Access Method</h3> 
    
        1. Tunnel
        2. VM
    <h4>1. Tunnel</h4>
    
        1. OpenVPN
    <h4>2. VM</h4>
    
    === "VM Shape"
        |Virt| CPU| Mem| Disk| MRC    |
        |----|----|----|-----|--------|
        | LXC| 1C |0.5G|8G   | Free   |

        ??? "IX Global Rules"
            * Comply with the laws of the Republic of China (Taiwan). Any high-risk legal activity that could cause the operator's computer to be seized is prohibited.
            * Any activity involving "money" is strictly prohibited. For example: game top-ups, registering related accounts, or opening/operating accounts on financial websites/programs.
            * Sending packets to the Internet or within the IX (whether proactive or not) is prohibited, including ARP attacks, ARP hijacking, weak password scanning, malicious brute force, DDoS, trojans, and interference with other servers.
            * Sending spam email/messages or spreading trojans/viruses (including referencing malicious files from other servers) is prohibited.
            * Using Torrents/BT to download or distribute copyrighted content is prohibited.
            * Using net_speeder/finalspeed/kcptun or any form of packet multiplication tool that interferes with network operation is prohibited.
            * Fair use applies. Long-term consumption/occupation of CPU/network/bandwidth resources (e.g., rclone transfer, mining) is prohibited, **at the operator's discretion**.
            * Using it for crawling/account registration that may cause IPs to be flagged as bots is prohibited.
            * Running resource-intensive programs such as online games is prohibited.

        ???+ "VM Access Requirements"
            * Only for staff/students of National Central University.
            * Personal use only; transfer/rental/commercial use is prohibited.
    === "Connectivity"
        Network Connections
        
        All **outgoing** connections from the IX VM follow the routing policies outlined below:
        
        | Dst IP           | Dst port                | Connection                 | Speed    | MTU  |
        |------------------|-------------------------|----------------------------|----------|------|
        | 0.0.0.0/0        | `any`                   | National Central University| 500M/500M | 1500 |


=== "MoeDove"
    
    <h3>PoP Attributes</h3>
    
    * Operator: [Gugumnt](https://t.me/gugumnt)([Franchisee](#TYPE))
    * Physical Location: Taipei City, Chief Telecom
    * SLA guaranteed
    
    <h3>Access Method</h3> 
        
        1. VM

    <h4>1. VM</h4>
    
    === "VM Shape"
        |Virt| CPU| Mem| Disk| MRC    |
        |----|----|----|-----|--------|
        | KVM| 1C |  1G|10G  | 10 USD |

        ??? "IX Global Rules"
            * Comply with the laws of the Republic of China (Taiwan). Any high-risk legal activity that could cause the operator's computer to be seized is prohibited.
            * Any activity involving "money" is strictly prohibited. For example: game top-ups, registering related accounts, or opening/operating accounts on financial websites/programs.
            * Sending packets to the Internet or within the IX (whether proactive or not) is prohibited, including ARP attacks, ARP hijacking, weak password scanning, malicious brute force, DDoS, trojans, and interference with other servers.
            * Sending spam email/messages or spreading trojans/viruses (including referencing malicious files from other servers) is prohibited.
            * Using Torrents/BT to download or distribute copyrighted content is prohibited.
            * Using net_speeder/finalspeed/kcptun or any form of packet multiplication tool that interferes with network operation is prohibited.
            * Fair use applies. Long-term consumption/occupation of CPU/network/bandwidth resources (e.g., rclone transfer, mining) is prohibited, **at the operator's discretion**.
            * Using it for crawling/account registration that may cause IPs to be flagged as bots is prohibited.
            * Running resource-intensive programs such as online games is prohibited.

        ???+ "VM Access Requirements"
            * Contact Telegram @gugumnt
    === "Connectivity"
        Network Connections
        
        All **outgoing** connections from the IX VM follow the routing policies outlined below:
        
        | Dst IP           | Dst port                | Connection     | Speed       | MTU  |
        |------------------|-------------------------|----------------|-------------|------|
        | 0.0.0.0/0        | `any`                   | MoeDove Global | 1000M/1000M | 1500 |
        

=== "Luobo-TWDSVM"

    <h3>PoP Attributes</h3>
    
    * Operator: [@KSKB](https://t.me/KusakabeShi)(Luobo [Sponsored](#TYPE))
    * Physical Location: Neihu District, Taipei City, Chief Telecom
    * No SLA guarantee
    
    <h3>Access Method</h3> 
    
        1. Tunnel
    <h4>1. Tunnel</h4>

        1. OpenVPN
        2. GRETAP
        3. VXLAN

## Direct vs Franchisee vs Sponsored<a name="TYPE"></a>

### Direct
* PoP is owned by KSKB
* Operator is KSKB
* Access requires the consent of KSKB

### Franchisee
* PoP is owned and managed by the franchisee
* Operator is the franchisee
* Access requires the consent of the PoP operator and does not require KSKB consent
* If abuse is found, KSKB may request the operator to remove members

### Sponsored
* PoP is sponsored and is **not** owned by KSKB
* Operator is KSKB
* Access requires the consent of KSKB
