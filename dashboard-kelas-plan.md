# Plan: Dashboard Pengurusan Kelas — Politeknik Ungku Omar

## Overview

Bina sebuah web application menggunakan **PHP + MySQL + Bootstrap** sebagai dashboard pengurusan kelas untuk pensyarah di Politeknik Ungku Omar. Aplikasi ini merangkumi modul senarai pelajar, jadual kelas, kehadiran, dan markah (dengan komponen Kuiz, Ujian, Tugasan, Peperiksaan). Sistem mempunyai halaman login khusus untuk pensyarah.

**Skop:**
- Sistem login pensyarah (satu peranan sahaja)
- Modul Pelajar (CRUD)
- Modul Jadual Kelas
- Modul Kehadiran
- Modul Markah (multi-komponen + gred automatik)
- Halaman Overview/Dashboard (statistik ringkas)

**Teknologi:**
- Backend: PHP (native, tanpa framework)
- Database: MySQL
- Frontend: Bootstrap 5 + HTML + CSS + JavaScript
- Persekitaran: XAMPP (localhost)

---

## Struktur Projek

```
dashboard-kelas/
├── index.php               (redirect ke login atau dashboard)
├── login.php
├── logout.php
├── dashboard.php           (overview utama)
├── pelajar/
│   ├── index.php           (senarai pelajar)
│   ├── tambah.php
│   ├── edit.php
│   └── padam.php
├── jadual/
│   └── index.php
├── kehadiran/
│   ├── index.php
│   └── rekod.php
├── markah/
│   ├── index.php
│   └── input.php
├── includes/
│   ├── db.php              (sambungan database)
│   ├── auth.php            (semak sesi login)
│   └── header.php / footer.php / sidebar.php
└── assets/
    ├── css/style.css
    └── js/script.js
```

---

## Skema Database

### Jadual `pensyarah`
| Lajur | Jenis | Keterangan |
|---|---|---|
| id | INT PK AUTO | - |
| nama | VARCHAR(100) | - |
| email | VARCHAR(100) | Untuk login |
| password | VARCHAR(255) | bcrypt hash |

### Jadual `pelajar`
| Lajur | Jenis | Keterangan |
|---|---|---|
| id | INT PK AUTO | - |
| no_matrik | VARCHAR(20) | Unik |
| nama | VARCHAR(100) | - |
| program | VARCHAR(100) | - |
| semester | INT | - |
| pensyarah_id | INT FK | - |

### Jadual `jadual_kelas`
| Lajur | Jenis | Keterangan |
|---|---|---|
| id | INT PK AUTO | - |
| subjek | VARCHAR(100) | - |
| hari | ENUM | Isnin-Ahad |
| masa_mula | TIME | - |
| masa_tamat | TIME | - |
| bilik | VARCHAR(50) | - |
| pensyarah_id | INT FK | - |

### Jadual `kehadiran`
| Lajur | Jenis | Keterangan |
|---|---|---|
| id | INT PK AUTO | - |
| pelajar_id | INT FK | - |
| jadual_id | INT FK | - |
| tarikh | DATE | - |
| status | ENUM | hadir / tidak_hadir / mc |

### Jadual `markah`
| Lajur | Jenis | Keterangan |
|---|---|---|
| id | INT PK AUTO | - |
| pelajar_id | INT FK | - |
| jadual_id | INT FK | Subjek berkaitan |
| kuiz | DECIMAL(5,2) | Max 10 |
| ujian | DECIMAL(5,2) | Max 30 |
| tugasan | DECIMAL(5,2) | Max 20 |
| peperiksaan | DECIMAL(5,2) | Max 40 |
| jumlah | DECIMAL(5,2) | Dikira automatik |
| gred | VARCHAR(2) | A/B/C/D/F |

**Formula Gred:**
- A: 80–100
- B: 65–79
- C: 50–64
- D: 40–49
- F: < 40

---

## Sub-Tasks

---

### Sub-Task 1: Persediaan Projek & Database

**Status:** [ ] pending

**Intent:**
Buat struktur folder projek, fail konfigurasi database, dan jalankan SQL untuk cipta semua jadual. Ini adalah asas untuk semua modul seterusnya.

