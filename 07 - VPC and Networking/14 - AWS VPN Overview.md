# AWS VPN Overview

AWS mein VPN ek service hai jo tumhari local device ko ya fir on-premises network ko AWS VPC ke saath securly connect karti hai.

Matlab VPN ke through data ek network se dusre network tak encrypt hoke jata hai. Isliye ye secure hota hai.

AWS VPC VPN (Virtual Private Network) ek aisi service hai jo aapke local computer, on-premises network (jaise aapka office, local data center, ya physical server room) aur aapke AWS VPC ke beech ek secure, encrypted tunnel banati hai public internet ke threw.

<br>

Jab koi company apna poora setup cloud par shift nahi karti aur kuch data apne local servers par rakhti hai aur kuch AWS par, toh is approach ko hum **Hybrid Cloud Architecture** kehte hain. Is hybrid environment mein security ke sath communication karne ke liye AWS VPN ka use sabse zyada kiya jata hai.

<br>
<br>

### Sabse pehle samjho ki mobile mein use hone wala VPN kya hota hai.

A mobile VPN (Virtual Private Network) is a mechanism that creates a secure and encrypted connection between your mobile device and a network.

Mobile mein use hone wala VPN bhi same kaam karta hai, ye mobile phone ko securly kisi network ke saath connect karta hai.

<br>
<br>

### How VPN Works?

Ye hum samjh rhe hain ki normal koi bhi VPN kaise kaam karta hai

When you connect to a VPN, here's what happens:

1 - Connection Established: You connects to a VPN server, through a vpn client application on your device such as Nord_VPN (client application on your device).

2 - Encryption: Your data is encrypted when it leaves your device.

3 - Data Transmission: The enrypted data is sent through vpn tunnel to the VPN server from your device (Tunnel is the secure pathway).

4 - Decryption: The VPN server decrypts your data and sends it to the destination on the internet.

5 - Response Transmission: Any data coming back to you follows the same process: it is encrypted by the VPN server, sent through the tunnel, and decrypted by your VPN client.

<br>
<br>

### Sabse pehle problem samajhte hain

Maan lo tumhari company ka ek Office hai. Us office ke andar bahut saare Servers hain.

Diagram dekho.
```
Company Office

|

------------------------

|          |           |

App      Database     File Server
```

Ye saare servers office ke andar building mein chal rahe hain. Inhe hum **On-Premises Servers** bolte hain.

Yaani company ke apne Data Center ke servers.

Ab company ne decide kiya ki kuch applications AWS mein migrate karni hain.

Isliye AWS mein ek VPC banayi gayi.
```
AWS

|

VPC

|

EC2

RDS

Application
```

Ab company ke paas do alag networks hain.
- Ek Office Network.
- Ek AWS VPC.

<br>

**Ab problem kya hai?**

Office ke Application Server ko AWS ke Database se baat karni hai Ya AWS ke EC2 ko Office ke Database se data lena hai.

Ab sawal hai, Ye communication kaise hogi?

<br>

**Pehla idea**:

Ek engineer bolega.

"Internet use kar lete hain."

Architecture.
```
Office

↓

Internet

↓

AWS VPC
```
Technically ye possible hai. Hum internet ke thorugh office ke network ko aws ke vpc ke saath communicate kar sakte hain.

Lekin company mana kar deti hai.

<br>

**Company mana kyun karti hai?**

Company ke paas bahut confidential data hai.

Jaise:
- Customer Details.
- Bank Account Numbers.
- Credit Card Information.
- Employee Salary Data.
- Medical Records.

Company nahi chahti ki ye data normal internet par travel kare. Kyunki internet ek public network hai.

Har data packet bahut saare routers aur ISPs se hokar jaata hai. Agar data encrypt na ho to koi bhi usse padh sakta hai.

<br>

**AWS ne kya solution diya?**

AWS ne kaha.

Internet ko hi use karo.

Lekin uske andar ek encrypted tunnel bana lo.

