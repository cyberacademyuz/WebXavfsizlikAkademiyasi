# Ajax request-header manipulation

Biz bugungi bo'limda DOM ga asoslangan Ajax request-headerni boshqarish nima ekanligi haqida, uning ta'siri qandayligi va uni qanday oldini olish mumkinligi haqida gaplashamiz.

## <mark style="color:yellow;">DOMga asoslangan Ajax request-headerni boshqarish nima ?</mark> <a href="#dom-ga-asoslangan-ajax-request-header-manipulation-nima" id="dom-ga-asoslangan-ajax-request-header-manipulation-nima"></a>

Ajax asosida so'rovlarni jo'natish web saytni asinxron ishlashiga yordam beradi va bu butun saytni qayta yangilamasdan kontentni dinamik o'zgarishini ta'minlaydi. Agar haker tomonidan yozilgan skript Ajax request-headeriga ta'sir o'tkaza olsa DOM ga asoslangan Ajax request-headerni boshqarish zaifligi paydo bo'ladi, ko'pincha ushbu muammolar `XmlHttpRequest` obyekti asosida yuzaga keladi. Hacker ushbu zaiflikdan URL manzilini yaratish uchun foydalanishi mumkin, agar unga biror foydalanuvchi kirsa, keyingi Ajax so'rovida o'zboshimchalik bilan header o'rnatadi. Ushbu zaiflik boshqa zaifliklarni hosil bo'lishiga zanjir vazifasini o'tashi mumkin, shu orqali ushbu zaiflikning jiddiylik darajasi ortib boradi.

## <mark style="color:yellow;">DOMga asoslangan Ajax request-headerni boshqarish zaifligining ta'siri qanday ?</mark> <a href="#dom-ga-asoslangan-ajax-request-header-manipulation-tasiri-qanday" id="dom-ga-asoslangan-ajax-request-header-manipulation-tasiri-qanday"></a>

Zaiflikning  ta'siri Ajax so'rovini server tomonida qayta ishlashda maxsus HTTP headerlarning vazifasiga bog'liq. Agar header Ajax so'rovidan yuzaga keladigan xatti-harakatlarni boshqarish uchun ishlatilsa, Haker headerni boshqarish orqali foydalanuvchini kutilmagan harakatlarni qilishiga majbur qilishi mumkin. Yana ushbu zaiflikning tasiri haker headerlar bilan nimalarni ineksiya qila olishiga ham bog'liq.

## <mark style="color:yellow;">DOMga asoslangan Ajax request-headerni boshqarishni keltirib chiqaradigan metodlar qaysilar ?</mark> <a href="#dom-ga-asoslangan-ajax-request-header-manipulation-ni-keltirib-chiqaradigan-sink-lar-qaysilar" id="dom-ga-asoslangan-ajax-request-header-manipulation-ni-keltirib-chiqaradigan-sink-lar-qaysilar"></a>

Quyida keltirilgan metodlar DOMga asoslangan Ajax request-header manipulation ni keltirib chiqaruvchilar hisoblanadi:

```
XMLHttpRequest.setRequestHeader()
XMLHttpRequest.open()
XMLHttpRequest.send()
jQuery.globalEval()
$.globalEval()
```

## <mark style="color:yellow;">Qanday qilib DOMga asoslangan Ajax request-headerni boshqarish zaifliklini oldini olish mumkin ?</mark> <a href="#qanday-qilib-dom-ga-asoslangan-ajax-request-header-manipulation-zaifliklarini-oldini-olish-mumkin" id="qanday-qilib-dom-ga-asoslangan-ajax-request-header-manipulation-zaifliklarini-oldini-olish-mumkin"></a>

<mark style="color:yellow;"></mark>[<mark style="color:yellow;">DOM-ga asoslangan zaifliklar</mark>](broken-reference) mavzusida aytilgan umumiy choralarga qo'shimcha ravishda, har qanday ishonchsiz manbadan olingan ma'lumotlarni o'z ichiga olgan Ajax-so'rovlarni yuborishdan saqlanish kerak.
