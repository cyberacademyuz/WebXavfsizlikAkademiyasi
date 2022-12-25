---
cover: ../.gitbook/assets/sql.png
coverY: 141.2855377008653
---

# SQL ineksiya UNION hujumlari

Agar vebsaytda SQL ineksiya bo'lsa va so'rov natijalari vebsayt javoblarida qaytarilsa, `UNION` kalit so'zidan foydalanib ma'lumotlar bazasidagi boshqa jadvallardan ma'lumotlarni olish mumkin. Bu **SQLi UNION** hujumiga olib keladi.

`UNION` kalit so'zi bir yoki bir nechta qo'shimcha SELECT so'rovlarini bajarish va natijalarni haqiqiy so'rovga qo'shish imkonini beradi. Misol uchun:

```sql
SELECT a, b FROM table1 UNION SELECT c, d FROM table2
```

Ushbu SQL so'rovi 1-jadvaldagi a va b ustunlarining va 2-jadvaldagi c va d ustunlaring qiymatlarini o'z ichiga olgan ikkita ustunli natijalar to'plamini qaytaradi.

UNION so'rovi ishlashi uchun ikkita asosiy talab bajarilishi kerak:

* Har bir so'rov bir xil sonli ustunlarni qaytarishi kerak.
* Har bitta ustundagi ma'lumotlar turi har bir so'rovga mos kelishi kerak.

**SQL ineksiya UNION** hujumini amalga oshirish uchun siz, hujumingiz ushbu ikkala talabga javob berishiga ishonch hosil qilishingiz kerak. Buning uchun avval quyidagilarni aniqlang:

* Dastlabki so'rovdan nechta ustun qaytarilishi
* Dastlabki so'rov orqali qaytarilgan qaysi ustunlar, kiritilgan so'rovning natijalarini saqlash uchun mos keladigan ma'lumotlar turiga ega ?

### <mark style="color:yellow;">So'rov natijasida qaytarilgan ustunlar sonini aniqlash uchun UNION SQL ineksiya hujumi</mark> <a href="#sql-inektsion-union-hujumida-foydali-malumotlar-turiga-ega-ustunlarni-topish" id="sql-inektsion-union-hujumida-foydali-malumotlar-turiga-ega-ustunlarni-topish"></a>

SQL ineksiya orqali UNION hujumini amalga oshirayotganda, dastlabki so'rovdan qancha ustun qaytarilganligini aniqlashning 2 ta samarali usuli mavjud.

Birinchi usul: **ORDER BY** kalit so'zini kiritish va xatolik yuzaga kelguncha ustun indeksini oshirib borish. Misol uchun, ineksiya nuqtasini, dastlabki so'rovning **WHERE** qismidagi bir tirnoq deb hisoblasak siz buni yuborgan bo'lasiz:

```
' ORDER BY 1-- 
' ORDER BY 2-- 
' ORDER BY 3-- 
va hkz...
```

Bu payloadlar natijalar to'plamidagi turli ustunlarda natijalarni tartiblash uchun dastlabki so'rovni o'zgartiradi. **ORDER BY** qismidagi ustun indeks bilan belgilanishi mumkin, shuning uchun siz ustunlar nomlarini bilishingiz shart emas. Belgilangan ustun indeksi natijalar to'plamidagi haqiqiy ustunlar sonidan oshsa, ma'lumotlar bazasi xatolik qaytaradi, masalan:

`The ORDER BY position number 3 is out of range of the number of items in the select list.` &#x20;

`3-sonli ORDER BY pozitsiyasi tanlangan ro‘yxatdagi elementlar soni doirasidan tashqarida.`

Sayt HTTP javobida `database`xatosini qaytarishi yoki umumiy xatoni qaytarishi yoki hech qanday natija bermasligi mumkin. Sayt javobida qandaydir farq borligini aniqlasangiz, so'rovdan nechta ustun qaytarilayotganini aniqlashingiz mumkin.

Ikkinchi usul turli xil **NULL** qiymatlarni ko'rsatuvchi **UNION SELECT** payloadlarini yuborish deyiladi:

```
' UNION SELECT NULL-- 
' UNION SELECT NULL,NULL--
' UNION SELECT NULL,NULL,NULL-- 
va hkz...
```

Agar **NULL**lar soni ustunlar soniga mos kelmasa, ma'lumotlar bazasi xatolik qaytaradi, masalan:

`All queries combined using a UNION, INTERSECT or EXCEPT operator must have an equal number of expressions in their target lists.`

`UNION, INTERSECT yoki EXCEPT operatorlari bilan birlashtirilgan barcha so'rovlar maqsadli ro'yxatlarida teng miqdordagi ifodalarga ega bo'lishi kerak.`

