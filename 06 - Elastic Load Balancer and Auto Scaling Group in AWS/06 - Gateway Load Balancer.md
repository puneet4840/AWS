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

