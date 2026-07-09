# Accessing AWS using AWS CLI

AWS CLI (Command Line Interface) ka use karke AWS ko access karna ek bahut hi powerful tarika hai. Isse aap apne terminal ya command prompt se direct commands likhkar AWS resources ko manage kar sakte hain.

<br>

###  AWS CLI Kya Hai Aur Kyun Use Karte Hain?

Jab aap AWS Console (GUI) use karte hain, toh aapko har kaam ke liye click karna padta hai. Agar aapko 50 S3 buckets banane hon, toh click karte-karte bahut time waste hoga.

AWS CLI ka use automation aur scripting ke liye kiya jata hai, agar koi task apko automate karna hai to aap aws cli ka use karke kar sakte hain.

Aap ek line ka script likhkar seconds mein saara kaam automatic kar sakte hain.


Jab hum cloud engineer bante hain, toh humara maximum kaam automation par dependent hota hai.
- **AWS Management Console (GUI)**: Yeh seekhne ke liye bohot accha hai. Lekin isme human error ke chances bohot hote hain. Agar aapko 10 alag-alag regions mein ja kar ek hi setting change karni ho, toh aapko 10 baar click karna padega, jo bohot time-consuming hai.
- **AWS CLI**: Yeh ek unified tool hai. Iska matlab hai ki sirf ek single software download karke aap AWS ki har ek service (S3, EC2, IAM, Lambda, RDS, VPC) ko control kar sakte hain. Yeh scripts ke andar chal sakta hai, isko schedule kiya ja sakta hai (Cron jobs ke zariye), aur yeh repeatable hota hai.

Mainly AWS CLI automation aur scripting ke liye use kiya jata hai.

<br>
<br>

### Step 1: AWS CLI Ko Install Karna

AWS CLI ke do major versions hain: Version 1 aur Version 2. Aaj ke samay mein humesha AWS CLI Version 2 hi use karna chahiye kyunki isme auto-prompting, wizard features, aur better authentication support milta hai.

Sabse pehle aapko apne computer par AWS CLI software install karna hoga.
- **Windows**: AWS ki website se .msi installer download karke install karein.
- **Mac** (using Homebrew): Terminal mein run karein: brew install awscli.
```
brew install awscli
```

- **Linux**: Apne package manager (apt/yum) se ya direct curl command se install karein.
```
curl "https://amazonaws.com" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

**Installation Check Kaise Karein?**:
```
aws --version
```
Agar screen par version number (jaise ```aws-cli/2.x.x```) dikhe, toh installation successful hai.

<br>
<br>

### Step 2: Credentials Generate Karna (IAM)

AWS CLI ko chalane ke liye aapko AWS ko batana padega ki aap kaun hain. Iske liye main roop se do models use hote hain:

**Model A: Access Keys (For Local Laptops/Desktops)**:

Agar aap apne local laptop ya computer par AWS CLI ko use karna chte hain to apko Access Keys wala method use karna hai. Ye method apko sirf tabhi use karna hai jab aap apne local laptop mein aws cli use kar rahe ho.

Ab aapko apne computer ke terminal ko batana hai ki aap kaun se AWS account aur keys ka use karna chahte hain.

AWS CLI aapke computer se AWS cloud par requests bhejta hai. Un requests ko authenticate karne ke liye aapko Access Keys ki zaroorat hoti hai.
- AWS Console mein **IAM (Identity and Access Management)** par jayein.
- Apne **User** par click karein aur **Security Credentials** tab mein jayein.
- **Access Keys** section mein ja kar **Create Access Key** par click karein.
- Aapko do cheezein milengi:
  - **Access Key ID**: Yeh aapka username jaisa hota hai.
  - **Secret Access Key**: Yeh aapka password jaisa hota hai (isko safe rakhna, kisi ko dikhana mat).
 
Apko Access Key ID aur Secret Access Key niche di hui ```aws configure``` command mein deni hoti hai.


<br>

**Model B: IAM Roles (For AWS Resources like EC2)**:

Agar aapka code ya script kisi AWS ke server (EC2 instance) ke andar chal raha hai, toh wahan Access Keys rakhna sakht mana hai. Agar wo server hack hua, toh aapki keys leak ho jayengi.

Isliye is jab aap kisi AWS resource jaise EC2 instance mein AWS CLI ka use kar rahe ho to IAM Role ka use karo, AWS CLI ko authenticate karne ke liye.

Solution: 
- Aap ek **IAM Role** banate hain aur use EC2 instance par attach kar dete hain.
- Piche AWS ka **Instance Metadata Service (IMDSv2)** kaam karta hai. AWS CLI automatically is metadata service se temporary, short-lived credentials (jo har thodi der mein badalte rehte hain) utha leta hai. Aapko koi key configure karne ki zaroorat nahi padti.

<br>
<br>

### Step 3: AWS CLI Ko Configure Karna (Access Key method)

Agar aapne IAM User ke liye Access Keys create kar li hain to ye step follow karna hai.

Ab aapko apne computer ke terminal ko batana hai ki aap kaun se AWS account aur keys ka use karna chahte hain.

Terminal mein yeh command chalayein:
```
aws configure
```

Yeh aapse chaar cheezein poochega, aapko unhe fill karna hai:
- **AWS Access Key ID**: (Apni access key paste karein).
- **AWS Secret Access Key**: (Apni secret key paste karein).
- **Default region name**: (Jis region mein kaam karna hai, jaise Mumbai ke liye ```ap-south-1``` ya N. Virginia ke liye ```us-east-1```).
- **Default output format**: (Hamesha ```json``` type karein).

Yeh saari details aapke computer ke home folder mein ek hidden folder ```.aws``` ke andar ```credentials``` aur ```config``` naam ki files mein save ho jati hain.

<br>
<br>

### Configuration & Multi-Profile Management

Jab aap terminal par ```aws configure``` chalate hain, toh piche kya hota hai?

Aapke operating system ke home directory mein ek hidden folder banta hai: ```~/.aws/``` (Linux/Mac) ya ```C:\Users\Username\.aws\``` (Windows). Iske andar do files hoti hain:
- **credentials file**: Isme aapki access keys secure save hoti hain.
- **config file:** Isme aapka default region (jaise ```ap-south-1```) aur output format (```json```, ```text```, ya ```table```) save hota hai.

<br>

**Advance Concept: Named Profiles (Multi-Account Setup)**:

Agar apko AWS CLI ke through sirf ek AWS Account mein kaam karna hai to aap normal kar sakte hain. Lekin agar apke paas multiple AWS Accounts hain, aur un accounts mein ek saath kaam karna hai to apko ek ke aws cli se logout karna hoga aur fir dusre account mein login karke aap aws cli ka use kar paoge.

Lekin AWS CLI ek feature deta hai **Named Profiles**.

Agar aap ek se zyada AWS accounts par kaam karte hain (jaise ek Production Account aur ek Testing Account), toh aap Named Profiles ka use karte hain.

Jab aap aws account configure karte hain to apko ```aws configure``` command mein ek flag ```--profile``` dena hai.

Aap configuration ke samay ```--profile``` flag lagate hain:
```
aws configure --profile testing
aws configure --profile production
```

Ab jab aapko testing account mein buckets dekhne hon, toh aap command aise chalayenge:
```
aws s3 ls --profile testing
```

Aur agar production mein kuch check karna ho:
```
aws s3 ls --profile production
```

Isse aapko baar-baar login-logout karne ki zaroorat nahi padti.

<br>
<br>

### Mastering AWS CLI Commands Structure

AWS CLI commands ka ek standard structure hota hai jise samajhna bohot zaroori hai:
```
aws <service_name> <operation_name> [parameters] [flags]
```

Aaiye is structure ke kuch advance aur real-world examples dekhte hain:

**1. EC2 Instance Create Karna (With Details)**:
```
aws ec2 run-instances \
    --image-id ami-0c55b159cbfafe1f0 \
    --count 1 \
    --instance-type t2.micro \
    --key-name my-laptop-key \
    --security-group-ids sg-0123456789abcdef0 \
    --subnet-id subnet-01234567
