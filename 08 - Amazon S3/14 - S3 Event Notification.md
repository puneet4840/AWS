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

### S3 mein Kaun-Kaun Se Events Par Notification Trigger Hoti Hai?

S3 mein aap lagbhag har ek event par notification laga sakte hain:
- ```s3:ObjectCreated:*```: Jab bucket mein koi nayi file aati hai (jaise PUT, POST, COPY, ya Multipart Upload).
- ```s3:ObjectRemoved:*```: Jab koi file delete hoti hai ya jab versioning bucket mein kisi file par Delete Marker lagta hai.
- ```s3:ObjectRestore:*```: Agar aapne Glacier se koi purani file wapas nikaali hai, to restore shuru hone aur restore complete hone par alert milta hai.
- ```s3:Replication:*```: Jab cross-region replication fail ya complete hota hai.

<br>
<br>

### S3 Notification Ke 3 Main Destinations (Kahan Jata Hai Alert?)

S3 direct aapke phone par SMS nahi bhej sakta, woh niche di gayi teen AWS services ko hi alert bhej sakta hai:
- AWS SNS (Simple Notification Service).
- AWS SQS (Simple Queue Service).
- AWS Lambda.

S3 bass inhi AWS service ki event ki notification bhej sakta hai.

**AWS SNS (Simple Notification Service - For Broadcasting)**:

AWS SNS ka use ek message ko multiple destination tak broadcast karne ke liye use hota hai.

Amazon SNS ek managed pub/sub messaging service hai. Jab aap S3 ka destination SNS set karte hain, to S3 event ka payload ek SNS Topic ko bhej deta hai. Uske baad SNS ki zimmedari hoti hai ki wo us message ko aage distribute kare.

AWS SNS ka use ek message ko multiple users tak ya destination tak broadcase karne ke liye hota hai. Agar ek S3 event ko multiple subscribers tak broadcast karna hai, to SNS useful ho sakta hai.

Yeh Kaise Kaam Karta Hai?
- Aap ek SNS Topic banate hain aur S3 bucket ko permission dete hain ki wo us topic par message publish kar sake. Us SNS Topic ke andar aap multiple "Subscribers" (recipients) add kar sakte hain.

Aage Data Kahan Ja Sakta Hai?: Ek baar event SNS ke paas chala gaya, to wo use ek saath kayi jagah bhej sakta hai:
- Email / SMS: Agar aapko security ya admin team ko immediate alert bhejna hai.
- HTTP/HTTPS Endpoints: Aapke kisi on-premise server ya web-hook (jaise Slack ya Microsoft Teams) par.
- AWS Lambda / Amazon SQS: Kisi doosre processing workflows ke liye.

<br>

**Amazon SQS (Simple Queue Service)**:

Amazon SQS ek fully managed message queuing service hai. Jab aap nahi chahte ki S3 event aate hi aapka system turant load se dab jaye, tab aap beech mein SQS Queue ko lagate hain. Agar bohot saari files ek sath aa rahi hain, toh SQS unhe ek line (queue) mein laga deta hai taaki aapka backend system load se crash na ho.

Aap chahte ho ki events queue mein store ho jaayen to S3 ke destination mein SQS use karo.

Yeh Kaise Kaam Karta Hai?
- Jaise hi S3 mein koi object upload ya delete hota hai, S3 us event ka ek message banakar SQS Queue mein daal deta hai. Wo message queue mein tab tak surakshit (store) rehta hai jab tak aapka backend application use khud aakar read aur delete nahi karta (isey polling kehte hain).

Kab Use Karein? (Heavy Load & Rate Limiting):
- Maan lijiye aapki website par achanak se ek hi second mein 10,000 users ne invoices upload kar diye. Agar aap direct kisi server ya database par event bhejenge, to aapka server crash ho jayega. SQS yahan ek buffer/shock absorber ka kaam karta hai. Wo saare 10,000 messages ko line (queue) mein khada kar dega. Aapka backend application apni speed ke hisab se (jaise 50 messages per second) unhe aaram se process karega.

<br>

**AWS Lambda**:

AWS Lambda ek serverless service hai jahan aap apna custom code (Python, Node.js, Java, etc.) likhte hain. Yeh S3 Event Notifications ka sabse jyada use hone wala destination hai.

