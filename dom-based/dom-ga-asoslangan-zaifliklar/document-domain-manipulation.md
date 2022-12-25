# Document-domain manipulation

Ushbu bo'limda biz `document.domain` xususiyatining DOM-ga asoslangan manipulyatsiyasini tushuntirib beramiz va bu turdagi hujumlarni amalga oshirish yo'llarini qisqartirish haqida gaplashamiz.

## <mark style="color:yellow;">DOMga asoslangan document-domain manipulation nima ?</mark> <a href="#dom-ga-asoslangan-document-domain-manipulation-nima" id="dom-ga-asoslangan-document-domain-manipulation-nima"></a>

Document-domain manipulation qachonki Haker `document.domain` ga ma'lumot qo'sha olganida qilinadi. Balki ushbu xujumni Haker URL tuzish orqali ham amalga oshirishi mumkindir, misol uchun foydalanuvchi o'sha URL ga tashrif buyursa `document.domain` qiymati o'zgaradi.

`document.domain` brauzer tomonidan same orign policyni amalga oshirishda foydalaniladi. Agar turli manbalardagi ikkita sahifa aniq bir xil `document.domain` qiymatini olsa, bu ikki sahifa cheklanmagan usullarda o'zaro ta'sir ko'rsatishi mumkin. Agar Haker web saytga ham va o'zi boshqarayotgan sahifasiga (dinamik tarzda yoki XSS ga o'xshash zaiflik bilan) bir xil  `document.domain` qiymatini o'rnatsa o'zi boshqaroytgan sahifa orqali nishondagi sahifani to'liq murosaga keltira oladi. Bu holat esa bir qancha [<mark style="color:yellow;">XSS</mark>](broken-reference) xujumlarini amalga oshirish imkonini yaratadi.

Brauzerlar odatda `document.domain`ga tayinlanishi mumkin bo'lgan qiymatlarga ba'zi cheklovlar qo'yadi va sahifaning haqiqiy originiga qaraganda butunlay boshqa qiymatlardan foydalanishga to'sqinlik qilishi mumkin. Ammo bu borada ikkita muhim ogohlantirish mavjud. Birinchisi, brauzerlar subdomenlardan foydalanishga ruxsat beradi, shuning uchun Haker nishondagi sahifaning domenini himoyasi zaiflroq bo'lgan web-sayt domeniga o'tkazishi mumkin. Ikkinchidan, ba'zi brauzerlar aloqasi bo'lmagan domenlarga o'tish imkonini beradi. Ushbu ogohlantirishlar sahifaning `document.domain` xususiyatini boshqarish qobiliyatiga, odatda jiddiyligi XSSga o'xshash xavfsizlik zaifligini anglatadi.

## <mark style="color:yellow;">Qaysi metodlar DOMga asoslangan document-domain manipulation zaifliklarini keltirib chiqaradi ?</mark> <a href="#qaysi-sink-lar-dom-ga-asoslangan-document-domain-manipulation-zaifliklarini-keltirib-chiqaradi" id="qaysi-sink-lar-dom-ga-asoslangan-document-domain-manipulation-zaifliklarini-keltirib-chiqaradi"></a>

`document.domain` metodi DOMga asoslangan document-domain manipulation zaifliklarini keltirib chiqaradi.

## <mark style="color:yellow;">Qanday qilib DOMga asoslangan document-domain zaifliklarini oldini olish mumkin ?</mark> <a href="#qanday-qilib-dom-ga-asoslangan-document-domain-zaifliklarini-oldini-olish-mumkin" id="qanday-qilib-dom-ga-asoslangan-document-domain-zaifliklarini-oldini-olish-mumkin"></a>

Bu haqida ko'proq [<mark style="color:yellow;">DOM-ga asoslangan zaifliklar</mark> ](broken-reference)mazusida aytganmiz, siz document.domain qiymatlarini dinamik o'zgarmasligini ta'minlashingiz zarur.
