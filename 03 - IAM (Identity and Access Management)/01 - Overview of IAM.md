# Overview of IAM

AWS Identity and Access Management (IAM) ek service hai jo AWS resources ke access control ko securely manage karti hai.

Yh ek distributed cryptographic identity provider aur policy evaluation engine hai jo har incoming API request ko intercept karke Authentication aur Authorization process ko enforce karta hai.

AWS mein har action ek API call hota hai. Jab bhi koi user, application, ya AWS service kisi resource (jaise EC2 instance terminate karna, ya S3 bucket read karna) par action perform karti hai, toh IAM back-end par us request ko validate karta hai.

<br>

IAM AWS ki security service hai jo Users, Groups, Roles aur Policies ke through decide karti hai ki kaun User AWS mein login kar sakta hai aur kaun kis resource par kya action perform kar sakta hai.

Matlab IAM ek service hai jo authentication aur authorization karti hai.

<br>
<br>

### Sabse pehle problem samajhte hain

Maan lo tum ek company mein Cloud Engineer ho.

Company ne AWS Account banaya.

Is AWS Account ke andar bahut saare resources hain, Jaise:
- EC2 Instances
- S3 Buckets
- RDS Databases
- VPC
- Load Balancers
- Lambda Functions

Architecture kuch aisa hai.
```
AWS Account

|
|---- EC2
|
|---- S3
|
|---- RDS
|
|---- Lambda
|
|---- VPC
```

Ab company mein sirf tum nahi ho. Company mein alag-alag teams hain.
- DevOps Team
- Developers
- Database Team
- Network Team
- Security Team
- Finance Team

Ab sawal aata hai. 

Kya sabko AWS Account ka Root User de diya jaye?

<br>

**Agar Root User sabko de diya to?**

AWS mein jab hum ek account create karte hain to ek rrot user milta hai jiske thorugh hum AWS console mein login karte hain aur user ke paas highest priviliges hoti hain.

Socho company mein 200 employees hain. Aur sabke paas Root User ka username aur password hai.

Ab kya ho sakta hai?
- Ek Developer galti se Production Database delete kar de.
- Ek Intern poori VPC delete kar de.
- Koi EC2 terminate kar de.
- Koi IAM Users delete kar de.
- Koi Billing Information change kar de.

Company ka poora infrastructure kuch hi minutes mein destroy ho sakta hai.

Isliye Root User ko sabko dena bahut dangerous hai.

<br>

**AWS ne kya socha?**

AWS Engineers ne socha. Har employee ko Root User dena galat hai.

Har employee ko sirf utni hi permission milni chahiye jitni uske kaam ke liye zaruri hai.

Isi concept ko AWS ne implement kiya. Aur us service ka naam rakha.

**IAM**

Identity and Access Management.

<br>
<br>

### IAM ka Full Form samajhte hain

**Identity**:

Identity ka matlab. AWS ko ye pata hona chahiye ki request kisne bheji.

Matlab.
- Kaun login kar raha hai?
- Kaun API Call kar raha hai?
- Kaun EC2 create kar raha hai?
- Kaun S3 Bucket access kar raha hai?

Yaani.

User ki pehchan.

<br>

**Access**:

Access ka matlab. Ye User AWS account mein kya kar sakta hai? Aur kya nahi kar sakta?

Example.

- Developer, EC2 bana sakta hai. Lekin Billing nahi dekh sakta.
- Database Admin, RDS manage kar sakta hai. Lekin IAM delete nahi kar sakta.
- Finance Team, Billing dekh sakti hai. Lekin EC2 terminate nahi kar sakti.

Yaani.

Permission.

<br>

**Management**:

Management ka matlab. AWS in identities aur permissions ko centrally manage karta hai.

Yahi IAM ka kaam hai.

<br>
<br>

### IAM ka sabse bada principle

AWS ka ek bahut famous Security Principle hai.

**Least Privilege Principle**

Iska matlab hai har user ko sirf utni permission do jitni uske kaam ke liye zaruri hai. Usse zyada nahi.

<br>
<br>

### AWS IAM Kaise Kaam Karta Hai?

**1. Request Send Hona**:
- Jab bhi koi user ya service AWS par koi kaam karna chahti hai (jaise S3 bucket banana), toh woh AWS ko ek api request bhejti hai.
- Har request ek encrypted HTTPS call hoti hai jo AWS endpoint par pahunchti hai. Isme request headers ke andar Signature Version 4 (SigV4) cryptographic signature hota hai.

**2. Authentication**:
- AWS sabse pehle check karta hai ki request bhejne wala real hai ya nahi. Iske liye woh login credentials, Access Keys, ya digital signatures verify karta hai.
- IAM aapke Access Key ID aur Secret Access Key (ya temporary security credentials) ka use karke signature ko verify karta hai. Agar signature validation successful hota hai, toh request Creator ki Identity confirm ho jati hai.

**3. Authorization**:
- Pehchan confirm hone ke baad, IAM check karta hai ki is user ke paas woh kaam karne ki permission hai ya nahi. Iske liye IAM saari attached Policies ko read karta hai.

**4. Explicit Deny Check**:
- IAM ka ek rule hai: Agar kisi bhi policy mein kisi cheez ko Deny (mana) kiya gaya hai, toh user woh kaam bilkul nahi kar payega, chahe doosri policy mein 'Allow' hi kyun na likha ho. Deny ko hamesha sabse pehle priority milti hai.

**5. Evaluation Logic**:
- Agar koi Deny policy nahi hai, aur ek Allow policy milti hai, tabhi request approve hoti hai. Agar dono mein se kuch nahi milta, toh request by default block (Implicit Deny) ho jaati hai.

**6. Action Approve ya Deny**:
- Permissions match hone ke baad AWS request ko execute karta hai (jaise S3 bucket ban jata hai) ya phir 'Access Denied' ka error screen par dikha deta hai.

<br>
<br>

### IAM ke 4 Main Components

IAM ko samajhne ke liye iske 4 main components samajhna bahut zaruri hai.

- IAM Users.
- IAM Groups.
- IAM Policies.
- IAM Roles.

Ye chaaron milkar IAM ka foundation banate hain.

Aage ki slides mein hum ek-ek karke in sab chizo ko samjhenge.

