# CHEATSHEET METODE NUMERIK — PANDUAN BERTAHAN HIDUP UAS

> **Cara pakai:** Baca soal → cek Bagian 1 (Peta Materi) → cek rumus di Bagian 4 → ikuti langkah di Bagian 5.

---

# 1. PETA MATERI

| Jika soal meminta … | Gunakan | Alasan |
|---|---|---|
| Turunan dari tabel data (1 titik, beberapa orde) | **Selisih Pusat** O(h²), O(h⁴), O(h⁶) | Rumus simetris = akurasi lebih tinggi dari maju/mundur |
| Meningkatkan akurasi turunan secara bertahap | **Ekstrapolasi Richardson** | Eliminasi suku galat secara rekursif tanpa data baru |
| Aproksimasi turunan + estimasi galat pemotongan | **Richardson** (dari tabel selisih pusat) | Galat pemotongan terbaca langsung dari perbedaan antar level |
| Integral sederhana, cepat, tanpa syarat | **Riemann** (Kiri/Kanan/Tengah) | Paling mudah, cocok untuk perbandingan galat |
| Integral dengan akurasi moderat | **Trapesium Komposit** | Mudah, O(h²), cocok untuk n sembarang |
| Integral dengan akurasi lebih tinggi | **Simpson 1/3 Komposit** | O(h⁴), jauh lebih akurat dari Trapesium, syarat: n genap |
| Integral dengan akurasi sangat tinggi | **Romberg** | Kombinasi Trapesium + Richardson, akurasi bisa sangat tinggi |
| ODE, akurasi rendah, cepat | **Euler** | Paling sederhana, cocok jika h sangat kecil |
| ODE, akurasi sedang (orde 2) | **Heun / RK2 Modified Euler** | Prediktor-korektor, lebih baik dari Euler |
| ODE, titik tengah (orde 2) | **RK2 Midpoint** | Gunakan titik tengah interval untuk taksiran lebih baik |
| ODE, akurasi tinggi (orde 4) | **RK4** | Standar industri untuk ODE, keseimbangan akurasi & efisiensi |
| Bandingkan galat antar metode | Hitung **galat absolut & relatif** | Metode terbaik = galat absolut terkecil terhadap nilai eksak |

---

# 2. KAMUS KATA KUNCI UAS

| Kata pada soal | Yang harus dipikirkan |
|---|---|
| *"aproksimasi turunan"* | Selisih pusat, pilih orde sesuai permintaan |
| *"selisih pusat orde O(h²/h⁴/h⁶)"* | Ada rumus spesifik untuk tiap orde — lihat Bagian 4 |
| *"titik tengah"* | Dalam diferensiasi: titik yang diapit. Dalam RK2: gunakan k₁ di tengah langkah |
| *"komposit"* | Bagi interval menjadi n subinterval, terapkan aturan berulang |
| *"nilai eksak"* | Digunakan sebagai pembanding untuk menghitung galat |
| *"galat absolut"* | |nilai eksak − nilai hampiran| |
| *"galat relatif"* | galat absolut / nilai eksak |
| *"galat relatif persen"* | (galat relatif) × 100% |
| *"orde kesalahan"* | O(hⁿ) — makin besar n makin akurat saat h kecil |
| *"galat pemotongan / truncation error"* | Galat akibat memotong deret Taylor |
| *"Ekstrapolasi Richardson"* | Eliminasi galat O(h²) → O(h⁴) → O(h⁶) secara rekursif |
| *"integrasi Romberg"* | Tabel trapesium untuk n=1,2,4,8,… lalu aplikasikan Richardson |
| *"Runge-Kutta / RK"* | ODE orde tinggi: RK2 (Midpoint/Heun) atau RK4 |
| *"Euler"* | ODE paling sederhana, satu evaluasi f per langkah |
| *"Heun / Modified Euler"* | RK2 dengan prediksi + koreksi |
| *"Midpoint Method"* | RK2 dengan titik tengah, bukan prediktor-korektor |
| *"y(0) = …"* | Kondisi awal ODE, titik mulai iterasi |
| *"n subinterval"* | Jumlah pembagian interval untuk metode integral |
| *"h = …"* | Ukuran langkah: h = (b−a)/n untuk integral, langkah konstan untuk ODE |
| *"R_{i,j}"* | Notasi tabel Romberg baris ke-i, kolom ke-j |

---

# 3. RINGKASAN KONSEP DASAR

