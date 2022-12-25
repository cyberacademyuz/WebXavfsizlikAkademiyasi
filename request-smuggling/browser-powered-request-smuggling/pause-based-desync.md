# Pause-based desync

Xavfsiz ko'rinadigan saytlar, requestning o'rtasida uni pauza qilganingizdagina aniqlanuvchi yashirin desync zaifliklarini o'z ichiga olgan bo'lishi mumkin.

Agar serverlar ma'lum vaqt davomida qo'shimcha ma'lumot olmasalar, requestni to'liq deb hisoblashadi va qancha bayt kutish kerakligidan qat'iy nazar responseni chiqaradi. **Pause-based desync**-ga asoslangan zaifliklar, server requestni kechiktirsayu lekin ulanishni qayta ishlatish uchun ochiq qoldirsa yuzaga kelishi mumkin. Bu server-side va client-side-da ham desinxronlash hujumlari uchun muqobil vektor bo'lishi mumkin.

## <mark style="color:yellow;">Server tomonidagi pause-based desync</mark>  <a href="#server-side-pause-based-desync" id="server-side-pause-based-desync"></a>

Siz [<mark style="color:yellow;">CL.0</mark>](cl.0.md) ga o'xshash xatti-harakatni aniqlash uchun pause-based texnikadan foydalanishingiz mumkin, bu sizga dastlab zaiflik yo'qdek ko'ringan saytlar uchun server-side request smuggling exploitlarini yaratishga imkon beradi.

Bu quyidagi shartlarga bog'liq:

* Front-end server requestning har bir baytini to'liq requstni qabul qilishini kutmasdan, darhol back-endga yuborishi kerak.
* Front-end server requestlarni back-end serverdan oldin tugatmasligi kerak&#x20;
* O'qish vaqti tugashidan keyin server qayta foydalanish uchun ulanishni ochiq qoldirishi kerak.

Ushbu texnikaning qanday ishlashini ko'rsatish uchun keling, bir misolni ko'rib chiqaylik. Quyida standart [<mark style="color:yellow;">CL.0 request smuggling</mark>](cl.0.md) tekshiruvi keltirilgan:

```http
POST /example HTTP/1.1
Host: vulnerable-website.com
Connection: keep-alive
Content-Type: application/x-www-form-urlencoded
Content-Length: 34

GET /hopefully404 HTTP/1.1
Foo: x
```

Agar biz headerlarni zaif saytga yuborsak, nima bo'lishini ko'rib chiqing, lekin bodyni yuborishdan oldin pauza qiling.

1. Front-end headerlarni back-endga yo'naltiradi, keyin `Content-Length` headerdan kelishi kerak bo'lgan qolgan baytlarni kutishda davom etadi.
2. Birozdan keyin, requestning faqat bir qismini qabul qilgan bo'lsa ham, back-end time-out bo'ladi va response yuboradi. Ayni paytda, front-end ushbu responseni o'qishi yoki o'qimasligi mumkin va uni bizga yuborishi ham mumkin.
3. Biz nihoyat, bu holda asosiy request smuggling prefiksini o'z ichiga olgan bodyni yuboramiz.
4. Front-end server buni dastlabki requestni davomi sifatida ko'rib chiqadi va uni xuddi shu ulanish orqali back-endga yo'naltiradi.
5. Back-end server dastlabki requestga allaqachon javob bergan, shuning uchun bu baytlar boshqa requestni boshlanishi deb taxmin qiladi.

Shu yerda, biz CL.0 desync-ga erishdik, front-end/back-end ulanishni request prefiksi bilan zaharladik.

Biz, serverlar requestni saytga o‘tkazishi o‘rniga o'zlari responseni yaratishsa ko'proq himoyasiz bo‘lishi mumkinligini aniqladik.

{% hint style="success" %}
**PortSwigger tadqiqoti**

