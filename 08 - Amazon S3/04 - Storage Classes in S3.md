# Storage Classes in S3

AWS S3 mein storage class ka matlab hai storage tier. Matlab AWS S3 mein upload hue objects ko kaise store karega aur objects kaise access honge.

Storage class simply ek storage tier hai, jo batata hai ki apka data jo s3 bucket ke ander store hai, usko aap kitni frequently access kar sakte hain.

Storage Class ek storage tier hoti hai jo define karti hai ki AWS object ko kis type ki storage infrastructure mein store karega aur us object ki cost, availability, durability, retrieval speed aur intended access pattern kya hoga.

Simple words mein:

Storage Class ye decide karti hai ki:
- Object kis purpose ke liye store ho raha hai.
- Object kitni frequently access hoga.
- Storage ki cost kitni hogi.
- Retrieval cost kitni hogi.
- Retrieval kitni fast hogi.

Jaise Azure mein storage accounts ke tier hote hain: Hot, Cool, Cold aur Archieve. Isi tarah AWS ke S3 service ke ander storage classes hoti hain.

Storage classes simply batati hain ki object kitni frequently access hoga.

<br>

Storage Class actually define karti hai ki kisi object ko kis tarah store kiya jayega, uski cost kitni hogi, usse retrieve karne mein kitna time lagega, uski availability kitni hogi aur wo kis type ke workload ke liye suitable hai.

<br>

S3 Storage Classes basic mein alag-alag "Buckets ya Tiers" hain jahan aapka data store hota hai. Har class ke piche AWS ka hardware aur management system alag hota hai.
- Agar aapko data har roz chahiye, toh alag storage class hai.
- Agar aapko data saal mein ek baar dekhna hai, toh alag storage class hai.

Inki wajah se aapko Cost (Paisa), Availability (Data kab milega), aur Performance (Kitni jaldi milega) ka alag-alag combination milta hai.

<br>
<br>

### AWS ne S3 Storage Classes kyun banayi?

Jab Amazon S3 launch hui thi, tab usme sirf ek hi storage type available thi wo thi **S3 Standard**.

Us samay ye sufficient thi, kyunki users ka zyada focus sirf data ko safely store karne par tha. 

Lekin dheere-dheere AWS ne observe kiya ki har customer ka data ek jaisa nahi hota:
- Kuch data aisa hota hai jise application har second access karti hai.
- Kuch data aisa hota hai jise mahine mein sirf ek baar access kiya jata hai.
- Kuch data sirf compliance ke liye 10 saal tak store rakhna hota hai, lekin usse kabhi access nahi kiya jata.
- Aur kuch data aisa hota hai jiska access pattern predictable hi nahi hota.

Agar AWS in sabhi data types ke liye ek hi pricing model rakhta, to customers ko ya to unnecessary paise dene padte, ya performance compromise karni padti. Isi problem ko solve karne ke liye AWS ne **S3 Storage Classes** introduce ki.

Storage Class ka matlab sirf "alag storage location" nahi hota.

Storage Class actually define karti hai ki kisi object ko kis tarah store kiya jayega, uski cost kitni hogi, usse retrieve karne mein kitna time lagega, uski availability kitni hogi aur wo kis type ke workload ke liye suitable hai.
