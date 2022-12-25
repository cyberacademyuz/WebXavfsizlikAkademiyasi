# Local file-path manipulation

Bugungi bo'limda DOMga asoslangan lokal fayl-joylashuvini boshqarish nima ekanligi haqida, uning ta'siri qandayligi va uni qanday oldini olish mumkinligini o'rganamiz.

## <mark style="color:yellow;">DOM ga asoslangan lokal fayl-joylashuvini boshqarish nima?</mark> <a href="#dom-ga-asoslangan-lokal-file-path-manipulation-nima" id="dom-ga-asoslangan-lokal-file-path-manipulation-nima"></a>

Hacker tuzgan skript hacker tomonidan boshqariladigan ma'lumotlarni file-handlingga  `filename` parametri sifatida APIga uzatganda lokal faylni boshqarish zaifligi paydo bo'ladi. Hacker ushbu zaiflikdan URL manzilini yasash uchun foydalanishi mumkin, agar unga biror foydalanuvchi kirsa, foydalanuvchi brauzeri o'zboshimchalik bilan lokal faylni ochishiga olib keladi.

## <mark style="color:yellow;">DOMga asoslangan lokal file-joylashuvini boshqarish zaifligining ta'siri qanday ?</mark> <a href="#dom-ga-asoslangan-lokal-file-path-manipulation-tasiri-qanday" id="dom-ga-asoslangan-lokal-file-path-manipulation-tasiri-qanday"></a>

Uning ta'siri, web sayt fayllarni qanday qilib ochishiga ham bog'liq:

* Agar web sayt faylni o'qisa unda Haker fayl haqidagi ma'lumotni ola oladi.
* Agar web-sayt maxsus ma'lumotlarni faylga yozsa, Haker operatsion tizimning konfiguratsiya fayli bo'lishi mumkin bo'lgan faylga o'z ma'lumotlarini yozishi mumkin.

Bu ikkala holatda ham zaiflikdan foydalanish mumkinligi web-saytda mavjud bo'lgan boshqa funksiyalarga bog'liq bo'lishi mumkin.

## <mark style="color:yellow;">Qaysi metodlar DOMga asoslangan lokal faylni boshqarish zaifligiga sabab bo'lishi mumkin ?</mark> <a href="#qaysi-sink-lar-dom-ga-asoslangan-lokal-file-path-manipulation-zaifliklariga-sabab-bolishi-mumkin" id="qaysi-sink-lar-dom-ga-asoslangan-lokal-file-path-manipulation-zaifliklariga-sabab-bolishi-mumkin"></a>

Quyida keltirilgan metodlar DOM ga asoslangan lokal faylni boshqarish zaifligini paydo bo'lishiga sabab bo'lishi mumkin:

```javascript
FileReader.readAsArrayBuffer()
FileReader.readAsBinaryString()
FileReader.readAsDataURL()
FileReader.readAsText()
FileReader.readAsFile()
FileReader.root.getFile()
```

## <mark style="color:yellow;">DOMga asoslangan lokal faylni boshqarish zaifligini qanday  oldini olish mumkin ?</mark> <a href="#qanday-qilib-dom-ga-asoslangan-lokal-file-path-manipulation-zaifliklarini-oldini-olish-mumkin" id="qanday-qilib-dom-ga-asoslangan-lokal-file-path-manipulation-zaifliklarini-oldini-olish-mumkin"></a>

<mark style="color:yellow;"></mark>[<mark style="color:yellow;">DOM-ga asoslangan zaifliklar</mark>](broken-reference) mazsuida aytib o'tilgan umumiy choralarga qo'shimcha ravishda, har qanday ishonchsiz manbadan olingan ma'lumotlarni o'z ichiga olgan API yordamida fayl qabul qilishdan saqlaning.
