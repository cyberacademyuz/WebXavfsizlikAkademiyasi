# Reflected XSS

![](../.gitbook/assets/reflected-xss.png)

Biz ushbu bo'limda XSS zaifliklaridan biri bo'lmish **Reflected XSS** haqida gaplashamiz va Reflected XSS zaifliklarini qanday topishni o'rganamiz.

## <mark style="color:yellow;">Reflected Cross-site scripting nima?</mark> <a href="#reflected-cross-site-scripting-nima" id="reflected-cross-site-scripting-nima"></a>

**Reflected XSS** - web sayt HTTP requestida ma'lumotlarni qabul qilganda paydo bo'ladi va bu ma'lumotlar xavfli tarzda darhol responsega aylanadi.

Aytaylik bitta web sayt foydalanuvchi kiritgan ma'lumotlarni qidiruv funksiyasida parametr sifatida qabul qilib URL yaratadi:

```url
https://insecure-website.com/search?term=gift
```

Web sayt responseni mana bunday ko'rinishda qaytaradi:

```html
<p>You searched for: gift</p>
```

Web sayt ma'lumotlarga boshqa protsessni amalga oshirmasa, haker quyidagi hujumni amalga oshirishi mumkin:

```url
https://insecure-website.com/search?term=<script>/*+Bad+stuff+here...+*/</script>
```

Ushbu URL natijalari quyidagicha ko'rinish oladi:

```html
<p>You searched for: <script>/* Bad stuff here... */</script></p>
```

Agar web saytning boshqa foydalanuvchisi hakerning URL-manziliga tashrif buyursa, haker tomonidan taqdim etilgan skript jabrlanuvchining brauzerida, ular o'rtasida web sayt bilan sessiyasi kontekstida ishlaydi va ularni o'g'irlashi mumkin.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Hech qanday enkodlashsiz Reflected XSS ≫](https://portswigger.net/web-security/cross-site-scripting/reflected/lab-html-context-nothing-encoded)
{% endhint %}

## <mark style="color:yellow;">Reflected XSS hujumining ta'siri</mark> <a href="#reflected-xss-ning-tasiri" id="reflected-xss-ning-tasiri"></a>

Agar haker jabrlanuvchining brauzerida bajariladigan skriptni boshqarishi mumkin bo'lsa,  odatda ushbu foydalanuvchini to'liq buzishi mumkin. Shu bilan bir qatorda haker:

* Web sayt ichida foydalanuvchi bajara oladigan har qanday amalni bajarishi.
* Foydalanuvchi ko'ra oladigan har qanday ma'lumotni ko'rishi.
* Foydalanuvchi o'zgartira oladigan har qanday ma'lumotni o'zgartira olishi.
* Web saytning boshqa foydalanuvchilari bilan, shu jumladan jabrlangan foydalanuvchidan kelib chiqadigan zararli hujumlar bilan o'zaro aloqalarni boshlashi mumkin.

Haker jabrlangan foydalanuvchini nazorat qiladigan so'rov yuborishda, reflected XSS hujumini amalga oshirishga undashi mumkin bo'lgan turli xil usullar mavjud. Bularga haker tomonidan boshqariladigan web-saytga yoki boshqa web-saytga havolalarni joylashtirish yoki elektron pochta, tvit yoki boshqa xabarlarda havola yuborishlar kiradi. Hujum to'g'ridan-to'g'ri ma'lum foydalanuvchiga qarshi qaratilgan bo'lishi mumkin yoki dasturning har qanday foydalanuvchisiga qarshi tasodifiy amalga oshiriladigan hujum bo'lishi mumkin:

Reflected XSS ta'siri odatda Stored XSSga qaraganda kuchsizroq  ekanligini anglatadi, bunda himoyasiz web saytning o'zida mustaqil hujum amalga oshirilishi mumkin.

{% hint style="info" %}
**Ko'proq o'qish:**

[XSS zaifliklarini eksplutatatsiya qilish ☰](xss-zaifliklarini-exploit-qilish.md)
{% endhint %}

## <mark style="color:yellow;">Reflected XSS turli xil konteksda</mark> <a href="#reflected-xss-har-xil-kontekslarda" id="reflected-xss-har-xil-kontekslarda"></a>

Cross-site scripting ni turli xil turlari mavjud. Web sayt responsesidagi ma'lumotlarning joylashuvi undan foydalanish uchun qanday payload kerakligini belgilaydi va zaiflikga ham ta'sir qilishi mumkin.

Bunga qo'shimcha ravishda, agar web saytda taqdim etilgan ma'lumotlar aks ettirilgunga qadar har qanday tekshirish yoki boshqa ishlov berishni amalga oshirsa, bu odatda qanday XSS payloadi kerakligiga ta'sir qiladi.

{% hint style="info" %}
**Ko'proq o'qish:**

[Reflected  XSS turli xil konteksda ☰](xss-kontekstlari.md)
{% endhint %}

## <mark style="color:yellow;">Reflected XSS zaifliklarini topish va test qilish</mark> <a href="#reflected-xss-zaifliklarini-topish-va-test-qilish" id="reflected-xss-zaifliklarini-topish-va-test-qilish"></a>

