# Stored XSS

<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

Biz ushbu bo'limda XSS zaifliklaridan biri bo'lmish stored XSS zaifliklari haqida gaplashamiz va stored XSS zaifliklarini qanday topishni o'rganamiz

## <mark style="color:yellow;">Stored Cross-site scripting nima?</mark> <a href="#stored-cross-site-scripting-nima" id="stored-cross-site-scripting-nima"></a>

Stored XSS (second-order yoki persistent XSS ham deyiladi ) websayt ishonchsiz manbadan maʼlumotlarni qabul qilib va bu maʼlumotlarni oʻzining keyingi HTTP responselariga xavfli tarzda qoʻshganda paydo boʻladi.

Aytaylik bitta web sayt postlarga izoh qoldirish imkoniyatiga ega. Foydalanuvchilar quyidagi HTTP so'rovi ko'rinishida izohlar qoldirishadi:

```http
POST /post/comment HTTP/1.1
Host: vulnerable-website.com
Content-Length: 100

postId=3&comment=This+post+was+extremely+helpful.&name=Carlos+Montoya&email=carlos%40normal-user.net
```

Izoh jo'natib bo'lingandan so'ng foydalanuvchilar quyidagicha web saytdan javob qabul qilishadi:

```html
<p>This post was extremely helpful.</p>
```

Web sayt hech qanday ma'lumotlarni tekshirmaydi va bu holatdan haker unumli foydalanib boshqa foydalanuvchilarga ta'sir etuvchi skript yozib jo'nata oladi:

```html
<p><script>/* Bad stuff here... */</script></p>
```

Haker so'rovi quyidagicha kodlanib so'rov yuboriladi:

```http
comment=%3Cscript%3E%2F*%2BBad%2Bstuff%2Bhere...%2B*%2F%3C%2Fscript%3E
```

Har qanday foydalanuvchi ushbu jo'natilgan izohni ko'ra oladi:

```html
<p><script>/* Bad stuff here... */</script></p>
```