## Diferensiasi Numerik (Selisih Pusat)
- **Tujuan:** Estimasi f'(x) dari tabel data diskret
- **Kapan dipakai:** Data diberikan dalam tabel, bukan fungsi analitik
- **Input:** Tabel x dan f(x), jarak h antar titik
- **Output:** f'(x₀) dengan galat O(h²), O(h⁴), atau O(h⁶)
- **Prinsip:** Gabungkan titik-titik simetris di kiri dan kanan x₀

## Ekstrapolasi Richardson
- **Tujuan:** Meningkatkan orde akurasi dari hasil yang sudah ada
- **Kapan dipakai:** Ada dua atau lebih hampiran dengan h berbeda, ingin eliminasi galat
- **Input:** D(h) dan D(h/2), atau nilai selisih pusat dengan h berbeda
- **Output:** Hampiran baru berorde lebih tinggi (O(h⁴), O(h⁶), …)
- **Prinsip:** Kombinasi linear berbobot yang menghapus suku galat terendah

## Riemann
- **Tujuan:** Aproksimasi integral dengan penjumlahan persegi panjang
- **Kapan dipakai:** Soal meminta perbandingan Kiri/Kanan/Tengah
- **Input:** Fungsi f(x), batas a dan b, jumlah subinterval n
- **Output:** Perkiraan ∫f(x)dx
- **Kiri & Kanan kurang akurat; Tengah lebih akurat (O(h²))**

## Trapesium Komposit
- **Tujuan:** Aproksimasi integral dengan luas trapesium
- **Kapan dipakai:** Perlu akurasi O(h²), n sembarang
- **Input:** f(x), a, b, n (jumlah subinterval)
- **Output:** Hampiran ∫f(x)dx

## Simpson 1/3 Komposit
- **Tujuan:** Aproksimasi integral dengan akurasi O(h⁴)
- **Kapan dipakai:** Soal meminta Simpson 1/3; **n harus genap**
- **Input:** f(x), a, b, n genap
- **Output:** Hampiran ∫f(x)dx lebih akurat dari Trapesium

## Romberg
- **Tujuan:** Integral dengan akurasi sangat tinggi menggunakan Richardson bertingkat
- **Kapan dipakai:** Soal meminta tabel Romberg R_{i,j}
- **Input:** f(x), a, b; hitung Trapesium untuk n=1,2,4,8,16
- **Output:** Tabel R dimana R_{k,k} semakin akurat

## Euler
- **Tujuan:** Solusi ODE sederhana, orde pertama
- **Kapan dipakai:** Soal meminta metode Euler, h kecil
- **Input:** f(t,y), y₀, h, jumlah langkah
- **Output:** Aproksimasi y pada titik-titik berikutnya

## Heun (RK2 Modified Euler)
- **Tujuan:** ODE dengan akurasi O(h²) menggunakan prediktor-korektor
- **Kapan dipakai:** Soal meminta Heun atau Modified Euler
- **Input:** f(t,y), y₀, h
- **Output:** y hampiran lebih akurat dari Euler

## RK2 Midpoint
- **Tujuan:** ODE orde 2 menggunakan titik tengah langkah
- **Kapan dipakai:** Soal secara spesifik meminta "Midpoint Method"
- **Input:** f(t,y), y₀, h
- **Output:** y hampiran, berbeda dari Heun meski sama-sama RK2

## RK4
- **Tujuan:** ODE dengan akurasi O(h⁴), standar baku
- **Kapan dipakai:** Soal meminta RK4 atau akurasi tertinggi pada ODE
- **Input:** f(t,y), y₀, h
- **Output:** y hampiran paling akurat di antara metode eksplisit sederhana

---

# 4. RUMUS + ARTI SIMBOL

---

### SELISIH PUSAT O(h²)
**Tujuan:** Estimasi f'(x₀) dari dua titik terdekat

$$f'(x_0) \approx \frac{f(x_0+h) - f(x_0-h)}{2h}$$

| Simbol | Arti |
|---|---|
| x₀ | Titik yang dicari turunannya |
| h | Jarak antar titik data |
| f(x₀±h) | Nilai fungsi satu langkah di kiri/kanan x₀ |

**Orde Akurasi:** O(h²)
**Catatan:** Pakai f(x₀+h) dan f(x₀−h), bukan titik lebih jauh.

---

### SELISIH PUSAT O(h⁴)
**Tujuan:** Estimasi f'(x₀) lebih akurat menggunakan 4 titik

$$f'(x_0) \approx \frac{-f(x_0+2h) + 8f(x_0+h) - 8f(x_0-h) + f(x_0-2h)}{12h}$$