Isi secure tunnel ko VPN Tunnel kehte hain.

VPN ek encrypted tunnel hoti hai jo do alag networks ko public internet ke through securely connect karti hai.

Matlab do network apas mein connect ho jate hain aur data ko internet par encrypt karke ek dusre ko bhejte hain.

<br>
<br>

### Tunnel ka matlab kya hai?

Bahut log sochte hain ki AWS sach mein internet ke andar koi alag cable bana deta hai.

Nahi.

Tunnel ek logical concept hai.

Iska matlab hai.

Data ko encrypt karke packets ke andar wrap kar diya gaya hai.

Jo bhi packet internet par travel karega.

Uske andar ka data unreadable hoga.

<br>

**Example**:

Without VPN.
```
Name=Puneet

Salary=500000
```
Ye packet agar kisi attacker ne dekh liya.

To wo sab padh lega.

VPN ke saath.

Packet kuch aisa dikhega.
```
KJH8@#98HDF7SD8FSD87FSD8
```
Attacker packet dekh sakta hai.

Lekin samajh nahi sakta.

Kyunki packet encrypted hai.

<br>
<br>

### AWS VPN Kyun Zaroori Hai?

Humne padha hai ki VPN ke through on-premises network aur AWS VPC ko apas mein connect kiya jata hai.

**Problem**: Agar aap apne office se AWS VPC ke andar chal rahe private EC2 instances ko access karna chahte hain, toh normal internet ke throug nahi kar sakte kyunki private IPs internet par routable nahi hote. Agar aap unhein public IPs denge, toh hacking ka khatra badh jata hai.

**Solution**: AWS VPN internet ka hi use karta hai, lekin yeh aapke office aur AWS ke beech ek Encrypted Tunnel (crypto tunnel) bana deta hai. Is tunnel ke andar ka data koi third-party ya hacker read nahi kar sakta. Isko hum IPsec (IP Security) connection kehte hain.

<br>
<br>

### AWS VPN ke do major types

AWS mainly do types ke VPN provide karta hai.
- Site-to-Site VPN.
- Client VPN.

<br>

### 1 - Site-to-Site VPN

Jab aap on-premises network aur AWS ke VPC ko apas mein VPN ke through connect karte ho to usko Site-to-Site VPN kehte hain.

Matlab do networks ko connect karna ho to site-to-site vpn use karte hain.

Yeh connection Network-to-Network hota hai. Yeh tab use hota hai jab pure office ke network ko AWS se jodna ho taaki office mein baitha koi bhi computer AWS ke servers ko securly access kar sake.

Yeh connection tab use hota hai jab do poore ke poore networks ko aapas mein permanent jodna ho (Office Network + AWS Cloud Network).

- Always-On Connection: Yeh connection 24/7 active rehta hai. Office ka koi bhi employee bina kisi extra software ke AWS resources ko access kar sakta hai agar firewall rules allowed hain.
- Hardware Dependent: Iske liye aapke office mein ek proper router ya firewall device hona zaroori hai.

Example.
```
Office Network

↓

VPN

↓

AWS VPC
```

<br>

**Iske Core Components**:

Site-to-Site VPN ko chalane ke liye 3 main cheezein chahiye hoti hain:

**1.1 - Customer Gateway (CGW)**:
- Yeh aapke on-premises (office) network side ka physical router ya software appliance hota hai.
- AWS ko is device ka public IP address chahiye hota hai taaki woh isse connection establish kar sake.
- Examples: Cisco ASA, Juniper Networks, Fortinet FortiGate, ya Check Point firewalls.

**1.2 - Virtual Private Gateway (VGW) ya Transit Gateway (TGW)**:
- Yeh AWS side par lagne wala ek VPN concentrator hai.
- Yeh aapke VPC ke sath attach hota hai. Jab office se encrypted data aata hai, toh VGW usko decrypt karke VPC ke andar bhejta hai. Jab VPC se data office jana hota hai, toh VGW use encrypt karta hai.
- Ek Virtual Private Gateway sirf ek hi VPC ke sath attach ho sakta hai.

