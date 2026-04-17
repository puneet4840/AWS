# AWS Global Infrastructure

### AWS Global Infrastructure kya hota hai?

AWS (Amazon Web Services) ka global infrastructure ek worldwide distributed data center network hai jo duniya bhar mein spread hota hai, jahan AWS apni services (EC2, S3, RDS, etc.) host karta hai.

Matlab AWS ne duniya bhar mein data centers banaye hue hain jahan pe AWS ki services chalti hain.

Simple words mein:
- AWS ne duniya ke alag-alag countries aur cities mein apne data centers ka network banaya hai.
- Ye network itna design kiya gaya hai ki high availability, fault tolerance aur low latency mile.

<br>
<br>

### AWS Infrastructure ke Core Components

Ab AWS infrastructure ko samajhne ke liye tumhe ye 5 main building blocks samajhne honge:
- Regions (Sabse top-level unit).
- Availability Zones (AZs).
- Edge Locations (CDN points).
- Regional Edge Caches.
- Data Centers.

<br>


**1 - Regions**:

Region ek geographical area hota hai jahan AWS ke multiple data centers hote hain.

Example:
- Mumbai → ```ap-south-1```.
- Ohio → ```us-east-2```.
- Frankfurt → ```eu-central-1```.

Important points:
- Har region completely isolated hota hai.
- Data automatically region ke bahar nahi jata (jab tak tum configure na karo).
- Compliance aur latency ke liye region choose karte hain.

Example:
- Agar tumhari users India mein hain → tum Mumbai region use karoge taaki latency kam ho.

<br>

**2 - Availability Zones (AZs)**:

Availability Zone (AZ) = Region ke andar ek independent data center (ya cluster of data centers).

Availability Zone ek data center ko bolte hain. 

Data center vo physical location hoti hai jahan bade level par Servers, Storage, Network Gear, Cooling System aur Power Backup rakhe hote hain. Inhi data centers mein aws ki services run hoti hain.

Key characteristics:
- Ek region mein multiple AZs hote hain (usually 2–6).
- Har AZ:
  - Power independent hota hai.
  - Network independent hota hai.
  - Cooling independent hota hai.
 
Matlab:
- Agar ek AZ down ho gaya, baaki AZs still working rahenge.

<br>

**3 - Edge Locations (CDN points)**:

Edge Locations wo jagah hain jahan AWS apni CDN service Amazon CloudFront ke through content cache karta hai.

Isko tum mini data center bhi samjh sakte ho jo multiple cities mein available hota hai.

Purpose:
- Static content fast deliver karna (images, videos, JS, CSS).
- Latency reduce karna.

Example:
- User Delhi mein hai, server Mumbai mein hai.
- Instead of Mumbai se data lana → nearest edge location se milega.

<br>
<br>

### Ye sab kaam kaise karte hain ek saath?

- User browser se request karta hai.
- Request nearest Edge Location tak jaati hai.
- Agar cache mein data hai → wahi se user ko response mil jayega.
- Nahi hai → Region ke server (EC2/S3) tak request.
- Region ke andar multiple AZs ensure karte hain ki system down na ho.

<br>
<br>

### Kya AWS ke regions apas mein connected hote hain via highspeed cable, jaise azure ka khud ka ek private network hai, kya aws ka bhi esa hi hai?

Haan, AWS ke regions bhi aapas mein high-speed private network se connected hote hain — bilkul Microsoft Azure ki tarah.

AWS ka apna global private network backbone hota hai, matlab khud ka high-speed fiber cable se bana hua private network hai.

Ye internet pe depend nahi karta (ya minimal depend karta hai).

Is backbone ko operate karta hai: **Amazon Web Services**.

<br>

**Ye private network network actually hota kya hai?**

AWS ne duniya bhar mein:
- Submarine fiber optic cables (samundar ke neeche),
- Terrestrial fiber cables (land pe),

deploy kiye hue hain.

Ye sab milke ek global private backbone network banate hain jo:
- Regions ko connect karta hai.
- AZs ko connect karta hai.
- Edge locations ko connect karta hai.

Jab tum AWS ke andar services use karte ho (same account ya VPC peering etc.), traffic mostly AWS ke private network pe hi travel karta hai.

<br>

**AWS ka internal traffic flow**:

Case 1: Same Region (e.g., Mumbai):
- EC2 → RDS dono same region mein bane hue hain to,
- Traffic stays inside region.
- Fully private network.

Case 2: Different Regions (e.g., Mumbai → Singapore)

By default:
- Traffic AWS backbone use karta hai.
- Internet pe nahi jata (unless explicitly routed).

