# Advanced request smuggling

Bu bo'limda biz siz hozirgacha o'rgangan tushunchalarga asoslanib sizga **** [<mark style="color:yellow;">**HTTP request smuggling**</mark>](../http-request-smuggling/)ning yanada murakkab usullarini o'rgatamiz. Bu darslarda,[ <mark style="color:yellow;">Burpning unique HTTP/2-sinov imkoniyatlari</mark>](https://hackthebrain.gitbook.io/burpsuite-uchun-qollanma/qanday-qilaman-tez-kunda/http-2-orqali-websaytni-tekshirish) orqali amalga oshiriladigan turli HTTP/2-based hujumlar ham qamrab olingan. Agar siz HTTP/2 haqida hech narsani bilmasangiz tashvishlanmang - biz barcha asosiy narsalarni ko‘rib chiqamiz.

Biz quydiagilarga ko'proq e'tibor beramiz:

* HTTP/2 qanday qilib [<mark style="color:yellow;">request smuggling uchun bir qator kuchli yangi vektorlar</mark>](./#http2-request-smuggling)ni ishga tushiribk xavfsiz hisoblangan veb-saytlarni ushbu turdagi hujumlarga nisbatan himoyasiz qoldirishini.
* Qanday qilib saytni butunlayga qo'lga kiritish uchun **Request Smuggling** dan foydalanib[ <mark style="color:yellow;">javoblar ro'yhatini zararlash</mark>](response-queue-poisoning.md) haqida.
* Web sayt front-end va back-end serverlar o'rtasidagi[ <mark style="color:yellow;">ulanishni umuman qayta ishlatmasa ham</mark>](request-tunelling.md) yuqori darajadagi exploitlarni yaratish uchun HTTP/2-eksklyuziv inputlardan qanday foydalanishingiz mumkinligi haqida.

