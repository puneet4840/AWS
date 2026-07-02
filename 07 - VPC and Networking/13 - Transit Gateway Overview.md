# Transit Gateway Overview

Isse pehle wali slide mein humne VPC Peering ke baare mein padha tha, Jab 2 ya usse jyada VPCs ko privately connect karna hota hai to VPC Peering ka use karte hain.

Agar apke paas 2 se 5 VPCs hain to aap VPC Peering se inko aapas mein connect kar sakte ho, Lekin agar 10, 50, ya 100 VPCs hain to to VPC peering complex ho jati hai, kyunki aapko har VPC ke beech mein alag connection banana padta hai (jisko Mesh Network kehte hain).

To itne saare VPCs ko aaps mein connect karne ke liye AWS ne **Transit Gateway** service ko launch kiya.

<br>

Transit Gateway is problem ko solve karta hai. Yeh ek Hub-and-Spoke model par kaam karta hai. Isme Transit Gateway beech mein "Hub" (central router) hota hai, aur saare VPCs ya data centers uske "Spokes" (connections) ban jaate hain.

<br>
<br>

### Transit Gateway kya hota hai?

Transit Gateway ek Regional Network Hub hai jo multiple VPCs, VPN Connections aur Direct Connect Gateways ko ek hi central point par connect karta hai.

Yaani.

Har VPC ko har doosri VPC se connect karne ki zarurat nahi.

Bas Transit Gateway se connect kar do.

Jab agar bohot saare VPCs aapas mein privately connect karne ho to, vpc peering na use karke, transit gateway ka use karte hain. Transit gateway ke through sabhi VPCs apas mein connect ho jate hain.

Transit Gateway ek central component hota hai, jisse sabhi VPCs connect ho jate hain. Isme sirf VPC se transit gateway tak ki connection manage karni padti hai.

<br>
<br>

### Transit Gateway ki jarurat kyu padi?

Maan lo tum ek chhoti company mein DevOps Engineer ho.

Company ke paas sirf do VPC hain.
```
Production VPC

10.0.0.0/16


Development VPC

192.168.0.0/16
```

Production VPC mein application chal rahi hai. Development VPC mein developers testing kar rahe hain. Dono VPC ko aapas mein communicate karna hai.

Tumne dono VPC ke beech mein VPC Peering bana di. Ab dono vpc aapas mein privately communicate kar rahe hain

Sab kuch sahi chalne laga.

<br>

**Company grow karti hai**:

6 mahine baad company badi ho gayi. Ab company ne naye projects launch kiye.

Ab architecture kuch aisa ho gaya.
```
Production VPC

Development VPC

Testing VPC

Security VPC

Logging VPC

Shared Services VPC
```
Ab total 6 VPC ho gayi.

<br>

**Ab company ki requirement badal gayi**:

Company bolti hai.

Production VPC ko:
- Logging VPC se baat karni hai.
- Shared Services VPC se DNS lena hai.
- Security VPC ko Logs bhejne hain.

Development VPC ko:
- Shared Services VPC se Active Directory access karni hai.

Testing VPC ko:
- Logging VPC ko Logs bhejne hain.

Matlab lagbhag har VPC ko har doosri VPC se baat karni hai.

<br>

**DevOps Engineer kya karega?**

Sabse pehle tumhare dimaag mein aayega: "Har VPC ke saath VPC Peering bana dete hain." Matlab VPCs ko apas mein VPC peering ke through connect kar dete hain.

Shuru mein ye idea sahi lagta hai.

Lekin jab tum diagram banana shuru karte ho.

To kuch aisa dikhta hai.
```
          Production
         /   |    \
        /    |     \
       /     |      \
Development--Testing--Logging
      \       |      /
       \      |     /
        \     |    /
      Shared Services
```
Uper diagram mein ab har VPC doosri VPC ke saath connected hai. Connections itne zyada ho gaye ki diagram samajhna mushkil ho gaya.

<br>

**Is problem ko Mesh Network bolte hain**:

Jab har VPC har doosri VPC se directly connected hoti hai. Us architecture ko **Mesh Topology** kehte hain.

Ye chhoti environment mein theek hai. Lekin production mein isko samjhna aur implement karna bahut difficult ho jata hai.

Example:

Maan lo sirf 3 VPC hain.
```
A

B

C
```

VPC Peering Connections.
```
A ↔ B

A ↔ C

B ↔ C
```

Total 3 VPC Peering Connections bane.

Ab agar tum VPC badha do. Maanlo 10 VPC ho gye.

