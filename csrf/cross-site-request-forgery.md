# Cross site request forgery

inBiz ushbu bo'limda saytlararo so'rovlarni qalbakilashtirish nima ekanini tushuntirib o'tamiz, bir qancha CSRF ga yaqin bo'lgan zaifliklarni ham tasvirlab beramiz va CSRF xujumlaridan qanday qutulish mumkinligini tushuntirib beramiz.

## <mark style="color:yellow;">CSRF nima ?</mark> <a href="#csrf-nima" id="csrf-nima"></a>

**Saytlararo so'rovlarni qalbakilashtirish** (CSRF deb tanilgan) haker tomonidan, foydalanuvchiga u xohlamagan harakatlarini bajarishga majburlash hisoblanadi. Bu hakerga turli veb-saytlarning bir-biriga xalaqit berishiga yo'l qo'ymaslik uchun yaratilgan **same origin policy**ni qisman chetlab o'tishga imkon beradi.

<figure><img src="../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

## <mark style="color:yellow;">CSRF xujumining ta'siri qanday ?</mark> <a href="#csrf-xujumining-tasiri-qanday" id="csrf-xujumining-tasiri-qanday"></a>

Muvaffaqiyatli CSRF xujumi natijasida Haker foydalanuvchining o'zi istamagan harakatlarini bajarishga majburlay oladi. Misol uchun, hisob raqamdagi **email manzilini o'zgartirish, parollarni o'zgartirish**, yoki **pul mablag'lari o'tkazmalarini** amalga oshirishga olib keladi. Xujumning xususiyatiga qarab, Haker foydalanuvchining barcha harakatlarini o'z nazorati ostiga olishi mumkin. Agarda xujumga uchragan foydalanuvchi dasturda qandaydir huquqga ega bo'lsa, unda Haker foydalanuvchi bilan birga saytning funksionalligi va ma'lumotlariga ega chiqishi mumkin.

## <mark style="color:yellow;">CSRF qanday ishlaydi ?</mark> <a href="#csrf-qanday-ishlaydi" id="csrf-qanday-ishlaydi"></a>

CSRF xujumini amalga oshirish uchun saytda 3 ta asosiy jihat bo'lishi lozim:

* **Tegishli harakat.** Saytda foydalanuvchi tomonidan qandaydir huquq bo'lishi, masalan boshqa foydalanuvchilarga ta'sir o'tkazish, parollarni o'zgartirish va hkz.
* **Cookie ga asoslangan foydalanuvchini tanish siyosati.** Bir yoki bir nechta HTTP so'rovlarini yubora oladigan va sayt so'rovlarni amalga oshirgan foydalanuvchini aniqlash uchun faqatgina sessiya cookie-fayllariga tayanadigan.
* **Hech qanday so'rov parametrlarining mavjud emasligi.** Biror bir amalni bajarish uchun yuborilayotgan so'rovlarning parametrlarni yo'qlihi, masalan, haker biror bir foydalanuvchini parolini o'zgartirmoqchi bo'lsa, so'rov jo'natayotgan vaqtda eski **parol**ning so'ralmasligi.

Misol uchun, quyidagi so'rovga e'tibor bering, bu yerda foydalanuvchining **email** pochtasi o'zgartirilmoqda:

```http
POST /email/change HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 30
Cookie: session=yvthwsztyeQkAPzeQ5gHgTvlyxHfsAfE

email=wiener@normal-user.com
```

Bu yerda CSRF xujumini amalga oshirish uchun kerakli bo'lgan holatlar mavjud:

* Ushbu so'rov amalga oshirilsa haker emailni o'zi xohlagandek qilib o'zgartirishi, parolni o'zi uchun qayta tiklashi va foydalanuvchining profiliga ega chiqishi mumkin.
* Website **Cookie** orqali foydalanuvchini tanish siyosatidan foydalanadi. Bu yerda boshqa hech qanaqa foydalanuvchini tanish uchun ishlatiladigan tokenlar yo'q.
* Haker bu yerda bemalol emailni o'zgartirishi mumkin, chunki eski email manzil qanaqaligi so'ralmayapti.

Ushbu holatdan kelib chiqib, haker HTML asosida **email**ni manzilini o'zgartirishga qaratilgan web sahifa yaratishi mumkin:

```html
<html>
    <body>
        <form action="https://vulnerable-website.com/email/change" method="POST">
            <input type="hidden" name="email" value="pwned@evil-user.net" />
        </form>
        <script>
            document.forms[0].submit();
        </script>
    </body>
</html>
```

