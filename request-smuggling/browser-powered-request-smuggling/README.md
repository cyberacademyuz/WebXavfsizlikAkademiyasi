# Browser-powered request smuggling

Ushbu bo'limda siz brauzerlar hech qachon yubormaydigan [<mark style="color:yellow;">malformed requestlar</mark>](https://www.google.com/search?q=what+is+%22malformed+request%22)ga tayanmasdan, qanday qilib yuqori darajadagi exploitlarni yaratishni o'rganasiz. Bu nafaqat veb-saytlarning butunlay yangi diapazonini server qismidagi request smuggling-ga olib keladi, balki foydalanuvchi brauzerini zaif veb-server bilan o'z ulanishini zaharlashga undash orqali ushbu hujumlarning browserdagi o'zgarishlarini amalga oshirish imkonini beradi.

{% hint style="success" %}
Portswigger Reasearch:\
Bu bo'limdagi materiallar va labaratoriyalar Browser-Powered Desync Attacks ga asoslangan: PortSwigger Research tomonidan [<mark style="color:yellow;">HTTP requestlarini yashirin olib o'tishda yangi chegara.</mark>](https://portswigger.net/research/browser-powered-desync-attacks) Ushbu tadqiqot, shuningdek, <mark style="color:yellow;">ulanish holatidagi kamchiliklar</mark>dan foydalangan holda Host headerlari filtrlari uchun aylanib o'tish texnikasini kashf etishga olib keldi.
{% endhint %}

## <mark style="color:yellow;">CL.0 request smuggling</mark>

Back-end serverlarini ba'zan `Content-Length` headerlarini e'tiborsiz qoldirishiga ko'ndirish mumkin, ya'ni ular kiruvchi requestlarning asosiy qismini e'tiborsiz qoldiradilar. Bu [<mark style="color:yellow;">chunked transfer encoding</mark>](../http-request-smuggling/#qanday-qilib-http-request-smuggling-zaifliklari-vujudga-keladi)ga yoki [<mark style="color:yellow;">HTTP/2 versiyasini pasaytirishga</mark>](../advanced-request-smuggling/http-2-downgrade.md) tayanmaydigan requestni yashirin olib o'tish hujumlariga yo'l ochadi.

{% hint style="info" %}
**Batafsil o'qish:**\
[O'quv materiallari ☰](cl.0.md)
{% endhint %}

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [CL.0 request smuggling ≫](https://portswigger.net/web-security/request-smuggling/browser/cl-0/lab-cl-0-request-smuggling)
{% endhint %}

## <mark style="color:yellow;">Client-side desync</mark>

<mark style="color:yellow;"></mark>

Request smuggling an'anaviy ravishda server tomonidagi muammo hisoblanadi, chunki undan faqat Burp Repeater kabi maxsus vositalar yordamida foydalanish mumkin standart brauzerlar desinxronlashni boshlash uchun zarur bo'lgan so'rovlarni yubormaydi. Biroq, CL.0 hujumlaridan olingan saboqlarga asoslanib, ba'zida brauzerga to'liq mos keladigan HTTP/1 so'rovlari yordamida desinxronlashni keltirib chiqarishi mumkin.

Brauzer va zaif veb-server o‘rtasida browser tomonida desinxronlashni (CSD) ishga tushirish uchun ushbu brauzerga mos requestlardan foydalanishingiz mumkin, bu esa yashirin olib o'tish va intranet saytlariga kira olmaydigan yagona serverli saytlarga hujumlarni bevosita amalga oshirish imkonini beradi.

{% hint style="info" %}
**Batafsil o'qish:**

[Client-side desynce ☰](client-side-desync.md)
{% endhint %}

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Client-side desync ≫](https://portswigger.net/web-security/request-smuggling/browser/client-side-desync/lab-client-side-desync)\
<mark style="color:yellow;">**Lab:**</mark> [Client-side desync orqali Browser cache poisoning ≫](https://portswigger.net/web-security/request-smuggling/browser/client-side-desync/lab-browser-cache-poisoning-via-client-side-desync)
{% endhint %}

## <mark style="color:yellow;">Pause-based desync</mark>

Xavfsiz ko'rinadigan saytlar, requestning o'rtasida uni pauza qilganingizdagina aniqlanuvchi yashirin desync zaifliklarini o'z ichiga olgan bo'lishi mumkin. Headerlarni yuborish, bodyni va'da qilish va shunchaki kutish orqali ba'zan server tomonida ham, browser tomonidan ham exploitlar uchun ishlatilishi mumkin bo'lgan qo'shimcha desync zaifliklarini topishingiz mumkin.&#x20;

{% hint style="info" %}
**Batafsil o'qish:**\
[Pause-based desync ☰](pause-based-desync.md)
{% endhint %}

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark>[ Pause-based desync ≫](https://portswigger.net/web-security/request-smuggling/browser/pause-based-desync/lab-server-side-pause-based-request-smuggling)
{% endhint %}
