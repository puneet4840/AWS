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

