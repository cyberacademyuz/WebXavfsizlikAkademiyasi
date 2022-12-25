# Maxsus HTTP/2 vektorlar

HTTP/2 matnga asoslanganmas, balki ikkilik sanoq sistemasi asosida ishlaydigan protokol bo'lib,  sintaksisidagi cheklovlar tufayli HTTP/1 da qilish mumkin bo'lmagan bir qator potentsial vektorlar mavjud.

Biz allaqachon [<mark style="color:yellow;">CRLF ketma-ketliklarini header qiymatiga qanday kiritish</mark>](./#request-smuggling-crlf-injection-bilan) mumkinligini ko'rib chiqdik. Ushbu bo'limda biz sizga payloadlarni kiritish uchun foydalanishingiz mumkin bo'lgan boshqa maxsus HTTP/2 vektorlari haqida tavsiya beramiz. Ushbu turdagi so'rovlar HTTP/2 spetsifikatsiyasi tomonidan rasman taqiqlangan bo'lsa-da, ba'zi serverlar ularni yaxshi tekshira olmaydi va bloklamaydi.

{% hint style="info" %}
**Eslatma:**

Bu xujumlarni faqatgina Burp ning Inspektor panelidagi HTTP/2 ning maxsus xususiyatlari orqaligina amalga oshira olasiz.
{% endhint %}

## <mark style="color:yellow;">Header nomlari orqali ineksiya</mark> <a href="#header-nomlari-ila-inektsiya" id="header-nomlari-ila-inektsiya"></a>

HTTP/1 da header nomida ikki nuqta boʻlmasligi kerak, chunki bu belgi parserlarga header nomining oxirini koʻrsatish uchun ishlatiladi. HTTP/2 da esa bunday emas.

Ikki nuqtani \r\n belgilar bilan birlashtirib, HTTP/2 header nomi maydonidan boshqa headerlarni front-end filtrlaridan yashirincha o‘tkazishda foydalanishingiz mumkin. Request HTTP/1 sintaksisi yordamida rewrite bo'lgandan keyin, ular back-endda alohida headerlar sifatida talqin qilinadi:

**Frontend (HTTP/2)**

```http
foo: bar\r\nTransfer-Encoding: chunked\r\nX:  ignore
```

**Backend (HTTP/1)**

```http
Foo: bar\r\n
Transfer-Encoding: chunked\r\n
X: ignore
```

## <mark style="color:yellow;">Psevdo-header nomlari orqali ineksiya</mark> <a href="#psevdo-header-nomlari-ila-inektsiya" id="psevdo-header-nomlari-ila-inektsiya"></a>

HTTP/2 so'rov qatori yoki status qatoridan foydalanmaydi. Buning o'rniga, bu ma'lumotlar so'rovning old qismidagi bir qator "psevdo-headerlar" orqali uzatiladi. HTTP/2 xabarlarining matnga asoslangan ko'rinishlarida ularni oddiy headerlardan farqlash uchun ular odatda ikki nuqta bilan qo'yiladi. Umumiy beshta psevdo headerlar mavjud:

* `:method` - so'rov metodini bildiradi
* `:path` - so'rov yo'lini bildiradi. U o'zida query matnini ham olishi mumkinligiga e'tibor bering
* `:authority` - HTTP/1 dagi Host headerini bildiradi
* `:scheme` -  http yoki https protokolining qaysi biridan foydalanish kerakligini bildiradi
* `:status` - javob statusini ko'rsatadi (ammo so'rovlarda ishlatilmaydi)

Veb-saytlar so'rovlarni HTTP/1 ga downgrade qilganda, ular request qatorini dinamik ravishda yaratish uchun ushbu psevdo-headerlarning ba'zi qiymatlaridan foydalanadilar. Bu hujumlarni yaratishning ba'zi qiziqarli yangi usullarini beradi.

## <mark style="color:yellow;">Noaniq xostni kiritish</mark> <a href="#noaniq-xostni-taqdim-etish" id="noaniq-xostni-taqdim-etish"></a>

HTTP/1 dagi `Host` headeri HTTP/2 da `authority` psevdo-headeri bilan o'zgargan ekan, demak siz so'rovda `host` headerini bemalol qo'shib yubora olasiz.

Ba'zi hollarda, bu qayta yozilgan HTTP/1 requestida ikkita Hpst headeri paydo bo'lishiga sabab bo'lishi mumkin, bu, masalan, front-end "Dublicate Host header" filtrlarini chetlab o'tish uchun yana bir imkoniyatni ochadi. Bu boshqa yo'l bilan himoyalangan web saytni <mark style="color:yellow;">Host header xujumlar</mark>iga qarshi himoyasiz qiladi.

## <mark style="color:yellow;">Noaniq joylashuvni kiritish</mark> <a href="#noaniq-yolni-taqdim-etish" id="noaniq-yolni-taqdim-etish"></a>

