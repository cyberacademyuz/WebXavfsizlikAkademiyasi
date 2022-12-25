# Ma'lumotlar bazasini tekshirish

## <mark style="color:yellow;">Ma'lumotlar bazasini SQL ineksiya hujumlariga tekshirish</mark> <a href="#malumotlar-bazasi-turi-va-versiya-sorovlari" id="malumotlar-bazasi-turi-va-versiya-sorovlari"></a>

SQL ineksiya zaifliklaridan foydalanganda ko'pincha ma'lumotlar bazasi (keyingi o'rinlarda <mark style="color:yellow;">**database**</mark> deb yuritiladi ) haqida ham ma'lumot to'plash kerak bo'ladi. Bunga database dasturiy ta'minotining turi va versiyasi, shuningdek, qanday jadvallar va ustunlar mavjudligi nuqtai nazaridan database tarkibi kiradi.

## <mark style="color:yellow;">Database turi va versiya so'rovlari</mark> <a href="#malumotlar-bazasi-turi-va-versiya-sorovlari" id="malumotlar-bazasi-turi-va-versiya-sorovlari"></a>

Turli databaselar versiyasini ko'rsatuvchi so'rovlarning har xil usullarini taklif qiladi. Siz ko'p kerak bo'ladigan so'rovlarni topish uchun turli xil so'rovlarni sinab ko'rishingiz kerak, shuning uchun siz databasen dasturiy ta'minotining turini va versiyasini aniqlashingiz kerak.

Ba'zi mashhur database turlari va versiyasini aniqlash uchun so'rovlar quyidagilar:

|                  |                           |
| ---------------- | ------------------------- |
| Database nomi    | So'rov                    |
| Microsoft, MySQL | `SELECT @@version`        |
| Oracle           | `SELECT * FROM v$version` |
| PostgreSQL       | `SELECT version()`        |

Masalan, siz quyidagi payload bilan UNION hujumidan foydalanishingiz mumkin:

```sql
' UNION SELECT @@version--
```

Bu database **Microsoft SQL Server** va foydalanilayotgan versiyasini ko'rsatishi mumkin:

`Microsoft SQL Server 2016 (SP2) (KB4052908) - 13.0.5026.0 (X64) Mar 18 2018 09:11:49 Copyright (c) Microsoft Corporation Standard Edition (64-bit) on Windows Server 2016 Standard 10.0 <X64> (Build 14393: ) (Hypervisor)`

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [Oracle-da ma'lumotlar baza turini va versiyasini so'rash uchun SQL ineksiya hujumi ≫](https://portswigger.net/web-security/sql-injection/examining-the-database/lab-querying-database-version-oracle)
{% endhint %}

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [MySQL va Microsoft-da ma'lumotlar baza turi va versiyasini so'rash uchun SQL ineksiya hujumi ≫](https://portswigger.net/web-security/sql-injection/examining-the-database/lab-querying-database-version-mysql-microsoft)
{% endhint %}

## <mark style="color:yellow;">Database tarkibini ro'yxatlash</mark> <a href="#malumotlar-bazasi-tarkibini-royxatlash" id="malumotlar-bazasi-tarkibini-royxatlash"></a>

Ko'pgina database turlari _(**Oracle**dan tashqari_) database haqida ma'lumot beruvchi **information schema** deb ataladigan to'plamga ega.

Databasedagi jadvallarni ro'yxatga olish uchun ushbu`information_schema.tables` so'rovini amalga oshirishingiz mumkin:

```sql
SELECT * FROM information_schema.tables
```

Bu quyidagicha ma'lumotlarni ko'rsatadi:

| \`TABLE\_CATALOG | TABLE\_SCHEMA | TABLE\_NAME | TABLE\_TYPE |
| ---------------- | ------------- | ----------- | ----------- |
| MyDatabase       | dbo           | Products    | BASE TABLE  |
| MyDatabase       | dbo           | Uses        | BASE TABLE  |
| MyDatabase       | dbo           | Feedback    | BASE TABLE  |

Bu `Products`, `Users` va `Feedback` deb nomlangan 3 ta jadval mavjudligini ko'rsatadi.

Keyin alohida jadvallardagi ustunlarni roʻyxatga olish uchun `information_schema.columns` soʻrovini bajarishingiz mumkin:&#x20;

```sql
SELECT * FROM information_schema.columns WHERE table_name = 'Users'
```

**Bu quyidagicha ma'lumotlarni qaytaradi:**

| \`TABLE\_CATALOG | TABLE\_SCHEMA | TABLE\_NAME | COLUMN\_NAME | DATA\_TYPE |
| ---------------- | ------------- | ----------- | ------------ | ---------- |
| MyDatabase       | dbo           | Users       | UserId       | int        |
| MyDatabase       | dbo           | Users       | Username     | varchar    |
| MyDatabase       | dbo           | Users       | Password     | varchar    |

Bunda belgilangan jadvaldagi ustunlarni va har bir ustunning ma'lumotlar turini ko'rsatadi.

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark> [SQL Inceksiya hujumi, ORALE bo'lmagan ma'lumotlar bazalarida ma'lumotlar bazasi tarkib ro'yxati uchun SQLi ≫](https://portswigger.net/web-security/sql-injection/examining-the-database/lab-listing-database-contents-non-oracle)
{% endhint %}

## <mark style="color:yellow;">Oracledagi axborot sxemasiga teng</mark> <a href="#oracledagi-axborot-sxemasiga-teng" id="oracledagi-axborot-sxemasiga-teng"></a>

Oracle-da siz bir xil ma'lumotni biroz boshqacha so'rovlar bilan olishingiz mumkin.

Siz `all_tables` so'rovi orqali jadvallarni ro'yxatga olishingiz mumkin:

`SELECT * FROM all_tables` va yana `all_tab_columns` so'rovi orqali ham ustunlarni ro'yxatga olishingiz mumkin:

```sql
SELECT * FROM all_tab_columns WHERE table_name = 'USERS'
```

{% hint style="warning" %}
<mark style="color:yellow;">**Lab:**</mark>[ Oracleda ma'lumotlar bazasi tarkib ro'yhati uchu SQL ineksiya hujumi ≫](https://portswigger.net/web-security/sql-injection/examining-the-database/lab-listing-database-contents-oracle)
{% endhint %}
