# High Availability and Scalability in AWS

High Availability ka matlab hai ki application hamesha up and running rahe, traffic jyada badhne par aur system failure par.

In AWS world High availability generally refers to using multiple AZs.

<br>

High Availability (HA) ka simple matlab hota hai:
- System ko is tarah design karna ki agar koi ek server, service, VM, database, ya pura datacenter bhi fail ho jaye, tab bhi application band na ho aur users ko service milti rahe.

Yaani:
- Downtime minimum ho.
- Single point of failure na ho.
- Application continuously available rahe.

AWS mein High Availability ka matlab hota hai:
- Application ko multiple AWS resources aur datacenters mein is tarah deploy karna ki agar koi server, EC2 instance, disk, ya pura datacenter fail ho jaye tab bhi application chalti rahe.

<br>

**Ek Real-Life Example**:

Socho tumne ek website banayi:
```
Users → 1 Server → Website
```

Ab:
- Agar ye single server crash ho gaya.
- RAM full ho gayi.
- Disk kharab ho gayi.
- VM band ho gaya

To puri website down ho jayegi.

Isko bolte hain:
```
❌ Single Point of Failure (SPOF)
```

High Availability ka purpose hota hai:
- “Ek component fail ho jaye tab bhi system chalna chahiye.”


<br>

### High Availability ka Basic Architecture

Instead of 1 server:
```
           ┌─────────┐
Users ───► │ Load    │
           │ Balancer│
           └────┬────┘
                │
      ┌─────────┴─────────┐
      │                   │
┌──────────┐       ┌──────────┐
│ Server-1 │       │ Server-2 │
└──────────┘       └──────────┘
```

Ab:
- Server-1 down hua.
- Traffic automatically Server-2 par chala jayega.

Website chalti rahegi. Yahi High Availability hai.

<br>
<br>

### High Availability ke Principles

HA ke 3 core principles hote hain:

**1 - Redundancy**:

Ek cheez ka backup version rakhna.

Examples:

Multiple servers, Multiple databases, Multiple network paths, Multiple availability zones.

<br>

**2 - Failover**:

Agar primary component fail ho jaye: system automatically backup component par switch ho jaye

Example:
- Primary DB down.
- Replica DB active ho gayi.

**3 - Health Checks**:

System continuously check karta hai:
- Server alive hai ya nahi.
- App response de rahi hai ya nahi.

Agar unhealthy: traffic hata diya jata hai.

<br>
<br>

### Availability ka Meaning

Availability percentage mein measure hoti hai.

Example:

| Availability | Downtime Per Year |
| ------------ | ----------------- |
| 99%          | ~3.65 days        |
| 99.9%        | ~8.7 hours        |
| 99.99%       | ~52 minutes       |
| 99.999%      | ~5 minutes        |


<br>
<br>

### AWS Infrastructure Samjho

AWS ka HA samajhne ke liye ye hierarchy samajhna bahut important hai.
```
AWS Global Infrastructure
        │
     Region
        │
Availability Zones (AZ)
        │
Datacenters
```

**1 - Region**

Region ek geographical location hoti hai.

Examples:
- Mumbai
- Frankfurt
- Virginia

AWS names:
```
ap-south-1 → Mumbai
us-east-1 → Virginia
```

**2 - Availability Zone (AZ)**

Har region ke andar multiple AZs hoti hain.

Example:
```
Mumbai Region (ap-south-1)

├── ap-south-1a
├── ap-south-1b
└── ap-south-1c
```

Important:

Har AZ ek separate datacenter hota hai, Separate:
- power
- cooling
- networking

Agar ek AZ fail ho gaya, Dusri AZ survive karti hai.

Yahi AWS HA ka core foundation hai.


<br>
<br>

### Important AWS Services for HA

- Elastic Load Balancer (ELB).
- Auto Scaling Group (ASG).
- Multi-AZ Deployment: AWS strongly recommend karta hai “Never keep production workload in single AZ.”
- Amazon RDS Multi-AZ.
- RDS Multi-AZ.
- Route 53 (DNS Failover).
- S3 High Availability: Amazon S3 automatically multiple AZs mein data replicate karta hai, Tumko manually HA setup nahi karna padta. S3 already highly available hai.

Lekin hum is session mein sirf Load Balancer aur Auto Scaling Group ke baare mein pdhenge. 

