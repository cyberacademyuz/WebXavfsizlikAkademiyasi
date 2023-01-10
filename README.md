---
cover: .gitbook/assets/sqlinjection banner.png
coverY: 0
---

# SQL ineksiya (SQL injection)

Ushbu bo'limda SQL ineksiya nima ekanligini o'rganamiz, va buni ba'zi misollar bilan tariflaymiz, turlicha SQL ineksiya zaifliklarini qanday topish va ulardan foydalanishni tushuntiramiz va qanday qilib SQL ineksiyani oldini olishni ham ko'rib chiqamiz.

![](<.gitbook/assets/image (30).png>)

## <mark style="color:yellow;">SQL</mark> <mark style="color:yellow;"></mark><mark style="color:yellow;"><mark style="color:green;"><mark style="color:green;"></mark> <mark style="color:yellow;"></mark><mark style="color:yellow;">ineksiya o'zi nima ?</mark> <mark style="color:yellow;"></mark><mark style="color:yellow;"><mark style="color:red;">****<mark style="color:red;"></mark><mark style="color:yellow;">** **</mark><mark style="color:yellow;">**(SQLi)**</mark> <a href="#retrieving-hidden-data-yashirin-malumotlarni-olish" id="retrieving-hidden-data-yashirin-malumotlarni-olish"></a>

<mark style="color:yellow;">**SQL ineksiya**</mark> - bu hackerga websaytning ma'lumotlar ba'zasiga yuboriladigan so'rovlarga o'zgartirishlar kiritish imkonini beruvchi web-xavfsizlik zaifligi hisoblanadi. Bu asosan hackerga, to'g'ridan to'g'ri olishning imkoni bo'lmagan maxfiy ma'lumotlarni ko'rish imkonini beradi. Masalan bularga, saytda ro'yxatdan o'tgan foydalanuvchilarga tegishli bo'lgan ma'lumotlar yoki websayting o'zi kirishi mumkin bo'lgan boshqa ma'lumotlar bo'lishi mumkin. Hacker ko'pincha ushbu ma'lumotlarni o'zgartirishi yoki o'chirib tashlashi mumkin, bu esa websaytdagi ma'lumotlarga yoki sayt bajaradigan harakatlarida doimiy o'zgarishlarga olib keladi.



Ba'zi hollarda haker SQL ineksiya hujumini rivojlantirib, asosiy server yoki boshqa infratuzilmani buzishi yoki DOS hujumini amalga oshirishi mumkin.

## <mark style="color:yellow;">SQL ineksiya</mark> <mark style="color:yellow;"></mark><mark style="color:yellow;">**hujumining tasiri qanday ?**</mark> <a href="#retrieving-hidden-data-yashirin-malumotlarni-olish" id="retrieving-hidden-data-yashirin-malumotlarni-olish"></a>

Muvaffaqiyatli amalga oshirilgan SQL ineksiya hujumi parollar, kredit karta ma'lumotlari yoki foydalanuvchi ma'lumotlari kabi maxfiy ma'lumotlarga ruxsatsiz kirishga imkon yaratib berishi mumkin. So'nggi yillarda ko'plab o'ta maxfiy ma'lumotlar buzilishi SQL ineksiya hujumlari natijasida yuzaga keldi. Ba'zi holatlarda, hacker tashkilot tizimlariga doimiy kirish uchun backdoorni ham qo'lga kiritishi mumkin.

## <mark style="color:yellow;">SQL ineksiyaga misollar</mark> <a href="#retrieving-hidden-data-yashirin-malumotlarni-olish" id="retrieving-hidden-data-yashirin-malumotlarni-olish"></a>

Har xil holatda yuzaga keladigan turlicha SQL ineksiya zaifliklari, hujumlar va usullar mavjud. Ba'zi umumiy SQL ineksiya zaifliklariga quyidagilar misol bo'la oladi:

