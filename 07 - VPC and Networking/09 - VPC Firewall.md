# VPC Firewall

AWS VPC (Virtual Private Cloud) mein security ke liye do alag tarah ke firewalls hote hain. Yeh dono firewalls aapke data aur servers ko safe rakhte hain.

Inke naam hain:
- **Security Groups (SGs)**.
- **Network Access Control Lists (NACLs)**.

Sabse pehle hum Security Groups ke baare mein samjhenge for NACL ke baare mein samjhenge.

<br>
<br>

## Security Groups (SGs)

<br>

### Security Group kya hai?

Security Group ek virtual firewall hai jo ec2 instance ke ander aane wale aur bahar jane wale traffic ko control karta hai. Matlab incoming aur outgoing traffic ko control karta hai.

Yeh firewall EC2 Instance ke network interface (ENI - Elastic Network Interface) ke saath attach hota hai.

Iska kaam har aane aur jaane wale network packet ko check karna hota hai.

Har packet aane se pehle woh poochta hai:
- Tum kaun ho?
- Kis port par ja rahe ho?
- Kis protocol ka use kar rahe ho?
- Kya tumhari permission hai?

Agar rule match ho gaya, to packet andar chala jayega. Agar rule match nahi hua, to packet wahin drop kar diya jayega.

Security Group sirf EC2 instance ke liye nahi hota hai. Yeh AWS ke bohot saare doosre resources ke liye bhi use hota hai.

Aap aise samajh sakte hain ki AWS mein jis bhi resource ke paas ek Network Interface (ENI) hota hai ya jo resource aapke VPC ke andar private network par kaam karta hai, usko secure karne ke liye Security Group ka hi use kiya jaata hai.

<br>

**Kaun-kaun se resources Security Groups use karte hain?**

EC2 ke alawa, yahan kuch main AWS resources hain jo Security Groups ka use karte hain:
- Load Balancers (ALB / NLB): Jab internet se traffic aata hai, toh sabse pehle Load Balancer par takrata hai. Security Group se hum control karte hain ki internet se kaun sa traffic Load Balancer ke andar aayega.
- Databases (AWS RDS): Aapka database secure hona chahiye. RDS databases par Security Group lagakar hum rule set karte hain ki sirf hamara EC2 instance ya backend server hi database se connect kar sake, baaki koi nahi.
- Cache Servers (Amazon ElastiCache): Redis ya Memcached jaise caching servers ko secure karne ke liye bhi iska use hota hai.
- Container Services (Amazon ECS / EKS): Agar aap Docker containers chala rahe hain, toh un containers ke tasks aur pods par bhi Security Groups lagaye jaate hain.
- Serverless Functions (AWS Lambda): Jab ek Lambda function ko aapke VPC ke andar ke resources (jaise RDS database) se baat karni hoti hai, toh Lambda function ko bhi Security Group assign kiya jaata hai.

<br>
<br>

### Security Group ki jarurat kyu padi?

Jab hum AWS mein koi EC2 Instance launch karte hain, to sabse pehle ek sawal aata hai.

**"Is server ko kaun access kar sakta hai?"**

Socho tumne ek EC2 launch kiya.

Uska Public IP hai.
```
43.204.120.50
```

Ab ye server internet par visible hai. Ab poori duniya ke log is server ko request bhej sakte hain.

Ab sawal ye hai ki:
- Kya har koi SSH kar sakta hai?
- Kya har koi website open kar sakta hai?
- Kya har koi Database access kar sakta hai?

Agar AWS kisi bhi request ko bina check kiye server tak pahucha de, to koi bhi attacker tumhare server par attack kar sakta hai. Isi problem ko solve karne ke liye AWS ne Security Group introduce kiya.

<br>
<br>

### Security Group kis level par kaam karta hai?

Security Group Subnet level par nahi lagta.

Security Group VPC level par bhi nahi lagta.

Security Group EC2 Instance ke **Network Interface (ENI)** par lagta hai.

