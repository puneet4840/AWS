# IAM Permission Boundary

Permission Boundary ek managed IAM Policy hoti hai jo kisi IAM User ya IAM Role ke liye maximum permissions ki boundary define karti hai. Ye permissions grant nahi karti, balki ye permission limit set karti hai ki identity ko maximum kitni permissions mil sakti hain.

Permission Boundary ka sidha sa matlab hai ki ye user ki permission ke liye ek limit set karti hai.

Permission Boundary Allow nahi karti. Permission Boundary sirf limit set karti hai.

Permissions Boundary ek special type ki IAM Policy hoti hai jo IAM User ya IAM Role ke liye maximum permissions define karti hai.

Normal IAM Policy ye batati hai:
- "Is identity ko kaunsi permissions deni hain."

Lekin Permissions Boundary ye batati hai:
- "Is identity ko is limit se zyada permissions kabhi nahi mil sakti."

<br>
<br>

Jab aap kisi identity par permissions boundary apply karte hain, toh woh user ya role sirf wahi API actions execute kar sakta hai jo uski Identity-Based Policy AND uski Permissions Boundary dono ke intersection (common area) mein aate hain.

Iska seedha matlab yeh hai ki kisi AWS user ya role ke paas koi kaam karne ki taqat tabhi hogi, jab use do alag-alag jagahon par haan (allow) bola gaya ho. Agar kisi ek jagah bhi mana kiya ya allow nahi kiya, toh woh kaam nahi karega.

**1. Dono Policies Kya Hain?**:
- **Identity-Based Policy**: Yeh woh permission hai jo aap normal user ko dete hain (jaise: "Aap S3, EC2 aur RDS use kar sakte ho").
- **Permissions Boundary**: Yeh ek tarah ki boundary (deewar) hai jo user ki maximum limits set karti hai (jaise: "Aap duniya bhar ke saare kaam kar lo, par S3 aur EC2 se bahar nahi ja sakte").

**2. Intersection (Common Area) Ka Matlab**:
- Intersection ka matlab hai dono ka aapas mein milna. User sirf wahi kaam kar payega jo dono (Identity Based Polic aur Permission Boundary) mein likhe honge.

Ise ek simple example se samajhte hain:
- Identity-Based Policy (User kya karna chahta hai): S3, EC2, aur RDS.
- Permissions Boundary (Company ne kya limit lagayi hai): S3, EC2, aur DynamoDB.

Result (User kya kar payega):
- S3: Kar sakta hai (Dono mein hai).
- EC2: Kar sakta hai (Dono mein hai).
- RDS: Nahi kar sakta (Boundary mein allow nahi hai).
- DynamoDB: Nahi kar sakta (Identity policy mein allow nahi hai).

<br>

**Example**:

Maan lijiye aapne kisi Junior Admin ko Permission Policy ke zariye ```AdministratorAccess``` (full power) de di, lekin saath mein ek Permission Boundary lagayi jisme likha hai ki "Kucch bhi ho jaye, yeh S3 bucket delete nahi kar sakta". Toh woh user full admin hote hue bhi S3 bucket delete nahi kar payega.

<br>
<br>

### Sabse pehle problem samajhte hain

Maan lo tum ek badi company mein Senior Cloud Engineer ho.

Tumhare neeche 20 Junior Cloud Engineers kaam karte hain.

Company chahti hai ki Junior Engineers khud IAM Users aur IAM Roles bana saken.

Kyunki har baar Senior Engineer ko involve karna practical nahi hai.

To tum unhe IAM User create karne ki permission de dete ho.

Ab problem shuru hoti hai.

<br>

**Problem kya hai?**:

Maan lo Junior Engineer ka naam hai Rahul.Rahul ke paas permission hai.
```
iam:CreateUser
```
Ab Rahul naya User bana sakta hai.

Ye to theek hai.

Lekin Rahul ek aur kaam karta hai.

Wo naye User ke saath ye Policy attach kar deta hai.
```
AdministratorAccess
```
Ab jo naya User bana.

Uske paas poore AWS Account ki permissions aa gayin. Matlab vo account ka administrator ban gya.