| Simbol | Arti |
|---|---|
| x₀ | Titik yang dicari turunannya |
| h | Jarak antar titik data |
| f(x₀±h), f(x₀±2h) | Nilai di 1 dan 2 langkah kiri/kanan x₀ |

**Orde Akurasi:** O(h⁴)
**Catatan:** Bobot: −1, +8, (−8), +1 dibagi 12h.

---

### SELISIH PUSAT O(h⁶)
**Tujuan:** Estimasi f'(x₀) paling akurat menggunakan 6 titik

$$f'(x_0) \approx \frac{f(x_0-3h) - 9f(x_0-2h) + 45f(x_0-h) - 45f(x_0+h) + 9f(x_0+2h) - f(x_0+3h)}{60h}$$

| Simbol | Arti |
|---|---|
| x₀ | Titik yang dicari turunannya |
| h | Jarak antar titik data |
| f(x₀±kh) | Nilai fungsi k langkah dari x₀, k=1,2,3 |

**Orde Akurasi:** O(h⁶)
**Catatan:** Bobot: +1, −9, +45, (−45), +9, −1 dibagi 60h. Perlu 3 titik di kiri dan kanan.

---

### EKSTRAPOLASI RICHARDSON (untuk Diferensiasi)
**Tujuan:** Eliminasi suku galat secara bertahap untuk meningkatkan orde

Level 1 (eliminasi O(h²) → O(h⁴)):
$$D_1 = \frac{4 \cdot D(h/2) - D(h)}{3}$$

Level 2 (eliminasi O(h⁴) → O(h⁶)):
$$D_2 = \frac{16 \cdot D_1(h/2) - D_1(h)}{15}$$

Level 3 (eliminasi O(h⁶) → O(h⁸)):
$$D_3 = \frac{64 \cdot D_2(h/2) - D_2(h)}{63}$$

**Rumus umum eliminasi dari O(h^{2k}) ke O(h^{2k+2}):**
$$D_{baru} = \frac{4^k \cdot D(h_{kecil}) - D(h_{besar})}{4^k - 1}$$

| Simbol | Arti |
|---|---|
| D(h) | Hampiran turunan dengan langkah h |
| D(h/2) | Hampiran turunan dengan langkah lebih kecil |
| k | Level Richardson (k=1,2,3,...) |

**Catatan:** Untuk soal galat pemotongan O(h²/h⁴/h⁶), justru Richardson digunakan untuk *memperoleh* nilai lebih akurat sebagai referensi. Galat pemotongan ≈ selisih antar level.

---

### RIEMANN KIRI
$$\int_a^b f(x)\,dx \approx h \sum_{i=0}^{n-1} f(x_i)$$

**Kanan:**
$$\int_a^b f(x)\,dx \approx h \sum_{i=1}^{n} f(x_i)$$

**Tengah:**
$$\int_a^b f(x)\,dx \approx h \sum_{i=0}^{n-1} f\!\left(\frac{x_i + x_{i+1}}{2}\right)$$

| Simbol | Arti |
|---|---|
| h = (b−a)/n | Lebar tiap subinterval |
| xᵢ = a + i·h | Titik ke-i pada partisi |
| n | Jumlah subinterval |

**Orde Akurasi:** Kiri/Kanan O(h), Tengah O(h²)

---

### TRAPESIUM KOMPOSIT
$$\int_a^b f(x)\,dx \approx \frac{h}{2}\left[f(x_0) + 2\sum_{i=1}^{n-1}f(x_i) + f(x_n)\right]$$

| Simbol | Arti |
|---|---|
| h = (b−a)/n | Lebar subinterval |
| x₀ = a, xₙ = b | Titik ujung |
| f(xᵢ), i=1..n-1 | Titik-titik interior (dikalikan 2) |

**Orde Akurasi:** O(h²)

---

### SIMPSON 1/3 KOMPOSIT
$$\int_a^b f(x)\,dx \approx \frac{h}{3}\left[f(x_0) + 4\sum_{\text{ganjil}}f(x_i) + 2\sum_{\text{genap}}f(x_i) + f(x_n)\right]$$

Pola koefisien: **1, 4, 2, 4, 2, …, 4, 1**

| Simbol | Arti |
|---|---|
| h = (b−a)/n | Lebar subinterval |
| n | Harus **genap** |
| ganjil | Indeks 1, 3, 5, … (koef = 4) |
| genap | Indeks 2, 4, 6, … (koef = 2) |

**Orde Akurasi:** O(h⁴)
**SYARAT KRITIS: n harus genap!**

---

### ROMBERG
**Tabel Romberg R_{i,j}:**

