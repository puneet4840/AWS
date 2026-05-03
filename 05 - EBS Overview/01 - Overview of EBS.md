# Overview of EBS

EBS = Elastic Block Store

EBS Volume disk hoti hai jo EC2 ke saath attach hoti hai.

Ye AWS ka block-level storage system hai jo primarily EC2 instances ke saath use hota hai. Simple language mein, EBS tumhare cloud server ki “hard disk” ya “SSD” ki tarah kaam karta hai.

Jab tum AWS mein EC2 instance launch karte ho, tab EC2 basically ek virtual machine hoti hai jo CPU, RAM, aur networking provide karti hai.

Lekin sirf CPU aur RAM se koi bhi system practical kaam nahi kar sakta, kyunki operating system ko install karne ke liye, applications ko store karne ke liye, logs ko save karne ke liye, databases ko maintain karne ke liye, aur user files ko preserve karne ke liye storage chahiye hoti hai.

Isi problem ko solve karne ke liye AWS ne EBS provide kiya, jo EC2 ke liye persistent block-level storage system hai. Persistent ka matlab ye hai ki agar tumhara EC2 stop ho jaye ya restart ho jaye, to tumhara data survive kar sakta hai, kyunki storage compute se separate maintained hoti hai.

Ye separation bahut important hai kyunki traditional physical server mein agar disk fail ho jaye to major recovery issue ho sakta hai, jabki AWS architecture storage ko more flexible aur resilient banata hai.

<br>
<br>

### EBS Ka Real Meaning

Elastic Block Store naam ke andar hi iska design philosophy chhupa hua hai.

**Elastic**:

Elastic ka matlab flexible ya dynamically adjustable hota hai.

AWS ne EBS ko is tarah design kiya hai ki tum storage size ko increase kar sako, performance type change kar sako, snapshots le sako, encryption apply kar sako, aur workload ke hisaab se optimize kar sako bina traditional hardware replacement ke.

Ye business ke liye useful hai kyunki requirements static nahi hoti; aaj 100 GB chahiye, kal 500 GB ho sakta hai.

<br>

**Block**:

Block storage ka matlab hota hai ki operating system storage ko small addressable blocks mein dekhta hai, bilkul waise hi jaise physical SSD ya HDD ko dekhta hai.

Isliye OS ko lagta hai ki uske paas ek normal disk attached hai, aur tum NTFS, EXT4, XFS jaise file systems use kar sakte ho.
Ye database aur OS workloads ke liye ideal hai kyunki low-level disk operations possible hote hain.

<br>

**Store**:

Store simply indicates data retention. Matlab data permanently store. Ye sirf temporary cache nahi hai; iska main kaam durable storage provide karna hai.

<br>
<br>

### EBS Key Terminologies

- **Volume Size** - The storage capacity of an EBS volume, measured in GiB (Gibibytes).
  
- **IOPS (Input/Output Operations Per Second)**:
  - Measures how many read and write operations per second a volume can handle.
  - Indicates the speed and responsiveness of storage. Important for transaction-heavy workloads (e.g. databases).
 
- **Baseline IOPS**:
  - The default guaranteed performance for your volume type (before bursting or provisioning).
  - Matlab minimum performance from a volume.
 
-  **Provisioned IOPS (PIOPS)**:
  -   feature where you explicitly set desired IOPS (up to 256,000).

- **Burst Performance / Burst Credits**:
  - Temporary performance boost above baseline IOPS when needed.
  - Volumes accumulate burst credits when idle and spend them under load.
  - Helps small volumes handle occasional heavy I/O spikes.

- **Throughput (MB/s)**:
  - Measures the amount of data transferred per second, typically in MB/s.
  - IOPS = “how many”; Throughput = “how fast”.

<br>
<br>

### Why EBS Instead of Internal Disk?

Traditional servers mein storage often machine ke andar physically hoti hai.

AWS ne storage ko logically separate design kiya taaki compute aur storage independently manage ho sakein. Iska benefit ye hai ki agar tumhe bigger server chahiye, to tum instance resize kar sakte ho bina data ko manually migrate kiye.

Similarly, agar tumhe storage performance improve karni hai, to tum volume type change kar sakte ho bina poora server rebuild kiye. 

Ye architecture DevOps aur enterprise operations ke liye huge advantage hai.

<br>
<br>

### EBS Ka Architecture Kaise Kaam Karta Hai?

EBS physically AWS ke backend infrastructure par redundant hardware systems par stored hota hai. Tumhara EC2 instance us storage ko network ke through access karta hai as if wo local disk ho.

