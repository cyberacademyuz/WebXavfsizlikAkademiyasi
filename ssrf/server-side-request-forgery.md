# Server-side request forgery

Bu bo'limda SSRF nimligini va undagi xujumlar qanday amalga oshirilishi haqida gaplashamiz.

## <mark style="color:yellow;">SSRF nima ?</mark> <a href="#ssrf-nima" id="ssrf-nima"></a>

Server-side request forgery (SSRF ham deyiladi)  - haker, serverdagi web saytni mo'ljallanmagan joyga requestlar yuborishga undash imkonini beruvchi web-xavfsizlik zaifligidir.

Odatiy SSRF xujumida haker serverni kompaniya infratuzilmasi ichidagi ichki xizmatlarga ulanishiga olib kelishi mumkin. Boshqa hollarda, ular serverni o'zboshimchalik bilan tashqi serverlarga ulanishga majburlashi mumkin, bu esa avtorizatsiya ma'lumotlari kabi maxfiy ma'lumotlar aniqlanishiga olib kelishi mumkin.

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
<mark style="color:yellow;">**Labaratoriyalar:**</mark>

Agar siz SSRF zaifliklari haqida bilsangiz pastdagi link orqali, haqiqiy web sayt kabi tuzilgan laboratoriyalarni yechishingiz mumkin.[<mark style="color:yellow;"></mark>\ <mark style="color:yellow;">Barcha SSRF labaratoriyalarini ko'rish ≫</mark>](https://portswigger.net/web-security/all-labs#server-side-request-forgery-ssrf)<mark style="color:yellow;"></mark>
{% endhint %}

## <mark style="color:yellow;">SSRF xujumlarning ta'siri qanday ?</mark> <a href="#ssrf-xujumlarning-tasiri-qanday" id="ssrf-xujumlarning-tasiri-qanday"></a>

Muvaffaqiyatli SSRF xujumi maxfiy ma'lumotlarga kirish, ular bilan ishlashga ruxsat berishi mumkin yoki backend tizimlar bilan aloqa o'rnatishga olib keladi. Ba'zi hollarda SSRF hakerga avtomatik buyruq bajarish imkonini beradi.

Tashqi uchinchi tomon tizimlariga ulanishga olib keladigan SSRF exploiti, zaif web sayt orqali u tomonidan bajariladigan zararli xujumlarga olib kelishi mumkin.

## <mark style="color:yellow;">Keng tarqalgan SSRF xujumlari</mark> <a href="#keng-tarqalgan-ssrf-xujumlari" id="keng-tarqalgan-ssrf-xujumlari"></a>

Ko'pincha SSRF xujumlar ruxsat etilmagan harakatlarni bajarishga imkon beradi. Ushbu imkoniyat orqali boshqa serverlarga ham ta'sir o'tkazish mumkin.

## <mark style="color:yellow;">Serverning o'ziga qarshi SSRF xujum</mark> <a href="#serverni-oziga-ozi-qarshi-sodir-etilishi-mumkin-bolgan-ssrf-xujumlar" id="serverni-oziga-ozi-qarshi-sodir-etilishi-mumkin-bolgan-ssrf-xujumlar"></a>

Serverning o'ziga qarshi SSRF xujumida, haker web saytni o'zining tarmoq interfeysi orqali sayt joylashtirilgan hosting serveriga HTTP so'rov yuborishga undaydi. Bu odatda `127.0.0.1` (loopback adapterini bildiruvchi oldindan belgilangan IP manzil) yoki localhost (aynan shu adapter uchun tez-tez ishlatiladigan nom) kabi xost nomi bilan URL manzilini taqdim qilishni o'z ichiga oladi.

Misol uchun, foydalanuvchiga mahsulot ma'lum bir do'konda mavjud yoki yo'qligini ko'rish imkonini beruvchi xarid qilish web saytini ko'rib chiqing. Qolgan mahsulot ma'lumotlarini taqdim etish uchun web sayt mahsulotga va savolga qarab, turli xil backend REST API-larga murojaat qilishi kerak. Funktsiya URL manzilni HTTP so'rovi orqali  tegishli APIning endpointiga o'tkazish yordamida amalga oshiriladi. Shunday qilib, foydalanuvchi mahsulotga tegishli ma'lumotlarni ko'rganida, uning brauzeri quyidagi so'rovni yuboradi:

```http
POST /product/stock HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 118

stockApi=http://stock.weliketoshop.net:8080/product/stock/check%3FproductId%3D6%26storeId%3D1
```

Bu serverni URLga so'rov yuborishiga va mahsulot borligi yoki yo'qligini tekshirib uni foydalanuvchiga ko'rsatishiga sabab bo'ladi.

Bu holatda haker uddaburonlik bilan URL ni localhost ga o'zgartirib requestni serverni o'ziga yuborishi mumkin:

```http
POST /product/stock HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 118

stockApi=http://localhost/admin
```

Bu yerda endi server localhost dagi `/admin` sahifasini oladi va foydalanuvchiga jo'natadi.

Endi hacker `/admin` sahifasiga shunchaki kira oladi. Ammo odatda haker sahifaga faqatgina kira oladi xolos, admin funksiyanalligi esa ishlamaydi, chunki faqatgina ro'yhatdan o'tgan foydalanuvchilargagina bunga ruxsat bor. Biroq, `/admin` URL manziliga so'rov lokal kompyuterning o'zidan kelganda, <mark style="color:yellow;">kirish boshqaruvlari</mark> chetlab o'tiladi. Websayt administratorlik funksiyalarga toʻliq kirish huquqini beradi, chunki soʻrov ishonchli yerdan kelayotgandek bo'ladi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Serverning o'ziga qarshi SSRF xujum ≫](https://portswigger.net/web-security/ssrf/lab-basic-ssrf-against-localhost)
{% endhint %}

Nega ko'plab saytlar shunday ishlaydi, ya'ni agarda local tizimdan request kelsa, request larga ishonadi ? Bunga har xil sabablar bor:

* Access control tekshiruvi web sayt serverining oldida joylashgan boshqa tarkibiy qismda amalga oshirilishi mumkin. Ulanish serverning o'ziga qaytarilsa, tekshiruv aylanib o'tiladi.
* Favqulodda vaziyatlar yuzaga kelganda tizimni tuzatish maqsadida sayt boshqa local  kompyuterdan kelgan har qanday foydalanuvchiga tizimga kirmasdan admin huquqini olishga ruxsat berishi mumkin. Bu administrator login parol ma'lumotlarini yo'qotganida tizimni tiklash imkonini beradi. Bunda faqat ishonchli bo'lgan foydalanuvchi to'g'ridan-to'g'ri server orqali kira oladi, deb taxmin qilinadi.
* Admin interfeysi asosiy web saytga nisbatan boshqa port raqamini tinglayotgan bo'lishi mumkin va shuning uchun foydalanuvchilar to'g'ridan-to'g'ri foydalana olmasligi mumkin.

Bu tizim ishonadigan so'rovlar tasiri hisoblanadi, local kompyuterdan keladigan so'rovlar oddiy so'rovlardan farqli ravishda ko'rib chiqiladi, bu ko'pincha SSRFni muhim zaiflikka aylantiradi.

## <mark style="color:yellow;">Boshqa backend tizimlarga SSRF xujumlar uyushtirish</mark> <a href="#boshqa-backend-tizimlarga-ssrf-xujumlar-uyushtirish" id="boshqa-backend-tizimlarga-ssrf-xujumlar-uyushtirish"></a>

Yana bir tizim ishonadigan so'rovlar tasiri zaifliklardan biri SSRF, bu yerda saytning serverdagi foydalanuvchilari backend tizimlar bilan o'zaro aloqa qilishi mumkin. Ushbu tizimlar ko'pincha qayta o'rnatiladigan shaxsiy IP manzillarga ega. Backend tizimlar odatda [<mark style="color:yellow;">tarmoq topologiyasi</mark> ](https://community.uzbekcoders.uz/post/tarmoq-topologiyalari-6020bdb28b6f6400da16113c)bilan himoyalanganligi sababli ular ko'pincha zaif xavfsizlik holatiga ega. Ko'pgina hollarda, ichki backend tizimlar, tizimlar bilan o'zaro aloqada bo'lgan har bir kishi tomonidan autentifikatsiya qilinmasdan kirishi mumkin bo'lgan nozik funksionallikni o'z ichiga oladi.

Ilgarigi misol kabi,  quyidagi  [https://192.168.0.68/admin](https://192.168.0.68/admin) backend URLida admin sahifasi mavjud deb tahmin qilaylik. Bu yerda haker quyida keltirilgan so'rovni jo'natish orqali SSRF ni exploit qilishi mumkin:

```http
POST /product/stock HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 118

stockApi=http://192.168.0.68/admin
```

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Boshqa backend tizimlarga SSRF xujumlar uyushtirish ≫](https://portswigger.net/web-security/ssrf/lab-basic-ssrf-against-backend-system)
{% endhint %}

## <mark style="color:yellow;">Umumiy SSRF himoyasini chetlab o'tish</mark> <a href="#umumiy-ssrf-himoyasini-chetlab-otish" id="umumiy-ssrf-himoyasini-chetlab-otish"></a>

Ko'p web saytlar SSRF ga qarshi himoyalarni amalga oshiradi. Ushbu himoyalarni chetlab o'tish mumkin.

### <mark style="color:yellow;">Blacklist asosida input filterlari mavjud tizimga SSRF hujumi</mark> <a href="#ssrf-qora-royhatdagi-input-filterlari" id="ssrf-qora-royhatdagi-input-filterlari"></a>

Ba'zi web saytlar 127.0.0.1 va localhost dan keluvchi request larni bloklaydi yoki `/admin` mavjud bo'lgan URL larni. Ushbu vaziyatlarda siz ko'pincha quyida keltirgan usullarimiz orqali ularni aylanib o'tishingiz mumkin:

* 127.0.0.1 ga alternativ IP manzilidan foydalanish, masalan `2130706433`, `017700000001`, yoki `127.1`.
* O'zingizni domeningizni `127.0.0.1` ni qaytaruvchi holatda ro'yhatga olish. Buning uchun siz `spoofed.burpcollaborator.net` dan foydalanishingiz mumkin.
* URL kodlash yoki katta-kichik registrlar yordamida bloklangan stringlarni [obfuskatsiya](https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrI3bnq4ApxRcW4MU3hFQ%2Fuploads%2F4rQ3tOEqAbGqvi3GlJJv%2Fimage.png?alt=media\&token=01d2ca8b-c3bb-4045-9af8-ea2cfa0b5bea) qilish.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Blacklist asosida input filterlari mavjud tizimga SSRF hujumi ≫](https://portswigger.net/web-security/ssrf/lab-ssrf-with-blacklist-filter)
{% endhint %}

### <mark style="color:yellow;">Whitelist asosida input filterlari mavjud tizimga SSRF hujumi</mark> <a href="#ssrf-qora-royhatdagi-input-filterlari" id="ssrf-qora-royhatdagi-input-filterlari"></a>

Ba'zi web saytlar, ularga kelgan requestlar whitelistdagi domen nomi bilan boshlansa yoki whistlestdagi domen bilan bir xil bo'lsa ularni qabul qiladi. Ushbu xolatda siz URL-parsingdagi nomuvofiqliklardan foydalanish orqali filtrni aldashingiz mumkin.

URL spetsifikatsiyasi URL manzillarini parsing qilish va tekshirayotganida e'tibordan chetda qolishi mumkin bo'lgan bir qator xususiyatlarni o'z ichiga oladi:

* Siz @ belgisidan foydalanib, hisob maʼlumotlarini URL manzilga hostnamedan (xhost nomi) oldin joylashtirishingiz mumkin. Masalan:

```url
https://expected-host@evil-host
```

* ''#'' belgisini URL fragmentini ko'rsatishda foydalanishingiz mumkin. Masalan:

```url
https://evil-host#expected-host
```

* Siz o'zingiz nazorat qiladigan [<mark style="color:yellow;">FQDN</mark>](https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F6OQTXTKi2jwkYoRLierX%2Fuploads%2FV8nc8w5DsKczqL8w4g3s%2FNomsiz%20dizayn%20nusxasi%20nusxasi.png?alt=media\&token=e156b79d-821e-4a0d-aaa5-2b2334fa3146)-ga  kerakli ma'lumotlarni joylashtirish uchun [DNS nomlash ierarxiyasidan](https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrI3bnq4ApxRcW4MU3hFQ%2Fuploads%2F4hR2SVqSXaKCCszf2iXk%2Fimage.png?alt=media\&token=5d04dcd1-6eae-42ce-b106-6115cc6a5f77) foydalanishingiz mumkin. Misol uchun:

```url
https://expected-host.evil-host
```

* URL parsing kodini chalkashtirib yuborish uchun URL-kodlash belgilaridan foydalanishingiz mumkin.
* Bu ayniqsa, filtrlash vazifasini bajradigan kod, encode qilingan URL belgilarni HTTP so'rovini yuboradigan koddan farqli ravishda ishlatsa samarali bo'ladi.
* Siz bu texnikalarning kombinatsiyalarini birga ishlata olasiz,.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Whitelist asosida input filterlari mavjud tizimga SSRF hujumi ≫](https://portswigger.net/web-security/ssrf/lab-ssrf-with-whitelist-filter)
{% endhint %}

{% hint style="info" %}
**Ko'proq o'qish:**

[SSRFning yangi davri ☰](https://portswigger.net/blog/top-10-web-hacking-techniques-of-2017#1)
{% endhint %}

### <mark style="color:yellow;">SSRF filterlarini open redirection orqali aylanib o'tish</mark> <a href="#ssrf-filterlarini-open-redirection-ila-aylanib-otish" id="ssrf-filterlarini-open-redirection-ila-aylanib-otish"></a>

Ba'zida har qanday filterni open redirection orqali aylanib o'tsa bo'ladi.

Oldingi SSRF xujumlarda keltirilgan misollarga qarab tasavvur qiling, foydalanuvchi uchun faqatgina qat'iy tekshiruvdan o'tuvchi URL larga ruxsat beradi va bu SSRF xujumlarini oldini olish uchun ishlatiladi. Ammo web saytda open redirection zaifligi mavjud. Siz ushbu vaziyatdan kelib chiqib filter qabul qiluvchi URL ni yaratib redirect qilish uchun request jo'natishingiz mumkin.

Misol uchun quyidagi web saytda open redirection zaifligi mavjud:

```url
/product/nextProduct?currentProductId=6&path=http://evil-user.net
```

va u [http://evil-user.net](http://evil-user.net) ga redirect qiladi.

Siz ushbu holatda SSRF xujum uchun open redirection dan foydalanib SSRF uchun yasalgan filterni aylanib o'tishingiz mumkin, masalan:

```http
POST /product/stock HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 118

stockApi=http://weliketoshop.net/product/nextProduct?currentProductId=6&path=http://192.168.0.68/admin
```

Ushbu SSRF exploiti ishlaydi, chunki filter birinchi bo'lib stockApi dagi URL ni tekshiradi va u ruxsat betilgan domen. Keyin web sayt open redirectionni ishga tushiradigan URL-manzilni so'raydi. U redirectionga o'tadi va haker kiritgan ichki URL manzilga soʻrov yuboradi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [SSRF filterlarini open redirection orqali aylanib o'tish ≫](https://portswigger.net/web-security/ssrf/lab-ssrf-filter-bypass-via-open-redirection)
{% endhint %}

## <mark style="color:yellow;">Blind SSRF zaifliklar</mark> <a href="#blind-ssrf-zaifliklar" id="blind-ssrf-zaifliklar"></a>

Blind SSRF zaifligi, web saytning backend qismi HTTP so'rovlarni URL ga jo'natib, javobni saytning frontend qismiga qaytarmaganda ishlaydi.

Blind SSRF ni exploit qilish qiyin ammo ba'zida u butunlayga masofadan turib serverga yoki boshqa backend komponentlarga buyruq berish imkonini beradi.

{% hint style="info" %}
**Ko'proq o'qish:**

[Blind SSRF zaifliklarini topish va foydalanish ☰](blind-ssrf.md)
{% endhint %}

## <mark style="color:yellow;">SSRF zaifligi uchun hujum qilish mumkin bo'lgan yashirin joylarni topish</mark> <a href="#blind-ssrf-xujumlarni-yuzasini-topish" id="blind-ssrf-xujumlarni-yuzasini-topish"></a>

SSRFning ko'plab zaifliklarini topish juda oson, chunki Veb-saytning trafigi to'liq URL manzillarini o'z ichiga olgan so'rov parametrlarini o'z ichiga oladi.&#x20;

### <mark style="color:yellow;">So'rovlardagi qisman URL manzillar</mark> <a href="#sorovlardagi-qisman-url-manzillar" id="sorovlardagi-qisman-url-manzillar"></a>

Ba'zida web sayt so'rov parametrlariga faqat hostnameni yoki URLning bir qismini joylashtiradi. Berilgan qiymat request yuborilgan URL manziliga server tomonida kiritiladi. Agar qiymat xost nomi yoki URL yo'li sifatida osongina aniqlansa, xujum qilish mumkin bo'lgan joy topilishi mumkin. Biroq, to'liq SSRF sifatida foydalanish imkoniyati cheklangan bo'lishi mumkin, chunki siz request yuboriladigan butun URLni nazorat qilmaysiz.

### <mark style="color:yellow;">Ma'lumot formatlari ichidagi URL manzillar</mark> <a href="#malumotlar-formatlari-ichidagi-url-manzillari" id="malumotlar-formatlari-ichidagi-url-manzillari"></a>

Ba'zi veb-saytlar ma'lumotlarni spetsifikatsiyasi format uchun tahlil qilish imkoniyati  mumkin bo'lgan URL-larni kiritishga imkon beradigan formatlarda uzatadi. Buning yaqqol misoli XML ma'lumotlar formati bo'lib, u browserdan serverga, **tuzilgan ma'lumotlarni uzatish** uchun web-saytlar keng qo'llaniladi. Web-sayt XML formatidagi ma'lumotlarni qabul qilganda va uni tahlil qilganda, u [<mark style="color:yellow;">XXE ineksiyasiga</mark>](broken-reference) zaif bo'lishi mumkin va o'z navbatida XXE orqali SSRF zaifligi bo'lshi mumkin. [<mark style="color:yellow;">XXE ineksiya zaifliklari</mark>](broken-reference)ni ko'rib chiqsangiz, buni yaxshiroq tushunasiz.

### <mark style="color:yellow;">Referrer header orqali SSRF</mark> <a href="#ssrf-referrer-header-bilan" id="ssrf-referrer-header-bilan"></a>

Ba'zi web saytlar saytga kirganlarni kuzatib boradigan server-side analitik dasturlardan foydalanadi. Bu dasturlar esa ko'pincha log larni **referrer header**ga yozib boradi. Bu esa kirilayotgan linklarni kuzatish uchun qiziqish uyg'otadi. Analitik dastur ko'pincha **Referer header**dagi istalgan 3-tomon URL manzilga kiradi. Bu, odatda, kirilgan saytlar tarkibini tahlil qilish uchun amalga oshiriladi. Natijada, Referer headeri ko'pincha SSRF zaifliklari uchun xujum qilish imkonini yaratadi. Referer headeri bilan bog'liq zaifliklarga misollar uchun [<mark style="color:yellow;">Blind SSRF zaifliklariga</mark>](blind-ssrf.md) o'ting.

