# Gateway Load Balancer Overview

Gateway Load Balancer ALB aur NLB se bilkul different hota hai. Ye application traffic ko serve karne ke liye nhi hota.

Gateway Load Balancer traffic ko inspect karne ke liye hota hai matlab traffic mein koi vulnerabilities to nhi hain.

<br>

Gateway Load Balancer (GWLB) ek Layer 3/Layer 4 load balancer hai jo network traffic ko third-party virtual security appliances, jaise Firewalls, IDS aur IPS ke paas bhejta hai, taaki traffic inspect hone ke baad hi apni destination tak pahunch sake.

Iska matlab hai ki Gateway Load Balancer source aur destination beech mein rehta hai aur network traffic ko destination par pahuchne se pehle third-party security services ko behjta hai, wahan se traffic ko inspect karwake fir destination par bhejta hai.

Security appliances, Jaise:
- Firewall.
- Intrusion Detection System (IDS).
- Intrusion Prevention System (IPS).
- Deep Packet Inspection (DPI).
- Malware Inspection.
- Network Monitoring Appliances

In sab se traffic ke packet ko inspect karwake fir destination par bhejta hai.

<br>

**Diagram**:

<img src="https://drive.google.com/uc?export=view&id=1gbcIEp-mb4wg07kCqRUIhpIo3zlHDGeJ" width="850" height="430">

<br>

Gateway Load Balancer ek aisa AWS Load Balancer hai jo network traffic ko security appliances tak bhejta hai, un security appliances ke beech load distribute karta hai aur traffic ko inspect karwane ke baad usse uske destination tak forward karta hai.

<br>
<br>

### Sabse pehle problem samajhte hain

Maan lo tum ek Bank mein DevOps Engineer ho.

Bank ki Application AWS mein chal rahi hai.

Architecture.
```
Internet

↓

Application Load Balancer

↓

EC2 Web Servers

↓

Database
```
Sab kuch sahi chal raha hai.

Lekin Bank ki Security Team ek nayi requirement deti hai. Wo bolti hai.
- ```Har request ko pehle Security Firewall ke through jana chahiye.```
- ```Har packet ko Malware Scanner check kare.```.
- ```Intrusion Detection System (IDS) bhi har traffic inspect kare.```

Yaani har packet ko application tak pahunchne se pehle inspect karna hai.

<br>

**Sawal**:

Ab Internet se jo request aa rahi hai. Usko kaise ensure karoge ki wo pehle Firewall ke paas jaye? Phir IDS ke paas jaye. Phir IPS ke paas jaye.

Aur uske baad hi Application Server tak pahunch paye. Agar tum iske liye manually routes banaoge.

To architecture bahut complex ho jayega.

<br>

**AWS ne kya socha?**

AWS Engineers ne dekha.

Customers third-party Security Appliances use kar rahe hain.

Jaise.
- Palo Alto Firewall
- Fortinet Firewall
- Check Point Firewall
- Cisco Firewall
- Suricata IDS
- Trend Micro
- F5 Appliances

Lekin in Appliances ke saamne traffic distribute karna bahut difficult tha. Isliye AWS ne **Gateway Load Balancer** introduce kiya.

<br>
<br>

### Gateway Load Balancer ki jarurat kyu padi?

Jab cloud computing tezi se badha, toh badi companies ne apne servers AWS par move karna shuru kar diye. Lekin, unke paas ek sabse bada challenge tha: Security aur Traffic Inspection.

Purane tareeqon mein jo dikkaten aati thin aur unko solve karne ke liye GWLB ki zaroorat kyu padi.

**1. Traditional Firewalls ko Cloud Mein Scale Karna Mushkil Tha**:

Pehle jab companies ko AWS mein third-party firewalls (jaise Palo Alto, Check Point, Fortinet) setup karne hote the, toh unhe har ek firewall ko alag se manage karna padta tha.
- **Pareshani**: Agar aapke website par traffic achanak badh gaya, toh aapko firewall servers (EC2 instances) ki quantity bhi badhani padegi. Lekin purane Application Load Balancer (ALB) ya Network Load Balancer (NLB) third-party firewalls ke traffic ko sahi tarike se balance aur scale nahi kar paate the.
- **GWLB ka Solutio**n: GWLB ne firewalls ko "bump-in-the-wire" ki tarah treat kiya. Yeh traffic ke load ke hisab se firewall appliances ko automatic scale-up ya scale-down kar sakta hai, bina kisi manual setting ke.