Kolom pertama = Trapesium komposit:
$$R_{i,1} = T(h_i), \quad h_i = \frac{b-a}{2^{i-1}}, \quad i=1,2,3,4,5$$

Kolom berikutnya = Richardson:
$$R_{i,j} = \frac{4^{j-1} \cdot R_{i,j-1} - R_{i-1,j-1}}{4^{j-1} - 1}$$

| Simbol | Arti |
|---|---|
| R_{i,j} | Elemen baris i, kolom j tabel Romberg |
| hᵢ | Ukuran langkah pada baris ke-i |
| 4^{j-1} | Faktor Richardson untuk kolom ke-j |

**Orde Akurasi:** R_{i,i} memiliki orde O(h^{2i})
**Catatan:** R_{1,1} = Trapesium n=1; R_{2,1} = Trapesium n=2; dst.

---

### EULER
$$y_{i+1} = y_i + h \cdot f(t_i, y_i)$$

| Simbol | Arti |
|---|---|
| yᵢ | Nilai y pada langkah ke-i |
| tᵢ | Nilai t pada langkah ke-i |
| h | Ukuran langkah |
| f(t,y) | Fungsi dari ODE y' = f(t,y) |

**Orde Akurasi:** O(h)

---

### HEUN (RK2 — Modified Euler / Prediktor-Korektor)
$$k_1 = f(t_i, y_i)$$
$$k_2 = f(t_i + h,\; y_i + h \cdot k_1)$$
$$y_{i+1} = y_i + \frac{h}{2}(k_1 + k_2)$$

| Simbol | Arti |
|---|---|
| k₁ | Kemiringan di titik awal |
| k₂ | Kemiringan di titik akhir (prediksi Euler) |
| h | Ukuran langkah |

**Orde Akurasi:** O(h²)
**Catatan:** k₂ menggunakan prediksi Euler (y + h·k₁), bukan nilai tepat.

---

### RK2 MIDPOINT
$$k_1 = f(t_i, y_i)$$
$$k_2 = f\!\left(t_i + \frac{h}{2},\; y_i + \frac{h}{2} \cdot k_1\right)$$
$$y_{i+1} = y_i + h \cdot k_2$$

| Simbol | Arti |
|---|---|
| k₁ | Kemiringan di titik awal |
| k₂ | Kemiringan di titik **tengah** interval |
| h/2 | Setengah langkah untuk menuju titik tengah |

**Orde Akurasi:** O(h²)
**Perbedaan dari Heun:** k₂ dievaluasi di t + h/2, bukan t + h; y_{i+1} = yᵢ + h·k₂ (tidak dirata-rata).

---

### RK4
$$k_1 = f(t_i, y_i)$$
$$k_2 = f\!\left(t_i + \frac{h}{2},\; y_i + \frac{h}{2}k_1\right)$$
$$k_3 = f\!\left(t_i + \frac{h}{2},\; y_i + \frac{h}{2}k_2\right)$$
$$k_4 = f(t_i + h,\; y_i + h \cdot k_3)$$
$$y_{i+1} = y_i + \frac{h}{6}(k_1 + 2k_2 + 2k_3 + k_4)$$

| Simbol | Arti |
|---|---|
| k₁ | Kemiringan di awal interval |
| k₂ | Kemiringan di titik tengah (pakai k₁) |
| k₃ | Kemiringan di titik tengah (pakai k₂) |
| k₄ | Kemiringan di akhir interval (pakai k₃) |
| h/6 | Bobot rata-rata tertimbang |

**Orde Akurasi:** O(h⁴)
**Bobot:** k₁:k₂:k₃:k₄ = 1:2:2:1

---

# 5. LANGKAH CEPAT PENGERJAAN

---

## SELISIH PUSAT (O(h²), O(h⁴), O(h⁶))

**LANGKAH 1:** Tentukan x₀ (titik yang dicari) dan h dari tabel.

**LANGKAH 2:** Identifikasi nilai f yang dibutuhkan (1, 2, atau 3 titik di kiri dan kanan x₀).

**LANGKAH 3:** Masukkan nilai ke rumus yang sesuai (lihat Bagian 4).

**LANGKAH 4:** Hitung hasilnya. Bandingkan ketiga nilai — yang O(h⁶) paling akurat.

---

## EKSTRAPOLASI RICHARDSON (dari Selisih Pusat)

**LANGKAH 1:** Hitung D(h) menggunakan selisih pusat O(h²) dengan h asli.

**LANGKAH 2:** Hitung D(h/2) menggunakan selisih pusat O(h²) dengan h/2 (ganti h di rumus menjadi h/2).

