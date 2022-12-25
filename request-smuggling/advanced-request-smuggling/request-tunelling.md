# Request tunelling

Biz koʻrib chiqqan request smuggling hujumlarining ko'pi faqat ehtimoliydir chunki Front-end va Back-end oʻrtasidagi bir xil connection multiple requestlarni. Garchi ba'zi serverlar har qanday so'rovlar uchun ulanishni qayta ishlatsa-da, boshqalari qatiy siyosatlarga ega.

Masalan, bazi serverlar connectiondan qayta foydalanish uchun, faqat bir xil IP addressdan yoki bir xil clientdan kelgan requestlarga ruhsat beradi. Boshqa serverlar esa connectiondan umuman qayta foydalanmaydi, bu esa oddiy Request Smuggling orqali olishingiz mumkin boʻlgan narsalarni cheklaydi, chunki sizda boshqa foydalanuvchilar trafigiga taʼsir qilishning aniq usuli yoʻq.

<figure><img src="../../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

Boshqa foydalanuvchilarning requestlariga aralashish uchun socketni zararlay olmasangiz ham, Backend tomondan ikkita responseni keltirib chiqaradigan bitta request yuborishingiz mumkin. Bu sizga so'rovni va unga mos keladigan response-ni Front-end dan butunlay yashirish imkonini beradi.

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Muayyan so'rovlarni yuborishingizga to'sqinlik qiladigan front-end xavfsizligini chetlab o'tish uchun ushbu texnikadan foydalanishingiz mumkin. Darhaqiqat, Request Smuggling xujumlarining oldini olish uchun maxsus ishlab chiqilgan ba'zi mexanizmlar ham Request Tunnelling ni to'xtata olmaydi.

Requestlarni Backendga shu tarzda tunnelling qilish requestlarni yashirin olib o'tishning yanada cheklangan usulini taklif qiladi, ammo bu baribir yuqori darajadagi exploitlarni olib kelishi mumkin.

## <mark style="color:yellow;">HTTP/2 bilan Request Tunnelling</mark>&#x20;

Request tunellingni HTTP/1 va HTTP/2 bilan ham amalga oshirish mumkin, ammo HTTP/1 da aniqlash ancha qiyinroq. Doimiy (`keep-alive`) ulanishlar HTTP/1 da ishlashi tufayli, hatto ikkita response olsangiz ham, bu requestni muvaffaqiyatli yashirin olib oʻtilganligini tasdiqlamaydi.

HTTP/2 da esa har bir "stream" faqat bitta request va responseni o'z ichiga olishi kerak. Agar response javoblarida HTTP/1 response kabi ko'rinadigan HTTP/2 response qabul qilsangiz. ikkinchi requestni muvaffaqiyatli **tunelled** amin bo'lishingiz mumkin.

## <mark style="color:yellow;">HTTP/2 Request tunnelling orqali ichki headerlarni leak qilish.</mark>