- Transit Gateway ka use tab karte hain jab ek hi VPN se multiple VPCs ko connect karna ho (Transitive Routing ki wajah se).
- Agar aapke paas 50 VPCs hain aur aap sabko apne office se connect karna chahte hain, toh 50 alag-alag VGW lagana mushkil ho jayega. Wahan par aap ek central Transit Gateway banate hain aur VPN ko directly TGW se connect kar dete hain. Isse saare VPCs ek hi VPN connection se connect ho jate hain.

**1.3 - VPN Tunnel**:
- Customer Gateway aur Virtual Private Gatewa/Transite Gateway ke beech jo encrypted rasta banta hai, use tunnel kehte hain.
- Yeh ek logical path hai jo internet ke upar banta hai jismein data encrypt hokar chalta hai.
-  Jab aap AWS par ek Site-to-Site VPN banate hain, toh AWS automatically do (2) alag-alag Tunnels create karta hai. Ye do tunnels high availability ke liye banai jaati hain. Yeh do alag alag AWS Availability Zones (data centers) par terminate hoti hain. Agar ek tunnel ya ek AWS data center down ho jaye, toh aapka traffic automatically dusri tunnel par shift ho jata hai. Isse connectivity breakdown nahi hoti.

<br>

**Routing Strategy**: 

Site-to-Site VPN configure karte waqt aapko routing ka tarika chunna hota hai:

**1. Static Routing**: 
- Aapko AWS mein manually likhna padta hai ki aapke office ka IP range kya hai (e.g., ```192.168.1.0/24```). Agar office mein koi naya network add hoga, toh aapko AWS mein aakar on-premises network ki IP range dobara manually update karna padega.
- Agar aapka office network chota hai aur IPs change nahi hote, toh aap manually network routes define karte hain.

**2. Dynamic Routing (BGP)**:
- Isme aapka office router aur AWS ka gateway BGP (Border Gateway Protocol) ke threw aapas mein automatic baat karte hain aur aws ke gateway mein office ki ip range automatically add ho jati hai. Agar office mein koi naya subnet banta hai, toh ip range apne aap AWS VPN table mein add ho jata hai.
- AWS hamesha BGP routing recommend karta hai.

<br>

### 2 - Client VPN

Jab aap apne local computer ko AWS VPC ke saath VPN ke through connect karte hain to usko client vpn kehte hain.

Yeh connection tab use hota hai jab pure office ko nahi, balki individual users ya remote employees ko AWS VPC ka secure access chahiye ho.

- Yeh ek managed client-based VPN service hai. Employees apne laptop ya mobile par ek OpenVPN client application install karte hain.
- Jab employee connect karta hai, toh AWS uski identity check karta hai (Active Directory, Okta, ya Certificates ke threw).
-  Connect hone ke baad, employee ghar baithe-baithe AWS ke private resources ko securely access kar sakta hai.

<br>
<br>

### AWS VPN mein Encryption kaise hoti hai? aur ye kaise kaam karta hai?

AWS VPN mein encryption ka poora process **IPsec (Internet Protocol Security)** framework par kaam karta hai. Yeh koi single protocol nahi hai, balki protocols ka ek poora group (suite) hai jo milkar data ko secure, private aur unalterable (jise badla na ja sake) banata hai.

Jab aapka data aapke office (Customer Gateway) se AWS (Virtual Private Gateway) ki taraf nikalta hai, toh encryption direct nahi shuru hoti. Iske peeche ek poora step-by-step cryptographic process hota hai.

**Part 1: Connection Setup (Sirf Ek Baar Hota Hai)**:

Kisi bhi data ko encrypt karne se pehle, dono sides (AWS aur Aapka office Router) ko aapas mein agreement karna padta hai ki wo kaun sa encryption formula aur kaun si keys use karenge. Iske liye **IKE (Internet Key Exchange)** protocol ka use hota hai.