```
Yahan humne control plane ko command di ki is specific subnet aur security group ke andar ek t2.micro server launch karo.

<br>

**2. Advanced S3 Operations (Sync Command)**:

AWS CLI mein S3 ke liye do tarah ke commands hote hain: ```s3``` (high-level) aur ```s3api``` (low-level). ```s3 sync``` ek bohot hi powerful command hai jo backup ke liye use hoti hai.
```
aws s3 sync /home/user/my_project_folder s3://my-backup-bucket-2026/project
```
Yeh command aapke local folder aur S3 bucket ko compare karegi. Sirf wahi files upload hongi jo nayi hain ya jinme badlav hua hai. Yeh bilkul rsync jaisa kaam karta hai.

<br>
<br>

### Filtering and Querying Output (--query aur --output)

AWS CLI jab response deta hai, toh wo bohot bada JSON block hota hai. Pure JSON mein se sirf kaam ki information nikalne ke liye AWS CLI ke paas do powerful flags hain: ```--query``` (jo JMESPath query language ka use karta hai) aur ```--output```.

**Example: Mujhe sirf chal rahe (running) EC2 instances ki IDs aur Public IPs chahiye, baaki ka kachra JSON nahi chahiye.**
```
aws ec2 describe-instances \
    --query "Reservations[*].Instances[*].{ID:InstanceId,IP:PublicIpAddress}" \
    --output table
```

**Iska Fayda**: Screen par JSON ke bajaye ek saaf-suthra Text Table ban kar aayega jisme sirf Instance ID aur Public IP likha hoga. Is output ko aap aage kisi shell script mein variable ke andar store kar sakte hain.

<br>
<br>

### CLI Security Best Practices

**Least Privilege Principle**: CLI user ko kabhi bhi AdministratorAccess nahi dena chahiye. Agar aapka laptop chori ho gaya ya malware aa gaya, toh aapka poora AWS account khatre mein pad jayega. CLI user ko sirf utni hi permission dein jitni uske script ko zaroorat hai.

**MFA (Multi-Factor Authentication) with CLI**: Aap CLI par bhi MFA enforce kar sakte hain. Iske liye aapko aws sts get-session-token command ka use karke temporary credentials lene padte hain jab aap apna Google Authenticator ka 6-digit code enter karte hain.

**Automatic Key Rotation**: CLI ki Access Keys ko har 90 din mein badalna (rotate karna) industry ka standard rule hai.

