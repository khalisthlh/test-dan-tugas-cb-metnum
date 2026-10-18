# Catatan Metode Numerik

**Cakupan:** Galat, Akar Persamaan Non-Linear, Sistem Persamaan Linear (SPL)
**Sumber:** materi kuliah CIF63309 pertemuan 02, 04, 05, ditambah penjelasan dosen di kelas dan hasil tanya-jawab tambahan.

Setiap metode ditulis dengan urutan yang sama: **penjelasan singkat → kegunaan → rumus → langkah pengerjaan → contoh pengerjaan matematis → implementasi Python.** Semua angka pada contoh sudah dihitung ulang dengan program. Pembulatan pada tabel: 6 angka di belakang koma, kecuali ditulis lain.

Dokumen ini memakai notasi LaTeX (`$...$` dan `$$...$$`), jadi buka dengan aplikasi yang bisa merender LaTeX (VS Code preview, Obsidian, Typora, dan sejenisnya).

**Notasi sigma.** Pada rumus berderet panjang, bentuk dengan titik-titik ($\cdots$) ditemani bentuk sigma ($\sum$). Cara bacanya: $\sum_{k=a}^{b} s_k$ berarti *jumlahkan $s_k$ untuk $k=a,\,a+1,\,\ldots,\,b$*. Contoh: $\sum_{k=0}^{3}\dfrac{x^k}{k!}=1+x+\dfrac{x^2}{2}+\dfrac{x^3}{6}$. Jika batas atas lebih kecil daripada batas bawah, jumlahnya kosong dan bernilai $0$.

---

## Daftar Isi dan Peta Materi

Klik nama topik untuk loncat ke pembahasannya.

