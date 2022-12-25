# Response queue poisoning

**Response Queue poisoning** Request Smuggling hujumining kuchli shakli bo'lib, front-end serverini, back-endning no'to'g'ri so'rovlardan kelgan javoblarini xaritalashiga olib keladi. Bu shuni anglatadiki, bir xil frontend/backend ulanishdagi barcha foydalanuvchilar boshqa odam uchun mo'ljallangan responselarga doim hizmat qiladi.&#x20;

Bunga Request Smuggling xujumini amalga oshirib erishish mumkin, shunday qilib Frontend bitta javob kutganida backend ikkita javob qaytarishi mumkin.

## <mark style="color:yellow;">Response queue poisoning ta'siri qanday ?</mark> <a href="#response-queue-poisoning-tasiri-qanday" id="response-queue-poisoning-tasiri-qanday"></a>

Response queue poisoning ta'siri halokatli hisoblanadi. Queue(navbat) zararlangandan so'ng, tajovuzkor o'zboshimchalik bilan kuzatuv so'rovlarini berish orqali boshqa foydalanuvchilarning responselarini qo'lga kiritishi mumkin.  Bu responselarda esa maxfiy personal ma'lumotlar, session tokenlarga o'xshash ma'lumotlar bo'lishi mumkin va Haker shular orqali foydalanuvchining akkauntiga to'la qonli kirish huquqini qo'lga kiritishi mumkin.

Response queue poisoning, trafigi bir xil TCP connection orqali backendga yuborilayotgan har qanday boshqa foydalanuvchilar uchun saytni buzuvchi ikkinchi darajali zararlar kelib chiqishiga sababchi bo'ladi. Saytdan odatdagidek foydalanayotganda, foydalanuvchilar serverdan randomga o'xshash responselarni qabul qiladi, bu esa ko'p funksiyalarning to'g'ri ishlashiga o'sqinlik qiladi.

## <mark style="color:yellow;">Response queue poisoning xujumi qanday qilinadi ?</mark> <a href="#response-queue-poisoning-xujumini-qanday-qurish-mumkin" id="response-queue-poisoning-xujumini-qanday-qurish-mumkin"></a>

Response queue poisoning xujumnii muvaffaqiyatli amalga oshishi uchun quyidagi kriterialarni bilish kerak:

* Frontend va Backend serveri o'rtasidagi TCP connection ko'p sonli request/response tsikli uchun qayta ishlatiladi.
* Xaker back-end serverdan o'zining turli responseni qabul qiladigan standalone requestini to'liq muvaffaqiyatli olib o'ta oladi.
* Xujum ikkala serverning ham TCP connectionini yopilishiga olib kelmaydi. Serverlar odatda invalid requestni olganlarida kiruvchi ulanishlarni yopadilar, chunki ular so'rov qayerda tugashini aniqlay olmaydilar.

## <mark style="color:yellow;">Request Smuggling oqibatlarini tushunish</mark> <a href="#request-smuggling-oqibatlarini-tushunish" id="request-smuggling-oqibatlarini-tushunish"></a>

Request Smuggling xujumlari odatda server ulanishidagi keyingi request boshlanishida prefiks sifatida qo'shiluvchi so'rov qismini yashirin olib o'tishni o'z ichiga oladi, Shuni ta'kidlash kerakki, yashirin olib o'tilgan requestning tarkibi dastlabki hujumdan keyin connection bilan nima sodir bo'lishiga ta'sir qiladi.

Agar siz request tarkibida ba'zi headerlarni ham yashirib olib oʻtsangiz, keyin koʻp oʻtmay connectionga boshqa request yuboriladi deb faraz qilsak, backend server ikkita toʻliq requestni koʻradi.

<figure><img src="../../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

Agar siz headerlar o'rniga to'liq tarkibni oʻz ichiga olgan requestni yashirib olib o'tsangiz, ulanishdagi keyingi request, smuggled soʻrovining asosiy qismiga qoʻshiladi. Bu ko'pincha `Content-length` ga asoslangan so'ngi requestni qisqartirishning nojo'ya ta'siriga ega bo'ladi. Natijada, backend qism uchta so'rovni ko'radi, bunda uchinchi "request" qolgan baytlar qatoridir:

**Front-end (CL)**

```http
POST / HTTP/1.1
Host: vulnerable-website.com
Content-Type: x-www-form-urlencoded
Content-Length: 120
Transfer-Encoding: chunked

0

POST /example HTTP/1.1
Host: vulnerable-website.com
Content-Type: x-www-form-urlencoded
Content-Length: 25

x=GET / HTTP/1.1
Host: vulnerable-website.com
```

**Back-end (TE)**

```http
POST / HTTP/1.1
Host: vulnerable-website.com
Content-Type: x-www-form-urlencoded
Content-Length: 120
Transfer-Encoding: chunked

0

POST /example HTTP/1.1
Host: vulnerable-website.com
Content-Type: x-www-form-urlencoded
Content-Length: 25

x=GET / HTTP/1.1
Host: vulnerable-website.com
```

Ushbu qolgan baytlar valid requestni tashkil qilmagani uchun, bu odatda xatoga olib keladi va server ulanishni yopadi.

