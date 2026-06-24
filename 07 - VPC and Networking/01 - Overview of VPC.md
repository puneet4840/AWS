# Overview of VPC

VPC stands for Virtual Private Cloud.

A VPC is a private network in the cloud which allows AWS resources like (EC2, ECS, EKS, Load Balancer, Lambda, etc) to securely communicate to each other, or on internet, or on-premises network.

AWS mein VPC(Virtual Private Cloud) ek logacally isolated network hai jismein tum apne AWS resources (EC2, RDS, EKS, Load Balancer, Lambda, etc.) deploy karte ho.

VPC AWS ke ander ek private network hota hai jisme aws resources ek-dusre se, ya internet par ya on-premises network se securly connect karte hain.


<br>
<br>

### VPC ki jarurat kyu padi?

<br>

**Pehle Traditional Datacenter Ko Samjho**:

Cloud aane se pehle companies apne khud ke datacenter chalati thi.

Maan lo ek company hai jiska naam ```ABC Pvt Ltd``` hai.

Uske office ya datacenter mein kuch physical servers lage hue hain.
- Ek server website ke liye.
- Ek application ke liye.
- Ek database ke liye.
- Ek backup ke liye.

In sab servers ko switches aur routers ke through connect kiya gaya hai. Network poori tarah company ke control mein hota tha.

Company khud decide karti thi:
- Kaunsi IP range use karni hai.
- Kaunse servers internet se accessible honge.
- Kaunse servers private rahenge.
- Firewall rules kya honge.
- Routing kaise hogi

Simple words mein, company apne network ki malik thi.

Fir **March 2006** mein AWS Cloud aa gya.

<br>

**Cloud Aane Ke Baad Problem Kya Hui?**:

Jab AWS aaya to usne kaha: ```Aapko khud server kharidne ki zarurat nahi hai. Hamare datacenter ke servers use karo.```

Ye idea bahut achha tha kyunki:
- Server kharidne ka kharcha bach gaya.
- Maintenance ka tension khatam ho gaya.
- Scaling aasaan ho gayi.

Lekin ek bahut badi problem create hui.

AWS ek hi datacenter mein hazaron nahi balki lakhon customers ko host karta hai.

Maan lo:
- HDFC Bank bhi AWS use kar rahi hai.
- Flipkart bhi AWS use kar raha hai.
- Zomato bhi AWS use kar raha hai.
- Ek choti startup bhi AWS use kar rahi hai.

Ab sawal ye hai:

Agar sab ek hi datacenter ke andar hain to kya sabke servers ek doosre ko dekh sakte hain? Agar aisa hua to security completely fail ho jayegi.

Isi problem ko solve karne ke liye VPC ka janm hua.

<br>

**VPC Ka Sabse Pehla Purpose - Network Isolation**:

VPC ka sabse important kaam hai **Isolation**. Isolation ka matlab hai alag karna.

AWS ke physical datacenter mein bhale hi sab customers ke servers ek hi building mein rakhe ho, lekin networking ke perspective se har customer ko lagna chahiye ki: "Ye mera khud ka private network hai."

Isi liye AWS har customer ko logically isolated network provide karta hai. Jisko VPC kehte hain.

Example:

Maan lo 3 companies hain.

Company A: ```10.0.0.0/16```.

Company B: ```172.16.0.0/16```.

Company C: ```192.168.0.0/16```.

Ab Company A ka server Company B ke server ko directly access nahi kar sakta. Dono ek hi AWS datacenter mein hain lekin networking ke level par completely isolated hain. Ye isolation VPC provide karta hai.

<br>

**Har Company Ki Networking Alag Hoti Hai**:

Har company ki networking requirements alag hoti hain.

Maan lo kisi company ka on-premise network hai:
```
10.0.0.0/16
```
Dusri company ka hai:
```
172.16.0.0/16
```
Teesri company ka hai:
```
192.168.0.0/16
```

Agar AWS sabko ek hi IP range de deta to bahut problem ho jati. On-premise aur cloud ko connect karna mushkil ho jata. Isliye VPC create karte waqt customer khud CIDR choose karta hai.

Yaani customer apni networking khud design kar sakta hai. Ye flexibility VPC deta hai.

<br>

**Public Aur Private Resources Ko Alag Karna Zaruri Hai**

Ab maan lo aap ek E-commerce website bana rahe ho. Website ka frontend internet par accessible hona chahiye. Customer browser mein URL likhe aur website open ho jaye.

Lekin database ko internet par accessible nahi hona chahiye.

Database mein sensitive information hoti hai:
- Customer data.
- Orders.
- Payment information.
- Product inventory.

Agar database internet par expose ho gaya to hacker attack kar sakta hai.

Isliye architecture kuch is tarah banta hai:

Public Subnet:
- Load Balancer
- Bastion Host
- Web Server

Private Subnet:
- Application Server
- Database Server
- Redis Cache

VPC hume ye separation provide karta hai subnets ke through. Yahi production security ka foundation hai.

<br>

**Security Requirements Har Company Ki Alag Hoti Hain**:

Har organization ke security rules alag hote hain.

Example:

Banking Application:

Sirf HTTPS allow.
```
443
```
Baaki sab block.

Development Environment:
```
22 SSH
80 HTTP
443 HTTPS
```

AWS sab customers ke liye same firewall policy use nahi kar sakta. Isliye VPC ke andar Security Groups aur Network ACLs diye gaye.

Inke through customer khud decide karta hai:
- Kaun andar aa sakta hai.
- Kaun bahar ja sakta hai.
- Kaunsa port open rahega.
- Kaunsa block rahega.

<br>

**Routing Control Ki Zarurat**:

Network sirf IP address ka naam nahi hai. Traffic ko destination tak pahunchana bhi zaruri hai. Maan lo application ko teen jagah traffic bhejna hai.
- VPC ke andar.
- Internet par.
- Company ke On-Prem Datacenter mein.

Ab AWS ko kaise pata chalega traffic kahan bhejna hai?

Isi liye Route Tables hoti hain.

VPC customer ko routing control provide karta hai.

Yaani aap decide karte ho:
- Internet traffic kahan jayega.
- VPN traffic kahan jayega.
- Internal traffic kahan jayega.

Ye enterprise networking ka bahut important feature hai.

<br>

**DevOps Engineer Ke Perspective Se VPC**:

Jab aap AWS mein infrastructure banate ho to sab kuch VPC ke around hi ghoomta hai.

Aap:
- EC2 launch karte ho.
- RDS create karte ho.
- EKS cluster banate ho.
- Load Balancer create karte ho.

Sabse pehle AWS puchta hai: "Kis VPC mein deploy karna hai?"

Kyuki VPC hi network boundary define karta hai. Ek tarah se VPC AWS infrastructure ka foundation hai.

