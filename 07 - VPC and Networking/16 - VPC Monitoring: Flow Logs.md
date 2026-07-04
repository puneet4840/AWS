# VPC Monitoring: Flow Logs

VPC Flow Logs ek logging feature hai jo VPC mein aane wale aur VPC se bahar jane wale network traffic ko capture karta hai.

Matlab vpc mein konsa traffic aaya aur konsa traffic bahar gya isko store karta hai.

Dhyan dene wali baat.

Flow Logs actual packet ka data record nahi karte. Ye sirf packet ki information record karte hain.

Jaise:
- Source IP
- Destination IP
- Source Port
- Destination Port
- Protocol
- Packet Accept hua ya Reject
- Kitne bytes transfer hue

Flow Logs data packet ke ander ka data nhi capture karta sirf data packet ki information jisko metadata kehte hain usko record karta hai.

<br>
<br>

### Sabse pehle ek problem samajhte hain

Maan lo tum ek DevOps Engineer ho.

Tumhari company ka production application AWS mein chal raha hai.

Architecture kuch aisa hai:
```
Internet

↓

Application Load Balancer

↓

EC2 Instance

↓

Database
```

Sab kuch normally chal raha tha.

Ek din client call karta hai "Website open nahi ho rahi."

Ab tum investigation shuru karte ho.

<br>

**Tum kya-kya check karoge?**

Sabse pehle tum EC2 check karoge. EC2 Running hai. Fir Security Group check karoge. Security Group bhi sahi hai.

Fir NACL check karoge. Wo bhi sahi hai. Fir Route Table check karoge. Wo bhi sahi hai.

Ab tumhare paas ek bahut bada question hai.

"**Actual mein network mein ho kya raha hai?**"

Kya packet EC2 tak pahunch raha hai?

Ya beech mein hi block ho raha hai?

Ya Security Group ne block kiya?

Ya NACL ne block kiya?

Tumhe kuch pata hi nahi chal raha.

<br>

**AWS ne kya socha?**

AWS Engineers ne socha.

Customers ko ye pata hi nahi chalta ki network mein packet ke saath kya hua.

To kyun na ek logging system bana diya jaye.

Jo har network flow ka record rakh sake.

Isi service ka naam hai **VPC Flow Logs**.

<br>

VPC flow logs network traffic ka metadata store karta hai, data packet ke ander ka data store nhi karta.

<br>
<br>

### Flow Logs packet ke ander ke data ko store karte hain?

Answer.

**Nahi**.

Flow Logs packet ka content record nahi karte. Ye packet ke andar ka data save nahi karte.

**Example**:

Suppose packet ke andar likha hai.
```
Username=Puneet

Password=123456
```

Flow Logs kabhi bhi ye data record nahi karenge.

Ye sirf itna record karenge:
```
Source IP: 10.0.1.10

Destination IP: 10.0.2.20

Protocol: TCP

Port: 443

Action: ACCEPT
```

Yaani Metadata. Content nahi.

<br>
<br>

### Flow Logs kis level par enable hote hain?

AWS mein tum Flow Logs teen jagah enable kar sakte ho.

**1. VPC Level**:

Agar tum VPC Level par Flow Logs enable karte ho. To us VPC ke andar jitne bhi Subnets aur ENIs hain. Sabka traffic record hoga.

<br>

**2. Subnet Level**:

Agar tum sirf ek Subnet monitor karna chahte ho. To sirf usi Subnet par Flow Log enable kar sakte ho.

<br>

**3. Network Interface (ENI) Level**:

Agar tum sirf ek EC2 monitor karna chahte ho.

To us EC2 ke Network Interface par Flow Logs enable kar sakte ho.

Ye sabse granular level hai.

<br>
<br>

### Flow Logs Ka Data Kahan Save Hota Hai?

Flow Logs in logs ko apne paas permanently save nahi rakhta, aapko chunna hota hai ki aap is data ko kahan bhejna chahte hain:

**Amazon CloudWatch Logs**: Agar aapko live network par nazar rakhni hai aur kisi specific IP ke aate hi alert (alarm) bajana hai, toh data CloudWatch mein bhejein.

**Amazon S3 Bucke**t: Agar aapko kafi mahino ya saalon ka data store karke rakhna hai (compliance/audit ke liye), toh S3 bucket best aur sasta option hai.

**Kinesis Data Firehose**: Agar aapko is data ko kisi third-party security software (jaise Splunk ya Datadog) par real-time analysis ke liye bhejna hai

<br>
<br>

### Ek Flow Log Record Mein Kya-Kya Details Hoti Hain?

