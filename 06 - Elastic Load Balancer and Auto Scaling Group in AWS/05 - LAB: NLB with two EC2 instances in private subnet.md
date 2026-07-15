# LAB: NLB with two EC2 instances in private subnets

Is lab mein hum Network Load Balancer ko implement karenge. Humare paas 2 ec2 instances honge private subnets mein, NLB ke through in ec2 instances par traffic ko distribute karenge.

<br>
<br>

### Phase 1: Lab Ki Kahani Aur Requirement 

Maan lijiye aap ek FinTech (Financial) Company mein Cloud Network Engineer hain.

**Problem**: Ek badi bank aapki service ko use karna chahti hai, lekin unka security rule hai: "Hum kisi dynamic load balancer URL par traffic nahi bhejenge. Hame strictly 2 Fixed Static Public IP Addresses do, jise hum apne security firewall mein whitelist (allow) kar sakein."

**Solution**: Agar aap ALB use karenge, toh uske IP addresses lagatar badalte rehte hain. Isliye hum Network Load Balancer (NLB) ka use karenge, aur use Elastic IP (permanent public fixed IP) ke sath configure karenge. Hum apne main web servers ko strictly Private Subnets mein chhupa kar rakhenge taaki internet se koi unhe direct attack na kar sake.

<br>
<br>

### Step 1: Create Custom VPC (The Isolated Network)

Sabse pehle hum company ke liye ek fresh custom network block design karenge, bina kisi shortcut wizard ke.

- AWS Console mein search bar mein **VPC** type karke VPC Dashboard par jayein.
- Left-hand side menu se **Your VPCs** par click karein aur fir top right mein orange color ke **Create VPC** button par click karein.
- Settings mein strictly select karein: ```VPC only```.
  - **Name tag**: ```nlb-production-vpc```.
  - **IPv4 CIDR block**: Manual chunein aur type karein: ```172.16.0.0/16``` (Yeh hame total 65,536 private internal IPs deta hai).
  - **IPv6 CIDR block**: No IPv6 CIDR block rehne dein.
  - **Tenancy**: Default select rakhein.
- Sabse neeche jaakar **Create VPC** par click kar dein. Aapka base network boundary ready hai.

<br>
<br>

### Step 2: Create 4 Subnets (2 Public for NLB + 2 Private for Servers)

High availability ke liye hum traffic ko do alag-alag data centers (Availability Zones - AZ) mein divide karenge. Isliye NLB ko 2 subnets mein deploy karenge aur Ec2 ko bhi 2 private subnets mein create karenge.

- Left menu se **Subnets** par click karein, fir top right mein **Create subnet** par click karein.
- **VPC ID**: Dropdown se apna abhi-abhi banaya naya VPC ```nlb-production-vpc``` select karein.
- Ab hum ek-ek karke 4 subnets add karenge (**Add new subnet** button par click karte hue):
  - **Subnet 1 (Public Zone A)**:
    - Subnet name: ```nlb-pub-1a```.
    - Availability Zone: ```us-east-1a``` (ya aapke region ka pehla zone).
    - IPv4 CIDR block: ```172.16.1.0/24``` (251 usable IPs).
  - **Subnet 2 (Public Zone B)**: (Scroll karke Add new subnet par click karein)
    - Subnet name: ```nlb-pub-1b```.
    - Availability Zone: ```us-east-1b``` (ya aapke region ka doosra zone).
    - IPv4 CIDR block: ```172.16.2.0/24```.
  - **Subnet 3 (Private Zone A)**: (Scroll karke Add new subnet par click karein)
    - Subnet name: ```nlb-priv-1a```.
    - Availability Zone: ```us-east-1a```.
    - IPv4 CIDR block: ```172.16.3.0/24```.
  - **Subnet 4 (Private Zone B)**: (Scroll karke Add new subnet par click karein)
    - Subnet name: ```nlb-priv-1b```.
    - Availability Zone: ```us-east-1b```.
    - IPv4 CIDR block: ```172.16.4.0/24```.

- Sabse neeche jaakar **Create subnet** par click kar dein. Saare 4 subnets successfully ban chuke hain.

**Public Subnets Ka Auto-IP Setting Enable Karna**:
- Subnets ki list mein ```nlb-pub-1a``` ke checkbox par tick lagayein.
- Top right mein **Actions** -> **Edit subnet settings** par click karein.
- Auto-assign IP settings ke andar **Enable auto-assign public IPv4 address** par check mark lagayein aur Save kar dein.
- Same yahi step ```nlb-pub-1b``` ke liye bhi repeat karein taaki dono public subnets ko auto-IP mil sake.

<br>
<br>

### Step 3: Gateways And Route Tables Setup

Abhi subnets aapas mein disconnected hain aur unhe internet ka rasta nahi pata.

**Part A: Internet Gateway (IGW)**:
- Left menu se **Internet Gateways** par click karein -> **Create internet gateway**.
- Name tag dalein: ```nlb-igw``` aur **Create** kar dein.
- Gateway banne ke baad, top right mein **Actions** -> **Attach to VPC** par click karein.
- Dropdown se apna ```nlb-production-vpc``` chunein aur **Attach** par click kar dein.

