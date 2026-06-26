# Overview of Subnet

Jab hum AWS mein VPC (Virtual Private Cloud) create karte hain, tab hum ek private network create karte hain. Lekin sirf VPC banana kaafi nahi hota.

VPC ek bahut bada network hota hai. Is bade network ke andar hum chhote-chhote network banate hain. Inhi chhote networks ko Subnet kehte hain.

<br>

Subnet ka matlab hai, ek bade network ko multiple small network mein divide kar dena.

A subnet is the small network divided form a large network.

<br>

Subnet ka matlab hai "Sub-Network". Jab aap ek bade VPC network ko chhote-chhote hisson (chunks) mein todte hain, toh har ek hisse ko Subnet kehte hain.

Subnetting is the process of creating a subnet.

Example: Suppose there is a network in a company, it has multiple departments like Finance, Payroll, Sales and Development. If a company assign a single complete network to all these departments, If any employee from from sales department access malicious website and hacker got that device access through that malicous website. Now hacker can access all those device which are in the same network. This can be very dangerous situation for the company.

To avoid this subnetting is being used.

<br>
<br>

### Types of Subnet in AWS

AWS VPC mein 2 types ke subnet hote hain:
- Public Subnet.
- Private Subnet.

<br>

**Public Subnet**:

Public subnet vo subnet hota hai jiske paas internet access hota hai. Matlab public subnet mein servers directly internet access kar sakte hain.

Public Subnet AWS VPC ka woh hissa hai jo directly internet se juda hota hai.

<br>

**Private Subnet**:

Private subnet vo subnet hota hai jiske pas direct internet access nahi hota. Matlab private subnet mein server internet access nahi kar sakte aur unke paas public ip nhi hoti, sirf private ip hoti hai.

Agar tumko private subnet mein server ko internet access dena hai to tumko **NAT Gateway** use karna padega, jisse sirf servers se request internet par ja sakti hai lekin koi request internet se server nahi aa sakti.


<br>

**Note**:

AWS ke VPC mein jab bhi hum ek subnet create karte hain, to vo subnet by default private subnet hota hai. Us subnet ko public subnet banane ke liye vpc mein **Intenet Gateway**, **Route Table** ka use karke us subnet tak internet pahuchana hoga, tab hi vo subnet ek public subnet ban payega.

Nahi to jo bhi hum ek normal subnet vpc mein create karte hain vo ek private subnet hi hota hai.

<br>
<br>

### Subnet Ki Jarurat Kyu Padi?

Socho tumhare paas ek application hai.

Application mein 3 components hain:

**1. Web Server**:

Users internet se access karenge.

Example:
- Nginx
- Apache
- React Frontend

**2. Application Server**:

Business logic handle karega.

Example:
- NodeJS
- Java
- Python

**3. Database Server**:

Data store karega.

Example:
- MySQL
- PostgreSQL
- MongoDB

Ab kya tum database ko internet par expose karna chahoge? ```Bilkul nahi```.

Database sirf application server se baat kare. Public users database tak na pahunch sake.

Isi requirement ko solve karne ke liye subnet use hote hain.

<br>

**Ek Basic Architecture**:
```
VPC
│
├── Public Subnet
│      │
│      └── Web Server
│
└── Private Subnet
       │
       ├── Application Server
       │
       └── Database Server
```

Yahan:
- Public Subnet internet se accessible hai.
- Private Subnet internet se directly accessible nahi hai.

Isse security improve hoti hai.

<br>
<br>

### CIDR Aur Subnet Ka Relation

Subnet CIDR Block ke through create kiya jata hai.

Maan lo VPC ka CIDR hai:
```
10.0.0.0/16
```

Iska matlab:

VPC ke paas total IP range hai:
```
10.0.0.0 - 10.0.255.255
```

Ab is bade network ko hum chhote pieces mein divide kar sakte hain.

Example:
```
10.0.1.0/24
10.0.2.0/24
10.0.3.0/24
10.0.4.0/24
```
Ye sab subnets hain.

<br>

**CIDR Division Example**

VPC
```
10.0.0.0/16
```
Subnet-1
```
10.0.1.0/24
```
Subnet-2
```
10.0.2.0/24
```
Subnet-3
```
10.0.3.0/24
```
Subnet-4
```
10.0.4.0/24
```
Sab subnet VPC ke andar hi hain. Lekin sabki IP range alag hai. Isliye conflict nahi hota.

