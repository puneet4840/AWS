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

