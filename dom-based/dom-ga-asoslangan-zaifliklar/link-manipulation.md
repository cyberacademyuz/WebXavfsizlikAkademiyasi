# Link manipulation

Biz ushbu bo'limda DOM ga asoslangan Linkni boshqarish nima ekani, ushbu xujumning qanday ta'sir qilishini va uning oldini qanday olish mumkinligi haqida gaplashamiz.

## <mark style="color:yellow;">DOM ga asoslangan linkni boshqarish  nima ?</mark> <a href="#dom-ga-asoslangan-link-manipulation-nima" id="dom-ga-asoslangan-link-manipulation-nima"></a>

DOM-ga asoslangan linkni boshqarish zaifligi skript haker tomonidan boshqariladigan ma'lumotlarni sahifadagi biror linkga yoki submit qiliivchi URL manziliga yozganida paydo bo'ladi. Haker ushbu zaiflikdan URL-manzil yaratish uchun foydalanishi mumkin, agar foydalanuvchi unga kirsa, javob ichidagi havolalar qiymatlarini o'zgartiradi.

## <mark style="color:yellow;">DOM ga asoslangan linkni boshqarish zaifligining ta'siri qanday ?</mark> <a href="#dom-ga-asoslangan-link-manipulation-tasiri-qanday" id="dom-ga-asoslangan-link-manipulation-tasiri-qanday"></a>

Quyida keltirilgan holatlar amalga oshishi mumkin:

* Foydalanuvchini o'zboshimchalik bilan tashqi URL manziliga yo'naltirilishga olib keladi, bu esa fishing hujumini osonlashtirishi mumkin.
* Foydalanuvchining maxfiy ma'lumotlarini Haker tomonidan boshqariladigan serverga yuborishiga sabab bo'ladi.
* Link bilan bog'langan fayl yoki so'rovlar qatorini o'zgartirishi mumkin, bu foydalanuvchini web saytda kutilmagan ishni bajarishiga olib keladi.
* XSS xujumlarini mavjud saytning linklarini kiritish orqali brauzerning XSSga qarshi himoyasini chetlab o'tishga olib kelishi mumkin, chunki XSSga qarshi himoya odatda saytdagi linklarni hisobga olmaydi.

## <mark style="color:yellow;">Qaysi metodlar DOM ga asoslangan linkni boshqarish zaifliklarini keltirib chiqaradi ?</mark> <a href="#qaysi-sink-lar-ushbu-dom-ga-asoslangan-link-manipulation-zaifliklarini-keltirib-chiqaradi" id="qaysi-sink-lar-ushbu-dom-ga-asoslangan-link-manipulation-zaifliklarini-keltirib-chiqaradi"></a>

Quyida keltirilgan metodlar ushbu zaifliklarni keltirib chiqaradi:

```javascript
element.href
element.src
element.action
```

## <mark style="color:yellow;">DOM ga asoslangan linkni boshqarish zaifliklarini qanday qilib oldi olinadi ?</mark> <a href="#qanday-qilib-dom-ga-asoslangan-link-manipulation-zaifliklarini-oldini-olish-mumkin" id="qanday-qilib-dom-ga-asoslangan-link-manipulation-zaifliklarini-oldini-olish-mumkin"></a>

Bu haqida ko'proq [<mark style="color:yellow;">DOM-ga asoslangan zaifliklar</mark>](broken-reference) mavzusida aytib o'tganmiz, siz Link qiymatlarini dinamik o'zgarmasligini ta'minlashingiz zarur.
