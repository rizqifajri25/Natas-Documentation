# Natas 08 → Natas 09

## 🎯 Objective

Mendapatkan password untuk level Natas 09.

## 🔎 Initial Analysis

Halaman web menampilkan sebuah form input yang meminta pengguna memasukkan sebuah *secret*. Terdapat juga tautan ke *source code* PHP yang menangani form tersebut. 

Setelah memeriksa *source code*, terlihat bahwa input dari pengguna akan diubah (di-encode) melalui sebuah fungsi buatan developer, lalu dicocokkan dengan sebuah *string* acak (`3d3d516343746d4d6d6c315669563362`). Jika cocok, password level selanjutnya akan ditampilkan.

## 🧠 Vulnerability / Concept

**Concept:** Security by Obscurity / Reversible Encoding (Kriptografi Lemah)

Fungsi perlindungan *secret* yang digunakan bukanlah algoritma *hashing* searah (seperti SHA-256 atau bcrypt), melainkan rangkaian fungsi *encoding* biasa. Fungsi *encoding* diciptakan untuk mengubah format data agar aman ditransfer, bukan untuk menyembunyikan data secara permanen, sehingga prosesnya bisa dibalik (di-dekode) dengan mudah.

## 🔬 Source / Page Analysis

Bagian PHP yang relevan:
```php
$encodedSecret = "3d3d516343746d4d6d6c315669563362";

function encodeSecret($secret) {
    return bin2hex(strrev(base64_encode($secret)));
}
```

## Analisis:

-> Apa fungsi encodeSecret?
Fungsi ini melakukan tiga tahap manipulasi terhadap input secara berurutan:

1. base64_encode(): Mengubah string input menjadi format Base64.

2. strrev(): Membalikkan urutan karakter string dari belakang ke depan (Reverse).

3. bin2hex(): Mengubah string tersebut menjadi representasi Heksadesimal.

-> Mengapa mekanisme ini rentan?
Ketiga fungsi PHP di atas memiliki fungsi kebalikan (invers) bawaan (hex2bin, strrev, base64_decode). Karena kita mengetahui hasil akhir ($encodedSecret) dan urutan proses pembentukannya, kita dapat melakukan "Reverse Engineering" dengan mengeksekusi fungsi kebalikannya dalam urutan yang terbalik pula.

## 🧪 Investigation

Percobaan (Reverse Engineering):
Untuk menemukan secret aslinya, kita harus membalikkan proses dari tahap 3 ke tahap 1.
Urutan decoding:

1. Ubah hex kembali menjadi teks biasa (hex2bin).

2. Balikkan kembali urutan karakternya ke posisi semula (strrev).

3. Dekode dari format Base64 menjadi teks asli (base64_decode).

Berikut adalah script PHP sederhana untuk membongkarnya:

```php
PHP
<?php
$encodedSecret = "3d3d516343746d4d6d6c315669563362";
$decodedSecret = base64_decode(strrev(hex2bin($encodedSecret)));
echo "Secret aslinya adalah: " . $decodedSecret;
?>
```
Namun di kasus ini saya menggunakan tools yang bernama Cyberchef yang merupakan tools untuk kriptografi online.

Setelah proses decoded selesai maka hasil yang akan didapat adalah *oubWYf2kBq* yang dimana itu merupakan secret yang harus dimasukkan ke form input level ini agar bisa mendapatkan password untuk level selanjutnya.

Setelah secret tersebut dimasukkan ke form input maka akan keluar password nya sebagai berikut : Access granted. The password for natas9 is UdxmI27dTaXmnd............

## 🛡️ Mitigation

1. Jangan pernah menggunakan encoding (seperti Base64, Hex) atau sekadar membalikkan string untuk mengamankan kredensial atau secret. Mekanisme ini disebut Security by Obscurity dan sangat mudah dipatahkan.

2. Gunakan fungsi Cryptographic Hashing yang kuat dan searah (seperti password_hash() di PHP yang menggunakan algoritma bcrypt/Argon2) untuk memvalidasi password atau data rahasia.

## 📚 Yang Saya Pelajari 

1. Perbedaan mendasar antara Encoding (dapat dibalik) dan Hashing (searah/tidak dapat dibalik).

2.Bagaimana melakukan Reverse Engineering pada logika fungsi PHP sederhana untuk membongkar kredensial statis (hardcoded).

## 🧰 Tools

CyberChef (Web-based)