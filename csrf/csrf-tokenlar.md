# CSRF tokenlar

Biz ushbu bo'limda **CSRF token**lari qanday qilib **CSRF xujumlar**ning oldini olishi va ularni qanday qilib yasash kerakligi haqida o'rganamiz.

## <mark style="color:yellow;">CSRF tokenlar nima ?</mark> <a href="#csrf-tokenlar-nima" id="csrf-tokenlar-nima"></a>

**CSRF token** bu oldindan taxmin qilib bo'lmaydigan, uniqe qiymatga ega tokenlardir, ular serverga foydalanuvchi tomonidan so'rov jo'natilayotganda parametr sifatida qo'shib jo'natiladi. Ushbu tokenlarni server birozdan so'ng qabul qiladi va tekshiradi. Agarda ularning qiymatlari to'g'ri kelsa unda uni server qabul qiladi, bo'lmasa qabul qilmaydi.

**CSRF token**lar **CSRF** xujumlarning oldini oladi, ya'ni Haker tokenga oldindan qiymat berolmaydi va foydalanuvchining amallariga ta'sir o'tkazolmaydi. Shu sababdan haker endi **CSRF xujum** qilayotganida parametrlarni berolmaydi va xujum qila olmaydi.

## <mark style="color:yellow;">CSRF tokenlar qanday yasalishi kerak ?</mark> <a href="#csrf-tokenlar-qanday-yasalishi-kerak" id="csrf-tokenlar-qanday-yasalishi-kerak"></a>

**CSRF token**lar oldindan taxmin qilib bo'lmaydigan, sirli xolatda bo'lishi kerak, ya'ni Session tokenlarida qanday xususiyatlar bo'lishi kerak bo'lsa shunday xususiyatlarga ega bo'lishi kerak.

Biz sizga **cryptographic strength pseudo-random number generator (PRNG)** ni ishlatgan holda yasashingizni tavsiya qilamiz, chunki ushbu genarator asnosida siz vaqtinchalik bo'lgan static sirli tokenni yarata olasiz.

Agar sizga PRNG imkoniyatlaridan tashqari qo'shimcha xavfsizlik kerak bo'lsa, siz uning **CSRF tokeni**ni ba'zi bir foydalanuvchiga xos entropiya bilan birlashtirib, individual tokenlarni yaratishingiz va butun tuzilishning kuchli xeshini olishingiz mumkin. Bu ularga berilgan namuna asosida tokenlarni tahlil qilishga urinayotgan Hakerga qo'shimcha to'siq bo'ladi.

## <mark style="color:yellow;">CSRF tokenlarni qanday berish kerak ?</mark> <a href="#csrf-tokenlarini-qanday-uzatish-kerak" id="csrf-tokenlarini-qanday-uzatish-kerak"></a>

Tokenni HTML dagi input tegining hidden atributini qo'shgan holda POST so'rovini jo'natayotganda qo'shib uzatish tavsiya qilinadi, shunda token parametr sifatida jo'natilgan hisoblanadi:

```html
<input type="hidden" name="csrf-token" value="CIwNZNlR4XbisJF39I8yWnWX9wX4WFoz" />
```

Qo'shimcha xavfsizlik uchun CSRF tokenini o'z ichiga olgan maydon HTML sahifada iloji boricha tezroq, ideal holda har qanday yashirin bo'lmagan input maydonlaridan oldin va HTML ichiga foydalanuvchi tomonidan boshqariladigan ma'lumotlar kiritilgan har qanday joydan oldin joylashtirilishi kerak. Bu Hakerga HTML sahifani manipulyatsiya qilish va uning mazmunli qismlarini qo'lga kiritish uchun yaratilgan ma'lumotlardan foydalanishi mumkin bo'lgan turli xil usullarni bajarishiga to'siq bo'ladi.

Tokenni HTTP so'rovi turgan joyga qo'shib jo'natish biroz bo'lsada xavfsiz emas, chunki query da ushbu quyida keltirilgan holatlar bor bo'lishi mumkin:

* Browser va server tomonida turli joylarda tizimga kirgan
* HTTP Referer sarlavhasida uchinchi tomonlarga uzatilishi mumkin va
* foydalanuvchi brauzerida ekranda ko'rsatilishi mumkin.

Ba'zi web saytlar **CSRF** tokenlarini maxsus so'rov headeri ichida uzatadi. Bu boshqa foydalanuvchining tokenini oldindan aytishga yoki qo'lga kiritishga muvaffaq bo'lgan Hakerga qarshi qo'shimcha himoyani taqdim etadi, chunki brauzerlar odatda domenlar o'rtasida maxsus headerlarni yuborishga ruxsat bermaydi. Biroq, **XHR** yordamida (**HTML** shakllaridan farqli o'laroq) **CSRF** bilan himoyalangan so'rovlarni amalga oshirish bilan cheklaydi va ko'p vaziyatlar uchun juda murakkab deb hisoblanishi mumkin.

**CSRF token**lar Cookie ga joylangan bo'lishi kerak emas.

## <mark style="color:yellow;">CSRF tokenlarni qanday tekshirish kerak ?</mark> <a href="#csrf-tokenlarini-qanday-tekshirish-kerak" id="csrf-tokenlarini-qanday-tekshirish-kerak"></a>

Qachonki CSRF tokeni yasalgan bo'lsa u foydalanuvchining session tokeni bilan birga serverda saqlanishi lozim. Chunki qachonki tashqi tomondan serverga so'rov jo'natilsa, shunda server CSRF token bilan birga session tokenlarini tekshirishi oson bo'ladi. Ushbu tekshirish HTTP so'rovi yoki so'rovning kontent turidan qat'iy nazar amalga oshirilishi kerak. Agar so'rovda umuman token bo'lmasa, u noto'g'ri token mavjud bo'lgan deb rad etilishi kerak.
