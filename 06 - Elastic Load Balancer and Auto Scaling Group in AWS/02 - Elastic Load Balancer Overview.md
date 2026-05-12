# Elastic Load Balancer

Elastic Load Balancer AWS ki ek managed service hai jo Incoming traffic ko multiple servers par distribute karta hai.

AWS mein jab tum applications ko servers mein internet par run karte ho — jaise websites, APIs, microservices, Kubernetes apps, ya enterprise applications — tab ek bahut bada problem aata hai:

“Agar saare users ek hi server par aa gaye toh kya hoga?”:
- Server overload ho jayega.
- Application slow ho jayegi.
- Kabhi-kabhi pura app down bhi ho sakta hai.

Isi problem ko solve karta hai: **Elastic Load Balancer**.

AWS mein Load Balancer incoming requests ko sirf EC2 instance par hi distribute nahi karta, ye EC2 instance ke alawa alag-alag backend par par bhi distribute karte hai, Jaise:
- Container.
- Ip Addresses.
- Lambda Functions.

<br>
<br>

### Real World Example

Suppose tumhari ek ecommerce website hai:
```
www.mystore.com
```
Aur tumhare paas 3 servers hain:
```
Server 1
Server 2
Server 3
```
Agar 10,000 users ek saath website open karein aur sab traffic sirf Server 1 par jaye:
```
Users ---> Server 1 💥
```
Server 1 crash ho sakta hai.

Toh ELB kya karega?
```
              ELB
               |
    -----------------------
    |         |           |
Server1    Server2    Server3
```
Ab ELB users ka traffic intelligently divide karega.

Example:
```
User 1 → Server1
User 2 → Server2
User 3 → Server3
User 4 → Server1
```
Isko bolte hain: **Load Balancing**.

