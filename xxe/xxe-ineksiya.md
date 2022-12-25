# XXE ineksiya

Bu bo'limda **XML external entity ineksiya** nima ekanligi, bir qancha xujumlarni qanday amalga oshirish misollarini va uni qanday qilib oldini olish mumkinligini o'rganamiz.

## <mark style="color:yellow;">XML external entity (XXE) ineksiya nima ?</mark> <a href="#xml-external-entity-xxe-inektsiya-nima" id="xml-external-entity-xxe-inektsiya-nima"></a>

XML external entity ineksiya (XXE ham deyiladi) web xavfsizlik zaifligi hisoblanib Hakerga web saytning XML ma'lumotlariga ta'sir o'tkazish imkonini beradi. U ko'pincha hackerga sayt serverining fayl tizimidagi fayllarni ko'rish va saytning o'zi kirishi mumkin bo'lgan har qanday back-end ga ta'sir ko'rsatish yoki tashqi tizimlar bilan o'zaro aloqa qilish imkonini beradi.

Ba'zi holatlarda Haker XXE zaifligidan foydalanib, <mark style="color:yellow;">Server-Side Request Forgery</mark> (SSRF) xujumlarini amalga oshirish orqali asosiy server yoki boshqa backend infratuzilmasini buzish uchun XXE xujumidan foydalanib kuchaytirishi mumkin.

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
<mark style="color:yellow;">Labaratoriyalar:</mark>

