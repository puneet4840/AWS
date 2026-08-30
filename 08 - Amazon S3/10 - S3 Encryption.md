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

**Server-side Encryption 3 type se hota hai**:

Humko pta hai data ko encrypt karne ke liye ek Key ki zaroori hoti hai. Ab AWS humkp 3 options deta hai ki aapko server-side encryption ke liye key kaise manage karni hai:
- SSE-S3 (Server-Side Encryption with Amazon S3-Managed Keys).
- SSE-KMS (Server-Side Encryption using AWS manaed service KMS).
- SSE-C (Server-Side Encryption with Customer-Provided Keys).

<br>

**1 - SSE-C (Server-Side Encryption with Customer-Provided Keys)**:

SSE-S3 ka matlab hai ki encryption ki puri zimmedari Amazon S3 ki hai. Isme encryption keys ko poori tarah se AWS S3 khud manage aur rotate karta hai.
