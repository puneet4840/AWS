# AWS Users

AWS mein IAM user human user ko represent karta hai isko use karte hain AWS ke resources ko access karne ke liye.

Jaise ek insaan hai usko, AWS account access karna hai, to vo kaise karega. Uske liye AWS mein us insaan ke naam ka ek user create kiya jata hai, us user ke login credentials ka use karke vo insaan aws account par login karta hai. To kisi human ko AWS account ka login access dene ke liye hum AWS account mein ek user create karte hain.

IAM User ek individual identity hoti hai jo AWS account ke andar create ki jaati hai. Is user ka apna unique login hota hai aur isko sirf wahi permissions milti hain jo administrator assign karta hai.

IAM User AWS account ke andar ek unique identity (pehchan) hoti hai, jo kisi ek specific insaan ya phir kisi external application/program ko represent karti hai. 

<br>
<br>

### Sabse Pehle AWS Root User Ko Samajho

Jab tum AWS account create karte ho to us AWS account par login karne ke liye to AWS tumko ek user create karke deta hai.

Jisko bolte hain: **Root User**.

Root user AWS account ka owner hota hai. Iske paas AWS ki saari permissions hoti hain.

Root user par koi bhi IAM policy apply nahi ho sakti aur na hi isko permissions ko contorl kar sakte hain.

<br>

Example:

Tum Gmail account banate ho.
```
Email:
abc@gmail.com

Password:
xxxxxxxx
```
Ye tumhara main account hai.

AWS mein bhi:
```
AWS Account

↓

Root User

↓

Email + Password
```

Root User sab kuch kar sakta hai.
- EC2 delete
- IAM delete
- Billing access
- Account close
- Organization delete

Sab kuch.

AWS bolta hai, ki daily tasks ke liye Root User ko use nahi karna chiaye, iske liye tumko ek IAM user create karna chiaye jo daily task perform karega.

<br>
<br>

### Fir IAM User Kyun Banate Hain?

Maan lo tumhari company mein 500 employees hain.

Kya tum sabko Root User ka password de doge?

Answer hai **Bilkul nahi**.

Kyun?

Kyuki agar kisi insaan ko root user ke login credentials de diye to vo pura AWS account uda sakta hai.

Agar kisi se galti se production database delete ho gaya ya kisi ka password leak ho gaya, to poora AWS account danger mein aa sakta hai.

Isi problem ko solve karne ke liye IAM User ka concept introduce kiya gaya.

Administrator har employee ke liye ek alag IAM User create karta hai aur usko sirf utni hi permissions deta hai jitni uske kaam ke liye zaroori hain.

AWS account ke ander kuch bhi galat kar sakta hai, jo ki ek best practise nhi hai.

Isliye administrator har employee ke liye alag IAM User banata hai.

<br>

**Real Life Example**:

Maan lo company ka naam hai:
```
ABC Technologies
```

Company AWS use karti hai.

Company mein kaam karte hain:
```
Rahul
Puneet
Amit
Neha
Rohan
```

Ab is sabhi users ko AWS console par login karna hai, Administrator sabke liye IAM User create karega.
```
IAM User

Rahul

↓

Username:
rahul

Password:
********
```

```
IAM User

Puneet

↓

Username:
puneet

Password:
********
```

Ab ye users AWS console par inko diye gaye username aur password ka use karke login kar sakte hain.

<br>
<br>

### IAM User Ko Identity Kyun Bola Jata Hai?

Identity ka matlab sirf naam nahi hota. Identity ka matlab hota hai ki system ko ye pata ho ki login karne wala exactly kaun hai.

Maan lo AWS account mein teen log kaam karte hain.
- Rahul
- Puneet
- Neha

Teeno subah AWS Console mein login karte hain. AWS ko kaise pata chalega ki kaun login hua hai?

AWS unka username check karta hai.

Agar username "rahul" hai to AWS samajh jayega ki Rahul login hua hai.

Agar username "puneet" hai to AWS samajh jayega ki Puneet login hua hai.

Isliye IAM User ko identity kaha jata hai.

<br>
<br>

### IAM User Create Karne Ka Matlab Kya Hota Hai?

Jab administrator IAM User create karta hai, tab AWS ke andar ek nayi digital identity create hoti hai.

