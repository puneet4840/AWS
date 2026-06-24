# Overview of VPC CIDR

### Sabse Pehle IP Address Ko Samjho

CIDR samajhne se pehle IP Address samajhna zaruri hai.

Internet aur networking mein har device ko identify karne ke liye ek address diya jata hai jisko IP Address kehte hain.

Jaise:
- Har ghar ka address hota hai.
- Har office ka address hota hai.
- Har shop ka address hota hai.

Waise hi network mein har device ka ek address hota hai jisko IP Address kehte hain.

Example:
```
192.168.1.10
```
Ya
```
10.0.1.25
```
Ya
```
172.16.5.100
```
Ye sab IP addresses hain. 

Agar IP address na ho to network ko pata hi nahi chalega ki packet kahan bhejna hai.

<br>
<br>

### IPv4 Address Kya Hota Hai?

AWS VPC mostly IPv4 CIDR ke saath create kiya jata hai. IPv4 address 32 bits ka hota hai.

Example:
```
10.0.0.0
```
Isko binary mein convert karein:
```
00001010.00000000.00000000.00000000
```
Total:
```
8 + 8 + 8 + 8 = 32 bits
```
Yahi reason hai ki IPv4 address 32-bit ka address kehlata hai.

<br>
<br>

### CIDR Ka Full Form

CIDR ka full form hai: ```Classless Inter-Domain Routing```.

CIDR ek method hai jo network ka size aur uske IP address range ko define karta hai.

Ya

CIDR batata hai ki network mein kitne IP addresses available honge.

<br>

**CIDR Ki Zarurat Kyu Padi?**:

Internet ke shuruati dino mein CIDR nahi tha. Tab network classes hoti thi.

Jisme Class A, Class B aur Class C networks use hote the.

Class A
```
10.0.0.0
```
Bahut bada network. Lagbhag 16 million IPs.

Class B
```
172.16.0.0
```
Medium network. Lagbhag 65,000 IPs.

Class C
```
192.168.1.0
```
Small network. 256 IPs.

<br>

To Class-Based System mein kuch problems thi.

Problem ye thi ki Maan lo ek company ko sirf 500 IP addresses chahiye. Company ne Class B choose kiya to Class B mein lakhon IPs mil jate the.

Class C:
```
256
```
Kam pad gaye.

Class B:
```
65,536
```
Bahut zyada ho gaye.

Result:
```
65,536 - 500 = 65,036
```
IPs waste ho gaye.

Is problem ko solve karne ke liye CIDR introduce hua. CIDR mein Ab company jitne IPs chahiye utna hi network bana sakti hai.

<br>
<br>

### CIDR Notation Ko Samjho

Example:
```
10.0.0.0/16
```

Iske do parts hain.

Part 1:
```
10.0.0.0
```
Network Address

Part 2:
```
/16
```
Subnet Mask Prefix Length

<br>

**Prefix Length Kya Batata Hai?**

IPv4 mein total 32 bits hoti hain.

Example:
```
10.0.0.0
```
Binary mein:
```
00001010.00000000.00000000.00000000
```

Total bits: 32

Ab agar likha hai: ```10.0.0.0/16```

Matlab:
- Pehle 16 bits Network part ko denote kar rhi hain.
- Baaki 16 bits Host part ko denote kar rhi hain.

<br>

**/16 Mein Kitne IPs Hote Hain?**:

Formula: ```2^(32-prefix)```.

Yaani:
```
2^(32-16)

2^16

65536
```

Total: 65,536 IPs

Range:
```
10.0.0.0 se 10.0.255.255
```

<br>
<br>

<img width="800" height="450" src="https://drive.google.com/uc?export=view&id=1m_dZSLgFvoQZTzhKYLDbPv3c4bann1LG">

<br>
<br>

### AWS Mein VPC CIDR Kyu Dena Padta Hai?

Jab AWS VPC create karta hai to usko pehle se pata hona chahiye:
- Kitne servers aa sakte hain.
- Kitne subnets banenge.
- Kitne databases honge.
- Future growth kitni hogi.

AWS future predict nahi kar sakta.

Isliye woh aapse poochta hai: CIDR kya rakhna hai?

Yaani:
- Aapko kitna bada network chahiye?

**VPC Create Karte Time CIDR Ka Matlab**:

Maan lo hum VPC create kar rahe hain.

VPC
```
10.0.0.0/16
```
AWS ko hum keh rahe hain: Mere VPC ke andar saare IP addresses ```10.0.x.x``` range ke honge.

Ye VPC ka address space hai. Is VPC ke andar jitne bhi:
- EC2
- RDS
- Load Balancer
- EKS Nodes

honge unko isi range se IP milega.

<br>
<br>

### AWS Mein Common CIDR Blocks

Large VPC
```
10.0.0.0/16
```
65536 IPs

Medium VPC
```
10.0.0.0/20
```
4096 IPs

Small VPC
```
10.0.0.0/24
```
256 IPs

<br>
<br>

### CIDR Aur Subnet Ka Relation

VPC ke andar hum Subnets banate hain.

Example:

VPC
```
10.0.0.0/16
```
Ab is VPC ke andar:

Public Subnet
```
10.0.1.0/24
```
Aur

Private Subnet
```
10.0.2.0/24
```

Aur

Database Subnet
```
10.0.3.0/24
```
Ye sab VPC ke CIDR ke andar hone chahiye.

<br>
<br>

### AWS 5 IP Addresses Reserve Karta Hai

Ye interview ka bahut famous question hai.

Maan lo subnet hai:
```
10.0.1.0/24
```
Total IPs: 256

Lekin usable: 251

Kyuki AWS 5 IPs reserve kar leta hai.

Reserved IPs
```
10.0.1.0
Network Address

10.0.1.1
VPC Router

10.0.1.2
DNS

10.0.1.3
Future Use

10.0.1.255
Broadcast Reserved
```
Isliye usable: 251

<br>
<br>

### CIDR Planning Kyu Important Hai?

Maan lo aapne VPC banaya:
```
192.168.1.0/24
```
Sirf: 256 IPs available hain.

Ab company grow kar gayi.

500 servers chahiye. 

Ab IPs sirf 256 the , isliye aur servers ke liye ip khatam ho jayenge.

CIDR expand karna complicated ho sakta hai. Isliye production mein CIDR future growth ko dekh kar select kiya jata hai.

<br>
<br>

### Hybrid Cloud Mein CIDR Planning

Maan lo company ka on-prem network hai:
```
10.0.0.0/16
```
Aur AWS VPC bhi:
```
10.0.0.0/16
```
Ab VPN connect karoge to routing conflict hoga. Isliye AWS VPC banate waqt pehle existing datacenter ka CIDR check kiya jata hai.

Example:
```
On-Prem
10.0.0.0/16

AWS
172.16.0.0/16
```
Ab connectivity smoothly kaam karegi.

<br>
<br>

### AWS CIDR Expansion

AWS additional CIDR blocks attach karne ki facility deta hai.

Example:

Pehla CIDR:
```
10.0.0.0/16
```
Baad mein add:
```
10.1.0.0/16
```
Isko Secondary CIDR kehte hain. Ye feature tab useful hota hai jab primary CIDR mein IPs kam pad jayein.

