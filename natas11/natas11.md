# Natas 11 → Natas 12

## 🎯 Objective

Mendapatkan password untuk level Natas 12 dengan cara memanipulasi *cookie* yang dienkripsi.

## 🔎 Initial Analysis

Halaman web memungkinkan pengguna mengubah warna latar belakang (`bgcolor`). Aplikasi menyatakan bahwa *cookies* dilindungi dengan enkripsi XOR. Dengan melihat *source code*, kita melihat bahwa jika variabel array `$data["showpassword"]` bernilai `"yes"`, aplikasi akan menampilkan password untuk level selanjutnya. Masalahnya, nilai default-nya adalah `"no"` dan data ini disimpan di dalam *cookie* pengguna.

## 🧠 Vulnerability / Concept

**Concept:** Known-Plaintext Attack pada XOR Cipher & Cookie Tampering

Enkripsi XOR (Exclusive OR) memiliki kelemahan matematis yang fatal jika kuncinya (*key*) digunakan berulang dan kita mengetahui isi sebagian pesan aslinya (*plaintext*). 
Sifat dasar XOR:
*   `Plaintext ⊕ Key = Ciphertext` (Proses Enkripsi)
*   `Ciphertext ⊕ Key = Plaintext` (Proses Dekripsi normal)
*   **`Plaintext ⊕ Ciphertext = Key` (Kerentanan Known-Plaintext)**

Karena kita tahu persis format data default sebelum dienkripsi (Plaintext) dan kita bisa melihat hasil enkripsinya di *cookie* browser (Ciphertext), kita bisa membongkar Kunci (Key) yang disensor oleh developer. Setelah kunci didapat, kita bisa mengenkripsi payload buatan kita sendiri.

## 🔬 Source / Page Analysis

Bagian PHP yang relevan:
```php
$defaultdata = array( "showpassword"=>"no", "bgcolor"=>"#ffffff");

function xor_encrypt($in) {
    $key = '<censored>';
    // ... iterasi XOR ...
}

// Format Cookie: Base64 -> XOR -> JSON
$tempdata = json_decode(xor_encrypt(base64_decode($_COOKIE["data"])), true);

if($data["showpassword"] == "yes") {
    print "The password for natas12 is <censored><br>";
}
```

## Analisis

1. Format Plaintext: Data di-encode menggunakan json_encode($defaultdata). Bentuk string-nya pasti persis seperti ini: {"showpassword":"no","bgcolor":"#ffffff"}

2. Format Ciphertext: Data ini kemudian di-XOR dengan secret key, lalu di-encode dengan Base64 dan disimpan di cookie bernama data.

3. Target: Kita perlu mengubah isi JSON menjadi {"showpassword":"yes","bgcolor":"#ffffff"}, mengenkripsinya kembali dengan XOR key yang sama, mengubahnya ke Base64, dan mengganti cookie lama kita dengan yang baru ini.

## 🧪 Investigation

Langkah 1: Mengambil Ciphertext dari Browser

Buka Developer Tools di browser (F12) -> masuk ke tab Application (Chrome) atau Storage (Firefox) -> Cookies.
Disitu kita melihat cookies dengan `Name` -> `data` dan `value` -> `EGAgHwQ1IxYYMSQYGSZxTUksPFVHYDEQCC0/GBlgaVVIJDURDSQ1VRY=`

Langkah 2: Membongkar Kunci Rahasia (Key)

Karena sifat XOR adalah Ciphertext ⊕ Plaintext = Key, kita akan memasukkan ciphertext (cookie) dan menyandingkannya dengan plaintext (JSON asli).

1. Buka situs CyberChef.

Pada panel Input (kanan atas), masukkan nilai cookie data yang kita temukan tadi:
EGAgHwQ1IxYYMSQYGSZxTUksPFVHYDEQCC0/GBlgaVVIJDURDSQ1VRY=

2. Pada panel Operations (kiri), cari From Base64, lalu tarik (drag) ke kolom Recipe (tengah). Ini akan mengubah Base64 kembali menjadi teks mentah (raw bytes).

3. Cari operasi XOR di kiri, lalu tarik ke bawah From Base64 di kolom Recipe.

4. Pada pengaturan operasi XOR yang baru kita tarik:

Ubah Scheme menjadi UTF8.

5. Pada kolom Key, masukkan teks JSON aslinya secara persis: `{"showpassword":"no","bgcolor":"#ffffff"}`

6. Lihat panel Output di kanan bawah. Kita akan melihat teks yang berulang yaitu kBSwkBSwkBSwkBSwkBSwkBSwkBSwkBSwkBSwkBSwk

7. Dari pengulangan tersebut, Anda berhasil menyimpulkan bahwa kuncinya adalah `KBSw`

Langkah 3: Membuat Cookie Baru (Forging)

Sekarang kita balik posisinya. Kita akan mengenkripsi plaintext baru dengan kunci `KBSw` yang baru saja kita temukan.

1. Klik ikon tempat sampah (Clear recipe) dan ikon silang di Input untuk membersihkan area kerja.

2. Pada panel Input, masukkan teks JSON yang sudah dimodifikasi (ubah "no" menjadi "yes"):
{"showpassword":"yes","bgcolor":"#ffffff"}

3. Tarik operasi XOR ke kolom Recipe.

4. Pada pengaturan XOR:

Ubah Scheme menjadi UTF8.

5. Pada kolom Key, masukkan kunci yang kita temukan tadi: KBSw

6. Tarik operasi To Base64 dan letakkan tepat di bawah operasi XOR di kolom Recipe.

7. Lihat panel Output. Anda akan langsung mendapatkan string baru:
`MGAgHyQ1IxY4MSQYOSZxTWk7NgRpbnEVLCE8GyQwcU1pYTURLSQ1EWk/`

8. Kita tinggal menyalin teks dari output tersebut, menempelkannya sebagai nilai cookie data di browser, dan me-refresh halaman Natas 11 untuk mendapatkan password-nya.