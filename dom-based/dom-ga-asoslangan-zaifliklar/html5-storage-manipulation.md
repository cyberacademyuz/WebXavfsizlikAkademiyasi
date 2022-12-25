# HTML5-storage manipulation

Bugungi bo'limda DOMga asoslangan HTML5-xotirani boshqarish nimaligi haqida, uni keltirib chiqaruvchi metodlar haqida, uning ta'siri qandayligi va uni qanday oldini olish mumkinligi haqida gaplashamiz.

## <mark style="color:yellow;">DOMga asoslangan HTML5-xotirani boshqarish nima ?</mark> <a href="#dom-ga-asoslangan-html5-storage-manipulation-nima" id="dom-ga-asoslangan-html5-storage-manipulation-nima"></a>

**HTML5-storage manipulation** - bu Haker skript yozib HTML5 ning xotirasiga haker tomonidan boshqariladigan ma'lumotlarni kiritganida paydo bo'ladi (`localStorage` yoki `sessionStorage` ga). Haker ushbu zaiflikdan foydalanib URL yasashi va ushbu URLga foydalanuvchi kirsa, hacker tomonidan boshqariladigan ma'lumotlarni foydalanuvchi brauzerida saqlashiga olib keladi.

Bu holat o'z-o'zidan xavfsizlik zaifligini tashkil qilmaydi. Biroq, agar web sayt keyinchalik ma'lumotlarni xotiradan olib o'qisa va ularni himoyasiz tarzda qayta ishlasa, Haker [<mark style="color:yellow;">XSS</mark>](broken-reference) va JavaScript inektsiyasi kabi boshqa DOM-ga asoslangan hujumlarni amalga oshirish uchun xotira mexanizmidan foydalanishi mumkin.

## <mark style="color:yellow;">Qaysi metodlar DOMga asoslangan HTML5-xotirani boshqarish zaifligini keltirib chiqarishi mumkin?</mark> <a href="#qaysi-sink-lar-dom-ga-asoslangan-html5-storage-manipulation-ga-asos-bolishi-mumkin" id="qaysi-sink-lar-dom-ga-asoslangan-html5-storage-manipulation-ga-asos-bolishi-mumkin"></a>

Quyida keltirilgan metodlar DOMga asoslangan HTML5-xotirani boshqarish zaifligini paydo bo'lishiga sabab bo'lishi mumkin:

```javascript
sessionStorage.setItem()
localStorage.setItem()
```

## <mark style="color:yellow;">DOMga asoslangan HTML5-xotirani boshqarish zaifligini qanday oldini olish mumkin ?</mark> <a href="#qanday-qilib-dom-ga-asoslangan-html5-storage-manipulation-zaifliklarini-oldini-olish-mumkin" id="qanday-qilib-dom-ga-asoslangan-html5-storage-manipulation-zaifliklarini-oldini-olish-mumkin"></a>

<mark style="color:yellow;"></mark>[<mark style="color:yellow;">DOM-ga asoslangan zaifliklar</mark>](broken-reference) mavzusida aytilgan umumiy chora tabirlarga qo'shimcha ravishda, har qanday ishonchsiz manbadan olingan ma'lumotlarni HTML5-xotirasi o'z ichiga olishidan saqlang.
