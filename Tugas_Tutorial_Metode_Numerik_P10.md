# Latihan Tugas Tutorial — Metode Numerik
**Pertemuan 10 | Program Studi Sains Data, ITERA**

> **Topik:** Runge–Kutta Orde 2 & 4 dan Predictor–Corrector Adams Orde Empat

---

## Tugas 1 — RK2: Midpoint dan Heun

**Masalah Nilai Awal:**

$$y' = t + y, \quad y(0) = 1, \quad 0 \leq t \leq 0.5, \quad h = 0.1$$

**Solusi eksak:** $y(t) = 2e^t - t - 1$

---

### 1.1 Metode Midpoint

**Rumus:**

$$k_1 = f(t_i, w_i), \quad k_2 = f\!\left(t_i + \tfrac{h}{2},\; w_i + \tfrac{h}{2} k_1\right), \quad w_{i+1} = w_i + h \cdot k_2$$

**Nilai awal:** $w_0 = 1.0000000$, $t_0 = 0.0$

| Iterasi | t    | wᵢ        | k1        | k2        | wᵢ₊₁      |
|---------|------|-----------|-----------|-----------|-----------|
| 1       | 0.0  | 1.0000000 | 1.0000000 | 1.1000000 | 1.1100000 |
| 2       | 0.1  | 1.1100000 | 1.2100000 | 1.3205000 | 1.2420500 |
| 3       | 0.2  | 1.2420500 | 1.4420500 | 1.5641525 | 1.3984653 |
| 4       | 0.3  | 1.3984653 | 1.6984653 | 1.8333885 | 1.5818041 |
| 5       | 0.4  | 1.5818041 | 1.9818041 | 2.1308943 | 1.7948935 |

**Hasil Midpoint: y(0.5) ≈ 1.7948935**

---

### 1.2 Metode Heun

**Rumus:**

$$k_1 = f(t_i, w_i), \quad k_2 = f(t_i + h,\; w_i + h \cdot k_1), \quad w_{i+1} = w_i + \frac{h}{2}(k_1 + k_2)$$

**Nilai awal:** $w_0 = 1.0000000$, $t_0 = 0.0$

| Iterasi | t    | wᵢ        | k1        | w*        | k2        | wᵢ₊₁      |
|---------|------|-----------|-----------|-----------|-----------|-----------|
| 1       | 0.0  | 1.0000000 | 1.0000000 | 1.1000000 | 1.2000000 | 1.1100000 |
| 2       | 0.1  | 1.1100000 | 1.2100000 | 1.2310000 | 1.4310000 | 1.2420500 |
| 3       | 0.2  | 1.2420500 | 1.4420500 | 1.3862550 | 1.6862550 | 1.3984653 |
| 4       | 0.3  | 1.3984653 | 1.6984653 | 1.5683118 | 1.9683118 | 1.5818041 |
| 5       | 0.4  | 1.5818041 | 1.9818041 | 1.7799845 | 2.2799845 | 1.7948935 |

**Hasil Heun: y(0.5) ≈ 1.7948935**

---

### 1.3 Tabel Perbandingan Tugas 1

| t   | Midpoint  | Heun      | Eksak     | Galat Midpoint | Galat Heun |
|-----|-----------|-----------|-----------|----------------|------------|
| 0.0 | 1.0000000 | 1.0000000 | 1.0000000 | 0.0000000      | 0.0000000  |
| 0.1 | 1.1100000 | 1.1100000 | 1.1103418 | 0.0003418      | 0.0003418  |
| 0.2 | 1.2420500 | 1.2420500 | 1.2428055 | 0.0007555      | 0.0007555  |
| 0.3 | 1.3984653 | 1.3984653 | 1.3997176 | 0.0012524      | 0.0012524  |
| 0.4 | 1.5818041 | 1.5818041 | 1.5836494 | 0.0018453      | 0.0018453  |
| 0.5 | 1.7948935 | 1.7948935 | 1.7974425 | 0.0025490      | 0.0025490  |

