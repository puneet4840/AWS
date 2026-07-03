# AWS Direct Connect Overview

AWS mein Direct Connect (DX) ek service hai jo on-premises data center ko AWS cloud se ek physical, private fiber-optic cable ke zariye connect karti hai.

AWS Direct Connect (DX) asal mein aapke on-premises data center ko physically AWS ke data center (ya region) se hi connect karta hai.

Dhyan dene wali baat ye hai ki is connection mein Public Internet ka use nahi hota. Matlab tumhara network packets Google, Airtel, Jio ya kisi public ISP ke normal Internet routing se travel nahi karte.

Data Packets on-premises data center se directly AWS ke private network mein enter kar jate hain aur AWS ke data center tak pahuchte hain.

<br>
<br>

### Sabse pehle samajhte hain ki Direct Connect ki zarurat kyun padi?

Maan lo tum ek badi company mein DevOps Engineer ho. Company ka apna ek Data Center hai. Is Data Center mein company ke saare important applications chal rahe hain. Jaise HR Application, Banking Application, ERP Software, Database Servers aur File Servers.

Diagram dekho.
```
Company Data Center

|
|---- Application Server
|
|---- Database Server
|
|---- File Server
|
|---- Active Directory
```
Sab kuch company ke office ke andar hi chal raha hai. Is setup ko **on-premises** data center kehte hain.

Ab company decide karti hai ki wo Cloud adopt karegi. Lekin company ek hi din mein saare servers AWS mein migrate nahi kar sakti. Migration ek long process hota hai.

Isliye company decide karti hai ki kuch servers office mein rahenge aur kuch applications AWS mein shift kar di jayengi.

Ab architecture kuch aisa ban jata hai.
```
Company Data Center
        |
        |
        |
       AWS Cloud
        |
      VPC
        |
      EC2
      RDS
      S3
```

Ab company ke paas do alag networks hain.
- On-Premises Network.
- AWS Network.

Ab in dono ko aapas mein communicate karna hai.

<br>

**Pehla Solution - VPN**:

Sabse pehle Network Engineer bolta hai,

"Hum Site-to-Site VPN bana dete hain." Isse dono network privately connect ho jayenge.

Architecture kuch aisa ho jata hai.
```
Office

↓

Internet

↓

VPN Tunnel

↓

AWS
```
Ab Office ke Servers aur AWS ke Servers securely communicate karne lagte hain.

Sab kuch sahi chal raha hai.

Lekin kuch mahine baad problem shuru hoti hai.

<br>

**VPN mein problem kya aayi?**

Company ka business bahut grow kar gaya. Ab har din bahut zyada data transfer hone laga.

Jaise:
- Database Replication.
- Backup Files.
- Log Files.
- Video Processing.
- Analytics Data.
- AI Model Training Data.

Har din 20 TB se 50 TB tak data AWS aur Office ke beech transfer hone laga.

Ab employees complain karne lage. "Kabhi application fast chalti hai." "Kabhi bahut slow ho jaati hai." Ab Network Team ne investigation shuru ki.

<br>

**Investigation mein kya pata chala?**

Unhone dekha ki VPN mein koi problem nahi hai.

Encryption bhi sahi hai.

Configuration bhi sahi hai.

Problem Internet mein hai.

Yahan ek bahut important concept samajhna hai.

VPN secure hota hai.

Lekin VPN Internet ko replace nahi karta.

VPN sirf Internet ke upar ek encrypted tunnel banata hai. Jisme data encrypted hoke source se destination par jata hai.

Sirf data secure ho jata hai.

Matlab agar Internet slow hai.

To VPN bhi slow hoga.

<br>

**Company ki Requirement badal gayi**:

Ab Company bolti hai:
- "Hume security bhi chahiye."
- "Hume stable speed bhi chahiye."
- "Hume low latency bhi chahiye."
- "Hume ye guarantee chahiye ki Office aur AWS ke beech network hamesha same performance de."

Yahan VPN fail hone lagta hai. Kyunki Internet unpredictable hai.

<br>

**AWS ne kya socha?**

AWS Engineers ne socha.

Problem encryption ki nahi hai.

Problem Internet ki hai.

To agar Internet hi remove kar diya jaye to?

AWS ne decide kiya ki company ke Data Center aur AWS ke beech ek dedicated private network provide kiya jaye.

Isi service ka naam hai **AWS Direct Connect**.

<br>
<br>

### AWS Direct Connect Kyun Chahiye? (The Problem vs Solution)

