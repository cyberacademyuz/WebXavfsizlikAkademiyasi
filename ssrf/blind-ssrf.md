# Blind SSRF

Bu bo'limda Blind SSRF nima ekanligi haqida, Blind SSRF xujumlaridan bir qancha misollar keltiramiz va uni qanday topish va exploit qilishni o'rganamiz.

## <mark style="color:yellow;">Blind SSRF nima ?</mark> <a href="#blind-ssrf-nima" id="blind-ssrf-nima"></a>

Blind SSRF zaifligi, web saytning backend qismi HTTP so'rovlarni URL ga jo'natib, javobni saytning frontend qismiga qaytarmaganda ishlaydi.

## <mark style="color:yellow;">Blind SSRF qanday ta'sirga ega ?</mark> <a href="#blind-ssrf-tasiri-qanday" id="blind-ssrf-tasiri-qanday"></a>

Blind SSRF zaifliklarining ta'siri ko'pincha ularning o'ziga hos tabiati tufayli biz yuqorida o'rgangan SSRF zaifliklariga qaraganda past bo'ladi. Ba'zi hollarda ulardan masofaviy kod bajarilishiga erishish uchun foydalanish mumkin bo'lsa-da, ularni masofadan turib back-end tizimlardagi ma'lumotlarni olish uchun ishlatib bo'lmaydi.

## <mark style="color:yellow;">Blind SSRF zaifliklarini topish va exploit qilish</mark> <a href="#blind-ssrf-zaifliklarini-topish-va-exploit-qilish" id="blind-ssrf-zaifliklarini-topish-va-exploit-qilish"></a>

Blind SSRF zaifliklarini  topishning eng ishonchli yo'li bu out-of-band ([<mark style="color:yellow;">OAST</mark>](https://portswigger.net/burp/application-security-testing/oast)) texnikasini ishlatish. Bu siz boshqaradigan tashqi tizimga HTTP so'rovini yuborishga urinish va ushbu tizim bilan tarmoqning o'zaro ta'sirini kuzatishni o'z ichiga oladi.

out-of-band texnikasini qo'llashning eng oson yo'li bu [<mark style="color:yellow;">Burp Collaborator</mark>](https://hackthebrain.gitbook.io/burpsuite-uchun-qollanma/burp-toollari-haqida-organish-tez-kunda/collabrator-client-pro) <mark style="color:yellow;"></mark> ni ishlatish. Siz Burp Collaborator, domen yasashda, payloadlarni kuzatishda va ushbu tizim bilan tarmoqning o'zaro ta'sirini kuzatish imkonini beradi. Agar saytdan keluvchi HTTP so'rovi kuzatilsa, u SSRF zaifligi mavjud hisoblanadi.

{% hint style="info" %}
**Eslatma:**

SSRF zaifliklarda HTTP dan ko'ra ko'pincha DNS ni tekshirish foydali hisoblanadi. Bu odatda, websayt, domenga HTTP soʻrov yuborishga uringani uchun sodir boʻladi, bu esa dastlabki DNS qidiruviga sabab boʻlgan, lekin haqiqiy request network-level filtrlash orqali bloklangan. Infratuzilma uchun chiquvchi DNS-trafikga ruxsat berish nisbatan keng tarqalgan, chunki bu juda ko'p vazifalar uchun kerak, ammo kutilmagan manzillarga HTTP ulanishlarni blokirovka qiladi.
{% endhint %}

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark>[ Out-of-bandni aniqlash orqali SSRF ≫](https://portswigger.net/web-security/ssrf/blind/lab-out-of-band-detection)
{% endhint %}

Out-of-band HTTP requestlarni ishga tushirishi mumkin bo'lgan Blind SSRF zaifligini aniqlashning o'zi foydalanish imkoniyatiga yo'l bermaydi. Back-end requestidan responseni ko'ra olmaganingiz uchun, xatti-harakatlardan sayt serveri kirishi mumkin bo'lgan tizimlardagi tarkibni o'rganish uchun ishlatib bo'lmaydi. Biroq, serverning o'zida yoki boshqa backend tizimlardagi boshqa turdagi zaifliklarni tekshirish uchun undan hali ham foydalanish mumkin. Siz ko'p qo'llaniladiagn zaifliklarni aniqlash uchun mo'ljallangan payloadlarni jo'natib, ichki IP manzil maydonini ko'rishingiz mumkin. Agar ushbu payloadlarda blind out-of-band texnikalar qo'llanilsa, siz tuzatilmagan ichki serverda muhim zaiflikni aniqlashingiz mumkin.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Shellshock explotaion orqali Blind SSRF ≫](https://portswigger.net/web-security/ssrf/blind/lab-shellshock-exploitation)
{% endhint %}

Blind SSRF zaifliklaridan foydalanishning yana bir yo'li bu web saytni haker nazorati ostidagi tizimga ulanishga undash va ulanishni amalga oshiruvchi HTTP mijoziga zararli responselarni qaytarish. Agar siz serverning HTTP-ni amalga oshirishda mijoz tomonidagi jiddiy zaiflikdan foydalana olsangiz, web sayt infratuzilmasida masofadan buyruq bajartira olishingiz mumkin.
