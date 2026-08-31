# S3 Versioning

S3 Versioning ka matlab hai ki ek hi object ke multiple version ko store karke rakhna, kyuki object delete bhi ho jaye to uske pichle version se recover kiya ja sake.

S3 Versioning ek hi object ke multiple versions ko preserve karne ki capability hai, taaki agar kisi existing object ko overwrite ya delete kar diya jaye, to uske purane versions ko recover kiya ja sake.

Isliye Versioning particularly useful hai:
- Accidental overwrite protection ke liye.
- Accidental deletion recovery ke liye.
- Application rollback ke liye.
- Data protection ke liye.
- Replication ke saath.
- Lifecycle management ke saath.

Jab aap kisi bucket par versioning ko ON (enable) karte hain, toh AWS S3 us bucket mein aane wali har ek file ke saare purane aur naye roop (versions) ko hamesha ke liye apne paas track karke save rakh leta hai. Iska matlab hai ki agar aapne kisi file ko galti se overwrite kar diya ya delete bhi kar diya, toh aapka data hamesha ke liye gayab nahi hota; aap jab chahein uske purane roop ko wapas la (restore kar) sakte hain.

<br>
<br>

### Sabse pehle problem samjho — Versioning ki zarurat kyu padi?

Maan lo tumhare paas S3 bucket hai:
```
company-data
```
Aur usmein object hai:
```
report.pdf
```

Aaj report.pdf ke andar data hai:
```
Sales = ₹10 lakh
```
Kal tumne same naam se new file upload kar di: ```report.pdf```.

jisme:
```
Sales = ₹15 lakh
```
data hai.

Normal S3 behavior mein existing object overwrite ho jata hai, Matlab agar bucket versioning on nhi hai to purana object overwrite ho jata hai, matlab purane object mein naya data aa jata hai aur purana data delete ho jata hai.

Ab problem ye hai:
- Agar tumhe purani ```report.pdf``` wapas chahiye to?

Agar versioning enabled nahi hai, to old version recover karna generally possible nahi hota, unless tumhare paas separate backup/replication etc. ho.

Isi problem ko solve karne ke liye bucket versioning ki jati hai.

<br>
<br>

### Versioning ka main purpose kya hai?

Versioning ka primary purpose hai accidental overwrite aur accidental deletion se data recovery capability provide karna.

Maan lo production bucket mein ```app-config.json``` file hai.

Application administrator ne galti se same ```app-config.json``` file ko same bucket mein upload kar diya jisse file wrong configuration se overwrite ho gya.

Without versioning:
```
Old Configuration
       ↓
   Overwritten
       ↓
New Configuration
```
Matlab ab ```app-config.json``` file mein naya data aa gya aur purana data delete ho gaya. Ab Purani version normally recover nahi ki ja sakti. Kyuki bucket par versioning enable nhi hai.

Lekin agar bucket par Versioning enabled hai to:
```
Version 1
Old Configuration
        │
        │ overwrite
        ▼
Version 2
New Configuration
```
Version 1 preserve rahegi. Iska matlab hai ki file mein naya data aa jayega aur purane data wali file as a version 1 bucket mein hi stored rahegi.

Tum us version ko retrieve karke data recover kar sakte ho.

<br>
<br>

### Versioning ko "backup" samajhna galat hai

S3 Versioning backup service ka replacement nahi hai.

Versioning ka primary purpose object ke versions preserve karna hai.

Suppose ransomware attack hua:
```
Original File
      ↓
Version 1

Ransomware
      ↓
File Modified
      ↓
Version 2

Ransomware
      ↓
File Modified
      ↓
Version 3
```
Versioning ki wajah se old versions exist kar sakti hain.

Lekin attacker agar permissions ke through versions bhi permanently delete kar de, to versioning alone sufficient protection nahi hai.

Production environments mein versioning ko additional controls ke saath use kiya ja sakta hai, jaise:
- MFA Delete.
- Object Lock.
- Cross-Region Replication.
- Backup/DR strategy.
- Least-privilege IAM.
- Separate administrative controls.

<br>
<br>

### Versioning enable hone ke baad kya change hota hai?

Agar bucket par versioning enabled hai, to S3 same object key ke multiple versions maintain kar sakta hai.

Example:
```
report.pdf
```
Pehli baar upload:
```
Version ID = v1
```
Dusri baar upload:
```
Version ID = v2
```
Teesri baar:
```
Version ID = v3
```

Ab S3 internally conceptually:
```
report.pdf
│
├── Version v1
├── Version v2
└── Version v3
```
rakhta hai.

Important point:
- Object key same reh sakti hai, lekin har version ka unique Version ID hota hai.

<br>

**Version ID kya hota hai?**

Version ID S3 dwara generate kiya gaya identifier hai jo object ke ek particular version ko uniquely identify karta hai.

**Unique Identity**: Jab versioning ON hoti hai, to bucket mein upload hone wale har ek object ko AWS ek unique string assign karta hai, jise Version ID kehte hain (Jaise: ```3HL4kqCxF3vth4bNsGLdskni9```).

**Unversioned Objects**: Jo files versioning on karne se pehle upload ki gayi thin, unki Version ID null hoti hai.

Version ID ka matlab object ka version number jo object ko uniquely identify karne ke liye use hota hai. Agar humko particular version par wapas jana hai to version id se hi pta lagega ki previous version konsa hai.

