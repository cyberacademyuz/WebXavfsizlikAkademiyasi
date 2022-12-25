# Insecure deserialization

Ushbu bo'limda biz **Insecure deserialization** nima ekanligi va u qanday qilib web saytni yuqori darajali xujumlarga nisbatan zaif qilishi mumkinligi haqida gaplashamiz. Biz muxim mavzularni yoritib beramiz va aniq PHP, RUBY, JAVA deserialization-ni aniq misollar yordamida keng qo'llaniladigan ba'zi texnikalarni ko'rsatamiz. Biz yana, web saytingizni qanday qilib Insecure deserialization dan himoya qilishning bir qancha yo'llarni ham ko'rib chiqamiz.

<figure><img src="../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
<mark style="color:yellow;">**Labaratoriyalar:**</mark>

Agar siz deserialization zaifliklari haqida bilsangiz pastdagi link orqali, haqiqiy web sayt kabi tuzilgan laboratoriyalarni yechishingiz mumkin.[<mark style="color:yellow;"></mark>\ <mark style="color:yellow;">Barcha deserialization labaratoriyalarini ko'rish ≫</mark>](https://portswigger.net/web-security/all-labs#insecure-deserialization)<mark style="color:yellow;"></mark>
{% endhint %}

## <mark style="color:yellow;">Serialization (ketma-ketlashtirish)</mark> <mark style="color:yellow;">nima ?</mark> <a href="#serialization-nima" id="serialization-nima"></a>

**Serialization** obyeklar va ularning formatlari kabi complex malumotlarni bytelar ketma-katliklaridek yuborish va qabul qilish uchun "flatter" formatiga o'tkazish jarayoni. Ma'lumotlarni ketma-ketlashtirish quyidagilarni osonlashtiradi.&#x20;

* Jarayonlar-aro xotiraga, faylga yoki ma'lumotlar bazasiga murakkab ma'lumotlarni yozishni
* Murakkab ma'lumotlarni yuborishni, masalan, tarmoq orqali, saytning turli komponentlari o'rtasida yoki API chaqiruvida&#x20;

Eng muhimi, obyektni **serialization** qilishda uning holati ham saqlanib qoladi. Boshqacha qilib aytganda, obyektning atributlari ularga berilgan qiymatlar bilan birga saqlanadi.

## <mark style="color:yellow;">Serialization vs Deserialization</mark> <a href="#serialization-vs-deserialization" id="serialization-vs-deserialization"></a>

**Deserialization** bu bayt oqimini u serialization qilinganidek aniq holatda haqiqiy obyektning to'liq funksional nusxasini tiklash jarayoni. Keyin sayt logikasi har qanday obyekt bilan bo'lgani kabi, bu **deserialization** qilingan obyekt bilan o'zaro aloqa qilishi mumkin.

<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

Ko'plab dasturlash tillari **serialization** ni qo'llab quvvatlaydi. Bu aslida dasturlash tilida obyektni qanday serialization qilinishiga ham bog'liq. Ba'zi dasturlash tillari obyektlarni [<mark style="color:yellow;">ikkilik sanoq sistemasiga</mark>](https://www.youtube.com/watch?v=DMMRYE\_NXRw) **serialization** qiladi, va ba'zi boshqalari esa odamlar o'qishi mumkin bo'lgan turli darajadagi turlicha string formatlaridan foydalanadi. Esda tutingki, haqiqiy obyektning barcha atributlari serialization qilingan ma'lumotlar oqimida, shu jumladan har qanday shaxsiy maydonlarda saqlanadi. Maydonni serialization qilinishini oldini olishuchun u class deklaratsiyasida "transient" sifatida belgilanishi kerak.

Shuni yodda tutingki, turli xil dasturlash tillari bilan ishlashda serialization qilish marshalling (Ruby-da) yoki pickling (Python-da) deb nomlanishi mumkin. Bu atamalar "serialization" ga sinonim hisoblanadi.

## <mark style="color:yellow;">Insecure Deserialization nima ?</mark> <a href="#insecure-deserialization-nima" id="insecure-deserialization-nima"></a>

**Insecure deserialization** bu qachonki web sayt tomonidan deserialization harakati amalga oshirilsa shunga aytiladi. Bu hakerga **serialized qilingan obyektlarga** web sayt uchun **xavfli kodlarni** yoki **ma'lumotlarni** kiritish imkonini beradi.

Bu hatto serialized qilingan obyektni butunlay boshqa turdagi obyekt ga almashtirish imkonini beradi. Ajablanarlisi, web-saytda mavjud bo'lgan har qanday turdagi obyektlar, qaysi turdagi obyekt kutilganidan qat'iy nazar, deserialization qilinadi va yaratiladi. Shu sababli, **insecure deserialization** ba'zan "object injection" zaifligi sifatida tanilgan.

Kutilmagan turdagi obyekt istisnoga olib kelishi mumkin. Biroq, bu vaqtga kelib, allaqachon zarar  yetgan bo'lishi mumkin. Deserialization-ga asoslangan ko'plab xujumlar deserialization tugaguniga qadar tugallanadi. Bu shuni anglatadiki, deserialization jarayonining o'zi, hatto web-saytning funksionalligi zararli obyekt bilan to'g'ridan to'g'ri aloqa qilmasa ham qilmasa ham, xujumni boshlashi mumkin. Shu sababli, logikasi kuchli yozilgan tillarga asoslangan web-saytlar ham ushbu usullarga nisbatan himoyasiz bo'lishi mumkin.

## <mark style="color:yellow;">Insecure Deserialization zaifliklari qanday hosil bo'ladi ?</mark> <a href="#insecure-deserialization-zaifliklari-qanday-hosil-boladi" id="insecure-deserialization-zaifliklari-qanday-hosil-boladi"></a>

Insecure Deserialization zaifliklari, **foydalanuvchi tomonidan boshqariladigan ma'lumotlarni deserialization qilish** qanchalik xavfli ekanligini tushunmaslik tufayli yuzaga keladi. Eng yaxshisi foydalanuvchi kiritadigan ma'lumotlar deserialization qilinishi kerak emas.

Biroq, ba'zida sayt dasturchilari deserialization qilingan ma'lumotni tekshirganliklari sabab biz xavfsizmiz deb o'ylashadi. Bundat yondashuv samarasiz hisoblanadi, sababi buni amalga oshirish va har bir hodisani hisobga olish imkonsiz. Bu tekshiruvlar fundamental xato hisoblanadi sababi tekshiruv, ma'lumot deserialization qilingandan so'ng amalga oshiriladi va ko'p hollarda hujumning oldini olish uchun juda kech bo'ladi.

Zaifliklar deserialization qilingan obyektlarni ishonchli deb tahmin qilgani uchun ham paydo bo'lishi mumkin. Ayniqsa dasturlash tili obyektlarni binar serialization formatidan foydalanayotganida dasturchilar, **foydalanuvchilar ushbu ma'lumotlarni o'qiy olmaydi** yoki **o'zgartira olmaydi** deb hisoblashadi. Biroq, bu ko'proq harakat talab qilishi mumkin bo'lsa-da, haker  stringga asoslangan formatlardan foydalanganidek, binar formatida serialization qilingan obyektlardan ham foydalanishi mumkin.

Zamonaviy web saytlardagi 3 tomon servislari bilan o'zaro bog'liklar soni tufayli deserialization ga asoslangan xujumlarni amalga oshirish mumkin. Oddiy sayt, har biri o'zinig shaxsiy 3-tomon xizmatlari bilan o'zaro bog'liqlikga ega kutubxonalarni ishlatishi mumkin. Bu xavfsiz boshqarish qiyin bo'lgan katta hajmdagi turlarni (obyektlardagi turlar kabi) va metodlarni yaratadi. Haker ushbu turlarning har qanday nuxsalarini yaratishi mumkinligi sababli, zararli ma'lumotlarga qanday metodlarni qo'llash mumkinligini oldindan aytish qiyin. Agar hacker uzun ketma ketlikdagi kutilmagan funksiya chaqiruvlarini birlashtira olsa, asl manbaga aloqasi bo'lmagan ma'lumotni funksiyaga kirita oladi. Shuning uchun zararli ma'lumotlar oqimini oldindan bilish va har bir zaiflik nuqtasini yopish deyarli imkonsiz.

## <mark style="color:yellow;">Insecure Deserialization ta'siri qanday ?</mark> <a href="#insecure-deserialization-tasiri-qanday" id="insecure-deserialization-tasiri-qanday"></a>

Insecure Deserializationning ta'siri juda jiddiy bo'lishi ham mumkin, chunki u hujum qilinuvchi qisimni zaiflik darajasini katta miqdorda oshiradi. Bu hakerga sayt kodlarini zarar keltiruvchi usullar uchun qayta foydalanishga imkon beradi, bu esa ko'plab boshqa zaifliklar paydoga olib keladi, ko'pincha **remote code execution** (_masofadan turib sayt serverida buyruqlar bajarish_) ni keltirib chiqaradi.

Remote code executionni amalga oshirish imkoni bo'lmagan hollarda ham, **Insecure deserialization** fayllarga o'zboshimchalik bilan kirish va DOS xujumlarini amalga oshirish uchun o'z imtiyozlarning kuchayishtira oladi.&#x20;

## <mark style="color:yellow;">Insecure deserialization zaifliklarini qanday qilib exploit qilish mumkin ?</mark> <a href="#insecure-deserialization-zaifliklarini-qanday-qilib-exploit-qilish-mumkin" id="insecure-deserialization-zaifliklarini-qanday-qilib-exploit-qilish-mumkin"></a>

Mana siz serialization va deserialization asoslari haqida tushunib oldingiz, endi Insecure deserialization zaifliklarini qanday qilib exploit qilish mumkinligi haqida o'rganishingiz mumkin.

{% hint style="info" %}
**Ko'proq o'qish:**\
****[Insecure deserialization zaifliklarini exploit qilish ≫](insecure-deserialization-zaifliklarini-exploit-qilish.md)
{% endhint %}

## <mark style="color:yellow;">Insecure Deserialization zaifliklarini qanday oldini olish mumkin ?</mark> <a href="#qanday-qilib-insecure-deserialization-zaifliklarini-oldini-olish-mumkin" id="qanday-qilib-insecure-deserialization-zaifliklarini-oldini-olish-mumkin"></a>

Umuman olib aytganda, agar foydalanuvchi kiritadigan ma'lumotlarni deserialization qilish judayam muhim bo'lmasa bundan foydalanmaslik kerak. Bu, amalga oshirish mumkin bo'lgan exploitlar juda jiddiy va ularga qarshi kurashish ko'pincha unchalik ham samara bermasligi mumkinligini bildiradi.&#x20;

Agar sizga ishonchsiz manbalardan ma'lumotlarni deserialize qilish kerak bo'lsa unda siz kelayotgan obyektlarni buzilmaganligiga ishonchingiz komil bo'lishi kerak. Masalan siz [raqamli signature](https://support.microsoft.com/en-us/office/digital-signatures-and-certificates-8186cd15-e7ac-4a16-8597-22bd163e8e96)-dan foydalanib ma'lumotning butunligini tekshirishingiz mumkin. Biroq shuni esda tutingki har qanday tekshiruv deserialization jarayonidan oldin amalga oshirilishi shart. Bo'lmasam ular foydasiz hisoblanadi.

Agar iloji bo'lsa, barcha deserialization xususiyatlarini o'chirib qo'yganingiz maqul. Serialized (ketma-ket qilingan) obyekt ushbu metodlardan orginal obyektning barcha atributlarini, hamda o'zida maxfiy ma'lumotlarni saqlaydigan maxfiy ma'lumot maydonlarini o'z ichiga olishi mumkin. Buning o'rniga, hech bo'lmaganda qaysi maydonlar serialization qilinishini nazorat qilishingiz uchun o'zingizning turga xos  serialize qilish metodlaringizni yaratishingiz mumkin.