Ye design users ko abstraction deta hai, jisse unhe physical disk management ki tension nahi hoti. AWS internally replication aur reliability mechanisms use karta hai taaki hardware failure ke case mein durability maintain rahe.

Yani user ke liye disk ek logical service hai, backend complexity AWS handle karta hai.

<br>
<br>

### Availability Zone (AZ) Dependency

Har EBS volume ek specific Availability Zone ke andar create hota hai. Iska reason latency aur performance optimization hai, kyunki same AZ ke andar network communication faster aur more reliable hota hai.

Agar EBS ko freely har AZ mein mount karne diya jaye to latency aur architecture complexity increase ho sakti hai. Isi liye EBS direct same AZ attachment tak limited hota hai.

Agar cross-AZ move karna ho, to snapshot create karke new AZ mein restore karte hain.

Ye design resilience aur architecture discipline dono maintain karta hai.

<br>
<br>

### Types of Volumes

AWS mein mainly 2 types ke volumes hote hain:
- Root Volume (OS Disk).
- Data Volume (Data Disk).

Jaise Azure mein OS store karne ke liye OS Disk hoti hai aur Data Store karne ke liye data disk attach hoti hai vaise hi AWS mein OS Store karne ke liye Root Volume hoti hai aur Data store karne ke liye Data volume hoti hai.

**Root Volume Kya Hota Hai?**:

Jab tum EC2 launch karte ho, operating system ko boot hone ke liye primary storage chahiye hoti hai. Is primary bootable storage ko root volume kehte hain.

Linux mein ye usually /dev/xvda ya /dev/sda1 hota hai, aur Windows mein C drive hota hai. Is root volume ke bina system boot nahi karega, kyunki OS files available nahi hongi.

**Additional Volumes Kyu Chahiye?**:

Production environments mein sirf OS storage kaafi nahi hoti. Applications, databases, logs, backups, aur user-generated content alag storage structure demand karte hain.

Isliye additional EBS volumes attach kiye jaate hain taaki data segregation ho. Data segregation ka reason management, security, scaling, aur backup simplification hai.

Example ke liye database ko OS se separate volume par rakhna restore aur performance tuning ko easier banata hai.

<br>
<br>

### Delete on Termination — Ye Critical Setting Kyu Hai?

Jab EC2 terminate hota hai, AWS optionally associated root volume ko delete kar sakta hai. Ye setting default scenarios ke liye useful hai jahan temporary instances quickly remove karne hote hain.

Lekin production ya important data workloads mein agar ye accidentally enabled raha to data permanently loss ho sakta hai. Isliye admins ko samajhna chahiye ki compute lifecycle aur data lifecycle same nahi hone chahiye.

Safe design usually important data ko preserve karta hai independent of server existence.

<br>
<br>

### EBS Volume Types

AWS mein volumes yani disk multiple types ke hote hain:

1 - SSD

2 - HDD

Ye further aur divided hote hain.

<img src="https://drive.google.com/uc?export=view&id=1JpAhOzJvqqdzH2zDNMugCp0PP6ivstWX" width="800" height="430">

<br>

<img src="https://drive.google.com/uc?export=view&id=1OhMMSkJn5Pp2MFk0vNtk5JtBXPSLkJ2c" width="800" height="400">

<br>
<br>

### gp3 and gp2

**gp2 (Old Generation)**:
- gp2 volume AWS ki old generation volume hai.

- Burst Balance: "Burst Balance" gp2 volumes (SSD) ke liye ek temporary speed boost system hai. Ye ek tarah se credits hote hain jo jarurat ke time par use hote hain.
  - Har gp2 volume ki ek fixed baseline speed hoti hai jo uske size par depend karti hai (3 IOPS per GB).
  - Jab aapka server shaant hota hai aur apni baseline speed se kam kaam kar raha hota hai, to "credits" (paise) jama hote rehte hain.
  - Jab achanak heavy load aata hai (jaise koi application start hona), to volume baseline se zyada speed (up to 3,000 IOPS) deta hai. Is extra speed ke liye "credits" jama hue credits kharch hote hain.
  - Burst Balance Percentage: AWS console mein ye 0-100% ke beech dikhta hai.
  - 100%: Credits poore hain, aap full burst (3,000 IOPS) use kar sakte hain.
  - 0%: Credtis kharch ho gaye hai. Ab aapka volume slow ho jayega aur sirf apni baseline speed par hi chalega.
  
