# Transit Gateway Overview

Isse pehle wali slide mein humne VPC Peering ke baare mein padha tha, Jab 2 ya usse jyada VPCs ko privately connect karna hota hai to VPC Peering ka use karte hain.

Agar apke paas 2 se 5 VPCs hain to aap VPC Peering se inko aapas mein connect kar sakte ho, Lekin agar 10, 50, ya 100 VPCs hain to to VPC peering complex ho jati hai, kyunki aapko har VPC ke beech mein alag connection banana padta hai (jisko Mesh Network kehte hain).

To itne saare VPCs ko aaps mein connect karne ke liye AWS ne **Transit Gateway** service ko launch kiya.

<br>

Transit Gateway is problem ko solve karta hai. Yeh ek Hub-and-Spoke model par kaam karta hai. Isme Transit Gateway beech mein "Hub" (central router) hota hai, aur saare VPCs ya data centers uske "Spokes" (connections) ban jaate hain.

<br>
<br>

### Transit Gateway kya hota hai?
