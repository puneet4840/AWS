# AWS Direct Connect Overview

AWS mein Direct Connect (DX) ek service hai jo on-premises data center ko AWS cloud se ek physical, private fiber-optic cable ke zariye connect karti hai.

AWS Direct Connect (DX) asal mein aapke on-premises data center ko physically AWS ke data center (ya region) se hi connect karta hai.

Dhyan dene wali baat ye hai ki is connection mein Public Internet ka use nahi hota. Matlab tumhara network packets Google, Airtel, Jio ya kisi public ISP ke normal Internet routing se travel nahi karte.

Data Packets on-premises data center se directly AWS ke private network mein enter kar jate hain aur AWS ke data center tak pahuchte hain.

<br>
<br>

### Sabse pehle samajhte hain ki Direct Connect ki zarurat kyun padi?

Maan lo tum ek badi company mein DevOps Engineer ho. Company ka apna ek Data Center hai. Is Data Center mein company ke saare important applications chal rahe hain. Jaise HR Application, Banking Application, ERP Software, Database Servers aur File Servers.

Diagram dekho.
```
Company Data Center

|
|---- Application Server
|
|---- Database Server
|
|---- File Server
|
|---- Active Directory
```
Sab kuch company ke office ke andar hi chal raha hai. Is setup ko **on-premises** data center kehte hain.

Ab company decide karti hai ki wo Cloud adopt karegi. Lekin company ek hi din mein saare servers AWS mein migrate nahi kar sakti. Migration ek long process hota hai.

Isliye company decide karti hai ki kuch servers office mein rahenge aur kuch applications AWS mein shift kar di jayengi.

Ab architecture kuch aisa ban jata hai.
```
Company Data Center
        |
        |
        |
       AWS Cloud
        |
      VPC
        |
      EC2
      RDS
      S3
```

Ab company ke paas do alag networks hain.
- On-Premises Network.
- AWS Network.

Ab in dono ko aapas mein communicate karna hai.

<br>

**Pehla Solution - VPN**:

Sabse pehle Network Engineer bolta hai,

"Hum Site-to-Site VPN bana dete hain." Isse dono network privately connect ho jayenge.

Architecture kuch aisa ho jata hai.
```
Office

↓

Internet

↓

VPN Tunnel

↓

AWS
```
Ab Office ke Servers aur AWS ke Servers securely communicate karne lagte hain.
