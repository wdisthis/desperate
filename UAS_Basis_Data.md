# 📚 STUDY GUIDE UAS — BASIS DATA
**Sains Data ITERA 2024/2025 | Pertemuan 9–14**

---

## 📋 DAFTAR ISI

1. [Pertemuan 9 — ERD & Transformasi ke Basis Data Fisik](#1-erd--transformasi-ke-basis-data-fisik)
2. [Pertemuan 10 — Penerapan Basis Data (Relasi Tabel & Kodifikasi)](#2-penerapan-basis-data--relasi-tabel--kodifikasi)
3. [Pertemuan 11 — Data Definition Language (DDL)](#3-data-definition-language-ddl)
4. [Pertemuan 12 — Data Manipulation Language I (DML)](#4-data-manipulation-language-i-dml)
5. [Pertemuan 13 — Data Manipulation Language II (Subquery Lanjutan)](#5-data-manipulation-language-ii-subquery-lanjutan)
6. [Pertemuan 14 — EDA dalam SQL & Data Control Language (DCL)](#6-eda-dalam-sql--data-control-language-dcl)
7. [Ringkasan Perintah SQL](#7-ringkasan-perintah-sql)
8. [Latihan Soal Essay](#8-latihan-soal-essay)

---

## 1. ERD & Transformasi ke Basis Data Fisik

### 1.1 Apa itu ERD?

**Entity Relationship Diagram (ERD)** adalah diagram yang menggambarkan hubungan antar entitas dalam sebuah sistem informasi atau basis data.

**Komponen Utama ERD:**

| Komponen | Deskripsi | Contoh |
|----------|-----------|--------|
| **Entitas** | Objek nyata atau konsep | Mahasiswa, Dosen, Buku |
| **Atribut** | Ciri/informasi dari entitas | Nama, NIM, Alamat |
| **Relasi** | Hubungan antar entitas | Mahasiswa *mengambil* Matakuliah |
| **Primary Key** | Atribut unik pembeda tiap entitas | NIM |
| **Foreign Key** | Atribut yang merujuk ke entitas lain | kode_dosen di tabel Mahasiswa |

---

### 1.2 Jenis Entitas

#### Entitas Kuat (Strong Entity)
- Dapat **berdiri sendiri**, tidak bergantung pada entitas lain
- Memiliki **atribut kunci (Primary Key)** yang unik
- Digambarkan dalam **persegi panjang biasa**

**Contoh:**
```
Mahasiswa (NIM [PK], Nama, Alamat)
```
> NIM adalah Primary Key → Mahasiswa adalah entitas kuat

#### Entitas Lemah (Weak Entity)
- **Tidak memiliki** atribut kunci sendiri
- Identitasnya **bergantung** pada entitas kuat melalui hubungan identifikasi
- Digambarkan dalam **persegi panjang ganda**

**Contoh:**
```
Faktur (NoFaktur [PK]) — entitas kuat
DetailFaktur — entitas lemah, butuh NoFaktur dari Faktur
```

---

### 1.3 Derajat Relasi (Cardinality)

#### Relasi 1:1 (One to One)
Satu entitas A hanya berhubungan dengan satu entitas B, dan sebaliknya.

**Aturan Transformasi:** Atribut kunci salah satu entitas ditambahkan ke tabel entitas lain sebagai Foreign Key.

```
DOSEN (KodeDosen, NamaDosen, Alamat)
JURUSAN (KodeJurusan, NamaJurusan, KodeDosen)
                                      ↑ FK dari Dosen
```

**Implementasi SQL:**
```sql
CREATE TABLE dosen (
    kode_dosen CHAR(9) PRIMARY KEY,
    nama_dosen VARCHAR(100)
);

CREATE TABLE jurusan (
    kode_jurusan CHAR(5) PRIMARY KEY,
    nama_jurusan VARCHAR(100),
    kode_dosen CHAR(9),
    FOREIGN KEY (kode_dosen) REFERENCES dosen(kode_dosen)
);
```

---

#### Relasi 1:N (One to Many)
Satu entitas A berhubungan dengan banyak entitas B, tapi satu B hanya ke satu A.

**Aturan Transformasi:** Atribut kunci dari sisi "satu" ditambahkan ke tabel sisi "banyak" sebagai Foreign Key.

```
DOSEN (KodeDosen, NamaDosen)
MATAKULIAH (KodeKuliah, NamaKuliah, SKS, Semester, KodeDosen, Tempat, Waktu)
                                                        ↑ FK dari Dosen
```

**Implementasi SQL:**
```sql
CREATE TABLE dosen (
    kode_dosen CHAR(9) PRIMARY KEY,
    nama_dosen VARCHAR(100)
);

CREATE TABLE matakuliah (
    kode_kuliah CHAR(8) PRIMARY KEY,
    nama_kuliah VARCHAR(100),
    sks INT,
    semester INT,
    kode_dosen CHAR(9),
    FOREIGN KEY (kode_dosen) REFERENCES dosen(kode_dosen)
        ON DELETE CASCADE ON UPDATE CASCADE
);
```

---

#### Relasi N:N (Many to Many)
Satu entitas A bisa terhubung ke banyak B, dan sebaliknya.

**Aturan Transformasi:** Dibuat **tabel relasi baru** yang berisi Primary Key dari kedua entitas sebagai Foreign Key, sekaligus bisa dijadikan Primary Key gabungan (Composite Key).

```
DOSEN (KodeDosen, NamaDosen)
MATAKULIAH (KodeKuliah, NamaKuliah, SKS)
JADWAL (KodeDosen, KodeKuliah, Tempat, Waktu)   ← tabel relasi baru
         ↑ FK          ↑ FK
```

**Implementasi SQL:**
```sql
CREATE TABLE mahasiswa (
    nim CHAR(8) PRIMARY KEY,
    nama_mahasiswa VARCHAR(100)
);

CREATE TABLE matakuliah (
    kode_kuliah CHAR(8) PRIMARY KEY,
    nama_kuliah VARCHAR(100),
    sks INT
);

-- Tabel relasi N:N
CREATE TABLE krs (
    nim CHAR(8),
    kode_kuliah CHAR(8),
    nilai CHAR(1),
    PRIMARY KEY (nim, kode_kuliah),         -- Composite PK
    FOREIGN KEY (nim) REFERENCES mahasiswa(nim),
    FOREIGN KEY (kode_kuliah) REFERENCES matakuliah(kode_kuliah)
);
```

---

### 1.4 Ringkasan Aturan Transformasi ERD

| Derajat Relasi | Cara Transformasi |
|----------------|-------------------|
| **1:1** | Tambahkan FK salah satu entitas ke tabel entitas lain |
| **1:N** | Tambahkan FK sisi "satu" ke tabel sisi "banyak" |
| **N:N** | Buat tabel relasi baru dengan FK dari kedua entitas |

---

## 2. Penerapan Basis Data — Relasi Tabel & Kodifikasi

### 2.1 Hal yang Perlu Dipersiapkan

1. **Tabel Basis Data (File Data)** — tempat menyimpan data
2. **Struktur File** — menjelaskan rincian fisik setiap file/tabel
3. **Relasi Tabel** — hubungan antar tabel menggunakan FK

---

### 2.2 Relasi Tabel

**Relasi Tabel** adalah hubungan yang terjadi pada suatu tabel dengan tabel lainnya, berfungsi untuk mengatur operasi basis data.

**Contoh Skema Relasi:**
```
tb_dosen (kode_dosen [PK], nama_dosen)
    │
    ├──→ tb_prodi (kode_prodi [PK], nama_prodi, kode_dosen [FK])
    │
    └──→ tb_mahasiswa (nim [PK], nama_mahasiswa, kode_dosen [FK])
```

**Notasi Kardinalitas** digunakan untuk menggambarkan hubungan min-max antar entitas dalam diagram:
- `(0,1)` → minimum 0, maksimum 1
- `(1,1)` → minimum 1, maksimum 1
- `(0,N)` → minimum 0, maksimum banyak
- `(1,N)` → minimum 1, maksimum banyak

---

### 2.3 Struktur File

Struktur file digunakan untuk menentukan **struktur fisik** basis data, menjelaskan rincian dari setiap file/tabel.

**Contoh Struktur File:**

| Field | Keterangan |
|-------|------------|
| **Nama File** | `tb_mahasiswa` |
| **Kunci Utama** | `nim` |
| **Jumlah Atribut** | 3 |

| No | Nama Atribut | Tipe Data |
|----|-------------|-----------|
| 1 | `nim` | `CHAR(8)`, Primary Key, Not Null |
| 2 | `nama_mahasiswa` | `VARCHAR(35)` |
| 3 | `kode_dosen` | `CHAR(9)`, Foreign Key, On Delete Cascade On Update Cascade |

---

### 2.4 Kodifikasi

**Kodifikasi** digunakan sebagai identitas singkat untuk setiap data yang di-input. Kode dibuat dari angka, huruf, atau gabungan keduanya.

#### Jenis Kodifikasi:

**a) Sekuensial** — mengasosiasikan data dengan kode terurut (bilangan atau abjad)

```
Nama Atribut: indek_nilai
Jenis: Sekuensial

A → Sempurna
B → Baik
C → Cukup
D → Kurang
E → Buruk
```

**b) Mnemonic** — pengkodean dengan singkatan bermakna

```
Nama Atribut: jenis_kelamin
Jenis: Mnemonic

L → Laki-Laki
P → Perempuan
```

**c) Blok** — pengkodean dengan format blok terstruktur

```
Nama Atribut: nim
Jenis: Blok
Format: xx-x-xx-xxx (A-B-C-D)
Jumlah Digit: 8

A (2 digit) → Kode jurusan
B (1 digit) → Kode fakultas
C (2 digit) → Tahun masuk/angkatan
D (3 digit) → Nomor urut mahasiswa dalam jurusan

Contoh: 10507234
→ 10 = jurusan, 5 = fakultas, 07 = angkatan 2007, 234 = urutan ke-234
```

---

## 3. Data Definition Language (DDL)

### 3.1 Kategori Perintah SQL

SQL dibagi menjadi 4 kategori utama:

| Kategori | Singkatan | Fungsi | Perintah |
|----------|-----------|--------|----------|
| **Data Definition Language** | DDL | Membuat/mengubah struktur database | `CREATE`, `ALTER`, `DROP` |
| **Data Manipulation Language** | DML | Mengelola data di tabel | `INSERT`, `UPDATE`, `DELETE`, `SELECT` |
| **Data Control Language** | DCL | Mengatur hak akses | `GRANT`, `REVOKE` |
| **Transaction Control Language** | TCL | Mengelola transaksi | `COMMIT`, `ROLLBACK`, `SAVEPOINT` |

---

### 3.2 Apa itu DDL?

**Data Definition Language (DDL)** adalah kumpulan perintah SQL yang digunakan untuk:
- **Membuat** objek baru di database
- **Memodifikasi** struktur objek yang sudah ada
- **Menghapus** objek dari database
- **Mengatur** hubungan antar tabel dan atribut

> ⚠️ DDL **tidak** mengelola isi data, tetapi **struktur/kerangka** database itu sendiri.

---

### 3.3 Perintah DDL

#### CREATE DATABASE
```sql
-- Sintaks
CREATE DATABASE nama_database;

-- Contoh
CREATE DATABASE siakad;
CREATE DATABASE rental;
CREATE DATABASE perpustakaan;
```

#### CREATE TABLE
```sql
-- Sintaks
CREATE TABLE nama_tabel (
    nama_kolom1 tipe_data [CONSTRAINT],
    nama_kolom2 tipe_data [CONSTRAINT],
    ...
);

-- Contoh lengkap
CREATE TABLE mahasiswa (
    id_mahasiswa INT PRIMARY KEY,
    nama         VARCHAR(100) NOT NULL,
    jurusan      VARCHAR(50),
    angkatan     YEAR
);

-- Contoh dengan Foreign Key
CREATE TABLE mahasiswa (
    nim          CHAR(8) PRIMARY KEY NOT NULL,
    nama         VARCHAR(35),
    kode_dosen   CHAR(9),
    FOREIGN KEY (kode_dosen) REFERENCES dosen(kode_dosen)
        ON DELETE CASCADE ON UPDATE CASCADE
);
```

#### ALTER TABLE — Menambah Kolom (ADD)
```sql
-- Sintaks
ALTER TABLE nama_tabel ADD nama_kolom tipe_data;

-- Contoh
ALTER TABLE mahasiswa ADD email VARCHAR(100);
ALTER TABLE kendaraan ADD tahun_kendaraan DATE;
```

#### ALTER TABLE — Mengubah Tipe Data (MODIFY)
```sql
-- Sintaks
ALTER TABLE nama_tabel MODIFY nama_kolom tipe_data_baru;

-- Contoh: ubah tipe data dari DATE menjadi YEAR
ALTER TABLE mahasiswa MODIFY email VARCHAR(150);
ALTER TABLE kendaraan MODIFY tahun_kendaraan YEAR;
```

#### ALTER TABLE — Menghapus Kolom (DROP COLUMN)
```sql
-- Sintaks
ALTER TABLE nama_tabel DROP COLUMN nama_kolom;

-- Contoh
ALTER TABLE mahasiswa DROP COLUMN email;
```

#### ALTER TABLE — Mengganti Nama Tabel (RENAME)
```sql
-- Sintaks
ALTER TABLE nama_tabel RENAME TO nama_tabel_baru;

-- Contoh: ubah nama tabel transaksi menjadi sewa
ALTER TABLE transaksi RENAME TO sewa;
ALTER TABLE mahasiswa RENAME TO siswa;
```

#### DROP
```sql
-- Menghapus tabel (beserta data dan strukturnya)
DROP TABLE nama_tabel;
DROP TABLE mahasiswa;

-- Menghapus database
DROP DATABASE nama_database;
DROP DATABASE perpustakaan;
```

#### TRUNCATE
```sql
-- Menghapus SEMUA DATA dalam tabel, tapi STRUKTUR tabel tetap ada
TRUNCATE TABLE nama_tabel;
TRUNCATE TABLE mahasiswa;
```

> **Perbedaan DROP vs TRUNCATE:**
> - `DROP TABLE` → struktur tabel ikut hilang
> - `TRUNCATE TABLE` → struktur tabel tetap ada, datanya dihapus semua
> - `DELETE FROM` → bisa hapus data tertentu (dengan WHERE)

---

### 3.4 Tipe Data Umum di MySQL

| Tipe Data | Deskripsi | Contoh |
|-----------|-----------|--------|
| `INT` | Bilangan bulat | id, umur |
| `CHAR(n)` | String panjang tetap n karakter | NIM, kode |
| `VARCHAR(n)` | String panjang variabel, max n | nama, email |
| `TEXT` | String panjang (tak terbatas) | deskripsi |
| `DATE` | Tanggal (YYYY-MM-DD) | tanggal_lahir |
| `YEAR` | Tahun saja | angkatan |
| `DECIMAL(p,s)` | Bilangan desimal presisi | harga, gaji |
| `FLOAT` / `DOUBLE` | Bilangan desimal | nilai |

---

### 3.5 Contoh Kasus: Database Rental Mobil

```sql
-- Langkah 1: Buat database
CREATE DATABASE rental;
USE rental;

-- Langkah 2: Buat tabel pelanggan
CREATE TABLE pelanggan (
    id_pelanggan INT PRIMARY KEY AUTO_INCREMENT,
    nama         VARCHAR(100) NOT NULL,
    no_telp      VARCHAR(15),
    alamat       TEXT
);

-- Langkah 3: Buat tabel kendaraan
CREATE TABLE kendaraan (
    id_kendaraan INT PRIMARY KEY AUTO_INCREMENT,
    nama_kendaraan VARCHAR(100),
    plat_nomor     CHAR(10) UNIQUE,
    harga_sewa     DECIMAL(10,2)
);

-- Langkah 4: Buat tabel transaksi
CREATE TABLE transaksi (
    id_transaksi  INT PRIMARY KEY AUTO_INCREMENT,
    id_pelanggan  INT,
    id_kendaraan  INT,
    tanggal_sewa  DATE,
    tanggal_kembali DATE,
    FOREIGN KEY (id_pelanggan) REFERENCES pelanggan(id_pelanggan),
    FOREIGN KEY (id_kendaraan) REFERENCES kendaraan(id_kendaraan)
);

-- Modifikasi: Rename tabel transaksi → sewa
ALTER TABLE transaksi RENAME TO sewa;

-- Modifikasi: Tambah kolom tahun_kendaraan
ALTER TABLE kendaraan ADD tahun_kendaraan DATE;

-- Modifikasi: Ubah tipe data tahun_kendaraan menjadi YEAR
ALTER TABLE kendaraan MODIFY tahun_kendaraan YEAR;
```

---

## 4. Data Manipulation Language I (DML)

### 4.1 Apa itu DML?

**Data Manipulation Language (DML)** adalah perintah SQL untuk mengakses, memanipulasi, dan mengelola **data** (bukan struktur) dalam basis data.

| Perintah | Fungsi |
|----------|--------|
| `INSERT` | Menambahkan data baru ke tabel |
| `SELECT` | Mengambil/menampilkan data |
| `UPDATE` | Mengubah data yang sudah ada |
| `DELETE` | Menghapus data dari tabel |

---

### 4.2 INSERT

```sql
-- Sintaks
INSERT INTO nama_tabel (kolom1, kolom2, ...)
VALUES (nilai1, nilai2, ...);

-- Contoh: insert satu baris
INSERT INTO dokter (nama, spesialis, pengalaman_tahun)
VALUES ('Dr. Andi', 'Kardiologi', 15);

-- Contoh: insert banyak baris sekaligus
INSERT INTO dokter (nama, spesialis, pengalaman_tahun)
VALUES
    ('Dr. Andi',  'Kardiologi',  15),
    ('Dr. Budi',  'Ortopedi',    10),
    ('Dr. Cindy', 'Pediatri',     8),
    ('Dr. Dimas', 'Bedah Umum',  12),
    ('Dr. Erika', 'Neurologi',   11);
```

---

### 4.3 SELECT

#### Seluruh kolom
```sql
-- Sintaks
SELECT * FROM nama_tabel;

-- Contoh
SELECT * FROM dokter;
```

#### Kolom tertentu
```sql
-- Sintaks
SELECT kolom1, kolom2 FROM nama_tabel;

-- Contoh
SELECT spesialis FROM dokter;
SELECT nama, pengalaman_tahun FROM dokter;
```

#### Dengan kondisi (WHERE)
```sql
-- Sintaks
SELECT nama_kolom FROM nama_tabel WHERE kondisi;

-- Contoh
SELECT * FROM dokter WHERE spesialis = 'Pediatri';
SELECT * FROM dokter WHERE pengalaman_tahun > 10;
SELECT * FROM mahasiswa WHERE jurusan = 'Sains Data' AND angkatan = 2022;
```

#### Operator yang bisa digunakan di WHERE

| Operator | Fungsi | Contoh |
|----------|--------|--------|
| `=` | Sama dengan | `WHERE kota = 'Jakarta'` |
| `!=` atau `<>` | Tidak sama dengan | `WHERE status != 'aktif'` |
| `>`, `<`, `>=`, `<=` | Perbandingan | `WHERE gaji > 5000000` |
| `BETWEEN` | Dalam rentang | `WHERE umur BETWEEN 20 AND 30` |
| `IN` | Dalam daftar | `WHERE kota IN ('Jakarta','Bandung')` |
| `LIKE` | Pola string | `WHERE nama LIKE 'A%'` |
| `IS NULL` | Nilai kosong | `WHERE email IS NULL` |
| `AND`, `OR`, `NOT` | Logika gabungan | `WHERE a = 1 AND b = 2` |

---

### 4.4 UPDATE

```sql
-- Sintaks
UPDATE nama_tabel
SET kolom1 = nilai_baru1, kolom2 = nilai_baru2
WHERE kondisi;

-- Contoh: ubah pengalaman Dr. Cindy menjadi 10 tahun
UPDATE dokter SET pengalaman_tahun = 10 WHERE nama = 'Dr. Cindy';

-- Contoh: update beberapa kolom
UPDATE mahasiswa
SET jurusan = 'Teknik Informatika', angkatan = 2023
WHERE nim = '12345678';
```

> ⚠️ **Selalu gunakan WHERE pada UPDATE!** Tanpa WHERE, semua baris akan terupdate.

---

### 4.5 DELETE

```sql
-- Sintaks
DELETE FROM nama_tabel WHERE kondisi;

-- Contoh: hapus dokter spesialis Bedah Umum
DELETE FROM dokter WHERE spesialis = 'Bedah Umum';

-- Contoh: hapus mahasiswa tertentu
DELETE FROM mahasiswa WHERE nim = '12345678';
```

> ⚠️ **Selalu gunakan WHERE pada DELETE!** Tanpa WHERE, semua data akan terhapus.
> 
> ⚠️ Data yang dihapus **tidak boleh memiliki keterkaitan** dengan data di tabel lain (Foreign Key), kecuali ada `ON DELETE CASCADE`.

---

### 4.6 JOIN

**JOIN** adalah perintah untuk **menggabungkan data** dari dua atau lebih tabel berdasarkan relasi antar kolom (biasanya melalui Foreign Key).

#### Jenis-Jenis JOIN

| Jenis JOIN | Deskripsi | Hasilnya |
|------------|-----------|----------|
| `INNER JOIN` | Data yang cocok di **kedua tabel** | Irisan |
| `LEFT JOIN` | Semua data kiri + yang cocok dari kanan | NULL jika kanan tidak cocok |
| `RIGHT JOIN` | Semua data kanan + yang cocok dari kiri | NULL jika kiri tidak cocok |
| `FULL JOIN` | Semua data dari **kedua tabel** | NULL di sisi yang tidak cocok |
| `CROSS JOIN` | Semua kombinasi dari kedua tabel | Cartesian product |

---

#### INNER JOIN
Mengambil **hanya baris yang cocok** di kedua tabel.

```sql
-- Sintaks
SELECT kolom1, kolom2
FROM tabel1
INNER JOIN tabel2
ON tabel1.kolom_kunci = tabel2.kolom_kunci;

-- Contoh: tampilkan nama pelanggan beserta tanggal dan total transaksi
SELECT pelanggan.nama, transaksi.tanggal, transaksi.total_harga
FROM pelanggan
INNER JOIN transaksi ON pelanggan.id = transaksi.id_pelanggan;

-- INNER JOIN bisa ditulis hanya JOIN (default-nya INNER)
SELECT p.nama, t.tanggal, t.total_harga
FROM pelanggan p
JOIN transaksi t ON p.id = t.id_pelanggan;
```

---

#### LEFT JOIN
Mengambil **semua data dari tabel kiri**, dan yang cocok dari kanan. Jika tidak cocok → `NULL`.

```sql
-- Sintaks
SELECT nama_kolom
FROM tabel1
LEFT JOIN tabel2
ON tabel1.kolom = tabel2.kolom;

-- Contoh: tampilkan semua produk, termasuk yang belum pernah ditransaksikan
SELECT produk.id, produk.nama_produk, transaksi.jumlah
FROM produk
LEFT JOIN transaksi ON produk.id = transaksi.id_produk;
```

---

#### RIGHT JOIN
Mengambil **semua data dari tabel kanan**, dan yang cocok dari kiri. Jika tidak cocok → `NULL`.

```sql
-- Sintaks
SELECT kolom1, kolom2
FROM tabel1
RIGHT JOIN tabel2
ON tabel1.kolom = tabel2.kolom;

-- Contoh
SELECT produk.id, produk.nama_produk, transaksi.jumlah
FROM produk
RIGHT JOIN transaksi ON produk.id = transaksi.id_produk;
```

---

#### FULL JOIN
Mengambil **semua data dari kedua tabel**. Bagian yang tidak cocok diisi `NULL`.

> ⚠️ MySQL tidak mendukung `FULL JOIN` secara langsung. Gunakan `UNION` dari `LEFT JOIN` dan `RIGHT JOIN`.

```sql
-- MySQL: Simulasi FULL JOIN dengan UNION
SELECT produk.id, produk.nama_produk, transaksi.jumlah
FROM produk
LEFT JOIN transaksi ON produk.id = transaksi.id_produk

UNION

SELECT produk.id, produk.nama_produk, transaksi.jumlah
FROM produk
RIGHT JOIN transaksi ON produk.id = transaksi.id_produk;
```

---

### 4.7 SUBQUERY (Pengenalan)

**Subquery** adalah sebuah `SELECT` yang berada **di dalam query lain**, bertujuan mengambil data tambahan untuk membantu query utama.

```sql
-- Contoh sederhana: tampilkan karyawan yang gajinya di atas rata-rata
SELECT nama
FROM karyawan
WHERE gaji > (SELECT AVG(gaji) FROM karyawan);
```

**Jenis Subquery:**

| Jenis | Deskripsi |
|-------|-----------|
| **Single-Row Subquery** | Mengembalikan satu baris/nilai → gunakan `=`, `>`, `<` |
| **Multi-Row Subquery** | Mengembalikan lebih dari satu baris → gunakan `IN`, `ANY`, `ALL` |
| **Correlated Subquery** | Mengacu ke kolom dari query utama |
| **Non-Correlated Subquery** | Tidak bergantung pada query utama, bisa dijalankan sendiri |

**Letak Subquery:**
- `SELECT` clause
- `FROM` clause
- `WHERE` clause
- `HAVING` clause

---

## 5. Data Manipulation Language II (Subquery Lanjutan)

### 5.1 SUBQUERY di SELECT Clause

Subquery di dalam `SELECT` digunakan untuk **menampilkan nilai tambahan** dari tabel lain sebagai kolom baru.

```sql
-- Contoh: tampilkan first_name dan nama departemennya
SELECT 
    first_name,
    (SELECT department_name 
     FROM departments 
     WHERE departments.department_id = employees.department_id
    ) AS department_name
FROM employees;
```

> Subquery dalam SELECT harus mengembalikan **tepat satu nilai** per baris.

---

### 5.2 SUBQUERY di FROM Clause

Subquery dalam `FROM` menghasilkan **tabel sementara (derived table/alias)** yang digunakan oleh query utama.

```sql
-- Sintaks umum
SELECT *
FROM (SELECT ... FROM ... WHERE ...) AS alias_tabel;

-- Contoh: ambil departemen dengan rata-rata gaji > 8000
SELECT dept, rata_rata_gaji
FROM (
    SELECT department_id AS dept, AVG(salary) AS rata_rata_gaji
    FROM employees
    GROUP BY department_id
) AS sub
WHERE rata_rata_gaji > 8000;
```

---

### 5.3 SUBQUERY di WHERE Clause

Subquery dalam `WHERE` digunakan untuk **menyaring baris** berdasarkan hasil query lain.

```sql
-- Contoh: tampilkan karyawan di departemen yang berlokasi di atas location_id 1500
SELECT first_name
FROM employees
WHERE department_id IN (
    SELECT department_id 
    FROM departments 
    WHERE location_id > 1500
);
```

---

### 5.4 SUBQUERY di HAVING Clause

`HAVING` memfilter hasil **setelah pengelompokan (GROUP BY)**. Subquery di HAVING berguna untuk membandingkan hasil agregat.

```sql
-- Contoh: tampilkan produk dengan total penjualan di atas rata-rata
SELECT product_id, SUM(subtotal) AS total_per_product
FROM order_items
GROUP BY product_id
HAVING SUM(subtotal) > (
    SELECT AVG(total_sub)
    FROM (
        SELECT SUM(subtotal) AS total_sub
        FROM order_items
        GROUP BY product_id
    ) AS subquery
);
```

> **Perbedaan WHERE vs HAVING:**
> - `WHERE` → filter **sebelum** pengelompokan (tidak bisa pakai fungsi agregat)
> - `HAVING` → filter **setelah** pengelompokan (bisa pakai SUM, AVG, COUNT, dll)

---

### 5.5 Fungsi Agregat Penting

| Fungsi | Deskripsi | Contoh |
|--------|-----------|--------|
| `COUNT(*)` | Hitung jumlah baris | `SELECT COUNT(*) FROM mahasiswa;` |
| `COUNT(kolom)` | Hitung baris non-NULL | `SELECT COUNT(email) FROM mahasiswa;` |
| `SUM(kolom)` | Jumlah total nilai | `SELECT SUM(gaji) FROM karyawan;` |
| `AVG(kolom)` | Rata-rata nilai | `SELECT AVG(ipk) FROM mahasiswa;` |
| `MIN(kolom)` | Nilai terkecil | `SELECT MIN(harga) FROM produk;` |
| `MAX(kolom)` | Nilai terbesar | `SELECT MAX(harga) FROM produk;` |
| `STDDEV(kolom)` | Simpangan baku | `SELECT STDDEV(nilai) FROM nilai;` |

---

### 5.6 COUNT, DISTINCT, dan COUNT(DISTINCT)

```sql
-- COUNT: hitung jumlah baris
SELECT COUNT(category_name) FROM categories;

-- DISTINCT: tampilkan nilai unik saja (tanpa duplikat)
SELECT DISTINCT(category_name) FROM categories;

-- COUNT(DISTINCT): hitung jumlah nilai unik
SELECT COUNT(DISTINCT status) FROM orders;
```

---

### 5.7 GROUP BY

`GROUP BY` digunakan untuk mengelompokkan baris yang memiliki nilai sama pada kolom tertentu.

```sql
-- Sintaks
SELECT kolom, fungsi_agregat(kolom)
FROM tabel
GROUP BY kolom;

-- Contoh: hitung jumlah mahasiswa per jurusan
SELECT jurusan, COUNT(*) AS jumlah_mahasiswa
FROM mahasiswa
GROUP BY jurusan;

-- Contoh: total penjualan per kategori
SELECT kategori, SUM(harga) AS total_penjualan
FROM produk
GROUP BY kategori;
```

---

### 5.8 Kombinasi JOIN + Subquery + HAVING

```sql
-- Contoh kompleks: total penjualan per kategori di atas 1 juta
SELECT 
    categories.category_name, 
    SUM(order_items.subtotal) AS total_penjualan
FROM order_items
JOIN products ON order_items.product_id = products.product_id
JOIN categories ON products.category_id = categories.category_id
GROUP BY categories.category_name
HAVING SUM(order_items.subtotal) > 1000000;
```

---

### 5.9 ORDER BY

```sql
-- Urutkan hasil dari kecil ke besar (ASC = default)
SELECT nama, gaji FROM karyawan ORDER BY gaji ASC;

-- Urutkan dari besar ke kecil
SELECT nama, gaji FROM karyawan ORDER BY gaji DESC;

-- Urutkan berdasarkan beberapa kolom
SELECT jurusan, nama FROM mahasiswa ORDER BY jurusan ASC, nama ASC;
```

---

## 6. EDA dalam SQL & Data Control Language (DCL)

### 6.1 Apa itu EDA?

**Exploratory Data Analysis (EDA)** adalah tahap awal dalam proses analisis data yang bertujuan untuk:

- Memahami **struktur data** — tipe data, jumlah data, missing values
- Mendapatkan **insight awal** — tren, pola, distribusi
- Mendeteksi **anomali/outlier** — nilai yang menyimpang
- Menguji **asumsi statistik** sebelum membangun model
- Menentukan **transformasi data** — normalisasi, encoding, dll

**EDA dalam basis data** adalah proses mengeksplorasi data langsung dari DBMS sebelum diproses lebih lanjut.

---

### 6.2 Aspek EDA dalam SQL

#### 6.2.1 Struktur Tabel dan Skema

```sql
-- Melihat struktur tabel (kolom, tipe data, constraint)
DESCRIBE nama_tabel;
DESCRIBE obat;

-- Hitung jumlah baris
SELECT COUNT(*) AS jumlah_obat FROM obat;

-- Melihat nilai unik pada kolom
SELECT DISTINCT jenis FROM obat;
```

---

#### 6.2.2 Statistik Numerik

Memeriksa nilai maksimum, minimum, rata-rata, dan simpangan baku.

```sql
-- Contoh: statistik lengkap pada kolom harga tabel obat
SELECT 
    MIN(harga)    AS harga_terendah,
    MAX(harga)    AS harga_tertinggi,
    AVG(harga)    AS rata_rata,
    STDDEV(harga) AS deviasi
FROM obat;

-- Statistik pada kolom gaji
SELECT
    MIN(gaji)    AS gaji_min,
    MAX(gaji)    AS gaji_max,
    AVG(gaji)    AS gaji_rata,
    STDDEV(gaji) AS gaji_std
FROM karyawan;
```

---

#### 6.2.3 Distribusi Kategorik

Memeriksa distribusi tiap kategori pada kolom bertipe kategorik.

```sql
-- Sintaks
SELECT kolom_kategori, COUNT(*) AS jumlah
FROM tabel
GROUP BY kolom_kategori;

-- Contoh: distribusi jumlah obat per kategori
SELECT kategori, COUNT(*) AS jumlah_obat
FROM obat
GROUP BY kategori;

-- Contoh: distribusi mahasiswa per jurusan
SELECT jurusan, COUNT(*) AS jumlah
FROM mahasiswa
GROUP BY jurusan
ORDER BY jumlah DESC;
```

---

#### 6.2.4 Cek Missing Value (NULL)

```sql
-- Metode 1: hitung NULL per kolom
SELECT 
    SUM(CASE WHEN id_obat IS NULL THEN 1 ELSE 0 END)             AS null_id_obat,
    SUM(CASE WHEN tanggal_transaksi IS NULL THEN 1 ELSE 0 END)   AS null_tanggal,
    SUM(CASE WHEN jumlah IS NULL THEN 1 ELSE 0 END)              AS null_jumlah,
    SUM(CASE WHEN total_harga IS NULL THEN 1 ELSE 0 END)         AS null_total_harga,
    SUM(CASE WHEN jenis_transaksi IS NULL THEN 1 ELSE 0 END)     AS null_jenis
FROM transaksi;

-- Metode 2: tampilkan baris yang mengandung NULL
SELECT *
FROM transaksi
WHERE id_obat IS NULL
   OR tanggal_transaksi IS NULL
   OR jumlah IS NULL
   OR total_harga IS NULL
   OR jenis_transaksi IS NULL;
```

> **CASE WHEN** adalah ekspresi kondisional:
> ```sql
> CASE WHEN kondisi THEN nilai_jika_benar ELSE nilai_jika_salah END
> ```

---

#### 6.2.5 Deteksi Duplikasi

Mendeteksi data ganda berdasarkan kombinasi kolom tertentu.

```sql
-- Temukan baris duplikat pada tabel transaksi
SELECT 
    id_obat, 
    tanggal_transaksi, 
    jumlah, 
    total_harga, 
    jenis_transaksi,
    COUNT(*) AS jumlah_duplikat
FROM transaksi
GROUP BY 
    id_obat, 
    tanggal_transaksi, 
    jumlah, 
    total_harga, 
    jenis_transaksi
HAVING COUNT(*) > 1;
```

> Kunci: `GROUP BY` kolom yang dicurigai duplikat, lalu `HAVING COUNT(*) > 1`

---

#### 6.2.6 Deteksi Outlier

Mendeteksi pencilan menggunakan metode **mean ± 2×standar deviasi**.

```sql
-- Deteksi outlier pada harga obat (di luar mean ± 2*stddev)
SELECT *
FROM obat
WHERE harga > (SELECT AVG(harga) + 2 * STDDEV(harga) FROM obat)
   OR harga < (SELECT AVG(harga) - 2 * STDDEV(harga) FROM obat);
```

> **Logika:** Data dianggap outlier jika berada lebih dari 2 standar deviasi dari rata-rata.

---

#### 6.2.7 Analisis Waktu (Time Series)

Menganalisa data berdasarkan dimensi waktu (tahun, bulan, dll).

```sql
-- Penjualan obat per bulan per tahun per jenis transaksi
SELECT 
    YEAR(tanggal_transaksi)  AS tahun,
    MONTH(tanggal_transaksi) AS bulan,
    jenis_transaksi,
    SUM(total_harga)         AS total_uang
FROM transaksi
GROUP BY tahun, bulan, jenis_transaksi
ORDER BY tahun, bulan, jenis_transaksi;
```

**Fungsi waktu berguna lainnya:**

| Fungsi | Deskripsi | Contoh |
|--------|-----------|--------|
| `YEAR(date)` | Ambil tahun | `YEAR('2024-01-15')` → 2024 |
| `MONTH(date)` | Ambil bulan | `MONTH('2024-01-15')` → 1 |
| `DAY(date)` | Ambil hari | `DAY('2024-01-15')` → 15 |
| `DAYNAME(date)` | Nama hari | `DAYNAME('2024-01-15')` → Monday |
| `MONTHNAME(date)` | Nama bulan | `MONTHNAME('2024-01-15')` → January |
| `DATE_FORMAT(date, fmt)` | Format tanggal | `DATE_FORMAT(tgl, '%Y-%m')` |
| `NOW()` | Tanggal & waktu sekarang | - |

---

### 6.3 Data Control Language (DCL)

**Data Control Language (DCL)** adalah bagian SQL yang digunakan untuk **mengatur kontrol akses** terhadap data dalam basis data.

DCL berperan penting dalam:
- **Keamanan** basis data
- **Otorisasi** pengguna
- **Pengelolaan hak akses** (user privileges)

**Hanya dua perintah utama:**

| Perintah | Fungsi |
|----------|--------|
| `GRANT` | Memberikan hak akses kepada pengguna |
| `REVOKE` | Mencabut hak akses dari pengguna |

---

#### GRANT

```sql
-- Sintaks
GRANT hak_akses ON nama_database.nama_tabel TO 'username'@'host';

-- Contoh: berikan akses SELECT pada tabel mahasiswa ke user 'budi'
GRANT SELECT ON siakad.mahasiswa TO 'budi'@'localhost';

-- Berikan akses SELECT dan INSERT
GRANT SELECT, INSERT ON siakad.* TO 'budi'@'localhost';

-- Berikan semua hak akses
GRANT ALL PRIVILEGES ON siakad.* TO 'admin'@'localhost';

-- Buat user baru sekaligus berikan hak akses
CREATE USER 'budi'@'localhost' IDENTIFIED BY 'password123';
GRANT SELECT, INSERT ON siakad.* TO 'budi'@'localhost';
FLUSH PRIVILEGES;
```

---

#### REVOKE

```sql
-- Sintaks
REVOKE hak_akses ON nama_database.nama_tabel FROM 'username'@'host';

-- Contoh: cabut hak INSERT dari user 'budi'
REVOKE INSERT ON siakad.* FROM 'budi'@'localhost';

-- Cabut semua hak akses
REVOKE ALL PRIVILEGES ON siakad.* FROM 'budi'@'localhost';
```

---

#### Jenis-Jenis Hak Akses (Privileges)

| Privilege | Deskripsi |
|-----------|-----------|
| `SELECT` | Boleh membaca data |
| `INSERT` | Boleh menambah data |
| `UPDATE` | Boleh mengubah data |
| `DELETE` | Boleh menghapus data |
| `CREATE` | Boleh membuat tabel/database |
| `DROP` | Boleh menghapus tabel/database |
| `ALTER` | Boleh mengubah struktur tabel |
| `ALL PRIVILEGES` | Semua hak akses |

---

## 7. Ringkasan Perintah SQL

### DDL (Data Definition Language)

```sql
CREATE DATABASE db_name;
CREATE TABLE nama_tabel (kolom tipe_data [CONSTRAINT], ...);
ALTER TABLE nama_tabel ADD kolom tipe_data;
ALTER TABLE nama_tabel MODIFY kolom tipe_data_baru;
ALTER TABLE nama_tabel DROP COLUMN kolom;
ALTER TABLE nama_tabel RENAME TO nama_baru;
DROP TABLE nama_tabel;
DROP DATABASE nama_db;
TRUNCATE TABLE nama_tabel;
```

### DML (Data Manipulation Language)

```sql
INSERT INTO tabel (kol1, kol2) VALUES (val1, val2);
SELECT * FROM tabel;
SELECT kol1, kol2 FROM tabel WHERE kondisi;
SELECT kol, COUNT(*) FROM tabel GROUP BY kol HAVING kondisi ORDER BY kol;
UPDATE tabel SET kol = val WHERE kondisi;
DELETE FROM tabel WHERE kondisi;
```

### JOIN

```sql
-- INNER JOIN
SELECT ... FROM t1 INNER JOIN t2 ON t1.fk = t2.pk;

-- LEFT JOIN
SELECT ... FROM t1 LEFT JOIN t2 ON t1.fk = t2.pk;

-- RIGHT JOIN
SELECT ... FROM t1 RIGHT JOIN t2 ON t1.fk = t2.pk;
```

### SUBQUERY

```sql
-- Di WHERE
SELECT ... FROM t WHERE kolom IN (SELECT ... FROM t2);

-- Di FROM
SELECT ... FROM (SELECT ... FROM t) AS alias;

-- Di SELECT
SELECT kol, (SELECT ... FROM t2 WHERE ...) AS alias FROM t1;

-- Di HAVING
SELECT ... FROM t GROUP BY kol HAVING agg > (SELECT AVG(kol) FROM t);
```

### DCL (Data Control Language)

```sql
GRANT SELECT, INSERT ON db.tabel TO 'user'@'host';
REVOKE INSERT ON db.tabel FROM 'user'@'host';
```

### EDA SQL

```sql
DESCRIBE tabel;
SELECT COUNT(*) FROM tabel;
SELECT DISTINCT kolom FROM tabel;
SELECT MIN(k), MAX(k), AVG(k), STDDEV(k) FROM tabel;
SELECT kat, COUNT(*) FROM tabel GROUP BY kat;
SELECT SUM(CASE WHEN kol IS NULL THEN 1 ELSE 0 END) AS null_count FROM tabel;
```

---

## 8. Latihan Soal Essay

### Soal 1 — Transformasi ERD
**Pertanyaan:** Jelaskan perbedaan antara entitas kuat dan entitas lemah, beserta contohnya. Bagaimana cara mentransformasikan relasi N:N ke dalam basis data fisik?

**Jawaban Panduan:**
- Entitas kuat: memiliki PK sendiri, dapat berdiri sendiri (contoh: Mahasiswa dengan NIM)
- Entitas lemah: tidak punya PK sendiri, butuh entitas kuat (contoh: DetailFaktur butuh NoFaktur dari Faktur)
- Relasi N:N → buat tabel relasi baru berisi FK dari kedua entitas + atribut relasi jika ada

---

### Soal 2 — DDL
**Pertanyaan:** Buat struktur database untuk sistem perpustakaan yang memiliki tabel `buku`, `anggota`, dan `peminjaman`. Sertakan constraint yang sesuai.

```sql
CREATE DATABASE perpustakaan;
USE perpustakaan;

CREATE TABLE buku (
    id_buku     INT PRIMARY KEY AUTO_INCREMENT,
    judul       VARCHAR(200) NOT NULL,
    penulis     VARCHAR(100),
    tahun_terbit YEAR,
    stok        INT DEFAULT 0
);

CREATE TABLE anggota (
    id_anggota  INT PRIMARY KEY AUTO_INCREMENT,
    nama        VARCHAR(100) NOT NULL,
    email       VARCHAR(100) UNIQUE,
    no_hp       VARCHAR(15)
);

CREATE TABLE peminjaman (
    id_pinjam       INT PRIMARY KEY AUTO_INCREMENT,
    id_buku         INT NOT NULL,
    id_anggota      INT NOT NULL,
    tanggal_pinjam  DATE NOT NULL,
    tanggal_kembali DATE,
    status          VARCHAR(20) DEFAULT 'dipinjam',
    FOREIGN KEY (id_buku)    REFERENCES buku(id_buku),
    FOREIGN KEY (id_anggota) REFERENCES anggota(id_anggota)
);
```

---

### Soal 3 — DML & JOIN
**Pertanyaan:** Dengan tabel `mahasiswa (nim, nama, jurusan)` dan `krs (nim, kode_matkul, nilai)`, tulis query untuk menampilkan nama mahasiswa beserta semua mata kuliah yang diambil, termasuk mahasiswa yang belum mengambil matakuliah apapun.

```sql
-- Gunakan LEFT JOIN agar mahasiswa yang belum ambil matkul tetap muncul
SELECT m.nama, k.kode_matkul, k.nilai
FROM mahasiswa m
LEFT JOIN krs k ON m.nim = k.nim
ORDER BY m.nama;
```

---

### Soal 4 — Subquery
**Pertanyaan:** Tampilkan nama mahasiswa yang IPK-nya di atas rata-rata IPK seluruh mahasiswa.

```sql
SELECT nama
FROM mahasiswa
WHERE ipk > (SELECT AVG(ipk) FROM mahasiswa);
```

---

### Soal 5 — EDA SQL
**Pertanyaan:** Anda memiliki tabel `penjualan (id, id_produk, tanggal, jumlah, harga_satuan)`. Tulis query untuk:
a) Cek missing value
b) Deteksi duplikat
c) Statistik numerik harga_satuan
d) Analisis penjualan bulanan

```sql
-- a) Cek missing value
SELECT
    SUM(CASE WHEN id IS NULL THEN 1 ELSE 0 END)          AS null_id,
    SUM(CASE WHEN id_produk IS NULL THEN 1 ELSE 0 END)   AS null_id_produk,
    SUM(CASE WHEN tanggal IS NULL THEN 1 ELSE 0 END)     AS null_tanggal,
    SUM(CASE WHEN jumlah IS NULL THEN 1 ELSE 0 END)      AS null_jumlah,
    SUM(CASE WHEN harga_satuan IS NULL THEN 1 ELSE 0 END) AS null_harga
FROM penjualan;

-- b) Deteksi duplikat
SELECT id_produk, tanggal, jumlah, harga_satuan, COUNT(*) AS jumlah_duplikat
FROM penjualan
GROUP BY id_produk, tanggal, jumlah, harga_satuan
HAVING COUNT(*) > 1;

-- c) Statistik numerik
SELECT
    MIN(harga_satuan)    AS min_harga,
    MAX(harga_satuan)    AS max_harga,
    AVG(harga_satuan)    AS avg_harga,
    STDDEV(harga_satuan) AS std_harga
FROM penjualan;

-- d) Analisis bulanan
SELECT
    YEAR(tanggal)  AS tahun,
    MONTH(tanggal) AS bulan,
    SUM(jumlah * harga_satuan) AS total_penjualan,
    COUNT(*)                   AS jumlah_transaksi
FROM penjualan
GROUP BY tahun, bulan
ORDER BY tahun, bulan;
```

---

### Soal 6 — DCL
**Pertanyaan:** Jelaskan perbedaan GRANT dan REVOKE, dan berikan contoh pemberian hak akses READ-ONLY kepada user `laporan` pada database `siakad`.

```sql
-- Buat user
CREATE USER 'laporan'@'localhost' IDENTIFIED BY 'password_aman';

-- Berikan hak akses SELECT saja (read-only)
GRANT SELECT ON siakad.* TO 'laporan'@'localhost';

-- Terapkan perubahan
FLUSH PRIVILEGES;

-- Jika ingin mencabut kembali:
REVOKE SELECT ON siakad.* FROM 'laporan'@'localhost';
```

---

### Soal 7 — Kodifikasi
**Pertanyaan:** Jelaskan tiga jenis kodifikasi dalam basis data beserta contoh penerapannya.

**Jawaban Panduan:**
1. **Sekuensial** — kode berurutan: `A=Sempurna, B=Baik, C=Cukup` untuk nilai
2. **Mnemonic** — singkatan bermakna: `L=Laki-Laki, P=Perempuan` untuk jenis kelamin
3. **Blok** — kode terstruktur dalam segmen: NIM `xx-x-xx-xxx` dimana setiap segmen memiliki makna (kode jurusan, fakultas, angkatan, nomor urut)

---

*Good luck UAS-nya, Fitra! 💪*