<br>
<br>

### AWS Mein Subnet Hamesha Ek Availability Zone Mein Hota Hai

**VPC**: Multiple Availability Zones mein spread ho sakta hai.

VPC khud kisi ek Availability Zone (AZ) mein nahi banta. VPC poore Region (jaise Mumbai) ke level par banta hai. Jab aap Mumbai mein ek VPC banate hain, toh vo Mumbai ke sabhi AZs ko cover kar leta hai. Us VPC ke andar multiple AZs ka faayda uthane ke liye hume Subnets ka hi istemaal karna padta hai.

Lekin

**Subnet**: Sirf ek Availability Zone mein exist karta hai.

Iska bohot seedha matlab yeh hai ki ek Subnet ki physical boundary sirf ek hi building (Data Centre) tak limited hoti hai. Ek single Subnet kabhi bhi do alag-alag jagaho par split (banta) nahi ho sakta.

- Jab aap ek Subnet banate hain, toh aap usko IP addresses ka ek chota group (jaise ```10.0.1.0/24```) dete hain.
- AWS ya kisi bhi cloud mein Subnet banate waqt aapko compulsory select karna padta hai ki yeh Subnet kaun se AZ (jaise ```ap-south-1a```) mein rahega.
- Agar aapne Subnet-1 ko Mumbai ke AZ-1a mein banaya hai, toh us Subnet ke andar aap jitne bhi servers (EC2) khade karenge, vo saare physical roop se sirf AZ-1a ki building ke andar hi chalenge.
- Reserved IPs: AWS har subnet ke pehle 4 aur aakhri 1 IP address (Total 5 IPs) ko apne internal kaam ke liye reserve rakhta hai.

Example:
```
Mumbai Region
│
├── ap-south-1a
│      └── Subnet-A
│
├── ap-south-1b
│      └── Subnet-B
│
└── ap-south-1c
       └── Subnet-C
```

Ek Subnet sirf ek Az mein hi ho sakta hai, ek subnet multiple AZs mein nahi ho sakta.

<br>

**AWS Ne Aisa Kyu Kiya?**:

High Availability ke liye.

Maan lo:
```
AZ-A Down Ho Gaya
```
Agar tumhara poora application AZ-A mein tha to application down ho jayega.

Lekin agar:
```
Web Server → AZ-A

Application Server → AZ-B

Database → AZ-C
```

To ek AZ fail hone par bhi application survive kar sakta hai. Isi concept ko Multi-AZ Architecture kehte hain.


<br>

**Yahan tak humne padha ki ek VPC mein public aur private subnet hote hain, Ab aage dekhte hain ki inko setup karne ke liye kon kon se components ka use kiya jata hai**:

Uper humne dekha ki ek vpc mein public aur private do tarah se subnet create kiye ja sakte hain, lekin by default jab hum vpc mein koi subnet create karte hain to vo subnet by default ek private subnet hota hai. Us subnet mein koi internet access nhi hota. Ye AWS ka security feature hai ki normal subnet private subnet hota hai, usko public subnet banane ke liye vpc mein aur bhi alag-alag components hote hain jinka use kiya jata hai jisse subnet mein internet acces ho pata hai.

Ab aage hum unhi concepts ko clear karenge.

<br>
<br>

Jab aap VPC ke andar koi naya subnet banate hain, to woh by default ek Private Subnet hota hai. Usko Public Subnet mein badalne ke liye hi hum Internet Gateway aur Route Table ka use karte hain. Ab hum aage Route Table aur Internet Gateway ko samjhenge.

<br>
<br>

### Sabse Pehle Samjho Route Kya Hota Hai?

Networking mein Route ka matlab hota hai: **Traffic ko kis direction mein bhejna hai**.

Example:

Agar EC2 instance kisi website ko access karna chahta hai: ```www.google.com```

To packet ko pata hona chahiye:
- Kahan jana hai?
- Kaunse gate se jana hai?

Ye information Route Table deti hai.


VPC ke andar jo bhi communication hota hai, wo IP Routing ke through hota hai. Network mein koi bhi packet randomly travel nahi karta. Har packet ke paas ek Destination IP Address hota hai.

Example:

EC2 Instance
```
Private IP
10.0.1.10
```
Ye Google ko access karna chahta hai.

