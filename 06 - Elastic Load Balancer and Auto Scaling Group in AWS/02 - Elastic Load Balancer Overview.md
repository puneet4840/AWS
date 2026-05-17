# Elastic Load Balancer

Elastic Load Balancer AWS ki ek managed service hai jo Incoming traffic ko multiple servers par distribute karta hai.

AWS mein jab tum applications ko servers mein internet par run karte ho — jaise websites, APIs, microservices, Kubernetes apps, ya enterprise applications — tab ek bahut bada problem aata hai:

“Agar saare users ek hi server par aa gaye toh kya hoga?”:
- Server overload ho jayega.
- Application slow ho jayegi.
- Kabhi-kabhi pura app down bhi ho sakta hai.

Isi problem ko solve karta hai: **Elastic Load Balancer**.

AWS mein Load Balancer incoming requests ko sirf EC2 instance par hi distribute nahi karta, ye EC2 instance ke alawa alag-alag backend par par bhi distribute karte hai, Jaise:
- Container.
- Ip Addresses.
- Lambda Functions.

ELB ek regional service hai aur availability zones mein high availability bhi provide karta hai aur do EC2s alag-alag availability zones hai to traffic ko distribute karta hai.

ELB application ko access karne ke liye IP Address provide nahi karta balki ek DNS entry provide karta hai jisse tum application access kar sakte ho.

**Example-1**:

Suppose tumahar application on-premises servers par run kar rha hai, tumne on-premise network ko aws vpc ke saaath site-to-site ya fir direct connection se connect kiya hua hai. To ELB tumhare on-premises ke server par bhi traffic distribute kar sakta hai agar tumne load balancer ke backend mein server ki ip register ki hai to.

**Example-2**:

ELB lambda function ko bhi execute kar sakta hai agar ELB ke backend mein lambda function register kiya hai to. Har request par lambda trigger ho sakta hai.

<br>
<br>

### Real World Example

Suppose tumhari ek ecommerce website hai:
```
www.mystore.com
```
Aur tumhare paas 3 servers hain:
```
Server 1
Server 2
Server 3
```
Agar 10,000 users ek saath website open karein aur sab traffic sirf Server 1 par jaye:
```
Users ---> Server 1 💥
```
Server 1 crash ho sakta hai.

Toh ELB kya karega?
```
              ELB
               |
    -----------------------
    |         |           |
Server1    Server2    Server3
```
Ab ELB users ka traffic intelligently divide karega.

Example:
```
User 1 → Server1
User 2 → Server2
User 3 → Server3
User 4 → Server1
```
Isko bolte hain: **Load Balancing**.

<br>
<br>

### Types of Elastic Load Balancer

AWS ne time ke saath multiple load balancers introduce kiye.

Aaj mainly 4 types hain:

| Type                            | Layer     | Use Case            |
| ------------------------------- | --------- | ------------------- |
| Application Load Balancer (ALB) | Layer 7   | HTTP/HTTPS          |
| Network Load Balancer (NLB)     | Layer 4   | TCP/UDP             |
| Gateway Load Balancer (GWLB)    | Layer 3/4 | Security appliances |
| Classic Load Balancer (CLB)     | Old       | Legacy              |

<br>

### Application Load Balancer

Application load balancer ek layer 7 load balancer hai jo incoming HTTP/HTTPs traffic ko multiple backends par distribute karta hai.

Yahan Layer-7 ka matlab hai ki Application Load Balancer Layer-7 ke Http aur Https traffic ko inspect karta hai aur route karta hai.

ALB modern cloud-native architectures ke liye banaya gaya hai jahan:
- microservices
- containers
- Kubernetes
- APIs
- serverless applications

use hote hain.

Traditional load balancers sirf traffic distribute karte the, lekin ALB traffic ko samajhta bhi hai.

Ye:
- URL path samajhta hai
- headers samajhta hai
- hostname samajhta hai
- cookies samajhta hai
- query strings samajhta hai

Aur in sabke basis par intelligent routing decisions leta hai.

