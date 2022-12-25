# AngularJS sandbox

Ushbu bo'limda biz client-side template zaifliklarini va ularni [<mark style="color:yellow;">XSS</mark> ](broken-reference)hujumlari uchun qanday ishlatishni ko'rib chiqamiz. Client-side template umumiy zaiflik bo'lsa-da, biz AngularJS sandbox misollariga e'tibor qaratamiz, chunki bu juda keng tarqalgan. Biz AngularJS sandboxni aylanib o'tuvchi ekspluatatsiyalarni qanday yaratishingizni va kontent xavfsizligi siyosatini ([<mark style="color:yellow;">CSP</mark>](content-security-policy.md)) chetlab o‘tish uchun AngularJS funksiyalaridan qanday foydalanishingiz mumkinligini tasvirlab beramiz .

## <mark style="color:yellow;">Client-side template nima ?</mark> <a href="#client-side-template-nima" id="client-side-template-nima"></a>

Client-side template ineksiya qilishda zaifliklar client-side template frameworklar foydalanuvchi ma'lumotlarini veb-sahifalarga dinamik tarzda joylashtirganda paydo bo'ladi. Sahifani ko'rsatayotganda, framework uni expressionlar uchun skanerlaydi va duch kelgan har qanday ishni bajaradi. Hacker XSS hujumini amalga oshirganda zararli template expressionni taqdim etish orqali bundan foydalanishi mumkin.

## <mark style="color:yellow;">AngularJS Sandbox o'zi nima ?</mark> <a href="#angularjs-sandbox-ozi-nima" id="angularjs-sandbox-ozi-nima"></a>

AngularJS Sandbox bu potensiali xavfli bo'lgan obyektlarga Angular shabloni orqali kirishga imkon beruvchi mexanizm, masalan window yoki document obyektlariga. U yana xavfli potensialga ega xususiyatlarga ham kirish imkonini beradi masalan `__proto__`. AngularJS jamoasi tomonidan xavfsizlik chegarasi hisoblanmasa ham, kattaroq dasturchilar hamjamiyati umuman boshqacha fikrda. Sandbox ni chetlab o'tish dastlab qiyin bo'lsa-da, xavfsizlik bo'yicha tadqiqotchilar buni amalga oshirishning ko'plab usullarini o'ylab topishdi. Natijada, u oxir-oqibat 1.6 versiyasida AngularJS-dan olib tashlandi. Biroq, ko'pgina eski saytlar hali ham AngularJS ning eski versiyalaridan foydalanadi va buning natijasida ularda zaiflik bo'lishi mumkin.

## <mark style="color:yellow;">AngularJS Sandbox qanaqa ishlaydi ?</mark> <a href="#angularjs-sandbox-qanaqa-ishlaydi" id="angularjs-sandbox-qanaqa-ishlaydi"></a>

Sandbox JavaScript-ni qayta yozish va keyin qayta yozilgan kodda xavfli ob'ektlar mavjudligini tekshirish uchun turli funksiyalardan foydalanish orqali ishlaydi. Misol uchun, `ensureSafeObject()` funksiyasi berilgan obyektning o'ziga murojaat qilishini tekshiradi. Bu masalan, `window` obyektini aniqlashning bir usulidir. `Function` konstruktori konstruktor xususiyatining o'ziga murojaat qilish yoki yo'qligini tekshirish orqali taxminan xuddi shunday tarzda aniqlanadi.

`ensureSafeMemberName()` funksiyasi obyektning har bir xususiyatga kirishini tekshiradi va agar u `__proto__` yoki `__lookupGetter__` kabi xavfli xususiyatlarni o'z ichiga olsa, obyekt bloklanadi. `ensureSafeFunction()` funksiyasi `call()`, `apply()`, `bind()` yoki `constructor()` ni chaqirishdan himoyalaydi.

