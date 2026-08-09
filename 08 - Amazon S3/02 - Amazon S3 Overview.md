# Amazon S3 Overview

S3 ki full form hai: **Simple Storage Service**.

AWS S3 ek Object storage hai jo data ko objects ke form mein store karti hai. Yeh ek aisi service hai jahan aap kisi bhi tarah ka data (jaise photos, videos, documents, backups, aur logs) kitni bhi badi quantity mein store kar sakte hain aur internet se access kar sakte hain.

Amazon S3 (Simple Storage Service) ek highly scalable, highly durable aur highly available Object Storage Service hai jo virtually unlimited amount ka unstructured data Objects ke form mein store kar sakti hai.

Traditional storage mein jab aap koi file save karte hain, toh woh ek folder ke andar hoti hai (jaise ```D:\Movies\Action\IronMan.mp4```). Lekin Object Storage ek bilkul flat architecture par kaam karta hai. Yahan koi hierarchy nahi hoti. Saara data ek bade (Bucket) mein khula rehta hai.

Objects ka matlab hai ki S3 ke ander particular data ek object ki tarah treat kiya jata hai. Har Object ke teen important parts hote hain.
- **Data (The Payload)**: Yeh woh asli file hoti hai jise aap store kar rahe hain (jaise koi photo, video, ya document).
- **Metadata (Data about Data)**: Yeh file ke baare mein extra information hoti hai, jaise file ka size kya hai, isko kisne upload kiya, iska content-type kya hai, ya koi bhi custom tag jo aap lagana chahein.
- **Unique Identifier (The Key)**: Yeh har ek file ka ek unique naam ya web URL hota hai jisse us file ko poore internet par kahin se bhi dhoonda ya access kiya ja sake.

Ye teeno milkar ek complete Object banate hain. Isliye S3 mein upload hone wale har data ko object bolte hain, kyuki us object ke paas data, metadata aur unique identifier hota hai.

**Example**:

S3 service mein data upload karne ke liye tumko pehle ek bucket banani padti hai, ye bucket ek container hoti hai jo data ko store karke rakhti hai, ye internet par unique naam se hoti chaiye.

Suppose S3 bucket banake usme tum ek Image upload karte ho.
```
profile-photo.png
```

S3 ke liye ye sirf ek File nahi hai. Ye ek Object hai.

Us Object ke andar.
```
Data -> Actual Image.
Metadata -> Content-Type, Size, Upload Time, Encryption Information, Storage Class.
Object Key -> Unique Identifier (Web URL, internet par access karne ke liye).
```

Ye sab ek saath store hota hai. Isi wajah se ise Object Storage bolte hain.

<br>

Ye AWS ki ek fully managed Object Storage Service hai jiska use internet ke through kisi bhi amount ka data securely store karne, retrieve karne aur manage karne ke liye kiya jata hai.

Yahan ek phrase bahut important hai:
- Fully Managed Service.

Iska matlab ye hai ki jab tum S3 use karte ho, tab tumhe storage servers, storage disks, RAID configuration, hardware failures, disk replacement, storage scaling, operating system management, patching ya storage infrastructure ki maintenance ke baare mein bilkul bhi sochne ki zarurat nahi hoti.

Ye saari responsibility AWS ki hoti hai.

Tum sirf data upload karte ho aur AWS backend mein poora storage infrastructure automatically manage karta hai.

<br>
<br>

### AWS Ne S3 Kyun Banaya?

Maan lo tumhari company ne AWS mein ek Web Application deploy ki hai.

Architecture:
```
Internet

↓

Application Load Balancer

↓

Auto Scaling Group

↓

EC2-1

EC2-2

EC2-3
```

Ye application users ko allow karti hai ki wo apni profile picture upload kar saken. Har baar jab koi user image upload karta hai. Application ko us image ko permanently store karna hoga. 

Ab sawal aata hai. Image ko kahan store kiya jaye?

<br>

**Pehla Solution**:

Developer bolta hai.

```"Image ko EC2 ke andar hi save kar dete hain."```

Suppose EC2-1 ke andar.
```
/var/www/uploads/profile.jpg
```
Is path par image save ho gayi.

Shuru mein ye solution sahi lagta hai. Lekin production mein ye bahut badi problem create karta hai.

<br>

**Problem Number 1**:

Maan lo user ne image upload ki wo image sirf ```EC2-1``` par save hui.

Ab agla request ALB ne ```EC2-2``` ko bhej diya. Lekin ```EC2-2``` ke paas wo image hi nahi hai.

Result: Image load nahi hogi yaani application inconsistent behavior dikhane lagegi.

<br>

**Problem Number 2**:

Suppose.

```EC2-1``` crash ho gayi. ASG ne automatically nayi ```EC2``` launch kar di.

Lekin nayi EC2 ke andar purani uploaded images nahi hongi. Kyuki wo purani EC2 ke local storage mein thi. Data permanently lose ho gaya.

<br>

**Problem Number 3**:

Suppose.

Application Auto Scaling use kar rahi hai.

