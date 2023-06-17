# Poema IX POP List

## Topology

Poema IX is composed of multiple Points of Presence (PoP), each operating independently.
The PoPs are interconnected using EVPN+VXLAN.

![Alt text](./files/topology.drawio.svg)

## Node List

=== "KSKB-HOME"

    <h3>Pop Attributes</h3>
    
    * Operator: KSKB
    * Pop Type: [Direct](#TYPE)
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
        | KVM| 1C | 1G | 8G  | NTD200 |
        | KVM| 1C | 2G | 16G | NTD350 |
        
        ??? "IX Global Rules"
            * Complying with the laws of the Republic of China (Taiwan) and refrain from any action that may result in the confiscation of the computer.
            * Especially prohibited are any operations involving "money". For example, game card top-up or registration of related accounts, as well as account opening or operations related to financial web pages/programs.
            * For personal use only; transfer, rental, and commercial uses are prohibited.
            * Prohibited actions include sending packages from the host to the outside or inside (regardless of whether they are active), ARP attacks, ARP hijacking, scanning weak passwords, malicious enumeration, DDoS, Trojans, and interference with the operation of other servers.
            * Prohibited activities include sending spam emails or messages, spreading Trojans and viruses (including malicious files referencing other servers).
            * Prohibited from using Torrents, BT, or similar methods to download or distribute copyrighted content.
            * Prohibited from using tools such as net_speeder/finalspeed/kcptun, or any form of packet multiplication that interferes with network operation.
            * Utilize resources reasonably. Prohibited from consuming/occupying CPU/network bandwidth for a long time, such as rclone storage transfer/mining, or making my home network feel slow (this is the primary criterion).
            * Prohibited from activities such as web crawling/account registration that may result in the IP being flagged as a bot (commonly known as IP contamination).
            * Prohibited from running resource-intensive programs, such as online games.
        

        ???+ PoP Specific Regulations
            KSKB provides resources for free to provide a place for individuals interested in network operation to practice with network configurations. It also allows networking enthusiasts to connect, discuss, and experiment with different network architectures, collectively improving their technical skills. However, for KSKB, electricity costs (≈NT200/month), internet fees (NT1099/month), and hardware costs (≈NT25000) are expenses. KSKB does not want its funds to be used for purposes other than experimentation and practicing network configurations. Therefore, the use of VMs is subject to the following regulations:

            <h4>Usage Regulations</h4>
            
            * VMs are only allowed for internal network tunneling and network configuration purposes (such as OSPF/eBGP/iBGP/Route Reflector).
            * The network provided by VMs is only for member-to-member or internal network traffic exchange and should not be used as an external network.
                * The connected entities must meet the following criteria:
                   1. Other nodes within your internal network
                   2. Peering partners
                   3. Apt update/git clone traffic required for node operation
                * Long time bandwidth occupation that affects others' usage is prohibited.
            * Prohibited use for other purposes such as proxy, hosting, etc., including but not limited to:
                * Provision of public services or public connections is prohibited.
                    * Looking glass or network configuration-related services are allowed.
                * File host, such as image hosting/file/web servers is prohibited.
                * VPN egress is prohibited.
            * If you are a friend of KSKB, you may bypass the above restrictions with KSKB's consent.
        ???+ "VM Access Requirements"
            * You must prove to KSKB that you get the following knowledges:
                1. Understanding of packet forwarding processes in switches.
                2. Knowledge of router routing selection processes.
                3. Understanding of the purpose of dynamic routing and the differences between dynamic and static routing.
                4. Familiarity with the operation of eBGP/iBGP/IGP/RR and the problems these technologies aim to solve.
                5. Must have your own internal network (tunnel-based is acceptable) and include the provided VM in the network.
                6. The internal network must have IGP and iBGP routing protocols and be able to differentiate between peer and transit tables.
                7. The network interface attached on the IX VM is only used for constructing an internal network tunnel and not for any other services.
                8. Approach learning with a humble attitude and be nice.
            * Proof: Recommendation from group members/exams/interviews/self-provided materials.
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
            tcp/udp porforwarding, a total of 100 portsrt
                *For internal network tunnel setup  
                * 🂠🂠🂠00~🂠🂠🂠99
            * ssh port:
                Connect to port 22 of VMID machine
                * :10🂠🂠🂠 → :22
    <h4>3. Physical(Wifi)</h4>
    
        Location: Taipei City
        Short-range access, limited to neighbors
            * 2.4GHz 802.11n
            * 5GHz 802.11ac
        Long-range access
            The access point must be within 3km of Liuzhangli MRT Station, Taipei, with direct line of sight and no building obstruction.
            KSKB provides the rooftop, and the directional Wi-Fi construction cost will be borne by you.
    <h4>4. Physical (AX.25 Radio Access) (Not Implemented)</h4>
    
        Not implemented, still considering whether to proceed.

=== "Jord-Home"

    <h3>Pop Attributes</h3>
    
    * Operator:  [@jord903](https://t.me/jord903)
    * Pop Type: [Franchisee](#TYPE)
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
            * Complying with the laws of the Republic of China (Taiwan) and refrain from any action that may result in the confiscation of the computer.
            * Especially prohibited are any operations involving "money". For example, game card top-up or registration of related accounts, as well as account opening or operations related to financial web pages/programs.
            * For personal use only; transfer, rental, and commercial uses are prohibited.
            * Prohibited actions include sending packages from the host to the outside or inside (regardless of whether they are active), ARP attacks, ARP hijacking, scanning weak passwords, malicious enumeration, DDoS, Trojans, and interference with the operation of other servers.
            * Prohibited activities include sending spam emails or messages, spreading Trojans and viruses (including malicious files referencing other servers).
            * Prohibited from using Torrents, BT, or similar methods to download or distribute copyrighted content.
            * Prohibited from using tools such as net_speeder/finalspeed/kcptun, or any form of packet multiplication that interferes with network operation.
            * Utilize resources reasonably. Prohibited from consuming/occupying CPU/network bandwidth for a long time, such as rclone storage transfer/mining, or making my home network feel slow (this is the primary criterion).
            * Prohibited from activities such as web crawling/account registration that may result in the IP being flagged as a bot (commonly known as IP contamination).
            * Prohibited from running resource-intensive programs, such as online games.

        ???+ "VM Access Requirements"
            * Anyone can access, educational use only, illegal activities are strictly prohibited
            
    === "Connectivity"
        Network Connections

        All **outgoing** connections from the IX VM follow the routing policies outlined below:
        
        | Dst IP           | Dst port                | Connection       | Speed   | MTU  |
        |------------------|-------------------------|------------------|---------|------|
        | 0.0.0.0/0        | `1 ~ 9999`              | Cloudflare WARP  |100M/40M | 1440 |
        | 0.0.0.0/0        | `10000 ~ 65535`         | SeedNet          |100M/40M | 1500 |

        The VM is behind NAT, connections with destination ports less than 10000 will be redirected to Cloudflare WARP for security reasons
    
=== "JK-Lab"

    <h3>Pop Attributes</h3>
    
    * Operator: whojk
    * Pop Type: [Franchisee](#TYPE)
    * Physical Location: Zhongli District, Taoyuan City, National Central University
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
        | LXC| 1C |0.5G|8G   | Free   |

        ??? "IX Global Rules"
            * Complying with the laws of the Republic of China (Taiwan) and refrain from any action that may result in the confiscation of the computer.
            * Especially prohibited are any operations involving "money". For example, game card top-up or registration of related accounts, as well as account opening or operations related to financial web pages/programs.
            * For personal use only; transfer, rental, and commercial uses are prohibited.
            * Prohibited actions include sending packages from the host to the outside or inside (regardless of whether they are active), ARP attacks, ARP hijacking, scanning weak passwords, malicious enumeration, DDoS, Trojans, and interference with the operation of other servers.
            * Prohibited activities include sending spam emails or messages, spreading Trojans and viruses (including malicious files referencing other servers).
            * Prohibited from using Torrents, BT, or similar methods to download or distribute copyrighted content.
            * Prohibited from using tools such as net_speeder/finalspeed/kcptun, or any form of packet multiplication that interferes with network operation.
            * Utilize resources reasonably. Prohibited from consuming/occupying CPU/network bandwidth for a long time, such as rclone storage transfer/mining, or making my home network feel slow (this is the primary criterion).
            * Prohibited from activities such as web crawling/account registration that may result in the IP being flagged as a bot (commonly known as IP contamination).
            * Prohibited from running resource-intensive programs, such as online games.


        ???+ "VM Access Requirements"
            * Only for staff/students of National Central University
    === "Connectivity"
        Network Connections

        All **outgoing** connections from the IX VM follow the routing policies outlined below:
        
        | Dst IP           | Dst port                | Connection                 | Speed    | MTU  |
        |------------------|-------------------------|----------------------------|----------|------|
        | 0.0.0.0/0        | `any`                   | National Central University|500M/500M | 1500 |
        

=== "Yi-VM"

    <h3>Pop Attributes</h3>
    
    * Operator: KSKB
    * Pop Type: [Sponsored](#TYPE)，Sponsor: SteveYi
    * Physical Location: Neihu District, Taipei City, Chief Telecom
    * No SLA guarantee
    
    <h3>Access Method</h3> 
    
        1. Tunnel
    <h4>1. Tunnel</h4>

        1. OpenVPN
        2. GRETAP
        3. VXLAN

=== "Luobo-TWDSVM"

    <h3>Pop Attributes</h3>
    
    * Operator: KSKB
    * Pop Type: [Sponsored](#TYPE)，Sponsor: Luobo
    * Physical Location: Neihu District, Taipei City, Chief Telecom
    * No SLA guarantee
    
    <h3>Access Method</h3> 
    
        1. Tunnel
    <h4>1. Tunnel</h4>

        1. OpenVPN
        2. GRETAP
        3. VXLAN

=== "Zerotier"

    <h3>Pop Attributes</h3>
    
    * Operator: KSKB
    * Pop Type: [Direct](#TYPE)
    * Physical Location: Virtual PoP, no concrete location
    * No SLA guarantee
    
    <h3>Access Method</h3> 
    
        1. Tunnel
    <h4>1. Tunnel</h4>

        1. ZeroTier


## Direct vs Franchisee vs Sponsored<a name="TYPE"></a>

### Direct
* Directly managed by KSKB
* Access requires the consent of KSKB
* PoP hardware is owned by KSKB

### Franchisee
* PoP hardware and node rules are managed by franchisees
* Access requires the consent of the administrator of the PoP, not the consent of KSKB
* However, if any abuse has occured, KSKB can ask the franchisee to withdraw members
* KSKB authorized the use of "Poema IX" trademark and operating rights to franchisees
* Use VXLAN + EVPN to access IX LAN

### Sponsored
* Managed by KSKB
* Sponsored PoP of Poema IX by sponsoring to KSKB
* PoP hardware is owned by sponsors
