# DOMga asoslanga XSS

<figure><img src="../.gitbook/assets/reflected-xss(1).png" alt=""><figcaption></figcaption></figure>

Ushbu bo'limda biz DOM ga asoslangan XSS (DOM XSS) nimaligi, zaifliklarri haqida va qanday qilib exploit qilish mumkinligi haqida gaplashamiz.

## <mark style="color:yellow;">DOM ga asoslangan XSS nima?</mark> <a href="#dom-ga-asoslangan-cross-site-scripting-nima" id="dom-ga-asoslangan-cross-site-scripting-nima"></a>

DOM ga asoslanagn XSS zaifliklari odatda JavaScript funksiyalari haker tomonidan nazorat qilinishi mumkin bo'lgan source lardan ma'lumot qabul qilganda paydo bo'ladi, masalan URL orqali yoki  eval() va innerHTML() kabi dinamik kod bajarilishini amalga oshiradigan funksiyalar orqali. Bu esa Hakerga boshqa foydalanuvchilarning hisob raqamlarini o'g'irlashga imkon beruvchi zararli JavaScript skriptini ishga tushirish imkonini beradi.

DOM ga asoslangan XSS ni amalga oshirishingiz uchun siz funksiyaga bog'langan JavaScriptni ruhsatsiz ishga tushiruvchi sourcega ma'lumotni joylashitirishingiz kerak.

DOM XSS uchun eng keng tarqalgan usul odatda `window.location`ob'ekt bilan kirish mumkin bo'lgan URL manzilidir. Haker jabrlanuvchiga query satri va fragment qismlariga payload yuklangan URL yaratib, jo'natishi mumkin. Muayyan holatlarda, masalan, 404 sahifa yoki PHP bilan ishlaydigan web-saytni nishonga olganda, payload ham path ga joylashtirilishi mumkin.

Source va funksiyalar orasidagi taint-flow zaifliklari haqida yanada to'liqroq ma'lumot olish uchun <mark style="color:yellow;">DOM ga asoslangan zaifliklar</mark> sahifasiga o'ting.

## <mark style="color:yellow;">Qanday qilib DOM ga asoslangan XSS ni test qilish mumkin?</mark> <a href="#qanday-qilib-dom-ga-asoslangan-xss-ni-test-qilish-mumkin" id="qanday-qilib-dom-ga-asoslangan-xss-ni-test-qilish-mumkin"></a>

DOM ga asoslangan XSS zaifliklarni asosiy qismi Burp Suite ning zaifliklar skaneri yordamida ishonchli hamda tezda topiladi. DOM ga asoslangan XSSni qo'lda test qilishingiz uchun sizga Developer Tools ga ega bo'lgan brauzer kerak bo'ladi, masalan Chrome. Siz har bir source ni test qilib chiqishingiz zarur.

### <mark style="color:yellow;">HTML methodlarida sinovdan o'tkazish</mark> <a href="#html-sink-larini-test-qilish" id="html-sink-larini-test-qilish"></a>

DOM XSS ni HTML methodlari orqali test qilish uchun alphanumeric paylloadni URLga joylashtiring (masalan location.search) keyin siz kiritgan ma'lumot qayerda paydo bo'lishini Developer tools orqali HTML dan qidiring. Esda tutingki Brauzerning "View Source" optsiyasi DOM XSS uchun ishlamaydi chunki u JavaScript orqali HTML da o'zgartirilgan hisoblarni o'z ichiga qabul qilmaydi. Chrome ning Developer tools da DOM dan payloadingizni qidirish uchun `Control+F`dan foydalanishingiz mumkin.

DOM ichida kiritgan ma'lumotingiz paydo bo'ladigan har bir joy uchun kontekstni aniqlashingiz kerak. Shu kontekstga asoslanib siz kiritgan ma'lumotlaringiz qanday ishlov berilishini ko'rish uchun ma'lumotlarni aniqlashitirishingiz zarur. Misol uchun, agar siz kiritgan so'z qo'shtirnoq ichida paydo bo'lsa, atributdan tashqariga chiqish mumkinligini tekshirish uchun satringizga qo'sh tirnoqlarni kiritishga harakat qiling.

