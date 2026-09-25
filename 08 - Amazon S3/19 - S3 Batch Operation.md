# S3 Batch Operation

Amazon S3 Batch Operations AWS ki ek bohot hi powerful aur fully managed data management feature hai. Agar aasan shabdon mein kahein, to jab aapko S3 bucket ke andar kisi ek ya do files par kaam karna hota hai, to aap normal tarike se kar lete hain. Lekin jab aapke paas hazaron, lakhon, ya arabon (billions) objects/files hon aur un sabhi par ek sath koi action lena ho, to wahan kaam aata hai S3 Batch Operations.

Maan lijiye aapke paas ek S3 bucket hai jismein 5 crore (50 million) files hain, aur aapko un sabhi files par ek naya tag lagana hai ya unhe kisi dusre bucket mein copy karna hai. Agar aap ek normal Python script likhenge, to usko chalne mein din lag jayenge, network failure handle karna padega, errors track karne padenge, aur infrastructure ka kharcha alag hoga.S3 Batch Operations isi problem ko ek single click mein hal karta hai.

<br>
<br>

### S3 Batch Operations Ke Core Components (Yeh Kaise Kaam Karta Hai?)

S3 Batch Operation ko chalane ke liye 4 main pillars (components) hote hain. Jab aap koi batch job create karte hain, to aapko ye 4 chizen specify karni hoti hain:

**1. The Manifest (Files Ki List)**:

S3 Batch Operations ko kaise pata chalega ki use kin-kin files par action lena hai? Uske liye aap use ek list dete hain jise Manifest kaha jata hai. Manifest file ek simple list hoti hai jismein har ek object ka naam (Bucket Name aur Key) hota hai.

Aap ye list do tareeqon se generate kar sakte hain:
- **S3 Inventory Report (Recommended)**: Yeh S3 ka ek built-in feature hai jo roz ya har hafte aapke bucket ke saare objects ki ek CSV format mein list (inventory) automatic generate kar deta hai. S3 Batch Operations direct is inventory report ko accept kar leta hai.
- **Custom CSV File**: Agar aapko poore bucket par nahi, balki chuninda 10 lakh files par action lena hai, to aap khud ek CSV file bana sakte hain. Is CSV file mein sirf do columns hote hain: Bucket Name aur Object Key (File Path). Agar versioning active hai, to aap teesra column Version ID ka bhi jod sakte hain.

<br>

**2. Job Definition (Action & Priority)**:

Jab aap manifest file de dete hain, tab aap ek "Job" create karte hain. Job ka matlab hai ki aap AWS ko bata rahe hain ki aapko kya action lena hai (jaise copy karna hai, tag lagana hai, etc.). Yahan aap Job ki Priority (priority number) bhi set kar sakte hain, taaki agar multiple jobs chal rahi hain, to zaroori kaam pehle ho sake.

Manifest dene ke baad aap AWS ko batate hain ki un files ke saath karna kya hai. AWS S3 Batch Operations default taur par niche diye gaye actions ko support karta hai:

- Copy (Replication/Migration): Lakhon files ko ek bucket se uthakar doosre bucket mein copy karna (bhale hi doosra bucket kisi alag AWS account ya alag region mein ho).
- Replace Object Tags: Purane saare tags hatakar naye key-value tags lagana.
- Modify Object Tags: Existing tags mein naye tags jodna ya unhe badalna.
- Restore from Glacier: Agar aapka karodon files ka data Glacier (archive) storage mein soya pada hai, to unhe ek saath wapas active (Standard) storage mein lana.
- Put Object Legal Hold / Retention: Lakhon files par ek saath Object Lock (Legal Hold) lagana takki unhe koi delete na kar sake.
- Invoke AWS Lambda Function (Sabse Powerful): Agar aapka kaam upar diye gaye standard actions se poora nahi hota, to aap har ek file par ek custom Lambda Function chala sakte hain (Jaise: lakhon text files ka data read karke badalna, images ko compress karna, ya files ka naam change karna). to aap ek custom Lambda function isse attach kar sakte hain. S3 Batch automatically list ke har ek object ke liye us Lambda ko parallelly trigger kar dega.

<br>

**3. IAM Role (Permission)**:

Kyunki S3 Batch Operations aapke bihaaf behalf par croren files ko read, write, ya delete karega, isliye aapko ek IAM (Identity and Access Management) Role banana padta hai. Is role mein permissions hoti hain ki S3 Batch aapke Manifest file ko read kar sake, destination bucket mein write kar sake, aur agar Lambda function hai to use execute kar sake.

<br>

**4. Completion Report**:

Job khatam hone ke baad, S3 Batch Operations ek Completion Report generate karke aapke bataye gaye kisi doosre S3 bucket mein save kar deta hai. Is report mein saari details hoti hain:
- Kaun-kaun se objects successfully process ho gaye.
- Kaun-kaun se objects fail hue aur unka exact Error Code/Reason kya tha.

<br>
<br>

### S3 Batch Job Ka Complete Lifecycle (Step-by-Step Execution)

