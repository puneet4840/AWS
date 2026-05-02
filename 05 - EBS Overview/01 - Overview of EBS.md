# Overview of EBS

EBS = Elastic Block Store

EBS Volume disk hoti hai jo EC2 ke saath attach hoti hai.

Ye AWS ka block-level storage system hai jo primarily EC2 instances ke saath use hota hai. Simple language mein, EBS tumhare cloud server ki “hard disk” ya “SSD” ki tarah kaam karta hai.

Jab tum AWS mein EC2 instance launch karte ho, tab EC2 basically ek virtual machine hoti hai jo CPU, RAM, aur networking provide karti hai.

Lekin sirf CPU aur RAM se koi bhi system practical kaam nahi kar sakta, kyunki operating system ko install karne ke liye, applications ko store karne ke liye, logs ko save karne ke liye, databases ko maintain karne ke liye, aur user files ko preserve karne ke liye storage chahiye hoti hai.

Isi problem ko solve karne ke liye AWS ne EBS provide kiya, jo EC2 ke liye persistent block-level storage system hai. Persistent ka matlab ye hai ki agar tumhara EC2 stop ho jaye ya restart ho jaye, to tumhara data survive kar sakta hai, kyunki storage compute se separate maintained hoti hai.

Ye separation bahut important hai kyunki traditional physical server mein agar disk fail ho jaye to major recovery issue ho sakta hai, jabki AWS architecture storage ko more flexible aur resilient banata hai.

<br>
<br>

### EBS Ka Real Meaning

Elastic Block Store naam ke andar hi iska design philosophy chhupa hua hai.

**Elastic**:

Elastic ka matlab flexible ya dynamically adjustable hota hai.

AWS ne EBS ko is tarah design kiya hai ki tum storage size ko increase kar sako, performance type change kar sako, snapshots le sako, encryption apply kar sako, aur workload ke hisaab se optimize kar sako bina traditional hardware replacement ke.

Ye business ke liye useful hai kyunki requirements static nahi hoti; aaj 100 GB chahiye, kal 500 GB ho sakta hai.

<br>

**Block**:

Block storage ka matlab hota hai ki operating system storage ko small addressable blocks mein dekhta hai, bilkul waise hi jaise physical SSD ya HDD ko dekhta hai.

Isliye OS ko lagta hai ki uske paas ek normal disk attached hai, aur tum NTFS, EXT4, XFS jaise file systems use kar sakte ho.
Ye database aur OS workloads ke liye ideal hai kyunki low-level disk operations possible hote hain.

<br>

**Store**:

Store simply indicates data retention. Matlab data permanently store. Ye sirf temporary cache nahi hai; iska main kaam durable storage provide karna hai.

<br>
<br>

### Why EBS Instead of Internal Disk?

Traditional servers mein storage often machine ke andar physically hoti hai.

AWS ne storage ko logically separate design kiya taaki compute aur storage independently manage ho sakein. Iska benefit ye hai ki agar tumhe bigger server chahiye, to tum instance resize kar sakte ho bina data ko manually migrate kiye.

Similarly, agar tumhe storage performance improve karni hai, to tum volume type change kar sakte ho bina poora server rebuild kiye. 

Ye architecture DevOps aur enterprise operations ke liye huge advantage hai.

<br>
<br>

