
---

### `natas-02/README.md`

```markdown
# Natas 02 → Natas 03

## 🎯 Objective

Mendapatkan password untuk level Natas 03.

## 🔎 Initial Analysis

Halaman utama tidak memberikan informasi penting secara langsung.

Saya kemudian memeriksa source HTML dan resource yang digunakan oleh halaman.

## 🧠 Vulnerability / Concept

**Concept:** Information Disclosure

Hal yang dipelajari:

- Informasi sensitif dapat ditemukan melalui resource yang tidak terlihat pada halaman utama.
- File atau direktori yang tidak ditampilkan pada UI tetap dapat diakses jika server menyediakannya.

## Mitigation
Server seharusnya tidak mengekspos file, direktori, atau informasi sensitif yang tidak diperlukan oleh pengguna.

## 🔬 Source / Page Analysis

Potongan source yang relevan:

```html
<img src="files/pixel.png">

disitu kita bisa melihat bahwa ada informasi sensitif mengenai letak folder yang berisi sesuatu misalnya disitu kita lihat ada file dengan nama "pixel.png" pada folder files, dengan begitu kita mengetahui bahwa di server http://natas2.natas.labs.overthewire.org/ ada endpoint(folder) tersembunyi yaitu endpoint "files".

Maka dengan informasi itu kita bisa mengakses URL http://natas2.natas.labs.overthewire.org/files/ dan kita mendapati bahwa folder files bisa dibuka melalui URL tersebut dan berisi 2 file yaitu "pixel.png" dan "text.txt" yang dimana ketika membuka file text.txt, file tersebut berisi credential sensitif berupa : 
# username:password
alice:BYNdCesZqW
bob:jw2ueICLvT
charlie:G5vCxkVV3m
natas3:K30JrS...............
eve:zo4mJWyNj2
mallory:9urtcpzBmH

dengan begitu kita mendapati bahwa password untuk natas3 adalah "K30JrS..............."

