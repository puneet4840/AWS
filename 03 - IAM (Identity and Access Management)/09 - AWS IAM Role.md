# AWS IAM Role Overview

AWS mein IAM Role ek identity hai jisko Users, applications aur services assume matlab use karte hain AWS resources ko securly access karne ke liye bina long-term credentials ka use kiye.

IAM Role ek AWS Identity hai jiske saath permissions attached hoti hain, lekin iske paas permanent username, password ya long-term access keys nahi hoti. Jab koi trusted entity jaise User, application ya services is Role ko assume matlab use karti hain, to AWS usse temporary security credentials provide karta hai.
Jisse trusted entities securly AWS ke resources ko use kar sake.

<br>

Jaise IAM User ek identity hai.

Waise hi IAM Role bhi ek identity hai.

Difference ye hai ki IAM User ek permanent identity hoti hai, jabki IAM Role ek assumable identity hoti hai. Yaani Role khud permanently kisi ek user ka nahi hota.

Jab zarurat hoti hai tab koi user, AWS service ya application us Role ko assume (temporarily use) karti hai.

<br>
<br>

### Sabse pehle problem samajhte hain

Maan lo tumhari company mein ek Application chal rahi hai. Ye Application AWS ke EC2 Instance ke upar run kar rahi hai.

```
Architecture.

Internet

↓

EC2 Instance

↓

Application

↓

S3 Bucket
```

Ye Application har 5 minute baad ek Log File generate karti hai. Aur us Log File ko S3 Bucket mein upload karna hai.

<br>

**Ab sawal**:

Application S3 Bucket mein file upload kaise karegi?

AWS S3 kisi bhi anonymous request ko accept nahi karta.

S3 hamesha poochta hai. ```"Request kisne bheji hai?"``` Aur. ```"Kya uske paas permission hai?"```

Yaani Authentication aur Authorization dono zaruri hain.

<br>

**Pehla idea**:

Developer bolta hai. Mai ek IAM User create karunga aur us user ke liye Access keys create kar dunga. Aur,

"Main Access Key aur Secret Access Key application ke andar save kar deta hoon."

Example.

Access Key ID
```
AKIAxxxxxxxxxxxx
```
```
Secret Access Key

abc123xxxxxxxxxxxxxxxx
```

Ab Application ye Keys use karke S3 mein upload kar degi. Sunne mein solution sahi lag raha hai. Lekin ye bahut dangerous hai.

<br>

**Dangerous kyun hai?**:

Maan lo kisi Hacker ne EC2 Instance compromise kar diya.

Ab Hacker EC2 ke andar ki files dekh sakta hai.

Agar Access Keys file ke andar save hain. To Hacker unhe copy kar lega.

Ab hacker sirf EC2 hi nahi. Poora AWS Account access kar sakta hai.

Yaani.

Problem sirf EC2 hack hone ki nahi hai. Problem AWS Credentials leak hone ki hai.

Matlab agar Access Keys EC2 ke ander store hain to hacker un keys ko EC2 hack karke dekh sakta hai.
