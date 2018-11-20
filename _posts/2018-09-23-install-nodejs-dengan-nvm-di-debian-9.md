---
layout: post
title: Install NodeJS Dengan NVM di Debian 9
date: 2018-09-23 00:21 +0700
author: Iman Sugirman
cover: /assets/images/nodejs-nvm-debian.png
image: https://res.cloudinary.com/imans/image/upload/v1537637948/blog/jantine-doornbos-398339-unsplash.jpg
header:
    layout: hero
    vh: vh-50 vh-50-m vh-80-l
    navigation: fancy
    image: https://res.cloudinary.com/imans/image/upload/v1537637948/blog/jantine-doornbos-398339-unsplash.jpg
    caption: "[Jantine Doornbos](https://unsplash.com/@jantined)"
    class: bg-darken-20 bg-darken-25-l
author_bio: true
categories:
    - Tutorial
    - NodeJS
    - Debian
tags:
    - Debian 9
    - NVM
---
Node.js adalah platform JavaScript untuk pemrograman tujuan umum yang memungkinkan pengguna untuk membangun aplikasi jaringan dengan cepat. Dengan memanfaatkan JavaScript di bagian depan dan belakang, Node.js membuat pengembangan lebih konsisten dan terintegrasi.

Dalam panduan ini, kami akan menunjukkan kepada Anda bagaimana memulai dengan Node.js pada server Debian 9.

### Prasyarat
Panduan ini mengasumsikan bahwa Anda menggunakan Debian 9. Sebelum memulai, Anda harus memiliki akun pengguna `non-root` dengan hak akses sudo yang diatur pada sistem Anda. Anda dapat mempelajari cara mengaturnya dengan mengikuti [Pengaturan Awal Server Debian 9](/pengaturan-awal-server-dengan-debian-9).

### Install Menggunakan NVM ( Node Version Manager)
Alternatif untuk menginstal Node.js melalui `apt` adalah menggunakan alat yang disebut `nvm`, yang merupakan singkatan dari "Node.js Version Manager". Daripada bekerja pada level sistem operasi, `nvm` bekerja pada level direktori independen dalam direktori home Anda. Ini berarti Anda dapat menginstal beberapa versi mandiri Node.js tanpa mempengaruhi keseluruhan sistem.

Mengontrol lingkungan Anda dengan `nvm` memungkinkan Anda untuk mengakses versi terbaru Node.js dan mempertahankan serta mengelola rilis sebelumnya. Ini adalah utilitas yang berbeda dari apt, namun, dan versi Node.js yang Anda kelola dengannya berbeda dari yang Anda kelola dengan apt.

Untuk mengunduh skrip instalasi `nvm` dari halaman [Proyek Github](https://github.com/creationix/nvm), Anda dapat menggunakan `curl`. Perhatikan bahwa nomor versi dapat berbeda dari yang disorot di sini:
```bash
curl -sL https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh -o install_nvm.sh
```
Periksa skrip pemasangan dengan `nano`:
```bash
nano install_nvm.sh
```
Jalankan skrip dengan `bash`:
```bash
bash install_nvm.sh
```
Ini akan menginstal perangkat lunak ke dalam subdirektori direktori home Anda di `~/.nvm`. Ini juga akan menambahkan baris yang diperlukan ke file `~/.profile` Anda untuk menggunakan file.

Untuk mendapatkan akses ke fungsi nvm, Anda harus keluar dan masuk lagi atau sumber file `~/.profile` sehingga sesi Anda saat ini tahu tentang perubahan:
```bash
source ~/.profile
```
Dengan `nvm` terinstal, Anda dapat menginstal versi Node.js yang terisolasi. Untuk informasi tentang versi Node.js yang tersedia, ketik:
```bash
nvm ls-remote
```
Dan Outputnya :
```
...
         v8.11.1   (Latest LTS: Carbon)
         v9.0.0
         v9.1.0
         v9.2.0
         v9.2.1
         v9.3.0
         v9.4.0
         v9.5.0
         v9.6.0
         v9.6.1
         v9.7.0
         v9.7.1
         v9.8.0
         v9.9.0
        v9.10.0
        v9.10.1
        v9.11.0
        v9.11.1
        v10.0.0  
        v10.1.0
        v10.2.0
        v10.2.1
        v10.3.0
        v10.4.0
        v10.4.1
        v10.5.0
        v10.6.0
        v10.7.0
        v10.8.0
        v10.9.0
```
Seperti yang Anda lihat, versi LTS saat ini pada tulisan ini adalah v8.11.1. Anda dapat menginstalnya dengan mengetik:
```bash
nvm install 8.11.1
```
Biasanya, `nvm` akan beralih menggunakan versi yang paling baru dipasang. Anda dapat memberi tahu `nvm` untuk menggunakan versi yang baru saja Anda unduh dengan mengetik:
```bash
nvm use 8.11.1
```
Ketika Anda menginstal Node.js menggunakan `nvm`, executable disebut `node`. Anda dapat melihat versi yang saat ini digunakan oleh shell dengan mengetik:
```bash
node -v
```
Outputnya :
```
v8.11.1
```
Jika Anda memiliki beberapa versi Node.js, Anda dapat melihat apa yang diinstal dengan mengetik:
```bash
nvm ls
```
Jika Anda ingin default salah satu versi, ketik:
```bash
nvm alias default 8.11.1
```
Versi ini akan dipilih secara otomatis saat sesi baru muncul. Anda juga bisa mereferensikannya dengan alias seperti ini:
```bash
nvm use default
```
Setiap versi Node.js akan melacak paketnya sendiri dan menyediakan `npm` untuk mengelola ini.

Anda juga dapat menginstal paket `npm` ke direktori Node.js `./node_modules`. Gunakan sintaks berikut untuk menginstal modul `express`:
```bash
npm install express
```
Jika Anda ingin menginstal modul secara global, membuatnya tersedia untuk proyek lain menggunakan versi Node.js yang sama, Anda dapat menambahkan `-g` flag:
```bash
npm install -g express
```
Ini akan menginstal paket di:
```
~/.nvm/versions/node/node_version/lib/node_modules/express
```
Menginstal modul secara global akan membiarkan Anda menjalankan perintah dari baris perintah, tetapi Anda harus menautkan paket ke dalam lingkup lokal Anda untuk mewajibkannya dari dalam program:
```bash
npm link express
```
Anda dapat mempelajari lebih lanjut tentang opsi yang tersedia bagi Anda dengan nvm dengan mengetik:
```bash
nvm help
```