Ab in sabko aapas mein VPC Peering se connect karna hai. To inko connect karne ke liye bohot saare VPC peering connections banane padenge.

Jaise-jaise VPC badhegi. Connections bahut tezi se badhne lagenge. Network manage karna mushkil ho jayega.

**Ek aur problem**:

Maan lo:
```
VPC-A

↓

Peering

↓

VPC-B

↓

Peering

↓

VPC-C
```
Matlab VPC-A ko VPC-B ke saath peering kiya, Aur VPC-B ko VPC-C ke saath peering kiya.

Ab VPC-A, VPC-C ke ec2 access karna chta hai. Kya traffic VPC-A se VPC-C tak VPC-B ke through paas ho jayegi?

Answer hai **Nahi**. Kyu?

Kyuki VPC Peering

**Transitive** nahi hoti.

AWS intentionally traffic ko teesri VPC tak forward nahi karta.

<br>

"VPC Peering transitive nahi hoti" (Non-Transitive Routing) ka seedha matlab yeh hai ki do VPCs ke beech ka connection kisi teesre VPC ke zariye pass-through (via) nahi ho sakta.

Agar apko VPC-A ko VPC-C ke saath communicate karnwana hai to alag se in dono VPCs ke beech mein peering karni padegi, tab hi vpc apas mein communicate kar payenge.

<br>

**AWS Engineers ne kya socha?**

AWS ne dekha.

Customers ke paas
- 20 VPC
- 50 VPC
- Kabhi-kabhi 100 VPC

tak hote hain.

Har VPC ki Peering banana. Har Route Table update karna. Har Connection maintain karna. Ye bahut difficult ho raha tha.

AWS ne socha. Ek Central Router bana dete hain. Har VPC usi Router se connect hogi. Aur Router khud traffic forward karega. Isi Central Router ka naam hai **Transit Gateway**.

<br>
<br>

### Key Features & Benefits

**Centralized Routing**: Saara traffic ek hi central point se ho kar guzarata hai.

**Easy Scalability**: Naya VPC connect karne ke liye bas use Transit Gateway se attach karna hota hai. Baaki VPCs ke saath alag se pairing nahi karni padti.

**Inter-Region Peering**: Agar do alag-alag regions mein VPCs ko transit gateway ke though connect kiya hua hai to, aap un dono regions ke transit gatways ko apas mein peering ke though connect kar sakte ho. Matlab do alag-alag regions ke transit gatways ko apas mein connect kiya ja sakta hai.

<br>
<br>

### TGW Ke Core Components

**Attachments**: Jab aap kisi resource (jaise VPC, VPN, ya Direct Connect) ko TGW se jodte hain, to use Attachment kehte hain.
- VPC Attachment: VPCs ko jodne ke liye.
- VPN Attachment: On-premises network ko IPsec VPN se jodne ke liye.
- Direct Connect Gateway Attachment: High-speed dedicated connection ke liye.
- Peering Attachment: Do alag-alag Transit Gateways ko aapas mein jodne ke liye.

**Transit Gateway Route Tables**: TGW ke andar apni route tables hoti hain. Yeh tables decide karti hain ki incoming traffic kis attachment par jayega. Ek attachment sirf ek hi route table se associate ho sakta hai.

**Associations**: Jab aap kisi attachment ko TGW ki kisi specific route table se link karte hain, toh use Association kehte hain. Iska matlab hai ki us attachment se aane wala traffic us route table ke rules ko follow karega.

**Propagations**: Iska matlab hai routes ko automatically seekhna (learn karna). Jab koi attachment TGW se banta hai, toh wo apne network ke IP ranges (CIDR) ko TGW route table mein automatically register kar deta hai.

<br>
<br>

### TGW Kaam Kaise Karta Hai?

Jab aap ek Transit Gateway setup karte hain aur use apne VPCs se connect karte hain, toh back-end mein yeh steps follow hote hain:

**1 - ENI Deployment**:

Jab aap kisi VPC ko TGW se attach karte hain, toh aapko us VPC ke subnets select karne hote hain. AWS har selected Availability Zone (AZ) ke subnet mein ek Elastic Network Interface (ENI) deploy karta hai.

Yeh ENI aapke VPC ke traffic ke liye ek entry aur exit point ki tarah kaam karta hai.

Best Practice: TGW attachment ke liye hamesha ek chota dedicated subnet (/28) banana chahiye har AZ mein, taaki application traffic aur TGW management traffic alag rahe.

**2 - Cross-AZ Routing**:

TGW internally AZ-to-AZ traffic ko maintain karta hai. Agar VPC-A ke AZ-1 se traffic nikal kar VPC-B ke AZ-2 mein ja raha hai, toh TGW automatic infrastructure routing use karke traffic ko sahi destination AZ tak deliver karta hai bina kisi extra latency ke.

**3 - Route Evaluation**:

Traffic jab ek VPC se nikal kar doosre VPC mein jata hai, toh routing do levels par check hoti hai:
- VPC Subnet Route Table: Pehle traffic aapke EC2 instance ke subnet se nikalta hai. Route table dekhti hai ki destination IP range ke liye target tgw-xxxxxx (Transit Gateway) set hai ya nahi.
- Transit Gateway Route Table: Traffic TGW ke paas pahunchta hai. TGW apni internal route table check karta hai ki is destination IP ke liye kaunsa Attachment ID mapped hai. Phir woh traffic us specific VPC ke attachment par forward kar deta hai.

**TGW Route Table Kaise Dikhegi?**:

TGW ke paas ek master route table hogi jo is tarah se routes ko map karegi:
| Destination CIDR (IP Range) | Target Attachment              | Route Type        |
| --------------------------- | ------------------------------ | ----------------- |
| 10.1.0.0/16                 | VPC-Production-Attachment      | Propagated (Auto) |
| 10.2.0.0/16                 | VPC-Development-Attachment     | Propagated (Auto) |
| 10.3.0.0/16                 | VPC-Shared-Services-Attachment | Propagated (Auto) |
| 192.168.1.0/24              | VPN-Office-Attachment          | Static / BGP      |

TGW Route Table ke andar **Propagated Routes** aur **Static/BGP** Routes do alag tareeqe hain jisse router (TGW) ko pata chalta hai ki kaunsa IP address kis VPC ya kis on-premises network ka hai.

In dono ka main kaam routes (rasta) seekhna hai, lekin ek yeh kaam automatically karta hai aur doosra manually ya ek protocol ke threw karta hai.

**1 - Propagated Routes (Automatic Learning)**:

Propagated route ka matlab hai ki jab bhi aap koi AWS resource (jaise VPC) TGW se connect karte hain, toh TGW us resource ki IP range (CIDR block) ko automatically apni route table mein add kar leta hai. Aapko manually koi entry nahi karni padti.
- Jaise hi aapne VPC-A (10.1.0.0/16) ka attachment TGW se joda aur "Route Propagation" ko enable kiya, TGW khud samajh jayega ki “Achha, 10.1.0.0/16 ka traffic ab se mujhe VPC-A attachment par bhejna hai.”
- Kaise kaam karta hai: Kab use hota hai: Yeh mostly VPC Attachments ke liye use hota hai. Agar aap apne VPC mein koi naya CIDR block add karte hain, toh TGW use bhi bina aapke bataye khud update kar leta hai.

**2 - Static / BGP Routes (Manual or Protocol Based)**:

Yeh woh routes hote hain jo TGW khud se nahi seekh sakta. Isme ya toh aapko khud route likhna padta hai (Static), ya phir ek network protocol ke threw rasta share karna padta hai (BGP).

A. Static Routes (Manual Input):
- Kaise kaam karta hai: Aap TGW ke dashboard par jaate hain aur khud type karte hain: "Agar traffic ```192.168.1.0/24``` par jana chahta hai, toh use VPN-Attachment par bhej do."
- Kab use hota hai: Jab aapko kisi traffic ko zabardasti kisi specific raaste par bhejna ho. Jaise ki saara internet traffic (0.0.0.0/0) ek Central Security/Firewall VPC ki taraf modna ho.

B. BGP Routes (Border Gateway Protocol):
- Kaise kaam karta hai: BGP ek networking (protocol) hai. Jab aap apne office ke physical router ko AWS ke VPN ya Direct Connect se jodte hain, toh aapka office router aur AWS TGW aapas mein "baat" karte hain. Office router TGW ko bolta hai: "Mere paas yeh ```192.168.1.0/24``` ki range hai." TGW is baat ko sunkar apne route table mein entry save kar leta hai.
- Kab use hota hai: Yeh hamesha On-Premises connections (Data Center / Office VPN / Direct Connect) ke liye use hota hai. Agar office mein koi naya network banta hai, toh BGP ke threw TGW ko apne aap pata chal jata hai, aapko manually entry nahi karni padti.

<br>
<br>

### Transit Gateway Route Table

Transit Gateway ke paas apni alag Route Table hoti hai.

