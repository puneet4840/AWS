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


