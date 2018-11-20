---
layout: post
title: Initial Server Setup with Ubuntu 16.04
date: 2018-08-30 03:00 +0700
author: Iman Sugirman
image: https://res.cloudinary.com/wpcloudserver/image/upload/v1535740297/luca-bravo-217276-unsplash.jpg
header:
    layout: hero
    vh: vh-50 vh-50-m vh-80-l
    navigation: fancy
    image: https://res.cloudinary.com/wpcloudserver/image/upload/v1535740297/luca-bravo-217276-unsplash.jpg
    caption: "[Luca Bravo](https://unsplash.com/@lucabravo)"
    class: bg-darken-20 bg-darken-25-l
author_bio: true
categories:
    - Ubuntu 16.04
    - Tutorial
tags:
    - Git
    - Initial Server
    - Digitalocean
---
Ketika Anda pertama kali membuat server Ubuntu 16.04 baru, ada beberapa langkah konfigurasi yang harus Anda ambil sejak awal sebagai bagian dari pengaturan dasar. Ini akan meningkatkan keamanan dan kegunaan server Anda dan akan memberi Anda dasar yang kuat untuk tindakan selanjutnya.

### Langkah Satu - Login Root

Untuk masuk ke server Anda, Anda harus mengetahui alamat IP publik server Anda. Anda juga akan memerlukan kata sandi atau, jika Anda menginstal kunci SSH untuk otentikasi, kunci pribadi untuk akun pengguna "root". Jika Anda belum masuk ke server Anda, Anda mungkin ingin mengikuti tutorial pertama dalam seri ini, Cara Menghubungkan ke Tetesan Anda dengan SSH, yang mencakup proses ini secara detail.

Jika Anda belum terhubung ke server Anda, lanjutkan dan masuk sebagai pengguna root menggunakan perintah berikut (ganti kata yang disorot dengan alamat IP publik server Anda):

```bash
ssh root@your_server_ip
```
Selesaikan proses masuk dengan menerima peringatan tentang autentisitas host, jika muncul, kemudian berikan otentikasi root Anda (kata sandi atau kunci pribadi). Jika ini adalah pertama kalinya Anda masuk ke server dengan kata sandi, Anda juga akan diminta untuk mengubah kata sandi root.

### Tentang Root

Pengguna root adalah pengguna administratif di lingkungan Linux yang memiliki hak yang sangat luas. Karena hak istimewa yang tinggi dari akun root, Anda sebenarnya tidak disarankan menggunakannya secara teratur. Ini karena bagian dari kekuatan yang melekat pada akun root adalah kemampuan untuk membuat perubahan yang sangat merusak, bahkan karena kecelakaan.

Langkah selanjutnya adalah membuat akun pengguna alternatif dengan lingkup pengaruh yang berkurang untuk pekerjaan sehari-hari. Kami akan mengajari Anda cara mendapatkan peningkatan hak istimewa pada saat Anda membutuhkannya.

### Langkah Dua - Buat Pengguna Baru

Setelah Anda masuk sebagai root, kami siap menambahkan akun pengguna baru yang akan kami gunakan untuk masuk dari sekarang.

Contoh ini membuat pengguna baru bernama `asep`, tetapi Anda harus menggantinya dengan nama pengguna yang Anda sukai:

```bash
adduser asep
```
Anda akan ditanya beberapa pertanyaan, dimulai dengan kata sandi akun.

Masukkan kata sandi yang kuat dan, secara opsional, isi salah satu informasi tambahan jika Anda mau. Ini tidak diperlukan dan Anda cukup menekan `ENTER` di bidang apa pun yang ingin Anda lewati.

### Langkah Tiga - Hak Istimewa Root

Sekarang, kami memiliki akun pengguna baru dengan hak istimewa akun reguler. Namun, kadang-kadang kita perlu melakukan tugas-tugas administratif.

Untuk menghindari keluar dari pengguna normal dan masuk kembali sebagai akun root, kita dapat mengatur apa yang dikenal sebagai "superuser" atau hak akses root untuk akun normal kita. Ini akan memungkinkan pengguna normal kami untuk menjalankan perintah dengan hak akses administratif dengan meletakkan kata `sudo` di depan setiap perintah.

Untuk menambahkan hak istimewa ini ke pengguna baru kami, kami perlu menambahkan pengguna baru ke grup `sudo`. Secara default, pada Ubuntu 16.04, pengguna yang termasuk dalam grup `sudo` diizinkan untuk menggunakan perintah `sudo`.

Sebagai root, jalankan perintah ini untuk menambahkan pengguna baru Anda ke grup `sudo` (gantikan kata yang disorot dengan pengguna baru Anda):

```bash
usermod -aG sudo asep
```

Sekarang pengguna Anda dapat menjalankan perintah dengan hak akses superuser! Untuk informasi lebih lanjut tentang cara kerjanya, lihat tutorial sudoers ini.

Jika Anda ingin meningkatkan keamanan server Anda, ikuti langkah-langkah selanjutnya dalam tutorial ini.

### Langkah Empat - Tambahkan Otentikasi Kunci Publik (Disarankan)

Langkah selanjutnya dalam mengamankan server Anda adalah mengatur otentikasi kunci publik untuk pengguna baru Anda. Pengaturan ini akan meningkatkan keamanan server Anda dengan meminta kunci SSH pribadi untuk masuk.

Hasilkan Pasangan Kunci
Jika Anda belum memiliki pasangan kunci SSH, yang terdiri dari kunci publik dan pribadi, Anda perlu membuatnya. Jika Anda sudah memiliki kunci yang ingin Anda gunakan, lewati ke Menyalin langkah Kunci Publik.

