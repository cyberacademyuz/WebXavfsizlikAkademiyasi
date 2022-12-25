# Content Security Policy

Ushbu bo'limda CSP nima ekanligi haqida va ko'p tarqalgan xujumlarning havfini qanday qilib kamaytirish mumkinlgi haqida gaplashamiz.

## <mark style="color:yellow;">CSP (Content Security Policy) nima?</mark> <a href="#csp-content-security-policy-nima" id="csp-content-security-policy-nima"></a>

CSP bu [<mark style="color:yellow;">XSS</mark>](broken-reference) va shunga o'xshash xujumlarni zararsizlantiruvchi mexanizm. U web sahifa yuklashi mumkin bo'lgan ma'lumotlar(skriptlar/rasmlar)ni  cheklash va sahifani boshqa sahifalar bilan framelashni cheklash orqali ishlaydi.

CSPni yoqish uchun bu siyosatni oʻz ichiga olgan `Content-Security-Policy` nomli HTTP  response headeri boʻlishi kerak. Siyosatning o'zi nuqta-vergul bilan ajratilgan bir yoki bir nechta ko'rsatmalardan iborat.

<figure><img src="../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

## <mark style="color:yellow;">XSS xujumlarini CSP orqali zararsizlash</mark> <a href="#xss-xujumlariga-csp-orqali-yumshatish" id="xss-xujumlariga-csp-orqali-yumshatish"></a>

Quyidagi usul faqatgina <mark style="color:yellow;">same orign</mark>dan olingan scriptlarni ishlatishga ruxsat beradi

```http
script-src 'self'
```

Quyidagi misol faqat ma'lum bir domendan skriptlarni yuklashga ruxsat beradi:

```http
script-src https://scripts.normal-website.com
```

Tashqi domenlardan skriptlarga ruxsat berayotganda extiyot bo'lish kerak. Agar tashqi domendan taqdim etilayotgan kontentni haker boshqarishining biron bir usuli bo'lsa, u hujumni amalga oshirishi mumkin. Masalan, `ajax.googleapis.com` kabi mijoz uchun URL manzillaridan foydalanmaydigan CDNlarga ishonmaslik kerak, chunki uchinchi tomonlar o‘z domenlariga ma'lumot yetkazishlari mumkin.

Muayyan domenlarni whitelistga kiritishdan tashqari, CSP havfsiz manbalarni ko'rsatishning yana ikkita usulini ham taqdim etadi, bular: `nonces` va `xeshlar`:

* CSP yo'naltiruvchisi `nonce` (tasodifiy qiymat)ni belgilashi mumkin va xuddi shu qiymat skriptni yuklaydigan tegda ishlatilishi kerak. Agar qiymatlar mos kelmasa, skript ishlamaydi. Boshqaruv samarali bo'lishi uchun har bir sahifa yuklanishida `nonce` xavfsiz tarzda yaratilishi va haker uni tahminan ham qilolmasligi kerak.
* CSP yo'naltiruvchisi havsiz skript tarkibining xeshini belgilashi mumkin. Agar haqiqiy skriptning xeshi yo'naltiruvchida ko'rsatilgan qiymatga mos kelmasa, u holda skript ishlamaydi. Agar skript har doim o'zgarib qolsa, siz, albatta, misolda ko'rsatilgan xesh qiymatini yangilashingiz kerak bo'ladi.

CSP ko'pincha scriptga o'xshash resurslarni bloklaydi. Biroq ko'pgina CSPlar rasmlarga ruxsat beradi. Bu shuni anglatadiki siz `img` elementlariga bemalol tashqi server dan so'rovlarni yaratishingiz mumkin, masalan bu orqali CSRF tokenlarni oshkor qilish mumkin.

Ba'zi brauzerlar, masalan Chrome, <mark style="color:yellow;">dangling markup</mark> tasirini kamaytirish xususiyatiga ega bo'lib, u ma'lum bir ( ',",$,<> -  kabi) belgilar mavjud bo'lgan so'rovlarni bloklaydi.

