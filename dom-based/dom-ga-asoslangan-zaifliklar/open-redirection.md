# Open redirection

Biz ushbu bo'limda DOM ga asoslangan zaifliklardan biri bo'lgan Open Redirection haqida gaplashamiz, xujumlar qanday amalga oshirilishi va Open Redirection ni qanday qilib oson aniqlash haqida ham gaplashamiz.

## <mark style="color:yellow;">DOMga asoslangan Open Redirection nima ?</mark> <a href="#dom-ga-asoslangan-open-redirection-nima" id="dom-ga-asoslangan-open-redirection-nima"></a>

DOM-ga asoslangan open redirection zaifliklari haker tomonidan boshqariladigan ma'lumotlarni metodga yozganda paydo bo'ladi, bu esa domenlararo navigatsiyani ishga tushirishi mumkin. Masalan, `location.hash` xususiyatidan noto'g'ri foydalanilgani sababli havfli vaziyat yuzaga kelgan va shu tufayli quyidagi kod himoyani zaiflashtirgan:

```javascript
let url = /https?:\/\/.+/.exec(location.hash);
if (url) {
  location = url[0];
}
```

Haker ushbu zaiflikdan URL manzilini yaratish uchun foydalanishi mumkin, agar unga boshqa foydalanuvchi kirsa, avtomatik tarzda tashqi domenga yo'naltirishga olib keladi.

## <mark style="color:yellow;">DOMga asoslangan Open Redirection ta'siri qanday ?</mark> <a href="#dom-ga-asoslangan-open-redirection-tasiri-qanday" id="dom-ga-asoslangan-open-redirection-tasiri-qanday"></a>

Bu sayt foydalanuvchilariga fishing hujumlarini uyushtirishni osonlashtirishi mumkin. To'g'ri domenga mo'ljallangan ([click.uz](https://click.uz/) va [cIick.uz](https://ciick.uz/) kabi) va haqiqiy TLS sertifikati bilan (agar TLS (https) ishlatilsa) saytning haqiqiy URL manzilidan foydalanish fishing hujumini ishonchli ko'rinishiga yordam beradi, chunki ko'p foydalanuvchilar, hatto bu xususiyatlarni tekshirgan taqdirda ham, boshqa manzilga yo'naltirilgalarini sezishmaydi.

Agar Haker redirection API-ga uzatiladigan stringning boshlanishini nazorat qila olsa, bu zaiflikni JavaScript ineksiya hujumiga aylantirish mumkin. Haker URL-manzilni `javascript` bilan tuzishi mumkin: URL brauzer tomonidan qayta ishlanganida avtomatik tarzda kodni bajarish uchun psevdo-protokol ishlatiladi.

{% hint style="info" %}
<mark style="color:yellow;">**Lab:**</mark> [DOMga asoslangan Open Redirection ≫](https://portswigger.net/web-security/dom-based/open-redirection/lab-dom-open-redirection)
{% endhint %}

## <mark style="color:yellow;">Qaysi metodlar Open Redirection ni keltirib chiqaradi ?</mark> <a href="#qaysi-sink-lar-open-redirection-ni-keltirib-chiqaradi" id="qaysi-sink-lar-open-redirection-ni-keltirib-chiqaradi"></a>

Quyida umumiy DOM Open Redirection uchun ishlatilishi mumkin bo'lgan metodlarni keltiramiz:

```javascript
location
location.host
location.hostname
location.href
location.pathname
location.search
location.protocol
location.assign()
location.replace()
open()
element.srcdoc
XMLHttpRequest.open()
XMLHttpRequest.send()
jQuery.ajax()
$.ajax()
```

## <mark style="color:yellow;">Qanday qilib Open Redirection ni oldini olish mumkin ?</mark> <a href="#qanday-qilib-open-redirection-ni-oldini-olish-mumkin" id="qanday-qilib-open-redirection-ni-oldini-olish-mumkin"></a>

Bu haqida ko'proq [<mark style="color:yellow;">DOM-ga asoslangan zaifliklar</mark>](./) mavzusida aytganmiz, siz redirection manzillarini dinamik o'zgarmasligini ta'minlashingiz zarur.
