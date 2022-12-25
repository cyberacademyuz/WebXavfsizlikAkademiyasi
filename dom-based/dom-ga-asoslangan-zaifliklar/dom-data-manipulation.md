# DOM-data manipulation

Biugungi bo'limda DOM-data boshqaruvi nima ekanligi haqida, uning ta'siri qanday ekanligi va uni qanday oldini olish mumkinligi haqida gaplashamiz.

## <mark style="color:yellow;">DOM-data manipulation nima ?</mark> <a href="#dom-data-manipulation-nima" id="dom-data-manipulation-nima"></a>

Skript haker tomonidan boshqariladigan ma'lumotlarni DOM ichidagi ko'rinadigan UI yoki client-side logikasida ishlatiladigan maydonga yozganda DOM-data manipulation paydo bo'ladi. Haker bu zaiflikdan URL manzil yaratish uchun foydalanishi mumkin, agar unga biror foydalanuvchi kirsa, client-side UI ning ko'rinishini yoki harakatini o'zgartiradi. Dom-data manipulation zaifligi Domga asoslanga reflected va stored hujumlaridan foydalanish mumkin.

## <mark style="color:yellow;">DOM-data manipulation ta'siri qanday ?</mark> <a href="#dom-data-manipulation-tasiri-qanday" id="dom-data-manipulation-tasiri-qanday"></a>

Eng past ta'siri bu haker veb-saytni virtual buzishni amalga oshirish uchun ushbu zaiflikdan foydalanishi mumkin, masalan sahifadagi matn yoki rasmlarni o'zgartirib. Ammo, zaiflik tufayli xujumlar jiddiyroq bo'lishi ham mumkin. Masalan, agar haker `src` qiymatini o'zgartira olsa unda zararli JavaScript faylni yuklashi va foydalanuvchi o'zi istamagan amallarini qilishiga majbur qilishi mumkin.

## <mark style="color:yellow;">DOM-data manipulationni paydo qiluvchi metodlar qaysilar ?</mark> <a href="#dom-data-manipulation-ni-hosil-qiluvchi-sink-lar-qaysilar" id="dom-data-manipulation-ni-hosil-qiluvchi-sink-lar-qaysilar"></a>

Ko'pincha quyida keltirilgan metodlar ushbu zaiflikni kelib chiqishiga sabab bo'ladi:

```javascript
script.src
script.text
script.textContent
script.innerText
element.setAttribute()
element.search
element.text
element.textContent
element.innerText
element.outerText
element.value
element.name
element.target
element.method
element.type
element.backgroundImage
element.cssText
element.codebase
document.title
document.implementation.createHTMLDocument()
history.pushState()
history.replaceState()
```

## <mark style="color:yellow;">DOM-data manipulationni qanda oldini olish mumkin ?</mark> <a href="#qanday-qilib-dom-data-manipulation-ni-oldini-olish-mumkin" id="qanday-qilib-dom-data-manipulation-ni-oldini-olish-mumkin"></a>

<mark style="color:yellow;"></mark>[<mark style="color:yellow;">DOM-ga asoslangan zaifliklar</mark>](broken-reference) mavzusida aaytilgan umumiy choralarga qo'shimcha ravishda, har qanday ishonchsiz manbadan olingan ma'lumotlarni DOMga ta'sir qiluvchi maydonlar o'z ichiga olishidan saqlashingiz kerak. Shuni esda tutingki, Burp Suite bu muammoni statik kod tahlili yordamida avtomatik ravishda aniqlaydi, bu esa noto'g'ri pozitivlarga olib kelishi mumkin. Ushbu zaiflik haqiqatan ham mavjud yoki yo'qligini yoki exploitni oldini oladigan choralari mavjudligini aniqlash uchun tegishli kod va bajarilish yo'llari orqali ko'rib chiqilishi kerak.