Suppose tumhare VPC mein ek EC2 Instance hai.

Ek client us EC2 par SSH karne ki koshish karta hai.

AWS Flow Logs mein ek record generate hota hai:
```
version      2
account-id   123456789012
interface-id eni-08ab123456789
srcaddr      203.0.113.10
dstaddr      10.0.1.25
srcport      52000
dstport      22
protocol     6
packets      15
bytes        1200
start        1718001000
end          1718001015
action       ACCEPT
log-status   OK
```

Ab sab chiz ek-ek karke samjhte hain:

**1. Version**:

Example
```
version = 2
```

Version batata hai ki Flow Log kis format mein generate hua hai. AWS time-to-time Flow Logs ka format improve karta rehta hai. Har naye version mein naye fields add ho sakte hain. Isliye Version field batati hai ki AWS ne kis format mein ye log generate kiya hai. Real life mein is field ko troubleshooting ke time normally ignore kar diya jata hai. Lekin AWS ko ye pata hota hai ki is log ko kaise interpret karna hai.

<br>

**2. Account ID**:

Example
```
account-id = 123456789012
```

Ye batata hai ki ye Flow Log kis AWS Account ka hai. Maan lo ek company ke paas 20 AWS Accounts hain.

Agar saare Logs ek hi S3 Bucket mein aa rahe hain. To Account ID dekh kar turant pata chal jayega ki ye log kis AWS Account se generate hua hai.

<br>

**3. Interface ID**:

Example
```
interface-id = eni-08ab123456789
```

Ye batata hai ki ye traffic kis Elastic Network Interface (ENI) se pass hui. Yahan ek important baat samajho. Flow Logs directly EC2 ko monitor nahi karte. Flow Logs ENI ko monitor karte hain. Kyuki EC2 network par directly communicate nahi karta. EC2 ke paas ek Network Interface hota hai. Aur wahi Network Interface packets receive aur send karta hai. Isliye AWS ENI ka ID record karta hai.

<br>

**4. Source Address (srcaddr)**:

Example
```
srcaddr = 203.0.113.10
```

Iska matlab hai ki ye packet kis IP Address se aaya.

Wo batata hai.

Maan lo.

Tum apne Laptop se SSH kar rahe ho. Tumhara Public IP hai.
```
203.0.113.10
```
To Flow Log isi IP ko Source Address ke roop mein store karega.

Yaani.

Packet kisne bheja. Ye field batati hai.

<br>

**5. Destination Address (dstaddr)**:

Example
```
dstaddr = 10.0.1.25
```
Ye batata hai ki packet kis IP Address par ja raha tha.

Suppose.

Tumhare EC2 ka Private IP hai.
```
10.0.1.25
```
To Flow Log Destination Address mein isi IP ko store karega.

Yaani.

Packet kis machine tak pahunchna chahta tha.

<br>

**6. Source Port (srcport)**:

Example.
```
52000
```

Source Port ka matlab. Client ne kaunsa temporary port use kiya.

Maan lo.

Tum browser se Website open karte ho.

Browser automatically ek random port choose karta hai, Jaise.
```
52000
```
Ye random port har request ke saath change hota rehta hai. Isliye Source Port normally high number hota hai.

<br>

**7. Destination Port (dstport)**:

Example.
```
22
```
Ye sabse useful field hai.

Ye batata hai.

Packet kis service ke liye tha. Matlab packet konse destination port par ja rha tha.

Example.
```
22
```
Matlab SSH.
```
80
```
Matlab HTTP.
```
443
```
Matlab HTTPS.

<br>

**8. Protocol**:

Example.
```
protocol = 6
```
Yahan AWS Number store karta hai. Protocol Name nahi.

Example.
```
6
```
Matlab TCP.
```
17
```
Matlab UDP.
```
1
```
Matlab ICMP (Ping).

<br>

**9. Packets**:

Example.
```
packets = 15
```
Ye batata hai. Is connection ke andar total kitne packets transfer hue.

Suppose.

Browser ne Website open ki.

HTML aaya, CSS aayi, Images aayi, JavaScript aayi.

Ye sab multiple packets mein transfer hota hai. Flow Log total packet count bata deta hai.

<br>

**10. Bytes**:

Example.
```
bytes = 1200
```
Ye batata hai. Total kitna data transfer hua.

Example.
```
1200 Bytes.
```
Agar tum dekhte ho.
```
bytes = 1000000000
```
To samajh jaoge. Bahut bada data transfer hua. Is field ko Network Monitoring aur Cost Analysis mein bahut use kiya jata hai.

