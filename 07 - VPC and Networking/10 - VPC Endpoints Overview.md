# VPC Endpoints Overview

VPC Endpoint AWS ki esi service hai jo aapke VPC (Virtual Private Cloud) aur baaki AWS services (jaise S3, DynamoDB, EC2 APIs) ke beech ek private connection banati hai.

VPC Endpoint ek private connection hai jo tumhari VPC ko directly AWS Services se connect karta hai, bina Internet Gateway, NAT Gateway ya Public IP use kiye.

Yaani traffic kabhi bhi public internet par nahi jaata. Sab kuch AWS ke private backbone network ke andar hota hai.

<br>
<br>

### Sabse pehle problem samajhte hain

Maan lo tumhari company ne ek VPC banayi hai.

Uske andar ek Private Subnet hai.

Us Private Subnet mein ek EC2 Instance chal raha hai.

Architecture kuch aisa hai.
```
VPC

|

Private Subnet

|

EC2 Instance
```

Ye EC2 Private Subnet mein hai. Iske paas Public IP nahi hai. Ye bilkul secure hai.

Ab company chahti hai ki ye EC2 Amazon S3 se files download kare aur baaki ki aws service ko bhi access kare.

Jaise:
- Backup restore karna.
- Log files upload karna.
- Images download karna.
- Terraform state file read karna.
- Application ka data S3 mein store karna.

Ab sawal aata hai. EC2 S3 tak kaise pahunchhega?

**Pehla Solution - NAT Gateway**:

Sabse pehle DevOps Engineer kya sochega? Wo bolega.

"Private EC2 ko NAT Gateway de dete hain."

Architecture ban gaya.
```
Private EC2

↓

NAT Gateway

↓

Internet Gateway

↓

Internet

↓

Amazon S3
```

Ab EC2 S3 se baat kar sakta hai. Problem solve ho gayi. Lekin AWS Engineers ne kaha.

**"Ek minute."**

<br>

**Yahan actual problem kya hai?**:

EC2 kis service se baat kar raha hai? ```Amazon S3```.

Question: S3 kis company ki service hai? AWS ki.

Ab socho.

Dono services AWS ke Data Centers ke andar hi hain. To fir traffic ko internet par kyun bheja ja raha hai? Ye uneccessary hai.

Matlab jab dono services jaise vpc aur s3 aws ke ek hi data center mein avilable hain to traffic ko hum internet se hoke s3 tak kyu bhej rahe hain, internet se na bhejke traffic ko direct AWS ke private opticle-fiber network se hoke behjna chaiye.

To AWS ne bhi socha.

<br>

**AWS ne kya socha?**

AWS Engineers ne decide kiya. Jab dono services AWS ki hi hain. To in dono ko AWS ke private opticle-fibre infrastructure ke through ek private connection bana dete hain.

Taaki traffic internet par hi na jaaye.

Isi private connection ko AWS ne naam diya **VPC Endpoint**.

<br>

VPC Endpoint ka matlab hai ki AWS ki service ko apas mein AWS ke khud ke private opticle-fibre network se aapas mein karna. Data ko khud ke private network se ek dusre service ke paas behjna.

VPC Endpoint ek esi service hai jo ki aapke VPC (Virtual Private Cloud) aur baaki AWS services (jaise S3, DynamoDB, EC2 APIs) ko private infrastructure ke through connect karna, jisse traffic internet par hoke na jaye balki aws ke khud ke opticle-fiber network se hoke jaye.

