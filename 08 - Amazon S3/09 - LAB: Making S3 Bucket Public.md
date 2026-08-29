# LAB: Making S3 Bucket Public

### S3 Bucket ko Public Kaise Banayein (Step-by-Step Lab)

Agar aapko koi static website host karni hai ya open assets serve karne hain, toh Account Level aur Bucket Level dono ka OFF hona mandatory hai.

Matlab apko Account Level par Block Public Access (BPA) aur Bucket Level par BPA, dono ko off karna hoga aur public access ke liye bucket policy lagani padegi, jab jake apki bucket public ho payeg.

Bucket public hone ka matlab hai ki koi bhi user apki bucket ka data dekh sakta hai.

Account level par Block Public Access by default OFF hota hai, matlab account level par bucket public hoti hain. Lekin by any chance woh ON hai to apko off karna hoga.

<br>
<br>

### Steps by step, kaise bucket ko public banana hai

Bucket ko public karne ye steps hote hain:
- Account-Level BPA ko Review/Turn OFF Karein.
- Bucket-Level BPA ko OFF Karein.
- Bucket Policy Laga Kar Explicit Public Read Access Dena.

In uper ke 3 stpes ko follow karne se apki bucket public ho jati hai aur koi bhi use apki bucket ka data dekh sakta hai.

```
Account BPA = OFF ───► Bucket BPA = OFF ───► Apply Bucket Policy (Allow) ───► Bucket is now PUBLIC
```

<br>

**Step 1: Account-Level BPA ko Review/Turn OFF Karein**:

- AWS Management Console mein login karein aur Amazon S3 service open karein.
- Left navigation pane mein "Account and organization settings" (kuch consoles par "Block Public Access settings for this account") par click karein.
- Right side mein Block Public Access settings for this account ke under Edit button par click karein.
- "Block all public access" checkbox ko un-tick karein, matlab apko checkbox ko un-tick karna hai, jisse ye setting disable ho jayegi.
- Save changes par click karein.
- Confirm karne ke liye pop-up dialog box mein type karein: ```confirm``` aur Confirm par click kar dein.

<br>

**Step 2: Bucket-Level BPA ko OFF Karein**:

- Apne target bucket ke Permissions tab mein jayein.
- Block public access (bucket settings) ke Edit par click karein.
- Block all public access ko Uncheck kar dein.
- Save changes karein aur acknowledgement prompt par ```confirm``` enter karein.

<br>

**Step 3: Bucket Policy Laga Kar Explicit Public Read Access Dena**:

Sirf BPA off karne se bucket public nahi hota; AWS by default sab kuch deny rakhta hai. Ab aapko permissions open karni hongi:
- Bucket ke Permissions tab mein niche scroll karein aur Bucket policy ke aage Edit par click karein.
- Niche diya gaya JSON policy structure paste karein (Apna bucket name replace karein):
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::YOUR_BUCKET_NAME/*"
        }
    ]
}
```
- Save changes par click karein.
- Bucket dashboard par red color ka "Public" badge active ho jayega.


Ab apko bucket public hai, koi bhi user apki bucket ka data browser par dekha sakta hai download kar sakta hai.

DONE!!!
