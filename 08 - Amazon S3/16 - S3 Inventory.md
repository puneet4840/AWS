# S3 Inventory

S3 Inventory apke S3 bucket ki daily aur weekly ek detailed report bana ke deta hai, jisme S3 bucket ke ander stored objects ki metadata information hoti hai.

It provides a scheduled report (daily or weekly) listing all objects and metadata in a bucket.

Includes details like object size, storage class, encryption status, replication status, object version, ETag, last modified date, and Object Lock info etc.

Amazon S3 Inventory S3 ka ek behad kaam ka storage management feature hai, jo aapko aapke bucket ke andar maujood saare objects (files) ki ek Excel/CSV jaisi report ready-made bana kar deta hai.

<br>

Agar aapke bucket mein lakhon, crores, ya billions of files hain, aur aap dekhna chahte hain ki kaun si file kitni badi hai, kab upload hui thi, uspar kya encryption laga hai, ya kaun sa lifecycle tier chal raha hai—to aap manual checking nahi kar sakte. Aise bade buckets ka auditting aur review karne ke liye S3 Inventory ka use kiya jata hai.  Yeh bina kisi API performance par asar dale background mein poore bucket ki files ka ek flat report sheet generate kar deta hai.

<br>
<br>

### S3 Inventory Kyun Zaroori Hai? (The Problem it Solves)

Maan lijiye aapke paas ek bucket hai jismein 50 Crore (500 Million) files hain. Aapko un saari files ki list chahiye.

**Normal Tarika** (```ListObjects``` API call): Agar aap AWS CLI ya code se standard list command chalayenge, to S3 ek baar mein sirf 1,000 files ki list deta hai. 50 crore files ke liye aapko lakhon API calls karni padengi, jismein bohot zyada time lagega, aapka server network load se dab jayega, aur un API calls ka bhari-bharkam bill alag se aayega.

**S3 Inventory Tarika**: S3 Inventory bina kisi API call ke, background mein khud hi bucket ka saara data scan karta hai aur ek single file (CSV, ORC, ya Parquet format) mein report bana kar aapke doosre bucket mein deliver kar deta hai. Yeh tareeqa List API calls se lagbhag 50% sasta aur bohot tez hota hai.

<br>
<br>

### S3 Inventory Report Mein Kya-Kya Information Hoti Hai?

Jab S3 Inventory ki report generate hoti hai, to aap decide kar sakte hain ki aapko list ke sath-sath kaun-kaun se **metadata columns** chahiye:
- **Bucket Name & Object Key**: File ka naam aur uska poora folder path.
- **Size**: File ka exact size bytes mein.
- **Last Modified Date**: File kab upload ya update hui thi.
- **Storage Class**: File abhi kis tier mein hai (Standard, IA, Glacier, etc.).
- **Versioning Details**: File ka Version ID aur kya wo ek Delete Marker hai.
- **Encryption Status**: Kya file encrypted hai? Agar haan, to kaun si key (SSE-S3, SSE-KMS) use hui hai.
- **Replication Status**: Kya yeh file doosre region mein replicate ho chuki hai (PENDING, COMPLETED, FAILED)?
- **Object Lock Status**: Kya file par Legal Hold ya Retention periods lagaye gaye hain?

<br>
<br>

### S3 Inventory Kaise Kaam Karta Hai? 

Aap Amazon S3 Console ke Management tab mein ja kar ek inventory rule create karte hain.

S3 Inventory ko configure karna bohot aasan hai aur yeh automatic schedule par chalta hai:
- **Scope (Kitna Data Scan Karna Hai?)**: Aap chun sakte hain ki aapko poore bucket ki list chahiye ya fir kisi specific folder (prefix) ki (jaise sirf ```images/``` ya ```logs/``` folder ka data). Saath hi aap yeh bhi set kar sakte hain ki report mein sirf Current Versions shamil hon ya saare Purane Chhupaye Hue Versions bhi dikhane hain
- **Schedule Choose Karein**: Aap setup karte waqt chun sakte hain ki aapko report **Daily** (Har roz) chahiye ya **Weekly** (Har hafte).
  - Daily: Agar aapka data har roz lakhon-karodon badal raha hai, toh S3 har 24 ghante mein ek nayi report banakar bhejega.
  - Weekly: Har Sunday ko ek consolidated report deliver hogi, jo analytics ke liye kaafi sasta aur behtareen padta hai.