Bizning tadqiqot guruhimiz keng foydalaniladigan Apache HTTP serverida quidagi zaiflikni topdi. `/example dan /example/ ga`server-level redirecti amalga oshirilganida ushbu xatti-harakatni ko'rsatdi. Biz bu zaiflik haqida xabar berdik va u 2.4.53 versiyasida tuzatildi, shuning uchun hali Apacheni yangilamagan bo'lsangiz, uni yangilashni unutmang. Batafsil ma’lumot olish uchun “ [<mark style="color:yellow;">Browser-powered Desync Attacks: PortSwigger Research tomonidan HTTP request smugglingning yangi chegarasi</mark>](https://portswigger.net/research/browser-powered-desync-attacks#pause)” ni ko‘rib chiqing.
{% endhint %}

## <mark style="color:yellow;">Pause-based CL.0 zaifliklari uchun sinov</mark> <a href="#testing-for-pause-based-cl-0-vulnerabilities" id="testing-for-pause-based-cl-0-vulnerabilities"></a>

Burp Repeater yordamida pause-based CL.0 zaifliklarini sinab ko'rish mumkin, ammo faqat front-end server back-endning taym-outdan keyingi response-ini yaratgan paytidayoq sizga yuborsagina sinab ko'ra olasiz, lekin bu doim ham bunday bo'lmaydi. Biz sizga [<mark style="color:yellow;">Turbo Intruder</mark>](https://portswigger.net/bappstore/9abaa233088242e8be252cd4ff534988) extensionidan foydalanishni tavsiya etamiz, chunki u requestning oʻrtasida toʻxtatib turish va response olgan bo'lsangiz ham requestni davom ettirish imkonini beradi.

1. Burp Repeater-da, yuqoridagi misolda qo'llaganimizdek, CL.0 request smuggling tekshiruvini yarating, so'ng uni Turbo Intruder-ga yuboring.&#x20;

```http
POST /example HTTP/1.1
Host: vulnerable-website.com
Connection: keep-alive
Content-Type: application/x-www-form-urlencoded
Content-Length: 34

GET /hopefully404 HTTP/1.1
Foo: x
```

2\. Turbo Intruderning  Python muharriri panelida quyidagi parametrlarni o'rnatish uchun request mexanizmining konfiguratsiyasini sozlang:

```
concurrentConnections=1
requestsPerConnection=100
pipeline=False
```

3\. `queue()` Interfeysiga quyidagi argumentlarni qo'shib requestni navbatga qo'ying :

* `pauseMarker`- Turbo Intruder pauza qilishni xohlagan stringlar ro'yxati.
* `pauseTime`- millisekundlarda pauza davomiyligi.

Masalan, headerlardan keyin 60 soniya to'xtatib turish uchun so'rovni quyidagicha navbatga qo'ying:

```javascript
engine.queue(target.req, pauseMarker=['\r\n\r\n'], pauseTime=60000)
```

4\. O'zboshimchalik bilan keyingi so'rovini odatdagidek navbatga qo'ying:

```javascript
followUp = 'GET / HTTP/1.1\r\nHost: vulnerable-website.com\r\n\r\n'
engine.queue(followUp)
```

5\. Natijalar jadvalidagi barcha responselarni qayd etayotganingizga ishonch hosil qiling:

```python
def handleResponse(req, interesting): 
    table.add(req)
```

Hujumni birinchi marta boshlaganingizda, jadvalda hech qanday natijani ko'rmaysiz. Biroq, belgilangan pauza muddatidan keyin siz ikkita natijani ko'rishingiz kerak. Agar ikkinchi requestga berilgan response yashirin olib o'tish prefiksidan kutganingizga toʻgʻri kelsa (bu holda 404), bu desync muvaffaqiyatli boʻlganidan dalolat beradi.

{% hint style="info" %}
<mark style="color:yellow;">**Lab:**</mark> [Pause-based CL.0 zaifliklari uchun sinov ≫](https://portswigger.net/web-security/request-smuggling/browser/pause-based-desync/lab-server-side-pause-based-request-smuggling)
{% endhint %}

{% hint style="info" %}
**Eslatma:**

Pause-based stringlarni moslashtirishni o'rnatish uchun `pauseMarker` dan foydalanish o'rniga ofsetni belgilash uchun `pauseBefore` argumentidan foydalanishingiz mumkin. Masalan, `Content-Length`( `pauseBefore=-34`) ning teskari ofsetini belgilab, bodydan oldin pauza qilishingiz mumkin.
{% endhint %}

## <mark style="color:yellow;">Client-side pause-based desync</mark> <a href="#client-side-pause-based-desync" id="client-side-pause-based-desync"></a>

Nazariy jihatdan, pauzaga asoslangan CL.0 desinxronlashning browser tomonidagi o'zgarishni amalga oshirish mumkin bo'lishi mumkin. Afsuski, biz hali so'rov o'rtasida brauzerni pauza qilishning ishonchli usulini topa olmadik. Biroq, qilish mumkin bo'lgan vaqtinchalik bir yechim bor - bu esa active MITM hujumi.

TLS tomonidan amalga oshirilgan shifrlash MITM ishga tushganda trafikni o'qishiga to'sqinlik qilishi mumkin, ammo brauzerdan veb-serverga o'tishda TCP paketlarini kechiktirishga hech narsa to'sqinlik qilmaydi. Web-server response qaytarmaguncha ohirgi paketni kechiktirish orqali brauzer ulanishini desinxronlashtirishingiz mumkin.

Ushbu hujum, boshqa har qanday client-side desync hujumiga o'xshaydi. Foydalanuvchi zararli saytga kiradi, bu esa o'z brauzerining target saytga bir qator domenlararo requestlar yuborishiga olib keladi. Bunday holda, operatsion tizim uni bir nechta TCP paketlarga bo'lish uchun birinchi so'rovni ataylab to'ldirishingiz kerak. To'ldirishni boshqarganingizda, oxirgi paket aniq o'lchamga ega bo'lguncha so'rovni to'ldirishingiz mumkin, shuning uchun qaysi birini kechiktirishni aniqlay olasiz.

Bu praktikada qanday bo'lishini ko'rish uchun ushbu "[<mark style="color:yellow;">Browserga asoslangan desynce hujumlar: HTTP request smugglingdagi yangi chegara</mark>](https://portswigger.net/research/browser-powered-desync-attacks#mitm)" mavzusiga qarang.

## <mark style="color:yellow;">Pause-based desync zaifliklarini qanday oldini olish mumkin</mark> <a href="#how-to-prevent-pause-based-desync-vulnerabilities" id="how-to-prevent-pause-based-desync-vulnerabilities"></a>

pause-based desync zaifliklarni va desync hujumining boshqa shakllarini oldini olish uchun qilishingiz kerak bo'lgan yuqori darajadagi baʼzi chora-tadbirlarni o'rganish uchun "[<mark style="color:yellow;">HTTP request smuggling zaifliklarini qanday oldini olish mumkin</mark>](../http-request-smuggling/#qanday-qilib-http-request-smuggling-zaifliklarini-oldini-olish-mumkin)" nomli mavzuni o'qing.
