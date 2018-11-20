---
layout: post
title: Ngeblog dengan Jekyll di Netlify Gratis dan Ngebut
date: 2018-09-01 23:49 +0700
author: Iman Sugirman
image: https://res.cloudinary.com/imans/image/upload/v1535823550/blog/maik-jonietz-535261-unsplash.jpg
header:
    layout: hero
    vh: vh-50 vh-50-m vh-80-l
    navigation: fancy
    image: https://res.cloudinary.com/imans/image/upload/v1535823550/blog/maik-jonietz-535261-unsplash.jpg
    caption: "[Maik Jonietz](https://unsplash.com/@der_maik_)"
    class: bg-darken-20 bg-darken-25-l
author_bio: true
categories:
    - CMS
    - Static Site
tags:
    - Blog Platform
    - Jekyll
    - Ruby
---
Tidak perlu WordPress. Jekyll adalah CMS dengan Konsep Static Generator yang ditulis dengan Markdown dan Netlify adalah platform Hosting terbaik saat ini :clap:. Jekyll sendiri dibangun dengan pemrograman [Ruby](/tag/ruby/).

Bagi orang yang tidak terbiasa dengan topik ini, Static Site Generator adalah konsep yang sedang tren untuk membuat situs web yang tidak rumit tanpa memerlukan platform hosting basis data, yang membuat pembuatan situs web baru jauh lebih sederhana. `Jekyll` adalah Static Site Generator yang paling populer sehingga saya akan fokus di sini.

Oke kita mulai saja untuk *Ngeblog* pastikan anda Sudah Menginstall Homebrew dan Ruby di MacOs anda. Jika belum bisa ikutin langkah dibawah ini :

* Install Homebrew di macOS [Tutorial Install Homebrew](/tutorial-install-homebrew-di-mac-osx/) :punch:
* Install Ruby dengan rbenv di macOS [Tutorial Install Ruby on Rails](/install-ruby-on-rails-di-mac-dengan-rbenv/) :hand:


### Langkah 1 : Instal Jekyll di Local

Install Gems Jekyll dan Bundler, Ikuti panduannya dengan memasukan perintah di terminal / CLI favorit kamu:

```bash
gem install jekyll bundler
```
Setelah terinstall Gems Jekyll dan Bundler sekarang kita bisa buat website / blog dengan memasukan perintah :

```bash
jekyll new blogaing
```
dan sekarang kita arahkan ke folder path project dari Jekyll yang kita buat :

```bash
cd blogaing
```
Setelah berada di folder path project, kamu bisa buka Text Editor favorit kamu entah itu Sublime atau Atom atau lainnya. dan untuk menjalankan Jekyll di Localhost bisa memasukan perintah :

```bash
bundle exec jekyll serve
```
Buka browser dan masukan url : `localhost:4000`

### Langkah 2: Buat Repositori baru di GitHub, Gitlab atau Bitbucket

Buat akun di Github, Gitlab atau Code Hosting lainnya. sebagai penyimpanan blog / website Jekyll kamu yang baru dibuat ke dalam branch master. Jika Anda ingin menyimpan repositori pribadi Anda secara gratis, Anda dapat menggunakan BitBucket atau juga Gitlab.

Untuk Lebih jelas mengenai Tutorial Membuat Repositori di Github bisa ikuti tutorial ini

Deploy Website / Blog Jekyll kamu ke Git Hosting favorit kamu disini saya asumsikan kamu memakai Github dan kamu harus mengotentikasikan nantinya dengan akun Netlify kamu.

### Langkah 3: Buat Akun gratis di Netlify
![Daftar Akun Netlify](https://res.cloudinary.com/imans/image/upload/v1535822233/blog/Jepretan_Layar_2018-09-02_pada_00.14.50.png)

Daftar ke Netlify dengan mengunjungi tautan ini [Daftar Akun Netlify](https://app.netlify.com/signup). Gunakan akun platform `git` pilihan Anda untuk otentikasi. dan Otentikasikan Github, Gitlab atau Bitbucket kamu dengan Netlify

[Daftar Akun Netlify](https://app.netlify.com/signup){: .btn .btn-outline--success .btn--small}

### Langkah 4: Tambahkan situs baru di Netlify
Untuk melakukan ini, gunakan tombol yang tepat pada dashboard Netlify.

![Netlify Dashboard](https://res.cloudinary.com/imans/image/upload/v1535822382/blog/Jepretan_Layar_2018-09-02_pada_00.19.31.png)

Dalam wizard tiga langkah, Kamu akan diminta untuk memilih penyedia Git antara GitHub, BitBucket atau GitLab.Setelah Kamu memilih opsi yang tepat, Kamu akan dipindahkan ke layar kedua yang, jika Kamu mengesahkan aplikasi Netlify dengan benar, harus mencantumkan semua repositori git Kamu.

Pilih yang Kamu buat untuk situs `Jekyll`. Di layar ketiga, biarkan semua pengaturan build kosong. Ini akan ditemukan secara otomatis. Pastikan bahwa `branch master` dipilih sebagai branch untuk diterapkan. Klik tombol `Deploy Site` untuk melakukan Deploy pertama situs Kamu.

Setelah berhasil, Kamu akan tiba di halaman ringkasan situs web. Seperti yang Kamu lihat, semua pengaturan penyebaran telah diatur secara otomatis:

### Langkah 5: Konfigurasi alamat situs

Situs Jekyll anda sekarang sudah ada di Host Netlify di bawah subdomain random seperti `https://something-random-40716.netlify.com`. Kamu dapat mengubah subdomain ini menjadi nama lain atau apapun yang kamu inginkan. Pastikan Kamu memasukkan nilai yang sama ke `variabel url` di `_config.yml` file proyek Jekyll Kamu. Jika tidak, url halaman dan navigasi mungkin tidak berfungsi dengan benar:

### Langkah 6: Menambahkan Domain anda Sendiri

Mengubah Domain dari `https://something-random-40716.netlify.com` ke domain yang sudah anda beli misal di Namecheap atau Godaddy sangatlah mudah. Ikuti Langkah ini :

* Klik Domain Setting
* Di Custom Domain Klik Add Domain Alias masukan domain kamu dan klik `Save`

Di Domain Manager Godaddy atau Namecheap arahkan Name Server kamu ke point netlify DNS. Kurang lebih seperti ini :

```
dns1.p02.nsone.net
dns2.p02.nsone.net
dns3.p02.nsone.net
dns4.p02.nsone.net
```
dan tunggu beberapa saat atau bisa memakan waktu 1x24 jam. sampai akhirnya kamu bisa akses Jekyll Website / blog kamu ke domain yang sudah kamu beli.

### Kesimpulan
Netlify adalah Salah satu Hosting untuk Static Site Generator, ada banyak pilihan lain diantaranya adalah [Now](https://zeit.co/now) atau [Surge](https://surge.sh) dan [Github Page](https://pages.github.com/) itu adalah beberapa referensi dari saya ketika kamu ingin Ngeblog Gratis dengan Static Site Generator. Kamu juga bisa memanfaatkan AWS Amazon S3 untuk Deploy Static Site Generator atau bahkan bisa menggunakan Cloud Server Digitalocean dengan Gratis $10 untuk pendaftar pertama dengan menggunakan Link ini [Daftar Digitalocean Dapat $10](https://m.do.co/c/70b5b463c03f) :moneybag:
