---
layout: post
title: Deploy Aplikasi Laravel Ke Heroku
date: 2018-09-25 08:08 +0700
author: Iman Sugirman
cover: /assets/images/laravel-heroku-deploy.png
image: https://res.cloudinary.com/imans/image/upload/v1537974092/blog/goran-ivos-307271-unsplash.jpg
header:
    layout: hero
    vh: vh-50 vh-50-m vh-80-l
    navigation: fancy
    image: https://res.cloudinary.com/imans/image/upload/v1537974092/blog/goran-ivos-307271-unsplash.jpg
    caption: "[Goran Ivos](https://unsplash.com/@goran_ivos)"
    class: bg-darken-20 bg-darken-25-l
author_bio: true
categories:
    - Tutorial
    - Laravel
    - Heroku
tags:
    - SASS
    - Cloud
    - Postgres
---
Heroku adalah platform mengagumkan, yang awalnya dibangun di atas AWS dan saat ini dimiliki oleh **Salesforce**. Dan Laravel adalah framework web PHP yang kuat untuk membangun aplikasi.

Dalam tutorial singkat ini kita akan menghasilkan aplikasi dari laravel-5-boilerplate dan di Deploy ke Heroku.

Jika Anda lebih suka memiliki aplikasi Laravel 5 generik, bukan boiler besar yang mengikuti inti ini. Tanpa basa-basi lagi, mari kita mulai.

### Install Laravel di Localhost
Untuk Menginstall Pertama Laravel dibutuhkan Composer, ikuti langkah dibawah untuk menginstallnya :
``` bash
php -r "if (hash_file('sha1', 'deployer.phar') === '35e8dcd50cf7186502f603676b972065cb68c129') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('deployer.phar'); } echo PHP_EOL;"
```
Dan Outputnya adalah :

```shell
Installer verified
```
Setelah itu Pindahkan file `composer.phar` ke `/usr/local/bin/composer` dengan menggunakan CLI tentunya :

```bash
mv composer.phar /usr/local/bin/composer
```
#### Install Laravel Installer
Masukan perintah :
```bash
composer global require "laravel/installer"
```
Kalo Kamu sudah menginstall `Laravel Installer` tinggal masukan perintah :
```bash
laravel new laravelblog
```
Namun jika belum menginstall **Laravel Valet**

### Inisialisasi Aplikasi dengan Git

```bash
git init
Initialized empty Git repository in ~/hello_laravel_heroku/.git/
git add .
git commit -m "new laravel project"
[master (root-commit) 6ae139d] new laravel project
 76 files changed, 5458 insertions(+)
...
```
### Deploy Ke Heroku

Untuk menyebarkan aplikasi Anda ke Heroku, Anda harus terlebih dahulu membuat `Procfile`, yang memberi tahu Heroku apa perintah yang digunakan untuk meluncurkan server web dengan pengaturan yang benar. Setelah Anda selesai melakukannya, Anda akan membuat instance aplikasi di Heroku, konfigurasi beberapa variabel lingkungan Laravel, lalu `git push` untuk menerapkan kode Anda!

### Buat Procfile

Secara default, Heroku akan meluncurkan server web Apache bersama dengan PHP untuk melayani aplikasi dari direktori root proyek.

Namun, akar dokumen aplikasi Anda adalah publik / subdirektori, jadi Anda perlu membuat Procfile yang mengonfigurasi akar dokumen yang benar:

```bash
echo web: vendor/bin/heroku-php-apache2 public/ Procfile
git add .
git commit -m "Procfile for Heroku"
[master 1eb2be6] Procfile for Heroku
 1 file changed, 1 insertion(+)
 create mode 100644 Procfile
```
