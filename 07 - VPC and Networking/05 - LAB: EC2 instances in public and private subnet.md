# LAB: EC2 instances in public and private subnet

Is lab mein hum ec2 instances ko public aur private subnets mein create karenge aur unki connectivity check karenge.

Isse pichle slide mein humne ek lab ki thi jisme humne jisme humne ek vpc create kiya tha, usme ek public subnet aur ek private subnet create kiya tha. VPC se internet gatway attach kiya tha.

2 route tables create kiye the ek public aur ek private route table, public route table ko public subnet se internet-gateway ka route banake attach kiya tha, aur private route table ko private subnet se attach kiya tha.

<br>

### Goal

To aaj ki lab mein hum usi VPC ke ander 2 ec2 instances create karenge, Ek ec2 instance public subnet mein hoga, aur dusra ec2 private subnet mein hoga.

EC2-A jo public subnet mein hoga, usko connect karke, usme EC2-B jo private subnet mein usko connect karenge. Aur check karenge ki agar ec2 instance private subnet mein hota hai to locally dusre ec2 se connect hota hai ya nhi.


<br>
<br>

### Step-1: Create EC2 instances in public subnet.

- Ek linux ec2 instance connect karo public subnet mein.
- Auto assign public ip enable rakhna jisse usko ek public ip mil jayegi.
- Security group mein port 22 open rakhna hai.

<br>
<br>

### Step-2: Create EC2 instance in private subnet.

- Ab ek linux ec2 instance create karo private subnet mein.
- Auto assign public ip disable rakhna hai.
- Security group mein ek rule add karna hai Custom aur usme VPC ka CIDR range deni hai. Jiska matlab hai ki same vpc se koi bhi ec2 isko acces kar sakta hai.

<br>
<br>

### Step-3: Connect to the public EC2 instance

- Ab tumko public subnet mein rakhe hue EC2 instance ko connect karna hai.
- Jo pem file tumne private ec2 instance create karte time di thi usko notepad mein open karke complete private key copy karlo aur public ec2 instance mein key.pem file create karke paste kardo.
- Ab is command se private ec2 ko access karo: ```ssh -i key.pem ec2-user@<private-ip>```.

Tum public ec2 se private ec2 par connect ho jaoge.


DONE!!!