> **Kesimpulan:** Untuk $f(t,y) = t + y$ yang linear, Midpoint dan Heun menghasilkan nilai identik di setiap iterasi karena secara aljabar koefisiennya sama. Keduanya jauh lebih akurat dari Euler biasa karena menggunakan dua evaluasi fungsi — orde kesalahan O(h²).

---

## Tugas 2 — RK4

**Masalah Nilai Awal:**

$$y' = t \cdot e^{3t} - 2y, \quad y(0) = 0, \quad 0 \leq t \leq 1, \quad h = 0.25$$

**Rumus RK4:**

$$K_1 = h \cdot f(t_i, w_i)$$
$$K_2 = h \cdot f\!\left(t_i + \tfrac{h}{2},\; w_i + \tfrac{K_1}{2}\right)$$
$$K_3 = h \cdot f\!\left(t_i + \tfrac{h}{2},\; w_i + \tfrac{K_2}{2}\right)$$
$$K_4 = h \cdot f(t_i + h,\; w_i + K_3)$$
$$w_{i+1} = w_i + \frac{K_1 + 2K_2 + 2K_3 + K_4}{6}$$

**Nilai awal:** $w_0 = 0.0000000$, $t_0 = 0.00$

---

**Iterasi 1 (t₀ = 0.00 → t₁ = 0.25)**

| Koefisien | Perhitungan | Nilai |
|-----------|-------------|-------|
| K1 | 0.25 × f(0.00, 0.0000000) | 0.0000000 |
| K2 | 0.25 × f(0.125, 0.0000000) | 0.0454685 |
| K3 | 0.25 × f(0.125, 0.0227342) | 0.0341014 |
| K4 | 0.25 × f(0.25, 0.0341014) | 0.1152618 |
| **w₁** | 0.0000000 + (K1+2K2+2K3+K4)/6 | **0.0457336** |

**Iterasi 2 (t₁ = 0.25 → t₂ = 0.50)**

| Koefisien | Perhitungan | Nilai |
|-----------|-------------|-------|
| K1 | 0.25 × f(0.25, 0.0457336) | 0.1094457 |
| K2 | 0.25 × f(0.375, 0.1004564) | 0.2385421 |
| K3 | 0.25 × f(0.375, 0.1650046) | 0.2062680 |
| K4 | 0.25 × f(0.50, 0.2520016) | 0.4342103 |
| **w₂** | 0.0457336 + (K1+2K2+2K3+K4)/6 | **0.2846130** |

**Iterasi 3 (t₂ = 0.50 → t₃ = 0.75)**

| Koefisien | Perhitungan | Nilai |
|-----------|-------------|-------|
| K1 | 0.25 × f(0.50, 0.2846130) | 0.4179047 |
| K2 | 0.25 × f(0.625, 0.4935654) | 0.7720953 |
| K3 | 0.25 × f(0.625, 0.6706607) | 0.6835477 |
| K4 | 0.25 × f(0.75, 0.9681607) | 1.2948702 |
| **w₃** | 0.2846130 + (K1+2K2+2K3+K4)/6 | **1.0552898** |

**Iterasi 4 (t₃ = 0.75 → t₄ = 1.00)**

| Koefisien | Perhitungan | Nilai |
|-----------|-------------|-------|
| K1 | 0.25 × f(0.75, 1.0552898) | 1.2513056 |
| K2 | 0.25 × f(0.875, 1.6809426) | 2.1792793 |
| K3 | 0.25 × f(0.875, 2.1449294) | 1.9472859 |
| K4 | 0.25 × f(1.00, 3.0025757) | 3.5200964 |
| **w₄** | 1.0552898 + (K1+2K2+2K3+K4)/6 | **3.2260452** |

### Tabel Hasil RK4

| i | tᵢ   | wᵢ        |
|---|------|-----------|
| 0 | 0.00 | 0.0000000 |
| 1 | 0.25 | 0.0457336 |
| 2 | 0.50 | 0.2846130 |
| 3 | 0.75 | 1.0552898 |
| 4 | 1.00 | 3.2260452 |

> **Catatan:** RK4 menggunakan empat evaluasi fungsi per langkah dengan bobot 1:2:2:1, menghasilkan aproksimasi orde keempat O(h⁴).

---

