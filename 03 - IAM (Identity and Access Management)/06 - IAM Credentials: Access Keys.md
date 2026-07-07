# IAM Credentials: Access Keys

Jab aap AWS Management Console par login karte ho to aap username aur password daalkar console par login kar lete ho.

Lekin jab kisi programming code, automation script, CI/CD pipeline (jaise Jenkins ya GitHub Actions), ya developer machine se AWS ko access karna ho to toh wahan authentication handle karne ke liye IAM Access Keys ka use kiya jata hai.

<br>

IAM Access Keys IAM user ke credentials hote hain, jo programatically AWS ko access karne ke liye use kiye jaate hain. 

Matlab agar apko programatically jaise programming code, automation script, CI/CD pipeline se AWS ko access karna hai to vo AWS IAM ke Acces Keys ka use karke karte hain.

<br>

IAM Access Keys AWS Identity and Access Management (IAM) framework ke andar chalne wale Long-Lived Security Credentials hote hain. Technical terms mein, yeh asymmetric authentication mechanisms ke parallel kaam karne wale programmatic tokens hain, jinka use AWS Control Plane ke programmatic interfaces—jaise AWS CLI (Command Line Interface), AWS SDKs (Software Development Kits), aur Direct HTTPS API Calls—ko authenticate karne ke liye kiya jata hai.

IAM Access Keys ek type ke credentials hote hain jo IAM User ya application ko programmatically AWS Services ko access karne ki permission dete hain.

Yahan ek word bahut important hai:

**Programmatically**

Iska matlab hai:

Browser mein login karke kaam karna nahi.

Balki:
- AWS CLI
- Terraform
- Python Script
- Java Application
- Jenkins
- GitHub Actions
- Azure DevOps Pipeline
- Ansible

inke through AWS ko access karna.

<br>
<br>

### Sabse pehle problem samajhte hain

Maan lo tum ek DevOps Engineer ho. Tumhare paas ek IAM User hai.

Uska naam hai.
```
Puneet
```

Tum AWS Management Console open karte ho.

Browser mein login karte ho.

Username likhte ho.

Password likhte ho.

Agar MFA enabled hai.

To OTP bhi enter karte ho.

Fir tum AWS Console access kar lete ho.

Yahan tak sab kuch simple hai.

<br>

**Ab ek naya scenario**:

Maan lo tum ek Ubuntu Linux Server par kaam kar rahe ho.

Tumhare paas Browser hi nahi hai.

Ya Browser use nahi karna chahte.

Tum Terminal se AWS ko manage karna chahte ho.

Jaise.
```
aws s3 ls
```
Ya.
```
aws ec2 describe-instances
```

Ab sawal aata hai. CLI ko kaise pata chalega ki command kis user ne chalayi hai? Kya CLI tumse Username aur Password maangegi?

Nahi.

AWS CLI Username aur Password use nahi karti.

<br>

**AWS ne kya problem dekhi?**

AWS Engineers ne dekha ki bahut saare log AWS ko programmatically access karna chahte hain.

Jaise.
- AWS CLI
- Python Script
- Java Application
- Terraform
- Ansible
- Jenkins
- GitHub Actions
- Azure DevOps Pipeline

Ye sab Browser open nahi kar sakte.

Ye Username aur Password type nahi kar sakte. Inhe machine-to-machine authentication chahiye.

Isi problem ko solve karne ke liye AWS ne Access Keys introduce ki.

<br>
<br>

### Access Key ke do parts hote hain

Tum jab AWS console mein IAM user ke ander jake Access Keys create karte ho.

To tumhe do values milti hain:

**1. Access Key ID**:

Example.
```
AKIAIOSFODNN7EXAMPLE
```

Yeh ek 20-character ka alphanumeric string hota hai jo unique identifier (Public Component) ki tarah kaam karta hai.

Yeh bilkul aapke "Username" jaisa hai. AWS public endpoints par jab aap API call bhejte hain, toh yeh ID plaintext request parameters ya headers ke threw pass hoti hai taaki AWS ko pata chal sake ki kaunsa IAM User request bhej raha hai.

<br>

**2. Secret Access Key**:

Example.
```
wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```

Yeh ek 40-character ka highly confidential string hota hai jo "Private Component" ya "Password" ki tarah kaam karta hai.

Isse kabhi bhi kisi ke saath share nahi karna chahiye.

Crucial Rule: Yeh key sirf ek baar—creation ke waqt—hi screen par dikhni ya download (.csv format mein) hoti hai. Agar aapne ise kho diya ya save nahi kiya, toh aap ise dubara retrieve nahi kar sakte; aapko ek naye access key pair ko regenerate karna padega.

<br>
<br>

### Access Key kaam kaise karti hai?

Maan lo tum command chalate ho.
```
aws s3 ls
```
CLI sabse pehle tumhari configured Access Key ID aur Secret Access Key read karti hai.

Fir request ko digitally sign karti hai.

Ye signing process AWS Signature Version 4 (SigV4) ke through hota hai.

Iske baad request AWS ko bheji jaati hai.

AWS Access Key ID dekhkar identify karta hai ki request kis IAM User ya credential se aayi hai.

Fir Secret Access Key ke basis par signature verify karta hai.

Agar signature sahi hai.

To Authentication successful ho jaati hai.

Uske baad AWS IAM Policies check karta hai.

Agar permission bhi allowed hai.

To request execute ho jaati hai.

<br>
<br>

###  Lifecycle Management Protocols

IAM user identity level par Access Keys ki execution limits aur configuration settings strictly enforced hoti hain:

**Maximum Limit**: Ek single IAM User identity ke sath max Do **(2) Active Access Keys** hi links ho sakti hain. Yeh limit intentional hai taaki aap safely and zero downtime ke sath validation keys ko rotate kar sakein.

**Status States**: Keys do status pools mein rahti hain: Active (API requests handle karne ke liye enabled) aur Inactive (Temporary block, jisme tokens invalid ho jate hain lekin infrastructure delete nahi hota).

**Zero-Downtime Rotation Pipeline**:

Purani keys ko safely rotate karne ke liye is engineering sequence ko follow kiya jata hai:
- Ek naya key pair create karein (V2). Ab User ke paas do active keys hain: V1 (Old) aur V2 (New).
- Apne application scripts ya machines par naye credentials (V2) distribute karke update karein.
- VPC Flow Logs ya AWS CloudTrail monitor karke verify karein ki kya purani key ID (V1) par API activity fully zero ho chuki hai.
- Purani key (V1) ka status badal kar Inactive karein taaki testing confirm ho sake.
- Verification complete hone ke baad purani key (V1) ko permanently Delete kar dein.

<br>
<br>
<br>

## LAB: Create IAM user access key

Steps:
- Login to AWS account using Root user or IAM admin user.
- Go to IAM console -> Users -> Select the user for which IAM access key to be created.
- Go to Security Credentials -> Access Keys -> Create access key -> Save the keys locally.