**LANGKAH 3:** Hitung level 1: D₁ = (4·D(h/2) − D(h)) / 3 → hasil ini O(h⁴).

**LANGKAH 4:** Jika dibutuhkan O(h⁶), ulangi: hitung D(h/4) lalu D₁(h/2), kemudian D₂ = (16·D₁(h/2) − D₁(h)) / 15.

**LANGKAH 5:** Galat pemotongan O(h²) ≈ |D(h) − D₁|; galat O(h⁴) ≈ |D₁ − D₂|; dst.

---

## RIEMANN KIRI / KANAN / TENGAH

**LANGKAH 1:** Hitung h = (b−a)/n.

**LANGKAH 2:** Tentukan titik-titik xᵢ: x₀=a, x₁=a+h, …, xₙ=b.

**LANGKAH 3:**
- Kiri: evaluasi f di x₀, x₁, …, x_{n-1}
- Kanan: evaluasi f di x₁, x₂, …, xₙ
- Tengah: evaluasi f di titik tengah tiap subinterval: (xᵢ+xᵢ₊₁)/2

**LANGKAH 4:** Jumlahkan semua nilai f, kalikan dengan h.

**LANGKAH 5:** Hitung galat absolut terhadap nilai eksak.

---

## TRAPESIUM KOMPOSIT

**LANGKAH 1:** Hitung h = (b−a)/n, buat daftar xᵢ.

**LANGKAH 2:** Hitung f(xᵢ) untuk semua i = 0, 1, …, n.

**LANGKAH 3:** Terapkan rumus: (h/2) × [f(x₀) + 2(f(x₁)+…+f(xₙ₋₁)) + f(xₙ)].

**LANGKAH 4:** Jumlahkan titik interior (i=1 s/d n-1) dulu, kalikan 2, tambah ujung-ujung, baru kalikan h/2.

---

## SIMPSON 1/3 KOMPOSIT

**LANGKAH 1:** Pastikan n genap. Hitung h = (b−a)/n.

**LANGKAH 2:** Buat daftar xᵢ dan hitung f(xᵢ) untuk semua i.

**LANGKAH 3:** Kelompokkan: ujung (×1), indeks ganjil (×4), indeks genap interior (×2), ujung akhir (×1).

**LANGKAH 4:** Terapkan: (h/3) × [f(x₀) + 4f(x₁) + 2f(x₂) + 4f(x₃) + … + 4f(xₙ₋₁) + f(xₙ)].

---

## ROMBERG

**LANGKAH 1:** Hitung T(h) = Trapesium untuk n = 1, 2, 4, 8, 16 → baris pertama tabel R_{1,1} s/d R_{5,1}.

**LANGKAH 2:** Gunakan rumus rekursif untuk kolom j ≥ 2:
$$R_{i,j} = \frac{4^{j-1} R_{i,j-1} - R_{i-1,j-1}}{4^{j-1}-1}$$

**LANGKAH 3:** Isi tabel dari kiri ke kanan, atas ke bawah. Perhatikan: R_{i,j} membutuhkan R_{i,j-1} (sejajar) dan R_{i-1,j-1} (satu baris di atas).

**LANGKAH 4:** Nilai paling akurat ada di diagonal: R_{5,5}.

**Tips:** Buat tabel 5×5. Kolom 1 = Trapesium. Isi kolom 2,3,4,5 secara bertahap.

---

## EULER

**LANGKAH 1:** Tentukan f(t,y), y₀, h, dan titik tujuan.

**LANGKAH 2:** Hitung jumlah langkah: N = (tujuan − t₀)/h.

**LANGKAH 3:** Untuk setiap langkah: yᵢ₊₁ = yᵢ + h·f(tᵢ, yᵢ).

**LANGKAH 4:** Catat dalam tabel: tᵢ, yᵢ, f(tᵢ,yᵢ), yᵢ₊₁.

**LANGKAH 5:** Ulangi hingga mencapai titik tujuan.

---

## HEUN (RK2 Modified Euler)

**LANGKAH 1:** Tentukan f(t,y), y₀, h.

**LANGKAH 2:** Untuk setiap langkah i:
- Hitung k₁ = f(tᵢ, yᵢ)
- Hitung prediksi: ỹ = yᵢ + h·k₁
- Hitung k₂ = f(tᵢ + h, ỹ)
- Hitung yᵢ₊₁ = yᵢ + (h/2)(k₁ + k₂)

**LANGKAH 3:** Catat dalam tabel: tᵢ, yᵢ, k₁, k₂, yᵢ₊₁.

---

## RK2 MIDPOINT

