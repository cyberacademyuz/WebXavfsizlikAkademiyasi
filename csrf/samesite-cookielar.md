# SameSite cookielar

Ba'zi web saytlar [<mark style="color:yellow;">**CSRF**</mark>](broken-reference) xujumlarga qarshi **SameSite Cookie** tordamida ximoyalanadi.

`SameSite` **** atributdan saytlararo so'rovlarda qanday cookielar yuborilishini nazorat qilish uchun foydalanish mumkin. Sessiya cookilarining attributini sozlash, cookilar qayerdan paydo bolishidan qatiy nazar websayt browserning cookilarni requestga qo'shish harakatlarini avtomatik bloklaydi.

Server cookilarni qaytarganida `SameSite` attributi responsedagi `Set-Cookie`ga qo'shiladi va attributega `Strict` yoki `Lax qiymatlarini beradi.`

```http
Set-Cookie: SessionId=sYMnfCUrAlmqVVZn9dqevxyFpKZt30NN; SameSite=Strict;
```

```http
Set-Cookie: SessionId=sYMnfCUrAlmqVVZn9dqevxyFpKZt30NN; SameSite=Lax;
```

Agarda `SameSite` qiymati `Strict` bo'ladigan bo'lsa unda browser boshqa saytlardan kelayotgan so'rovlarni barchasiga rad javobini berib yuboradi. Ammo bu xavfsizlikni ta'minlash foydalanuvchi uchun biroz noqulaylikni paydo qilishi mumkin, agar foydalanuvchi ro'yhatdan o'tgan bo'lsa va agar foydalanuvchi uchinchi tomon saytlaridan birortasiga kiradigan bo'lsa, saytga qaytganda yana bir bor ro'yhatdan o'tishi kerak bo'ladi. Sababi `SameSite` qolgan saytlardan kelgan so'rovlarni qabul qilmadi va `Cookie` larni tozalab tashladi.

Agarda `SameSite` qiymati `Lax` bo'ladigan bo'lsa unda Browser faqatgina ikkita istisno asosida boshqa web saytlardan keluvchi so'rovlarni qabul qiladi:

* Faqatgina **GET** so'rovi yuborilsa qabul qiladi, **POST** so'rovini esa qabul qilmaydi.
* Foydalanuvchining boshqa saytlarga kirishidan tashqari, skriptlar tomonidan yozilgan so'rovlar qabul qilinmaydi.

`SameSite` ning `Lax` qiymatli xolati qisman **CSRF** xujumlarini oldini oladi, chunki ko'p **CSRF** xujumlar **POST** so'rovi jo'natilgan xolatida amalga oshiriladi. Ammo shunga qaramay ikkita ogohlantirishni ta'kidlab o'tish kerak:

* Ba'zi web saytlar nozik amallarni **GET** so'rovini amalga oshirish asnosida bajarishadi
* Juda ko'plab saytlar va frameworklar turli so'rovlarni bajarishadi. Bunday holatda, sayt **POST** so'rovini qabul qilayotganida u so'rovni **GET** holatiga o'tkazib keyin qabul qiladi.

Ushbu sabablardan kelib chiqib shuni aytish mumkinki, **CSRF** xujumlarga qarshi turish uchun faqatgina SameSite cookie ximoyalanish usulini amalga oshirish tavsiya qilinmaydi. CSRF tokenlari bilan birgalikda ishlatiladi, ammo Samesit Cookie tokenga asoslangan himoyadagi har qanday kamchiliklarni yumshatishi mumkin bo'lgan qo'shimcha himoya qatlamini ta'minlaydi.
