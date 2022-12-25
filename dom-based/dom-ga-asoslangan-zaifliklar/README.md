# DOM ga asoslangan zaifliklar

Biz ushbu bo'limda DOM nima ekanligini, undagi zaifliklar qandayligini va ushbu zaifliklarni qanday qilib oldini olish mumkinligi haqida o'rganamiz.

{% hint style="warning" %}
<mark style="color:yellow;">**Labaratoriyalar:**</mark>

Agar siz DOM ga asoslangan zaifliklar haqida bilsangiz pastdagi link orqali, haqiqiy web sayt kabi tuzilgan laboratoriyalarni yechishingiz mumkin.

<mark style="color:yellow;"></mark>[<mark style="color:yellow;">Labratoriyalarga o'tish ≫</mark>](https://portswigger.net/web-security/all-labs#dom-based-vulnerabilities)<mark style="color:yellow;"></mark>
{% endhint %}

## <mark style="color:yellow;">DOM nima ?</mark> <a href="#dom-nima" id="dom-nima"></a>

DOM (Document Object Model) web sahifadagi elementlarning ierarhiyasi. Veb-saytlar  nodelar va DOM obyektlarini, shuningdek ularning xususiyatlarini boshqarish uchun JavaScript-dan foydalanishlari mumkin. DOMni boshqarishning o'zi muammo emas. Aslida, bu zamonaviy veb-saytlar qanday ishlashining ajralmas qismidir.Biroq  Xavfsiz ma'lumotlarni oladigan JavaScript turli xil hujumlarni yoqishi mumkin. Qachonki web sayt hacker boshqaruvidagi ma'lumotni source kod deb bilganida va havfli funksiyalarni method deb qabul qilganida DOMga asoslangan zailiklar yuzaga keladi.

<figure><img src="../../.gitbook/assets/image (25).png" alt=""><figcaption><p>Qachonki sayt yuklanganida browser sahifaning <strong>D</strong>ocument <strong>O</strong>bject <strong>M</strong>odel ini yaratadi</p></figcaption></figure>

## <mark style="color:yellow;">Taint-Flow zaifliklar</mark> <a href="#taint-flow-zaifliklar" id="taint-flow-zaifliklar"></a>

DOM-ga asoslangan ko'plab zaifliklarni mijoz tomonidagi kod xakerlar tomonidan boshqariladigan ma'lumotlarni manipulyatsiya qilish bilan bog'liq muammolardan kelib chiqishi mumkin.

## <mark style="color:yellow;">Taint flow o'zi nima?</mark> <a href="#taint-flow-ozi-nima" id="taint-flow-ozi-nima"></a>

Ushbu zaifliklarni oldini olish yoki buzib kirish uchun, oldin Taint-flow dagi Sink lar va Source lar aslida nima ekanligi bilan tanishib chiqish zarur.

{% hint style="info" %}
**Sourcelar:**\
Source bu JavaScript xususiyati bo'lib u Haker ga ma'lumotni nazorat qilish uchun yo'l ochib beradi. Source ga misol uchun `location.search` ni keltirishimiz mumkin, chunki u query stiringidan inputlarni o'qydi, bu esa Haker ga ma'lumotni osonlik bilan nazorat qilishiga yo'l ochadi. O'zi umuman olganda har qanday xususiyat Hakerga nazorat qilish imkonini taqdim etadi. Bular havola qilish linkini (`document.referrer` orqali aniqlangan), foydalanuvchining Cookielarini (`document.cookie`) va web xabarlarni o'z ichiga oladi.
{% endhint %}

{% hint style="info" %}
**Metodlar:**

Metod DOM obektini o'zi xohlamagan amallarni bajarishiga majburlashi mumkin. Masalan, `eval()` funksiyasi metod hisoblanadi, chunki u JavaScript sifatida unga uzatiladigan argumentni qayta ishlaydi. HTML da bo'lsa `document.body.innerHTML` shunday metod hisoblanadi, chunki unga HTML kod yozib jo'natib ishga tushirish mumkin.
{% endhint %}

Asosan, DOM-ga based zaifliklar web-sayt ma'lumotlarini sourcedan metodga uzatilganda paydo bo'ladi, keyin esa client session kontekstida ma'lumotlarni xavfli tarzda qayta ishlaydi.

Eng ko'p duch kelinadigan zaifliklarni hosil qiluvchi source va metod bu `location` obyekti. Chunki bu xususiyat bilan haker foydalanuvchiga o'zining payloadi yuklangan web sahifasinimg linkini jo'natishi mumkin. Quyida shunday zaiflikni keltirib chiqaruvchi kodni ko'rishingiz mumkin:

```javascript
goto = location.hash.slice(1)
if (goto.startsWith('https:')) {
  location = goto;
}
```

Bu kod <mark style="color:yellow;">DOM ga asoslangan open redirection</mark> zaifligini keltirib chiqaradi, chunki `location.hash`xavfsiz bo'lmagan usulda ishlatilmoqda. Agar URL, hash fragmenti hisoblangan `https:` bilan boshlansa u yangi oynani ochadi va web saytga redirect qilib yuboradi. Quyida misolda kabi:

```url
https://www.innocent-website.com/example#https://www.evil-user.net
```

Qachonki ushbu linkka foydalanuvchi kirsa location obekti uni [https://www.evil-user.net](https://www.evil-user.net) ga yo'naltiradi, ya'ni zararli saytni foydalanuvchiga avtomatik tarzda yuboradi. Misol uchun bu holat Phishing xujumini amalga oshirish uchun qulay sharoit yaratadi.

## <mark style="color:yellow;">Umumiy sourcelar</mark> <a href="#umumiy-manbalar" id="umumiy-manbalar"></a>

Quyida keltiriladigan sourcelar xilma xil taint-flow zaifliklar uchun qulay hisoblanadi:

```javascript
document.URL
document.documentURI
document.URLUnencoded
document.baseURI
location
document.cookie
document.referrer
window.name
history.pushState
history.replaceState
localStorage
sessionStorage
IndexedDB (mozIndexedDB, webkitIndexedDB, msIndexedDB)
Database
```

Quyida keltiradigan ma'lumotlarimiz ham taint-flow zaifligiga oid hisoblanadi:

* <mark style="color:yellow;"></mark>[<mark style="color:yellow;">Reflected ma'lumot</mark> ](../../xss/domga-asoslanga-xss.md#stored-va-reflected-malumotlar-bilan-biriktirilgan-dom-xss) <mark style="background-color:green;">Lab</mark>&#x20;
* <mark style="color:yellow;"></mark>[<mark style="color:yellow;">Stored ma'lumot</mark>](../../xss/domga-asoslanga-xss.md#stored-va-reflected-malumotlar-bilan-biriktirilgan-dom-xss) <mark style="background-color:green;">Lab</mark>&#x20;
* <mark style="color:yellow;">Web xabarlar</mark>  <mark style="background-color:green;">Lab</mark>&#x20;

## <mark style="color:yellow;">Qaysi metodlar DOM based zaifliklariga sabab bo'la oladi ?</mark>

Quyidagi ro'yxat keng tarqalgan DOM based zaifliklarning qisqacha ko'rinishini va har bir sabab bo'lishi mumkin bo'lgan metodlarni taqdim etadi. Tegishli metodlarning ko'proq ro'yxati uchun quyidagi havolalarni bosish orqali zaiflikka oid sahifalarga kiring.

| DOM ga asoslangan zaifliklar                                                                                                                                                       | Metodlar                 |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------ |
| <mark style="color:yellow;"></mark>[<mark style="color:yellow;">DOM XSS</mark>](../../xss/cross-site-scripting.md#dom-based-xss) <mark style="background-color:green;">Lab</mark>  | document.write           |
| <mark style="color:yellow;">Open Redirection</mark> <mark style="background-color:green;">Lab</mark>                                                                               | window.location          |
| <mark style="color:yellow;">Cookie manipulation</mark> <mark style="background-color:green;">Lab</mark>                                                                            | document.cookie          |
| <mark style="color:yellow;">JavaScript Injection</mark>                                                                                                                            | eval()                   |
| <mark style="color:yellow;">Document-domain manipulation</mark>                                                                                                                    | document.domain          |
| <mark style="color:yellow;">WebSocket-URL Poisoning</mark>                                                                                                                         | WebSocket()              |
| <mark style="color:yellow;">Link manipulation</mark>                                                                                                                               | element.src              |
| <mark style="color:yellow;">WebMessage Manipulation</mark>                                                                                                                         | postMessage()            |
| <mark style="color:yellow;">Ajax request-header manipulation</mark>                                                                                                                | setRequestHeader()       |
| <mark style="color:yellow;">Local file-path manipulation</mark>                                                                                                                    | FileReader.readAsText()  |
| <mark style="color:yellow;">Client-side SQL injection</mark>                                                                                                                       | ExecuteSql()             |
| <mark style="color:yellow;">HTML5-storage manipulation</mark>                                                                                                                      | sessionStorage.setItem() |
| <mark style="color:yellow;">Client-side XPath injection</mark>                                                                                                                     | document.evaluate()      |
| <mark style="color:yellow;">Client-side JSON injection</mark>                                                                                                                      | JSON.parse()             |
| <mark style="color:yellow;">DOM-data manipulation</mark>                                                                                                                           | element.setAttribute()   |
| <mark style="color:yellow;">Denial of service</mark>                                                                                                                               | RegExp()                 |

## <mark style="color:yellow;">Qanday qilib DOMga asoslangan taint-flow zaifliklarni oldini olish mumkin</mark> <a href="#qanday-qilib-dom-ga-asoslangan-taint-flow-zaifliklarni-oldini-olish-mumkin" id="qanday-qilib-dom-ga-asoslangan-taint-flow-zaifliklarni-oldini-olish-mumkin"></a>

DOM-ga asoslangan hujumlar tahdidini butunlay yo'q qilish uchun siz qila oladigan yagona harakat mavjud emas. Biroq, umuman olganda, DOM-ga asoslangan zaifliklarni oldini olishning eng samarali usuli har qanday ishonchsiz source dan olingan ma'lumotlarning har qanday metodga uzatiladigan qiymatni dinamik ravishda o'zgartirishiga yo'l qo'ymaslikdir.

Agar saytning funksionalligi bu xatti-harakatning muqarrarligini anglatsa, u holda, ximoya client kodida amalga oshirilishi kerak. Ko'pgina hollarda, tegishli ma'lumotlar whitelist asosida tasdiqlanishi mumkin, faqat xavfsiz ekanligi ma'lum bo'lgan tarkibga ruxsat beriladi. Boshqa hollarda, ma'lumotlarni o'chirib tashlash yoki kodlash kerak bo'ladi. Bu murakkab vazifa bo'lishi mumkin va ma'lumotlar kiritilishi kerak bo'lgan kontekstga qarab, tegishli ketma-ketlikda, **JavaScript-dan qochish**, **HTML kodlash** va **URL kodlash** kombinatsiyasini o'z ichiga olishi mumkin.

Muayyan zaifliklarning oldini olishda kerakli chora-tadbirlar uchun yuqoridagi jadvalda ko'rsatilgan zaifliklar sahifalarini ko'ring.

## DOM Clobbering <a href="#dom-clobbering" id="dom-clobbering"></a>

**DOM clobbering** - bu DOMni manipulyatsiya qilish va natijada web-saytdagi JavaScript xatti-harakatlarini o'zgartirish uchun sahifaga HTML kiritadigan ilg'or usuldir. DOM blokirovkasining eng keng tarqalgan shakli global o'zgaruvchining ustiga yozish uchun elementdan foydalanish, keyinchalik u web sayt tomonidan dinamik skript URL-manzilini yaratish kabi xavfli tarzda ishlatiladi.

{% hint style="info" %}
**Ko'proq o'qish:**

[Dom clobbering ☰](./#dom-clobbering)
{% endhint %}