**LANGKAH 1:** Tentukan f(t,y), y₀, h.

**LANGKAH 2:** Untuk setiap langkah i:
- Hitung k₁ = f(tᵢ, yᵢ)
- Hitung k₂ = f(tᵢ + h/2, yᵢ + (h/2)·k₁)
- Hitung yᵢ₊₁ = yᵢ + h·k₂

**LANGKAH 3:** Catat: tᵢ, yᵢ, k₁, k₂, yᵢ₊₁.
**Perhatikan:** yᵢ₊₁ = yᵢ + h·k₂ (bukan (h/2)(k₁+k₂) seperti Heun!).

---

## RK4

**LANGKAH 1:** Tentukan f(t,y), y₀, h.

**LANGKAH 2:** Untuk setiap langkah i:
- k₁ = f(tᵢ, yᵢ)
- k₂ = f(tᵢ + h/2, yᵢ + (h/2)k₁)
- k₃ = f(tᵢ + h/2, yᵢ + (h/2)k₂)
- k₄ = f(tᵢ + h, yᵢ + h·k₃)

**LANGKAH 3:** yᵢ₊₁ = yᵢ + (h/6)(k₁ + 2k₂ + 2k₃ + k₄)

**LANGKAH 4:** Catat semua kᵢ dalam tabel; hindari salah substitusi.

---

# 6. TABEL PERBANDINGAN METODE

| Metode | Orde | Akurasi | Kelebihan | Kekurangan |
|---|---|---|---|---|
| Riemann Kiri/Kanan | O(h) | Rendah | Paling mudah dihitung | Bias ke kiri atau kanan |
| Riemann Tengah | O(h²) | Sedang | Lebih baik dari Kiri/Kanan | Perlu titik tengah subinterval |
| Trapesium Komposit | O(h²) | Sedang | Fleksibel, n bebas | Kurang akurat vs Simpson |
| Simpson 1/3 Komposit | O(h⁴) | Tinggi | Jauh lebih akurat dari Trapesium | n harus genap |
| Romberg | O(h^{2k}) | Sangat tinggi | Konvergen cepat, rekursif | Perlu banyak evaluasi Trapesium |
| Selisih Pusat O(h²) | O(h²) | Sedang | Mudah, cukup 2 titik | Kurang akurat untuk h besar |
| Selisih Pusat O(h⁴) | O(h⁴) | Tinggi | Lebih akurat dari O(h²) | Perlu 4 titik |
| Selisih Pusat O(h⁶) | O(h⁶) | Sangat tinggi | Paling akurat di antara selisih pusat | Perlu 6 titik; x₀ tidak di ujung |
| Richardson | Meningkat | Sangat tinggi | Tidak perlu data baru | Bergantung pada perhitungan sebelumnya |
| Euler | O(h) | Rendah | Sangat sederhana | Akumulasi galat besar |
| Heun (RK2) | O(h²) | Sedang | Lebih akurat dari Euler | Dua evaluasi f per langkah |
| RK2 Midpoint | O(h²) | Sedang | Sedikit berbeda dari Heun | Dua evaluasi f per langkah |
| RK4 | O(h⁴) | Tinggi | Standar, akurasi sangat baik | Empat evaluasi f per langkah |

**Urutan akurasi (dari rendah ke tinggi):**
Euler < Riemann Kiri/Kanan < Trapesium ≈ Riemann Tengah < Simpson ≈ RK4 << Romberg (R_{5,5})

---

# 7. GALAT DAN AKURASI

### Galat Absolut
$$E_a = |y_{eksak} - y_{hampiran}|$$
- **Arti:** Selisih mutlak antara nilai eksak dan hampiran
- **Kapan:** Membandingkan akurasi antar metode; satuan sama dengan f(x)

### Galat Relatif
$$E_r = \frac{|y_{eksak} - y_{hampiran}|}{|y_{eksak}|}$$
- **Arti:** Proporsi galat terhadap nilai eksak (tanpa satuan)
- **Kapan:** Membandingkan galat antar soal yang memiliki skala berbeda

### Galat Relatif Persen
$$E_r\% = \frac{|y_{eksak} - y_{hampiran}|}{|y_{eksak}|} \times 100\%$$
- **Arti:** Galat dalam persen
- **Kapan:** Soal meminta galat relatif dalam persen (%)

### Truncation Error (Galat Pemotongan)
- **Arti:** Galat akibat memotong deret Taylor hingga suku tertentu
- **Contoh Selisih Pusat O(h²):**
$$E_{truncation} \approx -\frac{h^2}{6}f^{(3)}(x_0)$$
- **Kapan:** Soal meminta estimasi galat pemotongan; biasanya diperoleh dari perbedaan antar level Richardson
- **Interpretasi praktis:** |D(h) − D_Richardson| ≈ besar galat pemotongan level tersebut