Agar foydalanuvchi bu web sahifaga kirsa, ushbu hodisalar sodir bo'ladi.

* Sahifa **zaif websaytga** ushbu so'rovni jo'natadi
* Agar foydalanuvchi zaif websaytda **ro'yhatdan o'tgan bo'lsa** va **Cookie** fayllarini **tozalamagan** bo'lsa ushbu so'rov muvaffaqiyatli amalga oshishi mumkin.
* Zaif web sayt ushbu so'rovni odatdagi so'rov deb hisoblaydi va emailni o'zgartiradi

{% hint style="info" %}
**Eslatma:**

Biz hozir faqat **Cookiega asoslangan foydalanuvchini tanish siyosati** qo'llanilgan saytlar misolini keltirdik, undan tashqari **HTTP Basic authentication** va **certificate-based authentication** orqali ham **CSRF** xujumlarini amalga oshirish mumkin.
{% endhint %}

## <mark style="color:yellow;">CSRF xujumni qanday amalga oshirish mumkin ?</mark> <a href="#qanday-qilib-csrf-xujumni-amalga-oshirish-mumkin" id="qanday-qilib-csrf-xujumni-amalga-oshirish-mumkin"></a>

Ko'pincha **HTML** orqali qilingan **CSRF** xujumlar beo'xshov holatda bo'ladi, ayniqsa ko'p parametrlar talab etadigan so'rov jo'natish. **CSRF** payloadini yaratish uchun eng qulay usul bu [<mark style="color:yellow;">CSRF PoC generator</mark>](https://hackthebrain.gitbook.io/burpsuite-uchun-qollanma/foydali-funksiyalar-tez-kunda/csrf-poc-generatori) vositasi orqali yaratishdir. Ushbu vosita [<mark style="color:yellow;">Burp Suite Professional</mark>](https://portswigger.net/burp/pro) dagina ishlaydi:

* Siz test qilmoqchi yoki **exploit** qilmoqchi bo'lgan biror bir so'rovni tanlang
* Sichqonchani o'ng tomonini bosib **context menu** dan **Engagement tools / Generate CSRF PoC** ni tanlang
* **Burp Suite** sizga test qilish uchun so'rov tayyorlab beradi (**Cookie**lardan tashqari, chunki Cookielar o'zi qo'shiladi)
* Siz **Generate** **CSRF PoC** ning so'rovlarini sozlashingiz mumkin. Ba'zida noodatiy holatlar mavjud bo'ladi, shunda siz biroz g'alatiroq jihatlarni qo'shishingiz kerak bo'ladi.
* Endi esa ushbu so'rovni oling va HTML sahifa yaratib, uni biror bir **target**ga jo'nating, u kirganini tekshiring va xujumni yakunlang

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Hech qanday himoyaga ega bo'lmagan CSRF zaifligi ≫](https://portswigger.net/web-security/csrf/lab-no-defenses)
{% endhint %}

## <mark style="color:yellow;">CSRF exploitni qanday yuborish mumkin ?</mark> <a href="#qanday-qilib-csrf-xujumini-jonatish-mumkin" id="qanday-qilib-csrf-xujumini-jonatish-mumkin"></a>

CSRFni yuborish mexanizmlari **reflected XSS** bilan o'xshash. Odatda Haker zararli HTML sahifani web saytga yuklaydi va foydalanuvchini qiziqtirgan xolda ushbu xujumni amalga oshiradi. Bu ko'pincha **email xabarlar**, biror bir **saytdagi reklama,** **ijtimoiy tarmoq**dagi linklar orqali yoki biror bir ommaviy web saytga saytning linkini joylash orqali (masalan izoh qoldirayotganda) jo'natilib, foydalanuvchini unga kirishini kutish asnosida amalga oshiriladi.

Shuni aytish kerakki ko'pincha sodda **CSRF** xujumlari bitta **URL** manzilga ega bo'lgan zaif web sayt ga **GET** so'rovini yuborish bilan amalga oshiriladi. Bunday holda Haker o'zi yaratgan web sahifani biror bir tashqi saytga yuklab to'g'ridan to'g'ri xujum uyushtirishi mumkin. Quyida siz o'sha xujumdan misolni ko'rishingiz mumkin:

```html
<img src="https://vulnerable-website.com/email/change?email=pwned@evil-user.net">
```

{% hint style="info" %}
**Ko'proq o'qish:**

[XSS vs CSRF ☰](xss-vs-csrf.md)
{% endhint %}

## <mark style="color:yellow;">CSRF xujumlarni oldini olish</mark> <a href="#csrf-xujumlarini-oldini-olish" id="csrf-xujumlarini-oldini-olish"></a>

**CSRF** xujumlarga qarshi turishning eng mustahkam yo'li har bir so'rovga muvofiq **CSRF token** larni berishdir. Tokenlarda quyida keltirilgan jihatlari bo'lishi lozim:

* Oldindan aytib bo'lmaydigan darajada ko'p tokenlar yaratish, ayniqsa **Session token**larni
* **User session** bilan bog'lash
* Har bir harakatni tekshirib, keyin amalga oshirish

{% hint style="info" %}
Batafsil:\
[CSRF Tokenlar ☰](csrf-tokenlar.md)

[Find CSRF vulnerabilities using Burp Suite's web vulnerability scanner](https://portswigger.net/burp/vulnerability-scanner)
{% endhint %}

**CSRF** ga qarshi qisman samarali boʻlgan **CSRF** tokenlari bilan birgalikda ishlatilishi mumkin boʻlgan qoʻshimcha himoya bu **SameSite** **cookie**.

## <mark style="color:yellow;">Umumiy CSRF zaifliklar</mark> <a href="#umumiy-csrf-zaifliklar" id="umumiy-csrf-zaifliklar"></a>

Juda ko'plab **CSRF** xujumlar [<mark style="color:yellow;">CSRF tokenlar</mark>](csrf-tokenlar.md) noto'g'ri yaratilishi natijasida xosil bo'ladi.

Boyagi zaiflikni endi CSRF token bilan birgalikda qanday ko'rinish olishini quyida ko'rishingiz mumkin:

```http
POST /email/change HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 68
Cookie: session=2yQIDcpia41WrATfjPqvm9tOkDvkMvLm

csrf=WfF1szMUHhiokx9AHFply5L2xAOfjRkE&email=wiener@normal-user.com
```

Bu yerda ko'rib turganingizdek boya biz aytib o'tgan **CSRF** tokenlar bilan sodir bo'ladigan xujum keltirilgan: Endi web sayt Cookie larga tayanmaydi, so'rovda bo'lsa Haker aniqlolmaydigan parametr ham mavjud. Ammo shunday bo'lsa ham ushbu himoya buzilishi mumkin, bu degani sayt hali ham CSRF dan ximoylanmaganini anglatadi.

## <mark style="color:yellow;">CSRF tokenini tasdiqlash request metodiga bog'liq</mark> <a href="#csrf-tokenini-aniqlash-sorov-metodiga-bogliq" id="csrf-tokenini-aniqlash-sorov-metodiga-bogliq"></a>

Ba'zi saytlarda faqatgina `POST` so'rovi yuborilganda CSRF tokenni tekshiradi GET so'rov yuborilsa tekshirilmaydi. Natijada Haker ushbu xujumni amalga oshirshi uchun imkoniyat paydo bo'ladi.

Bu holatda haker saytning CSRF token bilan ximoylangan ximoyasini bemalol aylanib o'tadi:

```http
GET /email/change?email=pwned@evil-user.net HTTP/1.1
Host: vulnerable-website.com
Cookie: session=2yQIDcpia41WrATfjPqvm9tOkDvkMvLm
```

{% hint style="warning" %}
<mark style="color:yellow;">**Lab**</mark>: [Tokenni tasdiqlash requestga bog'liq bo'lganida CSRF ≫](https://portswigger.net/web-security/csrf/lab-token-validation-depends-on-request-method)
{% endhint %}

## <mark style="color:yellow;">CSRF tokenni tasdiqlash uning mavjudligiga bog'liq</mark> <a href="#csrf-tokenni-faqatgina-mavjudligida-tekshirish" id="csrf-tokenni-faqatgina-mavjudligida-tekshirish"></a>

Ba'zi web saytlar **CSRF token** mavjud bo'lsagina tekshiradi agarda **CSRF token** mavjud bo'lmasa unda web sayt tokenni o'tkazib yuboradi.

Shu holatda haker validationni bypass qilish va CSRF hujumini amalga oshirish uchun token tarkibidagi parametrni olib tashlaydi:

```http
POST /email/change HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 25
Cookie: session=2yQIDcpia41WrATfjPqvm9tOkDvkMvLm

email=pwned@evil-user.net
```

{% hint style="warning" %}
<mark style="color:yellow;">**Lab**</mark>: [Tokenni tasdiqlash uning mavjudligiga bog'liq bo'lganida CSRF ≫](https://portswigger.net/web-security/csrf/lab-token-validation-depends-on-token-being-present)
{% endhint %}

## <mark style="color:yellow;">User-sessionga bog'lanmagan CSRF-token</mark> <a href="#agar-csrf-token-user-sessionga-boglanmagan-bolsa" id="agar-csrf-token-user-sessionga-boglanmagan-bolsa"></a>

Ba'zi web saytlar requestlarni qaysi foydalanuvchi yuboraytoganini tekshirmaydi faqatgina **Global token** larga qaraydi.

Ushbu holatda Haker web saytdan ro'yhatdan o'tadi, valid token ni oladi va foydalanuvchiga ushbu CSRF xujumini amalga oshiradi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab**</mark>: [Token user-sessionga bog'lanmaganida CSRF ≫](https://portswigger.net/web-security/csrf/lab-token-not-tied-to-user-session)
{% endhint %}

## <mark style="color:yellow;">Mabodo CSRF token non-session cookiega bog'langan bo'lsa</mark> <a href="#mabodo-csrf-token-non-session-ga-boglangan-bolsa" id="mabodo-csrf-token-non-session-ga-boglangan-bolsa"></a>

Ba'zi web saytlar CSRF token larni cookie larga bog'laydi, ammo session larni bog'lamaydi. Ushbu xolat ko'pincha web sayt ikki xil framework asosida yozilgani sabab sodir bo'ladi bir framework session larga javob bersa ikkinchisi CSRF token larga, va shu sabab bu ikkalasi bir biriga bog'lanmay qoladi:

```http
POST /email/change HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 68
Cookie: session=pSJYSScWKpmC60LpFOAHKixuFuM4uXWF; csrfKey=rZHCnSzEp8dbI6atzagGoSYyqJqTz5dv

csrf=RhV7yQDO0xcq9gLEah2WVbmuFqyOq7tY&email=wiener@normal-user.com
```

Ushbu xolatda **Exploit** qilish qiyin ammo sayt zaif. Agar web sayt Hakerga qurbonning brauzerida cookie larni o'rnatishga imkon beradigan har qanday xatti-harakatni o'z ichiga olsa, u holda hujum qilish mumkin. Haker saytdan ro'yhatdan o'tadi, kerakli **CSRF** va **Session** **tokenlar**ni oladi, cookie larni qurbonning brauzeriga joylashtirish uchun cookie larni sozlash xatti-harakatlaridan foydalaning va CSRF hujumida qurbonga tokenni bering.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab**</mark>: [Token non-session cookiega bo'glangan bo'lganida CSRF ≫](https://portswigger.net/web-security/csrf/lab-token-tied-to-non-session-cookie)
{% endhint %}

{% hint style="info" %}
**Eslatma:**

Cookie larni sozlash har doim ham CSRF bilan bir xil web saytda bo'lishi shart emas. Bir xil DNS manzil ega Web sayt larda Cookie larni sozlash imkoniyati mavjud. Misol uchun, staging.demo.normal-website.com da cookie ni sozlash imkoniyati mavjud, ushbu imkoniyat asnosida ushbu cookie lar secure.normal-website.com ga ham jo'natilishi mumkin
{% endhint %}

## <mark style="color:yellow;">CSRF-token Cookie da duplikat bo'ladi.</mark> <a href="#csrf-token-cookie-da-takrorlanishi" id="csrf-token-cookie-da-takrorlanishi"></a>

Tepada ta'kidlab o'tgan muammoyimiz har doim ham ana shunday ko'rinishda kelmasligi mumkin, balki shunchaki **CSRF token** **Cookie**da ham takrorlanishi ham mumkin. Ushbu holatda web sayt Cookie ni ham CSRF ni ham birga tekshiradi va so'rovni qabul qiladi.

```http
POST /email/change HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 68
Cookie: session=1DQGdzYbOJQzLP7460tfyiv3do7MjyPw; csrf=R8ov2YBfTYmzFyjit8o2hKBuoIjXXVpa

csrf=R8ov2YBfTYmzFyjit8o2hKBuoIjXXVpa&email=wiener@normal-user.com
```

Ushbu holat yuz berganda Haker yana bir bor Cookie orqali xujum uyushtirishi mumkin. Bu yerda endi Haker o'zining tokenlarini olishi shart emas. Haker shunchaki endi Cookie ni topishi (yuqorida keltrilgan misol ko'rinishida) va u orqali CSRF xujumini uyushtirsa yetarli.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab**</mark>: [Token cookieda duplikat bo'lganida CSRF ≫](https://portswigger.net/web-security/csrf/lab-token-duplicated-in-cookie)
{% endhint %}

## <mark style="color:yellow;">Referer-ga asoslangan himoyaga qarshi CSRF xujum</mark> <a href="#referer-based-himoyaga-qarshi-csrf-xujum-uyushtirish" id="referer-based-himoyaga-qarshi-csrf-xujum-uyushtirish"></a>

Ba'zi web saytlarda `HTTP Referer` orqali CSRF xujumlarga qarshi himoyalanishadi. Ko'pincha ushbu hodisa bir xil domen orqali ma'lumot almashinayotganini tasdiqlash uchun **Referer**dan foydalaniladi. Bu yondashuv biroz samarasiz va ko'pincha aylanib o'tish imkonini beradi.

{% hint style="success" %}
**Referer header:**\
**HTTP Referer** header (**HTTP** spetsifikatsiyasida beixtiyor noto'g'ri yozilgan) ixtiyoriy so'rov **header**i bo'lib, unda so'ralayotgan manbaga bog'langan web-sahifaning **URL** manzili mavjud bo'ladi. Odatda, foydalanuvchi **HTTP** so'rovini ishga tushirganda, shu jumladan havolani bosish yoki formani yuborish orqali brauzerlar tomonidan avtomatik ravishda qo'shiladi. Ulanish sahifasiga `Referer` sarlavhasining qiymatini ushlab turish yoki o'zgartirish imkonini beruvchi turli usullar mavjud. Bu ko'pincha maxfiylik sabablari bilan amalga oshiriladi.
{% endhint %}

## <mark style="color:yellow;">Refererni tasdiqlash header mavjudligiga bog'liq</mark>

Ba'zi web saytlarda **Referer header** bor bo'lsagina tekshiriladi, agarda mavjud bo'lmasa unda o'tkazib yuboriladi.

Ushbu holatda Haker o'zining **CSRF Exploit** xujumini amalga oshirishga urinib ko'rishi mumkin, chunki qurbonning web sayti `Referer` ni o'tkazib yuboradi. Ushbu holat uchun juda ko'p usullar mavjud ammo eng yaxshi usul bu HTML dagi `meta` teg lar bilan amalga oshiriladigan usul:

```html
<meta name="referrer" content="never">
```

{% hint style="warning" %}
<mark style="color:yellow;">**Lab**</mark>: [Refererni tasdiqlah header mavjudligiga bog'liq bo'lganida CSRF ≫](https://portswigger.net/web-security/csrf/lab-referer-validation-depends-on-header-being-present)
{% endhint %}

## <mark style="color:yellow;">Referer ni tasdiqlashni aylanib o'tish</mark> <a href="#referer-ni-tekshirilishini-chetlab-otish" id="referer-ni-tekshirilishini-chetlab-otish"></a>

Ba'zi web saytlarda Referer ni chetlab o'tish juda ham sodda bo'ladi. Misol uchun Referer faqat boshidagi **HTTP spesifikatsiya**sini tekshirishi mumkin, buni natijasida haker o'zining **sub domain**iga ana shunday domen qo'yishi mumkin:

```url
http://vulnerable-website.com.attacker-website.com/csrf-attack
```

Agar Referer faqat o'zini domenini qo'llasa unda Haker o'zini domenini / dan keyin qo'yib xujumni amalga oshirshi mumkin:

```
http://attacker-website.com/csrf-attack?vulnerable-website.com
```

{% hint style="info" %}
**Eslatma:**

Shuningdek siz ushbu holatni Burp orqali ham aniqlashingiz mumkin, ko'pgina hollarda Browser lar ushbu tekshirishlarni o'tkazish uchun yaroqsiz bo'ladi. Ko'pincha browser lar Referer headerini default holatida so'rovga qo'shib jo'natadi.

Siz ushbu xususiyatni o'chirib jo'natishingiz mumkin, ya'ni `Referrer-Policy: unsafe-url`. Ushbu xolat URL ni so'rovda to'g'ri borishini ta'minlaydi.
{% endhint %}

{% hint style="warning" %}
<mark style="color:yellow;">**Lab**</mark>: [Buzilgan Referer tasdiqlanishi orqali CSRF hujumi ≫](https://portswigger.net/web-security/csrf/lab-referer-validation-broken)
{% endhint %}
