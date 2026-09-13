# S3 Lifecycle rules

S3 lifecycle rule ek automated mechanism hai jo S3 bucket mein stored object ko autmatically different storage class mein move karti hai ya fir permanently delete karti hai.

Lifecycle rule ka primary purpose hota hai storage cost ko optimize karna.

S3 Lifecycle Rule ek automated rule hai jiske through hum S3 objects ke lifecycle ko automatically manage kar sakte hain.

Matlab aap S3 ko ye policy de sakte ho:
- “Agar object 30 days purana ho jaye, to usko S3 Standard se Standard-IA mein move kar do.”

Ya:
- “Agar object 90 days purana ho jaye, to usko Glacier mein transition kar do.”

Ya:
- “Agar object 365 days purana ho jaye, to usko permanently delete kar do.”

Iska main purpose hai ki aapko manually har object ko manage na karna pade.

<br>

Simple words mein, jab hum S3 mein koi object upload karte hain, to zaroori nahi hai ki woh object hamesha same Storage Class mein rahe ya hamesha S3 mein indefinitely stored rahe.

Example ke liye, maan lo application S3 mein daily logs upload karti hai:
```
logs/
├── 2026-01-01/
├── 2026-01-02/
├── 2026-01-03/
├── ...
```

Ho sakta hai:
- first 30 days logs frequently access hon,
- 30 days ke baad rarely access hon,
- 90 days ke baad almost kabhi access na hon,
- aur 1 year ke baad business ko unki zarurat hi na ho.

Aise case mein manually har object ki storage class change karna ya delete karna practical nahi hai. Yahin par **S3 Lifecycle Rules** ka use hota hai.

Lifecycle Rule S3 ko automatically bata sakti hai:
- "Object ke creation ke itne days baad usse kisi cheaper storage class mein move kar do, aur itne days ke baad delete kar do."

<br>
<br>

### Lifecycle ka main purpose kya hai?

S3 mein generally data ka access pattern time ke saath change ho sakta hai.

For example, maan lo aapki application daily logs S3 mein store karti hai.

Aaj ka log frequently access ho sakta hai.

Lekin 30 din purana log shayad rarely access ho.

90 din purana log almost kabhi access nahi hota.

1 saal purana log shayad sirf compliance ya investigation ke time chahiye.

Aise case mein agar aap sabhi objects ko permanently S3 Standard mein rakhoge, to unnecessary storage cost aa sakti hai.

S3 Lifecycle rule ke through aap data ko automatically cheaper storage classes mein move kara sakte ho.

Example:
```
Day 0
  ↓
S3 Standard
  ↓
30 days
  ↓
S3 Standard-IA
  ↓
90 days
  ↓
S3 Glacier Flexible Retrieval
  ↓
365 days
  ↓
Delete
```
Is tarah Lifecycle automatically data ko uske age aur access pattern ke according manage karta hai.

<br>
<br>

### S3 Lifecycle ka basic concept

Jab tum S3 mein ek lifecycle rule banate ho to usme 2 tarah ke actions select kar sakte ho.
- Transition.
- Expiration.

**1 - Transition (Storage Class Badalna)**:

Transition ka matlab hai file ko ek storage class se doosri sasti storage class mein transfer karna. Yeh action data ko ek storage class se doosri sasti storage class mein transfer karta hai.

Is action ka use tab kiya jata hai jab aap chahte hain ki aapka data delete na ho, balki ek saste storage class mein shift ho jaye. Jaise-jaise data purana hota hai, log use kam access karte hain. S3 mein alag-alag storage classes hoti hain jinme data rakhne ka kharcha alag hota hai.

Example: Jab aap koi file upload karte hain, toh woh S3 Standard (mehangi aur fast) mein hoti hai. Aap rule laga sakte hain ki: "Agar file 30 din purani ho jaye, toh use S3 Standard-IA (Infrequent Access) mein bhej do, aur agar 90 din purani ho jaye toh S3 Glacier Flexible Retrieval (archival storage) mein shift kar do."

<br>

**2 - Expiration (Data Delete Karna)**:

Yeh action data ki validity khatam hone par use automatically permanently delete kar deta hai.

Is action ka use tab kiya jata hai jab ek fixed time ke baad aapko us data ki bilkul jarurat nahi hoti aur aap use permanently delete karna chahte hain. Isse automatic deletion ho jati hai aur storage free ho jati hai.

Example: Aap rule laga sakte hain ki "Agar logs file 180 din se zyada purani ho jaye, toh use bucket se Permanently Delete kar do." Isse aapko manually files dhoodh kar delete karne ki zaroorat nahi padti.

<br>
<br>

### Lifecycle Rule ke major components

Ek Lifecycle Rule ko samajhne ke liye kuch important concepts hain:
- Rule
- Scope
- Filter

**Lifecycle Rule**:

Sabse pehle aap ek rule create karte ho.

For example:
```
Rule Name:
Move logs to Glacier
```
Aur rule ke andar define karte ho ki kis object par apply karna hai aur kya action perform karna hai.

Example:
```
If object is 90 days old
        ↓
Move object to Glacier Flexible Retrieval
```

Yahan:
- Condition = Object 90 days old.
- Action = Transition to Glacier Flexible Retrieval.

<br>

**Lifecycle Rule ka Scope**:

Lifecycle rule ko aap poore bucket par apply kar sakte ho ya specific objects par.

For example, bucket:
```
my-company-data
```
Is bucket mein:
```
logs/
backup/
images/
documents/
```
Suppose aap sirf ```logs/``` ke objects par lifecycle apply karna chahte ho.

To aap filter use kar sakte ho.

Example:
```
Prefix = logs/
```
Ab rule sirf:
```
logs/application.log
logs/server.log
logs/2026/01/app.log
```
jaise objects par apply hoga.

<br>

**Prefix ke through Lifecycle Rule**:

S3 mein traditional/general-purpose bucket ke andar folders actually filesystem directories nahi hote.

For example:
```
logs/2026/server.log
```
mein:
```
logs/2026/
```
object key ka prefix hai.

Lifecycle rule mein aap prefix ke basis par objects select kar sakte ho.

Example:
```
Prefix:
logs/
```
Then:
```
logs/app1.log
logs/app2.log
logs/server.log
```
match karenge.

Lekin:
```
images/photo.jpg
backup/db.sql
```
match nahi karenge.

<br>

**Tags ke through Lifecycle Rule**:

Aap object tags ke basis par bhi Lifecycle Rule apply kar sakte ho.

For example, objects ko tag kar diya:
```
Environment = production
```
Ya:
```
DataType = logs
```

Then Lifecycle Rule keh sakta hai:
```
DataType = logs
        ↓
Apply lifecycle
```
Example:
```
Object:
server.log

Tags:
DataType = logs
Environment = production
```

Lifecycle Rule:
```
If DataType = logs
        ↓
After 30 days → Standard-IA
After 90 days → Glacier
After 365 days → Delete
```
Is approach ka benefit ye hai ki aap prefix structure ke bajay business classification ke according lifecycle manage kar sakte ho.

<br>
<br>

### Minimum Storage Duration Rules

Lifecycle rules lagate waqt AWS ke minimum days ke limits ko dhyan mein rakhna zaroori hai, nahi toh transition fail ho jayega ya extra charge lagega.

AWS S3 mein Minimum Storage Duration Rules ka matlab hai ek tarah ka "Lock-in Period" ya "Minimum Recharge Validity".

AWS jab aapko sasti storage classes (jaise Infrequent Access ya Glacier) deta hai, toh woh ek shart rakhta hai: "Hum aapse per-GB ka kiraya toh bohot kam lenge, lekin aapko apni file ko ek nirdharit samay (minimum days) tak hamare paas rakhna hi padega।".

Agar aap us time limit se pehle file ko delete kar dete hain ya kisi doosri storage class mein shift kar dete hain, toh AWS aapse bachaye hue dinon ka Penalty (Early Deletion Charge) vasool karta hai.

<br>

**S3 Storage Classes Ki Minimum Duration Limits**:

S3 ki har storage class ki apni ek minimum duration limit hoti hai:

|            Storage Class           |  Minimum Duration Limit |                                                     Simple Explanation                                                     |
|:----------------------------------:|:-----------------------:|:--------------------------------------------------------------------------------------------------------------------------:|
|             S3 Standard            | 0 Days (Koi limit nahi) | Yeh sabse mehangi hai, isme jab chahein file dalein aur agle hi second delete kar dein, sirf unhi seconds ka paisa lagega। |
| S3 Standard-IA (Infrequent Access) |         30 Days         |                                      File ko isme kam se kam 30 din tak rehna padega।                                      |
|           S3 One Zone-IA           |         30 Days         |                                Same 30 days ka rule hai, bas data ek hi zone mein hota hai।                                |
|    S3 Glacier Flexible Retrieval   |         90 Days         |                         Archive storage hai, isme data kam se kam 3 months (90 days) rehna chahiye।                        |
|       S3 Glacier Deep Archive      |        180 Days         |               Sabse sasti storage (backups ke liye), isme data kam se kam 6 months (180 days) rehna chahiye।               |


<br>

**Penalty (Early Deletion Charge) Kaise Calculate Hoti Hai?**

Scenario:

Aapke paas ek 100 GB ki file hai। Aapne ek Lifecycle Rule lagaya aur us file ko S3 Glacier Flexible Retrieval (jiska minimum duration 90 days hai) mein bhej diya।
- Galti: Upload karne ke theek 10 din baad aapko yaad aaya ki yeh file bekaar hai, aur aapne use Delete kar diya.
- AWS Ka Hisab (The Penalty): AWS kahega, "Aapne file sirf 10 din rakhi, lekin hamara rule 90 days ka tha। Isliye hum aapse 10 din ka normal kiraya toh lenge hi, sath mein bache hue 80 din (90 - 10) ka Early Deletion Charge bhi vasoolenge।"
- Result: Aapko lag raha hoga ki file delete karke aapne paisa bacha liya, lekin AWS aapse poore 90 din ka paisa le chuka hoga।

<br>
<br>

### Lifecycle Rules Banate Waqt Kyun Dhyan Rakhna Zaroori Hai?

Jab aap cascading lifecycle rules banate hain (yaani ek class se doosri class mein data transfer karna), toh aapko in days ko plus (+) karna padta hai, nahi toh aapko double penalty lag sakti hai.

**Galat Lifecycle Setting (High Penalty Risk)**:
- Day 0: File uploaded to S3 Standard.
- Day 10: Transition to S3 Standard-IA (Minimum duration limit is 30 days).
- Day 20: Transition to S3 Glacier.

Kya nuksan hua? Kyunki file Standard-IA mein sirf 10 din rahi (jabki limit 30 din ki thi), toh jaise hi woh Day 20 par Glacier mein jayegi, AWS aapse Standard-IA ka 20 din ka penalty charge le lega.

**Sahi Lifecycle Setting (No Penalty)**:
- Day 0: File uploaded to S3 Standard.
- Day 30: Transition to S3 Standard-IA (File poor 30 din Standard mein rahi, koi dikkat nahi).
- Day 60: Transition to S3 Glacier (File poor 30 din Standard-IA mein reh chuki hai, isliye ab move karne par koi penalty nahi lagegi).

<br>
<br>

### S3 Lifecycle Rule kya bucket level par lagta hai ya object level par?

S3 Lifecycle rules humesha Bucket Level par lagaye (configure kiye) jaate hain, lekin unka action Object Level par apply hota hai.

Aap kisi ek single object ke andar jaakar lifecycle rule nahi likhte. Aap poore bucket ke liye ek rule banate hain, aur us rule ke andar yeh decide karte hain ki yeh bucket ke saare objects par lagega ya fir kuch specific objects par.

<br>
<br>

### Kya S3 Lifecycle ke liye Versioning ON hona zaroori hai? Matlab kya ye versioning on aur off dono par kaam karta hai?

**Nahi**, S3 Lifecycle rules bina versioning ke bhi perfectly kaam karti hain. Matlab versioning on aur off dono state mein bhi kaam karta hai.

Lekin agar Versioning ON hai, to Lifecycle rules kaam karne ka tareeka thoda badal jata hai aur aapko extra controls milte hain. S3 dono scenarios ko alag tarike se handle karta hai:

**Scenario A: Agar Versioning OFF hai (Bina Versioning Ke)**:

Jab aapke bucket mein versioning off hoti hai, to har object ka sirf ek hi version hota hai (Current Version).
- Transition: Rule set karne par, object direct kisi saste tier (jaise Glacier) mein move ho jayega.
- Expiration: Rule ke mutabik jab expiry date aayegi, to object permanently delete ho jayega. Aap use wapas nahi la sakte.

**Scenario B: Agar Versioning ON hai**:

Jab versioning on hoti hai, to ek hi file ke multiple versions ho sakte hain (Current Version aur Noncurrent/Past Versions). S3 Lifecycle aapko dono ke liye alag rules banane ki flexibility deta hai:
- Current Version Actions: Jo file abhi active hai, uspar kya rule lagana hai.
- Noncurrent Version Actions: Jo file purani ho chuki hai (overwritten ya deleted), uske purane versions ko kab transition karna hai ya kab permanently delete karna hai.

<br>

**Real-World Examples**:

**Example 1: E-commerce Website ke Log Files (Bina Versioning Ke)**:

Maan lijiye aapki ek shopping website hai. Us website ke server logs har din ek S3 bucket mein save hote hain (Versioning OFF hai).

Requirement: Aapko pehle 30 din ke logs turant dekhne pad sakte hain troubleshooting ke liye. 30 se 90 din ke logs kabhi-kabhi audits ke liye chahiye hote hain. 90 din baad logs ki koi jarurat nahi hoti.

Lifecycle Rule:
- Day 0: Log file S3 Standard mein upload hui.
- Day 30: Transition action trigger hua -> File automatically S3 Standard-IA mein chali gayi (cost kam ho gayi).
- Day 90: Expiration action trigger hua -> File permanently delete ho gayi.

**Example 2: Financial Documents & Invoices (Versioning ON ke saath)**:

Aapki company ke financial invoices S3 bucket mein hain aur unme aksar updates hote rehte hain, isliye Versioning ON hai. Government rule ke mutabik aapko purana data 7 saal tak rakhna zaroori hai, par use roz dekhne ki jarurat nahi hai.

Lifecycle Rule for Current Version:
- Active invoice hamesha S3 Standard mein rahegi taaki employees use access kar sakein.

Lifecycle Rule for Noncurrent Versions (Purane Versions):
- Jaise hi koi employee invoice update karega, purana version Noncurrent ban jayega.
- Rule: Noncurrent banne ke 1 din baad, use S3 Glacier Deep Archive mein bhej diya jaye (kyunki yeh sabse sasta hai aur bas backup ke liye chahiye).
- Rule: Noncurrent banne ke 7 saal (2555 din) baad, wo version permanently delete ho jaye.

<br>
<br>

### S3 Lifecycle Rules Ke Fayde

**Automatic Cost Optimization**: Aapko manually files check karke delete ya move nahi karni padti. AWS background mein khud sab manage karta hai, jisse bill bohot kam ho jata hai.

**Compliance aur Data Retention**: Agar aapki industry mein rule hai ki data ko 5 saal tak rakhna hi hai, to aap rule set karke bhool sakte hain. Data safe rahega aur time aane par hi delete hoga.

**Clean-up of Incomplete Multipart Uploads**: Jab bad files upload ho rahi hoti hain aur beech mein fail ho jati hain, to wo bucket mein space ghere rakhti hain. Lifecycle rule se aap in adhure uploads ko 7 din mein auto-delete kar sakte hain.

<br>
<br>

### Do Chhupe Hue Rules (Billing Traps Se Bachne Ke Liye)

