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

<br>

**AWS ne kya socha?**

AWS Engineers ne socha. Machines ko bhi AWS access chahiye. Lekin hum permanent credentials nahi dena chahte.

To kyun na Machine ko temporary credentials diye jayein. Aur jab zarurat khatam ho. To wo credentials expire ho jayein.

Isi concept ka naam hai **IAM Role**.

Isse pehle humne padha tha ki IAM User ek insaan (human) ke liye hota hai jiske paas permanent password ya access keys hoti hain. Lekin IAM Role ko koi insaan directly login karne ke liye use nahi karta, balki ise AWS services, applications, ya dusre accounts ke users asume (adopt) karte hain.

<br>
<br>

### Role Kaise Kaam Karta hai

AWS IAM Role ek temporary Identity (pehchan) hoti hai jise aap AWS resources ko secure access dene ke liye banate hain. Isme koi username ya password nahi hota. Jab koi user ya service is role ko assume (adopt) karti hai, toh AWS unhe temporary security credentials deta hai.

Yeh poora system do main policies par chalta hai: **Trust Policy** aur **Permission Policy**.

```
                  ┌───────────────────────────────────────────┐
                  │              IAM ROLE ENGINE              │
                  └─────────────────────┬─────────────────────┘
                                        │
             ┌──────────────────────────┴──────────────────────────┐
             ▼                                                     ▼
┌─────────────────────────┐                           ┌─────────────────────────┐
│ Trust Policy (Inbound)  │                           │Permission Policy(Outbound)
└────────────┬────────────┘                           └────────────┬────────────┘
             │                                                     │
   "Kaun is Role ke andar  "                               "Role ke andar aane ke baad,
    enter kar sakta hai?"                                   kya-kya kaam kiya ja sakta hai?"
```
<br>

**1. Trust Policy Kya Hai Aur Kaise Kaam Karti Hai?**

Trust Policy yeh decide karti hai ki kaun is role ko istemal (assume) kar sakta hai. Matlab kon is role ko use karke aws resource access kar sakta hai. Jab tak Trust Policy allow nahi karegi, koi is role ko touch bhi nahi kar sakta. Isko ```"Principal"``` bhi kehte hain.
- Kaise kaam karti hai: Agar aap chahte hain ki sirf ek specific EC2 server ya koi doosra AWS account hi is role ko use kare, toh aap use Trust Policy mein define karenge.
- Example: Agar aapne trust policy mein ://amazonaws.com likha hai, toh sirf EC2 service hi us role ko touch kar payegi, koi lambda function nahi.
- Example: "Sirf EC2 service hi is role ko use kar sakti hai."

<br>

**2. Permission Policy Kya Hai Aur Kaise Kaam Karti Hai?**

Permission Policy yeh decide karti hai ki role assume karne ke baad samne wala kya kya kaam kar sakta hai. Yeh ek standard IAM Policy (JSON document) hoti hai jo yeh decide karti hai ki is role ke paas AWS resources par kya permissions hongi.
- Kaise kaam karti hai: Yeh un actions ki list hoti hai jo allowed ya denied hain.
- Example: Isme aap likhte hain ki yeh role sirf S3 bucket se files read (s3:GetObject) kar sakta hai, par delete (s3:DeleteObject) nahi kar sakta.
- Example: "S3 bucket se data read karne ki permission."

<br>

**Kya Trust Policy Aur Permission Policy Dono Ek Saath Attach Karni Hoti Hain?**

Haan, dono policies ka ek saath hona zaroori hai, tabhi IAM Role poori tarah kaam karega. Inka relation aise samjho:

- Trust Policy bina Permission Policy ke: User ya service role ke andar enter toh kar payegi (kyunki trust hai), par andar aane ke baad woh kuch kaam nahi kar payegi (kyunki permissions zero hain).
- Permission Policy bina Trust Policy ke: Role ke paas bohot saari powers hain, par un powers ko use karne ke liye koi bhi andar nahi aa sakta (kyunki kisi par trust nahi hai). AWS console par jab aap Role banate hain, toh yeh dono policies setup karna mandatory hota hai.

