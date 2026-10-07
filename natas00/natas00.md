# Natas 00 → Natas 01

## 🎯 Objective

Mendapatkan password untuk level Natas 01.

## 🔎 Initial Analysis

Pada level ini, mungkin bisa dibilang adalah level untuk pemanasan yang dimana untuk menemukan password di level ini cukup masuk dev tools (F12) pada browser dan lihat di comment section dimana disitu tertulis "The password for natas1 is scfWG6..............", yang dimana sebagian password akan saya sensor karena bersifat credential key.

## 🧠 Vulnerability / Concept

**Concept:** Client-side restriction

Hal penting yang dipelajari:

- Selalu Cek Dev tools (F12) pada website target jika memungkinkan.
- Jangan taruh Credential Penting pada comment section, karena comment section tersebut hanya tidak menampilkan pada halaman website namun jika di inspect akan tetap muncul pada halaman inspect element tersebut.