**Problem (With VPN)**: VPN sasta aur accha hai, lekin woh chalta public internet par hi hai. Agar aapke shehar ka internet slow ho jaye, ya internet provider ke cables mein issue aaye, toh aapki speed drop ho jayegi aur ping (latency) badh jayegi. Saath hi, iski maximum speed sirf 1.25 Gbps hoti hai.

**Solution (With Direct Connect):** Chunki isme internet ka koi role nahi hai, isliye aapko ekdam fix aur guaranteed speed milti hai. Isme network kabhi lag nahi karta aur aap heavy data (jaise terabytes of database) ko bohot fast speed par transfer kar sakte hain.

<br>
<br>

### Physical Architecture Aur Connection Process (Ground Level Setup)

Direct Connect ko samajhne ke liye pehle iske physical network setup ko samajhna zaroori hai. Yeh koi software-only solution nahi hai, isme physical hardware involve hota hai.

**AWS Direct Connect Location (Point of Presence - PoP)**:

Bahut log sochte hain. AWS unke office tak cable lekar aata hai. Ye galat hai. AWS duniya bhar mein bahut saare Direct Connect Locations banata hai. Ye specially designed Data Centers hote hain. AWS ne duniya bhar ke bade-bade third-party data centers (jaise Equinix, Digital Realty, ya NTT) mein apne khud ke high-end routers install kiye hue hain. Is jagah ko Direct Connect Location ya PoP bolte hain.

Tumhari company apne office se us Direct Connect Location tak fiber connection leti hai.

Uske baad AWS apne Backbone Network ke through us traffic ko AWS Region tak pahunchata hai.

**AWS Backbone Network kya hota hai?**

AWS ke paas duniya bhar mein apna private fiber network hai.

Ye Internet se completely alag hota hai.

Is network ko sirf AWS hi use karta hai.

Jab packet Direct Connect Location tak pahunch jata hai.

Tab packet isi Backbone Network ke through AWS Region tak travel karta hai.

Yahi reason hai ki Direct Connect ki latency bahut stable hoti hai.

<br>

**Meet-Me Room (MMR)**: Har bade data center mein ek central room hota hai jise Meet-Me Room kehte hain. Yahan par saare networking providers aur AWS ke cables aakar terminate hote hain.

<br>

**Last Mile Connection**: Aap kisi telecom provider ko hire karte hain. Woh provider aapke office se lekar us DX Location ki building tak ek private physical line (cable) bichha deta hai.

Customer Router: Agar aapka data center usi same Equinix facility mein hai jahan AWS hai, toh aapka router wahan pehle se hoga. Agar aapka office kisi doosri city mein hai, toh aap ek telecom provider (jaise Airtel, Tata, Orange) ki line lekar is location tak apni connectivity pahochaenge.

<br>

**Cross Connect (The Real Joint)**: Jab aapka wire DX Location tak pahunch jata hai, toh wahan aapki cable ko ek physical patch cord ke threw AWS ke router ke port se direct jor diya jata hai. Is physical connection ko Cross Connect kehte hain.

<br>

**The Final Path**: Ek baar yeh joint lag gaya, toh aapka office ka data center direct AWS ke data center se connect ho jata hai. Ab aap bina internet ke seedhe AWS ke network par travel kar sakte hain.

<br>
<br>

### Direct Connect mein Kon Kon se components hote hain

AWS Direct Connect (DX) ek bohot bada aur heavy infrastructure setup hai. Isko poori tarah samajhne ke liye hum iske har ek component ko ek-ek karke deep mein dekhenge.

Aapko poora concept clear karne ke liye, hum ek imaginary example le kar chalte hain: "MatrixCorp" naam ki ek badi banking company hai jiska apna ek bohot bada physical Data Center Mumbai mein hai. MatrixCorp apna core banking software AWS cloud par shift kar rahi hai aur unhe high-speed, ultra-secure link chahiye bina internet ka use kiye.

Chaliye Direct Connect ke har ek component ko is MatrixCorp ke safar ke sath detail mein samajhte hain.

<br>

**1. PHYSICAL COMPONENTS (Physical Layer)**:

Yeh woh cheezein hain jise aap physically touch kar sakte hain, ya jo real world mein wire aur router ke roop mein exist karti hain.

**A. AWS Direct Connect Location (The Meeting Point)**:

AWS direct aapke office building mein apna router nahi laga sakta. Isliye pure duniya mein alag-alag sheharon mein bade-bade secure data centers hote hain jise Co-location Center (Cage) ya DX Location kehte hain. Yeh ek aisi third-party building hoti hai (jaise Equinix, Netmagic, ya CtrlS) jahan AWS apna network rack (routers) install karke baithta hai.

MatrixCorp Example: MatrixCorp ne check kiya ki Mumbai mein AWS ki Direct Connect Location kaunsi building mein hai. Unhe pata chala ki GPX Data Center (Mumbai) ek DX location hai. Ab MatrixCorp ko pata chal gaya ki unhe apna wire isi building tak pahunchana hai.

<br>

**B. Customer Gateway (CGW - Aapka Router)**:

Yeh aapke data center ya office ke border par laga hua ek heavy-duty enterprise router hota hai (jaise Cisco ASR, Juniper MX series). Yeh router aapke local network ka aakhri point hota hai aur iski configuration aapki networking team ke haath mein hoti hai.

MatrixCorp Example: MatrixCorp ne apne Mumbai data center ke rack mein ek brand new Cisco router lagaya. Is router ka kaam MatrixCorp ke saare computers aur servers ke traffic ko ik इकट्ठा karna aur AWS ki taraf bhejni ki taiyari karna hai.

<br>

**C. AWS Direct Connect Router (AWS Side Port)**:

Yeh DX Location ke andar baitha hua AWS ka apna core router hota hai. MatrixCorp ka saara data aakhiri mein isi router ke port ke andar enter karega.

<br>

**D. Last Mile Connection (The Long Cable)**:

Aapke office se lekar DX Location ki building tak jo physical fiber-optic cable zameen ke niche se ya poles ke threw bichhayi jati hai, use Last Mile Connection kehte hain. Yeh cable MatrixCorp khud nahi bichha sakti, iske liye hume network providers (jaise Airtel, Tata Communications, ya Vodafone/Orange) ko contract dena padta hai.

MatrixCorp Example: MatrixCorp ne Airtel se baat ki. Airtel ne Mumbai mein MatrixCorp ke data center se lekar GPX Data Center (DX Location) tak zameen ke niche se kai kilometers lambi Dark Fiber Cable bichha di.

<br>

**E. Cross Connect (The Final Patch Cord)**:

Jab Airtel ki cable GPX Data Center (DX Location) ke andar pahunch gayi, toh woh MatrixCorp ke ek chote se box (Meet-me-room) mein connect ho gayi. Ab savaal yeh hai ki is cable ko AWS ke router se kaise jodein? AWS ke rack se lekar MatrixCorp ke box tak jo ek choti si final fiber optic wire (patch cord) lagayi jati hai, use Cross Connect kehte hain.

MatrixCorp Example: MatrixCorp AWS console par jaakar Direct Connect connection request karti hai. AWS unhe ek letter deta hai jise LOA-CFA (Letter of Authorization and Connecting Facility Assignment) kehte hain. Is letter mein likha hota hai ki AWS ke kis router ke kis port number (e.g., Rack 4, Port 12) par wire jorani hai. MatrixCorp yeh letter data center ke admin ko deti hai, aur woh physically ek wire lekar AWS ke port aur MatrixCorp ke port ko aapas mein jor deta hai. Jaise hi yeh judta hai, Direct Connect ka physical setup complete ho jata hai.

<br>

**2. CONNECTION TYPES (Logical Purchase Models)**:

Physical wire jodne se pehle MatrixCorp ko AWS se port khareedna padta hai. AWS iske do options deta hai:

**A. Dedicated Connection**: Isme poore ka poora physical network port sirf aur sirf aapki company ke liye dedicated ho jata hai. Us port ke andar kisi doosre customer ka data nahi aa sakta. Yeh sirf do speeds mein aata hai: 1 Gbps, 10 Gbps, ya 100 Gbps.

MatrixCorp Example: MatrixCorp ek bada bank hai aur unka data bohot zyada hai. Unhone AWS se ek 10 Gbps Dedicated Connection khareeda. Iska matlab GPX data center mein AWS router ka ek 10 Gbps ka poora slot ab MatrixCorp ke naam par booked hai. Isme unhe maximum speed aur zero sharing milti hai.

**B. Hosted Connection**: Maan lijiye MatrixCorp ek chota startup hota aur unhe sirf 200 Mbps ki speed chahiye hoti. Ab AWS toh 1 Gbps se chota port deta nahi Dedicated mein. Aise mein kaam aate hain AWS Direct Connect Partners (jaise Tata ya Airtel). Yeh partners AWS se ek bada 10 Gbps ka dedicated port khud khareed lete hain aur usme se chote-chote tukde (slice) karke baaki companies ko rent par de dete hain. Isme aapko 50 Mbps se lekar 10 Gbps tak ki choti-badi koi bhi speed mil sakti hai.