Keyin haker tomonidan taqdim etilgan skript jabrlanuvchining brauzerida websayt bilan sessiyasi kontekstida ishlaydi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark>[ Oddiy Stored XSS ≫](https://portswigger.net/web-security/cross-site-scripting/stored/lab-html-context-nothing-encoded)
{% endhint %}

## <mark style="color:yellow;">Stored XSS ta'siri</mark> <a href="#stored-xss-tasiri" id="stored-xss-tasiri"></a>

Agar Haker jabrlanuvchining brauzerida skriptni ishga tushira olsa ular ushbu foydalanuvchini butunlay xavf ostiga qo'yishi mumkin. Keyin esa haker [<mark style="color:yellow;">Reflected XSS zaifligi</mark>](reflected-xss.md)da nima qila olgan bo'lsa u ishlarni ham qila oladi.

Reflected XSS va stored XSS exploit qilinishidagi farqi shundaki Stored XSS da Haker ishtirok etishi shart emas exploit saytning o'zida sodir bo'ladi. Haker yana qo'shimcha yo'l topib foydalanuvchilarni URL ga kirishga chaqirishi shart emas. Haker exploitni web sayt ichida qoldiradi va foydalanuvchini unga duch kelishini kutadi.

Stored XSS ning exploitlari ko'pincha web saytda ro'yhatdan o'tganlarga ta'sir ko'rsatadi. Agarda XSS Reflected bo'lsa unda hujum ba'zi holatlarda bo'ladi : chunki URL orqali kirgan foydalanuvchilarning hammasi ham web saytdan ro'yhatdan o'tmagan bo'lishi mumkin va bu ularning web sayt bilan bo'lgan aloqalariga hech qanday zarar yetkazmaydi. Aksincha, agar XSS stored bo'lsa, exploit sodir bo'lganida foydalanuvchi ro'yhatdan o'tganligi aniq bo'ladi.

{% hint style="info" %}
**Ko'proq o'qish:**

[XXS zaifliklarini exploit qilish ☰](xss-zaifliklarini-exploit-qilish.md)
{% endhint %}

## <mark style="color:yellow;">Turli holatlardagi Stored XSS</mark> <a href="#stored-xss-har-xil-kontekstlarda" id="stored-xss-har-xil-kontekstlarda"></a>

Stored XSSning har xil turlari mavjud. Web sayt responsida saqlangan ma'lumotlarning joylashuvi undan foydalanish uchun qanday payload kerakligini ifodalaydi va zaiflikning niqtasiga ham tassir qilishi mumkin.

Bunga qo'shimcha ravishda, agar web sayt ma'lumotlar saqlanishidan oldin yoki saqlangan ma'lumotlar responselarga qo'shilgan vaqtda har qanday tekshirish yoki boshqa ishlov berishni amalga oshirsa, bu odatda stored XSS ni amalga oshirish imkonini beradi.

{% hint style="info" %}
**Ko'proq o'qish:**

[XSS kontekstlari ☰](xss-kontekstlari.md)
{% endhint %}

## Stored XSS zaifliklarini qanday topish va test qilish mumkin <a href="#stored-xss-zaifliklarini-qanday-topish-va-test-qilish-mumkin" id="stored-xss-zaifliklarini-qanday-topish-va-test-qilish-mumkin"></a>

Stored XSS zaifliklarining ko'pchiligi Burp Suite ning [<mark style="color:yellow;">web zaifliklar skaneri</mark>](https://portswigger.net/burp/vulnerability-scanner) bilan topish mumkin.

Stored XSS ni test qilish biroz qiyin hisoblanadi. Siz haker tomonidan boshqariladigan ma'lumotlar web sayt protsesslariga kirishga imkon beruvchi barcha tegishli "kirish nuqtalari"ni va ushbu ma'lumotlar web sayt javoblarida paydo bo'lishi mumkin bo'lgan barcha "chiqish nuqtalarini" test qilib ko'rishingiz kerak.

Websayt protseslarini o'z ichiga oluvchi kirish nuqtalari quyidagilar:

* URL ning query siga va matniga qo'yiladigan parametr yoki har qanday boshqa ma'lumotni
* URL fayl yo'lini
* <mark style="color:yellow;"></mark>[<mark style="color:yellow;">Reflected XSS</mark>](reflected-xss.md) bilan bog'liq holda foydalanilmasligi mumkin bo'lgan HTTP so'rovi sarlavhalarini
* Haker web saytgama'lumot kiritishining iloji bo'lgan har qanday tarmoqdan tashqari marshrutlarni. Mavjud marshrutlar butunlay web sayt tomonidan amalga oshirilgan funksionallikka bog'liq: web-pochta ilovasi elektron pochta orqali olingan ma'lumotlarni qayta ishlaydi; Twitter lentasini ko'rsatadigan web sayt uchinchi tomon tvitlaridagi ma'lumotlarni qayta ishlashi mumkin; va yangiliklar agregatori boshqa web-saytlardan olingan ma'lumotlarni o'z ichiga oladi.

Stored XSS hujumlari uchun "chiqish nuqtalari" har qanday vaziyatda har qanday web sayt foydalanuvchisiga qaytariladigan barcha HTTP javoblaridir.

Stored XSS zaiflilklarini test qilishning birinchi qadami kirish va chiqish nuqtalari orasidagi kiritlgan ma'lumotni chiqaruvchi bo'glamlarni aniqlash. Nima uchun bu ancha mushkul ekanligiga quyidagilar sabab bo'ladi:

* Har qanday kirish nuqtasiga taqdim etilgan ma'lumotlar, qoida tariqasida, har qanday chiqish nuqtasidan chiqarilishi mumkin. Masalan, foydalanuvchi tomonidan faqat ba'zi web sayt foydalanuvchilariga ko'rinadigan usernamelar noaniq audit logida paydo bo'lishi mumkin.
* Hozirda web sayt tomonidan saqlanadigan ma'lumotlar web sayt ichida amalga oshirilgan boshqa harakatlar tufayli ko'pincha qayta o'zgartirilishi mumkin. Misol uchun, qidiruv funksiyasi so'nggi qidiruvlar ro'yxatini ko'rsatishi mumkin, ular foydalanuvchilar boshqa qidiruvlarni amalga oshirayotganda tezda almashtiriladi.

Kirish va chiqish nuqtalari o'rtasidagi aloqalarni har tomonlama aniqlash uchun har bir o'zgarishni alohida sinab ko'rish, kirish nuqtasiga ma'lum qiymatni kiritish, to'g'ridan-to'g'ri chiqish nuqtasiga o'tish va qiymat u yerda paydo bo'ladimi yoki yo'qligini aniqlash kerak. Biroq, bu usul bir necha sahifadan ortiq bo'lgan  saytda ishlamaydi.

Buning o'rniga, ma'lumotlarni kiritish nuqtalari orqali tizimli ravishda ishlash, har biriga ma'lum qiymatni taqdim etish va taqdim etilgan qiymat paydo bo'lgan holatlarni aniqlash uchun web saytning javoblarini kuzatish yanada yaxshiroq usuldir. Web saytning tegishli funksiyalariga, masalan, blog postdagi izohlarga alohida e'tibor berilishi kerak. Kiritilgan ma'lumot javobda ko'ringanida, darhol javobda ko'rinishidan farqli o'laroq, ma'lumotlar haqiqatan ham turli so'rovlar bo'ylab saqlanishini aniqlashingiz kerak.

Websaytdagi kirish va chiqish nuqtalari orasidagi aloqalarni aniqlaganingizda, stored XSS zaifligi mavjudligini aniqlash uchun har bir havola maxsus sinovdan o'tkazilishi kerak. Bu stored ma'lumotlar response ichidagi kontekstda borligini aniqlashni va ushbu kontekstga mos keladigan XSS payloadlarini sinab ko'rishni o'z ichiga oladi. Bu yerda sinovdan o'tkazish metodologiyasi [<mark style="color:yellow;">reflected XSS zaiflik</mark>](reflected-xss.md)larini topish bilan bir ximl