<br>

**2. IP Routing aur Complex Architecture**:

GWLB se pehle, agar aapko do ya teen VPCs (Virtual Private Clouds) ka traffic kisi central firewall se guzarna hota tha, toh routing tables bohot complex ho jaati thin.
- **Pareshani**: Har ek VPC mein alag-alag complex route tables likhni padti thin. Agar ek firewall down ho jaye, toh poora ka poora network rasta (routing) toot jata tha. Isko engineers "Spaghetti Routing" kehte the kyunki network ka naksha bohot ulajh jata tha.
- **GWLB ka Solution**: GWLB ne Gateway Load Balancer Endpoints (GWLBE) ka concept laya. Ab aapko sirf ek simple endpoint ka rasta dikhana hota hai. Baaki saara complex routing ka kaam backend par GWLB khud sambhal leta hai.

<br>

**3. Original Traffic Packet Data Khone Ka Darr**:

Security firewalls ka ek niyam hota hai: Jis raste se request aayi hai, reply bhi usi raste se jana chahiye. Isko Symmetric Routing kehte hain.
- **Pareshani**: Purane load balancers traffic ko transparently pass nahi karte the. Woh source IP ko badal dete the (NATing kar dete the). Isse firewall ko yeh pata hi nahi chalta tha ki traffic sach mein kahan se aa raha hai aur kis asli server ke liye ja raha hai. deep packet inspection (DPI) karna namumkin ho jata tha.
- **GWLB ka Solution**: GWLB ne **GENEVE** Protocol ka use kiya. Yeh original traffic packet ko ek special wrapper (encapsulation) mein lapet kar firewall ke paas bhejta hai. Firewall packet ko open karta hai, inspect karta hai, aur wapas usi wrapper mein daal kar GWLB ko de deta hai. Isse original Source IP, Destination IP, aur Port bilkul safe rehte hain aur badalte nahi hain.

<br>

**4. High Availability aur Failover ka Na Hona**:

Company ke business ke liye network ka har waqt chalu rehna zaroori hai.
- **Pareshani**: Agar aapne ek single firewall lagaya aur woh crash ho gaya, toh aapka poora business thapp ho jayega (Single Point of Failure). Agar aap do firewall lagate the, toh unke beech mein traffic switch karne ka active-passive script likhna padta tha, jismein failover hone mein 1 se 2 minute ka time lag jata tha.
- **GWLB ka Solution**: GWLB har waqt aapke firewalls ka health check karta rehta hai. Jaise hi koi ek firewall appliance kharab ya freeze hota hai, GWLB millisecond ke andar traffic ko dusre healthy firewall par bhej deta hai. Users ko pata bhi nahi chalta ki piche koi dikkat aayi thi.

<br>
<br>

### Gateway Load Balancer ka purpose

Traffic ko multiple security appliances ke beech distribute karna aur unke through transparent inspection karwana.

Gateway Load Balancer ka kaam hai.
- Traffic Receive karna.
- Security Appliances mein distribute karna.
- Health Check karna.
- Same appliance ke through return traffic bhi bhejna.
- Network ko transparent rakhna.

<br>

**Transparent ka matlab kya hota hai?**

Application Server ko pata bhi nahi hota ki packet pehle Firewall ke paas gayi thi.

Client ko bhi pata nahi hota.

Firewall silently background mein inspection karta rehta hai.

<br>
<br>

### Agar Gateway Load Balancer na ho

Suppose company ke paas 5 Firewalls hain.
```
Internet

↓

Firewall-1

↓

Application
```
Abhi sirf ek hi firewall use ho rha hai. 

Thodi der baad traffic badh gaya. Ek Firewall overload ho gaya. Ab kya karoge?