ALB ke Backend servers ho sakte hain:
- EC2
- ECS containers
- EKS pods
- Lambda functions
- IP targets


<br>

### Ye Layer-7 ALB ka kya matlab hai.

Networking mein hum **OSI Model** use karte hain jisme 7 layers hoti hain. OSI (Open Systems Interconnection) model ek conceptual framework hai jo ye samajhne mein madad karta hai ki ek computer se dusre computer tak data kaise travel karta hai. Ismein 7 layers hoti hain, aur har layer ka apna ek specific kaam hota hai.

OSI Model koi hardware nhi, balki rules hote hain. Jo batate hain, Jab ek computer se dusre computer tak data jata hai, toh wo in 7 steps (Layers) se guzarta hai.

Aur inhi layers mein se ek layer hoti hai **Layer-7**, jisko **Application Layer** bhi bolte hain.

**Application Load Balancer** isi **Layer-7** yani **Application Layer** par kaam karta hai. Iska matlab hai ki ALB ke paas Layer-7 ka data pahunchta hai isliye ALB ko Layer-7 load balancer kehte hain.

<br>

**Application Layer (Layer-7)**:

Application Layer OSI model ki sabse upar wali layer (Layer 7) hoti hai. Ye wo layer hai jisse hum (users) direct interact karte hain. Jab aap Chrome ya Safari mein koi website kholte hain, toh aap actually Application Layer ka use kar rahe hote hain.

To ye layer background mein kaafi saara "raw data" aur "instructions" generate karti hai taaki networking process shuru ho sake.

Jab aap browser mein koi URL type karke hit karte hain, toh browser ek HTTP Request Packet taiyar karta hai server ko bhejne ke liye. Ye HTTP request mein ye data hota hai:

- Request Line.
- Headers.
- Body (optional).

**Request Line**:

Yeh packet ka sabse pehla hissa hota hai. Isme 3 major cheezein hoti hain:
- Method: Browser batata hai ki wo kya karna chahta hai (e.g., ```GET``` page dekhne ke liye, ```POST``` data bhejne ke liye). Website se data lena hai ya data dena hai.
- Path/URI: Website ka kaunsa hissa chahiye (e.g., ```/index.html``` ya ```/login```).
- HTTP Version: HTTP protocol ka Kaunsa version use ho raha hai request bhejne ke loye(e.g., HTTP/1.1 ya HTTP/2).

Example: ```GET /profile HTTP/1.1```.

**HTTP Headers (Browser ki detail)**:

Yeh sabse bada aur important hissa hai. Headers mein browser bahut saari details "Key-Value" pairs mein pack karta hai. Ye browser ke baare mein extra information hoti hai:
- Host Header: Isme aapki website ka naam hota hai (Host: ```www.puneet-website.com```). Gateway isi ko dekh kar samajhta hai ki request kis website ke liye hai.
- User-Agent: Browser ki details (e.g., Mozilla/5.0, Chrome/120). Isse Gateway ko pata chalta hai ki request mobile se hai ya laptop se.
- Accept: Browser batata hai ki use kaisa data chahiye (e.g., text/html, image/webp).
- Cookie: Agar aapne pehle login kiya hai, toh browser Session ID bhejta hai. Gateway isse dekh kar aapko purane wale VM (Server) par hi bhejta hai (jise hum Sticky Session kehte hain).
- Authorization: Agar website password-protected hai, toh login credentials isi header mein jaate hain.
- Referer: Ye batata hai ki aap kis purani website se link click karke yahan aaye hain.

**Message Body (The Payload)**:

Ye packet ka last hissa hota hai. Isme aap website ko apna data bhej rahe hote hain. Toh browser us saare data ko Message Body mein daal deta hai. Application Gateway (agar usme WAF enabled hai) is body ko scan karta hai taaki koi hacker galat code (SQL Injection) na bhej raha ho.

Agar aap sirf website "open" kar rahe hain (GET request), toh ye body aksar khali (empty) hoti hai, Lekin agar aap:
- Koi form bhar rahe hain.
- File upload kar rahe hain.
- Login button daba rahe hain.

