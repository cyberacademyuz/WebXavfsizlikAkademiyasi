# Directory traversal


Ushbu bo'limda <b>directory traversal</b> nima ekanligini tushuntiramiz, directory traversal hujumlarini qanday amalga oshirish va umumiy to'siqlarni chetlab o'tishni ko'rib chiqamiz va directory traversal zaifliklarini oldini olishni o'rgatamiz.



{% hint style="warning" %}
<mark style="color:yellow;">**Labaratoriyalar:**</mark>

Agar siz directory traversal haqida bilsangiz pastdagi link orqali, haqiqiy web sayt kabi tuzilgan laboratoriyalarni yechishingiz mumkin.[<mark style="color:yellow;"></mark>\ <mark style="color:yellow;">Barcha directory traversal labaratoriyalarini ko'rish ≫</mark>](https://portswigger.net/web-security/all-labs#directory-traversal)<mark style="color:yellow;"></mark>
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
<mark style="color:yellow;">**Lab:**</mark> [Oddiy holatdagi file path traversal≫](https://portswigger.net/web-security/file-path-traversal/lab-simple)
{% endhint %}
