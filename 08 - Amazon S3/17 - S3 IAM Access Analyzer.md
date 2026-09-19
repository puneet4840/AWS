# S3 IAM Access Analyzer

IAM Access Analyzer for S3 (jise Amazon S3 Access Analyzer bhi kaha jata hai) AWS ki ek aisi powerful security feature hai jo aapke buckets ko secure rakhne ka kaam karti hai. Iska main maqsad yeh check karna hota hai ki kya aapka koi S3 bucket galti se internet par public to nahi ho gaya hai ya fir kisi external AWS account ko iska access mil gaya hai.

Jab bade enterprise ya multi-account setups hote hain, to hazaron buckets mein galti se public permission lag jana ek aam baat hai. S3 Access Analyzer isi risk ko khatam karta hai.

<br>

### Yeh Kaam Kaise Karta Hai?

S3 Access Analyzer aapke buckets par lagi saari resource-based policies ko automatic aur continuously monitor karta hai. Isme shaamil hain:
- Bucket Policies.
- Bucket ACLs (Access Control Lists).

Yeh un sabhi policies ko scan karke Mathematic Proofs (Zelkova technology) ka use karta hai aur un permissions ka analysis karta hai. Agar koi bhi policy aisi milti hai jo aapke AWS Organization ya aapke khud ke account ke bahar kisi ko bhi access de rahi ho, to yeh turant ek alert create kar deta hai, jise Finding kehte hain.

<br>

### Yeh Kin 2 Main Risks Ko Alert Karta Hai?

S3 Access Analyzer main roop se do tarah ke access ko dhoondhta hai:
- **Public Access**: Agar koi bucket poore internet ke liye khula hai (chahe wo sirf read-only ho ya write access), yeh use turant spot karega.
- **Shared with External Accounts**: Agar aapne apni kisi bucket ka access kisi external company, vendor, ya kisi aise AWS account ko de rakha hai jo aapke AWS Organization ka hissa nahi hai, to yeh use bhi point-out karega.


<br>
<br>

### S3 Console Mein Yeh Kahan Aur Kaise Dikhta Hai?

Pehle isko dekhne ke liye IAM console mein jana padta tha, lekin ab AWS ne ise S3 Console ke left sidebar mein hi "Access Analyzer for S3" naam se jod diya hai.

- AWS S3 Console mein jayein.
- Left-hand side menu mein aapko Access Analyzer for S3 ka option dikhega.
- Wahan dashboard par aapko ek clear summary dikhegi:
  - **Buckets with public access**: (Yahan un buckets ki sankhya dikhegi jo public hain).
  - **Buckets shared externally**: (Yahan un buckets ki list hogi jo doosre accounts ke sath shared hain).
 
<br>
<br>

### Findings Milne Par Aap Kya Kar Sakte Hain?

Jab Access Analyzer kisi bucket ko external ya public dhoondhta hai, to apko alert karta hai to aapke paas teen (3) choice hoti hain:
- **Remediate (Fix karein)**: Agar aapko lagta hai ki yeh galti se public ho gaya hai, to aap wahan se direct bucket settings mein jaakar Block Public Access ko ON kar sakte hain ya bucket policy ko delete kar sakte hain.
- **Archive (Accept karein)**: Maan lijiye aapki website ki assets (jaise images/logos) public hain aur aap chahte hain ki wo public hi rahein kyunki wo intentional hai. To aap us finding ke liye ek Archive Rule bana sakte hain. Agli baar se Access Analyzer use alert nahi karega, par backup mein dhyan rakhega.
- **Review Later**: Aap use pending rakh kar apni security team se discuss kar sakte hain.

<br>
<br>

### IAM Access Analyzer For S3 Ke Fayde (Benefits)

**Continuous Monitoring**: Yeh koi one-time check nahi hai. Jaise hi koi employee kisi bucket par koi nayi policy lagayega jo public access deti ho, Access Analyzer kuch hi minutes ke andar nayi finding generate kar dega.

**No Extra Performance Load**: Yeh bucket ke andar ke data ko read nahi karta, balki sirf metadata aur policies ko evaluate karta hai. Isliye isse aapke S3 ki latency ya performance par 0% asar padta hai.

**Absolutely FREE**: S3 console ke andar Access Analyzer ka use karna bilkul free hai. AWS iska koi alag se charge nahi leta.

<br>
<br>

### Zaroori Technical Restrictions (Limitation)

**Region-Specific**: IAM Access Analyzer region-specific hota hai. Agar aapke buckets Mumbai, US, aur Europe alag-alag regions mein hain, toh aapko har ek region mein ja kar ise alag se enable (activate) karna padega.

**No Object-Level Tracking**: Yeh tool sirf Bucket, Access Points aur resource-level policies ko scan karta hai. Agar aapne bucket ke andar kisi single file (object) par manually ja kar Object ACL se use public kiya hai, toh Access Analyzer use detect nahi kar payega. uske liye aapko S3 Inventory Reports use karni padengi.