To ye HTTP request packet **Application Load Balancer** ke paas pahunchti hai aur **Application Load Balancer** is detail ko dekh pata hai.

<br>
<br>

**Ab complete end-to-end samjhiye ki kaise ek Http request server ke paas jati hai**:

Browser mein URL daal kar 'Enter' hit karna ek simple step lagta hai, lekin parde ke peeche (behind the scenes) ek pura networking protocol ka mela chalta hai.

Jab aap ```https://www.example.com/index.html``` likhte hain, to browser seedhe server se baat nahi kar sakta. Use pehle rasta dhoondna padta hai aur phir ek formal document (HTTP Request) taiyar karna padta hai.

Isko step-by-step detail mein samajhte hain:

**Step-1: URL Parsing (Structure Samajhna)**:

Sabse pehle browser aapke URL ko todta hai taaki wo samajh sake ki jaana kaha hai aur mangna kya hai.
- Protocol: ```https``` (Matlab data secure tarike se jayega).
- Domain: ```www.example.com``` (Ye server ka naam hai).
- Path: ```/index.html``` (Ye wo specific file hai jo aapko chahiye matlab browser ko chaiye jisko render karne se website dikhegi).

**Step-2: DNS Lookup (Address Dhoondna)**:

Internet par servers ko unke naam se nahi, balki IP Address se pehchana jata hai. Browser ko ab ```www.example.com``` ka IP address pata karna hoga.
- Browser IP ko pehle apne Cache mein dekhta hai.
- Agar wahan nahi mila, to wo OS se poochta hai.
- OS phir DNS Resolver (ISP) ke paas jata hai.
- Ant mein, browser ko ek IP address mil jata hai, jaise: ```93.184.216.34```.

**Step-3: TCP/IP Handshake (Connection Banana)**:

IP milne ke baad browser server ko ek TCP connection request bhejta hai. Isse TCP Three-Way Handshake kehte hain:
- SYN: Browser kehta hai, "Kya main connection start kar sakta hoon?".
- SYN-ACK: Server kehta hai, "Haan, main ready hoon.".
- ACK: Browser kehta hai, "Theek hai, main request bhej raha hoon.".

**Step-4: HTTP Request Create Karna (The Main Part)**:

Ab browser ek text-based message likhta hai jise HTTP Request kehte hain. Iske teen main parts hote hain:
- Request Line:

  Isme teen cheezein hoti hain:
  - Method: ```GET``` (Kyuki hume data mangwana hai).
  - Path: ```/index.html```.
  - Version: ```HTTP/1.1``` ya ```HTTP/2```.

- Request Headers:

  Ye browser ke baare mein extra information hoti hai:
  - Host: ```www.example.com```.
  - User-Agent: Aapka browser kaunsa hai (Chrome, Safari, etc.).
  - Accept: Browser kis type ki file handle kar sakta hai (text/html).
  - Cookie: Agar aapne pehle visit kiya hai, to purani identity details.
 
- Request Body:

  GET request mein body aksar khali hoti hai. Lekin agar aap koi form bhar rahe hote, to wo data yahan hota.

Example of a Real HTTP Request:

Jab aap enter marte hain, to parde ke peeche ye text message server ko bheja jata hai, isi ko http request kehte hain:

```
GET /index.html HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)
Accept: text/html,application/xhtml+xml
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
```

**Request Ka Safar (OSI Model Application)**:

Browser is request ko Application Layer par banata hai. Aur ye hi request Application Load Balancer ko pass pahuncti hai, isi information ko ALB dekh sakta hai Layer-7 par. Isliye Application Load Balancer ko Layer-7 load balancer kehte hain.

<br>
<br>

### Detailed working of Application Load Balancer

Ab hum ALB ka end-to-end internal flow detail mein samjhenge.

**Step 1 — User Sends Request**:

User browser mein enter karta hai:
```
https://example.com
```
Browser DNS lookup start karta hai.

<br>

**Step 2 — DNS Resolution**:

DNS:
```
example.com
```
ko map karta hai:
```
my-alb-123.us-east-1.elb.amazonaws.com
```
ALB ka DNS name AWS automatically provide karta hai.

