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

