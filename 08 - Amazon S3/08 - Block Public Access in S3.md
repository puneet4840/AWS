# Block Public Access in S3

Amazon S3 ka **Block Public Access (BPA)** ek centralized security feature hai jo S3 buckets aur objects ko galti se internet par public hone se rokta hai. Yeh ek "Master Switch" ki tarah kaam karta hai jo IAM Policies, Bucket Policies, aur ACLs ke upar baithta hai. Agar BPA enable hai, toh aap chahe jitni koshish kar lein, aapka bucket public nahi ho sakta.

Iska basic kaam yeh ensure karna hai ki aapka S3 bucket ya uske andar ka data (objects) galti se bhi internet par public (sabke liye open) na ho jaye.

AWS mein data leak hone ka sabse bada reason human error (insani galti) hota hai. Koi developer galti se bucket policy ya ACL (Access Control List) galat set kar deta hai, aur data public ho jata hai. BPA isi galti ko rokne ke liye ek centralized safety net (suraksha kavach) ki tarah kaam karta hai.

<br>
<br>

### Yeh Kaise Kaam Karta Hai?

Block Public Access ko aap do levels par laga sakte hain:
- **Account Level**: Agar aapne ise account level par on kar diya, toh us AWS account ke sare S3 buckets par public access block ho jayega. Koi bhi naya ya purana bucket public nahi ban sakta.
- **Bucket Level**: Agar aap kisi specific bucket par ise lagate hain, toh sirf usi bucket ka data secure hoga, baaki buckets normal rahenge.

**Rule of Thumb**: Agar BPA ON hai, toh aapki baaki saari public permissions (Bucket Policies, ACLs) fail ho jayengi. BPA sabko override kar deta hai.

<br>
<br>

### Block Public Access (BPA) ko Account Level aur Bucket Level par On karna.

**A - Account Level par Block Public Access ON karna**:

- **AWS Management Console** mein login karein aur **Amazon S3** service open karein.
- Left navigation pane mein **"Account and organization settings"** (kuch consoles par "Block Public Access settings for this account") par click karein.
- Right side mein **Block Public Access settings for this account** ke under **Edit** button par click karein.
- **"Block all public access"** checkbox ko tick karein. (Isse charon sub-settings automatically select ho jayengi):
  - BlockPublicAcls
  - IgnorePublicAcls
  - BlockPublicPolicy
  - RestrictPublicBuckets
 
- **Save changes** par click karein.
- Confirm karne ke liye pop-up dialog box mein type karein: confirm aur **Confirm** par click kar dein.

<br>

**B - Bucket Level par Block Public Access ON karna**:

Agar aap account level ko BPA off rakh kar kisi individual bucket ko secure/block karna chahte hain, By default Account Level par BPA off hota hai.

- **Amazon S3 Console** par jayein aur **Buckets** ki list mein se apna bucket choose karein.
- Bucket ke andar jaakar **Permissions** tab par click karein.
- Scroll karke **Block public access (bucket settings)** section par aayein aur **Edit** par click karein.
- **"Block all public access"** checkbox ko select karein.
- **Save changes** par click karein aur prompt aane par confirm type karke confirm karein.

<br>
<br>

### BPA ke 4 Alag-Alag Settings

Jab aap BPA ON karte hain to wahan apko 4 settings dikhti hai, chalo unko samjhte hain:

AWS BPA ke andar 4 specific check-boxes hote hain:

**1. Block public access to buckets and objects granted through new access control lists (ACLs)**:

Yeh setting naye objects ya buckets ko public hone se rokti hai jo ACL ke zariye kiye ja rahe hon.

**Example**: Aapne ek naya photo upload kiya aur upload karte waqt uski setting mein ```public-read``` ACL laga diya taaki internet par sab dekh sakein. Agar yeh setting ON hai, toh AWS us upload ko hi reject kar dega ya use public nahi hone dega. Lekin jo purane photos pehle se public ACL ke sath hain, unpar farq nahi padega.

<br>

**2. Block public access to buckets and objects granted through any access control lists (ACLs)**:

Yeh pehli setting ka bada bhai hai. Yeh naye aur purane dono ACLs ko block kar deta hai.

**Example**: Aapki company ka ek purana bucket hai jismein 10,000 images hain aur sabhi par ```public-read``` ACL laga hua hai (yaani sab public hain). Jaise hi aap is setting ko **ON** karenge, woh saari 10,000 images ek second mein private ho jayengi. Purana koi bhi ACL ab kaam nahi karega.

<br>

**3. Block public access to buckets and objects granted through new public bucket policies**:

Yeh setting nayee (new) Bucket Policies ko block karti hai jo bucket ko public banane ki koshish karti hain.

**Example**: Ek developer ne ek naya JSON bucket policy likha:
```
{
  "Effect": "Allow",
  "Principal": "*",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::my-secure-bucket/*"
}
```
(Yahan ```Principal: "*"``` ka matlab hai internet ka koi bhi aadmi).

Agar yeh BPA setting **ON** hai, toh AWS developer ko yeh policy save hi nahi karne dega. Ek bada sa "Access Denied" ka error aa jayega.

<br>

**4. Block public access to buckets and objects granted through any public bucket policies**:

Yeh setting sari (naye + purani) **public bucket policies** ko ignore kar deta hai aur access block karta hai.

**Example**: Maan lo aapke bucket par upar public access provide karne wali bucket policy pehle se chal rahi thi aur log aapki website ki images wahan se dekh rahe the. Jaise hi aapne is 4th setting ko ON kiya, AWS us chal rahi policy ko override (bypass) kar dega. Policy wahan likhi hui toh dikhegi, lekin internet par logon ko 403 Forbidden error aane lagega.

<br>
<br>

### Best Practices: Block Public Access Kab ON karein aur kab OFF?

Aap sochenge ki agar yeh itna achha hai toh hamesha ON hi rakhein? Haan, 95% cases mein ise ON hi rakhna chahiye, lekin kuch exceptions hain:
- **Kab ON rakhein (Mandatory)**: Data lakes, financial reports, user personal data, source code, backups, aur internal logs wale buckets par ise hamesha ON rakhein.
  
- **Kab OFF rakhein (Exceptions)**: Agar aap S3 ka use Static Website Hosting ke liye kar rahe hain (jahan aap chahte hain ki poori duniya aapki HTML/CSS file dekhe), ya fir aap koi Public Assets (jaise company logo, public fonts, open-source software downloads) host kar rahe hain. Aise buckets par aapko BPA OFF karna padega aur manually secure bucket policy lagani hogi.
