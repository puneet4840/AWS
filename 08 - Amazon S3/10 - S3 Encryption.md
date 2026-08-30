# S3 Encryption

### Sabse Pehle Problem Samjho

Jab tum S3 bucket mein koi file upload karte ho, tab wo file kis state mein hoti hai?

Example:
```
salary.xlsx
```
Ya:
```
customer-data.csv
```
Ya:
```
medical-record.pdf
```

Ye files agar plain text mein store ho jayein aur koi unauthorized person storage ko access kar le, to wo directly data padh sakta hai.

Isi problem ko solve karne ke liye **Encryption** use hoti hai.

<br>
<br>

### Encryption Kya Hoti Hai?

Encryption ka simple meaning hai ki original readable data ko unreadable form mein convert kar dena ki bina correct key ke koi encrypted data ko samajh na sake.

Encryption ka basic purpose hai readable data ko unreadable form mein convert karna, taaki agar unauthorized person ko encrypted data mil bhi jaye, to woh encryption key ke bina original information ko practically recover na kar sake.

**Example**:

Maan lo tumhare paas ek file hai:
```
employee-salary.csv
```

File ke andar:
```
Puneet     ₹1,00,000
Amit       ₹80,000
Rahul      ₹90,000
```
Ye **plaintext** data hai.

Agar koi unauthorized person raw data dekh le, to information samajh sakta hai.

Lekin **Encryption** ke baad data kuch is tarah ka unreadable **ciphertext** ban jata hai:
```
8F4A9C7D...
A81D73BC...
91C72F10...
```

Ye encrypted text kisi bhi insaan ke liye meaningless hota hai. Sirf jis system ke paas correct encryption key hogi, wahi original data wapas bana sakta hai.

Is process ko **decryption** kehte hain.

<br>

**Encryption ka Basic Formula**

Plain text data ko ciphertext mein convert karne wale process ko **Encryption** kehte hain.

```
Plaintext
   +
Encryption Key
   +
Encryption Algorithm
   ↓
Ciphertext
```

Aur ciphertext data ko plaintext form mein convert karne wale process ko **Decryption** kehte hain.

```
Ciphertext
   +
Decryption Key
   +
Decryption Algorithm
   ↓
Plaintext
```

<br>
<br>

### Encryption ke do major stages

AWS S3 mein encryption do tarike se hoti hai:
- **Encryption in Transit**: Jab data aapke computer se internet ke zariye S3 bucket tak ja raha ho, ise Encryption in Transit kehte hain. (Iske liye **HTTPS/SSL** protocol ka use hota hai taaki beech raste mein koi data chura na sake).
- **Encryption at Rest**: Jab data S3 ke hard drives par permanently save ho jata hai, to wahan data encrypted form mein store hota hai. Matlab data store hone ke baad bhi secure hona chaiye.

Jab koi file S3 mein upload hoti hai, tab uski journey ko do parts mein divide kiya ja sakta hai.
- Data in transit and Data in rest.

```
User

↓

Internet

↓

Amazon S3

↓

Storage
```

Is journey mein do jagah security ki zarurat hoti hai.
- Data travel karte waqt secure hona chahiye.
- Data store hone ke baad bhi secure hona chahiye.

Isi wajah se AWS do major encryption concepts provide karta hai.
- Encryption in Transit.
- Encryption at Rest.

<br>
<br>

### Encryption in Transit

Encryption in Transit ka matlab hai Jab data network ke through travel kar raha ho, tab usse encrypt karke bhejna.

Matlab jab data tumhare local computer se S3 tak upload hone jaye to vo encrypted hoke jaye.

Jab tumhare data ko client/application se S3 tak network ke through transfer kiya ja raha hai, us communication ko encrypted karna.

S3 ke mamle mein, jab aap apne laptop se koi file S3 bucket mein upload karte hain, ya S3 se koi photo download karte hain, toh data internet ke taaron aur routers se hokar guzarta hai. Agar yeh data raste mein khula (Plain Text) raha, toh raste mein baitha koi bhi hacker (jaise public Wi-Fi par) use chura sakta hai ya dekh sakta hai. Ise Man-in-the-Middle (MITM) attack kehte hain. Encryptionn in transit is problem ko silve karta hai.

**Example**:

Tum apne laptop se S3 bucket mein file upload karte ho.

Journey:
```
Laptop

↓

Internet

↓

AWS S3
```

Agar ye data plain text mein travel kare. To beech mein koi attacker network traffic capture karke file dekh sakta hai. Isliye data ko encrypt karke S3 tak behjna hota hai.

Isi risk ko eliminate karne ke liye AWS HTTPS/TLS use karta hai.

Architecture:
```
Application
     │
     │ HTTPS / TLS
     │
     ▼
    S3
```
AWS documentation ke according S3 data in transit ko SSL/TLS ke through protect kiya jata hai.

<br>

**Yeh Kaam Kaise Karta Hai?**

S3 mein data ko raste mein secure karne ke liye **HTTPS (Hypertext Transfer Protocol Secure)** ka use hota hai, jo backend par **TLS (Transport Layer Security)** protocol par chalta hai.

HTTPS ka naye version ko TLS kehte hain.

**TLS kya karta hai?**

Suppose tum S3 mein file upload kar rahe ho:
```
employee-data.xlsx
```

Laptop se file directly plain form mein nahi jaati. Pehle tumhare browser(jisme tumne AWS portal open kiya hai) aur S3 ke beech mein ek  TLS connection establish hota hai.

