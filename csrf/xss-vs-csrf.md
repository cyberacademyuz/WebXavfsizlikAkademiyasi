# XSS vs CSRF

Biz ushbu bo'limda **XSS** va **CSRF** orasidagi farqlarni gaplashamiz va [<mark style="color:yellow;">**CSRF tokenlar**</mark>](csrf-tokenlar.md) qanday qilib **XSS** xujumlarni oldini olishini o'rganamiz.

## <mark style="color:yellow;">XSS va CSRF orasidagi farq qanday ?</mark> <a href="#xss-va-csrf-orasidagi-farq-nima" id="xss-va-csrf-orasidagi-farq-nima"></a>

<mark style="color:yellow;">****</mark>[<mark style="color:yellow;">**Cross-site scripting**</mark>](broken-reference) (**XSS**) Hakerga JavaScript kodlarini web saytda yozib ishga tushirish orqali foydalanuvchini zararlashga aytiladi.

<mark style="color:yellow;">****</mark>[<mark style="color:yellow;">**Cross-site Request Forgery**</mark> <mark style="color:yellow;"></mark><mark style="color:yellow;"></mark> ](broken-reference)(**CSRF**) Haker foydalanuvchini o'zi xohlamagan amallarni bajarishiga majbur qilishiga aytiladi.

Ko'pgina xollarda **XSS** xujumlarning natijalari **CSRF** xujumlarnikiga qaraganda jiddiyroq hisoblanadi:

* **CSRF** ko'p hollarda faqatgina foydalanuvchining ba'zi bir amalga oshira oladigan amallariga ta'sir o'tkazadi. Bir ikkita holatlarni hisobga olmasak ko'pgina saytlar **CSRF** hujumiga qarshi ximoyalangan. Muvaffaqiyatli **XSS** xujumi odatda foydalanuvchini zaiflik paydo bo'lgan funksionalligidan qat'i nazar, foydalanuvchi bajarishi mumkin bo'lgan har qanday harakatni bajarishga undashi mumkin.
* Shuningdek **CSRF** "one-way" (bir tomonlama) zaifligi deb ham ataladi, sababi haker jo'natayotgan so'rovlarning javoblari qanday kelganini javobini bilan olmaydi. Aksincha, XSS "two-way"(ikki tomonlama) bo'lib, Haker tomonidan kiritilgan skript o'zboshimchalik bilan so'rovlar berishi, javoblarni o'qishi va ma'lumotlarni Haker tanlagan tashqi domenga chiqarishi mumkin.

## <mark style="color:yellow;">CSRF tokenlar XSS xujumlarni oldini ola oladimi ?</mark> <a href="#csrf-tokenlar-xss-xujumlarni-oldini-ola-oladimi" id="csrf-tokenlar-xss-xujumlarni-oldini-ola-oladimi"></a>

Ayrim **XSS** xujumlarini oldini olishni ba'zan eng to'g'ri va yaxshi yo'li bu **CSRF tokenlar**dan foydalanish hisoblanadi. Keling sodda **reflected XSS** xujumi misolida ko'ramiz:

```url
https://insecure-website.com/status?message=<script>/*+Bad+stuff+here...+*/</script>
```

Ushbu funksiya uchun hozir **CSRF token** kerak:

```url
https://insecure-website.com/status?csrf-token=CIwNZNlR4XbisJF39I8yWnWX9wX4WFoz&message=<script>/*+Bad+stuff+here...+*/</script>
```

Shu holatda Server **CSRF token**ni kutadi va agarda token bo'lmasa yuborilgan so'rovni qabul qilmay ortga qaytarib yuboradi, shu orqali **CSRF token** **XSS** xujumni oldini olishi mumkin. **CSRF** xujumlarini oldini olish evaziga saytga **XSS** xujumlarini biroz bo'lsada oldini olinadi.

Bir qancha muhim ogohlantirishlar mavjud:

* Agar [<mark style="color:yellow;">**reflected XSS**</mark>](../xss/reflected-xss.md) zaifligi saytning boshqa biron bir joyida **CSRF token**i bilan himoyalanmagan funksiya doirasida mavjud bo'lsa, u holda **XSS** dan odatdagidek foydalanish mumkin.
* Agar saytning istalgan joyida foydalanish mumkin bo'lgan **XSS** zaifligi mavjud bo'lsa, u holda zaiflikdan foydalanuvchining harakatlarni amalga oshirishga majburlash uchun foydalanish mumkin, hatto bu harakatlarning o'zi **CSRF token**lari bilan himoyalangan bo'lsa ham ishlaydi. Bunday holatda, Hakerning skripti kerakli sahifadan to'g'ri [<mark style="color:yellow;">**CSRF token**</mark>](csrf-tokenlar.md)ini olish uchun so'rashi va keyin himoyalangan amalni bajarish uchun tokendan foydalanishi mumkin.
* **CSRF token**lari [<mark style="color:yellow;">**stored XSS**</mark>](../xss/stored-xss.md)ga qarshi kurashmaydi. Agar sahifa **CSRF token** bilan himoyalangan bo'lsa ham [<mark style="color:yellow;">**stored XSS**</mark>](../xss/stored-xss.md)ni amalga oshirish mumkin. Shu xolatida **XSS** xujumini amalga oshirish va foydalanuvchi sahifaga kirganida skriptni ishga tushirish mumkin.
