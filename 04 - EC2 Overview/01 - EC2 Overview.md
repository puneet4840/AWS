# EC2 Overview

EC2 stands for Elastic Compute Cloud.

EC2 is a virtual machine provided by AWS.

Amazon EC2 (Elastic Compute Cloud) AWS ki ek aisi service hai jo aapko internet par virtual servers (jinhe "instances" kehte hain) rent par provide karti hai.


**Bina Hardware Kharide Server**: Aapko khud ka physical CPU ya computer kharidne ki zaroorat nahi hai. Aap AWS se kuch minutes mein server lekar apna kaam shuru kar sakte hain.

**Scale Karna Aasaan Hai**: Agar aapki website par traffic badh jaye, toh aap ek click mein server ki power (RAM/CPU) badha sakte hain.

**Pay-as-you-g**o: Aapko sirf utne hi time ka paisa dena hota hai jitni der aapne server chalaya.

**Full Control**: Aap apna pasandida Operating System (jaise Windows ya Linux) install kar sakte hain aur use duniya mein kahin se bhi access kar sakte hain.

**Example**: 

Maan lijiye aapne ek nayi app banayi hai. Usse online chalane ke liye aapko ek computer chahiye jo 24/7 chalta rahe. EC2 wahi computer hai jo Amazon ke data center mein rakha hai, par control aapke paas hai.

<br>
<br>

## EC2 Configuration Options

<br>

### AWS AMI (Amazon Machine Image)

AWS me AMI (Amazon Machine Image) ek pre-configured template hota hai jiska use karke tum EC2 instance launch karte ho.

AMI ke ander:
- Operating System.
- Configuration.
- Software.
- Permissions.
- Boot Instructions

ye sab hote hain.

AMI ka simple use hai, Matlab agar tum ek server baar-baar same configuration ke saath banana chahte ho (same OS, same apps, same settings), to tum manually baar-baar install nahi karoge.

Tum ek AMI banaoge → usse multiple EC2 launch karoge.

AMI ek pre-configured machine template hota hai jiske basis par AWS tumhare liye new EC2 instances launch karta hai.

<br>

**AMI Types**:

**Type 1: AWS Provided AMI**:

AWS directly provide karta hai:
- Amazon Linux.
- Ubuntu.
- Red Hat.
- Windows.

Best for:
- General use.
- Quick deployments.

**Type 2: Marketplace AMI**:

Third-party vendors apni AMI bhi provide karte hain:
- Palo Alto
- Jenkins prebuilt
- WordPress
- MongoDB

Best for:
- Specialized apps

Caution:
- Extra charges ho sakte hain.

**Type 3: Custom AMI**:

Tum khud banate ho:
- Custom software
- Security hardening
- Monitoring agents
- Golden image

Best for:
- Production

**Region Dependency**:

AMI region-specific hota hai. Matlab agar tumne mumbai region mein ek AMI banai to dusre region mein available nhi hogi. Dusre region ke liye tumko copy karna padega.


**AMI Creation Process**:

Scenario:

Tumne ek EC2 banaya:
- Ubuntu
- Docker
- Nginx
- Security patches

Ab tum isko reusable banana chahte ho.

Steps:
- EC2 configure karo
- Stop instance (recommended)
- Actions → Image → Create Image
- Name do
- AWS snapshot create karega
- New AMI ready

<br>
<br>

### AWS Instace Types

Jab tum AWS par EC2 (Elastic Compute Cloud) launch karte ho, tab sabse important decision hota hai: Kaunsa instance type choose karna hai?

**EC2 Instance Type kya hota hai?**

Instance type matlab ec2 ke liye ek hardware choose karna.

EC2 instance type ek predefined hardware configuration hoti hai jisme decide hota hai:
- Kitna CPU milega (vCPU)
- Kitni RAM milegi
- Storage kis type ka hoga
- Network speed kitni hogi
- GPU hai ya nahi
- Kis workload ke liye optimized hai

<br>

**AWS Naming Convention**:

```t3.micro```

Breakdown:
- ```t``` = Family
  - Family batata hai kis category ka instance hai.
- ```3``` = Generation
  - 3rd generation hardware
- ```micro``` = Size
  - Kitna bada ya chhota
 
**Types of Instance**:
- General Purpose (Sabse zyada use hone wale):
  - Ye instances compute, memory, aur networking ka balance rakhte hain.
  - Families: T-series (T2, T3, T4g) aur M-series (M5, M6i).
  - Use Case: Web servers, small databases, ya testing environments ke liye best hain.
 
- Compute Optimized (Sirf speed aur calculation ke liye):
  - Inmein processor bahut powerful hota hai.
  - Families: C-series (jaise C5, C6g).
  - Use Case: Gaming servers, video encoding, aur high-performance web servers.
 
- Memory Optimized (Bhari data processing ke liye):
  - Agar aapka application bahut zyada RAM (memory) mangta hai, toh ye best hain.
  - Families: R-series, X-series, aur Z-series.
  - Use Case: Real-time big data analytics aur in-memory databases.
 
- Storage Optimized (Bhari storage aur speed ke liye):
  - Ye un kamo ke liye hain jinhein bahut fast read/write speed chahiye hoti hai.
  - Families: I-series, D-series, H-series.
  - Use Case: Data warehousing aur Log processing.
 
- Accelerated Computing (Graphics aur AI ke liye):
  - Inmein GPUs ya specialized hardware lage hote hain.
  - Families: G-series, P-series, F-series.
  - Use Case: Graphics rendering, machine learning, aur AI training.
 
<br>
<br>

### Security Group

Security Group EC2 instance ka virtual firewall hota hai jo inbound aur outbound traffic control karta hai. Ye decide karta hai kaunsa traffic instance ke andar aa sakta hai aur bahar ja sakta hai.

