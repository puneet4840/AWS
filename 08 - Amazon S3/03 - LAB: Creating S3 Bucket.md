# Creating S3 Bucket and uploading object into it

Is slide mein hum ek S3 bucket create karenge, aur usme ek object upload karenge.

<br>

S3 bucket ek global service hai.

<br>
<br>

### Prerequisites (Zaroori Cheezein)
- Ek active **AWS** Account.
- Upload karne ke liye aapke computer par ek dummy file (jaise ```sample.txt``` ya koi image).

<br>
<br>

### Step 1: AWS Console mein Sign In karein

Sabse pehle aapko AWS cloud platform par login karna hoga.
- AWS Management Console par jayein.
- **Sign In to the Console** par click karein.
- Apne Root user ya IAM user ke credentials (Email aur Password) dalkar login karein.
- Top-right corner mein **Region** check karein. S3 ek global service hai, lekin bucket kisi specific region mein banti hai. Udaharan ke liye, **US East (N. Virginia)** ```us-east-1``` ya **Asia Pacific (Mumbai)** ```ap-south-1``` select karein.

<br>
<br>

### Step 2: S3 Dashboard par Jayen
- Top bar mein diye gaye **Search Bar** mein ```S3``` type karein.
- Services ke andar **S3** par click karein. Aap S3 ke main dashboard par pahunch jayenge.

<br>
<br>

### Step 3: Ek Naya S3 Bucket Banayein

S3 mein file rakhne ke liye hume ek container banana hota hai, jise **Bucket** kehte hain.
- Orange color ke Create bucket button par click karein.
- Ab aapko niche di gayi settings configure karni hain:

**A. General Configuration**:
- **Bucket type**: Yahan aapko do options dikhenge: **General purpose** aur **Directory** (S3 Express One Zone). Hum yahan General purpose select karenge.
- **Bucket name**: Yahan apne bucket ka ek unique naam likhein.
  - Niyam: Yeh naam poore AWS mein unique hona chahiye (jaise website ka domain name hota hai). Isme capital letters ya spaces nahi ho sakte.
  - Example: ```my-unique-learning-bucket-2026``` (Aap isme apna naam ya koi random number jod sakte hain).

<br>

**B. Object Ownership (Zaroori Security Update)**:
- Isko ACLs disabled (recommended) par hi check rehne dein. 2026 mein AWS poori tarah se IAM aur Bucket Policies par nirbhar hai, purane ACLs ko ab use nahi kiya jata.

Page ke aakhir mein scroll karein aur **Create bucket** par click karein.

<br>
<br>

### Step 4: S3 Bucket mein Object (File) Upload Karein

AWS S3 mein kisi bhi file ko Object ke roop mein store kiya jata hai.
- Apne naye bane hue bucket ```aws-2026-hands-on-lab-bucket``` ke naam par click karke use open karein.
- **Objects** tab ke andar, **Upload** button par click karein.
- **Add files** par click karein aur apne local system se ```lab-file.txt``` select karein.
- File select karne ke baad scroll down karein. Yahan aapko **Storage class** chunne ka option dikhega:
  - Isko default Standard storage class par hi rehne dein (Yeh frequent access aur live labs ke liye best hai).
- Sabse niche scroll karke **Upload** button par click karein.
- Upload poora hone par green color ka **Upload succeeded** message aayega. Top-right se **Close** par click karein.



DONE!!!