---

# 8. JEBAKAN YANG SERING MUNCUL

### Diferensiasi Numerik
- **Salah titik untuk O(h⁶):** Jika x₀ di dekat ujung tabel, tidak ada 3 titik di salah satu sisi → tidak bisa pakai O(h⁶)
- **Salah indeks:** Untuk O(h⁴), bobot adalah −1, +8, (−8), +1 — jangan balik urutannya
- **Lupa bagi 60h pada O(h⁶):** Bukan 12h atau 2h
- **Salah h:** h adalah jarak antar titik dalam tabel, bukan sembarang nilai

### Richardson
- **Salah faktor pembagi:** Level 1 → bagi 3; Level 2 → bagi 15; Level 3 → bagi 63 (= 4ᵏ − 1)
- **Salah nilai h:** Pastikan D(h/2) benar-benar menggunakan titik yang lebih rapat
- **Bingung arah:** Richardson selalu: (4^k × nilai baru − nilai lama) / (4^k − 1)

### Simpson 1/3
- **n ganjil:** Simpson 1/3 TIDAK BISA dipakai jika n ganjil → pakai Simpson 3/8 atau Trapesium
- **Salah pola koefisien:** Ujung = 1, ganjil = 4, genap interior = 2, ujung akhir = 1
- **Lupa dibagi 3:** Faktor adalah h/3, bukan h/2

### Romberg
- **Salah rumus rekursif:** Pastikan R_{i,j} menggunakan R_{i,j-1} (sejajar kiri) dan R_{i-1,j-1} (diagonal atas kiri)
- **Salah nilai Trapesium awal:** R_{1,1} = Trapesium n=1 (hanya 2 titik: a dan b)
- **Lupa bahwa n berlipat ganda:** n=1,2,4,8,16 → bukan n=1,2,3,4,5
- **Salah faktor:** Untuk kolom j, faktornya adalah 4^{j-1}

### ODE (Euler, Heun, RK)
- **Salah tanda f:** Pastikan mensubstitusi tᵢ dan yᵢ yang benar ke f(t,y)
- **Salah rumus Heun vs Midpoint:**
  - Heun: k₂ di (t+h, y+h·k₁), lalu rata-rata k₁ dan k₂
  - Midpoint: k₂ di (t+h/2, y+(h/2)k₁), lalu yᵢ₊₁ = yᵢ + h·k₂
- **RK4 salah k₃:** k₃ pakai k₂ (bukan k₁), tapi evaluasi di t+h/2 (sama dengan k₂)
- **RK4 lupa ×2 untuk k₂ dan k₃:** Bobot adalah 1:2:2:1, bukan 1:1:1:1
- **Jumlah langkah salah:** N = (target − t₀)/h. Untuk y(1) dengan h=0.1 → 10 langkah

### Galat
- **Galat absolut bukan negatif:** Selalu pakai nilai mutlak |eksak − hampiran|
- **Galat relatif bukan persen:** Hanya ×100% jika diminta dalam persen
- **Membandingkan galat:** Metode terbaik = galat absolut paling kecil

---

# 9. PREDIKSI VARIASI UAS

### Topik yang Kemungkinan Diuji
- Diferensiasi numerik selisih pusat pada tabel data dengan h tertentu — ketiga orde sekaligus
- Richardson ekstrapolasi untuk meningkatkan orde akurasi turunan atau integral
- Perhitungan integral dengan Riemann (Kiri/Kanan/Tengah) + perbandingan galat
- Trapesium dan Simpson komposit + perbandingan keduanya
- Tabel Romberg lengkap (5×5) dari data Trapesium
- ODE dengan Euler + salah satu metode RK2 + RK4 pada soal yang sama
- Perbandingan galat absolut dan relatif antara dua atau lebih metode

