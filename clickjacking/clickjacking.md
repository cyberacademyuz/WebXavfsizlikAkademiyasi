# Clickjacking

Bugungi bo'limda biz Clickjacking nimaligini o'rganamiz va ushbu xujumlarni qanday qilib oldini olishni ko'rib chiqamiz.

## <mark style="color:yellow;">Clickjacking nima ?</mark> <a href="#clickjacking-nima" id="clickjacking-nima"></a>

**Clickjacking** bu **UI** ga asoslangan xujum hisoblanadi, ya'ni foydalanuvchi saytga kiradi, u yerdan biror tugmani bosadi, aslida u tugmaga hech qanday havola biriktirilmagan bo'ladi u va o'zi ko'rgan tugmani emas uning orqasida ko'rinmas qilib qo'yilgan boshqa saytning tugmasini bosgan bo'ladi. masalan:

Clickjacking xujumi uchun tayyorlangan sayt bor deylik, unda Yutdingiz degan tugma mavjud, u tugma ortida esa pul o'tkazmasini bajarish berilgan boshqa saytning tugmasi joylashtirilgan bo'ladi. Agarda foydalanuvchi Yutdingiz tugmasiga bossa pul o'tkazmasi o'tib ketadi. Mana shu **Clickjacking** xujumga misol bo'la oladi. Ushbu xujumlar iframe asnosida bajariladi. Iframe sohta sayt ustida qoplangan bo'ladi. Ushbu xujum **CSRF** xujumdan farq qilishi shundaki ushbu xujumda foydalanuvchi tugma ustiga bosishi kerak, **CSRF** da esa barchasi avtomatlashtirilgan holda sodir bo'ladi.

<figure><img src="../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

**CSRF** xujumlaridan odatda **CSRF tokenlar** bilan himoyalanilaniladi. Clickjacking dan himoyalanish uchun **CSRF tokenlar** ish bermaydi sababi Clickjackingda biz ro'yhatdan o'tilgan saytning UI qismini joylashtiramiz va u yerda **CSRF tokenlar** mavjud bo'ladi va barchasi bitta domenda amalga oshiriladi. **CSRF tokenlar** so'rovlarda joylashadi va serverga jo'natiladi. **Clickjacking** xujumida biz ko'rinmas iframe asosida xujumlarni amalga oshiramiz shu sababli **CSRF tokenlar** himoyalanish uchun yaroqsiz.

{% hint style="warning" %}
<mark style="color:yellow;">**Labaratoriyalar:**</mark>&#x20;

Agar Clickjacking zaifliklar**i** haqida bilsangiz pastdagi link orqali, haqiqiy web saytdek tuzilgan laboratoriyalarni yechishingiz mumkin.&#x20;

