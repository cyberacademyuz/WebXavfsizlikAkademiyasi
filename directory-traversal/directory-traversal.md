Ushbu bo'limda <b>directory traversal</b> nima ekanligini tushuntiramiz, directory traversal hujumlarini qanday amalga oshirish va umumiy to'siqlarni chetlab o'tishni ko'rib chiqamiz va directory traversal zaifliklarini oldini olishni o'rgatamiz.

{% hint style="warning" %}
<mark style="color:yellow;">**Labaratoriyalar:**</mark>

Agar siz directory traversal haqida bilsangiz pastdagi link orqali, haqiqiy web sayt kabi tuzilgan laboratoriyalarni yechishingiz mumkin.[<mark style="color:yellow;"></mark> <mark style="color:yellow;">Barcha directory traversal labaratoriyalarini ko'rish ≫</mark>](https://portswigger.net/web-security/all-labs#directory-traversal)<mark style="color:yellow;"></mark>
{% endhint %}

# <mark style="color:yellow;">Directory traversal nima ?</mark>

Directory traversal (shuningdek, file path traversal deb ham ataladi) bu hackerga websayt joylashgan serverdagi fayllarni o'zboshimchalik bilan o'qish imkonini beruvchi web-xavfsizlik zaifligi. Bunga websayt kodi va maʼlumotlari, ichki tizimlar uchun hisob maʼlumotlari va operatsion tizimning maxfiy fayllari kiradi. Ba'zi holatlarda hacker serverdagi istalgan fayllarga yozishi mumkin, bu unga websayt ma'lumotlarini yoki xatti-harakatlarini o'zgartirishi va oxir-oqibat serverni to'liq nazorat qilish imkonini beradi.

# <mark style="color:yellow;"> Directory traversal orqali fayllarni o'qish</mark>

Sotiladigan mahsulotlar rasmlari mavjud online xarid websaytini ko'zdan kechiring.  Rasmlar HTML orqali yuklanadi, masalan:

```
<img src="/loadImage?filename=218.png">
```

 `loadImage` URL manzili `filename` parametrini oladi va belgilangan faylni qaytaradi. Rasm fayllari diskda /var/www/images/ joylashuvida saqlanadi. Rasmni ko'rsatish uchun websayt ushbu asosiy katalogga so'ralgan fayl nomini qo'shadi va faylni o'qish uchun filesystem API dan foydalanadi. Yuqoridagi holatda, websayt faylni quyidagi fayl joylashuvidan o'qiydi:

```
/var/www/images/218.png
```

Websayt directory traversalga qarshi himoyani amalga oshirmaydi, shuning uchun hacker serverning fayl tizimidan o'zboshimchalik bilan faylni olish uchun quyidagi URL manziliga request yuborishi mumkin:

```
https://insecure-website.com/loadImage?filename=../../../etc/passwd
```

Bu websaytnig quyidagi fayl yo'lini o'qishiga olib keladi:

```
/var/www/images/../../../etc/passwd
```

`../` ketma-ketligi fayl joylashuviga o'tishga yordam beradi va har bir `../` bitta katalog ortga qaytishni anglatadi. Uchta `../` ketma-ketligi /var/www/images/ dan fayl tizimining root (`/`) qismiga o'tadi, shu sababli quyidagi fayl o'qiladi:

```
/etc/passwd
```

Unix-ga asoslangan operatsion tizimlarda bu standart fayl bo'lib, serverda ro'yxatdan o'tgan foydalanuvchilar haqidagi ma'lumotlarni o'z ichiga oladi.

Windows-da `../` va `..\` ikkalasi ham kataloglarga o'tish ketma-ketligi bo'lib, operatsion tizimdagi standart faylini olish quyidagicha bo'ladi:

```
https://insecure-website.com/loadImage?filename=..\..\..\windows\win.ini
```
{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Oddiy holatdagi file path traversal ≫](https://portswigger.net/web-security/file-path-traversal/lab-simple)
{% endhint %}

# <mark style="color:yellow;">File path traversal zaifliklarini exploit qilishdagi umumiy to'siqlar</mark>

Foydalanuvchi kiritgan ma'lumotlarni fayl yo'llariga joylashtirishni amalga oshiruvchi ko'plab websaytlar path traversal hujumlaridan himoya qiladi ammo ko'pincha ularni chetlab o'tish mumkin.

Agar websayt foydalanuvchi kiritgan fayl nomidan katalog yo'lini olib tashlasa yoki bloklasa, turli usullar yordamida bu himoyani chetlab o'tish mumkin.

Fayl tizimining root qismidan foydalanishingiz mumkin, masalan, `filename=/etc/passwd`, hech qanday `../` dan foydalanmasdan to'g'ridan-to'g'ri faylga borish uchun.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Ichki yo'lni chetlab o'tish orqali bloklangan yo'l ketma-ketliklari (../) da path traversal hujumi ≫](https://portswigger.net/web-security/file-path-traversal/lab-absolute-path-bypass)
{% endhint %}

Siz `....//` yoki `....\/` kabi ichki o'tish ketma-ketliklaridan foydalanishingiz mumkin, ular ichki ketma-ketlik o'chirilganda oddiy o'tish ketma-ketliklariga qaytadi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Rekursiv bo'lmagan holda olib tashlangan o'tish ketma-ketligi bilan file path traversal hujumi ≫](https://portswigger.net/web-security/file-path-traversal/lab-sequences-stripped-non-recursively)
{% endhint %}

URL yo'li yoki multipart/form-data requestining filename parametri kabi ba'zi kontekstlarda web-serverlar kiritilgan ma'lumotni websaytga o'tkazishdan oldin har qanday kataloglarni o'tkazish ketma-ketligini olib tashlashi mumkin.  Ba'zan siz URL encode yoki URL encodedan ikki marta foydalanib, `../` belgilar yordamida bu turdagi filtrdan o'tishingiz mumkin, natijada `../`, %2e%2e%2f yoki `../../` %252e%252e%252f bo'ladi. `..%c0%af` yoki `..%ef%bc%8f` kabi turli nostandart encode ham yordam berishi mumkin.

<mark style="color:yellow;">[Burp Suite Professional](https://portswigger.net/burp/pro)</mark> foydalanuvchilari uchun Burp Intruder sinab ko'rishingiz mumkin bo'lgan turli xil encodelangan path traversal ketma-ketligini o'z ichiga olgan built-in payloadlar ro'yxatini <b>(Fuzzing - path traversal)</b> taqdim etadi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [O'tish (/) belgilarini 2 marta URL-decodelash bilan olib tashlab file path traversal ≫](https://portswigger.net/web-security/file-path-traversal/lab-superfluous-url-decode)
{% endhint %}

Agar websayt foydalanuvchi tomonidan berilgan fayl nomini kerakli papka bilan boshlanishini talab qilsa, masalan, `/var/www/images`, u holda birinchi talab qilingan papkani, keyin esa kerakli harakatlanish ketma-ketligini kiritish mumkin.  Masalan:

```
filename=/var/www/images/../../../etc/
```

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Pathning boshlanishini tekshirilgan holatda path traversal ≫](https://portswigger.net/web-security/file-path-traversal/lab-validate-start-of-path)
{% endhint %}

Agar websayt foydalanuvchi tomonidan kiritilgan fayl nomi kutilgan fayl kengaytmasi bilan tugashini talab qilsa, masalan, `.png`bilan, u holda fayl yo'lini kerakli kengaytmadan oldin tugatish uchun null bytedan foydalanish mumkin.  Masalan:

```
filename=../../../etc/passwd%00.png
```

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Pathning boshlanishini tekshirilishini null byte bilan chetlab o'tish ≫](https://portswigger.net/web-security/file-path-traversal/lab-validate-file-extension-null-byte-bypass)
{% endhint %}

# <mark style="color:yellow">Directory traversal hujumini qanday oldini olinadi</mark>

File path traversal zaifliklarini oldini olishning eng samarali usuli foydalanuvchi ma'lumotlarining fayl tizimi API ga o'tkazilishini to'liq oldini olishdir.  Bamalga oshiradigan ko'plab websayt funktsiyalari xuddi shu xatti-harakatni yanada xavfsizroq ta'minlash uchun qayta yozilishi mumkin.

Agar foydalanuvchi ma'lumotlarini fayl tizimning API siga o'tkazish shart bo'lsa, hujumlarning oldini olish uchun ikkita himoya usulidan foydalanish kerak:

* Websayt uni qayta ishlashdan oldin foydalanuvchi kiritgan ma'lumotni tekshirishi kerak. Eng yaxshi usul, ruxsat berilgan qiymatlarni white listga kiritish va solishtirish kerak. Agar talab qilinadigan funksionllikda buning iloji bo'lmasa, foydalanuvchi kiritgan qiymay faqat alpha-numeric belgilar kabi ruhxsat berilgan qiymatlardan iborat ekanliliga ishonch hosil qilishi kerak.

* kiritilgan ma'lumotni tekshirgandan so'ng, websayt uni asosiy katalogga qo'shishi va yo'lni kanoniklashtirish uchun platformaning fayl tizimi API-dan foydalanishi kerak.  U kanoniklashtirilgan yo'lni kutilgan katalogdan boshlanishini ta'minlashi kerak.

Quyida user inputiga asoslangan kanonik fayl yo'lini tekshirish uchun oddiy Java kodining namunasi keltirilgan:

```
File name = new File(BASE_DIRECTORY, userInput);
if (file.getCanonicalPath().startsWith(BASE_DIRECTORY)) {
    // proccess file
}
```

{% hint style="info" %}
<b>Ko'proq o'qish</b>
[Burp Suitening web zaiflik skaneri yordamida directory traversal zaifliklarini topishingiz](https://portswigger.net/burp/vulnerability-scanner)
{% endhint %}