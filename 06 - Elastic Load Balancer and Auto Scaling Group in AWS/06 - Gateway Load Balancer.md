# Gateway Load Balancer Overview

Gateway Load Balancer ALB aur NLB se bilkul different hota hai. Ye application traffic ko serve karne ke liye nhi hota.

Gateway Load Balancer traffic ko inspect karne ke liye hota hai matlab traffic mein koi vulnerabilities to nhi hain.

<br>

Gateway Load Balancer (GWLB) ek Layer 3/Layer 4 load balancer hai jo network traffic ko third-party virtual security appliances, jaise Firewalls, IDS aur IPS ke paas bhejta hai, taaki traffic inspect hone ke baad hi apni destination tak pahunch sake.

Iska matlab hai ki Gateway Load Balancer source aur destination beech mein rehta hai aur network traffic ko destination par pahuchne se pehle third-party security services ko behjta hai, wahan se traffic ko inspect karwake fir destination par bhejta hai.

Security appliances, Jaise:
- Firewall.
- Intrusion Detection System (IDS).
- Intrusion Prevention System (IPS).
- Deep Packet Inspection (DPI).
- Malware Inspection.
- Network Monitoring Appliances

In sab se traffic ke packet ko inspect karwake fir destination par bhejta hai.

<br>

**Diagram**:

<img src="https://drive.google.com/uc?export=view&id=1gbcIEp-mb4wg07kCqRUIhpIo3zlHDGeJ" width="600" height="430">

<br>

Gateway Load Balancer ek aisa AWS Load Balancer hai jo network traffic ko security appliances tak bhejta hai, un security appliances ke beech load distribute karta hai aur traffic ko inspect karwane ke baad usse uske destination tak forward karta hai.

<br>
<br>

### Sabse pehle problem samajhte hain

Maan lo tum ek Bank mein DevOps Engineer ho.

Bank ki Application AWS mein chal rahi hai.

Architecture.
```
Internet

↓

Application Load Balancer

↓

EC2 Web Servers

↓

Database
```
Sab kuch sahi chal raha hai.

Lekin Bank ki Security Team ek nayi requirement deti hai. Wo bolti hai.
- ```Har request ko pehle Security Firewall ke through jana chahiye.```
- ```Har packet ko Malware Scanner check kare.```.
- ```Intrusion Detection System (IDS) bhi har traffic inspect kare.```

Yaani har packet ko application tak pahunchne se pehle inspect karna hai.

<br>

**Sawal**:

Ab Internet se jo request aa rahi hai. Usko kaise ensure karoge ki wo pehle Firewall ke paas jaye? Phir IDS ke paas jaye. Phir IPS ke paas jaye.

Aur uske baad hi Application Server tak pahunch paye. Agar tum iske liye manually routes banaoge.

To architecture bahut complex ho jayega.

<br>

**AWS ne kya socha?**

AWS Engineers ne dekha.

Customers third-party Security Appliances use kar rahe hain.

Jaise.
- Palo Alto Firewall
- Fortinet Firewall
- Check Point Firewall
- Cisco Firewall
- Suricata IDS
- Trend Micro
- F5 Appliances

Lekin in Appliances ke saamne traffic distribute karna bahut difficult tha. Isliye AWS ne **Gateway Load Balancer** introduce kiya.

<br>
<br>