<br>

**In Policies Ko Kaise Attach Karte Hain?**

Jab aap AWS Management Console use karte hain:
- **IAM Dashboard** mein jakar **Roles** par click karein aur **Create Role** select karein.
- Sabse pehle aapko **Trusted Entity** chunna hota hai (jaise AWS Service, ya Dusra AWS Account). Yeh step automatic aapki **Trust Policy** bana deta hai.
- Agle step mein aapko **Permissions** select karne ka option milta hai. Yahan aap AWS ki bani-banaye (Managed) ya apni custom JSON policy select karte hain. Yeh aapki **Permission Policy** hoti hai.
- Role create hone ke baad, aap kabhi bhi Role ke andar jakar **Trust Relationships** tab se trust policy badal sakte hain, aur **Permissions** tab se permission policy add ya remove kar sakte hain.

Ab tumhara role create ho chuka hai, use karne ke liye.

<br>

**Role Create Karne Ke Baad Kya Hota Hai?**

Role banne ke baad woh active ho jata hai aur temporary access dene ke liye tayar rehta hai, Fir aap is role ko AWS ki service ke saath attach karte hain. 

Role attach karne ke baad AWS ki service is role ko use karke dusri AWS service ko securely access kar sakti hai.

<br>
<br>

### IAM Role Kaun-Kaun use kar sakta hai?

IAM Role ko ye 3 chize use kar sakti hain:
- AWS service such as Amazon EC2, Lambda to access other AWS service or resource.
- An IAM user in the same or a different AWS account (e.g. for cross-account access).
- An external user authenticated by an external identity provider (IdP) service such as Google, 
Facebook to get access to AWS accoun.

Ab inko detail mein samjho.

Industry mein IAM Roles ka use teen major scenarios mein sabse zyada hota hai:

<br>

**Use Case 1: AWS Services Ko Dusri Service Access Karne Dena (EC2 to S3)**:

Maan lijiye aapka ek Java application ek EC2 instance (virtual server) par chal raha hai. Is application ko S3 bucket se kuch images read karni hain.
- **Galat Tarika**: Aapne ek IAM User banaya, uski Access Keys nikali, aur use EC2 instance ke andar ek file mein save kar diya. Agar kal ko aapka wo server hack ho gaya, toh hacker ko wo permanent Access Keys mil jayengi aur aapka poora account khatre mein pad jayega.

- **Sahi Tarika (IAM Role)**: Aap ek IAM Role banayenge jiske paas S3 Read permission hogi. Is role ko aap EC2 Instance Profile ke zariye directly us EC2 server par attach kar denge. Ab EC2 server ke andar chal raha AWS CLI ya SDK code piche se automatic temporary credentials utha lega jo har 1 se 6 ghante mein badalte rehte hain. Fir EC2 instance, S3 ko securly access kar sakti hai. Agar server hack bhi ho gaya, toh hacker ke haath koi permanent key nahi lagegi.

<br>

**Use Case 2: Cross-Account Access (Ek AWS account se dusre AWS account mein jana)**:

Maan lijiye aapki company ke paas do AWS accounts hain: ek Development Account aur ek Production Account. Aap Dev account ke kisi developer ko Production account ke kisi S3 bucket ko check karne ki permission dena chahte hain.
- Aap Production account mein ek IAM Role banayenge aur uski Trust Policy mein likhenge ki Dev account ka yeh specific user is role ko assume kar sakta hai.
- Developer Dev account se login karega aur AWS Console mein "Switch Role" par click karke Production account ke resources ko safe tarike se access kar lega, bina naye password ke.

<br>

**Use Case 3: Identity Federation (Office ke login se AWS chalana)**:

Badi companies mein hazaron employees hote hain. Har ek employee ke liye alag se AWS IAM User banana pagalpan hoga.
- Iske liye SSO (Single Sign-On) ya SAML 2.0 ka use hota hai. OpenID Connect (OIDC) ya SAML 2.0 federation framework setup kiya jata hai.
- Employee apne office ke email aur password (jaise Active Directory ya Okta) se login karta hai. AWS unhe verify karke ek temporary IAM Role de deta hai. Jab employee office chhodta hai, toh office ka email block hote hi uska AWS access bhi automatic khatam ho jata hai.

<br>
<br>

### IAM Role Step-by-step kaise kaam karta hai?

AWS IAM Role ko assume karne ka poora process background mein kaise chalta hai, isko step-by-step aur aasan shabdon mein samajhte hain.

Maan lijiye aapke paas ek EC2 Instance (Virtual Server) hai. Aapne is server ke andar ek Python script likhi hai jo S3 Bucket se daily reports download karti hai. Is kaam ke liye EC2 server IAM Role ko kaise assume karega.

IAM Role se pehle log kya karte the? Woh user ki permanent Access Key aur Secret Key ko code ke andar ya server ki ek file mein save kar dete the. Yeh bilkul unsafe hai. Agar server hack hua, toh poora AWS account khatre mein aa jata hai.

Iska solution hai IAM Role. Aap decide karte hain ki aap ek aisi temporary pehchan (Identity) banayenge jise server khud adopt kar sake.

<br>

**Step 1: IAM Role Create Karna (Console Par Setup)**:

Aap AWS Management Console mein IAM Dashboard par jaate hain aur Create Role par click karte hain. Yahan aap do main cheezein banate hain:
- Pehla aap aws resource select karte hain jiske liye ye role create hoga aur use hoga. AWS backend mein ek trust policy create karta hai, jo esi dikhti hai:
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": { "Service": "://amazonaws.com" },
      "Action": "sts:AssumeRole"
    }
  ]
}
```
Yahan ```sts:AssumeRole``` ka matlab hai ki EC2 ko is role ko adopt karne ki permission hai.

- Dusra aap role ke liye permission set karte hain, jiske liye apko ek policy create karni hoti hai. Isme ya to aap aws managed policies select kar sakte hain ya fir khud ki json document mein policy create kar sakte hain.

Suppose Aap select karte hain S3 ki permission. JSON mein yeh aisa dikhta hai:
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-report-bucket/*"
    }
  ]
}
```
Yahan ```s3:GetObject``` ka matlab hai sirf file download karne ki permission.

Ab aap is role ko ek naam deta hain,  jaise: ```EC2-S3-Reader-Role```. Aur role ko create kar dete hain, apka rol create ho jata hai.

<br>

**Step 2: Role Ko Server Se Attach Karna**:

Role banne ke baad, aap EC2 Console par jaate hain. Apne chalte hue server (Instance) ko select karte hain.
- Actions -> Security -> Modify IAM Role par click karte hain.
- Dropdown se aap apna banaya hua ```EC2-S3-Reader-Role``` select karke save kar dete hain.
- Back-end mein kya hua: AWS aapke server ke virtual hardware settings mein ek link bana deta hai jise IAM Instance Profile kehte hain. Ab server ko pata chal chuka hai ki uski nayi identity kya hai.

<br>

**Step 3: Application Code Ka Request Bhejna**:

Ab aap server ke andar apni Python script run karte hain. Script mein aapne AWS ka official tool AWS SDK (Boto3) use kiya hai. Code kuch aisa dikhta hai:
```
import boto3
s3 = boto3.client('s3')
s3.download_file('my-report-bucket', 'report.pdf', 'local.pdf')
```

Jaise hi yeh code chalta hai, AWS SDK active ho jata hai. Woh poore server mein dhoondhta hai ki login karne ke liye chabi (Credentials) kahan hain. Jab use koi permanent key nahi milti, toh woh samajh jata hai ki mujhe server ke internal meta data se temporary key maangni padegi.

<br>