### Variasi Soal yang Mungkin Muncul
- Diminta menghitung turunan kedua f''(x₀) (bukan f'(x₀)) dengan selisih pusat
- Tabel Romberg dimulai dari T(h) yang berbeda (bukan a=0)
- ODE dengan fungsi yang lebih kompleks tetapi langkah yang sama
- Simpson 3/8 (koefisien 3/8, n kelipatan 3) sebagai alternatif
- Integrasi Gauss-Legendre sebagai bonus
- Diminta memilih n optimal berdasarkan batas galat yang diberikan
- Soal gabungan: turunan dari integral atau sebaliknya

### Metode Alternatif yang Perlu Diwaspadai
- Selisih maju O(h): f'(x₀) ≈ [f(x₀+h)−f(x₀)]/h (lebih rendah dari selisih pusat)
- Selisih mundur O(h): f'(x₀) ≈ [f(x₀)−f(x₀−h)]/h
- Simpson 3/8: (3h/8)[f₀ + 3f₁ + 3f₂ + f₃], n kelipatan 3
- RK2 Ralston: variasi lain dari RK2

---

# 10. LAST MINUTE REVIEW

## Kenali Soal dalam 30 Detik

| Soal menyebutkan … | Langsung pikirkan … |
|---|---|
| "tabel data", "h = …", "f'(x)" | Selisih Pusat (pilih orde) |
| "Richardson", "ekstrapolasi", "tingkatkan akurasi" | Richardson, faktor 4^k |
| "Riemann", "Kiri/Kanan/Tengah" | Penjumlahan persegi panjang × h |
| "Trapesium komposit", "n = …" | Koef: 1, 2, 2, …, 2, 1; bagi h/2 |
| "Simpson 1/3", "n = …" | Koef: 1, 4, 2, 4, …, 4, 1; bagi h/3; n genap! |
| "Romberg", "R_{5,5}" | Tabel 5×5; kolom 1 = Trapesium |
| "Euler", "y(t)" | yᵢ₊₁ = yᵢ + h·f(tᵢ,yᵢ) |
| "Heun" atau "Modified Euler" | k₁, k₂ → rata-rata (h/2)(k₁+k₂) |
| "Midpoint Method" | k₁, k₂ di tengah → h·k₂ |
| "RK4" | 4 nilai k, bobot 1:2:2:1, bagi 6 |

---

## Rumus Wajib Hafal

```
SELISIH PUSAT:
O(h²)  = [f(x+h) − f(x−h)] / (2h)
O(h⁴)  = [−f(x+2h) + 8f(x+h) − 8f(x−h) + f(x−2h)] / (12h)
O(h⁶)  = [f(x−3h) − 9f(x−2h) + 45f(x−h) − 45f(x+h) + 9f(x+2h) − f(x+3h)] / (60h)

RICHARDSON:  D_new = (4^k · D_baru − D_lama) / (4^k − 1)
  k=1 → bagi 3 | k=2 → bagi 15 | k=3 → bagi 63

TRAPESIUM:   (h/2)[f₀ + 2(f₁+…+fₙ₋₁) + fₙ]
SIMPSON 1/3: (h/3)[f₀ + 4f₁ + 2f₂ + 4f₃ + … + 4fₙ₋₁ + fₙ], n GENAP
ROMBERG:     R_{i,j} = (4^{j-1}·R_{i,j-1} − R_{i-1,j-1}) / (4^{j-1}−1)

EULER:       yᵢ₊₁ = yᵢ + h·f(tᵢ,yᵢ)
HEUN:        k₁=f(t,y); k₂=f(t+h, y+hk₁); y=y+(h/2)(k₁+k₂)
MIDPOINT:    k₁=f(t,y); k₂=f(t+h/2, y+(h/2)k₁); y=y+h·k₂
RK4:         k₁,k₂,k₃,k₄ → y=y+(h/6)(k₁+2k₂+2k₃+k₄)
```

---

## Syarat Penting Tiap Metode

- **Simpson 1/3:** n harus **genap**
- **O(h⁶):** x₀ harus memiliki minimal 3 titik di kiri DAN 3 titik di kanan
- **Romberg:** Hitung Trapesium untuk n = 1, 2, 4, 8, 16 (berlipat dua)
- **RK4:** Perlu 4 evaluasi f per langkah — hati-hati substitusi k₃ yang pakai k₂
- **Heun vs Midpoint:** Keduanya RK2, tapi rumus dan titik evaluasi berbeda

---

## Urutan Akurasi (dari rendah ke tinggi)

```
Euler (O(h)) 
  < Riemann Kiri/Kanan (O(h)) 
  < Trapesium (O(h²)) ≈ Riemann Tengah (O(h²)) ≈ Heun/Midpoint (O(h²)) 
  < Simpson 1/3 (O(h⁴)) ≈ RK4 (O(h⁴)) 
  << Romberg R_{5,5} (O(h^{10}))
```

---

*Cheatsheet ini dibuat berdasarkan pola soal Kuis Metode Numerik, Juni 2026.*
*Gunakan bersama tabel iterasi yang sistematis untuk mendapat nilai penuh.*