**Step 1: Connection Request Inititate Hona**:
- Aapke office ka router (Customer Gateway) AWS ke VPN IP par ek request bhejta hai: "Mujhe tumse ek secure tunnel banani hai."

**Step 2: Identity Verify Karna (Authentication)**:
- Dono routers ek dusre se Pre-Shared Key (PSK) ya Certificates maangte hain. Agar dono side par password match ho jata hai, toh hi process aage badhta hai, nahi toh connection vahin reject ho jata hai.

**Step 3: Management Tunnel Banana (IKE Phase 1)**:
- Dono devices Diffie-Hellman (DH) algorithm ka use karke internet par ek temporary secret key aapas mein share karte hain. Is key se ek temporary secure raasta ban jata hai jise Management Tunnel kehte hain.

**Step 4: Real Data Tunnel Banana (IKE Phase 2)**:
- Ab us safe Management Tunnel ke andar baithkar dono routers real data ko encrypt karne ke rules decide karte hain. Yahan par final IPsec Security Association (SA) tunnel banti hai. Ab setup complete ho chuka hai aur real data transfer ke liye tunnel taiyar hai.

<br>

**Part 2: Real-Time Data Encryption**:

Maan lijiye ab aapke office ke kisi computer se AWS ke EC2 instance par data bheja ja raha hai. Ab har ek packet ke sath ye exact steps chalenge:

**Step 5: Packet ka Router Par Pahunchna**:
- Aapke office ke computer se ek normal data packet nikalta hai aur office ke main firewall/router par pahunchta hai. Is packet par likha hota hai: Source: Office Computer IP -> Destination: AWS EC2 Private IP.

**Step 6: Encryption Process (AES-256)**:
- Office ka router dekhta hai ki ye data AWS jana hai. Wo pure packet ko uthata hai aur **AES-256 bit encryption** algorithm lagakar use ek unreadable code (Ciphertext) mein badal deta hai. Ab iske andar ka data ya original private IPs koi nahi dekh sakta.

**Step 7: Encapsulation (ESP Header Lagana)**:
- Router is encrypted code ke aage ek ESP (Encapsulating Security Payload) Header aur peeche ek ESP Trailer laga deta hai. Yeh ek tarah se data ko ek naye secure lifafe (envelope) mein pack karne jaisa hai.

**Step 8: Naya Public IP Header Lagana**:
- Kyunki original private IPs encrypt ho chuki hain, isliye internet routers ko rasta dikhane ke liye office ka router ek naya Public IP Header upar se chipka deta hai. Is naye header par likha hota hai: Source: Office Public IP -> Destination: AWS VPN Public IP.

**Step 9: Tampering Check Lagana (Hashing/HMAC)**:
- Packet ke sabse end mein ek digital signature (Hash value) lagaya jata hai SHA-256 ka use karke. Isse ye guarantee milti hai ki internet par travel karte waqt koi hacker packet mein koi badlav na kar sake.

**Step 10: Internet par Travel**:
- Ab ye fully packed aur secure packet aapke office se nikal kar public internet ke throug AWS ki taraf travel karta hai. Agar raste mein koi ise chura bhi le, toh use andar ka kuch samajh nahi aayega.

**Step 11: AWS Par Entry aur Verification**:
- Packet AWS ke Virtual Private Gateway (VGW) par pahunchta hai. AWS sabse pehle Step 9 wale digital signature ko check karta hai. Agar signature sahi hai (matlab packet ke sath koi chhedchhad nahi hui), toh AWS use accept karta hai.

**Step 12: Decryption aur Delivery**:
- AWS ka gateway apni secret key ka use karke packet ko decrypt karta hai (lifafa kholta hai). Naya public header aur ESP header hat jata hai aur original packet bahar aa jata hai. Ab AWS ko original private IP dikh jati hai aur wo data ko VPC ke andar sahi EC2 instance tak delivered kar deta hai.

<br>
<br>

### Site-to-Site VPN kaise setup karte hain?

