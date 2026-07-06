### AWS Groups

Jab ek saath bohot saare IAM Users ko same permissions deni ho to hum ek group create karte hain.

IAM Group = Similar kaam karne wale IAM Users ka ek collection.

IAM Group ek logical collection hota hai jisme multiple IAM Users ko add kiya ja sakta hai. Is group ko ek baar permissions di jaati hain aur group ke andar jitne bhi users hote hain, un sabko wahi permissions automatically mil jaati hain.

IAM Group ke andar sirf IAM Users hote hain.

IAM Group ke andar:
- IAM Role add nahi hota.
- Dusra IAM Group add nahi hota.
- Root User add nahi hota.

Sirf IAM Users add kiye ja sakte hain.

<br>
<br>

### AWS Mein IAM Group Kyun Banaya Gaya?

AWS ke engineers ne socha ki agar har user ko individually permissions assign karni padengi, to administrator ka kaam bahut difficult ho jayega.

Isliye unhone ek aisa mechanism banaya jahan permissions ek jagah manage ki ja sakein. To Administrator ek IAM Group create karta hai aur group mein policy attach karta hai. Jisse sabhi users ko same permissions mil jaati hain.

Isi mechanism ko IAM Group kehte hain.

Yaani IAM Group ka main purpose hai:

Permission Management ko easy banana.


<br>
<br>

### IAM Group Permissions Kaise Deta Hai?

Actually Group directly permission create nahi karta.

Administrator Group ke saath IAM Policies attach karta hai.

Example:
```
Developer Group

↓

AmazonS3ReadOnlyAccess

↓

AmazonEC2ReadOnlyAccess
```

Ab group ke sabhi users ko same permissions mil jayengi.

<br>
<br>

### IAM Group ke Kuch Important Rules aur Limits

AWS mein Groups ko use karte waqt kuch rules ka dhyaan rakhna padta hai:

**Group ke andar Group nahi ban sakta (No Nesting)**: Aap kisi Group ko doosre Group ke andar nahi daal sakte (Jaise Group A ke andar Group B nahi ho sakta). Group ke andar sirf aur sirf Users ho sakte hain.

**True Identity Nahi Hai**: Group koi insaan ya program nahi hota, isliye Group ke paas apna koi login password ya Access Keys nahi hoti. Group se koi login nahi kar sakta.

**Multiple Groups**: Ek IAM User ek se zyada Groups ka member ho sakta hai. Jaise 'Amit' naam ka user ek hi waqt par 'Developers-Group' ka member bhi ho sakta hai aur 'Billing-Group' ka member bhi ho sakta hai. Aise mein Amit ko dono groups ki saari permissions mil jayengi.

**Default Group**: AWS mein koi default group nahi hota jahan naye users automatic chale jayein. Aapko khud create karke users ko add karna padta hai.