Google ka IP maan lo
```
142.250.193.78
```
Ab EC2 ke operating system ko pata hai ki destination IP kya hai. Lekin usko ye nahi pata ki Packet ko bhejna kidhar hai? Isi point par Route Table ka role shuru hota hai.

<br>
<br>

### Route Table Kya Hoti Hai?

Route Table ek table ya ek database hoti hai jisme multiple routing rules stored hote hain.

Route table ek set of rules hote hain.

Ye batati hai:
- Agar traffic is network ke liye hai.
- To usko is destination par bhejo.

Route table ka primary kaam hota hai, apke VPC mein subnet ke ander rakhe hu EC2 ya server se nikalne wale traffic ko route karna hota hai.

Matlab apke vpc mein subnet ke ander rakhe hue server se nikalne wale traffic ko uski sahi jagah par pahuchana.

Route table mein har rule ke do important parts hote hain.
- **Destination**: Aapko kahan jana hai (IP address ya range). Ye packet mein likha hua destination address hota hai.
- **Target**: Packet ko next kahan behjna hai (Internet Gateway, NAT Gateway, Local, etc.).

| Destination | Target           |
| ----------- | ---------------- |
| 10.0.0.0/16 | local            |
| 0.0.0.0/0   | Internet Gateway |

Uper likhi hue destination aur target ka matlab hai ki:

- Agar apke data packet mein destination ip ye hai ```10.0.0.0/16``` to is data packet ko ```local``` yani same vpc mein hi route karo. Yahan ```local``` ka matlab hai ki data packet same vpc mein kisi bhi subnet mein ja sakta hai. Iska matlab hai agar VPC ka ek server dusre server se baat karega, to traffic VPC ke andar hi rahega, bahar nahi jayega.


**Route Table Mein ```0.0.0.0/0``` Ka Kya Matlab Hai?**:

Networking ki duniya mein ```0.0.0.0/0``` ko "Default Route" ya "Anywhere" kaha jata hai. Iska seedha sa matlab hai: "Duniya ka koi bhi IP address."

Jab aap AWS route table mein ```0.0.0.0/0``` likhte hain, to aap router ko yeh bata rahe hote hain:
- Agar aapke paas koi aisa network traffic aaye jiska IP address aapke VPC ke andar ka nahi hai.
- Aur us IP address ka koi alag se specific rule bhi table mein nahi likha hai.
- To us saare traffic ko utha kar is default raste par bhej do.

Simple sa matlab hai ki agar aap route table mein ye ```0.0.0.0/0``` destination address dete ho, to traffic apke vpc se nikal ke apke vpc mein lage internet gateway par jayega. Matlab traffic sidha internet par jayega.


Isko Ek Example Se Samjhein:
- Maan lijiye aap apne computer par baith kar ```google.com``` (IP: ```142.250.190.46```) kholte hain ya ```instagram.com``` kholte hain.
- Aapka server pehle route table mein check karega ki kya ```142.250.190.46``` ka koi alag se rasta likha hai?
- Jab use koi specific rasta nahi milega, to woh dekhega ki table mein ```0.0.0.0/0``` (Anywhere) ka rule hai.
- Woh samajh jayenge ki yeh VPC ke bahar ka traffic hai, aur use turant aapke Internet Gateway ki taraf bhej dega.

<br>

**Route Table Kaise Work Karti Hai?**:

Route table ek Routing Map ya Signboard ki tarah kaam karti hai. Jab bhi aapke subnet ke andar se koi network packet (data) nikalta hai, to woh pehle route table ke paas jata hai. Route table us packet par likha hua Destination IP (kahan jana hai) check karti hai aur use sahi rasta dikhati hai.

Fir route table, table mein likhe hue rules ko dekhti hai aur matching rule network packet par apply karke, packet ko sahi jagah bhej deti hai.

| Destination   | Target                        | Matlab                                                                 |
| ------------- | ----------------------------- | ---------------------------------------------------------------------- |
| 10.0.0.0/16   | Local                         | VPC ke andar ka traffic andar hi rahega.                               |
| 172.31.0.0/24 | pcx-112233 (VPC Peering)      | Agar traffic is specific range ke liye hai, to dusre VPC mein bhej do. |
| 0.0.0.0/0     | igw-556677 (Internet Gateway) | Baaki bacha hua saara traffic internet par bhej do.                    |