Ba'zi siyosatlar yanada cheklovchi va tashqi so'rovlarning har qanday shakllarini oldini oladi. Biroq ba'zi foydalanuvchilarning huquqlarini ko'tarish orqali ushbu cheklovlardan o'tish mumkin. Ushbu siyosatni chetlab o'tish uchun siz HTML elementini kiritishingiz kerak, u bosilganda kiritilgan elementdagi hamma narsani saqlaydi va tashqi serverga yuboradi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Juda qatiy CSP orqali himoyalangan dangling markup hujumida Reflected XSS ≫](https://portswigger.net/web-security/cross-site-scripting/content-security-policy/lab-very-strict-csp-with-dangling-markup-attack)
{% endhint %}

## <mark style="color:yellow;">Dangling markupni CSP orqali zararsizlash</mark> <a href="#dangling-markup-ni-csp-orqali-yumshatish" id="dangling-markup-ni-csp-orqali-yumshatish"></a>

Quyidagi misolsa rasmlarni faqat sahifaning o'zi bilan bir xil manbadan yuklash imkonini beradi:

```
img-src 'self'
```

Quyidagi misolda faqat ma'lum bir domendan rasmlarni yuklashga ruxsat beradi:

```
img-src https://images.normal-website.com
```

Shuni esda tutingki, ushbu siyosatlar ba'zi <mark style="color:yellow;">dangling markup</mark> exploitlarini oldini oladi, chunki foydalanuvchi ma'lumotlarini olishning oson yo'li `img` tegidan foydalanishdir. Biroq, bu boshqa exploitlarni oldini olmaydi, masalan, `href` atributiga ega bo'lgan `a` tegini kiritadigan exploitlarni.

## <mark style="color:yellow;">Siyosat kiritish bilan CSPni bypass qilish</mark> <a href="#siyosat-kiritish-bilan-cspni-chetlab-otish" id="siyosat-kiritish-bilan-cspni-chetlab-otish"></a>

Siz web sayt inputini haqiqiy siyosatga kiritishi mumkinligini ko'rishingiz mumkin, masalan report uri usuliga ko'proq duch kelishingiz mumkin<mark style="color:yellow;">.</mark> Agar sayt siz boshqarishingiz mumkin bo'lgan parametrni aks ettirsa, o'zingizning CSP yo'nalitiruvchilaringizni qo'shish uchun nuqta-vergul qo'yishingiz mumkin. Odatda, bu `report-uri` yo'naltiruvchisi ro'yxatda oxirgisi hisoblanadi. Bu shuni anglatadiki ushbu zaiflikdan foydalanish va siyosatni aylanib o'tish uchun mavjud ko'rsatmalarni qayta yozishingiz kerak bo'ladi.

Odatda, `cript-src` yo'naltiruvchisini qayta yozish mumkin emas. Biroq, Chrome yaqinda `script-src-elem` ni qo'shdi, bu sizga skript elementlarini boshqarish imkonini beradi, lekin eventlarni emas. Eng muhimi, u `script-src` ni qayta yozishga imkon beradi. Bu bilimlardan foydalanib quyidagi laboratoriyani hal qila olishingiz kerak.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [CSP ni aylanib o'tish orqali CSP himoyasiga Reflected XSS hujumi ≫](https://portswigger.net/web-security/cross-site-scripting/content-security-policy/lab-csp-bypass)
{% endhint %}

## <mark style="color:yellow;">CSP orqali</mark> [<mark style="color:yellow;">Clickjacking</mark>](broken-reference)<mark style="color:yellow;">dan himoyalanish</mark> <a href="#csp-orqali-clickjacking-dan-himoya" id="csp-orqali-clickjacking-dan-himoya"></a>

Quyidagi misol sahifani faqat <mark style="color:yellow;">same orign</mark>dagi boshqa sahifalar bilan framelash imkonini beradi:

```
frame-ancestors 'self'
```

Quyidagi misol frame yaratishni butunlay oldini oladi:

```
frame-ancestors 'none'
```

`X-Frame-Options` headerini ishlatishdan ko'ra, Clickjackingni oldini olish uchun CSP dan foydalanish ancha qulay, chunki siz bir nechta domenlarni belgilashingiz va wildcardlardan foydalanishingiz mumkin. Misol uchun:

```http
frame-ancestors 'self' https://normal-website.com https://*.robust-website.com
```

CSP, shuningdek, asosiy freym ierarxiyasidagi har bir freymni tasdiqlaydi, `X-Frame-Options` esa faqat yuqori darajadagi frameni tasdiqlaydi.

<mark style="color:yellow;">Clickjacking</mark> hujumlaridan himoya qilish uchun CSP dan foydalanish tavsiya etiladi. Internet Explorer kabi CSP-ni qo'llab-quvvatlamaydigan eski brauzerlarda himoyani ta'minlash uchun buni X-Frame-Options headeri bilan ham birlashtira olasiz.
