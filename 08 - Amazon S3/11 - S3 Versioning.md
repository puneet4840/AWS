# S3 Versioning

S3 Versioning ka matlab hai ki ek hi object ke multiple version ko store karke rakhna, kyuki object delete bhi ho jaye to uske pichle version se recover kiya ja sake.

S3 Versioning ek hi object ke multiple versions ko preserve karne ki capability hai, taaki agar kisi existing object ko overwrite ya delete kar diya jaye, to uske purane versions ko recover kiya ja sake.

Isliye Versioning particularly useful hai:
- Accidental overwrite protection ke liye.
- Accidental deletion recovery ke liye.
- Application rollback ke liye.
- Data protection ke liye.
- Replication ke saath.
- Lifecycle management ke saath.

Jab aap kisi bucket par versioning ko ON (enable) karte hain, toh AWS S3 us bucket mein aane wali har ek file ke saare purane aur naye roop (versions) ko hamesha ke liye apne paas track karke save rakh leta hai. Iska matlab hai ki agar aapne kisi file ko galti se overwrite kar diya ya delete bhi kar diya, toh aapka data hamesha ke liye gayab nahi hota; aap jab chahein uske purane roop ko wapas la (restore kar) sakte hain.

<br>
<br>

### Sabse pehle problem samjho — Versioning ki zarurat kyu padi?

Maan lo tumhare paas S3 bucket hai:
```
company-data
```
Aur usmein object hai:
```
report.pdf
```

Aaj report.pdf ke andar data hai:
```
Sales = ₹10 lakh
```
Kal tumne same naam se new file upload kar di: ```report.pdf```.

jisme:
```
Sales = ₹15 lakh
```
data hai.

Normal S3 behavior mein existing object overwrite ho jata hai, Matlab agar bucket versioning on nhi hai to purana object overwrite ho jata hai, matlab purane object mein naya data aa jata hai aur purana data delete ho jata hai.

Ab problem ye hai:
- Agar tumhe purani ```report.pdf``` wapas chahiye to?

Agar versioning enabled nahi hai, to old version recover karna generally possible nahi hota, unless tumhare paas separate backup/replication etc. ho.

Isi problem ko solve karne ke liye bucket versioning ki jati hai.

<br>
<br>

### Versioning ka main purpose kya hai?

Versioning ka primary purpose hai accidental overwrite aur accidental deletion se data recovery capability provide karna.

Maan lo production bucket mein ```app-config.json``` file hai.

Application administrator ne galti se same ```app-config.json``` file ko same bucket mein upload kar diya jisse file wrong configuration se overwrite ho gya.

Without versioning:
```
Old Configuration
       ↓
   Overwritten
       ↓
New Configuration
```
Matlab ab ```app-config.json``` file mein naya data aa gya aur purana data delete ho gaya. Ab Purani version normally recover nahi ki ja sakti. Kyuki bucket par versioning enable nhi hai.

Lekin agar bucket par Versioning enabled hai to:
```
Version 1
Old Configuration
        │
        │ overwrite
        ▼
Version 2
New Configuration
```
Version 1 preserve rahegi. Iska matlab hai ki file mein naya data aa jayega aur purane data wali file as a version 1 bucket mein hi stored rahegi.

Tum us version ko retrieve karke data recover kar sakte ho.

<br>
<br>

### Versioning ko "backup" samajhna galat hai

S3 Versioning backup service ka replacement nahi hai.

Versioning ka primary purpose object ke versions preserve karna hai.

Suppose ransomware attack hua:
```
Original File
      ↓
Version 1

Ransomware
      ↓
File Modified
      ↓
Version 2

Ransomware
      ↓
File Modified
      ↓
Version 3
```
Versioning ki wajah se old versions exist kar sakti hain.

Lekin attacker agar permissions ke through versions bhi permanently delete kar de, to versioning alone sufficient protection nahi hai.

Production environments mein versioning ko additional controls ke saath use kiya ja sakta hai, jaise:
- MFA Delete.
- Object Lock.
- Cross-Region Replication.
- Backup/DR strategy.
- Least-privilege IAM.
- Separate administrative controls.

<br>
<br>

### Versioning enable hone ke baad kya change hota hai?

Agar bucket par versioning enabled hai, to S3 same object key ke multiple versions maintain kar sakta hai.

