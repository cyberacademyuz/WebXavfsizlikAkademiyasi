# Cross-site scripting

![](<../.gitbook/assets/Manjaro & windows nusxasi nusxasi.png>)

Ushbu bo'limda biz cross-site scripting nima ekanligi, undagi har xil zaifliklar qanday ekanligi haqida va qanday qilib cross-site scripting zaifliklarini oldini olish mumkinligi haqida gaplashamiz

## <mark style="color:yellow;">Cross-site scripting nima?</mark> <a href="#crosssitescripting" id="crosssitescripting"></a>

Cross-site scripting (XSS deb ham tanilgan) bu hakerga foydalanuvchilarning zaif web sayt bilan o'zaro aloqalarini buzishga imkon beruvchi web-xavfsizlik zaifligi. U Hakerga har xil web saytlarni bir biridan ajratish uchun yasalgan same origin policy ni aylanib o'tishga imkon beradi. Cross-site scripting zaifliklari odatda hakerga jabrlanuvchi foydalanuvchi qiyofasini yaratish, foydalanuvchi bajarishi mumkin bo'lgan har qanday harakatlarni amalga oshirish va foydalanuvchining istalgan ma'lumotlariga kirish imkonini beradi. Agarda jabrlanuvchi foydalanuvchida qandaydir imtiyozlar bo'ladigan bo'lsa haker o'sha foydalanuvchi yordamida to'la qonli web saytning ma'lumotlariga va funksionalliklariga ega chiqadi.

## <mark style="color:yellow;">XSS qanday ishlaydi?</mark> <a href="#xssqandayishlaydi" id="xssqandayishlaydi"></a>

Cross-site scripting zaif web saytni manipulatsiya qilib foydalanuvchilarga zararli JavaScript qaytarganda ishlaydi. Agar zararli JavaScript kodi jabrlanuvchining brauzerida ishga tushsa unda haker foydalanuvchi va web sayt o'rtasidagi bog'lanishni buzgan hisoblanadi.

![](../.gitbook/assets/cross-site-scripting\(1\).png)

{% hint style="warning" %}
Agar siz XSS zaifliklarining asosiy tushunchalari bilan tanishgan bo'lsangiz va endi ba'zi real jaiflikda o'rganishni xohlasangiz, ushbu mavzudagi barcha laboratoriyalarga quyidagi link orqali kirishingiz mumkin.

