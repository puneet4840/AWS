# LAB: Using S3 Storage Classes

Yeh ek step-by-step S3 Storage Classes Hands-on Lab hai. Isme hum manually ek file upload karenge aur uski storage class ko change karenge.

<br>


### Lab Objective

Ek file ko **S3 Standard** (Default) se manually **S3 Standard-IA** aur phir **Glacier Flexible Retrieval** mein change karna.

<br>
<br>

### Step 1: Ek S3 Bucket Banayein (Create a Bucket)

Sabse pehle hume ek bucket chahiye jahan hum file rakh sakein:
- **AWS Console** mein login karein aur search bar mein **S3** search karke open karein.
- Orange color ke Create bucket button par click karein.
- **Bucket name** mein koi bhi unique naam dalein (e.g., ```meri-bucket-puneet```).
- AWS Region jo default hai vahi rehne dein (e.g., ```ap-sount-1```).
- Baaki saari settings ko default chodh dein aur page ke sabse neeche jaakar **Create bucket** par click karein.

<br>
<br>

### Step 2: File Upload Karein (Default Class Check Karein)

Ab hum is bucket mein ek dummy file upload karenge:
- Jo bucket aapne banayi hai, uske **Naam par click** karke use open karein.
- **Upload button** par click karein.
- **Add files** par click karein aur apne computer se koi bhi choti text file ya image select kar lein.
- **Scroll down** karke page ke neeche jayein. Wahan aapko **Properties** ka ek section dikhega.
- Properties ke andar **Storage classes** dikhega. Dekhiye wahan automatic **Standard** selected hoga. (Isko abhi Standard hi rehne dein).
- Sabse neeche jaakar **Upload** par click kar dein.
- Upload complete hone ke baad **Close** button daba dein.

<br>
<br>

### Step 3: Storage Class Ko Manually Change Karein (Standard to Standard-IA)

Ab hum manual tareeqe se is file ki class badlenge:
- Bucket ke andar aapko aapki upload ki hui file dikhegi. Us **File ke naam par click** karein.
- File ki details open ho jayengi. Right side mein top par ek **Actions** ka dropdown button hoga, uspar click karein.
- Dropdown menu mein se **Edit storage class** option ko select karein.
- Ab aapke samne saari storage classes ki list aa jayegi.
- List mein se **Standard-IA (Standard-Infrequent Access)** ke samne wale radio button ko select karein.
- Page ke sabse neeche jayein aur **Save changes** par click kar dein.
- **Success!** Aapki file ki storage class ab Standard-IA ho chuki hai. Aap file ki properties mein check kar sakte hain.