Phir file encrypted channel ke andar travel karti hai. Matlab laptop se nikalne se pehle ye file encrypt ho jati hai, fir encrypt data internet par travel karta hai aur S3 tak pahuchta hai, fir S3 us data ko decrypt karta hai aur apne storage mein store karta hai.

Conceptually.
```
Laptop

↓

TLS Tunnel

↓

Amazon S3
```

Is encrypted tunnel ke bahar koi attacker traffic dekh bhi le. To usse sirf encrypted packets milenge. Original file nahi. Isi ko Encryption in Transit kehte hain.



**HTTPS aur TLS ka relation**:

AWS S3 HTTPS use karta hai. HTTPS ke andar TLS protocol ka use hota hai.

Example URL.
```
https://company-data.s3.amazonaws.com
```
Isliye production mein S3 ko HTTP ke bajay HTTPS ke through access karna best practice hai.

<br>

**Encryption Process**:

S3 mein data ko raste mein secure karne ke liye HTTPS (Hypertext Transfer Protocol Secure) ka use hota hai, jo backend par TLS (Transport Layer Security) protocol par chalta hai.

```
[Aapka Laptop/Server] ---------(Encrypted TLS Tunnel)---------> [AWS S3 Endpoint]
   (Data locked with                 (Raste mein hacker ko        (S3 decrypts data
    Session Key)                      sirf kachra dikhega)         and stores it)
```

- **Handshake**: Jab aap S3 ko data bhejna shuru karte hain, toh aapka system aur AWS S3 aapas mein ek "TLS Handshake" karte hain. AWS S3 apna ek digital certificate dikhata hai jo prove karta hai ki woh asli AWS hi hai, koi fraud nahi.
- **Key Exchange**: Dono system aapas mein milkar ek temporary Session Key (ek baar istemal hone wali chabi) banate hain.
- **Secure Tunnel**: Ab aapka data us Session Key se lock hokar ek secure tunnel ke zariye travel karta hai. Agar koi hacker raste mein data intercept bhi kar le, toh use sirf random kachra shabdon (Ciphertext) ka dher dikhega, jise decode karna impossible hai.
- **Arrival**: Jaise hi data S3 ke endpoints par pahunchta hai, S3 use usi Session Key se khol (decrypt) leta hai aur uske baad bucket mein safe rakh deta hai.

<br>

**AWS S3 Mein Ise Enforce (Mandatory) Kaise Karein?**

Halanki AWS S3 default roop se HTTPS links ko support karta hai, lekin yeh purane aur unsecure HTTP (bina S3 encryption ke) requests ko block nahi karta jab tak aap khud na bolein.

Agar aap ek Secure Cloud Architect hain, toh aapki zimmedari hai ki aap aisi Bucket Policy lagayein jo kisi bhi HTTP request ko laat maar kar bahar nikal de aur sirf HTTPS ko allow kare.

**HTTPS-Only Bucket Policy**:

Aap apne bucket ke Permissions tab mein ja kar yeh policy lagate hain:
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "EnforceHTTPSOnly",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::aapke-bucket-ka-naam",
        "arn:aws:s3:::aapke-bucket-ka-naam/*"
      ],
      "Condition": {
        "Bool": {
          "aws:SecureTransport": "false"
        }
      }
    }
  ]
}
```

Is Policy Ka Technical Logic Samjhein:
- ```"Effect": "Deny"```: Hum saaf keh rahe hain ki hume block karna hai.
- ```"aws:SecureTransport": "false"```: Yeh AWS ka ek global variable hai. ```"false"``` ka matlab hai ki request HTTP (unsecure) se aayi hai. Agar yeh false hai, toh policy use turant Deny (Block) kar degi.
- Kyunki AWS mein Deny sabse pehle mana jata hai, isliye ab koi bhi chah kar bhi bina HTTPS ke data na toh upload kar payega aur na hi download.

<br>
<br>
<br>

### Encryption at Rest

Apne file S3 mein upload ki aur ab maan lo file successfully S3 mein pahunch gayi.

Question:
- Ab ye file storage ke andar kis form mein rahegi?

Agar AWS usse plain text mein store kare. To storage compromise hone ki situation mein data expose ho sakta hai. Isi problem ko solve karta hai **Encryption at Rest**.

Encryption at Rest ka matlab hai Data storage ke andar encrypted form mein stored rehta hai.

Conceptually:
```
Client
   │
   │ HTTPS/TLS
   ▼
 S3
   │
   │ Encryption at rest
   ▼
Encrypted Storage
```

Encryption at rest ka matlab hai jab object S3 ke storage infrastructure mein stored hai, tab us object ke data ko encrypted form mein maintain karna.

Maan lijiye AWS ke data center mein koi chor ghus jaye ya koi badmaash employee wahan ki physical hard drive hi nikal kar bhaag jaye. Agar data encrypt nahi hai, toh woh drive ko apne computer mein laga kar aapki saari personal files padh lega. Lekin agar Encryption at Rest ON hai, toh use drive ke andar sirf ek un-readable kachra code (Ciphertext) dikhega, jise padhna impossible hai.

**Example**:

Suppose tumne:
```
salary.xlsx
```
S3 mein upload kiya.

S3 object ko storage infrastructure par encrypted form mein store karta hai.

Storage ke andar:
```
8A92XJQ91LK2PQ8...
```
Jab authorized user file download karta hai. AWS automatically decrypt karke original file return kar deta hai.

<br>

**S3 Encryption at Rest ka flow**:

```
User Upload

↓

Amazon S3 receives object

↓

Object encrypted

↓

Encrypted object stored

↓

Authorized download

↓

Automatic decryption

↓

