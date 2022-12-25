# Denial of service

Bugungi bo'limda DOMga asoslangan DOS nima ekanligi haqida, uning ta'siri qanday ekanligi va uni qanday oldini olish mumkinligi haqida gaplashamiz.

## <mark style="color:yellow;">DOMga asoslangan DOS nima ?</mark> <a href="#dom-ga-asoslangan-denail-of-service-nima" id="dom-ga-asoslangan-denail-of-service-nima"></a>

Skript Haker tomonidan boshqariladigan ma'lumotlarni, foydalanuvchi kompyuterida protsessor yoki disk maydonini haddan tashqari ko'p sarflashi mumkin bo'lgan API kabi muammoli platforma APIsiga himoyasiz tarzda o'tkazganda DOM-ga asoslangan denial of service zaifligi paydo bo'ladi. Agar brauzer `localStorage`-da ma'lumotlarni saqlashga urinishlarni bekor qilish yoki ishlayotgan skriptlarni to'htatish kabi veb-sayt funksiyalarini cheklasa, bu nojo'ya ta'sirlarga olib kelishi mumkin.

## <mark style="color:yellow;">DOMga asoslangan DOS qaysi metodlar sabab paydo bo'ladi ?</mark> <a href="#dom-ga-asoslangan-denial-of-service-qaysi-sink-lar-sabab-paydo-boladi" id="dom-ga-asoslangan-denial-of-service-qaysi-sink-lar-sabab-paydo-boladi"></a>

Ko'pgina hollarda DOMga asoslangan DOS xujumini amalga oshirishda quyida metodlar asos bo'ladi:

```javascript
requestFileSystem()
RegExp()
```

## <mark style="color:yellow;">DOM ga asoslangan DOSni  qanday oldini olish mumkin ?</mark> <a href="#qanday-qilib-dom-ga-asoslangan-denial-of-service-ni-oldini-olish-mumkin" id="qanday-qilib-dom-ga-asoslangan-denial-of-service-ni-oldini-olish-mumkin"></a>

<mark style="color:yellow;"></mark>[<mark style="color:yellow;">DOM-ga asoslangan zaifliklar</mark> ](broken-reference)mavzusida aytilgan umumiy choralarga qo'shimcha ravishda, har qanday ishonchsiz manbadan olingan ma'lumotlarni dinamik tarzda muammoli platformaning APIlariga o'tkazishidan saqlash kerak.
