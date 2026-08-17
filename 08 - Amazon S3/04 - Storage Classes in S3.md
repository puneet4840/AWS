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

Jab hum AWS S3 (Simple Storage Service) ki baat karte hain, to sirf itna samajhna kaafi nahi hai ki S3 ek object storage service hai jisme hum files store karte hain. Real-world production environments mein sabhi files ki importance, access frequency, performance requirement aur retention period alag-alag hota hai. Isi wajah se AWS ne ek hi tarah ki storage provide nahi ki, balki multiple Storage Classes provide ki hain, taaki organizations apne data ki requirement ke hisab se cost aur performance ko optimize kar saken.

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

<br>

**Example**:

Socho kisi company ke paas 500 TB data hai.

Is data mein kuch files aisi hain jo har second users access karte hain. Jaise website ki images, application ke CSS aur JavaScript files, user profile pictures, ya product catalog images. Agar ye files slow storage mein rakhi jayengi to application ka response time kharab ho jayega.

Lekin isi company ke paas kuch aur files bhi hain, jaise:
- 5 saal purane invoices
- Old audit reports
- Historical application logs
- Backup files
- Compliance documents

Ye files shayad saal mein ek ya do baar hi access hoti hain.

Ab agar company in purani files ko bhi usi expensive high-performance storage mein rakhegi jahan frequently accessed files rakhi hui hain, to company har mahine unnecessary storage cost pay karegi.

Isi problem ko solve karne ke liye AWS ne alag-alag Storage Classes introduce ki.

Har Storage Class ka objective alag hai:
- Kuch classes maximum performance provide karti hain.
- Kuch classes lowest storage cost provide karti hain.
- Kuch classes disaster recovery ke liye bani hain.
- Kuch classes archival purpose ke liye design ki gayi hain.
- Kuch classes automatically data ko optimize karti hain.

<br>

Maan lo ek company ke paas alag-alag type ka data hai.
- Application ki images
- Customer ke documents
- Database backups
- 10 saal purane archives
- Daily logs
- Machine learning datasets

Kya in sab data ka usage pattern same hoga? Nahi.
- Kuch data har second access hota hai.
- Kuch data mahine mein ek baar access hota hai.
- Kuch data sirf emergency ke time access hota hai.

Agar AWS sab data ko ek hi type ki storage mein rakhe, to customer ko unnecessary zyada cost deni padegi. Isi problem ko solve karne ke liye AWS ne Storage Classes introduce ki.

<br>
<br>

### S3 Storage Classes Kitni Hain?

AWS currently major ye Storage Classes provide karta hai.
- S3 Standard.
- S3 Intelligent-Tiering.
- S3 Standard-Infrequent Access (Standard-IA).
- S3 One Zone-Infrequent Access (One Zone-IA).
- S3 Glacier Instant Retrieval.
- S3 Glacier Flexible Retrieval.
- S3 Glacier Deep Archive.

<br>

### 1. S3 Standard:

S3 Standard AWS ki default Storage Class hai.

Agar tum object upload karte waqt koi Storage Class specify nahi karte to AWS automatically us object ko S3 Standard mein store karta hai.

Ye Storage Class un files ke liye design ki gayi hai jin files ko users frequently access karte hain aur jinke liye low latency aur high throughput bahut important hota hai. Matlab jab files ko baar baar access karna ho to tab object is storage class mein store hota hai.

Is class mein data multiple Availability Zones (AZs) mein replicate hota hai.

**Ye multiple AZs mein replicate kyu hota hai?**

AWS ka objective sirf data ko store karna nahi hota, balki us data ko highly available aur durable banana hota hai. Agar ek Availability Zone mein hardware failure, storage failure, power outage ya kisi aur reason se problem aa jaye, tab bhi data dusri Availability Zones mein available rehta hai.

Isi wajah se S3 Standard bahut high durability (11 nines) aur high availability provide karta hai.

**Kab use karte hain?**

Ye class un workloads ke liye best hai jahan users baar-baar data access karte hain.

Examples:
- Website images.
- Mobile application assets.
- Videos jo regularly stream hote hain.
- User uploaded photos.
- Application configuration files.
- Frequently accessed business documents.

**AWS ne is class ko default kyun banaya?**

Reason ye hai ki production applications ka majority data frequently access hota hai.

**Iski Characteristics**
- Low latency.
- High throughput.
- Milliseconds mein object retrieval.
- Multi Availability Zone storage.
- High Availability.
- 11 nines durability (99.999999999%).

**Iska disadvantage kya hai?**

Iska storage cost sabse zyada hota hai (archive classes ke comparison mein), kyunki AWS continuously high availability aur fast retrieval maintain karta hai.

<br>
<br>

### 2. S3 Intelligent-Tiering

Ye AWS ki smart Storage Class hai.

Iska purpose hai:
- Customer ko manually decide na karna pade ki object kis Storage Class mein hona chahiye.

Suppose ek Company mein 100 TB data hai. Company ko pata hi nahi.
- Kaunsa data frequently access hota hai.
- Kaunsa data rarely access hota hai.
- Aaj jo File upload hui vo important hai.
- Kal shayad koi use na kare.
- Aur jo File 6 mahine se use nahi hui. Kal achanak uski demand aa sakti hai.

Aise scenario mein manually decide karna almost impossible hai ki kis file ko Standard mein rakhen aur kis file ko IA mein.

Isi problem ko solve karne ke liye AWS ne **S3 Intelligent-Tiering** banaya.