Original file returned
```

<br>
<br>

### Encryption at rest ke types:

S3 mein encryption at rest do tarah se hoti hai: **Server-Side Encryption (SSE)** (jahan AWS data ko save karte samay encrypt karta hai) aur **Client-Side Encryption** (jahan aap khud data ko upload karne se pehle encrypt karte hain).
- **Server-side Encryption (SSE)**.
- **Client-side Encryption**.

By default, AWS S3 ke har naye bucket mein Server-Side Encryption (SSE-S3) automatically enabled hoti hai.

<br>

### 1. Server-Side Encryption (SSE)

Server-Side Encryption (SSE) ka matlab hai ki jab aap AWS S3 mein data upload karte hain to AWS S3 bucket mein data save hone se pehle use encrypt kar deta hai. 

AWS S3 ka server-side encryption default behaviour hai. Matlab jab bhi aap ek nayi bucket banaoge aur jo bhi data usme upload karoge to by-default server-side encryption on rahega aur AWS khud se un keys ko manage karta hai, AWS khud se data ko storage mein save karne se pehle usko encrypt karega aur storage mein encrypted data ko save karega. Aur jab aap S3 se data download karte hain, toh AWS use automatically decrypt karke aapko deta hai.

**AWS ne Server-Side Encryption kyun banaya?**

Har developer cryptography expert nahi hota. Agar har application ko khud encryption implement karni pade.

To.
- bugs aayenge.
- wrong algorithms use honge.
- key management difficult hoga.

AWS ne kaha: "Encryption hum handle karenge."

Isi wajah se Server-Side Encryption bahut popular hai.

<br>

**Server-side Encryption 3 type se hota hai**:

Humko pta hai data ko encrypt karne ke liye ek Key ki zaroori hoti hai. Ab AWS humkp 3 options deta hai ki aapko server-side encryption ke liye key kaise manage karni hai:
- SSE-S3 (Server-Side Encryption with Amazon S3-Managed Keys).
- SSE-KMS (Server-Side Encryption using AWS manaed service KMS).
- SSE-C (Server-Side Encryption with Customer-Provided Keys).

<br>
<br>

**1 - SSE-S3 (Server-Side Encryption with Amazon S3-Managed Keys)**:

SSE-S3 (Server-Side Encryption with Amazon S3-Managed Keys) ka matlab hai ki encryption ki puri zimmedari Amazon S3 ki hai. Isme encryption keys ko poori tarah se AWS S3 khud manage aur rotate karta hai.

User ko kisi bhi external Key Management System (jaise AWS KMS) ko setup karne ki zaroorat nahi hoti. Ek Cloud Administrator ya Developer ke taur par, aapko keys ke lifecycle (creation, expiration, deletion, permission policies) ki koi chinta nahi karni padti.

Yeh **AES-256** (Advanced Encryption Standard) algorithm ka use karta hai. Jab aap file bhejte hain, S3 backend par ek unique Data Key banata hai, use AES-256 (duniyaka sabse strong encryption standard) se lock karta hai, aur data ko save kar deta hai.

Yeh bilkul free hai, iska koi extra charge nahi lagta aur aapko koi key manage nahi karni padti.

Tum:
- Key create nahi karte.
- Key manually rotate nahi karte.
- Key policy manage nahi karte.
- Key deletion lifecycle manage nahi karte.

AWS ye responsibility handle karta hai. Isi wajah se SSE-S3 operationally simplest encryption option hai.

<br>

**SSE-S3 Kaise kaam Karta hai?**

SSE-S3 sirf ek single key se aapka saara data encrypt nahi karta. Agar ek hi key se billions of objects encrypt kiye jayein, toh cryptographic risk badh jata hai. Isliye AWS ek Two-Tier Key Hierarchy (**Envelope Encryption**) ka use karta hai:
- Master Key (Root Key): Amazon S3 ke pass har region mein ek master key hoti hai (jo security modules ke andar safely locked hoti hai). Yeh master key kabhi bhi S3 environment se bahaar nahi aati aur na hi kisi user ko dikhti hai.
- Data Key (Object Key): Jab bhi aap S3 mein koi naya object (jaise koi photo, video, ya document) upload karte hain, toh S3 us specific object ke liye ek bilkul nayi, unique Data Key generate karta hai. Aapke object ke actual bytes (data) ko isi Data Key se encrypt kiya jata hai.

**Envelope Encryption Ka Pura Process**:

Encryption Phase (Jab Data S3 mein jata hai):
- Aapne request bheji: "Mera ```invoice.pdf``` upload karo."
- S3 ne backend mein ek unique Data Key banayi.
- Us Data Key se ```invoice.pdf``` ko encrypt karke plain text se ciphertext (readable format se unreadable format) mein badal diya.
- Ab twist: S3 apni Master Key ka use karke us Data Key ko bhi encrypt kar deta hai (Ise hum Encrypted Data Key kehte hain).
- S3 aapke encrypted object (```invoice.pdf```) aur uski Encrypted Data Key ko ek sath S3 storage disk par save kar deta hai.
- Jo original plain Data Key thi, use memory se permanently wipe (delete) kar diya jata hai taaki woh kisi ke hath na lage.

Decryption Phase (Jab Data S3 se bahar nikalta hai):
- Aapne request ki: "Mujhe ```invoice.pdf``` download karna hai."
- S3 storage se encrypted object aur uski judi hui Encrypted Data Key ko nikalta hai.
- S3 apni Master Key se us Encrypted Data Key ko decrypt karta hai, jisse plain Data Key wapas mil jaati hai.
- Us plain Data Key se encrypted object (```invoice.pdf```) ko decrypt kiya jata hai.
- S3 aapko aapki original file clean format mein deliver kar deta hai.

<br>

**SSE-S3 Kab Use Karein?**

Agar tumhari requirement simply hai:
- "Mujhe S3 data at rest encrypted chahiye aur mujhe apni encryption keys independently manage karne ki requirement nahi hai."

Yeh option un organizations ke liye convenient hai jahan advanced key management requirement nahi hoti.

Examples:
```
Company ke paas:

Public Document
Website assets
Application files
Non-sensitive logs
General documents
Backups
```
Store hain.

Company ko sirf data-at-rest encryption chahiye. To SSE-S3 sufficient ho sakta hai.

<br>

**SSE-S3 Comprehensive Enterprise Architecture Example**:

Chaliye ek badi E-Commerce Company (jaise Flipkart ya Amazon) ka example lete hain jo S3 ko apna Data Lake banakar use kar rahi hai.

Maan lijiye is company ke paas har din 10 Million (1 Crore) customers ke transaction logs aur invoices aate hain. Security compliance ke tehat, in sabhi logs ko rest par encrypt rakhna compulsory hai.

Step 1: Bucket-Level Automation Configuration:
- Company ka Cloud Architect S3 bucket ki settings mein jata hai. Pehle ke zamane mein encryption manually set karni padti thi, par AWS ke naye rule ke mutabik, har naye bucket par SSE-S3 default roop se enabled hota hai. Phir bhi, architect properties mein jaakar confirm karta hai ki "Bucket Key" aur "SSE-S3" turned on hain.

Step 2: High-Scale Ingestion (Data Insertion):

Company ke application servers lagatar S3 mein log files push kar rahe hain:
- ```log_001.txt``` aaya -> S3 ne instantly ek unique key banayi -> encrypt kiya -> disk par save kiya.
- ```log_002.txt``` aaya -> S3 ne ek doosri, bilkul alag unique key banayi -> encrypt kiya -> disk par save kiya.

Is pure scale par (chahe ek second mein 50,000 files aayein), company ke application ko encryption ke liye koi extra code nahi likhna padta. Unka normal PutObject API call waise hi kaam karta hai jaise bina encryption ke karta.

Step 3: Analytics Tool Accessing Data:

Do din baad, Data Analytics team ek AWS Athena query chalati hai taaki un logs ko analyze kiya ja sake.
- Athena S3 se data read karne ki request bhejta hai.
- S3 pehle check karta hai ki kya Athena ke paas us bucket ke liye IAM (Identity and Access Management) Role aur permission hai?
- Agar IAM permission valid hai, toh S3 background mein automatically un 10 million files ko decrypt karta jata hai aur Athena ko stream karta jata hai.
- Analytics team ko pata bhi nahi chalta ki data encrypted format mein store tha; unhe unka SQL query result normal tarike se mil jata hai.

<br>

**SSE-S3 Ke Technical aur Operational Fayde**:
- Performance Efficiency (Zero Latency Impact): SSE-S3 ka sabsay bada fayda yeh hai ki yeh hardware-accelerated encryption ka use karta hai. S3 ka internal infrastructure is tarah design kiya gaya hai ki encryption/decryption process ki wajah se aapke data transfer speed (throughput) ya download latency par koi asar nahi padta.
- Automatic Key Rotation: Cryptographic security ka ek asool hai ki keys ko samay-samay par badalna chahiye (key rotation). SSE-S3 mein Amazon S3 khud back-end par cryptographic master keys ko regularly rotate karta rehta hai. Is rotation se aapke purane data ki accessibility par koi farq nahi padta, par security hamesha tight rehti hai.
- Cost Optimization (Free of Cost): Agar aap SSE-KMS use karte hain, toh har mahine KMS key ka $1 ya $2 fix charge lagta hai, aur usse bhi bada expense hota hai per-request charge (KMS API calls ke paise). Agar aapke paas billions of requests hain, toh bill hazaron dollars mein ja sakta hai. SSE-S3 bilkul free hai. Ismein encryption ke liye koi extra penny charge nahi ki jaati.
- Reduced Human Error: Kyunki ismein insani interference (human intervention) zero hai, isliye koi developer galti se encryption key delete nahi kar sakta, na hi kisi key ki access policy galat configure karke pure production setup ko down kar sakta hai.

<br>
<br>

**2 - SSE-KMS (AWS Key Management Service)**:

SSE-KMS ka matlab hai Encryption S3 karta hai, lekin encryption keys AWS ki service KMS manage karta hai. Yahan S3 aur KMS dono milkar kaam karte hain.

SSE-KMS mein AWS ek service jisko AWS KMS (Key Management Service) kehte hain, ye service in keys ko manage karti hai.

SSE-S3 mein aapne dekha ki sab kuch Amazon S3 khud hi manage karta hai—keys bhi uski, storage bhi uska. Lekin enterprise security ka ek sabsay bada asool hai: Separation of Duties (Kaam ka batwara). Yaani, jo service data store kar rahi hai (S3), uske paas encryption keys ka poora control nahi hona chahiye.

SSE-KMS isi problem ko solve karta hai. Ismein:
- Storage ka kaam Amazon S3 karta hai.
- Key Management (banaana, delete karna, rotate karna, aur permissions set karna) ka kaam ek dedicated security service karti hai, jise AWS KMS (Key Management Service) kehte hain.
- Hardware Security Modules (HSMs): KMS ke andar jo keys hoti hain, woh FIPS 140-3 Level 3 validated physical cryptographic hardware (HSMs) ke andar generate aur store hoti hain. In keys ko un hardware se bahar nikalna na-mumkin hota hai.

<br>

SSE-KMS ko use karne ke liye aap AWS ki server KMS ko S3 ke saath integrate kiya jata hai, jab jake keys manage hoti hain.

Architecture:
```
Application
     │
     ▼
    S3
     │
     ├──────────► AWS KMS
     │                │
     │                ▼
     │           KMS Key
     │
     ▼
