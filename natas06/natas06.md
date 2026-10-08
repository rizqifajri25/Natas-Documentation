
---

### `natas-06/README.md`

```markdown
# Natas 06 → Natas 07

## 🎯 Objective

Mendapatkan password untuk level Natas 07.

## 🔎 Initial Analysis

Halaman menyediakan sebuah form yang menerima input tertentu.

Saya kemudian memeriksa source code untuk memahami bagaimana input tersebut diproses.

## 🧠 Vulnerability / Concept

**Concept:** Source code disclosure / hardcoded secret

## 🔬 Source / Page Analysis

Potongan source yang relevan:

```php
<?

include "includes/secret.inc";

    if(array_key_exists("submit", $_POST)) {
        if($secret == $_POST['secret']) {
        print "Access granted. The password for natas7 is <censored>";
    } else {
        print "Wrong secret";
    }
    }
?>
```

## 💡 Exploitation

Saat melihat source code, pandangan pertama saya tertuju pada `include "includes/secret.inc";` lalu setelah itu baru melihat kelanjutan code nya.

dari yang saya tangkap inti nya adalah jika kita memasukkan sesuai dengan `secret` yang benar maka password untuk level selanjutnya akan muncul.

Dari Source Code kita bisa melihat ada endpoint `includes/secret.inc` lalu dengan informasi itu kita bisa membuka URL http://natas6.natas.labs.overthewire.org/includes/secret.inc yang dimana isinya adalah 
```php
<?
$secret = "FOEIUWGHFEEUHOFUOIU";
?>
```
Dengan begitu kita sudah mengetahui bahwa teks `secret` yang dimaksud adalah "FOEIUWGHFEEUHOFUOIU", maka kita tinggal memasukkan kata secret tersebut ke form input tadi dan password untuk level selanjutnya akan muncul 

Access granted. The password for natas7 is B1szg95UcTnrz.............