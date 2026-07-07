# Accessing AWS

### AWS Khud Kya Hai?

AWS duniya bhar mein bahut saare data centers aur servers ka collection hai. Jab tum AWS account banate ho, tab tumhare computer mein EC2, S3 ya VPC install nahi hote. Ye sab AWS ke cloud infrastructure mein run karte hain.

Iska matlab jab bhi tum koi resource create karte ho, jaise EC2 instance, S3 bucket ya Lambda function, to tumhare laptop par kuch create nahi hota. Tumhara laptop sirf AWS ko request bhejta hai aur AWS apne cloud mein woh resource create karta hai.

Ab sawal aata hai: Tumhara laptop AWS ko command kaise bhejta hai?

Iska answer hai: **AWS API (Application Programming Interface)**.

<br>
<br>

### AWS Mein Har Kaam API Call Hota Hai

Chahe tum browser se button click karo.

Chahe CLI command chalao.

Chahe Python script likho.

Chahe Terraform use karo.

Chahe Jenkins pipeline chalaye.

Chahe GitHub Actions deployment kare.

End mein AWS ke paas API Call hi jaati hai.

AWS ke liye koi farq nahi padta request kahan se aayi hai.

Uske liye har request ek API Request hai.

AWS ke saath aap jo bhi kaam karte hain, peeche se woh hamesha ek API (Application Programming Interface) call hi hoti hai.

AWS backend par ek REST API request jaati hai jo resource ko create, delete ya modify karti hai.

<br>

**Example**:

Tum EC2 create karte ho. Browser mein button click kiya. Tumhe lagta hai EC2 Console ne instance bana diya.

Actually aisa nahi hua.

Background mein AWS Console ne EC2 Service ko API Call bheji.
```
Create Instance
```

AWS ne request receive ki. Authentication check hui. Permissions check hui. Aur EC2 create ho gaya.

Yaani tumne button click kiya, lekin backend mein API Call hui.

<br>
<br>

### AWS Access Karne Ke Major Ways

AWS ko access karne ke mainly teen popular methods hain.
- AWS Management Console
- AWS CLI
- AWS SDK

<br>

**1. AWS Management Console (Browser-Based Access)**:

Yeh AWS ka official web portal hai jahan aap kisi bhi browser (Chrome, Safari, Firefox) se login karke graphical interface (GUI) ke zariye kaam karte hain.

Aap browser mein ja kar username, password aur MFA daal kar login karte hain. Jab aap wahan kisi button par click karte hain (jaise "Create Bucket"), toh console peeche se AWS ki API ko call karta hai.

Kiske liye best hai:
- Beginners ke liye jo cloud seekh rahe hain.
- Visual dashboards dekhne aur quick checks karne ke liye (jaise billing check karna ya logs dekhna).

Drawback: 
- Isme automation nahi ho sakta. Agar aapko 50 servers banane hain, toh aap console par baith kar 50 baar click nahi kar sakte, isme bohot time kharab hoga.

<br>

**2. AWS CLI (Command Line Interface)**:

CLI ka matlab hota hai: Command Line Interface.

Isme browser ki jagah tum terminal use karte ho.

Yeh ek open-source tool hai jise aap apne local computer (Windows, Mac, Linux) ke terminal ya command prompt par install karte hain.

Isme login karne ke liye password nahi, balki aapke IAM User ki Access Key ID aur Secret Access Key ki zaroorat hoti hai. Ek baar setup (aws configure) hone ke baad, aap seedhe commands likhkar AWS ko control karte hain.

**Example**:

Tum command likhte ho:
```
aws ec2 describe-instances
```
Ye command AWS CLI ko di gayi. CLI ne us command ko API Request mein convert kiya aur EC2 API ko request bheji.

EC2 ne response diya. CLI ne output terminal mein print kar diya.

<br>

**3. AWS SDK (Software Development Kit)**:

Ab maan lo tum ek application bana rahe ho.

Application ke andar button hai: "Upload File"

Jab user click kare. Automatically S3 bucket mein upload ho jaye. Kya user CLI command likhega?

Nahi.

Application khud AWS se baat karegi. Yahan AWS SDK use hota hai.

<br>

**SDK Kya Hai?**

SDK ek programming library hoti hai.

AWS har popular programming language ke liye SDK provide karta hai.

Jaise:
- Python (Boto3)
- Java
- JavaScript
- .NET
- Go
- PHP
- Ruby

Aap isko apne code mein library ki tarah import karte hain. Isme bhi authentication ke liye Access Keys ya IAM Roles ka use hota hai. Aapka application code seedhe AWS API endpoints par secure requests bhejta hai.

Kiske liye best hai:
- Developers ke liye jo cloud-native applications bana rahe hain.
- Example: Ek aisi website jahan jab koi user apni profile picture upload kare, toh aapka backend code use automatic AWS S3 bucket mein save kar de.