## <mark style="color:yellow;">To'liq so'rovni yashirin olib o'tish</mark> <a href="#toliq-sorovni-kontrabanda-qilish" id="toliq-sorovni-kontrabanda-qilish"></a>

Biroz ehtiyotkorlik bilan, shunchaki prefiks o'rniga to'liq so'rovni yashirin olib o'tishingiz mumkin. Aynan ikkita so'rovni bittada yuborsangiz, ulanish bo'yicha keyingi so'rovlar o'zgarishsiz qoladi:

**Front-end (CL)**

```
POST / HTTP/1.1\r\n
Host: vulnerable-website.com\r\n
Content-Type: x-www-form-urlencoded\r\n
Content-Length: 61\r\n
Transfer-Encoding: chunked\r\n
\r\n
0\r\n
\r\n
GET /anything HTTP/1.1\r\n
Host: vulnerable-website.com\r\n
\r\n
GET / HTTP/1.1\r\n
Host: vulnerable-website.com\r\n

```

**Back-end (TE)**

```
POST / HTTP/1.1\r\n
Host: vulnerable-website.com\r\n
Content-Type: x-www-form-urlencoded\r\n
Content-Length: 61\r\n
Transfer-Encoding: chunked\r\n
\r\n
0\r\n
\r\n
GET /anything HTTP/1.1\r\n
Host: vulnerable-website.com\r\n
\r\n
GET / HTTP/1.1\r\n
Host: vulnerable-website.com\r\n

```

E'tibor bering, hech qanday invalid requestlar backend tarafga yuborilmaydi, shuning uchun hujumdan keyin ulanish ochiq qolishi mumkin.

## <mark style="color:yellow;">Response queue poisoning ni desinxronlash</mark> <a href="#response-queue-poisoning-ni-desinxronlash" id="response-queue-poisoning-ni-desinxronlash"></a>

Toʻliq soʻrovni yashirin olib o'tishingizda, front-end server hali ham faqat bitta soʻrovni yuborgan deb oʻylaydi. Narigi tomonda, backend ikkita alohida so'rovni ko'radi va shunga mos ravishda ikkita javob yuboradi:

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

Frontend boshlang'ich "wrapper requestiga birinchi javobni to'g'ri ko'rsatadi va uni browserga yuboradi. Responseni kutayotgan boshqa requestlar yo'qligi sababli, tasodifiy ikkinchi response Frontend va Backend o'rtasidagi connectionda navbatda turadi.

Qachonki Frontend boshqa requestni qabul qilsa, Backend ga oddiy holatda yo'naltiriladi. Biroq, Responseni yuborishda u navbat turgan birinchi requestni yuboradi, ya'ni yashirin olib o'tilayotgan requestga qolgan requestni yuboradi.

Keyin Backendning to'g'ri response-i munosib requestsiz qoldiriladi. Ushbu tsikl har safar yangi so'rov xuddi shu ulanish orqali Backendga yo'naltirilganda takrorlanadi.

## <mark style="color:yellow;">Boshqa foydalanuvchilarning responselarini o'g'rilash</mark> <a href="#boshqa-foydalanuvchilarning-javoblarini-ogrilash" id="boshqa-foydalanuvchilarning-javoblarini-ogrilash"></a>

Agar Response queue poisoning xujumi o'xshasa unda Haker boshqa foydalanuvchilarning responselarini o'g'rilashi mumkin.

<figure><img src="../../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

Hackerlar qaysi responselarni olishlarini nazorat qila olmaydilar chunki ularga har doim navbatda turgan keyingi response yuboriladi. Biroq, Burp Intruder kabi vositalardan foydalangan holda, Haker requestni qayta yuborish jarayonini osongina avtomatlashtirishi mumkin. Shunday qilib, ular turli foydalanuvchilar uchun mo'ljallangan responselar assortimentini tezda qo'lga kiritishlari mumkin, hech bo'lmaganda ba'zilarida foydali ma'lumotlar bo'lishi mumkin.

Front-end/back-end ulanish ochiq qolar ekan, hacker bu kabi responselarni o'g'irlashda davom etishi mumkin. Ulanish yopilganda serverdan serverga farq qiladi, lekin umumiy standart bo'yicha ulanish 100 ta so'rovni ko'rib chiqqandan keyin to'xtatiladi. Joriy ulanish yopilgandan keyin yangi ulanishni qayta tiklash ham ahamiyatsiz.

{% hint style="success" %}
**Maslahat:**

Oʻgʻirlangan responselarni oʻz requestlaringizga berilgan javoblardan farqlashni osonlashtirish uchun siz yuborgan har ikkala soʻrovda mavjud boʻlmagan yoʻlni ishlatib koʻring. Shunday qilib, sizning so'rovlaringiz doimiy ravishda masalan 404 javobini olishi kerak.
{% endhint %}

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [H2.TE request smuggling orqali Response queue poisoning ≫](https://portswigger.net/web-security/request-smuggling/advanced/response-queue-poisoning/lab-request-smuggling-h2-response-queue-poisoning-via-te-request-smuggling)
{% endhint %}

{% hint style="info" %}
**Eslatma:**

Ushbu hujum oddiy HTTP/1 request smuggling orqali ham, HTTP/2 downgrading orqali ham bo'lishi mumkin.
{% endhint %}