**Expected Outcomes:**
- Folder `dashboard-kelas/` wujud dengan struktur lengkap
- Fail `includes/db.php` berfungsi untuk sambungan MySQL
- SQL script `database.sql` boleh diimport ke phpMyAdmin
- Fail `includes/auth.php` untuk semak sesi

**Todo List:**
- [ ] Cipta struktur folder projek
- [ ] Tulis `database.sql` dengan semua jadual dan data contoh (1 pensyarah, 5 pelajar)
- [ ] Tulis `includes/db.php` untuk sambungan PDO MySQL
- [ ] Tulis `includes/auth.php` untuk semak sesi login
- [ ] Tulis `includes/header.php`, `includes/footer.php`, dan `includes/sidebar.php` (navbar + sidebar Bootstrap)
- [ ] Tulis `assets/css/style.css` untuk warna tema Politeknik (biru/merah)

**Relevant Context:**
- Tiada fail sedia ada — projek baru dari awal
- Guna PDO untuk sambungan database (lebih selamat)
- Password disimpan sebagai bcrypt hash (`password_hash()`)

---

### Sub-Task 2: Halaman Login & Logout

**Status:** [ ] pending

**Intent:**
Bina halaman login untuk pensyarah dengan pengesahan menggunakan email dan password. Pastikan sesi diuruskan dengan betul.

**Expected Outcomes:**
- `login.php` — borang login dengan Bootstrap, pengesahan email+password
- `logout.php` — hapus sesi dan redirect ke login
- `index.php` — redirect berdasarkan status login
- Mesej ralat jika login gagal

**Todo List:**
- [ ] Tulis `login.php` dengan borang Bootstrap (email + password)
- [ ] Tulis logik PHP untuk semak email & password menggunakan `password_verify()`
- [ ] Set sesi `$_SESSION['pensyarah_id']` dan `$_SESSION['nama']` selepas login berjaya
- [ ] Redirect ke `dashboard.php` selepas login berjaya
- [ ] Tulis `logout.php` untuk hapus sesi
- [ ] Tulis `index.php` untuk redirect ke dashboard jika sudah login, atau ke login jika belum

**Relevant Context:**
- Jadual `pensyarah` dalam database
- Guna `password_hash(PASSWORD_BCRYPT)` untuk hash password dalam data contoh SQL

---

### Sub-Task 3: Halaman Dashboard / Overview

**Status:** [ ] pending

**Intent:**
Bina halaman utama dashboard yang memaparkan statistik ringkas dan maklumat berguna untuk pensyarah.

**Expected Outcomes:**
- `dashboard.php` memaparkan:
  - Jumlah pelajar
  - Purata peratusan kehadiran
  - Bilangan subjek/kelas diajar
  - Jadual kelas hari ini
  - Senarai pelajar kehadiran rendah (< 80%)

**Todo List:**
- [ ] Tulis `dashboard.php` dengan semakan sesi (`auth.php`)
- [ ] Query statistik ringkas dari database (count pelajar, avg kehadiran)
- [ ] Paparkan kad statistik Bootstrap (cards)
- [ ] Paparkan jadual kelas hari ini berdasarkan hari semasa
- [ ] Paparkan amaran pelajar kehadiran < 80%

**Relevant Context:**
- Guna `includes/header.php`, `sidebar.php`, `footer.php`
- Hari semasa: `date('l')` dalam PHP — perlu map ke nama hari Melayu

---

### Sub-Task 4: Modul Pelajar (CRUD)

**Status:** [ ] pending

**Intent:**
Bina modul untuk pensyarah melihat, menambah, mengedit, dan memadam rekod pelajar.

**Expected Outcomes:**
- `pelajar/index.php` — jadual senarai pelajar dengan fungsi carian
- `pelajar/tambah.php` — borang tambah pelajar baru
- `pelajar/edit.php?id=X` — borang edit maklumat pelajar
- `pelajar/padam.php?id=X` — padam dengan konfirmasi

**Todo List:**
- [ ] Tulis `pelajar/index.php` dengan jadual Bootstrap + carian mengikut nama/no. matrik
- [ ] Tulis `pelajar/tambah.php` dengan borang dan validasi (no. matrik unik)
- [ ] Tulis `pelajar/edit.php` dengan borang pra-isi maklumat semasa
- [ ] Tulis `pelajar/padam.php` dengan modal konfirmasi Bootstrap
- [ ] Pastikan semua operasi CRUD menggunakan PDO prepared statements

