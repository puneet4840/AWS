# S3 Replication

S3 Replication ka matlab hai ki S3 bucket ke objects ko automatically ek bucket se doosre bucket mein copy karna.

S3 Replication mein source bucket ke objects ki copies destination bucket mein create hoti hain.

Basic flow:
```
                 SOURCE BUCKET
                      |
                      |
                S3 Replication
                      |
                      ↓
               DESTINATION BUCKET
```

S3 Replication ek automated mechanism hai jisme AWS S3 bucket ke objects ko ek source bucket se automatically ek ya usse jyada destination buckets mein copy kiya ja sakta hai.

Simple language mein:
- Ek source S3 bucket mein object upload hota hai, aur S3 automatically us object ki copy destination S3 bucket mein bana deta hai.

**Example**:
```
India Region
ap-prod-data
     |
     | Replication
     ↓
Singapore Region
ap-dr-data
```
Agar application ka important data:
```
s3://ap-prod-data/
```
mein hai, to S3 replication ke through us data ki copy automatically:
```
s3://ap-dr-data/
```
mein maintain ki ja sakti hai.

<br>
<br>

### S3 Replication ki jarurat kyu padti hai?

Ab question hai ki jab S3 already highly durable hai, to replication ki zarurat kyun?

S3 normally highly durable storage provide karta hai, lekin organizations ko kabhi-kabhi additional copy in another bucket, account, region, ya security boundary ki requirement hoti hai.

Replication ka use different business requirements ke liye hota hai, jaise:
- Disaster Recovery.
- Business continuity.
- Cross-Region data protection.
- Compliance.
- Geographic redundancy.
- Data locality.
- Lower-latency access for users in another region.
- Centralized data aggregation.
- Production aur backup environment separation.

**1. Disaster Recovery**:

Agar primary AWS Region mein koi major problem ho, to doosre Region mein replicated data available ho sakta hai.

Example:
```
Mumbai Region
Production S3
      │
      │ CRR
      ▼
Singapore Region
DR S3
```
Agar primary Region mein problem hoti hai, organization ke paas DR location mein data ki copy ho sakti hai.

<br>

**2. Business Continuity**:

Critical business data ki additional copy maintain karke organization disaster ke baad operations recover karne ki capability improve kar sakti hai.

<br>

**3. Compliance**:

Kabhi-kabhi organization ko data ki additional copy:
- different account
- different Region
- specific geographical location

mein compliance ki wajah se maintain karni hoti hai.

Replication is type ke architectures mein useful ho sakti hai.

<br>

**4. Data Residency**:

Kuch workloads mein organization ko data ko particular geographical Region mein maintain karna hota hai. Replication architecture ko business aur regulatory requirements ke according design kiya ja sakta hai.

<br>

**5. Centralized Data Collection**:

Multiple AWS accounts ya environments se data ko ek central S3 bucket mein replicate karna bhi useful architecture ho sakta hai.

Example:
```
Account A
Production
    │
    ├─────────────┐
    │             │
Account B         │
Production        │
    │             │
    └──────┐      │
           ▼      ▼
       Central S3 Bucket
```

<br>
<br>

### S3 Replication Ke Do Main Types

AWS S3 mein replication do tarike se hoti hai, is baat par nirbhar (depend) karte hue ki aapka doosra bucket kahan hai:
- CRR (Cross-Region Replication).
- SRR (Same-Region Replication).

**CRR (Cross-Region Replication)**:

Cross-Region Replication ka matlab hai ki isme Source aur Destination buckets alag-alag AWS Regions mein hote hain (Jaise: Source bucket Mumbai mein hai aur Destination bucket North Virginia, USA mein hai).

AWS ek region se dusre region ki S3 bucket mein data ko copy karta hai.

Example: Aapka main server Mumbai (ap-south-1) mein chal raha hai aur aap chahte hain ki har file ki ek copy automatic North Virginia (us-east-1) mein chali jaye.

Kyun use karte hain? 
- Disaster Recovery ke liye. Agar khuda-na-khasta poora Mumbai data center kisi bhookamp ya disaster se down ho jaye, tab bhi aapka data USA mein safe rahega. Isse global users ke liye latency (speed) bhi achhi milti hai.
- Yeh un badi companies ke liye mandatory hota hai jinki regulatory compliance kehti hai ki backup data kam se kam 500 miles door hona chahiye.

<br>

**SRR (Same-Region Replication)**:

Same-Region Replication mein Source aur Destination buckets ek hi AWS Region mein hote hain (Jaise: Dono buckets Mumbai region mein hain).

AWS same region mein ek bucket se dusre bucket mein data copy karta hai.

Example: Dono buckets Mumbai (ap-south-1) region mein hi hain, bas unka naam alag hai (jaise ```production-bucket``` aur ```compliance-backup-bucket```).

Kyun use karte hain? 
- Compliance aur data isolation ke liye. Maan lijiye aapko apne live application data aur testing environment ka data alag rakhna hai, par dono data ek hi country/region mein hone chahiye legal reasons ki wajah se.
- Agar aapko ek hi region ke andar alag AWS accounts ya alag teams (jaise Development aur Analytics) ke beech data automatic share karna ho.

