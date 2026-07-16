# Auto Scaling Group Overview

Auto Scaling Group (ASG) AWS ki ek service hai jo EC2 instances ke ek logical group ko manage karti hai. Iska kaam ye ensure karna hota hai ki application ke liye required number of EC2 instances hamesha available rahein aur workload ke according automatically increase ya decrease hote rahein.

Auto-Scaling group AWS ki ek service hai, jo EC2 instances ko ek group mein run karti hai. Ye EC2 instances ko load badhne par scale up karti hai aur load kam hone par scale down karti hai.

<br>

AWS Auto Scaling Group ek aisi capability hai jo aapke application ke load ke hisab se EC2 instances ki counting ko automatically kam ya jyada karti hai. Agar aapke website par traffic achanak badh jata hai, toh ASG khud se naye servers launch kar deta hai, aur jab traffic kam ho jata hai, toh yeh extra servers ko terminate (delete) kar deta hai. Isse aapko manually servers ko manage nahi karna padta aur aapka application hamesha running state mein rehta hai

ASG bahut saare kaam karta hai.

Jaise:
- Naye EC2 launch karna.
- Unhealthy EC2 replace karna.
- Load badhne par instances increase karna.
- Load kam hone par instances terminate karna.
- Desired capacity maintain karna.
- Multiple Availability Zones mein instances distribute karna.
- Load Balancer ke saath integrate hona.

Yaani ASG sirf scaling nahi karta, poore EC2 fleet ka lifecycle manage karta hai.

<br>
<br>

### Sabse pehle problem samajhte hain

Maan lo tumhari company ki ek E-Commerce Website hai.

Architecture.
```
Internet

↓

Application Load Balancer

↓

EC2-1

EC2-2
```
Normal days par Website par lagbhag 2,000 users aate hain. Do EC2 Instances easily saara traffic handle kar leti hain.

Sab kuch smoothly chal raha hai.

<br>

**Ab Festival Sale shuru ho gayi**:

Ek din Company ne Diwali Sale announce kar di. Raat ke 8 baje. Achaanak Website par **2 lakh users** aa gaye.

Ab kya hoga?
- Dono EC2 Instances par CPU utilization bahut badh jayegi.
- Memory bhi bharne lagegi.
- Response Time increase ho jayega.
- Users ko website slow lagne lagegi.
- Kuch requests timeout hone lagenge.

Aur agar load aur badh gaya. To dono EC2 crash bhi ho sakti hain.

<br>

**Pehla Solution**:

Koi bolega.

"EC2 ka size bada kar dete hain."

```t3.micro``` ko hata kar. ```m7i.large``` kar dete hain ya ```c7i.4xlarge``` kar dete hain.

Ye **Vertical Scaling** hai.

Lekin iski bhi limit hoti hai. Aur bahut bada Instance lene ka matlab hai. Zyada Cost.

<br>

**Dusra Solution**:

Koi bolega "Do aur EC2 bana dete hain."

Ab Architecture.
```
Internet

↓

ALB

↓

EC2-1

EC2-2

EC2-3

EC2-4
```
Ab load divide ho jayega.

Ye **Horizontal Scaling** hai.

Ye solution better hai.

<br>

**Lekin ek aur problem**:

Diwali Sale sirf 2 din ki thi. 2 din baad. Traffic phir se normal ho gayi. Ab Website par sirf 2,000 users aa rahe hain.

Lekin.

4 EC2 Instances abhi bhi chal rahi hain. Ab kya hoga?

Company unnecessary paise degi. Kyuki AWS per-hour ya per-second billing karta hai. Yaani resources idle hone ke baad bhi paise lagenge.

<br>

**AWS ne kya socha?**

AWS Engineers ne socha.

"Kyun na EC2 Instances automatically badh aur kam ho jayein."
```
Traffic badhe.

↓

Automatic naye EC2 create ho jayein.
```
```
Traffic kam ho.

↓

Automatic unnecessary EC2 terminate ho jayein.
```
Isi feature ka naam hai **Auto Scaling Group**.

Auto-Scaling group desired EC2 instances running rehte hain, traffic badhne par, autmatically EC2 instances badh jaate hain, jaise 2 se 4 ho jayenge. Agar traffic kam hua to autmatically EC2 instances kam ho jayenge.