Diagram dekho:
```
VPC

    |

Public Subnet

    |

EC2 Instance

    |

Network Interface (ENI)

    |

Security Group
```
Yaani jab koi packet EC2 tak pahunchne wala hota hai, tab sabse pehle us packet ko Security Group check karta hai.

<br>
<br>

### Security Group ka main kaam kya hai?

Security Group ke do major sections hote hain.

**Inbound Rules**:

Inbound Rules decide karte hain ki bahar se kaunsi traffic server ke andar aa sakti hai.

Matlab:
```
Internet

↓

EC2
```
Ye allow hoga ya nahi?

<br>

**Outbound Rules**:`

Outbound Rules decide karte hain ki server bahar kis jagah request bhej sakta hai.

Matlab:
```
EC2

↓

Internet
```
Ye allow hoga ya nahi?

<br>

**Example**:

Suppose tumhare EC2 par ek website chal rahi hai. Tum chahte ho ki duniya ka koi bhi user website open kar sake. Lekin SSH sirf tum kar sako.

To Security Group kuch aisa hoga.

Inbound Rules
```
HTTP

Port 80

Source

0.0.0.0/0
```

Matlab duniya ka koi bhi user website open kar sakta hai.

Aur
```
SSH

Port 22

Source

My IP
```

Matlab sirf tumhare laptop ka IP SSH kar sakta hai. Baaki duniya SSH nahi kar sakti.

<br>
<br>

### Packet Level par kya hota hai?

Suppose tum browser mein likhte ho
```
http://43.204.120.50
```
Request AWS tak pahunchti hai.

Ab AWS seedha request EC2 ko nahi bhejta. Sabse pehle Security Group check hota hai.

Security Group dekhta hai:

Destination Port?
```
80
```

Rule check karta hai.
```
HTTP

Port 80

Allowed
```
Packet allow. Ab request EC2 tak pahunch gayi.

<br>

Ab maan lo koi hacker SSH karna chahta hai.
```
ssh ec2-user@43.204.120.50
```
Source IP
```
101.50.80.20
```
Security Group check karega.

SSH Rule
```
Allowed

↓

Sirf

100.25.10.1
```

Current IP
```
101.50.80.20
```
Match nahi hua. Packet drop. Server ko pata bhi nahi chalega ki kisi ne SSH try kiya tha.

<br>
<br>

### Security Group Deny Rule kyun nahi rakhta?

Security Group mein sirf **Allow** Rules hote hain. Kabhi bhi Deny Rule nahi hota.

**Ab sawal aata hai ki Deny Rule kyun nahi hai?**

AWS ne design hi aisa banaya hai. Security Group ka logic hai:
- "Jo explicitly allow hai wahi chalega. Baaki sab automatically block ho jayega."

Example

Agar tumne sirf
```
HTTP

