# AWS Overview

<br>

### What is Cloud Computing?

Cloud Computing is the on demand delivery of computing resources, such as Storage, Servers, Networking, Databases, Software, Analytics and Intelligence, etc, over the internet.

<br>
<br>

### What is Cloud?

_Accessing a Data Center remotely is called cloud._

<br>
<br>

### Why do we need cloud?

There are multiple reasons to choose cloud:

- **TO save cost**:
  - If you want to run a website, instead of buying and maintaining your own server (which can be expensive and require dedicated IT staff), you can host your website on a cloud service and pay only for what you use.
 
- **For Scalability and Flexibility**:
  - Imagine you run an online store that suddenly becomes very popular during a holiday sale. With traditional servers, you’d need to buy extra hardware to handle the increased traffic. With cloud computing, you can quickly scale up your resources (more servers, more storage) to handle the surge and scale them down when the traffic drops.

We know that if we have an application we need a server to expose that application to outside world.

- **What is Server?**

  Server is a remote computer which has Local Storage, CPU and RAM. These servers are used to run application.

So, 10 or 15 years back companies like Google, Yahoo procure the servers from vendors like IBM, HP, etc. These companies used to procure 100's of servers from vendors. To manage these servers companies had particular System Administrators which mangage these server. 

After few years back companies have their own servers. Procuring the servers from vendors used to seems expensive. So companies decided get their own servers.

- Example of Google:

  Google installed 100's or 1000's server inside the racks in its server rooms. These servers are connected through High Speed cables, Switches or Routers. This complete setup is called **_Data Center_**.

<br>
<br>

### What is AWS

Amazon Web Services (AWS) ek cloud computing platform hai jo Amazon ne banaya hai.

AWS is a cloud service provider which provides on demand delivery of computing resources. 

Simple language mein:

AWS ek aisa platform hai jahan tum bina apna physical server kharide, internet ke through servers, storage, database, networking sab kuch use kar sakte ho.

AWS ek On-demand Cloud Platform hai. Iska matlab hai "Pay-as-you-go". Jo bhi resource tumko use karna hai uske paise do aur use karo, Jitna use karoge, sirf utne ka paisa dena hai.

Tumko ek application ko run karne ke liye kya-kya chaiye, Compute resource, storage aur networking. Amanzon ne ek platform banaya tha AWS, Ye AWS on-demand resources ya services provide karta hai, jisko tum use karte ho aur apne application host karte ho.

Pehle companies ko apna software chalane ke liye khud ke servers kharidne padte the, AC wala kamra banana padta tha, aur bijli ka bill bharna padta tha. AWS ne bola, "Ye sab tension tum mat lo, ye sab mere paas hai. Tum bas apna code lao aur mere servers rent pe use karo."

<br>
<br>

### On-Premise vs Cloud

<br>

**On-Premises**:

On-premises ka matlab hai khud ka data center.

Pehle ke time par jab cloud ka concept nahi tha to companies apn application run karne ke liye, khud ka data centre banati thi, aur us data centre ko maintain karna padta tha. Ye on-premises aaj bhi use hota hai jab company ko khud ka data store karna ho.

On-premises ka matlab hai ki tumhari company ke paas apna khud ka physical data center hai. Servers, wires, AC, security guards—sab kuch tumhari building ke andar hai.

Cooling, electricity, maintenance sab tumhari zimmedari.

- Paisa (Capital Expense - CapEx): Tumhe shuruat mein hi karodon rupaye kharch karne padenge servers kharidne ke liye. Chahe tumhara business chale ya na chale, paisa lag gaya.
- Maintenance: Agar koi hard drive crash hui, toh tumhara banda jaake usse badlega. Bijli ka bill, cooling ka kharcha, aur security—sab tumhara sar-dard.
- Control: Iska ek hi fayda hai—Full Control. Tumhara data tumhari aankhon ke samne hai. Banks aur government defense projects aksar isi wajah se on-premises prefer karte hain.

<br>

**Cloud**:

Cloud ka matlab hai remote data center ko internet par access karna.

Cloud ka matlab hai ki tum cloud provider se rent par service kharidte ho aur unko use karte ho.

Cloud ka matlab hai "Someone else's computer." Tum Amazon, Microsoft, ya Google ke servers ko internet ke zariye rent pe use kar rahe ho.

- Paisa (Operational Expense - OpEx): Shuruat mein 0 rupaye kharch hote hain. Jitne ghante server chalaya, utne ka bill bhara. Isse Pay-as-you-go model kehte hain.
- Maintenance: Server ki physical security, bijli, aur hardware repair ki tension AWS/Azure ki hai. Tumhe bas apna application chalane se matlab hai.


<br>
<br>

### AWS Ki Main Services (The Big Four)

AWS mein 200 se zyada services hain, lekin agar tumne ye 4 samajh liye, toh tumne 80% game jeet liya:

**1 - EC2 (Elastic Compute Cloud) – "Kiraye ka Computer"**:

Ye AWS ki sabse popular service hai. Ye basically ek virtual machine (VM) hai. Tum decide karte ho ki tumhe kitni RAM chahiye, kitna CPU chahiye, aur kaunsa OS (Windows ya Linux).

Example: Tumhe ek Python script 24/7 chalani hai. Tum apne laptop pe nahi chala sakte kyunki laptop band ho jayega. Tumne AWS pe ek EC2 instance banaya aur script wahan daal di. Ab wo duniya ke kisi bhi kone se access ho sakti hai.

<br>

**2 - S3 (Simple Storage Service) – "Unlimited Google Drive"**:

S3 ek storage service hai jahan tum kitna bhi data (photos, videos, logs) daal sakte ho. Ye kabhi full nahi hota.

Example: Netflix apni saari movies aur thumbnails S3 mein store karta hai. Jab tum "Stranger Things" click karte ho, toh wo file S3 se fetch hoti hai.

<br>

**3 - RDS (Relational Database Service) – "Managed Database"**:

Database setup karna sar-dard hai (backups, updates, security). RDS ye sab khud handle karta hai. Tum bas MySQL, PostgreSQL, ya Oracle select karo aur kaam shuru karo.

Example: Ek e-commerce site ka user data, orders, aur payments table format mein store karne ke liye RDS best hai.

<br>

**4 - Lambda – "Serverless Magic"**:

Ye DevOps engineers ka favourite hai. Isme tumhe server manage hi nahi karna. Tum bas code likho, aur AWS usko tabhi chalayega jab zaroorat hogi.

Example: Jaise hi koi user apni profile photo upload kare, uska size chota (thumbnail) ho jaye. Is kaam ke liye 24 ghante server chalane ki zaroorat nahi hai. Lambda sirf us 2-second ke liye chalega aur band ho jayega. Paise bhi sirf un 2 seconds ke lagenge.

<br>

**5 - VPC**:

VPC (Virtual Private Cloud) AWS ke andar tumhara apna private network hota hai.

VPC ka use AWS mein ek secure aur private network banane ke liye hota hai jahan tum apni application ko safely aur controlled way mein run karte ho.