Doosra Firewall manually use karna padega.

Ye scalable nahi hai.

**Gateway Load Balancer ke saath**:

Architecture kuch aisa ban jata hai.
```
Internet

↓

Gateway Load Balancer

↓

 ┌──────────────┐
 │ Firewall-1   │
 │ Firewall-2   │
 │ Firewall-3   │
 │ Firewall-4   │
 └──────────────┘

↓

Application
```
Ab Gateway Load Balancer automatically traffic ko sab Firewalls mein distribute karega.

<br>
<br>

### Firewall ke saath network architecture kaisa hota hai?

<br>

**Sabse pehle ek normal architecture dekhte hain (Without Firewall)**:

Maan lo tumhari company ka naam ABC Pvt Ltd hai.

Tumhari website hai:
```
www.abc.com
```

Architecture kuch aisa hai.
```
Internet

      │
      ▼
Application Load Balancer

      │
      ▼
Private EC2 Servers

      │
      ▼
Database
```

Yaha traffic ka flow hai
```
Internet

↓

ALB

↓

EC2

↓

Database
```
Yaha koi Firewall nahi hai. Sirf Security Groups hain.

<br>

**Lekin company ki Security Team bolti hai**:

Security Team kehti hai
- "Hume sirf Security Group par trust nahi karna."

Wo chahte hain ki Har packet:
- Virus scan ho.
- Malware scan ho.
- SQL Injection detect ho.
- DDoS detect ho.
- IPS check kare.
- IDS monitor kare.
- Deep Packet Inspection ho.

Ab problem ye hai ki ALB ye sab kaam nahi karta. EC2 bhi ye kaam nahi karta.

<br>

**Isliye company Firewall deploy karti hai**:

Ab architecture kuch aisa ban jata hai.
```
Internet

↓

Firewall

↓

ALB

↓

EC2
```
Ab har packet pehle Firewall ke paas jayega. Firewall decide karega Safe hai ya dangerous.

<br>

**Lekin ek Firewall hi kyu?**:

Production mein ek hi Firewall kabhi nahi hota.

Maan lo tumhare paas 50 lakh users hain.

Ek Firewall itna traffic handle nahi kar sakta. Isliye multiple Firewalls deploy kiye jaate hain.

Example
```
Firewall-1

Firewall-2

Firewall-3

Firewall-4
```
Ye saare same configuration wale Firewalls hote hain.

<br>

**Ye Firewalls kahan deploy hote hain?**:

Ye bhi EC2 instances ki tarah deploy hote hain.

Example
```
Security VPC

Private Subnet A

↓

Palo Alto Firewall

Private Subnet B

↓

Palo Alto Firewall
```

Ya
```
Fortinet Firewall

↓

EC2 Instance
```
Ya
```
Check Point Firewall

↓

EC2 Instance
```
Dhyan do.

Firewall AWS ka resource nahi hota. Firewall actually ek Third Party Virtual Appliance hota hai.

Jo Marketplace se launch hota hai. Bilkul EC2 ki tarah.

**Example**:
```
AWS Marketplace

↓

Palo Alto VM-Series

↓

Launch

↓

EC2 ban gaya

↓

Ab ye Firewall ki tarah behave karega.
```

Yaani andar Linux nahi chal raha. Andar Palo Alto ka Firewall Operating System chal raha hai.

<br>

**Ab problem**:

Humne AWS marketplace se same configuration ke 4 Firewalls create kiye.
```
Firewall-1

Firewall-2

Firewall-3

Firewall-4
```

Internet se traffic aa raha hai. Kaun decide karega, Kaunsi Firewall packet inspect kare?

<br>

**Yahi kaam Gateway Load Balancer karta hai**:

Architecture
```
Internet

↓

Gateway Load Balancer

↓

Firewall-1

Firewall-2

Firewall-3

Firewall-4

↓

ALB

↓

EC2
```
Ab Gateway Load Balancer Traffic ko Sab Firewalls mein distribute karega.

<br>

**Real Request Flow**:

Suppose Puneet browser open karta hai.
```
www.abc.com
```
Request
```
Internet

↓

Gateway Load Balancer
```

