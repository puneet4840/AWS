# LAB: NAT Gateway Implementation

Hum is slide mein ek LAB karenge NAT Gateway ke liye.

Pichli lab mein humne ek vpc setup kiya tha, jisme:
- 2 subnets, ek public aur ek private.
- 2 ec2 instances, ek public subnet mein aur dusra private subnet mein.
- Internet Gateway attach kiya the, VPC ke saath.
- 2 route tables banai thi, ek public subent ke liye aur ek private subnet ke liye.


Humne Public ec2 instance ko connect kiya aur usi ke ander pem file ke thorugh private ec2 connect kiya the. Yahan tak ki lab thi vo.

<br>

Ab is lab mein hum is vpc setup mein ek NAT Gateway add karenge public subnet mein.

AWS par VPC ke left section mein NAT Gateway par click karo.

<br>

### NAT Gateway Create

AWS Console

NAT Gateway

Create

Name
```
demo-NAT
```
Subnet
```
Public Subnet
```
Elastic IP

Allocate New

Create.

<br>

### Private Route Table Update

Private Route Table

Route Add
```
Destination

0.0.0.0/0

↓

NAT Gateway
```
Save.

<br>

### Ping Google.com from private ec2

Private EC2
```
ping google.com
```
Ab

Google resolve ho jayega.

Output
```
PING google.com

64 bytes

64 bytes

64 bytes
```

Hua ye ki private ec2 ab internet se connect kar gya NAT Gateway ke through.
