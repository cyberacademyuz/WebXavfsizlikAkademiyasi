# Access-control-allow-origin

## <mark style="color:yellow;">CORS va Access-Control-Allow-Origin response headerlari</mark> <a href="#access-control-allow-origin-response-headeri-nima" id="access-control-allow-origin-response-headeri-nima"></a>

Ushbu bo'limda biz [<mark style="color:yellow;">CORS</mark>](broken-reference) ga nisbatan `Access-Control-Allow-Origin` headeri nimaligini va u CORSni amalga oshirishning bir qismini qanday tashkil qilishini o'rganamiz.

CORS, HTTP headerlari toʻplamidan foydalanish orqali bir web-sayt domeniga boshqasidan HTTP soʻrovlari uchun [<mark style="color:yellow;">same origin policy</mark> ](same-orign-policy.md)ni nazorat ostida yumshatish imkonini beradi. Brauzer esa cross-origin requestlarga header instruksiyasiga qarab ruxsat beradi.

## <mark style="color:yellow;">Access-Control-Allow-Origin response headeri nima ?</mark> <a href="#access-control-allow-origin-response-headeri-nima" id="access-control-allow-origin-response-headeri-nima"></a>

`Access-Control-Allow-Origin` response headeri bu response da boshqa bir web saytdan so'rov qabul qilib boshqa web sayt originiga yuborish ruxsatini beruvchi header hisoblanadi. Brauzer `Access-Control-Allow-Origin` response headerini o'rganib chiqadi va agarda ular so'rov jo'natilayotgan saytga to'g'ri kelsa unda response ga ruxsat beradi.

## <mark style="color:yellow;">Oddiy CORSni amalga oshirish</mark> <a href="#oddiy-cross-origin-resource-sharing-ni-amalga-oshirish" id="oddiy-cross-origin-resource-sharing-ni-amalga-oshirish"></a>

CORS spetsifikatsiyasi web-serverlar va brauzerlar o'rtasida almashinadigan header tarkibini belgilaydi, bu esa **origin** domenidan tashqaridagi web-resurs so'rovlarining **origin**ini cheklaydi. CORS header protokolida `Access-Control-Allow-Origin` juda katta ahamiyatga ega. Bu header sabab Origin uchun ruxsat olish mumkin.

Masalan, manabu `normal-website.com` domenlararo so'rovlarni amalga oshirmoqchi shunda quyidagi kabi request jo'natiladi:

```http
GET /data HTTP/1.1
Host: robust-website.com
Origin: https://normal-website.com 
```

va `robust-website.com` saytining serveri mana bunday javob qaytarishi zarur:

```http
HTTP/1.1 200 OK
...
Access-Control-Allow-Origin: https://normal-website.com
```

Orignlar mos kelsa brauzer `normal-website.com` responselarga javob bera olishiga ruxsat beradi.

`Access-Control-Allow-Origin` spesifikatsiyasi bir qancha originlarga ruxsat berishi yoki `null` qiymatiga teng bo'lishi yoki \* belgisidan foydalanishi mumkin. Ammo hech qaysi brauzer bir qancha originlarga ruxsat bermaydi, \* belgisini ishlatishda bir qancha cheklovlar mavjud.

## <mark style="color:yellow;">Hisob ma'lumotlari orqali CORSni boshqarish</mark> <a href="#cross-origin-resource-sharing-ni-hisobga-olish-malumotlari-bilan-qolga-olish" id="cross-origin-resource-sharing-ni-hisobga-olish-malumotlari-bilan-qolga-olish"></a>

CORS standart xatti-harakat soʻrovlari cookielar va avtorizatsiya headerlari kabi hisob maʼlumotlarisiz uzatiladi. Ammo ushbu hisob ma'lumotlarni olishning imkoni bor, buning uchun `Access-Control-Allow-Credentials` headerini responsega qo'shib jo'natish kerak. Endi, agar so'rov yuborayotgan web-sayt so'rov bilan cookielarni yuborayotganini bildirish qilish uchun JavaScript-dan foydalansa:

```http
GET /data HTTP/1.1
Host: robust-website.com
...
Origin: https://normal-website.com
Cookie: JSESSIONID=<value>
```

response mana bunday bo'ladi:

```http
HTTP/1.1 200 OK
...
Access-Control-Allow-Origin: https://normal-website.com
Access-Control-Allow-Credentials: true
```

Shundan keyin brauzer request jo'natvotgan saytga responselarni o'qishga ruxsat beradi, chunki `Access-Control-Allow-Credentials` headerining qiymati `true`. Boshqa holatlarda brauzer ruxsat bermaydi.

## <mark style="color:yellow;">CORS spesifikatsiyalarini \* lar bilan yumshatish</mark> <a href="#cors-spesifikatsiyalarini-wildcardlar-bilan-yumshatish" id="cors-spesifikatsiyalarini-wildcardlar-bilan-yumshatish"></a>