<mark style="color:yellow;"></mark>[<mark style="color:yellow;">Labaratoriyalar</mark> ](https://portswigger.net/web-security/all-labs#cross-site-scripting)<mark style="color:yellow;">**≫**</mark>
{% endhint %}

### <mark style="color:yellow;">XSSni aniqlash</mark> <a href="#xss-proof-of-concept" id="xss-proof-of-concept"></a>

Siz XSS zaifligining ko'p turlarini o'z brauzeringiz **** orqali o'zboshimchalik bilan JavaScript-ni ishga tushirishiga olib keladigan payloadni kiritish orqali aniqlashingiz mumkin. Anchadan buyon XSS zaifliklarida `alert()`funksiyasidan foydalanib kelinadi, bunga asosiy sabab u qisqa va muvaffaqiyatli amalga oshganini ko'rish oson bo'lgan funksiya hisoblanadi. Siz bizning laboratoriya ishlarimizning juda ko'p qismini aynan `alert()` funksiyasini victimning brauzerida ishga tushirib bajarib ko'rasiz.

Afsuski, agar siz Chrome brauzeridan foydalansangiz, biroz muammo bor. Chunki 92-versiyadan boshlab (20-iyul 2021-yil) **cross-origin iframe**lar `alert()` funksiyasini ishga tushirilishini oldini ola boshladi. Ular ba'zi ilg'or XSS hujumlarini yaratish uchun ishlatilganligi sababli, ba'zida boshqa payloadlardan foydalanishingiz kerak bo'ladi. Bu holatda biz `print()` funksiyasidan foydalanishni maslahat beramiz.&#x20;

Laboratoriyalarimizdagi simulyatsiya qilingan victimning Chrome brauzeridan foydalanganligi sababli, biz zaiflik mavjud bo'lgan laboratoriyalarni `print()` yordamida ham hal qilish uchun oʻzgartirdik. Biz buni kerakli joylarda ko'rsatmalar bilan ko'rsatdik.

## <mark style="color:yellow;">XSSning qanday xujum turlari mavjud?</mark> <a href="#xsstypes" id="xsstypes"></a>

Uchta asosiy xujum turlari mavjud bo'lib ular:

* <mark style="color:yellow;">****</mark>[<mark style="color:yellow;">**Reflected XSS**</mark> ](reflected-xss.md)- zararli skript HTTP so'rovi orqali jo'natilganda ishga tushuvchi XSS
* <mark style="color:yellow;">**Stored XSS**</mark>** ** - zararli skript web sayt ma'lumotlar bazasidan ishga tushganda ishga tushuvchi XSS.
* <mark style="color:yellow;">**DOM-Based XSS**</mark> - bunda zaiflik server tomonidagi kodda emas, balki browser kodida mavjud bo'lganda ishga tushuvchi XSS.

## <mark style="color:yellow;">Reflected XSS</mark> <a href="#reflected" id="reflected"></a>

**Reflected XSS** - XSSning sodda ko'rinishlaridan biri. Reflected XSS web sayt HTTP so'rovida ma'lumotlarni qabul qilganda paydo bo'ladi va bu ma'lumotlar xavfli tarzda darhol javobga aylanadi.

Mana oddiygina reflected XSS zaifligidan misol:

```url
https://insecure-website.com/status?message=Hammasi+joyida.
<p>Status: Hammasi joyida.</p>
```

Web sayt ma'lumotni tekshirmayapti va bu hakerga quyidagi ko'rinishda xujumni tashkil etishiga turtki bo'ladi:

```url
https://insecure-website.com/status?message=<script>/*+Bad+stuff+here...+*/</script>
<p>Status: <script>/* Bad stuff here... */</script></p>
```

Agar foydalanuvchi ushbu URL ga tashrif buyursa unda haker yozgan skript jabrlanuvchining brauzerida ishga tushadi. Bunda skript foydalanuvchiga ruxsat etilgan har qanday ishni qilishi mumkin.

{% hint style="info" %}
**Ko'proq o'qish:**

[Reflected XSS ☰](cross-site-scripting.md#reflected)

[Cross-site scripting cheat sheet ☰](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet)
{% endhint %}

## <mark style="color:yellow;">Stored XSS</mark> <a href="#stored-xss" id="stored-xss"></a>

**Stored XSS** (persistent yoki ikkinchi darajali XSS ham deyiladi) qachonki web sayt ishonchsiz manbadan ma'lumotni qabul qilib keyin uni HTTP so'rovlarga javob qilib qaytarganda ishga tushadi.

Tekshirilayotgan ma'lumotlar HTTP so'rovi orqali web saytga yuborilishi mumkin, bular saytda xabar qoldirish sahifasidagi komentlar, chatdagi foydalanuvchi nomlari yoki browserning so'rovi bo'yicha aloqa ma'lumotlari misol bo'la oladi. Bundan tashqari ma'lumotlar boshqacha ishonchsiz manbalardan kelishi ham mumkin; masalan, **SMTP** orqali qabul qilingan xabarlarni aks ettiruvchi web-pochta ilovasi, ijtimoiy tarmoq xabarlarini aks ettiruvchi marketing web sayti yoki tarmoq trafigidan olingan paket maʼlumotlarini aks ettiruvchi tarmoq monitoringi web sayti.

Quyida **Stored XSS**ning sodda ko'rinishidan misol keltiramiz. Web saytdagi xabar qoldirish sahifasi foydalanuvchilarga xabar yozib qoldirishga ruxsat beradi va xabarlar qolgan foydalanuvchilarga ko'rinadi:

```html
<p>Salom, bu mening xabarim!</p>
```

Web sayt hech qanday ma'lumotlarni tekshirmaydi va bu holatdan haker unumli foydalanib boshqa foydalanuvchilarga ta'sir etuvchi skript yozib jo'nata oladi:

```html
<p><script>/* Bad stuff here... */</script></p>
```

{% hint style="info" %}
**Ko'proq o'qish:**

[Stored cross site scripting ☰](cross-site-scripting.md#stored-xss)

[Cross-site scripting cheat sheet ☰](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet)
{% endhint %}

## <mark style="color:yellow;">DOM-Based XSS</mark> <a href="#dom-based-xss" id="dom-based-xss"></a>

**DOM-based XSS** (DOM XSS ham deyiladi) qachonki web sayt JavaScript kodini browser tomonidan ma'lumotlarni xavfsiz yo'l orqali olsa paydo bo'ladi.

Quyidagi misolda, websayt inputdagi qiymatni o'qish va bu qiymatni HTML ichidagi elementga yozish uchun JavaScript-dan foydalanadi:

```javascript
var search = document.getElementById('search').value;
var results = document.getElementById('results');
results.innerHTML = 'You searched for: ' + search;
```

Agar haker inputni nazorat qila olsa unda bu orqali osongina zararli qiymatlarni o'zining ishga tushuvchi skriptiga qo'shib jo'nata oladi:

```javascript
You searched for: <img src=1 onerror='/* Bad stuff here... */'>
```

Odatda, input HTTP so'rovining bir qismidan, masalan URL so'rovlar qatori parametridan to'ldirilib, hakerga reflected XSS dek zararli URL yordamida hujumni amalga oshirishga imkon beradi.

{% hint style="info" %}
**Ko'proq o'qish:**[\
Domga asoslangan XSS ☰](cross-site-scripting.md#dom-based-xss)
{% endhint %}

## <mark style="color:yellow;">XSSni nimalarga ishlatish mumkin?</mark> <a href="#xss-ni-nimalarga-ishlatish-mumkin" id="xss-ni-nimalarga-ishlatish-mumkin"></a>

**XSS**ni exploit qilayotgan hakerlar ko'pincha quyidagilarni amalga oshirs olishadi:

* Jabrlangan foydalanuvchi bo'la olish
* Foydalanuvchi bajara oladigan ishlarni amalga oshirish
* Foydalanuvchi o'qishi mumkin bo'lgan ma'lumotlarni o'qish
* Foydalanuvchining login ma'lumotlarini qo'lga kiritish
* Web saytni virtual buzishni amalga oshirish
* Web saytga troyan funksionallikni olib kirish

## <mark style="color:yellow;">XSS zaifliklarning ta'siri</mark> <a href="#xss-zaifliklarning-tasiri" id="xss-zaifliklarning-tasiri"></a>

Haqiqiy XSS zaifligining ta'siri web saytning funksionalligi, ma'lumotlari va foydalanuvchi bilan qanday murosaga kelganiga bog'liq. Masalan:

* Barcha foydalanuvchilar anonim bo'lgan va barcha ma'lumotlar ochiq bo'lgan broshyura web saytida ta'sir ko'pincha minimal bo'ladi.
* Bank operatsiyalari, elektron pochta xabarlari yoki sog'liqni saqlash yozuvlari kabi nozik ma'lumotlarga ega bo'lgan web saytda ta'sir odatda jiddiy bo'ladi.
* Agar buzg'unchilikka uchragan foydalanuvchi web sayt ichida yuqori imtiyozlarga ega bo'lsa, bu ta'sir odatda juda muhim bo'lib, hakerga zaif dasturni to'liq nazorat qilish va barcha foydalanuvchilar va ularning ma'lumotlarini buzish imkonini beradi.

{% hint style="info" %}
**Ko'proq o'qish:**

[XSS zaifliklaridan foydalanish ☰](xss-zaifliklarini-exploit-qilish.md)
{% endhint %}

## <mark style="color:yellow;">XSS zaifliklarini qanday topib ularni sinash mumkin?</mark> <a href="#xss-zaifliklarini-qanday-topib-ularni-test-qilish-mumkin" id="xss-zaifliklarini-qanday-topib-ularni-test-qilish-mumkin"></a>

XSS zaifliklarining ko'pini **Burp Suite** web zaiflik skaneri yordamida tez va ishonchli tarzda topilishi mumkin.

**Reflected** va **Stored XSS** ni qo'lda sinovdan o'tkazish quyidagilarni o'z ichiga oladi: &#x20;

* Web saytning har bir input qismiga oddiy unique ma'lumotlar(_qisqa harf-raqamli string_)ni kiritish
* HTTP responsida **yuborilgan ma'lumotlar qaytariladigan har bir joy**ni aniqlash
* Aniqlash uchun har bir joyni alohida tekshirish.&#x20;

Shunday qilib, siz XSS paydo bo'ladigan kontekstni aniqlashingiz va undan foydalanish uchun mos payloadni tanlashingiz kerak.

URL parametrlari orqali bajariladigan DOM-ga asoslangan XSS-ni qo'lda sinovdan o'tkazish quyidagiga o'xshash jarayonni o'z ichiga oladi:&#x20;

* Parametrga oddiy unique string berish&#x20;
* Ushbu string uchun DOM-ni qidirishda brauzerning Dev toolsdan foydalanish va undan foydalanish mumkinligini aniqlash uchun har bir joyni tekshirish.

Biroq, DOM XSS ning boshqa turlarini aniqlash qiyinroq. URL-ga asoslanmagan input (masalan, `document.cookie`) yoki HTML-ga asoslanmagan (`setTimeout` kabi)  DOM-based zaifliklarni topishda JavaScript kodini ko'rib chiqish uchun uni o'rnini bosuvchi hech narsa yo'q, bu juda ko'p vaqt talab qilishi mumkin. Burp Suite web-zaiflik skaneri DOM-ga asoslangan zaifliklarni aniqlashni ishonchli tarzda avtomatlashtirish uchun JavaScriptning statik va dinamik tahlilini birlashtiradi.

{% hint style="info" %}
**Ko'proq o'qish:**

[Cross site scripting kontekslari **** ☰](xss-kontekstlari.md)
{% endhint %}

## <mark style="color:yellow;">Content Security Policy</mark> <a href="#content-security-policy" id="content-security-policy"></a>

**Content Security Policy** (<mark style="color:yellow;">CSP</mark>) - saytlararo skriptlarning ta'sirini va boshqa ba'zi zaifliklar havfini kamaytishga qaratilgan brauzer mexanizmi. Agar CSP-dan foydalanadigan web sayt XSS-ga o'xshash xatti-harakatlarga ega bo'lsa, CSP zaiflikdan foydalanishga to'sqinlik qilishi yoki oldini olishi mumkin. Ko'pincha, asosiy zaiflikdan foydalanishni ta'minlash uchun CSP ni chetlab o'tish mumkin.

{% hint style="info" %}
**Ko'roq o'qish:**

[Content Security Policy ☰](cross-site-scripting.md#content-security-policy)
{% endhint %}

## <mark style="color:yellow;">Dangling markup ineksiyasi</mark> <a href="#dangling-markup-injection" id="dangling-markup-injection"></a>

**Dangling markup ineksiyasi** - kirish filtrlari yoki boshqa himoya vositalari tufayli saytlararo skriptlardan to'liq foydalanish mumkin bo'lmagan holatlarda o'zaro domen ma'lumotlarini olish uchun ishlatilishi mumkin bo'lgan usul. U ko'pincha boshqa foydalanuvchilar uchun ko'rinadigan shahsiy ma'lumotlarni, shu jumladan foydalanuvchi nomidan ruxsatsiz harakatlarni amalga oshirish uchun ishlatilishi mumkin bo'lgan CSRF tokenlarini olish uchun ishlatilishi mumkin.

{% hint style="info" %}
**Ko'proq o'qing:**

[Dangling markup ineksiyasi ☰](cross-site-scripting.md#xss-zaifliklarini-qanday-topib-ularni-test-qilish-mumkin)
{% endhint %}

## <mark style="color:yellow;">XSS xujumlarini qanday oldini olish mumkin?</mark> <a href="#xss-xujumlarini-qanday-oldini-olish-mumkin" id="xss-xujumlarini-qanday-oldini-olish-mumkin"></a>

Cross-site scripting ni oldini olish ba'zi hollarda muhim emas, ammo dasturning murakkabligi va foydalanuvchi tomonidan boshqariladigan ma'lumotlarni qayta ishlash usullariga qarab ancha muhim bo'lishi mumkin.

XSS hujumini samarali oldini olish ko'pincha quyidagi o'lchovlarga asoslangan bo'ladi:

* **Kiruvchi input ni filtrlash.** Bu nuqtada foydalanuvchi inputdan kiritgan ma'lumoti kelib tushishi bilan uni kuchli filtrlash kerak.
* **Yuborayotgan ma'lumotni shifrlash.** HTTP javoblarida foydalanuvchi tomonidan boshqariladigan ma'lumotlarni yuborish qismida, faol tarkib sifatida talqin qilinishining oldini olish uchun ma'lumotni yuborayotganda shifrlang. Ma'lumotni yuborish vaqtida kontekstiga qarab, bu HTML, URL, JavaScript va CSS shifrlash kombinatsiyalarini qo'llashni talab qilishi mumkin.
* **To'gri keladigan response headerlaridan foydalaning** HTML yoki JavaScriptni o'z ichiga olishi mo'ljallanmagan HTTP responslarida XSS ning oldini olish uchun brauzerlar responselarni siz xohlagan taminlashiga ishonch hosil qilish uchun **Content-Type** va **X-Content-Type-Options** headerlaridan foydalanishingiz mumkin.
* **Content Security Policy.** Oxirgi himoya chizig'i sifatida siz hali ham mavjud bo'lgan XSS zaifliklarining jiddiyligini kamaytirish uchun Content Security Policy (CSP) dan foydalanishingiz mumkin.

{% hint style="info" %}
**Ko'proq o'qing:**

[Qanday qilib XSSni oldini olish mumkin ☰](qanday-qilib-xssni-oldini-olish-mumkin.md)[\
Burp Suite skaneridan foydalanib, XSS zaifliklarini toping ☰](https://portswigger.net/burp/vulnerability-scanner)
{% endhint %}

## <mark style="color:yellow;">Cross-site scriptingga oid eng ko'p so'raladigan savollar</mark> <a href="#cross-site-scripting-ga-oid-eng-kop-soraladigan-savollar" id="cross-site-scripting-ga-oid-eng-kop-soraladigan-savollar"></a>

**XSS zaifliklari keng tarqalganmi?** XSS zaifliklari hozirgi davrda eng ko'p tarqalgan zaifliklardan biri hisoblanadi.

**XSS hujumlari qanchalik keng tarqalgan?** Haqiqiy XSS hujumlari haqida ishonchli ma'lumotlarni olish qiyin, lekin u boshqa zaifliklarga qaraganda kamroq foydalaniladi.

**XSS va CSRF o'rtasida qanday farq bor?** XSS zararli JavaScriptni bajarishni o'z ichiga olsa, CSRF jabrlangan foydalanuvchini o'zi istamagan amallarni bajarishiga majburlaydi.

**XSS va SQL Ineksiya orasida qanday farq bor?** XSS browser tomonlama boshqa foydalanuvchilarni nishonga oladi, SQL ineksiya esa server tomonlama zaiflik bo'lib u web saytning ma'lumotlar bazasini nishonga oladi.

**PHPda XSS ni oldini qanday olsam bo'ladi?** Kiritilgan maʼlumotlarni ruxsat etilgan belgilar **whitelist** bilan filtrlang va koʻrsatmalar turi yoki translatsiya turidan foydalaning. Natijalaringizni `htmlentities` va HTML kontekstlari uchun `ENT_QUOTES` yoki JavaScript kontekstlari uchun **JavaScript Unicode escapes** bilan saqlang.

**Javada XSS ni qanday oldini olsam bo'ladi?** Input qismlarni ruxsat etilgan **whitelist** bilan filtrlang va HTML kontekstlaridagi ma'lumotni HTML-kodlash orqali [<mark style="color:yellow;">Google Guava</mark> ](https://github.com/google/guava)kabi kutubxonadan foydalaning yoki JavaScript kontekstlari uchun **JavaScript Unicode escape**laridan foydalaning.