<br>
<br>

### Auto Scaling Group kya karta hai?

Auto Scaling Group continuously application ko monitor karta rehta hai.

Jaise hi load badhta hai, ye automatically naye EC2 instances launch kar deta hai.

Jaise hi load kam hota hai, ye unnecessary EC2 instances terminate kar deta hai.

Agar koi EC2 crash ho jaye, to uski jagah automatically naya instance launch kar deta hai.

Yaani Auto Scaling Group ka kaam sirf scaling nahi, balki infrastructure ki health maintain karna bhi hota hai.

<br>
<br>

### ASG khud EC2 create nahi karta

Auto Scaling Group ko khud nahi pata hota ki EC2 kaunsi AMI se banani hai.

Kitni RAM honi chahiye.

Kitna Storage hona chahiye.

Kaunsa Security Group attach karna hai.

Kaunsi IAM Role attach karni hai.

Kaunsi User Data script run karni hai.

Ye saari information Auto Scaling Group ko **Launch Template** se milti hai.

<br>
<br>

### Launch Template kya hoti hai?

Sabse pehle Auto Scaling ko ye batana padta hai ki agar future mein naya EC2 banana pade to uska configuration kya hoga.

AWS khud se guess nahi kar sakta.

Usko exact instructions chahiye.

Ye instructions Launch Template mein store hoti hain.

Launch Template basically ek blueprint hota hai.

Waise hi AWS instance banane se pehle Launch Template dekhta hai.

Isme normally ye information hoti hai:
- AMI ID
- Instance Type
- Key Pair
- Security Group
- IAM Role
- User Data Script
- EBS Volume
- Monitoring
- Network Settings

**Ye kyu zaruri hai?**:

Maan lo Auto Scaling ko raat ke 2 baje automatically 20 naye instances create karne pade. Us time koi engineer office mein nahi hoga.

AWS Launch Template dekhega. Aur uske according exact same configuration ke naye EC2 launch kar dega.

Agar Launch Template nahi hota to AWS ko pata hi nahi hota ki kis AMI se instance banana hai.

<br>
<br>

### Iske Main Components Kya Hote Hain?

ASG ko setup karne ke liye aur isko chalane ke liye 3 sabse main components ka use hota hai:

**A. Launch Template (Ya Launch Configuration)**:

Yeh ek tarah ka blueprint ya recipe hota hai jo ASG ko batata hai ki naya server kaisa dikhna chahiye. Iske andar aap yeh sab chize define karte hain

<br>

**B. Size Parameters (Group Size)**

ASG ko sahi tareeke se control karne ke liye aapko teen tarah ke limits/sizes set karne hote hain:
- **Minimum Size**: Minimum Capacity batati hai ki ASG ke andar minimum kitne EC2 instances hamesha running hone chahiye. (example: minimum 2 servers hamesha chalne chahiye).
- **Maximum Size**: Maximum Capacity batati hai ki ASG maximum kitne instances tak scale kar sakta hai. (example: max 10 servers tak hi ja sakta hai).
- **Desired Capacity**: Yeh woh number hai jo ASG hamesha maintain karne ki koshish karta hai jab tak koi naya scaling rule trigger nahi hota (example: normal time par 4 servers chalne chahiye).

<br>

**C. Scaling Policies**:

AWS Auto Scaling Group (ASG) mein Scaling Policies woh rules hote hain jo yeh decide karte hain ki aapke servers (EC2 instances) kab automatic badhne chahiye (Scale-Out) aur kab automatic kam hone chahiye (Scale-In)

**1. Target Tracking Scaling Policy**:

Yeh sabse aasan aur sabse zyada use hone wali policy hai. Isme aapko koi complex rules nahi banane hote, bas ek metric aur uski ek target value set karni hoti hai,
- **Kaise kaam karti hai**: Aap ek target value set kar dete hain (jaise ki CPU utilization hamesha 60% rahe).
- **Action**: Agar load badhta hai aur CPU 70% ho jata hai, toh ASG naye instances add kar deta hai. Agar load kam hota hai aur CPU 40% ho jata hai, toh yeh instances ko remove kar deta hai taaki paise bachein.

<br>

**2. Step Scaling Policy**:

Yeh policy load ke hisab se alag-alag "steps" (charan) mein kaam karti hai. Jab aapko lagta hai ki load achanak bohot tezi se badh sakta hai, tab iska use hota hai.

Example:
- Agar CPU 50% se 70% ke beech hai → 1 naya instance add karo.
- Agar CPU 70% se 90% ke beech hai → 3 naye instances add karo.
- Agar CPU 90% se upar chala gaya → 5 naye instances add karo.

Fayda: Yeh achanak aaye bohot bade traffic spike ko bohot jaldi handle kar leti hai.

<br>

**3. Simple Scaling Policy**:

Yeh ASG ki sabse purani aur basic policy hai. Yeh ek single CloudWatch Alarm par kaam karti hai, lekin ismein ek rukawat hoti hai jise Cooldown Period kehte hain.
- **Kaise kaam karti hai**: Aapne rule banaya ki "Agar CPU 70% se upar jaye, toh 2 instances add kar do".
- **Cooldown Period**: Ek baar instances add hone ke baad, ASG ek fixed time (jaise 5 minute) ke liye shant ho jata hai (wait karta hai). Us 5 minute ke dauran chahe CPU 90% bhi chala jaye, yeh naye instances add nahi karega jab tak cooldown poora nahi hota. Jab tak cooldown period (default 300 seconds) chal raha hai, ASG dusra koi bhi scaling action trigger nahi karega, chahe metric threshold breach hi kyun na rahe.
- **Nuksaan**: Aaj kal iska use kam hota hai kyunki yeh achanak aaye bade traffic ko jaldi handle nahi kar paati. Iski jagah Step Scaling ko prefer kiya jata hai.

<br>

**4. Predictive Scaling Policy**:

Yeh ek advance policy hai jo Machine Learning (ML) ka use karti hai. Yeh aapke purane traffic pattern ko check karke aane wale traffic ka andaza lagati hai.
- **Kaise kaam karti hai**: Yeh aapke pichle 14 dino ka traffic data check karti hai. Agar isko lagta hai ki har Monday subah 9:00 baje aapki website par traffic achanak badhta hai, toh yeh Monday subah 8:45 baje hi naye instances ready kar degi.
- **Fayda**: Traffic aane se pehle hi aapka infra ready rehta hai. Instances ko start hone mein jo time lagta hai, us dauran users ko koi slow performance dekhne ko nahi milti.


<br>
<br>

### Production Architecture Mein ALB Aur ASG Ka Relationship

AWS ke andar Application Load Balancer (ALB) aur Auto Scaling Group (ASG) ka combination ek ideal standard architecture maana jata hai. Jab yeh dono milkar kaam karte hain, toh aapka application completely automated, highly available, aur scalable ban jata hai.

Production environment mein Application Load Balancer (ALB) aur Auto Scaling Group (ASG) ek doosre ke complementary services hain.

Iska matlab ye hai ki dono alag-alag problems solve karte hain, lekin jab dono ko ek saath use kiya jata hai to application Highly Available, Scalable aur Fault Tolerant ban jaati hai.

Production architecture mein generally flow kuch is tarah hota hai:
```
Internet Users

↓

Application Load Balancer (ALB)

↓

Target Group

↓

Auto Scaling Group (ASG)

↓

EC2-1

EC2-2

EC2-3
```

<br>

**ALB Aur ASG Saath Kyun Use Hote Hain?**

Sabse pehle ye samajho ki production application ka traffic kabhi constant nahi hota.

Subah kam users ho sakte hain.

Dopahar mein traffic badh sakta hai.

Kisi festival sale, marketing campaign ya breaking news ke time traffic bahut zyada increase ho sakta hai.

Agar backend mein sirf fixed 2 ya 3 EC2 instances honge, to ek time ke baad woh overloaded ho jayenge.

Isi wajah se infrastructure ko dynamically badalna padta hai.

Yahan ASG ka role shuru hota hai. To EC2 instances ko load ke hisab se scale karne ke liye Auto-scaling group use hota hai.

Lekin jab naye instances launch ho jate hain, tab users ki requests un naye instances tak kaise pahunchengi?

