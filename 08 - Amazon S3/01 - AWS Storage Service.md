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

Block Storage mein poora data chhote-chhote equal-sized blocks mein divide kar diya jata hai.

Operating System un blocks ko manage karta hai. Application ko ye nahi pata hota ki data kitne blocks mein divide hua ha

Application sirf File System ke through read aur write karti hai. Block Storage ka primary objective hota hai.
- **Very low latency**.
- **High performance random read/write operations**.
- **Operating System level integration**.

Isi wajah se Block Storage databases aur operating systems ke liye best hoti hai. AWS mein Block Storage ka primary example hai **Amazon EBS**.

Aur temporary Block Storage ka example hai **EC2 Instance Store**.

<br>

### 2. File Storage

File Storage ka concept Block Storage se completely different hai.

File Storage mein data files aur directories ke form mein organize hota hai.

Yahan ek centralized file system hota hai. Multiple servers ek hi file system ko simultaneously mount kar sakte hain. Aur sabhi servers ek hi files dekhte hain.

AWS mein File Storage ka primary example hai **Amazon EFS**.

**Example**:

Suppose tumhare paas ek Auto Scaling Group hai. Usme 20 EC2 Instances chal rahi hain.

Application ko ek shared configuration file read karni hai. Ya shared images use karni hain.

Agar har EC2 ke paas apni alag EBS Volume hogi. To har EC2 par file manually copy karni padegi. Aur agar image update hui. To 20 servers par update karna padega. Ye scalable approach nahi hai.

AWS ne is problem ko solve karne ke liye Amazon EFS banaya. EFS ek centralized file system provide karta hai.

Sabhi EC2 usi shared file system ko access karti hain. File ek jagah update hui. Automatically sabhi EC2 latest version dekhengi.

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