MatrixCorp Example (Alternative): Agar MatrixCorp choti company hoti, toh woh Airtel ke paas jaati. Airtel apne pehle se lage hue AWS link mein se 200 Mbps ka ek logical raasta MatrixCorp ke liye allocate kar deta. Isko Hosted Connection kehte hain.

<br>

**3. ROUTING & LOGICAL COMPONENTS (Software Layer)**:

Physical wire lag gayi, port select ho gaya. Ab data ko sahi jagah pahunchane ke liye network routing setup karni padti hai. Iske bina wires mein data travel nahi karega.

**A. Virtual Interfaces (VIF)**:

Ek baar jab 10 Gbps ki physical line lag jaati hai, toh aap us ek single wire ke andar software ke threw alag-alag virtual tunnels (roads) bana sakte hain. In roads ko Virtual Interfaces (VIF) kehte hain. Yeh teen tarah ke hote hain aur inka concept samajhna sabse zaroori hai:

Physical link (Cross Connect) khada hone ke baad, data tab tak flow nahi karega jab tak aap Virtual Interfaces (VIF) nahi banate. VIF ka kaam yeh taiyar karna hai ki aapka physical link AWS ki kis service se baat karega. Yeh teen tarah ke hote hain:

- Private VIF (Virtual Interface):
  - Purpose: Agar aapko apne on-premises servers ko apne AWS ke VPC (Virtual Private Cloud) ke andar chal rahe private resources (jaise EC2 instances, RDS databases) se connect karna hai, toh Private VIF banta hai.
  - How it works: Yeh connection private IP addresses (jaise 10.0.0.0/16) par chalta hai. Internet se iska koi lena-dena nahi hota. Yeh Direct Connect ko Virtual Private Gateway (VGW) se jotta hai.
 
- Public VIF (Virtual Interface):
  - Purpose: AWS ki kuch services VPC ke andar nahi chalti, unke paas public IP addresses hote hain (jaise Amazon S3 storage, DynamoDB, Amazon SQS, ya AWS Management Console). Agar aapko in public services ko bina internet ke private line se access karna hai, toh Public VIF banta hai.
  - How it works: AWS apne saare public IP ranges ko aapke on-premises router par advertise kar deta hai. Jab aapka on-premise server S3 par data bhejega, toh woh internet standard routes par na jakar, Direct Connect ki private line se seedha AWS ki public service tak pahocha dega.
 
- Transit VIF (Virtual Interface):
  - Purpose: Agar aapke paas ek se zyada VPCs hain (jaise 10 ya 50 alag-alag VPCs hain alag-alag departments ke liye) aur aap un sabhi ko ek single Direct Connect line se jodhna chahte hain.
  - How it works: Yeh VIF AWS Transit Gateway (TGW) ke sath connect hota hai. Transit Gateway ek central hub ki tarah kaam karta hai jo saare VPCs ko aapas mein jotta hai, aur Transit VIF us hub ko aapke physical data center se jodh deta hai. Isse aapko har VPC ke liye alag-alag Private VIF banane ki zaroorat nahi padti.
 
<br>

**B. Direct Connect Gateway (DXGW - The Regional Bridge)**:

Yeh ek global AWS network component hai jo bohot bade kaam aata hai. Normal rule yeh hai ki agar aapki Direct Connect line Mumbai ke data center mein aakar judi hai, toh aap sirf Mumbai region ke VPCs se hi baat kar sakte hain. Lekin agar MatrixCorp ka ek backup VPC Singapore Region mein ya US Region mein chal raha hai, toh woh kya karenge? Kya wahan ke liye alag se wire bichhayenge? Bilkul nahi!

Wahan kaam aata hai Direct Connect Gateway (DXGW):
- Yeh ek central router ki tarah kaam karta hai jo AWS ke internal global network par baithta hai.
- Aap apni Mumbai wali Direct Connect line ko is DXGW se jor dete hain.
- Aur yeh DXGW aage chal kar alag-alag regions (Mumbai, Singapore, Virginia) ke VPCs ya Transit Gateways se jor diya jata hai.
- MatrixCorp Example: MatrixCorp ne ek DXGW banaya. Apne Mumbai wale Direct Connect connection ko is DXGW se link kiya. Ab is DXGW ke throwing, unhone Mumbai VPC aur Singapore VPC dono ko aikas mein jor diya. Ab MatrixCorp ka office ek hi physical wire se India aur Singapore dono jagah ke servers se bina internet ke communicate kar sakta hai.

