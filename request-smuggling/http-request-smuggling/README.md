# HTTP request smuggling

Bu bo'limda HTTP Request smuggling xujumlari va request smuggling zaifliklar qanday paydo bo'lishi haqida gaplashamiz.

<figure><img src="../../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
<mark style="color:yellow;">**Labaratoriyalar:**</mark>

Agar siz Request smuggling zaifliklari haqida bilsangiz pastdagi link orqali, haqiqiy web sayt kabi tuzilgan laboratoriyalarni yechishingiz mumkin.\ <mark style="color:yellow;"></mark>[<mark style="color:yellow;">Barcha Request smuggling labaratoriyalarini ko'rish ≫</mark>](https://portswigger.net/web-security/all-labs#http-request-smuggling)<mark style="color:yellow;"></mark>
{% endhint %}

## <mark style="color:yellow;">HTTP Request Smuggling nima ?</mark> <a href="#http-request-smuggling-nima" id="http-request-smuggling-nima"></a>

HTTP Request Smuggling bu web-saytning bir yoki bir nechta foydalanuvchilardan olingan HTTP requestlarning ketma-ketligiga xalaqt berish uchun bir usul. Request smuggling xavfli hisoblanadi, hakerga xavfsizlik protokollaridan aylanib o'tish, ruxsat berilmagan maxfiy ma'lumotlarga kirish imkonini beradi va web saytdagi boshqa foydalanuvchilarga ta'sir o'tkazadi.

{% hint style="info" %}
**Eslatma:**

HTTP Request Smuggling birinchi marta 2005-yilda aniqlangan, va yana [PortSwigger izlanishlari](https://portswigger.net/research/http-desync-attacks-request-smuggling-reborn)dan so'ng yana qayta ommalashdi.
{% endhint %}

## <mark style="color:yellow;">HTTP Request Smuggling xujumida nimalar sodir bo'ladi ?</mark> <a href="#http-request-smuggling-xujumida-nimalar-sodir-boladi" id="http-request-smuggling-xujumida-nimalar-sodir-boladi"></a>

Bugungi kunda websaytlar foydalanuvchi va website logikasi o'rtasida HTTP serverlar zanjiridan foydalanmoqda. Foydalanuvchilar Frontend qismga so'rov yuborishadi (bular ba'zida **load balancer** yoki **reverse proxy** deb nomlanadi) va server ushbu so'rovni bitta backend tizimga yoki bir nechta backend tizimlarga yuboradi. Ushbu arxitektura rivojlanishda davom etmoqda va ba'zi holatlarda bularsiz tasavur qilib bo'lmas bo'lib qolayapti ayniqsa cloud-modern web saytlarda.

Qachonki frontend qism, backend ga HTTP so'rov yuborsa qulay va samarali bo'lishi uchun bitta tarmoqdan foydalanadi. Protokol judayam oddiy: HTTP so'rovlar bittasidan keyin keyingisi yuboriladi va HTTP so'rovni tahlil qiluvchi ularning headerlariga qarab qay biri tugagani va boshlanganini qabul qiladi:

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

Bunday vaziyatda front-end va backend qismlar, requestlar orasidagi chegaralarni kelishib olishlari juda muhimdir. Bo'lmasa haker har xil noaniq so'rovlarni frontend va backend orasida jo'natadi:

<figure><img src="../../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

Bu yerda haker o'zini front-end qismdagi requestining bir qismini backend server tomonidan keyingi so'rovning boshlanishi sifatida yuborishiga olib keladi. Bu keyingi so'rovga samarali qo'shiladi va web saytning so'rovlarni boshlanish va oxirini ajratish holatiga ta'sir ko'rsatadi. Bu Request Smuggling deb ataladi va ba'zida uning oqibatlari ayanchli bo'ladi.

## <mark style="color:yellow;">HTTP Request Smuggling zaifliklari qanday paydo bo'ladi ?</mark> <a href="#qanday-qilib-http-request-smuggling-zaifliklari-vujudga-keladi" id="qanday-qilib-http-request-smuggling-zaifliklari-vujudga-keladi"></a>

HTTP request smuggling zaifligi - HTTP spetsifikatsiyasi so'rovning qayerda tugashini belgilash uchun ikki xil usulini ta'minlaganligi uchun yuzaga keladi, Bular: `Content-Length` header va `Transfer-Encoding` header.

```http
POST /search HTTP/1.1
Host: normal-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 11

q=smuggling
```

Request **chunked** encodingdan foydalansa `Transfer-Encoding` headeri ishlatiladi. Bu degani so'rov tanasi bir va undan ko'p bo'laklarga bo'lingan holda jo'natiladi. Har bir bo'lak bo'laklangan baytlardan iborat (o‘n oltilik sistemadan foydalangan holda) bo'lib undan keyin, yangi qator va undan keyin bo'lak tarkibidan iborat. Habar nol hajmdagi bo'lak bilan tugaydi. Masalan:&#x20;

```http
POST /search HTTP/1.1
Host: normal-website.com
Content-Type: application/x-www-form-urlencoded
Transfer-Encoding: chunked

b
q=smuggling
0
```

{% hint style="info" %}
**Eslatma:**

Ko'plab pentesterlar chunked encoding, HTTP so'rovlarda ishlatilishi haqida bilishmaydi. Buning 2 ta sababi bor:&#x20;

* Burp Suite HTTP habarni ko'rishga va o'zgartirishga oson bo'lishligi uchun chunked encoding ni avtomatik tarzda unpack qiladi.
* Brauzerlar avtomatik tarzda chunked encoding dan foydalanmaydi va buni faqatgina server tomondan ko'rish mumkin.
{% endhint %}

HTTP spetsifikatsiyasi HTTP xabarlarining uzunligini belgilashning ikki xil usulini taqdim etganligi sababli, bitta xabar bir vaqtning o'zida ikkala usulni ham ishlatishi mumkin, shunda ular bir-biri bilan to'qnashadi. HTTP spetsifikatsiyasi ushbu muammoni oldini olishga harakat qiladi, agar `Content-Length` va `Transfer-Encoding` headerlari mavjud bo'lsa, unda `Content-Length` headeri bekor qilinadi. Balki ushbu harakat serverdagi noaniqlikni oldini olar, ammo bu faqat bitta server uchungina ishlaydi. Shunaqa vaziyatlarda ikkita sababga ko'ra muammolar paydo bo'ladi:

* Ba'zi serverlar `Transfer-Encoding` headerini qo'llab-quvvatlamaydi.
* `Transfer-Encoding` headerini qo'llab-quvvatlaydigan ba'zi serverlar, agar header qandaydir tarzda obfuskatsiya qilingan bo'lsa, uni qayta ishlamasliklari mumkin.

Agar frontend va backend serverlari `Transfer-Encoding` sarlavhasiga nisbatan boshqacha harakat qilsalar, (ehtimol obfuskatsiya qilingan) ular ketma-ket so'rovlar o'rtasidagi chegaralar borasida kelishmovchiliklar keltirib chiqarishi mumkin, bu esa reequest smuggling zaifliklarga olib keladi.

## <mark style="color:yellow;">Qanday qilib HTTP Request Smuggling xujumini amalga oshirish kerak ?</mark> <a href="#qanday-qilib-http-request-smuggling-xujumini-amalga-oshirish-kerak" id="qanday-qilib-http-request-smuggling-xujumini-amalga-oshirish-kerak"></a>

Request Smuggling xujumlari `Content-Length` va `Transfer-Encoding` headerlarini o'zgartirib bitta HTTP so'rov ichiga joylashtirib frontend va backend orasida so'rovni har xil ishlashga olib kelishni o'z ichiga oladi. Buni amalga oshirishning puxta usuli ikkita serverning xatti-harakatiga bog'liq:

* CL.TE: Frontend server `Content-Length` headeridan foydalanadi va Backend serveri esa `Transfer-Encoding` headeridan foydalanadi.
* TE.CL: Frontend server `Transfer-Encoding` headeridan foydalanadi va Backend server esa `Content-Length` headeridan foydalanadi.
* TE.TE: ikkala server ham `Transfer-Encoding` dan foydalanadi, ammo serverlardan biri ushbu headerni obsfuskatsiya qilingan holda qabul qiladi.

## <mark style="color:yellow;">CL.TE zaifliklari</mark> <a href="#clte-zaifliklar" id="clte-zaifliklar"></a>

Bu yerda Frontend server `Content-Length` headeridan foydalanadi va Backend serveri esa `Transfer-Encoding` headeridan foydalanadi. Biz bu holatda quyidagicha HTTP Request Smuggling qilishimiz mumkin:

```http
POST / HTTP/1.1
Host: vulnerable-website.com
Content-Length: 13
Transfer-Encoding: chunked

0

SMUGGLED
```

Frontend server `Content-Length` nI qayta ishlaydi va  request tanasi `SMUGGLED` gacha 13 bayt uzunlikda ekanligini aniqlaydi. Ushbu so'rov esa backend ga yo'naltiriladi. Back-end server `Transfer-Encoding` headeri asosida uni qayta ishlaydi va requestda chunked encoding dan foydalandi. U nol uzunlikdagi deb ko'rsatilgan birinchi bo'lakni qayta ishlaydi va requestni  tugatish sifatida qabul qilinadi. Shunda `SMUGGLED` qayta ishlanmay qolib ketadi va backend ga yuboriladi, backend esa uni keyingi so'rovning boshlanishi deb qabul qiladi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [HTTP request smuggling, Boshlang'ich CL.TE zaifligi ≫](https://portswigger.net/web-security/request-smuggling/lab-basic-cl-te)
{% endhint %}

## <mark style="color:yellow;">TE.CL zaifliklari</mark> <a href="#tecl-zaifliklari" id="tecl-zaifliklari"></a>

Bu yerda Frontend  `Transfer-Encoding` headeridan foydalanadi va Backend esa `Content-Length` headeridan foydalanadi. Biz bu holatda quyidagicha HTTP Request Smuggling qilishimiz mumkin:

```http
POST / HTTP/1.1
Host: vulnerable-website.com
Content-Length: 3
Transfer-Encoding: chunked

8
SMUGGLED
0
```

{% hint style="info" %}
**Eslatma:**

Ushbu so'rovni Burp Repeaterdan orqali yuborishingiz uchun, Repeater menusidan "Update Content-Length" xususiyatini o'chirishingiz zarur.

0 gacha `\r\n\r\n` ketma-ketligini kiritishingiz kerak.&#x20;
{% endhint %}

Frontend server `Transfer-Encoding` headeri asosida uni qayta ishlaydi va requestda chunked encoding dan foydalandi. U 8 bayt uzunlikdagi birinchi qismni `SMUGGLED` dan keyingi qatorning boshigacha qayta ishlaydi. U 0 uzunlik deb ko'rsatilgan ikkinchi bo'lakni qayta ishlaydi va shuning uchun so'rovni tugatish sifatida qabul qilinadi. Ushbu so'rov backend serveriga yuboriladi.

Back-end server `Content-Length` headerini qayta ishlaydi va request tanasining uzunligi 8 baytdan keyingi qatorning boshigacha 3 bayt ekanligini aniqlaydi. `SMUGGLED` bilan boshlanadigan keyingi baytlarga ishlov berilmaydi va backend serveri ularni navbatdagi so'rovning boshlanishi sifatida qabul qiladi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [HTTP request smuggling, boshlang'ich TE.CL zaifligi ≫](https://portswigger.net/web-security/request-smuggling/lab-basic-te-cl)
{% endhint %}

## <mark style="color:yellow;">TE.TE hatti-xarakatlari: TE headerini obfuskatsiya qilish</mark> <a href="#tete-hatti-xarakatlari-te-headerini-xiralashishi" id="tete-hatti-xarakatlari-te-headerini-xiralashishi"></a>

Bu yerda ikkala server ham `Transfer-Encoding` dan foydalanadi, ammo serverlardan biri ushbu headerni obfuskatsiyalangan holda qabul qiladi.

`Transfer-Encoding` headerini obfuskatsiyalashni juda ko'p usullari mavjud. Masalan:

```http
Transfer-Encoding: xchunked

Transfer-Encoding : chunked

Transfer-Encoding: chunked
Transfer-Encoding: x

Transfer-Encoding:[tab]chunked

[space]Transfer-Encoding: chunked

X: X[\n]Transfer-Encoding: chunked

Transfer-Encoding
: chunked
```

Ushbu keltirilgan barcha misollar HTTP spesifikatsiyasidan chiqishni o'z ichiga oladi. Protokol spetsifikatsiyasini amalga oshiradigan asl kod kamdan-kam hollarda unga to'g'ri amal qiladi va turli web saytlar uchun spetsifikatsiyadan turli xil o'zgarishlarni qabul qilish odatiy holdir. TE.TE zaifligini aniqlash uchun `Transfer-Encoding` headerining ba'zi o'zgarishlarini topish kerak, shunda faqat front-end yoki backend serverlaridan biri uni qayta ishlaydi, boshqa server esa buni bekor qiladi.

Front-end yoki back-end server obfuskatsiyakangan `Transfer-Encoding` headerini qayta ishlamasligiga, xujumning qolgan qismi biz yuqorida tariflagan CL.TE yoki TE.CL zaifliklari bilan bir xil tarzda bo'ladi

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [HTTP request smuggling, TE headerini obfuskatsiya qilish ≫](https://portswigger.net/web-security/request-smuggling/lab-obfuscating-te-header)
{% endhint %}

## <mark style="color:yellow;">HTTP Request Smuggling zaifliklarini qanday aniqlash mumkin ?</mark> <a href="#qanday-qilib-http-request-smuggling-zaifliklarini-aniqlash-mumkin" id="qanday-qilib-http-request-smuggling-zaifliklarini-aniqlash-mumkin"></a>

Biz HTTP Request Smuggling zaifliklarini qanday qilib aniqlash mumkinligi haqida quyida keltirgan bir qancha maslahatlarimizni ko'rib chiqishing. Biz yana bir qancha  laboratoriyalarni ham berganmiz, siz ularni yechib HTTP Request Smuggling qanday ishlashi haqida kengroq tushunchaga ega bo'lasiz.

{% hint style="info" %}
**Ko'proq o'qish:**

[HTTP request smuggling zaifliklarini topish ☰](topish.md)
{% endhint %}

## <mark style="color:yellow;">HTTP Request Smuggling zaifliklarini qanday exploit qilish mumkin ?</mark> <a href="#qanday-qilib-http-request-smuggling-zaifliklarini-exploit-qilish-mumkin" id="qanday-qilib-http-request-smuggling-zaifliklarini-exploit-qilish-mumkin"></a>

Endi siz **HTTP Request Smuggling**ning asosiy tushunchalaridan xabaringiz bor, endi siz bu bilimlarni xujumlar uyushtirish uchun ishlatishingiz zarur. Har doimgidek bizda  to'liq interaktiv laboratoriyalar bor, va siz buni amalda qanday ishlashini ko'rishingiz mumkin.

{% hint style="info" %}
**Ko'proq o'qish:**

[HTTP reques smuggling zaifliklaridan foydalanish ☰](foydalanish.md)
{% endhint %}

## <mark style="color:yellow;">Yuqori darajali HTTP Request Smuggling</mark> <a href="#murakkab-http-request-smuggling" id="murakkab-http-request-smuggling"></a>

Agar siz biz bergan laboratoriyalarni tugatgan bo'lsangiz, endi bir qancha murakkab usullarni o'rganishga tayyorsiz. Biz oxirgi PortSwigger izlanishlariga asoslanib yana bir nechta interaktiv laboratoriyalarni tayyorlaganmiz. Sizda Burp ning HTTP/2 ga asoslangan pentestlarini amalga oshirish imkoniyati bor.

{% hint style="info" %}
**Ko'proq o'qish:**

[Yuqori darajali HTTP request smuggling zaifliklari ☰](../advanced-request-smuggling/)
{% endhint %}

## <mark style="color:yellow;">Browser yordamida request smuggling</mark>

Siz hozirgacha oʻrgangan request smuggling usullari Burp Repeater kabi maxsus xakerlik vositalaridan foydalangan holda ataylab notoʻgʻri soʻrovlar yuborishga asoslanadi. Aslida, `Content-Length` headeri yordamida ikkita serverni sinxronizatsiya qiluvchi brauzerga to'liq mos keladigan so'rovlar orqali bir xil hujumlarni amalga oshirish mumkin.

Shu tarzda, siz hatto ushbu hujumlarning browser tomonidagi o'zgaruvchanligini ham ishga tushirishingiz mumkin, bu esa foydalanuvchining brauzerini zaif veb-saytga o'z ulanishini zararlash uchun aldaydi. Bu nafaqat bitta serverli saytlarni request smuggling uslubidagi hujumlarga duchor qiladi, balki siz to'g'ridan-to'g'ri kirish imkoniga ega bo'lmagan saytlarga ham hujum qilishingiz mumkin. Buni qanday qilishni o'rganish uchun o'quv materiallar va labaratoriyalar bilan tanishib chiqing

{% hint style="info" %}
**Ko'proq o'qish:**\
[Browser yordamida request smuggling ☰](../browser-powered-request-smuggling/)
{% endhint %}

## <mark style="color:yellow;">HTTP Request Smuggling zaifliklarini qanday oldini olish mumkin ?</mark> <a href="#qanday-qilib-http-request-smuggling-zaifliklarini-oldini-olish-mumkin" id="qanday-qilib-http-request-smuggling-zaifliklarini-oldini-olish-mumkin"></a>

Frontend va Backend so'rovlar o'rtasidagi chegarani aniqlash uchun har xil mexanizmlardan foydalanganida **HTTP Request Smuggling** zaifligi paydo bo'ladi. Bu, request qayerda tugashini aniqlashda, `HTTP/1` serverlar `Content-Length` headeridan yoki `Transfer-Encoding`da `chunked`dan foydalanishidagi tafovutlar tufayli bo'lishi mumkin. `HTTP/2` muhitida, backend uchun <mark style="color:yellow;">HTTP/2 requestlarini downgrade</mark> qilish muammolarni yanada ko'paytiradi va qo'shimcha hujumlarni yuzaga keltiradi yoki ularni osonlashtiradi.

HTTP Request Smugglingni oldini olish uchun, biz quyida bir qancha maslahatlarni beramiz:

* Har doim HTTP/2 protokolidan foydalaning va uni downgrade qilish imkoniyatlarini o'chirib qo'ying. HTTP/2 so'rovlar uzunligi aniqlash uchun mustahkam  mexanizmdan foydalanadi va doim HTTP/2 dan foydalanishlik HTTP Request Smuggling ni oldini oladi. Agar siz HTTP downgradeni o'chira olmasangiz, unda  HTTP/1.1 spetsifikatsiyasiga qarshi qayta yozilgan requestni tasdiqlaganingizga amin bo'ling.
* Front-end serverni noaniq so'rovlarni tartiblashga va backend serverni  esa har qanday noaniq so'rovni rad qilishiga majburlang, bu jarayonda TCP ulanishni yopadi.
* Hech qachon [requestlarning tanasi](https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrI3bnq4ApxRcW4MU3hFQ%2Fuploads%2F83bMoIZiTjb7LYjKTVBA%2Fimage.png?alt=media\&token=7feb01ab-ebd9-4f40-8a43-ad3b98011f8e) yo'q deb o'ylamang. Bu  CL.0 va client-side desynce zaifligining asosiy sababi.
* Standart tarzda, requestlarni qayta ishlash jarayonida server darajasidagi istisnolar o'rnatilsa, ulanish uziladi.
* Agar siz proksi-server orqali trafikni yo'naltirayotgan bo'lsangiz, iloji boricha yuqori oqim HTTP/2 yoqilganligiga ishonch hosil qiling.

O‘quv materiallarida ko‘rsatib o‘tganimizdek, backend ulanishlardan qayta foydalanishni o‘chirib qo‘yish ma’lum turdagi hujumlarni yumshatishga yordam beradi, ammo bu baribir sizni [<mark style="color:yellow;">request tunnelling</mark>](../advanced-request-smuggling/request-tunelling.md) hujumlaridan himoya qilmaydi.

{% hint style="info" %}
**Ko'proq o'qish:**

[Burp Suite web zaiflik skaneridan foydalanib HTTP request smuggling zaifliklarini topish ☰](https://portswigger.net/burp/vulnerability-scanner)
{% endhint %}