Encrypted Object
```
Yahan S3 object ko encrypt karta hai, lekin encryption key management mein AWS KMS involved hota hai.

<br>

**KMS Kya Hai?**

AWS Key Management Service (KMS) ek managed service hai jo cryptographic keys create, control aur manage karne ke liye use hoti hai.

KMS tumhe zyada control deta hai.

Tum:
- Customer managed KMS keys create kar sakte ho.
- Key policies define kar sakte ho.
- IAM permissions control kar sakte ho.
- Key rotation configure kar sakte ho.
- CloudTrail ke through KMS API usage audit kar sakte ho.

SSE-KMS mein encryption to S3 service karti hai lekin keys ka management S3 service na karke KMS service karti hai.

<br>

**Key Architecture: KMS Ke Andar Kya Hoti Hain Keys?**

SSE-KMS ko samajhne ke liye aapko do tarah ki KMS keys ke baare mein pata hona chahiye:
- AWS Managed Key (aws/s3): Yeh key AWS aapke liye automatic banata hai jab aap pehli baar SSE-KMS use karte hain. Yeh bilkul free hoti hai (par use karne ke API charges lagte hain). Iski key policy ko aap badal nahi sakte.
- Customer Managed Key (CMK): Yeh woh asli weapon hai jise badi companies use karti hain. Yeh key aap khud create karte hain. Iska poora control aapke paas hota hai—aap taiyar karte hain ki kaun is key ko use kar sakta hai aur kaun nahi. Aap ise jab chahein disable ya delete bhi kar sakte hain.

<br>

SSE-KMS mein envelope encryption ka use hota hai.

**Envelope Encryption Kya Hai?**

Technical terms mein, data ko encrypt karne ke liye hume ek cryptographic key ki zaroorat hoti hai. Agar hum ek hi key se saara data encrypt karte rahein, ya phir key ko galat jagah store kar dein, toh poora system unsafe ho jata hai. Is problem ko solve karne ke liye janam hua Envelope Encryption ka.

Envelope Encryption ka seedha niyam hai: Data ko encrypt karne ke liye ek alag chabi hogi, aur us chabi ko surakshit (secure) rakhne ke liye ek dusri master chabi hogi.

Example:

Maan lijiye aapke paas ek bohot hi kimti Vasiyat (Will Document) hai jise aap duniya se chhupa kar rakhna chahte hain.
- The Data: 
- DEK (Data Encryption Key): Yeh woh key hoti hai jiska use aapke actual data (jaise files, databases, photos) ko encrypt aur decrypt karne ke liye kiya jata hai.
- KEK (Key Encryption Key): Yeh aapki Master Key hoti hai. Iska kaam sirf DEK (data key) ko encrypt (lock) karna hota hai. Yeh hamesha ek super secure jagah jaise KMS (Key Management Service) ya HSM (Hardware Security Module) ke andar rehti hai.

<br>

**1 - AWS Managed Keys**:
- Kise milti hai?: Jab aap S3 bucket banate hain aur bina kisi mehnat ke KMS encryption select kar lete hain, toh AWS aapke liye background mein ek key bana deta hai. Iska naam default roop se ```aws/s3``` hota hai. Yeh keys AWS services (jaise S3) aapke account mein automatically banati hain
- AWS is key ko har 3 saal (1095 din) mein automatically rotate (badal) deta hai.
- Kiske liye sahi hai?: Un companies ya projects ke liye jo security toh chahti hain, lekin unhe bohot strict compliance (jaise banking rules) follow nahi karne aur jo extra paisa kharch nahi karna chahte.
- Aap inki Key Policy ko change nahi kar sakte aur na hi inhein manually delete kar sakte hain.

**2 - Customer Managed Keys (CMK)**:
- Kise milti hai?: Is key ko aap khud KMS console mein ja kar "Create Key" par click karke banate hain. Isko aap apna manpasand naam (Alias) de sakte hain, jaise MyCompany-S3-Production-Key.
- Management & Control: Is par aapka 100% full control hota hai. Aap ek-ek user ko chun kar permission de sakte hain ki "Rahul is key se data encrypt kar sakta hai, lekin decrypt nahi kar sakta." Aap jab chahein is key ko ek click se Disable (band) kar sakte hain, jisse poora data block ho jayega.
- Kharcha (Cost): Iska ek fix monthly charge hota hai (lagbhag $1 per key/month), chahe aap ise use karein ya na karein.
- Rotation: Isme aapke paas option hota hai. Aap automatic rotation on kar sakte hain, jo har 1 saal (365 din) mein key badal dega, ya fir aap manually bhi jab chahein badal sakte hain.
- Kiske liye sahi hai?: Banking, Healthcare, ya bade enterprises ke liye jahan audit hota hai aur unhe har ek action par poora control chahiye hota hai.

<br>

**SSE-KMS kaise kaam karta hai?**

Maan lijiye aap S3 bucket mein ek photo (data.jpg) upload kar rahe hain aur aapne SSE-KMS (Customer Managed Key) chuni hai.

Phase A: Uploading & Encryption:
- Aapka Request: Aapne S3 ko bola, "Meri yeh photo upload karo aur meri yeh KMS Key use karo."
- S3+KMS Coordination: S3 seedha KMS ke paas jata hai aur bolta hai, "Mujhe is user ki KMS Key ke liye ek Data Key do."
- KMS Generates Keys: KMS do cheezein banakar S3 ko deta hai:
  - Plaintext Data Key: File encrypt karne ke liye.
  - Encrypted Data Key: Wahi chabi jo KMS Root Key se encrypt ho chuki hai.
- S3 Encryption: S3 us Plaintext Data Key ko uthata hai, aapki photo (data.jpg) ko encrypt karta hai, aur encrypt karte hi us Plaintext Chabi ko memory se turant delete kar deta hai.
- Storage: S3 ab aapki Encrypt hui photo aur uske sath Encrypted Data Key ko bucket mein ek sath store kar deta hai.

Phase B: Downloading & Decryption:
- Aapka Request: Aapne click kiya "Download salary_structure.xlsx".
- S3 Checks Permissions: S3 aapki file ke sath stored Encrypted Data Key (band lifafa) ko uthata hai aur use KMS ke paas bhejta hai: "Is lifafe ko apni Master Key se kholo."
- KMS Decryption: KMS pehle check karta hai ki kya aapke paas is key ko use karne ki permission hai? Agar Haan, toh KMS us lifafe ko kholkar Plaintext Data Key wapas S3 ko de deta hai.
- S3 Decrypts Data: S3 us Plaintext Data Key se aapki photo ko decrypt (un-hide) karta hai, aapko original photo download karwata hai, aur plaintext chabi ko phir se memory se delete kar deta hai.

**Audit Trail (Kaun Dekh Raha Hai Yeh Sab?)**:

Is poore process mein agar aapne AWS Managed Key use ki hoti, toh sab kuch chupchaap ho jata. Lekin kyunki aapne Customer Managed Key use ki hai, AWS background mein ek teesra department active rakhta hai jise AWS CloudTrail kehte hain.

Jab bhi Phase A ya Phase B chalega, CloudTrail mein ek create hoga:
```
"Tareekh 30 August 2026 ko, Shaam 07:50 baje, User_Rahul ne S3 ke jariye Master Key 'MyCompany-S3-Key' ko hit kiya taaki file statement.pdf ko decrypt kiya ja sake. Status: SUCCESS."
```

<br>

**SSE-KMS Ke Features**:

- Granular Access Control: Aap S3 aur Encryption Key dono par alag-alag permissions laga sakte hain. Yeh double-lock security ki tarah kaam karta hai.
- Comprehensive Auditing via CloudTrail: S3 mein data read/write karne ki har ek request jo KMS ke paas jaati hai, woh AWS CloudTrail mein record hoti hai. Aap exact track rakh sakte hain ki kis IAM User ya kis Role ne, kis samay, kis key ka use karke, kaun sa object decrypt kiya.
- Manual and Automatic Key Rotation: Aap KMS ke andar set kar sakte hain ki aapki master key har saal (ya custom time par) automatically rotate ho jaye. Purana data purani key ke naye version se link rehta hai, aur naya data naye version se encrypt hota hai. Aap chahein toh manually purani key ko disable karke turant nayi key bana sakte hain.
- Cryptographic Shredding: Maan lijiye aapka bank kisi desh se apna business band kar raha hai aur aapko compliance ke tehat unka saara data instant delete karna hai. Billion of files ko ek-ek karke delete karne mein ghante lag sakte hain. SSE-KMS mein aap bas us region ki KMS Key ko delete ya disable kar dijiye. Jaise hi key khatam, disk par rakha saara data instant useless junk ban jayega, kyunki use decrypt karne ka koi tarika poori duniya mein nahi bacha. Ise Cryptographic Shredding kehte hain.

<br>

**SSE-KMS Ke Nuqsaan ya Trade-offs**:

Itni tight security ke sath kuch baatein dhyan mein rakhni hoti hain:
- KMS Request Limits (Throttling Risk): KMS ki ek default limit hoti hai ki woh ek second mein kitni API requests (Encrypt/Decrypt) handle kar sakta hai (misal ke taur par 10,000 requests per second region ke hisab se). Agar aapka koi bohot bada Data Lake hai jismein ek sath lakhon requests aa rahi hain, toh KMS Throttling Error (HTTP 400 - SlowDown) de sakta hai.
  - Solution: Iske liye AWS ne S3 Bucket Keys ka feature nikala hai, jo KMS par aane wale load ko 99% tak kam kar deta hai ek hi data key ko thodi der tak reuse karke.

- Cost Factor (Kharcha): SSE-KMS free nahi hai.
  - Har Customer Managed Key (CMK) ka $1 per month fix charge hota hai.
  - Har 10,000 KMS API requests par approx $0.03 ka charge lagta hai. Agar aapka application din mein crore-o baar S3 se files read/write karta hai, toh aapka KMS ka bill bohot bada ho sakta hai.
 
<br>
<br>

**3 - SSE-C (Server-Side Encryption with Customer-Provided Keys)**:

SSE-C ka full form hai Server-Side Encryption with Customer-Provided Keys.

SSE-C ka matlab hai.
- Customer encryption key khud provide karta hai.

AWS object ko encrypt karta hai. Lekin encryption key permanently store nahi karta.

SSE-C ka simple matlab hai: Data ko encrypt/decrypt karne ka poora kaam AWS S3 ka server karega, lekin encryption Key 100% aapki hogi. AWS aapki chabi ko kabhi bhi apne paas save nahi karega. Aap AWS ko encryption aur decryption ke liye key provide karoge jisse AWS data ko encrypt karke store karega aur aapne dene se pehle data ko decrypt karega.

Maan lijiye aap ek aisi highly confidential organization hain jo AWS par trust toh karti hai apna data store karne ke liye, par apni encryption keys par AWS ko bilkul hath nahi lagane dena chahti. Aise scenario ke liye SSE-C banaya gaya hai.

<br>

**SSE-C Ka Core Philosophy Kya Hai?**

SSE-S3 aur SSE-KMS mein AWS ke paas aapki encryption keys kisi na kisi roop mein store rehti thi. Lekin SSE-C mein philosophy badal jaati hai:
- Storage AWS Ka, Key Aapki: Amazon S3 aapke data ko encrypt aur decrypt toh karega (isliye yeh Server-Side encryption hai), lekin encryption ke liye jo key use hogi, woh AWS ke paas kabhi bhi store nahi hogi.
- Zero Trust Model: AWS ke bade se bade admin ya software engineer ke paas bhi aisa koi tarika nahi hai jisse woh aapki file dekh sakein, kyunki unke pure data center ya system mein aapki encryption key kahi save hi nahi hoti.
- Aapki Zimmedari (High Risk): Agar aap apni encryption key kho dete hain, toh AWS ke paas bhi koi master-key ya back-door nahi hai jisse aapka data wapas mil sake. Data hamesha ke liye permanently lost ho jayega.

<br>

**Architecture aur Workflow**:

SSE-C poori tarah se HTTPS Headers par chalta hai. Jab bhi aap S3 ke sath koi transaction (Upload ya Download) karte hain, toh aapko request ke sath apni encryption key ko cryptographic headers mein bhejni padti hai.

```
[ Aapka Safe Application Server ] -- (1) File + Raw Encryption Key via HTTPS --> [ Amazon S3 Memory ]
      (Key stored locally)                                                                 |
                                                                              (2) Encrypts data in RAM
                                                                                           |
                                                                                           v
