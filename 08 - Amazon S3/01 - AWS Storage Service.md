# AWS Storage Services

Yahan hum dekhenge ki AWS kon-kon si aur kitne types ki storage services provide karta hai.

<br>

### AWS Storage Services - Complete Foundation

Jab hum apna application AWS Cloud mein deploy karte hain, tab application sirf CPU aur RAM se nahi chalti.

Application ko hamesha kisi na kisi storage ki zarurat hoti hai.

Jaise:
- Operating System ko install hone ke liye storage chahiye.
- Application binaries ko store karne ke liye storage chahiye.
- User uploads ko store karne ke liye storage chahiye.
- Database ko records store karne ke liye storage chahiye.
- Log files ko continuously write karne ke liye storage chahiye.
- Backup ko long-term preserve karne ke liye storage chahiye.
- Multiple application servers ko ek hi file share karni ho to shared storage chahiye.

Ab agar AWS sirf ek hi storage service provide karta, to kya wo in sabhi requirements ko efficiently fulfill kar pata?

Answer hai Nahi.

Kyunki har workload ka storage access pattern alag hota hai.

Har workload ki latency requirement alag hoti hai.

Har workload ki durability requirement alag hoti hai.

Har workload ki scalability requirement alag hoti hai.

Aur har workload ki cost sensitivity bhi alag hoti hai.

Isi wajah se AWS ne storage ko ek service ke roop mein nahi, balki multiple specialized services ke roop mein design kiya.

<br>
<br>

### AWS Storage ko samajhne ka sabse important concept

AWS ki storage services ko samajhne se pehle ek concept samajhna bahut zaruri hai.

Cloud mein generally storage ko teen fundamental categories mein divide kiya jata hai:
- Block Storage.
- File Storage.
- Object Storage.

AWS ki lagbhag saari storage services in teen concepts ke upar build ki gayi hain. Agar tum ye teen concepts achhi tarah samajh gaye, to EBS, EFS, FSx aur S3 automatically samajh aa jayenge.

<br>
<br>

### 1. Block Storage

AWS mein Block Storage ek aisi storage technology hai jo bilkul aapke laptop ya physical computer ki Hard Disk (HDD) ya Solid State Drive (SSD) ki tarah kaam karti hai. Isme data ko bade-bade raw pieces mein divide kiya jata hai, jinhe hum "Blocks" kehte hain. Har ek block ka apna ek alag address hota hai, aur isme koi metadata ya direct internet URL nahi hota.

Block Storage mein poora data chhote-chhote equal-sized blocks mein divide kar diya jata hai.

<br>

**Block Storage Kaise Kaam Karti Hai?**:

Traditional Object Storage (S3) ki tarah isme puri file ek sath ek single unit bankar save nahi hoti.
- **Data Ka Partition**: Jab aap koi badi file (jaise 1 GB ki video ya database file) Block Storage mein save karte hain, toh file system us data ko chote-chote chunks ya blocks (jaise 4 KB ya 8 KB ke blocks) mein tod deta hai.
- **Independent Storage**: Yeh blocks poori drive par alag-alag jagah par store ho sakte hain. Storage controller ko pata hota hai ki kaun sa block kahan rakha hai.
- **Ultra-Fast Access**: Jab aapko file read karni hoti hai, toh storage controller saare blocks ko bohot fast speed se aapas mein jor kar aapke samne file open kar deta hai. Is fast mechanism ki wajah se iski Latency (delay time) bohot kam hoti hai, aur IOPS (Input/Output Operations Per Second) bohot high hoti hai.

<br>

**Isko Kyu Use Karte Hain?**
- **Operating System Install Karna**: Aap Amazon S3 ya EFS ke andar Windows ya Linux ka Operating System install nahi kar sakte. OS chalane ke liye aapko aisi drive chahiye jo direct server ke microprocessor se block-level par baat kar sake. Isliye har EC2 server ke sath ek Block Storage (EBS) jodi jati hai jo uski Boot Volume banti hai.