Example:
```
report.pdf
```
Pehli baar upload:
```
Version ID = v1
```
Dusri baar upload:
```
Version ID = v2
```
Teesri baar:
```
Version ID = v3
```

Ab S3 internally conceptually:
```
report.pdf
│
├── Version v1
├── Version v2
└── Version v3
```
rakhta hai.

Important point:
- Object key same reh sakti hai, lekin har version ka unique Version ID hota hai.

<br>

**Version ID kya hota hai?**

Version ID S3 dwara generate kiya gaya identifier hai jo object ke ek particular version ko uniquely identify karta hai.

**Unique Identity**: Jab versioning ON hoti hai, to bucket mein upload hone wale har ek object ko AWS ek unique string assign karta hai, jise Version ID kehte hain (Jaise: ```3HL4kqCxF3vth4bNsGLdskni9```).

**Unversioned Objects**: Jo files versioning on karne se pehle upload ki gayi thin, unki Version ID null hoti hai.

Version ID ka matlab object ka version number jo object ko uniquely identify karne ke liye use hota hai. Agar humko particular version par wapas jana hai to version id se hi pta lagega ki previous version konsa hai.

Example:
```
Object Key:
report.pdf
```
Versions:
```
Version ID: abc123...
Version ID: xyz789...
Version ID: pqr456...
```
Tum same object key:
```
report.pdf
```
ke multiple versions rakh sakte ho.

Isliye:
```
Object Key
    +
Version ID
```
milkar specific object version ko identify kar sakte hain.

<br>
<br>

### Versioning Ke 3 Main States (States of Bucket Versioning)

Ek S3 bucket hamesha in teen mein se kisi ek state mein hota hai:

- **Unversioned (Default)**: Jab aap bilkul naya bucket banate hain, toh usmein versioning OFF hoti hai. Isme file overwrite ya delete karne par purana data permanently loss ho jata hai. Matlab agar fresh bucket banayi hai to usme versioning off hogi, to jab aap us bucket mein file upload karoge to us file ka koi version nhi hoga, Lekin aapse thoda data add karke same file fir se upload karoge to us file ka purana data delete hoke naya data aa jayega.

- **Versioning-Enabled**: Jab aap manually ja kar versioning ko ON karte hain. Iske baad se upload hone wali har file ke multiple versions bante hain.

- **Versioning-Suspended**: Yaad rakhein, ek baar bucket par versioning ON karne ke baad aap use **kabhi bhi poori tarah se turned OFF (delete) nahi kar sakte**. Aap use sirf **Suspend (rok)** sakte hain. Suspend karne ke baad, nayi files ke naye versions banna band ho jate hain, lekin jo purane versions pehle se ban chuke the, woh bucket mein waise hi safe rehte hain.

<br>
<br>

### S3 Versioning Kaise kaam karti hai?

Jab aap ek aam computer par kisi file ko badalte (overwrite karte) hain, to Operating System (OS) hard drive ke us block par jaakar naya data likh deta hai, jisse purana data hamesha ke liye mit jata hai. Isko hum **In-place Mutation** kehte hain.

Lekin AWS S3 ek **"Immutable Object Store"** hai. Iska matlab hai ki S3 mein kisi bhi file ko upload karne ke baad use badla (modify) nahi ja sakta. To fir versioning kaise hoti hai?

S3 backend mein har ek file ko do hisson mein todkar dekhta hai:
- **Metadata**: File ka naam (Key), Size, Creation Date, Content-Type, aur sabse zaroori—Version ID.
- **Data Payload**: Asli file jo hard drive par store hoti hai.

Jab aap S3 bucket mein Versioning ko ON (Enable) kar dete hain, to AWS S3 aapke bucket ke behavior ko badal deta hai. Ab jab bhi aap koi naya action (Upload, Delete) karte hain, to S3 purane data payload aur metadata ko touch nahi karta. Wo har ek action ke liye ek naya metadata record aur naya data payload banata hai, aur unhe ek Chain (Linked List) ki tarah maintain karta hai.

<br>

Maan lijiye hamare paas ek bucket hai jisme humne Versioning ko ON (Enable) kar diya hai.

**Scenario 1: Pehli Baar File Upload Karna (Fresh Upload)**:

Jab aap bucket mein pehli baar koi file dalte hain, to S3 use ek unique ID deta hai jise **Version ID** kehte hain. Yeh file aapka **Current Version** ban jaati hai.