Shuni esda tutingki Brauzerlar URL encoding ga nisbatan har xil amallarni amalga oshirishadi. Chrome, Firefox va Safari `location.search` va `location.hash`ni [<mark style="color:yellow;">URL encoding</mark>](https://www.w3schools.com/tags/ref\_urlencode.ASP) <mark style="color:yellow;"></mark> qiladi, IE11 va Microsoft Edge bu URLlarnilarni URL encode qilmaydi. Agarda ma'lumotlaringiz ishlov berilishidan oldin URL encode qilinsa unda XSS xujumi ish bermaydi.

### <mark style="color:yellow;">JavaScriptni bajaradigan  methodlarni tekshirish</mark> <a href="#javascript-ni-bajaradigan-sink-larni-tekshirish" id="javascript-ni-bajaradigan-sink-larni-tekshirish"></a>

DOM XSS uchun JavaScript-ni bajaradigan methodlarni tekshirish biroz qiyinroq. Bu funksiyalar bilan siz kiritgan ma'lumot DOM da paydo bo'lmasligi mumkin va siz uni qidirib topa olmaysiz. Buni o'rniga siz kiritgan ma'lumot funksiyaga qanday yuborilganini aniqlash uchun siz JavaScript debugger dan foydalanishingiz zarur.&#x20;

Har bir potensial funksiya uchun, masalan location uchun siz birinchi sahifaning JavaScript kodi qanday qilib kodga yetkazilayotganini aniqlashingiz kerak. Chrome ning Developer Tools da siz `Control+Shift+F` orqali barcha sahifaning source kod uchun ishlatilgan JavaScript kodlarini ko'ra olasiz.

O'qilayotgan source kodni topganingizdan keyin siz JavaScript debugger dan foydalanib break point qo'yishingiz va source koddagi qiymat qanday ishlatilganini o'rganishingiz kerak. Source  boshqa o'zgaruvchilarga tayinlanganligini bilib olishingiz mumkin. Agar shunday bo'lsa, ushbu o'zgaruvchilarni kuzatish va ular funksiyaga uzatilganligini tekshirish uchun qidiruv funksiyasidan yana foydalanishingiz kerak bo'ladi. Source dan olingan ma'lumotlar tayinlanayotgan methodni topganingizda, o'zgaruvchiga sichqonchani yuborishdan oldin uning qiymatini ko'rsatish uchun kursorni o'zgartirish orqali qiymatni tekshirish uchun debugger dan foydalanishingiz mumkin. Keyin, HTML methodlarida bo'lgani kabi, muvaffaqiyatli XSS hujumini amalga oshirishning iloji bor yo'qligini bilish uchun ma'lumotingizni aniqlashtirishingiz kerak.

### <mark style="color:yellow;">DOM XSS ni DOM Invader yordamida test qilish</mark> <a href="#dom-xss-ni-dom-invader-yordamida-test-qilish" id="dom-xss-ni-dom-invader-yordamida-test-qilish"></a>

DOM XSS ni topish va uni exploit qilish tashqaridan qaraganda juda zerikarli protses bo'lib, siz minifikatsiya qilingan JavaScript fayllarini o'qishingizga ham to'g'ri kelishi mumkin. Agar siz Burp ning brauzeridan foydalansangiz unda sizga DOM Invader extensioni yordam berib ko'pgina ishingizni yengillashtiradi.

{% hint style="info" %}
**Ko'proq o'qish:**[\
DOM Invader Documentation ☰](https://hackthebrain.gitbook.io/burpsuite-uchun-qollanma/burp-toollari-haqida-organish-tez-kunda/dom-invader/dom-invader-ni-yoqish)
{% endhint %}

## <mark style="color:yellow;">DOM XSSni har xil source lar va methodlar lar bilan exploit qilish</mark> <a href="#dom-xss-ni-har-xil-source-lar-va-sink-lar-bilan-exploit-qilish" id="dom-xss-ni-har-xil-source-lar-va-sink-lar-bilan-exploit-qilish"></a>

Printsipial jihatdan, agar ma'lumotlar source dan methodga o'tishi mumkin bo'lgan bajariladigan yo'l mavjud bo'lsa web-sayt DOM-ga asoslangan XSSga himoyasiz bo'lib qoladi. Amalda, turli source lar va methodlar exploitga ta'sir qilishi mumkin bo'lgan turli xil xususiyatlar va xatti-harakatlarga ega va qanday texnikalar zarurligini aniqlaydi. Bundan tashqari, web-sayt skriptlari zaiflikdan foydalanishga urinishda moslashtirilishi kerak bo'lgan ma'lumotlarni tekshirishi yoki boshqa qayta ishlashni amalga oshirishi mumkin. DOM-ga asoslangan zaifliklarga tegishli bo'lgan turli xil methodlar mavjud. Tafsilotlar uchun quyidagi[ <mark style="color:yellow;">roʻyxatga qarang</mark>.](domga-asoslanga-xss.md#qaysi-sink-lar-dom-xss-zaifliklariga-sabab-boladi)

document.write methodi skript elementlari bilan ishlaydi, shuning uchun siz quyidagi kabi oddiy payloaddan foydalanishingiz mumkin:

```html
document.write('... <script>alert(document.domain)</script> ...');
```

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [`location.search` qiymatidan foydalanib `document.write` methodiga DOM XSS ≫](https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-document-write-sink)
{% endhint %}

Ammo shuni yodda tutingki, ba'zi hollarda `document.write-`ga yozilgan tarkibda siz exploit qilishda hisobga olishingiz kerak bo'lgan ba'zi bir jihadlar mavjud. Masalan, JavaScript payloadidan foydalanishdan oldin ba'zi mavjud elementlarni yopishingiz kerak bo'lishi mumkin.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark>**  **  [Select elementining ichida `location.search` qiymatidan foydalanib `document.write` methodiga DOM XSS ≫](https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-document-write-sink-inside-select-element)
{% endhint %}

`InnerHTML` methodi hech qanday zamonaviy brauzerda `script` elementlarini qabul qilmaydi va `svg onload` eventlarini yoqmaydi. Bu `img` yoki `iframe` kabi muqobil elementlardan foydalanishingiz kerakligini anglatadi. Ushbu elementlar bilan birgalikda `onload` va `onerror` kabi hodisalarni qayta ishlash vositalaridan foydalanish mumkin. Misol uchun:

```html
element.innerHTML='... <img src=1 onerror=alert(document.domain)> ...'
```

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [`location.search` qiymatidan foydalanib `innerHTML` methodiga DOM XSS ≫](https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-innerhtml-sink)
{% endhint %}

## <mark style="color:yellow;">Qiymatlar va methodlar lar 3-tomon kutubxonalarida</mark> <a href="#source-lar-va-sink-lar-3-partiya-kutubxonalarida" id="source-lar-va-sink-lar-3-partiya-kutubxonalarida"></a>

Zamonaviy Web-dasturlar odatda bir qator uchinchi tomon kutubxonalari va ramkalari yordamida tuziladi, ular ko'pincha dasturchilar uchun qo'shimcha funksiyalar va imkoniyatlarni taqdim etadi. Shuni yodda tutish kerakki, ulardan ba'zilari DOM XSS uchun potentsial methodlar va sourcelardir.

### <mark style="color:yellow;">jQueryda DOM XSS</mark> <a href="#dom-xss-jquery-da" id="dom-xss-jquery-da"></a>

Agar jQuery kabi JavaScript kutubxonasi ishlatilayotgan bo'lsa, sahifadagi DOM elementlarini o'zgartirishi mumkin bo'lgan methodlarga e'tibor bering. Masalan, jQuery `attr()` funksiyasi DOM elementlarining atributlarini o'zgartirishi mumkin. Agar ma'lumotlar URL manzili kabi foydalanuvchi tomonidan boshqariladigan source dan o'qilsa, keyin `attr()` funksiyasiga o'tkazilsa, XSS ni keltirib chiqarish uchun yuborilgan qiymatni o'zgartirish mumkin bo'lishi mumkin. Misol uchun, bizda URL ma'lumotlaridan foydalangan holda a elementining `href` atributini o'zgartiradigan ba'zi JavaScript kod mavjud:

```javascript
$(function() {
    $('#backLink').attr("href",(new URLSearchParams(window.location.search)).get('returnUrl'));
});
```

Siz buni URL ni o'zgartirish orqali exploit qilishingiz mumkin va shunda `location.search`da zararli JavaScript URLi mavjud bo'ladi. Sahifaning JavaScripti ushbu zararli URL-ni havolaning `href`ga qo'llaganidan so'ng, havolani bosish orqali exploitni amalga oshiradi:

```javascript
?returnUrl=javascript:alert(document.domain)
```

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [`hreflocation.search` yordamida jQueryning`href` atributi metodida DOM XSS ≫](https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-jquery-href-attribute-sink)
{% endhint %}

Yana bir potensial method bu jQuery ning $() selector funksiyasi bo'lib u DOM ga yangi elementlarni qo'shishga ishlatiladi.

jQuery juda ham mashxur va klassik DOM XSS zaifligi web-saytlarning ushbu selector dan animatsiyalar uchun location.hash qiymati bilan birgalikda foydalanishi tufayli yuzaga kelgan. Ushbu hatti xarakatlar quyidagiga o'xshash zaif `hashchange` event handler orqali tez tez amalga oshiriladi:

```javascript
$(window).on('hashchange', function() {
    var element = $(location.hash);
    element[0].scrollIntoView();
});
```

`Hash` ko'rinib turibdiki foydalanuvchi tomonidan nazorat qilish mumkin, shunda Haker buni XSS vektorini kritish uchun selector $( ) dan foydalanishi mumkin. JQuery-ning eng so'nggi versiyalari kirish xesh belgisi (#) bilan boshlanganda HTML-ni selector ga kiritishingizga yo'l qo'ymaslik orqali ushbu zaiflikni tuzatdi. Biroq, siz hali ham internetdan zaif kodni topishingiz mumkin.

Ushbu klassik zaiflikdan foydalanish uchun siz foydalanuvchiga `hashchange` hodisasini ishga tushirish yo'lini topishingiz kerak bo'ladi. Buning eng oddiy usullaridan biri bu `iframe` orqali exploit ni yetkazib berishdir:

```html
<iframe src="https://vulnerable-website.com#" onload="this.src+='<img src=1 onerror=alert(1)>'">
```

Ushbu misolda `src` atributi bo'sh xesh qiymatiga ega zaif sahifaga ishora qiladi. `Iframe` yuklanganda, xeshga XSS vektori qo'shiladi, bu `hashchange` hodisasining ishlashiga olib keladi.

{% hint style="info" %}
**Eslatma:**\
Hatto jQuery-ning yangi versiyalarida # prefiksni talab qilmaydigan manbadan kiritilgan ma'lumotlar ustidan to'liq nazoratga ega bo'lsangiz ham `$()`selektor methodi orqali zaif bo'lishi mumkin.
{% endhint %}

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark>[`hashchange` eventidan foydalanib jQuery selektor metodida DOM XSS  ≫](https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-jquery-selector-hash-change-event)
{% endhint %}

### <mark style="color:yellow;">AngularJS da DOM XSS</mark> <a href="#angularjs-da-dom-xss" id="angularjs-da-dom-xss"></a>

Agar AngularJS kabi framework ishlatilsa, JavaScript-ni <> qavslarsiz yoki eventlarsoz bajarish mumkin bo'lishi mumkin. Sayt HTML elementida `ng-app` atributidan foydalansa, u AngularJS tomonidan qayta ishlanadi. Bunday holda, AngularJS to'g'ridan-to'g'ri HTML yoki ichki atributlarda paydo bo'lishi mumkin bo'lgan {} qavslar ichida JavaScript-ni bajaradi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [HTML-kodlangan <> qavslar va qo'sh tirnoqli AngularJS bilan DOM XSS ≫](https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-angularjs-expression)
{% endhint %}

## <mark style="color:yellow;">Stored va Reflected ma'lumotlar bilan biriktirilgan DOM XSS</mark>

Ba'zi toza DOM ga asoslangan zaifliklar bitta sahifani o'zida amalga oshirilishi mumkin. Agar skript URL dan biror ma'lumotni o'qisa va uni xavfli methodga yozsa unda bu zaiflik to'laqonli client-side.

Biroq, source lar brauzerlar tomonidan to'g'ridan-to'g'ri ochiladigan ma'lumotlar bilan cheklanmaydi - ular web-saytdan ham kelib chiqishi mumkin. Masalan, web-saytlar odatda serverdan HTML javobida URL parametrlarini aks ettiradi. Bu odatda oddiy XSS bilan bog'liq, lekin u reflected+ DOM zaifliklariga ham olib kelishi mumkin.

Reflected + DOM zaifligida server so'rovdagi ma'lumotlarni qayta ishlaydi va ma'lumotlarni javobda aks ettiradi. Ko'rsatilgan ma'lumotlar JavaScript satr literaliga yoki DOM ichidagi ma'lumotlar elementiga, masalan, shakl maydoniga joylashtirilishi mumkin. Keyin sahifadagi skript aks ettirilgan ma'lumotlarni xavfli tarzda qayta ishlaydi va oxir-oqibat uni xavfli methodga yozadi.

```
eval('var data = "reflected string"');
```

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> <mark style="color:orange;"></mark> [Reflected DOM XSS ≫](https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-dom-xss-reflected)
{% endhint %}

Web-saytlar, shuningdek, ma'lumotlarni serverda saqlashi va boshqa joylarda ko'tsatishi mumkin. Stored + DOM zaifligida server bitta so'rovdan ma'lumotlarni oladi, uni saqlaydi va keyin ma'lumotlarni keyingi javobga qo'shadi. Keyingi javobdagi skriptda ma'lumotlarni xavfsiz tarzda qayta ishlovchi method mavjud bo'ladi.

```javascript
element.innerHTML = comment.author
```

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> <mark style="color:orange;"></mark> [Stored DOM XSS ≫](https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-dom-xss-stored)
{% endhint %}

## <mark style="color:yellow;">Qaysi methodlar DOM-XSS zaifliklariga sabab bo'ladi?</mark> <a href="#qaysi-sink-lar-dom-xss-zaifliklariga-sabab-boladi" id="qaysi-sink-lar-dom-xss-zaifliklariga-sabab-boladi"></a>

Quyidagi methodlar DOM-XSS zaifliklariga sabab bo'lishi mumkin:

```javascript
document.write()
document.writeln()
document.domain
element.innerHTML
element.outerHTML
element.insertAdjacentHTML
element.onevent
```

Quyidagi jQuery funksiyalari ham DOM-XSS zaifliklarini paydo qilishi mumkin:

```javascript
add()
after()
append()
animate()
insertAfter()
insertBefore()
before()
html()
prepend()
replaceAll()
replaceWith()
wrap()
wrapInner()
wrapAll()
has()
constructor()
init()
index()
jQuery.parseHTML()
$.parseHTML()
```

## <mark style="color:yellow;">DOM-XSS ni qanday oldini olish mumkin?</mark> <a href="#dom-xss-ni-qanday-oldini-olish-mumkin" id="dom-xss-ni-qanday-oldini-olish-mumkin"></a>

DOM-ga asoslangan zaifliklar sahifasida tavsiflangan umumiy choralarga qo'shimcha ravishda, har qanday ishonchsiz manba ma'lumotlarini HTML hujjatiga dinamik ravishda yozishga ruxsat bermaslik kerak.
