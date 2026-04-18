# AWS IAM (Identity and Access Management)

### AWS IAM kya hota hai?

AWS Identity and Access Management (IAM) ek service hai jo decide karti hai: “Kaun (who) AWS ke kaunse resource ko (what) kis tarah se (how) access karega”.

IAM (Identity and Access Management) AWS ki ek service hai jo AWS ki services aur resources par user ka access manage karti hai.

<br>
<br>

### IAM ke Core Components

IAM ko samajhne ke liye 5 major building blocks hain:
- Users.
- Groups.
- Policies.
- Roles.
- Permissions.

<br>

### Users

IAM User ek identity hai AWS ke ander.

AWS mein IAM user human user or workload ko represent karta hai jo IAM user ko use karte hain AWS ke resources ko access karne ke liye.

Aur IAM User ka matlab hai:
- Ek individual identity (insaan ya application) jo AWS resources use kar sakta hai.

Simple language mein:
- IAM User = “AWS account ke andar ek alag login account”.

AWS mein user 2 type ke hote hain:
- **Root User**: Owner of AWS Account.
- **IAM User**: Individual user, services or applications.

Jab tum ek aws account create karte ho to by default ek root user create hoke milta hai jiske paas sabhi permission aur access hoti hain.

Lekin jab tum alag se user create karte ho IAM ke ander vo IAM user hota hai. Is user ko hum khud se permission dete hain.

Best practice yehi hoti hai ki tum root user ko use mat karo, simple ek IAM user banao, usko permissions do aur usko use karo. Kyuki root user ke paas sabhi access hote hain agar kisi ko root user ke credential pta lag gaye to vo tumhare aws account ko hack kar sakta hai.


Root user par koi bhi IAM policy apply nahi ho sakti aur na hi isko permissions ko contorl kar sakte hain.

IAM user par policy apply hoti hain aur isko permissions ko control bhi kiya ja sakta hai.

Root user ko day-to-day operations ke liye use karna sahi nhi hota hai. 

IAM user ko day-to-day operations ke liye use kar sakte hain.

<br>

**IAM User ke components**:

Jab tum ek IAM user create karte ho, to uske paas yeh cheezein hoti hain:

1 - Username:
- Unique naam.
- Example: ```puneet-dev```, ```backend-user```.

<br>

2 - Authentication (Login ka tarika):

IAM User login kar sakta hai 2 tarike se:

a) Console Access:
- Username + Password.
- AWS dashboard login.

(b) Programmatic Access:
- Access Key ID + Secret Key.
- CLI, SDK, scripts use karte hain.

<br>

3 - Permissions:

Permissions create hoti hai, jaise User kya kar sakta hai aur kya nahi.

Policies ke through user ko permissions provide ki jati hain. User ko policy attach ki jati hai jisse user ko vo permissions mil jati hain.

Example:
- EC2 start/stop kar sakta hai.
- S3 bucket read kar sakta hai.
- Delete nahi kar sakta.

<br>

**Example 1: Developer IAM User**:

Maan lo tum ek DevOps engineer ho.

Tum ek IAM user create karte ho:
- Username: ```dev-user```.
- Access: CLI + Console.
- Permission: EC2 full access.

Iska matlab:
- EC2 instances bana sakta hai.
- Start/stop kar sakta hai.
- Billing access nahi hai.

<br>

**Example 2: Read-only User**:

Ek manager ko sirf monitoring karni hai:
- Username: ```manager-user```.
- Permission: ReadOnlyAccess.

Iska matlab:
- Sab dekh sakta hai.
- Kuch change nahi kar sakta.

<br>

**Example 3: Application IAM User**:

Maan lo tumhari app S3 se images fetch karti hai.

To tum IAM user banaoge:
- Username: app-user.
- Programmatic access only.
- Permission: S3 read access.

Ab app secure tarike se AWS se baat karegi.

<br>
<br>

### Groups

AWS IAM mein group ek logical container hota hai jisme multiple users ko ek saath manage kiya jata hai. manage karne ka matlab users ki permissions ko ek saath manage karna.

Simple words mein:
- Group = users ka collection jinke permissions same hote hain.

<br>

**Real-Life Analogy**:

Socho tumhari ek company hai:
- Kuch log Developers hain.
- Kuch log Testers hain.
- Kuch log Admins hain.

Ab agar tum har ek user ko alag-alag permission doge to bahut messy ho jayega.

Isliye tum groups banate ho, jaise:
- ```Developers Group```.
- ```Testers Group```.
- ```Admins Group```.

Aur har group ko ek specific permission de dete ho.

In groups mein tum users ko add karte ho, jisse sabhi users ko same type ki permissions mil jati hain.

<br>

**IAM Group ka kaam kya hai?**

IAM Group ka main purpose:
- Permission management easy banana: Har user ko alag-alag permissions na deke sabhi ki group mein ek saath permission dena.
- Centralized access control: 
- Scalability improve karna: Group mein jitne bhi users add karna.

Ek baar group pe policy laga do → sab users automatically same access le lenge.

<br>

**Example (Practical Scenario 💻)**:

Scenario:

Tum ek DevOps engineer ho.

Company mein 3 developers hain:
- Rahul
- Amit
- Neha

Step 1: Group create karo:
```
Group Name: Developers
```

Step 2: Policy attach karo:

Example policy:
- EC2 start/stop kar sakte hain.
- S3 read access.

Policy Example:
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:StartInstances",
        "ec2:StopInstances",
        "s3:GetObject"
      ],
      "Resource": "*"
    }
  ]
}
```
Ye policy agar group pe attach hogi, to sab users ko ye permissions mil jayengi.

Step 3: Users add karo:
```
Rahul → Developers
Amit → Developers
Neha → Developers
```

Result:
- Teenon ko same permissions mil gayi.
- Alag-alag configure karne ki zarurat nahi.

<br>
<br>

### Policies (IAM Policies)

IAM Policy ek JSON document hota hai jiske ander access permissions ko json form mein likha jata hai.

Matlab kaun user, kya kya action kar sakta hai aur kis resource par kar sakta hai.

Matlab:
- Kaun? → User / Role / Group.
- Kya? → Action (like S3 read, EC2 start).
- Kis par? → Resource (bucket, instance, etc.).
- Kab allow ya deny? → Conditions.

Matlab AWS jab bhi koi request receive karta hai (jaise kisi user ne S3 se file read karni hai), toh AWS backend mein check karta hai:
- “Kya is user ke paas is action ke liye permission hai ya nahi?”

To is IAM policy ke basis par dekhata hai ki permission hai ya nhi.

<br>

**IAM Policy ka Structure (JSON)**:

Ek basic IAM Policy kuch aisi dikhti hai:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-bucket/*"
    }
  ]
}
```

OR

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ReadOnlyAccess",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::my-bucket",
        "arn:aws:s3:::my-bucket/*"
      ],
      "Condition": {
        "IpAddress": {
          "aws:SourceIp": "192.168.1.1/32"
        }
      }
    }
  ]
}
```

Ab isko line-by-line breakdown karte hain 👇

**1 - Version**:

Yeh policy language ka version hai. AWS ne ab tak sirf ek stable version use kiya hai: "2012-10-17".

Yeh backward compatibility ke liye hai — AWS future mein syntax change kare toh yeh version help karega.


**2 - Statement**:

Ye ek array hota hai jiske ander rules likhe jaate hain. Iske ander multiple rules bhi sakte hain.


**3 - Sid (Statement ID)**:

- Optional hota hai.
- Debugging aur readability ke liye use hota hai.


**Effect (Decision)**:

```
"Effect": "Allow"
```

Do hi options:
- Allow
- Deny

Iska matlab hai ki kya rule ko allow ya deny karna hai?

**Action (What you can do)**:

Yeh define karta hai ki kaunsa operation allowed hai.

```
"Action": "s3:GetObject"
```

Wildcard use:
```
"s3:*"
```
All S3 actions allowed.


**Resource (Kahan apply hoga)**:

Yeh define karta hai kis particular resource par action allowed hai rule.

Matlab:
- AWS ke andar har resource (S3 bucket, EC2 instance, IAM role, etc.) ka ek unique “address” hota hai — usi ko ARN bolte hain.


Jaise Azure mein resource id hoti hai resource ko identify karne ke liye vaise ki aws mein ARN hota resource ko identify karne ke liye.

**ARN**:

ARN: Amazon Resource Name.

Ye ek unique identifier hota hai jo AWS ke har resource ko globally identify karta hai.

Format:
```
arn:partition:service:region:account-id:resource
```

Example:
```
arn:aws:s3:::my-bucket/file.txt
```

Explanation:

1. arn (prefix):
- Har ARN arn se start hota hai.
- Yeh batata hai ki yeh Amazon Resource Name hai.

2. partition:

AWS ka environment batata hai.

Common values:
- ```aws``` → normal AWS (most common)
- ```aws-cn``` → China region
- ```aws-us-gov``` → GovCloud

Insight:
- Yeh AWS ke internal isolation boundaries ko represent karta hai.

3. service:

Ye batata hai ki kaunsa AWS service hai.

Examples:
- s3.
- ec2.
- lambda.
- iam.

4. region:

Ye batata hai ki resource kis region mein hai.

Examples:
- ```us-east-1```.
- ```ap-south-1```.

⚠️ Important:
- S3 aur IAM jaise kuch services global hoti hain → region blank ho sakta hai.

5. account-id:
- AWS account ka unique ID.

Example:
```
123456789012
```
- Yeh batata hai resource kis account ka hai.

6. resource:
- Ye vo particular resource hota hai.

<br>

Different ARN Formats:

1. Slash format:
```
arn:aws:ec2:ap-south-1:123456789012:instance/i-1234567890abcdef
```
EC2 instance ke liye

2. Colon format
```
arn:aws:lambda:ap-south-1:123456789012:function:my-function
```

3. Mixed format
```
arn:aws:iam::123456789012:role/MyRole
```
IAM roles ke liye

<br>
<br>

### Roles (IAM Roles)
