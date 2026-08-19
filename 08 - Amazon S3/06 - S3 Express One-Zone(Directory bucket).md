# S3 Express One-Zone

S3 mein do tarah ki buckets hoti hain:
- **General Purpose Buckets**: Jo hum normal use karte hain (Standard, Standard-IA, Glacier ke liye).
- **Directory Buckets**: Yeh ek special naye tarah ka bucket architecture hai jise AWS ne sirf S3 Express One Zone ke liye banaya hai.

<br>
<br>

### S3 Express One Zone Kya Hai?

S3 Express One Zone AWS ki sabse nayi aur super-fast(High Performance) storage class hai.

Amazon S3 Express One Zone ek high-performance S3 storage class hai jo un applications ke liye design ki gayi hai jahan bahut high request rate aur extremely low latency object access chahiye.

Amazon S3 Express One Zone ek high-performance S3 storage class hai jo un applications ke liye design ki gayi hai jahan object ko access karne ki latency bahut kam, performance bahut high aur requests ki quantity bahut zyada hoti hai.

Normal S3 Standard mein bhi tum data bahut fast access kar sakte ho, lekin kuch workloads aise hote hain jahan application ko repeatedly aur extremely fast object access chahiye hota hai.

Example:
- High-performance analytics.
- AI/ML workloads.
- Machine learning training data.
- Large-scale data processing.
- Real-time processing.
- Media processing.
- High-performance computing.

Aise scenarios mein storage ke saath interaction bahut frequent hota hai.

Maan lo tumhari application ko normal tarike se ek object read nahi karna hai, balki:
```
Read
Write
Read
Write
Read
Delete
Read
Write
```
ye operations millions ya billions of times perform karne hain.

Aise workload ke liye AWS ne S3 Express One Zone introduce kiya.

<br>
<br>

### Yeh Kyu Alag Hai?

Baaki jitni bhi S3 classes hain, wo aapka data **Standard S3 Buckets** mein store karti hain.

Lekin S3 Express One Zone aapka data ek special bucket mein rakhta hai jise **Directory Bucket** kehte hain.
- **Speed**: Isme data access karne ki speed Single-digit millisecond (1ms se kam) hoti hai. Yeh normal S3 Standard se bhi 10 guna (10x) tak fast hai.
- **Request Cost**: Isme data par lagne wali API request cost (GET/PUT requests) normal S3 se 50% tak sasti hoti hai. Yani aap crore-on baar data read/write kar sakte hain bina bada bill banaye.

<br>
<br>

### Iska naam "One Zone" kyun hai?

Normal S3 storage classes, jaise S3 Standard, generally data ko multiple Availability Zones ke across store karti hain.

Conceptually:
```
S3 Standard

Region

├── AZ-1
├── AZ-2
└── AZ-3
```
Iska objective high availability aur infrastructure failure ke against protection provide karna hota hai.

Lekin kuch workloads mein customer ka priority maximum resilience se zyada **performance aur low latency** ho sakti hai.

S3 Express One Zone mein data ek specific Availability Zone ke mein store kiya jata hai. Matlab agar ek region mein 3 availability zones hain to S3 Express One Zone ke liye directory bucket banate time humko ek particular availability zone select karna padta hai.

Conceptually:
```
AWS Region

S3 Standard

        Data
          │
   ┌──────┼──────┐
   ▼      ▼      ▼

  AZ-1   AZ-2   AZ-3
```

Isliye iska naam hai: **S3 Express One Zone**.

<br>
<br>

### Iska naam "Express" kyu hai?

"Express" ka concept yahan performance se related hai.

Normal S3 ek general-purpose object storage service hai jo bahut large number of use cases ke liye design ki gayi hai.

Lekin S3 Express One Zone specifically un workloads ke liye optimized hai jahan:
- latency bahut low honi chahiye,
- objects ko rapidly access karna ho,
- bahut high number of requests process karni ho,
- storage aur compute ke beech fast interaction chahiye ho.

Matlab agar tumhara workload simple website images store kar raha hai, to tumhe zaruri nahi ki S3 Express One Zone use karna chahiye.

Lekin agar tumhari application high-performance processing kar rahi hai aur storage access hi bottleneck ban raha hai, tab ye useful ho sakta hai.

<br>
<br>

### S3 Express One Zone Ke Liye Normal General Purpose Bucket Use Nahi Hota

Normal S3 buckets ko AWS ab **General Purpose Buckets** kehta hai.

Tum in buckets mein S3 Standard, Standard-IA, Intelligent-Tiering, Glacier aur doosri supported storage classes use kar sakte ho.

Lekin:

**S3 Express One Zone ko use karne ke liye Directory Bucket create karna zaroori hota hai.**

S3 Express One Zone ko general purpose bucket mein simply select karke object upload nahi kar sakte. AWS ke architecture mein S3 Express One Zone specifically directory buckets ke saath use hota hai.