- Performance tied to Size: Ismein IOPS (Input/Output Operations Per Second) volume ke size par depend karti hai. Har 1 GB par aapko 3 IOPS milte hain. Iska matlab agar aapko zyada speed chahiye, to aapko majbooran bada storage size lena padega.
  - Example: Agar aapko 10,000 IOPS chahiye, to aapko kam se kam 3.33 TiB ka volume lena hoga, chahe aapko sirf 500 GB storage ki zaroorat ho. Isse paise ki barbaadi hoti hai.
 
- Cost: Ye gp3 ke muqable lagbhag 20% mehenga padta hai.

<br>

**gp3 (Latest Generation)**:
- gp3 volume AWS ki latest generation volume hai.
- Independent Performance: Iski sabse badi khasiyat ye hai ki aap Storage, IOPS, aur Throughput ko alag-alag control kar sakte hain. Ab speed badhane ke liye faltu storage kharidne ki zaroorat nahi hai.
- Baseline Speed: Ismein default taur par 3,000 IOPS aur 125 MiB/s ki speed milti hai, chahe volume ka size kitna bhi chota ho.
- More Speed: Agar aapko aur zyada performance chahiye, to aap ise bina storage badhaye 16,000 IOPS aur 1,000 MiB/s tak up-scale kar sakte hain.
- Cost Effective: Ye gp2 se 20% sasta hai aur behtar performance deta hai.

<br>

### gp3 — Why Recommended?

gp3 modern general-purpose SSD hai jo most workloads ke liye balanced option hai. AWS ne gp3 ko gp2 se better isliye banaya kyunki gp2 mein performance disk size se heavily linked thi.

Iska matlab users ko unnecessary large disks leni pad sakti thi sirf performance ke liye. gp3 ne storage aur performance ko decouple kar diya, jisse users cost-efficient aur flexible architecture bana sakte hain.

<br>
<br>

### io2 and io1

io1 aur io2 ko "Provisioned IOPS" volumes kaha jata hai. Inka matlab hai ki aap AWS ko pehle se advance mein batate hain ki aapko kitni speed (IOPS) chahiye, aur AWS aapko woh speed har waqt guaranteed deta hai.

Inka istemal tab kiya jata hai jab aapko bahut zyada speed aur zero delay (latency) chahiye ho.

**io1 (First Generation High-Performance)**:
- Yeh purana high-performance SSD volume hai.
- Fix Speed: Aap jitni IOPS select karenge, volume hamesha usi speed par chalega. Ismein Burst Balance jaisa koi chakkar nahi hota.
- Limit: Aap 1 GiB storage par maximum 50 IOPS mang sakte hain.
- Capacity: Ek single volume par aap maximum 64,000 IOPS tak ja sakte hain.

**io2 (Modern High-Performance)**:
- Yeh io1 ka upgraded version hai aur aaj ke samay mein behtar choice hai.
- Zyada Efficiency: Ismein aap 1 GiB storage par 500 IOPS mang sakte hain. Iska matlab hai ki kam storage mein bhi aap bahut zyada speed hasil kar sakte hain.
- Behtar Durability: Iski reliability 99.999% hai (io1 se 100 guna behtar). Iska matlab hai ki disk fail hone ka khatra na ke barabar hai.
- Cost: io2 ki keemat io1 jitni hi hai, lekin features behtar hain.

**io2 Block Express (Sabse Tez)**:
- Jab aap io2 volume ko bade instances (jaise R5b, X2idn) ke saath jodte hain, to woh automatically "Block Express" ban jata hai.
- Speed: Yeh 2,56,000 IOPS aur 4,000 MiB/s ki throughput tak ja sakta hai.
- Use Case: Yeh un bade databases ke liye hai jo hazaron transactions ek second mein karte hain.

<br>

### io2 — Why Expensive?

Mission-critical databases jaise Oracle, SAP, ya financial systems predictable latency demand karte hain. Agar storage unpredictable ho to transactions slow ya inconsistent ho sakte hain, jo business-critical issue hai.

io2 guaranteed high IOPS aur lower latency provide karta hai, isliye premium hai. Yani tum reliability aur predictability ke liye extra pay kar rahe ho.

<br>
<br>

### HDD Types — Why Still Exist?

Har workload SSD-level random speed demand nahi karta. Large log processing ya archival data mostly sequential ya infrequent access hota hai. SSD use karna aise cases mein unnecessary expensive ho sakta hai.

st1 aur sc1 cost optimization ke liye design hue hain. Business goal har jagah max performance nahi, right-sized performance hota hai.

<br>
<br>

### Ek Practical Rule:

- Default workload → gp3
- Critical DB → io2
- Sequential big data → st1
- Archive → sc1

