# S3 Pre-Signed URL

AWS S3 Presigned URL S3 ka ek bohot secure feature hai jo aapko apne Private S3 Objects (files) ka access kisi doosre user ko ek limited time ke liye dene ki permission deta hai, wo bhi bina unhe aapke AWS account ka access diye ya bina file ko public kiye.

S3 Pre-signed URL ek aisa temporary link (URL) hota hai jise aapka AWS account generate karta hai, taaki aap kisi bhi normal user ya application ko apni private S3 file ka safe access de sakein, woh bhi bina bucket ko public kiye aur bina kisi AWS login ke.

Matlab agar koi user jiske paas koi AWS account nhi hai aur vo tumhari S3 bucket ke object access karna chta hai ya bucket mein koi file upload karna chta hai, to tum ek pre-signed url create karke user ko dete, jisse vo user kuch time ke liye bucket ko access kar sakta hai.

S3 buckets aur unke andar ki files by default 100% private hoti hain. Agar aap chahte hain ki koi aam user (jiske paas AWS account nahi hai) aapke bucket se koi file download kare ya usmein koi file upload kare, to aap ek Presigned URL generate karke use de sakte hain. Wo URL ek fix time baad (jaise 15 minutes ya 1 ghante baad) apne aap expire (invalid) ho jata hai.

<br>
<br>

### Yeh Kaam Kaise Karta Hai? 

Maan lijiye aapki ek photo ```my-private-photo.jpg``` S3 bucket mein hai, jo ki poori tarah se private hai (internet par koi use nahi dekh sakta).
- **User Request Karta Hai**: Ek normal user aapki website ya app par aata hai aur "Download File" button par click karta hai.
- **Aapka Backend Code URL Banata Hai**: Aapka server (jo Node.js, Python, ya PHP mein ho sakta hai) AWS SDK ka use karke S3 se ek URL generate karne ko bolta hai. Jab aapka backend code (Python, Node.js, etc.) S3 se kehta hai ki ek Presigned URL banao, to AWS SDK aapke khud ke IAM User/Role ke security credentials (permissions) ka use karke ek unique cryptographically signed URL generate karta hai.
- **Security/Expiry Set Hoti Hai**: Code generate karte waqt aap tay karte hain ki yeh link kitni der tak chalega (jaise 15 minutes, 1 ghanta, ya 2 din). Maximum limit iski AWS IAM credentials ke hisab se up to 7 din tak ho sakti hai.
- **S3 Cryptographic Token Jodta Hai**: S3 ek lamba sa URL bana kar deta hai jismein aapke AWS account ka ek temporary signature (token) juda hota hai.
  - Uss URL ke andar hi saari details chhupi hoti hain:
    - Kaun si file access karni hai.
    - Kya action perform karna hai (GET download karne ke liye ya PUT upload karne ke liye).
    - Expiration Time: Yeh URL kab tak valid rahega (e.g., agle 10 minutes tak).
- **User Ke Paas Link Jata Hai**: Aapka backend ye URL user ke browser ko de deta hai. User ka browser direct S3 ke us URL par hit karta hai aur file download ya upload ho jaati hai. Jaise hi set kiya gaya time (expiry) khatam hoga, link "Access Denied" dikhane lagega.

<br>
<br>

### Presigned URL Ke Do Main Modes

Aap do tarah ke kaam ke liye Presigned URL bana sakte hain:

**1. Presigned GET (File Download Karne Ke Liye)**:

Jab aapko koi private file kisi ko dikhani ya download karwani ho.

Real-world Example: Udemy ya Netflix par jab aap koi video chalaate hain, to unka backend background mein ek 2-ghante ka Presigned GET URL generate karta hai aur video player ko de deta hai. Video khatam hote hi wo URL bekar ho jata hai, taaki koi us link ko copy-paste karke dosto ke sath share na kar sake.

<br>

**2. Presigned PUT (File Upload Karne Ke Liye)**:

Jab aap chahte hain ki user aapke bucket mein file upload kare, lekin aap data ko pehle apne web server par lekar memory waste nahi karna chahte.

Real-world Example: Kisi job portal par jab aap 'Upload Resume' par click karte hain, to backend aapko ek Presigned PUT URL deta hai. Aapka browser direct us resume file ko aapke laptop se uthakar direct Amazon S3 bucket mein bhej deta hai. Isse aapke main server par koi load nahi aata.

<br>
<br>

### Expiration Limits: Yeh Kab Tak Valid Rehta Hai?

Presigned URL ki validity is baat par depend karti hai ki use kis tarah ke credentials se banaya gaya hai:
- **IAM Instance Profile / Assumed Role (AWS Best Practice)**: Agar aapka code EC2 instance, AWS Lambda, ya ECS par chal raha hai aur wo IAM Role use kar raha hai, to Presigned URL maximum 12 ghante (Up to 12 hours) tak hi valid reh sakta hai.
- **Permanent IAM User (Long-term Keys)**: Agar aapne manually AWS_ACCESS_KEY_ID aur SECRET_ACCESS_KEY hardcode kiye hain, to aap maximum 7 din (7 days) tak ka valid URL bana sakte hain. (Lekin security reasons ki wajah se ise avoid kiya jata hai).

<br>
<br>

### Python (Boto3) Se Presigned URL Kaise Banate Hain?

Developers sabse zyada isi tarike se programming language mein iska use karte hain. Ek simple python example dekhiye:

```
import boto3
from botocore.exceptions import ClientError

s3_client = boto3.client('s3')

try:
    # 1 Hour (3600 seconds) ke liye private file ka download link banana
    response = s3_client.generate_presigned_url(
        'get_object',
        Params={'Bucket': 'my-secure-vault', 'Key': 'confidential_report.pdf'},
        ExpiresIn=3600 
    )
    print("Aapka Presigned URL yeh hai:\n", response)
except ClientError as e:
    print("Error generating URL:", e)
```

<br>
<br>

### Presigned URL Ke Fayde (Benefits)

**No AWS Account Needed for Users**: Aapka end-user ek normal mobile app user ya browser user ho sakta hai, unhe AWS ke baare mein kuch pata hone ki zaroorat nahi hai.

**Server Performance Saver**: Serverless aur decoupled architectures mein ye vardaan hai. Saara heavy network upload/download browser aur S3 ke beech hota hai, aapka main web server bilkul free rehta hai.

**Granular Access Control**: Aap exact ek single file ke liye access dete hain, pure bucket ke liye nahi.

<br>
<br>

### S3 Pre-Signed URL pricing

S3 Pre-signed URL ko generate karne ki koi alag se fee ya extra charge nahi hota hai. Yeh AWS ka ek free feature hai. Lekin, jab koi user us Pre-signed URL par click karke file ko download karta hai ya us link ke zariye file upload karta hai, tab S3 ke normal charges lagte hain. Yeh saara kharcha Bucket Owner (yaani aapko) hi dena hota hai.

**Agar User Pre-signed URL se File Download (GET) karta hai**:

Jab user link par click karke file download karega, to aapko ye do charges lagenge:
- **Data Transfer Out (Bandwidth)**: S3 se data nikal kar user ke computer par ja raha hai.
  - Cost: Har mahine pehla 100 GB bilkul free hota hai. Uske baad lagbhag $0.09 per GB (approx ₹7.50/GB) lagta hai.

- **GET Request Charge**: File ko read karne ki request:
  - Cost: Yeh bohot sasta hota hai, lagbhag $0.0004 per 1,000 requests (yaani ₹0.033 per 1,000 downloads).
 
<br>

**Agar User Pre-signed URL se File Upload (PUT) karta hai**:

Agar aapne user ko file upload karne ke liye link diya hai, to aapko ye charges lagenge:
- **Data Transfer In (Upload)**: Yeh hamesha 100% FREE hota hai. User chahein 10 GB ki file upload kare, upload ka koi bandwidth charge nahi lagega.
- **PUT Request Charge**: Nayi file bucket mein daalne ki request:
  - Cost: Lagbhag $0.005 per 1,000 requests (yaani approx ₹0.42 per 1,000 uploads).
 
- **Storage Cost**: Jo file upload hui hai, woh ab aapki bucket mein space legi, isliye uska monthly storage charge lagega (Standard S3 mein lagbhag ₹2.10 per GB/month).

<br>
<br>

### Pre-signed URL Use Karte Waqt Billing Risks (Zaroori Baat)

Pre-signed URL banate waqt aapko ek baat ka dhyan rakhna chahiye: Galti se bada bill banna.

Maan lijiye aapne ek 1 GB ki video ka Pre-signed URL banaya aur uski expiry 2 din (48 hours) rakh di. Agar us user ne woh link kisi public group ya social media par share kar diya, aur 1,000 logo ne us link se video download kar li:
- Data Transfer: 1 GB × 1,000 downloads = 1,000 GB (1 TB) data transfer ho jayega.
- Estimated Bill: 100 GB free nikal kar, bache hue 900 GB ka kharcha lagbhag $81 (approx ₹6,800+) aapke account mein aa jayega.

**Bill Se Bachne Ke Best Practices**:
- **Short Expiry Time Raxhein**: URL ki expiry jitni kam ho sake utni rakhein (jaise 5 se 15 minutes). Isse user ke paas link share karne ka time nahi bachaega.
- **CloudFront (CDN) Ka Use Karein**: Agar aapko bohot saare logo ko files download karwani hain, to S3 ke aage Amazon CloudFront lagayein. CloudFront par Pre-signed URL lagane se data transfer cost bohot kam ho jaati hai aur har mahine 1 TB (1,000 GB) tak ka data transfer free milta hai.

<br>
<br>

### Pre-signed URL kaise generate karte hain

File ko access ya download karne ka pre-signed url aws console se generate kar sakte hain, lekin agar file upload karne ke pre-signed url generate karna hai to usko AWS SDK (Software Development Kit) use karke generate karna hoga.

AWS SDK se dono download aur upload dono ke pre-signed url generate ho jate hain. Lekin console se sirf download ka hi generate hota hai.

<br>

**Console se download karne ka pre-signed url generate karna**:

Agar aap chahte hain ki aap kisi specific file ka download link ek custom expiration time (jaise 5 minutes, 1 hour, ya max 12 hours) ke sath banayein, to in steps ko follow karein:

- AWS Management Console mein log in karein aur Amazon S3 open karein.
- Apne Buckets ki list mein se us bucket par click karein jismein aapki private file save hai.
- File ke path (folders) ke andar navigate karein aur us file (object) ke naam ke aage bane Checkbox par click karke use select kar lein.
- Upper right corner mein aapko Actions ka ek dropdown menu dikhega, us par click karein.
- Dropdown list mein se "Share with a presigned URL" option ko select karein.
- Ek naya box open hoga. Wahan aapko Time interval choose karna hoga (e.g., Minutes ya Hours select karke number daalna hoga ki link kitni der mein expire ho jaye).
- Settings select karne ke baad "Create presigned URL" button par click kar dein.
- AWS screen par ek success message dikhayega aur aapko ek link milega. "Copy presigned URL" par click karke aap use kisi ke bhi sath share kar sakte hain.

