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
- Root User.
- IAM User.

Jab tum ek aws account create karte ho to by default ek root user create hoke milta hai jiske paas sabhi permission aur access hoti hain.

Lekin jab tum alag se user create karte ho IAM ke ander vo IAM user hota hai. Is user ko hum khud se permission dete hain.

Best practice yehi hoti hai ki tum root user ko use mat karo, simple ek IAM user banao, usko permissions do aur usko use karo. Kyuki root user ke paas sabhi access hote hain agar kisi ko root user ke credential pta lag gaye to vo tumhare aws account ko hack kar sakta hai.

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