Example:
```
Object Key:
report.pdf
```
Versions:
```
Version ID: abc123...
Version ID: xyz789...
Version ID: pqr456...
```
Tum same object key:
```
report.pdf
```
ke multiple versions rakh sakte ho.

Isliye:
```
Object Key
    +
Version ID
```
milkar specific object version ko identify kar sakte hain.

<br>
<br>

### Versioning Ke 3 Main States (States of Bucket Versioning)

Ek S3 bucket hamesha in teen mein se kisi ek state mein hota hai:

- **Unversioned (Default)**: Jab aap bilkul naya bucket banate hain, toh usmein versioning OFF hoti hai. Isme file overwrite ya delete karne par purana data permanently loss ho jata hai. Matlab agar fresh bucket banayi hai to usme versioning off hogi, to jab aap us bucket mein file upload karoge to us file ka koi version nhi hoga, Lekin aapse thoda data add karke same file fir se upload karoge to us file ka purana data delete hoke naya data aa jayega.

- **Versioning-Enabled**: Jab aap manually ja kar versioning ko ON karte hain. Iske baad se upload hone wali har file ke multiple versions bante hain.

- **Versioning-Suspended**: Yaad rakhein, ek baar bucket par versioning ON karne ke baad aap use **kabhi bhi poori tarah se turned OFF (delete) nahi kar sakte**. Aap use sirf **Suspend (rok)** sakte hain. Suspend karne ke baad, nayi files ke naye versions banna band ho jate hain, lekin jo purane versions pehle se ban chuke the, woh bucket mein waise hi safe rehte hain.

<br>
<br>

### S3 Versioning Kaise kaam karti hai?

Jab aap ek aam computer par kisi file ko badalte (overwrite karte) hain, to Operating System (OS) hard drive ke us block par jaakar naya data likh deta hai, jisse purana data hamesha ke liye mit jata hai. Isko hum **In-place Mutation** kehte hain.

Lekin AWS S3 ek **"Immutable Object Store"** hai. Iska matlab hai ki S3 mein kisi bhi file ko upload karne ke baad use badla (modify) nahi ja sakta. To fir versioning kaise hoti hai?

S3 backend mein har ek file ko do hisson mein todkar dekhta hai:
- **Metadata**: File ka naam (Key), Size, Creation Date, Content-Type, aur sabse zaroori—Version ID.
- **Data Payload**: Asli file jo hard drive par store hoti hai.

Jab aap S3 bucket mein Versioning ko ON (Enable) kar dete hain, to AWS S3 aapke bucket ke behavior ko badal deta hai. Ab jab bhi aap koi naya action (Upload, Delete) karte hain, to S3 purane data payload aur metadata ko touch nahi karta. Wo har ek action ke liye ek naya metadata record aur naya data payload banata hai, aur unhe ek Chain (Linked List) ki tarah maintain karta hai.

<br>

Maan lijiye hamare paas ek bucket hai jisme humne Versioning ko ON (Enable) kar diya hai.

**Scenario 1: Pehli Baar File Upload Karna (Fresh Upload)**:

Jab aap bucket mein pehli baar koi file dalte hain, to S3 use ek unique ID deta hai jise **Version ID** kehte hain. Yeh file aapka **Current Version** ban jaati hai.

- **Aapne kya kiya**: Aapne ```invoice.txt``` naam ki file upload ki, Iska size 10 KB hai.
- **S3 Backend kya karega**: S3 is request ko receive karega. Wo dekhega ki is bucket ki versioning state ```"Enabled"``` hai. S3 ka internal system turant ek cryptographically secure, random string generate karega, jaise: ```#1234@qw```. Yeh iski **Version ID** ban gayi.

| Object Name (Key)      | Version ID | Status                       | Size |
| ---------------------- | ---------- | ---------------------------- | ---- |
| invoice.txt | #1234@qw   | Current Version (Latest)     | 10KB  |

Result: Agar koi developer ya application bina koi version ID bataye simple S3 URL (```s3://my-bucket/production_config.json```) par call karega, to S3 backend is table ko scan karega, dekhega ki Is Current Version = Latest kiske samne hai, aur use #1234@qw wala data (5 KB) deliver kar dega.

<br>

**Scenario 2: File Ko Dobara Upload Karna (Overwrite / Update)**:

Maan lijiye aapne us file mein kuch badlav kiye (jaise naya bill amount joda) aur use fir se same naam (```invoice.txt```) se upload kar diya.

- **Aapne kya kiya**: Nayi ```invoice.txt``` jiska size 15 KB hai usko fir se ko upload kiya.
- **S3 Backend kya karega**: S3 purani file jiska version id ```#1234@qw``` ko chuhega bhi nahi. Wo ek nayi unique Version ID generate karega, maano ```#5678@qw```. Aur file ko naye version id se saath store kar lega.

| Object Name (Key) | Version ID | Status                     | Size  |
| ----------------- | ---------- | -------------------------- | ----- |
| invoice.txt       | #5678@qw   |  Current Version (Latest)  | 15KB  |
| invoice.txt       | #1234@qw   | Noncurrent Version (Old)   | 10 KB |

Result:
- Jo sabse latest hai (ID: ```#5678@qw```), wo Current Version ban gaya.
- Purani file (ID: ```#1234@qw```) Noncurrent Version ban gayi.
- Agar aap normal download karenge, to aapko 15 KB wali nayi file milegi. Lekin purani file ab bhi bucket mein chhupi hui hai.

<br>

**Scenario 3: File Ko Delete Karna (Simple Delete)**:

