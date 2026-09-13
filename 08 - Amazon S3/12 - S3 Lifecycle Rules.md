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