## Tugas 3 — Perbandingan Euler, RK2 Midpoint, dan RK4

**Masalah Nilai Awal:**

$$y' = y - t^2 + 1, \quad y(0) = 0.5, \quad 0 \leq t \leq 2$$

**Solusi eksak:** $y(t) = (t+1)^2 - \frac{1}{2}e^t$

---

### 3.1 Metode Euler (h = 0.1, N = 20)

| t   | Euler (h=0.1) | Eksak     | Galat     |
|-----|---------------|-----------|-----------|
| 0.0 | 0.5000000     | 0.5000000 | 0.0000000 |
| 0.2 | 0.8140000     | 0.8292986 | 0.0152986 |
| 0.4 | 1.1815400     | 1.2140877 | 0.0325477 |
| 0.6 | 1.5970634     | 1.6489406 | 0.0518772 |
| 0.8 | 2.0538467     | 2.1272295 | 0.0733828 |
| 1.0 | 2.5437545     | 2.6408591 | 0.0971046 |
| 1.2 | 3.0569430     | 3.1799415 | 0.1229986 |
| 1.4 | 3.5815010     | 3.7324000 | 0.1508990 |
| 1.6 | 4.1030162     | 4.2834838 | 0.1804676 |
| 1.8 | 4.6040496     | 4.8151763 | 0.2111267 |
| 2.0 | 5.0635000     | 5.3054720 | **0.2419719** |

---

### 3.2 RK2 Midpoint (h = 0.2, N = 10)

| i  | t   | Midpoint  | Eksak     | Galat     |
|----|-----|-----------|-----------|-----------|
| 0  | 0.0 | 0.5000000 | 0.5000000 | 0.0000000 |
| 1  | 0.2 | 0.8280000 | 0.8292986 | 0.0012986 |
| 2  | 0.4 | 1.2113600 | 1.2140877 | 0.0027277 |
| 3  | 0.6 | 1.6446592 | 1.6489406 | 0.0042814 |
| 4  | 0.8 | 2.1212842 | 2.1272295 | 0.0059453 |
| 5  | 1.0 | 2.6331668 | 2.6408591 | 0.0076923 |
| 6  | 1.2 | 3.1704634 | 3.1799415 | 0.0094781 |
| 7  | 1.4 | 3.7211654 | 3.7324000 | 0.0112346 |
| 8  | 1.6 | 4.2706218 | 4.2834838 | 0.0128620 |
| 9  | 1.8 | 4.8009586 | 4.8151763 | 0.0142177 |
| 10 | 2.0 | 5.2903695 | 5.3054720 | **0.0151025** |

---

### 3.3 RK4 (h = 0.2, N = 10)

| i  | t   | RK4       | Eksak     | Galat     |
|----|-----|-----------|-----------|-----------|
| 0  | 0.0 | 0.5000000 | 0.5000000 | 0.0000000 |
| 1  | 0.2 | 0.8292933 | 0.8292986 | 0.0000053 |
| 2  | 0.4 | 1.2140762 | 1.2140877 | 0.0000114 |
| 3  | 0.6 | 1.6489220 | 1.6489406 | 0.0000186 |
| 4  | 0.8 | 2.1272027 | 2.1272295 | 0.0000269 |
| 5  | 1.0 | 2.6408227 | 2.6408591 | 0.0000364 |
| 6  | 1.2 | 3.1798942 | 3.1799415 | 0.0000474 |
| 7  | 1.4 | 3.7323401 | 3.7324000 | 0.0000599 |
| 8  | 1.6 | 4.2834095 | 4.2834838 | 0.0000743 |
| 9  | 1.8 | 4.8150857 | 4.8151763 | 0.0000906 |
| 10 | 2.0 | 5.3053630 | 5.3054720 | **0.0001089** |

---

### 3.4 Perbandingan Galat pada t = 2

| Metode              | Hasil y(2) | Galat Absolut | Rasio Peningkatan |
|---------------------|------------|---------------|-------------------|
| Euler (h=0.1)       | 5.0635000  | 0.2419719     | —                 |
| RK2 Midpoint (h=0.2)| 5.2903695  | 0.0151025     | ~16× lebih baik   |
| RK4 (h=0.2)         | 5.3053630  | 0.0001089     | ~222× lebih baik  |