[ S3 Hard Disk ] <-------- (3) Stores Encrypted File + MD5 Hash of Key <-------------------+
                             (Deletes raw key from RAM instantly)
```

 Data Upload (PutObject Request):
 - Jab aap apne application se S3 mein koi file upload karte hain, toh aapko HTTP/HTTPS request ke headers mein teen zaroori parameters bhejne hote hain:
   - ```x-amz-server-side-encryption-customer-algorithm```: Isme aap batate hain ki aap kaun sa algorithm use kar rahe hain (S3 sirf AES256 standard ko support karta hai).
   - ```x-amz-server-side-encryption-customer-key```: Isme aap apni 256-bit, base64-encoded encryption key bhejte hain.
   - ```x-amz-server-side-encryption-customer-key-MD5```: Yeh us key ka MD5 hash hota hai, jisse S3 verify karta hai ki key raste mein corrupt toh nahi hui.
- S3 Ka Action:
  - Amazon S3 ko jab yeh request milti hai, toh woh aapki raw key ko temporary apni volatile memory (RAM) mein dalta hai. Fir aapke data ko encrypt (readable format se unreadable format mein convert) karta hai, S3 encrypted file ko hard disk par save kar deta hai. Uske sath metadata mein sirf key ka MD5 hash save karta hai (asli key nahi). Iske turant baad, S3 apni RAM se aapki asli key ko permanently wipe out (clear) kar deta hai.
 
Download Process (GET Request):
- Kyunki S3 ke paas data ko wapas normal karne (decrypt karne) ka koi zariya nahi hai, isliye jab aapko woh file download karni hogi, Aapko dobara wahi exact teen headers (Algorithm, Key, aur Key-MD5) download request ke sath bhejne honge.
- The Verification: S3 aapki bheji hui key ka MD5 hash nikalta hai aur file ke sath save kiye gaye MD5 hash se match karta hai.
- Decryption: Agar hash match ho jata hai, toh S3 aapki key ko RAM mein lekar file decrypt karta hai aur aapko plaintext file de deta hai. Phir se, key RAM se mita di jaati hai.
- The Failure Mode: Agar aapne download request mein key nahi bheji, ya galat key bheeji, toh S3 direct HTTP 403 Forbidden ya InvalidRequest error throw kar dega. S3 khud bhi us file ko nahi padh sakta.

<br>

**SSE-C Ke Fayde**:

- Keys 100% aapke control mein hain. AWS ke infrastructure par aapka data aane ke baad bhi key ka malik kaun hai, iska koi sawal hi nahi rehta.
- SSE-KMS ke muqable ismein koi AWS KMS ka kharcha nahi hota. Aap chahe crore-o requests marein, AWS aap se encryption ka ₹1 bhi extra nahi lega, kyunki infrastructure key management ka aap apna use kar rahe hain.
- Yeh option un sabhi companies ke liye lifesaver hai jinhe strict data sovereignty compliance laws (jaise sovereign cloud acts ya strict national defense protocols) follow karne hote hain.

<br>

**SSE-C Ka Sabse Bada Risk Factor (The Single Point of Failure)**:

SSE-C ka use karte waqt poori responsibilty aur accountability customer ki hoti hai.
- No Backup Management: AWS aapki key ko save nahi karta, isliye agar aapki application ke database se ya aapke system se woh key delete ho gayi, toh AWS Support Team bhi aapka data recover nahi kar sakti. Woh data hamesha ke liye ek useless junk file ban kar S3 mein pada rahega.
- Key Rotation: Agar compliance ke mutabik aapko har 90 din mein encryption keys badalni (rotate karni) hain, toh aapko khud purani key se data download karke, nayi key se use dobara upload karna padega. AWS isme koi automatic rotation support nahi deta.
- HTTPS (Encryption in Transit) is Mandatory: Kyunki aap har request ke sath apni asli chabi S3 ko bhej rahe hain, isliye HTTPS (SSL/TLS) ka use compulsory hai. Agar aap HTTP par request bhejenge, toh S3 request ko reject kar dega taaki raste mein koi chabi chura na sake.

<br>

**Yeh Option Kab Use Kiya Jata Hai? (Real-World Use Cases)**:

Aap sochenge ki itna risk aur jhanjhat kyun uthana, jab SSE-S3 aur SSE-KMS jaise automated options available hain? Iske peeche kuch strict corporate aur legal reasons hote hain:
- Strict Compliance Regulations: Kuch sovereign countries ya defense/finance organizations ke strict local laws hote hain ki unka encryption material (keys) kisi bhi third-party cloud provider ke infrastructure ke andar enter ya save nahi hona chahiye.
- On-Premises Key Infrastructure: Agar kisi badi enterprise company ne pehle se hi apna khud ka crores ka HSM (Hardware Security Module) ya Key Management appliance apne personal data center mein lagaya hua hai, toh woh usi existing setup ka use karke S3 ka data secure karna chahte hain.

<br>
<br>

### 2. Client-Side Encryption

Client-Side Encryption ka simple matlab hai: Data ko AWS S3 ke paas bhejne se PEHLE hi encrypt kar dena.

Isme Amazon S3 ke servers ka encryption process mein koi role nahi hota. S3 ko sirf ek encrypted file (Ciphertext) milti hai, aur woh use waise hi store kar leta hai. S3 ko yeh tak nahi pata hota ki file ke andar kya data hai. Encryption aur Decryption ka saara heavy computer processing aapke apne server ya application par hota hai.

- Client must encrypt the objects locally before sending it to S3.
- Client must also decrypt the objects locally after retrieving it from S3.

<br>

**Client-Side Encryption Kaise Kaam Karta Hai?**

Iska workflow do tariko se manage kiya jata hai, is baat par nirbhar (depend) karte hue ki aap apni cryptographic keys ko kahan rakhna chahte hain. Matlab user apni keys kaha rakhna chta hai.

**Option A: Using AWS KMS (Key Management Service)**:

Is tarike mein, keys AWS KMS ke paas rehti hain, lekin encryption aapke server par hota hai.
- Requesting Key: Aapka application AWS KMS ko bolta hai ki mujhe ek temporary ```Data Key``` do.
- KMS Response: KMS aapko do cheezein bhejta hai—ek ```Plain-text Data Key``` aur ek ```Encrypted Data Key```.
- Local Encryption: Aapka application us Plain-text Data Key ka use karke aapki file ko encrypt kar deta hai. Iske turant baad aapka application us plain chabi ko memory se delete kar deta hai.
- S3 Upload: Ab aapka application encrypted file aur us Encrypted Data Key ko ek sath S3 bucket mein upload kar deta hai.

**Option B: Using a Master Key in Your Own Application (Customer-Managed)**:

Is tarike mein AWS ka koi lena-dena nahi hota. Aapka poora control hota hai.
- Local Key Generation: Aapke application ke paas pehle se ek Master Key hoti hai (jaise aapke apne data center mein stored).
- Local Encryption: Aapka code khud ek temporary data key generate karta hai, file ko encrypt karta hai, aur us temporary key ko apni Master Key se encrypt kar deta hai.
- S3 Upload: Encrypted data ko S3 mein bhej diya jata hai. AWS ko sirf ek unreadable file dikhti hai.

**Data Wapas Kaise Milta Hai? (Decryption Workflow)**:

Jab aapko S3 se file wapas chahiye hoti hai:
- Aapka application S3 se us encrypted file ko download karta hai.
- Kyunki file encrypted hai, aapka application use direct open nahi kar sakta.
- Aapka application local Master Key ya AWS KMS ka use karke us file ke sath aayi temporary key ko unlock (decrypt) karta hai.
- Us unlocked temporary key se aapki main file aapke server par wapas normal (readable) ho jaati hai.

<br>

**Client-Side Encryption Ke Fayde**:

- Ultimate Security (Zero-Trust): Agar koi hacker S3 ka poora control bhi haasil kar le, tab bhi woh aapka data nahi padh sakta, kyunki data ko unlock karne ka code aur key aapke server par hai.
- End-to-End Cryptographic Security: Data internet par (in transit) aur hard disk par (at rest) dono hi jagah continuously secure rehta hai. Man-in-the-middle attacks ka koi khatra nahi hota, chahe network kitna bhi insecure ho.
- Compliance: Yeh un industries (jaise military, high-finance, ya healthcare) ke liye perfect hai jahan strict rule hota hai ki cloud provider par bilkul bharosa nahi kiya ja sakta.


**Client-Side Encryption Ke Nuksan**:
- Management Overhead: Cryptographic keys ko surakshit rakhna aur manage karna poori tarah aapki zimmedari hai.
- Heavy Computational Burden on Client: Encryption aur decryption ek mathematically heavy process hai. Agar aapka client application har ek second mein hazaron files upload/download kar raha hai, toh aapke local servers ka CPU usage 90% tak ja sakta hai, kyunki encryption ka saara load ab AWS par nahi, aapke servers par hai.
- No AWS Features: Kyunki S3 ke paas sirf encrypted data hota hai, isliye aap S3 ke in-built features jaise S3 Select (file ke andar search karna) ya Object Lifecycle Management (data contents ke hisab se rules lagana) ka use nahi kar sakte.
- Code Complexity: Developers ko normal AWS API calls (s3.putObject) ki jagah specialized cryptographic clients initialize karne padte hain, jisse debugging aur code maintenance mushkil ho jaati hai.