Agar siz XXE ga asoslangan zaifliklar haqida bilsangiz pastdagi link orqali, haqiqiy web sayt kabi tuzilgan laboratoriyalarni yechishingiz mumkin.\ <mark style="color:yellow;"></mark>[<mark style="color:yellow;">Barcha XXE labaratoriyalarini ko'rish ≫</mark>](https://portswigger.net/web-security/all-labs#xml-external-entity-xxe-injection)<mark style="color:yellow;"></mark>
{% endhint %}

## <mark style="color:yellow;">XXE xujumlari qayerdan paydo bo'ladi ?</mark> <a href="#xxe-xujumlari-qayerdan-paydo-boladi" id="xxe-xujumlari-qayerdan-paydo-boladi"></a>

Ba'zi web saytlar server bilan o'zaro ma'lumot almashayotganda XML ishlatadi. Buni amalga oshiradigan web saytlarning serverdagi XML ma'lumotlarini qayta ishlashi uchun deyarli har doim standart kutubxona yoki platforma APIsidan foydalanadi. Bunda XXE zaifligi paydo bo'ladi chunki XML spesifikatsiyasida bir qancha har xil xavfli xususiyatlar bor va standard parserlar ularni qo'llab quvvatlaydi hatto u xususiyatlar umuman ishlatilmasa ham.

{% hint style="info" %}
**Ko'proq o'qish:**

[XML formati, DTD va tashqi obyektlar haqida bilib oling ☰](xml-entitylar.md)
{% endhint %}

XXE- belgilangan qiymatlari e'lon qilingan [<mark style="color:yellow;">DTD</mark>](xml-entitylar.md#document-type-definition-nima) tashqarisidan yuklanadigan maxsus XML obyektining bir turi. **External entitiy**lar xavfsizlik nuqtai nazaridan qiziqarli, chunki ular obyektni fayl yo'li yoki URL asosida aniqlash imkonini beradi.

## <mark style="color:yellow;">XXE hujumlarining qanday turlari mavjud ?</mark> <a href="#what-are-the-types-of-xxe-attacks" id="what-are-the-types-of-xxe-attacks"></a>

XXE xujumlarini har xil turlari mavjud:

* <mark style="color:yellow;"></mark>[<mark style="color:yellow;">XXEni fayllarni ko'rish uchun exploit qilish</mark>](xxe-ineksiya.md#xxe-ni-faylni-aks-ettirish-uchun-exploit-qilish) - qayerda external entity da fayl haqida ma'lumot joylashtirilgan bo'lsa, unda server fayl kontentin response da qaytaradi.
* <mark style="color:yellow;"></mark>[<mark style="color:yellow;">SSRF xujumlarini amalga oshirish uchun XXEni exploit qilish</mark>](xxe-ineksiya.md#ssrf-xujumlarini-amalga-oshirish-uchun-xxe-ni-exploit-qilish) - external entity back-end tizimning URL manzili asosida aniqlanadi.
*   <mark style="color:yellow;">Blind XXEni exploit qilib maʼlumotlarni olish uchun out-of-banddan foydalanish</mark>,

    &#x20;bunda maxfiy ma'lumotlar sayt serveridan Haker boshqaradigan tizimga uzatiladi.
* <mark style="color:yellow;">Error xabarlar orqali malumotlarni olish</mark>, Haker bu holatda error xabarlar bilan maxfiy ma'lumotlarga ega chiqishi mumkin.

## <mark style="color:yellow;">Fayllarni olish uchun XXE-dan foydalanish</mark>

XXE ineksiya hujumi orqali serverdan fayllarni olmoqchi bo'lsangiz unda siz yuborilgan XML-ni ikki yo'l bilan o'zgartirishingiz kerak:

* Faylga yo'lni o'z ichiga olgan tashqi obyektni belgilaydigan `DOCTYPE` elementini kiriting (yoki o'zgartiring).
* Belgilangan tashqi ob'ektdan foydalanish uchun web saytning response da qaytariladigan XML ma'lumotlar qiymatini o'zgartiring.

Misol uchun, internet do'kon sayti serverga quyidagi XML-ni yuborish orqali mahsulotning omborda qancha qolganligini tekshiradi deylik:

```
<?xml version="1.0" encoding="UTF-8"?>
<stockCheck><productId>381</productId></stockCheck>
```

Sayt XXE xujumiga qarshi hech qanday himoyalanmagan, ushbu zaiflikdan foydalanib `/etc/passwd`faylini ko'rsatuvchi XXE payload yasab uni serverga yuboring:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<stockCheck><productId>&xxe;</productId></stockCheck>
```

Ushbu XXE payload `&xxe;` qiymati `/etc/passwd` ga teng bo'lgan external entity ni aniqlaydi va uni `productId` da qiymat sifatida ishlatadi. Bu esa response mana bunday kelishini ta'minlaydi:

```
Invalid product ID: root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
...
```

{% hint style="info" %}
**Eslatma:**

Ko'pincha XXE zaifligi bilan yuborilgan XML ichida ma'lumotlaring katta qismi bo'ladi, ulardan istalgan biri web sayt responsida ishlatilishi mumkin. XXE zaifligini tizimli ravishda tekshirish uchun External entity-dan foydalanib va u javob ichida paydo bo'ladimi yoki yo'qligini ko'rib chiqish orqali XML ichidagi har bir element nodeni alohida-alohida tekshirishingiz kerak.
{% endhint %}

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Fayllarni olish uchun XXE-dan foydalanish <mark style="color:yellow;"></mark> ≫](https://portswigger.net/web-security/xxe/lab-exploiting-xxe-to-retrieve-files)
{% endhint %}

## <mark style="color:yellow;">SSRF hujumlarini amalga oshirish uchun XXEdan foydalanish</mark>

XXE zaifliklari faqatgina ma'lumotlarni ko'rsatish bilan cheklanib qolmagan, XXE xujumlarining ta'siri server-side request forgery (SSRF) ni ham keltirib chiqara oladi. Bu potentsial jiddiy zaiflik bo'lib, unda server tomonidagi web sayt server kirishi mumkin bo'lgan har qanday URL manziliga HTTP so'rovlarini yuborishi mumkin.

SSRF xujumlarini amalga oshirishda XXEdan foydalanish uchun, sizning URL payloadingizni o'z ichiga olgan XML entity yasashingiz kerak va o'sha entity ni qiymat sifatida ishlatishingiz zarur. Agar siz saytning responsida qaytariladigan ma'lumotlar qiymating ichida yaratilgan entity-dan foydalana olsangiz, u holda saytning responsida URL manzilidan javobni ko'rishingiz mumkin bo'ladi va shu bilan backend tizimi bilan ikki tomonlama o'zaro tasir o'tkazishga ega bo'lasiz. Aks holda, siz faqat Blind SSRF hujumlarini amalga oshirishingiz mumkin (bu ham jiddiy oqibatlarga olib keladi).

Quyidagi XXE misolida external entity serverni tashkilot infratuzilmasi ichidagi ichki tizimga HTTP so'rovini yuborishiga sabab bo'ladi:

```xml
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://internal.vulnerable-website.com/"> ]>
```

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [SSRF hujumlarini amalga oshirish uchun XXEdan foydalanish ≫](https://portswigger.net/web-security/xxe/lab-exploiting-xxe-to-perform-ssrf)
{% endhint %}

## <mark style="color:yellow;">Blind XXE zaifliklari</mark> <a href="#blind-xxe-zaifliklar" id="blind-xxe-zaifliklar"></a>

Ko'p xolatlarda XXE zaifliklar blind holatda bo'ladi. Bu degani yuqorida keltirgan xujumlarimiz kabi response da fayl kontentini ko'rish, server bilan aloqa o'rnatish imkoni yo'q degani.

Blind XXE zaifliklarni ham exploit qilsa bo'ladi, lekin sizdan ko'proq bilim talab etiladi. Ba'zan zaifliklarni topish va ma'lumotlarni o'chirishda ulardan foydalanish uchun out-of-band texnikasidan foydalanishingiz kerak va siz ba'zan xato xabarlar ichida maxfiy ma'lumotlarni oshkor qilishga olib keladigan XML tahlil xatolarini keltirib chiqarishingiz mumkin.

{% hint style="info" %}
**Batafsil o'qish:**\
[Blind XXE zaifliklarini topish va exploit qilish ☰](https://portswigger.net/web-security/xxe/blind)
{% endhint %}

## <mark style="color:yellow;">XXE ineksiyasi uchun xujum qilish mumkin bo'lgan yashirin joylarni topish</mark>

XXE ineksiya uchun xujum qilinuvchi joylar ko'p holatlarda aniq va ravshan ko'rinib turadi, chunki saytning oddiy HTTP trafigi XML formatidagi ma'lumotlardan tashkil topgan so'rovlarni o'z ichiga oladi. Boshqa hollarda, hujum qilish mumkin bo'lgan qismlarni topish qiyin bo'ladi. Biroq, agar siz to'g'ri joylarni tekshirsangiz XML bo'lmagan so'rovlarda ham XXE hujumini qilish mumkin bo'lgan joylarni topasiz.

## <mark style="color:yellow;">XInclude xujumlari</mark> <a href="#xinclude-xujumlar" id="xinclude-xujumlar"></a>

Ba'zi saytlar client-side bergan ma'lumotlarni oladi, ularni XML hujjatiga server-sidega joylashtiradi va keyin hujjatni tahlil qiladi. Bunga misol, client-side bergan ma'lumotlar SOAP so'roviga joylashtirilganida yuzaga keladi, so'ngra SOAP backend tomonidan qayta ishlanadi.

Bu holatda siz odatiy XXE xujumlarini amalga oshira olmaysiz, chunki endi siz XML hujjatni boshqara olmaysiz va `DOCTYPE` elementiga o'zgartirish kirita olmaysiz. Biroq siz ularning o'rniga `XInclude`ni ishlatishingiz mumkin. `XInclude` ham XML dokumenti ichida dokument yarata oladigan XML dokument spesifikatsiyasining bir qismi. `XInclude` xujumini XML dokumentidagi har qanday ma'lumotlar qiymatiga joylashtirishingiz mumkin, shuning uchun xujum server tomonidagi XML hujjatiga joylashtirilgan faqat bitta ma'lumot elementini boshqaradigan holatlarda amalga oshirilishi mumkin.

XInclude xujumini amalga oshirishingiz uchun XInclude nom maydonidan foydalanishingiz va qo'shmoqchi bo'lgan faylingizga yo'lni ko'rsatishingiz kerak, masalan:

```xml
 <foo xmlns:xi="http://www.w3.org/2001/XInclude">
<xi:include parse="text" href="file:///etc/passwd"/></foo>
```

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Fayllarni olish uchun XIncludeni explot qilish ≫](https://portswigger.net/web-security/xxe/lab-xinclude-attack)
{% endhint %}

## <mark style="color:yellow;">Fayl yuklash orqali XXE xujumi</mark>

Ba'zi web saytlar foydalanuvchilarga server tomonida qayta ishlanadigan fayllarni yuklash imkonini beradi. Ba'zi keng tarqalgan fayl formatlari XML dan foydalanadi yoki XML subkomponentlarini o'z ichiga oladi. Misol tariqasida XML ga asoslangan office fayllari DOCX va SVG rasmlarni keltirish mumkin.

Masalan, bir web sayt foydalanuvchilarga rasm yuklashga ruxsat beradi va ular yuklanganidan so'ng serverda qayta ishlov beradi yoki tasdiqlaydi. Agar web sayt PNG va JPEG formatlarini qo'llab quvvatlasa unda rasmni tekshiruvchi kutubxona SVG formatini ham qo'llab quvvatlashi mumkin. SVG esa XML ishlatadi, Haker shundan foydalanib SVG ga o'zining zararli payloadini yozib ko'rinmas XXE xujumini amalga oshira oladi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Rasm yuklash orqali XXEni exploit qilish ≫](https://portswigger.net/web-security/xxe/lab-xxe-via-file-upload)
{% endhint %}

## <mark style="color:yellow;">Content-Type ni o'zgartirish orqali  XXE xujumi</mark> <a href="#kontent-turini-ozgartirish-ila-xxe-xujumlar" id="kontent-turini-ozgartirish-ila-xxe-xujumlar"></a>

Ko'p POST requestlar `application/x-www-form-urlencoded` kabi HTML formalari tomonidan yaratilgan standart kontent turidan foydalaadi. Ba'zi web saytlar esa boshqacha kontent turlarini ham qabul qiladi, masalan XML ni.

Masalan, agar normal request mana bunday bo'lsa:

```
POST /action HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 7

foo=bar
```

Balki siz ushbu requestdan so'ng XML kiritib jo'natishingiz mumkindir:

```
POST /action HTTP/1.0
Content-Type: text/xml
Content-Length: 52

<?xml version="1.0" encoding="UTF-8"?><foo>bar</foo>
```

Agar web sayt request larda XML ni o'z ichiga olgan so'rovlarni qabul qilsa va asosiy tarkibni XML sifatida tahlil qilsa, siz XML formatidan foydalanish uchun so'rovlarni qayta formatlash orqali yashirin XXE xujumini qilishingiz mumkin.

## <mark style="color:yellow;">Qanday qilib XXE zaifliklarini topish va ularni test qilish mumkin ?</mark> <a href="#qanday-qilib-xxe-zaifliklarini-topish-va-ularni-test-qilish-mumkin" id="qanday-qilib-xxe-zaifliklarini-topish-va-ularni-test-qilish-mumkin"></a>

XXE zaifliklarining katta qismi Burp Suite web-zaiflik skaneri yordamida tez va ishonchli tarzda topilishi mumkin.

Qo'lda test qilish esa asosan quyidagilarni o'z ichiga oladi:

* Operatision tizim fayli asosida external entityni aniqlash orqali va sayt javobida qaytarilgan malumot ichidagi entitydan foydalanib [<mark style="color:yellow;">fayllarni olish</mark>](xxe-ineksiya.md#fayllarni-olish-uchun-xxe-dan-foydalanish) uchun tekshirish&#x20;
* Siz boshqarayotgan tizim URLi asosida external entityni aniqlash orqali <mark style="color:yellow;">blind XXE zaifliklarini</mark> tekshirish va ushbu tizim bilan oʻzaro aloqalarni kuzatish. Bunda [<mark style="color:yellow;">Burp Collaborator</mark>](https://hackthebrain.gitbook.io/burpsuite-uchun-qollanma/burp-toollari-haqida-organish-tez-kunda/collabrator-client-pro) eng yaxshi tanlovdir.
* <mark style="color:yellow;"></mark>[<mark style="color:yellow;">XInclude hujumidan</mark>](xxe-ineksiya.md#xinclude-xujumlar) foydalanib operatsion tizim fayllarini kor'ish uchun, foydalanuvchi tomonidan xml bo'lmagan hujjat faylini serverdagi xml hujjat faylining ichiga kiritish zaifligini tekshirish

{% hint style="info" %}
**Eslatma:**

XML ma'lumotlarni uzatish formati ekanligini unutmang. [<mark style="color:yellow;">XSS</mark>](broken-reference) va [<mark style="color:yellow;">SQL ineksiya</mark>](broken-reference) kabi boshqa zaifliklar uchun XML-ga asoslangan har qanday funksionallikni tekshirganingizga amin bo'ling. Sintaksisni buzmaslik uchun payloadingizni XMLni aylanib o'tish ketma-ketligidan foydalanib kodlashingiz kerak bo'lishi mumkin, ammo zaif himoyani chetlab o'tish uchun <mark style="color:yellow;">hujumingizni obfuskatsiya qilish</mark>dan foydalanishingiz mumkin.&#x20;
{% endhint %}

## <mark style="color:yellow;">Qanday qilib XXE zaifliklarni oldini olish mumkin ?</mark> <a href="#qanday-qilib-xxe-zaifliklarni-oldini-olish-mumkin" id="qanday-qilib-xxe-zaifliklarni-oldini-olish-mumkin"></a>

Deyarli barcha XXE zaifliklari web-saytning XML parsing kutubxonasi web saytga kerak bo'lmagan yoki foydalanilmaydigan xavfli XML xususiyatlarini qo'llab-quvvatlaganligi sababli yuzaga keladi. XXE xujumlarini oldini olishning eng oson va samarali usuli, bu xususiyatlarni o'chirishdir.

Umuman olganda, external entitylarga ruxsatni o'chirib qo'yish va `XInclude` uchun qo'llab-quvvatlashni o'chirish kifoya. Bu odatda konfiguratsiya opsiyalari yoki standart amallarni dasturiy jihatdan bekor qilish orqali amalga oshirilishi mumkin. Keraksiz imkoniyatlarni qanday o'chirish haqida batafsil ma'lumot olish uchun XML parsing kutubxonangiz yoki API uchun ma'lumotnomalarga murojaat qiling.

{% hint style="info" %}
**Ko'proq o'qish:**

[Burp Suite zaiflik skaneri yordamida XXE zaifliklarini topish ☰](https://portswigger.net/burp/vulnerability-scanner)
{% endhint %}
