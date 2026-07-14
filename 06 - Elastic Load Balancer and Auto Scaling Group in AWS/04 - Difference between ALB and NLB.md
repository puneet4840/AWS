# Difference Between ALB and NLB

AWS mein Application Load Balancer (ALB) aur Network Load Balancer (NLB) dono hi traffic management ke liye use hote hain, lekin dono OSI Model ke alag-alag layers par kaam karte hain.

<br>

### Main Differences (ALB vs NLB)

|     Feature     |                                 Application Load Balancer (ALB)                                | Network Load Balancer (NLB)                                                                                |
|:---------------:|:----------------------------------------------------------------------------------------------:|------------------------------------------------------------------------------------------------------------|
| OSI Layer       | Layer 7 (Application Layer)                                                                    | Layer 4 (Transport Layer)                                                                                  |
| Traffic Type    | Sirf HTTP, HTTPS, aur gRPC                                                                     | TCP, UDP, aur TLS                                                                                          |
| Routing Logic   | Smart Routing (Host-based, Path-based, Cookies, Headers)                                       | Raw Routing (Sirf Port aur IP address ke basis par)                                                        |
| IP Address Type | Dynamic IP (Iske IPs lagatar badalte rehti hain, isko hamesha DNS URL se hit karte hain)       | Static IP (Iske har subnet ko ek permanent fixed IP ya Elastic IP assign kiya ja sakta hai)                |
| Performance     | Performance acchi hai, par heavy payload parsing mein milliseconds ka delay (latency) hota hai | Ultra-high performance (Microseconds ki latency, yeh ek sath millions of requests ko handle kar sakta hai) |

<br>
<br>

### Application Load Balancer (ALB) Kab Use Karte Hain?

ALB request ke andar ka data read kar sakta hai (jaise URL path, domain name, headers, cookies). Isliye jab aapko smart routing chahiye ho, tab ALB ka use kiya jata hai.

<br>

**Yeh Kaam Kaise Karta Hai?**
- **Connection Termination**: Jab koi user request bhejta hai, toh ALB pehle us user ke browser ke sath apna TCP connection wahi khatam (terminate) kar deta hai.
- **Packet Inspection**: ALB packet ke andar jata hai aur HTTP/HTTPS headers, Cookies, URL paths (```/images```), Query Parameters, aur Hostnames (```app.com```) ko dhyan se check karta hai.
- **New Connection**: Path based ya host based routing rule match hone ke baad, ALB piche backend EC2 server ke sath ek naya alag TCP connection banata hai aur request wahan forward karta hai.

<br>

**Best Use-Cases**:
- **Websites & Web Apps**: Agar aap standard web application chala rahe hain jo HTTP/HTTPS traffic receive karti hai. Jab bhi koi user browser mein aapki website ka naam likhta hai (jaise ://example.com), toh unka computer aapki website ke server se connect karne ke liye HTTP ya HTTPS (secure) protocol ka use karta hai. ALB isi tarah ke traffic ko samajhne aur manage karne ke liye bana hai.
- **Microservices & Containers**: Jaisa humne pichli lab mein dekha, agar aap ek hi URL mein alag alag path ke through alag web application use karna chte hain. Jaise ```/blog``` ko alag server par aur ```/app``` ko alag server par bhej rahe hain (Path/Host-Based Routing).
- **Sticky Sessions**: Jab aapko user ki login state ko kisi ek specific server se baandh kar rakhna ho (Cookie-based stickiness).

<br>

**ALB Ki Advanced Capabilities**:
- **AWS WAF Integration**: ALB ke aage aap AWS Web Application Firewall (WAF) laga sakte hain jo SQL Injection ya Cross-Site Scripting (XSS) jaise cyber attacks ko website tak pahunchne se pehle hi block kar deta hai.
- **User Authentication**: ALB ke paas power hai ki woh user ko backend server par bhejne se pehle hi login check karwa sake (jaise Google, Cognito, ya corporate OIDC ke zariye integration).
- **Smart Health Checks**: ALB sirf yeh nahi dekhta ki server chalu hai ya nahi. Yeh server par ek specific URL (jaise /healthcheck) hit karke check karta hai ki kya server 200 OK ka response code de raha hai ya nahi.

<br>
<br>
<br>

### 2. Network Load Balancer (NLB) Kab Use Karte Hain?

NLB request ke andar ka application data read nahi kar sakta. Woh sirf packet ke network parameters (Source IP, Destination IP, Port) ko dekhta hai aur traffic ko aage throw (forward) kar deta hai. Yeh bina kisi checking ke super-fast raw performance deta hai.

<br>

**Yeh Kaam Kaise Karta Hai?**
- **No Termination (Passthrough)**: NLB packet ko kholti nahi hai aur na hi connection terminate karti hai. Yeh seedhe raw packets ko uthati hai aur piche chal rahe servers par flow-hash algorithm ke zariye bypass kar deti hai.
- **Preserve Source IP**: Kyunki yeh packet ke sath chhedchhad nahi karti, backend EC2 server ko direct user ka asli public IP address dikhta hai. (ALB ke case mein server ko load balancer ka private IP dikhta hai).

<br>

**Best Use-Cases**:
- **Ultra-High Traffic Environments**: Agar aapki application par sudden millions of requests per second ka traffic spike aata hai (jaise online gaming servers, flash sales, ya real-time stock trading systems). ALB itne heavy extreme traffic par load-scaling mein thoda time leta hai, par NLB bina rukaawat handle kar leta hai.
- **Non-HTTP Protocols**: Agar aapki application web traffic ke alawa koi aur protocol use karti hai, jaise SFTP, SMTP (Email servers), MQTT (IoT devices), ya Database Replication (TCP traffic) ko easily handle kar leta hai.
- **Whitelisting (Static IP Requirement)**: Kuch corporate clients ya third-party firewalls ki demand hoti hai ki hum sirf ek fixed IP address se hi traffic accept karenge. Kyunki ALB ki IPs badalti rehti hain, wahan hum NLB use karte hain aur use ek Elastic IP (Permanent IP) de dete hain.

<br>

**NLB Ki Advanced Capabilities**:
- Static & Elastic IPs: Jab aap NLB banate hain, toh uske har Availability Zone ko ek permanent fix Static IP milta hai. Aap apna khud ka Elastic IP bhi de sakte hain. Yeh tab sabse zaroori hota hai jab badi banks ya financial clients apne firewalls mein kisi fixed IP ko whitelist karna chahte hain.
- Extreme Scaling: ALB ko jab heavy traffic milta hai, toh use scale hone (bada hone) mein thoda samay lagta hai. NLB bina kisi scaling delay ke, sudden spikes (jaise 0 se sudden millions of requests per second) ko microseconds ki latency mein handle kar leta hai.
- AWS PrivateLink Support: Agar aapko apni service ko kisi doosre company ke VPC ke sath bina internet ke securely connect karna hai, toh aap sirf NLB ka use karke hi VPC Endpoint Service (PrivateLink) bana sakte hain. ALB ise natively support nahi karta.

<br>
<br>

### Production Summary

Agar aapka sawal "Website URL aur HTTP smart routing" se juda hai, toh aankh band karke ALB chunein.

Agar aapka sawal "Speed, Latency, Millions of Requests, TCP/UDP Connection, aur Static IP" se juda hai, toh aankh band karke NLB chunein.
