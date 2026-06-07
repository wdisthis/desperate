# 📚 Panduan Belajar UAS — Functional Programming (Sains Data)
> Minggu 11–15 | Decorator · PyMonad · Concurrency · Big Data · Scala

---

## 📋 Daftar Isi

1. [Minggu 11 — Fungsi Decorator](#minggu-11--fungsi-decorator)
2. [Minggu 12 — Functional Design Patterns: PyMonad Library](#minggu-12--functional-design-patterns-pymonad-library)
3. [Minggu 13 — Concurrency dan Parallel Functional Programming](#minggu-13--concurrency-dan-parallel-functional-programming)
4. [Minggu 14 — Functional Programming in Big Data](#minggu-14--functional-programming-in-big-data)
5. [Minggu 15 — Pengantar Scala dan Functional Programming](#minggu-15--pengantar-scala-dan-functional-programming)
6. [Ringkasan Konsep Kunci](#ringkasan-konsep-kunci)
7. [Latihan Soal UAS](#latihan-soal-uas)

---

---

# Minggu 11 — Fungsi Decorator

## 1.1 Prasyarat: Fungsi sebagai First-Class Object

Sebelum memahami decorator, penting untuk paham bahwa Python memperlakukan **fungsi sebagai objek**. Fungsi bisa:

| Kemampuan | Contoh |
|---|---|
| Disimpan dalam variabel | `f = greet` |
| Dikirim sebagai argumen | `apply(greet, "Budi")` |
| Dikembalikan dari fungsi | `return inner_fn` |
| Disimpan dalam struktur data | `[fn1, fn2]` |

```python
# Fungsi disimpan dalam variabel
def greet(name):
    return f"Hello, {name}"

fn = greet
print(fn("Alice"))  # Hello, Alice

# Fungsi sebagai argumen (Higher-Order Function)
def say_something(fn, value):
    print(fn(value))

say_something(greet, "Bob")  # Hello, Bob

# Fungsi mengembalikan fungsi (Closure)
def multiplier(factor):
    def multiply(x):
        return x * factor
    return multiply

double = multiplier(2)
print(double(5))  # 10
```

---

## 1.2 Apa itu Decorator?

**Decorator** adalah fungsi yang:
- Menerima fungsi lain sebagai argumen
- Mengembalikan fungsi baru (yang "membungkus" fungsi asli)
- Menambahkan fungsionalitas **tanpa mengubah kode asli**

> **Analogi:** Decorator = Bingkai foto. Foto (fungsi asli) tidak berubah, tapi tampilannya dipercantik.

```
FUNCTION  ──passed to──►  FUNCTION DECORATOR  ──returns──►  DECORATED FUNCTION
```

### Sintaks dasar

```python
# CARA MANUAL (tanpa @)
def my_decorator(func):
    def wrapper():
        print("Sebelum.")
        func()
        print("Setelah.")
    return wrapper

def say_hello():
    print("Hello!")

say_hello = my_decorator(say_hello)   # pembungkusan manual
say_hello()

# Output:
# Sebelum.
# Hello!
# Setelah.
```

```python
# CARA DENGAN @ (sugar syntax — ekuivalen persis)
def my_decorator(func):
    def wrapper():
        print("Sebelum.")
        func()
        print("Setelah.")
    return wrapper

@my_decorator          # ← ini setara: say_hello = my_decorator(say_hello)
def say_hello():
    print("Hello!")

say_hello()
```

---

## 1.3 Decorator dengan Argumen Fungsi (*args, **kwargs)

Agar decorator bisa membungkus fungsi yang **menerima argumen apapun**, gunakan `*args` dan `**kwargs`:

```python
def log_decorator(func):
    def wrapper(*args, **kwargs):       # terima semua argumen
        print(f"[LOG] Memanggil {func.__name__} dengan args={args}, kwargs={kwargs}")
        result = func(*args, **kwargs)  # teruskan ke fungsi asli
        print(f"[LOG] Hasil: {result}")
        return result
    return wrapper

@log_decorator
def tambah(a, b):
    return a + b

tambah(3, 4)
# [LOG] Memanggil tambah dengan args=(3, 4), kwargs={}
# [LOG] Hasil: 7
```

---

## 1.4 Tujuan Decorator (Mengapa Digunakan?)

### A. Memisahkan Logika Tambahan dari Fungsi Utama

```python
# TANPA decorator — logika logging campur aduk
def tambah(a, b):
    print(f"Memanggil tambah...")   # ← noise, bukan logika utama
    result = a + b
    print(f"Hasil: {result}")       # ← noise
    return result

# DENGAN decorator — fungsi utama bersih
@log_decorator
def tambah(a, b):
    return a + b   # hanya logika utama
```

### B. DRY — Don't Repeat Yourself (Menghindari Duplikasi)

```python
def validate_nonzero(func):
    def wrapper(a, b):
        if b == 0:
            return "Error: tidak bisa dibagi nol!"
        return func(a, b)
    return wrapper

@validate_nonzero
def bagi(a, b):
    return a / b

@validate_nonzero
def kecepatan(jarak, waktu):
    return jarak / waktu

# Validasi yang sama dipakai di dua fungsi — tidak perlu tulis ulang!
print(bagi(10, 2))       # 5.0
print(bagi(10, 0))       # Error: tidak bisa dibagi nol!
print(kecepatan(100, 0)) # Error: tidak bisa dibagi nol!
```

### C. Open/Closed Principle (Tambah fitur tanpa ubah fungsi)

```python
def uppercase_decorator(func):
    def wrapper(*args, **kwargs):
        result = func(*args, **kwargs)
        return result.upper()       # tambah fitur: ubah ke huruf kapital
    return wrapper

@uppercase_decorator
def greet(name):
    return f"Halo, {name}"         # fungsi asli tidak berubah

print(greet("dunia"))  # HALO, DUNIA
```

### D. Reusability (Satu decorator, banyak fungsi)

```python
def simple_log(func):
    def wrapper(*args, **kwargs):
        print(f"[LOG] Memanggil {func.__name__}")
        return func(*args, **kwargs)
    return wrapper

@simple_log
def salam():
    print("Halo!")

@simple_log
def pamit():
    print("Sampai jumpa!")

salam()   # [LOG] Memanggil salam \n Halo!
pamit()   # [LOG] Memanggil pamit \n Sampai jumpa!
```

### E. Testing/Debugging

```python
def debug_decorator(func):
    def wrapper(*args, **kwargs):
        print(f"[DEBUG] Args: {args}, Kwargs: {kwargs}")
        result = func(*args, **kwargs)
        print(f"[DEBUG] Return: {result}")
        return result
    return wrapper

@debug_decorator
def perkalian(a, b):
    return a * b

perkalian(5, 3)
# [DEBUG] Args: (5, 3), Kwargs: {}
# [DEBUG] Return: 15
```

---

## 1.5 Decorator dengan Parameter (Decorator Bertingkat)

Jika decorator sendiri perlu menerima argumen:

```python
def repeat(n):                        # layer 1: menerima argumen decorator
    def decorator(func):              # layer 2: menerima fungsi
        def wrapper(*args, **kwargs): # layer 3: wrapper fungsi
            for _ in range(n):
                result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator

@repeat(3)      # ← setara: say_hi = repeat(3)(say_hi)
def say_hi():
    print("Hi!")

say_hi()
# Hi!
# Hi!
# Hi!
```

---

## 1.6 Chaining Decorator (Dekorator Berantai)

Beberapa decorator bisa diterapkan sekaligus. **Urutan evaluasi: dari bawah ke atas.**

```python
def uppercase_decorator(func):
    def wrapper(*args, **kwargs):
        result = func(*args, **kwargs)
        return result.upper()
    return wrapper

def exclamation_decorator(func):
    def wrapper(*args, **kwargs):
        result = func(*args, **kwargs)
        return result + "!"
    return wrapper

@uppercase_decorator         # diterapkan ke-2 (paling luar)
@exclamation_decorator       # diterapkan ke-1 (paling dalam)
def greet(name):
    return f"Hello, {name}"

print(greet("Kaitlyn"))  # HELLO, KAITLYN!
# Alur: greet("Kaitlyn") → "Hello, Kaitlyn" → "Hello, Kaitlyn!" → "HELLO, KAITLYN!"
```

---

## 1.7 `functools.wraps` — Menjaga Metadata

Tanpa `functools.wraps`, metadata fungsi asli (nama, docstring) **hilang** karena tertimpa oleh wrapper:

```python
import functools

def my_decorator(func):
    @functools.wraps(func)          # ← ini yang menjaga metadata
    def wrapper(*args, **kwargs):
        print("Wrapper called")
        return func(*args, **kwargs)
    return wrapper

@my_decorator
def example():
    """This is an example function"""
    print("Example function")

print(example.__name__)  # example  (bukan 'wrapper')
print(example.__doc__)   # This is an example function
```

**Mengapa penting?** Karena debugging tools, dokumentasi otomatis (Sphinx), dan framework (Flask, FastAPI) bergantung pada `__name__` dan `__doc__` untuk mengidentifikasi fungsi.

---

## 1.8 Contoh Nyata: Validasi Input dengan functools.wraps

```python
import functools

def validasi_umur_positif(func):
    @functools.wraps(func)
    def wrapper(age):
        if not isinstance(age, int) or age <= 0:
            raise ValueError("Validasi Gagal: Umur harus angka positif")
        return func(age)
    return wrapper

@validasi_umur_positif
def set_age_user1(age):
    print(f"Umur user1 diset ke {age}")

@validasi_umur_positif
def set_age_user2(age):
    print(f"Umur user2 diset ke {age}")

set_age_user1(25)      # Umur user1 diset ke 25
set_age_user2(-5)      # ValueError: Validasi Gagal: Umur harus angka positif
```

---

## 1.9 Soal Praktikum & Jawaban

**Soal 1 — log_decorator**
```python
def log_decorator(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        print("Memulai fungsi...")
        result = func(*args, **kwargs)
        print("Fungsi selesai.")
        return result
    return wrapper

@log_decorator
def hello(name):
    print(f"Hello, {name}")

hello("Budi")
# Memulai fungsi...
# Hello, Budi
# Fungsi selesai.
```

**Soal 2 — double_result**
```python
def double_result(func):
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs) * 2
    return wrapper

@double_result
def get_number():
    return 5

print(get_number())  # 10
```

**Soal 3 — log_args**
```python
def log_args(func):
    def wrapper(*args, **kwargs):
        print(f"Fungsi dipanggil dengan argumen: {args}")
        return func(*args, **kwargs)
    return wrapper

@log_args
def multiply(a, b):
    return a * b

multiply(3, 4)  # Fungsi dipanggil dengan argumen: (3, 4)
```

**Soal 4 — repeat(n)**
```python
def repeat(n):
    def decorator(func):
        def wrapper(*args, **kwargs):
            for _ in range(n):
                func(*args, **kwargs)
        return wrapper
    return decorator

@repeat(3)
def say_hi():
    print("Hi!")

say_hi()
# Hi!
# Hi!
# Hi!
```

---

---

# Minggu 12 — Functional Design Patterns: PyMonad Library

## 2.1 Konsep Dasar: Monad dan Functor

### Functor
Sebuah kontainer yang bisa di-`map` — yaitu menerapkan fungsi ke nilai di dalamnya tanpa mengubah struktur kontainernya.

```
Functor: map(f, Container(x)) = Container(f(x))
```

### Monad
Monad adalah **design pattern** yang:
- Membungkus nilai dalam konteks tertentu (sukses/gagal, ada/tidak ada)
- Memungkinkan **chaining operasi** secara berurutan (left-to-right)
- Menangani side-effect atau kondisi khusus secara elegan

> **Analogi:** Monad seperti "conveyor belt" di pabrik. Produk (nilai) berjalan dari satu mesin (fungsi) ke mesin berikutnya. Jika ada masalah di satu titik, produk diarahkan ke jalur error — mesin-mesin berikutnya tidak dieksekusi.

### Tiga Hukum Monad
1. **Left Identity:** `unit(a).bind(f) == f(a)`
2. **Right Identity:** `m.bind(unit) == m`
3. **Associativity:** `m.bind(f).bind(g) == m.bind(x => f(x).bind(g))`

---

## 2.2 PyMonad: Instalasi & Setup

```bash
pip install PyMonad
```

PyMonad menyediakan: **Maybe**, **Either**, **List**, **Reader**, **Writer**, **State**, **IO**, dan `@curry` decorator.

---

## 2.3 Maybe Monad

Digunakan ketika komputasi **mungkin gagal** (nilai bisa `None`/tidak ada). Terdiri dari dua state:
- `Just(value)` — ada nilai
- `Nothing` — tidak ada nilai (operasi gagal/tidak valid)

```python
from pymonad.maybe import Maybe, Just, Nothing
from pymonad.tools import curry

@curry(2)
def safe_divide(y, x):
    if y == 0:
        return Nothing
    return Just(x / y)

@curry(1)
def tambah_satu(x):
    return Just(x + 1)

# Chaining dengan .then()
hasil = (Maybe.insert(10)
              .then(tambah_satu)      # Just(11)
              .then(safe_divide(2))   # Just(5.5)
              .then(safe_divide(0))   # Nothing — berhenti di sini
              .then(tambah_satu))     # tidak dieksekusi

print(hasil)  # Nothing

# Mengekstrak nilai dengan .maybe()
nilai = hasil.maybe(default=-1, function=lambda x: x)
print(nilai)  # -1  (karena Nothing, kembalikan default)
```

### Contoh praktis: Lookup yang mungkin gagal

```python
from pymonad.maybe import Maybe, Just, Nothing

users = {1: "Alice", 2: "Bob"}
emails = {"Alice": "alice@mail.com"}

def get_user(user_id):
    user = users.get(user_id)
    return Just(user) if user else Nothing

def get_email(username):
    email = emails.get(username)
    return Just(email) if email else Nothing

# user_id=1 → Alice → alice@mail.com
hasil1 = Maybe.insert(1).bind(get_user).bind(get_email)
print(hasil1)  # Just alice@mail.com

# user_id=99 → tidak ada
hasil2 = Maybe.insert(99).bind(get_user).bind(get_email)
print(hasil2)  # Nothing
```

---

## 2.4 Either Monad

Mirip Maybe, tapi **Either menyimpan informasi error**. Terdiri dari:
- `Right(value)` — sukses (jalur kanan/benar)
- `Left(error)` — gagal, dengan pesan error

```python
# Implementasi manual Either (karena API PyMonad versi berbeda-beda)
class Either:
    pass

class Right(Either):
    def __init__(self, value):
        self.value = value
    def bind(self, func):
        return func(self.value)
    def map(self, func):
        return Right(func(self.value))
    def __repr__(self):
        return f"Right({self.value})"

class Left(Either):
    def __init__(self, error):
        self.error = error
    def bind(self, func):
        return self          # error: lewati semua langkah berikutnya
    def map(self, func):
        return self
    def __repr__(self):
        return f"Left({self.error})"

# Fungsi yang mengembalikan Either
def parse_age(value):
    try:
        age = int(value)
        return Right(age)
    except ValueError:
        return Left(f"'{value}' bukan angka valid")

def validate_age(age):
    if age < 0 or age > 150:
        return Left(f"Umur {age} tidak realistis")
    return Right(age)

def format_age(age):
    return Right(f"Umur Anda: {age} tahun")

# Chaining
def process_age(input_str):
    return (parse_age(input_str)
            .bind(validate_age)
            .bind(format_age))

print(process_age("25"))    # Right(Umur Anda: 25 tahun)
print(process_age("abc"))   # Left('abc' bukan angka valid)
print(process_age("-5"))    # Left(Umur -5 tidak realistis)
```

---

## 2.5 Railway Oriented Programming (ROP)

ROP adalah **metafora visual** untuk Either Monad dalam konteks error handling. Bayangkan dua jalur rel kereta:

```
Input ──►[Validate]──►[Transform]──►[Save]──►[Notify]──► Output (SUCCESS)
              │              │          │         │
              ▼              ▼          ▼         ▼
         ════════════════════════════════════════════════ FAILURE TRACK
```

Setiap fungsi bisa "membelokkan" data ke jalur failure. Setelah di jalur failure, **semua langkah berikutnya dilewati**.

```python
# Implementasi ROP sederhana
def validate_email(data):
    if "@" not in data.get("email", ""):
        return Left("Email tidak valid")
    return Right(data)

def validate_name(data):
    if len(data.get("name", "")) < 2:
        return Left("Nama terlalu pendek")
    return Right(data)

def save_user(data):
    # Simulasi penyimpanan
    return Right(f"User {data['name']} berhasil disimpan")

def register_user(data):
    return (Right(data)
            .bind(validate_email)
            .bind(validate_name)
            .bind(save_user))

print(register_user({"email": "alice@mail.com", "name": "Alice"}))
# Right(User Alice berhasil disimpan)

print(register_user({"email": "invalid", "name": "Alice"}))
# Left(Email tidak valid)

print(register_user({"email": "alice@mail.com", "name": "A"}))
# Left(Nama terlalu pendek)
```

---

## 2.6 List Monad

Merepresentasikan komputasi yang menghasilkan **banyak kemungkinan nilai** sekaligus (non-determinisme).

```python
from pymonad.list import ListMonad

# Kombinasi semua kemungkinan
angka = ListMonad(1, 2, 3)
huruf = ListMonad("a", "b")

# Cartesian product via monad chaining
hasil = angka.bind(lambda n: huruf.bind(lambda h: ListMonad(f"{n}{h}")))
print(hasil)  # ['1a', '1b', '2a', '2b', '3a', '3b']
```

---

## 2.7 `@curry` — Currying dengan PyMonad

Currying = mengubah fungsi dengan banyak argumen menjadi serangkaian fungsi satu-argumen:

```python
from pymonad.tools import curry

@curry(3)   # fungsi ini membutuhkan 3 argumen
def volume(panjang, lebar, tinggi):
    return panjang * lebar * tinggi

# Partial application: bisa dipanggil sebagian
vol_dengan_p5 = volume(5)          # tunggu lebar dan tinggi
vol_p5_l3     = vol_dengan_p5(3)   # tunggu tinggi
hasil         = vol_p5_l3(2)       # 5 * 3 * 2 = 30

print(hasil)  # 30

# Atau langsung semua argumen
print(volume(5, 3, 2))  # 30
```

---

## 2.8 Komposisi Operasi Monadik

```python
from pymonad.tools import curry
from pymonad.maybe import Maybe, Just, Nothing

@curry(2)
def safe_sqrt(x):
    if x < 0:
        return Nothing
    return Just(x ** 0.5)

@curry(2)
def safe_log(x):
    import math
    if x <= 0:
        return Nothing
    return Just(math.log(x))

# Pipeline: akar → log
pipeline = (Maybe.insert(16)
                 .then(safe_sqrt(None))   # Just(4.0)
                 .then(safe_log(None)))   # Just(1.386...)

print(pipeline)  # Just 1.3862943611198906
```

---

---

# Minggu 13 — Concurrency dan Parallel Functional Programming

## 3.1 Konsep Dasar

| Istilah | Definisi | Analogi |
|---|---|---|
| **Concurrency** | Mengelola banyak tugas sekaligus (bergantian) | 1 mekanik mengerjakan 3 mobil bergantian |
| **Parallelism** | Benar-benar berjalan bersamaan di beberapa CPU | 3 mekanik mengerjakan 3 mobil serentak |
| **Async** | Concurrency berbasis event/non-blocking I/O | Mekanik order part, sambil tunggu kerjakan hal lain |

### Kapan pakai apa?

| Jenis Tugas | Solusi Terbaik | Contoh |
|---|---|---|
| **I/O-bound** (menunggu network, disk) | `threading` atau `asyncio` | Web scraping, API calls |
| **CPU-bound** (komputasi berat) | `multiprocessing` | Image processing, ML training |
| **I/O-bound async** | `asyncio` + `await` | Server web, chat apps |

---

## 3.2 Immutability dan Thread Safety

Dalam Functional Programming, **immutability** adalah kunci keamanan konkurensi:
- Data yang tidak bisa diubah **tidak bisa menyebabkan race condition**
- Fungsi murni (pure functions) **aman dijalankan paralel** karena tidak berbagi state

```python
# BERBAHAYA: shared mutable state
counter = 0
def increment():
    global counter
    counter += 1   # race condition jika dijalankan paralel!

# AMAN: tidak ada shared state, fungsi murni
def process(x):
    return x * 2   # tidak ada state yang dibagikan

from multiprocessing import Pool
data = [1, 2, 3, 4, 5]
with Pool() as p:
    hasil = p.map(process, data)  # aman karena process() adalah pure function
print(hasil)  # [2, 4, 6, 8, 10]
```

---

## 3.3 Parallel Map, Filter, dan Reduce

### Parallel Map dengan `multiprocessing.Pool`

```python
from multiprocessing import Pool
import time

def kuadrat(x):
    time.sleep(0.1)   # simulasi komputasi berat
    return x ** 2

data = list(range(10))

# Sequential
start = time.time()
hasil_seq = list(map(kuadrat, data))
print(f"Sequential: {time.time()-start:.2f}s")  # ~1.0s

# Parallel
start = time.time()
with Pool(processes=4) as pool:
    hasil_par = pool.map(kuadrat, data)
print(f"Parallel:   {time.time()-start:.2f}s")  # ~0.3s

print(hasil_par)  # [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```

### Parallel dengan `concurrent.futures`

```python
from concurrent.futures import ProcessPoolExecutor, ThreadPoolExecutor

def proses_data(n):
    return n ** 2

angka = [1, 2, 3, 4, 5]

# ProcessPoolExecutor — untuk CPU-bound
with ProcessPoolExecutor(max_workers=4) as executor:
    hasil = list(executor.map(proses_data, angka))
print("CPU-bound:", hasil)  # [1, 4, 9, 16, 25]

# ThreadPoolExecutor — untuk I/O-bound
import urllib.request

def fetch_url(url):
    with urllib.request.urlopen(url, timeout=5) as r:
        return len(r.read())

urls = [
    "https://www.python.org",
    "https://www.wikipedia.org",
]

with ThreadPoolExecutor(max_workers=2) as executor:
    ukuran = list(executor.map(fetch_url, urls))
print("I/O-bound:", ukuran)
```

---

## 3.4 Global Interpreter Lock (GIL)

GIL adalah mekanisme di CPython yang **hanya mengizinkan satu thread menjalankan Python bytecode pada satu waktu**, bahkan di mesin multi-core.

```
Thread 1: ████░░░░░░████░░░░░░████  ← bergantian dapat giliran
Thread 2: ░░░░████░░░░░░████░░░░░░
                    ↑ GIL mengatur giliran
```

**Implikasi:**
- `threading` → cocok untuk I/O-bound (saat I/O berlangsung, GIL dilepas)
- `multiprocessing` → **bypass GIL** dengan membuat proses terpisah (masing-masing punya GIL sendiri)

---

## 3.5 Multiprocessing dalam Functional Programming

```python
from multiprocessing import Pool
from functools import reduce

# Pipeline fungsional yang diparalelkan
data = list(range(1, 21))

# Step 1: filter bilangan genap (sequential — ringan)
genap = list(filter(lambda x: x % 2 == 0, data))

# Step 2: kuadratkan secara paralel (CPU-bound)
def kuadrat(x):
    return x * x

with Pool(4) as p:
    hasil_kuadrat = p.map(kuadrat, genap)

# Step 3: jumlahkan (sequential — hasil sudah kecil)
total = reduce(lambda a, b: a + b, hasil_kuadrat)

print(f"Bilangan genap: {genap}")
print(f"Kuadrat: {hasil_kuadrat}")
print(f"Total: {total}")
# Total: 4+16+36+64+100+144+196+256+324+400 = 1540
```

---

## 3.6 Async/Await dengan Pendekatan Fungsional

```python
import asyncio

# Fungsi async = coroutine (bisa di-pause dan dilanjutkan)
async def fetch_data(name, delay):
    print(f"[START] {name}")
    await asyncio.sleep(delay)   # non-blocking wait
    print(f"[DONE]  {name}")
    return f"Data dari {name}"

async def main():
    # Jalankan semua task secara concurrent
    tasks = [
        fetch_data("API-1", 2),
        fetch_data("API-2", 1),
        fetch_data("API-3", 3),
    ]
    hasil = await asyncio.gather(*tasks)   # tunggu semua selesai
    return hasil

hasil = asyncio.run(main())
print(hasil)
# [START] API-1
# [START] API-2
# [START] API-3
# [DONE]  API-2   (selesai duluan, delay=1)
# [DONE]  API-1
# [DONE]  API-3
# ['Data dari API-1', 'Data dari API-2', 'Data dari API-3']
# Total waktu: ~3 detik (bukan 2+1+3=6 detik)
```

---

## 3.7 Race Condition dan Deadlock Prevention

### Race Condition
Terjadi ketika dua thread/proses **menulis ke data yang sama** secara bersamaan.

```python
# Berbahaya!
from threading import Thread
counter = 0

def naik():
    global counter
    for _ in range(100000):
        counter += 1   # bisa corrupt karena dua thread akses bersamaan

t1 = Thread(target=naik)
t2 = Thread(target=naik)
t1.start(); t2.start()
t1.join(); t2.join()
print(counter)   # bisa kurang dari 200000 karena race condition!
```

```python
# Solusi 1: Lock
from threading import Lock
lock = Lock()
counter = 0

def naik_aman():
    global counter
    for _ in range(100000):
        with lock:       # hanya satu thread masuk blok ini
            counter += 1

# Solusi 2: Gunakan immutable + functional approach (TERBAIK)
# Tidak ada shared mutable state → tidak ada race condition
hasil = sum(pool.map(lambda _: 1, range(200000)))
```

### Deadlock
Terjadi ketika dua proses **saling menunggu** satu sama lain.

```
Thread A menunggu Lock B (yang dipegang Thread B)
Thread B menunggu Lock A (yang dipegang Thread A)
→ keduanya stuck selamanya
```

**Pencegahan:** Selalu akuisisi lock dalam **urutan yang sama** di semua thread, atau gunakan pendekatan fungsional dengan immutable data.

---

## 3.8 Futures dan Promises

**Future** = representasi nilai yang **belum tersedia** (akan ada di masa depan).

```python
from concurrent.futures import Future, ThreadPoolExecutor
import time

def hitung_lama(n):
    time.sleep(2)
    return n * n

with ThreadPoolExecutor() as executor:
    future = executor.submit(hitung_lama, 7)   # mulai komputasi di background
    print("Komputasi sedang berjalan di background...")
    print("Kita bisa lakukan hal lain...")
    hasil = future.result()                    # tunggu hasil (blocking)
    print(f"Hasil: {hasil}")                   # 49

# Future dengan callback
def on_done(f):
    print(f"Callback dipanggil! Hasil: {f.result()}")

with ThreadPoolExecutor() as executor:
    future = executor.submit(hitung_lama, 5)
    future.add_done_callback(on_done)          # dipanggil otomatis saat selesai
```

---

---

# Minggu 14 — Functional Programming in Big Data

## 4.1 Apache Spark dan Functional Programming

**Apache Spark** adalah engine komputasi terdistribusi untuk data besar. Spark mengadopsi prinsip-prinsip functional programming secara mendalam:

| Konsep FP | Implementasi di Spark |
|---|---|
| Immutability | RDD/DataFrame bersifat immutable |
| Pure Functions | Transformasi (map, filter, dll) adalah pure |
| Lazy Evaluation | Transformasi tidak dievaluasi sampai ada action |
| Higher-Order Functions | `map()`, `filter()`, `reduce()`, `flatMap()` |

### Arsitektur Spark

```
Driver Program
    │
    ├── SparkContext
    │       │
    │       ├── Worker Node 1 (Executor) ─── Partition 1, 2
    │       ├── Worker Node 2 (Executor) ─── Partition 3, 4
    │       └── Worker Node 3 (Executor) ─── Partition 5, 6
    │
    └── Cluster Manager (YARN / Mesos / Standalone)
```

---

## 4.2 MapReduce Paradigm

MapReduce adalah **paradigma pemrograman paralel** untuk memproses data besar, diperkenalkan Google (2004).

### Alur MapReduce

```
Input Data
    │
    ▼
[MAP] — setiap record diproses secara paralel → menghasilkan key-value pairs
    │
    ▼
[SHUFFLE & SORT] — key yang sama dikumpulkan
    │
    ▼
[REDUCE] — agregasi per key
    │
    ▼
Output
```

### Contoh: Word Count

```python
# Implementasi MapReduce manual dengan Python
from functools import reduce
from collections import defaultdict

teks = [
    "the quick brown fox",
    "the fox jumped over",
    "the lazy dog"
]

# MAP: setiap kata → (kata, 1)
def mapper(baris):
    return [(kata, 1) for kata in baris.split()]

mapped = []
for baris in teks:
    mapped.extend(mapper(baris))

print("Setelah MAP:", mapped[:6])
# [('the', 1), ('quick', 1), ('brown', 1), ('fox', 1), ('the', 1), ...]

# SHUFFLE: kelompokkan per key
shuffled = defaultdict(list)
for kata, count in mapped:
    shuffled[kata].append(count)

# REDUCE: jumlahkan per key
def reducer(key, values):
    return (key, sum(values))

hasil = dict(reducer(k, v) for k, v in shuffled.items())
print("Hasil:", sorted(hasil.items(), key=lambda x: -x[1]))
# [('the', 3), ('fox', 2), ('quick', 1), ...]
```

---

## 4.3 PySpark Dasar

```python
from pyspark.sql import SparkSession
from pyspark import SparkContext

# Inisialisasi Spark
spark = SparkSession.builder \
    .appName("FP_BigData") \
    .master("local[*]") \
    .getOrCreate()

sc = spark.sparkContext

# ============================================================
# RDD (Resilient Distributed Dataset)
# ============================================================

# Buat RDD
data = sc.parallelize([1, 2, 3, 4, 5, 6, 7, 8, 9, 10])

# Transformations (lazy — belum dieksekusi)
genap  = data.filter(lambda x: x % 2 == 0)    # [2,4,6,8,10]
kuadrat = genap.map(lambda x: x ** 2)          # [4,16,36,64,100]

# Action (memicu eksekusi)
total = kuadrat.reduce(lambda a, b: a + b)
print(f"Total: {total}")   # 220

semua = kuadrat.collect()
print(f"Semua: {semua}")   # [4, 16, 36, 64, 100]

# ============================================================
# Word Count dengan PySpark
# ============================================================
teks_rdd = sc.parallelize([
    "spark is great",
    "spark is fast",
    "hadoop is slow"
])

word_count = (teks_rdd
    .flatMap(lambda line: line.split(" "))    # pisah kata
    .map(lambda word: (word, 1))              # buat pasangan (kata, 1)
    .reduceByKey(lambda a, b: a + b))         # jumlahkan per kata

print(word_count.collect())
# [('spark', 2), ('is', 3), ('great', 1), ('fast', 1), ('hadoop', 1), ('slow', 1)]
```

---

## 4.4 Stream Processing dengan Pendekatan Fungsional

**Stream Processing** = memproses data **yang terus mengalir** secara real-time (bukan batch).

```python
# Simulasi stream processing dengan generator (lazy evaluation)
import time
from itertools import islice

def data_stream():
    """Generator yang mensimulasikan aliran data real-time"""
    nilai = 0
    while True:
        yield nilai
        nilai += 1
        time.sleep(0.1)

def filter_genap(stream):
    return (x for x in stream if x % 2 == 0)

def kuadratkan(stream):
    return (x ** 2 for x in stream)

def ambil_n(stream, n):
    return list(islice(stream, n))

# Pipeline fungsional yang lazy
pipeline = kuadratkan(filter_genap(data_stream()))
hasil = ambil_n(pipeline, 5)
print(hasil)  # [0, 4, 16, 36, 64]
```

---

## 4.5 Data Pipeline Design

**Data Pipeline** = serangkaian transformasi data yang mengalir dari sumber ke tujuan.

```python
from functools import reduce

# Pola: Pipeline sebagai komposisi fungsi
def compose(*functions):
    """Komposisi fungsi: compose(f, g, h)(x) = h(g(f(x)))"""
    return reduce(lambda f, g: lambda x: g(f(x)), functions)

# Definisi langkah-langkah pipeline
def extract(data):
    """Ambil data mentah"""
    print(f"[EXTRACT] {len(data)} records")
    return data

def clean(data):
    """Bersihkan: hapus None, strip whitespace"""
    cleaned = [x.strip() for x in data if x is not None and x.strip()]
    print(f"[CLEAN] {len(cleaned)} records setelah dibersihkan")
    return cleaned

def transform(data):
    """Transform: uppercase"""
    result = [x.upper() for x in data]
    print(f"[TRANSFORM] {len(result)} records ditransform")
    return result

def load(data):
    """Load: simulasi penyimpanan"""
    print(f"[LOAD] Menyimpan {len(data)} records")
    return data

# Buat pipeline
pipeline = compose(extract, clean, transform, load)

# Jalankan
raw_data = ["  Alice  ", None, "Bob", "  Charlie  ", ""]
hasil = pipeline(raw_data)
print("Output:", hasil)
# [EXTRACT] 5 records
# [CLEAN] 3 records setelah dibersihkan
# [TRANSFORM] 3 records ditransform
# [LOAD] Menyimpan 3 records
# Output: ['ALICE', 'BOB', 'CHARLIE']
```

---

## 4.6 Perbandingan: Python vs PySpark untuk Data Pipeline

```python
# PYTHON (lokal, data kecil)
data = [1, 2, 3, 4, 5]
hasil = list(map(lambda x: x**2, filter(lambda x: x%2==0, data)))
# [4, 16]

# PYSPARK (terdistribusi, data besar — sintaks serupa!)
rdd = sc.parallelize(range(1, 1000001))
hasil = rdd.filter(lambda x: x%2==0).map(lambda x: x**2).collect()
```

---

## 4.7 Best Practices dalam Production

1. **Gunakan immutable data** → hindari bug yang sulit di-trace
2. **Pisahkan transformasi dari I/O** → buat unit testing lebih mudah
3. **Gunakan lazy evaluation** → hemat memori, proses hanya yang perlu
4. **Tulis pure functions** → mudah di-parallelize dan di-test
5. **Log setiap tahap pipeline** → mudah debugging di production
6. **Handle error secara eksplisit** → gunakan Either/Result, bukan exception tersembunyi
7. **Idempotent operations** → operasi yang aman dijalankan ulang jika gagal

---

---

# Minggu 15 — Pengantar Scala dan Functional Programming

## 5.1 Mengapa Scala?

Scala adalah bahasa yang **menggabungkan OOP dan FP** secara seamless. Berjalan di JVM, sehingga bisa menggunakan library Java.

| Fitur | Python | Scala |
|---|---|---|
| Paradigma | Multi (OOP + FP) | Multi (OOP + FP, lebih kuat di FP) |
| Typing | Dynamic | Static (dengan type inference) |
| Performa | Lambat untuk CPU-bound | Lebih cepat (JVM) |
| Big Data | PySpark | Spark native (Scala) |
| Immutability | Opsional | Didorong (val vs var) |

---

## 5.2 Sintaks Dasar Scala

```scala
// ============================================================
// Variabel
// ============================================================
val x = 10        // immutable (seperti final di Java / const)
var y = 20        // mutable
val nama: String = "Alice"   // dengan type annotation

// ============================================================
// Fungsi
// ============================================================
def tambah(a: Int, b: Int): Int = {
    a + b    // ekspresi terakhir otomatis jadi return value
}

// Fungsi satu baris
def kuadrat(x: Int): Int = x * x

// Fungsi anonim (lambda)
val double = (x: Int) => x * 2
println(double(5))   // 10

// ============================================================
// Collections (immutable by default)
// ============================================================
val daftar = List(1, 2, 3, 4, 5)
val map    = Map("a" -> 1, "b" -> 2)
val set    = Set(1, 2, 3)

// ============================================================
// Higher-Order Functions
// ============================================================
val genap     = daftar.filter(_ % 2 == 0)     // List(2, 4)
val kuadrats  = daftar.map(x => x * x)         // List(1, 4, 9, 16, 25)
val total     = daftar.reduce(_ + _)            // 15
val totalFold = daftar.foldLeft(0)(_ + _)       // 15

println(genap)    // List(2, 4)
println(kuadrats) // List(1, 4, 9, 16, 25)
```

---

## 5.3 Pattern Matching

Pattern matching di Scala jauh lebih kuat dari `switch` di Java:

```scala
// Matching nilai sederhana
def describe(x: Any): String = x match {
    case 0          => "nol"
    case i: Int     => s"integer: $i"
    case s: String  => s"string: '$s'"
    case _          => "sesuatu yang lain"
}

println(describe(0))       // nol
println(describe(42))      // integer: 42
println(describe("hello")) // string: 'hello'
println(describe(3.14))    // sesuatu yang lain

// Matching dengan guard (kondisi tambahan)
def kategorikan(n: Int): String = n match {
    case x if x < 0  => "negatif"
    case 0            => "nol"
    case x if x < 10 => "kecil"
    case _            => "besar"
}

println(kategorikan(-5))  // negatif
println(kategorikan(0))   // nol
println(kategorikan(7))   // kecil
println(kategorikan(100)) // besar
```

---

## 5.4 Case Classes

Case class adalah immutable data class dengan banyak fitur otomatis:

```scala
// Definisi case class
case class Mahasiswa(nama: String, nim: String, ipk: Double)

val m1 = Mahasiswa("Alice", "124450001", 3.8)
val m2 = Mahasiswa("Bob",   "124450002", 3.5)

// Otomatis: toString, equals, hashCode, copy
println(m1)              // Mahasiswa(Alice,124450001,3.8)
println(m1 == m2)        // false

// copy: buat objek baru dengan beberapa field diubah
val m3 = m1.copy(ipk = 3.9)
println(m3)              // Mahasiswa(Alice,124450001,3.9)
println(m1)              // tidak berubah: Mahasiswa(Alice,124450001,3.8)

// Pattern matching dengan case class
def evaluasi(m: Mahasiswa): String = m match {
    case Mahasiswa(_, _, ipk) if ipk >= 3.5 => "Cumlaude"
    case Mahasiswa(_, _, ipk) if ipk >= 3.0 => "Sangat Memuaskan"
    case _                                   => "Memuaskan"
}

println(evaluasi(m1))  // Cumlaude
println(evaluasi(m2))  // Cumlaude
```

---

## 5.5 Collections dan Higher-Order Functions

```scala
val angka = List(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)

// map, filter, reduce
val genap       = angka.filter(_ % 2 == 0)           // List(2,4,6,8,10)
val kuadrat     = genap.map(x => x * x)               // List(4,16,36,64,100)
val total       = kuadrat.reduce(_ + _)               // 220

// flatMap (seperti map + flatten)
val kata = List("hello world", "scala is fun")
val semua_kata  = kata.flatMap(_.split(" "))
// List("hello", "world", "scala", "is", "fun")

// groupBy
val dikelompokkan = angka.groupBy(_ % 2 == 0)
// Map(true -> List(2,4,6,8,10), false -> List(1,3,5,7,9))

// Chaining — pipeline fungsional
val hasil = (1 to 100)
    .filter(_ % 3 == 0)      // kelipatan 3
    .map(_ * 2)               // dikali 2
    .take(5)                  // ambil 5 pertama
    .sum                      // jumlahkan

println(hasil)  // 90   (6+12+18+24+30=90)
```

---

## 5.6 For Comprehensions

Sintaks elegan untuk kombinasi operasi monadik (map, flatMap, filter):

```scala
// Ekuivalen dengan: List(1,2,3).flatMap(x => List(1,2,3).map(y => x+y))
val kombinasi = for {
    x <- List(1, 2, 3)
    y <- List(1, 2, 3)
    if x != y           // guard
} yield (x, y, x + y)

kombinasi.foreach(println)
// (1,2,3), (1,3,4), (2,1,3), (2,3,5), (3,1,4), (3,2,5)

// For comprehension dengan Option (setara Maybe)
val nilaiMap = Map("Alice" -> 90, "Bob" -> 85)

def cariNilai(nama: String): Option[Int] = nilaiMap.get(nama)
def konversiHuruf(n: Int): Option[String] = n match {
    case x if x >= 90 => Some("A")
    case x if x >= 80 => Some("B")
    case _             => Some("C")
}

val hasilAlice = for {
    nilai <- cariNilai("Alice")
    huruf <- konversiHuruf(nilai)
} yield s"Alice mendapat $huruf"

println(hasilAlice)  // Some(Alice mendapat A)

val hasilZara = for {
    nilai <- cariNilai("Zara")   // None — Zara tidak ada
    huruf <- konversiHuruf(nilai)
} yield s"Zara mendapat $huruf"

println(hasilZara)   // None
```

---

## 5.7 Perbandingan Python vs Scala untuk Functional Programming

| Aspek | Python | Scala |
|---|---|---|
| **Immutability** | `tuple`, `frozenset` (opsional) | `val`, immutable collections (default) |
| **Pattern Matching** | `match` (Python 3.10+, terbatas) | `match ... case` yang sangat kuat |
| **Type System** | Dynamic | Static + Type Inference |
| **Currying** | Manual / `functools.partial` | Native dengan `_` placeholder |
| **For Comprehensions** | List/dict/gen comprehension | For comprehension (lebih powerful, monadik) |
| **Tail Recursion** | Tidak dioptimasi (stack overflow) | `@tailrec` annotation |
| **Concurrency** | GIL menjadi hambatan | Akka, Futures tanpa GIL |
| **Big Data** | PySpark (wrapper) | Spark native (performa optimal) |
| **Ekosistem Data Science** | ⭐⭐⭐⭐⭐ (NumPy, Pandas, sklearn) | ⭐⭐⭐ (terutama untuk data engineering) |

### Contoh: Fungsi yang sama di Python vs Scala

```python
# Python
def proses(data):
    return list(
        map(lambda x: x**2,
            filter(lambda x: x % 2 == 0, data))
    )

print(proses([1,2,3,4,5]))  # [4, 16]
```

```scala
// Scala
def proses(data: List[Int]): List[Int] =
    data.filter(_ % 2 == 0).map(x => x * x)

println(proses(List(1,2,3,4,5)))  // List(4, 16)
```

---

---

# Ringkasan Konsep Kunci

## Peta Konsep Keseluruhan

```
FUNCTIONAL PROGRAMMING
│
├── Prinsip Dasar
│   ├── Pure Functions (tidak ada side effect)
│   ├── Immutability (data tidak diubah)
│   ├── First-Class Functions (fungsi = data)
│   └── Declarative Style (what, bukan how)
│
├── [Minggu 11] DECORATOR
│   ├── Inner Functions & Closure
│   ├── Higher-Order Functions
│   ├── @syntax sugar
│   ├── *args, **kwargs
│   ├── functools.wraps
│   ├── Decorator dengan parameter
│   └── Chaining decorator
│
├── [Minggu 12] DESIGN PATTERNS (PyMonad)
│   ├── Functor (mappable container)
│   ├── Maybe (Just/Nothing) → handle None
│   ├── Either (Right/Left) → handle error + info
│   ├── List Monad → non-determinisme
│   ├── Railway Oriented Programming
│   └── @curry → partial application
│
├── [Minggu 13] CONCURRENCY & PARALLEL
│   ├── Immutability → thread safety
│   ├── multiprocessing.Pool → CPU-bound
│   ├── concurrent.futures → abstraksi tinggi
│   ├── asyncio → I/O-bound async
│   ├── GIL dan implikasinya
│   └── Future & Promises
│
├── [Minggu 14] BIG DATA
│   ├── Apache Spark (RDD, DataFrame)
│   ├── MapReduce Paradigm
│   ├── Lazy Evaluation
│   ├── Stream Processing
│   └── Data Pipeline Design
│
└── [Minggu 15] SCALA
    ├── val vs var (immutability)
    ├── Pattern Matching (powerful)
    ├── Case Classes
    ├── HOF: map, filter, reduce, flatMap
    └── For Comprehensions
```

---

## Tabel Perbandingan Cepat: Kapan Pakai Apa?

| Kebutuhan | Tool/Pattern |
|---|---|
| Tambah logging/timing ke banyak fungsi | **Decorator** |
| Handle nilai yang mungkin None | **Maybe Monad** |
| Handle error dengan informasi detail | **Either Monad** |
| Pipeline dengan banyak langkah yang bisa gagal | **Railway Oriented Programming** |
| Komputasi berat di banyak CPU | **multiprocessing.Pool** |
| Request jaringan yang banyak, non-blocking | **asyncio** |
| Proses data ratusan GB | **Apache Spark** |
| Bahasa yang aman untuk FP + Big Data | **Scala** |

---

---

# Latihan Soal UAS

## Soal Teori

**1.** Jelaskan perbedaan antara `*args` dan `**kwargs`. Kapan masing-masing digunakan?

**2.** Mengapa kita perlu `functools.wraps` dalam menulis decorator? Apa yang terjadi jika tidak digunakan?

**3.** Jelaskan perbedaan antara `Just(5)` dan `Nothing` pada Maybe Monad. Bagaimana chaining bekerja ketika bertemu `Nothing`?

**4.** Apa itu GIL (Global Interpreter Lock) dan mengapa ini penting saat memilih antara `threading` dan `multiprocessing`?

**5.** Jelaskan perbedaan antara `val` dan `var` di Scala dan kaitannya dengan prinsip immutability dalam FP.

**6.** Apa itu Railway Oriented Programming? Bagaimana metafora "dua jalur rel" menjelaskan alur sukses dan gagal?

**7.** Mengapa lazy evaluation penting dalam Stream Processing dan Big Data?

---

## Soal Coding

**A. Decorator Timing**
```
Buat decorator `timer` yang mengukur waktu eksekusi fungsi.
Output: "Fungsi <nama> selesai dalam X.XXXXs"
```

```python
import time
import functools

def timer(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        elapsed = time.time() - start
        print(f"Fungsi {func.__name__} selesai dalam {elapsed:.4f}s")
        return result
    return wrapper

@timer
def hitung_total(n):
    return sum(range(n))

hitung_total(1000000)
# Fungsi hitung_total selesai dalam 0.0423s
```

**B. Chaining Decorator**
```
Buat dua decorator: `bold` yang membungkus teks dengan **teks**,
dan `italic` yang membungkus dengan _teks_.
Terapkan keduanya pada fungsi greet(name).
```

```python
def bold(func):
    def wrapper(*args, **kwargs):
        return f"**{func(*args, **kwargs)}**"
    return wrapper

def italic(func):
    def wrapper(*args, **kwargs):
        return f"_{func(*args, **kwargs)}_"
    return wrapper

@bold
@italic
def greet(name):
    return f"Hello, {name}"

print(greet("Fitra"))  # **_Hello, Fitra_**
```

**C. Maybe Monad Pipeline**
```
Buat pipeline menggunakan Either manual yang:
1. Menerima string input (angka)
2. Parse ke integer
3. Validasi harus positif
4. Hitung akar kuadrat
5. Format hasilnya
```

```python
import math

class Right:
    def __init__(self, value):
        self.value = value
    def bind(self, f):
        return f(self.value)
    def __repr__(self):
        return f"Right({self.value})"

class Left:
    def __init__(self, error):
        self.error = error
    def bind(self, _):
        return self
    def __repr__(self):
        return f"Left({self.error})"

def parse_int(s):
    try:
        return Right(int(s))
    except ValueError:
        return Left(f"'{s}' bukan angka")

def harus_positif(n):
    return Right(n) if n > 0 else Left(f"{n} bukan bilangan positif")

def akar(n):
    return Right(math.sqrt(n))

def format_hasil(n):
    return Right(f"Akar kuadrat: {n:.4f}")

def pipeline(input_str):
    return (Right(input_str)
            .bind(parse_int)
            .bind(harus_positif)
            .bind(akar)
            .bind(format_hasil))

print(pipeline("16"))    # Right(Akar kuadrat: 4.0000)
print(pipeline("-4"))    # Left(-4 bukan bilangan positif)
print(pipeline("abc"))   # Left('abc' bukan angka)
```

**D. Parallel Processing**
```
Gunakan multiprocessing.Pool untuk menghitung apakah
setiap angka dari 1-20 adalah bilangan prima secara paralel.
```

```python
from multiprocessing import Pool

def is_prima(n):
    if n < 2:
        return (n, False)
    for i in range(2, int(n**0.5) + 1):
        if n % i == 0:
            return (n, False)
    return (n, True)

if __name__ == "__main__":
    angka = list(range(1, 21))
    with Pool(4) as p:
        hasil = p.map(is_prima, angka)
    prima = [n for n, p in hasil if p]
    print("Bilangan prima:", prima)
    # Bilangan prima: [2, 3, 5, 7, 11, 13, 17, 19]
```

**E. MapReduce Word Count**
```
Implementasikan word count sederhana dengan pola MapReduce
menggunakan functools.reduce dan collections.
```

```python
from functools import reduce
from collections import defaultdict

dokumen = [
    "functional programming is great",
    "programming in python is fun",
    "great python programs use functional style"
]

# MAP
mapped = reduce(
    lambda acc, baris: acc + [(kata, 1) for kata in baris.split()],
    dokumen,
    []
)

# SHUFFLE (group by key)
grouped = defaultdict(list)
for kata, count in mapped:
    grouped[kata].append(count)

# REDUCE
word_count = {kata: sum(counts) for kata, counts in grouped.items()}

# Top 5
top5 = sorted(word_count.items(), key=lambda x: -x[1])[:5]
print("Top 5 kata:", top5)
# [('functional', 2), ('programming', 2), ('python', 2), ('great', 2), ...]
```

---

## Jawaban Soal Teori (Singkat)

**1.** `*args` = argumen posisional variabel (dibungkus tuple). `**kwargs` = argumen keyword variabel (dibungkus dict). Gunakan `*args` saat jumlah argumen tidak tentu, `**kwargs` saat nama argumen tidak tentu.

**2.** Tanpa `functools.wraps`, `func.__name__` dan `func.__doc__` akan mengembalikan info dari `wrapper`, bukan fungsi asli. Ini merusak debugging, dokumentasi otomatis, dan framework yang membaca metadata fungsi.

**3.** `Just(5)` membungkus nilai 5. `Nothing` merepresentasikan ketiadaan nilai. Saat chaining bertemu `Nothing`, semua langkah `.then()` atau `.bind()` berikutnya **dilewati** — `Nothing` langsung diteruskan ke akhir.

**4.** GIL memastikan hanya satu thread yang menjalankan Python bytecode sekaligus. Untuk I/O-bound: `threading` cukup karena GIL dilepas saat I/O. Untuk CPU-bound: `multiprocessing` wajib karena setiap proses punya Python interpreter dan GIL sendiri.

**5.** `val` = immutable (tidak bisa di-reassign), `var` = mutable. Dalam FP, `val` diutamakan karena data yang tidak berubah aman untuk concurrency, mudah di-reason, dan bebas side-effect.

**6.** ROP adalah pola error handling yang membayangkan dua jalur rel: **success track** dan **failure track**. Setiap fungsi bisa mengembalikan sukses (lanjut ke langkah berikutnya) atau gagal (langsung ke jalur failure, melewati semua langkah berikutnya). Diimplementasikan dengan Either monad.

**7.** Lazy evaluation hanya memproses data **saat dibutuhkan**. Untuk stream: data mengalir tanpa perlu dimuat semua ke memori. Untuk big data: transformasi yang tidak diperlukan tidak dieksekusi, menghemat komputasi yang sangat besar.

---

*Selamat belajar dan semoga sukses UAS! 🎓*
*— Panduan ini mencakup Minggu 11–15: Decorator · PyMonad · Concurrency · Big Data · Scala*