Jab aap console, CLI, ya SDK se ek Batch Job create karte hain, to backend mein woh kai states se hokar guzarti hai:
```
[New] ──> [Preparing] ──> [Awaiting Confirmation] ──> [Active / Running] ──> [Completed]
```

**Step 1: Preparation**:

Jaise hi aap Job create karte hain, S3 pehle Preparing state mein jata hai. Is state mein S3 aapki di hui Manifest file (CSV) ko read karta hai, check karta hai ki format sahi hai ya nahi, aur total kitni files par action lena hai uski ginti (count) karta hai.

<br>

**Step 2: Awaiting Confirmation**:

Agar aapne batch job banate waqt "Confirmation Required" ka option select kiya hai, to S3 saari taiyari karke ruk jayega. Yeh aapko console par dikhayega: "Maine Manifest padh liya hai, isme total 45 Lakh files hain. Kya main action shuru karoon?" Jaise hi aap Confirm dabayenge, job Active ho jayegi.

<br>

**Step 3: Execution**:

Active hote hi S3 backend mein karodon computational threads chalu kar deta hai. Yeh aapke aam server ki tarah ek-ek karke file process nahi karta. Yeh ek saath thousands of requests per second chala kar parallel mein files par action leta hai. Aap console par live progress bar dekh sakte hain ki kitne percent files process ho chuki hain, kitni success hui hain, aur kitni fail hui hain.

<br>

**Step 4: Completion & Completion Report**:

Job poori hone ke baad status Completed ho jata hai. S3 Batch Operations ka sabse behtareen feature hai iski Completion Report.
- Yeh report aapke bataye gaye ek log folder mein save ho jaati hai.
- Isme ek-ek file ka status hota hai: file ka naam, action successful hua ya nahi, aur agar fail hua to uske piche ka exact HTTP Error Code (jaise 403 Access Denied ya 404 Not Found) kya tha. Isse debugging bohot aasan ho jaati hai.

<br>
<br>

### Yeh Kab Use Karna Chahiye? (Real-World Use Cases)

**Data Migration**: Jab koi company apna data ek S3 bucket se dusre naye bucket mein shift karti hai.

**Disaster Recovery (DR)**: Kisi dusre AWS region mein backup data ka replica bulk mein taiyar karna.

**Security Compliance**: Achanak pata chale ki 50 lakh files par encryption enabled nahi hai, to batch operation chala kar sabhi par ek sath KMS/S3 Managed Encryption apply karna.

**Data Cleansing / Machine Learning**: Lakhon raw text files ya CSVs ko process karke ML model ke liye taiyar karne ke liye Lambda invoke karna.

<br>
<br>

### Failure Handling Aur Limits

S3 Batch Operations ko handle karte waqt kuch architectural precautions lena zaroori hai:
- **Job Cancellation**: Agar aapne dekha ki batch job chalu hote hi errors aane lage hain (jaise permissions galat thin), to aap chalti hui job ko beech mein Cancel kar sakte hain. Jo files process ho chuki hain wo waise hi rahengi, baaki bachi hui ruk jayengi.
- **Throttling Risk**: Kyunki batch operations bohot tezi se requests bhejta hai, isliye agar aapne sahi se prefixes partition nahi kiye hain (jaisa humne prefix partitioning mein padha tha), to aapka bucket throttle ho sakta hai. AWS recommends ki batch jobs ko aaram se scale hone diya jaye.
- **No Rollback**: Agar aapne batch job se lakhon files ke tags replace kar diye ya unhe copy kar diya, to koi "Undo" ya "Ctrl+Z" button nahi hota. Galat action ko sudharne ke liye aapko ek nayi counter-batch job chalani padegi.

**Limits**:
- **Object Limits**: Ek single batch job default roop se 4 billion (400 crore) objects ko process kar sakti hai. Copy, Tagging aur Lambda operations ke liye yeh limit 20 billion objects tak ja sakti hai.
- **At-least-once execution**: AWS guarantee deta hai ki manifest mein di gayi har ek file par kam se kam ek baar action zaroor liya jayega. Rare cases mein agar koi network retry hota hai, to ek file do baar bhi process ho sakti hai (isliye Lambda functions idempotent hone chahiye).

<br>
<br>

### Pricing Breakup (S3 Batch Operation Kitna Paisa Leta Hai?)

S3 Batch Operations bilkul muft nahi hai. Isme do tarah ke main charges lagte hain jo aapke bill mein aate hain:
- **Job Fee**: Aap jitni baar ek nayi batch job banakar use execute karte hain, AWS aapse par-job ek fixed fees leta hai (Aam taur par **$0.25 per job**).
- **Object Processing Fee**: Aapne us job ke andar jitni files process ki hain, uski ginti ke hisab se charge lagta hai (Aam taur par **$1.00 per million** objects processed).

Note: Iske alawa jo standard S3 charges hain (jaise data copy karne ki PUT request fees ya Lambda execution cost), wo alag se add honge.

**Example**:

Chaliye ek simple cost calculation model dekhte hain:

Maan lijiye aapke paas ek job hai jismein aapko 50 Million (5 crore) objects ko copy karna hai:
- Fixed Job Fee = $0.25
- Object Fee = 50 Million × $1.00 = $50.00
- Total S3 Batch Operations Cost = $50.25 (Approx ₹4,200)

