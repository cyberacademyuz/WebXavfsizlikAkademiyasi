# Client-side XPath injection

Bugungi bo'limda DOMga asoslangan client-side XPath ineksiya nima ekanligi haqida, uning ta'siri qanday ekanligi va uni qanday oldini olish mumkinligi haqida gaplashamiz.

## <mark style="color:yellow;">DOMga asoslangan client-side XPath ineksiya nima ?</mark> <a href="#dom-ga-asoslangan-client-side-xpath-inektsiya-nima" id="dom-ga-asoslangan-client-side-xpath-inektsiya-nima"></a>

DOM-ga asoslangan XPath-ineksiya zaifligi skript XPath so'roviga haker tomonidan boshqariladigan ma'lumotlarni kiritganida paydo bo'ladi. Haker ushbu zaiflikdan URL-manzil yaratish uchun foydalanishi mumkin, agar unga biror foydalanuvchi kirsa, o'zboshimchalik bilan XPath so'rovi bajarilishini boshlaydi, bu web-sayt tomonidan turli ma'lumotlarni olish va qayta ishlashga olib kelishi mumkin.

## <mark style="color:yellow;">DOMga asoslangan client-side XPath ineksiya ta'siri qanday ?</mark> <a href="#dom-ga-asoslangan-client-side-xpath-inektsiya-tasiri-qanday" id="dom-ga-asoslangan-client-side-xpath-inektsiya-tasiri-qanday"></a>

So'rov natijalaridan foydalanish maqsadiga qarab, haker web-sayt logikasini buzishi yoki foydalanuvchi nomidan kutilmagan harakatlarni bajarishiga sabab bo'lishi mumkin.

## <mark style="color:yellow;">DOMga asoslangan client-side XPath ineksiyani qaysi metodlar keltirib chiqaradi ?</mark> <a href="#dom-ga-asoslangan-client-side-xpath-inektsiya-ni-qaysi-sink-lar-keltirib-chiqaradi" id="dom-ga-asoslangan-client-side-xpath-inektsiya-ni-qaysi-sink-lar-keltirib-chiqaradi"></a>

Quyida keltirilgan metodlar DOMga asoslangan client-side XPath ineksiyaning paydo bo'lishini ta'minlashi mumkin:

```javascript
document.evaluate()
element.evaluate()
```

## <mark style="color:yellow;">DOMga asoslangan client-side XPath ineksiyani qanday oldini olish mumkin?</mark> <a href="#dom-ga-asoslangan-client-side-xpath-inektsiya-ni-qanday-qilib-oldini-olish-mumkin" id="dom-ga-asoslangan-client-side-xpath-inektsiya-ni-qanday-qilib-oldini-olish-mumkin"></a>

<mark style="color:yellow;"></mark>[<mark style="color:yellow;">DOM-ga asoslangan zaifliklar</mark>](broken-reference) mavzusida aytilgan umumiy choralarga qo'shimcha ravishda, har qanday ishonchsiz manbadan olingan ma'lumotlarni XPath querylar o'z ichiga olishidan saqlang.
