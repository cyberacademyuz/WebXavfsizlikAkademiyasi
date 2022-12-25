# Same orign policy

Biz ushbu bo'limda Same origin policy (SOP) nima ekanligi va u qanday amalga oshirilishi haqida gaplashamiz.

## <mark style="color:yellow;">Same origin policy nima ?</mark> <a href="#same-origin-policy-nima" id="same-origin-policy-nima"></a>

Same origin policy brauzerning xavfsizlik mexanizmi bo'lib u saytlarni o'zaro bir biriga xujum qilishini oldini oladi. Same origin policy bir sayt boshqa saytning ma'lumotlariga kirishni cheklaydi. Saytning URI tuzilishi  domen va port raqamlaridan iborat bo'ladi. Misol uchun, quyidagi URL ni ko'rib chiqing:

```url
http://normal-website.com/example/example.html
```

Ushbu URL `http` protokolidan foydalanadi, domen esa `normal-website`, port raqami esa `80`. Quyidagi jadvalda yuqoridagi URL manzilidagi kontent boshqa manbalarga kirishga harakat qilsa, same-orign policy qanday qo‘llanilishi ko‘rsatilgan:

| Kiruvchi URL                              | Ruxsat beriladimi ?                 |
| ----------------------------------------- | ----------------------------------- |
| `http://normal-website.com/example/`      | Ha: protakol, port va domen bir xil |
| `http://normal-website.com/example2/`     | Ha: sxema, port va domen bir xil    |
| `https://normal-website.com/example/`     | Yo'q: protokol va port boshqa       |
| `http://en.normal-website.com/example/`   | Yo'q: domen boshqa                  |
| `http://www.normal-website.com/example/`  | Yo'q: domen boshqa                  |
| `http://normal-website.com:8080/example/` | Yo'q: port boshqa                   |

Internet explorer ba'zilariga ruxsat beradi chunki IEning same-origin siyosati  port raqamiga e'tibor bermaydi.

## <mark style="color:yellow;">Nima uchun same-origin policy muhim ?</mark> <a href="#nima-uchun-same-origin-policy-muhim" id="nima-uchun-same-origin-policy-muhim"></a>

Qachonki brauzer bir mandabadan boshqa bir manbaga HTTP so'rov yuborsa, unda uning ichida gicookielar, autentikatsiya sessiya cookielari va boshqa domenga tegishli bo'lgan narsalar ham so'rovning bir qismi sifatida yuboriladi. Bu degani responga, **user session** va **foydalanuvchiga tegishli** bo'lgan narsalar bilan birgalikda qaytariladi. Agar hozirgi kunda same-origin policy bo'lmaganda, siz biror zararli saytga kirsangiz unda u sayt sizni emaillaringizni o'qishi, Facebookdagi sirli yozishmalaringizni ko'rishi va boshqa ishlarni qila olardi.

## <mark style="color:yellow;">Qanday qilib same-origin policy amalga oshiriladi ?</mark> <a href="#qanday-qilib-same-origin-policy-amalga-oshiriladi" id="qanday-qilib-same-origin-policy-amalga-oshiriladi"></a>

Same origin policy odatda, JavaScriptning kodi domenlararo yuklangan kontentga kirishini nazorat qiladi. Odatda sahifalarning manbalarini yuklashiga ruxsat beriladi. Misol uchun, [<mark style="color:yellow;">SOP</mark>](same-orign-policy.md) rasmlarni `<img>` tegi bilan, videolarni esa `<video>` tegi bilan va JavaScriptni `<script>` tegi bilan birga yuklash imkonini beradi. Ammo bu tashqi resurslar faqat sahifa tomonidan yuklanadi va undagi biriktirilgan JavaScript bu resurslarni o'qiy olmaydi.

Same-origin policy da bir qancha har xil istisnolar bor:

* Ba'zi obyektlarni yozish mumkin ammo o'qiy olish imkoni mavjud emas, misol uchun `location` obyekti yoki `location.href` xususiyatidan keladigan iframe va yangi oynalar.
* Ba'zi obyektlarni o'qish mumkin ammo yozish imkoni mavjud emas, misol uchun `window` obyektining `length` xususiyati (sahifada foydalanilayotgan freymlar sonini saqlaydi) va `closed` xususiyati.
* `replace` funksiyasi domenlararo `location` obyektini o'zgartira oladi.
* Siz yana bir qancha funksiyalarni domenlararo ham chaqira olasiz. Misol uchun, siz `close`, `blur` va `focus` funksiyalarini yangi oynada chaqira olasiz. `postMessage` funksiyasi ham yangi oynalarda yoki iframe larda domenlararo chaqirilish imkoniyati bor.

Eski talablardan kelib chiqqan holda, cookie bilan ishlashda same origin policy aylanib o'tsa bo'ladi, shuning uchun har bir subdomain texnik jihatdan boshqa manba bo'lsa ham ularga saytning barcha subdomenlaridan kirish mumkin bo'ladi. `HttpOnly` cookie yordamida bu xavfni qisman kamaytirishingiz mumkin.

`document.domain` yordamida same origin policy ni yumshatish mumkin. Ushbu maxsus xususiyat sizga ma'lum bir domen uchun SOPni yumshatish imkonini beradi, lekin bu sizga [FQDN](https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F6OQTXTKi2jwkYoRLierX%2Fuploads%2FV8nc8w5DsKczqL8w4g3s%2FNomsiz%20dizayn%20nusxasi%20nusxasi.png?alt=media\&token=e156b79d-821e-4a0d-aaa5-2b2334fa3146) (fully qualified domain name) ning bir qisminigina bajarishga ruxsat etadi. Misol uchun, sizda `marketplace.example.com` nomli domen bor va siz ushbu domendagi kontentlarni `example.com`da ham o'qimoqchisiz. Bunday qilish uchun ikkala domenlarning `document.domain` xususiyatini example.com ga tenglashtirish kerak bo'ladi. Shundan keyin SOP bu ikkala domenga bir biri bilan ma'lumot almashishiga ruxsat beradi. Oldinlari `document.domain`da shunchaki domen nomi (`.com`)ni bir xil qilish kifoya edi, hozirgi brauzerlarda buning oldi olingan.