Gateway Load Balancer bolta hai

"Ye request Firewall-2 inspect karegi."
```
GWLB

↓

Firewall-2
```

Firewall inspect karta hai.

Agar request safe hai. Packet wapas GWLB ko jata hai, for GWLB ALB ko bhejta hai, fir ALB packet ko EC2 ko behjta hai.

Agar Malware mila firewall bolta hai
```
DROP
```
Packet wahi khatam. Application ko pata bhi nahi chalta.

<br>
<br>

### Components of Gateway Load Balancer

AWS Gateway Load Balancer (GWLB) ke poore architecture ko samajhne ke liye hamein yeh dekhna hoga ki iske components physically ya logically kahan deploy hote hain (Location) aur unka exact technical operation (Working) kya hota hai.

Badi companies security ko centralize karne ke liye hamesha **Two-VPC Architecture** ka use karti hain:
- **Application VPC (Consumer VPC)**: Jahan aapki real website ya database chal raha hota hai.
- **Security VPC (Provider VPC)**: Jahan saare firewalls aur GWLB ka main setup hota hai.

<br>

**1. Gateway Load Balancer Endpoint (GWLBE)**:

Yeh aapke application waale network (VPC) aur security waale network (VPC) ke beech mein ek bridge (pull) ka kaam karta hai.

Kyuki application vpc aur security vpc dono alag hain, to application vpc se traffic ko secrity vpc mein bhejne ka kaam isi component ka hota hai. 

**Kahan Laga Hota Hai (Location)**:
- Yeh Application VPC ke public aur private subnets ke andar laga hota hai.
- Yeh ek Elastic Network Interface (ENI) ki tarah aapke subnets mein dikhta hai.

**Kaise Kaam Karta Hai**:
- Jab internet se koi traffic Internet Gateway (IGW) par aata hai, toh Route Table ki settings ke mutabiq use sabse pehle is GWLBE par bheja jata hai.
- GWLBE us traffic ko AWS PrivateLink (internal dark fiber network) ke zariye seedhe doosre VPC mein baithe GWLB ke paas transfer kar deta hai, bina internet par expose kiye.

Example: 

Aapka ek ```App-VPC``` hai jahan aapki website host hai, aur ek ```Security-VPC``` hai jahan saare firewalls hain. Aap App-VPC ke andar ek GWLBE banayenge. Jab bhi koi user website open karega, traffic pehle is GWLBE door ke andar enter karega, wahan se woh check hone ke liye doosre VPC ke firewall par chala jayega.

<br>

**2. Gateway Load Balancer (GWLB)**:

Ye ek load balancer hota hai jo Gateway Load Balancer Endpoint se aaye hue traffic ko firewalls par distribute karta hai.

**Kahan Laga Hota Hai (Location)**:
- Yeh Security VPC ke ek dedicated subnet (GWLB Subnet) ke andar laga hota hai.

**Kaise Kaam Karta Hai**:
- Jaise hi GWLBE se traffic iske paas aata hai, yeh check karta hai ki iske piche kaun-kaun se firewall targets active hain.
- Yeh incoming traffic ko leta hai aur use piche maujood multiple security firewalls par barabar baant (load balance) deta hai.
- Yeh continuously check karta rehta hai ki saare firewalls sahi se kaam kar rahe hain ya nahi (Health Checks).
- Agar traffic badhta hai, toh yeh auto-scaling ko trigger karke naye firewall instances add karwa deta hai.

Example:

Maal lijiye aapke web server par achanak 10,000 requests aa gayin. GWLB un saari requests ko pakdega aur unhe aapke backend mein chal rahe 3 alag-alag Palo Alto firewalls par 3333 requests ke hisab se distribute kar dega, taaki kisi ek firewall par load na aaye.

<br>

**3. Target Group / Security Appliances**:

Target Group un virtual servers (EC2 instances) ka ek group hota hai jahan aapne third-party security softwares ya firewalls install kiye hote hain. GWLB hamesha isi Target Group ko traffic forward karta hai

