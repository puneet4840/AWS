# S3 Event Notification

S3 Event Notifications ek mechanism hai jiske through Amazon S3 mein kisi particular event ke occur hone par S3 kisi doosri AWS service ya supported destination ko notification bhej sakta hai.

Amazon S3 mein Event Notification ek mechanism hai jiske through S3 bucket mein kisi particular event ke hone par automatically kisi doosri AWS service ko notification bheji ja sakti hai.

Dusri service jaise ki: SNS, SQS, Lambda.

Simple language mein agar hum kahen, to iska meaning hai:
- "Jab S3 bucket mein koi important event ho, to S3 automatically kisi doosri service ko bata de ki ye event hua hai."

Jaise S3 bucket mein koi bhi operation hota hai, to S3 us operation ka ek event generate karti hai aur destination AWS service ko bhejti hai.

Sabse pehle ye samjho ki S3 mein bahut saare operations hote hain. For example, application kisi bucket mein object upload kar sakti hai, existing object delete kar sakti hai, object ko overwrite kar sakti hai, multipart upload complete kar sakti hai, ya object restore kar sakti hai.

Agar application ko ye pata hona chahiye ki "S3 bucket mein abhi kuch hua hai", to application ko continuously S3 bucket ko poll karne ki zarurat nahi honi chahiye.

Instead, S3 event-driven architecture use kar sakta hai.

Basic flow:
```
Application
     |
     | Upload Object
     ↓
   S3 Bucket
     |
     | Event occurs
     ↓
S3 Event Notification
     |
     +------------+-------------+
     |            |             |
     ↓            ↓             ↓
   SQS          SNS          Lambda
```

Yaani S3 mein event hota hai aur S3 us event ke basis par configured destination ko notification bhejta hai.

**Example**:

Maan lo application ne S3 bucket mein ek image upload ki:
```
Application
     |
     | Upload image
     ▼
S3 Bucket
     |
     | ObjectCreated event
     ▼
Notification
     |
     ▼
Lambda
```

Jaise hi S3 mein image successfully upload hui, S3 ek event generate karta hai.

Ab Lambda ko manually check karne ki zarurat nahi hai ki:
- "S3 bucket mein koi nayi image aayi hai ya nahi?"

Is event ko Lambda tak bheja ja sakta hai. Lambda phir image ko resize kar sakta hai, thumbnail generate kar sakta hai, metadata process kar sakta hai, database update kar sakta hai, ya koi aur business logic execute kar sakta hai.

Isliye S3 Event Notification ka fundamental idea hai:
- “S3 mein kuch important event hua hai, ab kisi doosri service ko automatically inform/action trigger karna hai.”

<br>
<br>

### Event ka matlab kya hai?

Event ka simple meaning hai: S3 bucket mein koi activity hui jo S3 observe karta hai aur jiske liye notification configured ho sakti hai.

For example, kisi object ka upload hona ek event hai.
```
image.jpg upload
       ↓
ObjectCreated
```
Similarly, object delete hona bhi ek event ho sakta hai.
```
image.jpg deleted
       ↓
ObjectRemoved
```

Example:

Application ne:
```
image.jpg
```
S3 bucket mein upload ki.

S3 ke perspective se ek event hua:
```
ObjectCreated
```
Ab S3 configured hai ki jab bhi ObjectCreated event aaye, notification:
```
SQS Queue
```
mein bhejni hai.

Flow:
```
Client
   |
   | PUT image.jpg
   ↓
S3 Bucket
   |
   | ObjectCreated
   ↓
S3 Event Notification
   |
   ↓
SQS Queue
```
Queue mein notification aayegi, aur koi consumer us notification ko process kar sakta hai.

<br>
<br>

### S3 Event Notification ki zarurat kyun padti hai?

Without Event Notification, application ko repeatedly S3 ko check karna pad sakta hai ki:
- “Kya koi new object upload hua?”

Traditional architecture mein application baar-baar S3 se pooch sakti thi:
- "Kya koi naya object upload hua?"

Agar application har 10 seconds mein S3 ko query kare:
```
Application
    ↓
S3: Any new file?
    ↓
No

10 seconds later
    ↓
S3: Any new file?
    ↓
No

10 seconds later
    ↓
S3: Any new file?
    ↓
Yes
```
Is approach ko **Polling-Based approach** kehte hain. lekin isme unnecessary API calls aur operational complexity create kar sakti hai.

S3 Event Notifications mein application ko repeatedly S3 se poochne ki zarurat nahi hoti. S3 khud event hone ke baad configured destination ko notification bhej deta hai.

Isko **event-driven processing** kehte hain.

Event-driven approach mein S3 khud event generate karta hai:
```
Object Uploaded
      ↓
S3 Event
      ↓
Target Service
```
Isliye application ko continuously S3 se poochne ki zarurat nahi padti.

<br>
<br>

### S3 mein kaunse events ho sakte hain?