<mark style="color:yellow;"></mark>[<mark style="color:yellow;">Ushbu sahifaga</mark>](http://jsfiddle.net/2zs2yv7o/1/) kirish orqali sandboxni ko'rishingiz va `angular.js` faylining 13275-qatorida breakpoint o‘rnatishni amalda sinab ko‘rishingiz mumkin. fnString o'zgaruvchisi qayta yozilgan kodingizni o'z ichiga oladi, shuning uchun siz AngularJS uni qanday o'zgartirishini ko'rishingiz mumkin.

## <mark style="color:yellow;">AngularJS sandboxni qanday aldash mumkin ?</mark> <a href="#angularjs-sandboxdan-qanday-qilib-qochish-mumkin" id="angularjs-sandboxdan-qanday-qilib-qochish-mumkin"></a>

Eng keng tarqalgan aldash usuli bu o'zgartirilgan global `charAt()` funksiyasidan foydalanish:

```javascript
'a'.constructor.prototype.charAt=[].join
```

Birinchi o'zgartirish kiritilganida AngularJS bu modifikatsiyaga to'sqinlik qilmadi. Hujum funksiyani `[].join` usuli yordamida qayta yozish orqali ishlaydi, bu esa `charAt()` funksiyasini ma’lum bir belgi emas, balki unga yuborilgan barcha belgilarni qaytarishiga olib keladi. AngularJS’dagi `isIdent()` funksiyasi mantig‘idan kelib chiqib, u bitta belgi deb o‘ylaganini bir nechta belgilar bilan solishtiradi. Bitta belgi har doim bir nechta belgilardan kichik bo'lganligi sababli, `isIdent()` funksiyasi quyidagi misolda ko'rsatilganidek, har doim true ni qaytaradi:

```javascript
isIdent= function(ch) {
return ('a' <= ch && ch <= 'z' ||
'A' <= ch && ch <= 'Z' ||
'_' === ch || ch === '$');
}
isIdent('x9=9a9l9e9r9t9(919)')
```

`isIdent()`funksiyasini alganda, siz zararli JavaScript kodni kiritishingiz mumkin. Misol uchun, `$eval('x=alert(1)')` kabi paylodga ruxsat beriladi, chunki AngularJS har bir belgini identifikator sifatida ko'radi. Esda tutingki, biz AngularJS ning `$eval()` funksiyasidan foydalanishimiz kerak, chunki `charAt()` funksiyasini qayta yozish faqat sinov muhitidagi kod bajarilgandan keyin kuchga kiradi. Keyinchalik, bu usul sinov muhitini chetlab o'tadi va JavaScript kodini o'zboshimchalik bilan bajarishga imkon beradi.

## <mark style="color:yellow;">AngularJS sandboxni aldashning murakkab usullaridan foydalanish</mark> <a href="#angularjs-sandboxdan-qochishning-murakkab-usullarini-ishga-solish" id="angularjs-sandboxdan-qochishning-murakkab-usullarini-ishga-solish"></a>

Shunday qilib, siz oddiy sandboxni aldash qanday ishlashini bilib oldingiz, lekin qaysi belgilarga ruxsat berishni cheklovchi saytlarga duch kelishingiz mumkinligini bilmaysiz. Masalan, sayt qo'shtirnoq yoki birtirnoqdan foydalanishga to'sqinlik qilishi mumkin. Bunday vaziyatda belgilaringizni kitish uchun `String.fromCharCode()` kabi funksiyalardan foydalanishingiz kerak. AngularJS kod ichida `String` konstruktoridan foydalanishni taqiqlasa-da, buning o'rniga satrning konstruktor xususiyatidan foydalanishingiz mumkin. Bu shubhasiz, stringni talab qiladi, shuning uchun bunday hujumni amalga oshirish uchun siz birtirnoq yoki qo'shtirnoqdan foydalanmasdan string yaratish usulini topishingiz kerak bo'ladi.

Standart sandboxni aldashda siz JavaScript payloadingizni bajartirish uchun `$eval()` dan foydalanasiz, ammo quyidagi laboratoriyada `$eval()` funksiyasi aniqlanmaydi. Yaxshiyamki, biz o'rniga `orderBy` filtridan foydalanishimiz mumkin. OrderBy filtrining standart sintaksisi quyidagicha:

```javascript
[123]|orderBy:'biror string'
```

E'tibor bering, | operatori JavaScriptdagidan boshqa ma'noga ega. Javasctiptda bu  `OR` operatsiyasi, lekin AngularJS da u filtr vazifasini bildiradi. Yuqoridagi kodda biz chapdagi \[123] massivni o'ngdagi `orderBy` filtriga jo'natamiz. Ikki nuqta stringni filtrga yuborish uchun argument hisoblanadi. `OrderBy` filtri odatda obyektni saralash uchun ishlatiladi, lekin u ifodani ham qabul qiladi, ya'ni biz undan payloadni ishlatish uchun foydalanishimiz mumkin.

Endi keyingi laboratoriya bilan shug'ullanish uchun barcha kerakli vositalarga ega bo'lishingiz kerak.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Stringlarsiz AngularJS sandboxini aldash uchun reflected XSS ≫](https://portswigger.net/web-security/cross-site-scripting/contexts/client-side-template-injection/lab-angular-sandbox-escape-without-strings)
{% endhint %}

## <mark style="color:yellow;">AngularJS CSP ni aylanib o'tish qanday ishlaydi?</mark> <a href="#angularjs-csp-ni-aylanib-otish-qanday-ishlaydi" id="angularjs-csp-ni-aylanib-otish-qanday-ishlaydi"></a>

Content Security Policy (CSP) ni aylanib o'tish xuddi sandbox dan aldash kabi ishlaydi, biroq odatda bir qancha HTML ineksiyalarini o'z ichiga oladi. Agar CSP rejimi Angular JS da yoqiq bo'lsa, u shablon expressionlarini tahlil qiladi. Bu degani yuqorida keltirgan sandboxni aldash rejamiz endi bu yerda foyda bermaydi.

Maxsus siyosatlarga tayangan holda CSP JavaScript eventlarni bloklaydi. Biroq, AngularJS o'zining o'rniga ishlatilishi mumkin bo'lgan eventlarni belgilaydi. Event ichida AngularJS maxsus `$event` obyektini belgilaydi, u shunchaku brauzer event obyektiga murajaat qiladi. Ushbu obyektdan CSPni aylanib o'tish uchun foydalanishingiz mumkin. Chrome'da `$event/event` obyektida `path` deb nomlangan maxsus xususiyat mavjud. Bu xususiyat eventning bajarilishiga olib keladigan obyektlar to'plamini o'z ichiga oladi. Oxirgi xususiyat har doim `window` obyekti bo'lib, biz undan sandboxdni aldash uchun foydalanishimiz mumkin. Ushbu massivni `orderBy` filtriga o‘tkazish orqali biz massivni sanab o‘tamiz va `alert()` kabi global funksiyani bajarish uchun oxirgi elementdan (`window`) foydalanishimiz mumkin. Quyidagi kod buni ko'rsatadi:

```html
<input autofocus ng-focus="$event.path|orderBy:'[].constructor.from([1],alert)'">
```

Obyektni massivga aylantirish va ushbu massivning har bir elementida berilgan funksiyani (ikkinchi argumentda ko'rsatilgan) chaqirish imkonini beruvchi `from()` funksiyasidan foydalanilganiga e'tibor bering. Bunday holda, biz `alert()` funksiyasini chaqiramiz. Biz funksiyani to'g'ridan-to'g'ri chaqira olmaymiz, chunki AngularJS sandbox kodni tahlil qiladi va funksiyani chaqirish uchun `window` obyektidan foydalanilayotganligini aniqlaydi. Buning o'rniga `from()` funksiyasidan foydalanish `window` obyektini sandboxdan samarali ravishda yashiradi, bu bizga zararli kodni kiritish imkonini beradi.

## <mark style="color:yellow;">CSP ni AngularJS sandbox dan qochish orqali aylanib o'tish</mark> <a href="#csp-ni-angularjs-sandbox-dan-qochish-orqali-aylanib-otish" id="csp-ni-angularjs-sandbox-dan-qochish-orqali-aylanib-otish"></a>

Ushbu keyingi laboratoriya uzunlik cheklovidan foydalanadi, shuning uchun yuqoridagi vektor ishlamaydi. Laboratoriyadan foydalanish uchun siz AngularJS sandboxidan `window` obyektini yashirishning turli usullarini o'ylab ko'rishingiz kerak. Buning usullaridan biri `array.map()` funksiyasidan quyidagi tarzda foydalanishdir:

```
[1].map(alert)
```

`map()` funksiyani argument sifatida qabul qiladi va uni massivdagi har bir element uchun ishlatadi. Bu sandboxni aylanib o'tadi, chunki `alert()` funksiyasiga murajaat `window` ga murojaat qilinmasdan foydalanilmoqda. Laboratoriyani bajarish uchun AngularJS `window`ni aniqlashni ishga tushirmasdan `alert()`ni bajarishning turli usullarini sinab ko'ring.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [AngularJS sandboxni aldash va SCP uchun Reflected XSS ≫](https://portswigger.net/web-security/cross-site-scripting/contexts/client-side-template-injection/lab-angular-sandbox-escape-and-csp)
{% endhint %}

## <mark style="color:yellow;">AngularJS ineksiyasini qanday qilib oldini olish mumkin ?</mark> <a href="#angularjs-inektsiya-ni-qanday-qilib-oldini-olish-mumkin" id="angularjs-inektsiya-ni-qanday-qilib-oldini-olish-mumkin"></a>

AngularJS ineksiya hujumlarining oldini olish uchun shablon yoki expressionlarni yaratishda ishonchsiz foydalanuvchi kiritishidan foydalanmang.