Yeh Kaise Kaam Karta Hai?
- Jaise hi event trigger hota hai, S3 directly AWS Lambda function ko invoke (start) kar deta hai. S3 Lambda ko ek JSON payload bhejta hai, jismein bucket ka naam, file ka naam (key), size, aur upload ka exact timestamp hota hai. Lambda ka code chalte hi wo us file par processing shuru kar deta hai.

Real-World Processing Examples:
- Data Transformation: Kisi ne ```.csv``` file upload ki, Lambda ne turant chalu hokar use read kiya aur data ko Amazon DynamoDB database mein save kar diya.
- Security Check/Antivirus: Nayi file aate hi Lambda chalega aur check karega ki file mein koi virus ya malicious script to nahi hai.
- Media Transcoding: Kisi ne .mov ya .mp4 video upload kiya, Lambda chal kar use compress karega ya uske alag-alag resolutions (720p, 1080p) create karega.

<br>
<br>

### Amazon EventBridge (Pehle CloudWatch Events) — The Modern Enterprise Event Bus

Yeh S3 ka sabse modern aur advanced destination feature hai. Standard notifications ke alawa, aap S3 bucket par Amazon EventBridge ko ON kar sakte hain.

Standard S3 notifications mein aap sirf upar di gayi teen services (SNS, SQS, Lambda) ko hi direct event bhej sakte hain. Lekin agar aap Amazon EventBridge integration on karte hain, to aap S3 events ko AWS ki 20 se zyada services aur yahan tak ki third-party SaaS platforms par bhi bhej sakte hain.

```
                     S3
                      |
                Object Event
                      |
                      ▼
                 EventBridge
                      |
                 Event Rule
                      |
          ┌───────────┼───────────┐
          ▼           ▼           ▼
       Lambda        SQS       Step Functions
```

**EventBridge Se Jin Extra Destinations Par Event Ja Sakta Hai**:
- Amazon Kinesis Data Streams / Firehose: Agar aapko S3 events ka data live streaming analytics ke liye chahiye.
- AWS Step Functions: Agar aapko ek poora step-by-step workflow state machine chalana hai.
- Amazon ECS / AWS Batch: Agar file upload hone par aapko ek poora Docker container ya heavy batch job run karni hai.
- Third-Party Integrations: Aap direct S3 event ko EventBridge ke raste PagerDuty, Datadog, ya Zendesk jaise tools par alert banane ke liye bhej sakte hain.

**Advanced Filtering**: EventBridge ke andar aap bohot hi complex rules likh sakte hain. Jaise: "Sirf tabhi alert bhejo jab upload hone wali file ka size 5 GB se bada ho aur wo subah 9 baje se pehle upload hui ho." Aise advanced rules standard S3 notification mein nahi banaye ja sakte.

<br>
<br>

### S3 event mein actual information kya hoti hai?

Jab Amazon S3 koi event trigger karta hai, to wo destination (Lambda, SQS, ya SNS) ko ek JSON format mein data bhejta hai. Is JSON data ko **"Event Payload"** ya **"S3 Event Message"** kehte hain.

Lekin S3 event mein kabhi bhi payload nahi jata hai. Matlab kabhi actual data s3 event mein nahi jata hai.

**S3 Event Payload Ki 3 Main Structure Categories**:
- **System & Event Metadata (Event Ki Details)**:
  - Yeh batata hai ki event kab hua, kis wajah se hua, aur iska unique id kya hai.
- **Bucket Information (Kahan Hua?)**:
  - S3 bucket ka naam aur uski unique AWS identity (ARN).
- **Object Information (Kis File Par Hua?)**:
  - File ka exact naam (Key), uska size, uska version ID, aur uski security details (ETag).
 
**An Example S3 Event JSON (Kaisa Dikhta Hai?)**:

Jab aapka Lambda function trigger hoga, to use kuch aisa JSON dekhne ko milega. Notice kijiye ki main fields kahan hain:
```
{
  "Records": [
    {
      "eventVersion": "2.1",
      "eventSource": "aws:s3",
      "awsRegion": "ap-south-1",
      "eventTime": "2026-09-15T14:30:00.000Z",
      "eventName": "ObjectCreated:Put",
      "userIdentity": {
        "principalId": "AWS:AIDAIN0BFUEJNNEXAMPLE"
      },
      "requestParameters": {
        "sourceIPAddress": "192.0.2.1"
      },
      "s3": {
        "s3SchemaVersion": "1.0",
        "configurationId": "ImageUploadNotification",
        "bucket": {
          "name": "my-company-data-bucket",
          "arn": "arn:aws:s3:::my-company-data-bucket"
        },
        "object": {
          "key": "documents/invoices/invoice_101.pdf",
          "size": 134522,
          "eTag": "7b2b85a3c8f1a1d6a8b79213ef2b8b93",
          "versionId": "null"
        }
      }
    }
  ]
}
```

**Ek Bohot Zaroori Point: The "Records" Array ⚠️**: Aapne dhyan diya hoga ki upar ka poora JSON ek Records naam ki list (Array) ke andar hai [ ]. Iska matlab yeh hai ki S3 ek sath multiple events ko ek hi payload mein bhej sakta hai (halanki aksar standard notifications mein ek record hi hota hai, par SQS ya heavy flow mein ek sath 2-3 records bhi aa sakte hain).

Isliye jab bhi aap Lambda mein code likhte hain, to aapko hamesha loop chalana padta hai.

**Actual Fields Jo JSON Mein Hote Hain**:

Chaliye dekhte hain ki JSON ke andar kaun-kaun se exact keys hote hain aur unme kya details hoti hain:
- ```eventVersion``` aur ```eventSource```: Yeh batata hai ki payload ka version kya hai aur yeh message kahan se aaya hai (hamesha aws:s3 hoga).
- ```eventTime```: File kis exact second par upload ya delete hui (ISO timestamp format mein, jaise: ```2026-09-15T14:30:00.000Z```).
- ```eventName```: Sabse important field! Yeh batata hai ki bucket mein hua kya hai (e.g., ```ObjectCreated:Put```, ```ObjectRemoved:Delete```).
- ```userIdentity```: Jis IAM user ya AWS service ne file ko upload ya delete kiya, uski Principal ID.
- ```requestParameters```: Jis IP address (sourceIPAddress) se request aayi thi.
- ```s3.bucket.name```: Bucket ka actual naam jahan file padi hai.
- ```s3.bucket.arn```: Bucket ka Amazon Resource Name (e.g., ```arn:aws:s3:::my-awesome-bucket```).
- ```s3.object.key```: File ka poora naam aur path (Folder route ke sath). Agar file ```uploads/images/photo.jpg``` mein hai, to key yahi hogi.
- ```s3.object.size```: File ka exact size bytes mein. Iska use karke Lambda faisla le sakta hai ki file badi hai ya choti.
- ```s3.object.eTag```: File ka MD5 hash check. Yeh ensure karne ke liye hota hai ki file sahi se upload hui hai aur corrupt nahi hui.
- ```s3.object.versionId```: Agar bucket par Versioning ON hai, to us specific file ka unique version string.

**S3 Event Mein Kya Nahi Hota?**

Log aksar sochte hain ki event notification ke andar **file ka actual data (content)** bhi hota hai. **Nahi!**

Event notification mein file ke andar ka text, photo ya video nahi hota. Isme sirf file ki information (metadata) hoti hai. Agar aapke Lambda code ko file ke andar ka data read karna hai, to wo is JSON se bucket.name aur object.key uthayega, aur phir s3.get_object() command chala kar S3 se actual file download karega.

<br>
<br>

### S3 Event Notification mein filter ka concept

Suppose bucket mein:
```
uploads/
images/
videos/
documents/
backups/
```
sab kuch aa raha hai.

Aap nahi chahte ki har object upload par Lambda trigger ho. Aapko sirf images process karni hain. Aap prefix aur suffix based filtering use kar sakte ho.

**Prefix ko detail mein samjho**:

Suppose objects hain:
```
uploads/user1/a.jpg
uploads/user2/b.jpg
documents/a.pdf
logs/app.log
```
Agar event filter:
```
Prefix = uploads/
```
hai, to:
```
uploads/user1/a.jpg
uploads/user2/b.jpg
```
eligible honge.