**Relevant Context:**
- Jadual `pelajar` dengan FK `pensyarah_id` — pensyarah hanya nampak pelajar sendiri
- Guna `$_SESSION['pensyarah_id']` untuk tapis data

---

### Sub-Task 5: Modul Jadual Kelas

**Status:** [ ] pending

**Intent:**
Bina halaman untuk memaparkan jadual kelas pensyarah dalam format jadual mingguan.

**Expected Outcomes:**
- `jadual/index.php` — paparan jadual kelas dalam grid mingguan (Isnin–Jumaat)
- Setiap slot tunjuk: nama subjek, masa, bilik
- Borang tambah kelas baru

**Todo List:**
- [ ] Tulis `jadual/index.php` dengan grid jadual mingguan Bootstrap
- [ ] Query semua kelas pensyarah dari jadual `jadual_kelas`
- [ ] Paparan dalam bentuk jadual (baris = hari, lajur = masa)
- [ ] Tambah borang modal untuk tambah/edit/padam slot jadual

**Relevant Context:**
- Jadual `jadual_kelas` dengan lajur hari (ENUM Isnin–Ahad)

---

### Sub-Task 6: Modul Kehadiran

**Status:** [ ] pending

**Intent:**
Bina modul untuk pensyarah merekod kehadiran pelajar mengikut kelas dan tarikh, serta melihat laporan kehadiran.

**Expected Outcomes:**
- `kehadiran/index.php` — pilih kelas dan tarikh untuk rekod kehadiran
- `kehadiran/rekod.php` — paparkan senarai pelajar dengan toggle Hadir/Tidak Hadir/MC
- Laporan peratusan kehadiran setiap pelajar

**Todo List:**
- [ ] Tulis `kehadiran/index.php` dengan dropdown pilih kelas + input tarikh
- [ ] Tulis `kehadiran/rekod.php` yang paparkan semua pelajar dengan radio button status kehadiran
- [ ] Simpan rekod kehadiran ke jadual `kehadiran` (guna INSERT ... ON DUPLICATE KEY UPDATE)
- [ ] Paparkan % kehadiran setiap pelajar dalam jadual ringkasan

**Relevant Context:**
- Jadual `kehadiran` perlu kombinasi unik `(pelajar_id, jadual_id, tarikh)` untuk elak duplikat

---

### Sub-Task 7: Modul Markah

**Status:** [ ] pending

**Intent:**
Bina modul untuk pensyarah memasukkan markah pelajar mengikut komponen (Kuiz, Ujian, Tugasan, Peperiksaan), dengan pengiraan jumlah dan gred automatik.

**Expected Outcomes:**
- `markah/index.php` — senarai pelajar dengan ringkasan markah dan gred
- `markah/input.php` — borang input markah 4 komponen per pelajar per subjek
- Jumlah markah dan gred dikira automatik selepas simpan

**Todo List:**
- [ ] Tulis `markah/index.php` dengan jadual markah semua pelajar (boleh tapis mengikut subjek)
- [ ] Tulis `markah/input.php` dengan borang 4 komponen markah per pelajar
- [ ] Kira `jumlah = kuiz + ujian + tugasan + peperiksaan` dalam PHP
- [ ] Kira `gred` berdasarkan formula: A(80+), B(65-79), C(50-64), D(40-49), F(<40)
- [ ] Simpan ke jadual `markah` dengan INSERT ... ON DUPLICATE KEY UPDATE
- [ ] Paparkan warna gred berbeza (Bootstrap badges: hijau=A, biru=B, kuning=C, oren=D, merah=F)

**Relevant Context:**
- Jadual `markah` dengan lajur kuiz (max 10), ujian (max 30), tugasan (max 20), peperiksaan (max 40)
- Jumlah max = 100

---

## Pelan Implementasi

Sub-tasks perlu dilaksanakan mengikut urutan berikut kerana ada kebergantungan:

1. Sub-Task 1 (Persediaan) → 2 (Login) → 3 (Dashboard) → 4 (Pelajar) → 5 (Jadual) → 6 (Kehadiran) → 7 (Markah)