<br>
<br>

### S3 Replication Kaam Kaise Karta Hai?

Replication ko properly chalne ke liye kuch zaroori rules aur requirements hoti hain:
- **Versioning Zaroori Hai**: Replication ke liye Source aur Destination dono buckets par **Versioning ON** hona 100% compulsory hai. Agar versioning off hogi, to replication setup nahi ho sakta.
- **IAM Role**: S3 ko aapki taraf se data copy karne ki permission chahiye hoti hai, iske liye ek IAM Role banakar bucket se attach karna padta hai. Is role ke paas permission hoti hai ki woh source bucket se data padh (read) sake aur destination bucket mein data likh (write) sake.
- **Asynchronous Process**: Yeh bilkul real-time (instant) nahi hota, par upload hone ke kuch hi seconds ya minutes ke andar data doosre bucket mein copy ho jata hai. Agar aapko guranteed time chahiye, to AWS S3 Replication Time Control (RTC) ka option deta hai jo 15 minutes ke andar 99.99% data copy karne ki guarantee deta hai.

<br>
<br>

### Replication mein filters aur tags ka use

Lifecycle Rules ki tarah replication mein bhi filtering important hai.

Aap decide kar sakte ho ki:
- "Bucket ke saare objects replicate karne hain ya sirf selected objects?"

Filtering ke liye object key prefixes/tags jaise criteria use kiye ja sakte hain.

Example:
```
Prefix = backups/
```
Then:
```
backups/db.sql
backups/application.tar
```
replicate honge.

Lekin:
```
temp/test.txt
```
replicate nahi hoga agar rule usse target nahi karti.

<br>

**Tags ke basis par replication**:

Aap object tags ke basis par bhi replication scope define kar sakte ho.

Example:
```
Environment=production
```

Agar rule production-tagged objects ko target karti hai, to matching objects destination bucket mein replicate ho sakte hain.

Ye useful hai jab same bucket mein multiple types ka data ho.

Example:
```
Object A
Tag:
Environment=production
```
```
Object B
Tag:
Environment=development
```
Rule sirf:
```
Environment=production
```
ke liye configured hai. To Object A replicate hoga aur Object B nahi.

<br>
<br>

### S3 Replication ka basic architecture

Ek typical replication architecture:
```
                 AWS Region A
              ┌─────────────────┐
              │  Source Bucket  │
              │                 │
              │ object-1        │
              │ object-2        │
              │ object-3        │
              └────────┬────────┘
                       │
                       │ S3 Replication
                       ↓
              ┌─────────────────┐
              │ Destination     │
              │ Bucket          │
              │                 │
              │ object-1        │
              │ object-2        │
              │ object-3        │
              └─────────────────┘
                 AWS Region B
```

Ab question aata hai:
- S3 ko permission kaise milegi ki woh source bucket ke objects ko destination bucket mein copy kar sake?

Yahan IAM Role important hota hai.

Replication configuration ke andar S3 ko ek IAM role provide kiya jata hai.

Conceptually:
```
S3
 |
 | Assume IAM Role
 ↓
Replication IAM Role
 |
 | Read source
 | Write destination
 ↓
Destination Bucket
```

IAM role S3 ko required permissions provide karta hai.

For example, S3 ko source side par objects read karne ki permission chahiye.

Aur destination side par replicated objects create karne ki permission chahiye.

Isliye replication architecture mein IAM role + bucket permissions bahut important hain.

<br>
<br>

### S3 RTC (Replication Time Control)

Aam taur par S3 replication ka matlab data source bucket se destination bucket mein replicate hone ka koi fixed time nahi hota (yeh kuch seconds se lekar kuch ghante le sakta hai). Lekin agar aapki company ko strictly data backup jaldi chahiye, to aap S3 RTC ko ON kar sakte hain.
- AWS guarantee deta hai ki 99.99% objects upload hone ke 15 minutes ke andar replicate ho jayenge.
- Iska alag se extra charge lagta hai.

<br>
<br>

### Kya Replicate Hoga aur Kya Nahi?

Log aksar sochte hain ki replication ON karte hi poora bucket mirror ho jayega, par aisa nahi hai. Iske kuch strict rules hain:

**Kya Replicate Hota Hai?**
- Replication rule ON karne ke baad upload hone wale saare naye objects.
- Objects ke naye versions, unke tags, aur metadata.
- Objects ke naye versions aur unke Delete Markers (agar aapne configuration mein delete marker replication ON kiya hai).
- Objects ke sath unka metadata aur ACL permissions.
- Agar aapne replication rule setup karte waqt "Replicate existing objects" ka option select kiya hai, to purana data bhi copy ho jayega.

**Kya Replicate NAHI Hota (By Default)?**
- Existing Objects (Purana Data): Rule lagane se pehle jo data bucket mein pehle se tha, woh automatic copy nahi hota. (Usko copy karne ke liye aapko S3 Batch Operations ka use karna padega).
- System Deletions: Agar aap kisi file ke kisi specific Version ID ko permanently delete karte hain, toh woh destination se delete nahi hoga (Safety feature taaki galti se hacker data na mita sake).