| Bagian | Topik | Kegunaan singkat |
|---|---|---|
| **Bab 1** | [Galat](#bab-1-galat) | Mengukur dan mengendalikan seberapa meleset sebuah hampiran |
| 1.1 | [Akurasi dan Presisi](#11-akurasi-dan-presisi) | Membedakan "dekat dengan nilai benar" dan "konsisten" |
| 1.2 | [Galat Mutlak dan Galat Relatif](#12-galat-mutlak-dan-galat-relatif) | Menghitung besar kesalahan jika nilai sejati diketahui |
| 1.3 | [Galat Iteratif](#13-galat-iteratif) | Menghitung kesalahan dan menghentikan iterasi jika nilai sejati tidak diketahui |
| 1.4 | [Galat Pembulatan dan Pemotongan](#14-galat-pembulatan-dan-pemotongan) | Memahami dua sumber galat dalam komputasi |
| 1.5 | [Mantisa Terbatas](#15-mantisa-terbatas) | Meniru cara komputer menyimpan angka dengan digit terbatas |
| 1.6 | [Bilangan Biner dan Floating Point](#16-bilangan-biner-dan-floating-point) | Memahami penyimpanan bilangan di komputer dan machine epsilon |
| 1.7 | [Deret Taylor](#17-deret-taylor) | Menghampiri fungsi rumit dengan penjumlahan suku sederhana |
| 1.8 | [Cara Mengurangi Galat](#18-cara-mengurangi-galat) | Memilih tindakan sesuai sumber galat |
| **Bab 2** | [Akar Persamaan Non-Linear](#bab-2-akar-persamaan-non-linear) | Mencari $x$ yang membuat $f(x)=0$ |
| 2.0 | [Konsep Dasar](#20-konsep-dasar) | Membaca $f(a)$, $f(b)$, tebakan awal, dan kriteria berhenti |
| 2.1 | [Metode Grafis](#21-metode-grafis) | Menentukan selang $[a,b]$ dari gambar atau pemindaian tanda |
| 2.2 | [Metode Bisection](#22-metode-bisection) | Cara paling aman: pasti konvergen, tetapi lambat |
| 2.3 | [Metode Regula Falsi](#23-metode-regula-falsi) | Versi Bisection yang lebih cepat lewat interpolasi linear |
| 2.4 | [Iterasi Titik Tetap](#24-iterasi-titik-tetap) | Menyelesaikan $x=g(x)$ dengan iterasi sederhana |
| 2.5 | [Metode Newton-Raphson](#25-metode-newton-raphson) | Paling cepat, tetapi butuh turunan dan tebakan awal yang baik |
| 2.6 | [Metode Secant](#26-metode-secant) | Pengganti Newton jika turunan sulit dihitung |
| 2.7 | [Perbandingan Metode Akar](#27-perbandingan-metode-akar) | Memilih metode yang cocok |
| **Bab 3** | [Sistem Persamaan Linear](#bab-3-sistem-persamaan-linear) | Menyelesaikan beberapa persamaan linear sekaligus |
| 3.1 | [Substitusi Mundur](#31-substitusi-mundur) | Menyelesaikan sistem segitiga atas |
| 3.2 | [Eliminasi Gauss](#32-eliminasi-gauss) | Mengubah sistem umum menjadi segitiga atas |
| 3.3 | [Dekomposisi LU](#33-dekomposisi-lu) | Faktorisasi sekali, dipakai untuk banyak ruas kanan |
| 3.4 | [Dekomposisi Cholesky](#34-dekomposisi-cholesky) | Faktorisasi khusus matriks simetris, kerjanya separuh |
| 3.5 | [Gauss-Jordan dan Gauss-Seidel](#35-gauss-jordan-dan-gauss-seidel) | Varian eliminasi dan metode iteratif |
| 3.6 | [Perbandingan Metode SPL](#36-perbandingan-metode-spl) | Memilih metode yang cocok |
| Lampiran | [Glosarium](#lampiran-glosarium) | Arti istilah penting |

---

# Bab 1. Galat

> **Galat** adalah selisih antara nilai yang benar dan nilai hasil hitungan (hampiran). Metode numerik hampir selalu menghasilkan nilai *hampiran*, jadi kita perlu tahu seberapa besar meleset-nya. Galat bukan tujuan, melainkan alat untuk mengukur dan mengendalikan mutu solusi.

---

## 1.1 Akurasi dan Presisi

**Penjelasan singkat**
Dua istilah yang sering tertukar:

| Istilah | Arti | Contoh papan dart |
|---|---|---|
| **Akurasi** | seberapa **dekat** hasil ukur ke nilai sebenarnya | lemparan menyebar tetapi rata-rata di sekitar pusat |
| **Presisi** | seberapa **konsisten** hasil kalau pengukuran diulang pada kondisi sama | lemparan mengumpul rapat, tetapi jauh dari pusat |

**Kegunaan**
Menilai mutu hasil ukur atau hasil hitung. Bisa presisi tinggi tetapi tidak akurat, misalnya timbangan yang selalu salah 2 kg: hasilnya konsisten, tetapi meleset.

Bagian ini konsep saja, jadi tidak ada rumus atau kode.

---

## 1.2 Galat Mutlak dan Galat Relatif

**Penjelasan singkat**
Jika nilai sejati $T$ diketahui, galat bisa dinyatakan sebagai selisih langsung (galat mutlak) atau sebagai proporsi terhadap besar nilainya (galat relatif).

**Kegunaan**

| | Galat mutlak | Galat relatif |
|---|---|---|
| Menjawab | "berapa besar meleset-nya?" | "seberapa parah meleset-nya dibanding ukuran bendanya?" |
| Bentuk | angka dengan satuan yang sama (cm, detik, dan sebagainya) | persen (tanpa satuan) |
| Cocok untuk | toleransi dalam satuan nyata (misal ±0,1 mm) | membandingkan ketepatan antar-hitungan, dan menghentikan iterasi |

**Rumus**

$$
T = P + E \qquad\Longleftrightarrow\qquad E = T - P
$$

$$
E_m = \lvert T - P \rvert \qquad\qquad E_r = \frac{E_m}{\lvert T\rvert}\times 100\%
$$

dengan $T$ = nilai sejati (*true*), $P$ = nilai pendekatan (*approximation*), $E_m$ = galat mutlak, $E_r$ = galat relatif.

**Langkah pengerjaan**
1. Tulis $T$ (nilai yang seharusnya) dan $P$ (nilai yang didapat).
2. Hitung $E_m = \lvert T - P\rvert$.
3. Hitung $E_r = \dfrac{E_m}{\lvert T\rvert}\times100\%$.

**Contoh pengerjaan: sepatu dan baju**

*Sepatu:* ukuran seharusnya $40$ cm, jadinya $39{,}6$ cm.

$$
\begin{aligned}
E_m &= \lvert 40 - 39{,}6\rvert = 0{,}4 \text{ cm}\\
E_r &= \frac{0{,}4}{40}\times100\% = 1\%
\end{aligned}
$$

*Baju:* ukuran seharusnya $100$ cm, jadinya $99{,}6$ cm.

$$
\begin{aligned}
E_m &= \lvert 100 - 99{,}6\rvert = 0{,}4 \text{ cm}\\
E_r &= \frac{0{,}4}{100}\times100\% = 0{,}4\%
\end{aligned}
$$

Meleset 0,4 cm itu sama-sama 0,4 cm, tetapi pada sepatu (kecil) terasa jauh lebih parah (1%) daripada pada baju (0,4%). Itulah gunanya galat relatif.

> **Catatan:** persentase galat relatif adalah persentase terhadap nilai sebenarnya $T$, **bukan** persentase dari galat mutlaknya. Pada sepatu, $E_r=1\%$ artinya meleset 1% dari 40 cm. Sebagian sumber menulis $E_m=T-P$ tanpa tanda mutlak; pakai $\lvert T-P\rvert$ agar tidak negatif.

**Implementasi Python**

```python
def galat(T, P):
    """Mengembalikan (galat mutlak, galat relatif dalam persen)."""
    Em = abs(T - P)
    Er = Em / abs(T) * 100
    return Em, Er

print([round(v, 6) for v in galat(40, 39.6)])     # sepatu: [0.4, 1.0]
print([round(v, 6) for v in galat(100, 99.6)])    # baju  : [0.4, 0.4]
```

---

## 1.3 Galat Iteratif

**Penjelasan singkat**
Pada soal nyata, nilai sejati $T$ sering **tidak diketahui**. Solusinya: hitung berulang, lalu lihat *seberapa besar nilai berubah dari hitungan sebelumnya*. Kalau hasil sudah hampir tidak berubah, anggap cukup baik dan berhenti.

**Kegunaan**
Menjadi "rem" untuk semua metode iteratif di Bab 2 (Bisection, Regula Falsi, Titik Tetap, Newton, Secant) dan Gauss-Seidel di Bab 3.

**Rumus**

$$
E_r = \left\lvert \frac{P_{n+1} - P_n}{P_{n+1}} \right\rvert \times 100\%
\qquad\text{berhenti jika}\qquad E_r < E_s
$$

- $P_{n+1}$ = nilai hampiran **baru**, $P_n$ = nilai hampiran **lama**.
- $E_s$ = batas galat yang kita tentukan sendiri (misal $1\%$ atau $0{,}5\%$).

**Langkah pengerjaan**
1. Tentukan $E_s$ (batas galat).
2. Beri tebakan awal $x_0$.
3. Mulai dari $n=0$.
4. Hitung nilai baru $x_{n+1}$ memakai rumus soal.
5. Hitung $E_r = \left\lvert\dfrac{x_{n+1}-x_n}{x_{n+1}}\right\rvert\times100\%$.
6. Jika $E_r < E_s$, **berhenti**; jawabannya $x_{n+1}$.
7. Jika belum, geser $x_n \leftarrow x_{n+1}$, $n \leftarrow n+1$, lalu **kembali ke langkah 4** (bukan ke langkah 2; $x_0$ dan $E_s$ tidak ditentukan ulang).

**Contoh pengerjaan:** hitung $x = \dfrac{1-x^5}{2}$ dengan $x_0 = 1{,}1$ dan $E_s = 1\%$.

*Iterasi $n=0$:*

$$
\begin{aligned}
x_1 &= \frac{1 - (1{,}1)^5}{2} = \frac{1 - 1{,}61051}{2} = \frac{-0{,}61051}{2} = -0{,}305255\\[4pt]
E_r &= \left\lvert\frac{-0{,}305255 - 1{,}1}{-0{,}305255}\right\rvert\times100\% = \left\lvert\frac{-1{,}405255}{-0{,}305255}\right\rvert\times100\% = 460{,}354\%
\end{aligned}
$$

$460\% > 1\%$, belum berhenti. Geser: $x_0 \leftarrow -0{,}305255$.

*Iterasi $n=1$:*

$$
\begin{aligned}
x_2 &= \frac{1 - (-0{,}305255)^5}{2} = \frac{1 - (-0{,}0026504)}{2} = \frac{1{,}0026504}{2} = 0{,}501325\\[4pt]
E_r &= \left\lvert\frac{0{,}501325 - (-0{,}305255)}{0{,}501325}\right\rvert\times100\% = 160{,}890\%
\end{aligned}
$$

*Hasil seluruh iterasi:*

| $n$ | $x_n$ (lama) | $x_{n+1}$ (baru) | $E_r$ (%) | Lanjut? |
|---|---|---|---|---|
| 0 | 1,100000 | −0,305255 | 460,354458 | ya |
| 1 | −0,305255 | 0,501325 | 160,889617 | ya |
| 2 | 0,501325 | 0,484167 | 3,543897 | ya |
| 3 | 0,484167 | 0,486697 | 0,519892 | **berhenti** ($<1\%$) |

**Jawaban:** $x \approx 0{,}486697$.

Cek: $\dfrac{1-(0{,}486697)^5}{2} = 0{,}48670$, sama dengan $x$, jadi benar.

> **Catatan:** pada iterasi pertama $E_r$ sering besar sekali (ratusan persen); itu wajar. Berhenti saat $E_r < E_s$.

**Implementasi Python**

```python
def iterasi_galat(g, x0, Es=1.0):
    """Iterasi x_baru = g(x_lama) sampai galat relatif < Es (persen)."""
    Xprev = x0
    n = 0
    Er = 100.0          # trik agar putaran pertama pasti masuk
    print("n\tXprev\t\tXnext\t\tEr")
    while Er > Es:
        Xnext = g(Xprev)
        Er = abs((Xnext - Xprev) / Xnext) * 100
        print("%d\t%f\t%f\t%f" % (n, Xprev, Xnext, Er))
        n += 1
        Xprev = Xnext
    return Xprev

hasil = iterasi_galat(lambda x: (1 - x**5) / 2.0, 1.1, Es=1.0)
print(hasil)     # 0.486697...
```

---

## 1.4 Galat Pembulatan dan Pemotongan

**Penjelasan singkat**
Ada **dua penyebab galat** yang harus dibedakan:

| | **Galat pembulatan** (*roundoff error*) | **Galat pemotongan** (*truncation error*) |
|---|---|---|
| Penyebab | komputer hanya bisa menyimpan angka dengan **jumlah digit terbatas** | prosedur matematika **dihentikan lebih awal** (deret tak hingga dipotong) |
| Contoh | $\tfrac13$ disimpan sebagai $0{,}333333$ | $e^x$ dihitung dengan 3 suku saja |

**Kegunaan**
Mengetahui dari mana kesalahan hitungan berasal, supaya tahu cara memperbaikinya (lihat [1.8](#18-cara-mengurangi-galat)).

**Rumus**

$$
\text{Galat numerik total} = \text{Galat pemotongan} + \text{Galat pembulatan}
$$

Deret Maclaurin $e^x$ dalam bentuk titik-titik dan bentuk sigma:

$$
e^x = 1 + x + \frac{x^2}{2!} + \frac{x^3}{3!} + \cdots \;=\; \sum_{k=0}^{\infty}\frac{x^k}{k!}
$$

Jika dipotong sehingga hanya memakai $n$ suku pertama:

$$
S_n = \sum_{k=0}^{n-1}\frac{x^k}{k!}
$$

Galat pemotongannya adalah **suku-suku yang dibuang**. Untuk $n=3$ suku:

$$
\text{galat} = e^x - \left(1 + x + \frac{x^2}{2}\right) = \frac{x^3}{3!} + \frac{x^4}{4!} + \cdots = \sum_{k=3}^{\infty}\frac{x^k}{k!}
$$

Secara umum, galat pemotongan untuk $n$ suku adalah $\displaystyle\sum_{k=n}^{\infty}\frac{x^k}{k!}$.

**Langkah pengerjaan (hampiran deret dengan galat per suku)**
1. Hitung suku yang akan ditambahkan: $\dfrac{x^{n-1}}{(n-1)!}$ (suku ke-$n$).
2. Jumlahkan → hampiran baru $S_n=\displaystyle\sum_{k=0}^{n-1}\frac{x^k}{k!}$.
3. Hitung $\lvert\varepsilon_a\rvert = \dfrac{x^{n-1}/(n-1)!}{S_n}\times100\%$ (suku yang baru ditambahkan dibagi hampiran baru).
4. Berhenti jika $\lvert\varepsilon_a\rvert<E_s$.

**Contoh pengerjaan 1: galat pembulatan $\tfrac13$**

$$
\frac13 - 0{,}333333 = 0{,}000000333\ldots = 3{,}33\times10^{-7}
$$

Perhatikan jumlah nol: hasilnya $3{,}33\times10^{-7}$ (bukan $10^{-8}$). Bilangan seperti $\pi$ dan $\sqrt2$ juga tidak bisa disimpan secara eksak.

**Contoh pengerjaan 2: rudal Patriot, Dahran 25/02/1991**
Rudal gagal menangkis rudal Scud, **28 tentara tewas**. Jam internal menyimpan waktu dalam kelipatan $\tfrac1{10}$ detik pada register 24 bit. $\tfrac1{10}$ dalam biner tidak berhenti (seperti $\tfrac13$ dalam desimal), jadi terpotong:

$$
\left(0{,}1\right)_{10} = (0{,}00011001100110011001100\ldots)_2
$$

Galat per $0{,}1$ detik $\approx 9{,}537\times10^{-8}$ detik. Dalam 1 detik ada 10 kali "tik", dan 100 jam $=100\times3600$ detik, sehingga:

$$
9{,}537\times10^{-8}\times 10\times 3600\times 100 \approx 0{,}343\ \text{detik}
$$

(Sumber lain menulis $0{,}344$; bedanya hanya pembulatan.) Selisih 0,34 detik membuat target meleset sekitar 137 m dari yang dideteksi.

**Contoh pengerjaan 3: menjumlah $0{,}001$ berulang.** Pada Python, menjumlah `0.001` sebanyak 10 kali menghasilkan `0.010000000000000002`, **bukan** `0.01`. Sebabnya sama: $0{,}001$ tidak punya bentuk biner eksak.

**Contoh pengerjaan 4: $e^{1{,}2}$ dengan deret Maclaurin**

| suku | nilai suku |
|---|---|
| ke-1: $1$ | 1 |
| ke-2: $x$ | 1,2 |
| ke-3: $\frac{x^2}{2!} = \frac{1{,}44}{2}$ | 0,72 |
| ke-4: $\frac{x^3}{3!} = \frac{1{,}728}{6}$ | 0,288 |
| ke-5: $\frac{x^4}{4!} = \frac{2{,}0736}{24}$ | 0,0864 |
| ke-6: $\frac{x^5}{5!} = \frac{2{,}48832}{120}$ | 0,020736 |

Kolom $E_a$ = *suku yang baru ditambahkan*:

| jumlah suku $n$ | hampiran $e^{1{,}2}$ | $E_a$ | $\lvert\varepsilon_a\rvert$ (%) |
|---|---|---|---|
| 1 | 1 | – | – |
| 2 | 2,2 | 1,2 | 54,546 |
| 3 | 2,92 | 0,72 | 24,658 |
| 4 | 3,208 | 0,288 | 8,9776 |
| 5 | 3,2944 | 0,0864 | 2,6226 |
| 6 | 3,3151 | 0,020736 | 0,6255 |

Contoh satu baris ($n=3$): $\dfrac{0{,}72}{2{,}92}\times100\% = 24{,}658\%$.

**Kesimpulan:** setelah 6 suku, galat pemotongan sudah di bawah $1\%$. Nilai sejati $e^{1{,}2}=3{,}320117$, jadi galat sebenarnya $\approx 0{,}005$. Makin banyak suku, makin kecil galat pemotongan, tetapi hitungan makin panjang.

> **Catatan:** jangan menyamakan *truncation* dengan *roundoff*. Pemotongan = prosedur dihentikan; pembulatan = angka disimpan terbatas.

**Implementasi Python**

```python
import math

# (1) Galat pembulatan: 1/3 disimpan terbatas
print(1/3 - 0.333333)                 # sekitar 3.33e-07

# (2) Menjumlah 0.001 sebanyak 10 kali
s = 0.0
for _ in range(10):
    s += 0.001
print(s)                              # 0.010000000000000002, bukan 0.01

# (3) Patriot: galat akumulasi setelah 100 jam
galat_per_tik = 9.537e-8              # detik per 0,1 detik
print(galat_per_tik * 10 * 3600 * 100)   # sekitar 0.343 detik

# (4) Galat pemotongan: e^1.2 dengan deret Maclaurin
x = 1.2
hampiran = 0.0
print("n\thampiran\tEa\t\t|ea| (%)")
for n in range(1, 7):
    suku = x**(n - 1) / math.factorial(n - 1)
    hampiran += suku
    if n == 1:
        print(f"{n}\t{hampiran:.6f}\t-\t\t-")
    else:
        ea = suku / hampiran * 100
        print(f"{n}\t{hampiran:.6f}\t{suku:.6f}\t{ea:.4f}")
# Bentuk sigma: S_n = sum_{k=0}^{n-1} x^k / k!
print(sum(x**k / math.factorial(k) for k in range(6)))   # S_6 = 3.315136
print("nilai sejati:", math.exp(x))   # 3.320117...
```

---

## 1.5 Mantisa Terbatas

**Penjelasan singkat**
Komputer "menormalkan" bilangan desimal ke bentuk

$$
0{,}d_1 d_2 d_3 \ldots d_k \times 10^{n}\qquad\text{dengan } d_1 \neq 0
$$

- $0{,}d_1d_2\ldots d_k$ disebut **mantisa** (deretan digit penting).
- $n$ disebut **eksponen** (pangkat 10).
- Jika komputer hanya muat $k$ digit mantisa, digit sisanya dibuang. "Dipotong" (*chopping*) membuang tanpa menaikkan; "dibulatkan" (*rounding*) menaikkan digit terakhir jika digit berikutnya $\ge5$.

**Kegunaan**
Meniru bagaimana galat pembulatan muncul pada penjumlahan di komputer berpresisi terbatas.

**Rumus**
Mantisa dalam bentuk sigma (tiap $d_i$ adalah satu digit $0$ sampai $9$ pada posisi ke-$i$ di belakang koma):

$$
0{,}d_1 d_2 \ldots d_k = \sum_{i=1}^{k} d_i\,10^{-i}
\qquad\Longrightarrow\qquad
x = \pm\left(\sum_{i=1}^{k} d_i\,10^{-i}\right)\times10^{n},\quad d_1\neq0
$$

Contoh: $0{,}1557 = 1\cdot10^{-1}+5\cdot10^{-2}+5\cdot10^{-3}+7\cdot10^{-4}$. Galat $=$ hasil eksak $-$ hasil terpotong.

**Langkah pengerjaan (penjumlahan dengan mantisa terbatas)**
1. Ubah kedua bilangan ke bentuk $0{,}d_1d_2\ldots\times10^n$.
2. **Samakan eksponen** dengan menggeser koma pada bilangan yang eksponennya lebih kecil.
3. Jumlahkan mantisanya.
4. **Potong** (atau bulatkan, sesuai soal) hasil menjadi $k$ digit mantisa.
5. Galat = hasil eksak − hasil terpotong.

**Contoh pengerjaan:** $a = 1{,}557$ dan $b = 0{,}04341$, mantisa 4 digit.

*Langkah 1 dan 2: normalkan dan samakan eksponen*

$$
\begin{aligned}
a &= 1{,}557 = 0{,}1557\times10^{1}\\
b &= 0{,}04341 = 0{,}004341\times10^{1}
\end{aligned}
$$

($b$ aslinya $0{,}4341\times10^{-1}$. Eksponennya dinaikkan ke $1$ sehingga koma bergeser 2 tempat ke kiri.)

*Langkah 3: jumlahkan mantisa*

$$
a + b = (0{,}1557 + 0{,}004341)\times10^1 = 0{,}160041\times10^{1}
$$

*Langkah 4: potong menjadi 4 digit mantisa*

$$
a + b = 0{,}1600\times10^{1} = 1{,}600
$$

*Langkah 5: galat*

$$
1{,}60041 - 1{,}600 = 0{,}00041
$$

Digit "41" di belakang hilang karena tidak muat di 4 digit mantisa; itulah galat pembulatan. Pada contoh ini dipotong dan dibulatkan menghasilkan angka sama ($0{,}160041\to0{,}1600$), tetapi pada soal lain bisa berbeda. Ikuti kata di soal.

**Implementasi Python**

```python
from decimal import Decimal, ROUND_DOWN, ROUND_HALF_UP

def mantisa(x, k, mode="potong"):
    """Ubah x ke bentuk 0.d1d2...dk x 10^n. Mengembalikan (mantisa, n)."""
    x = Decimal(str(x))
    n = x.adjusted() + 1                    # eksponen agar 0.1 <= |mantisa| < 1
    m = x.scaleb(-n)
    q = Decimal(1).scaleb(-k)               # k digit di belakang koma
    aturan = ROUND_DOWN if mode == "potong" else ROUND_HALF_UP
    return m.quantize(q, rounding=aturan), n

def jumlah_mantisa(a, b, k, mode="potong"):
    eksak = Decimal(str(a)) + Decimal(str(b))
    m, n = mantisa(eksak, k, mode)
    hasil = m.scaleb(n)
    return eksak, hasil, eksak - hasil      # eksak, terpotong, galat

eksak, hasil, galat = jumlah_mantisa(1.557, 0.04341, k=4)
print(eksak, hasil, galat)                  # 1.60041  1.6000  0.00041
```

---

## 1.6 Bilangan Biner dan Floating Point

**Penjelasan singkat**
Komputer hanya mengenal $0$ dan $1$ (basis 2). Bilangan real disimpan dalam bentuk *floating point* dengan jumlah bit terbatas, sehingga banyak bilangan (misal $0{,}1$) tidak tersimpan eksak.

**Kegunaan**
Menjelaskan *mengapa* galat pembulatan ada, dan memberi batas atasnya (machine epsilon).

**Rumus**

*Biner.* Nilai tiap posisi adalah pangkat $2$:

| posisi | $2^3$ | $2^2$ | $2^1$ | $2^0$ | , | $2^{-1}$ | $2^{-2}$ | $2^{-3}$ | $2^{-4}$ |
|---|---|---|---|---|---|---|---|---|---|
| nilai | 8 | 4 | 2 | 1 | , | 0,5 | 0,25 | 0,125 | 0,0625 |

Bentuk umum biner → desimal dengan sigma, untuk $(b_m\ldots b_1b_0\,,\,f_1f_2\ldots f_t)_2$:

$$
\sum_{i=0}^{m} b_i\,2^{\,i} \;+\; \sum_{j=1}^{t} f_j\,2^{-j}
$$

(suku pertama untuk bagian bulat, suku kedua untuk bagian pecahan).

*Floating point.*

$$
x = \pm\,(1.d_1 d_2 d_3\ldots)_2 \times 2^{e}
\qquad\text{dengan}\qquad
1.d_1d_2d_3\ldots = 1 + \frac{d_1}{2^1} + \frac{d_2}{2^2} + \frac{d_3}{2^3} + \cdots
$$

Dalam bentuk sigma:

$$
1.d_1d_2d_3\ldots = 1 + \sum_{i=1}^{\infty} d_i\,2^{-i}
$$

Komputer hanya menyimpan $t$ digit pecahan:

$$
fl(x) = \pm\,(1.d_1 d_2 \ldots d_t)\times2^{e} = \pm\left(1 + \sum_{i=1}^{t} d_i\,2^{-i}\right)\times2^{e}
$$

$$
\text{galat relatif} = \frac{\lvert fl(x) - x\rvert}{\lvert x\rvert}
$$

*Machine epsilon* (batas galat relatif akibat pembulatan menurut standar IEEE), disebut juga *rounding unit* atau *machine precision*:

$$
\eta = \frac12\times2^{-t}
$$

| Jenis | Total bit | Bit pecahan ($t$) | $\eta$ |
|---|---|---|---|
| Double precision (contoh pada MATLAB) | 64 (52 pecahan + 12 untuk tanda & eksponen) | 52 | $2^{-53}\approx1{,}1\times10^{-16}$ |
| Single precision | 32 | 23 | $2^{-24}\approx5{,}96\times10^{-8}$ |

Contoh percobaan: fungsi $g(t)=e^{-t}(\sin(2\pi t)+2)$ yang dihitung dalam *single* lalu dibandingkan dengan *double* selalu punya galat relatif di bawah $\approx6\times10^{-8}$, sesuai $\eta$ single.

*Rentang bilangan:*

| | rentang |
|---|---|
| Integer 16 bit | $-32.768$ sampai $32.767$ |
| Integer 32 bit | $-2.147.483.648$ sampai $2.147.483.647$ |
| Floating point single (32 bit) | sekitar 7 digit pecahan, rentang sekitar $10^{-38}$ sampai $10^{39}$ |
| Floating point double (64 bit) | sekitar 15 digit, rentang sekitar $10^{-308}$ sampai $10^{308}$ |

Sebagian materi kuliah menamai single dan double sebagai "16 bit" dan "32 bit". Jika soal memakai penamaan itu, ikuti angka soal.

**Langkah pengerjaan**

*Biner → desimal:* kalikan tiap digit dengan nilai posisinya, lalu jumlahkan.

*Desimal → biner:*
- *Bagian bulat:* bagi $2$ terus-menerus, catat sisa, baca sisa **dari bawah ke atas**.
- *Bagian pecahan:* kalikan $2$ terus-menerus, catat **bagian bulatnya** (0 atau 1), baca **dari atas ke bawah**.

**Contoh pengerjaan 1: biner → desimal, $(1011{,}0011)_2$**

$$
\begin{aligned}
&\text{bagian bulat: } 1\times8 + 0\times4 + 1\times2 + 1\times1 = 11\\
&\text{bagian pecahan: } 0\times0{,}5 + 0\times0{,}25 + 1\times0{,}125 + 1\times0{,}0625 = 0{,}1875\\
&\Rightarrow (1011{,}0011)_2 = 11{,}1875
\end{aligned}
$$

**Contoh pengerjaan 2: desimal → biner, $11{,}875$**

$$
11 \div 2 = 5\ \text{sisa } 1,\quad 5\div2 = 2\ \text{sisa }1,\quad 2\div2=1\ \text{sisa }0,\quad 1\div2=0\ \text{sisa }1
\;\Rightarrow\; 1011
$$

$$
0{,}875\times2 = 1{,}75\to1,\quad 0{,}75\times2=1{,}5\to1,\quad 0{,}5\times2=1{,}0\to1
\;\Rightarrow\; 0{,}111
$$

Jadi $11{,}875 = (1011{,}111)_2$.

**Contoh pengerjaan 3: mengapa $0{,}1$ tidak eksak**

$$
0{,}1\times2=0{,}2\to0,\ \ 0{,}4\to0,\ \ 0{,}8\to0,\ \ 1{,}6\to1,\ \ 1{,}2\to1,\ \ 0{,}4\to0,\ \ 0{,}8\to0,\ \ 1{,}6\to1\ \ldots
$$

Pola $0011$ berulang tanpa henti: $(0{,}1)_{10} = (0{,}0001100110011\ldots)_2$. Tak pernah berakhir, jadi harus dipotong, dan timbul galat pembulatan (sama seperti $\tfrac13=0{,}333\ldots$ pada desimal).

**Contoh pengerjaan 4: nilai floating point**

$$
x = -\left(1 + \tfrac12 + \tfrac18\right)\times2 = -(1 + 0{,}5 + 0{,}125)\times 2 = -1{,}625\times2 = -3{,}25
$$

**Implementasi Python**

```python
import numpy as np

def desimal_ke_biner(x, maks_pecahan=12):
    bulat, pecahan = int(x), x - int(x)
    b = ""
    while bulat > 0:                          # bagi 2, baca sisa dari bawah
        b = str(bulat % 2) + b
        bulat //= 2
    b = b or "0"
    p = ""
    while pecahan > 0 and len(p) < maks_pecahan:   # kali 2, baca dari atas
        pecahan *= 2
        bit = int(pecahan)
        p += str(bit)
        pecahan -= bit
    return b + ("." + p if p else "")

def biner_ke_desimal(s):
    bulat, _, pecahan = s.partition(".")
    nilai = int(bulat, 2) if bulat else 0
    for i, bit in enumerate(pecahan, start=1):
        nilai += int(bit) * 2 ** (-i)
    return nilai

print(biner_ke_desimal("1011.0011"))   # 11.1875
print(desimal_ke_biner(11.875))        # 1011.111
print(desimal_ke_biner(0.1))           # 0.000110011001  (terpotong, tidak eksak)

# Nilai floating point pada contoh 4
print(-(1 + 1/2 + 1/8) * 2)            # -3.25

# Machine epsilon: numpy memberi eps (jarak 1 ke bilangan berikutnya) = 2*eta
for tipe in (np.float64, np.float32):
    eta = np.finfo(tipe).eps / 2
    print(tipe.__name__, eta)          # float64 1.11e-16 ; float32 5.96e-08
```

---

## 1.7 Deret Taylor

**Penjelasan singkat**
Fungsi rumit (misal $\sin x$) bisa dihampiri dengan penjumlahan suku-suku sederhana (pangkat $x$) jika kita tahu nilai fungsi dan turunan-turunannya di satu titik. Maclaurin adalah Taylor dengan $a=0$.

**Kegunaan**
Dasar banyak metode numerik (termasuk Newton di [2.5](#25-metode-newton-raphson)). Karena deretnya tak hingga lalu dipotong, selalu ada galat pemotongan; karena dihitung di komputer, ada pula galat pembulatan.

**Rumus**

Bentuk dengan jarak $h$ (titik awal $x$, titik baru $x+h$):

$$
f(x+h) = f(x) + \frac{h}{1!}f'(x) + \frac{h^2}{2!}f''(x) + \cdots + \frac{h^n}{n!}f^{(n)}(x) + R_n(x)
$$

Dalam bentuk sigma (dengan $f^{(0)}=f$ dan $0!=1$):

$$
f(x+h) = \sum_{k=0}^{n}\frac{h^k}{k!}\,f^{(k)}(x) + R_n(x)
$$

Suku sisa (galat pemotongan):

$$
R_n(x) = \frac{h^{\,n+1}}{(n+1)!}\,f^{(n+1)}(c)\qquad\text{untuk suatu } c \text{ dengan } x<c<x+h
$$

Bentuk umum (di sekitar titik $a$):

$$
f(x) = f(a) + \frac{(x-a)}{1!}f'(a) + \frac{(x-a)^2}{2!}f''(a) + \cdots + R_n(x) = T_n(x) + R_n(x)
$$

Dalam bentuk sigma:

$$
T_n(x) = \sum_{k=0}^{n}\frac{(x-a)^k}{k!}\,f^{(k)}(a)
\qquad\Longrightarrow\qquad
f(x) = \sum_{k=0}^{n}\frac{(x-a)^k}{k!}\,f^{(k)}(a) + R_n(x)
$$

Untuk Maclaurin ($a=0$): $T_n(x)=\displaystyle\sum_{k=0}^{n}\frac{x^k}{k!}\,f^{(k)}(0)$.

$T_n(x)$ adalah bagian yang dihitung; $R_n(x)$ adalah bagian yang dibuang (galat). Perhatikan pangkatnya $(x-a)^{n+1}$ dan semua turunan dihitung **di titik $a$**.

**Langkah pengerjaan**
1. Tentukan titik awal $x$ dan jarak $h$ (jadi titik yang dicari $=x+h$).
2. Turunkan $f$ berkali-kali, hitung semua turunan **di titik $x$**.
3. Masukkan ke rumus, hitung suku demi suku.
4. Jumlahkan. Makin banyak suku, makin akurat.

**Contoh pengerjaan:** hitung $\sin(2)$ dari titik $x=\dfrac\pi2$.

*Langkah 1: tentukan $h$*

$$
x + h = 2 \Rightarrow h = 2 - \frac\pi2 = 2 - 1{,}570796 = 0{,}42920
$$

*Langkah 2: turunan di $x=\frac\pi2$* (turunan $\sin$ berputar: $\sin\to\cos\to-\sin\to-\cos\to\sin$)

| turunan | rumus | nilai di $\frac\pi2$ |
|---|---|---|
| $f$ | $\sin x$ | $1$ |
| $f'$ | $\cos x$ | $0$ |
| $f''$ | $-\sin x$ | $-1$ |
| $f'''$ | $-\cos x$ | $0$ |
| $f^{(4)}$ | $\sin x$ | $1$ |

*Langkah 3 dan 4: masukkan ke rumus*

$$
\begin{aligned}
f\!\left(\tfrac\pi2 + h\right) &\approx 1 + 0\cdot h - 1\cdot\frac{h^2}{2!} + 0\cdot\frac{h^3}{3!} + 1\cdot\frac{h^4}{4!}\\[4pt]
&= 1 + 0 - \frac{(0{,}42920)^2}{2} + 0 + \frac{(0{,}42920)^4}{24}\\[4pt]
&= 1 - 0{,}092108 + 0{,}001414 \approx 0{,}90931
\end{aligned}
$$

Nilai sebenarnya $\sin(2)=0{,}909297$. Galatnya sekitar $8{,}7\times10^{-6}$, kira-kira sebesar suku berikutnya, $-\dfrac{h^6}{6!}$.

**Agar mendekati nilai eksak:** **tambah jumlah suku** (naikkan $n$) atau **perkecil $h$** (pilih titik awal lebih dekat ke titik yang dicari). Contoh: menambah suku $-\frac{h^6}{720}$ memberi $0{,}909297$, hampir persis. (Karena suku-suku berpangkat ganjil bernilai nol di titik $\frac\pi2$, suku ini baru muncul pada hitungan ke-7 jika suku nol ikut dihitung.)

> **Catatan:** bandingkan dengan nilai sebenarnya memakai $h$ yang sama ($0{,}42920$), yaitu $\sin(2)=0{,}909297$.

**Implementasi Python**

```python
import math

def taylor_sin(x, h, n_suku):
    """Hampiran sin(x+h) dengan n_suku suku deret Taylor di titik x."""
    turunan = [math.sin(x), math.cos(x), -math.sin(x), -math.cos(x)]  # berputar tiap 4
    hasil = 0.0
    for k in range(n_suku):
        hasil += turunan[k % 4] * h**k / math.factorial(k)
    return hasil

x0 = math.pi / 2
h = 2 - x0                                   # 0.42920...
sejati = math.sin(2)
for n in (3, 5, 7):
    p = taylor_sin(x0, h, n)
    print(f"{n} suku: {p:.6f}   galat = {abs(sejati - p):.2e}")
# 3 suku -> 0.907892 ; 5 suku -> 0.909306 (galat 8.7e-06) ; 7 suku -> 0.909297 (hampir persis)
```

---

## 1.8 Cara Mengurangi Galat

**Penjelasan singkat**
Cara terbaik mengurangi galat bergantung pada sumbernya.

**Kegunaan**
Panduan memilih tindakan perbaikan.

| Sumber galat | Cara mengurangi |
|---|---|
| Pemotongan (*truncation*) | tambah jumlah suku deret; perkecil $h$; teruskan iterasi lebih lama (batas $E_s$ lebih kecil) |
| Pembulatan (*roundoff*) | pakai presisi lebih tinggi (double, bukan single); jangan membulatkan di tengah hitungan |
| Pilihan metode / tebakan awal | tebakan awal dekat akar; metode yang lebih cepat konvergen (Newton dibanding Bisection) |

Metode langsung di Bab 3 (Gauss, LU, Cholesky) tidak punya galat iterasi; galat yang tersisa hanya galat pembulatan dari komputer.

[Kembali ke daftar isi](#daftar-isi-dan-peta-materi)

---

# Bab 2. Akar Persamaan Non-Linear

> **Akar** dari fungsi $f(x)$ adalah nilai $x$ yang membuat $f(x)=0$. Pada grafik, itu titik tempat kurva **memotong sumbu-$x$**.

---

## 2.0 Konsep Dasar

**Penjelasan singkat**

*Linear vs non-linear.* Persamaan linear pangkat variabelnya paling tinggi 1 (misal $y=2x+1$). Non-linear pangkatnya bisa lebih tinggi atau memuat fungsi lain (misal $y=2x^2+1$, $y=2x+\sin^2x$).

*Kenapa perlu metode numerik?* Persamaan kuadrat $ax^2+bx+c=0$ punya rumus

$$
x_{1,2} = \frac{-b\pm\sqrt{b^2-4ac}}{2a}\quad(a\neq0)
$$

tetapi untuk $x^3$, $\sin x$, $x^7-1000$ dan sebagainya **tidak ada rumus sederhana**, jadi akar dicari dengan *menebak lalu memperbaiki tebakan berulang-ulang*.

*Dua keluarga metode:*

| | **Bracketing** (mengurung) | **Terbuka** |
|---|---|---|
| Cara | tebak **dua** titik $a$ dan $b$ yang mengurung akar | pakai **satu** (atau dua) titik tebakan, lalu perbarui |
| Metode | [Grafis](#21-metode-grafis), [Bisection](#22-metode-bisection), [Regula Falsi](#23-metode-regula-falsi) | [Titik Tetap](#24-iterasi-titik-tetap), [Newton](#25-metode-newton-raphson), [Secant](#26-metode-secant) |
| Syarat awal | $f(a)\cdot f(b)<0$ | tebakan awal $x_0$ |

**Kegunaan**
Bagian ini menjelaskan hal yang dipakai semua metode di Bab 2: membaca $f(a)$, $f(b)$, menentukan $a$ dan $b$, dan menentukan kapan iterasi berhenti.

**Rumus**

*Syarat pengurungan akar:* $f(a)\cdot f(b)<0$, yaitu **tanda $f(a)$ dan $f(b)$ berlawanan**. Karena grafik kontinu, di antara keduanya pasti ada titik yang memotong sumbu-$x$.

*Galat relatif antar-iterasi:*

$$
\lvert E_r\rvert = \left\lvert\frac{c_{\text{sekarang}} - c_{\text{sebelumnya}}}{c_{\text{sekarang}}}\right\rvert\times100\% < E_s
$$

*Tiga cara menghentikan iterasi:*

| Kriteria | Yang dicek | Contoh |
|---|---|---|
| Galat relatif (cara utama di kuliah) | $\lvert E_r\rvert<E_s$ (dalam %) | $E_r<0{,}5\%$ |
| Sisi $x$ ("delta") | $\lvert x_{k+1}-x_k\rvert<\varepsilon$ | selisih $x$ kurang dari $0{,}001$ |
| Sisi $y$ ("epsilon") | $\lvert f(x_{k+1})\rvert<\varepsilon$ | nilai fungsi kurang dari $0{,}01$ |

**Langkah pengerjaan**

*Apa itu $f(a)$ dan $f(b)$?* Hasil memasukkan angka $a$ dan $b$ ke dalam fungsi $f(x)$ dari soal. Yang diperhatikan adalah **tanda** hasilnya.

*Dari mana nilai $a$ dan $b$?*
1. **Diberikan soal**, misal "selang $[0,1]$" → $a=0$, $b=1$.
2. **Dari grafik:** ambil satu titik di kiri dan satu di kanan titik potong kurva dengan sumbu-$x$ (lihat [2.1](#21-metode-grafis)).
3. **Coba-coba:** hitung $f$ untuk angka sederhana ($0,1,2,3,\ldots$) sampai ketemu dua angka dengan hasil **berbeda tanda**.

*Kapan berhenti?* Baca soal untuk melihat apa yang dibandingkan dengan batas: persen atau "galat relatif" → pakai $E_r$; "$\lvert f(x)\rvert<\varepsilon$" → cek nilai fungsi; "selisih $x$" → cek $\lvert x_{k+1}-x_k\rvert$. Jika soal menyebut toleransi $\varepsilon$ atau $E_s$ tertentu, angka itu sudah diketahui dan hanya berfungsi sebagai batas berhenti. Jika soal tidak menyebutnya, pakai $0{,}5\%$ (nilai yang dipakai di kuliah, bukan aturan baku) dan tulis asumsimu.

**Contoh pengerjaan:** $f(x)=x^3-2x^2+6x-4$.

- $a=0 \Rightarrow f(0)=0-0+0-4=-4$
- $b=1 \Rightarrow f(1)=1-2+6-4=1$

Tandanya berlawanan ($-4$ dan $1$), sehingga $f(a)\cdot f(b)=-4<0$ dan ada akar di $[0,1]$. Sebaliknya, $a=2$ dan $b=3$: $f(2)=8$ dan $f(3)=23$, keduanya positif, jadi pasangan itu **tidak valid** dan harus dipilih ulang.

*Contoh kriteria sisi $y$:* Bisection pada fungsi dan selang di atas dengan $\varepsilon=0{,}01$ pada nilai fungsi. Dari tabel di [2.2](#22-metode-bisection), $\lvert f(c)\rvert$ baru di bawah $0{,}01$ pada baris $n=7$ ($\lvert-0{,}001168\rvert<0{,}01$). Baris $n=5$ dan $n=6$ belum cukup ($0{,}0173$ dan $0{,}0196$). Jadi berhenti di baris $n=7$ (8 baris hitungan), akar $\approx0{,}792969$.

*Efek mengubah $E_s$* (Bisection, fungsi dan selang yang sama; akar sebenarnya $\approx0{,}79322$):

| $E_s$ | baris terakhir $n$ (mulai dari 0) | jumlah iterasi | hasil $c$ | selisih dengan akar sebenarnya |
|---|---|---|---|---|
| 5% | 4 | 5 | 0,781250 | 0,012 |
| 1% | 6 | 7 | 0,789062 | 0,0042 |
| **0,5%** | **7** | **8** | **0,792969** | **0,00025** |
| 0,1% | 10 | 11 | 0,793457 | 0,00024 |
| 0,01% | 13 | 14 | 0,793274 | 0,000057 |

- $E_s$ lebih besar: berhenti lebih cepat, hasil kurang akurat. $E_s$ lebih kecil: iterasi lebih banyak, hasil lebih akurat.
- Perbaikannya tidak selalu mulus (selisih untuk 0,5% dan 0,1% hampir sama), karena $E_r$ hanya mengukur *seberapa besar nilai berubah*, bukan galat sebenarnya terhadap akar.
- $E_s$ tidak bisa dikecilkan tanpa batas; di bawah ketelitian komputer, galat pembulatan yang menentukan.

> **Catatan:**
> - $E_s$ dan $\varepsilon$ adalah batas yang *kita pilih* untuk berhenti. Machine epsilon ($2^{-53}\approx1{,}1\times10^{-16}$ untuk double, lihat [1.6](#16-bilangan-biner-dan-floating-point)) adalah batas ketelitian komputer yang nilainya tetap. Keduanya jangan tertukar.
> - $E_r$ dalam persen dibandingkan dengan $E_s$ dalam persen. Jika toleransi berupa angka biasa (misal $0{,}001$), jangan dibandingkan dengan $E_r$ yang sudah dikali 100.
> - $E_r$ pertama belum bisa dihitung karena belum ada nilai sebelumnya.
> - **Jumlah iterasi** = jumlah baris hitungan di tabel. Jika tabel mulai dari $n=0$ dan baris terakhir $n=7$, itu 8 iterasi. Tabel boleh mulai dari $n=0$ atau $n=1$ asal konsisten.
> - Pada soal akar persamaan, $\sin$ dan $\cos$ dihitung dalam **radian**.

**Implementasi Python**

```python
f = lambda x: x**3 - 2*x**2 + 6*x - 4

def selang_valid(f, a, b):
    """True jika f(a)*f(b) < 0, yaitu akar terkurung di [a, b]."""
    return f(a) * f(b) < 0

print(f(0), f(1), selang_valid(f, 0, 1))   # -4 1 True
print(f(2), f(3), selang_valid(f, 2, 3))   # 8 23 False
```

---

## 2.1 Metode Grafis

**Penjelasan singkat**
Gambar grafik $f(x)$, lihat dari gambar di sekitar mana kurva memotong sumbu-$x$, lalu tebak $a$ dan $b$ di kiri-kanan titik potong itu.

**Kegunaan**
Memahami perilaku fungsi dan **menentukan $a$ dan $b$** untuk metode lain. Kelemahannya: hasil tidak akurat (hanya dari pengamatan) dan menggambar susah tanpa software.

**Rumus**
Tidak ada rumus; syarat yang dicek tetap $f(a)\cdot f(b)<0$.

**Langkah pengerjaan**
1. Gambar kurva $y=f(x)$ dan garis sumbu-$x$.
2. Cari tempat kurva memotong sumbu-$x$.
3. Ambil $a$ di kiri dan $b$ di kanan titik potong; makin sempit selangnya, makin baik.
4. Cek tanda: $f(a)\cdot f(b)<0$.

**Contoh pengerjaan 1:** $y=x^3-2x^2+6x-4$. Dari gambar, akar ada sekitar antara $0$ dan $1$. Cek tanda: $f(0)=-4$ (negatif), $f(1)=1-2+6-4=1$ (positif), jadi ada akar di $[0,1]$. Selang yang lebih sempit juga bisa, misal $a=0{,}7$ dan $b=0{,}8$ ($f(0{,}7)=-0{,}437$ dan $f(0{,}8)=0{,}032$, tanda berlawanan).

**Contoh pengerjaan 2:** $f(x)=\sin(10x)+\cos(3x)$ pada $[3,6]$, increment $0{,}01$. Dengan memindai tanda $f$ tiap $0{,}01$, terdeteksi **9 akar** (9 kali perubahan tanda). Pasangan $a,b$ yang bisa dipakai dan akarnya:

| $a$ | $b$ | akar $\approx$ |
|---|---|---|
| 3,26 | 3,27 | 3,26242 |
| 3,36 | 3,37 | 3,36599 |
| 3,74 | 3,75 | 3,74575 |
| 4,22 | 4,23 | 4,22907 |
| 4,26 | 4,27 | 4,26359 |
| 4,71 | 4,72 | 4,71239 |
| 5,16 | 5,17 | 5,16119 |
| 5,19 | 5,20 | 5,19571 |
| 5,67 | 5,68 | 5,67903 |

Soal ini sengaja punya banyak akar, jadi pilihan $a,b$ menentukan akar mana yang ketemu. Jika diminta satu akar, pilih satu pasangan lalu jalankan [Bisection](#22-metode-bisection).

**Implementasi Python**

```python
import numpy as np
import matplotlib.pyplot as plt

# (1) Menggambar kurva
x = np.arange(3, 6, 0.01)              # titik x dari 3 sampai 6, loncat 0.01
y = np.sin(10*x) + np.cos(3*x)         # f(x)
plt.plot(x, y, 'b-')                   # kurva biru
plt.plot(x, np.zeros_like(x), 'r-')    # garis sumbu-x (merah); zeros_like menyesuaikan panjang
plt.show()

# (2) Memindai perubahan tanda untuk mencari pasangan (a, b)
def cari_selang(f, awal, akhir, langkah=0.01):
    xs = np.arange(awal, akhir, langkah)
    ys = f(xs)
    return [(xs[i], xs[i + 1]) for i in range(len(xs) - 1) if ys[i] * ys[i + 1] < 0]

f = lambda x: np.sin(10*x) + np.cos(3*x)
pasangan = cari_selang(f, 3, 6)
print(len(pasangan), "akar terdeteksi")          # 9
for a, b in pasangan:
    print(f"a = {a:.2f}, b = {b:.2f}")
```

---

## 2.2 Metode Bisection

**Penjelasan singkat**
Akar terkurung di $[a,b]$. Belah dua selangnya di titik tengah $c$, lihat akarnya ada di separuh kiri atau kanan, buang separuh yang tidak ada akarnya, ulangi.

**Kegunaan**
Metode paling aman: **pasti konvergen** selama $f(a)f(b)<0$, karena selang menyusut separuh tiap iterasi. Kekurangannya lambat (8 iterasi untuk 3 angka desimal pada contoh di bawah).

**Rumus**

$$
c = \frac{a+b}{2}
$$

Aturan penggeseran:

| hasil | artinya | tindakan |
|---|---|---|
| $f(a)\cdot f(c)<0$ | akar ada di $[a,c]$ | $b\leftarrow c$ |
| $f(a)\cdot f(c)>0$ | akar ada di $[c,b]$ | $a\leftarrow c$ |
| $f(a)\cdot f(c)=0$ | $c$ adalah akar | selesai |

**Langkah pengerjaan**
1. Pilih $a,b$. **Cek** $f(a)\cdot f(b)<0$. Jika tidak, pilih ulang.
2. Hitung $c=\dfrac{a+b}{2}$ dan $f(c)$.
3. Bandingkan tanda: $f(a)\cdot f(c)<0\Rightarrow b=c$; $>0\Rightarrow a=c$ (pakai $a$ yang **belum diubah**).
4. Hitung $E_r=\left\lvert\dfrac{c_{\text{baru}}-c_{\text{lama}}}{c_{\text{baru}}}\right\rvert\times100\%$ (mulai iterasi ke-2).
5. Jika $E_r<E_s$ (mis. $0{,}5\%$), berhenti; akar $\approx c$. Jika belum, kembali ke langkah 2.

**Contoh pengerjaan:** $f(x)=x^3-2x^2+6x-4$, selang $[0,1]$, berhenti saat $E_r<0{,}5\%$.

*Cek selang:*

$$
f(0)=-4,\quad f(1)=1\ \Rightarrow\ f(0)\cdot f(1) = -4 < 0\ \checkmark
$$

*Iterasi 0:* $c=\dfrac{0+1}{2}=0{,}5$.

$$
f(0{,}5) = 0{,}125 - 0{,}5 + 3 - 4 = -1{,}375
$$

$f(a)\cdot f(c) = (-4)(-1{,}375) = 5{,}5 > 0$, akar di kanan, jadi $a\leftarrow0{,}5$. ($E_r$ belum bisa dihitung.)

*Iterasi 1:* $c=\dfrac{0{,}5+1}{2}=0{,}75$, $f(0{,}75)=-0{,}203125$.
$f(a)\cdot f(c)=(-1{,}375)(-0{,}203125)>0$, jadi $a\leftarrow0{,}75$.

$$
E_r=\left\lvert\frac{0{,}75-0{,}5}{0{,}75}\right\rvert\times100\% = 33{,}33\%
$$

*Iterasi 2:* $c=\dfrac{0{,}75+1}{2}=0{,}875$, $f(0{,}875)=0{,}388672$.
$f(a)\cdot f(c)=(-0{,}203125)(0{,}388672)<0$, akar di kiri, jadi $b\leftarrow0{,}875$.

$$
E_r=\left\lvert\frac{0{,}875-0{,}75}{0{,}875}\right\rvert\times100\% = 14{,}29\%
$$

*Tabel lengkap:*

| $n$ | $a$ | $b$ | $c$ | $f(c)$ | $E_r$ (%) |
|---|---|---|---|---|---|
| 0 | 0,000000 | 1,000000 | 0,500000 | −1,375000 | – |
| 1 | 0,500000 | 1,000000 | 0,750000 | −0,203125 | 33,3333 |
| 2 | 0,750000 | 1,000000 | 0,875000 | 0,388672 | 14,2857 |
| 3 | 0,750000 | 0,875000 | 0,812500 | 0,091064 | 7,6923 |
| 4 | 0,750000 | 0,812500 | 0,781250 | −0,056366 | 4,0000 |
| 5 | 0,781250 | 0,812500 | 0,796875 | 0,017254 | 1,9608 |
| 6 | 0,781250 | 0,796875 | 0,789062 | −0,019578 | 0,9901 |
| 7 | 0,789062 | 0,796875 | 0,792969 | −0,001168 | **0,4926** ← berhenti |

**Jawaban:** akar $\approx 0{,}792969$ (akar sebenarnya $0{,}793217$). Cek: $f(0{,}792969)=-0{,}001168\approx0$.

> **Catatan:** jawaban akhir adalah nilai $c$ pada **baris terakhir**. Jika $f(a)\cdot f(b)>0$, pasangan tidak valid; pilih ulang, jangan dipaksa lanjut. Ada versi program yang berhenti dengan syarat `abs(f(a)*f(b)) > 0.000001` (memeriksa perkalian $f$ di kedua ujung, bukan galat relatif); kalau soal meminta $E_r<0{,}5\%$, pakai versi di bawah.

**Implementasi Python**

```python
def bisection(f, a, b, es=0.5, cetak=True):
    """Mengembalikan (akar, jumlah_iterasi). es dalam persen."""
    if f(a) * f(b) >= 0:
        raise ValueError("Penentuan a dan b salah: f(a)*f(b) harus < 0")
    cp = None                          # nilai tengah sebelumnya
    n = 0
    if cetak:
        print("n\ta\t\tb\t\tc\t\tf(c)\t\tEr(%)")
    while True:
        c = (a + b) / 2
        er = None if cp is None else abs((c - cp) / c) * 100
        if cetak:
            print(f"{n}\t{a:.6f}\t{b:.6f}\t{c:.6f}\t{f(c):.6f}\t"
                  f"{'-' if er is None else format(er, '.4f')}")
        if f(c) == 0:                  # tepat di akar
            return c, n + 1
        if er is not None and er < es:
            return c, n + 1
        if f(a) * f(c) < 0:
            b = c
        else:
            a = c
        cp = c
        n += 1

f = lambda x: x**3 - 2*x**2 + 6*x - 4
akar, iterasi = bisection(f, 0, 1)
print(akar, iterasi)                   # 0.79296875  8

# Efek mengubah Es (lihat tabel di 2.0)
for es in (5, 1, 0.5, 0.1, 0.01):
    akar, iterasi = bisection(f, 0, 1, es=es, cetak=False)
    print(f"Es = {es}%  ->  {iterasi} iterasi, c = {akar:.6f}")
```

---

## 2.3 Metode Regula Falsi

**Penjelasan singkat**
Sama seperti Bisection (mengurung akar di $[a,b]$), tetapi titik $c$ **bukan tengah-tengah**. Tarik garis lurus dari titik $(a,f(a))$ ke $(b,f(b))$. Titik tempat garis itu memotong sumbu-$x$ dijadikan $c$. Disebut juga *false position* atau **interpolasi linear**.

**Kegunaan**
Umumnya lebih cepat daripada Bisection karena $c$ dihitung dari bentuk kurva, dan akar tetap terjaga di dalam selang. Bisa lambat jika fungsi sangat melengkung.

**Rumus**

$$
c = b - \frac{f(b)\,(a-b)}{f(a)-f(b)}
$$

Aturan penggeseran **sama persis** dengan Bisection (bandingkan tanda $f(a)\cdot f(c)$).

**Langkah pengerjaan:** sama seperti Bisection, hanya langkah 2 diganti rumus $c$ di atas.

**Contoh pengerjaan:** $f(x)=x^3-2x^2+6x-4$, $[a,b]=[0,1]$, $E_s=0{,}5\%$.

*Iterasi 0:* $f(a)=-4$, $f(b)=1$.

$$
c = 1 - \frac{1\cdot(0-1)}{-4-1} = 1 - \frac{-1}{-5} = 1 - 0{,}2 = 0{,}8
$$

$f(0{,}8)=0{,}032>0$; $f(a)\cdot f(c)=(-4)(0{,}032)<0$, jadi $b\leftarrow0{,}8$.

*Iterasi 1:* $a=0$, $b=0{,}8$, $f(b)=0{,}032$.

$$
c = 0{,}8 - \frac{0{,}032\,(0-0{,}8)}{-4-0{,}032} = 0{,}8 - \frac{-0{,}0256}{-4{,}032} = 0{,}8 - 0{,}006349 = 0{,}793651
$$

$$
E_r = \left\lvert\frac{0{,}793651-0{,}8}{0{,}793651}\right\rvert\times100\% = 0{,}8\%
$$

*Tabel lengkap:*

| $n$ | $a$ | $b$ | $c$ | $f(c)$ | $E_r$ (%) |
|---|---|---|---|---|---|
| 0 | 0 | 1 | 0,800000 | 0,032000 | – |
| 1 | 0 | 0,800000 | 0,793651 | 0,002048 | 0,8000 |
| 2 | 0 | 0,793651 | 0,793245 | 0,000133 | **0,0512** ← berhenti |

**Jawaban:** akar $\approx0{,}793245$. Hanya **3 iterasi**, dibanding 8 iterasi pada Bisection untuk fungsi dan selang yang sama.

> **Catatan:** ada versi program yang memakai nilai awal `cp = a` dan `Er = 10000` agar $E_r$ bisa dihitung sejak iterasi pertama. Dengan cara itu $E_r$ iterasi 0 menjadi $\left\lvert\frac{0{,}8-0}{0{,}8}\right\rvert\times100\%=100\%$.

**Implementasi Python**

```python
def regula_falsi(f, a, b, es=0.5, cetak=True):
    """Mengembalikan (akar, jumlah_iterasi). es dalam persen."""
    if f(a) * f(b) >= 0:
        raise ValueError("Penentuan a dan b salah: f(a)*f(b) harus < 0")
    cp = None
    n = 0
    while True:
        c = b - f(b) * (a - b) / (f(a) - f(b))
        er = None if cp is None else abs((c - cp) / c) * 100
        if cetak:
            print(n, round(a, 6), round(b, 6), round(c, 6), round(f(c), 6), er)
        if f(c) == 0 or (er is not None and er < es):
            return c, n + 1
        if f(a) * f(c) < 0:
            b = c
        else:
            a = c
        cp = c
        n += 1

f = lambda x: x**3 - 2*x**2 + 6*x - 4
print(regula_falsi(f, 0, 1))           # (0.793245..., 3)
```

---

## 2.4 Iterasi Titik Tetap

**Penjelasan singkat**
Ubah persamaan $f(x)=0$ menjadi bentuk $x=g(x)$, lalu iterasi: masukkan tebakan $x$ ke $g$, hasilnya jadi tebakan berikutnya. Titik tetap $x^*$ adalah titik yang tidak berubah: $x^*=g(x^*)$. Jika barisan konvergen, hasil akhirnya pasti titik tetap.

**Kegunaan**
Mudah dibuat dan tidak perlu turunan, tetapi **bisa divergen** dan hasilnya sangat bergantung pada pilihan $g(x)$.

**Rumus**

$$
x_{k+1} = g(x_k)\qquad k=0,1,2,\ldots
$$

*Syarat konvergen:* iterasi konvergen jika $\lvert g'(x^*)\rvert<1$ di sekitar akar.

*Apakah ada titik tetap di $[a,b]$?* Jika $g$ kontinu pada $[a,b]$ dan memenuhi

$$
g(a)\ge a\quad\text{dan}\quad g(b)\le b
$$

maka ada titik tetap di $[a,b]$ (jika $g(a)=a$ atau $g(b)=b$, titik tetap sudah ditemukan di ujung).

**Langkah pengerjaan**
1. Ubah $f(x)=0$ menjadi $x=g(x)$ (ada **banyak cara**, hasilnya beda-beda).
2. Pilih tebakan awal $x_0$.
3. Hitung $x_{k+1}=g(x_k)$ dan $E_r$.
4. Berhenti jika $E_r<E_s$.

**Contoh pengerjaan:** $f(x)=x^2-x-1=0$. Satu persamaan, tiga cara menulis $x=g(x)$:

| cara | langkah | $g(x)$ |
|---|---|---|
| (1) | $x^2=x+1\Rightarrow x=1+\frac1x$ (bagi $x$) | $g(x)=1+\dfrac1x$ |
| (2) | $x^2-x=1\Rightarrow x(x-1)=1\Rightarrow x=\frac1{x-1}$ | $g(x)=\dfrac1{x-1}$ |
| (3) | $x^2=x+1\Rightarrow x=\pm\sqrt{x+1}$ | $g(x)=\sqrt{x+1}$ |

**Pakai cara (1)**, $x_0=1$, $E_s=0{,}5\%$:

| $k$ | $x_k$ | $x_{k+1}=1+\frac1{x_k}$ | $E_r$ (%) |
|---|---|---|---|
| 0 | 1,000000 | 2,000000 | 50,0000 |
| 1 | 2,000000 | 1,500000 | 33,3333 |
| 2 | 1,500000 | 1,666667 | 10,0000 |
| 3 | 1,666667 | 1,600000 | 4,1667 |
| 4 | 1,600000 | 1,625000 | 1,5385 |
| 5 | 1,625000 | 1,615385 | 0,5952 |
| 6 | 1,615385 | 1,619048 | **0,2262** ← berhenti |

Hasil $x^*\approx1{,}619048$. Nilai sebenarnya $\dfrac{1+\sqrt5}{2}=1{,}618034$ (rasio emas). Contoh satu baris ($k=3$): $1+\dfrac1{1{,}666667}=1+0{,}6=1{,}6$.

**Pakai cara (2)**, $x_0=1{,}7$: barisan **tidak menuju akar $1{,}618$**.

$$
1{,}7\to1{,}4286\to2{,}3333\to0{,}75\to-4\to-0{,}2\to-0{,}8333\to\cdots
$$

Nilainya melompat-lompat dulu, lalu akhirnya menetap di $-0{,}618$, yaitu akar *lain* dari $x^2-x-1=0$ (akar yang kedua), bukan $1{,}618$.

*Kenapa cara (1) berhasil dan cara (2) gagal mendekati $1{,}618$?* Lihat turunan $g$ di akar tersebut:

$$
g_1'(x) = -\frac1{x^2}\Rightarrow \lvert g_1'(1{,}618)\rvert = 0{,}38<1\ \checkmark
\qquad
g_2'(x) = -\frac1{(x-1)^2}\Rightarrow \lvert g_2'(1{,}618)\rvert = 2{,}62>1\ \times
$$

*Cek titik tetap:* $g(x)=1+\frac1x$ pada $[1,2]$: $g(1)=2\ge1$ dan $g(2)=1{,}5\le2$, jadi ada titik tetap di $[1,2]$.

**Implementasi Python**

```python
def titik_tetap(g, x0, es=0.5, nmax=100):
    """Iterasi x_{k+1} = g(x_k). Mengembalikan hampiran titik tetap atau None."""
    x = x0
    print("k\txk\t\txk+1\t\tEr(%)")
    for k in range(nmax):
        xn = g(x)
        er = abs((xn - x) / xn) * 100
        print(f"{k}\t{x:.6f}\t{xn:.6f}\t{er:.4f}")
        if er < es:
            return xn
        x = xn
    print("Tidak konvergen dalam", nmax, "iterasi")
    return None

# Cara (1): konvergen ke 1.618...
titik_tetap(lambda x: 1 + 1/x, 1.0)

# Cara (3): g(x) = sqrt(x + 1) juga konvergen ke 1.618...
titik_tetap(lambda x: (x + 1) ** 0.5, 1.0)
```

---

## 2.5 Metode Newton-Raphson

**Penjelasan singkat**
Di titik tebakan $x_k$, tarik **garis singgung** kurva. Titik tempat garis singgung itu memotong sumbu-$x$ jadi tebakan berikutnya $x_{k+1}$. Garis singgung adalah hampiran lurus untuk kurva, dan akar garis lurus mudah dicari.

**Kegunaan**
Konvergensi **sangat cepat** (sedikit iterasi). Kelemahan: butuh turunan $f'(x)$, sensitif pada tebakan awal, dan gagal jika $f'(x_k)=0$.

**Rumus**

Asal rumus (dari deret Taylor): potong deret setelah suku turunan pertama, lalu set $f(x)=0$:

$$
0 \approx f(x_k) + f'(x_k)(x-x_k)\ \Longrightarrow\ x = x_k - \frac{f(x_k)}{f'(x_k)}
$$

$$
x_{k+1} = x_k - \frac{f(x_k)}{f'(x_k)}
$$

**Langkah pengerjaan**
1. Turunkan $f(x)$ → dapat $f'(x)$.
2. Pilih tebakan awal $x_0$.
3. Hitung $f(x_k)$ dan $f'(x_k)$.
4. Hitung $x_{k+1}=x_k-\dfrac{f(x_k)}{f'(x_k)}$.
5. Hitung $E_r=\left\lvert\dfrac{x_{k+1}-x_k}{x_{k+1}}\right\rvert\times100\%$.
6. Jika $E_r<E_s$, berhenti. Jika belum, $k\leftarrow k+1$, ulangi dari langkah 3.

**Contoh pengerjaan 1:** $f(x)=x^7-1000$, $x_0=3$, $E_s=0{,}5\%$.

*Langkah 1:* $f'(x)=7x^6$.

*Iterasi $k=0$:*

$$
\begin{aligned}
f(3) &= 3^7 - 1000 = 2187 - 1000 = 1187\\
f'(3) &= 7\cdot3^6 = 7\cdot729 = 5103\\
x_1 &= 3 - \frac{1187}{5103} = 3 - 0{,}232608 = 2{,}767392\\
E_r &= \left\lvert\frac{2{,}767392-3}{2{,}767392}\right\rvert\times100\% = 8{,}405\%
\end{aligned}
$$

| $k$ | $x_k$ | $f(x_k)$ | $f'(x_k)$ | $x_{k+1}$ | $E_r$ (%) |
|---|---|---|---|---|---|
| 0 | 3,000000 | 1187,000000 | 5103,0000 | 2,767392 | 8,4053 |
| 1 | 2,767392 | 243,066813 | 3144,2848 | 2,690087 | 2,8737 |
| 2 | 2,690087 | 19,447202 | 2652,7504 | 2,682756 | **0,2733** ← berhenti |

**Jawaban:** akar $\approx2{,}682756$ (sebenarnya $1000^{1/7}=2{,}682696$). Hanya 3 iterasi.

**Contoh pengerjaan 2 (dari penjelasan dosen): efek tebakan awal.** $f(x)=-x^3+6x-6$, $f'(x)=-3x^2+6$. Fungsi ini hanya punya **satu akar nyata**, di $x\approx-2{,}8473$.

*Tebakan $x_0=-3$ (di kiri, aman):*

| $k$ | $x_k$ | $f(x_k)$ | $f'(x_k)$ | $x_{k+1}$ | $E_r$ (%) |
|---|---|---|---|---|---|
| 0 | −3,000000 | 3,000000 | −21,000000 | −2,857143 | 5,0000 |
| 1 | −2,857143 | 0,180758 | −18,489796 | −2,847367 | 0,3433 |
| 2 | −2,847367 | 0,000818 | −18,322492 | −2,847322 | 0,0016 |
| 3 | −2,847322 | 0,000000 | −18,321729 | −2,847322 | 0,0000 |

Dengan $E_s=0{,}5\%$, iterasi berhenti pada $k=1$ dengan hasil $-2{,}847367$ (sudah benar sampai 3 desimal).

*Tebakan $x_0=-2$:* tetap konvergen, tetapi lebih lama (jauh dari akar, loncatan pertama sampai $-3{,}67$), 5 iterasi ($-2\to-3{,}667\to-3{,}046\to-2{,}864\to-2{,}847\to-2{,}847$).

*Tebakan $x_0=1$:* **tidak konvergen dengan baik**. Nilainya berputar-putar di sekitar $1{,}3$ sampai $1{,}9$, lalu pada $x\approx1{,}417$ turunan $f'\approx-0{,}026$ hampir nol sehingga pembagian menghasilkan loncatan jauh ke $-11{,}97$. Baru setelah itu perlahan kembali.

*Kenapa?* Di sekitar $x=\sqrt2\approx1{,}414$ kurva mendatar ($f'(x)=0$ di titik puncak), garis singgung hampir sejajar sumbu-$x$, dan pembagian dengan angka sangat kecil melempar tebakan jauh. **Tebakan $x_0=\sqrt2$ tepat membuat $f'=0$ → pembagian nol → gagal total.**

**Aturan praktis (dari dosen):** pilih tebakan awal **dekat dengan akar** dan **jauh dari titik puncak/lembah kurva**. Pada contoh ini tebakan di sisi kiri ($-2,-3,-5,-10$) aman karena fungsinya masih menurun; makin dekat ke akar, makin sedikit iterasi.

**Implementasi Python**

```python
def newton(f, df, x0, es=0.5, nmax=50):
    """Newton-Raphson. Mengembalikan hampiran akar."""
    x = x0
    print("k\txk\t\tf(xk)\t\tf'(xk)\t\txk+1\t\tEr(%)")
    for k in range(nmax):
        if df(x) == 0:
            raise ZeroDivisionError(f"f'(x) = 0 di x = {x}; ganti tebakan awal")
        xn = x - f(x) / df(x)
        er = abs((xn - x) / xn) * 100
        print(f"{k}\t{x:.6f}\t{f(x):.6f}\t{df(x):.4f}\t{xn:.6f}\t{er:.4f}")
        if er < es:
            return xn
        x = xn
    return x

# Contoh 1
newton(lambda x: x**7 - 1000, lambda x: 7*x**6, 3.0)            # 2.682756...

# Contoh 2 (dosen): tebakan -3 aman
newton(lambda x: -x**3 + 6*x - 6, lambda x: -3*x**2 + 6, -3.0)  # -2.847367...
```

---

## 2.6 Metode Secant

**Penjelasan singkat**
Newton butuh turunan $f'(x)$, yang kadang sulit dihitung. Secant **mengganti turunan dengan hampiran beda hingga**, yaitu kemiringan garis antara dua titik terakhir:

$$
f'(x_k)\approx\frac{f(x_k)-f(x_{k-1})}{x_k-x_{k-1}}
$$

**Kegunaan**
Cepat dan tidak perlu turunan. Kelemahan: butuh **dua** tebakan awal dan tidak dijamin konvergen.

**Rumus**

$$
x_{k+1} = x_k - \frac{f(x_k)\,(x_k - x_{k-1})}{f(x_k) - f(x_{k-1})}
$$

**Langkah pengerjaan**
1. Pilih dua tebakan awal $x_0$ dan $x_1$.
2. Hitung $f(x_0)$ dan $f(x_1)$.
3. Hitung $x_2=x_1-\dfrac{f(x_1)(x_1-x_0)}{f(x_1)-f(x_0)}$ dan $E_r$.
4. Jika $E_r<E_s$, berhenti. Jika belum, **geser**: $x_0\leftarrow x_1$, $x_1\leftarrow x_2$, ulangi langkah 3.

**Contoh pengerjaan:** $f(x)=x^7-1000$, $x_0=2$, $x_1=3$, $E_s=0{,}5\%$.

*Langkah 2:* $f(2)=128-1000=-872$, $f(3)=1187$.

*Langkah 3:*

$$
x_2 = 3 - \frac{1187\,(3-2)}{1187-(-872)} = 3 - \frac{1187}{2059} = 3 - 0{,}576494 = 2{,}423507
$$

$$
E_r=\left\lvert\frac{2{,}423507-3}{2{,}423507}\right\rvert\times100\%=23{,}79\%
$$

Geser: $x_0\leftarrow3$, $x_1\leftarrow2{,}423507$.

| $k$ | $x_{k-1}$ | $x_k$ | $x_{k+1}$ | $E_r$ (%) |
|---|---|---|---|---|
| 0 | 2,000000 | 3,000000 | 2,423507 | 23,7876 |
| 1 | 3,000000 | 2,423507 | 2,596515 | 6,6631 |
| 2 | 2,423507 | 2,596515 | 2,712552 | 4,2778 |
| 3 | 2,596515 | 2,712552 | 2,679741 | 1,2244 |
| 4 | 2,712552 | 2,679741 | 2,682598 | **0,1065** ← berhenti |

**Jawaban:** akar $\approx2{,}682598$. Butuh 5 iterasi (Newton 3 iterasi dari $x_0=3$), tetapi tanpa menurunkan $f$.

> **Catatan:** Secant dan Regula Falsi rumusnya *terlihat sama*. Bedanya, Regula Falsi **selalu menjaga akar tetap terkurung** (memakai aturan tanda $f(a)f(c)<0$), sedangkan Secant hanya menggeser dua titik terakhir tanpa memeriksa tanda, sehingga lebih cepat tetapi tidak dijamin konvergen.

**Implementasi Python**

```python
def secant(f, x0, x1, es=0.5, nmax=50):
    """Metode Secant. Mengembalikan hampiran akar."""
    print("k\txk-1\t\txk\t\txk+1\t\tEr(%)")
    for k in range(nmax):
        x2 = x1 - f(x1) * (x1 - x0) / (f(x1) - f(x0))
        er = abs((x2 - x1) / x2) * 100
        print(f"{k}\t{x0:.6f}\t{x1:.6f}\t{x2:.6f}\t{er:.4f}")
        if er < es:
            return x2
        x0, x1 = x1, x2            # geser
    return x1

secant(lambda x: x**7 - 1000, 2.0, 3.0)    # 2.682598...
```

---

## 2.7 Perbandingan Metode Akar

**Penjelasan singkat**
Ringkasan untuk memilih metode.

**Kegunaan**
Menentukan metode yang cocok sesuai informasi yang tersedia (ada turunan atau tidak, perlu jaminan konvergen atau tidak).

| Metode | Jenis | Perlu | Kecepatan | Kelebihan | Kelemahan |
|---|---|---|---|---|---|
| [Grafis](#21-metode-grafis) | bracketing | gambar | – | memahami fungsi, cari $a,b$ | tidak akurat, tidak praktis |
| [Bisection](#22-metode-bisection) | bracketing | $a,b$ dengan $f(a)f(b)<0$ | lambat | **pasti konvergen** | banyak iterasi |
| [Regula Falsi](#23-metode-regula-falsi) | bracketing | $a,b$ dengan $f(a)f(b)<0$ | lebih cepat dari Bisection | pasti terkurung | bisa lambat bila fungsi sangat melengkung |
| [Titik Tetap](#24-iterasi-titik-tetap) | terbuka | $g(x)$, $x_0$ | tergantung $g$ | mudah dibuat | **bisa divergen**, tergantung pilihan $g$ |
| [Newton](#25-metode-newton-raphson) | terbuka | $f'(x)$, $x_0$ | **sangat cepat** | sedikit iterasi | butuh turunan, sensitif pada $x_0$, gagal jika $f'\approx0$ |
| [Secant](#26-metode-secant) | terbuka | $x_0,x_1$ | cepat | tidak perlu turunan | butuh 2 tebakan, tidak dijamin konvergen |

Hasil pada contoh (iterasi sampai $E_r<0{,}5\%$): Bisection 8 iterasi vs Regula Falsi 3 iterasi (fungsi $x^3-2x^2+6x-4$); Newton 3 iterasi vs Secant 5 iterasi ($x^7-1000$).

[Kembali ke daftar isi](#daftar-isi-dan-peta-materi)

---

# Bab 3. Sistem Persamaan Linear

> **SPL** adalah kumpulan beberapa persamaan linear dengan beberapa variabel yang harus dipenuhi **sekaligus**. Contoh dua persamaan dua variabel: $x+y=3$ dan $x-y=1$ → $x=2,\ y=1$.

**Bentuk umum** ($n$ persamaan, $n$ variabel):

$$
\begin{aligned}
a_{11}x_1 + a_{12}x_2 + \cdots + a_{1n}x_n &= b_1\\
a_{21}x_1 + a_{22}x_2 + \cdots + a_{2n}x_n &= b_2\\
&\ \,\vdots\\
a_{n1}x_1 + a_{n2}x_2 + \cdots + a_{nn}x_n &= b_n
\end{aligned}
$$

Dalam bentuk sigma, persamaan ke-$i$ ditulis:

$$
\sum_{j=1}^{n} a_{ij}\,x_j = b_i,\qquad i=1,2,\ldots,n
$$

**Bentuk matriks:** $A\mathbf{x}=\mathbf{b}$

$$
\underbrace{\begin{bmatrix}a_{11}&a_{12}&\cdots&a_{1n}\\a_{21}&a_{22}&\cdots&a_{2n}\\\vdots&&&\vdots\\a_{n1}&a_{n2}&\cdots&a_{nn}\end{bmatrix}}_{A\ (n\times n)}
\underbrace{\begin{bmatrix}x_1\\x_2\\\vdots\\x_n\end{bmatrix}}_{\mathbf x\ (n\times1)}
=
\underbrace{\begin{bmatrix}b_1\\b_2\\\vdots\\b_n\end{bmatrix}}_{\mathbf b\ (n\times1)}
$$

- $A$ = matriks koefisien (angka di depan variabel), $\mathbf x$ = yang dicari, $\mathbf b$ = ruas kanan.
- **Solusi** = nilai $x_1,x_2,\ldots,x_n$ yang memenuhi semua $n$ persamaan.
- *Augmented matrix* $[A\mid\mathbf b]$ = $A$ dan $\mathbf b$ ditulis berdampingan, dipisah garis tegak, supaya operasi baris cukup ditulis sekali.
- Aturan Cramer (determinan) **tidak praktis untuk sistem besar**, jadi dipakai metode numerik di bawah.

**Istilah penting:**

| istilah | arti |
|---|---|
| matriks **segitiga atas** ($U$) | semua angka **di bawah** diagonal utama adalah 0 |
| matriks **segitiga bawah** ($L$) | semua angka **di atas** diagonal utama adalah 0 |
| **diagonal utama** | $a_{11},a_{22},\ldots,a_{nn}$ (pojok kiri-atas ke kanan-bawah) |
| **pivot** | elemen diagonal yang dipakai untuk menolkan angka-angka di bawahnya |
| matriks **identitas** $I$ | diagonal 1, sisanya 0 (seperti angka "1" pada perkalian) |

**Cek hasil untuk semua metode di bab ini:** masukkan $\mathbf x$ ke persamaan awal. Jika tidak pas, periksa tanda pengali $m_{ik}$ dan arah substitusi (maju vs mundur). Langkah boleh berbeda (urutan baris, pilihan pivot) sehingga $U$ bisa berbeda, tetapi $\mathbf x$ akhir harus sama.

---

## 3.1 Substitusi Mundur

**Penjelasan singkat**
Dipakai jika sistem sudah berbentuk **segitiga atas**. Persamaan paling bawah hanya punya 1 variabel, jadi mulai dari situ, lalu naik ke atas.

**Kegunaan**
Langkah akhir pada eliminasi Gauss, LU, dan Cholesky.

**Rumus**

$$
x_n = \frac{b_n}{a_{nn}}\qquad\qquad
x_k = \frac{b_k - \displaystyle\sum_{j=k+1}^{n} a_{kj}\,x_j}{a_{kk}},\quad k=n-1,\,n-2,\ldots,1,\ \ a_{kk}\neq0
$$

Artinya: untuk baris ke-$k$, pindahkan semua suku yang $x$-nya sudah diketahui ke ruas kanan, lalu bagi dengan koefisien $x_k$.

**Langkah pengerjaan**
1. Mulai dari baris terakhir: $x_n = b_n/a_{nn}$.
2. Naik satu baris. Masukkan $x$ yang sudah diketahui, hitung $x$ baris itu.
3. Ulangi sampai baris pertama.

**Contoh pengerjaan:**

$$
\begin{aligned}
4x_1 - x_2 + 2x_3 + 3x_4 &= 20\\
-2x_2 + 7x_3 - 4x_4 &= -7\\
6x_3 + 5x_4 &= 4\\
3x_4 &= 6
\end{aligned}
$$

*Baris 4:* $3x_4 = 6\ \Rightarrow\ x_4 = 2$.

*Baris 3:* masukkan $x_4=2$.

$$
6x_3 + 5(2) = 4\ \Rightarrow\ 6x_3 = 4 - 10 = -6\ \Rightarrow\ x_3 = -1
$$

*Baris 2:* masukkan $x_3=-1$ dan $x_4=2$.

$$
-2x_2 + 7(-1) - 4(2) = -7\ \Rightarrow\ -2x_2 = -7 + 7 + 8 = 8\ \Rightarrow\ x_2 = -4
$$

*Baris 1:* masukkan $x_2=-4$, $x_3=-1$, $x_4=2$.

$$
4x_1 - (-4) + 2(-1) + 3(2) = 20\ \Rightarrow\ 4x_1 + 4 - 2 + 6 = 20\ \Rightarrow\ 4x_1 = 12\ \Rightarrow\ x_1 = 3
$$

**Jawaban:** $\mathbf x = (3,\,-4,\,-1,\,2)^T$.

Cek persamaan 1: $4(3)-(-4)+2(-1)+3(2)=12+4-2+6=20$ ✓.

**Contoh kedua:**

$$
A=\begin{bmatrix}2&3&-1\\0&-2&-3\\0&0&-5\end{bmatrix},\qquad
\mathbf b=\begin{bmatrix}5\\-7\\-15\end{bmatrix}
$$

$x_3=\frac{-15}{-5}=3$; $-2x_2-3(3)=-7\Rightarrow x_2=-1$; $2x_1+3(-1)-3=5\Rightarrow x_1=5{,}5$.

> **Catatan:**
> - **Syarat $a_{kk}\neq0$.** Jika ada $a_{kk}=0$, rumus membagi dengan nol sehingga metode berhenti. Lebih tepatnya, *metodenya tidak bisa dilanjutkan* (bukan "SPL tidak punya jawaban"); biasanya solusinya menukar urutan baris.
> - Matriks pada contoh kedua punya $-3$ pada baris 2 kolom 3, sedangkan hasil eliminasi pada contoh di [3.2](#32-eliminasi-gauss) punya $-1$ di posisi itu. Dengan $-1$ jawabannya $(1,2,3)$; dengan $-3$ jawabannya $(5{,}5,\,-1,\,3)$. Ikuti angka yang tertulis di soal.

**Implementasi Python**

```python
def subst_mundur(A, b):
    """Menyelesaikan A x = b untuk A segitiga ATAS."""
    n = len(b)
    x = [0.0] * n
    for k in range(n - 1, -1, -1):                        # dari baris terakhir ke atas
        s = sum(A[k][j] * x[j] for j in range(k + 1, n))  # suku yang x-nya sudah diketahui
        x[k] = (b[k] - s) / A[k][k]
    return x

# Contoh pertama
A1 = [[4, -1, 2, 3], [0, -2, 7, -4], [0, 0, 6, 5], [0, 0, 0, 3]]
print(subst_mundur(A1, [20, -7, 4, 6]))        # [3.0, -4.0, -1.0, 2.0]

# Contoh kedua
A2 = [[2, 3, -1], [0, -2, -3], [0, 0, -5]]
print(subst_mundur(A2, [5, -7, -15]))          # [5.5, -1.0, 3.0]
```

Versi MATLAB:
```
x(n) = b(n) / A(n,n);
for k = n-1:-1:1
    x(k) = ( b(k) - A(k,k+1:n)*x(k+1:n) ) / A(k,k);
end
```

---

## 3.2 Eliminasi Gauss

**Penjelasan singkat**
Ubah $A\mathbf x=\mathbf b$ menjadi $U\mathbf x=\mathbf y$ dengan $U$ **segitiga atas**, lalu selesaikan dengan [substitusi mundur](#31-substitusi-mundur). Solusinya sama persis dengan sistem aslinya.

**Kegunaan**
Metode langsung paling dasar untuk SPL umum; syaratnya hanya pivot $\neq0$.

**Rumus**

Tiga operasi baris elementer (tidak mengubah solusi):

| operasi | arti |
|---|---|
| **1. Pertukaran** | dua baris boleh ditukar tempatnya |
| **2. Penskalaan** | satu baris boleh dikali konstanta **bukan nol** |
| **3. Penggantian** | satu baris diganti dengan (baris itu − kelipatan baris lain) |

Operasi 3 yang dipakai utama:

$$
R_i \leftarrow R_i - m_{ik}\,R_k\qquad\text{dengan faktor pengali}\qquad m_{ik} = \frac{a_{ik}}{a_{kk}}
$$

$R_k$ = baris **pivot**, $a_{kk}$ = elemen pivot, $a_{ik}$ = elemen yang ingin dijadikan 0.

**Lokasi pivot** (kata dosen): mulai di pojok kiri atas $a_{11}$, lalu **geser kanan satu, turun satu** ($a_{22},a_{33},\ldots$). Pivot **dibiarkan apa adanya** (tidak dibuat 1); tugasnya hanya menolkan elemen di bawahnya.

**Langkah pengerjaan**
1. Tulis augmented matrix $[A\mid\mathbf b]$.
2. Pivot pertama $a_{11}$. Untuk tiap baris di bawahnya hitung $m_{i1}=a_{i1}/a_{11}$, lalu $R_i\leftarrow R_i-m_{i1}R_1$ (seluruh baris, **termasuk kolom $\mathbf b$**).
3. Pivot kedua $a_{22}$ (nilai **barunya**). Tolkan elemen di bawahnya dengan cara yang sama.
4. Lanjut sampai terbentuk segitiga atas.
5. Selesaikan dengan **substitusi mundur**.

**Contoh pengerjaan:**

$$
\begin{aligned}
2x_1 + 3x_2 - x_3 &= 5\\
4x_1 + 4x_2 - 3x_3 &= 3\\
-2x_1 + 3x_2 - x_3 &= 1
\end{aligned}
\quad\Longrightarrow\quad
\left[\begin{array}{ccc|c}2&3&-1&5\\4&4&-3&3\\-2&3&-1&1\end{array}\right]
$$

*Langkah A: pivot $a_{11}=2$.*

Baris 2: $m_{21}=\dfrac{4}{2}=2$ → $R_2\leftarrow R_2-2R_1$:

$$
\begin{aligned}
R_2 &= (4,\ 4,\ -3\mid 3)\\
2R_1 &= (4,\ 6,\ -2\mid 10)\\
R_2-2R_1 &= (0,\ -2,\ -1\mid -7)
\end{aligned}
$$

Baris 3: $m_{31}=\dfrac{-2}{2}=-1$ → $R_3\leftarrow R_3-(-1)R_1 = R_3+R_1$:

$$
(-2+2,\ 3+3,\ -1-1\mid 1+5) = (0,\ 6,\ -2\mid 6)
$$

Hasil:

$$
\left[\begin{array}{ccc|c}2&3&-1&5\\0&-2&-1&-7\\0&6&-2&6\end{array}\right]
$$

*Langkah B: pivot $a_{22}=-2$.*

$m_{32}=\dfrac{6}{-2}=-3$ → $R_3\leftarrow R_3-(-3)R_2=R_3+3R_2$:

$$
(0,\ 6+3(-2),\ -2+3(-1)\mid 6+3(-7)) = (0,\ 0,\ -5\mid -15)
$$

Hasil akhir (segitiga atas):

$$
\left[\begin{array}{ccc|c}2&3&-1&5\\0&-2&-1&-7\\0&0&-5&-15\end{array}\right]
$$

*Langkah C: substitusi mundur.*

$$
\begin{aligned}
-5x_3 = -15 &\Rightarrow x_3 = 3\\
-2x_2 - x_3 = -7 &\Rightarrow -2x_2 = -7 + 3 = -4 \Rightarrow x_2 = 2\\
2x_1 + 3x_2 - x_3 = 5 &\Rightarrow 2x_1 = 5 - 6 + 3 = 2 \Rightarrow x_1 = 1
\end{aligned}
$$

**Jawaban:** $\mathbf x=(1,\,2,\,3)^T$.

Cek di soal asli: $2(1)+3(2)-3=5$ ✓, $\ 4(1)+4(2)-3(3)=3$ ✓, $\ -2(1)+3(2)-3=1$ ✓.

> **Catatan:** jika pivot $=0$, tukar baris dulu. Pada Gauss pivot dibiarkan dan hanya bagian **bawah** yang dinolkan; pada Gauss-Jordan ([3.5](#35-gauss-jordan-dan-gauss-seidel)) pivot dijadikan 1 dan atas-bawah dinolkan.

**Implementasi Python**

```python
def gauss(A, b):
    """Eliminasi Gauss + substitusi mundur (memakai subst_mundur dari 3.1)."""
    n = len(b)
    A = [row[:] for row in A]
    b = b[:]
    for k in range(n - 1):                    # pivot ke-k
        for i in range(k + 1, n):             # baris-baris di bawah pivot
            m = A[i][k] / A[k][k]             # faktor pengali
            for j in range(k, n):
                A[i][j] -= m * A[k][j]
            b[i] -= m * b[k]                  # kolom b ikut diubah
    return subst_mundur(A, b)

print(gauss([[2, 3, -1], [4, 4, -3], [-2, 3, -1]], [5, 3, 1]))   # [1.0, 2.0, 3.0]
```

---

## 3.3 Dekomposisi LU

**Penjelasan singkat**
Pecah matriks $A$ menjadi perkalian dua matriks segitiga:

$$
A = LU
$$

- $L$ (*lower*) segitiga **bawah**, diagonalnya semua **1**.
- $U$ (*upper*) segitiga **atas**, diagonalnya bebas.

$$
\begin{bmatrix}a_{11}&a_{12}&a_{13}\\a_{21}&a_{22}&a_{23}\\a_{31}&a_{32}&a_{33}\end{bmatrix}
=
\begin{bmatrix}1&0&0\\l_{21}&1&0\\l_{31}&l_{32}&1\end{bmatrix}
\begin{bmatrix}u_{11}&u_{12}&u_{13}\\0&u_{22}&u_{23}\\0&0&u_{33}\end{bmatrix}
$$

**Kegunaan**
$A=LU$ cukup dihitung **sekali**. Jika ruas kanan $\mathbf b$ berganti-ganti (banyak kasus dengan $A$ sama), tinggal ulang substitusi maju dan mundur. Seperti Gauss, metode ini butuh semua pivot $\neq0$ (jika ada pivot 0, perlu tukar baris).

**Rumus**

$$
A\mathbf x=\mathbf b\ \Rightarrow\ LU\mathbf x=\mathbf b
\qquad\text{misalkan } U\mathbf x=\mathbf y \ \Rightarrow\ \underbrace{L\mathbf y=\mathbf b}_{\text{selesaikan dulu}}\ \to\ \underbrace{U\mathbf x=\mathbf y}_{\text{lalu ini}}
$$

Substitusi maju (untuk $L\mathbf y=\mathbf b$, karena diagonal $L$ adalah 1):

$$
y_i = b_i - \sum_{j=1}^{i-1} l_{ij}\,y_j
$$

Jika diagonalnya bukan 1 (misalnya $U^T$ pada Cholesky), bagi dengan diagonalnya:

$$
y_i = \frac{b_i - \displaystyle\sum_{j=1}^{i-1} l_{ij}\,y_j}{l_{ii}}
$$

Pengali dari eliminasi: $m_{ik}=\dfrac{a_{ik}}{a_{kk}}$, dengan operasi $R_i\leftarrow R_i-m_{ik}R_k$. Yang ditulis ke $L$ adalah **$m_{ik}$ apa adanya, dengan tandanya**.

**Langkah pengerjaan**

*Besar (3 langkah):*
1. **Faktorkan** $A=LU$.
2. Selesaikan $L\mathbf y=\mathbf b$ dengan **substitusi maju** → dapat $\mathbf y$.
3. Selesaikan $U\mathbf x=\mathbf y$ dengan **substitusi mundur** → dapat $\mathbf x$.

*Cara memfaktorkan (LU Gauss):*
1. Tulis $A=I\cdot A$ (sebelah kiri matriks identitas).
2. Lakukan eliminasi Gauss pada $A$ di ruas kanan sampai menjadi segitiga atas $U$.
3. **Setiap faktor pengali** $m_{ik}$ yang dipakai ditulis ke **baris $i$, kolom $k$** pada matriks di kiri.
4. Setelah selesai: matriks kiri = $L$, matriks kanan = $U$.

Mengapa boleh: $A=I\cdot A$ selalu benar. Tiap operasi baris mengubah ruas kanan, dan "catatan" operasinya disimpan di ruas kiri sehingga perkaliannya tetap sama dengan $A$.

**Contoh pengerjaan: faktorisasi**

$$
A=\begin{bmatrix}4&3&-1\\-2&-4&5\\1&2&6\end{bmatrix}
$$

*Pivot $a_{11}=4$:*

$m_{21}=\dfrac{-2}{4}=-0{,}5$ → $R_2\leftarrow R_2-(-0{,}5)R_1=R_2+0{,}5R_1$:

$$
(-2+2,\ -4+1{,}5,\ 5-0{,}5)=(0,\ -2{,}5,\ 4{,}5)
$$

$m_{31}=\dfrac{1}{4}=0{,}25$ → $R_3\leftarrow R_3-0{,}25R_1$:

$$
(1-1,\ 2-0{,}75,\ 6+0{,}25)=(0,\ 1{,}25,\ 6{,}25)
$$

*Pivot $a_{22}=-2{,}5$:*

$m_{32}=\dfrac{1{,}25}{-2{,}5}=-0{,}5$ (pembaginya pivot $-2{,}5$) → $R_3\leftarrow R_3-(-0{,}5)R_2=R_3+0{,}5R_2$:

$$
(0,\ 0,\ 6{,}25+0{,}5(4{,}5)) = (0,\ 0,\ 8{,}5)
$$

*Hasil:*

$$
L=\begin{bmatrix}1&0&0\\-0{,}5&1&0\\0{,}25&-0{,}5&1\end{bmatrix},\qquad
U=\begin{bmatrix}4&3&-1\\0&-2{,}5&4{,}5\\0&0&8{,}5\end{bmatrix}
$$

Cek $A=LU$ (baris × kolom): baris 2 kolom 2: $(-0{,}5)(3)+(1)(-2{,}5)=-4$ ✓ ($a_{22}=-4$). Baris 3 kolom 3: $0{,}25(-1)+(-0{,}5)(4{,}5)+1(8{,}5)=-0{,}25-2{,}25+8{,}5=6$ ✓ ($a_{33}=6$).

**Contoh pengerjaan: menyelesaikan $A\mathbf x=\mathbf b$ dengan $\mathbf b=(9,\,2,\,16)^T$**

*Substitusi maju* ($L\mathbf y=\mathbf b$):

$$
\begin{aligned}
y_1 &= 9\\
-0{,}5y_1 + y_2 = 2 &\Rightarrow y_2 = 2 + 0{,}5(9) = 6{,}5\\
0{,}25y_1 - 0{,}5y_2 + y_3 = 16 &\Rightarrow y_3 = 16 - 0{,}25(9) + 0{,}5(6{,}5) = 16 - 2{,}25 + 3{,}25 = 17
\end{aligned}
$$

*Substitusi mundur* ($U\mathbf x=\mathbf y$ dengan $\mathbf y=(9,\,6{,}5,\,17)^T$):

$$
\begin{aligned}
8{,}5x_3 = 17 &\Rightarrow x_3 = 2\\
-2{,}5x_2 + 4{,}5x_3 = 6{,}5 &\Rightarrow -2{,}5x_2 = 6{,}5 - 9 = -2{,}5 \Rightarrow x_2 = 1\\
4x_1 + 3x_2 - x_3 = 9 &\Rightarrow 4x_1 = 9 - 3 + 2 = 8 \Rightarrow x_1 = 2
\end{aligned}
$$

**Jawaban:** $\mathbf x=(2,\,1,\,2)^T$.

Cek di soal asli: $4(2)+3(1)-2=9$ ✓, $\ -2(2)-4(1)+5(2)=2$ ✓, $\ 2+2(1)+6(2)=16$ ✓.

**Contoh kecil (dibahas dosen):**

$$
\begin{bmatrix}2&-1&-1\\0&-4&2\\6&-3&1\end{bmatrix}
=
\begin{bmatrix}1&0&0\\0&1&0\\3&0&1\end{bmatrix}
\begin{bmatrix}2&-1&-1\\0&-4&2\\0&0&4\end{bmatrix}
$$

Cek: baris 3 kolom 1 dari hasil kali $=3\cdot2+0\cdot0+1\cdot0=6$ ✓. $L$ dibaca dari pengali: $m_{31}=\dfrac62=3$, sedangkan baris 2 sudah punya 0 di kolom 1 sehingga $m_{21}=0$.

**Implementasi Python**

```python
def lu(A):
    """Faktorisasi LU Gauss. Mengembalikan (L, U) dengan A = L U."""
    n = len(A)
    L = [[1.0 if i == j else 0.0 for j in range(n)] for i in range(n)]
    U = [row[:] for row in A]
    for k in range(n - 1):
        for i in range(k + 1, n):
            m = U[i][k] / U[k][k]
            L[i][k] = m                       # simpan pengali (dengan tanda) di L
            for j in range(k, n):
                U[i][j] -= m * U[k][j]
    return L, U

def subst_maju(L, b):
    """Menyelesaikan L y = b untuk L segitiga BAWAH."""
    n = len(b)
    y = [0.0] * n
    for i in range(n):
        s = sum(L[i][j] * y[j] for j in range(i))
        y[i] = (b[i] - s) / L[i][i]
    return y

A = [[4, 3, -1], [-2, -4, 5], [1, 2, 6]]
L, U = lu(A)
print(L)    # [[1,0,0],[-0.5,1,0],[0.25,-0.5,1]]
print(U)    # [[4,3,-1],[0,-2.5,4.5],[0,0,8.5]]

y = subst_maju(L, [9, 2, 16])                 # [9.0, 6.5, 17.0]
x = subst_mundur(U, y)                        # memakai subst_mundur dari 3.1
print(y, x)                                   # x = [2.0, 1.0, 2.0]
```

---

## 3.4 Dekomposisi Cholesky

**Penjelasan singkat**
Faktorkan matriks **simetris** $A$ menjadi

$$
A = U^T U
$$

dengan $U$ segitiga atas (disebut **faktor Cholesky**) dan $U^T$ = $U$ yang dicerminkan (jadi segitiga bawah).

**Kegunaan**
Lebih hemat daripada LU karena hanya separuh matriks yang dihitung (bagian bawah kembar dengan bagian atas).

**Syarat:** $A=A^T$ (elemen $a_{ij}=a_{ji}$; cermin terhadap diagonal). **Cek simetris dulu**; jika tidak simetris, soal tidak bisa dikerjakan dengan Cholesky. Agar akar kuadratnya selalu bisa dihitung, matriks juga harus *positif definit*; jika ada bilangan negatif di bawah akar, metode gagal.

**Rumus** (untuk matriks $3\times3$, hasil menyamakan $A=U^TU$ elemen demi elemen):

| dari | menjadi |
|---|---|
| $a_{11}=u_{11}^2$ | $u_{11}=\sqrt{a_{11}}$ |
| $a_{12}=u_{11}u_{12}$ | $u_{12}=\dfrac{a_{12}}{u_{11}}$ |
| $a_{13}=u_{11}u_{13}$ | $u_{13}=\dfrac{a_{13}}{u_{11}}$ |
| $a_{22}=u_{12}^2+u_{22}^2$ | $u_{22}=\sqrt{a_{22}-u_{12}^2}$ |
| $a_{23}=u_{12}u_{13}+u_{22}u_{23}$ | $u_{23}=\dfrac{a_{23}-u_{12}u_{13}}{u_{22}}$ |
| $a_{33}=u_{13}^2+u_{23}^2+u_{33}^2$ | $u_{33}=\sqrt{a_{33}-u_{13}^2-u_{23}^2}$ |

Urutan hitung: $u_{11}\to u_{12}\to u_{13}\to u_{22}\to u_{23}\to u_{33}$ (setiap rumus memakai yang sudah dihitung sebelumnya).

Bentuk umum untuk matriks $n\times n$ (sigma mewakili "kurangi semua suku yang sudah dihitung"):

$$
u_{ii}=\sqrt{a_{ii}-\sum_{k=1}^{i-1}u_{ki}^{2}}
\qquad\qquad
u_{ij}=\frac{a_{ij}-\displaystyle\sum_{k=1}^{i-1}u_{ki}\,u_{kj}}{u_{ii}}\quad(j>i)
$$

Untuk $i=1$ jumlahnya kosong ($=0$), sehingga $u_{11}=\sqrt{a_{11}}$ dan $u_{1j}=a_{1j}/u_{11}$. Rumus ini sama dengan fungsi `cholesky` pada kode di bawah.

**Langkah pengerjaan**
1. **Cek simetris**: $a_{12}=a_{21}$, $a_{13}=a_{31}$, $a_{23}=a_{32}$.
2. Hitung $U$ dengan rumus di atas.
3. Tulis $U^T$ (cerminkan $U$).
4. Selesaikan $U^T\mathbf y=\mathbf b$ → substitusi **maju** → dapat $\mathbf y$.
5. Selesaikan $U\mathbf x=\mathbf y$ → substitusi **mundur** → dapat $\mathbf x$.

**Contoh pengerjaan:**

$$
A=\begin{bmatrix}25&15&-5\\15&18&0\\-5&0&11\end{bmatrix},\qquad
\mathbf b=\begin{bmatrix}45\\48\\34\end{bmatrix}
$$

*Langkah 1: cek simetris.* $a_{12}=a_{21}=15$ ✓, $a_{13}=a_{31}=-5$ ✓, $a_{23}=a_{32}=0$ ✓.

*Langkah 2: hitung $U$.*

$$
\begin{aligned}
u_{11} &= \sqrt{25} = 5\\
u_{12} &= \frac{15}{5} = 3\\
u_{13} &= \frac{-5}{5} = -1\\
u_{22} &= \sqrt{18 - 3^2} = \sqrt{9} = 3\\
u_{23} &= \frac{0 - (3)(-1)}{3} = \frac{3}{3} = 1\\
u_{33} &= \sqrt{11 - (-1)^2 - 1^2} = \sqrt{9} = 3
\end{aligned}
$$

$$
U=\begin{bmatrix}5&3&-1\\0&3&1\\0&0&3\end{bmatrix}\qquad
U^T=\begin{bmatrix}5&0&0\\3&3&0\\-1&1&3\end{bmatrix}
$$

Cek $A=U^TU$: $(1,1)$: $5\cdot5=25$ ✓. $(1,2)$: $5\cdot3=15$ ✓. $(1,3)$: $5\cdot(-1)=-5$ ✓. $(2,2)$: $3^2+3^2=18$ ✓. $(2,3)$: $3(-1)+3(1)=0$ ✓. $(3,3)$: $(-1)^2+1^2+3^2=11$ ✓.

*Langkah 4: $U^T\mathbf y=\mathbf b$ (maju).*

$$
\begin{aligned}
5y_1 = 45 &\Rightarrow y_1 = 9\\
3y_1 + 3y_2 = 48 &\Rightarrow 27 + 3y_2 = 48 \Rightarrow y_2 = 7\\
-y_1 + y_2 + 3y_3 = 34 &\Rightarrow -9 + 7 + 3y_3 = 34 \Rightarrow 3y_3 = 36 \Rightarrow y_3 = 12
\end{aligned}
$$

*Langkah 5: $U\mathbf x=\mathbf y$ (mundur).*

$$
\begin{aligned}
3x_3 = 12 &\Rightarrow x_3 = 4\\
3x_2 + x_3 = 7 &\Rightarrow 3x_2 = 3 \Rightarrow x_2 = 1\\
5x_1 + 3x_2 - x_3 = 9 &\Rightarrow 5x_1 = 9 - 3 + 4 = 10 \Rightarrow x_1 = 2
\end{aligned}
$$

**Jawaban:** $\mathbf x=(2,\,1,\,4)^T$.

Cek di soal asli: $25(2)+15(1)-5(4)=45$ ✓, $\ 15(2)+18(1)=48$ ✓, $\ -5(2)+11(4)=34$ ✓.

> **Catatan:** materi kuliah memakai simbol $U$ (atas). Beberapa buku memakai $L$ (bawah, $A=LL^T$); itu sama saja, hanya beda nama.

**Implementasi Python**

```python
def cholesky(A):
    """Faktor Cholesky U (segitiga atas) dengan A = U^T U."""
    n = len(A)
    for i in range(n):
        for j in range(i):
            if A[i][j] != A[j][i]:
                raise ValueError("Matriks tidak simetris")
    U = [[0.0] * n for _ in range(n)]
    for i in range(n):
        U[i][i] = (A[i][i] - sum(U[k][i] ** 2 for k in range(i))) ** 0.5
        for j in range(i + 1, n):
            U[i][j] = (A[i][j] - sum(U[k][i] * U[k][j] for k in range(i))) / U[i][i]
    return U

A = [[25, 15, -5], [15, 18, 0], [-5, 0, 11]]
U = cholesky(A)                                         # [[5,3,-1],[0,3,1],[0,0,3]]
UT = [[U[j][i] for j in range(3)] for i in range(3)]    # transpose
y = subst_maju(UT, [45, 48, 34])                        # memakai subst_maju dari 3.3
x = subst_mundur(U, y)                                  # memakai subst_mundur dari 3.1
print(U, y, x)                                          # y = [9, 7, 12] ; x = [2, 1, 4]
```

---

## 3.5 Gauss-Jordan dan Gauss-Seidel

> Bagian ini tidak ada di materi utama, hanya dibahas sekilas oleh dosen.

### Gauss-Jordan

**Penjelasan singkat dan kegunaan.** Sama seperti eliminasi Gauss, tetapi (a) pivot dijadikan **1** (bagi barisnya dengan pivot) dan (b) elemen **di atas dan di bawah** pivot dijadikan **0**. Hasilnya matriks identitas, sehingga **solusi langsung terbaca di kolom $\mathbf b$** tanpa substitusi mundur.

**Contoh pengerjaan** (lanjutan contoh 3.2, mulai dari hasil Gauss):

$$
\left[\begin{array}{ccc|c}2&3&-1&5\\0&-2&-1&-7\\0&0&-5&-15\end{array}\right]
\xrightarrow{R_3\div(-5)}
\left[\begin{array}{ccc|c}2&3&-1&5\\0&-2&-1&-7\\0&0&1&3\end{array}\right]
$$

$$
\xrightarrow{R_2+R_3,\ \ R_1+R_3}
\left[\begin{array}{ccc|c}2&3&0&8\\0&-2&0&-4\\0&0&1&3\end{array}\right]
\xrightarrow{R_2\div(-2)}
\left[\begin{array}{ccc|c}2&3&0&8\\0&1&0&2\\0&0&1&3\end{array}\right]
$$

$$
\xrightarrow{R_1-3R_2,\ \ R_1\div2}
\left[\begin{array}{ccc|c}1&0&0&1\\0&1&0&2\\0&0&1&3\end{array}\right]
\ \Rightarrow\ x_1=1,\ x_2=2,\ x_3=3
$$

**Implementasi Python**

```python
def gauss_jordan(A, b):
    """Gauss-Jordan: pivot jadi 1, atas dan bawah pivot jadi 0."""
    n = len(b)
    M = [A[i][:] + [b[i]] for i in range(n)]          # augmented matrix
    for k in range(n):
        p = M[k][k]
        M[k] = [v / p for v in M[k]]                  # pivot jadi 1
        for i in range(n):
            if i != k:
                m = M[i][k]
                M[i] = [vi - m * vk for vi, vk in zip(M[i], M[k])]
    return [M[i][n] for i in range(n)]

print(gauss_jordan([[2, 3, -1], [4, 4, -3], [-2, 3, -1]], [5, 3, 1]))   # [1.0, 2.0, 3.0]
```

### Gauss-Seidel

**Penjelasan singkat.** Metode **iteratif** (berulang), bukan eliminasi. Tebak nilai awal semua variabel (paling mudah: semua $0$), lalu hitung ulang tiap variabel satu per satu **memakai nilai terbaru yang sudah ada**, berulang sampai nilainya berhenti berubah (konvergen).

**Kegunaan.** Cocok untuk sistem besar dan renggang. Biasanya konvergen jika diagonal $A$ dominan, yaitu $\lvert a_{ii}\rvert$ lebih besar dari jumlah $\lvert a_{ij}\rvert$ lainnya pada baris itu.

**Rumus**

$$
x_i^{(k+1)} = \frac{b_i - \displaystyle\sum_{j<i}a_{ij}\,x_j^{(k+1)} - \sum_{j>i}a_{ij}\,x_j^{(k)}}{a_{ii}}
$$

**Contoh pengerjaan:** $4x+y=9$ dan $x+3y=5$ (jawaban pasti $x=2,\ y=1$). Persamaan diubah menjadi $x=\dfrac{9-y}{4}$ dan $y=\dfrac{5-x}{3}$. Mulai $x=0,\ y=0$:

| iterasi | $x=\frac{9-y}{4}$ | $y=\frac{5-x}{3}$ |
|---|---|---|
| 1 | 2,2500 | 0,9167 |
| 2 | 2,0208 | 0,9931 |
| 3 | 2,0017 | 0,9994 |
| 4 | 2,0001 | 1,0000 |
| 5 | 2,0000 | 1,0000 |

Contoh satu baris (iterasi 1): $x=\dfrac{9-0}{4}=2{,}25$, lalu langsung dipakai: $y=\dfrac{5-2{,}25}{3}=0{,}9167$. Nilainya mendekati nilai tertentu, yaitu konvergen.

**Implementasi Python**

```python
def gauss_seidel(A, b, x0=None, es=0.5, nmax=100):
    """Gauss-Seidel. es = batas galat relatif maksimum (persen)."""
    n = len(b)
    x = x0[:] if x0 else [0.0] * n
    for k in range(1, nmax + 1):
        x_lama = x[:]
        for i in range(n):
            s = sum(A[i][j] * x[j] for j in range(n) if j != i)   # x[j] sudah yang terbaru
            x[i] = (b[i] - s) / A[i][i]
        er = max(abs((x[i] - x_lama[i]) / x[i]) * 100 for i in range(n) if x[i] != 0)
        print(k, [round(v, 4) for v in x], round(er, 4))
        if er < es:
            break
    return x

gauss_seidel([[4, 1], [1, 3]], [9, 5])        # menuju [2.0, 1.0]
```

---

## 3.6 Perbandingan Metode SPL

**Penjelasan singkat**
Ringkasan untuk memilih metode.

**Kegunaan**
Memilih metode sesuai bentuk matriks dan kebutuhan (satu $\mathbf b$ atau banyak $\mathbf b$).

| | [**Eliminasi Gauss**](#32-eliminasi-gauss) | [**Dekomposisi LU**](#33-dekomposisi-lu) | [**Cholesky**](#34-dekomposisi-cholesky) |
|---|---|---|---|
| Syarat | pivot $\neq0$ | pivot $\neq0$ | $A$ **simetris** (dan positif definit) |
| Hasil faktorisasi | $U$ (segitiga atas) | $L$ dan $U$ | $U$ saja ($A=U^TU$) |
| Pengali $m_{ik}$ | dipakai lalu dibuang | **disimpan di $L$** | tidak ada pengali |
| Selesaikan dengan | substitusi mundur | maju ($L\mathbf y=\mathbf b$), lalu mundur | maju ($U^T\mathbf y=\mathbf b$), lalu mundur |
| Kelebihan | sederhana | $A$ sama + banyak $\mathbf b$ → hemat | hanya hitung separuh matriks |
| Pivot | dibiarkan (tidak dibuat 1) | dibiarkan | – |

**Hal yang sering salah**
- **Pivot $=0$** → pembagian nol → tukar baris dulu.
- Saat operasi baris, ubah **seluruh baris termasuk kolom $\mathbf b$** (pada augmented matrix).
- Pada $L$, **diagonalnya 1** dan yang ditulis adalah **pengali beserta tanda**.
- Substitusi **maju** untuk matriks segitiga **bawah** ($L$, $U^T$); substitusi **mundur** untuk segitiga **atas** ($U$).
- Cholesky: **cek simetris dulu**; yang dihitung hanya $U$, lalu pakai $U^T$ untuk $\mathbf y$.

[Kembali ke daftar isi](#daftar-isi-dan-peta-materi)

---

# Lampiran. Glosarium

| Istilah | Arti sederhana |
|---|---|
| **Galat (error)** | selisih antara nilai benar dan nilai hasil hitungan |
| **Hampiran / pendekatan** | nilai perkiraan yang dipakai pengganti nilai benar |
| **Iterasi** | satu putaran hitungan; diulang berkali-kali |
| **Konvergen** | hasil makin lama makin mendekat ke satu nilai tertentu |
| **Divergen** | hasil makin menjauh / tidak menuju satu nilai |
| **Mantisa** | deretan digit penting pada bentuk $0{,}d_1d_2\ldots\times10^n$ |
| **Eksponen** | pangkat pada basis (10 atau 2) |
| **Machine epsilon** | batas galat relatif pembulatan karena penyimpanan di komputer |
| **Akar** | nilai $x$ dengan $f(x)=0$ |
| **Bracketing** | menjepit akar di antara dua titik $a$ dan $b$ |
| **Titik tetap** | titik $x^*$ dengan $g(x^*)=x^*$ |
| **Pivot** | elemen diagonal pemandu untuk menolkan elemen di bawahnya |
| **Augmented matrix** | matriks $A$ digabung kolom $\mathbf b$ |
| **Segitiga atas / bawah** | matriks yang bagian bawah / atas diagonalnya semua nol |
| **Substitusi mundur / maju** | menghitung variabel dari persamaan terakhir ke atas / dari pertama ke bawah |
| **Simetris** | matriks yang sama dengan transposenya ($a_{ij}=a_{ji}$) |
| **Faktor Cholesky** | matriks $U$ pada $A=U^TU$ |

[Kembali ke daftar isi](#daftar-isi-dan-peta-materi)