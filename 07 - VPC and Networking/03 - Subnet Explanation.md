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

Jab aap VPC ke andar koi naya subnet banate hain, to woh by default ek Private Subnet hota hai. Usko Public Subnet mein badalne ke liye hi hum Internet Gateway aur Route Table ka use karte hain.

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