(Note: Jo internal S3 COPY ya PUT requests lagengi aur data transfer ka cost hoga, wo aapke standard S3 pricing ke hisab se alag se bill hoga).

<br>
<br>
<br>

## LAB: S3 Batch Operation

**Scenario**: Is lab mein hum seekhenge ki kaise ek Manifest file ka use karke hum apni bucket ke multiple objects par ek sath custom Tags apply karenge.

<br>

### Lab Prerequisites & Setup

S3 Batch Operations ko chalane ke liye 3 cheezein zaroori hain:
- Source Bucket: Jisme aapki files rakhi hain.
- Manifest File: Ek aisi ```.csv``` file jo AWS ko batati hai ki kis-kis file par action lena hai.
- IAM Role: Jo Batch Operations ko permission dega bucket ke andar kaam karne ki.

**Step A: Dummy Files Upload Karein**: Apni bucket (```meri-bucket-puneet```) ke andar jayein aur 3-4 dummy pictures ya text files upload kar dein (jaise ```file1.txt```, ```file2.txt```, ```file3.txt```).

**Step B: Manifest.csv File Banayein**: Batch Operations ko ek guide-map chahiye hota hai jise manifest kehte hain.
- Apne computer par Notepad kholein.
- Niche diye gaye format mein apni bucket ka naam aur files ke naam likhein (Dhyan rahe, isme koi space nahi hona chahiye, bas bucket name aur file key):
```
meri-bucket-puneet,file1.txt
meri-bucket-puneet,file2.txt
meri-bucket-puneet,file3.txt
```
- Is file ko save karein aur naam rakhein ```manifest.csv``` (Save as type mein All Files select karke .csv extension lagayein).
- Is ```manifest.csv``` file ko bhi apni ```meri-bucket-puneet``` bucket ke andar normal tarah se upload kar dein.

<br>
<br>

### Step-by-Step Batch Operations Tagging Lab

**Step 1: Batch Operations Menu Mein Jayein**:
- S3 Dashboard ke left-side panel/menu mein dekhein. Wahan aapko Batch Operations ka option dikhega, uspar click karein.
- Orange color ke Create job button par click karein.

**Step 2: Region Aur Manifest File Select Karein**:
- **AWS Region**: Wahi region chunein jahan aapki bucket hai (e.g., Asia Pacific (Mumbai) ap-south-1).
- **Manifest format**: Amazon S3 csv wale radio button par click karein.
- **Manifest object**: Browse S3 par click karein, apni bucket (```meri-bucket-puneet```) ke andar se jo ```manifest.csv``` upload ki thi, use select kar lein.Niche scroll karke Next par click karein.

**Step 3: Action Chunein (Replace Object Tags)**:

Yahan aapko saare batch actions dikhenge.
- List mein se Replace object tags wale option ko select karein.
- Niche Options ka section khulega. Wahan aapko apna custom tag lagana hai.
  - ```Key: Environment```.
  - ```Value: Production```.
- Iska matlab in saari files par ```Environment=Production``` ka tag lag jayega. Next par click karein.

<br>
<br>

### Step 4: Completion Report Aur IAM Role Set Karein

- Completion report: Jab job khatam hogi, toh AWS ek report banakar deta hai. Ise Create a completion report par check rehne dein.
- Path to completion report destination: Browse S3 karke apni wahi bucket (meri-bucket-puneet) select kar lein taaki report wahi save ho.
- Permissions (IAM Role): Niche scroll karein. Choose from existing IAM roles ke badle Create a new IAM role with standard policies select kar lein (ya agar automated option na dikhe, toh inline tools ke zariye default generated role select karein jo AWS suggest kare). AWS khud back-end par resources access karne ka role bana dega.
- Next par click karein, saari details review karein aur sabse niche jaakar Create job par click kar edin.

<br>
<br>

### Step 5: Job Ko Run Karein (Most Important Step)

Batch Operations mein job bante hi direct run nahi hoti, wo pehle "Awaiting your confirmation" state mein rukti hai taaki aap galti se crore-on files par galat operation na chala dein.
- Batch Operations ke dashboard par aapko aapki job Awaiting your confirmation status ke sath dikhegi.
- Us Job ID ke samne wale radio button par tick lagayein.
- Upar top-right mein ek button active hoga: Run job. Uspar click karein.
- Ek review page aayega, sabse niche jaakar Run job par dobara click kar dein.
- Ab status Preparing se badalkar Active aur fir Completed ho jayega. (3 files ke liye isme mushkil se 10-20 seconds lagenge).

<br>
<br>

### Live Testing (Kaise Check Karein?)

- Wapas apni S3 bucket meri-bucket-puneet ke andar jayein.Kisi bhi ek file (jaise file1.txt) ke naam par click karke use open karein.
- Uske Properties tab mein jayein aur thoda niche scroll karke Tags ka section dekhein.

Boom! Aapko wahan ```Environment: Production``` ka tag automatic laga hua dikh jayega. Aapne ek click mein saari files par tag apply kar diya hai!
