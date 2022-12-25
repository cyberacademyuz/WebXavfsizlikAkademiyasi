# Cookie manipulation

Biz ushbu bo'limda DOM ga asoslangan zaifliklardan biri cookie manipulation haqida gaplashamiz, qanday qilib xujumlar amalga oshirilishi va cookie manipulationni qanday qilib osonroq topish haqida ham gaplashamiz.

## <mark style="color:yellow;">DOM ga asoslangan cookieni boshqarish nima ?</mark> <a href="#dom-ga-asoslangan-cookie-manipulation-nima" id="dom-ga-asoslangan-cookie-manipulation-nima"></a>

Ba'zi DOM ga asoslangan zaifliklar Hakerlarga ular boshqarishi mumkin bo'lmagan ma'lumotlarni ham boshqarish imkonini beradi. Bu potensial source ichidagi cookie kabi normally-safe malumot turlarini o'zgartiradi. Script Haker tomonidan boshqariladigan ma'lumotlarni cookie qiymatiga yozganda DOM-ga based cookie manipulation zaifligi paydo bo'ladi.

Haker bu zaiflikdan foydalanib URL tuzishi va o'sha URLga foydalanuvchi tashrif buyursa cookie qiymatlarini o'zgartirishi mumkin. Ko'pgina metodlar ma'lum darajada zararsiz bo'lsada, ammo DOM-ga asoslangan cookie-manipulyatsiya hujumlari, ba'zida past darajadagi zaifliklardan qanday qilib yuqori jiddiylikdagi hujum uchun ekspluatatsiya zanjirining bir qismi sifatida foydalanish mumkinligini ko'rsatadi. Masalan, agar JavaScript manbadan ma'lumotni tozalamasdan `document.cookie` ga yozsa, hacker o'zboshimchalik bilan qiymatlarni kiritish uchun bitta cookie qiymatini o'zgartirishi mumkin:&#x20;

```javascript
document.cookie = 'cookieName='+location.hash.slice(1);
```

Agar sayt cookie qiymatlarini HTML-kodlashsiz himoyasiz tarzda ko'rsatsa, hacker bundan foydalanish uchun cookielarni boshqarish usullaridan foydalanishi mumkin.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Domga asoslangan cookieni boshqarish zaifligi ≫](https://portswigger.net/web-security/dom-based/cookie-manipulation/lab-dom-cookie-manipulation)
{% endhint %}

## <mark style="color:yellow;">DOMga asoslangan cookieni boshqarishning ta'siri qanday ?</mark> <a href="#dom-ga-asoslangan-cookie-manipulation-tasiri-qanday" id="dom-ga-asoslangan-cookie-manipulation-tasiri-qanday"></a>

Ushbu zaiflikning ta'siri cookielarning web-saytdagi vazigasiga bog'liq. Agar cookie foydalanuvchining muayyan harakatlari (masalan, Demo rejimi sozlamalari) natijasida yuzaga keladigan xatti-harakatlarni boshqarish uchun ishlatilsa, Haker cookie qiymatini boshqarish orqali foydalanuvchiga kutilmagan harakatlarni qilishiga majburlashi mumkin.

Agar cookie foydalanuvchi sessiyasi kuzatish uchun ishlatilsa, Haker sessiyani aniqlash hujumini amalga oshirishi mumkin, bunda hacker cookie qiymatini web-saytdan olingan haqiqiy tokenga o'rnatadi va keyin sessiyani o'g'irlashi mumkin. Bu kabi cookieni-boshqarish zaifligi nafaqat zaif web-saytga, balki bir xil sub domenidagi har qanday boshqa web-saytga hujum qilish uchun ham ishlatilishi mumkin.

## <mark style="color:yellow;">Qaysi metodlar ushbu zaiflikni keltirib chiqaradi ?</mark> <a href="#qaysi-sink-lar-ushbu-zaiflikni-keltirib-chiqaradi" id="qaysi-sink-lar-ushbu-zaiflikni-keltirib-chiqaradi"></a>

`document.cookie` bu zaiflikning keltirib chiqaruvchi asosiy metod hisoblanadi.

## <mark style="color:yellow;">Qanday qilib DOMga asoslangan cookieni boshqarish zaifligini oldini olish mumkin ?</mark> <a href="#qanday-qilib-dom-ga-asoslangan-cookie-manipulation-ni-oldini-olish-mumkin" id="qanday-qilib-dom-ga-asoslangan-cookie-manipulation-ni-oldini-olish-mumkin"></a>

Bu haqida ko'proq DOM-ga asoslangan zaifliklar mavzusida aytganmiz, siz cookie qiymatlarini dinamik o'zgarmasligini ta'minlashingiz zarur.