- **Target Bucket Set Karein**: Jaise Server Logging mein hota hai, Inventory ki reports ko bhi ek alag Target Bucket mein save kiya jata hai taaki main data ke sath mix na ho. Ese hi S3 Inventory poore process ko do alag-alag buckets ke beech divide karta hai, source bucket aur target bucket.
  - Source Bucket: Woh main bucket jismein aapka asli data (crores of files) pada hua hai aur jiska inventory list aapko nikalna hai.
  - Destination Bucket: Woh bucket jahan S3 har din ya har hafte us inventory ki Final Report File (.csv, .parquet, ya .orc) banakar deliver karega.

⚠️ Rule: Destination bucket aur Source bucket dono ka ek hi AWS Region mein hona mandatory hai, chahe dono alag-alag AWS accounts mein hi kyun na hon.

- **Output Format Select Karein**: Aap report ko CSV (Excel mein dekhne ke liye), Apache Parquet, ya Apache ORC formats mein generate karwa sakte hain. (Parquet aur ORC formats bad data analytics ke liye best hote hain).
  - CSV: Standard Excel format, jise insaan aaram se padh sake. (S3 Batch Operations ke liye CSV zaroori hota hai).
  - Apache Parquet / ORC: Columns wale advanced compressed formats jo Big Data analytics ke liye super-fast hote hain.
- **Delivery**: S3 schedule ke mutabik background mein report generate karke target bucket mein ```.gzip``` compressed form mein deliver kar deta hai.
- **Metadata Fields (Report Mein Kya-Kya Likhna Hai?)**: Aap select kar sakte hain ki aapko list mein file ke naam ke alawa aur kya metadata chahiye, jaise:
  - Size: File ka aakar kitna hai.
  - Storage Class: Standard mein hai, IA mein hai, ya Glacier mein?
  - Encryption Status: File SSE-S3 se encrypt hai ya SSE-KMS se?
  - Object Lock Details: Kya file par Legal Hold ya Retention mode laga hua hai?

<br>
<br>

### Real-World Advanced Use Cases (Hum Ise Kyun Use Karte Hain?)

**1. SQL Query se Data Analytics (With Amazon Athena)**:

Jab S3 Inventory apni report .parquet ya .orc format mein destination bucket mein daalta hai, toh woh sath mein ek Hive symlink (index map) bhi bana deta hai. Aap seedhe Amazon Athena ke zariye us inventory list par standard SQL Query chala sakte hain:
Isse aapko milliseconds mein pata chal jayega ki aapke bucket ka kitna Terabyte data Glacier mein pada hai aur kitna Standard mein, bina kisi heavy query cost ke.

**2. Feeding into S3 Batch Operations**:

Maan lijiye aapke audit report mein aaya ki 10 Lakh files bina encryption ke padi hain aur aapko unhe encrypt karna hai. Aap S3 Inventory ki us CSV report ko utha kar direct S3 Batch Operations ko as an input (Manifest File) de sakte hain. S3 Batch us report ko padh kar un saari 10 Lakh files par ek sath ek single command se bulk action le lega.

**3. Data Replication Validation**:

Agar aap Cross-Region Replication use kar rahe hain, to aap inventory report se easily filter kar sakte hain ki kaun-kaun se objects ka replication status FAILED ya PENDING hai, taaki aap unhe wapas sync kar sakein.

<br>
<br>

### S3 Inventory Pricing

S3 Inventory bilkul free nahi hai, par yeh bohot hi sasti hai. AWS aappar **$0.0025** per million objects listed charge karta hai. Yani agar aapke bucket mein 10 Lakh (1 Million) objects hain, to ek baar report generate karne ka kharcha sirf 25 paise (INR) ke aas-pass aayega.

Agar yahi kaam aap manually list API se karte toh bill hazaron mein ja sakta tha.

**Ek Choti Si Cheez**: Jab aap pehli baar S3 Inventory setup karte hain, toh pehli report deliver hone mein AWS ko 48 ghante tak ka samay lag sakta hai, isliye turant na dikhne par ghabrayein nahi.
