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

