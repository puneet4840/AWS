# Network Load Balancer

Network Load Balancer ek layer-4 load balancer hai jo incoming TCP/UPD traffic ko backends par distribute karta hai.

Backends, Jaise:
- EC2.
- Container Instance.
- Ip Address.

Layer-4 ka matlab hai ki TCP aur UDP traffice. Matlab Network Load Balancer ke paas TCP aur UDP traffic aata hai aur usko hi NLB samajh sakta hai.

Matlab NLB ke paas:
- Source IP Address.
- Source Port number.
- Destinationn IP Address.
- Destination Port Number
- Protocol: TCP/UDP

To ye uper di hui 5 information NLB ke paas pahunchti hai aur inko understand karta hai aur isi ke basis pe traffic ko multiple backends par distribute karta hai.

Application Load Balancer ke paas complete HTTP/HTTPS request aati hai, jisme request line, headers aur body ALB ke paas aati hai, aur ALB us data ke basis par traffic multiple backends par distribute karta hai.

<br>
<br>

AWS mein jab hum bahut high-performance applications run karte hain — jahan:
- millions of requests aa sakti hain.
- ultra low latency chahiye hoti hai.
- TCP/UDP traffic handle karna hota hai.
- ya static IP ki requirement hoti hai.

tab hum mostly use karte hain:**Network Load Balancer (NLB)**.

<br>

Network Load Balancer ek Layer 4 load balancer hai jo:
- incoming TCP/UDP traffic ko distribute karta hai.
- extremely high performance provide karta hai.
- ultra low latency maintain karta hai.
- aur millions of requests handle kar sakta hai.

<br>
<br>

### NLB Extremely Fast Kyu Hai?

Kyuki:
- ye Layer 7 processing nahi karta.
- HTTP parsing nahi karta.
- cookies inspect nahi karta.
- URL evaluate nahi karta.

Ye sirf packets forward karta hai.

Result:
```
Very high speed
```

<br>
<br>

### NLB Static IP provide karta hai

NLB ke paas ek static public ip hoti hai jisse NLB par request behjte hain, lekin esa ALB ke paas nhi hota. ALB ek FQDN provide karta hai jisse users ALB ke paas request bhej sakte hain.

NLB ka public IP fixed reh sakta hai.

Example:
```
3.90.10.20
```
Ye IP:
- same rahega
- stable rahega
- predictable rahega

<br>

**Why Static IP Important Hai?**:

Bahut saare enterprise systems: fixed IP expect karte hain.

**Example — Firewall Whitelisting**:

Suppose:
- ek external company tumhare API ko access karna chahti hai.

Unhone firewall lagaya hua hai. Wo bolte hain:
- Apna fixed IP do

Taaki:
- sirf wahi IP allow ho.


<br>
<br>

### Request Flow End-to-End

**Real Production Scenario**

Hum ek normal production web application ka example lenge jahan user browser se website access karta hai.

Suppose tumhari company ki ek banking API application hai:
```
https://api.mybank.com
```

Aur architecture kuch aisa hai:
```
Users
   |
Internet
   |
Route53
   |
NLB
   |
-------------------------
|           |           |
App1       App2       App3
```

Ab hum samjhenge: Browser se request kaise backend server tak jaati hai through NLB.

**Step 1 — User Browser Mein URL Hit Karta Hai**:

Suppose user browser open karta hai:
```
https://api.mybank.com
```
Aur Enter press karta hai.

Browser Sabse Pehle Kya Karega? Browser ko actual server ka IP nahi pata. Usko sirf domain pata hai:
```
api.mybank.com
```
Toh browser karega: **DNS Resolution**.

<br>

**Step 2 — DNS Resolution**:

Usually DNS handle karta hai: Amazon Web Services Route 53 service

Route53 Mein Record
```
api.mybank.com ---> NLB DNS Name
```
Example:
```
internal-nlb-123.us-east-1.elb.amazonaws.com
```

Browser ko finally milta hai:
```
52.10.20.30
```
Ye NLB ka IP hai.

<br>

**Step 3 — Browser TCP Connection Start Karta Hai**:

Ab browser NLB ke saath TCP connection establish karega. 