Untuk menghasilkan pasangan kunci baru, masukkan perintah berikut di terminal mesin lokal Anda (mis. Komputer Anda):

```bash
ssh-keygen
```
Dengan asumsi pengguna lokal Anda disebut "localuser", Anda akan melihat output yang terlihat seperti berikut:

```bash
ssh-keygen output
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/localuser/.ssh/id_rsa):
```
Hit kembali untuk menerima nama dan jalur file ini (atau masukkan nama baru).

Selanjutnya, Anda akan dimintai frasa sandi untuk mengamankan kunci. Anda dapat memasukkan passphrase atau membiarkan passphrase kosong.

#### Catatan:
Jika Anda membiarkan passphrase kosong, Anda akan dapat menggunakan kunci privat untuk autentikasi tanpa memasukkan frasa sandi. Jika Anda memasukkan kata sandi, Anda akan memerlukan kunci pribadi dan frasa sandi untuk masuk. Mengamankan kunci Anda dengan frasa sandi lebih aman, tetapi kedua metode tersebut memiliki kegunaannya dan lebih aman daripada otentikasi kata sandi dasar.

Ini menghasilkan kunci privat, id_rsa, dan kunci publik, id_rsa.pub, dalam direktori .ssh dari direktori home localerer. Ingat bahwa kunci pribadi tidak boleh dibagikan dengan siapa pun yang seharusnya tidak memiliki akses ke server Anda!

### Salin Kunci Publik

> Setelah menghasilkan pasangan kunci SSH, Anda akan ingin menyalin kunci publik Anda ke server baru Anda. Kami akan membahas dua cara mudah untuk melakukan ini.


#### Catatan:
Metode ssh-copy-id tidak akan berfungsi pada DigitalOcean jika kunci SSH dipilih selama pembuatan Droplet. Ini karena DigitalOcean menonaktifkan otentikasi kata sandi jika ada kunci SSH, dan ssh-copy-id bergantung pada otentikasi kata sandi untuk menyalin kunci.
Jika Anda menggunakan DigitalOcean dan memilih kunci SSH selama pembuatan Tetesan, gunakan opsi 2 sebagai gantinya.
{: .alert .alert--warning}

#### Opsi 1: Gunakan ssh-copy-id

Jika mesin lokal Anda memiliki skrip ssh-copy-id diinstal, Anda dapat menggunakannya untuk menginstal kunci publik Anda untuk setiap pengguna yang Anda memiliki kredensial login.

Jalankan skrip ssh-copy-id dengan menentukan alamat IP pengguna dan server yang ingin Anda instal kuncinya, seperti ini:

```bash
ssh-copy-id sammy@your_server_ip
```

Setelah memberikan kata sandi Anda di prompt, kunci publik Anda akan ditambahkan ke file remote pengguna .ssh / authorized_keys. Kunci pribadi terkait sekarang dapat digunakan untuk masuk ke server.

#### Opsi 2: Instal Kunci Secara Manual

Dengan asumsi Anda menghasilkan pasangan kunci SSH menggunakan langkah sebelumnya, gunakan perintah berikut di terminal mesin lokal Anda untuk mencetak kunci publik Anda (id_rsa.pub):

```bash
cat ~/.ssh/id_rsa.pub
```
Ini harus mencetak kunci SSH publik Anda, yang seharusnya terlihat seperti berikut:

```bash
id_rsa.pub contents
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDBGTO0tsVejssuaYR5R3Y/i73SppJAhme1dH7W2c47d4gOqB4izP0+fRLfvbz/tnXFz4iOP/H6eCV05hqUhF+KYRxt9Y8tVMrpDZR2l75o6+xSbUOMu6xN+uVF0T9XzKcxmzTmnV7Na5up3QM3DoSRYX/EP3utr2+zAqpJIfKPLdA74w7g56oYWI9blpnpzxkEd3edVJOivUkpZ4JoenWManvIaSdMTJXMy3MtlQhva+j9CgguyVbUkdzK9KKEuah+pFZvaugtebsU+bllPTB0nlXGIJk98Ie9ZtxuY3nCKneB+KjKiXrAvXUPCI9mWkYS/1rggpFmu3HbXBnWSUdf localuser@machine.local
```
Pilih kunci publik, dan salin ke clipboard Anda.

Untuk mengaktifkan penggunaan kunci SSH untuk mengotentikasi sebagai pengguna jarak jauh baru, Anda harus menambahkan kunci publik ke file khusus di direktori home pengguna.

Di server, sebagai pengguna root, masukkan perintah berikut untuk beralih sementara ke pengguna baru (gantikan nama pengguna Anda sendiri):

```bash
su - asep
```
Sekarang Anda akan berada di direktori home pengguna baru Anda.

Buat direktori baru bernama `.ssh` dan batasi izinnya dengan perintah berikut:

```
mkdir ~/.ssh
chmod 700 ~/.ssh
```
Sekarang buka file di `.ssh` disebut `authorized_keys` dengan editor teks. Kami akan menggunakan nano untuk mengedit file:

```bash
nano ~/.ssh/authorized_keys
```
Sekarang masukkan kunci publik Anda (yang seharusnya ada di clipboard Anda) dengan menempelkannya ke editor.

Tekan `CTRL-x` untuk keluar dari file, lalu `y` untuk menyimpan perubahan yang Anda buat, lalu `ENTER` untuk mengkonfirmasi nama file.

Sekarang batasi izin dari file `authorized_keys` dengan perintah ini:

```bash
chmod 600 ~/.ssh/authorized_keys
```
