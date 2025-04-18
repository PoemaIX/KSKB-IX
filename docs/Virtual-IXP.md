# What is Virtual IXP

在了解什麼是虛擬IXP以前，先了解什麼是「網」吧!  
這裡的網，是指大家平常上網的網   
我一開始是因為興趣，接入了 DN42 網，後來取得真實 ASN  
先來個名詞定義，我們把真正幫我們傳遞封包的稱作「專業ISP」，我這種業餘的叫做「Player」吧  
講的可能有誤，歡迎糾錯

## 專業ISP
真正的 ISP 的網，大概像這樣子  
![](https://i.imgur.com/EUixT9N.png)  
有著自己的的眾多節點，提供著各式各樣的服務  
個個節點和資料中心之間，使用物理電路/光纜線相連  
並且給客戶提供服務時，也是真的拉一條線去那邊(比如物理上拉了一條線到你家，讓你上網)  

## 業餘 Player
至於業餘的網，大概長這樣子  
有許多節點，但幾乎都是VM，或是租用別人的基礎設施  
VM之間也沒有自己的基礎光纖設施，而是**使用隧道來「模擬」光纜**，使節點間互聯  
隧道仍然要依賴「專業ISP」提供的物理鏈路才能建立
![](https://i.imgur.com/ukRolkU.png)  

## 網路互連
每家業者都有自己的眾多節點，節點之間互相拉光纖來傳輸流量  
但是，不同業者之間，也需要交換流量。這就是「互連」  
中華電信有自己的網，遠傳電信也有自己的網。  
但是在中華電信和遠傳之間，各有一個(可能有多個)節點是**戶相連起來**的這就是「互聯」  
每當中華電信的使用者，想要連線遠傳的網站時，就要經過這這個互連點   
![](https://i.imgur.com/xC9vlU5.png)  
互連點之間，也是透過光纖連接  
因為這條光纖同時連接兩家業者，所以可能會拆帳，比如建設費用你付一半我付一半之類  

## IXP: 網路交換中心
但是這個時候，就會遇到問題了  
現在業者越來越多了，但是每家業者之間都要兩兩互連  
就會像這樣子，要蓋的光纜線越來越多了，呈現指數上漲  
![](https://i.imgur.com/TYyAiNt.png)  
於是，就有了「網路交換中心(IXP)」的概念出現了  
只要像這樣，大家都連線到交換中心，就可以和其他 ISP 互連了  
![](https://i.imgur.com/iH6Nm5w.png)  

這樣子的設計，簡化了網路的互聯難度，也增加了網路的互連度  

如果 IX 的成員增多了，互連度就會增加  
網路間封包流動的成本降低，延遲也降低了，**同時也增加了網路的吞吐量**  

## 物理 IXP
而物理 IXP 就是**真的有光纜**  

每家業者都有著眾多的節點，甚至有著自己的資料中心，提供各式各樣的服務  
但是現在業者間不用互相喬攏，誰蓋光纖，誰挖馬路，誰出錢  
只要各自挖馬路，挖到交換中心就好了  
大家一起鋪光纖，「把**自己的網路線插到 IXP 的交換機上面**」，便能在這邊交換路由和流量  

一般有兩種模式

1. **進線**，直接接到 IXP port 上(圖中右邊)
2. **進線**，先接自己 colo 的機器，然後自己機器**跳線**到 IXP port 上(圖中左邊)

![](https://i.imgur.com/5M8FD5a.png)  

這張圖提到三個名詞，分別解釋一下

* colo: 指 Colocation 。意思是向機房管理方租用一塊空間(機櫃)和電力，使自己可以擺一台機器在機房內部
* 跳線: **同大樓機房內，不同機櫃之間的牽線服務**。機房內有很多機櫃。擺了很多機器。如果不同機櫃之間的機器要物理連線，就需要「跳線」。也可能不同大樓，但屬同一間公司擁有。
    * 一般來說，如果「起點→終點，**整條路都是同一家的**」，就是跳線。
    * 購買「跳線服務」以後，管理方就會幫忙在起點機櫃到終點機櫃之間牽一條光纖網路線，兩邊的機器就能連接起來
* 進線: 把**機房外部的光纖網路線，牽入機房內部**的服務。作為一個 ISP ，要服務各地的人。不可能整個網路只侷限在機房裡面。因此 ISP 在各地佈光纖以後，若需要把業務流量送入交換中心的機房，就需要「進線」。
    * 一般來說，「起點→終點，**兩邊是不同家的**」，就是進線。機房是管理方的財產，從外面拉線進去，一定要機房管理方同意和協助。
    * 購買「進線服務」以後，管理方便會協助「進入機房→目標機櫃」這段路程的網路線鋪設

**進線服務和 IXP 自身幾乎同等重要**。  
[這篇文章](https://www.ptt.cc/bbs/Broad_Band/M.1239365152.A.886.html) 就吐槽了中華電信一直死守 TWIX 不讓其它人進線。連進 IXP LAN 的成本比直接蓋互連光纜還要高出 N 倍，也為台灣的畸形的網路環境推了一把  
(最近稍微好了一點，但還是遠遠比不上香港/新加坡/日本)  
如果一個 IX 不管是把封包送進去，還是把封包送出來都難如登天。讓大家都很難在這邊交換，那他也失去交換中心本身的意義了  

順帶一提「port接入、colo、跳線、進線」 分別是獨立不相干服務，甚至可能由不同提供商提供  

## 虛擬 IXP

虛擬 IXP 則是類似這張圖，「**模擬**」物理 IXP，讓業餘愛好者體驗接入 IXP 的感覺  
![](https://i.imgur.com/Rzir1RX.png)  

1. 隧道接入: 模擬**外部進線**直接接入 IXP port。從自己外部的節點，直接連接到 IX 的交換機上
2. VM 接入: 
    1. VM本體: 模擬自己把機器 colo 在 IXP 相同的機房
    2. 上網卡: 模擬**外部進線**到自己的機器，讓大家打隧道去自己其他的內網節點。通常是 eth0
    3. IX網卡: 模擬**跳線**到 IXP port 上面

和物理IX不同，虛擬IX 做為教育和實驗用途。他**不會改善網路的吞吐量**  
以 Poema IX 為例，因為(除了Wifi接入的成員)，物理上仍依托於中華電信的電路  
物理層面的網路拓樸結構沒有改變，因此網路的總吞吐量也不會變化  

### IX VM 的上網卡<a name="IX-VM-ETH0"></a>

物理 IX 需要和「自己的其他內網節點」物理連接(進線服務)  

假如 Google 和遠傳在 TPIX 交換流量，遠傳的用戶瀏覽 Google 時，都是走橘色路線  
![](https://i.imgur.com/DAEiOfm.png)

所以**物理 IX 是沒有「上網卡」這個概念的**，都是物理上的光纖線路  
頂多給個 offband 網路，供 ssh 管理使用。但絕對不會在這裡面跑業務流量  

虛擬 IX 和物理 IX 類似，同樣也需要「進線」  
但不同於物理 IX 實際業務跑在物理光纖上面，虛擬 IX 的「進線」是**使用隧道來模擬**的  
而**隧道必須跑在上網卡**上面。因此上網卡的網路品質就變得相對重要  
如果上網卡延遲很高，相當於瓶頸卡在「進線」這一塊，導致這在這邊交換的延遲都被拉高  

以 Poema IX 為例。上網卡有「中華電信直連」、「WARP VPN(Cloudflare)」兩條線路  
可以選擇適合的線路和自己的內網節點打隧道，降低一點延遲  
但要注意的是，物理上仍依托於中華電信的電路

在 Poema IX 這邊，有規定提供的 IX VM 上網卡「**僅供模擬進線服務**，禁止用於其他目的」  
因為虛擬 IX 用隧道**模擬進線**時。連線對象一定是「自己其他的內網節點」  
[這條限制](../#ix-vm)(禁止流量落地，通訊對象必須是自己其他的內網節點)就是這樣來的  

KSKB 提供 VM 的目的很明確: **模擬**交換中心的運作模式 ，供大家測試和練習網路配置。  
而不是免費丟給你一個 VM 隨便架服務，或是落地當成 VPN 出口節點來用  

上網卡的某些線路是群友贊助的。所以，使用上也會有一些限制。比如說某些線路要登記 Dst IP 才能用  

## 混合 IXP

還有一種IX: 混合IX。就是同時開放物理接入和隧道/VM接入的 IX  
![](https://i.imgur.com/6D4H9st.png)

因為混合了兩種類型的接入成員  
對於實體接入的成員，上網卡能 ssh 就好，主要流量透過物理光纖傳輸  
但是對於虛擬接入的成員，因為業務都跑在隧道裡，延遲就變得相對重要  
  
虛擬接入的成員，實際的網路封包仍然依託於物理接入成員的線路  
如圖所示:  
![](https://i.imgur.com/G7iMI9q.png)

雖然虛擬接入的成員不改變物理拓樸，也不改變網路的吞吐總量  
但仍能對**網路的整體效應帶來正向的效果**  
比如原本會繞路的路由，經由混合 IX，使得封包不繞路了  
降低了無謂的資源浪費  

## KSKB-IX
KSKB-IX 是「虛擬 IXP」，模擬公網的基礎設施的運作模式  
主要使用[隧道和 VM](/#接入--join)接入，供業餘愛好和體驗測試用    

主機硬體是一台規格不高的電腦 (8 core + 24G RAM)，給所有人都開 kvm 記憶體會不夠  
因此 VM 接入的成員，只能提供 LXC VM(1core/512M 記憶體，合理使用禁止佔滿)   
只能夠真的有需要才開 kvm (比如 RouterOS)  
但不管是 KVM 或是 LXC，都能安裝 bird/frr 開轉發，扮演一個路由器的角色  
![](https://i.imgur.com/Dmtb14v.png)

虛擬 IXP 並不提供給商業用途使用，也不能長時間跑大流量占用... 
有此需求請接入物理 IXP 或是商業性質的混合 IXP  
但若想測試和學習，或是增加 peer 數量，非常歡迎加入喔  