Request tunnelini o'tkazish sizning yagona variantingiz bo'lsa, biz [<mark style="color:yellow;">oldingi laboratoriyalarimizdan birida</mark> ](https://portswigger.net/web-security/request-smuggling/exploiting/lab-reveal-front-end-request-rewriting)ko'rib chiqqan texnikadan foydalanib, ichki headerlarni leak qila olmaysiz, ammo HTTP/2 versiyasini downgrade muqobil yechimga imkon beradi.

Siz potentsial tarzda front-endni aldashingiz mumkin, bu ichki headerlani back-endda body parametriga aylantiradi. Keling mana bunga o'xshash so'rovni ko'rib chiqaylik:

```http
        :method POST
          :path /comment
     :authority vulnerable-website.com
   content-type application/x-www-form-urlencoded
            foo bar\r\n
                Content-Length: 200\r\n
                \r\n
                comment=
x=1
```

Bunday holda, Frontend va Backend faqat bitta request borligiga rozi bo'ladi. Qizig'i shundaki, ular headerlar qayerda tugashi borasida kelishmovchilikka olib kelishi mumkin.

Front-end biz kiritgan hamma narsani headerning bir qismi sifatida ko'radi, shuning uchun keyingi comment= so'zidan keyin har qanday yangi headerlarni qo'shadi. Backend esa `\r\n\r\n` ketma-ketligini ko'radi va bu headerlarning oxiri deb o'ylaydi. `comment=`  ichki headerlar bilan birga bodyning bir qismi sifatida ko'rib chiqiladi. Natijada, uning qiymati sifatida ichki headerlari bilan `comment` parametri hosil bo'ladi.

```http
POST /comment HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 200

comment=X-Internal-Header: secretContent-Length: 3
x=1
```

## <mark style="color:yellow;">Blind Request Tunnelling</mark>&#x20;

Ba'zi Front-end serverlar Back-end dan kelgan barcha ma'lumotlarni o'qib chiqadi. Bu degani, agarda siz so'rovni muvaffaqiyatli tunneldan o'tkazgan bo'lsangiz unda ikkala response ham browserga yuboriladi, responsening xabar matni ichida siz yuborgan tunnellangan so'rov joylashgan bo'ladi.

Boshqa Frontend serverlar bo'lsa faqatgina `Content-length` da berilgan raqamlarnigina o'qiydi, shunday ekan faqatgina birinchi request browserga yo'naltiriladi. Natijada siz tunelldan o'tkazgan requestingizni responselarda ko'rmaysiz.

## <mark style="color:yellow;">HEAD yordamida Non-blind Request tunnnelling</mark>&#x20;

Blind request tunnelling-ni exploit qilish biroz qiyin bo'lishi mumkin ammo siz Blind zaifliklarni HEAD metodi bilan non-blind ga o'zgartirishingiz mumkin.

`HEAD` requestlarning responselari har doim `Content-length` headerini o'z ichiga oladi, hattoki requestda xabar matni bo'lmasa ham. Bu odatda GET so'rovi bilan bir xil endpointga qaytariladigan resurs uzunligiga ishora qiladi. Ba'zi Front-end serverlar buni hisobga olmaydilar va headerda ko'rsatilgan baytlar sonidan qatiy nazar sonlarni o'qishga harakat qiladilar. Agar siz buni amalga oshiradigan front-end server orqali requestni muvaffaqiyatli tunnel qilsangiz, bu xatti-harakat uning Back-endidan responseni qayta o'qishiga olib kelishi mumkin. Natijada, siz olgan response tunnel requestiga response boshlanganidan boshlab baytlarni o'z ichiga olishi mumkin.

**Request**

```http
:method HEAD 
:path /example 
:authority vulnerable-website.com 
       foo bar\r\n
       \r\n
       GET /tunnelled HTTP/1.1\r\n
       Host: vulnerable-website.com\r\n 
       X: x 
```

**Response**

```http
:status 200 
content-type text/html 
content-length 131
HTTP/1.1 200 OK 
Content-Type: text/html 
Content-Length: 4286

<!DOCTYPE html>
<h1>Tunnelled</h1>
<p>This is a tunnelled respo
```

`Content-length` headerini bir responsedan boshqasining xabar matni bilan samarali aralashtirayotganingiz uchun, ushbu texnikadan muvaffaqiyatli foydalanish biroz to'g'ri harakat.

Agar siz `HEAD` requestini yuborgan endpoint siz oʻqimoqchi boʻlgan tunnelli responsedan qisqaroq resursni qaytarsa, yuqoridagi misolda boʻlgani kabi kerakli narsalarni koʻrishingizdan oldin u qisqartirilishi mumkin. Boshqa tomondan, agar qaytarilgan `Content-length` qiymati sizning tunnelli requestingiz responsidan uzunroq bo'lsa, Front-end serveri Back-enddan qo'shimcha baytlar kelishini kutayotganligi sababli siz timeoutga duch kelishingiz mumkin.

Yaxshiyamki, birozgina sinab ko'rish va xatolik bilan quyidagi yechimlardan birini ishlatib, ushbu muammolarni yengishingiz mumkin:

* HEAD requestingizni kerak boʻlganda uzunroq yoki qisqaroq sourceni qaytaradigan boshqa endpointga yoʻnaltiring.&#x20;
* Agar resource juda qisqa bo'lsa, o'zboshimchalik bilan to'ldirish belgilarini kiritish uchun asosiy HEAD so'rovida reflected inputdan foydalaning. Hatto inputni reflectedligini ko'rmasangiz ham, qaytgan responseda `Content-Length` shunga mos ravishda oshganini ko'rasiz.&#x20;
* Agar resource juda uzun bo'lsa, tunnel qilingan javob uzunligi kutilgan kontent uzunligiga mos kelishi yoki oshib ketishi uchun o'zboshimchalik bilan belgilar kiritish uchun tunnel so'rovida aks ettirilgan kiritishdan foydalaning.&#x20;

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [HTTP/2 tunelling orqali access controllni aylanib o'tish ≫](https://portswigger.net/web-security/request-smuggling/advanced/request-tunnelling/lab-request-smuggling-h2-bypass-access-controls-via-request-tunnelling)
{% endhint %}

## <mark style="color:yellow;">Request Tunnelling orqali Web Cache Poisoning</mark>&#x20;

Hatto request tunnellling, klassik Request Smugglingga qaraganda ancha cheklangan bo'lsa ham, ba'zida yuqori darajadagi hujumlarni amalga oshirishingiz mumkin. Misol uchun, siz web-cache poisoningning qo'shimcha kuchaytirish uchun biz hozirgacha ko'rib chiqqan request tunelling usullarini birlashtira olasiz.

Non-blind Request tunnelling yordamida, bir responsening headerlarini boshqasining xabar matni bilan samarali tarzda aralashtirishingiz va moslashtirishingiz mumkin. Agar xabar matnidagi response kodlanmagan foydalanuvchi kiritgan ma'lumot bo'lsa, brauzer odatda kodni bajarmaydigan kontekstlarda [reflected XSS ](../../xss/reflected-xss.md)uchun ushbu xatti-harakatdan foydalanishingiz mumkin.

Masalan, quyidagi response ma'lumotlari kodlanmagan, Haker tomonidan boshqariladigan ma'lumot kiritilgan:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{ "name" : "test<script>alert(1)<script>" }
[etc.]
```

O'z-o'zidan, bu nisbatan zararsizdir. `content-type` esa payloadni brauzer tomonidan oddiygina JSON sifatida interpreted qilinishini anglatadi. Ammo requestni Back-endga tunel qilsangiz nima bo'lishini o'ylab ko'ring. Bu response boshqa responsening xabar matnida paydo bo'lib, uning headerlarini, jumladan `content-type`ni meros qilib oladi.

<pre class="language-http"><code class="lang-http"><strong>             :status 200
</strong><strong>        content-type text/html
</strong>      content-length 174
HTTP/1.1 200 OK
Content-Type: application/json

{ "name" : "test&#x3C;script>alert(1)&#x3C;script>" } 
[etc.] 
</code></pre>

Keshlash front-endda amalga oshirilganligi sababli, keshlarni boshqa foydalanuvchilarga aralash javoblarga xizmat qilish uchun aldash mumkin.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [HTTP/2 request tunelling orqali Web cache poisoning ≫](https://portswigger.net/web-security/request-smuggling/advanced/request-tunnelling/lab-request-smuggling-h2-web-cache-poisoning-via-request-tunnelling)
{% endhint %}