Reflected Cross-site scripting zaifliklarning katta qismi [<mark style="color:yellow;">Burp Suite web-zaiflik skaneri</mark>](https://portswigger.net/burp/vulnerability-scanner) yordamida tez va ishonchli tarzda topish mumkin.

Reflected XSS ni test qilish odatda quyidagi bosqichlarni o'z ichiga oladi:

* **Har bir entry point tekshirish.** Web saytning HTTP so'rovlaridagi ma'lumotlar uchun har bir kirish nuqtasini alohida sinab ko'ring. Bunga URL so'rovlar qatori va xabarlar matni va URL fayl yo'lidagi parametrlar yoki boshqa ma'lumotlar kiradi. U HTTP headerlarini ham o'z ichiga oladi, ammo faqat ma'lum HTTP headerlari orqali ishga tushirilishi mumkin bo'lgan XSS-ga o'xshash xatti-harakatlar amalda ishlatilmasligi mumkin.
* **Alfa raqamli qiymatlarni yuboring.** Har bir kirish nuqtasi uchun tasodifiy qiymat kiriting va qiymat resonseda aks ettirilganligini aniqlang. Qiymat ko'pgina XSS tekshiruvlari uchun himoyalangan bo'lishi kerak, shuning uchun juda qisqa bo'lishi va faqat alfa raqamli belgilarni o'z ichiga olishi kerak. Ammo response ichida tasodifiy moslashish ehtimoli juda kam bo'lishi uchun u yetarlicha uzun bo'lishi kerak. Taxminan 8 ta belgidan iborat tasodifiy alfa raqamli qiymat odatda ideal hisoblanadi. Tegishli tasodifiy qiymatlarni yaratish uchun Burp Intruder sonining payloadlarini tasodifiy yaratilgan qiymatlari bilan ishlatishingiz mumkin. Siz yuborilgan qiymatni o'z ichiga olgan javoblarni avtomatik ravishda belgilash uchun Burp Intruderning [<mark style="color:yellow;">grep payloadlar opsiyasi</mark>](https://portswigger.net/burp/documentation/desktop/tools/intruder/options#grep-payloads)dan foydalanishingiz mumkin.
* **Reflection kontekstni aniqlang.** Tasodifiy qiymat aks ettirilgan response ichidagi har bir joy uchun uning kontekstini aniqlang. Bu HTML teglari orasidagi matnda, iqtibos keltirilishi mumkin bo'lgan teg atributida, JavaScript qatorida va hokazolarda bo'lishi mumkin.
* **Mahsus payloadni test qilib ko'ring.** Reflected kontekstiga asoslanib, responseda o'zgartirilmagan holda aks ettirilsa, JavaScript-ni ishga tushirishni boshlaydigan dastlabki mahsus XSS payloadini sinab ko'ring. Payloadlarni sinab ko'rishning eng oson yo'li so'rovni [<mark style="color:yellow;">Burp Repeater</mark>](https://portswigger.net/burp/documentation/desktop/tools/repeater)-ga yuborish, mahsus payloadni kiritish uchun requestni o'zgartirish, request yuborish va undan so'ng payload ishlaganligini tekshirish uchun responseni ko'rib chiqishdir. Ishlashning samarali usuli requestda haqiqy random qiymatni qoldirish va maxsus XSS payloadini undan oldin yoki keyin joylashtirishdir. Keyin Burp Repaterning javob ko'rinishi bo'yicha qidiruv atamasi sifatida random qiymatni sinab ko'ring. Burpda qidirilgan so'z bor bo'lgan har bir joy ajratib ko'rsatilgani sababli uni tezda aniqlash imkonini beradi
* **Alternativ payloadlarni test qilish.** Agar kerakli payload web sayt tomonidan o'zgartirilsa yoki bloklansa unda siz XSS hujumi amalga oshirish uchun boshqa payloadlarni yoki boshqa usullarni sinab ko'rishda davom etishingiz kerak.Qo'shimcha ma'lumot olish uchun, <mark style="color:yellow;">Cross site script kontekstlarga( tez kunda)</mark> qarang
* **Xujumni brauzerda sinab ko'rish.** Agar siz qilayotgan urinishlar nihoyat Burp Repeaterda muvaffaqiyatli tugasa, xujumni brauzerda bajraing (URL manzilini manzil satriga joylashtirish yoki [<mark style="color:yellow;">Burp Projy-ning intercept</mark>](https://portswigger.net/burp/documentation/desktop/tools/proxy/intercept) ko'rinishda so'rovni o'zgartirish orqali va kiritilgan JavaScript payload haqiqatan ham bajarilganligini tekshirish orqali. Ko'pincha, agar hujum muvaffaqiyatli bo'lsa, brauzerda ko'rinadigan qalqib chiquvchi oynani ishga tushiradigan alert(document.domain) kabi oddiy JavaScript-ni bajarish yaxshi.)

## <mark style="color:yellow;">Reflected XSS bo'yicha so'raladigan eng ko'p savollar</mark> <a href="#reflected-xss-boyicha-soraladigan-eng-kop-savollar" id="reflected-xss-boyicha-soraladigan-eng-kop-savollar"></a>

**Reflected XSS va Stored XSS orasida qanday farqlar mavjud?** Reflected XSS web sayt HTTP requestida ma'lumotlarni qabul qilganida paydo bo'ladi va bu ma'lumotlar xavfli tarzda darhol responsega aylanadi, stored XSS da esa web sayt input orqali kiritlgan ma'lumotlarni olib keyinroq ko'rsatadi.

**Reflected XSS va Self XSS orasida qanday farq bor?** Self-XSS odatiy reflected XSSga o'xshash web sayt xatti-harakatlarini o'z ichiga oladi, ammo uni yaratilgan URL yoki o'zaro domen so'rovi orqali oddiy usullarda ishga tushirish mumkin emas. Buning o'rniga, zaiflik faqatgina jabrlanuvchi o'z brauzeridan XSS payloadini yuborgan taqdirdagina paydo bo'ladi. O'z-o'zidan XSS hujumini amalga oshirish, odatda, jabrlanuvchini brauzeriga haker tomonidan taqdim etilgan ba'zi ma'lumotlarni joylashtirish uchun ijtimoiy muhandislik qilishni o'z ichiga oladi.

