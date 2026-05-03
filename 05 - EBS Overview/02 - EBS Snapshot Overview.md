# EBS Snapshot Overview

<br>

### EBS Snapshot kya hota hai?

EBS Snapshot ek point-in-time backup hota hai jo tumhare EBS volume ke data ka logical copy preserve karta hai.

Simple words mein, ye tumhari cloud disk ka “backup image” hota hai jise future mein restore, clone, migrate, ya recover kiya ja sakta hai.
Agar EBS volume tumhari hard disk hai, to snapshot us hard disk ka saved recovery state hai.

Ye especially important hai kyunki production systems mein data accidental deletion, corruption, ransomware, failed deployment, ya infrastructure issues ki wajah se lose ho sakta hai.

- You can delete snapshots and retain it in Recycle Bin for a desired retention period.
- Amazon Data Lifecycle Manager (DLM) automates EBS snapshot creation and retention.

<br>
<br>

### Snapshot “Point-in-Time” Kyu Bola Jata Hai?

Point-in-time ka matlab hota hai ki snapshot exactly us waqt ka state preserve karta hai jab snapshot initiate hua. Agar us moment par disk par OS files, apps, database, logs, aur configs jo bhi available hain, snapshot unka captured state maintain karta hai.

Ye disaster recovery ke liye useful hai kyunki tum kisi specific known-good state par rollback kar sakte ho. Example ke liye, deployment ke pehle snapshot liya aur deployment fail ho gaya, to old stable state restore ho sakti hai.

<br>
<br>

### Snapshot Data Kahan Store Hota Hai?

Sanpshot S3 bucket mein store hote hain.

AWS snapshots ko direct same EBS disk par store nahi karta. Snapshots backend mein Amazon S3 architecture par securely maintain hote hain. Iska reason durability aur scalability hai, kyunki S3 highly durable storage architecture hai.

Agar same disk ya same server par backup hota to infrastructure failure ka risk high hota.

<br>
<br>

### Snapshot Full Copy Ya Incremental?

Snapshots are incremental – One full snapshot + changed data only.

**First Snapshot**:
- Jab tum pehli baar volume ka snapshot lete ho, AWS ko complete baseline state save karni padti hai. Matlab EBS ka complete data save karna hota hai.
- Isliye first snapshot relatively larger aur slower ho sakta hai.

**Next Snapshots**:
- AWS sirf changed blocks save karta hai.
- Isse incremental snapshot model kehte hain.
- Matlab jo data EBS mein add ho rha hai snapshot ke baad usko save karta hai.

<br>

**Incremental Model Kyu Important Hai?**:

Agar har snapshot full hota, to storage cost aur backup time dramatically increase hota. Incremental architecture unnecessary duplication avoid karta hai. Agar 500 GB disk mein sirf 5 GB change hua hai, to AWS ideally sirf changed blocks track karta hai.
Isse cost optimization hota hai.

Example:
- Day 1: 100 GB baseline
- Day 2: 2 GB change
- Day 3: 1 GB change

Storage logic:
- 100 + 2 + 1
Not:
- 100 + 100 + 100

<br>
<br>

### Snapshot Use Cases

**1. Backup**:
- Sabse obvious use case hai.
- Daily, weekly, ya hourly backup policy maintain ki ja sakti hai.

**2. Disaster Recovery**:
- Agar primary volume fail ho gaya to snapshot se naya volume create karke service restore ho sakti hai.
- Business continuity ke liye essential hai.

**3. Cloning**:
- Same server ya disk configuration ko quickly duplicate kar sakte ho.
- Dev/Test environment ke liye useful.

**4. Migration**:
- Snapshot copy karke another AZ ya region mein restore possible hai.
- Cross-region DR aur expansion ke liye important.

<br>
<br>

### Snapshot Restore Kaise Kaam Karta Hai?

Snapshot directly running disk nahi hota. Snapshot se tum new EBS volume create karte ho. Ye naya volume old state ka recoverable version hota hai.

Phir ise EC2 par attach karke use kar sakte ho. Yani snapshot = source, restored volume = usable disk.

<br>

### Cost Structure

Snapshots “free backup” nahi hote. Billing changed stored blocks ke basis par hoti hai. Lekin incremental model cost reduce karta hai.

Old unnecessary snapshots cost waste kar sakte hain. Lifecycle management isliye important hai.

<br>
<br>
<br>

## Snapshot Encryption

<br>

### Snapshot Encryption kya hota hai?

Snapshot encryption ka matlab hai ki tumhare EBS snapshot ke andar jo bhi data stored hai, wo cryptographic protection ke through unreadable form mein secure rahe jab tak authorized access aur correct decryption key available na ho.

Matlab snapshot ke data ko encrypt karke rakhna.

Ye isliye important hai kyunki backup data often production data ka full ya partial historical copy hota hai, aur agar backup secure nahi hua to attacker ko sensitive data ka alternate path mil sakta hai.

Security sirf live server par nahi, backup chain par bhi equally apply honi chahiye. Isliye snapshot encryption cloud security architecture ka fundamental part hai.

<br>

### Encryption Ka Main Goal

Encryption ka primary purpose confidentiality preserve karna hai. Confidentiality ka matlab unauthorized parties data ko readable form mein access na kar saken.

Agar snapshot accidentally share ho jaye, unauthorized account access ho, ya compliance audit ho, encryption additional protection layer provide karta hai.

<br>

### AWS Snapshot Encryption Backend Kaise Kaam Karta Hai?

AWS EBS snapshots encryption ke liye AWS Key Management Service (KMS) use karta hai. KMS centralized cryptographic key management platform hai jo encryption keys create, store, control, aur audit karne deta hai.

Yani encryption sirf random password se nahi hoti; enterprise-grade key governance ke saath hoti hai.

Keys ke through define hota hai kaun decrypt kar sakta hai. Isse security + compliance + auditability improve hoti hai.

