# S3 Security - (Access and Data Security)

AWS S3 (Simple Storage Service) mein data rakhna jitna asaan hai, use secure rakhna usse bhi zyada zaroori hai. AWS mein ek bade level par kaha jata hai ki "Security is Job Zero" (yani sabse pehla kaam). S3 ke andar aapka data leak na ho aur koi anjaan insaan use touch na kar sake, iske liye AWS bohot saare security layers deta hai.

S3 Security ko hum 4 bade hisson mein samajh sakte hain:

### 1. Access Management (Data Kaun Dekh Sakta Hai?):

S3 mein default setting yeh hoti hai ki "**Sab kuch private hai**". Jab tak aap khud permission nahi denge, koi aapka data nahi dekh sakta. Permission dene ke 3 mukhya (main) tareeqe hain:
- **IAM Policies (User Level Security)**: Agar aapki company mein 10 employees hain, toh aap IAM (Identity and Access Management) policy se set kar sakte hain ki Puneet file upload kar sakta hai, par Rahul sirf file dekh sakta hai, delete nahi kar sakta.
- **Bucket Policies (Bucket Level Security)**: Yeh ek JSON script hoti hai jo seedhe bucket par lagayi jaati hai. Isse aap poore rule set kar sakte hain, jaise: "Sirf meri company ke IP Address se hi is bucket ka data open hona chahiye, baaki duniya ke liye block ho."
- **Access Control Lists (ACLs)**: Yeh purana tareeqa hai jahan har ek single file (object) par alag se permission di jaati thi. AWS ab ise use karne se mana karta hai aur Bucket Policies ko hi prefer karta hai.

<br>
<br>

### 2. Public Access Block:

Pehle zamane mein bohot saari companies ki buckets galti se public ho jaati thi aur data leak ho jata tha. Isko rokne ke liye AWS ne ek master switch banaya hai jise BPA (Block Public Access) kehte hain.
- Jab aap nayi bucket banate hain, toh AWS automatic "**Block all public access**" ko **ON** rakhta hai.
- Jab tak yeh switch ON hai, aap galti se bhi kisi hacker ya public ko data access nahi de sakte. Yeh ek tarah ka safety guard rail hai.

<br>
<br>

### 3. Encryption (Data Chupana):

Maan lijiye kisi ne physical datacenter mein jaakar hard drive hi chura li (jo ki lagbhag namumkin hai), ya fir data kaise bhi leak ho gya, toh bhi hacker aapka data nahi padh payega kyunki data Encrypted hota hai (readable format mein nahi hota). S3 mein do tarah ki encryption hoti hai:
- **Encryption in Transit (Raste mein)**: Jab data aapke computer se AWS datacenter ja raha hota hai, toh use HTTPS (SSL/TLS) ke zariye secure kiya jata hai taaki raste mein koi use intercept na kar sake.
- **Encryption at Rest (Hard drive par)**: Jab data S3 ke servers par store ho jata hai. AWS ab har ek bucket par automatic encryption lagata hai (SSE-S3). Iska matlab data server par encrypt hone ka baad store hota hai. Isme aap AWS ki keys use kar sakte hain ya fir apni khud ki keys (AWS KMS - Key Management Service) ka use karke zyada control rakh sakte hain.

<br>
<br>

### 4. Data Protection & Auditing:

- **S3 Versioning**: Agar aapne galti se kisi file ko delete kar diya ya overwrite kar diya, toh versioning uske purane versions ko sambhal kar rakhti hai. Aap pichle version par wapas ja sakte hain.

- **MFA Delete (Multi-Factor Authentication)**: Agar koi aapka password chura bhi le, toh bhi wo bucket ya file delete nahi kar payega jab tak wo aapke phone ka OTP (MFA code) nahi dalega. Kyuki isme multiple layer of authentication lagaya jata ha deletion ke liye.

- **S3 Object Lock**: Yeh regulatory ya legal data ke liye hota hai (WORM - Write Once, Read Many). Ek baar agar aapne isme file lock kar di, toh company ka CEO ya AWS ka owner bhi us file ko ek fixed time tak delete nahi kar sakta.

- **S3 Server Access Logs**: Yeh ek diary ki tarah hai. Is bucket mein kab, kisne, kis IP address se, kaun si file download ki ya delete ki—is sabka poora record is log file mein maintain hota hai. Auditing ke liye yeh bohot kaam aata hai.

<br>
<br>

### Summary Checklist (Ek Secure Bucket Ke Liye)

- **Block Public Access** hamesha **ON** rakhein (jab tak website host na kar rahe hon).
- **Encryption at Rest** ko hamesha enable rakhein.
- **Versioning** ko **ON** rakhein taaki galti se delete hone par data wapas mil sake.
- **IAM/Bucket Policies** mein hamesha ```Principle of Least Privilege``` (sirf utni hi permission dein jitni zaroorat ho) ka rule follow karein.