HTTP/1 da so'rov qatori tahlil qilinganligi sababli noaniq joylashuv bilan so'rov yuborib  bo'lmaydi. Ammo HTTP/2 dagi yo‘l psevdo-header yordamida ko‘rsatilganligi sababli, endi so‘rovni quyidagi tarzda ikki xil yo‘l bilan yuborish mumkin:

```http
:method POST
:path 	/anything
:path 	/admin
:authority vulnerable-website.com
```

Agar veb-saytning <mark style="color:yellow;">kirish boshqaruvlari</mark> tomonidan tasdiqlangan path va requestni yo'naltirish uchun qaysi joylashuvdan foydalanilganligi o'rtasida farq mavjud bo'lsa, bu sizga istalgan endpointga kirish imkonini berishi mumkin.

## <mark style="color:yellow;">To'liq request qatorini ineksiya qilish</mark> <a href="#tolaqonli-sorov-liniyasini-inektsiya-qilish" id="tolaqonli-sorov-liniyasini-inektsiya-qilish"></a>

Versiyani pasaytirish vaqtida `:method`  psevdo-headerining qiymati HTTP/1 requestining eng boshida yoziladi. Agar server `:method` qiymatiga bo'sh joy qo'shishga ruxsat bersa, siz butunlay boshqacha so'rov qatorini quyidagicha kiritishingiz mumkin:

**Frontend (HTTP/2)**

```http
:method GET /admin HTTP/1.1
:path 	/anything
:authority vulnerable-website.com
```

**Backend (HTTP/1)**

```http
GET /admin HTTP/1.1 /anything HTTP/1.1
Host: vulnerable-website.com
```

Agar server so'rovlar qatoridagi o'zboshimchalik bilan keyingi belgilarga ham toqat qilsa, bu noaniq yo'l bilan so'rovni yaratishning boshqa vositasini taqdim etadi.

## <mark style="color:yellow;">URL prefiksini ineksiya qilish</mark> <a href="#url-prefix-inektsiya" id="url-prefix-inektsiya"></a>

HTTP/2 ning yana bir qiziqarli xususiyati :scheme psevdo-headeri yordamida so'rovning o'zida protokolni ko'rsatish qobiliyati. Bu odatda http yoki https ni o'z ichiga olsa ham, siz ixtiyoriy qiymatlarni kiritishingiz mumkin.

Bu masalan, server URL manzilini dinamik ravishda yaratish uchun :scheme headeridan foydalanganda foyda berishi mumkin. Bunday holda, siz URL-ga prefiks qo'shishingiz yoki so'rov stringiga haqiqiy URL-ni  kiritish orqali uni butunlay bekor qilishingiz mumkin:

**So'rov**

```http
   :method GET
     :path /anything
:authority vulnerable-website.com
   :scheme https://evil-user.net/poison?
```

**Javob**

```http
 :status 301
location https://evil-user.net/poison?://vulnerable-website.com/anything/
```

## <mark style="color:yellow;">Pseudo-headerlarga yangi qatorlarni ineksiya qilish</mark> <a href="#pseudo-headerlarga-yangi-qatorlarni-kiritish" id="pseudo-headerlarga-yangi-qatorlarni-kiritish"></a>

`:path` yoki `:method` psevdo-sarlavhalarini qo'shganda, natijada HTTP/1 so'rovi hali ham to'g'ri request qatoriga ega ekanligiga ishonch hosil qilishingiz kerak.

HTTP/1-da `\r\n`  belgilari request qatorini tugatganligi sababli, `\r\n` belgilarini qo'shish requestni buzadi. Downgrading amalga oshirilgandan so'ng, qayta yozilgan so'rov siz kiritgan birinchi `\r\n` dan avval quyidagi ketma-ketlikni o'z ichiga olishi kerak.

```avisynth
<method> + space + <path> + space + HTTP/1.1
```

Sizning ineksiyangiz ushbu ketma-ketlikda qayerga to'g'ri kelishini tasavvur qiling va shunga mos ravishda qolgan barcha qismlarni kiriting. Misol uchun, `:path`ga kiritishda `\r\n` dan oldin **bo'sh joy** va **HTTP/1.1** ni qo'shishingiz kerak:

**Front-end (HTTP/2)**

<pre class="language-http"><code class="lang-http">   :method GET
<strong>     :path /example HTTP/1.1\r\n
</strong>           Transfer-Encoding: chunked\r\n
           X: x
:authority vulnerable-website.com
</code></pre>

**Back-end (HTTP/1)**

```http
GET /example HTTP/1.1\r\n
Transfer-Encoding: chunked\r\n
X: x HTTP/1.1\r\n
Host: vulnerable-website.com\r\n

```

E'tibor bering, bu holda biz rewriting paytida avtomatik ravishda qo'shilgan bo'sh joy va protokolni ushlab turish uchun o'zboshimchalik bilan keyingi headerlarni qo'shdik.