**Step 4: IMDS Se Contact Karna (Internal Request)**:

AWS SDK ab server ke andar hi ek special local IP address par background mein ek HTTP call bhejta hai. Is IP ko **IMDS (Instance Metadata Service)** kehte hain.
- IP address hota hai: ```http://169.254.169```.
- Yeh IP sirf usi server ke andar kaam karta hai, internet par nahi. Yeh server ka apna ek internal board computer hai jo AWS infrastructure se juda hota hai.

<br>

**Step 5: AWS STS Ka Pichhe Se Entry Lena**:

Server ka IMDS service us request ko receive karta hai. Woh turant AWS ke ek bohot bade security service ke paas jata hai jise **AWS STS (Security Token Service)** kehte hain.

IMDS bolta hai: "Bhai, mere andar jo code chal raha hai, use ```EC2-S3-Reader-Role``` ki temporary credentials chahiye."

<br>

**Step 6: Verification Aur Trust Policy Ka Check**:

AWS STS ke paas jaise hi request aati hai, woh sabse pehle us Role ki Trust Policy (jo aapne Step 2 mein banayi thi) ko open karta hai.
- STS verify karta hai: "Kya yeh request sach mein usi EC2 instance se aa rahi hai jise permission mili thi?"
- Jab digital signatures aur security checks pass ho jaate hain aur Trust Policy match ho jaati hai, toh STS agla step shuru karta hai.

<br>

**Step 7: Temporary Credentials Generate Hona**:

Verify hote hi, STS machine ek naya temporary token package generate karti hai. Is package mein 4 cheezein hoti hain:
- **Access Key ID**: Yeh temporary hoti hai aur hamesha ASIA se shuru hoti hai.
- **Secret Access Key**: Ek lamba encrypted text jo request ko sign karne ke kaam aata hai.
- **Session Token**: Ek extra security string jo sirf temporary logins mein milti hai.
- **Expiration Time**: Ek timer (jaise default thik 1 ghante baad ka time) jab yeh credentials bekaar (expire) ho jayegi.

<br>

**Step 8: Credentials Ka Code Tak Pahunchna**:

AWS STS yeh charo cheezein vapas EC2 ke IMDS ko bhejta hai, aur IMDS ise chupchaap aapke chalte hue Python code (AWS SDK) ki memory mein load kar deta hai. Yeh sab kuch RAM ke andar hota hai, server ki hard disk par koi credentials save nahi hoti, jisse security top-level rehti hai.

<br>

**Step 9: S3 Bucket Se Final Access Aur Validation**:

Ab aapka Python code un temporary keys ka use karke S3 bucket ko real request bhejta hai: "Mujhe report.pdf file de do".
- S3 Bucket ke paas request jaate hi, woh AWS IAM firewall se poochta hai: "Mere paas ek token aaya hai, kya iske paas file download karne ki permission hai?"
- IAM database check karta hai aur dekhta hai ki is Role ki Permission Policy mein ```s3:GetObject``` allowed hai.
- S3 turant lock khol deta hai aur file download ho jaati hai. Aapka code bina kisi error ke successfully chal jata hai.

<br>

**Step 10: Auto-Refresh System (Credentials Badalne Ka Process)**:

Aapka server 24 ghante chal raha hai, par STS ne jo credentials di thi woh toh 1 ghante mein expire ho jayege.
- Yahan par AWS SDK ka dimaag kaam aata hai. SDK lagatar background mein timer dekhta rehta hai.
- Jaise hi 50-55 minutes hote hain, SDK aapke chalte hue main code ko bina disturb kiye, khud-ba-khud Step 4 ko dobara repeat kar deta hai.
- Woh firse IMDS ke paas jata hai, IMDS firse STS se nayi credentials lata hai, aur purani expire hone se pehle nayi chabi memory mein replace ho jaati hai.

Is tarah aapka application saalo-saal chalta rehta hai aur background mein credentials automatic badalti rehti hain.