Shunga qaramay, vebsayt  bu xato xabarni qaytarishi yoki umumiy xatoni qaytarishi yoki hech qanday natija bermasligi mumkin. Agar NULLlar soni ustunlar soniga teng bo'lsa, ma'lumotlar bazasi natijalar to'plamida har bir ustunda NULL qiymatlarni o'z ichiga olgan qo'shimcha qatorni qaytaradi. Olingan HTTP javobiga ta'siri dastur kodiga bog'liq. Agar omadingiz kelsa, javobda HTML jadvalidagi qo'shimcha qator kabi qo'shimcha ma'lumotlarni ko'rasiz. Aks holda, NULL qiymatlar **NullPointerException** kabi boshqa xatoni keltirib chiqarishi mumkin. Eng yomoni, javob NULLlarning noto'g'ri sonidan kelib chiqadigan javobdan farqlab bo'lmasligi mumkin, bu esa ustunlar sonini aniqlashning ushbu usulini samarasiz qiladi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark>** **<mark style="color:blue;">****</mark> [SQL ineksiya UNION hujumida so'rov natijalarida qaytarilgan ustunlar sonini aniqlash ≫](https://portswigger.net/web-security/sql-injection/union-attacks/lab-determine-number-of-columns)
{% endhint %}

**Eslatma:**

* Kiritilgan `SELECT` so'rovi tomonidan qaytariladigan qiymatlar sifatida `NULL` dan foydalanishning sababi shundaki, har bir ustundagi ma'lumotlar turi **asl** va **kiritilgan so'rovla**r o'rtasida mos bo'lishi kerak. `NULL` har qanday tez-tez ishlatiladigan ma'lumotlar turiga o'zgartirilishi mumkinligi sababli, NULL dan foydalanish payloadning muvaffaqiyatli bo'lish imkoniyatini maksimal darajada oshiradi.
* Oracle bilan har bir  `SELECT`  so'rovi  `FROM`  kalit so'zidan foydalanishi va to'g'ri jadvalni ko'rsatishi kerak. Oracle'da `DUAL` deb nomlangan avvaldan yaratilgan jadval mavjud bo'lib, shu maqsadda foydalanish mumkin. Shuning uchun Oraclega kiritilgan so'rovlar quyidagicha ko'rinishi kerak: `'UNION SELECT NULL FROM DUAL--`
* Kiritilgan SQL ineksiya payloadidan keyin dastlabki so'rovning qolgan qismini komentga olish uchun 2 ta chiziq `--` koment belgisi ishlatadi. **MySQL** uchun ikki chiziqdan keyin bo'sh joy bo'lishi kerak. Shu bilan bir qatorda, # xeshtag belgisi izohni aniqlash uchun ishlatilishi mumkin.

Ma'lumotlar bazasiga xos sintaksis haqida ko'proq ma'lumot olish uchun [<mark style="color:yellow;">SQL ineksiya cheat sheet</mark>](sql-ineksiya-cheat-sheet.md) sahifasiga o'ting.

### <mark style="color:yellow;">Foydali ma'lumotlar turiga ega ustunlarni topish uchun UNION SQL ineksiya hujumi</mark> <a href="#sql-inektsion-union-hujumida-foydali-malumotlar-turiga-ega-ustunlarni-topish" id="sql-inektsion-union-hujumida-foydali-malumotlar-turiga-ega-ustunlarni-topish"></a>

UNION SQL ineksiya hujumidan maqsad, kiritilgan so'rovdan natijalarni olish imkoniyatidir. Umuman olganda, siz olmoqchi bo'lgan qiziqarli ma'lumotlar **string** shaklda bo'ladi,  shuning uchun siz asl so'rov natijalarida ma'lumotlar turi yoki string ma'lumotlariga mos keladigan bir yoki bir nechta ustunlarni topishingiz kerak.

Sizga kerak bo'lgan ustunlar sonini aniqlaganingizdan so'ng, har bir ustunga navbatma-navbat string qiymatini kiritadigan `UNION SELECT` payloadlarini yuborish orqali string ma'lumotlarni saqlashini tekshirish uchun har bir ustunni tekshirishingiz mumkin. Misol uchun, agar so'rov to'rtta ustunni qaytarsa, siz quyidagilarni payloadlarni yuborasiz:

`' UNION SELECT 'a',NULL,NULL,NULL--`

`' UNION SELECT NULL,'a',NULL,NULL--`

`' UNION SELECT NULL,NULL,'a',NULL--`

`' UNION SELECT NULL,NULL,NULL,'a'--`

Agar ustunning ma'lumotlar turi string ma'lumotlariga mos kelmasa, kiritilgan so'rov ma'lumotlar bazasida xatolik sodir bo'ladi, masalan:

```
Conversion failed when converting the varchar value 'a' to data type int. 
Varchar qiymati 'a' int ma'lumotlar turiga o'zgartirilganda konvertatsiya amalga oshmadi.
```

Hech qanday xatolik yuz bermasa va saytning javobi kiritilgan qator qiymatini o'z ichiga olgan qo'shimcha tarkibni o'z ichiga olsa, tegishli ustun qator ma'lumotlarini olish uchun mos keladi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Matnni o'z ichiga olgan ustunni topish uchun UNION SQL ineksiya hujumi ≫](https://portswigger.net/web-security/sql-injection/union-attacks/lab-find-column-containing-text)
{% endhint %}

## <mark style="color:yellow;">Qiziqarli ma'lumotlarni olish uchun UNION SQL ineksiya hujumidan foydalanish</mark> <a href="#qiziqarli-malumotlarni-olish-uchun-union-sql-ineksiya-hujumidan-foydalanish" id="qiziqarli-malumotlarni-olish-uchun-union-sql-ineksiya-hujumidan-foydalanish"></a>

Dastlabki so'rov orqali qaytayotgan ustunlar sonini aniqlaganingizda va qaysi ustunlar string ma'lumotlarni saqlashi mumkinligini aniqlaganingizda, siz qiziqarli ma'lumotlarni olish imkoniyatiga ega bo'lasiz.

Faraz qilaylik:

* Asl so'rov ikkita ustunni qaytaradi, ularning ikkalasi ham string ma'lumotlarini saqlashi mumkin.
* Ineksiya nuqtasi `WHERE` kalit so'zidagi bitirnoqli stringdir.
* Ma'lumotlar bazasida `username` va  `password` ustunlari bilan  `users`  deb nomlangan jadval mavjud.

Bunday holatda siz quyidagi SQL so'rovini kiritish orqali foydalanuvchilar jadvalining ma'lumotlarini olishingiz mumkin.

```sql
' UNION SELECT username, password FROM users--
```

Albatta, ushbu hujumni amalga oshirish uchun zarur bo'lgan ma'lumot  `users`  nomli jadval va uning ichidagi `username` va `password`  deb nomlangan ikkita ustun mavjud.  Ushbu ma'lumotsiz siz jadvallar va ustunlar nomlarini taxmin qilib topishga urinib ko'rishingiz mumkin. Darhaqiqat, barcha zamonaviy ma'lumotlar bazalari ma'lumotlar bazasi strukturasini tekshirish, unda qanday jadvallar va ustunlar mavjudligini aniqlash usullarini taqdim etadi.

{% hint style="warning" %}
&#x20;<mark style="color:yellow;">**Lab:**</mark> [SQL ineksiya uyushmasi hujumi, boshqa jadvallardan ma'lumotlarni olish ≫](https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-data-from-other-tables)
{% endhint %}

{% hint style="info" %}
**Ko'proq o'qish:**[<mark style="color:blue;">****</mark>\ <mark style="color:blue;">****</mark>SQL ineksiya hujumlarida ma'lumotlar bazasini o'rganish ☰](malumotlar-bazasini-tekshirish.md)
{% endhint %}

### <mark style="color:yellow;">Bitta ustun ichida bir nechta qiymatlarni olish</mark> <a href="#bitta-ustun-ichida-bir-nechta-qiymatlarni-olish" id="bitta-ustun-ichida-bir-nechta-qiymatlarni-olish"></a>

Faraz qilaylik, oldingi misolda so'rov faqat bitta ustunni qaytaradi.

Qiymatlarni ideal tarzda birlashtirib, birlashtirilgan qiymatlarni farqlash imkonini beradigan mos ajratgichni o'z ichiga olgan holda, ushbu bitta ustun ichida bir nechta qiymatlarni osongina olishingiz mumkin. Masalan, Oracleda siz quyidagi ma'lumotlarni yuborishingiz mumkin:

```sql
' UNION SELECT username || '~' || password FROM users--
```

Bunda || belgisi ishlatiladi Bu Oracleda qatorlarni birlashtirish operatori. Qo'yilgan so'rov,  \~ belgisi bilan ajratilgan `username` va `password` maydonlarining qiymatlarini birlashtiradi.

So'rov natijalari sizga barcha  `username` va  `password`larni o'qish imkonini beradi, masalan:

```
... 
administrator~s3cure 
wiener~peter 
carlos~montoya 
...
```

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark>** **<mark style="color:blue;">****</mark> [BItta ustunda bir nechta qiymatlarni olish uchun UNION SQL ineksiya hujumi ≫](https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-multiple-values-in-single-column)
{% endhint %}