Aaj ```3 EC2```, Kal ko ```20 EC2```, Parson ```5 EC2```.

Har baar uploaded files ko naye ec2 instances par synchronize karna bahut difficult ho jayega.

<br>

**AWS ne kya socha?**

AWS Engineers ne socha.

Application Servers ka kaam sirf application run karna hona chahiye. Files ko store karna unka kaam nahi hona chahiye. Files ke liye ek dedicated storage service honi chahiye.

Jo.
- Highly Durable ho.
- Highly Available ho.
- Unlimited Scale ho.

Aur duniya ke kisi bhi kone se accessible ho. Isi requirement ko fulfill karne ke liye Amazon S3 banaya gaya.

<br>

Cloud computing se pehle agar kisi organization ko bahut saara data store karna hota tha to uske paas do hi options hote the.

Ya to company apna storage server kharidti. Ya SAN (Storage Area Network) ya NAS (Network Attached Storage) purchase karti.

Phir un storage devices ko data center mein install kiya jata. Storage administrator unhe configure karta.
- RAID configure hota.
- Disk failures monitor hote.
- Capacity planning hoti.

Agar storage bhar jaata to naye storage arrays purchase karne padte. Ye process expensive bhi tha aur operationally bhi complex tha.

AWS ne is poori complexity ko eliminate kar diya. Ab customer ko storage hardware manage karne ki zarurat nahi hai. Customer sirf data upload karta hai.

AWS automatically:
- Storage provide karta hai.
- Replication karta hai.
- Failures handle karta hai.
- Capacity increase karta hai.
- High Availability maintain karta hai.
- Durability ensure karta hai.

Isi wajah se S3 cloud storage ka foundation ban gaya.

<br>
<br>

### Amazon S3 Ek Object Storage Service Hai

Bahut log sirf itna yaad karte hain ki:
- "S3 is an Object Storage."

Lekin Object Storage ka actual matlab nahi samajhte. S3 Object Storage use karta hai. Iska matlab ye hai ki S3 data ko Objects ke form mein store karta hai.

Ye traditional operating system ki tarah block storage ya file system use nahi karta. AWS S3 ke liye har uploaded file ek Object hoti hai.

Chahe tum upload karo:
- Image
- Video
- PDF
- ZIP
- HTML
- Java Application
- Database Backup
- CSV
- JSON
- Log File

AWS un sabko internally Object ke roop mein treat karta hai.

<br>
<br>

### Object Kya Hota Hai?

Object sirf file nahi hota.

Object actually multiple information ka combination hota hai.

Har Object ke andar generally teen major cheezein hoti hain.
- Actual Data.
- Metadata.
- Object Key.

<br>

**1. Actual Data**:

Ye file ka actual content hota hai.

**Example**:

Ek PDF upload hui.
- Us PDF ka content actual data hai.

Ek video upload hui.
- Video ke bytes actual data hain.

Ek image upload hui.
- Us image ka binary data actual data hai.

<br>

**2. Metadata**:

Metadata ka matlab hai: ```Data ke baare mein information```.

Metadata object ka content nahi hota. Metadata object ko describe karta hai. Matlab metadata object ki information hoti hai.

**Example**:

Jaise S3 mein ek file upload hui, us file ki information metadata hoga, Jaise:
- Content-Type
- Last Modified Date
- Object Size
- Encryption Status
- Owner
- Storage Class
- ETag
- Custom Metadata

Ye sari information Metadata hoti hai.

AWS Metadata ka use object management ke liye karta hai.

<br>

**3. Object Key (Unique Key)**:

Har object ka ek unique identifier hota hai. Isi unique identifier ko Key kehte hain.

Ye key object ko internet par uniquely access karne ke liye use karte hain.

**Example**:
```
documents/resume.pdf
```
Ye poori string object ki Key hai.

Yahan:
```
documents/
```
folder nahi hai. Ye sirf object key ka prefix hai.

<br>
<br>

### Bucket kya hota hai?

S3 mein Data directly upload nahi hota. Sabse pehle ek Bucket create karni padti hai.

Bucket ko tum logically ek Storage Container samajh sakte ho. Saare Objects kisi na kisi Bucket ke andar hi store hote hain.

S3 mein data store karne ke liye aapko sabse pehle ek container banana padta hai jise Bucket kehte hain.

Poore AWS mein aapke bucket ka naam completely unique hona chahiye, kyunki iske naam se hi ek global internet link banta hai.

Example.
```
Bucket Name: "company-images"
```

Ab is Bucket ke andar.
```
profile1.png

profile2.png

banner.jpg

logo.png
```
Ye saare Objects store honge.

<br>
<br>

### Bucket globally unique kyun hota hai?

S3 Bucket ka naam poori AWS duniya mein unique hota hai. Jab tum S3 mein ek bucket create karte ho to, us bucket ka naam poori duniya matlab aws par jitni bhi bucket bani hongi un sabme unique hona chaiye.

Suppose:

Tumne Bucket banayi.
```
company-images
```
Ab duniya ka koi aur AWS customer. Isi naam ki Bucket create nahi kar sakta.