<br>

**11. Start Time**:

Example.
```
start = 1718001000
```
Ye Unix Timestamp hota hai. Ye batata hai. Traffic kab start hui.

<br>

**12. End Time**

Example.
```
end = 1718001015
```
Ye batata hai Traffic kab end hui.

Ab Start aur End compare karke. Tum connection duration calculate kar sakte ho.

<br>

**13. Action**:

Ye sabse important field hai.

Example.
```
action = ACCEPT
```
Matlab Traffic allow ho gayi.

Example.
```
action = REJECT
```
Matlab Traffic block ho gayi.

Ab sawal.

Kisne block ki? Ho sakta hai.
- Security Group
- NACL
- Kisi aur network filtering ki wajah se

Flow Log sirf ye batata hai ki traffic accept hui ya reject. Exact blocking component identify karne ke liye tumhe Security Groups, NACLs aur routing configuration bhi check karni padti hai.

**Real Example**

Developer bolta hai "Mera Database connect nahi ho raha."

Tum Flow Log dekhte ho.
```
Destination Port: 3306

Action: REJECT
```
Ab tumhe turant samajh aa gaya. Database ka traffic block ho raha hai. Ab tum Security Group ya NACL investigate karoge.

<br>
<br>

### Ab ek poora Record samajhte hain

Suppose.

Flow Log kuch generate hua.
```
Source IP      : 203.0.113.10

Destination IP : 10.0.1.25

Source Port    : 52000

Destination    : 22

Protocol       : TCP

Packets        : 15

Bytes          : 1200

Action         : ACCEPT
```

Ek Laptop jiska Public IP **203.0.113.10** tha, usne EC2 Instance ke Private **IP 10.0.1.25** par **SSH (Port 22)** connection establish karne ki koshish ki. Laptop ne apna temporary source port **52000** use kiya. Communication **TCP** Protocol ke through hui. Is connection mein total **15** packets exchange hue aur lagbhag **1200 bytes** data transfer hua. Security checks pass ho gaye, isliye AWS ne is traffic ko **ACCEPT** kar diya aur connection successfully establish ho gaya.

<br>
<br>

### Real-World Troubleshooting Examples

**Example A: Server Connect Nahi Ho Raha (Finding Network Blocks)**:

Maan lijiye aapne ek naya Web Server lagaya, lekin internet se koi bhi us website ko open nahi kar pa raha hai. Aapne VPC Flow Logs check kiye aur wahan aapko dikha:```... 192.168.1.100 (User) -> 10.0.1.50 (Web Server) Port:80 ... REJECT```.

Pata Kaise Chala? Logs mein action REJECT dikha raha hai. Iska matlab aapke Security Group ya NACL mein Port 80 (HTTP) ka rasta open karna aap bhool gaye hain. Aap use allow karenge aur problem solve ho jayegi.

<br>

**Example B: Security Attack Ka Pata Lagana (Malicious Activity)**:

Achanak aapka database server slow ho jata hai. Aap Flow Logs check karte hain aur dekhte hain ki Russia ya kisi anjaan desh ki IP se har ek second mein hazaron baar Port 22 (SSH) par enter karne ki koshish ho rahi hai aur har baar status REJECT aa raha hai.

Pata Kaise Chala? Isko network language mein Brute Force Attack kehte hain, jahan hacker aapka password guess karne ki koshish kar raha hai. Aap turant us hacker ki IP range ko uthayenge aur apne NACL (Network Access Control List) mein jaakar use DENY (block) kar denge taaki woh aapke subnet ke gate tak bhi na aa sake.

<br>
<br>

### VPC Flow Logs Ki Best Practices

**NAT Gateway Cost Control**: Agar aapka NAT Gateway ka bill bohot zyada aa raha hai, toh Flow Logs chalu karke check karein ki kaunsa private EC2 instance internet se sabse zyada bytes ka data download kar raha hai.

**S3 Lifecycle Rules**: Flow logs ka data bohot tezi se badhta hai aur hazaron GBs tak pahunch sakta hai. Isliye agar aap S3 use kar rahe hain, toh ek rule laga dein ki 30 din purane logs automatically delete ho jayein ya Glacier (saste storage) mein chale jayein, taaki bill control mein rahe.

**AWS Athena for Analysis**: Agar S3 mein bohot saara logs data jama ho gaya hai, toh aap Amazon Athena service ka use karke usme SQL queries chala sakte hain (jaise: SELECT * WHERE action='REJECT'), jisse bade data mein se kaam ki cheez nikalna bohot aasan ho jata hai.