<mark style="color:yellow;">****</mark>[<mark style="color:yellow;">**Retriving hidden data:**</mark>](./#retrieving-hidden-data-yashirin-malumotlarni-olish-3) <mark style="color:orange;"></mark> Qo'shimcha natijalarga ega bo'lish uchun SQL so'rovini o'zgartirish va shu orqali yashirin ma'lumotlarni olish.

<mark style="color:yellow;">****</mark>[<mark style="color:yellow;">**Subverting application logic: (Ilova logikasini o'zgartirish)**</mark>](./#subverting-application-logic-ilova-logikasini-ozgartirish)<mark style="color:yellow;">,</mark> websayt logikasiga aralashish uchun SQL so'rovini o'zgartirishingiz mumkin.

<mark style="color:yellow;">****</mark>[<mark style="color:yellow;">**UNION**</mark><mark style="color:yellow;">** **</mark><mark style="color:yellow;"><mark style="color:orange;">****<mark style="color:orange;"></mark><mark style="color:yellow;">** **</mark><mark style="color:yellow;">**attacks: (UNION hujumlari)**</mark>](sql-ineksiya/sql-ineksiya-union-hujumlari.md) turli databasedagi jadvallardan ma'lumotlarni olishingiz mumkin.

<mark style="color:yellow;">****</mark>[<mark style="color:yellow;">**Examining the database: (Ma'lumotlar bazasini tekshirish)**</mark>](sql-ineksiya/malumotlar-bazasini-tekshirish.md) <mark style="color:yellow;"></mark> ma'lumotlar bazasining versiyasi va tuzilishi haqida ma'lumot olishingiz mumkin.

<mark style="color:yellow;">****</mark>[<mark style="color:yellow;">**Blind SQL injection**</mark>](sql-ineksiya/blind-sql-ineksiya.md) orqali, siz boshqarayotgan so'rov natijalari websayt javoblarida qaytarilmaydi.

## <mark style="color:yellow;">Retrieving hidden data (Yashirin ma'lumotlarni olish)</mark> <a href="#retrieving-hidden-data-yashirin-malumotlarni-olish" id="retrieving-hidden-data-yashirin-malumotlarni-olish"></a>

Turli mahsulotlarni xarid qilish mumkin bo'lgan elektron bozor websaytini ko'zdan kechiring. Foydalanuvchi "**Gifts**" toifasini bosganida, uning brauzeri quyidagi URLga so'rov jo'natadi.

```url
https://sizning-subdomeningiz.web-security-academy.net/filter?category=Gifts
```

Bu holatda websayt ma'lumotlar bazasidan kerakli mahsulotlarning ma'lumotlarini olish uchun quyidagicha SQL so'rovni amalga oshiradi:

```sql
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
```

SQL so'rovi, ma'lumotlar bazasidan quyidagi ma'lumotlarni berishini so'raydi:

* Barcha mahsulotlarni (\*) olish
* **products** jadavali
* kategotiyasi **Gifts** bo'lgan
* va **released =** 1 bo'lgan

`released = 1`  degani keraksiz mahsulotlarni yashirish uchun ishlatiladi. Chiqarilmagan mahsulotlar released = 0 bo'lishi mumkin.

Web saytda SQL ineksiya uchun hech qanday himoya vositasi bo'lmasa, quyidagicha http request yuborish orqali SQL ineksiya hujumini amalga oshirish mumkin:

```url
https://sizning-subdomeningiz.web-security-academy.net/products?category=Gifts'--
```

Bu request natijasida quyidagi SQL so'rovi bajariladi:

```plsql
SELECT * FROM products WHERE category = 'Gifts'--' AND released = 1
```

Bu yerda asosiy narsa 2 chiziq ( -- ). Bu SQLda komentariya hisoblanadi, SQL so'rovning qolgan qismi koment sifatida yuborilishini bildiradi. U SQL soʻrovning qolgan qismini olib tashlaydi, shuning uchun endi `' AND released = 1`  qismi ishlamaydi. Bu barcha mahsulotlar koʻrsatiladi degan maʼnoni anglatadi.

Hacker quyidagi request orqali ham websaytdagi istalgan toifadagi barcha mahsulotlarni, shu jumladan berkitilgan ma'lumotlarni aniqlashi mumkin:&#x20;

```url
https://sizning-subdomeningiz.web-security-academy.net/products?category=Gifts'+OR+1=1--
```

Bu requestning malumotlar bazasida bajariladigan SQL so'rovi:

```sql
SELECT * FROM products WHERE category = 'Gifts' OR 1=1--' AND released = 1
```

Ma'lumotlar bazasida joylashgan barcha ma'lumotlar jadvallarda joylashgan bo'ladi. Bu SQL so'rovni so'z bilan tariflaydigan bo'lsak yuqoridagi kod ma'lumotlar bazasiga, jadvalingda joylashgan barcha (\*) mahsulotlardan `Gifts` kategoriyasiga kiritilganlarni ko'rsat yoki 1, 1 ga teng bo'lsa (1=1) ma'lumotlarni chiqar va qolgan qatorni kamentariyaga ol demoqda. Albatta 1 doim 1 ga teng bo'ladi va so'rovning qolgan qismi koment shaklida yuboriladi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Yashirin ma'lumotlarni olish imkonini beruvchi WHERE bandidagi SQL ineksiya zaifligi **≫**](https://portswigger.net/web-security/sql-injection/lab-retrieve-hidden-data)****
{% endhint %}

## <mark style="color:yellow;">Subverting application logic: (</mark>_<mark style="color:yellow;">Ilova logikasini o'zgartirish</mark>_<mark style="color:yellow;">)</mark> <a href="#subverting-application-logic-ilova-logikasini-ozgartirish" id="subverting-application-logic-ilova-logikasini-ozgartirish"></a>

Foydalanuvchilarga username va parol orqali kirishga imkon beradigan sahifani tekshirib ko'ring. Agar foydalanuvchi username qismga <mark style="color:red;">wiener</mark> va parolga <mark style="color:red;">bluecheese</mark> ni yuborsa, dastur quyidagi SQL so'rovni bajarish orqali hisob ma'lumotlarini tekshiradi:

```sql
SELECT * FROM users WHERE username = 'wiener' AND password = 'bluecheese'
```

Agar response foydalanuvchining ma'lumotlarini qaytarsa shaxsiy kabinetga muvaffaqiyatli kiriladi, aks holda bekor qilinadi. Bu yerda hacker SQLdagi komentariya so'rovidan foydalanib `WHERE` bandidan parol tekshiruvini olib tashlash orqali parolsiz istalgan foydalanuvchi sifatida  tizimga kirishi mumkin. Masalan, foydalanuvchi nomiga <mark style="color:red;background-color:red;">administrator</mark><mark style="background-color:red;">'--</mark> yozib va parolga hech narsa yozmasdan request yuborish natijasida quyidagi SQL so'rovi amalga oshiriladi:

```sql
SELECT * FROM users WHERE username = 'administrator'--' AND password = ''
```

Bu SQL so'rov  <mark style="color:red;">`administrator`</mark>  foydalanuvchisi bo'lsa tizimga uning hisobidan kirishga imkon yaratib beradi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark>[ Loginni aylanib o'tishga imkon beruvchi SQL ineksiya zaifligi **≫**](https://portswigger.net/web-security/sql-injection/lab-login-bypass)&#x20;
{% endhint %}

## <mark style="color:yellow;">Retriving data from other database tables: (Boshqa database jadvallaridan ma'lumotlarni olish</mark> <a href="#subverting-application-logic-ilova-logikasini-ozgartirish" id="subverting-application-logic-ilova-logikasini-ozgartirish"></a>

Agar SQL so'rov natijalari websayt responsida qaytarilsa, hacker ma'lumotlar bazasidagi boshqa jadvallardan ma'lumotlarni olish uchun SQL ineksiya zaifligidan foydalanishi mumkin. Bu yana bir qo'shimcha `SELECT` so'rovini bajarish va natijani so'rovga qo'shish imkonini beruvchi `UNION` kalit so'zi yordamida amalga oshiriladi.&#x20;

Misol uchun foydalanuvchi  saytdagi "<mark style="color:green;">Gifts</mark>" kategoriyasini tanlashi orqali quyidagi so'rov yuborilsa:

```sql
SELECT name, description FROM products WHERE category = 'Gifts'
```

bunga  `' UNION SELECT username, password FROM users--` SQL so'rovini ham qo'shib yuborishi mumkin:

Bu websaytga mahsulot nomlari va ma'lumotlari bilan birga barcha foydalanuvchi usernamelari va parollarini ko'rsatishga olib keladi.

{% hint style="info" %}
**Ko'proq o'qish:**

[SQL ineksiya UNION hujumi ☰](sql-ineksiya/sql-ineksiya-union-hujumlari.md)
{% endhint %}

## <mark style="color:yellow;">Ma'lumotlar bazasini tekshirish</mark> <a href="#malumotlar-bazasini-tekshirish" id="malumotlar-bazasini-tekshirish"></a>

SQL ineksiya zaifligi aniqlangandan keyin, ma'lumotlar bazasi haqida ba'zi ma'lumotlarni olish ham foyda beradi. Ushbu ma'lumot ko'pincha keyingi qadamdagi boshqa eksplutatsiyalar uchun yo'l ochishi mumkin.

Ma'lumotlar bazasi uchun uning versiyasi haqidagi ma'lumotlarni so'rab bilib olishingiz ham mumkin. Buni amalga oshiruvchi SQL kod sintaksisi ma'lumotlar bazasining turiga bog'liq, shuning uchun qaysi ma'lumotlar bazasi ishlayotganidan qat'iy nazar, ma'lumotlar bazasining turini aniqlashingiz kerak. Masalan, **Oracle**-da siz quyida SQL so'rovini yuborish orqali uning versiyasi haqida ma'lumot olishingiz mumkin:

```sql
SELECT * FROM v$version
```

Shuningdek, ma'lumotlar bazasida qanday jadvallar borligini va ular qanday ustunlardan iboratligini aniqlashingiz ham mumkin. Masalan, ko'pgina ma'lumotlar bazalarida jadvallarning ro'yxatini olish uchun quyidagi so'rovni yuborishingiz mumkin:

```sql
SELECT * FROM information_schema.tables
```

{% hint style="info" %}
**Ko'proq o'qish:**

[SQL ineksiya hujumlarida ma'lumotlar bazasini tekshirish ☰](./#malumotlar-bazasini-tekshirish)

[SQL ineksiya cheat sheet ☰](sql-ineksiya/sql-ineksiya-cheat-sheet.md)
{% endhint %}

## <mark style="color:yellow;">Blind SQL ineksiya zaifligi</mark> <a href="#blind-sql-ineksiya-zaifligi" id="blind-sql-ineksiya-zaifligi"></a>

<mark style="color:yellow;">****</mark>[<mark style="color:yellow;">**Bind SQL**</mark>](sql-ineksiya/blind-sql-ineksiya.md) ineksiya ko'p ucharydigan ineksiya turi hisoblanadi. Bu zaiflikda, websayt SQL so'rov natijalarini yoki  database-ga tegishli xatolarning ma'lumotlarini responseda qaytarmaydi. Blind SQL ineksiyadan ma'lumotlarga ruxsatsiz kirish uchun hali ham foydalanish mumkin, ammo bu bilan bog'liq usullar odatda ancha murakkab va amalga oshirish qiyin.

Zaiflik turiga va tegishli ma'lumotlar bazasiga qarab, **Blind SQL** ineksiya orqali zaifliklardan foydalanish uchun quyidagi usullardan foydalanish mumkin:

* Siz conditionning to'griligiga qarab, websaytdan qaytayotgan javobda yuzaga keladigan farqni aniqlash uchun so'rovning logikasini o'zgartirishingiz mumkin. Bu bazi mantiqiy amal logikasiga yangi shart kiritishga imkon berishi yoki shartli ravishda **divide-by-zero** (nolga bo'lish) kabi xatoni keltirib chiqarishi mumkin.
* Siz so'rovni ko'rib chiqish vaqtini majburan sekinlashtirishingiz mumkin, bu sizga websaytning reponse qaytarish vaqtiga asoslanib, shartning to'g'riligini aniqlashga imkon beradi.
* <mark style="color:yellow;"></mark>[<mark style="color:yellow;">OAST</mark>](https://portswigger.net/burp/application-security-testing/oast) texnikasidan foydalangan holda **OAST** tarmog'ining o'zaro ta'sirini ishga tushirishingiz mumkin. Ushbu texnika juda kuchli va boshqa usullar ishlamagan holatlarda ham ishlaydi. Ko'pincha siz OAST kanali orqali ma'lumotlarni to'g'ridan-to'g'ri olishingiz mumkin, Masalan, ma'lumotlarni, **siz nazorat qiladigan** domen uchun DNS lookup-ga joylashtirish orqali.

{% hint style="info" %}
**Ko'proq o'qish:**

[Blind SQL ineksiya ☰](sql-ineksiya/blind-sql-ineksiya.md)
{% endhint %}

## <mark style="color:yellow;">SQL ineksiya zaifligini qanday aniqlash mumkin ?</mark> <a href="#sql-ineksiya-zaifligini-qanday-aniqlash-mumkin" id="sql-ineksiya-zaifligini-qanday-aniqlash-mumkin"></a>

SQL ineksiya zaifliklarining bazi birlarini [<mark style="color:yellow;">Burp Suite</mark>](https://portswigger.net/burp/communitydownload) web-zaiflik skaneri yordamida ham tez va samarali tarzda aniqlash mumkin.

SQL ineksiya tekshiruvini websaytning har bir SQLi bo'lishi mumkin bolgan qismlarida payloadlardan foydalangan holda qo'lda aniqlash ham mumkin. Bu odatda quyidagilarni o'z ichiga oladi:

* Bir tirnoq `'` belgisini yuborish va xatolar yoki boshqa [<mark style="color:yellow;">anomaliyalarni</mark> ](https://savodxon.uz/izoh?anomaliya)qidirish.
* Entry pointning asl qiymatini va boshqa qiymatni solishtiruvchi ba'zi SQL so'rov sintaksisini yuborish va natijada websayt javoblaridagi o'zgarishlarni aniqlash.
* `OR 1=1` va `OR 1=2, and` kabi **Boolean** shartlarini yuborish va websayt responsedagi o'zgarishlarni aniqlash.
* SQL so'rovi bajarilganda vaqtni sekinlashtirish uchun mo'ljallangan payloadlarni yuboring va javob qaytishdagi vaqting farqlarini aniqlang.
* SQL so'rovi bajarilganda OAST tarmog'ining o'zaro ta'sirini ishga tushirish uchun mo'ljallangan OAST payloadlarini yuborish va har qanday natijada ta'sirlarini kuzatish.

## <mark style="color:yellow;">So'rovning turli qismlarida SQL ineksiya</mark> <a href="#sorovning-turli-qismlariga-sql-inektsiyasi" id="sorovning-turli-qismlariga-sql-inektsiyasi"></a>

Ko'pgina SQL ineksiya zaifliklari `SELECT` so'rovining `WHERE` qismida paydo bo'ladi. Ushbu turdagi SQL ineksiyani tajribali pentesterlar yaxshi tushunishadi.

Biroq, **SQL ineksiya zaifliklari**, qoida tariqasida so'rovning istalgan joyida va turli so'rov turlarida paydo bo'lishi mumkin. Quiydagilar SQL ineksiya paydo bo'lishi mumkin bo'lgan eng keng tarqalgan boshqa qismlar:

* `UPDATE` bayonot (statement)larida, yangilangan qiymatlarda yoki `WHERE`qismida
* `INSERT` bayonotlarida, kiritilgan qiymatlar ichida.
* `SELECT` bayonotlarida, jadval yoki ustun nomi ichida.
* `SELECT`bayonotlarida, `ORDER BY` bandida.

## <mark style="color:yellow;">Turli kontekstlardagi SQL ineksiya</mark> <a href="#sql-injection-in-different-contexts" id="sql-injection-in-different-contexts"></a>

Hozirgacha barcha laboratoriyalarda siz zararli SQL payloadingizni ineksiya qilish uchun **string** turidagi SQL so'rovdan foydalandingiz. Ammo shuni ta'kidlash kerakki, siz websayt SQL so'rov sifatida amalga oshiradigan har qanday input yordamida SQL ineksiya hujumlarini amalga oshirishingiz mumkin.

Misol uchun, ba'zi websaytlar inputni **JSON** yoki **XML** formatida qabul qiladi va ma'lumotlar bazasini so'rash uchun undan foydalanadi.

Bu turli formatlar hatto **WAF** va boshqa ximoya mexanizmlari tufayli bloklangan hujumlarni obfuskatsiya qilish uchun boshqa usullarni ham taqdim etishi mumkin. Zaif websaytlar ko'pincha request ichidan oddiy SQL inektsiya keywordlarini qidiradi, shuning uchun siz bu filtrni chetlab o'tish uchun man qilingan belgilarni **encode** qilishingiz kerak yoki man qilingan belgilardan foydalanmaslik orqali ushbu filtrlarni aylanib o'tishingiz mumkin. Misol uchun, quyidagi XML-ga asoslangan SQL inektsiya `SELECT` qismida S harfini encode qilish uchun [<mark style="color:yellow;">XML escape</mark>](https://www.ibm.com/docs/en/was-liberty/base?topic=SSEQTP\_liberty/com.ibm.websphere.wlp.doc/ae/rwlp\_xml\_escape.htm) dan foydalanyapti.

```xml
<stockCheck>
    <productId>
        123
    </productId>
    <storeId>
        999 &#x53;ELECT * FROM information_schema.tables
    </storeId>
</stockCheck>
```

Bu SQL so'rovi bajarilishidan avval server tomonda decode qilinadi.

{% hint style="warning" %}
<mark style="color:yellow;">LAB:</mark> [XML encoding orqali filterni chetlab o'tuvchi SQL ineksiya **≫**](https://portswigger.net/web-security/sql-injection/lab-sql-injection-with-filter-bypass-via-xml-encoding)****
{% endhint %}

## <mark style="color:yellow;">Ikkinchi darajali SQL ineksiya</mark> <a href="#ikkinchi-darajali-sql-inektsiyasi" id="ikkinchi-darajali-sql-inektsiyasi"></a>

Websayt, foydalanuvchi kiritgan ma'lumotlarni **HTTP** **request**dan qabul qilganida va ushbu requestni **qayta ishlash jarayonida** ma'lumotlarni SQL so'roviga havfsiz bo'lmagan holda kiritganida birinchi darajali SQL ineksiya paydo bo'ladi.

Ikkinchi darajali SQL ineksiyada esa (shuningdek, **Stored SQL ineksiya** ham deyiladi) websayt, foydalanuvchi kiritgan ma'lumotlarni HTTP requestdan oladi va ulardan keyinchalik foydalanish uchun saqlab qo'yadi. Bu odatda input orqali kiritilgan ma'lumotlarni ma'lumotlar bazasiga joylashtirish orqali amalga oshiriladi, ammo ma'lumotlar saqlanadigan joyda hech qanday zaiflik paydo bo'lmaydi. Keyinchalik, boshqa HTTP so'rovni ko'rib chiqayotganida websaytda saqlangan ma'lumotlarni oladi va xavfsiz tarzda SQL so'roviga kiritadi.

![](<.gitbook/assets/image (16).png>)

<mark style="color:yellow;">**Ikkinchi darajali SQL ineksiya**</mark> <mark style="color:blue;"></mark> ko'pincha dasturchilar SQL ineksiya zaifliklaridan xabardor bo'lishgani sababli  ma'lumotlar bazasini joylashtirishni xavfsiz boshqarish holatlarida sodir bo'ladi. Ma'lumotlar keyinchalik qayta ishlanganda, ular xavfsiz deb hisoblanadi, chunki ular ilgari ma'lumotlar bazasiga xavfsiz tarzda joylashtirilgan. Ushbu qismda ma'lumotlar xavfsiz tarzda qayta ishlanadi, chunki dasturchi uni ishonchli deb hisoblaydi.

## <mark style="color:yellow;">Ma'lumotlar bazasiga xos omillar</mark> <a href="#malumotlar-bazasiga-xos-omillar" id="malumotlar-bazasiga-xos-omillar"></a>

Ma'lumotlar bazasi uchun SQL tilining ba'zi asosiy xususiyatlari mashhur **ma'lumotlar baza**larida ham bir xil tarzda amalga oshiriladi va SQL ineksiya zaifliklarini aniqlash va ma'lumotlar bazalari turlicha bo'lsada SQL zaifliklaridan foydalanishning ko'plab usullari bir xil.

Biroq, umumiy databaselar o'rtasida juda ko'p farqlar mavjud. Bu SQL ineksiyani aniqlash va ekspluatatsiya qilishning ba'zi usullari turli platformalarda boshqacha ishlashini anglatadi. Masalan:

* Stringlarni birlashtirish uchun sintaksis
* Komentlar
* To'plamli (yoki to'plangan) so'rovlar
* Platformaga xos APIlar
* Xatolik habarlari

{% hint style="info" %}
**Ko'proq o'qish:**\
[SQL ineksiya cheat sheet ☰](sql-ineksiya/sql-ineksiya-cheat-sheet.md)
{% endhint %}

## <mark style="color:yellow;">SQL ineksiyani oldini olish</mark> <a href="#sql-ineksiyasining-oldini-olish" id="sql-ineksiyasining-oldini-olish"></a>

SQL ineksiyaning ko'p holatlarini, SQL so'rovda stringlarni birlashtirish o'rniga **parametrlangan SQL so'rovlar** yordamida oldini olish mumkin.

Quyidagi kod SQL ineksiyani keltirib chiqaradi, chunki foydalanuvchi kiritgan ma'lumotlar to'g'ridan-to'g'ri so'rovga birlashtirilmoqda:

```sql
String query = "SELECT * FROM products WHERE category = '"+ input + "'";
Statement statement = connection.createStatement();
ResultSet resultSet = statement.executeQuery(query);
```

Ushbu kod osongina qayta ishlanishi mumkin, shunda foydalanuvchi kiritgan so'rovlar tuzilishini buzmaydi:

```plsql
PreparedStatement statement = connection.prepareStatement("SELECT * FROM products WHERE category = ?");
statement.setString(1, input);
ResultSet resultSet = statement.executeQuery();
```

<mark style="color:yellow;">**Parametrlangan so'rovlar,**</mark> ishonchsiz ma'lumotlar so'rovda **ma'lumot sifatida** paydo bo'ladigan har qanday vaziyatda, shu jumladan `WHERE` va`INSERT` yoki  `UPDATE` steytmentlardagi qiymatlar uchun ishlatilishi mumkin. Lekin ulardan soʻrovning boshqa qismlarida, masalan, jadval yoki ustun nomlari yoki `ORDER BY` qismida ishonchsiz maʼlumotlarni qayta ishlash uchun ishlatib boʻlmaydi. So'rovning ushbu qismlariga, ishonchsiz ma'lumotlarni joylashtiradigan websayt funksiyasi kiritish mumkin bo'lgan qiymatlarni **white list**ga kiritish yoki kerakli vazifani amalga oshirish uchun boshqa logikadan foydalanish kabi boshqa yondashuvni qo'llashi kerak.

<mark style="color:yellow;">**Parametrlangan so'rov**</mark> <mark style="color:yellow;"></mark><mark style="color:yellow;"></mark> SQL ineksiyaning oldini olishda samaraliroq bo'lishi uchun, so'rovda ishlatiladigan kod har doim yaxshi kod bo'lishi va hech qachon biron bir manbadan o'zgaruvchan ma'lumotlarni o'z ichiga olmasligi kerak. Har bir alohida holatda maʼlumotlarning ishonchli ekanligiga qaror qabul qilishim kerak degan hayollarga borishingiz shart emas va rostdan ham xavfsiz  holatlar uchun soʻrovda string birikmasidan foydalanishingiz mumkin.