**Main Purpose**:
- Ye server ko unauthorized access se protect karta hai.
- Sirf allowed ports, protocols, aur IPs ko traffic pass karne deta hai.

**Inbound Rules**:
- Inbound rules incoming traffic ko control karte hain.
- Ye define karte hain kaun user ya system tumhare EC2 tak pahunch sakta hai.

Example:
- Port 22 allow = SSH access milega.
- Port 80 allow = Website HTTP par accessible hogi.

**Outbound Rules**:
- Outbound rules instance se bahar jane wale traffic ko control karte hain.
- Ye define karte hain EC2 kis external system se connect kar sakta hai.

**Default Behavior**:
- By default inbound sab blocked hota hai.
- By default outbound usually sab allowed hota hai.

**Stateful Nature**:
- Security Group stateful hota hai matlab return traffic automatically allow hota hai.
- Agar inbound allow kiya hai to response ke liye separate outbound rule zaroori nahi.

**Source**:
- Source batata hai traffic kahan se allowed hai.
- Ye IP address, CIDR range, ya another Security Group ho sakta hai.

Example:

```0.0.0.0/0```: Isko source mein define karne se.
- Ye internet ke sabhi IPs ko allow karta hai.
- Public access ke liye use hota hai but risky ho sakta hai.

**Multiple Security Groups**:
- Ek EC2 par multiple Security Groups attach ho sakte hain.
- Sab SG rules combined allow model par kaam karte hain.

Important:
- Deny rule Security Group mein nahi hota.
- Sirf allow rules hote hain, jo allow nahi vo automatically deny hota hai.

**Security Group Scope**:
- Security Group instance level par kaam karta hai. Ye subnet level security nahi hai. Matlab SG instance par attach hota hai.

**Security Group vs NACL**:
- Security Group instance firewall hota hai.
- NACL subnet firewall hota hai.

<br>
<br>

### AWS EC2 Purchasing Options

AWS mein EC2 launch karna sirf instance choose karna nahi hota, balki payment model choose karna bhi important hota hai. Same instance ka cost purchasing option ke hisaab se kaafi change ho sakta hai.

Main EC2 Purchasing Options:
- On-Demand
- Spot Instance
- Reserved Instance (RI)
- Savings Plans

<br>

**On-Demand Instances**:

On-Demand mein tum pay-as-you-go model use karte ho. Jitni der instance chala, utne time ka bill.

Isme Per second ya per hour billing hoti hai(instance type dependent). Usage ke hisaab se direct payment hoti hai.

Koi long-term commitment nahi hota. Kabhi bhi launch karo, kabhi bhi stop karo.

Use Case:
- Jab tumhe pata nahi workload kitna chalega ya kab band hoga tab ye use karo.
- Dev/test, POC aur sudden traffic spikes ke liye ye perfect hai.

Cost:
- Ye sabse expensive option hota hai compared to baaki models.
- Tum flexibility ke badle premium price pay karte ho.

<br>

**Spot Instance**:

Spot instances AWS ke unused resources hote hain jo bahut cheap milte hain. Lekin AWS kabhi bhi inhe terminate kar sakta hai agar capacity chahiye ho.

AWS ke unused servers discounted price par milte hain.

AWS bolta hai:
- “Ye server abhi free hai, cheap mein le lo… but mujhe zarurat padi to wapas le lunga.”

AWS unused spare capacity ko heavy discount par deta hai. 70–90% tak cheaper ho sakta hai.

AWS kabhi bhi reclaim kar sakta hai agar capacity chahiye ho. 2-minute interruption warning mil sakti hai.

Use Case:
- Batch jobs, CI/CD, data processing jaise interruptible workloads ke liye best hai.
- Aise kaam jisme failure se problem na ho ya resume ho sake.

Cost:
- Ye on-demand se 70–90% tak cheap ho sakta hai.
- Cost saving high hoti hai lekin reliability low hoti hai.

Risk:
- AWS 2 minute ka warning dekar instance terminate kar sakta hai.
- Isliye critical applications ke liye suitable nahi hai.

<br>

**Reserved Instances**:

Reserved instance mein tum 1 ya 3 saal ke liye instance commitment dete ho specific instance type ke liye. Iske badle AWS tumhe heavy discount deta hai on-demand ke comparison mein.

Use Case:
- Predictable aur long-running workloads ke liye ye best hai.
- Jaise production servers jo hamesha chalte hain.

Cost:
- Ye 40–75% tak cheaper hota hai on-demand se.
- Zyada saving milti hai agar upfront payment karo.

Types:
- Standard RI: sabse cheap hota hai lekin flexibility kam hoti hai.
- Convertible RI: mein tum instance type change kar sakte ho lekin discount thoda kam hota hai.

<br>

**Savings Plans**:

Savings Plan mein tum specific instance nahi balki usage spend commit karte ho per hour. Matlab tum AWS se compute usage rent par lete ho. Isme tum instace ke saath saath lambda bhi use kar sakte ho. 

Ye RI se zyada flexible hota hai aur automatically discount apply karta hai.

Use Case:
- Jab tum different instance types ya regions use karte ho tab ye best hai.
- Dynamic workloads aur modern architectures ke liye suitable hai.

Cost:
- Ye bhi 66–72% tak cost reduce kar sakta hai on-demand se.
- Saving RI jaisi hoti hai but flexibility zyada hoti hai.

Types:
- Compute Savings Plan sabse flexible hota hai aur kisi bhi instance pe apply ho jata hai.
- EC2 Instance Savings Plan specific family/region ke liye hota hai aur thoda cheaper hota hai.

