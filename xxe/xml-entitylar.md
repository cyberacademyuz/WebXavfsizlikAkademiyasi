# XML entitylar

Biz ushbu bo'limda XML entitie lar nima ekanligi haqida gaplashamiz va [<mark style="color:yellow;">XXE zaifliklari</mark>](broken-reference) qanday ishlashini yanada yaxshiroq tushunamiz.

## <mark style="color:yellow;">XML nima ?</mark> <a href="#xml-nima" id="xml-nima"></a>

XML "**extensible markup language** \[ _kengayuvchan belgilash tili_ ]" degan ma'noni anglatadi. XML ma'lumot saqlash va almashish uchun ishlab chiqilgan. HTML ga o'xshab XML ham teglardan foydalanadi. HTML dan farqli o'laroq XML da teglar oldindan tuzilgan bo'lmaydi va teglarga ko'pincha ma'lumot nomi berilgan bo'ladi. Ilgari Internet tarixida XML ma'lumotlarni uzatish formati sifatida modada edi ("AJAX"dagi "X" harfi "XML" degan ma'noni anglatadi). Ammo endi JSON formati tufayli uning mashxurlik darajasi kamaydi.

## <mark style="color:yellow;">XML entitylar nima ?</mark> <a href="#xml-entitilar-nima" id="xml-entitilar-nima"></a>

XML entitylar ma'lumotlarning o'zidan foydalanish o'rniga XML hujjatidagi ma'lumotlar elementini ifodalash usulidir. XML tilining spetsifikatsiyasiga turli entitylar o'rnatilgan. Masalan, ushbu `&lt`; va `&gt;` < va > ni anglatadi. Bular XML teglarini belgilash uchun ishlatiladigan meta-belgilardir va shuning uchun ular odatda ma'lumotlar ichida paydo bo'lganda ularning entitylari yordamida ko'rsatilishi kerak.

## <mark style="color:yellow;">Document type definition nima?</mark> <a href="#document-type-definition-nima" id="document-type-definition-nima"></a>

**XML Document Type Definition** (DTD) - XML faylning tuzilishini, u o'z ichiga olishi mumkin bo'lgan ma'lumotlar qiymatlarining turlarini va boshqa narsalarni belgilaydigan deklaratsiyalarni o'z ichiga oladi. DTD, XML faylining boshida ixtiyoriy `DOCTYPE` elementida e'lon qilinadi. DTD hujjatning o'zida bo'lishi mumkin ("ichki DTD" deb nomlanadi) yoki boshqa joydan yuklanishi mumkin ("tashqi DTD" deb nomlanadi) yoki ikkalasining birikmasi bo'lishi mumkin.

## <mark style="color:yellow;">XML maxsus entitilar</mark> <a href="#xml-maxsus-entitilar" id="xml-maxsus-entitilar"></a>

XML DTD ichidagi maxsus entitylaringizga ruhsat beradi. Masalan:

```xml
<!DOCTYPE foo [ <!ENTITY myentity "my entity value" > ]>
```

Bu ta'riflash hozir `&myentity;` qiymati my entity value ga teng ekanligi bildirilmoqda va siz uni har qayerda ishlatishingiz mumkin.

## <mark style="color:yellow;">XML tashqi entitilar nima ?</mark> <a href="#xml-tashqi-entitilar-nima" id="xml-tashqi-entitilar-nima"></a>

XML tashqi entitilar ham aslida maxsus entitilarning bir turi, ya'ni ular DTD dan tashqarida e'lon qilinadi.

XML tashqi entitilarini e'lon qilish uchun `SYSTEM` kalit so'zi ishlatiladi va siz qaysi URL manzil orqali entitini chaqirayotganmingizni kiritishingiz zarur bo'ladi. Masalan:

```xml
<!DOCTYPE foo [ <!ENTITY ext SYSTEM "http://normal-website.com" > ]>
```

URL `file://` protokolini ham qo'llab quvvatlaydi va bu tashqi entitilar fayldan ham yuklanishi mumkin degani. Masalan:

```xml
<!DOCTYPE foo [ <!ENTITY ext SYSTEM "file:///path/to/file" > ]>
```

XML tashqi entitilari [<mark style="color:yellow;">XXE hujumlar</mark>](broken-reference)ini paydo bo'lishining asosini ta'minlaydi.