Port 80
```
allow kiya hai.

To:
- SSH
- FTP
- SMTP
- Database

sab automatically blocked hain. Tumhe alag se deny likhne ki zarurat hi nahi.

<br>

Security Group mein Deny Rules kyun nahi hote, iske peeche AWS ka ek bohot hi socha-samjha architectural aur performance-based reason hai. AWS ne isko janbujhkar aisa design kiya hai taaki network management simple, fast aur secure rahe.

Iske 3 sabse bade reasons hain:

- **Performance aur Speed (Fast Processing)**: Firewalls do tarah se kaam karti hain. Ek hoti hai jo top-to-bottom saare rules ek-ek karke check karti hai (jaise NACL), aur doosri jo saare rules ko ek sath parallel process karti hai.
  - Agar Security Group mein Allow aur Deny dono rules hote, toh AWS ko har packet ke aane par poori list ko sequence mein check karna padta ki kahin koi Deny rule toh nahi laga hai. Isse network latency (delay) badh jati.
  - Sirf Allow rules hone ka fayda: AWS saare allow rules ko ek sath (parallel) evaluate kar leta hai. Jaise hi koi matching allow rule milta hai, traffic instantly pass ho jata hai. Agar koi rule nahi milta, toh bina time waste kiye packet drop ho jata hai. Isse processing speed bohot fast ho jati hai.
 
- **Simplicity aur Human Error se Bachna (Conflict Prevention)**: Agar ek hi jagah Allow aur Deny dono rules honge, toh rules aapas mein takrayenge (conflict paida hoga).
  - Example: Maan lijiye aapne ek rule banaya: "Allow all traffic from ```10.0.0.0/16```" (Poore network ko allow karo). Phir doosra rule banaya: "Deny traffic from ```10.0.1.5```" (Is ek IP ko block karo).
  - Aise mein firewall confuse ho sakti hai ki pehle kiski baat maane—Allow ki ya Deny ki? System administrators se aksar aisi galtiyan ho jati hain jisse security mein lopholes ban jate hain
  - AWS ne Deny ka option hi hata diya taaki koi confusion hi na rahe. Aapne jo likha hai, bas wahi allow hoga, baaki sab band.
 
- **Separation of Concerns**: AWS networking ko layers mein divide karta hai (Defense in Depth). AWS chahta hai ki alag-alag security tools ka apna ek clear aur specific kaam ho:
  - Security Group (Micro-level Security): Iska kaam hai sirf aapke instance ko ek safe boundary dena. Yeh Whitelist model par kaam karta hai—yaani aap apne application ke hisab se batao ki use kiski zaroorat hai (jaise Port 80 ya Port 22), aur bas utna hi rasta kholo.
  - Network ACL (Macro-level Security): Iska kaam poore subnet (pure area) ko protect karna hai. Isliye AWS ne Allow aur Deny dono ka options NACL ko diya hai. Agar kisi hacker ka IP block karna hai, toh use poore subnet ke level par hi (NACL par) rok diya jata hai, taaki woh EC2 instance ke Security Group tak pahunch hi na paye.
 
<br>
<br>

### Security Group Stateful hota hai

Yeh 'Stateful' hote hain iska matlab hai ki agar aapne koi request andar aane di (Inbound), toh uska response bahar (Outbound) apne aap jaa sakega. Aapko alag se permission nahi deni hogi.

Stateful ka matlab hota hai: Security Group connection ko yaad rakhta hai.

**Example samajho**.

Tumhare laptop ne EC2 ko request bheji.
```
Laptop

↓

EC2
```
Security Group ne request allow kar di.

Ab EC2 response bhejega.
```
EC2

↓

Laptop
```
Ab kya Outbound Rule check hoga?

Nahi.

Kyun?

Kyuki Security Group ko yaad hai ki ye response usi connection ka part hai jo pehle allow hua tha. Isi ko Stateful kehte hain.

<br>
<br>

### Security Group Multiple Instances par lag sakta hai

Ek Security Group ko sirf ek EC2 tak limited mat samajhna. Suppose tumhare paas 50 Web Servers hain. Tum sab par ek hi Security Group attach kar sakte ho. Kal tum HTTP ka naya rule add karte ho. Wo automatically sabhi 50 servers par apply ho jayega. Isliye Security Groups reusable hote hain.

<br>
<br>

### Default Outbound Rule

Jab tum naya Security Group banate ho, AWS by default ek Outbound Rule add karta hai.
```
All Traffic

Destination