- Aapne kya kiya: Aapne ```invoice.txt``` naam ki file upload ki, Iska size 10 KB hai.
- S3 Backend kya karega: S3 is request ko receive karega. Wo dekhega ki is bucket ki versioning state ```"Enabled"``` hai. S3 ka internal system turant ek cryptographically secure, random string generate karega, jaise: ```#1234@qw```. Yeh iski **Version ID** ban gayi.

| Object Name (Key)      | Version ID | Status                       | Size |
| ---------------------- | ---------- | ---------------------------- | ---- |
| invoice.txt | #1234@qw   | Current Version (Latest)     | 10KB  |

Result: Agar koi developer ya application bina koi version ID bataye simple S3 URL (```s3://my-bucket/production_config.json```) par call karega, to S3 backend is table ko scan karega, dekhega ki Is Current Version = Latest kiske samne hai, aur use #1234@qw wala data (5 KB) deliver kar dega.

<br>

**Scenario 2: File Ko Dobara Upload Karna (Overwrite / Update)**:

Maan lijiye aapne us file mein kuch badlav kiye (jaise naya bill amount joda) aur use fir se same naam (```invoice.txt```) se upload kar diya.

- Aapne kya kiya: Nayi ```invoice.txt``` jiska size 15 KB hai usko fir se ko upload kiya.
- S3 Backend kya karega: S3 purani file jiska version id ```#1234@qw``` ko chuhega bhi nahi. Wo ek nayi unique Version ID generate karega, maano ```#5678@qw```. Aur file ko naye version id se saath store kar lega.

| Object Name (Key) | Version ID | Status                     | Size  |
| ----------------- | ---------- | -------------------------- | ----- |
| invoice.txt       | #5678@qw   |  Current Version (Latest)  | 15KB  |
| invoice.txt       | #1234@qw   | Noncurrent Version (Old)   | 10 KB |

Result:
- Jo sabse latest hai (ID: ```#5678@qw```), wo Current Version ban gaya.
- Purani file (ID: ```#1234@qw```) Noncurrent Version ban gayi.
- Agar aap normal download karenge, to aapko 15 KB wali nayi file milegi. Lekin purani file ab bhi bucket mein chhupi hui hai.

<br>

**Scenario 3: File Ko Delete Karna (Simple Delete)**:

Ab maan lijiye kisi employee se galti se S3 console par ```invoice.txt``` select karke "Delete" ka button dab gaya.

- Aapne kya kiya: ```invoice.txt``` ko bina koi version ID select kiye delete kar diya.
- S3 versioning ka sabsb bada rule hai: "Normal delete se kabhi data delete nahi hota." S3 ne dekha ki delete request mein koi specific Version ID nahi di gayi hai. To S3 ne kya kiya? S3 us file ke uper ek **Delete Marker** laga dega.Ab yeh Delete Marker hi aapka Current Version ban jata hai.

Yeh S3 versioning ka sabse zyada interview mein pucha jaane wala aur technical concept hai. Jab versioning ON ho aur aap kisi file ko normal tarike se delete karte hain, toh AWS data ko delete nahi karta. Woh bas us file ke upar ek choti si invisible chit chipka deta hai jise Delete Marker kehte hain.

**Delete Marker Kya hota hai?**: Yeh ek zero-byte (0 KB) ki khali metadata file hoti hai jisme koi asli data payload nahi hota, bas ek nayi Version ID hoti hai.

| Object Name (Key) | Version ID  | Status                          | Size  |
| ----------------- | ----------- | ------------------------------- | ----- |
| invoice.txt       | #9101112@qw | Current Version (Delete Marker) | 0 KB  |
| invoice.txt       | #5678@qw    |  Noncurrent Version (Old)       | 15KB  |
| invoice.txt       | #1234@qw    | Noncurrent Version (Old)        | 10 KB |

Jab koi user ab bucket dekhega, toh use ```invoice.txt``` file dikhayi hi nahi degi, user ko dikhega: "This bucket is empty" ya "No objects found". Kyunki us file ke upar Delete Marker laga hua hai. Agar koi application is file ko fetch karne ki koshish karegi, to S3 dekhega ki Current Version ek Delete Marker hai. S3 turant use 404 Not Found ya 405 Method Not Allowed ka error de dega.

<br>

**Scenario 4: Accidental Deletion Se Recover Kaise Karein? (File Wapas Lana)**:

Agar aapko galti se delete hui file wapas chahiye, to aapko S3 console par ek button dikhta hai: "Show Versions". Jaise hi aap use ON karenge, aapko upar di gayi poori table (teeno versions) dikhne lagegi.

- Aapko kya karna hai: Aapko us Delete Marker (ID: ```#9101112@qw```) ko select karke Delete karna hoga.
- S3 Backend mein kya hua: Jaise hi aapne Delete Marker ko hataya, uske theek neeche wali file (ID: ```#5678@qw```) apne aap dobara Current Version ban gayi!

| Object Name (Key) | Version ID  | Status                          | Size  |
| ----------------- | ----------- | ------------------------------- | ----- |
| invoice.txt       | #5678@qw    |  Current Version (Old)          | 15KB  |
| invoice.txt       | #1234@qw    | Noncurrent Version (Old)        | 10 KB |

Result: Aapki file poori tarah recover ho gayi! Kisi ko pata bhi nahi chalega ki file kabhi delete hui thi.

<br>

**Scenario 5: Hamesha Ke Liye Delete Karna (Permanent Delete)**:

Agar aapko pakka pata hai ki aapko data hamesha ke liye mitana hai (storage space khali karne ke liye) to aap kaise karenge?

- Aapko kya karna hai: Aap "Show Versions" on karenge aur jo specific version aapko nahi chahiye (Maan lijiye sabse purani file ID: ```#1234@qw```), aap sirf usko select karke delete karenge. S3 aap se confirmation ke liye capital letters mein "PERMANENTLY DELETE" likhne ko kahega.
- S3 Backend mein kya hua: Ab AWS us file ko hard drive se sach mein hatau (wipe) kar dega.

Result: Ab ID: ```#1234@qw``` ka data hamesha ke liye mit chuka hai, ise AWS bhi wapas nahi la sakta.

<br>

**Ek Zaroori Baat: Unversioned Files Ka Kya Hota Hai?**

Maan lijiye aapne ek naya bucket banaya aur usme versioning ON karne se pehle ek file daal di thi.
- Is case mein S3 us file ko koi random character wali ID nahi deta, balki uski Version ID ko ```null``` set kar deta hai.
- Baad mein jab aap versioning ON karte hain aur naye badlav upload karte hain, to naye waale data ko proper version IDs milti hain, lekin sabse purani file ki ID null hi rehti hai.

<br>

**S3 Versioning "Suspend" Kaise Karti Hai?**

Bohot se log sochte hain ki agar unhone galti se versioning ON kar di, to wo use wapas OFF ya Delete kar sakte hain. Nahi! Ek baar bucket mein versioning ON ho gayi, to aap use kabhi OFF (Delete) nahi kar sakte. Aap use sirf Suspend (rok) sakte hain.

Maan lijiye humne ```invoice.txt``` wale bucket ko Suspend kar diya. Iske baad backend kaise badalta hai, aaiye samajhte hain:
- Purane Versions Ka Kya Hoga? Jo versions versioning ON rehte waqt ban chuke the, wo waise ke waise hi bucket mein safe rahenge aur unka paisa lagta rahega.
- Naya Upload Karne Par Kya Hoga? Bucket suspend hone ke baad agar aap koi naya badlav upload karte hain, to S3 use koi random ID nahi deta. S3 uski Version ID ko hamesha ```null``` set karta hai.
- Overwrite in Suspended State: Agar bucket suspend hai, aur aapne null ID wali file ke upar dobara same naam se file upload ki, to ab S3 purani null ID wali file ko overwrite (permanently delete) kar dega aur nayi file ko null ID de dega.


<br>
<br>

### S3 Versioning Storage and Cost

S3 mein versioning on karne ke baad storage kaise calculate hoti hai aur aapka bill kaise banta hai.

**1. Core Rule: "Har Version Ek Alag Object Hai"**:

AWS S3 ka sabse bada niyam yeh hai ki backend mein har ek version ko ek independent (alag) file ki tarah treat kiya jata hai.
- No Delta Storage: Bohot se log sochte hain ki agar unhone 100 MB ki file mein sirf 1 MB ka badlav kiya, to S3 sirf us 1 MB ka extra charge lega. Yeh bilkul galat hai. S3 koi Git ya Database nahi hai jo sirf 'changes' (deltas) ko save kare. S3 poori ki poori file dobara save karta hai.
- Full Size Charge: Agar aapne 100 MB ki file ko 5 baar overwrite kiya, to bucket mein 5 alag-alag versions banenge. S3 aapse 100 MB × 5 = 500 MB ka poora storage charge lega. Matlab har version ka alag charge lagta hai.

