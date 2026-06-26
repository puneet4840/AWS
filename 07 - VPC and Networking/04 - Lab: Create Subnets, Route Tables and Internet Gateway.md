# Hands-on Lab: Create subnets, route tables and internet gatway

Is lab mein hum ye architecture banayenge:

```
                          Internet
                              |
                              |
                      Internet Gateway
                              |
                    ---------------------
                    |                   |
                 AWS VPC (10.0.0.0/16)
                    |                   |
        ---------------------      ---------------------
        | Public Subnet      |      | Private Subnet   |
        | 10.0.1.0/24        |      | 10.0.2.0/24      |
        ---------------------      ---------------------
                |                           |
        Public Route Table          Private Route Table
        10.0.0.0/16 Local           10.0.0.0/16 Local
        0.0.0.0/0 -> IGW
```

- Pehle ek VPC create karenge.
- Internet Gateway create karenge, usko VPC se attach karenge.
- 2 Subnets banayenge, public and private subnets.
- 2 Route Tables, public aur private route tables.
- Public route table mein route create karenge jo traffic ko internet gateway ko route karega, aur usko public subnet se attach karenge.
- Private route table ko private subnet se attach karenge.


<br>

### Note

Ek EC2 ko internet access karne ke liye 2 chaize jaruri hain:
- Route Table mein internet ke liye route hona chahiye: ```0.0.0.0/0 → Internet Gateway```.
  
- Us subnet mein launch hone wali EC2 instance ke paas **Public IP** ya **Elastic IP** hona chahiye.

Ek EC2 internet ko tabhi access kar sakta hai, ec2 ek public subnet mein ho aur uske paas ek public ip ho. Agar ec2 public subnet mein hai lekin uske paas public ip nhi hai to vo internet access nhi kar sakta.

Agar ec2 private subnet mein hai aur uske paas sirf public ip hai to bhi vo internet access nhi kar sakta.

<br>
<br>

### Step-1: Create a Custom VPC

AWS Console

Search
```
VPC
```
Open VPC Dashboard and Click
```
Create VPC
```
Select
```
VPC only
```
Fill details
```
Name
My-VPC

IPv4 CIDR
10.0.0.0/16

IPv6
No IPv6

Tenancy
Default
```
Click
```
Create VPC
```

**Samjho kya hua**:

Ek VPC create ho gya jiske paas ```65536``` total ip addresses hain.

<br>
<br>

### Step-2: Create Public Subnet

Left side
```
Subnets
```
Click
```
Create subnet
```
Select
```
My-VPC
```

Fill
```
Subnet Name: Public-Subnet

Availability Zone: ap-south-1a

CIDR: 10.0.1.0/24
```

Click
```
Create
```

Isse tumhare VPC mein ek Public subnet naam se ek subnet create ho gya. Jo ke abhi private subnet hai.

<br>
<br>

### Step-3: Create Private Subnet

Again
```
Subnet Name: Create Subnet
```
Fill
```
Private-Subnet

AZ: ap-south-1b

CIDR: 10.0.2.0/24
```
Create

Isse tumhare VPC mein ek private-subnet naam se ek subnet create ho gya, jo ke abhi private subnet hai. Is subnet ko humko private hi rakhna hai.

<br>

**Ab architecture**
```
VPC

10.0.0.0/16

        |
--------------------------
|                        |
Public              Private

10.0.1.0/24      10.0.2.0/24
```

<br>
<br>

### Step-4: Create Internet Gateway

Left
```
Internet Gateway
```
Create

Name
```
My-IGW
```
Create

Abhi ye kisi VPC se attached nahi hai.

<br>
<br>

### Step-5: Attach Internet Gateway

Select
```
My-IGW
```
Action
```
Attach to VPC
```
Choose
```
My-VPC
```
Attach

<br>

**Ab architecture**:
```
Internet

      |

Internet Gateway

      |

My-VPC
```

Lekin abhi bhi internet nahi chalega. Kyuki Route Table mein route nahi likha.

<br>
<br>

### Step-6: Create Public Route Table

Left
```
Route Tables
```
Create

Name
```
Public-RT
```
Select
```
My-VPC
```
Create

<br>

**Add Route**

Open
```
Public-RT
```
Routes
```
Edit Routes
```
Already hoga
```
10.0.0.0/16

Local
```
Ye automatically aata hai.

Ab

Add Route

Destination
```
0.0.0.0/0
```
Target
```
Internet Gateway
```
Choose
```
My-IGW
```
Save

<br>

**Ab Route Table**
```
Destination      Target

10.0.0.0/16      Local

0.0.0.0/0        IGW
```

<br>

**Matlab**

Agar packet
```
10.0.x.x
```
ke liye hai

to
```
VPC ke andar bhejo
```

Agar
```
Google

Facebook

Youtube

8.8.8.8

1.1.1.1
```
kahin bhi jana ho

to
```
Internet Gateway
```
ke paas bhejo.

<br>
<br>

### Step-7: Associate Public Route Table

Public Route Table

Subnet Associations

Edit

Select
```
Public Subnet
```
Save

<br>

Ab
```
Public Subnet

↓

Public Route Table

↓

IGW

↓

Internet
```

<br>
<br>

### Step-8: Create Private Route Table

Create Route Table

Name
```
Private-RT
```
VPC
```
My-VPC
```
Create


Isme route mat add karo.

Sirf
```
10.0.0.0/16

Local
```
rahega.

<br>
<br>

### Step-9: Associate Private Route Table

Open
```
Private-RT
```
Subnet Association

Edit

Select
```
Private Subnet
```
Save

<br>
<br>

### Ab final architecture
```
                    INTERNET
                        |
                        |
                Internet Gateway
                        |
          -------------------------------
          |                             |
        AWS VPC (10.0.0.0/16)
          |
   -------------------------------
   |                             |
Public Subnet              Private Subnet
10.0.1.0/24                10.0.2.0/24
   |                             |
Public Route Table         Private Route Table
10.0.0.0/16 Local          10.0.0.0/16 Local
0.0.0.0/0 -> IGW
```