> **Kesimpulan:** RK4 jauh lebih akurat (galat 0.0001089) meski menggunakan h yang lebih besar dari Euler. Ini karena orde kesalahan pemotongan: Euler O(h), RK2 O(h²), RK4 O(h⁴). RK4 dengan h=0.2 adalah pilihan terbaik antara akurasi dan efisiensi.

---

## Tugas 4 — Adams Predictor–Corrector Orde Empat

**Masalah Nilai Awal:**

$$y' = t + y, \quad y(0) = 1, \quad 0 \leq t \leq 1, \quad h = 0.2$$

**Solusi eksak:** $y(t) = 2e^t - t - 1$

---

### Langkah 1 — Hitung w₀–w₃ dengan RK4

| i | tᵢ  | wᵢ        | fᵢ = f(tᵢ, wᵢ) |
|---|-----|-----------|----------------|
| 0 | 0.0 | 1.0000000 | 1.0000000      |
| 1 | 0.2 | 1.2428000 | 1.4428000      |
| 2 | 0.4 | 1.5836359 | 1.9836359      |
| 3 | 0.6 | 2.0442129 | 2.6442129      |

<details>
<summary>Detail iterasi RK4 untuk nilai awal Adams</summary>

**Iterasi 1 (t₀=0.0 → t₁=0.2):**

K1 = 0.2 × f(0.0, 1.0000000) = 0.2000000  
K2 = 0.2 × f(0.1, 1.1000000) = 0.2400000  
K3 = 0.2 × f(0.1, 1.1200000) = 0.2440000  
K4 = 0.2 × f(0.2, 1.2440000) = 0.2888000  
w₁ = 1.0000000 + (0.2000000 + 2×0.2400000 + 2×0.2440000 + 0.2888000)/6 = **1.2428000**

**Iterasi 2 (t₁=0.2 → t₂=0.4):**

K1 = 0.2885600, K2 = 0.3374160, K3 = 0.3423016, K4 = 0.3970203  
w₂ = **1.5836359**

**Iterasi 3 (t₂=0.4 → t₃=0.6):**

K1 = 0.3967272, K2 = 0.4563999, K3 = 0.4623672, K4 = 0.5292006  
w₃ = **2.0442129**

</details>

---

### Langkah 2 — Adams–Bashforth (Predictor) Orde 4

$$w^*_{i+1} = w_i + \frac{h}{24}\left[55f_i - 59f_{i-1} + 37f_{i-2} - 9f_{i-3}\right]$$

**Langkah ke-4 (i=3, t=0.6 → t=0.8):**

$$w^*_4 = 2.0442129 + \frac{0.2}{24}\left[55(2.6442129) - 59(1.9836359) + 37(1.4428000) - 9(1.0000000)\right]$$

$$= 2.0442129 + 0.008333 \times 72.7808 = \mathbf{2.6507195}$$

---

### Langkah 3 — Adams–Moulton (Corrector) Orde 4

$$w_{i+1} = w_i + \frac{h}{24}\left[9f^*_{i+1} + 19f_i - 5f_{i-1} + f_{i-2}\right]$$

**Koreksi w₄:**

$f(0.8,\, 2.6507195) = 3.4507195$

$$w_4 = 2.0442129 + 0.008333 \times [9(3.4507195) + 19(2.6442129) - 5(1.9836359) + 1.4428000]$$

$$= 2.0442129 + 0.008333 \times 72.8211 = \mathbf{2.6510558}$$

Eksak $y(0.8) = 2.6510819$, Galat = 0.0000261

---

**Langkah ke-5 (i=4, t=0.8 → t=1.0):**

$f(0.8, 2.6510558) = 3.4510558$

$w^*_5 = 2.6510558 + 0.008333 \times 94.2088 = \mathbf{3.4361294}$

$f(1.0, 3.4361294) = 4.4361294$

$w_5 = 2.6510558 + 0.008333 \times 94.2578 = \mathbf{3.4365374}$

Eksak $y(1.0) = 3.4365637$, Galat = 0.0000263