Usually:
- Route 53
- GoDaddy
- Cloudflare

jaise DNS providers use hote hain.

<br>

**Step 3 — Request Reaches ALB**:

Browser HTTPS request bhejta hai.

ALB receive karta hai:
- source IP
- headers
- cookies
- path
- query string
- hostname
- protocol

<br>

**Step 4 — Listener Receives Traffic**:

ALB ke andar listeners hote hain.

Listener:
- incoming traffic ko continuously listen karta hai.

Example:
| Protocol | Port |
| -------- | ---- |
| HTTP     | 80   |
| HTTPS    | 443  |

Listener ek entry point hota hai.

HTTPS Listener

Example:
```
HTTPS : 443
```
Ye TLS handshake perform karega.

<br>

**Step 5 — SSL/TLS Handshake**:

Agar HTTPS use ho raha hai:

ALB:
- SSL certificate use karega
- encrypted traffic decrypt karega

Certificates usually: AWS Certificate Manager se attach kiye jate hain.

SSL Termination

Flow:
```
Client HTTPS
    ↓
ALB decrypts traffic
    ↓
Backend receives HTTP
```
Benefits:
- backend CPU load kam
- centralized certificate management
- easy SSL renewals

<br>

**Step 6 — Listener Rules Evaluation**:

ALB request inspect karta hai.

Example request:
```
GET /api/orders HTTP/1.1
Host: api.example.com
```
ALB rules evaluate karega.

<br>

Types of Routing Rules:

**1. Path-based Routing**:

Example:
```
/api/*
```
Traffic:
- backend API servers pe jayega.

**2. Host-based Routing**:

Example:
```
api.example.com
admin.example.com
```
Different domains different services pe.

**3. Header-based Routing**:

Example:
```
User-Agent: Mobile
```
Mobile requests separate service pe.

**4. Query Parameter Routing**:

Example:
```
?version=v2
```
Canary deployment ke liye useful.

<br>

**Listener Rule Structure**

Rule generally contain karta hai:
| Component | Description            |
| --------- | ---------------------- |
| Condition | Match criteria         |
| Action    | Kaha forward karna hai |
| Priority  | Rule evaluation order  |

Example Rule
```
IF path = /payments/*
THEN forward to Payment-TG
```

<br>

**Step 7 — Target Group Selection**:

ALB directly servers pe traffic nahi bhejta. Pehle Target Group select hoti hai.

What is Target Group?

Target Group: backend resources ka logical collection hota hai.

Example:
```
Frontend-TG
Backend-TG
Auth-TG
```

Target Types Supported

ALB support karta hai:
| Target Type | Description          |
| ----------- | -------------------- |
| EC2         | Virtual machines     |
| IP          | Pod IPs              |
| Lambda      | Serverless functions |
| ECS Tasks   | Containers           |

Example of Microservice Architecture:
```
ALB
 ├── /users → User-TG
 ├── /orders → Order-TG
 ├── /payments → Payment-TG
 └── /auth → Auth-TG
```

<br>

**Step 8 — Health Check Verification**:

ALB continuously health checks karta hai.

Health Check Purpose

Agar:
- server crash ho jaye
- app hang ho jaye
- DB connection fail ho jaye

to unhealthy server pe traffic nahi jana chahiye.

Health Check Example

ALB periodically call karega:
```
/health
```
Agar:
```
200 OK
```
to healthy.

Agar:
```
500
timeout
404
```
to unhealthy.

Health Check Parameters
| Parameter           | Meaning                      |
| ------------------- | ---------------------------- |
| Interval            | Kitni frequently check karna |
| Timeout             | Wait time                    |
| Healthy threshold   | Success count                |
| Unhealthy threshold | Failure count                |

Example:
```
Interval = 30 sec
Timeout = 5 sec
Unhealthy threshold = 2
```
Matlab 2 baar fail hone pe unhealthy mark hoga.

<br>

**Step 9 — Request Forwarding**:

Healthy targets me se ek target choose hota hai.

Request us server pe forward hoti hai.