<br>

**C. BGP Session (Border Gateway Protocol - The Language)**:

Wires lag gayi, VIFs ban gaye. Lekin MatrixCorp ke router aur AWS ke router ko aikas mein baatein bhi toh karni hain. Unhe ek doosre ko batana padega ki "Bhai, mere paas yeh wale IP addresses hain". Is networking language ko BGP (Border Gateway Protocol) kehte hain.
- Jab aap Direct Connect setup karte hain, toh aap dono routers ke beech ek BGP Session chalu karte hain.
- MatrixCorp ka router AWS ko bolta hai: "Mere paas office mein 192.168.1.0/24 ki range hai."
- AWS ka router bolta hai: "Mere paas cloud mein 10.0.0.0/16 ki range hai."
- Dono is data ko exchange karke apne paas routing table update kar lete hain. Agar kal ko MatrixCorp office mein koi naya server lagati hai, toh BGP protocol uski jankari automatically AWS ko de deta hai, kisi engineer ko manual entry nahi karni padti.

<br>

**DIRECT CONNECT KA REAL TRAFFIC JOURNEY (Full Example)**:

Ab saare components ko milakar dekhte hain ki jab MatrixCorp ka ek employee office mein baithkar AWS ke private server se data nikaalta hai, toh kya hota hai:
- **The Request**: Employee apne browser par enter karta hai 10.0.1.50 (AWS Private database).
- **Office Checkpoint**: Traffic pahunchta hai MatrixCorp ke Customer Gateway (Cisco Router) par.
- **BGP Route Lookup**: Router apni routing table check karta hai jo BGP ne update ki thi. Table bolti hai: 10.0.0.0/16 ka rasta Private VIF par hai.
- **The Physical Ride**: Data office se nikalta hai aur Airtel ki Last Mile Fiber Cable se hota hua Mumbai ke GPX Data Center (DX Location) mein enter karta hai.
- **The Joint**: GPX data center ke andar, data us physical wire se hota hua Cross Connect (Patch cord) ke threw AWS ke Direct Connect Router ke port mein chala jata hai.
- **The Cloud Bridge**: AWS router dekhta hai ki yeh traffic Direct Connect Gateway (DXGW) ki taraf mapped hai.
- **Destination Reached**: DXGW data ko AWS ke internal high-speed network se guzar kar MatrixCorp ke target VPC ke andar bheja deta hai, aur data safely EC2 instance (10.0.1.50) tak pahunch jata hai.
- Poore raste mein kahin bhi public internet ka 1% bhi use nahi hua. Ekdam solid, private, fast aur secure delivery!

<br>
<br>

### Direct Connect Kaise Work Karta Hai?

AWS Direct Connect (DX) ke kaam karne ke tarike ko poori detail mein, step-by-step ek aasan example ke saath samajhte hain.

Chaliye pehle ek Example (Scenario) maan lete hain taaki poori kahani aasan ho jaye:

```Maan lijiye: Aapki ek badi bank hai jiska naam hai "Apna Bank". Aapka apna ek private data center Mumbai mein hai. Ab aapne apne bank ka kuch data aur apps AWS Cloud (Mumbai Region) par shift kar diye hain. Aap chahte hain ki aapke Mumbai data center aur AWS ke beech ek aisi private line judi ho, jo public internet ka use na kare.```

Niche is setup aur working ko 6 steps mein poori detail mein samjhaya gaya hai:

<br>

**Step 1: Request aur DX Location ka Selection**:

Sabse pehle, Apna Bank ko yeh tay karna hoga ki unhe AWS se kahan connect hona hai. AWS ke har region ke paas kuch Direct Connect Locations (jise Colocation Facilities ya Partner Data Centers kehte hain) hote hain. Yeh facilities Equinix, Digital Realty jise bade vendors manage karte hain.
- **Kaam**: Apna Bank apne AWS Console mein jata hai aur ek Direct Connect Connection ki request karta hai.
- **Example**: AWS kehta hai ki Mumbai mein ek "Equinix Data Center" hai, jahan AWS ke routers pehle se lage hue hain. Apna Bank is location ko select kar leta hai.
- **Result**: AWS aapko ek document deta hai jise LOA-CFA (Letter of Authorization and Connecting Facility Assignment) kehte hain. Yeh ek tarah ka permission letter hai jo batata hai ki AWS ke router ke kaun se port par aapko apni cable lagani hai.