TCP Connection:
```
Browser:

SYN

NLB:

SYN-ACK

Browser:

ACK
```
Connection Established.

NLB ke paas user ki request pahunch gyi.

**NLB Is Stage Par Kya Dekhta Hai?**

NLB inspect karta hai:
| Field          | Example      |
| -------------- | ------------ |
| Source IP      | 203.0.113.10 |
| Destination IP | 52.10.20.30  |
| Protocol       | TCP          |
| Port           | 443          |

**Advanced Feature — Source IP Preservation**:

NLB original client IP preserve karta hai. Backend server actual user IP dekh sakta hai.

Why Important?
```
- Kaunsa real user request bhej raha hai?
- Traffic kaha se aa raha hai?
- Ek IP kitni requests bhej raha hai?
```

<br>

**Step 4 — TLS/HTTPS Negotiation**:

Kyuki URL:
```
https://
```
use kar raha hai.

TLS handshake start hoga.

Steps:
- Browser encrypted traffic bhejta hai.
- NLB certificate present karta hai. Usually certificate aata hai: Amazon Web Services ACM se.

Example:
```
api.mybank.com SSL certificate
```
- TLS handshake complete.
- NLB traffic decrypt karta hai.
- Backend ko plain TCP bhej sakta hai.

Benefits:
| Benefit                     | Explanation           |
| --------------------------- | --------------------- |
| Backend CPU save            | Encryption offload    |
| Easy certificate management | Centralized           |
| Better scaling              | Less backend overhead |

<br>

**Step 5 — Listener Processing**:

NLB ke paas listeners configured hote hain.

Example:
```
TCP : 443
```
Ya:
```
TLS : 443
```

Listener Ka Kaam Incoming traffic receive karna.

Example:
- Port 443 traffic accept karo.

<br>

**Step 6 — Flow Hashing**:

Ab NLB decide karega:
- Kaunsa backend server choose karna hai traffic bhejne ke liye.

Flow Hashing method se ek backend server select kiya jata hai.

Jab NLB ke paas user ki request aati hai to NLB ye chize us request mein dekh pata hai:
- Source IP Address.
- Source Port number.
- Destination IP Address.
- Destination Port Number.
- Protocol.

In 5 information ke base par NLB hash number calculate karta hai, us hash number se decide hota hai ki konse backend server par ye request bhejni hai.

Azure mein isko 5-tuple hash bolte hain aur AWS mein isko Flow Hashing bolte hain.

**Why Hashing?**:
- Consistent routing ke liye.

Matlab jaise request NLB pe aayi to information se hash number calculate karke suppose server1 ko request behj di. To next time same user NLB ko request bhjega to uska hash number bhi next time same calculate hoga, to request bhi same server1 par jayegi. 

Ye consistent routing ke liye kiya jata hai.

Useful For:
- stateful apps.
- TCP session consistency.
- long-lived connections.

<br>

**Step 7 — Health Check Validation**:

NLB blindly traffic nahi bhejta.

Pehle check karta hai:
```
Backend healthy hai ya nahi?
```

Suppose:
```
App1 ✅
App2 ❌
App3 ✅
```
NLB: 
- App2 ko traffic nahi bhejega.

Even though NLB Layer 4 hai, health checks HTTP bhi ho sakte hain.

Example:
```
GET /health
```

Backend:
```
/health endpoint
```
Response:
```
200 OK
```

<br>

**Step 8 — Traffic Forwarding**:

Ab NLB selected backend ko packet forward karta hai.

Example:
```
User ---> NLB ---> App3
```

<br>

**Step 9 — Backend Processing**:

Backend server request process karta hai.

Suppose request thi:
```
GET /accounts
```
Application response generate karegi.

Example Response
```
{
  "balance": 5000
}
```

<br>

**Step 10 — Response Back Through NLB**:

Response:
```
Backend ---> NLB ---> Browser
```

<br>
<br>

### NLB Proxy Protocol

Jab koi user aapke NLB ke zariye aapke backend server se connect hota hai, toh servers ke paas source IP address ki jagah NLB ka apna internal IP address dikhne lagta hai. Is dikkat ko solve karne ke liye Proxy Protocol ka use kiya jata hai.

