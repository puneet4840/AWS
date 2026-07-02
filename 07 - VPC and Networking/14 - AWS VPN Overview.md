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