Ab Packet Kaise Move Karega? (3 Scenarios):
- Scenario A (VPC ke andar ka traffic): Server A baat karna chahta hai Server B se (IP: ```10.0.1.5```). Route table check karegi. Yeh ```10.0.0.0/16``` ke andar aata hai. Target Local hai. Traffic turant VPC ke andar hi deliver ho jayega.
  
- Scenario B (Partner Company ka traffic): Server A ek data packet bhejta hai IP ```172.31.0.50``` par. Route table dekhegii ki iska to ek alag se specific rule likha hai (```172.31.0.0/24```). Woh use Internet Gateway par nahi bhejege, balki VPC Peering Connection (pcx) par bhej degi.

- Scenario C (Internet ka traffic): Server A download karna chahta hai ek file ```8.8.8.8``` (Google DNS) se. Route table check karegi. Na to yeh local hai, na hi peering ka hai. Ab bacha aakhri option: ```0.0.0.0/0```. Route table is packet ko utha kar Internet Gateway par phenk degi, aur aapka server internet se connect ho jayega.

<br>

**AWS Jab VPC Create Karta Hai Tab Kya Hota Hai?**:

Suppose tumne VPC create ki.

CIDR
```
10.0.0.0/16
```
AWS automatically do cheezein create karta hai.

Pehle vpc ek Default Route Table create karta hai.

Aur

Dusri, Us Route Table mein ek local route. Is local route ko create karne ka matlab hai ki VPC ke andar jo bhi subnet hain Wo ek dusre se communicate kar sakte hain.
```
10.0.0.0/16
↓

local
```
Ye automatically hota hai. Tum manually nahi banate.

<br>

**AWS Local Route Automatically Kyu Banata Hai?**:

Socho VPC ke andar 50 Subnets hain.
```
10.0.1.0/24

10.0.2.0/24

10.0.3.0/24

10.0.4.0/24

....
```

Ab agar ek EC2 ```10.0.1.15``` dusre EC2 ```10.0.3.22``` ko packet bhejna chahe, to packet ko VPC ke bahar nahi jana chahiye. Ye dono ec2 same VPC ke andar hain. Isi liye AWS automatically local route bana deta hai.
```
10.0.0.0/16

↓

local
```
Is route ki wajah se VPC ke andar ka traffic VPC ke andar hi travel karta hai.

Agar tumne route table mein local ke alawa koi aur rule nhi diya hai to vo packet discard ho jayega, vo packet vpc se bahar kahi nhi jayega. Agar server internet ko access karna chta hai, lekin route table mein esa matching ka koi route hai hi nhi to packet kabhi internet par nhi jayega. 

<br>
<br>

### Internet Gateway kya hota hai?

Internet Gateway (IGW) ek AWS managed service hai. Ye VPC ko Internet se connect karti hai.

Internet Gateway ka kaam sirf ek hi hai. VPC ko Internet se connect karna.

Dhyan rahe.
- Internet Gateway packet generate nahi karta.
- Internet Gateway routing decide nahi karta.
- Internet Gateway firewall nahi hai.
- Internet Gateway NAT Gateway bhi nahi hai.

Ye sirf ek gateway hai.

**Gateway Kya Hota Hai?**:

Networking mein Gateway ka matlab hota hai ```Ek network se doosre network tak pahunchne ka exit point```.

AWS mein Internet Gateway VPC aur Internet ke beech connection establish karta hai.

<br>

Agar aapke VPC mein Internet Gateway nahi hai, to aapka VPC poori tarah se duniya se kat jata hai—na to bahar ka koi user aapke server ko khol sakta hai, aur na hi aapka server internet se koi file download kar sakta hai.

<br>

**Internet Gateway Ke Do Sabse Main Kaam**:

**1 - Target For Route Tables**:

Jaise humne pehle baat ki, Route Table mein jab hum ```0.0.0.0/0``` (Internet) likhte hain, to uske aage target mein hume batana padta hai ki traffic kahan bhejein. Hum wahan apne Internet Gateway ki ID (jaise ```igw-12345678```) daalte hain. Yeh traffic ko bahar nikalne ka rasta deta hai. Isse traffic vpc se internet gatway par aata hai. Fir internet gateway se internet par jata hai.

**2 - Network Address Translation - NAT (IP Badalne Ka Kaam)**:

- Aapke VPC ke andar jitne bhi EC2 instances hote hain, unka ek Private IP hota hai (jaise ```10.0.1.5```). Yeh IP internet par kaam nahi karta.
- Jab aapka server internet se baat karta hai, to use ek Public IP ki zaroorat hoti hai.
- Jab aapke server ka data packet IGW se hokar guzarata hai, to IGW chupke se uske Private IP ko hata kar uski jagah aapka Public IP/Elastic IP likh deta hai (1:1 NAT). Jab internet se jawab aata hai, to IGW dobara Public IP ko Private IP mein badal kar server tak pahuncha deta hai.

<br>

**Kya Sirf Internet Gateway Bana Dena Kaafi Hai?**

Kya VPC se sirf internet gateway attach kar dena kaafi hai?

Humko pta hai ki agar apne vpc ko internet se connect karna hai to us vpc mein internet gateway attach karna hota hai. Agar humne vpc se sirf internet gatway attach kar diya to kya vpc internet se connect ho jayega. 

Jawab hai Nahi.

Suppose tumne VPC se internet gatway attach kar diya, lekin route table mein tumne koi route nhi banaya jo traffic ko internet par le jaye.

Bina route ke tumhari route table kuch esi dikhti hai:
| Destination | Target |
| ----------- | ------ |
| 10.0.0.0/16 | local  |

Is route table mein koi internet route hai hi nahi, Ab batao. Packet internet tak kaise jayega? Usko to koi route hi nahi mila. Internet Gateway attach hai. Lekin Route Table usko use hi nahi kar rahi.

Result
```
No Internet Access
```
Iska matlab vpc internet access kar hi nahi payega.

Agar tumne vpc se internet gatway attach kar diya hai aur route table mein internet gateway ka koi route diya hi nhi hai to vpc se nikalne wala traffic kabhi internet par jayga hi nahi. Tumko alag se route table mein ek route banana padega jo vpc ke traffic ko internet gatway tak route karega.

Solution:
- Tum Route Table mein ek naya route add karte ho.

Destination
```
0.0.0.0/0
```
Target
```
Internet Gateway
```

Ab Route Table ban gayi.
| Destination | Target |
| ----------- | ------ |
| 10.0.0.0/16 | local  |
| 0.0.0.0/0   | IGW    |


Ab jab network packet ka destination ```0.0.0.0/0``` internet hoga Route Table kahegi Ye packet Internet Gateway ko bhej do.

<br>

**Internet Gateway Ki Sabse Bade features**:

AWS ne Internet Gateway ko is tarah design kiya hai ki aapko iski maintenance ki chinta nahi karni padti:
- **Highly Available (Kabhi Down Nahi Hota)**: IGW koi ek single physical computer ya router nahi hai jo kharab ho jaye. Yeh AWS ki ek software-defined service hai jo piche bohot sare redundant systems par chalti hai. Yeh kabhi down nahi hoti.
- **Horizontally Scalable (Koi Speed Limit Nahi)**: Aapke VPC se chahe 1 MB ka traffic guzaare ya 100 GB ka, Internet Gateway apne aap bada (scale) ho jata hai. Isme network traffic ki koi bandwidth limit ya rukawat nahi hoti.
- **Bilkul Muft (Free of Cost)**: AWS mein Internet Gateway create karne ka ya use VPC se attach karne ka koi paisa nahi lagta. Yeh bilkul free component hai. (Aapko sirf us data ka paisa dena hota hai jo internet par transfer hota hai).

<br>

**Ek VPC Mein Kitne IGW Ho Sakte Hain?**:

Rule: Ek VPC ke saath aap sirf aur sirf ek (1) Internet Gateway hi attach kar sakte hain.

Aap ek se zyada IGW banakar ek hi VPC mein nahi jod sakte. Agar aapko lagta hai ki traffic bohot zyada hai, to chinta mat kijiye, kyunki ek hi IGW unlimited traffic handle karne ke liye kaafi hai.

<br>
<br>

## VPC mein public subnet banane ki complete process (VPC ko internet ke saath kaise connect karte hain)

We know that, ki jab hum AWS mein ek VPC create karte hain to, normally us vpc ke ander koi subnet nhi hota aur aap bina subnet ke kisi bhi VPC mein EC2 instance create nahi kar sakte hain. VPC ke andar subnet banana bilkul zaroori (mandatory) hai.

