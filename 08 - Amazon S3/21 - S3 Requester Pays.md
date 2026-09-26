# S3 Requester Pays

AWS S3 Requester Pays S3 bucket ka ek aisa setting ya feature hai jismein data download (Data Transfer Out) aur API requests ka saara kharcha Bucket ke Owner (jisne bucket banaya hai) ke bajaye Data Request Karne Wale User (Requester) ke account par bill hota hai.

Standard (Normal) S3 settings mein rule yeh hota hai ki bhale hi duniya ka koi bhi insaan aapke bucket se data download kare, bill hamesha bucket ke owner ko hi bharna padta hai. Lekin agar aapka data bohot bada hai aur aap use publically ya kisi specific teams ke saath share karna chahte hain, to standard model ke according bucket ke owner ko hi bill bharna padega, ese mein agar dusri team bohot apki s3 bucket se bohot jyada api request karke data leti hai to bohot saara bill apke account mein band jayega. Aise mein **Requester Pays** kaam aata hai.

Requester Pays feature se bucket se data access karne wale AWS account ko bill bharna padta hai, aapke pass s3 bucket access ka bill nahi aayega.

Requester Pays feature mein jo user data access kar raha hai, uske paas apna AWS account hona bilkul ZAROORI hai, aur us AWS account par hi billing create hogi. Jis user ke paas AWS account nahi hai, woh is bucket se data nahi nikal sakta.

<br>
<br>

### Standard S3 vs Requester Pays S3 (Billing Difference)

S3 humse teen tarah se charge leta hai:
- **Storage Cost**: Bucket mein data rakhne ka monthly rent (Kharcha). Yeh lagbhag $0.023 per GB per month hota hai, region ke according change hota hai.
- **Data Transfer Out Charge**: Bucket se data ko download karne ka charge.
- **Data IN (Upload)**: Bilkul FREE hai. Aap internet se jitna chahein data S3 mein upload karein, AWS ₹1 bhi nahi lega.
- **Requests Cost (API Calls)**: S3 bucket par aane waali sabhi request jaise, GET, POST, PUT, LIST, in sabhi ka charge leta hai.

<br>

**Is table se samajhie ki dono models mein bill kaun bharta hai**:

|        Cost Component        | Standard Bucket Model |              Requester Pays Model              |
|:----------------------------:|:---------------------:|:----------------------------------------------:|
|         Storage Cost         |  Bucket Owner bharega |              Bucket Owner bharega              |
| Data Transfer Out (Download) |  Bucket Owner bharega | 👤 Requester bharega (Jo download kar raha hai) |
|   API Requests (GET, LIST)   |  Bucket Owner bharega | 👤 Requester bharega (Jo download kar raha hai) |


Requester Pays enable karne ke baad bhi, data ko bucket mein safe rakhne ka (Storage) rent hamesha Bucket Owner ko hi dena padega. Sirf downloading aur requests ka kharcha dusron par shift hota hai.

<br>
<br>

### Real-World Example Se Samjhein

Maan lijiye aap ek Data Scientist hain aur aapne 10 TB (Terabytes) ka ek massive Weather Forecasting Dataset generate kiya hai. Aap chahte hain ki duniya bhar ke alag-alag universities aur researchers is data ko use karein.
- **Agar aap Requester Pays use nahi karte (Standard)**: Kisi university ne aapke dataset ko download kiya. 10 TB download karne ka AWS bill (approx $900) aapke (Owner) account mein aa jayega. Agar 10 alag-alag logon ne download kar liya, to aapko bina baat ke $9,000 ka jhatka lag jayega.
- **Agar aap Requester Pays enable kar dete hain**: Ab agar Stanford University aapka data download karegi, to download karne ka $900 ka bill direct Stanford University ke AWS account mein jayega. Aapka account bilkul safe rahega, aapko sirf storage ka chota sa amount dena hoga.

<br>
<br>

### Requester Pays Kaam Kaise Karta hai? 

Jab aap kisi bucket par Requester Pays enable karte hain, to bucket access karne ka tarika thoda badal jata hai. Kisi bhi anonymous (bin-pehchane) public user ko access nahi milta.
- **Authenticated Requests Only**: Request karne wale user ke paas apna khud ka AWS Account (IAM User/Credentials) hona zaroori hai. Kyunki agar unke paas AWS account hi nahi hoga, to AWS unhe bill kaise karega?
- **The** ```x-amz-request-payer``` **Header**: Jab requester S3 se data mangwata hai (API, CLI, ya SDK ke zariye), to use apni request mein ek specific header pass karna padta hai: x-amz-request-payer: requester. Yeh ek tarah ka agreement hota hai jismein requester kehta hai ki "Haan, main is request ka paisa dene ke liye taiyar hoon." Agar wo ye header nahi bhejega, to S3 request ko reject kar dega (403 Access Denied error dega).

**AWS CLI Se Download Karne Ka Example**:

Agar aap kisi aise bucket se download kar rahe hain jahan Requester Pays active hai, to aapko ```--request-payer``` parameter lagana padega:
```
aws s3 cp s3://someone-else-bucket/bigdata.zip . --request-payer requester
```

<br>
<br>

### Requester Pays Ki Limitations (Kahan Kaam Nahi Karta?)

- **Anonymous Access (Public URLs)**: Agar aapne bucket mein Requester Pays enable kar diya, to aap normal web browser link (```http://amazonaws.com```) banakar kisi aam public user ko nahi de sakte. Browser bina authentication aur header ke request bhejta hai, isliye wo fail ho jayegi.
- **BitTorrent Downloads**: Requester Pays buckets par aap BitTorrent ke zariye data distribute nahi kar sakte.
- **Same AWS Account Billing**: Agar aapke bucket ko aap hi ke company ka koi dusra IAM user access kar raha hai (same AWS account), to billing par koi asar nahi padega, wo ghum-fir kar aap hi ke main master bill mein aayega. Yeh sirf tab kaam aata hai jab do alag-alag AWS accounts aapas mein deal kar rahe honge.

<br>
<br>

### Bucket Par Ise Enable Kaise Karte Hain?

AWS Console mein jaakar apne **S3 Bucket** par click karein -> **Properties** tab mein jayein -> **Requester Pays** section par scroll karein -> Edit par click karke use **Enable** kar dein.
