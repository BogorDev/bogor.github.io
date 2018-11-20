---
layout: post
title: Install Ruby on Rails di Mac Dengan rbenv
subtitle: Tutorial Beginner Install di Mac OS
date: 2018-08-28 17:01 +0700
author: Iman Sugirman
image: https://res.cloudinary.com/wpcloudserver/image/upload/v1535741043/fabian-grohs-653254-unsplash.jpg
author_bio: true
categories:
    - macOS
    - Install
tags:
    - Ruby on Rails
    - Ruby
---
Sekarang kita telah menginstal Homebrew, kita dapat menggunakannya untuk menginstal Ruby. Kami akan menggunakan rbenv untuk menginstal dan mengelola versi Ruby. :hocho:

### Introduction
Kita akan menyiapkan untuk penginstallan Framework `Ruby on Rails` pada macOS High Sierra. Versi lama OS X sebagian besar kompatibel jadi ikuti sejauh yang Anda bisa dan kemudian `cari Google` untuk setiap masalah yang Anda hadapi. Ada banyak orang yang telah mendokumentasikan solusi untuk mereka.

### Install Homebrew

Pertama, kita perlu menginstal `Homebrew`. `Homebrew` memungkinkan kita untuk menginstal dan menyusun paket perangkat lunak dengan mudah dari sumber. Homebrew hadir dengan skrip pemasangan yang sangat sederhana. Ketika meminta Anda menginstal XCode CommandLine Tools, katakan ya.

Untuk Lebih Lengkap Mengenai Homebrew silahkan kunjungi [Install Homebrew di macOS]('https://imen.icu/tutorial-install-homebrew-di-mac-osx')

Buka Terminal dan jalankan perintah berikut:

``` bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Sekarang kita telah menginstal Homebrew, kita dapat menggunakannya untuk menginstal Ruby. Kami akan menggunakan rbenv untuk menginstal dan mengelola versi Ruby.

Untuk melakukan ini, jalankan perintah berikut di Terminal Anda:

``` bash
brew install rbenv ruby-build

# Add rbenv to bash so that it loads every time you open a terminal
echo 'if which rbenv > /dev/null; then eval "$(rbenv init -)"; fi' >> ~/.bash_profile
source ~/.bash_profile

# Install Ruby
rbenv install 2.5.1
rbenv global 2.5.1
ruby -v
```

### Mengkonfigurasi Git
Kami akan menggunakan Git untuk sistem kontrol versi kami sehingga kami akan menyiapkannya agar sesuai dengan akun Github kami. Jika Anda belum memiliki akun [Github]('https://github.com'), pastikan untuk mendaftar. Ini akan berguna untuk masa depan.

Ganti nama contoh dan alamat email dengan langkah-langkah berikut dengan yang Anda gunakan untuk akun Github Anda.

``` bash
git config --global color.ui true
git config --global user.name "YOUR NAME"
git config --global user.email "YOUR@EMAIL.com"
ssh-keygen -t rsa -C "YOUR@EMAIL.com"
```
Langkah selanjutnya adalah mengambil kunci SSH yang baru dibuat dan menambahkannya ke akun Github Anda. Anda ingin menyalin dan menempelkan output dari perintah berikut dan menempelkannya di sini.

``` bash
cat ~/.ssh/id_rsa.pub
```
Setelah Anda selesai melakukannya, Anda dapat memeriksa dan melihat apakah berhasil:

``` bash
ssh -T git@github.com
```
Anda harus mendapatkan pesan seperti ini:

``` bash
Hi Nama Pengguna! Youve successfully authenticated, but GitHub does not provide shell access.
```
### Installasi Ruby on Rails
Menginstall `Rails` sama mudahnya dengan menjalankan perintah berikut di Terminal Anda:

``` bash
gem install rails -v 5.2.0
```
Rails sekarang diinstal, tetapi agar kami dapat menggunakan `rails` yang dapat dieksekusi, kami perlu memberi tahu `rbenv` untuk melihatnya:

``` bash
rbenv rehash
```
Dan sekarang kami dapat memverifikasi Rails diinstal:
``` bash
rails -v
# Rails 5.2.0
```

### Menginstall Database
Kami akan menginstal `sqlite3` dari homebrew karena kami tidak dapat menggunakan versi built-in dengan macOS Sierra tanpa mengalami masalah.

``` bash
brew install sqlite3
```

Rails mengirim dengan sqlite3 sebagai basis data default. Kemungkinan Anda tidak ingin menggunakannya karena disimpan sebagai file sederhana pada disk. Anda mungkin menginginkan sesuatu yang lebih kuat seperti MySQL atau PostgreSQL.

Ada banyak dokumentasi untuk keduanya, jadi Anda bisa memilih salah satu yang sepertinya akan lebih nyaman bagi Anda. Jika Anda berasal dari PHP, Anda mungkin sudah akrab dengan MySQL. Jika Anda baru mengenal database, saya sarankan untuk beralih ke pengaturan PostgreSQL.

### MySQL
Anda dapat menginstal server `MySQL` dan klien dari Homebrew:
```
brew install mysql
```
Setelah perintah ini selesai, ini memberi Anda beberapa perintah untuk dijalankan. Ikuti instruksi dan jalankan mereka:

```
# Untuk memulai dan memulai mysql saat login:
brew services start mysql
```
