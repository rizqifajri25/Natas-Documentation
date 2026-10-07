
---

### `natas-03/README.md`

```markdown
# Natas 03 → Natas 04

## 🎯 Objective

Mendapatkan password untuk level Natas 04.

## 🔎 Initial Analysis

Saya memeriksa halaman dan source code untuk mencari informasi atau resource yang mungkin tidak terlihat secara langsung.

## 🧠 Vulnerability / Concept

**Concept:** robots.txt / Information Disclosure

File `robots.txt` digunakan untuk memberikan instruksi kepada search engine crawler.

Namun, `robots.txt` bukan mekanisme autentikasi atau access control.

## 🔬 Source / Page Analysis

Resource yang ditemukan:

```text
/robots.txt

dari mana saya mengetahui bahwa terdapat endpoint /robots.txt di situ? Satu hal yang paling menonjol adalah tulisan pada comment section yang mengatakan <!-- No more information leaks!! Not even Google will find it this time... --> yang artinya bahkan google pun tidak bisa menampilkannya kali ini, yang artinya ada endpoint tersembunyi di URL ini yang memang sengaja disembunyikan dari search engine crawler yaitu robots.txt

dan ketika kita membuka endpoint tersebut http://natas3.natas.labs.overthewire.org/robots.txt maka akan muncul text berupa : 
User-agent: *
Disallow: /s3cr3t/

disitu kita bisa melihat ada endpoint tersembunyi lagi di URL ini yang bernama s3cr3t.

lalu kita bisa membukan URL http://natas3.natas.labs.overthewire.org/s3cr3t/
hasilnya kita menemukan satu file yang bernama "users.txt" yang dimana ketika kita membuka file users.txt tersebut kita akan menemukan password untuk natas 4 yaitu :
"natas4:JDrPn...................."