---

### Tabel Lengkap Adams Predictor–Corrector

| i | tᵢ  | w* (Predictor) | wᵢ (Corrector) | y(tᵢ) Eksak | Galat     |
|---|-----|----------------|----------------|-------------|-----------|
| 0 | 0.0 | (RK4)          | 1.0000000      | 1.0000000   | 0.0000000 |
| 1 | 0.2 | (RK4)          | 1.2428000      | 1.2428055   | 0.0000055 |
| 2 | 0.4 | (RK4)          | 1.5836359      | 1.5836494   | 0.0000135 |
| 3 | 0.6 | (RK4)          | 2.0442129      | 2.0442376   | 0.0000247 |
| 4 | 0.8 | 2.6507195      | 2.6510558      | 2.6510819   | 0.0000261 |
| 5 | 1.0 | 3.4361294      | 3.4365374      | 3.4365637   | 0.0000263 |

> **Kesimpulan:** Adams Predictor–Corrector menghasilkan galat ~10⁻⁵ yang konsisten. Nilai awal w₀–w₃ dihitung dengan RK4 agar kesalahan awal tidak merambat ke langkah Adams berikutnya.

---

## Tugas 5 — Implementasi Python: Predictor–Corrector

**Masalah Nilai Awal:**

$$y' = y - t^2 + 1, \quad y(0) = 0.5, \quad 0 \leq t \leq 2, \quad h = 0.2$$

### Kode Program Python

```python
import numpy as np

def f(t, y):
    return y - t**2 + 1

def exact(t):
    return (t + 1)**2 - 0.5 * np.exp(t)

def rk4_step(t, w, h):
    K1 = h * f(t, w)
    K2 = h * f(t + h/2, w + K1/2)
    K3 = h * f(t + h/2, w + K2/2)
    K4 = h * f(t + h, w + K3)
    return w + (K1 + 2*K2 + 2*K3 + K4) / 6

h = 0.2
t0, w0 = 0.0, 0.5
t_vals = [t0]; w_vals = [w0]

# Hitung w0..w3 dengan RK4
t, w = t0, w0
for _ in range(3):
    w = rk4_step(t, w, h)
    t = round(t + h, 10)
    t_vals.append(t); w_vals.append(w)

f_vals = [f(t_vals[i], w_vals[i]) for i in range(4)]

# Adams Predictor–Corrector
for step in range(7):  # i=3..9, t=0.8..2.0
    i = 3 + step
    fi, fi1, fi2, fi3 = f_vals[i], f_vals[i-1], f_vals[i-2], f_vals[i-3]
    wi, ti = w_vals[i], t_vals[i]
    t_next = round(ti + h, 10)

    # Predictor (Adams–Bashforth)
    wp = wi + (h/24) * (55*fi - 59*fi1 + 37*fi2 - 9*fi3)

    # Corrector (Adams–Moulton)
    fp = f(t_next, wp)
    wc = wi + (h/24) * (9*fp + 19*fi - 5*fi1 + fi2)

    w_vals.append(wc); t_vals.append(t_next)
    f_vals.append(f(t_next, wc))

    ye = exact(t_next)
    print(f"{i+1:>3} {t_next:>5.1f} {wp:>12.7f} {wc:>12.7f} "
          f"{ye:>12.7f} {abs(ye-wc):>10.7f} {abs(wc-wp):>10.7f}")
```

### Output Program