**Kahan Laga Hota Hai (Location)**:
- Yeh bhi Security VPC ke andar hota hai, lekin aksar ek alag subnet (Data/Firewall Subnet) mein EC2 instances ke roop mein chal raha hota hai.

**Kaise Kaam Karta Hai**:
- Yeh instances GWLB ke Target Group mein registered hote hain.
- Inka kaam asli inspection karna hai. Yeh dekhte hain ki packet ke andar koi virus, malware, ya hackers ka attack code toh nahi hai.
- Agar packet safe hai, toh yeh use 'ALLOW' kar dete hain. Agar packet dangerous hai, toh yeh use wahin par 'DROP' (block) kar dete hain.

Example: 

Aapne Fortinet ya Check Point ke 3 EC2 instances banaye aur unhe ek Target Group mein daal diya. Jab GWLB packet bhejega, toh yeh instances packet ko deeply scan karenge. Agar kisi packet mein SQL Injection ka attack dikha, toh yeh appliance use wahin khatam kar dega, woh aapke main application server tak kabhi pahunch hi nahi payega.

<br>
<br>

### GENEVE Protocol

GENEVE (Generic Network Virtualization Encapsulation) protocol ek advanced Layer 3 network virtualization aur encapsulation technology hai. Ise IETF (Internet Engineering Task Force) ne design kiya hai, aur yeh modern software-defined networking (SDN) aur cloud infrastructure (jaise AWS Gateway Load Balancer) ka ek core foundation hai.

GWLB ke context mein, GENEVE protocol ka primary validation tunnel endpoints ke beech bina original network payload ya packet headers ko modify kiye, variable metadata transport karna hai.

GENEVE ka kaam hai. Original packet ko encapsulate karna. Matlab packet ke uper apni kuch information add karna. Aur Security Appliance tak safely bhejna. Inspection complete hone ke baad packet ko decapsulate kiya jata hai.

<br>

**GENEVE Protocol Kya Hai aur Iski Zaroorat Kyu Thi?**

Maan lo tumhare AWS VPC mein ek Application chal rahi hai.

Architecture.
```
Internet

↓

Gateway Load Balancer

↓

Firewall Appliance

↓

EC2 Application
```
Ab Client ek HTTP Request bhejta hai.

Suppose.
```
Client IP

43.205.10.15
```
Destination.
```
EC2

10.0.1.25
```
Ab Gateway Load Balancer ko ye packet Firewall ke paas bhejni hai.

<br>

**Ab sawal**:

Gateway Load Balancer packet ko Firewall tak kaise bheje? Kya original packet ko waise hi bhej de?

Sunne mein sahi lagta hai. Lekin isme bahut badi problem hai.

<br>

**Problem kya hai?**

Suppose Original Packet kuch aisi hai.
```
Source IP
43.205.10.15

Destination IP
10.0.1.25
```
Firewall packet receive karti hai. Inspection karti hai.

Ab Firewall ko packet wapas Gateway Load Balancer ko bhejni hai. Aur fir application wale EC2 tak pahunchani hai.

Lekin Firewall ko kaise pata chalega. 
- Ye packet kis Gateway Load Balancer se aayi thi?
- Kaunsi session ki packet hai?
- Return traffic kis path se bhejni hai?

Original packet mein ye information hi nahi hai. Yaani packet ke saath extra metadata bhi bhejna zaruri hai.

Isi problem ko solve karne ke liye **GENEVE protocol** bana. 

<br>

GENVE Protocol simply data packet ke uper ek GENEVE Header add kar deta hai.

<br>

**Sabse pehle ek normal packet dekhte hain**:

Maan lo tumhare laptop ka IP hai.
```
43.205.10.15
```
Aur tum AWS mein ek Web Server access kar rahe ho.

Web Server ka IP hai.
```
10.0.1.25
```

Browser request bhejta hai.
```
GET /login HTTP/1.1
Host: example.com
```

Network layer par packet kuch aisi hogi.
```
Source IP      : 43.205.10.15

Destination IP : 10.0.1.25

Protocol        : TCP

Destination Port: 443
```
Ye ek normal IP packet hai.

