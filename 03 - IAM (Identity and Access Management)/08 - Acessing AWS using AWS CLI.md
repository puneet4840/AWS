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
- Aap ek IAM Role banate hain aur use EC2 instance par attach kar dete hain.
- Piche AWS ka Instance Metadata Service (IMDSv2) kaam karta hai. AWS CLI automatically is metadata service se temporary, short-lived credentials (jo har thodi der mein badalte rehte hain) utha leta hai. Aapko koi key configure karne ki zaroorat nahi padti.