Isliye jab jab S3 bucket create karte hain to wahan ek option hota **Directory**, is option ko hi select karke tum directory bucket banate ho jo S3 Express One Zone storage class use karta hai.
Matlab jab tumne **Directory** option select kiya to s3 bucket khud se hi S3 Express One Zone storage class use karti hai.

Yaani:
```
General Purpose Bucket
        │
        ├── S3 Standard
        ├── S3 Intelligent-Tiering
        ├── S3 Standard-IA
        ├── S3 One Zone-IA
        └── Glacier classes




Directory Bucket
        │
        └── S3 Express One Zone
```

<br>
<br>

### Yahan directory ka matlab kya hai? Kya kisi directory ke ander data store ho rha hai?

**Nahi**, yahan "Directory" ka matlab aapke computer ke folder ya directory jaisa bilkul nahi hai. Yeh sirf AWS ka ek naam rakhne ka tareeqa hai.

Computer science mein ek term hota hai **"Directory Service"** (jaise Active Directory ya phone directory), jahan data bohot hi structured format mein hota hai aur use dhoondhna (look-up) super-fast hota hai.

AWS ne is naye system ka naam Directory Bucket isiliye rakha kyunki iska dhoondhne aur access karne ka mechanism normal S3 se alag hai. Yeh aapke data ko ek hi specialized hardware aur index system mein rakhta hai taaki request aate hi microsecond mein response mil sake.

<br>
<br>

### Iske Nuksan Kya Hain?

Har achhi cheez ke sath thoda risk aata hai, Express One Zone ke sath do bade catch hain:

- **Data Loss Ka Khatra (Low Redundancy)**: Kyunki aapka data sirf ek hi datacenter mein hai, agar us area mein koi natural disaster (earthquake, flood) aaya aur wo datacenter physical damage ho gaya, toh aapka data hamesha ke liye chala jayega.

- **Bohot Mehenga Storage**: Iska per GB storage cost S3 Standard se bhi kaafi zyada hota hai. Isiliye isme data sirf tab tak rakha jata hai jab tak uspar kaam chal raha ho.

<br>
<br>

## LAB: S3 Express Lab

Chaliye, S3 Express One Zone (Directory Bucket) ki ek practical hands-on lab karte hain.

<br>

### Lab Objective

Ek **Directory Bucket** banana, ek specific **Availability Zone** chunna, aur usme dummy file upload karke uski performance check karna.

<br>

### Step 1: Bucket Creation Menu Mein Jayein

- Apne **AWS Management Consol**e mein login karein.
- Search bar mein **S3** search karke S3 dashboard par jayein.
- Orange color ke **Create bucket** button par click karein.

<br>

### Step 2: Bucket Type Badlein

Yahan par aapko dhyan se settings change karni hain, kyunki default setting normal bucket ki hoti hai.
- **Bucket type** ka section dikhega. Wahan do options honge:
  - General purpose (Default selected hoga).
  - Directory.
- Aapko **Directory** wale radio button par click karna hai.

<br>

### Step 3: Name aur Location Chunnein

Jaise hi aap Directory select karenge, niche ki settings badal jayengi.
- **AWS Region**: Apna pasandeda region chunein (e.g., US East (N. Virginia) ```us-east-1```).
- **Availability Zone**: S3 Express One Zone sirf ek hi zone mein rehta hai, isiliye AWS aapse zone poochega. Aap koi bhi ek zone select kar lijiye (e.g., ```use1-az4```).
- **Bucket name**: Isme aapko sirf apna naam likhna hai (e.g., ```puneet-fast-data```).

Notice karein: S3 aapke naam ke aage automatic aapka zone code aur suffix jod dega. Aapka final bucket name aisa dikhega: ```puneet-fast-data--use1-az4--x-s3```.

<br>

### Step 4: Storage Class aur Bucket Banayein

- Niche scroll karein, **Storage class** ka section dikhega.
- Aap dekhenge ki yahan **S3 Express One Zone** automatic select ho chuka hai aur aap ise badal nahi sakte (kyunki directory bucket bani hi sirf is class ke liye hai).
- Baaki saari settings ko default chodhkar sabse niche jayein aur **Create bucket** par click kar dein.

<br>

### Step 5: Data Upload Karein

- S3 bucket list mein aapko aapki nayi directory bucket dikhegi. Uske **naam par click** karke use open karein.
- **Upload** button par click karein.
- **Add files** par click karke apne computer se koi bhi choti file (image ya text) select karein.
- Niche scroll karke **Upload** par click kar dein.
- Upload complete hone ke baad **Close** daba dein.

Aapki file ab S3 Express One Zone mein ja chuki hai. Agar aap is data ko kisi EC2 server (jo usi same ```use1-az4``` zone mein chal raha ho) se access karenge, toh data single-digit millisecond mein load hoga!

<br>

S3 Express One Zone ko use karte ke baad agar apka kaam khatam ho gya hai to, data ko aur bucket ko delete kar de. Nahi to charge lagta rahega. Data ko tabhi rakhe jab aapka kaam ho.