AWS Site-to-Site VPN ko complete setup karne ke liye in steps ko follow karein:

**Step-1: AWS Side Par Prerequisites Tyar Karein**:
- Pehle check karein ki aapka AWS VPC ready hai aur usme subnets baney hue hain.
- Aapko apne on-premises firewall/router ka Public IP Address pata hona chahiye.

**Step-2: Customer Gateway (CGW) Create Karein**:
- AWS Management Console mein VPC Dashboard par jayein.
- Left sidebar mein Customer Gateways par click karein aur Create Customer Gateway choose karein.
- Ek naam dein aur IP Address field mein apne office firewall ka Public IP dalein.
- Routing mein Dynamic (BGP use karne ke liye) ya Static choose karein.

**Step-3: Virtual Private Gateway (VGW) Create Aur Attach Karein**:
- Left sidebar mein Virtual Private Gateways par click karein.
- Create Virtual Private Gateway par click karein, naam dein aur create karein.
- Ab us VGW ko select karke Actions par jayein aur Attach to VPC par click karke apna VPC select karein.

**Step-4: VPN Connection Create Karein**:
- Left sidebar mein Site-to-Site VPN Connections par jayein.
- Create VPN Connection par click karein.
- Target Gateway Type mein Virtual Private Gateway select karein aur apna VGW choose karein.
- Customer Gateway mein Existing select karke apna banaya hua CGW select karein.
- Routing options mein apne on-premises network ka IP range (CIDR) dalein (agar static routing select ki thi).
- Create VPN Connection par click karein. Isko Available hone mein 5-10 minutes lagenge.

**Step-5: Configuration File Download Karein**:
- Jab VPN Connection ka status Available ho jaye, toh use select karein.
- Upar Download Configuration button par click karein.
- Apne on-premises firewall ka Vendor (jaise Cisco, Fortinet, etc.) aur Software Version select karke file download karein. Is file mein pre-shared keys aur AWS tunnel ke IP addresses hote hain.

**Step-6: On-Premises Firewall Configure Karein**:
- Download ki gayi configuration file ko open karein.
- Apne office ke firewall/router dashboard mein login karein.
- File mein diye gaye instructions ke mutabik IPsec Tunnels, Pre-shared keys, Phase 1 aur Phase 2 policies ko configure karein.

**Step-7: Route Tables Aur Security Groups Update Karein**:
- AWS Route Tables: Apne VPC ke Route Table mein jayein. Route Propagation tab par click karke Edit route propagation karein aur Enable par check lagayein. Isse AWS aapke office ke routes automatic sikh lega.
- Security Groups: Apne AWS EC2 instances ke Security Group mein on-premises network ke IP range (CIDR) se traffic allow (Inbound Rules) karein.

**Step-8: Verifying the Connection**:
- Setup complete hone ke baad AWS VPN dashboard mein Tunnel Details tab check karein.
- Dono tunnels ka status UP dikhna chahiye.
- Aap apne office ke kisi computer se AWS ke private IP ko ping karke connection test kar sakte hain.

<br>
<br>

### AWS Client VPN ka Working Flow

**Step 1 : Administrator Client VPN Endpoint create karta hai**:

Sabse pehle AWS Administrator VPC ke andar ek Client VPN Endpoint create karta hai.

Ye endpoint ek VPN server ki tarah kaam karta hai.

Example:
```
AWS

↓

Client VPN Endpoint

↓

Associated VPC
```
Ye endpoint remote users ki VPN requests receive karta hai.

<br>

**Step 2 : Authentication Configure ki jati hai**:

Ab administrator decide karta hai ki users ko authenticate kaise karna hai. AWS Client VPN multiple authentication methods support karta hai.

For example:
- Active Directory.
- SAML Identity Provider.
- Mutual Certificate Authentication

Maan lo company Microsoft Active Directory use karti hai.

Employee login karega.
```
Username

puneet

Password

********
```
AWS verify karega ki user valid employee hai ya nahi. Agar authentication successful ho gayi to VPN connection allow hoga.