**Part B: NAT Gateway (NGW)**:
- Left menu se **NAT Gateways** par click karein -> **Create NAT gateway**.
- Name dalein: ```nlb-nat-gw```.
- **Subnet**: Dhyan se dropdown se apna public subnet ```nlb-pub-1a``` select karein (NAT Gateway hamesha public area mein baithta hai).
- **Elastic IP allocation ID**: Saamne bane Allocate Elastic IP button par click karein. AWS khud ek static internal IP attach kar dega.
- **Create NAT gateway** par click kar dein.

**Part C: Route Tables Link Karna**:
- **Route Tables** -> Create route table. Name: ```nlb-public-rt``` | VPC: ```nlb-production-vpc``` | **Create** karein.
  - Routes tab -> Edit routes: Add route karke ```0.0.0.0/0``` select karein aur Target mein Internet Gateway (```nlb-igw```) chunein. Save changes.
  - Subnet associations tab -> Edit: Apne dono public subnets (```nlb-pub-1a```, ```nlb-pub-1b```) ko check mark karke Save kar dein.
 
- Firse **Create route** table. Name: ```nlb-private-rt``` | VPC: ```nlb-production-vpc``` | Create karein.
  - Routes tab -> Edit routes: Add route karke ```0.0.0.0/0``` select karein aur Target mein NAT Gateway (```nlb-nat-gw```) chunein. Save changes.
  - Subnet associations tab -> Edit: Apne dono private subnets (```nlb-priv-1a```, ```nlb-priv-1b```) ko check mark karke Save kar dein.
 
<br>
<br>

### Step 4: Reserve Elastic IPs (NLB Ke Fixed IPs Ke Liye)

Hume bank ko 2 permanent static public IPs deni hain, isliye hum AWS pool se 2 IPs pehle se book karenge.
- VPC Dashboard mein left menu mein niche scroll karein. Network & Security ke andar **Elastic IPs** par click karein.
- Top right mein orange color ke **Allocate Elastic IP** address button par click karein.
- Bina kisi settings ko chhede, seedhe niche jaakar **Allocate** par click kar dein. (Hame pehli Public Static IP mil gayi, jaise ```13.232.45.10```).
- Dobara **Allocate Elastic IP address** par click karein aur seedhe **Allocate** kar dein. (Ab hamare paas total 2 Fixed Static Public IPs ho gayi hain).

<br>
<br>

### Step 5: Configure Layer 4 Security Group

Important Production Note: Application Load Balancer (ALB) ke case mein hum server par sirf ALB ka security group allow karte the. Lekin Network Load Balancer (NLB) client ka real IP seedhe server par bypass (forward) kar deta hai. Isliye servers par hum pure VPC ka internal range allow karenge.

- Left menu se **Security Groups** -> **Create security group**.
- **Security group name**: ```nlb-web-server-sg```.
- **Description**: Allow internal TCP Port 80 from VPC.
- **VPC**: Dropdown se default VPC hata kar apna ```nlb-production-vpc``` chunein.
- **Inbound rules** -> **Add rule**:
  - **Type**: HTTP (Port 80).
  - **Source**: Select karein ```Custom``` aur manually type karein aapka pure VPC ka range: ```172.16.0.0/16```.
- Sabse neeche jaakar **Create security group** par click kar dein.

<br>
<br>

### Step 6: Launch Web Servers In Private Subnets

Ab hum dono instances ko strictly chhupa kar private subnets ke andar daalenge.

**Server A (Private Subnet 1A)**:
- **EC2 Dashboard** par jayein, left menu se **Instances** -> **Launch instances** par click karein.
- **Name**: ```NLB-Backend-A```.
- **AMI**: ```Amazon Linux 2023``` | Instance type: t2.micro (ya default free tier).
- **Key pair**: Proceed without a key pair chunein.
- **Network settings** ke saamne Edit par click karein:
  - **VPC**: ```nlb-production-vpc```.
  - **Subnet**: Dhyan se dropdown se chunein ```nlb-priv-1a``` (Zone A ka private subnet).
  - **Auto-assign public IP**: Yeh automatic ```Disable``` ho jayega, ise aise hi chhod dein.
  - **Firewall (security groups)**: Select existing security group chunein aur dropdown se ```nlb-web-server-sg``` par tick lagayein.
- Advanced Details ko expand karein, sabse end mein User data box mein yeh code dalein:
```
#!/bin/bash
sudo dnf update -y
sudo dnf install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
echo "<h1>Hello from High Performance Server A (NLB Node)</h1>" | sudo tee /usr/share/nginx/html/index.html
```
- Right corner mein **Launch instance** par click kar dein.

<br>

**Server B (Private Subnet 1B)**:
- Firse **Launch instances** par click karein.
- **Name**: ``NLB-Backend-B``` | AMI/Instance Type/Key pair same rakhein.
- **Network settings** ke saamne Edit par click karein:
  - **VPC**: ```nlb-production-vpc```.
  - **Subnet**: Is baar dropdown se chunein ```nlb-priv-1b``` (Zone B ka private subnet).
  - **Security Group**: Existing group ```nlb-web-server-sg``` select karein.
 
- Advanced Details -> User data box mein yeh code dalein:
```
#!/bin/bash
sudo dnf update -y
sudo dnf install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
echo "<h1>Hello from High Performance Server B (NLB Node)</h1>" | sudo tee /usr/share/nginx/html/index.html
```
- **Launch instance** par click kar dein.

<br>
<br>

### Step 7: Create TCP Target Group