- **High Performance Aur Speed**: Agar aapko aisi speed chahiye jahan micro-seconds mein data read aur write ho sake, toh Block Storage hi akela rasta hai. Yeh heavy applications ko bina ruke chalne mein help karti hai.

- **Modify Single Blocks (Adha Data Badanna)**: Maan lijiye aapki 100 GB ki ek file hai aur aapne uske andar sirf ek line ka badlav (edit) kiya. Object Storage (S3) mein aapko poori 100 GB ki file fir se upload karni padegi. Lekin Block Storage mein sirf wahi chota sa 4 KB ka block badla jayega jisme woh line thi. Isse time aur resources dono bachte hain.

Application sirf File System ke through read aur write karti hai. Block Storage ka primary objective hota hai.
- **Very low latency**.
- **High performance random read/write operations**.
- **Operating System level integration**.

<br>

**AWS Mein Iske Do Main Types Kya Hain?**

A. Amazon EBS (Elastic Block Store) - Persistent Network Drive:
- Yeh ek virtual hard disk hai jo network ke zariye aapke EC2 server se connect hoti hai.
- Yeh hamesha safe rehti hai (Persistent). Agar aapka EC2 server kharab ho jaye ya aap use stop kar dein, toh bhi aapka data EBS volume ke andar bilkul safe rahega. Aap is drive ko us server se hata kar kisi dusre naye server ke sath bhi jod sakte hain.

B. EC2 Instance Store - Temporary Physical Drive:
- Yeh drive network par nahi hoti. Jis physical server (hardware machine) par aapka EC2 chal raha hai, yeh drive usi machine ke andar physically lagi hoti hai.
- Ye ek tarah se temporary storage hoti hai.
- Kyunki yeh physically andar lagi hai, iski speed EBS se kahin zyada fast hoti hai. Lekin iska sabse bada nuksan yeh hai ki yeh Ephemeral (temporary) hoti hai. Agar aapne apna EC2 server stop kiya ya terminate kiya, toh is drive ka saara data hamesha ke liye delete ho jayega.

<br>
<br>

### 2. File Storage

AWS mein File Storage ek aisi storage technology hai jo bilkul aapke local computer ya office ke standard folder structure ki tarah kaam karti hai. Isme data ko hierarchical format mein save kiya jata hai, jahan ek main folder (directory) ke andar sub-folders hote hain, aur unke andar aapki files hoti hain (jaise ```SharedDrive/Projects/2026/Report.pdf```).

File Storage ki sabse badi khassiyat aur taqat yeh hai ki ise ek hi waqt par ek sath saikdo (hundreds) EC2 servers aapas mein connect karke share kar sakte hain.

File Storage mein data files aur directories ke form mein organize hota hai.

<br>

**File Storage Kaise Kaam Karti Hai?**:
- **Network Attached Storage (NAS)**: File Storage network par chalti hai. Yeh ek common shared network drive ki tarah hoti hai jo hawa mein (network ke zariye) chal rahi hoti hai.
- **Standard Protocols**: Yeh storage alag-alag operating systems se connect karne ke liye standard network protocols ka use karti hai:
  - Linux servers ke liye **NFS (Network File System)** protocol ka use hota hai.
  - Windows servers ke liye **SMB (Server Message Block)** protocol ka use hota hai.

Kyunki isko ek sath bohot saare servers use kar rahe hote hain, iske paas ek automatic locking system hota hai. Agar Server-A kisi file ko edit kar raha hai, toh yeh baki servers ko us file ko modify karne se tab tak rokta hai jab tak Server-A ka kaam poora na ho jaye, taaki data corrupt na ho.

AWS mein File Storage ka primary example hai **Amazon EFS**.

<br>