0.0.0.0/0
```
Iska matlab hai ki EC2 kahin bhi request bhej sakta hai. Lekin agar tum security ko aur strict banana chaho, to is rule ko remove karke sirf required destinations allow kar sakte ho.

<br>
<br>

### Security Group Ke Kuch Zaroori Rules Aur Features

- Jab bhi aap security group mein koi naya rule add ya delete karte hain, toh woh turant live ho jata hai. Iske liye instance ko restart karne ki zaroorat nahi padti.

Default Setting:
- Jab aap ek naya Security Group banate hain, toh usme Inbound traffic poori tarah block hota hai aur Outbound traffic poori tarah open (0.0.0.0/0) hota hai.
- Ek VPC ke sath ek Default Security Group pehle se aata hai, jisme usi group ke baaki resources se aane wala traffic inbound mein allow hota hai.

- SGs per Network Interface (ENI): Ek instance ya ENI par aap maximum 5 Security Groups ek sath laga sakte hain.

<br>
<br>
<br>

## NACL (Network Access Control Lists)

Network ACL ek Subnet Level Firewall hai jo decide karta hai ki subnet ke andar kaunsi traffic enter karegi aur kaunsi bahar jayegi.

NACL subnet level par incomming aur outoging traffic ko control karta hai. Ye subnet se attach hota hai. Subnet ke andar aane aur wahan se bahar jaane wale har ek data packet ko NACL se guzarana hi padta hai.

Network ACL (Network Access Control List), jise short mein NACL kehte hain, AWS VPC ke andar subnet level par kaam karne wala ek virtual firewall hai.

Security Group ek ec2 instance level firewall hai lekin NACL ek subnet level firewall hai, ye hi in dono mein difference hai.

<br>
<br>

### NACL ki jarurat kyu padi?

Pichle topic mein humne Security Group padha tha. Security Group EC2 Instance ko protect karta hai.

Maan lo tumhare paas ek Public Subnet hai.

Uske andar teen EC2 Instances chal rahe hain.
```
VPC

|

Public Subnet

|

---------------------------------

|               |              |

EC2-1          EC2-2         EC2-3

(Web)          (App)         (Test)
```

Ab har EC2 ke paas apna Security Group laga hua hai. Sab kuch sahi chal raha hai. Lekin AWS Engineers ne ek problem notice ki. Socho agar isi subnet ke andar 100 EC2 Instances hain.

Har EC2 ka alag Security Group hai.

Ab company bolti hai:
- "Hum nahi chahte ki is subnet mein internet se SSH traffic aaye."

Ab kya karoge?

100 EC2 hain. 100 Security Groups edit karoge?

Bahut difficult hai.

AWS ne socha

"Ki hum Subnet level par hi ek firewall bana dete hain, jo poore subnet ke sabhi ec2 instances par rule apply karega."

Isi problem ko solve karne ke liye AWS ne **Network ACL (NACL)** banaya.

<br>
<br>

### NACL kis level par lagta hai?

Security Group Instance Level par attach hota hai.

NACL Subnet Level par attach hota hai.

Diagram:
```
VPC

|

Subnet

|

NACL

|

-------------------

|                 |

EC2             EC2

|

SG             SG
```

Yaani subnet mein enter hone wali har traffic pehle NACL se guzregi. Uske baad hi EC2 tak jayegi.

<br>
<br>

### NACL exactly karta kya hai?

Socho internet se ek packet aa raha hai.
```
Internet

↓

AWS
```
Packet sabse pehle kis jagah jayega?

**NACL**.

NACL packet ko dekhega. Wo check karega.
- Source IP kya hai?
- Destination Port kya hai?
- Protocol kya hai?
- Rule Number kya bol raha hai?

Agar rule allow karega to Packet Subnet ke andar enter karega.

Agar rule deny karega to Packet wahin drop ho jayega. EC2 tak packet pahunch hi nahi payega.

<br>

**Example**:

Suppose tumhare paas ek Public Subnet hai. Usme Web Server hai. Tum chahte ho ```HTTP``` chale lein ```SSH``` na chale.

To NACL Rules kuch aise honge.

Inbound RUle-1
```
Rule 100

Allow

HTTP

Port 80

Source

0.0.0.0/0
```

Inbound Rule-2
```
110

Deny

SSH

22

