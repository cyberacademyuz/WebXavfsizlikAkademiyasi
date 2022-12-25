# Web message sourceni boshqarish

Ushbu bo'limda biz veb-xabarlardan qabul qiluvchi sahifasida DOM-ga asoslangan zaifliklardan foydalanish uchun manba sifatida qanday foydalanish mumkinligini ko'rib chiqamiz. Yana biz qanday qilib bu xujumlarni qilishimiz mumkinligi va qanday xolatlar orqali ushbu xujumni uyushtirish uchun nimalarni aylanib o'tishimiz kerakligini o'rganib chiqamiz

Agar sahifa kelayotgan xabarlarni himoyasiz yo'l bilan qabul qilsa, misol uchun, event listener da kelayotgan xabarlarni qayerdan kelayotganini tekshirmasa unda event listener orqali chaqirilgan xususiyatlar va funksiyalar metodlarga aylanadi. Misol uchun, haker o'zini `iframe` ini `postMessage()` funksiyasiga qo'yishi mumkin va natijada event listener ushbu payloadni sahifaga yuboradi va sahifa exploit bo'ladi. Bu holat shuni anglatadiki web messagelarda ma'lumotlarni propaganda qilish mumkin.

## <mark style="color:yellow;">DOMga asoslangan web message zaifligini ta'siri qanday ?</mark> <a href="#dom-ga-asoslangan-web-message-zaifligini-tasiri-qanday" id="dom-ga-asoslangan-web-message-zaifligini-tasiri-qanday"></a>

Agar websayt hujjat jo'natuvchiga xabardagi zararli ma'lumotlarni uzatmasligiga ishonsa va ma'lumotlarni metodga o'tkazish orqali himoyasiz tarzda ishlasa, u holda ikkita hujjatning birgalikdagi xatti-harakati hackerga foydalanuvchini buzishga imkon berishi mumkin

## <mark style="color:yellow;">Source sifatida web-xabarlardan foydalanib, hujumni qanday tuzish kerak ?</mark>

Agar quyidagi kodga duch kelsangiz:

```javascript
<script>
window.addEventListener('message', function(e) {
  eval(e.data);
});
</script>
```

Bu zaif chunki haker eval funksiyasi asnosida o'zining JavaScript kodini ishga tushirishi mumkin, masalan `iframe` orqali:

```html
<iframe src="//vulnerable-website" onload="this.contentWindow.postMessage('print()','*')">
```

Xabarni qayerdan kelayotganini event listener tekshirmaydi va `postMessage()`da `targetOrigin:` "`*`" metodi mavjud va bu event listenerning funksiyani qabul qilishiga va metodga aylanishiga olib keladi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Web habarlardan foydalanib DOM XSS qilish ≫](https://portswigger.net/web-security/dom-based/controlling-the-web-message-source/lab-dom-xss-using-web-messages)
{% endhint %}

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Web habarlardan va JavaScript URLidn foydalanib DOM XSS hujumi ≫](https://portswigger.net/web-security/dom-based/controlling-the-web-message-source/lab-dom-xss-using-web-messages-and-a-javascript-url)
{% endhint %}

## <mark style="color:yellow;">Origin verification</mark> <a href="#origin-verification" id="origin-verification"></a>

Agar event listener ba'zi bir origin verification arni qabul qilsa undayam u zaif hisoblanadi, masalan:

```javascript
window.addEventListener('message', function(e) {
    if (e.origin.indexOf('normal-website.com') > -1) {
        eval(e.data);
    }
});
```

`indexOf` metodi kiruvchi xabarning origini `normal-website.com` domeni ekanligini tekshirish uchun ishlatiladi. Biroq, amalda, u faqat "normal-website.com" qatori URL manzilining istalgan joyida mavjudligini tekshiradi. Natijada, Haker, zararli xabarida `http://www.normal-website.com.evil.net` bo'lsa, bu tekshirish bosqichini osongina chetlab o'tishi mumkin.

Xuddi shunday muammo `startsWith()` va `endsWith()` da ham uchraydi. Masalan, quyidagi event listener `http://www.malicious-websitenormal-website.com` originini xavfsiz deb hisoblaydi:

```javascript
window.addEventListener('message', function(e) {
    if (e.origin.endsWith('normal-website.com')) {
        eval(e.data);
    }
});
```

{% hint style="warning" %}
&#x20;<mark style="color:yellow;">**Lab:**</mark> [Web habarlar va `JSON.parse` dan foydalanib DOM XSS hujumi ≫](https://portswigger.net/web-security/dom-based/controlling-the-web-message-source/lab-dom-xss-using-web-messages-and-json-parse)
{% endhint %}

## <mark style="color:yellow;">Qaysi metodlar DOMga asoslangan web message zaifligini keltirib chiqaradi ?</mark> <a href="#qaysi-sink-lar-dom-ga-asoslangan-web-message-zaifligini-keltirib-chiqaradi" id="qaysi-sink-lar-dom-ga-asoslangan-web-message-zaifligini-keltirib-chiqaradi"></a>

Agar web-sayt ishonchli manbadan veb-xabar ma'lumotlarini qabul qilsa, kelib chiqishi to'g'ri tasdiqlanmaganligi sababli, kiruvchi xabar event listener tomonidan ishlatiladigan har qanday sink lar zaifliklarga olib kelishi mumkin.
