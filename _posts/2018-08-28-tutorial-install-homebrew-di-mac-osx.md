---
layout: post
title: Tutorial Install Homebrew di Mac OSX
date: 2018-08-28 17:22 +0700
author: Iman Sugirman
image: https://res.cloudinary.com/wpcloudserver/image/upload/v1535740168/aron-799872-unsplash.jpg
header:
    layout: hero
    vh: vh-50 vh-50-m vh-80-l
    navigation: fancy
    image: https://res.cloudinary.com/wpcloudserver/image/upload/v1535740168/aron-799872-unsplash.jpg
    caption: "[Aron](https://unsplash.com/@aronunsplash)"
author_bio: true
categories:
    - MacOs
    - Tutorial
tags:
    - Homebrew
    - Xcode
---
Pengguna Mac tingkat lanjut mungkin menghargai menggunakan manajer paket Homebrew, yang sangat menyederhanakan proses pemasangan perangkat lunak dan alat baris perintah pada Mac

I give this plugin two :basecamp:!
:cat:s :metal: :rocket:


#### Hello Bro :computer:

Misalnya, jika Anda ingin mudah menginstal alat baris perintah favorit di Mac seperti tong kecil, htop, wget, nmap, pohon, irssi, tautan, colordiff, atau hampir semua utilitas baris perintah unix lain yang dikenal, Anda dapat melakukannya dengan mudah perintah. Homebrew mengunduh dan membuat paket untuk Anda.

Ini jelas ditujukan untuk pengguna Mac yang lebih teknis dan cerdas yang menghabiskan banyak waktu di baris perintah. Meskipun tidak ada masalah khusus untuk pengguna pemula yang memasang Homebrew di Mac mereka, kemungkinan pemula merasa berguna adalah tipis, kecuali mereka bermaksud untuk mempelajari lingkungan baris perintah. Bandingkan itu untuk memberi kekuatan kepada pengguna yang praktis tinggal di lingkungan terminal, apakah pengguna Mac lama atau bermigrasi ke platform dari dunia Windows atau Linux, yang akan segera melihat nilai Homebrew.

### Persyaratan untuk Memasang Homebrew di Mac OS
Prasyarat untuk menginstal Homebrew pada Mac termasuk yang berikut:

  * Mac menjalankan Mac OS X 10.10 atau lebih baru, meskipun versi sebelumnya didukung
  * Menggunakan `Command Line Tools` Alat Baris Perintah harus diinstal pada Mac (baik secara mandiri atau melalui Xcode)
  * Pengetahuan tentang baris perintah dan menggunakan `bash` atau `zsh`

### Cara Memasang Homebrew di Mac OS
Cara termudah untuk menginstal Homebrew adalah melalui ruby dan curl, diselesaikan dengan satu perintah. Pendekatan ini sama untuk menginstal Homebrew di semua versi Mac OS dan Mac OS X yang didukung.

  * Buka aplikasi "Terminal", yang ditemukan di / Aplikasi / Utilitas

  * Masukkan perintah berikut ke dalam satu baris terminal:

  * Tekan Return dan Anda akan melihat serangkaian baris tentang apa yang akan dipasang skrip dan di mana, tekan Kembali lagi untuk menyetujui atau tekan Escape untuk membatalkan

  * Masukkan kata sandi administrator (diperlukan oleh eksekusi `sudo`) untuk memulai instalasi

  * Pemasangan Homebrew akan memakan waktu cukup lama tergantung pada kecepatan koneksi Mac dan internet Anda, karena setiap paket yang diperlukan diunduh dan dipasang oleh skrip.

Sekarang Anda siap memasang paket perangkat lunak melalui Homebrew, atau Anda dapat membaca dokumentasi bantuan dengan perintah berikut:

Mengingstall Homebrew :

Masukan Perintah Pada Terminal Mac anda :

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
Lalu, buka bantuan dengan :

```bash
brew help
```

### Menginstal Paket Perangkat Lunak melalui Homebrew di Mac
Instalasi paket dengan Homebrew sangat mudah, cukup gunakan sintaks berikut:
``` bash
brew install [package name]
```
Misalnya, untuk menginstal `wget` melalui Homebrew Anda bisa menggunakan sintaks berikut:

``` bash
brew install wget
```
Sederhana, mudah. Setelah selesai Anda dapat menjalankan wget seperti biasa.

### Kesimpulan

Homebrew bukan satu-satunya cara untuk menginstal perangkat lunak baris perintah, Anda dapat menginstal alat baris perintah pada Mac sendiri dan kemudian mengkompilasi dan membuat perangkat lunak secara mandiri.

Sebagai contoh, kami mendiskusikan menginstal wget di Mac OS tanpa Homebrew di sini dan ini menggunakan konfigurasi dan proses yang umum. Tidak ada yang salah dengan pendekatan itu (dan boleh dibilang mungkin lebih baik bagi pengguna yang menginginkan paket terbatas dan jejak yang lebih ramping) tetapi jika Anda terbiasa dengan manajer paket seperti dpkg, apt-get, atau rpm Anda hampir pasti akan menghargai dan lebih suka menggunakan `Homebrew`.

### Referensi

  * Website Resmi [Homebrew](https://brew.sh)
  * Repository Github [Github](https://github.com/Homebrew)
