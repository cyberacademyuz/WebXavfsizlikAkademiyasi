# Client-side SQL injection

Bugungi bo'limda DOMga asoslangan client-side [<mark style="color:yellow;">SQL ineksiya</mark>](broken-reference) nima ekanligi haqida, uning ta'siri qanday ekanligi va uni qanday oldini olish mumkinligi haqida gaplashamiz.

## <mark style="color:yellow;">DOM ga asoslangan client-side SQL inektsiya nima ?</mark> <a href="#dom-ga-asoslangan-client-side-sql-inektsiya-nima" id="dom-ga-asoslangan-client-side-sql-inektsiya-nima"></a>

Skript Haker tomonidan boshqariladigan ma'lumotlarni himoyasiz tarzda client-side SQL so'roviga kiritganida, client-side SQL-inektsiya zaifligi paydo bo'ladi. Haker ushbu zaiflikdan foydalangan holda SQL databazasidagi qiymatlarini o'zgartiruvchi URL yasashi mumkin.

## <mark style="color:yellow;">DOMga asoslangan client-side SQL inektsiyaning ta'siri qanday ?</mark> <a href="#dom-ga-asoslangan-client-side-sql-inektsiya-tasiri-qanday" id="dom-ga-asoslangan-client-side-sql-inektsiya-tasiri-qanday"></a>

Ushbu zaifliklarning ta'siri, web sayt SQL databazalaridan qanchalik foydalanishiga bog'liq. Agar databaza ijtimoiy tarmoqdagi xabarlar kabi maxfiy ma'lumotlarni saqlash uchun ishlatilsa, haker bu ma'lumotlarni olishi mumkin.

Agar databaza foydalanuvchi qilishi kerak bo'lgan ishlarni, masalan, elektron pochtasidan yuboriluvchi xabarlarni saqlash uchun ishlatilsa, haker ushbu ma'lumotlarni o'zgartirishi va foydalanuvchi nomidan o'zboshimchalik bilan turli ishlarni amalga oshirishi mumkin.

## <mark style="color:yellow;">Qaysi metodlar DOMga asoslangan client-side SQL ineksiyani paydo qilishi mumkin ?</mark> <a href="#qaysi-sink-lar-dom-ga-asoslangan-client-side-sql-inektsiyani-hosil-qilishi-mumkin" id="qaysi-sink-lar-dom-ga-asoslangan-client-side-sql-inektsiyani-hosil-qilishi-mumkin"></a>

JavaScriptning `executeSQL()` funksiyasi ushbu zaiflikni keltirib chiqarishi mumkin.

## <mark style="color:yellow;">DOMga asoslangan client-side SQL ineksiyani qanday oldini olish mumkin ?</mark> <a href="#qanday-qilib-dom-ga-asoslangan-client-side-sql-inektsiyani-oldini-olish-mumkin" id="qanday-qilib-dom-ga-asoslangan-client-side-sql-inektsiyani-oldini-olish-mumkin"></a>

<mark style="color:yellow;"></mark>[<mark style="color:yellow;">DOM-ga asoslangan zaifliklar</mark>](broken-reference) mavzusida aytilgan umumiy chora-tadbirlarga qo'shimcha ravishda, barcha ma'lumotlar bazasiga kirish uchun parametrlangan so'rovlardan foydalanishingizga ishonch hosil qilishingiz kerak. Ushbu usul buzilgan ma'lumotlarni SQL so'rovlariga xavfsiz kiritish uchun ikki bosqichdan foydalanadi:

* Web sayt so'rovning tuzilishini belgilaydi va foydalanuvchi kiritgan har bir element uchun joy qoldiradi.
* Web sayt har bir joyning tarkibini belgilaydi. So'rovning tuzilishi birinchi bosqichda allaqachon aniqlanganligi sababli, ikkinchi bosqichda noto'g'ri tuzilgan ma'lumotlar so'rovlarning strukturasiga xalaqit berishi mumkin emas.

JavaScript `executeSql()` APIda parametrlangan elementlar so'rovlar qatorida `?` so'rov belgisi yordamida belgilanishi mumkin. Har bir parametrlangan element uchun element qiymatini o'z ichiga olgan APIga qo'shimcha parametr uzatiladi. Nazoratni oldini olish va websaytning kod bazasining boshqa joylaridagi o'zgarishlar tufayli zaifliklarning oldini olish uchun, database so'rovlariga kiritilgan har bir o'zgaruvchan ma'lumot elementini, hatto aniq o'zgartirilmagan bo'lsa ham, parametrlash tavsiya etiladi.
