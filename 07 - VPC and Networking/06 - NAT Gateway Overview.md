# NAT Gateway

AWS VPC mein NAT Gateway ka kaam hota hai, private subnet mein bane hue server ko internet access dena. Jab hum vpc ke private subnet mein koi server run karte hain to, vo server
internet access nhi kar sakta, kyuki private subnet mein internet gateway ka route nhi hota aur saath hi server ke paas koi public ip nhi hota.

Server ko direct internet se expose na karne ke liye, server ko private subnet mein rakhte hain. 

Lekin server ko apne packages, libraries ko update kanrne ke liye internet ki jarurat padti hai kyuki ye packages aur libraries internet par available hote hain. To agar server private subnet mein hosted hai.
To internet se connect kaise karega.

Is problem ko solve karne ke liye NAT Gateway ka use kiya jata hai. Ab aage NAT Gateway ko samjhte hain.

<br>
<br>

### Sabse pehle samajhte hain NAT kya hota hai?

**NAT** ka full form **Network Address Translation** hota hai.

Is naam ko agar tod kar dekhein to:
- **Network** ka matlab computers ka network.
- **Address** ka matlab IP Address.
- **Translation** ka matlab ek cheez ko doosri cheez mein badalna.

NAT ek aisa device hai jo Private IP Address ko Public IP Address mein convert karta hai.

Lekin yahan sawal aata hai ki AWS ko IP Address convert karne ki zarurat hi kyun padti hai? Iska answer samajhne ke liye humein pehle Private IP aur Public IP samajhna padega.

<br>

### Public IP kya hota hai?

Public IP ek aisa IP Address hota hai jo poori duniya mein unique hota hai. Agar kisi server ke paas Public IP hai, to internet par maujood doosre computers us tak pahunch sakte hain (agar security rules allow karein).

Iska matlab hai ki jis server ke paas public ip hai vo internet access kar sakta hai.

Is IP ko duniya ka koi bhi computer access kar sakta hai.

Example:
```
52.95.110.10
13.233.100.5
8.8.8.8
```

Maan lo tumne AWS mein ek EC2 instance launch kiya aur usko Public IP mil gaya. Ab agar uske Security Group mein HTTP (Port 80) allow hai, to duniya ka koi bhi user browser mein uska Public IP type karke us web server tak pahunch sakta hai.

<br>

### Private IP kya hota hai?

Private IP sirf internal network ke liye hota hai. 

Jab tum VPC create karte ho to vpc ko ek CIDR range dete ho, usi cidr range mein se ek ip addres ec2 ko assign ho jata hai, jo ek private ip hota hai.

Example:
```
10.0.1.10
10.0.2.15
192.168.1.5
172.31.10.20
```

Ye IP addresses sirf VPC ya kisi internal network ke andar kaam karte hain.

Agar tum apne ghar ke Wi-Fi se connected ho aur tumhare laptop ka IP 192.168.1.20 hai, to duniya ka koi bhi insaan internet par is IP ko use karke tumhare laptop tak directly nahi pahunch sakta. Iska reason ye hai ki internet private IP addresses ko route nahi karta.

<br>
<br>

### Ab Problem kya hai?

Ab maan lo AWS mein tumhare paas ek Private EC2 instance hai.

Uska IP hai:
```
10.0.2.15
```
Ye server Private Subnet mein rakha gaya hai.

Ab tum is server par login karke command chalate ho:
```
sudo apt update
```
Ye command Ubuntu ke package repository se latest package list download karti hai. Lekin package list internet par rakhi hui hai. Ab server ko internet par request bhejni padegi. Lekin problem ye hai ki server ke paas sirf Private IP hai. Aur internet Private IP ko nahi samajhta.

To request kaise jayegi?

Yahin par NAT Gateway ka role shuru hota hai.

<br>

### NAT Gateway kya karta hai?

NAT Gateway ko tum ek translator ya middleman ki tarah samajh sakte ho.

Private Server directly internet se baat nahi kar sakta. Isliye Private Server apni request NAT Gateway ko bhejta hai.

NAT Gateway request ko receive karta hai aur kehta hai:
- "Tumhara Private IP main internet ko nahi dikhane wala. Main apna Public IP use karke request bhejunga."

Yaani NAT Gateway request ka source IP change kar deta hai.

**Example**:

Private Server:
```
10.0.2.15
```
NAT Gateway ka Elastic IP:
```
43.205.100.10
```

Jab request internet par jaati hai to internet ko ye nahi dikhta ki request ```10.0.2.15``` se aayi hai.

Internet ko sirf ye dikhta hai:
```
43.205.100.10
```

Matlab NAT Gateway ec2 instance ki request ko modify karta hai aur ec2 ke private ip jagah apna public laga deta hai, jisse internet par servers ko lagta hai ki request ek NAT gateway se aayi hai na ki kisi private ec2 instance se.

Yahi process Network Address Translation kehlata hai.

<br>

### Response kaise wapas aata hai?

Ab Google ya Ubuntu Repository response bhejti hai. Lekin response kahan bhejegi?

Wo usi Public IP par bhejegi jo usko request mein dikha tha.

