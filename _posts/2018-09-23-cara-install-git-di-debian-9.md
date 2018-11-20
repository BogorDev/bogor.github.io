---
layout: post
title: Cara Install Git di Debian 9
date: 2018-09-23 19:56 +0700
author: Iman Sugirman
cover: /assets/images/install-git-debian-9.png
image: https://res.cloudinary.com/imans/image/upload/v1537742970/blog/zany-jadraque-571205-unsplash.jpg
header:
    layout: hero
    vh: vh-50 vh-50-m vh-80-l
    navigation: fancy
    image: https://res.cloudinary.com/imans/image/upload/v1537742970/blog/zany-jadraque-571205-unsplash.jpg
    caption: "[Zany Jadraque](https://unsplash.com/@jenrielzany)"
    class: bg-darken-20 bg-darken-25-l
author_bio: true
categories:
    - Tutorial
    - Git
    - Debian
tags:
    - Github
    - SSH
---
Sistem kontrol versi perangkat lunak memungkinkan Anda untuk melacak perangkat lunak Anda di tingkat sumber. Dengan alat versi, Anda dapat melacak perubahan, kembali ke tahapan sebelumnya, dan cabang untuk membuat versi.

Git adalah salah satu sistem kontrol versi paling populer yang tersedia saat ini. Banyak file proyek dikelola di repositori Git, dan situs seperti GitHub, GitLab, dan Bitbucket membantu memfasilitasi berbagi proyek pengembangan perangkat lunak dan kolaborasi.

Dalam tutorial ini, kami akan memasang dan mengonfigurasi Git di server Debian 9. Kami akan membahas cara memasang perangkat lunak dalam dua cara berbeda, masing-masing memiliki manfaat sendiri tergantung pada kebutuhan khusus Anda.

Prasyarat
Untuk menyelesaikan tutorial ini, Anda harus memiliki pengguna non-root dengan hak akses sudo pada server Debian 9. Untuk mempelajari cara mencapai pengaturan ini, ikuti panduan [Penyiapan Awal Server Debian 9](/pengaturan-awal-server-dengan-debian-9) kami.

Dengan pengaturan server dan pengguna Anda, Anda siap untuk memulai.

### Install Git dengan Paket Default

Repositori default Debian memberi Anda metode cepat untuk menginstal `Git`. Perhatikan bahwa versi yang Anda instal melalui repositori ini mungkin lebih lama dari versi terbaru yang tersedia saat ini. Jika Anda memerlukan rilis terbaru, pertimbangkan pindah ke bagian selanjutnya dari tutorial ini untuk mempelajari cara menginstal dan mengkompilasi `Git` dari sumber.

Pertama, gunakan alat manajemen paket yang tepat untuk memperbarui indeks paket lokal Anda. Dengan pembaruan selesai, Anda dapat mengunduh dan menginstal Git:
```bash
sudo apt update
sudo apt install git
```
Anda dapat mengkonfirmasi bahwa Anda telah menginstal Git dengan benar dengan menjalankan perintah berikut:
```bash
git --version
```
Outputnya :
```
git version 2.11.0
```

### Setting Up Git
Setelah Anda menginstal Git, Anda harus mengkonfigurasinya sehingga pesan commit yang dihasilkan akan berisi informasi Anda yang benar.

Ini dapat dicapai dengan menggunakan perintah git config. Khususnya, kami perlu memberikan nama dan alamat email kami karena Git menyematkan informasi ini ke setiap komitmen yang kami lakukan. Kita dapat melanjutkan dan menambahkan informasi ini dengan mengetik:
```bash
git config --global user.name "Asep"
git config --global user.email "asep@domain.com"
```
Kita bisa melihat semua item konfigurasi yang telah diatur dengan mengetik:
```bash
git config --list
```
Outputnya :
```
user.name=Sammy
user.email=sammy@domain.com
```
Informasi yang Anda masukkan disimpan di file konfigurasi Git Anda, yang Anda dapat mengeditnya secara opsional dengan editor teks seperti ini:
```bash
nano ~/.gitconfig
```
Edit Git Config :
```bash
[user]
  name = Sammy
  email = sammy@domain.com
```
Ada banyak opsi lain yang dapat Anda atur, tetapi ini adalah dua hal penting yang diperlukan. Jika Anda melewati langkah ini, Anda mungkin akan melihat peringatan ketika Anda berkomitmen untuk Git. Ini membuat lebih banyak pekerjaan untuk Anda karena Anda akan harus merevisi komit yang telah Anda lakukan dengan informasi yang dikoreksi.