<br>

**Ab Gateway Load Balancer beech mein aa gaya**:

Architecture.
```
Client

↓

Gateway Load Balancer

↓

Firewall

↓

Web Server
```
Ab Gateway Load Balancer ko ye packet Firewall tak bhejni hai. Lekin sirf original packet bhejna enough nahi hai.

<br>

**Kyun enough nahi hai?**

Socho tum ek Courier Company ho.

Tumhare paas sirf ye information hai.
```
Sender
Rahul

Receiver
Puneet
```
Lekin Courier Hub ko aur bhi information chahiye.

Jaise.
- Tracking ID
- Parcel Number
- Priority
- Delivery Zone

Agar ye information nahi hogi. To Courier Company parcel ko correct location par nhi bhej payegi aur efficiently kaam nahi kar paayegi.

Gateway Load Balancer ke saath bhi exactly yehi problem hai.

<br>

**GWLB ko extra information chahiye**:

Gateway Load Balancer ko packet ke saath kuch additional information bhi bhejni hoti hai.

Jaise.
- Ye packet kis flow ki hai.
- Kis Security Appliance ko bhejna hai.
- Return traffic kis appliance se aani chahiye.
- Session ka identifier kya hai.
- Virtual network ki information.

Ye information original IP packet mein nahi hoti.

<br>

**To Gateway Load Balancer kya karta hai?**

Gateway Load Balancer yahan GENVE protocol ko use karta hai.

Ye original packet ko change nahi karta. Ye us packet ke bahar ek naya wrapper laga deta hai.

Isi wrapper ko GENEVE Header kehte hain.

Step-1 Original Packet
```
+--------------------------------------+
| Source : 43.205.10.15                |
| Destination : 10.0.1.25              |
| TCP/443                              |
| HTTP Data                            |
+--------------------------------------+
```
Ye packet waise ki waise rehti hai.

Step-2 GWLB Encapsulation karta hai

Ab Gateway Load Balancer packet ke bahar ek aur packet bana deta hai.

Diagram.
```
+----------------------------------------------+
| Outer IP Header                              |
+----------------------------------------------+
| UDP Header                                   |
+----------------------------------------------+
| GENEVE Header                                |
+----------------------------------------------+
| Original Packet                              |
|  Source : 43.205.10.15                       |
|  Destination : 10.0.1.25                     |
|  TCP                                         |
|  HTTP                                        |
+----------------------------------------------+
```

Dhyan do. Original packet ko touch bhi nahi kiya gaya. Uske bahar sirf 3 naye headers add hue.
- Outer IP Header
- UDP Header
- GENEVE Header

Isi process ko **Encapsulation** kehte hain.

<br>

**Firewall packet receive karti hai**:

Ab Firewall ko packet milti hai. Wo sabse pehle dekhti hai.
```
Outer Header
```
Fir.
```
GENEVE Header
```
Usse pata chal jata hai.
- Ye packet kis flow ki hai.
- Kis session ki hai.
- Kis virtual network se aayi hai.

Ab Firewall outer wrapper hata deti hai.

Wrapper hatane ko kya bolte hain **Decapsulation**.

Wrapper ko hatate hi Original Packet mil gayi.

<br>

**Ab Firewall inspection karti hai**:

Ab uske paas normal packet aa gayi.
```
GET /login HTTP/1.1
```
Ab Firewall check karti hai.

Kya ye packet safe hai ya nhi?
- Isme SQL Injection hai?
- Cross Site Scripting hai?
- Malware hai?

Agar packet safe hai. Firewall usko coorect mark usse aage wapas GWLB ko behj deti hai.

Agar packet dangerous ho? Suppose Hacker ne request bheji.
```
GET /login?id=' OR 1=1 --
```
Firewall packet inspect karegi. Aur bolegi. Ye SQL Injection Attack hai.

Packet ko yahi drop kar do. Application tak jaane ki zarurat hi nahi.

<br>

**Return Traffic kaise aata hai?**

Data packet firwall se inspection ke baad web server tak pahuch jati hai.