Jab aap console par rule banate hain, to niche do checkbox hote hain jise hamesha select karna chahiye:

**Delete expired object delete markers**: Jab aap versioning bucket mein kisi file ko delete karte hain, to ek Delete Marker ban jata hai. Agar aapne asli versions delete kar diye hain, to wo 0 KB ka khali Delete Marker wahan fuzool pada rehta hai. Yeh rule use apne aap saaf kar deta hai.

**Delete incomplete multipart uploads**: Agar aap koi badi file upload kar rahe the aur internet tootne ki wajah se upload aadhe mein ruk gaya, to wo aadhe-adhure tukde (fragments) bucket mein fase reh jaate hain aur unka paisa lagta rehta hai. Yeh rule unhe 7 din mein apne aap delete kar deta hai.


<br>
<br>

### Lifecycle aur S3 Intelligent-Tiering

S3 Lifecycle Rules aur S3 Intelligent-Tiering dono hi AWS mein storage cost kam karne ke tareeqe hain, lekin dono ke kaam karne ka concept bilkul alag hai.

Lifecycle Rules aapke predefined conditions ke according action leti hain.

Example:
```
30 days → Standard-IA
90 days → Glacier
```

Whereas S3 Intelligent-Tiering access patterns ko monitor karke objects ko appropriate access tiers mein automatically move karta hai.

Inka sabse bada difference yeh hai ki Lifecycle Rules "Time (Waqt)" ke hisab se kaam karti hain, jabki Intelligent-Tiering "Data Access Pattern (User Activity)" ke hisab se kaam karti hai.

**Lifecycle vs Intelligent-Tiering**:

|                    Feature                    |                                    S3 Lifecycle Rules                                   |                                           S3 Intelligent-Tiering                                          |
|:---------------------------------------------:|:---------------------------------------------------------------------------------------:|:---------------------------------------------------------------------------------------------------------:|
|                  Yeh kya hai?                 | Ek Policy/Rule hai jo data ko ek class se doosri class mein bhejti ya delete karti hai. |                  Ek Storage Class hai jismein data ko upload ya transition kiya jata hai.                 |
|        Kiske basis par kaam karta hai?        |      Age (Dinon ke hisab se): Jaise 30 din baad move karo, 90 din baad delete karo.     | Access Pattern (Activity): Agar file read nahi ho rahi to saste tier mein dalo, read hui to wapas le aao. |
|                Kab use karein?                |   Jab aapko pata ho ki data kab purana aur bekar hone wala hai (Predictable patterns).  |              Jab aapko na pata ho ki kaun si file kab kaam aa jaye (Unpredictable patterns).              |
| Retrieval Fee (Data wapas nikalne ka kharcha) |      Standard-IA ya Glacier se data baar-baar nikalne par Retrieval Fee lagti hai.      |                    Isme data jitni baar marzi read karo, koi Retrieval Fee nahi lagti.                    |
| Extra Cost                                    | Yeh feature bilkul free hai, iska koi alag se charge nahi hota.                         | Isme ek choti si Monitoring & Automation Fee lagti hai ($0.0025 per 1,000 objects monthly).               |
| Object Deletion                               | Yeh data ko automatically delete (expire) kar sakta hai.                                | Yeh data ko kabhi delete nahi karta, bas saste tiers mein shift karta hai.                                |

<br>

**Kya Dono Ko Ek Saath Use Kiya Ja Sakta Hai?**

Haan, aur yahi sabse best practice hai!

Aap ek aisa master plan bana sakte hain jismein dono ka fayda mile:
- Lifecycle Rule lagayein: Jo naye objects bucket mein aayein, unhe turant S3 Intelligent-Tiering class mein bhej de.
- Intelligent-Tiering apna kaam karegi: Wo un objects ko unke use ke hisab se saste tiers mein manage karegi taaki unpredictable use par bhi paisa bache.
- Lifecycle ka doosra rule lagayein: Jab objects 365 din purane ho jayein, to unhe permanently Delete (Expire) kar de, kyunki Intelligent-Tiering khud se data delete nahi kar sakti.