<br>

**Step 3 : User VPN Client Install karta hai**:

Employee apne laptop par AWS VPN Client software install karta hai. Ye software VPN connection establish karta hai.

Example:
```
Laptop

↓

AWS VPN Client

↓

Internet
```
User VPN profile import karta hai. Uske baad Connect button press karta hai.

<br>

**Step 4 : Secure Tunnel Create hoti hai**:

Jaise hi user Connect karta hai, Laptop aur AWS Client VPN Endpoint ke beech ek encrypted tunnel create hoti hai.
```
Laptop

↓

Encrypted Tunnel

↓

AWS Client VPN Endpoint
```
Ab Internet ke through jo bhi data jayega wo encrypted hoga.

<br>

**Step 5 : User Authenticate hota hai**:

AWS Client VPN user ki identity verify karta hai.

Example:
```
Laptop

↓

Username Password

↓

AWS Client VPN Endpoint

↓

Authentication Successful
```
Agar credentials galat hain
```
Access Denied
```
Agar sahi hain
```
VPN Connected
```

<br>

**Step 6 : Client ko IP Address milta hai**:

Connection establish hone ke baad AWS Client VPN user ko ek Client CIDR Range se IP address assign karta hai.

Example:
```
Client CIDR

192.168.100.0/22

↓

Laptop

192.168.100.10
```
Ye IP temporary hota hai. VPN disconnect hote hi release ho jata hai.

<br>

**Step 7 : Route Table Check hoti hai**:

Ab AWS dekhta hai ki user kis network ko access kar sakta hai.

Example
```
Destination

10.0.0.0/16

↓

Target

VPC
```
Matlab

Agar user ```10.0.1.15``` wale EC2 ko access karega. Traffic VPN Tunnel ke andar jayega.

<br>

**Step 8 : Authorization Rules Check hoti hain**:

Sirf VPN connect hona hi kaafi nahi hota. AWS Authorization Rules bhi check karta hai.

Example

Developer Team
```
Allowed

10.0.1.0/24
```
Finance Team
```
Allowed

10.0.2.0/24
```
Ab agar Developer Finance subnet access karega AWS us request ko reject kar dega.

<br>

**Step 9 : Traffic Encrypt hokar AWS pahunchta hai**:

Ab user jab EC2 access karega
```
SSH

↓

Laptop

↓

Encrypted VPN Tunnel

↓

Client VPN Endpoint

↓

VPC

↓

EC2
```
Internet ke upar kabhi bhi plain data travel nahi karta. Har packet encrypted hota hai.

<br>
<br>

### Client VPN kaise setup karte hain?

AWS Client VPN ko complete setup karne ke liye in main steps ko follow karein:

**1. Certificates Generate Karein (Mutual Authentication Ke Liye)**:
- Agar aap active directory use nahi kar rahe hain, toh server aur client certificates sabse aasan tareeqa hain.
- Apne local machine par OpenVPN easy-rsa tool ka use karke Server aur Client certificates generate karein.
- In certificates ko AWS Certificate Manager (ACM) mein upload karein. Aapko ek Server Certificate ARN aur ek Client Certificate ARN mil jayega

**2. Client VPN Endpoint Create Karein**:
- AWS Console mein VPC Dashboard par jayein aur Client VPN Endpoints select karein.
- Create Client VPN Endpoint par click karein.
- Client IPv4 CIDR: Ek naya IP range dalein (jaise ```10.0.0.0/22```) jo aapke VPC CIDR se overlap na karta ho. Yeh IP remote users ko milenge.
- Authentication: Use mutual authentication select karein aur ACM se Server aur Client certificates choose karein.
- Connection Logging: Agar logs chahiye toh CloudWatch log group select karein, nahi toh isko disable rakh sakte hain.

**3. Target Network Associate Karein (VPC Se Jodein)**:
- Endpoint banne ke baad, use select karein aur Target Networks tab par jayein.
- Associate Target Network par click karein.
- Apna VPC aur woh Subnet select karein jahan aapka data/servers hain. Is step se AWS background mein ek elastic network interface (ENI) banata hai.

