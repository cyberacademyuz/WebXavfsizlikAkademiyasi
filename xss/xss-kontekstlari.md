# XSS kontekstlari

<figure><img src="../.gitbook/assets/XSS contexts.png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
**XSS kontekslari nima ?**

XSS uchun kontekstlar - foydalanuvchi kiritgan ma'lumotlar, DOM ichida bo'lishi mumkin bo'lgan holatlar yoki joylashuvlardan boshqa narsa emas va agar filterlanmagan yoki  kod to'g'ri yozilmagan bo'lsa, XSSga olib kelishi mumkin.

XSS paydo bo'lishi mumkin bo'lgan kontekstlarning asosiy turlari:

1. HTML konteksti
2. Atribut konteksti
3. URL konteksti
4. Javascript konteksti
{% endhint %}

<mark style="color:yellow;">****</mark>[<mark style="color:yellow;">**Reflected**</mark>](reflected-xss.md) yoki [<mark style="color:yellow;">**Stored XSS**</mark>](stored-xss.md) ni tekshirayotganingizda birinchi navbatda siz XSS kontekstini aniqlab olishingiz zarur:&#x20;

* Javob ichida haker tomonidan boshqariladigan ma'lumotlar paydo bo'ladigan joy.
* Web sayt tomonidan ushbu ma'lumotlarga kiritilgan har qanday ma'lumot tekshiruvi yoki boshqa ishlov berishga ega kontekst.

Ushbu ma'lumotlarga tayangan holda siz bitta yoki undan ortiq XSS payloadlarini yaratib keyin ularni test qilib ko'rishingiz mumkin.

{% hint style="info" %}
**Eslatma:**

Biz sizga web saytni test qilishingiz uchun tushunarli [<mark style="color:yellow;">XSS cheat sheet</mark>](../sql-ineksiya/sql-ineksiya-cheat-sheet.md) yaratganmiz. Eventlar va teglar bo'yicha filtrlashingiz va qaysi vektorlar foydalanuvchiga ta'sir qilishini ko'rishingiz mumkin. Cheat sheet da, shuningdek, XSS ekshiruvlariga yordam beradigan <mark style="color:yellow;">AngularJS</mark> sandbox escapes va boshqa ko'plab bo'limlar mavjud.
{% endhint %}

## <mark style="color:yellow;">HTML teglari orasidagi XSS</mark> <a href="#html-teglari-orasidagi-xss" id="html-teglari-orasidagi-xss"></a>

XSS konteksi HTML teglari orasidagi matn bo'lsa, siz JavaScript-ni ishga tushirish uchun mo'ljallangan yangi HTML teglarini kiritishingiz kerak.

JavaScriptni ishga tushirishning ba'zi yo'llari:

```html
<script>alert(document.domain)</script>
<img src=1 onerror=alert(1)>
```

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> <mark style="color:orange;"></mark> [Reflected XSS ni HTML kontekstiga kodlanmagan holda kiritish ≫](https://portswigger.net/web-security/cross-site-scripting/reflected/lab-html-context-nothing-encoded)
{% endhint %}

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark>  <mark style="color:orange;"></mark>  [Stored XSS ni HTML kontekstiga kodlanmagan holda kiritish ≫](https://portswigger.net/web-security/cross-site-scripting/stored/lab-html-context-nothing-encoded)
{% endhint %}

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Reflected XSS ni HTML kontekstiga eng ko'p ishlatiladigan teglar va atributlarni bloklangan holda kiritsh ≫](https://portswigger.net/web-security/cross-site-scripting/contexts/lab-html-context-with-most-tags-and-attributes-blocked)
{% endhint %}

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Reflected XSS ni HTML kontekstiga barcha teglar bloklangan maxsus bittasidan tashqari holatda kiritish ≫](https://portswigger.net/web-security/cross-site-scripting/contexts/lab-html-context-with-all-standard-tags-blocked)
{% endhint %}

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Reflected XSS ni`href` attributi va event ushlovchilar bloklangan holda kiritish ≫](https://portswigger.net/web-security/cross-site-scripting/contexts/lab-event-handlers-and-href-attributes-blocked)
{% endhint %}

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> <mark style="color:orange;"></mark> [Bir qancha SVG Markup ruxsat etilganda Reflected XSS ni kiritish ≫](https://portswigger.net/web-security/cross-site-scripting/contexts/lab-some-svg-markup-allowed)
{% endhint %}

## <mark style="color:yellow;">HTML teg attributlaridagi XSS</mark> <a href="#html-teg-attributlaridagi-xss" id="html-teg-attributlaridagi-xss"></a>

XSS konteksi HTML teg atribut qiymatiga kiritilganda, siz ba'zan atribut qiymatini tugallashingiz, tegni yopishingiz va yangisini kiritishingiz mumkin. Misol uchun:

```html
"><script>alert(document.domain)</script>
```

Ko'pincha bu holatda <> qavslar bloklanadi yoki kodlanadi, shuning uchun kiritilgan ma'lumotlar u paydo bo'lgan tegdan chiqib keta olmaydi. Agar siz atribut qiymatini tugatishingiz mumkin bo'lsa, odatda event handler kabi skript kontekstni yaratadigan yangi atributni kiritishingiz mumkin. Misol uchun:

```javascript
" autofocus onfocus=alert(document.domain) x="
```

Yuqoridagi payload element fokusni qabul qilganda JavaScript-ni ishga tushiradigan `onfocus` hodisasini yaratadi, shuningdek, foydalanuvchi avtomatik ravishda `onfocus` hodisasini ishga tushirishga harakat qilish uchun `autofocus` atributini qo'shadi. Nihoyat, u quyidagi belgilarni toʻgʻirlash uchun x= ni qo'shadi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Reflected XSSni burchakli qavslar attributi orqali HTML kodlash bilan kiritish ≫](https://portswigger.net/web-security/cross-site-scripting/contexts/lab-attribute-angle-brackets-html-encoded)
{% endhint %}

Ba'zan XSS konteksti HTML teg atributining bir turiga kiradi, uning o'zi skript uchun kontekst yaratishi mumkin. Bu yerda siz atribut qiymatini bekor qilmasdan JavaScript-kodini bajarishingiz mumkin. Misol uchun, agar XSS konteksti a tegining `href` atributiga kirsa, skriptni bajarish uchun javascriptning psevdo-protokolidan foydalanishingiz mumkin. Misol uchun:

```html
<a href="javascript:alert(document.domain)">
```

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Stored XSS ni `href` attributida qo'shtirnoq orqali HTML kodlash bilan kiritish ≫](https://portswigger.net/web-security/cross-site-scripting/contexts/lab-href-attribute-double-quotes-html-encoded)
{% endhint %}

Siz <> qavslarni encode qiladigan, ammo atributlarni kiritishga ruhsat beradigan web-saytlarga duch kelishingiz mumkin. Ba'zida bunday ineksiyalar, odatda hodisalarni avtomatik ravishda yoqmaydigan teglar ichida ham boʻlishi mumkin, masalan, canonical teg. Bunda Chrome brauzerida Access keylar va foydalanuvchi oʻzaro taʼsiridan foydalanish orqali exploit qilishingiz mumkin. Access keylar ma'lum bir elementga murojaat qiluvchi klaviatura qisqartmalarini taqdim etish imkonini beradi. `Accesskey` atributi boshqa tugmachalar bilan birgalikda bosilganda hodisalarning boshlanishiga olib keladigan harfni aniqlash imkonini beradi. Keyingi laboratoriyada siz kirish kalitlari bilan tajriba o'tkazishingiz va canonical tegdan foydalanishingiz mumkin.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> <mark style="color:orange;"></mark> [Canonical link tegida Reflected XSS ≫](https://portswigger.net/web-security/cross-site-scripting/contexts/lab-canonical-link-tag)
{% endhint %}

## <mark style="color:yellow;">JavaScripga XSS kiritish</mark> <a href="#javascript-dagi-xss" id="javascript-dagi-xss"></a>

XSS konteksti javobda mavjud JavaScript bo'lsa, muvaffaqiyatli exploitni amalga oshirish uchun turli xil texnikalar zarur bo'lgan turli xil vaziyatlar yuzaga kelishi mumkin.

### <mark style="color:yellow;">Mavjud skriptni tugallash</mark> <a href="#mavjud-skriptni-tugatish" id="mavjud-skriptni-tugatish"></a>

Eng oddiy holatda, mavjud JavaScript-ni o'rab turgan skript tegini shunchaki yopish va JavaScript-ni ishga tushirishni boshlaydigan yangi HTML teglarini kiritish mumkin. Masalan, agar XSS konteksti quyidagicha bo'lsa:

```html
<script>
...
var input = 'controllable data here';
...
</script>
```

keyin mavjud JavaScriptni tugallash va o'zingiznikini bajartirish uchun quyidagi payload dan foydalanishingiz mumkin:

```html
</script><img src=1 onerror=alert(document.domain)>
```

Buning sababi shundaki, brauzer avval sahifa elementlarini, shu jumladan skript bloklarini aniqlash uchun HTML tahlilini amalga oshiradi va faqat keyinroq o'rnatilgan skriptlarni aniqlash va bajarish uchun JavaScript tahlilini amalga oshiradi. Yuqoridagi payload haqiqiy skriptni buzilib, tugallanmagan satr harfi bilan qoldiradi. Ammo bu keyingi skriptni tahlil qilish va odatdagi tarzda bajarilishiga to'sqinlik qilmaydi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Reflected XSSni JavaScript matniga birtirnoq va backslash ishlatib kiritish ≫](https://portswigger.net/web-security/cross-site-scripting/contexts/lab-javascript-string-single-quote-backslash-escaped)
{% endhint %}

### <mark style="color:yellow;">JavaScript kodidan chiqib ketish</mark> <a href="#javascript-qatoridan-chiqib-ketish" id="javascript-qatoridan-chiqib-ketish"></a>

XSS konteksti kodirovka qilingan satr literalida bo'lgan hollarda, ko'pincha satrdan chiqib ketish va JavaScript-ni to'g'ridan-to'g'ri bajarish mumkin. XSS kontekstiga rioya qilgan holda skriptni tuzatish juda muhim, chunki u yerdagi har qanday sintaksis xato butun skriptning bajarilishiga xalaqit beradi.

Satr literalidan chiqishning ba'zi foydali usullari quyidagilardir:

```javascript
'-alert(document.domain)-'
';alert(document.domain)//
```

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Reflected XSS ni JavaScript matniga HTML orqali kodlangan burchakli <> qavslar bilan kiritish ≫](https://portswigger.net/web-security/cross-site-scripting/contexts/lab-javascript-string-angle-brackets-html-encoded)
{% endhint %}

Ba'zi web saytlar / (teskari slash) bilan har qanday bir tirnoq belgsini kiritib, JavaScript qatorida kiritilgan matnning uzilishini oldini olishga harakat qiladi. / belgisidan oldin JavaScript parseriga belgilar aniq va chiziq terminatori kabi maxsus xarakterga ega emasligini aytadi. Bunday vaziyatda web saytlar ko'pincha drop belgisini aylanib o'ta olmay xato qiladi. Bu shuni anglatadiki, haker web sayt tomonidan qo'shilgan / ni zararsizlantirish uchun o'zining / belgisidan foydalanishi mumkin.

Masalan mana bunga e'tibor bering:

```javascript
';alert(document.domain)//
```

u mana bunday holga keladi:

```javascript
\';alert(document.domain)//
```

siz shunda bunga o'xshash boshqa payloadni amalga oshira olasiz:

```javascript
\';alert(document.domain)//
```

va u mana bu holga keladi:

```javascript
\\';alert(document.domain)//
```

Bu yerda birinchi backslash ikkinchi backslash maxsus belgi sifatida emas, balki tom ma'noda talqin qilinishini anglatadi. Bu shuni anglatadiki, kodirovka endi string terminatori sifatida ishlaydi va shuning uchun hujum muvaffaqiyatli amalga oshadi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Reflected XSS ni JavaScript matniga burchakli qavslar va HTML kodlangan qo'shtirnoq va birtirnoq qo'shilmagan holda kiritish ≫](https://portswigger.net/web-security/cross-site-scripting/contexts/lab-javascript-string-angle-brackets-double-quotes-encoded-single-quotes-escaped)
{% endhint %}

Ba'zi web-saytlar qaysi belgilardan foydalanishga ruxsat berilganligini cheklash orqali XSS-ni qiyinlashtiradi. Bu web-sayt darajasida yoki so'rovlaringiz web-saytga yetib borishiga to'sqinlik qiladigan WAF-ni o'rnatish orqali bo'lishi mumkin. Bunday holatlarda siz ushbu xavfsizlik choralarini chetlab o'tadigan boshqa funksiyalarni chaqirish usullarini sinab ko'rishingiz kerak. Buni qilishning usullaridan biri exception handler bilan throw dan foydalanishdir. Bu sizga argumentlarni qavsdan foydalanmasdan funksiyaga o'tkazish imkonini beradi. Quyidagi kod `alert()` funksiyasini global exception handler ga tayinlaydi va `throw` iborasi `1` ni exception handler ga beradi. Yakuniy natija `alert()` funksiyasi argument sifatida `1` bilan chaqiriladi.

```javascript
onerror=alert;throw 1
```

Keyingi laboratoriya muayyan belgilarni filtrlaydigan web-saytdir. Buni hal qilish uchun siz yuqorida tavsiflanganlarga o'xshash usullardan foydalanishingiz kerak bo'ladi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> <mark style="color:orange;"></mark> [Reflected XSS ni URL da birqancha belgilar bloklangan holda kiritish ≫](https://portswigger.net/web-security/cross-site-scripting/contexts/lab-javascript-url-some-characters-blocked)
{% endhint %}

### <mark style="color:yellow;">HTML Encoding dan foydalanish</mark> <a href="#html-encoding-dan-foydalanish" id="html-encoding-dan-foydalanish"></a>

Agar XSS konteksti kodirovka qilingan teg atributidagi mavjud JavaScript boʻlsa, masalan event handler, baʼzi input filtrlari bilan ishlash uchun HTML-kodlashdan foydalanish mumkin.

Brauzer javobdagi HTML teglarni va atributlarni tahlil qilgandan so'ng, teg atributlari qiymatlarini qayta ishlashdan oldin HTML-dekodlashni amalga oshiradi. Agar server tomonidagi dastur muvaffaqiyatli XSS exploit uchun zarur bo'lgan ma'lum belgilarni bloklasa yoki zararsizlantirsa, siz ko'pincha ushbu belgilarni HTML-kodlash orqali filterni chetlab o'tishingiz mumkin.

Masalan, agar XSS konteksti quyidagicha bo'lsa:

```html
<a href="#" onclick="... var input='controllable data here'; ...">
```

va Web sayt birtirnoq belgilarini bloklaydi, JavaScript qatoridan chiqib ketish va oʻz skriptingizni bajarish uchun quyidagi payload dan foydalanishingiz mumkin:

```javascript
&apos;-alert(document.domain)-&apos;
```

`&apos;` ketma-ketlik apostrof yoki bitta tirnoqni ifodalovchi HTML ob'ektidir. Brauzer HTML-kodni JavaScript-ni tahlil qilishdan oldin `onclick` atributining qiymatini dekodlashi sababli, obektlar birtirnoq sifatida dekodlanadi, ular qatorni ajratuvchiga aylanadi va shuning uchun hujum muvaffaqiyatli bo'ladi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Stored XSS ni `onclick` hodisasida burchakli qavslar va HTML kodlangan qo'shtirnoq va birtirnoq va backslash kiritilmagan holda amalga oshirish ≫](https://portswigger.net/web-security/cross-site-scripting/contexts/lab-onclick-event-angle-brackets-double-quotes-html-encoded-single-quotes-backslash-escaped)
{% endhint %}

### <mark style="color:yellow;">JavaScript shablon literallarida XSS</mark> <a href="#javascript-shablon-literallarida-xss" id="javascript-shablon-literallarida-xss"></a>

JavaScript shablon literallari oʻrnatilgan JavaScript ifodalariga ruxsat beruvchi satr literallaridir. O'rnatilgan iboralar baholanadi va odatda matnga birlashtiriladi. Shablon harflari oddiy qo'shtirnoq o'rniga teskari belgilar bilan inkapsullanadi va o'rnatilgan ifodalar ${...} sintaksisi yordamida aniqlanadi.

Misol uchun, quyidagi skript foydalanuvchining ko'rsatilgan nomini o'z ichiga olgan salomlash xabarini chop etadi:

```javascript
document.getElementById('message').innerText = `Welcome, ${user.displayName}.`;
```

XSS konteksti JavaScript shablonining literaliga aylanganda, literalni tugatishni hojati yo'q. Buning o'rniga, literal qayta ishlanganida bajariladigan JavaScript ifodasini joylashtirish uchun oddiygina ${...} sintaksisidan foydalanishingiz kerak. Masalan, agar XSS konteksti quyidagicha bo'lsa:

```html
<script>
...
var input = `controllable data here`;
...
</script>
```

keyin shablonning literalini tugatmasdan JavaScript-ni bajarish uchun quyidagi payload dan foydalanishingiz mumkin:

```javascript
${alert(document.domain)}
```

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Reflected XSS shablon literalida burchakli qavslar, birtirnoq, qoʻsh tirnoq, backslash va teskari belgilar bilan aks ettirish ≫](https://portswigger.net/web-security/cross-site-scripting/contexts/lab-javascript-template-literal-angle-brackets-single-double-quotes-backslash-backticks-escaped)
{% endhint %}

## <mark style="color:yellow;">AngularJS sandbox kontekstida XSS</mark> <a href="#angularjs-sandbox-kontekstida-xss" id="angularjs-sandbox-kontekstida-xss"></a>

Ba'zida XSS zaifliklari AngularJS sandbox kontekstida paydo bo'ladi. Bu exploit uchun qo'shimcha to'siqlarni keltirib chiqaradi, ularni ko'pincha aql bilan chetlab o'tish mumkin.

{% hint style="info" %}
**Ko'proq o'qish:**\
****[Client-side template ineksiyasi <mark style="color:yellow;"></mark> ☰](angularjs-sandbox.md#client-side-template-nima)
{% endhint %}