Ab wo naya user:
- EC2 delete kar sakta hai.
- S3 Bucket delete kar sakta hai.
- IAM Users delete kar sakta hai.
- Billing dekh sakta hai.
- VPC delete kar sakta hai.

Yaani.

Junior Engineer indirectly poore AWS Account ka Admin bana gaya.

Ye bahut bada security issue hai. Lekin esa nhi hona chiaye tha. Isliye is chiz ko rokne ke liye AWS ne Permission Boundary jaisa security feature intorduce kiya.

Isme user ke paas ek limit set ho jati hai ki user is permission se jyada kuch nhi kar sakta.

<br>

**AWS ne kya socha?**

AWS Engineers ne socha.

```"Hume aisa mechanism chahiye jo ye limit kar sake ki koi IAM User ya IAM Role maximum kitni permissions le sakta hai."```

Dhyan do.

Permission Boundary permission grant nahi karti.

Ye sirf maximum limit set karti hai.

Isi wajah se iska naam hai.

Permission Boundary

Boundary matlab.

Maximum Boundary.

Uske bahar nahi ja sakte.

<br>
<br>

### Permission Boundary ka main purpose

Permissions Boundary ka purpose hai:

Maximum permissions define karna.

Ye decide karti hai ki:

Ek IAM User ya IAM Role ko future mein bhi is limit ke bahar permissions nahi mil sakti.

Chahe koi uske saath aur policies attach kar de.

<br>
<br>

### IAM Policy aur Permission Boundary mein Difference

Suppose Rahul ke paas ek IAM Policy hai.
```
EC2 Full Access
```
Ye Policy bolti hai.

Rahul EC2 ke saare actions kar sakta hai.

Ab Rahul ke saath ek Permission Boundary bhi attach hai.

Usme likha hai.
```
Sirf EC2 Start aur Stop
```
Ab kya Rahul EC2 Delete kar sakta hai?

Nahi.

Kyun?

Kyunki Boundary usse allow nahi karti.

Yaani.

Final permissions hamesha IAM Policy aur Permission Boundary ke intersection hoti hain.

<br>

**Complete Evaluation Flow**:

Suppose Rahul EC2 Delete karna chahta hai.

AWS kya karega?

**Step-1**: Authentication.

Rahul kaun hai?

Authentication successful.

**Step-2**: Identity Policy check.

Policy bolti hai.
```
ec2:TerminateInstances

Allow
```

**Step-3**: Permission Boundary check.

Boundary bolti hai.
```
ec2:TerminateInstances

Not Allowed
```

**Final Result**

Access Denied.

Kyunki Boundary ne us permission ko allow nahi kiya.

<br>
<br>

### Real Production Example

Maan lo ek Company mein 50 Developers hain.

Company chahti hai.

Developers khud IAM Roles bana saken.

Lekin Company nahi chahti ki koi Developer Administrator Role bana de.

To Company ek Permission Boundary create karti hai.

Boundary ke andar likha hai.
```
EC2

S3

CloudWatch

Lambda
```
Bas.

Ab Developer jitne bhi naye Roles banayega.

Wo sirf inhi Services tak limited rahenge.

Chahe wo galti se AdministratorAccess attach karne ki koshish kare.

Boundary usse rok degi.

<br>
<br>

### Permission Boundary kin par lag sakti hai?

Ye sirf do identities par lag sakti hai.
- IAM User.
- IAM Role.

IAM Group par Permission Boundary apply nahi hoti.

**Kyu?**

Kyuki Boundary individual identity ki maximum permission limit define karti hai.

Group khud login nahi karta.

Group sirf permissions inherit karwata hai.

Isliye Boundary sirf User aur Role par apply hoti hai.

<br>
<br>

### Permissions Boundary Kab Use Karni Chahiye?

Permissions Boundary generally in situations mein use hoti hai:

**Delegated Administration**:

Jab kisi aur administrator ko limited administrative powers deni ho.

<br>

**Self-Service IAM**:

Jab Developers khud IAM Users ya IAM Roles create karte hon.

<br>

**Large Organizations**:

Jahan multiple teams IAM resources manage karti hain.

<br>

**Security Control**:

Jahan organization maximum permission limit centrally enforce karna chahti ho.
