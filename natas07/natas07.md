# Natas 07 → Natas 08

## 🎯 Objective

Mendapatkan password untuk level Natas 08.

## 🔎 Initial Analysis

Aplikasi web menampilkan tautan navigasi `Home` dan `About` yang URL-nya menggunakan parameter `page` (yaitu `index.php?page=home` dan `index.php?page=about`). 

Dengan memeriksa *source code* halaman (seperti yang dilampirkan), ditemukan sebuah komentar HTML yang disembunyikan oleh developer:
`<!-- hint: password for webuser natas8 is in /etc/natas_webpass/natas8 -->`

Petunjuk ini memberi tahu kita lokasi absolut file yang berisi password target di dalam server.

## 🧠 Vulnerability / Concept

**Concept:** Local File Inclusion (LFI) / Directory Traversal

Celah keamanan ini terjadi ketika sebuah aplikasi web memasukkan file (biasanya melalui parameter URL) tanpa memvalidasi atau membersihkan input dari pengguna dengan benar. Hal ini memungkinkan penyerang untuk memanipulasi parameter agar aplikasi membaca file sensitif di dalam server.

## 🔬 Source / Page Analysis

Bagian HTML yang relevan:
```html
<a href="index.php?page=home">Home</a>
<a href="index.php?page=about">About</a>
<!-- hint: password for webuser natas8 is in /etc/natas_webpass/natas8 -->
```

## 🧪 Investigation

Percobaan:

Request normal:
Mengakses URL:
http://natas7.natas.labs.overthewire.org/index.php?page=about

Response (Normal):
Menampilkan teks konten untuk halaman about:
this is the about page

Request exploit:
Mengganti nilai parameter page dengan file target yang disebutkan pada hint:
http://natas7.natas.labs.overthewire.org/index.php?page=/etc/natas_webpass/natas8

Response (exploit):
ugXL95KQmUAJJj6...........

Karena backend PHP langsung mengeksekusi include pada nilai tersebut tanpa filter keamanan, server membaca isi file konfigurasi tersebut dari root directory server dan menampilkannya di halaman web.

## 🛡️ Mitigation

1. Jangan gunakan input pengguna mentah untuk mengakses direktori atau file di server.

2. Terapkan Allowlist (Daftar Putih): Hanya izinkan nilai parameter tertentu secara eksplisit. Misalnya:
```php
PHP
$allowed_pages = ['home', 'about'];
if (in_array($_GET['page'], $allowed_pages)) {
    include($_GET['page'] . '.php');
} else {
    // Tampilkan error 404
}
```

3. Hapus direktori atau karakter peretasan jalur navigasi (seperti ../, ..%2f, atau path absolut /) sebelum memproses nama file menggunakan fungsi seperti basename().

## 📚 Yang Saya Pelajari

1. Selalu periksa Page Source untuk mencari komentar developer (<!-- ... -->) yang tertinggal, karena sering kali membocorkan informasi atau kredensial sistem.

2. Pemahaman tentang mekanisme Local File Inclusion (LFI).

3.  memetakan input parameter URL pengguna secara langsung ke fungsi pemanggilan file system server (seperti include).