# AWS IAM Policy

AWS IAM Policy ek aisa formalized JSON (JavaScript Object Notation) document hai jo AWS Cloud Infrastructure ke andar User, Groups aur Roles ke liye Permissions aur Access Boundaries ko define karta hai.

IAM Policy ek JSON document hota hai jiske ander access permissions ko json form mein likha jata hai. Jisse User, Groups aur Roles ke liye permissions dete hain.

Hum permissions ko JSON form mein likhte hain jisse ek policy create ho jati hai aur us policy ko  User, Groups ya Roles ko attach karte hain.

<br>

IAM Policy ek JSON document hoti hai jo define karti hai ki koi IAM User, Group ya Role kis AWS Resource par kaunsa Action perform kar sakta hai ya nahi kar sakta.

AWS IAM (Identity and Access Management) Policy ek tarah ka permission document hota hai. Yeh document decide karta hai ki AWS mein kaun kya kaam kar sakta hai aur kya nahi.

AWS mein, by default har resource ka access Implicitly Denied hota hai. Jab tak ek matching IAM Policy explicit permission allow nahi karti, tab tak koi bhi identity koi API call execute nahi kar sakti.


<br>
<br>

### AWS Mein Authentication Aur Authorization Ka Complete Flow

Jab bhi koi user, role, application, script ya service AWS ke kisi resource ko access karne ki request bhejti hai, tab AWS us request ko directly execute nahi karta.

AWS sabse pehle us request ki identity verify karta hai aur uske baad ye decide karta hai ki us identity ko requested action perform karne ki permission hai ya nahi.

Isi process ko do major phases mein divide kiya jata hai:
- Authentication
- Authorization

Ye dono processes ek ke baad ek execute hote hain.  Authentication complete hue bina Authorization start nahi hota.

<br>

**Step 1 : Request AWS Tak Pahunchti Hai**:

Sabse pehle koi request AWS ke paas aati hai. Ye request kisi bhi source se aa sakti hai.
- AWS Management Console
- AWS CLI
- AWS SDK
- Terraform
- CloudFormation
- Jenkins
- GitHub Actions
- Lambda
- EC2 Instance
- Kisi Application ke through

Source koi bhi ho, AWS ke liye har request ek API Request hoti hai.

Maan lo ek IAM User EC2 instance create karna chahta hai. Usne AWS console, AWS CLI ya kisi application ke thorugh EC2 create karne ki request bheji, user ki request API request mein convert hui aur AWS ke paas aayi.

Ab ye request AWS ke paas pahunch gayi. Lekin AWS is request ko turant process nahi karega.

AWS yahan request ka Authentication aur Authorization karega.

<br>

**Step 2 : Authentication Start Hota Hai**:

Jab bhi koi user, script, ya application AWS ko request bhejti hai, toh AWS sabse pehle uski identity check karta hai.

Authentication ka matlab hota hai: AWS ye verify karta hai ki request bhejne wali identity kaun hai.

Kaise hota hai? 
- Username/Password, Access Keys, ya MFA (Multi-Factor Authentication) ke zariye.
- Result: Agar credentials sahi hain, toh AWS kehta hai, "Haan, main aapko pehchanta hoon." Lekin iska yeh matlab nahi hai ki aap AWS mein kuch bhi delete ya create kar sakte hain.

Authentication Mein AWS Kya Check Karta Hai?

AWS request ke saath aaye credentials ko verify karta hai. Ye credentials alag-alag ho sakte hain.

Jaise:

Console Login ke case mein:
- Username
- Password
- MFA Token

CLI ya SDK ke case mein:
- Access Key ID
- Secret Access Key

IAM Role ke case mein:
- Temporary Security Credentials

Federated Login ke case mein:
- Identity Provider ke credentials

AWS request ke saath aaye credentials ko verify karta hai. Agar credentials valid hain to authentication successful ho jata hai. Agar credentials invalid hain to request isi stage par reject ho jaati hai. Authorization tak request pahunchti hi nahi.


Jaise hi AWS identity verify kar leta hai, uske baad second phase start hota hai. Ye phase hota hai: Authorization.

<br>

**Step 3: Authorization Start Hota Hai**:

Authorization ka matlab hota hai: AWS ye determine karta hai ki authenticated identity requested action perform kar sakti hai ya nahi.

- Kaise hota hai? Yahan entry hoti hai IAM Policies ki.
- Result: Agar policy permission deti hai, toh request Allow hoti hai, nahi toh Deny ho jati hai.

Is phase mein IAM permission evaluation engine applicable policies ko evaluate karta hai.

Ab AWS identity ko identify kar chuka hai.

Ab AWS us identity ke permissions evaluate karega.

- IAM us identity ke saath attached saari policies collect karta hai.
- Ye policies directly IAM User ke saath attached ho sakti hain.
- Ya IAM Group ke through inherit ho sakti hain.
- Ya IAM Role ke saath attached ho sakti hain.

Policy ke andar likha hota hai:
- Kaunsa Action allowed hai.
- Kaunse Resource par allowed hai.
- Kis Condition mein allowed hai.

Jab saari policies evaluate ho jaati hain, tab AWS final authorization decision leta hai.

Default Deny:
- By default, AWS mein har ek request Deny (reject) hoti hai. Agar aapne koi policy nahi lagayi hai, toh user kuch nahi kar sakta.

Explicit Allow:
- User ko koi action perform karne ke liye (jaise EC2 instance start karna ya S3 bucket create karna), policy mein saaf-saaf "Allow" likha hona chahiye.

Explicit Deny:
- Agar kisi bhi policy mein kisi action ke liye "Deny" likha hai, toh woh sabse powerful hota hai. Chahe doosri 10 policies mein "Allow" likha ho, ek single "Deny" sabko cancel kar dega.

Jaise hi authorization successful hota hai, identity action perform karti hai.

<br>

**Agar Authentication Fail Ho Jaye**:

Agar credentials invalid hain. Ya credentials expire ho chuke hain. Ya signature valid nahi hai. To AWS request ko immediately reject kar deta hai. Is case mein policy evaluation hoti hi nahi. Kyuki identity verify hi nahi hui.

<br>

**Agar Authentication Successful Ho Lekin Authorization Fail Ho Jaye**:

Ye situation bahut common hoti hai.

Identity valid hai.

AWS ko pata hai request kis IAM User ya IAM Role ne bheji hai.

Lekin us identity ke paas requested action ki permission nahi hai.

Is situation mein AWS response deta hai:

```Access Denied```

Yaani authentication pass ho gaya.

Lekin authorization fail ho gaya.
