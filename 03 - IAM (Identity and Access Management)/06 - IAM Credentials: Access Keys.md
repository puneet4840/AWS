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