Siz o'rganganlaringizni amaliyotda sinashingiz uchun biz bir qancha laboratoriyalar tayyorlab qo'yganmiz. Ular Black Hat USA 2021 da Portswiggerning Research direktori [<mark style="color:yellow;">James Kettle</mark>](https://portswigger.net/research/james-kettle) tomonidan taqdim qilingan haqiqiy zaifliklar asosida qurilgan.

{% hint style="info" %}
**PortSwigger Research:**

[HTTP/2: The sequel is always worse ☰](https://portswigger.net/research/http2)
{% endhint %}

## <mark style="color:yellow;">HTTP/2 Request Smuggling</mark> <a href="#http2-request-smuggling" id="http2-request-smuggling"></a>

Biz ushbu bo'limda HTTP/2 qanchalik ishonchni oqlay olmaganini, HTTP/2dan foydalanish saytlarda request smuggling ojizligini keltirib chiqarganini, hatto havfsiz saytlarni bu ojizlikka nisbatan himoyasiz qilib qo'yganini ko'rsatamiz.

### <mark style="color:yellow;">HTTP/2 xabar uzunligi</mark>

Request smuggling asosan turli serverlar, so'rov uzunligini qanday izohlashi o'rtasidagi nomuvofiqliklardan foydalanish bilan bog'liq. HTTP/2 haqida anchadan beri u Request smugglingga nisbatan himoya uchun yagona, mustahkam mexanizmni taqdim etadi deb o'ylangan.

Garchi siz buni Burpda ko'rmasangiz ham, HTTP/2 xabarlari sim orqali alohida "freymalr" qatori sifatida yuboriladi. Har bir freymdan oldin aniq uzunlik maydoni mavjud bo'lib, u serverga qancha baytni o'qish kerakligini aniq ko'rsatadi. Shuning uchun requestning uzunligi uning freym uzunligi yig'indisidir.

Nazariy jihatdan haker, HTTP/2 dan doimiy ravishda foydalanadigan sayt mexanizmiga qarshi hech qanday xujum amalga oshira olmaydi. Ammo, HTTP/2 downgrade qilish keng tarqalgan bo'lsada, xavfliligi sababli  downgrade qilinmaydi.

### <mark style="color:yellow;">HTTP/2 downgrade</mark> <a href="#http2-pasaytirish" id="http2-pasaytirish"></a>

HTTP/2 versiyasini pasaytirish HTTP/1 sintaksisidan foydalangan holda HTTP/2 so‘rovlarini HTTP/1 so‘rovini yaratish uchun qayta yozish jarayoni hisoblanadi. Ko'pgina web-serverlar va reverse proxy serverlar buni ko'pincha HTTP/1-ni ishlatadigan serverlar bilan aloqa qilishda browserga HTTP/2-ni qo'llab-quvvatlashni taklif qilish uchun ishlatadilar. Bu amaliyot, esa biz bugun o'rganadigan xujumlarga asos bo'la oladi.

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
**Ko'proq o'qish:**

[HTTP/2 versiyasini pasaytirish nima ? ☰](http-2-downgrade.md)
{% endhint %}

{% hint style="info" %}
**HTTP/2 xabar qayta taqdim qilinishi**

HTTP/2 bu ikkilik sanoq sistemasida ishlovchi protokol hisoblanib, biz HTTP/2 xabarlarini inson o'qiy oladigan formatga o'tkazish uchun biz quyidagi materiallardan foydalanamiz:

* Biz har bir xabarni alohida "freym" emas, balki bitta obyekt sifatida ko'rsatamiz.
* Biz headerlarni oddiy matn nomi va qiymat maydonlari yordamida ko'rsatamiz.
* Biz psevdo-header nomlarini oddiy headerlardan farqlash uchun ikkita nuqta bilan ajratib qo'yamiz.
{% endhint %}

### <mark style="color:yellow;">H2.CL zaifliklar</mark> <a href="#h2cl-zaifliklar" id="h2cl-zaifliklar"></a>

HTTP/2 so'rovlarida ularning qancha uzunlikda ekanligini bildiruvchi header mavjud emas. Versiyani pasauytirish davomida, front-end serverlar ko'pincha HTTP/1 `Content-Length` headerini qo'shib, [<mark style="color:yellow;">HTTP/2 ning built-in uzunlik mexanizmi</mark>](./#http-2-xabar-matni-uzunligi) yordamida uning qiymatini oladi. Qizig'i shundaki, HTTP/2 so'rovlari o'zlarining `Content-Length` headerini ham o'z ichiga olishi mumkin. Bu holatda ba'zi frontend serverlar shunchaki u qiymatni HTTP/1 so'rovida qayta ishlatadi.

HTTP/2 requestidagi har qanday Content-Length headeri, HTTP/2ning built-in length mexanizmi yordamida hisoblangan lengthga mos kelishi kerak **** lekin bu downgrade qilishdan oldin doim ham to'g'ri hisoblamaydi. Natijada, noto'g'ri Content-length headerini kiritish orqali so'rovlarni yashirincha olib o'tish mumkin bo'lishi mumkin. Front-end, request qayerda tugashini aniqlash uchun yashirin HTTP/2 uzunligidan foydalansa-da, HTTP/1 back-end siz kiritgan headerdan olingan Content-length headeriga murojaat qilishi kerak, natijada sinxronizatsiya buziladi, natijada sinxronizatsiya buziladi.

**Frontend (HTTP/2)**

```http
        :method	POST
          :path	/example
     :authority vulnerable-website.com
   content-type application/x-www-form-urlencoded
 content-length 0

GET /admin HTTP/1.1
Host: vulnerable-website.com
Content-Length: 10

x=1
```

**Back-end (HTTP/1)**

```http
POST /example HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 0

GET /admin HTTP/1.1
Host: vulnerable-website.com
Content-Length: 10

x=1GET / H
```

{% hint style="success" %}
**Ma'slahat**

Ba'zi Request Smuggling xujumlarini amalga oshirayotganingizda, o'zingizning smuggled requestingizga foydalanuvchining requestidagi headerlarni qo'shishni xohlashingiz mumkin. Lekin ular ba'zida xujumingizga xalaqit berishi mumkin, nusxasi mavjud header xatolari va shunga o'xshash xolatlar yuzaga keladi. Yuqoridagi misolimizda buni, parametr va `Content-Length` headerini o'z ichiga olgan smuggled prefiks orqali yumashtdik. Response hajmidan bir oz uzunroq boʻlgan `Content-Length` headeridan foydalangan holda, foydalanuvchining soʻrovi sizning smuggled prefiksingizga qoʻshiladi, lekin headerlardan oldin qisqartiriladi.
{% endhint %}

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [H2.CL request smuggling ≫](https://portswigger.net/web-security/request-smuggling/advanced/lab-request-smuggling-h2-cl-request-smuggling)
{% endhint %}

### <mark style="color:yellow;">H2.TE zaifliklar</mark> <a href="#h2te-zaifliklar" id="h2te-zaifliklar"></a>

Chunked (bo'laklangan) Transfer Encoding HTTP/2 protokoli bilan bir biriga mos kelmaydi va spetsifikatsiya siz kiritmoqchi bo'lgan har qanday `Transfer-encoding: chunked` headerini olib tashlashni yoki so'rovni butunlay blokirovka qilishni tavsiya qiladi. Agar front-end server buni uddalay olmasa va keyinchalik chunked kodlashni qo'llab-quvvatlaydigan HTTP/1 back-end so'rovI uchun requestni downgrade qilsa bu request smuggling hujumlarini ham yuzaga keltirishi mumkin.

**Frontend (HTTP/2)**

```http
          :method  POST
            :path  /example
       :authority  vulnerable-website.com
     content-type  application/x-www-form-urlencoded
transfer-encoding  chunked

0

GET /admin HTTP/1.1
Host: vulnerable-website.com
Foo: bar
```

**Backend (HTTP/1)**

```http
POST /example HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Transfer-Encoding: chunked

0

GET /admin HTTP/1.1
Host: vulnerable-website.com
Foo: bar
```

Agar web sayt H2.CL yoki H2.TE request smuggling uchun himoyasiz bo'lsa unda siz [<mark style="color:yellow;">oldingi darslarimizdan ko'rgan</mark> ](broken-reference)xujumlarimizni bemalol amalga oshirishingiz mumkin.

## <mark style="color:yellow;">Response queue poisoning</mark> <a href="#response-queue-poisoning" id="response-queue-poisoning"></a>

Response queue poisoning boshqa foydalanuvchilarning ham responselarini o'zboshimchalik bilan o'g'irlash imkonini beruvchi kuchli rewuest smuggling hujumi bo'lib, ularning hisoblarini va hatto butun saytni xavf ostiga qo'yishi mumkin.

{% hint style="info" %}
**Batafsil o'qish:**\
[Response queue poisoning ☰](response-queue-poisoning.md)
{% endhint %}

## <mark style="color:yellow;">CRLF ineksiya orqali Request Smuggling</mark> <a href="#request-smuggling-crlf-injection-bilan" id="request-smuggling-crlf-injection-bilan"></a>

Agar web saytlar `content-length`ni tekshirish yoki `transfer-encoding` headerlarini o'chirish orqali H2.CL yoki H2.TE hujumlarini oldini olish uchun choralar ko'rsa ham, HTTP/2 ning ikkilik sanoq sistemasi, bunday front-end choralarini chetlab o'tishning yangi usullarini paydo qiladi.

HTTP/1 da, baʼzan taqiqlangan headerlarni yashirincha olib oʻtish uchun, serverlar yangi qator (\n) belgilarini qo'llashidagi farqga qarab undan foydalanishingiz mumkin. Agar back-end buni chegaralovchi sifatida qo'llasa, lekin front-end server bunday qilmasa, ba'zi frontend serverlar ikkinchi headerlarni umuman aniqlolmaydi.

```http
Foo: bar\nTransfer-Encoding: chunked
```

Toʻliq CRLF (\r\n) ketma-ketligi bilan ishlashda bunday farq mavjud emas, chunki barcha HTTP/1 serverlar bu headerni bekor qilishga rozi bo'ladi.

Boshqa tomondan, HTTP/2 xabarlari matnga asoslangan emas, ikkilik sistemasida bo'lganligi sababli, har bir headerlarning chegaralari chegarani ajratuvchi belgilarga emas, balki aniq, oldindan belgilangan ofsetlarga asoslanadi. Bu shuni anglatadiki, \r\n endi header qiymatida alohida ahamiyatga ega emas va shuning uchun headerni ikkiga boʻlmasdan, qiymatning oʻziga kiritilishi mumkin:

```http
foo bar\r\nTransfer-Encoding: chunked
```

Bu ko'rinishidan xavfli emasga o'xshaydi, ammo bu, HTTP/1 requesti sifatida rewrite qilinsa, \r\n yana headerni chegaralovchi sifatida ishlatiladi. Natijada, HTTP/1 serveri ikkita alohida headerni ko'radi:

```
Foo: bar
Transfer-Encoding: chunked
```

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [CRLF ineksiya orqali HTTP/2 request smuggling ≫](https://portswigger.net/web-security/request-smuggling/advanced/lab-request-smuggling-h2-request-smuggling-via-crlf-injection)
{% endhint %}

{% hint style="info" %}
**Ko'proq o'qish:**

[Qo'shimcha HTTP/2-exclusive hujum vektorlari ☰](maxsus-http-2-vektorlar.md)
{% endhint %}

## <mark style="color:yellow;">HTTP/2 so'rovini ajratish</mark> <a href="#http2-sorov-bolinishi" id="http2-sorov-bolinishi"></a>

Biz [<mark style="color:yellow;">response queue poisoning</mark>](response-queue-poisoning.md)ni ko'rib chiqganimizda, back-endda bitta HTTP so'rovni ikkita to'liq so'rovga qanday ajratishni bilib oldingiz. Biz ko'rgan misolda, bo'linish xabar matnining ichida sodir bo'ldi, ammo HTTP/2 versiyani pasaytirish ishtirok etsa, bu bo'linishni headerlarda ham bajarishingiz mumkin.

Ushbu yondashuv ko'p imkoniyatli, chunki siz body o'z ichiga olishiga ruhsat berilgan request metodlardangina foydalanishga qaram emassiz. Masalan siz, hatto `GET` ni ham ishlata olasiz:

<pre class="language-http"><code class="lang-http"><strong>          :method  GET
</strong><strong>            :path  /
</strong>       :authority  vulnerable-website.com
              foo  bar\r\n
                   \r\n
                   GET /admin HTTP/1.1\r\n
<strong>                   Host: vulnerable-website.com
</strong></code></pre>

Bu yana tasdiqlangan `content-length` va back-end chunked encodingni qo'llab quvvatlamgan holatlarda ham foyda beradi.

## <mark style="color:yellow;">Front-end rewritingni hisobga olish</mark> <a href="#front-end-qayta-yozishni-hisobga-olish" id="front-end-qayta-yozishni-hisobga-olish"></a>

Headerlardagi so'rovlarni bo'layotganingizda ular front-end tomonidan qanday qilib qayta yozilayotganini tushunishingiz kerak va ularni HTTP/1 headerlariga qo'lda qo'shishni ham hisobga olish kerak. Bo'lmasa so'rovlardan biri majburiy headerlarsiz yuborilishi mumkin.

Masalan back-end ga yuborilayotgan so'rovlarda `Host` headeri bo'lishini ta'minlashingiz kerak. Front-end serverlar `:authority` psevdo-headerini olib tashlaydi va versiyani pasaytirish davomida HTTP/1 dagi `Host` headerni qo'shadi. Buni amalga oshirish uchun turli xil yondashuvlar mavjud, ular siz kiritayotgan `Host` headerini qayerda joylashtirishingiz kerakligiga ta'sir qilishi mumkin.

Quyidagi so'rovni ko'rib chiqing:

<pre class="language-http"><code class="lang-http">          :method  GET
            :path  /
<strong>       :authority  vulnerable-website.com
</strong>              foo  bar\r\n
                   \r\n
                   GET /admin HTTP/1.1\r\n
                   Host: vulnerable-website.com
</code></pre>

Rewriting davomida ba'zi frontend serverlar headerlar ro'yhatining oxiriga yangi `Host` headerini qo'shib qo'yishadi. HTTP/2 ga kelsak, bu ishni u foo headeridan keyin amalga oshirmoqda. E'tibor bering bu backendda request bo'linadigan joydan ishlamoqda. Bu degani birinchi so'rovda hech qanday `Host` headeri bo'lmasligi mumkin, ammo smuggled requestda 2ta bor. Bunday holda, siz kiritilgan `Host` headerini reqeust 2ga bo'linganidan keyin birinchi requestda tugaydigan qilib joylashtirishingiz kerak:

```
          :method  GET
            :path  /
       :authority  vulnerable-website.com
              foo  bar\r\n
            Host:  vulnerable-website.com\r\n
                   \r\n
                   GET /admin HTTP/1.1
```

Shuningdek, siz kiritmoqchi bo'lgan har qanday ichki headerlarning joylashishini xuddi shunday tarzda sozlashingiz kerak bo'ladi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [CRLF ineksiya orqali HTTP/2 so'rovini bo'lish ≫](https://portswigger.net/web-security/request-smuggling/advanced/lab-request-smuggling-h2-request-splitting-via-crlf-injection)
{% endhint %}

{% hint style="success" %}
**Maslahat:**

Yuqoridagi misolda biz requestni [<mark style="color:yellow;">**response queue poisoning**</mark> <mark style="color:yellow;"></mark><mark style="color:yellow;"></mark> ](response-queue-poisoning.md)ni yuzaga keltiradigan tarzda ajratdik, biroq siz shu yo'l bilan standart **Request Smuggling** xujumlari uchun prefikslarni ham olib oʻtishingiz mumkin. Bunday holda, kiritilgan headerlaringiz so'rovning backend tomonidagi prefiksingizga qo'shilgan headerlar bilan to'qnash kelishi mumkin, natijada nuxsasi mavjud header xatolariga yoki so'rov noto'g'ri joyda tugatilishiga olib keladi. Buni yumshatish uchun siz smuggling prefiksiga body parametrini hamda xabar matnidan bir oz uzunroq boʻlgan `Content-length` headerini kiritishingiz mumkin. Foydalanuvchining so'rovi sizning Smuggling prefiksingizga qo'shiladi, lekin headerlardan oldin qisqartiriladi.
{% endhint %}

## <mark style="color:yellow;">HTTP Request Tunnelling</mark> <a href="#http-request-tunnelling" id="http-request-tunnelling"></a>

Biz hozirgacha koʻrib chiqqan koʻplab request smuggling hujumlari faqat bir nechta so'rovlarni qayta ishlash uchun front-end va back-end serverlar o'rtasidagi bir xil ulanishdan foydalanilgani uchun mumkin.  HTTP request tunelling ulanishni qayta ishlatmasa ham, yuqori darajadagi ekspluatatsiyalarni yaratish usulini taqdim etadi.

{% hint style="info" %}
**Ko'proq o'qish:**\
[HTTP request tunelling ☰](request-tunelling.md)
{% endhint %}