Example.
```
10.0.0.0/16

↓

Production Attachment
```
```
192.168.0.0/16

↓

Development Attachment
```
```
172.16.0.0/16

↓

Logging Attachment
```

Jab packet Transit Gateway tak aata hai. Transit Gateway apni Route Table check karta hai. Fir decide karta hai. Packet kis VPC mein bhejna hai.

<br>
<br>

### Transit Gateway sirf VPC ko connect karta hai?

Nahi.

Transit Gateway connect kar sakta hai:
- VPC.
- Site-to-Site VPN.
- AWS Direct Connect.
- Connect Attachments (SD-WAN).
- Peering with another Transit Gateway.

Yaani.

On-Premises Data Center bhi isi se connect ho sakta hai.

<br>
<br>

### Transit Gateway Transitive hai

Transit Gateway Transitive hai—yeh iska sabse bada aur sabse important feature hai.

Networking mein "Transitive" ka simple matlab hota hai ki agar **A** connected hai **B** se, aur **B** connected hai **C** se, toh **A** aur **C** aapas mein automatically baat kar sakte hain.

TGW isi rule par kaam karta hai, jabki AWS ka purana network connection (VPC Peering) non-transitive tha.

**1. VPC Peering (Non-Transitive Network)**:

Pehle jab hum do VPCs ko aapas mein jodte the, toh use VPC Peering kehte the. VPC Peering hamesha Point-to-Point kaam karti hai.
```
[VPC-A] <=========> [VPC-B] <=========> [VPC-C]
```

- Problem: Is diagram mein VPC-A aur VPC-B aapas mein peered hain. VPC-B aur VPC-C bhi peered hain.
- Lekin agar VPC-A ko VPC-C se baat karni ho, toh woh VPC-B ke threw hoke nahi ja sakta. AWS VPC Peering mein traffic ko kisi doosre VPC se "hop" ya pass karne ki permission nahi hoti.
- Solution kya tha phir? Aapko VPC-A aur VPC-C ke beech mein alag se ek aur teesra Peering connection banana padta tha. Agar 10 VPCs ho jayein, toh 45 peering connections banane padte the, jo ek bura sapna (nightmare) ban jata tha.

<br>

**2. Transit Gateway (Transitive Network)**:

Transit Gateway ne is poore rule ko badal diya. TGW ek central cloud router hai. Jab aap multiple VPCs ko ek hi TGW se attach karte hain, toh TGW un sabke beech mein Transitive Routing allow kar deta hai.

```
[VPC-A] (10.1.0.0/16) ──┐
                        ▼
[VPC-C] (10.3.0.0/16) ──► [ AWS Transit Gateway ] ◄── [VPC-B] (10.2.0.0/16)
                        ▲
[Office Data Center] ───┘
```

Kaise kaam karta hai: 
- Aapne VPC-A ko TGW se joda, VPC-B ko bhi TGW se joda, aur VPC-C ko bhi TGW se joda.
- Ab agar VPC-A ko VPC-C se baat karni hai, toh use kisi direct connection ki zaroori nahi hai. VPC-A apna traffic central TGW ko bhejega, aur TGW us traffic ko forward karke VPC-C tak pahuncha dega.
- Yahan TGW ek "Transitive Hub" ki tarah kaam kar raha hai, jo beech mein baith kar sabka traffic aapas mein pass karwa raha hai.

<br>

**Iska Real-World Faida Kya Hai?**

**Centralized Internet (NAT Gateway)**: Aapko har VPC mein alag se mehenga NAT Gateway lagane ki zaroori nahi hai. Aap ek alag "VPC-Internet" banayein, usme NAT Gateway rakhein, aur use TGW se jod dein. Saare VPCs (A, B, C) TGW ke threw is single NAT Gateway ko use karke internet par ja sakte hain.

**Centralized Security (Firewall Inspection)**: Agar aap chahte hain ki office ka traffic jab bhi kisi VPC mein jaye, toh pehle firewall se check ho, toh aap ek "Inspection VPC" bana sakte hain. TGW ke transitive nature ki wajah se saara traffic pehle firewall VPC mein jayega, inspect hoga, aur phir destination VPC tak pahunchega.

**Easy On-Premises Access**: Agar aapne apne office ka VPN TGW se connect kar diya, toh transitive property ki wajah se office ke log ek hi connection se VPC-A, VPC-B, aur VPC-C teeno ko access kar sakte hain.

<br>
<br>

### Transit Gateway Setup Kaise Karte Hain?

TGW setup karne ke liye ye steps follow karein:

**Step 1: Transit Gateway Create Karein**:
- AWS Console mein VPC Dashboard par jayein.
- Transit Gateways par click karein aur Create Transit Gateway select karein.
- Isko ek naam dein aur Amazon Side ASN (Autonomous System Number) configure karein (Default bhi chhod sakte hain).

**Step 2: VPCs ko Attach Karein**:
- Transit Gateway Attachments par jayein aur Create Transit Gateway Attachment par click karein.
- Apna TGW select karein, Attachment type VPC chunyein, aur jis VPC ko connect karna hai use select karein.
- Wo Availability Zones aur subnets select karein jahan TGW ENI banega. (Aise hi baki saare VPCs ke liye attachments banayein).

**Step 3: TGW Route Table Configure Karein**:
- By default, TGW ek default route table bana deta hai jahan saare VPCs ke routes automatically propagate (add) ho jaate hain.
- Agar aapko custom routing chahiye (jaise VPC A sirf VPC B se baat kare, C se nahi), toh aap alag se custom TGW Route Table banakar attachments ko manual associate aur propagate kar sakte hain.

**Step 4: VPC Route Tables Update Karein (Sabse Zaroori Step)**:
- TGW setup karne ke baad bhi aapke EC2 instances ko nahi pata hota ki traffic TGW par bhejna hai.
- Isliye aapko apne VPC ke Subnet Route Tables mein jana hoga.
- Wahan ek naya route add karein: Target IP (jaise dusre VPC ka CIDR block ya ```0.0.0.0/0```) aur Target Type mein Transit Gateway select karke apna TGW chunyein.

<br>
<br>

### Packet Flow Kaise Hota Hai?

Maante hain ki VPC-A (Instance 1) se ek packet VPC-B (Instance 2) par bhejna hai. Iska packet flow aisa hoga:

- **Instance Se Nikalna**: Instance 1 packet generate karta hai jiska destination IP Instance 2 ka hota hai.
- **VPC-A Route Table Chec**k: Packet sabse pehle VPC-A ke subnet route table ko check karta hai. Wahan likha hota hai ki agar VPC-B ke IP par jana hai, toh target Transit Gateway (TGW) hai.
- **ENI Tak Pahunchna**: Packet VPC-A ke us subnet mein maujood TGW Elastic Network Interface (ENI) ke paas jata hai.
- **TGW Entrance**: ENI us packet ko secure AWS internal network ke zariye central Transit Gateway par bhej deta hai.
- **TGW Route Table Lookup**: TGW us packet ka destination IP dekhta hai aur apni associated TGW Route Table check karta hai.
- **Attachment Match**: Route table mein dikhta hai ki is destination IP ka rasta VPC-B Attachment se hokar jata hai.
- **VPC-B Entrance**: TGW us packet ko VPC-B ke TGW ENI par forward kar deta hai.
- **Destination Delivery**: VPC-B ka ENI us packet ko directly Instance 2 tak pahunche deta hai.

<br>
<br>

### Transit Gateway Cross-Region Peering

Transit Gateway (TGW) Cross-Region Peering ek aisi capability hai jisse aap do alag-alag AWS Regions (jaise US-East-1 aur EU-West-1) mein chal rahe Transit Gateways ko aapas mein connect kar sakte hain.

Iska matlab yeh hai ki agar aapka global network hai, toh aap alag-alag regions ke VPCs aur on-premises networks ko AWS ke private global backbone network ke threw secure aur high-speed par aapas mein jor sakte hain.

**Yeh Kaam Kaise Karta Hai?**

Maan lijiye aapki company ka setup do regions mein hai: **Mumbai Region (ap-south-1)** aur **N. Virginia Region (us-east-1)**.

**1. Local Connections**: Mumbai ke saare VPCs Mumbai TGW (TGW-Mumbai) se connected hain. Virginia ke saare VPCs Virginia TGW (TGW-Virginia) se connected hain.

**2. The Peering Connection**: Aap TGW-Mumbai par jaakar ek Peering Attachment request create karte hain aur target mein TGW-Virginia ki ID aur uska region daalte hain.

**3. Acceptance**: Virginia region mein jaakar aap is request ko accept karte hain. Ab dono TGWs ke beech ek direct pipeline (peering tunnel) ban jati hai.

**4. AWS Backbone Routin**g: Yeh peering connection public internet par nahi jata. AWS apne khud ke under-sea aur land-based fiber optic cables (Global Backbone Network) ka use karta hai, jisse latency bohot kam milti hai aur security maximum hoti hai.