| i  | tᵢ  | w* (AB)   | wᵢ (AM)   | Eksak y(tᵢ) | Galat     | \|wc−wp\|  |
|----|-----|-----------|-----------|-------------|-----------|------------|
| 0  | 0.0 | (RK4)     | 0.5000000 | 0.5000000   | 0.0000000 | —          |
| 1  | 0.2 | (RK4)     | 0.8292933 | 0.8292986   | 0.0000053 | —          |
| 2  | 0.4 | (RK4)     | 1.2140762 | 1.2140877   | 0.0000114 | —          |
| 3  | 0.6 | (RK4)     | 1.6489220 | 1.6489406   | 0.0000186 | —          |
| 4  | 0.8 | 2.1272892 | 2.1272056 | 2.1272295   | 0.0000239 | 0.0000836  |
| 5  | 1.0 | 2.6409314 | 2.6408286 | 2.6408591   | 0.0000305 | 0.0001028  |
| 6  | 1.2 | 3.1800275 | 3.1799026 | 3.1799415   | 0.0000389 | 0.0001249  |
| 7  | 1.4 | 3.7325032 | 3.7323505 | 3.7324000   | 0.0000495 | 0.0001527  |
| 8  | 1.6 | 4.2836074 | 4.2834208 | 4.2834838   | 0.0000630 | 0.0001866  |
| 9  | 1.8 | 4.8153242 | 4.8150964 | 4.8151763   | 0.0000799 | 0.0002279  |
| 10 | 2.0 | 5.3056490 | 5.3053707 | 5.3054720   | 0.0001013 | 0.0002783  |

> **Analisis:** Kolom |wc−wp| menunjukkan selisih predictor–corrector yang kecil (~10⁻⁴), menandakan konvergensi yang baik. Galat sedikit meningkat seiring t namun tetap dalam orde 10⁻⁴ — metode stabil sepanjang interval.

---

## Tugas 6 — Pertanyaan Konsep

### 1. Perbedaan Metode Satu-Langkah vs Banyak-Langkah

**Metode satu-langkah** (Euler, Runge–Kutta) menghitung $w_{i+1}$ hanya dari $w_i$. Rumus umum: $w_{i+1} = w_i + h \cdot \Phi(t_i, w_i, h)$. Dapat langsung digunakan dari kondisi awal tanpa nilai tambahan.

**Metode banyak-langkah** (Adams) menghitung $w_{i+1}$ menggunakan beberapa nilai sebelumnya ($w_i, w_{i-1}, w_{i-2}, w_{i-3}$). Tidak dapat langsung dimulai dari kondisi awal — membutuhkan beberapa nilai awal tambahan yang dihitung terlebih dahulu dengan metode satu-langkah.

---

### 2. Mengapa Adams Orde 4 Butuh Empat Nilai Awal?

Rumus Adams–Bashforth orde 4 menggunakan $f_i, f_{i-1}, f_{i-2}, f_{i-3}$ (empat evaluasi fungsi pada titik sebelumnya). Karena itu, sebelum Adams dapat berjalan, harus tersedia $w_0, w_1, w_2, w_3$ beserta nilai fungsi di setiap titiknya. Jumlah nilai awal ditentukan oleh orde metode banyak-langkah.

---

### 3. Mengapa RK4 Cocok untuk Menghitung Nilai Awal Tambahan?

RK4 adalah metode satu-langkah dengan akurasi orde keempat O(h⁴). Karena hanya butuh kondisi awal tunggal dan menghasilkan aproksimasi sangat akurat, RK4 ideal untuk menghitung $w_1, w_2, w_3$ sebelum Adams mulai. Jika nilai awal dihitung dengan Euler, galat awal dapat merambat dan menurunkan akurasi keseluruhan Adams.

---

### 4. Makna Predictor dan Corrector

**Predictor** menghitung taksiran awal $w^*_{i+1}$ menggunakan metode eksplisit (Adams–Bashforth). Nilai ini merupakan perkiraan kasar.

**Corrector** memperbaiki nilai prediksi menggunakan metode implisit (Adams–Moulton) dengan memanfaatkan $f(t_{i+1}, w^*_{i+1})$. Hasil koreksi $w_{i+1}$ lebih akurat karena menggunakan informasi slope di titik tujuan yang telah diprediksi.

---

### 5. Mengapa Memperkecil h Meningkatkan Akurasi?

Kesalahan pemotongan lokal bergantung pada pangkat h:

| Metode | Orde Kesalahan | Efek jika h → h/2 |
|--------|----------------|-------------------|
| Euler  | O(h)           | Galat berkurang ½ |
| RK2    | O(h²)          | Galat berkurang ¼ |
| RK4    | O(h⁴)          | Galat berkurang 1/16 |

Memperkecil h meningkatkan jumlah langkah dan biaya komputasi, sehingga perlu keseimbangan antara akurasi dan efisiensi.