<br>
<br>

### Replicate Existing Objects aur S3 Batch operation mein kya difference hai?

"Replicate Existing Objects" aur "S3 Batch Operations" dono hi S3 bucket ke purane (pehle se maujood) data par kaam karne ke liye use hote hain, lekin inke peeche ka mechanism aur use-case bilkul alag hai.

Inka sabse bada difference yeh hai ki Replicate Existing Objects S3 Replication rule ka hi ek hissa (checkbox) hai, jabki S3 Batch Operations ek alag standalone tool hai jo lakho-crores files par ek saath koi bhi bada kaam (jaise copy, restore, tag change) karne ke kaam aata hai.

**Replicate Existing Objects (Easy & Automatic)**:

Jab aap ek naya S3 Replication Rule (CRR/SRR) banate hain, to AWS default mein sirf unhi files ko copy karta hai jo rule banane ke baad upload hoti hain. Lekin agar aapke bucket mein pehle se 10 TB data pada hai, to AWS aapko ek checkbox deta hai: "Yes, replicate existing objects".
- Kaise kaam karta hai: Jaise hi aap is option ko select karke rule save karte hain, AWS background mein ek automatic Batch Operations Job khud hi bana deta hai.
- Kab use karein: Jab aapka ek matra maqsad yeh hai ki primary bucket ka saara purana data backup bucket mein chala jaye, aur aapko koi tension nahi chahiye.

**S3 Batch Operations (Powerful & Enterprise Control)**:

S3 Batch Operations tab kaam aata hai jab aapko billions of objects par bohot bada aur custom operation chalana ho. Yeh sirf replication tak सीमित (limited) nahi hai.

Matlab ye ek enterprise tool hai jo replication ke saath aur bhi features provide karta hai, lekin isko use karne ka main motive yehi hai ki jab aapko bohot saari files ko monitor karte hue dusre s3 bucket mein replicate karna ho.

Kaise kaam karta hai:
- Pehle aap ek Manifest File (ek CSV file ya S3 Inventory report) banate hain jismein un saare objects ki list hoti hai jinpar kaam karna hai.
- Aap AWS ko batate hain ki kya kaam karna hai (Jaise: In saari files ko Glacier se restore karo, ya In sabhi files par 'Confidential' ka tag lagao, ya Inhe doosre account mein copy karo).
- AWS ek Job create karta hai. Aap use review karte hain aur "Confirm" daba kar run karte hain.

Kab use karein:
- Jab aapko bohot bada data (billions of files) cross-account copy karna ho aur aapko ek-ek file ka status chahiye ki kaun si copy hui aur kaun si fail hui.
- Jab aapko 50 Lakh files ko ek saath Glacier se active (restore) karna ho.
- Jab aapko kisi specific folder ke saare objects par ek custom AWS Lambda function chalana ho.

<br>
<br>

### Versioning Ke Sath Deletion Kaise Kaam Karta Hai? (Crucial Concept)

Replication mein delete operations thode dhyan se samajhne padte hain:
- **Delete Marker Replication (By Default OFF)**: Agar aapne Source bucket se koi file delete ki aur wahan ek Delete Marker lag gaya, to S3 by-default us Delete Marker ko destination bucket mein replicate nahi karta. Aap chahein to ise settings mein ON kar sakte hain. Iske liye apko configuration mein delete marker replication ON karna hoga.
- **Permanent Deletions (Never Replicated)**: Agar aapne Source bucket se kisi file ka specific Version ID delete kar diya (Permanent Delete), to wo action destination bucket mein kabhi replicate nahi hota. Destination bucket mein wo version safe rahega. AWS ise isliye aana-kani karta hai takki agar koi hacker ya galti se source ka data permanent delete kare, to backup safe rahe.

<br>
<br>

### Costing 

Replication bilkul muft nahi hai, isme teen tarah ke charges lagte hain:
- **Storage Cost**: Aapko Source bucket ka storage charge to dena hi hai, sath hi Destination bucket mein jitna data copy hoga, uska storage cost bhi alag se dena hoga.
- **Data Transfer (OUT) Cost**: CRR (Cross-Region) ke case mein jab data ek region se doosre region jata hai, to AWS inter-region data transfer fees charge karta hai.
- **Replication Request Cost**: S3 jitni baar file ko copy karne ke liye internal API calls (PUT requests) chalayega, un requests ka charge lagta hai.

<br>
<br>

### Lifecycle Rules aur Replication Ka Ek Saath Use (Best Practice)

Companies aksar paise bachane ke liye dono features ko mila kar use karti hain.
- **Source Bucket (Production)**: Yahan log roz kaam karte hain. Data S3 Standard mein rehta hai.
- **Destination Bucket (Backup)**: Yahan CRR ke jariye data auto-copy hota hai. Par backup ko hamesha mehenge storage mein rakhna nuksan da hai.
- **Mila kar use**: Destination bucket par ek Lifecycle Rule laga diya jata hai jo replicate huye data ko turant ya 30 din baad Glacier Deep Archive mein bhej deta hai. Isse aapka backup safe bhi rehta hai aur uska kharcha bhi na ke barabar hota hai.
