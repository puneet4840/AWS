# Regional NAT Gateway Overview

Regional NAT Gateway AWS ka ek advanced aur naya feature hai, jo pichle traditional (Zonal) NAT Gateway ke saare jhanjhaton ko khatam kar deta hai.

AWS 2 tarah ke NAT Gateway provide karta hai:
- Zonal.
- Regional.

Pichli slides mein jo NAT Gateway padha hai vo Zonal NAT Gateway hai, jo ki AWS ka ek default NAT Gateway hai.

Ab hum Regional NAT Gateway ko samjhenge.

<br>

### Regional NAT Gateway kya hota hai?

Regional NAT Gateway AWS ki ek managed service hai, jo private subnet mein hosted ec2 servers ko internet se connect karti hai.

Pehle jab aap AWS mein NAT Gateway banate the, toh woh Zonal hota tha (yani kisi ek specific Availability Zone ke andar banta tha). Zonal ka matlab hai ki particular public subnet mein create karna padta tha.

Lekin Regional NAT Gateway poore Region (VPC) ke level par kaam karta hai aur background mein khud ko multiple AZs mein manage karta hai.

Iska matlab hai ki Regional NAT Gateway kisi particular subnet ke ander deploy nhi hota vo sirf ek vpc level par deploy hota hai aur vpc ke ander jitne bhi private subnets hote hain unko ek saath manage karta hai.

<br>
<br>

### AWS Regional NAT Gateway Kaise Kaam Karta Hai?

Jab aap AWS console ya CLI se NAT Gateway create karte hain, toh aapko Availability Mode mein **Regional** select karna hota hai. Regional select karne se hi regional NAT Gateway create hota hai. 

Aur dusra Availability Mode option hota hai **zonal**. Agar aapne zonal mode select kiya to normal availability zone mein create hone wala NAT Gateway ban jayega.

Regionl NAT Gateway create hone ke baad iska working process is tarah hota hai:

- **No Public Subnet Needed**: Purane NAT ke liye aapko har AZ mein public subnet banana padta tha. Regional NAT Gateway ek standalone resource hai. Isko host karne ke liye aapko VPC mein koi public subnet banane ki zaroorat nahi hoti. AWS iska background structure khud manage karta hai.

- **Automatic Multi-AZ Expansion**: Jab aap kisi naye Availability Zone (jaise AZ-3) mein apna koi private instance ya Elastic Network Interface (ENI) launch karte hain, toh Regional NAT khud usko detect kar leta hai. Yeh apne aap ko us naye AZ mein expand (phelna) kar leta hai taaki wahan ke servers ko local internet connectivity mil sake. Agar us AZ se saare workloads khatam ho jayein, toh yeh wahan se contract (hat) jata hai.

- **Single Route Table & NAT ID**: Isme aapko har AZ ke liye alag Route Table nahi banani padti. Poore VPC ke private subnets mein aap ek hi Route Table aur ek hi Regional NAT Gateway ID use karke internet traffic (0.0.0.0/0) ko route kar sakte hain.

<br>
<br>

### Pehle kya problem thi?

Maan lo tumhari VPC mein teen Availability Zones hain.

```
Mumbai Region (ap-south-1)

AZ-1
AZ-2
AZ-3
```

Aur har AZ mein ek Private Subnet hai.
```
AZ-1
Private Subnet

AZ-2
Private Subnet

AZ-3
Private Subnet
```

Ab tum chahte ho ki teeno Private Subnets internet access kar saken. Purane (Zonal) model mein tum ek hi NAT Gateway bana kar kaam nahi chala sakte the.

Best practise ye thi ki, high availability ke liye:
- AZ-1 ke liye alag NAT Gateway
- AZ-2 ke liye alag NAT Gateway
- AZ-3 ke liye alag NAT Gateway

Matlab har availability zone ke liye alag NAT Gateway. Aur har NAT Gateway apni Public Subnet mein hota tha.

<br>
<br>

### Itni complexity kyun thi?

Socho tumhari company ki application sirf ek AZ mein chal rahi thi.

Us time architecture kuch aisa tha.

```
AZ-1

Public Subnet
     |
 NAT Gateway



Private Subnet
     |
Backend Server
```

Sab kuch sahi chal raha tha. Lekin kuch mahine baad traffic badh gaya.

Traffic itna badh gaya ki ek AZ enough nahi tha. Load ko handle karne ke liye ab company ne AZ-2 mein bhi naye Backend Servers launch kar diye.

Ab problem shuru hui.

AZ-2 ke servers ko bhi internet access chahiye tha. Lekin AZ-2 mein NAT Gateway tha hi nahi.

Ab tumhe manually:
- Public Subnet banana padta.
- NAT Gateway banana padta.
- Elastic IP allocate karni padti.
- Route Table modify karni padti.

Yaani har naye AZ ke saath networking bhi manually update karni padti thi. Jisme kuch problems thi.

<br>

### AWS ne socha...

AWS ne dekha ki bahut saare customers ko ye repetitive kaam karna pad raha hai, Jaise:

- Har AZ ke liye alag NAT Gateway banao.
- Fir Route Table update karo.
- Fir monitoring karo.
- Fir High Availability maintain karo.

AWS ne socha ki ye saara kaam automation se ho sakta hai. Isi liye Regional NAT Gateway launch kiya gaya.

<br>
<br>

### Regional NAT Gateway ka concept

Regional NAT Gateway mein tum VPC mein sirf ek Regional NAT Gateway create karte ho. 

Uske baad AWS khud background mein dekhte rehta hai ki tumhare workloads kis-kis Availability Zone mein chal rahe hain.

Jaise hi AWS ko pata chalta hai ki kisi naye AZ mein resources launch hue hain, Regional NAT Gateway us AZ ko automatically manage karna shuru kar deta hai.

<br>

**Example**:

Maan lo tumhari VPC mein sirf AZ-1 use ho raha hai.
```
AZ-1

Private EC2
```

Tum ek Regional NAT Gateway create kar dete ho. Abhi AWS sirf AZ-1 ko serve karega. Do mahine baad tum AZ-2 mein bhi EC2 launch kar dete ho. Tumhe kuch bhi manually create nahi karna. AWS automatically detect karega ki ab AZ-2 mein bhi workload aa gaya hai. Fir Regional NAT Gateway us AZ tak bhi expand ho jayega

<br>

**Isme sabse bada benefit kya hai?**:

Sabse bada benefit hai ki tumhe baar-baar NAT Gateway create nahi karna padta. Networking ka management bahut simple ho jata hai. Pehle tumhe teen NAT Gateways manage karne padte the. Ab tum sirf ek Regional NAT Gateway manage karte ho. AWS background mein scaling aur availability handle karta hai.

Purane NAT Gateway ko Public Subnet mein deploy karna padta tha. Kyunki usse Internet Gateway tak pahunchna hota tha. Lekin Regional NAT Gateway ek standalone regional resource hai. Isliye tumhe har AZ mein Public Subnet banane ki zarurat nahi hoti. AWS iski networking ko khud manage karta hai.

<br>
<br>

### Route Table kaise kaam karegi?

Maan lo tumhare paas teen Private Subnets hain.
```
Private Subnet A

Private Subnet B

Private Subnet C
```
Tum teeno Route Tables mein sirf ek hi default route doge.
```
0.0.0.0/0

↓

Regional NAT Gateway
```

Ab chahe EC2 kisi bhi AZ mein ho, traffic usi Regional NAT Gateway ID ki taraf route kiya ja sakta hai. AWS backend mein ensure karta hai ki traffic appropriate AZ handling ke saath process ho.

<br>
<br>

### High Availability kaise milti hai?
