# What is Virtual IXP

我一開始是因為興趣，接入了 DN42 網，後來取得真實 ASN  
我們把真正幫我們傳遞封包的稱作「專業ISP」，我這種業餘的叫做「player」吧  
講的可能有誤，歡迎糾錯

## 專業ISP
真正的 ISP 的網，大概像這樣子  
有著自己的基礎設施，之間使用物理光纜相連  
並且底下真的有客戶，拉線客戶提供服務  
![](https://i.imgur.com/EUixT9N.png)

## 業餘 Player
至於業餘的網，大概長這樣子  
許多節點，幾乎都是租來的VM  
VM之間也沒有自己的基礎光纖設施，而是**使用隧道來「模擬」光纜**，使節點間互聯  
![](https://i.imgur.com/ukRolkU.png)

## 物理 IXP
而物理 IXP 就是**真的有光纜**  

大家挖馬路埋光纖(or租用 IEPL/IPLC) ，一起把網路線插到 IXP 的交換機上面，然後交換路由和流量(也就是跳線接入)  
或是 IXP colo ，把自己的路由器 colo 在 IXP 的機櫃裡面，然後用進線服務，外部用光纜連入自己 colo 的路由器，和自己內網相連  

「port接入、colo、跳線、進線」 分別是獨立不相干服務，甚至可能由不同提供商提供  
比如 STUIX 提供 port 接入、colo 服務，跳線、進線要找是方電訊  
![](https://i.imgur.com/0HXWiAX.png)

## 虛擬 IXP

虛擬 IXP 則是類似這樣，主要是「模擬」物理 IXP，讓業餘愛好者體驗接入 IXP 的感覺

1. 隧道接入: 模擬外部跳線接入。直接從自己外部的路由器連接到 IX 的交換機上
2. VM 接入: 
    1. VM本體: 模擬 IXP colo 接入，把自己的機器部屬在 IXP 裡面
    2. Underlay 網路: 模擬 colo 的外部進線服務，讓大家打隧道去自己其他的內網節點。通常被稱作「上網卡」

![](https://i.imgur.com/jt1pPjI.png)

## 混合 IXP

還有一種IX: 混和IX。就是類似這種，混和了虛擬IX和實體IX，開放物理接入的同時允許業餘玩家用隧道/VM的方式接入  
![](https://i.imgur.com/2PfdVuG.png)

實際的網路封包則是類似這樣流動  
![](https://i.imgur.com/Tgn7xYX.png)

## KSKB-IX
KSKB-IX 是「虛擬 IXP」，模擬公網的基礎設施，業餘愛好和體驗測試用  
示意圖可以看到分別模擬的部分  

硬體雖然只是一台規格不高的電腦(4 core+16G RAM)，給所有人都開 kvm 記憶體一定不夠  
因此，開 LXC 給成員是唯一的選擇  
只能夠真的有需要才開 kvm(RouterOS user)  
但不管是 KVM 或是 LXC，都能開轉發，安裝 bird/frr ，扮演一個路由器的角色  
![](https://i.imgur.com/PPVWC59.png)

對真正的從業人員，自有 infra + 物理光纜的大佬來說，這種低規硬體，應該會感到好笑  

但以虛擬IXP的定位來說，我覺得 KSKB-IX 有達成它的使命  
雖然性能很弱，但該模擬的都有模擬  

虛擬 IXP 也不是給跑大流量的... 有此需求請接入物理 IXP 或是商業性質的混和 IXP  
單純想測試和學習的話，歡迎加入  