`Access-Control-Allow-Origin` \* larni qo'llab quvvatlaydi. Masalan:

```http
Access-Control-Allow-Origin: *
```

{% hint style="info" %}
**Eslatma:**

\* lar boshqa qiymatlar bilan birga ishlatilmaydi.

```
Access-Control-Allow-Origin: https://*.normal-website.com
```
{% endhint %}

Yaxshiyamki, xavfsizlik nuqtai nazaridan, \* dan foydalanish spetsifikatsiyada cheklangan, chunki siz \* larni hisobga olish maʼlumotlarini (autentifikatsiya, cookie fayllari yoki mijoz sertifikatlari) bilan birlashtira olmaysiz. Shunday qilib, formaning domenlararo server javobi:

```http
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
```

ruxsat berilmaydi, chunki bu saytdagi har qanday autentifikatsiya qilingan kontentni hammaga oshkor qilib yuborishi mumkin.

Ushbu cheklovlarni hisobga olgan holda, ba'zi web-serverlar mijoz tomonidan ko'rsatilgan origin asosida dinamik ravishda `Access-Control-Allow-Origin` headerlarini yaratadilar. Bu ish xavfsiz hisoblanmaydi. Biz uni [<mark style="color:yellow;">qanday qilib exploit qilish</mark>](cross-origin-resource-sharing-cors.md#server-tomonidan-yaratilgan-acao-header-idan-client-specified-origin-header-i-kelib-chiqishi) mumkinligini keyinroq tushuntiramiz.

## <mark style="color:yellow;">Pre-flight tekshiruvi</mark> <a href="#pre-flight-tekshiruvi" id="pre-flight-tekshiruvi"></a>

Pre-flight tekshiruvi CORS spetsifikatsiyasiga CORS tomonidan ruxsat etilgan kengaytirilgan soʻrov xususiyatlaridan eski resurslarni himoya qilish uchun qoʻshilgan. Muayyan holatlarda, domenlararo so'rov nostandart HTTP usuli yoki headerlarni o'z ichiga olgan bo'lsa, oorign so'rovi oldidan `OPTIONS` usulidan foydalangan holda so'rov yuboriladi va CORS protokoli qanday usullar va headerlarga ruxsat berilganligini tekshirishni talab qiladi.

Masalan, ushbu pre-flight `PUT` requestini maxsus `Special-Request-Header` qo'shilgan holda yubormoqda:

```http
OPTIONS /data HTTP/1.1
Host: <some website>
...
Origin: https://normal-website.com
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: Special-Request-Header
```

va serverning javobi mana bunday:

```http
HTTP/1.1 204 No Content
...
Access-Control-Allow-Origin: https://normal-website.com
Access-Control-Allow-Methods: PUT, POST, OPTIONS
Access-Control-Allow-Headers: Special-Request-Header
Access-Control-Allow-Credentials: true
Access-Control-Max-Age: 240
```

Ushbu response da ko'rishimiz mumkinki, ishlatish mumkin bo'lgan request usullari (`PUT`, `POST` va `OPTIONS`) va ruxsat etilgan headerlar (`Special-Request-Header`) berilgan. Bunday holda, o'zaro faoliyat domen serveri hisob ma'lumotlarini yuborishga ham ruxsat beradi va `Access-Control-Max-Age` headeri qayta foydalanish uchun pre-flight javobini keshlash uchun maksimal vaqt oralig'ini belgilaydi. Agar berilgan usullardan to'g'ri jo'natilsa unda brauzer odatdagi kabi domenlararo so'rovlarga ruxsat beradi. Pre-flight tekshiruvlar oʻzaro domen soʻroviga qoʻshimcha HTTP soʻrovi aylanmasini qoʻshadi, shuning uchun ular koʻrib chiqish vaqtlarini oshiradi.

## <mark style="color:yellow;">CORS CSRFdan himoya qila oladimi ?</mark> <a href="#cors-csrf-ga-qarshi-turib-bera-oladimi" id="cors-csrf-ga-qarshi-turib-bera-oladimi"></a>

CORS umuman [<mark style="color:yellow;">CSRF</mark>](broken-reference)dan himoya qila olmaydi, bu noto'g'ri tushuncha.

CORS same-origin policy ni yumshatib berishi mumkin, shunday ekan noto'g'ri konfiguratsiya qilingan CORS tufayli CSRF xujumlarini amalga oshirish imkoniyati oshadi va ularning ta'siri kuchaytiradi.

O'zi CSRF xujumlarni CORS siz ham amalga oshirishni turli tuman usullari mavjud, shunchaki oddiy HTML formalari va domenlararo so'rovlar amalga oshirishning o'zi yetarli.