Us identity ke saath AWS bahut saari information store karta hai, Jaise:
- Username kya hai?
- Password hai ya nahi?
- Console login allowed hai ya nahi?
- Access Keys generate hui hain ya nahi?
- User kis group ka member hai?
- User ke paas kaunsi policies hain?
- MFA enable hai ya nahi?
- Kis date ko user create hua tha?
- Kisne create kiya tha?

Ye sab information AWS securely maintain karta hai.

<br>
<br>

### IAM User Ke Login Credentials

Ek IAM User ke paas generally do tarah ke credentials hote hain.
- AWS Management Console Password.
- Access Keys.

<br>

**1. AWS Management Console Password**:

Jab hum browser mein ```console.aws.amazon.com``` likhte hain to, ek aws ka console page open hota hai, jaha user apne username aur password daalkar aws console par login karta hai, isko hi AWS Management Console kehte hain.

- Yeh normal web interface hota hai jahan user browser ke zariye login karta hai.
- Security: Isme user ko ek Sign-in Link, Username, aur Password diya jata hai.
- Best Practice: Isme pehli baar login karte hi password change karne ka option (Require Password Reset) zaroor select karna chahiye.

User ko apne username aur password use karne hote hain console par login karne ke liye.
```
Username:
puneet

Password:
MyPassword123
```

<br>

**2. Access Keys**:

AWS mein har kaam browser par login karke nhi hota hai.

- Kabhi Terraform use karna hota hai.
- Kabhi AWS CLI use karni hoti hai.
- Kabhi Python script likhni hoti hai.
- Kabhi Jenkins pipeline EC2 create karti hai.
- Kabhi GitHub Actions deployment karta hai.

In sab cases mein browser se aws par login nahi hota.

Yahan **Access Key ID** aur **Secret Access Key** use hoti hain.

Agar aapko kisi script, code (Python/Node.js), AWS CLI (Command Line Interface), ya kisi third-party tool se AWS ko connect karna hai, toh aap programmatic access use karte hain.

Jab aap ek IAM User create karte hain, to uske saath Access Key ID aur Secret Access Key create hoti hai, jisko programatically AWS par login karne liye use kiya jata hai.

<br>
<br>

### IAM User Create Hone Ke Baad Kya Woh Sab Kuch Kar Sakta Hai?

Answer hai **Nahi**.

Jab administrator ek naya IAM User create karta hai, tab us user ke paas default mein koi permission nahi hoti.

Isko **Default Deny** kehte hain.

Naya IAM User jab banta hai, toh uske paas Zero Permissions hoti hain. Woh login toh kar payega, lekin AWS Console par use har jagah "Access Denied" ka error dikhega. Jab tak aap khud use permissions nahi denge, woh kuch nahi kar sakta.

**Example**:

Maan lo administrator ne sirf "Puneet" naam ka IAM User bana diya. Ab Puneet login karta hai aur EC2 service open karne ki koshish karta hai.

AWS turant check karega: "Kya is user ke paas EC2 access ki permission hai?"

Agar jawab "No" hai, to AWS Access Denied return karega.

Tumko alag se user ko permissions deni padenge, jab jake user aws account mein koi resource access kar payega.

<br>
<br>

### Ek Complete Real-World Scenario

Maan lo tum ek company mein DevOps Engineer ke roop mein join karte ho.

Administrator tumhare liye ek IAM User create karta hai.

Us user ka username puneet rakha jata hai.

Tumhe AWS Console login ke liye temporary password diya jata hai aur pehli login par password change karne ko bola jata hai.

Security ke liye tumhare account par MFA bhi enable kar diya jata hai.

Ab administrator tumhare kaam ko dekhte hue permissions assign karta hai:
- EC2 manage karne ki permission.
- VPC manage karne ki permission.
- S3 buckets ko sirf read karne ki permission.
- IAM ko sirf read-only access.

Ab jab tum EC2 instance create karoge, AWS allow karega.

Jab tum VPC create karoge, AWS allow karega.

Lekin agar tum IAM User delete karne ki koshish karoge, to AWS Access Denied dega, kyunki tumhare IAM User ko us action ki permission nahi di gayi hai.
