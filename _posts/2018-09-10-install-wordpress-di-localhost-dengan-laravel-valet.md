---
layout: post
title: Install Wordpress di Localhost dengan Laravel Valet
date: 2018-09-10 04:37 +0700
author: Iman Sugirman
image: https://res.cloudinary.com/imans/image/upload/v1536518274/blog/charles-deluvio-456501-unsplash.jpg
header:
    layout: hero
    vh: vh-50 vh-50-m vh-80-l
    navigation: fancy
    image: https://res.cloudinary.com/imans/image/upload/v1536518274/blog/charles-deluvio-456501-unsplash.jpg
    caption: "[Charles Deluvio](https://unsplash.com/@charlesdeluvio)"
    class: bg-darken-20 bg-darken-25-l
author_bio: true
categories:
    - Tutorial
    - Wordpress
    - macOs
tags:
    - CMS
    - Laravel
    - Valet
---
Valet adalah lingkungan pengembangan Laravel untuk Mac minimalis. Tidak ada file `Vagrant`, no `/etc/hosts`. Kamu bahkan dapat membagikan situs Kamu secara publik menggunakan `tunnel` lokal. Ya, kami juga menyukainya.

Laravel Valet mengkonfigurasi Mac Kamu untuk selalu menjalankan `Nginx` di latar belakang saat mesin Anda mulai. Kemudian, dengan menggunakan `DnsMasq`, Valet mem-proxy semua permintaan pada domain `*.test` untuk menunjuk ke situs yang diinstal pada komputer lokal kamu...iya kamu... :heart_eyes:

Dengan kata lain, lingkungan pengembangan Laravel yang sangat cepat yang menggunakan sekitar 7 MB RAM. Valet bukan pengganti lengkap untuk `Vagrant` atau `Homestead`, tetapi menyediakan alternatif yang bagus jika Anda ingin dasar-dasar yang fleksibel, lebih suka kecepatan ekstrim, atau bekerja pada mesin dengan jumlah RAM terbatas.

### Mari Kita mulai
Untuk menginstall Laravel Valet butuh beberapa yang harus kamu install diantaranya, PHP, Nginx, Mysql, dan Composer. Semua itu bisa diinstall dengan Homebrew. Karena Valet hanya bisa digunakan di Mac :

Beberapa prasyarat untuk menginstall Valet dibutuhkan instalasi Homebrew dan kamu bisa ikuti petunjuk instalasi disini : [Install Homebrew di Mac](/tutorial-install-homebrew-di-mac-osx)

```bash
brew update
brew install php@7.2
brew install mysql
brew install composer
```
Install Valet dengan Composer :
```bash
composer global require laravel/valet
```
Install untuk pertama kali Laravel Valet dengan cara :
```bash
valet install
```
Untuk membuat folder project kamu, mari kita sepakati jika kita buat folder di path user kita di `Sites` dan buat folder dengan lokasi `~/Users/asep/Sites` asumsikan asep adalah user Mac anda. Sekarang kamu ke folder tersebut dengan terminal dengan cara masukan command :
```bash
cd ~/Users/nakamuraagatha/Sites
```
Untuk men-setup folder tersebut lokasi Valet bisa menggunakan :
```bash
valet park
valet start
```
Jika kamu mau menggunakan extensi domain `.dev` bisa juga digunakan perintah :
```bash
valet domain dev  
```
Tujuannya agar lebih mudah ketika ingin melihat domain Development kamu. Jadi nanti ketika kamu ingin membuat project development kamu bisa mengunjungi contohnya  `projectwordpress.dev`

### Install WP Cli
Untuk memudahkan kamu bisa juga jadi alternatif buat mempercepat install bisa menggunakan wp-cli, bisa kamu install dengan :
```bash
curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar
```
Test wp-cli.phar dengan :
```bash
php wp-cli.phar --info
```
Untuk menggunakan command `wp` maka kamu harus menempatkan `wp-cli.phar` di bin folder :
```bash
chmod +x wp-cli.phar
sudo mv wp-cli.phar /usr/local/bin/wp
```
Ketik `wp --info` untuk mengetahui status dan versi wp-cli kamu.

### Menginstall dan Konfigurasi
Untuk menginstall Wordpress di Local dengan Laravel Valet :
```bash
cd ~/Users/nakamuraagatha/Sites
```
Buat folder project kamu :
```bash
mkdir -p projectdemo
cd projectdemo
```
Lalu Gunakan wp-cli
```bash
wp core download
```
Dan gunakan command valet dengan :
```bash
valet link
```
Biasanya meminta password mac kamu, lalu agar bisa setup otomatis dengan https silahkan masukan :
```bash
valet secure
```
Dan Saatnya menjalankan Mysql dengan `Homebrew`
```bash
brew install mysql@5.7
brew services start mysql
```
Silahkan kunjungi url project kamu dengan buka chrome dan ketik `projectdemo.dev`. Dan Silahkan sesuaikan database dengan yang kamu buat dengan mysql.

Untuk Selanjutnya akan dibahas mengenai Deploy Wordpress ke Server dengan Git. Silahkan ditunggu atau bisa subscribe Newsletter dengan memasukan email kamu.
