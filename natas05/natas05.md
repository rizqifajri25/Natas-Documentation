
---

### `natas-05/README.md`

```markdown
# Natas 05 → Natas 06

## 🎯 Objective

Mendapatkan password untuk level Natas 06.

## 🔎 Initial Analysis

Halaman memberikan indikasi bahwa pengguna belum dianggap memiliki akses yang sesuai, dengan tulisan yang muncul `Access disallowed. You are not logged in`

Saya kemudian memeriksa cookie yang diberikan oleh server.

## 🧠 Vulnerability / Concept

**Concept:** Cookie manipulation / Client-controlled state

Cookie disimpan pada sisi client dan dikirim kembali ke server melalui HTTP request.

Jika aplikasi menggunakan nilai cookie sebagai dasar authorization tanpa validasi server-side yang memadai, cookie dapat dimanipulasi.

## 🔬 Source / Page Analysis

Cookie yang ditemukan:

```text
Cookie sebelum perubahan:
loggedin : 0

Perubahan:
loggedin : 1

Response:
Access granted. The password for natas6 is 7mhjtShJAc.................

Pada awalnya cookie dengan name : loggedin dan value : 0 bisa kita ubah karena aplikasi menggunakan ookie sebagai dasar authorization tanpa validasi server-side yang memadai.

sehingga cookie dapat kita ubah menjadi name : loggedin dan value : 1 sehingga kita dapat teridentifikasi log in hanya dengan mengubah nilai cookie tersebut.
```

## 🛡️ Mitigation
Jangan menggunakan nilai cookie yang dikontrol client sebagai satu-satunya dasar authorization.

Gunakan server-side session dan validasi authorization.

## 📚Yang Saya Pelajari
1. Cara kerja HTTP cookie.

2. Cookie dikirim kembali oleh browser.

3. Client-controlled state tidak boleh dipercaya begitu saja.