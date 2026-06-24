# Overview of Subnet

Jab hum AWS mein VPC (Virtual Private Cloud) create karte hain, tab hum ek private network create karte hain. Lekin sirf VPC banana kaafi nahi hota.

VPC ek bahut bada network hota hai. Is bade network ke andar hum chhote-chhote network banate hain. Inhi chhote networks ko Subnet kehte hain.

<br>

Subnet ka matlab hai, ek bade network ko multiple small network mein divide kar dena.

A subnet is the small network divided form a large network.

<br>

Subnet ka matlab hai "Sub-Network". Jab aap ek bade VPC network ko chhote-chhote hisson (chunks) mein todte hain, toh har ek hisse ko Subnet kehte hain.

Subnetting is the process of creating a subnet.

Example: Suppose there is a network in a company, it has multiple departments like Finance, Payroll, Sales and Development. If a company assign a single complete network to all these departments, If any employee from from sales department access malicious website and hacker got that device access through that malicous website. Now hacker can access all those device which are in the same network. This can be very dangerous situation for the company.

To avoid this subnetting is being used.

<br>
<br>

### Types of Subnet in AWS

AWS VPC mein 2 types ke subnet hote hain:
- Public Subnet.
- Private Subnet.

<br>

**Public Subnet**:

Public subnet vo subnet hota hai jiske paas internet access hota hai. Matlab public subnet mein servers directly internet access kar sakte hain.

Public Subnet AWS VPC ka woh hissa hai jo directly internet se juda hota hai.

<br>

**Private Subnet**:

Private subnet vo subnet hota hai jiske pas direct internet access nahi hota. Matlab private subnet mein server internet access nahi kar sakte aur unke paas public ip nhi hoti, sirf private ip hoti hai.

Agar tumko private subnet mein server ko internet access dena hai to tumko **NAT Gateway** use karna padega, jisse sirf servers se request internet par ja sakti hai lekin koi request internet se server nahi aa sakti.


<br>

**Note**:

AWS ke VPC mein jab bhi hum ek subnet create karte hain, to vo subnet by default private subnet hota hai. Us subnet ko public subnet banane ke liye vpc mein **Intenet Gateway**, **Route Table** ka use karke us subnet tak internet pahuchana hoga, tab hi vo subnet ek public subnet ban payega.

Nahi to jo bhi hum ek normal subnet vpc mein create karte hain vo ek private subnet hi hota hai.

<br>
<br>

### Subnet Ki Jarurat Kyu Padi?