Lekin:
```
documents/a.pdf
logs/app.log
```
eligible nahi honge.

<br>

**Suffix ko detail mein samjho**:

Suppose aapko sirf CSV files process karni hain.

Objects:
```
data/users.csv
data/orders.csv
data/report.json
data/image.jpg
```
Aap suffix:
```
.csv
```
configure kar sakte ho.

Then:
```
users.csv
orders.csv
```
events processing ke liye eligible honge.

<br>

**Prefix + suffix ka powerful use**:

Suppose:
```
Prefix = incoming/
Suffix = .csv
```
To:
```
incoming/users.csv
incoming/orders.csv
```
match honge.

Lekin:
```
incoming/image.jpg
archive/users.csv
```
match nahi karenge.

Production mein ye kaafi useful hai.

<br>
<br>

### Permissions (The IAM Policy)

Log aksar S3 notification configure karte hain par alert nahi aata. Iska 90% reason hota hai Permissions.

AWS S3 ko itna adhikaar nahi hai ki woh bina permission ke kisi doosri service (jaise Lambda ya SNS) ko trigger kar sake.

**Solution**: Aapko apne Lambda function ya SNS topic par ek Resource-Based Policy lagani padti hai, jo saaf-saaf kehti hai ki: "Main is specific S3 bucket ko permission deta hoon ki woh mujhe notification bhej sake."

<br>
<br>

### Duplicate events ka concept

Production systems mein ek bahut important rule yaad rakho:
- Event-driven consumers ko duplicate event delivery ko safely handle karna chahiye.

AWS documentation ke according S3 Event Notifications delivery at least once model follow karti hai.

Iska matlab same logical event ka notification more than once receive ho sakta hai.

**At-Least-Once Delivery**: S3 guarantee deta hai ki event kam-se-kam ek baar destination par zaroor pahuchega. Lekin kabhi-kabhi rare cases mein ek hi file ka event do baar bhi ja sakta hai. Isliye aapka Lambda code aisa hona chahiye jo duplicate message se pareshan na ho (Idempotent code).

Example:
```
S3
 ↓
ObjectCreated
 ↓
Event 1
 ↓
Lambda

Same event
 ↓
Event 2
 ↓
Lambda
```
Agar Lambda blindly processing kare:
```
Invoice processed
Invoice processed again
```
to duplicate data create ho sakta hai.

Isliye application ko idempotent design karna important hai.

<br>
<br>

### S3 events - Exam Scenarios

**1. Scenario**: Generate an image thumbnail immediately whenever a user uploads a profile picture into the ```images/``` folder. 
- Use S3 Event Notification to Lambda where lambda can process the image and create thumbnails

**2. Scenario**: A compliance team wants to audit every S3 API call, including PUT, DELETE, tagging changes, access point usage, and internal S3 events, and route them to a central audit account.
- Use Amazon EventBridge to route all the S3 API calls to centralized Audit account.

**3. Scenario**: A video-processing pipeline needs to queue uploaded videos for background processing using a consumer fleet that scales automatically.
- Use Event Notification to Simple Queue Service (SQS) where video (object) metadata e.g. path, date, size etc. is sent to SQS queue. These messages in the queue are processed asynchrounsly by the downstream applications like Lambda or ECS tasks.

**4. Scenario**: A news app needs to fan-out notifications (email, SMS, mobile push) to multiple subscribers as soon as a new article is uploaded.
- Use S3 Event Notification to Simple Notification Service (SNS) topic. Topic should be subscribed by the end users / mobile.

**5. Scenario**: A finance team wants to trigger monthly billing workflows across multiple AWS accounts whenever a .csv report lands in a reports/ folder 
- Use Amazon EventBridge to trigger AWS Step function workflow across multiple AWS accounts.

**6. Scenario**: A media archive system wants to process archived files automatically using Lambda when a Glacier restore completes.
- Use S3 Event Notification to Lambda when Glacier restore event is generated.

<br>
<br>
<br>

## Exercise - S3 Event Notification

- Create SNS topic and create email subscription.
- SNS Topic Access Policy to allow S3 to send notification.
- Check your email and confirm the subscription.
- Create S3 event notification with SNS as target.
- Upload an object into source bucket and wait for few seconds.
- Verify if you have received an email.