<br>

**2. Live Calculation Example (Aapka Bill Kaise Banega?)**:

Aaiye ek real-world math example dekhte hain. Maan lijiye aap S3 Standard Storage Class use kar rahe hain, jahan average cost $0.023 per GB per month hai.

Aapke paas ek video file hai: ```intro.mp4```.

- Day 1 (Pehla Upload): Aapne 2 GB ki file upload ki.
  - Bucket Storage: 2 GB.

- Day 5 (Pehla Revision): Aapne video edit kiya aur same naam se dobara upload kiya. Nayi file ka size 2.5 GB hai.
  - Backend status: 2.5 GB (Current Version) + 2 GB (Noncurrent Version).
  - Bucket Storage: 4.5 GB.
 
- Day 10 (Doosra Revision): Aapne fir se edit kiya aur upload kiya. Naye video ka size 3 GB hai.
  - Backend status: 3 GB (Current Version) + 2.5 GB (Noncurrent) + 2 GB (Noncurrent).
  - Bucket Storage: 7.5 GB.
 
Mahine ke aakhir mein bill:

Aapko lag raha hoga ki aapke paas toh sirf ek hi video (```intro.mp4```) hai jiska size 3 GB hai. Lekin AWS aapko poore 7.5 GB ka bill bhejega:
```
Total Cost = 7.5 GB * $0.023 = $0.1725
```
Agar yahi file 200 GB ki hoti aur uske 10 versions hote, to bill bina vajah bohot bada ho jata.

<br>

**3. Delete Markers Ka Cost Aur Storage Impact**:

Maan lijiye aapne ```intro.mp4``` ko delete kar diya (bina version ID bataye, yaani normal delete).
- Storage Size: Jaise humne pehle baat ki thi, S3 uske upar ek Delete Marker bitha dega. Delete Marker ka khud ka size 0 bytes hota hai.
- Cost Impact: Delete Marker ka koi paisa nahi lagta. LEKIN, uske neeche jo teen purane versions (7.5 GB) chhupe baithe hain, wo delete nahi hue hain!
- The Trap: File delete karne ke baad bhi aapka 7.5 GB ka storage charge lagta rahega jab tak aap "Show Versions" mein jaakar un purane teeno versions ko Permanently Delete nahi karte. Yeh sabse bada reason hai kyun logo ka S3 bill unki umeed se zyada aata hai.

<br>

**4. Storage Classes Ka Paisa (Current vs Noncurrent)**:

S3 mein alag-alag (Storage Classes) hote hain—kuch mehnge aur fast, kuch saste aur slow. Versioning mein aap dono ke liye alag-alag classes chun sakte hain:
- Current Versions (Mehanga Storage Class): Kyunki latest file ko aapka application bar-bar read karta hai, isliye ise S3 Standard mein rakha jata hai, jahan storage cost thodi zyada hoti hai par access fees zero hoti hai.
- Noncurrent Versions (Sasta Storage Class): Jo purane versions hain, unhe koi roz-roz nahi dekhta. Wo sirf backup ke liye hain. Isliye unhe S3 Standard-IA (Infrequent Access) ya S3 Glacier mein bhej diya jata hai, jahan storage ki keemat 70% se 90% tak kam ho jaati hai.

<br>
<br>

### Ek Aur Chhupa Hua Charge: Multipart Upload Garbage

S3 mein jab aap koi badi file (jaise 5 GB se upar) upload karte hain, to S3 use chhote-chhote tukdon mein todkar upload karta hai, jise **Multipart Upload** kehte hain.
- Agar upload ke beech mein aapka internet toot gaya ya upload fail ho gaya, to wo aadhe-adhure tukde bucket mein hi fase reh jaate hain.
- Agar versioning ON hai, to wo fase hue tukde bhi storage gherte hain aur unka paisa lagta rehta hai, jabki wo file aapko console par dikhayi bhi nahi deti.

**Solution**: Lifecycle rule mein hamesha ek option select karna chahiye: "Delete expired object delete markers or incomplete multipart uploads".