Web Server request ka return response bhejta hai.
```
HTTP/1.1 200 OK
```
Ye response bhi Firewall ke through wapas aata hai.

Gateway Load Balancer GENEVE metadata ki madad se samajh leta hai ki ye response kis original client ke liye hai.

Fir GWLB outer header hata kar response client ko bhej deta hai.


<br>
<br>

### End-to-End Operational Example: Real-World Technical Flow

Chaliye ek exact technical enterprise scenario se samajhte hain jahan ek internet user corporate web-application ko access kar raha hai aur traffic central firewall se inspect ho raha hai.

**Scenario Specifications**:
- Client Machine IP: ```203.0.113.10```
- AWS Application Server ```IP: 10.0.1.50```
- AWS GWLB Internal IP: ```192.168.1.10```
- Firewall Instance IP: ```192.168.1.25```

<br>

**Step 1: Ingress Packet Arrival (At GWLBE)**

Bahar ka user request initiate karta hai. Internet Gateway (IGW) par packet capture hota hai. Ingress routing rules use GWLBE (Endpoint) par divert karte hain.

Packet State: ```[Source: 203.0.113.10]``` -> ```[Destination: 10.0.1.50]``` | ```[Payload: HTTP GET]```

<br>

**Step 2: AWS PrivateLink to GWLB**

GWLBE bina encapsulation ke AWS PrivateLink architecture ke infrastructure level virtualization se is packet ko safe-channel par GWLB tak route karta hai.

<br>

**Step 3: GENEVE Encapsulation by GWLB**

GWLB packet receive karta hai. Ab use yeh packet inspect karne ke liye Firewall Instance (```192.168.1.25```) ko forward karna hai. Yahan GWLB **GENEVE Encapsulation Engine** ko trigger karta hai:
- **Outer UDP Packet Creation**: Source IP banta hai ```192.168.1.10``` (GWLB) aur Destination IP banta hai ```192.168.1.25``` (Firewall). Port set hota hai 6081.
- **GENEVE Header Insertion**: Variable Option space mein GWLB do major metadata parameters inject karta hai:
  - Attachment ID / VNI: Yeh identify karne ke liye ki yeh traffic kis specific consumer endpoint (GWLBE-id) se generate hua hai.
  - Flow Cookie: Ek unique identification hash token jo stateful processing ke liye symmetric traffic path guarantee karta hai.
 
Final Encapsulated Format: 
```
[Outer IP: 192.168.1.10 -> 192.168.1.25]
[UDP: 6081] [GENEVE Header: GWLBE-ID, Cookie]
[Original Inner Packet: 203.0.113.10 -> 10.0.1.50 | HTTP GET]
```

<br>

**Step 4: Processing at Firewall Appliance**

Firewall instance Port 6081 par packet intercept karta hai.
- Firewall ka operating system system level driver level par GENEVE header ko parse (read) karta hai.
- Woh metadata parameters (GWLBE-ID) ko memory context mein drop karta hai taaki use segment/customer policy ka pata chale.
- Firewall inner packet (203.0.113.10 -> 10.0.1.50) ko extract karke use Deep Packet Inspection (DPI) engine mein pass karta hai bina koi Source NAT (SNAT) perform kiye.

<br>

**Step 5: Decapsulation and Return Loop**

Inspection positive clear hone par, Firewall inner packet ko tamper nahi karta.
- Firewall software back-encapsulation execute karta hai. Woh exact wahi GENEVE Header (vahi details aur cookie) dubara chipkata hai.
- Return Packet Structure: ```[Outer IP: 192.168.1.25 -> 192.168.1.10] [UDP: 6081] [GENEVE Header] [Inner Packet]```.
- Packet wapas GWLB ke network interface par land karta hai.

<br>

**Step 6: Final Delivery to Target**

GWLB GENEVE outer envelope ko permanently strip (remove) kar deta hai. Ab system ke paas sirf raw original packet bacha hai. GWLB use GWLBE gateway routing ke through destination web server 10.0.1.50 par drops kar deta hai. Web server ko un-altered traffic milta hai, jahan direct Client IP visible hoti hai.