<br>

**1. Proxy Protocol Kya Hota Hai?**

Proxy Protocol ek aisa mechanism hai jo NLB ko is baat ki ijaajat deta hai ki woh user ke original connection ki details (jaise Client IP address, Proxy IP address, aur Port numbers) ko ek chote se header ke roop mein TCP packet ke andar lapet kar backend server tak pahoncha sake. Isse aapke EC2 servers ko yeh pata chal jata hai ki real request kis user ne bheji thi, bhale hi beech mein NLB baitha ho.

<br>

**2. Yeh Kyu Zaruri Hai?**:

Agar aap Proxy Protocol use nahi karte, toh kya hota hai:
- User ka IP address ```1.2.3.4``` hai, aur woh NLB (```10.0.0.5```) se connect hota hai.
- NLB us request ko aapke EC2 server (```10.0.0.100```) par forward kar deta hai.
- Matlab NLB user ki IP ki jagah khud ki internal ip backend server ko bhejta hai.
- Backend server ke logs mein request bhejne wale ka IP ```10.0.0.5``` (NLB ka IP) save hota hai.

Nuksan: Aap user ki location track nahi kar sakte, security blocklists nahi laga sakte, aur na ki kisi user ko unke original IP ke hisab se identity verification de sakte hain.

<br>

**3. Example Se Samjhein (Real-World Scenario)**:

Maan lijiye aap ek gaming application ya fir ek secure banking chat application chala rahe hain jo TCP protocol par kaam karti hai.

Scenario Configuration:
- Client (User) IP: ```203.0.113.50```.
- NLB Internal IP: ```192.168.1.10```.
- EC2 Backend Server IP: ```192.168.1.50```.

Jab Proxy Protocol ON (Enabled) hota hai:
- Jab client aapke app se connect karega, toh NLB network packet ke bilkul shuruat mein ek simple readable text line (header) jod dega. TCP data stream ke shuru hone se pehle aapke server ko yeh text milega:
```
PROXY TCP4 203.0.113.50 192.168.1.10 56324 80\r\n
```

Is line ka matlab kya hai?
- ```PROXY```: Yeh batata hai ki yeh ek Proxy Protocol header hai.
- ```TCP4```: Yeh batata hai ki IPv4 protocol ka use ho raha hai.
- ```203.0.113.50```: Yeh Original Client IP hai (jo aapko chahiye tha).
- ```192.168.1.10```: Yeh NLB ka IP address hai.
- ```56324```: Yeh client ka source port hai.
- ```80```: Yeh destination port hai jahan request aayi hai.

Aapka web server (jaise Nginx, Apache, ya koi custom Node.js/Go app) is line ko padhta hai, client ka asli IP nikalta hai, aur uske baad aane wale normal application data ko process karta hai.

<br>

**4. Important Rules Aur Requirements**:

Proxy Protocol use karne ke liye aapko do sabse main baaton ka dhyan rakhna padta hai:
- **NLB Target Group Setting**: Aapko apne NLB ke Target Group ki attributes settings mein ja kar **Proxy Protocol v2** ko manually Enable karna hota hai.
- **Backend Server Configuration**: Yeh sabse zaruri step hai. Agar aapne NLB par proxy protocol chalu kar diya lekin aapka backend server (Nginx/Apache) iske liye configured nahi hai, toh aapka server is header ko samajh nahi payega aur connection ko corrupt man kar reject (400 Bad Request ya connection reset) kar dega. Isliye aapko Nginx mein proxy_protocol directive ko listen port ke sath add karna padta hai.

<br>

**5. Application Load Balancer (ALB) Se Yeh Kaise Alag Hai?**

Aapke dimaag mein sawal aa sakta hai ki ALB mein toh hum aisa kuch nahi karte.ALB ek Layer 7 (HTTP/HTTPS) load balancer hai, isliye woh HTTP headers ke andar X-Forwarded-For naam ka header automatically jod deta hai jisme client ka IP hota hai. Lekin NLB sirf network/TCP layer par kaam karta hai, wahan koi HTTP header nahi hota, isliye wahan Proxy Protocol hi akela rasta hai asli IP bachane ka.