0.0.0.0/0
```

Ab kya hoga? Website chalegi lekin SSH nahi chalega. Packet Security Group tak bhi nahi pahunch paayega. NACL gate par hi rok dega.

<br>
<br>

### Rule Number kya hota hai?

Security Group mein Rule Number nahi hota.

Lekin NACL mein rule number hota hai.

Rule number ka matlab hai ki konse rule ki priority jyada hai, matlab konsa rule pehle check hokar apply hoga.

NACL ke rules ek sequence (tarteeb) mein check hote hain, jise Rule Number (jaise 100, 200, 300) se tay kiya jata hai. AWS rules ko sabse chote number se check karna shuru karta hai. Jaise koi matching rule mil jata hai, wahi faisla (Allow ya Deny) apply ho jata hai, aur uske baad ke baaki rules ko check nahi kiya jata.

**Example**:
```
Rule 100

Allow HTTP

Rule 110

Allow HTTPS

Rule 120

Deny SSH

Rule 130

Allow MySQL
```

Ab sawal AWS pehle kaunsa Rule check karega?

Answer: Sabse chhota Rule Number.

Matlab NACL mein sabse chote number ki highest priority hoti hai, sabse chota number utni jyada priority.

Yaani
```
100

↓

110

↓

120

↓

130
```

Jaise hi koi Rule match ho gaya. Baaki Rules check hi nahi honge.

<br>

**Example**:

Rule
```
100

Allow

All Traffic
```

Rule
```
200

Deny

SSH
```

Ab SSH packet aaya. AWS kya karega? Pehle Rule 100 dekhega. Usme likha hai.

Allow All.

To Packet allow ho jayega.

Lekin Rule 200 tak pahunch hi nahi paayega. Isi liye Rule Order bahut important hota hai.

Matlab ek baar koi rule allow ho gya to baaki koi rule check nhi hoga.

<br>
<br>

### NACL Allow aur Deny dono support karta hai

Security Group sirf Allow support karta hai.

NACL allow bhi karta hai Deny bhi karta hai.

Security Group ke ult, NACL mein aapko Allow aur Deny dono tarah ke rules likhne ka option milta hai. Iska sabse bada fayda yeh hai ki agar aapko kisi hacker ya malicious IP address ko block karna hai, toh aap use yahan seedhe Deny kar sakte hain.

Example
```
Rule 100

Deny

IP

45.23.10.5
```
Ab agar ye Hacker ka IP hai. To packet Subnet ke andar hi nahi aa paayega.

<br>
<br>

### NACL Stateless hota hai

NACL poori tarah se Stateless hota hai. Iska matlab hai ki yeh koi state maintain nahi karta. Agar aapne kisi traffic ko Inbound (andar aane) ke liye allow kiya hai, toh woh apne aap bahar nahi ja sakta. Aapko Outbound (bahar jaane) ke liye alag se manually rule banana padega.

Example:

Laptop ne request bheji.
```
Laptop

↓

Web Server
```
Inbound Rule

Allow.

Packet andar chala gaya.

Ab Web Server response bhej raha hai.
```
Web Server

↓

Laptop
```
Ab kya Outbound Rule check hoga?

**YES**

Kyun? Kyuki NACL Stateless hai. Usko yaad hi nahi hai ki request pehle allow hui thi. Isliye response bhejne ke liye bhi Outbound Rule hona compulsory hai.

<br>

**Example**:

Inbound Rule
```
Allow

HTTP
```
Lekin Outbound Rule Nahi hai.

Ab Browser website open karega?

Nahi.

Request andar aa jayegi. Lekin response bahar nahi ja paayega. Website load nahi hogi.

Isi liye NACL mein Inbound aur Outbound dono Rules carefully banana padte hain.

<br>
<br>

### Default Settings

**Default NACL**: Jab aap ek naya VPC banate hain, toh uske sath ek Default NACL aata hai. Yeh default roop se sare Inbound aur Outbound traffic ko Allow karta hai.

**Custom NACL**: Agar aap khud koi naya NACL banate hain, toh woh default roop se sare Inbound aur Outbound traffic ko Block (Deny) karta hai, jab tak aap khud rules na add karein.

