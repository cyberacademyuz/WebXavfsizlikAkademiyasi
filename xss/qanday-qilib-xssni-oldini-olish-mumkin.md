# Qanday qilib XSSni oldini olish mumkin ?

Ushbu bo'limda biz cross-site scripting zaifliklarini oldini olish uchun ba'zi asosiy prinsiplarni va keng tarqalgan texnologiyalar orqali XSS xujumlaridan himoyalanish yo'llarini tushuntiramiz.

Cross-site scriptingni oldini olish asosan ikkita ximoyalanish yo'li orqali amalga oshiriladi:

* <mark style="color:yellow;"></mark>[<mark style="color:yellow;">Chiqayotgan ma'lumotni kodlash</mark>](qanday-qilib-xssni-oldini-olish-mumkin.md#chiqayotgan-malumotni-kodlash)<mark style="color:yellow;"></mark>
* <mark style="color:yellow;"></mark>[<mark style="color:yellow;">Har bir kelayotgan ma'lumotni tekshirish</mark>](qanday-qilib-xssni-oldini-olish-mumkin.md#har-bir-kelayotgan-kirishni-tekshirish)<mark style="color:yellow;"></mark>

## <mark style="color:yellow;">Chiqayotgan ma'lumotni kodlash</mark> <a href="#chiqayotgan-malumotni-kodlash" id="chiqayotgan-malumotni-kodlash"></a>

Foydalanuvchi tomonidan kiritilgan ma'lumot sahifaga yozilishidan oldin kodlanishi kerak, chunki siz yozayotgan kontekst siz qanday kodlashdan foydalanishingiz kerakligini aniqlaydi. Masalan, JavaScript qatoridagi qiymatlar HTML kontekstidagilardan boshqa turdagi aylanib o'tish usulini talab qiladi.

HTML kontekstida siz whitelistga kiritilmagan qiymatlarni HTML obyektlariga aylantirishingiz kerak:

* < belgisi bunga o'zgaradi: `&lt;`
* > belgisi bunga o'zgaradi: `&gt;`

JavaScript string kontekstida alfanumerik bo'lmagan qiymatlar Unicode-dan qochishi kerak:

* < belgisi bunga o'zgaradi: `\u003c`
* > belgisi bunga o'zgaradi: `\u003e`

Ba'zan kodlashning bir nechta qatlamini to'g'ri tartibda qo'llashingiz kerak bo'ladi. Masalan, foydalanuvchi ma'lumotlarini event handler ichiga xavfsiz joylashtirishi uchun siz JavaScript konteksti va HTML konteksti bilan ishlashingiz kerak. Shunday qilib avval Unicode inputdan qutilishingiz keyin esa uni HTML bilan kodlashingiz kerak:

```html
<a href="#" onclick="x='Ushbu satr ikkita qatlamdan qochishi kerak'">test</a>
```

## <mark style="color:yellow;">Har bir kiritilayotgan ma'lumotni tekshirish</mark> <a href="#har-bir-kelayotgan-kirishni-tekshirish" id="har-bir-kelayotgan-kirishni-tekshirish"></a>

Kodlash XSS dan himoyalanishdagi eng muhim yo'nalishdan biri bo'lsa ham yetarlicha emas, chunki kodlash barcha kontekstlargacha kira olmaydi. Siz foydalanuvchidan ma'lumot kelishi bilan tezda tekshirishingiz ham kerak.

Inputni tekshirishga misollar:

* Agar foydalanuvchi responseda keladigan URL manzilini yuborsa, u HTTP va HTTPS kabi xavfsiz protokoldan boshlanganligini tasdiqlaydi. Aks holda, kimdir saytingizdan `javascript` yoki `data` kabi zararli protokollar yordamida foydalanishi mumkin.
* Agar foydalanuvchi kutilgan raqamli qiymat kiritsa bu qiymat aslida butun sonni o'z ichiga olganligini tasdiqlaydi.
* Ushbu kiritish faqat kutilgan belgilar to'plamini o'z ichiga oladi.

Inputni tekshirish ideal tarzda noto'g'ri inputni bloklash orqali ishlashi kerak. Boshqacha qilib aytsak, noto'g'ri kiritilgan ma'lumotlarni to'g'rilash uchun uni to'g'irlashga urinishda ko'proq xatoga yo'l qo'yadi va iloji boricha undan qochish kerak.

### <mark style="color:yellow;">White list va Black list</mark> <a href="#oq-royhat-vs-qora-royhat" id="oq-royhat-vs-qora-royhat"></a>

Input tekshiruvida odatda black listdan koʻra white listlardadan foydalanishi kerak. Misol uchun, barcha zararli protokollar (`javascript`, `data` va boshqalar) ro'yxatini tuzish o'rniga, xavfsiz protokollar ro'yxatini (HTTP, HTTPS) tuzing va ro'yxatga kiritilmagan barcha narsaga ruxsat bermang. Bu sizning himoyangiz yangi zararli protokollar paydo bo'lganda buzilmasligini ta'minlaydi va uni qora ro'yxatdan aylanib o'tish uchun noto'g'ri qiymatlarni yashirishga intiladigan hujumlarga nisbatan kamroq tasir qiladi.

## <mark style="color:yellow;">"safe" HTML ga ruxsat berish</mark> <a href="#xavfsiz-html-ga-ruxsat-berish" id="xavfsiz-html-ga-ruxsat-berish"></a>

Foydalanuvchilarga HTML orqali post yuborish imkoniyatini iloji boricha qo'shmaslik zarur, ammo ba'zida vaziyat buni talab qilishi mumkin. Masalan blog web sayt kommentariyalarda kamroq HTMLni ishlatishga ruxsat berish mumkin.

Bunga odatiy yondashuv xavfli teglar va JavaScriptni filtrlashdir. Buni xavfsiz teglar va atributlarning white listidan foydalanib amalga oshirishga urinib koʻrishingiz mumkin, ammo brauzerni tahlil qilish mexanizmlaridagi nomuvofiqliklar va XSS mutatsiyasi kabi gʻayrioddiyliklar tufayli bu yondashuvni xavfsiz amalga oshirish juda qiyin.

Eng yomon usullardan biri DOMPurify kabi foydalanuvchi brauzerida filtrlash va kodlashni amalga oshiradigan JavaScript kutubxonasidan foydalanishdir. Boshqa kutubxonalar foydalanuvchilarga kontentni markdown formatida taqdim etish va markdownni HTMLga aylantirish imkonini beradi. Afsuski, bu kutubxonalarning barchasida vaqti-vaqti bilan XSS zaifliklari uchrab turadi, shuning uchun bu mukammal to'g'ri yechim emas. Agar siz ulardan foydalansangiz, xavfsizlik yangilanishlarini diqqat bilan kuzatib borishingiz kerak va doim yangilab turishingiz shart bo'ladi.

{% hint style="info" %}
**Eslatma:**

JavaScriptdan tashqari, ba'zi holatlarda CSS va hatto oddiy HTML kabi boshqa kontent ham zararli bo'lishi mumkin.

[Zararli CSS bilan xujumlarni amalga oshirish ☰](https://portswigger.net/research/detecting-and-exploiting-path-relative-stylesheet-import-prssi-vulnerabilities#badcss)
{% endhint %}

## <mark style="color:yellow;">Qanday qilib template mexanizmi bilan XSSni oldini olish mumkin ?</mark> <a href="#qanday-qilib-shablon-mexanizmi-bilan-xss-ni-oldini-olish-mumkin" id="qanday-qilib-shablon-mexanizmi-bilan-xss-ni-oldini-olish-mumkin"></a>

Ko'pgina zamonaviy Web-saytlar HTML-ga dinamik tarkibni joylashtirish uchun Twig va Freemarker kabi server-side template mexanizmlaridan foydalanadi. Ular odatda o'zlarining aylanib o'tish tizimini belgilaydilar. Misol uchun, Twig-da siz kontekstni belgilaydigan argument bilan `e()` filtridan foydalanishingiz mumkin:

```
{{ user.firstname | e('html') }}
```

Jinja va React kabi ba'zi boshqa template mexanizmlari sstandart tarzda dinamik tarkibdan qochadi, bu esa XSS ning ko'p holatlarini oldini oladi.

Berilgan template mexanizmi yoki frameworkdan foydalanishni baholashda qochadigan xususiyatlarni diqqat bilan ko'rib chiqishni tavsiya qilamiz.

{% hint style="info" %}
**Eslatma:**

Agar siz foydalanuvchi ma'lumotlarini to'g'ridan-to'g'ri template satrlariga birlashtirsangiz, web saytingiz server-side template injectionga zaif bo'ladi, bu esa ko'pincha XSSga qaraganda jiddiyroq.
{% endhint %}

## <mark style="color:yellow;">PHP da XSS ni oldini olish</mark> <a href="#php-da-xss-ni-oldini-olish" id="php-da-xss-ni-oldini-olish"></a>

PHP da `htmlentities` deb nomlangan obyektlarni kodlash uchun o'rnatilgan funksiya mavjud. HTML kontekstida inputdan qochish uchun ushbu funksiyani chaqirishingiz kerak. Funksiyani uchta argument bilan chaqirish kerak:

* Kiritayotgan satringiz bilan
* Qo'shtirnoqlarni kodlaydigan `ENT_QUOTES` bilan
* Ko'p hollarda UTF-8 bo'lishi kerak bo'lgan belgilar to'plami bilan

Masalan:

```php
<?php echo htmlentities($input, ENT_QUOTES, 'UTF-8');?>
```

JavaScript string kontekstida bo'lganingizda, siz allaqachon aytib o'tilganidek Unicode-escape ni kiritishingiz kerak. Afsuski, PHP Unicode-escape uchun API taqdim etmaydi. PHP da buni amalga oshirish uchun ba'zi kodlar:

```php
<?php
    function jsEscape($str) {
    $output = '';
    $str = str_split($str);
    for($i=0;$i<count($str);$i++) {
        $chrNum = ord($str[$i]);
        $chr = $str[$i];
        if($chrNum === 226) {
            if(isset(jjjjjjj$str[$i+1]) && ord($str[$i+1]) === 128) {
                if(isset($str[$i+2]) && ord($str[$i+2]) === 168) {
                    $output .= '\u2028';
                    $i += 2;
                    continue;
                }
                if(isset($str[$i+2]) && ord($str[$i+2]) === 169) {
                    $output .= '\u2029';
                    $i += 2;
                    continue;
                }
            }
        }
        switch($chr) {
            case "'":
            case '"':
            case "\n";
            case "\r";
            case "&";
            case "\\";
            case "<":
            case ">":
                $output .= sprintf("\\u%04x", $chrNum);
            break;
            default:
                $output .= $str[$i];
            break;
    }
    }
    return $output;
}
?>
```

Ushbu funksiyani PHP da mana bunday ishlatasiz:

```javascript
<script>x = '<?php echo jsEscape($_GET['x'])?>';</script>
```

yoki buni o'rniga template mexanizmidan foydalanishingiz ham mumkin.

## <mark style="color:yellow;">Client-side JavaScript da XSS ni oldini olish</mark> <a href="#client-side-javascript-da-xss-ni-oldini-olish" id="client-side-javascript-da-xss-ni-oldini-olish"></a>

Foydalanuvchi kiritayotgan ma'lumotlarni HTML konteksida JavaScript ga kiritishidan qochish uchun siz o'zingiz HTML kodlovchisini yasashingiz kerak chunki JavaScript bu uchun API ishlab chiqmagan. Masalan quyidagi JavaScript kod satrlarni HTML obyektlariga o'zgartiradi:

```javascript
function htmlEncode(str){
    return String(str).replace(/[^\w. ]/gi, function(c){
        return '&#'+c.charCodeAt(0)+';';
    });
}
```

Siz bu funksiyani quyidagicha ishlata olasiz:

```javascript
<script>document.body.innerHTML = htmlEncode(untrustedValue)</script>
```

Agar sizni kiritishingizda JavaScript satri ham bo'lsa unda Unicode dan qochadigan kodlovchini funksiyangizga qo'shishingiz kerak. Mana Unicode kodlovchisining kodi:

```javascript
function jsEscape(str){
    return String(str).replace(/[^\w. ]/gi, function(c){
        return '\\u'+('0000'+c.charCodeAt(0).toString(16)).slice(-4);
    });
}
```

Siz bu funksiyani quyidagicha ishlata olasiz:

```javascript
<script>document.write('<script>x="'+jsEscape(untrustedValue)+'";<\/script>')</script>
```

## <mark style="color:yellow;">jQuery da XSS ni oldini olish</mark> <a href="#jquery-da-xss-ni-oldini-olish" id="jquery-da-xss-ni-oldini-olish"></a>

JQuerydagi XSS ning eng keng tarqalgan shakli foydalanuvchi ma'lumotlarini jQuery selektoriga o'tkazishdir. Web-dasturchilar ko'pincha `location.hash`-dan foydalanadilar va uni selektorga o'tkazadilar, bu esa XSS-ni keltirib chiqaradi, chunki jQuery HTMLni ko'rsatadi. jQuery bu muammoni tan oldi va input xesh bilan boshlanganligini tekshirish uchun selektor logikasini tuzatdi. Endi jQuery HTML-ni faqat birinchi belgi < bo'lsagina ko'rsatadi.

## <mark style="color:yellow;">XSSni content security policy orqali zararsizlash</mark> <a href="#xss-ni-content-security-policy-orqali-yumshatish" id="xss-ni-content-security-policy-orqali-yumshatish"></a>

Content Security Policy (CSP) XSS dan himoya qilishning oxirgi yo'lidir. Agar XSSni oldini olish muvaffaqiyatsiz bo'lsa, Haker nima qila olishini cheklash orqali XSSni zararsizlash uchun CSP dan foydalanishingiz mumkin.

CSP turli xil narsalarni, masalan, tashqi skriptlarni yuklash mumkinmi yoki yo'qligini va inline skriptlar bajarilishini nazorat qilish imkonini beradi. CSP-ni o'rnatish uchun siz o'z siyosatingizni o'z ichiga olgan qiymatga ega Content-Security-Policy deb nomlangan HTTP javob headerini kiritishingiz kerak.

Masalan CSP quyidagicha:

```http
default-src 'self'; script-src 'self'; object-src 'none'; frame-src 'none'; base-uri 'none';
```

Bu siyosat skirpt va rasmlar faqat saytning domenidan yuklanishi mumkinligini ta'minlaydi. Mabodo Haker XSS ni muvaffaqiyatli amalga oshirsa ham faqatgina sahifani o'zini resurslarigagina ega chiqa oladi. Bu esa yaxshigina XSS exploit imkonini pasaytiradi.

Agar siz tashqi resurslarni yuklashni talab qilsangiz, faqat Hakerga saytingizdan foydalanishga yordam bermaydigan skriptlarga ruxsat berganingizga ishonch hosil qiling. Misol uchun, agar siz ba'zi domenlarni white listga kiritsangiz, Haker ushbu domenlardan istalgan skriptni yuklashi mumkin. Iloji bo'lsa, resurslarni o'z domeningizda joylashtirishga harakat qiling.

Agar yuqoridagilarni qilishni imkoni bo'lmasa unda siz `hesh`lar va `nonce` lar siyosatidan foydalanib har xil domenlardagi skriptlardan foydalanishga ruxsat berishingiz mumkin. Nonce - bu skript yoki resurs atributi sifatida qo'shiladigan tasodifiy qator bo'lib, u tasodifiy qator server tomonidan yaratilganiga mos kelgan taqdirdagina bajariladi. Haker tasodifiy qatorni taxmin qila olmaydi va shuning uchun skript yoki payload ishlatish mumkin bo'lgan holda chaqira olmaydi va shuning uchun payload bajarilmaydi.

{% hint style="info" %}
**Ko'proq o'qing:**

[CSP yordamida XSS hujumlarini yumshatish  ☰ ](content-security-policy.md#xss-xujumlariga-csp-orqali-yumshatish)
{% endhint %}
