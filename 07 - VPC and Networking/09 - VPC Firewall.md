# VPC Firewall

AWS VPC (Virtual Private Cloud) mein security ke liye do alag tarah ke firewalls hote hain. Yeh dono firewalls aapke data aur servers ko safe rakhte hain.

Inke naam hain:
- **Security Groups (SGs)**.
- **Network Access Control Lists (NACLs)**.

Sabse pehle hum Security Groups ke baare mein samjhenge for NACL ke baare mein samjhenge.

<br>
<br>

## Security Groups (SGs)

<br>

### Security Group kya hai?

Security Group ek virtual firewall hai jo ec2 instance ke ander aane wale aur bahar jane wale traffic ko control karta hai. Matlab incoming aur outgoing traffic ko control karta hai.

Yeh firewall EC2 Instance ke network interface (ENI - Elastic Network Interface) ke saath attach hota hai.

Iska kaam har aane aur jaane wale network packet ko check karna hota hai.

Har packet aane se pehle woh poochta hai:
- Tum kaun ho?
- Kis port par ja rahe ho?
- Kis protocol ka use kar rahe ho?
- Kya tumhari permission hai?

Agar rule match ho gaya, to packet andar chala jayega. Agar rule match nahi hua, to packet wahin drop kar diya jayega.

Security Group sirf EC2 instance ke liye nahi hota hai. Yeh AWS ke bohot saare doosre resources ke liye bhi use hota hai.

Aap aise samajh sakte hain ki AWS mein jis bhi resource ke paas ek Network Interface (ENI) hota hai ya jo resource aapke VPC ke andar private network par kaam karta hai, usko secure karne ke liye Security Group ka hi use kiya jaata hai.

<br>

**Kaun-kaun se resources Security Groups use karte hain?**

EC2 ke alawa, yahan kuch main AWS resources hain jo Security Groups ka use karte hain:
- Load Balancers (ALB / NLB): Jab internet se traffic aata hai, toh sabse pehle Load Balancer par takrata hai. Security Group se hum control karte hain ki internet se kaun sa traffic Load Balancer ke andar aayega.
- Databases (AWS RDS): Aapka database secure hona chahiye. RDS databases par Security Group lagakar hum rule set karte hain ki sirf hamara EC2 instance ya backend server hi database se connect kar sake, baaki koi nahi.
- Cache Servers (Amazon ElastiCache): Redis ya Memcached jaise caching servers ko secure karne ke liye bhi iska use hota hai.
- Container Services (Amazon ECS / EKS): Agar aap Docker containers chala rahe hain, toh un containers ke tasks aur pods par bhi Security Groups lagaye jaate hain.
- Serverless Functions (AWS Lambda): Jab ek Lambda function ko aapke VPC ke andar ke resources (jaise RDS database) se baat karni hoti hai, toh Lambda function ko bhi Security Group assign kiya jaata hai.

<br>
<br>

### Security Group ki jarurat kyu padi?

Jab hum AWS mein koi EC2 Instance launch karte hain, to sabse pehle ek sawal aata hai.

**"Is server ko kaun access kar sakta hai?"**

Socho tumne ek EC2 launch kiya.

Uska Public IP hai.
```
43.204.120.50
```

Ab ye server internet par visible hai. Ab poori duniya ke log is server ko request bhej sakte hain.

Ab sawal ye hai ki:
- Kya har koi SSH kar sakta hai?
- Kya har koi website open kar sakta hai?
- Kya har koi Database access kar sakta hai?

Agar AWS kisi bhi request ko bina check kiye server tak pahucha de, to koi bhi attacker tumhare server par attack kar sakta hai. Isi problem ko solve karne ke liye AWS ne Security Group introduce kiya.

<br>
<br>

### Security Group kis level par kaam karta hai?

Security Group Subnet level par nahi lagta.

Security Group VPC level par bhi nahi lagta.

Security Group EC2 Instance ke **Network Interface (ENI)** par lagta hai.

Diagram dekho:
```
VPC

    |

Public Subnet

    |

EC2 Instance

    |

Network Interface (ENI)

    |

Security Group
```
Yaani jab koi packet EC2 tak pahunchne wala hota hai, tab sabse pehle us packet ko Security Group check karta hai.

<br>
<br>

### Security Group ka main kaam kya hai?

Security Group ke do major sections hote hain.

**Inbound Rules**:

Inbound Rules decide karte hain ki bahar se kaunsi traffic server ke andar aa sakti hai.

Matlab:
```
Internet

↓

EC2
```
Ye allow hoga ya nahi?

<br>

**Outbound Rules**:`

Outbound Rules decide karte hain ki server bahar kis jagah request bhej sakta hai.

Matlab:
```
EC2

↓

Internet
```
Ye allow hoga ya nahi?

<br>

**Example**:

Suppose tumhare EC2 par ek website chal rahi hai. Tum chahte ho ki duniya ka koi bhi user website open kar sake. Lekin SSH sirf tum kar sako.

To Security Group kuch aisa hoga.

Inbound Rules
```
HTTP

Port 80

Source

0.0.0.0/0
```

Matlab duniya ka koi bhi user website open kar sakta hai.

Aur
```
SSH

Port 22

Source

My IP
```

Matlab sirf tumhare laptop ka IP SSH kar sakta hai. Baaki duniya SSH nahi kar sakti.

<br>
<br>

### Packet Level par kya hota hai?