Yahan ALB ka role shuru hota hai. Request ko un naye instances jo ASG ne add kiye the unpe load balancer distribute karta hai.

Isliye production mein dono services ek saath use hoti hain.

<br>

**1. Inka Core Relationship Kya Hai?**:

In dono ka kaam aapas mein divide hota hai:
- **ALB ka kaam hai Traffic Distribution**: Yeh bahar se aane wale saare users ke request (traffic) ko akela jhelta hai aur piche chal rahe multiple servers par barabar hisse mein baant (distribute) deta hai.
- **ASG ka kaam hai Fleet Management**: Yeh piche chal rahe servers (EC2 instances) ki ginti ko manage karta hai. Traffic ke hisab se servers ko badhana ya kam karna iska kaam hai.

Jab aap inko connect karte hain, toh ASG apne saare servers ko ALB ke Target Group ke andar register kar deta hai. Jab bhi ASG koi naya server launch karta hai to turant usko ALB ke target mein register kar deta hai. Aur ese hi target group se remove kar deta hai.

<br>

**2. Yeh Dono Saath Mein Kaise Work Karte Hain? (Step-by-Step Flow)**:

Jab koi user aapki website ko access karta hai, toh pura process niche diye gaye steps ke mutabik chalta hai:

**Step 1: User Request ALB Par Aati Hai**:

Duniya bhar se jitne bhi users aapki website par aate hain, woh direct servers se connect nahi hote. Unki request sabse pehle Application Load Balancer (ALB) ke paas pahonchti hai.

<br>

**Step 2: ALB Traffic Ko Route Karta Hai**:

ALB apne Target Group ko check karta hai ki kaun-kaun se servers is waqt active aur healthy hain. Jo servers ASG ne launch kiye hote hain, ALB un sabhi par user traffic ko round-robin fashion (baari-baari se) bhej deta hai.

<br>

**Step 3: Traffic Badhne Par Scale-Out Process**:
- Jab website par achanak bohot zyada users aa jaate hain, toh ALB un sabhi ka load piche chal rahe limited servers par dalne lagta hai.
- ASG is load ko notice karta hai (CloudWatch Alarms ke zariye) aur turant ek naya EC2 instance launch kar deta hai.
- Sabse important baat: Is naye server ko aapko manually load balancer se connect nahi karna padta. ASG khud hi is naye instance ko ALB ke Target Group mein automatically register kar deta hai
- ALB pehle is naye server par ek Health Check run karta hai yeh dekhne ke liye ki server sahi se start hua hai ya nahi. Jaise hi server healthy show hota hai, ALB uspar bhi naya traffic bhejna shuru kar deta hai.

<br>

**Step 4: Traffic Kam Hone Par Scale-In Process**:
- Jab user traffic kam ho jata hai, toh ASG ko samajh aa jata hai ki ab extra servers ki zarurat nahi hai.
- ASG jis server ko delete karne wala hota hai, use direct terminate nahi karta. Woh sabse pehle ALB ko bolta hai ki is server par naya traffic bhejna band karo. Is process ko Connection Draining (ya Deregistration Delay) kehte hain.
- Is dauran jo users pehle se us server par moujud hain, unka kaam poora hone ka wait kiya jata hai. Jaise hi purane connections khatam hote hain, ALB us server ko target group se hata deta hai aur ASG use safely terminate (delete) kar deta hai.

<br>

**3. Health Checks Ka Sabse Bada Fayda**:

Agar aap bina ALB ke ASG use karte hain, toh ASG sirf EC2 Health Check karta hai. Iska matlab hai ki agar server ka hardware chalu hai, toh ASG samjhega ki server theek hai, bhale hi aapki website ka software andar se crash ho gaya ho.

Jab aap ALB aur ASG ko combine karte hain, toh aap ASG ko bol sakte hain ki woh ALB Health Check ka use kare. Isme ALB lagatar servers par ek specific URL (jaise http://your-ip/health) ko hit karke check karta hai. Agar aapka web server 500 Error de raha hai ya crash ho gaya hai, toh ALB usko "Unhealthy" mark karega aur ASG turant us kharab server ko delete karke uski jagah ek fresh naya server khada kar dega.
