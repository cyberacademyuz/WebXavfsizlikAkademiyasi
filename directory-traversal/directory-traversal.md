# Directory traversal


Ushbu bo'limda <b>directory traversal</b> nima ekanligini tushuntiramiz, directory traversal hujumlarini qanday amalga oshirish va umumiy to'siqlarni chetlab o'tishni ko'rib chiqamiz va directory traversal zaifliklarini oldini olishni o'rgatamiz.



{% hint style="warning" %}
<mark style="color:yellow;">**Labaratoriyalar:**</mark>

Agar siz directory traversal haqida bilsangiz pastdagi link orqali, haqiqiy web sayt kabi tuzilgan laboratoriyalarni yechishingiz mumkin.[<mark style="color:yellow;"></mark>\ <mark style="color:yellow;">Barcha directory traversal labaratoriyalarini ko'rish ≫</mark>](https://portswigger.net/web-security/all-labs#directory-traversal)<mark style="color:yellow;"></mark>
{% endhint %}

# <mark style="color:yellow;">Directory traversal nima ?</mark>

Directory traversal (shuningdek, file path traversal deb ham ataladi) bu hackerga websayt joylashgan serverdagi fayllarni o'zboshimchalik bilan o'qish imkonini beruvchi web-xavfsizlik zaifligi. Bunga websayt kodi va maʼlumotlari, ichki tizimlar uchun hisob maʼlumotlari va operatsion tizimning maxfiy fayllari kiradi. Ba'zi holatlarda hacker serverdagi istalgan fayllarga yozishi mumkin, bu unga websayt ma'lumotlarini yoki xatti-harakatlarini o'zgartirishi va oxir-oqibat serverni to'liq nazorat qilish imkonini beradi.