Jab aap ek EC2 instance launch karte hain, toh use network se connect hone ke liye ek IP address aur network interface ki zaroorat hoti hai. VPC sirf ek bada network area (IP range) define karta hai, jabki subnet us area ka ek chota hissa hota hai jo actual mein EC2 instances ko hold karta hai. Bina subnet ke EC2 instance ko koi physical ya logical jagah nahi milti jahan usko network mil sake.

To VPC mein subnet create karna mandatory hai, Sirf ec2 instance launch karne ke liye hi nhi, aap VPC ke andar koi bhi resource launch karein, usme subnet banana bilkul zaroori (mandatory) hai.

AWS ka yeh niyam sirf EC2 ke liye nahi hai. VPC ke andar chalne wala koi bhi resource (jaise Database, Load Balancer, ya Containers) bina subnet ke kaam nahi kar sakta.

<br>

To jab bhi aap vpc ke ander ek subnet banate hain to vo subnet by default ek private subnet hota hai, matlab jo bhi resources us subnet mein create honge vo internet se connected nhi honge. Lekin vpc create hote hi, AWS vpc ke ander ek default route table create karta hai, jisme ek local route hota hai, us route ki wajah se vpc ke andar ke instances aapas mein toh baat kar sakte hain (VPC ke andar), lekin unka connection bahar ki internet duniya se nahi ho sakta.

To subnet ko humko public banana hota hai. Subnet ko public banane ke liye usme aws ke route table aur internet gatway jaise resources ka use karte hain. 

Ab poori process dekho ek private subnet ko public subnet banane ke liye.

<br>

**Ek normal subnet ko "Public Subnet" banane ke liye aapko 3 bade steps karne hote hain**:

Suppose apne ek VPC create kiya hai aur uske ander ek subnet create kiya hai:

**1 - Internet Gateway (IGW) Create Aur Attach Karna**: Sabse pehle aapko ek Internet Gateway banana hota hai aur use apne VPC se connect (attach) karna hota hai. Yeh gateway aapke VPC aur bahar ke internet ke beech ka darwaza hai.

**2 - Route Table Mein Route Add Karna**: Aapko ek alag Route Table banani hoti hai (ya fir main table ko edit karna hota hai) aur usme ek naya route dalna hota hai: ```0.0.0.0/0 -> igw-xxxxxx``` (Internet Gateway). Iska matlab hai ki agar kisi instance ko internet par jana hai, toh uska traffic is gateway ke paas bheja jaye.

**3 - Subnet Ko Associate Karna**: Is nayi Route Table ko aapko apne us subnet se connect (associate) karna hota hai jise aap public banana chahte hain.

**4 - Auto-Assign Public IP**: Iske sath hi aapko subnet ki settings mein jakar "Auto-assign public IPv4 address" ko enable karna padta hai, taaki wahan banne wale EC2 instances ko ek public IP mil sake.

<br>

### Example:

Suppose hum ek naya VPC banate hain.

VPC CIDR
```
10.0.0.0/16
```

**Step 1: Subnet create karo**.
```
10.0.1.0/24
```
Abhi ye sirf ek subnet hai. Na public hai na internet-enabled.

**Step 2: Internet Gateway create karo**.

**Step 3: Internet Gateway ko VPC ke saath attach karo**.

Ab VPC ke paas internet tak pahunchne ka ek gateway available hai. Lekin abhi bhi internet access nahi milega.

**Step 4: Ek Route Table create karo**.

Ya to to main route table ko edit karke usme route add kar sakte hain ya fir ek nayi route table create kar sakte hain.

Isme routes add karo.

| Destination | Target |
| ----------- | ------ |
| 10.0.0.0/16 | local  |
| 0.0.0.0/0   | IGW    |

**Step 5: Is Route Table ko Subnet ke saath associate karo**.

Ab subnet ke paas internet ki taraf route available hai. Is stage par AWS is subnet ko Public Subnet consider karta hai.

**Step 6**:

Agar tum chahte ho ki EC2 ko internet se directly access kiya ja sake, to EC2 ke paas Public IPv4 Address ya Elastic IP bhi hona chahiye. Agar Public IP nahi hoga, to Route Table aur Internet Gateway hone ke baad bhi internet se inbound communication possible nahi hoga.

