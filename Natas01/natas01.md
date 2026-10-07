# Natas 01 → Natas 02

## 🎯 Objective

Mendapatkan password untuk level Natas 02.

## 🔎 Initial Analysis

Pada level ini, saya menemukan bahwa terdapat pembatasan pada halaman web yang mencegah pengguna melakukan tindakan tertentu seperti misalnya rightclicking pada mouse

Hal pertama yang saya lakukan adalah memeriksa bagaimana pembatasan tersebut diterapkan oleh aplikasi.

## 🧠 Vulnerability / Concept

**Concept:** Client-side restriction

Hal penting yang dipelajari:

- Pembatasan yang dilakukan di sisi client tidak selalu dapat dipercaya.
- HTML dan JavaScript yang dikirim ke browser dapat diperiksa dan dimodifikasi.
- Security control seharusnya diterapkan di server-side.

## 🔬 Source / Page Analysis

Bagian yang menarik:

Karena rightclicking di blok dan tidak bisa melakukan rightclicking, maka hal pertama yang saya lakukan adalah mencari tau cara untuk melihat source code selain dari rightclicking, lalu saya menemukan cara " CTRL + U ", dengan begitu maka akan menampilkan source code website tersebut tanpa harus rightclicking dan disitu saya menemukan <!--The password for natas2 is vsDOxoX..................... --> pada comment section yang dimana persis seperti natas00 bisa kita lihat password untuk level selanjutnya ada di comment section.