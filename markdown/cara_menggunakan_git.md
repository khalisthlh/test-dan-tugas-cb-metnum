# **Panduan cara menggunakan git**

## 1 Pertama saat buka repo pertama kali

### 1.1 Clone Repository dengan cara sebagai berikut:

```
git clone https://github.com/nama-username/nama-repo
```

Contoh penggunaan:

```
git clone https://github.com/khalisthlh/test-dan-tugas-cb-metnum
```

### 1.2 Set nama dan email git

(opsional, kalau sudah set global. Tapi untuk kasus misal pakai akun kedua atau pakai akun lain, bisa dengan cara seperti ini)


```
git config user.name "Username"
git config user.email "namaemail@mail.com"
```

Tapi kalau mau sekalian global, biasanya seperti ini:

```
git config --global user.name "Username"
git config --global user.email "namaemail@mail.com"
```

### 1.3 Push pertama kali

Biasanya kalau pertama kalu push (kirim ke repo github), itu ada diminta input password. Hanya saja, password yang diminta itu bukan password dari akun anda saat login ke github. Tetapi menggunakan token khusus yang disebut sebagai Personal Access Token.

Cara untuk buat token:

1. Klik foto profil di GitHub --> Settings
2. Scroll bawah --> Developer settings
3. Personal access tokens --> Tokens (classic)
4. Klik Generate new token --> Generate new token (classic)
5. Isi:
    - Note: (sembarang mau isi apa)
    - Expiration: pilih 90 days atau No expiration (terserah)
    - Centang scope repo (yang paling atas)
6. Klik Generate token
7. Langsung copy token-nya. (muncul sekali btw)

Nanti di terminal saat pertama kali lakukan `git push`, Ada muncul diminta isi username dan password. Isi username anda, terus saat isi password, paste token tersebut dan harusnya atau normalnya berhasil nanti.

## 2. Git workflow

singkat saja.


ini untuk ambil versi yang trbaru atau sudah di update sama user lain misal

```
git pull
```

```
git add .
```

atau lebih baik misal contoh untuk spesifik

```
git add namafile.py
```

berikan apa penjelasan singkat perubahan

```
git commit -m "penjelasan disini"
```

kirim ke repo

```
git push
```

(misal ada salah dan ingin ubah)

kalau yang barusan commit

```
git commit --amend -m "pesan commit yang baru"
git push --force-with-lease
```

kalau yang berberapa commit sebelumnya

```
git rebase -i HEAD~N
```

(ganti N dengan angka)

.4t53ggerasdtverwf4refvvdaers fbgtreafgtrbynhjtgerfsdc

referensi lebih lanjut tanya chatbot ai atau baca ini https://git-scm.com/book/en/v2