Reason:

Har Bucket ka globally unique DNS endpoint hota hai.

Example.
```
company-images.s3.amazonaws.com
```

Agar do Buckets ka naam same ho. To DNS conflict ho jayega. Matlab dono bucket ka dns name same ho jayega aur DNS same hone par dono buckets ke ander data access kiya ja sakta hai. Isliye AWS ne ye security lagai hai ki S3 bucket ka naam internet par unique hona chiaye. Jisse s3 bucket ke ander ke data ko internet par dns ke through access kiya ja sake.

Isi wajah se Bucket Name globally unique hota hai.

<br>
<br>

### Bucket Ka Internal Role

Bucket sirf data store nahi karta. Bucket ke saath bahut saari configurations associate hoti hain.

Jaise:
- Bucket Policy
- Versioning
- Encryption
- Lifecycle Rules
- Replication
- Logging
- Event Notifications
- Access Configuration
- Object Ownership

Yaani Bucket sirf object container nahi hai. Ye object management ka central component hai.

<br>
<br>

### Amazon S3 Ka Sabse Important Design Goal

S3 ko design karte waqt AWS ke engineers ke paas kuch primary objectives the. Matlab S3 service kyu banai gayi.

**1 - Unlimited Scalability**:

Customer ko kabhi storage capacity plan na karni pade.

**2 - High Durability**:

Data hardware failure ke baad bhi survive kare.

**3 - High Availability**:

Storage service continuously available rahe.

**4 - Low Operational Overhead**:

Customer ko storage hardware manage na karna pade.

**5 - Global Accessibility**:

Internet ke through data securely access kiya ja sake. Isi design philosophy ki wajah se S3 aaj AWS ki sabse widely used service hai.

<br>
<br>

### S3 Data Kahan Store Hota Hai?

Jab tum S3 mein object upload karte ho.

Kya object kisi EC2 instance ke andar store hota hai?

Nahi.

Kya object kisi EBS Volume ke andar store hota hai?

Nahi.

AWS ke paas dedicated distributed storage infrastructure hota hai.

Jab tum object upload karte ho.

AWS backend mein us object ko apne storage infrastructure ke andar store karta hai.

Ye infrastructure user ko visible nahi hota.

User ko sirf Bucket aur Objects dikhte hain.

Backend storage completely AWS manage karta hai.

<br>

Jab bhi aap S3 ke andar ek naya bucket banate hain, toh AWS aapse sabse pehle ek option poochta hai: **Region**.
- Region ka matlab hota hai duniya ki ek mukhya physical jagah jahan AWS ke data centers maujood hain (jaise Mumbai, North Virginia, Singapore, London, etc.).
- Agar aapne bucket banate waqt Mumbai Region (ap-south-1) select kiya, toh aapka data physically India ke Mumbai shahar mein bane AWS ke real data centers ke andar chala jata hai.

Yeh S3 ka sabse bada aur mukhya security feature hai. S3 ke andar jab aap koi ek single file upload karte hain, toh woh kisi ek computer ya ek single building mein store nahi hoti.
- AWS ke ek Region ke andar multiple Availability Zones (AZs) hote hain []. Ek AZ ka matlab hota hai ek ya ek se zyada physical data center buildings jo ek dusre se thodi doori par hoti hain taaki agar ek jagah koi accident (jaise flood ya fire) ho, toh dusri jagah safe rahe.
- S3 Standard class mein jab aap 1 file upload karte hain, toh AWS use backend mein automatic kam se kam 3 alag-alag Availability Zones (data centers) ke andar copy karke store kar deta hai.
- Iska matlab hai ki aapka data ek sath teen alag-alag physical locations par chal rahe hazaron high-grade hard drives (storage servers) par barabar divide aur replicate hokar save hota hai.

Agar hum bilkul physical level par baat karein, toh Amazon ke data centers ke andar bade-bade server racks hote hain jinme lakhon-croron high-capacity commercial Solid State Drives (SSDs) aur Hard Disk Drives (HDDs) lagi hoti hain.

S3 ka custom-built operating system aur software layer in saari hard drives ko aapas mein jor kar ek unlimited storage pool bana deta hai. Jab aapki file in drives par jaati hai, toh S3 uske chote-chote pieces karke unhe alag-alag physical drives par automatic distribute kar deta hai taaki read aur write speed bohot fast mile.


<br>
<br>

### S3 Region Specific Service Hai

Jab Bucket create hoti hai. Tab tum Region choose karte ho.

Example:
```
ap-south-1
```

ya
```
us-east-1
```

Bucket jis Region mein create hoti hai.

Us Bucket ka data normally usi Region ke andar store hota hai.

Lekin us Region ke andar bhi AWS data ko multiple Availability Zones mein replicate karta hai (AWS managed storage architecture ke through), taaki durability aur availability maintain rahe.

Yaani:

Region choose karna customer ki responsibility hai. Region ke andar data ko kaise safely maintain karna hai. Ye AWS ki responsibility hai.