Yaani:
```
43.205.100.10
```
Ye response NAT Gateway receive karta hai.

NAT Gateway ke paas ek connection table hoti hai. Us table mein likha hota hai:
```
43.205.100.10

↓

Actually request aayi thi

10.0.2.15
```

Ab NAT Gateway response ko dubara Private IP par bhej deta hai. Isliye Private Server ko lagta hai ki usne directly internet se baat ki.

<br>

### NAT Gateway ko "One-Way Gate" kyun bolte hain?

Private Server NAT Gateway ke thorugh internet par request bhej sakta hai.

Internet us request ka response bhej sakta hai.

Lekin internet ka koi random user khud se Private Server ke paas connection start nahi kar sakta.

**Example**:

Tumhara backend server Private Subnet mein hai.

Uska IP hai:
```
10.0.2.15
```

Koi hacker internet se directly is server ko access karna chahe to nahi kar sakta.

Kyun?

Kyuki Private Server ka Public IP hi nahi hai.

Aur NAT Gateway sirf outbound requests aur unke responses handle karta hai. Iska matlab hai ki NAT Gateway sirf vpc se aayi hui request ko aur internet se us request ke aaye hue response ko hi handle kar sakta hai.

NAT Gateway internet se aayi hui direct request ko handle nhi karta. Isliye NAT Gateway security bhi improve karta hai.

<br>

### AWS mein NAT Gateway kahan deploy hota hai?

NAT Gateway backend par ek virtual machine hoti hai jo private ip ko pubic ip mein change karti hai.

NAT Gateway hamesha **Public Subnet mein deploy** hota hai. 

Reason simple hai.

NAT Gateway ko internet tak pahunchna hota hai.

Aur internet tak pahunchne ke liye uske subnet ke Route Table mein Internet Gateway ka route hona chahiye. Agar NAT Gateway ko Private Subnet mein bana doge to wo khud hi internet tak nahi pahunch payega.

Isliye AWS ka rule hai:
- NAT Gateway Public Subnet mein banao, aur Private Subnet ke resources usko use karein.

<br>
<br>

### Real Company Example

Maan lo ek e-commerce company hai.

Architecture kuch is tarah hai:
```
Internet

↓

Application Load Balancer

↓

Frontend Server (Public Subnet)

↓

Backend Server (Private Subnet)

↓

Database (Private Subnet)
```

Ab Backend Server ko har din ye kaam karne hote hain:
- Docker images download karna.
- Ubuntu updates install karna.
- GitHub se code pull karna.
- AWS APIs call karna.
- External payment gateway se communicate karna.

Ye sab internet ke through hota hai. Agar backend ko Public IP de diya jaye to security risk badh jayega. Isliye backend ko Private Subnet mein rakha jata hai aur usko internet access NAT Gateway ke through diya jata hai.

Is tarah backend internet use bhi kar leta hai aur internet se hidden bhi rehta hai.

<br>
<br>

### Route Table ka role

Private Subnet ka Route Table NAT Gateway ko batata hai ki internet ki traffic kahan bhejni hai. Isliye hum private subnet mein ek route create karte hain jo ```0.0.0.0/0``` traffic ko NAT Gateway tak route karta hai.

Private Route Table:
```
Destination

0.0.0.0/0

↓

NAT Gateway
```

Iska matlab hai:
- "Agar VPC ke ander ke traffic ki destination VPC ke bahar kahin bhi hai, to request NAT Gateway ke paas bhej do."

Fir NAT Gateway us request ko Internet Gateway ke through internet tak pahucha deta hai.

<br>
<br>

### Kya NAT Gateway ke paas Public IP hota hai?

Yes.

NAT Gateway ke saath

Elastic IP attach hota hai.

Example

Elastic IP
```
13.233.10.50
```

<br>
<br>

### NAT Gateway State Maintain Karta Hai

NAT Gateway state maintain karta hai, iska seedha matlab hai ki yeh ek Stateful (status yaad rakhne wala) device hai.

Jab aapka private network (jaise AWS VPC) internet se connect hota hai, tab NAT Gateway har ek connection ka hissa yaad rakhta hai.

**Yeh Kaise Kaam Karta Hai?**:

Jab aapke private subnet ka koi computer internet par kisi website ko request bhejta hai, toh NAT Gateway yeh teen cheezein apni ek table (Connection Tracking Table) mein save kar leta hai:
- **Private IP aur Port**: Request private subnet ke kis computer se aayi.
- **Public IP aur Port**: NAT Gateway apna kaunsa public address use kar raha hai, internet par request bhejne ke liye.
- **Destination IP aur Port**: Request internet par kis website ke paas ja rahi hai.

**State Maintain Karne Ka Fayda Kya Hai?**
- Jab website wapas jawaab (response) bhejti hai, toh NAT Gateway apni table check karta hai. Use turant yaad aa jata hai ki yeh jawaab kis private computer ke liye aaya hai.
- Internet se koi bhi bahar ka computer seedhe aapke private computer ko data nahi bhej sakta. NAT Gateway sirf unhi packets ko andar aane deta hai jinki request andar se pehle bahar gayi thi.

