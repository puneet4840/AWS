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