<br>

**Step 2: Physical Line Setup (The Last Mile)**:

Ab Apna Bank ko apne Mumbai data center se lekar us Equinix Data Center tak ek physical fiber-optic cable khinchwani hogi. Kyunki bank khud sadak khodkar cable nahi bicha sakti, isliye woh ek telecom company (jaise Airtel, Tata Communications, ya Jio) ko hire karti hai. Inhe Network Carrier kehte hain.
- **Kaam**: Telecom partner aapke data center se ek dedicated fiber line nikalta hai aur use Equinix Data Center (DX Location) tak pahunchata hai.
- **Speed Option**: Yeh line ya toh 1 Gbps, 10 Gbps, ya 100 Gbps ki capacity ki hoti hai.

<br>

**Step 3: Meet-Me-Room mein Cross-Connect (Asli Connection)**:

Jab telecom company ki cable Equinix Data Center ke andar pahunch jati hai, toh wahan ek jagah hoti hai jise Meet-Me-Room (MMR) kehte hain. Yeh woh kamra hai jahan saare networks aapas mein milte hain.
- **Kaam**: Equinix ke engineers aapka woh LOA-CFA document dekhte hain. Woh telecom partner ki bichi hui cable ka ek end uthate hain aur use AWS ke router ke specific port mein physical roop se plug kar dete hain.
- **Cross-Connect**: Is physical patch cable (yellow/blue fiber wire) jodne ki process ko hi Cross-Connect kehte hain.
- **Status**: Jaise hi yeh cable judti hai, AWS console par aapka connection status "Available" dikhane lagta hai. Iska matlab ab hardware level par dosti ho chuki hai.

<br>

**Step 4: Logical Configuration (Virtual Interfaces - VIF)**:

Sirf physical cable jodne se data transfer nahi hoga. Ab hume computer language mein configure karna hoga taaki data ko rasta mile. Iske liye hum Virtual Interfaces (VIF) banate hain. VIF do tarah ke hote hain:
- **Private VIF**: Agar Apna Bank ko AWS ke andar chal rahe apne private servers (VPC - Virtual Private Cloud) se connect hona hai (jaise EC2 instances ya Databases).
- **Public VIF**: Agar Apna Bank ko AWS ke un services se connect hona hai jo public hoti hain lekin unhe internet par nahi bhejna, jaise AWS S3 (Storage) ya DynamoDB.

Example: Apna Bank ek Private VIF banata hai taaki unka Mumbai data center unke AWS VPC se baat kar sake.

<br>

**Step 5: BGP (Border Gateway Protocol) Routing Setup**:

Yeh sabse important step hai jahan dono side ke routers ek dusre se baat karna shuru karte hain. Router ko kaise pata chalega ki kaun sa data cloud mein bhejna hai aur kaun sa office mein rakhna hai? Iske liye BGP (Border Gateway Protocol) routing configuration ki jaati hai.
- **Kaam**: Bank ka router aur AWS ka router aapas mein "IP addresses" exchange karte hain.

Example: Bank ka router AWS ko batata hai: "Mere paas IP range 10.0.0.0/16 hai." AWS ka router bank ko batata hai: "Mere paas cloud mein IP range 172.31.0.0/16 hai."

Ab dono routers ko rasta pata chal chuka hai. Jab bhi bank se koi employee cloud ke kisi server (172.31.X.X) ko access karega, toh router use internet par na bhejkar seedhe Direct Connect ki private line par daal dega.

<br>

**Step 6: Data Transfer (Real-Time Working)**:

Ab aapka setup poori tarah ready hai. Ab jab bhi Apna Bank ka koi customer ya employee koi transaction karta hai:
- Request Bank ke local data center ke router par aati hai.
- Router dekhta hai ki iska database toh AWS cloud par chal raha hai.
- Router us data ko local telecom line ke zariye Equinix DX Location bhejta hai.
- Equinix mein cross-connect cable se hote hue woh data AWS ke router mein jata hai.
- AWS apne private global network backhaul (high-speed fiber network) se us data ko milliseconds ke andar AWS Mumbai Region (Data Center) tak pahuncha deta hai.

Public internet ka is poore raste mein kahin bhi touch nahi hota, isiliye isme na toh traffic milta hai aur na hi hacking ka darr hota hai.

