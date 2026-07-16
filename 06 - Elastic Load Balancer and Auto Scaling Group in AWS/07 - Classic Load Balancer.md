# Classic Load Balancer Overview

Classic Load Balancer (CLB) ko samajhne ke liye sabse pehle ye samajhna zaruri hai ki AWS ne ALB aur NLB banane ke baad bhi CLB ka naam kyun exist karta hai?

<br>

### Sabse pehle history samajhte hain

Jab AWS ne shuru-shuru mein **Elastic Load Balancing (ELB)** service launch ki thi, tab sirf ek hi type ka Load Balancer available tha.

Usi ka naam tha.

**Classic Load Balancer (CLB)**.

Us samay applications bhi simple hoti thi.

Architecture kuch is tarah hota tha.
```
Internet

↓

Classic Load Balancer

↓

EC2-1

EC2-2

EC2-3
```
Internet se jo bhi request aati thi.

Wo pehle Classic Load Balancer ke paas jaati thi. Classic Load Balancer us request ko kisi ek healthy EC2 Instance ke paas bhej deta tha.

Us samay customers ke liye ye kaafi tha.

<br>

**Problem kab shuru hui?**

Jaise-jaise Cloud Computing popular hui. Applications bhi complex hone lagi.

Ab companies sirf ek Website nahi bana rahi thi. Ek hi Domain ke andar bahut saari Services chalne lagi.

Example.
```
example.com
```
Is Domain ke andar.
```
example.com/api
```
API Service.

```
example.com/images
```
Image Service.

```
example.com/admin
```
Admin Panel.

```
example.com/payment
```
Payment Service.

<br>

Ab problem ye thi. Classic Load Balancer ye decide nahi kar sakta tha ki.
- ```"/api"``` wali request API Server ko bhejni hai.
- ```"/images"``` wali request Image Server ko bhejni hai.

Uske paas itni intelligence nahi thi.

<br>

**AWS ne kya socha?**

AWS Engineers ne dekha. Customers ko smarter Load Balancer chahiye.
- Jo URL ko samajh sake.
- Host Name ko samajh sake.
- Microservices ko support kare.

Isi wajah se AWS ne **Application Load Balancer (ALB)** introduce kiya.

Dusri taraf. Bahut saare customers ko High Performance TCP aur UDP traffic handle karni thi.

Unke liye AWS ne **Network Load Balancer (NLB)** introduce kiya.

Aur Security Appliances ke liye.

AWS ne **Gateway Load Balancer (GWLB)** introduce kiya.

<br>

Isliye aaj ke time mein.

**Classic Load Balancer** ek Legacy Load Balancer mana jata hai.

Legacy ka matlab.

Purana.

Jo abhi bhi support hota hai.

Lekin naye workloads ke liye generally recommend nahi kiya jata.

<br>
<br>

### Classic Load Balancer kya hota hai?

Classic Load Balancer AWS ka pehla generation Load Balancer hai jo incoming traffic ko multiple EC2 Instances mein distribute karta hai. Ye Layer 4 (TCP/SSL) aur limited Layer 7 (HTTP/HTTPS) features support karta hai, lekin modern routing capabilities jaise Path-Based Routing aur Host-Based Routing support nahi karta.

Classic Load Balancer hybrid tha. Ye OSI Model ki Layer 4 aur Layer 7 Dono par kaam kar sakta tha.

Matlab, Ye.
- TCP.
- SSL.
- HTTP.
- HTTPS.

Support karta tha. Lekin. Layer 7 ki capabilities bahut limited thi.

<br>

**Classic Load Balancer ka purpose**

Uska main purpose tha. Ek hi Application ki requests ko multiple EC2 Instances mein distribute karna.


Taaki.
- High Availability mile.
- Fault Tolerance mile.


- Scalability mile.

<br>
<br>

### CLB Kaise Kaam Karta Hai? (End-to-End Traffic Process)

CLB ke traffic intercept karne aur use routing mechanism ke through forward karne ka execution path is tarah chalta hai:

**1. Connection Initiation (The Listener Layer)**

Jab ek client browser application url par hit karta hai, toh DNS resolution ke through traffic CLB ke public virtual IP par land karta hai. CLB ke paas Listeners hote hain jo specific ports (jaise Port 80 ya 443) par traffic ko continuously listen karte hain.

<br>

**2. Protocol Evaluation (Layer 4 vs Layer 7 Routing)**

CLB yahan do alag-alag mechanisms se traffic handle kar sakta hai:
- **Layer 7 Mode (HTTP/HTTPS)**: Agar request HTTP hai, toh CLB packet ke HTTP headers ko parse karta hai. Yeh ```X-Forwarded-For``` aur ```X-Forwarded-Proto``` headers ko automatic inject kar deta hai, taaki backend instance ko asli client ka original IP address aur protocol pata chal sake.
- **Layer 4 Mode (TCP/SSL)**: Agar application non-web protocols use kar rahi hai, toh CLB packet ke headers ko bina touch kiye (raw format mein) layer 4 level par transport layer TCP connection open karta hai aur traffic piche bhej deta hai.

<br>

**3. Health Check Validation**

Traffic forward karne se pehle, CLB piche maujood EC2 instances ki state check karta hai. Yeh regularly specified interval par ping path (jaise /index.html ya TCP handshake) perform karta hai. Jo instance unhealthy declare ho jata hai, CLB wahan traffic bhejna turant stop kar deta hai.


<br>

**4. Round-Robin Distribution**

CLB routing logic ke liye primary level par Round-Robin Routing Algorithm ka use karta hai. Yeh har ek naye incoming request ko sequentially ek ke baad ek registered ec2 instances par transfer karta jata hai, jab tak ki session stickiness configured na ho.


<br>
<br>

### Classic Load Balancer Ke Key Features Aur Limitations

**Sticky Sessions (Cookie Insertion)**: CLB application-controlled ya load-balancer-generated cookies ka use karke ek specific client ke traffic ko session duration tak ek hi discrete EC2 instance par bind rakh sakta hai.

**SSL Offloading / Termination**: CLB incoming HTTPS traffic ko load balancer level par hi decrypt kar sakta hai (SSL certificates handle karke), jisse backend ec2 instances par computation ka extra overhead load khatam ho jata hai.

**No Path-Based or Host-Based Routing (Major Limitation)**: CLB kisi URL path (jaise /images ya /api) ya domain host header (jaise ://example.com) ko read karke traffic ko alag-alag server groups par route nahi kar sakta. Yeh har ek listener port ke liye sirf ek hi common pool of instances par traffic bhej sakta hai.

**No Support for Containerized Microservices**: CLB dynamic ports aur target groups ko support nahi karta, isliye ise Amazon ECS ya Kubernetes (EKS) ke modern microservices micro-architecture ke sath dynamically scale karna lagbhag impossible hai.


<br>
<br>

### EOL (End of Life) Notification Alert (Context 2026)

AWS ne infrastructure upgrades ke tehat Classic Load Balancer (CLB) ko purani EC2-Classic networks ke sath deprecation phase mein daal diya hai. New scalable cloud infrastructures mein AWS official best practices ke mutabiq hamesha L7 use-cases ke liye ALB aur ultra-low latency L4 use-cases ke liye NLB ka validation compulsory hai.