[<mark style="color:yellow;">Clickjacking labaratoriyalarini ko'rish≫</mark>](https://portswigger.net/web-security/all-labs#clickjacking)<mark style="color:yellow;"></mark>
{% endhint %}

## <mark style="color:yellow;">Qanday qilib sodda Clickjacking xujumini amalga oshirish mumkin ?</mark> <a href="#qanday-qilib-sodda-clickjacking-xujumini-amalga-oshirish-mumkin" id="qanday-qilib-sodda-clickjacking-xujumini-amalga-oshirish-mumkin"></a>

Clickjacking xujumi ko'pincha CSS orqali tuzilgan web sahifalar hisoblanadi. Haker haqiqiy saytni sohta sayt tagiga joylashtiradi va sohta sayt orqali o'z xujumini amalga oshiradi. Teglarni va parametrlarni qanday to'g'ri ishlatishni quyida keltiramiz:

```html
<head>
    <style>
        #target_website {
            position:relative;
            width:128px;
            height:128px;
            opacity:0.00001;
            z-index:2;
            }
        #decoy_website {
            position:absolute;
            width:300px;
            height:400px;
            z-index:1;
            }
    </style>
</head>
...
<body>
    <div id="decoy_website">
    ...sohta web sayt kontenti...
    </div>
    <iframe id="target_website" src="https://vulnerable-website.com">
    </iframe>
</body>
```

Bu yerda aldoqchi web sayt bilan haqiqiy web saytning iframe lari shunday joylashtirilganki, balandlik, uzunlik qismlari orqali foydalanuvchini bemalol aldash mumkin. Bu yerda berilgan qiymatlar ko'pincha ekran uzunligi, balandliklariga ko'ra o'zgarishi mumkin. z-index esa elementning nimaning ustida turishini qiymatini beradi (odatda z-index 0.0 holatida bo'ladi). Iframe ni shaffof holatda aniqlash amalga oshishi mumkin (misol uchun Chrome 76 da ushbu imkoniyat mavjud, Firefox da esa yo'q).

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [CSRF token himoyasiga bilan sodda Clickjacking ≫](https://portswigger.net/web-security/clickjacking/lab-basic-csrf-protected)
{% endhint %}

## <mark style="color:yellow;">Clickbandit</mark> <a href="#clickjacking-ni-oldindan-toldirilgan-input-qiymati-bilan-amalga-oshirish" id="clickjacking-ni-oldindan-toldirilgan-input-qiymati-bilan-amalga-oshirish"></a>

Yuqorida aytib o'tilganidek, siz o'zingiz muustaqil tarzda kod yozib orqali yaratishingiz mumkin bo'lsa-da, bu amalda juda zerikarli va ko'p vaqt talab qilishi mumkin. Clickjacking hujumini amalga oshirayotganingizda, Burpning [<mark style="color:yellow;">Clickbandit</mark>](https://hackthebrain.gitbook.io/burpsuite-uchun-qollanma/burp-toollari-haqida-organish-tez-kunda/clickbandit) vositasidan foydalanishni tavsiya etamiz. Bu sizga frameli sahifada kerakli amallarni bajarish uchun brauzeringizdan foydalanishga imkon beradi, so'ngra to'g'ri keladigan clickjackingni o'z ichiga olgan HTML faylini yaratadi. Uning yordamida bir qator ham HTML yoki CSS kodini yozmasdan, bir necha soniya ichida kodlarni yaratishingiz mumkin.

## <mark style="color:yellow;">Clickjackingni oldindan kiritilgan input qiymati bilan amalga oshirish</mark> <a href="#clickjacking-ni-oldindan-toldirilgan-input-qiymati-bilan-amalga-oshirish" id="clickjacking-ni-oldindan-toldirilgan-input-qiymati-bilan-amalga-oshirish"></a>

Ba'zi web saytlarda input qiymatlari GET so'rovini yuborish orqali to'ldiriladi, bu esa inputlarni oldindan to'ldirish imkonini berishi mumkin. Shunday holatda Clickjacking orqali URL ga oldindan to'ldirilishi kerak bo'lgan qiymatni beramiz va UI yasaymiz va Clickjacking xujumini amalga oshiramiz.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [URL parametrdan avvaldan kiritilgan input maʼlumotlari bilan Clickjacking ≫](https://portswigger.net/web-security/clickjacking/lab-prefilled-form-input)
{% endhint %}

## <mark style="color:yellow;">Framedan himoyalash skriptlari</mark> <a href="#frame-ni-sindirish-skriptlari" id="frame-ni-sindirish-skriptlari"></a>

**Clickjacking** xujumlari qachonki web saytni framega olish imkoni bo'lsagina ishlaydi. Shuning uchun ba'zi web saytlarda **Frame**ga olish imkoni mavjud emas. Shuning uchun ko'pincha himoyalanish maqsadida **Framedan buster** skriptlaridan foydalaniladi. Bular ko'pincha brauzerga o'rnatilgan **NoScript** extension lar orqali amalga oshiriladi. Ko'pincha skriptlar quyida keltirilgan ishlarni hammasini yoki bir qanchasini amalga oshira oladi:

* Sayt barcha narsa ustidami yoki ustimasligini tekshiradi
* Barcha frame larni ko'rinadigan qiladi
* Ko'rinib turgan frame dagi tugmani bosishni oldini oladi
* Clickjacking xujumi borligi haqida ogohlantiradi

Frame buster sikritplari ko'pincha brauzer va platformaga xos bo'lib, HTMLning moslashuvchanligi tufayli ularni hakerlar chetlab o'tishlari mumkin. Frame buster lar ba'zida Brauzerning JavaScript sozlamalari orqali amalga oshirilishi mumkin yoki Brauzer JavaScript ni qo'llab quvvatlamasligi mumkin. Samarali aylanib o'tish usuli esa bu **HTML5** dagi iframe tegidagi`sandbox` attributini ishlatib amalga oshiriladi. Agarda ushbu attributga `allow-forms`, `allow-scripts` yoki `allow-top-navigation` qiymatlari berilgan bo'lsa frame buster sikritplari yuqorida keltirgan qila olishi mumkin bo'lgan ishlarni amalga oshira olmaydi va saytni tepada yoki tepada emasligini tekshirolmaydi:

```html
<iframe id="victim_website" src="https://victim-website.com" sandbox="allow-forms"></iframe>
```

`allow-forms` va `allow-scripts` ikkovi ham xujumni amalga oshirishga ruxsat beradi, ammo `allow-top-navigation` ruxsat bermasligi mumkin. Bu attributlarni qo'yish frame buster skriptlarini ishlashini sekinlashtirishi va shu vaqt davomida saytda Clickjacking xujumini amalga oshirishga imkon beradi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Frame buster skriptlariga clickjacking ≫](https://portswigger.net/web-security/clickjacking/lab-frame-buster-script)
{% endhint %}

## <mark style="color:yellow;">Clickjackingni DOM XSS xujumi bilan birlashtirish</mark> <a href="#dom-xss-xujumi-bilan-clickjacking-xujumi-bilan-birlashtirish" id="dom-xss-xujumi-bilan-clickjacking-xujumi-bilan-birlashtirish"></a>

Biz oddiy **Clickjacking** ni ko'rib chiqdik. Tarixda Facebookda "Like" larni tezroq yig'ish maqsadida **Clickjacking** ishlatilgan. Biroq, **Clickjacking**ni haqiqiy kuchi **DOM XSS** kabi boshqa xujum uchun tashuvchi sifatida foydalanilganda namoyon bo'ladi. Buning uchun Haker birinchi navbatda **XSS** topishi kerak. **XSS** zailigi bo'lgan joy frame ga olinadi va foydalanuvchiga jo'natiladi, u linkni ochsa **DOM XSS** xujumi amalga oshadi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [DOM-ga asoslangan XSS-ni ishga tushirish uchun clickjacking zaifligidan foydalanish ≫](https://portswigger.net/web-security/clickjacking/lab-exploiting-to-trigger-dom-based-xss)
{% endhint %}

## <mark style="color:yellow;">Ko'p bosqichli Clickjacking</mark> <a href="#kop-bosqichli-clickjacking" id="kop-bosqichli-clickjacking"></a>

Haker saytga kiritilgan ma'lumotlarni manipulyatsiya qilishi uchun bir nechta harakatlarni talab qilishi mumkin. Masalan, Haker foydalanuvchini aldab, savdo saytidan biror narsa sotib oldirishi mumkin, shuning uchun buyurtma berishdan oldin tovarlar savatga qo'shilishi kerak. Bu xarakatlarni amalga oshirish uchun bizga ko'proq iframe kerak bo'ladi. Agar ular samarali va yashirin bo'lishi kerak bo'lsa Haker nuqtai nazaridan bunday hujumlar katta aniqlik va ehtiyotkorlik bilan bajarish talab qilinadi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Ko'p bosqichli Clickjacking ≫](https://portswigger.net/web-security/clickjacking/lab-multistep)
{% endhint %}

## <mark style="color:yellow;">Qanday qilib Clickjacking xujumlarni oldini olish mumkin ?</mark> <a href="#qanday-qilib-clickjacking-xujumlarni-oldini-olish-mumkin" id="qanday-qilib-clickjacking-xujumlarni-oldini-olish-mumkin"></a>

Biz yuqorida Browser orqali bajariladigan himoya haqida gaplashdik, misol uchun Frame buster skirptlari. Biroq bildikki Haker bularni aylanib o'tishi mumkin ekan. Shu sababli serverlarda iframega bir qancha cheklov qo'llash uchun kerakli protokollar ishlab chiqilgan. Clickjacking - bu brauzer tomonidagi xatti-harakatlar va uning amalga oshishiga yoki boshqa yo'l bilan brauzerning funksionalligi va amaldagi veb-standartlarga muvofiqligiga bog'liq. Server orqali Clickjackingdan himoyalanish, iframelar kabi komponentlardan foydalanish boʻyicha cheklovlarni aniqlash va uzatish orqali taʼminlanadi. Biroq, himoyani amalga oshirish brauzerning muvofiqligi va ushbu cheklovlarning bajarilishiga bog'liq. Server orqali Clickjackingdan himoya qilishning ikkita mexanizmi `X-Frame-Options` va [<mark style="color:yellow;">Content Security Policy</mark>](../xss/content-security-policy.md) mavjud.

{% hint style="info" %}
**Ko'proq o'qish:**

[Burp Suite skaneri yordamida Clickjacking zaifliklarini topiish ☰](https://portswigger.net/burp/vulnerability-scanner)
{% endhint %}

## <mark style="color:yellow;">X-Frame-Options</mark> <a href="#x-frame-options" id="x-frame-options"></a>

X-Frame-Options birinchi marotaba Internet Explorer 8 da ko'rsatilgan va tezda boshqa brauzerlarga ham qabul qilingan. Headerga `X-Frame-Options: deny` ni qo'shish sayt egasiga iframe yoki obektlardan foydalanish mumkin yoki mumkin emasligini nazorat qilishni ta'minlaydi:

```http
X-Frame-Options: deny
```

Yoki sameorigin orqali qisqartirilishi mumkin:

```http
X-Frame-Options: sameorigin
```

Yoki `allow-from` orqali biror kerakli saytning domenini kiritish mumkin:

```http
X-Frame-Options: allow-from https://normal-website.com
```

X-Frame-Options brauzerlarda doim ham ishlamaydi (misol uchun `allow-form` Chrome 76 da va Safari 12 da ishlamaydi). Biroq [<mark style="color:yellow;">Content Security Policy</mark>](../xss/content-security-policy.md) bilan amalga oshirilgan himoya samarali bo'lishi mumkin.

## <mark style="color:yellow;">Content Security Policy (CSP)</mark> <a href="#content-security-policy-csp" id="content-security-policy-csp"></a>

Content Security Policy XSS va Clickjacking xujumlarini oldini olish uchun ishlatiladi. Ko'pincha serverdan Header ichida javob qaytarilganda bo'ladi:

```http
Content-Security-Policy: policy
```

bu yerda `policy` nuqta-vergul bilan ajratilgan policy ko'rsatmalari qatoridir. CSP mijoz brauzeriga veb-resurslarning ruxsat etilgan manbalari haqida ma'lumot beradi va bu brauzer zararli xatti-harakatlarni aniqlashiga va ushlab qolishi uchun qo'llaniladi.

Clickjackingdan ximoyalanish uchun `frame-ancestors` qiymati tavsiya qilinadi. Agarda uning qiymati '`none`' ga teng bo'lsa unda xuddi `X-Frame-Options` dagi `deny` kabi ishlaydi. Agar `frame-ancestors` qiymati '`self`' bo'lsa unda `X-Frame-Options` dagi sameorigin kabi ishlaydi:

```http
Content-Security-Policy: frame-ancestors 'self';
```

yoki ruxsat etilgan saytlar uchun:

```http
Content-Security-Policy: frame-ancestors normal-website.com;
```

Clickjacking va XSS dan samarali himoyalanish uchun CSP yaxshi tuzilgan bo'lishi kerak va yaxshi sinovdan o'tkazish kerak va bu ko'p qatlamli himoyaning bir qismi sifatida ishlatiladi.

{% hint style="info" %}
**Ko'proq o'qish:**

[CSP orqali Clickjacking dan himoyalanish <mark style="color:yellow;"></mark> ☰](../xss/content-security-policy.md#csp-orqali-clickjacking-dan-himoya)
{% endhint %}