**4. Authorization Rules Add Karein (Access Control)**:
- By default, VPN connect hone ke baad bhi kisi resource ka access nahi milta.
- Authorization Rules tab par jayein aur Add Authorization Rule par click karein.
- Destination Network: Apne VPC ka CIDR range dalein (jaise 172.31.0.0/16) taaki users ko poore VPC ka access mile.
- Allow Access to: Allow access to all users select karein (ya specific group set karein).

**5. Configuration File Download Aur Modify Karein**:
- AWS Console se Download Client Configuration par click karein. Ek ```.ovpn``` file download hogi.
- Is file ko kisi text editor (jaise Notepad) mein open karein.
- File mein jahan remote ://amazonaws.com 443 likha hai, uske shuru mein ek random string add karein (jaise remote randomstring.cvpn-endpoint-id...). Yeh step DNS caching issues ko rokne ke liye zaroori hai.
- File ke aakhir mein Client Certificate aur Private Key ka text insert karein (agar mutual authentication use ho raha hai):
```
<cert>
-----BEGIN CERTIFICATE-----
(Aapka client certificate text yahan aayega)
-----END CERTIFICATE-----
</cert>
<key>
-----BEGIN PRIVATE KEY-----
(Aapki client private key text yahan aayega)
-----END PRIVATE KEY-----
</key>
```

**6. Client App Mein Connect Karein**:
- Apne computer par AWS Client VPN Download karke install karein.
- Is software mein ```.ovpn``` configuration file ko import karein.
- Connect button par click karein. Connection successful hote hi aap AWS ke private resources ko bina internet par expose kiye access kar sakenge.

<br>
<br>
<br>

### Technical Limitations aur Architechtural Blind Spots

Jab aap production environment mein AWS VPN design karte hain, toh kuch limits aur baatein dhyan mein rakhni hoti hain jo log aksar miss kar dete hain:

**Bandwidth Limitation**: Ek single AWS VPN tunnel maximum **1.25 Gbps** ki throughput speed de sakti hai. Agar aapke office ka internet **10 Gbps** ka bhi hai, toh bhi VPN par speed **1.25 Gbps** hi milegi. Agar aapko isse zyada speed chahiye, toh aapko Equal-Cost Multi-Path (ECMP) routing configure karni padegi multiple tunnels ke sath Transit Gateway par, ya fir AWS Direct Connect (dedicated physical line) par switch karna padega.

**MTU (Maximum Transmission Unit) Size**: AWS VPN maximum 1425 bytes ka MTU size support karta hai. Normal internet traffic 1500 bytes ka packet use karta hai. VPN encryption ke headers ki wajah se packet size bada ho jata hai, isliye agar aapne TCP MSS clamping configure nahi ki, toh packets drop hone lagenge aur network slow ho jayega.

**Route Tables Configuration**: Sirf VPN tunnel banana kaafi nahi hota. Aapko apne VPC ke Subnet Route Tables mein jakar ek entry daalni padti hai ki agar traffic aapke office network ke IP range (CIDR) ke liye hai, toh use Virtual Private Gateway (VGW) ya Transit Gateway (TGW) ki taraf bhej diya jaye. Isko Route Propagation kehte hain.

<br>
<br>

### AWS VPN Billing Setup (Kharcha Kaise Hota Hai?)

AWS VPN free nahi hota, iska pricing model do cheezon par chalta hai:
- **VPN Connection Hour**: Jab tak aapki VPN tunnel active hai aur AWS side par configured hai, tab tak par-hour ke hisab se charge lagta hai (chahe aap data bhej rahe hon ya nahi).
- **Data Transfer OUT**: AWS mein data aane ka (Data In) koi charge nahi hota, lekin jab AWS VPC se koi data VPN ke throug aapke office network mein jata hai (Data Out), toh par-GB ke hisab se internet charges apply hote hain.