Aage baaki services ke baare mein pdhenge.

<br>
<br>
<br>

## Scalability

Scaling ka matlab hai system ki capacity ko traffic ke hisaab se increase aur decrese karna.

Matlab server par load badhne par ya to server ka size badha dena ya fir ek naya instance increase ho jana aur load kam hone par ya to server ka size lam kar dena ya fir ek instance decrese ho jana.

Example:

Suppose ek Ecommerce website sirf 1 server par chal rahi hai. 

Initially: 50 users

Ek EC2 enough hai.
```
50 users
    |
1 server
```
Agar koi sale shuru hoti hai to ```50 → 50,000 users``` aa jayenge aur load badhega. Ese mein agar sirf single server par website chalegi to:
- CPU aur Memory high ho jayegi.
- Website down ho jayegi.

Yaani System scalable nahi tha.

Isliye system ko is way mein scalable banate hain jisse load badhne par khud se hi system ki capacity adjust ho.

<br>

**Scalability ka Goal**:

AWS mein scalability ka purpose hota hai:
- More users handle karna
- More traffic handle karna
- More data process karna
- Performance maintain rakhna

<br>

### Types of Scalability

**1 - Horizontal Scaling**:

Horizontal scaling (jise Scale Out bhi kehte hain) ka matlab hai apne system ki capacity badhane ke liye ek hi machine ko upgrade karne ke bajaye aur naye servers ya machines add kar dena.

Matlab more servers add karna.

**Horizontal Scaling Kaise Kaam Karti Hai?**:
- Multiple Machines: Ismein aapke paas ek pura "cluster" hota hai machines ka.
- Load Balancer: Saara traffic ek Load Balancer ke paas aata hai, jo decide karta hai ki kaun sa request kis server par bhejna hai taaki koi ek server overload na ho.
- Stateless Nature: Zyada tar horizontal scaling "stateless" applications ke liye easy hoti hai kyunki kisi bhi server se response aa sakta hai.

<br>

**2 - Vertical Scaling**:

Vertical scaling, (jise Scale Up bhi kehte hain) ka matlab hai apne existing machine ki power badhana. Yani naye servers add karne ke bajaye, usi ek machine ke CPU aur Memory badha dena.

Matlab jis ek server par apka application chal rha hai usi server ke CPU aur Memory increase kar dena.

Example:

Maan lo aapke paas ek computer hai jo slow chal raha hai. Aapne usmein naya server lene ke bajaye, usi mein RAM badha di ya behtar Processor (CPU) laga diya. Ye vertical scaling hai.

Example

Before:
```
2 GB RAM
1 vCPU
```
After:
```
16 GB RAM
8 vCPU
```

**Key Points**:
- Capacity Upgrade: Ismein aap ek hi server ki RAM, CPU, ya Storage (SSD/HDD) ko upgrade karte hain.
- Simple Setup: Ismein load balancer ki zaroorat nahi padti kyunki server toh ek hi hai. Saara configuration simple rehta hai.
- Administrative Ease: Multiple machines ko manage karne ka jhanjhat nahi hota.

**Pros**:
- Maintenance Easy hai: Ek hi machine ko dekhna hota hai, toh software updates aur management asaan hai.
- Data Consistency: Saara data ek hi jagah hota hai, isliye data sync karne ki tension nahi hoti.
- Low Complexity: Application mein zyada badlav nahi karne padte.

**Cons**:
- Hardware Limit: Ek point ke baad aap machine ko upgrade nahi kar sakte. (Hardware ki apni limit hoti hai).
- Downtime: Aksar hardware upgrade karte waqt server ko band (shutdown) karna padta hai, jisse aapka kaam ruk sakta hai.
- Single Point of Failure: Agar wahi ek server kharab ho gaya, toh aapki puri service band ho jayegi.

**Kab use karein?**
- Chhote projects ya aisi applications ke liye jahan traffic stable rehta hai aur reliability se zyada simplicity zaroori hai.

<br>
<br>

### AWS Mein Scalability Kaise Achieve Karte Hain

1. Auto Scaling Group (ASG).
2. Elastic Load Balancer (ELB).

<br>

Use Elastic Load Balancer (ELB), Auto Scaling group (ASG) for achieving High Availability and Scalability

