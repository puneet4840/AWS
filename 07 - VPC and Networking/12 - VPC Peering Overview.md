# VPC Peering Overview

VPC peering means to connect two or more VPCs with each other.

VPC Peering AWS ka ek feature hai jo do alag VPCs ko directly connect karta hai, taaki dono VPC ke resources private IP addresses ke through aapas mein communicate kar saken.

Yaani traffic internet par nahi jaata. Traffic AWS ke private backbone network ke andar hi rehta hai.

<br>

Jab organizations multiple VPCs ka use karti hain (jaise alag-alag departments, environments, ya microservices ke liye), toh unhe aapas mein data share karne ki zarurat hoti hai.

Isliye data ko privately access karne ke liye VPC peering use ki jaati hai.

<br>
<br>

### Sabse pehle problem samajhte hain (VPC Peering ki jarurat kyu padi)

Maan lo tumhari company AWS use karti hai. Company ne ek VPC banayi hai. Us VPC ka naam hai **Production-VPC**.

Uske andar database aur application chal rahi hai:
```
Production VPC

10.0.0.0/16

|

Application Server

Database
```
Sab kuch sahi chal raha hai.

Kuch mahine baad company ne ek aur application develop ki. Security reasons ki wajah se company ne decide kiya ki is application ko alag VPC mein rakha jayega.

Ab architecture kuch aisa ho gaya:
```
Production VPC

10.0.0.0/16


Application Server

Database


--------------------------------


Development VPC

192.168.0.0/16

Developer Server
```

Ab dono applications alag VPC mein hain.

<br>

**Ab problem shuru hui**:

Development Team ko Production Database se data read karna hai.

Developer ne production-vpc mein rakha hua database ping kiya.
```
ping 10.0.1.10
```

Developer ne SSH try ki.
```
ssh ec2-user@10.0.1.10
```
Wo bhi fail.

Ab sawal aata hai. Dono VPC AWS ke andar hi hain. Fir communication kyun nahi ho rahi?

<br>

**Reason kya hai?**

AWS mein har VPC ek completely isolated network hoti hai. Iska matlab hai ki ek VPC ko doosri VPC ke baare mein koi information nahi hoti.

AWS intentionally VPCs ko isolate karta hai. Ye security ke liye bahut important hai. Agar isolation na ho, to ek customer doosre customer ke network tak pahunch sakta tha.

Isliye har VPC apna alag private network hota hai.

<br>

**AWS ne kya socha?**

AWS Engineers ne socha.

Kai companies ke paas multiple VPCs hoti hain. Aur unhe aapas mein communicate karna hota hai.

Jaise:
- Production VPC.
- Development VPC.
- Testing VPC.
- Shared Services VPC.

Har baar NAT Gateway ya Internet use karna galat tha. Isliye AWS ne ek private connection introduce kiya.

Uska naam rakha **VPC Peering**.

<br>
<br>

VPC Peering se pehle ya iske bina, niche di gayi problems aati thi jinko solve karne ke liye VPC Peering ko banaya gaya:
- **High Cost Se Bachne Ke Liye**: Agar do VPCs aapas mein internet ke zariye baat karenge, toh AWS public data transfer charges lagata hai jo kaafi costly hota hai. Peering se yeh kharcha kam ho jata hai.
- **Better Security Ke Liye**: Internet par data bhejne se security ka risk rehta hai. VPC Peering mein saara traffic AWS ke private network ke andar hi rehta hai, jisse data leak nahi hota.
- **Low Latency (Fast Speed)**: VPC Peering mein Private AWS network par traffic travel karne ki wajah se data transfer speed bahut fast milti hai aur delay (latency) nahi hota.
- **Simple Management**: Bina kisi extra hardware ya software appliance ke, do networks ko aapas mein jodne ka yeh sabse seedha tareeka hai.


<br>
<br>

### VPC Peering kaise create hoti hai?

Maan lo tumhare paas do VPC hain, **VPC-A** aur **VPC-B**.
```
VPC-A

10.0.0.0/16


VPC-B

192.168.0.0/16
```

- Sabse pehle tum **VPC-A** se ek Peering Request bhejte ho **VPC-B** ko.
- AWS **VPC-B** ke owner ko notification bhejta hai.
- **VPC-B** ka owner us request ko Accept karta hai.
- Jaise hi request accept hoti hai. Ek private connection ban jata hai.

Lekin...

Abhi bhi in dono VPCs ki communication apas mein nahi hogi.

**Kyun nahi hogi?**

Sirf Peering create karne se traffic automatically nahi chalti.
- Dono VPCs ke Route Tables mein ek entry add karni hoti hai jo traffic ko peer connection ki taraf point kare.
- Security Groups aur Network ACLs ke rules ko update karna hota hai taaki dusre VPC se aane wale traffic ko permission mil sake.

Example.

VPC-A Route Table
```
Destination

192.168.0.0/16

↓

VPC Peering Connection
```

Aur

VPC-B Route Table
```
Destination

10.0.0.0/16

↓

VPC Peering Connection
```

Ab dono VPC ko pata chal gaya ki doosri VPC tak pahunchne ka rasta Peering Connection hai.

<br>
<br>

### Key Features aur Rules

**No Transitive Peering**: Agar VPC A connected hai VPC B se, aur VPC B connected hai VPC C se, toh VPC A aur VPC C aapas mein baat nahi kar sakte. Unke beech alag se peering karni hogi.

**No Overlapping CIDR**: Jin do VPCs ko aap peer kar rahe hain, unka IP address range (CIDR block) same ya overlap nahi hona chahiye.

**Cross-Region & Cross-Account**: Aap same AWS account ya alag AWS accounts ke VPCs ko peer kar sakte hain. Aur alag-alag AWS Regions ke beech mein bhi VPC peering kar sakte hain.

<br>
<br>

### Packet Flow kaise hota hai?

Maan lo.

VPC-A ka EC2

IP
```
10.0.1.10
```

VPC-B ke EC2 ko ping karta hai.
```
192.168.1.20
```

Packet flow.
```
EC2

↓

Route Table

↓

VPC Peering

↓

Route Table

↓

Destination EC2
```

Dhyan do.

Yahan:
- NAT Gateway nahi hai.
- Internet Gateway nahi hai.
- Public IP nahi hai.

Sab kuch Private IP se ho raha hai.

<br>
<br>

### Security Groups ka role

Bahut log sochte hain "Peering ho gayi." "Ab communication automatically ho jayegi."

Nahi.

Security Group abhi bhi apply hoga. 

Maan lo VPC-B ke EC2 mein SSH allow hi nahi hai. To ping ya SSH fail ho sakta hai.

Isliye VPC Peering sirf network path provide karta hai. Permission Security Group aur NACL decide karte hain.

<br>
<br>

### Kya Internet Gateway use hota hai?

Nahi.

VPC Peering mein traffic kabhi bhi Internet Gateway se nahi jaati. Traffic AWS ke private backbone network ke andar hi travel karti hai.

Isliye communication secure hoti hai.

<br>
<br>

### Cross Region VPC Peering

Maan lo ek VPC Mumbai Region mein hai. Aur doosri Frankfurt Region mein.

```
Mumbai

↓

VPC-A


Frankfurt

↓

VPC-B
```

AWS Cross-Region VPC Peering bhi support karta hai. Isme bhi traffic public internet par nahi jaati. Traffic AWS ke global private backbone network ke through travel karti hai. Isliye secure aur encrypted communication hoti hai.

<br>
<br>

### VPC Peering ki sabse badi limitation

Ab maan lo teen VPC hain.
```
VPC-A

|

VPC-B

|

VPC-C
```

A aur B ke beech Peering hai.

B aur C ke beech bhi Peering hai.

Ab sawal.

Kya A directly C se baat kar sakta hai?

Answer.

**Nahi**.

Isko bolte hain **No Transitive Routing**.

VPC-A kabhi VPC-C se communicate nahi kar sakta. Iske liye tumko VPC-A aur VPC-C dono beech mein alag se peering karni padegi, jab jake communication ho payegi.

<br>
<br>

### Real Company Example

Maan lo ek company ke paas 10 VPC hain. Har VPC ko baaki 9 VPC se communicate karna hai. Agar VPC Peering use karenge. To bahut saari Peering Connections banani padengi.

Diagram.
```
A

|\

| \

|  \

B---C

 \ /

  D
```

Connections bahut zyada ho jaayengi. Isko Network Mesh bolte hain.

Ye manage karna difficult ho jata hai.

Isi problem ko solve karne ke liye AWS ne baad mein **Transit Gateway** introduce kiya.

<br>
<br>

### VPC Peering ki Conditions

VPC Peering banane se pehle ek important condition hoti hai **Dono VPC ke CIDR Blocks overlap nahi hone chahiye**.

Example.

VPC-A
```
10.0.0.0/16
```
VPC-B
```
10.0.0.0/16
```

In dono VPCs ke paas same CIDR range hai, isliey Ye VPC Peering nahi banegi.

Reason.

AWS ko samajh hi nahi aayega ki packet kis VPC mein bhejna hai. Isliye hamesha unique CIDR Blocks use kiye jaate hain.

<br>
<br>

### VPC Peering kab use karte hain?

**1 - Jab VPCs Ki Ginti Kam Ho (Small Scale)**:

Jab aapke paas connect karne ke liye sirf 2 se 5 VPCs hon. To aap VPC peering ka use kijiye.

Kyu?
- Kam VPCs hone par connections ko manage karna aasan hota hai. Agar 50-100 VPCs ho jayein, toh peering mushkil ho jati hai aur tab Transit Gateway use karna chahiye.

<br>

**2 - Jab Full Network Access Chahiye Ho**:

Jab do VPCs ke saare resources (jaise EC2 instances, Databases) ko aapas mein bina kisi restriction ke baat karni ho. Yeh pure CIDR block ke traffic ko route karta hai, na ki sirf kisi ek specific service ko.

<br>

**3 - Jab Low Latency Aur High Speed Chahiye Ho**:

Jab aapko do VPCs ke beech bade files transfer karne hon ya real-time data sync karna ho. Traffic AWS ke internal physical network par chalta hai, isliye isme koi network bottleneck (traffic jam) nahi hota aur speed maximum milti hai.

**4 - Jab Budget/Cost Ka Khayal Rakhna Ho**:

VPC Peering connection banane ka koi extra hourly charge nahi hota. 

AWS VPC Peering banana aur set up karna bilkul free hota hai। Lekin, iske upar hone wale data transfer par charges lagte hain.

Data Transfer Charges Kab Lagte Hain?
- Same Availability Zone (AZ): Agar dono VPC ke resources ek hi AZ ke andar hain, toh data transfer free hota hai.
- Different Availability Zones (AZ): Agar data transfer alag-alag AZs ke beech hota hai, toh standard Inter-AZ data transfer charges lagte hain.
- Different Regions (Inter-Region): Agar aap do alag AWS Regions (jaise Mumbai aur N. Virginia) ke VPCs ko peer karte hain, toh standard Inter-Region data transfer rates lagate hain।