**Isko Kyu Use Karte Hain?**
- **Simultaneous Multi-Server Access**: Agar aapne ek aisa application banaya hai jo multiple servers par chal raha hai (jaise Auto Scaling Group mein), aur un sabhi servers ko ek hi data read ya write karna hai, toh File Storage sabse best option hai.
- **Elastic Elasticity (Automatic Size Badhna)**: S3 ki tarah, isme bhi aapko pehle se dimaag nahi lagana padta ki mujhe 100 GB chahiye ya 500 GB. Jaise-jaise aap isme naye folders aur files dalte jayenge, iska size automatic badhta jayega. Jab aap files delete karenge, toh size automatic kam ho jayega. Aapko sirf utne ka hi paisa dena hai jitna data andar maujood hai.
- **Lift-and-Shift Compatibility**: Agar aapki company apne purane office ke data center (On-Premises) se cloud par shift ho rahi hai, aur wahan pehle se standard shared network drives use hoti thi, toh aapko apna code badalne ki koi zaroorat nahi hai. Aap AWS par direct File Storage use karke apne application ko cloud par la sakte hain.

<br>

**AWS Mein Iske Do Main Types Kya Hain?**

AWS aapko operating system aur performance ke hisab se do major file storage options deta hai:

A. Amazon EFS (Elastic File System) - Linux Ke Liye Perfect:
- Yeh fully managed network file system hai jo Linux instances ke liye bana hai.
- Isko setup karna bohot aasan hai. Yeh highly durable hoti hai aur multiple Availability Zones (alag-alag data centers) mein aapke data ko replicate karti hai taaki agar ek data center down bhi ho jaye, toh aapka shared folder hamesha chalu rahe.

B. Amazon FSx - Windows Aur High-Performance Ke Liye:
- Agar aap Linux use nahi kar rahe hain ya aapki requirement bohot zyada specialized hai, toh AWS FSx ka use hota hai. Iske kuch variants hote hain:
  - FSx for Windows File Server: Agar aap Windows Server chala rahe hain aur aapko Windows Active Directory aur SMB permissions chahiye.
  - FSx for Lustre: Agar aapko Machine Learning ya Big Data Analytics chalana hai jahan lakhon files ko ek sath super-fast speed par read karna hota hai.

**Example 1: WordPress Multi-Server Website**:

Maan lijiye aapki ek bohot badi news website hai jo WordPress par chal rahi hai. High traffic handle karne ke liye aapne 4 EC2 servers lagaye hain. Jab koi journalist website par ek nayi article ki image upload karta hai, toh woh image kisi ek server par save nahi honi chahiye, balki ek aisi common drive par save honi chahiye jise charo servers dekh sakein. Aise mein un charo servers ko ek Amazon EFS se connect kiya jata hai taaki koi bhi user kisi bhi server par aaye, use saari images barabar dikhein.

**Example 2: Corporate Windows Home Directories**:

Ek badi company apne hazaron employees ke liye Windows Virtual Desktops (AWS WorkSpaces) use karti hai. Company chahti hai ki har employee ka My Documents ya personal desktop folder ek secure jagah save rahe, aur agar employee apna virtual computer badle toh bhi uska data safe rahe. Aise mein company Amazon FSx for Windows ka shared folder backend mein setup karti hai.

<br>
<br>

### 3. Object Storage

AWS mein Object Storage ek aisi storage technology hai jahan data ko kisi traditional file system (folders aur sub-folders) ya block storage (hard drive sectors) ke roop mein nahi, balki individual "Objects" ke roop mein store kiya jata hai.

Har Object ke saath metadata hota hai. Aur ek unique key hoti hai.

Traditional storage mein jab aap koi file save karte hain, toh woh ek folder ke andar hoti hai (jaise ```D:\Movies\Action\IronMan.mp4```). Lekin Object Storage ek bilkul flat architecture par kaam karta hai. Yahan koi hierarchy nahi hoti. Saara data ek bade Bucket mein khula rehta hai.

Har ek object ke andar teen main chize hoti hain:
- **Data (The Payload)**: Yeh woh asli file hoti hai jise aap store kar rahe hain (jaise koi photo, video, ya document).
- **Metadata (Data about Data)**: Yeh file ke baare mein extra information hoti hai, jaise file ka size kya hai, isko kisne upload kiya, iska content-type kya hai, ya koi bhi custom tag jo aap lagana chahein.
- **Unique Identifier (The Key)**: Yeh har ek file ka ek unique naam ya web URL hota hai jisse us file ko poore internet par kahin se bhi dhoonda ya access kiya ja sake.

AWS mein Object Storage ka primary example hai **Amazon S3 (Simple Storage Service)**.

**Kaise Kaam Karti Hai?**
- Sabse pehle aap AWS S3 ke andar ek Bucket banate hain. Aap is bucket ko ek bada digital container samajh sakte hain. Is bucket ka naam poori duniya mein unique hona chahiye kyunki iske naam se hi ek web link banta hai.
- Kyunki har file ka ek unique URL hota hai, aap bina kisi server ke direct us URL par click karke file ko internet par open ya download kar sakte hain (agar aapne usko public access diya hua hai).

**Isko Kyu Use Karte Hain?**:
- Unlimited Scalability (Khatam na hone wali jagah): Traditional hard disks mein ek limit hoti hai (jaise 1 TB ya 2 TB). Lekin Object Storage mein space ki koi limit nahi hoti. Aap ek single bucket mein petabytes ya exabytes tak data store kar sakte hain, AWS aapko kabhi "Storage Full" ka error nahi dega.
  
- Extremely High Durability (Data khone ka zero chance): Amazon S3 aapko 99.999999999% (11 9s) durability deta hai. Iska matlab hai ki agar aap 10,000 saal tak 10 million files store karke rakhenge, toh galti se sirf ek file khone ka chance hota hai. AWS aapki file ko piche alag-alag data centers mein automatic copy karke rakhta hai.
  
- Bohot Hi Sasti Cost (Paisa Bachana): Yeh block storage (EBS) ke mukable bohot zyada sasti hoti hai. Saath hi isme aapko kisi server ko 24 gante chalu rakhne ka karcha nahi dena padta. Aap jitna data rakhenge, sirf utne ka hi paisa dena hota hai.

**Example 1: OTT Platforms (Netflix / Prime Video)**: Netflix apne saare movies aur web series ki video files ko Amazon S3 (Object Storage) mein store karta hai. Jab aap apne mobile par koi movie play karte hain, toh Netflix ka app direct S3 se us video file ke data ko stream karke aapke screen par dikhata hai. Unhe in files ko kisi mehange live server drive par rakhne ki zaroorat nahi hoti.

**Example 2: Social Media Platforms (Instagram / Facebook)**: Jab aap Instagram par koi photo ya reel post karte hain, toh woh direct ek Object Storage bucket mein chali jati hai. Us photo ka ek unique URL ban jata hai jo aapke profile database ke sath link ho jata hai. Jab bhi koi aapki profile dekhta hai, woh photo direct cloud bucket se load hoti hai.

<br>
<br>

### AWS Storage Services

| Service             | Storage Type                    | Primary Purpose                                          |
| ------------------- | ------------------------------- | -------------------------------------------------------- |
| Amazon EBS          | Block Storage                   | EC2 Operating System, Databases, Applications            |
| EC2 Instance Store  | Local Block Storage             | Temporary high-speed local storage                       |
| Amazon EFS          | File Storage                    | Shared Linux file system across multiple EC2 instances   |
| Amazon FSx          | Managed Enterprise File Storage | Windows, Lustre, NetApp ONTAP, OpenZFS workloads         |
| Amazon S3           | Object Storage                  | Images, videos, logs, backups, static assets, data lakes |
| AWS Storage Gateway | Hybrid Storage                  | On-premises storage integration with AWS                 |
| AWS Backup          | Backup Management               | Centralized backup and recovery                          |
| AWS DataSync        | Data Transfer                   | Online migration between storage systems                 |
| AWS Snow Family     | Offline Data Transfer           | Large-scale physical data migration                      |