Is Storage Class mein AWS continuously objects ke access pattern ko monitor karta hai.
- Agar object frequently access ho raha hai to usse frequent access tier mein rakha jata hai.
- Agar object ka access bahut kam ho jata hai to AWS automatically us object ko lower-cost tier mein move kar deta hai.
- Aur agar future mein wahi object dobara frequently access hone lage to AWS use automatically wapas frequent tier mein le aata hai.

**Ye automatic movement kyu ki jati hai?**

Objective ye hai ki customer ko manually lifecycle rules banane ki zarurat na pade aur storage cost automatically optimize hoti rahe.

Life Cycle Rule Matlab:  Jaise, "Jaise hi meri file 30 din purani ho jaye, use S3 Standard se hata kar S3 Standard-IA mein dal do. Aur jab 90 din purani ho jaye, toh Glacier mein bhej do." Isse aapko manually kuch nahi karna padta.

**Kab use karte hain?**
- Jab access pattern unknown ho.
- Jab millions of objects store hon.
- Jab manually storage classes manage karna difficult ho.

**Important point**:

Intelligent-Tiering mein monitoring ke liye per-object monitoring charge lagta hai. Ye additional charge isliye hota hai kyunki AWS continuously har object ka access pattern observe karta rehta hai.

**Ye Kaise Kaam Karti Hai?**

Suppose aaj ek object daily access ho raha hai. AWS usko Frequent Access Tier mein rakhega. Ek mahine baad object access hona band ho gaya. AWS automatically us object ko lower-cost tier mein move kar dega. Agar future mein object dobara frequently access hone lage. AWS usko wapas Frequent Tier mein le aayega. Ye process automatic hota hai. Administrator ko manually kuch karna nahi padta.

<br>
<br>

### 3. S3 Standard-Infrequent Access (Standard-IA)

Ye Storage Class un objects ke liye hai jo important hain. Lekin frequently access nahi hote.

Is Storage Class ka objective hai:
- "Data important hai, lekin frequently access nahi hota."

Matlab data ko fast retrieve karna zaruri hai, lekin users usse roz access nahi karte.


**Example**:

Ek company har mahine financial reports generate karti hai. Ye reports legally important hain. Kabhi bhi auditor ya finance team inhe access kar sakti hai. Lekin daily basis par koi in reports ko open nahi karta. Aise data ke liye **Standard-IA** suitable hai.

Ab agar Company S3 Standard use kare, To unnecessary storage cost pay karegi.

Aur agar Glacier use kare, To File instantly available nahi hogi.


Is Storage Class ka purpose hai. Storage cost kam karna. Lekin object ko milliseconds mein accessible rakhna. Yaani access kam hai. Lekin jab bhi access karo. Immediate mil jana chahiye. Retrieval ke liye additional charges lag sakte hain aur ye class generally long-lived, infrequently accessed objects ke liye suitable hoti hai.

**Iska storage cost Standard se kam kyu hota hai?**

AWS assume karta hai ki data rarely access hoga. Isliye storage ka monthly cost kam rakha jata hai.

Lekin jab bhi object retrieve karoge to retrieval charge pay karna pad sakta hai. Reason ye hai ki AWS low storage cost aur occasional access ke beech balance maintain karta hai.

**Availability aur durability**:

Standard-IA bhi multiple Availability Zones mein data replicate karta hai. Isliye durability aur availability high rehti hai.

<br>
<br>

### 4. S3 One Zone-Infrequent Access (One Zone-IA)

Ye Standard-IA jaisi hi hai.

Difference sirf itna hai ki isme data multiple Availability Zones mein replicate nahi hota. Data sirf ek hi Availability Zone mein store hota hai.

**AWS ne aisi class kyu banayi?**

Ab AWS ne observe kiya.

Kuch data aisa hota hai jise dobara create kiya ja sakta hai. Matlab kuch data easily recreate kiya ja sakta hai.

Jaise:
- Application Cache.
- Temporary Image Processing Output.
- Analytics Intermediate Files.

Agar ye data lose bhi ho jaye. To application dobara generate kar sakti hai.

AWS ne kaha, Agar customer multiple Availability Zones ki redundancy nahi chahta. To storage cost aur kam ki ja sakti hai.

Isliye AWS redundancy kam karke storage cost aur kam kar deta hai. Isi wajah se **One Zone-IA** introduce hui.

Isme object sirf ek Availability Zone mein store hota hai. Isliye ye Standard-IA se sasti hoti hai.

**Risk kya hai?**

Agar poori Availability Zone permanently unavailable ho jaye aur data ka aur kahin backup na ho, to data loss ho sakta hai. Isi wajah se One Zone-IA ko mission-critical data ke liye recommend nahi kiya jata.

**Kab Use Karein?**
- Jab data easily recreate kiya ja sakta ho.
- Ya secondary backup ho.
- Ya temporary processing files hon.

<br>
<br>

### 5. S3 Glacier Instant Retrieval

Ye archive Storage Class hai.

Kabhi-kabhi data bahut rarely access hota hai. Lekin jab access karna pade, tab immediately retrieve karna hota hai.

**Example**:

Ek hospital patient records legally 7–10 saal tak preserve karta hai. Doctor daily purane records access nahi karte.

Lekin agar patient dobara aaye, to records turant available hone chahiye. Aise scenarios ke liye Glacier Instant Retrieval design ki gayi hai.

**Iska objective kya hai?**

Storage cost Standard-IA se bhi kam rakhna aur retrieval ko milliseconds mein possible banana. Isliye ye archive-type workloads ke liye suitable hai jahan access rare hai lekin wait karna acceptable nahi.

