---
layout: post
title: Cara Menginstal Linux, Nginx, MySQL, PHP (LEMP stack) di Ubuntu 16.04
date: 2018-09-16 21:57 +0700
author: Iman Sugirman
image: https://res.cloudinary.com/imans/image/upload/v1537110032/blog/lempstack.png
header:
    layout: hero
    vh: vh-50 vh-50-m vh-80-l
    navigation: fancy
    image: https://res.cloudinary.com/imans/image/upload/v1537110032/blog/lempstack.png
    caption: "[Free Pik](https://www.freepik.com/)"
    class: bg-darken-20 bg-darken-25-l
author_bio: true
categories:
    - Tutorial
    - Ubuntu
    - LEMP
tags:
    - Nginx
    - MySQL
    - PHP
---
Kumpulan perangkat lunak LEMP adalah sekelompok perangkat lunak yang dapat digunakan untuk melayani halaman web dinamis dan aplikasi web. Ini adalah akronim yang menggambarkan sistem operasi Linux, dengan server web Nginx. Data backend disimpan dalam database MySQL dan pemrosesan dinamis ditangani oleh PHP. :hand:

Dalam panduan ini, kami akan menunjukkan cara memasang tumpukan LEMP di server Ubuntu 16.04. Sistem operasi Ubuntu menangani persyaratan pertama. Kami akan menjelaskan bagaimana caranya agar komponen-komponen lainnya bisa hidup dan berjalan.

### Prasyarat
Sebelum Kamu menyelesaikan tutorial ini, Kamu harus memiliki akun pengguna reguler, non-root di server Kamu dengan hak istimewa sudo. Kamu dapat mempelajari cara mengatur jenis akun ini dengan menyelesaikan [Pengaturan Awal Server Ubuntu 16.04](/initial-server-setup-with-ubuntu-16-04).

Setelah Kamu memiliki pengguna Kamu tersedia, masuk ke server Kamu dengan nama pengguna itu. Kamu sekarang siap untuk memulai langkah-langkah yang diuraikan dalam panduan ini.

### Install Web Server Nginx
Untuk menampilkan halaman web ke pengunjung situs kami, kami akan menggunakan Nginx, web server modern yang efisien.

Semua perangkat lunak yang akan kami gunakan untuk prosedur ini akan datang langsung dari repositori paket bawaan Ubuntu. Ini berarti kita dapat menggunakan paket manajemen paket `apt` untuk menyelesaikan instalasi.

Karena ini adalah pertama kalinya kami menggunakan `apt` untuk sesi ini, kami harus memulai dengan memperbarui indeks paket lokal kami. Kami kemudian dapat menginstal server:

```bash
sudo apt-get update
sudo apt-get install nginx
```
Pada Ubuntu 16.04, Nginx dikonfigurasi untuk mulai berjalan pada saat instalasi. :zap:

Jika Anda menjalankan firewall ufw, seperti yang dijelaskan dalam panduan pengaturan awal kami, Anda harus mengizinkan koneksi ke Nginx. Nginx mendaftarkan dirinya dengan ufw setelah instalasi, jadi prosedurnya agak lurus ke depan.

Disarankan agar Anda mengaktifkan profil yang paling terbatas yang akan tetap mengizinkan lalu lintas yang Anda inginkan. Karena kami belum mengonfigurasi SSL untuk server kami, dalam panduan ini, kami hanya perlu mengizinkan lalu lintas pada port `80`.

Anda dapat mengaktifkan ini dengan mengetik:
```bash
sudo ufw allow 'Nginx HTTP'
```
Anda dapat memverifikasi perubahan dengan mengetik:
```bash
sudo ufw status
```
Anda harus melihat lalu lintas HTTP yang diizinkan dalam output yang ditampilkan:
```
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere                  
Nginx HTTP                 ALLOW       Anywhere                  
OpenSSH (v6)               ALLOW       Anywhere (v6)             
Nginx HTTP (v6)            ALLOW       Anywhere (v6)
```
Dengan aturan firewall baru ditambahkan, Anda dapat menguji apakah server sudah aktif dan berjalan dengan mengakses nama domain server Anda atau alamat IP publik di browser web Anda.

Jika Anda tidak memiliki nama domain yang diarahkan ke server Anda dan Anda tidak tahu alamat IP publik server Anda, Anda dapat menemukannya dengan mengetik salah satu dari yang berikut ke terminal Anda:
```bash
ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'
```
Ini akan mencetak beberapa alamat IP. Anda dapat mencoba masing-masing secara bergantian di browser web Anda.

Sebagai alternatif, Anda dapat memeriksa alamat IP mana yang dapat diakses seperti yang dilihat dari lokasi lain di internet:
```bash
curl -4 icanhazip.com
```
Ketik salah satu alamat yang Anda terima di browser web Anda. Ini seharusnya membawa Anda ke halaman landing default Nginx:
```bash
http://server_domain_or_IP
```
![Nginx Default](https://res.cloudinary.com/imans/image/upload/v1537110663/blog/nginx_default.png)

Jika Anda melihat halaman di atas, Anda telah berhasil menginstal Nginx.

### Instal MySQL untuk Mengelola Data Situs

Sekarang kita memiliki server web, kita perlu menginstal MySQL, sistem manajemen basis data, untuk menyimpan dan mengelola data untuk situs kita.

Anda dapat menginstal ini dengan mudah dengan mengetik:
```bash
sudo apt-get install mysql-server
```
Anda akan diminta untuk memberikan kata sandi root (administratif) untuk digunakan dalam sistem MySQL.

Perangkat lunak database MySQL sekarang telah terinstal, tetapi konfigurasinya belum sepenuhnya lengkap.

Untuk mengamankan instalasi, kita dapat menjalankan skrip keamanan sederhana yang akan menanyakan apakah kita ingin memodifikasi beberapa default yang tidak aman. Mulai skrip dengan mengetik:
```bash
mysql_secure_installation
```
Anda akan diminta untuk memasukkan kata sandi yang Anda tetapkan untuk akun root MySQL. Selanjutnya, Anda akan ditanya apakah Anda ingin mengkonfigurasi `VALIDATE PASSWORD PLUGIN`.

Peringatan: Mengaktifkan fitur ini adalah sesuatu dari panggilan penilaian. Jika diaktifkan, kata sandi yang tidak sesuai dengan kriteria yang ditentukan akan ditolak oleh MySQL dengan kesalahan. Ini akan menyebabkan masalah jika Anda menggunakan kata sandi yang lemah dalam hubungannya dengan perangkat lunak yang secara otomatis mengkonfigurasi kredensial pengguna MySQL, seperti paket Ubuntu untuk phpMyAdmin. Aman untuk membiarkan validasi dinonaktifkan, tetapi Anda harus selalu menggunakan kata sandi yang kuat dan unik untuk kredensial database.
{: .alert .alert--danger}

Jawablah y untuk ya, atau apa pun untuk melanjutkan tanpa mengaktifkan.
```
VALIDATE PASSWORD PLUGIN can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD plugin?

Press y|Y for Yes, any other key for No:
```
Jika Anda telah mengaktifkan validasi, Anda akan diminta untuk memilih tingkat validasi kata sandi. Perlu diingat bahwa jika Anda memasukkan 2, untuk level terkuat, Anda akan menerima kesalahan saat mencoba mengatur kata sandi apa pun yang tidak mengandung angka, huruf besar dan huruf kecil, dan karakter khusus, atau yang didasarkan pada kata-kata kamus umum.
```
There are three levels of password validation policy:

LOW    Length >= 8
MEDIUM Length >= 8, numeric, mixed case, and special characters
STRONG Length >= 8, numeric, mixed case, special characters and dictionary                  file

Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 1
```
Jika Anda mengaktifkan validasi kata sandi, Anda akan diperlihatkan kekuatan kata sandi untuk kata sandi root yang ada, dan menanyakan Anda apakah Anda ingin mengubah kata sandi itu. Jika Anda senang dengan kata sandi Anda saat ini, masukkan n untuk "tidak" pada prompt:
```
Using existing password for root.

Estimated strength of the password: 100
Change the password for root ? ((Press y|Y for Yes, any other key for No) : n
```
Untuk pertanyaan lainnya, Anda harus menekan Y dan menekan tombol Enter pada setiap prompt. Ini akan menghapus beberapa pengguna anonim dan basis data uji, menonaktifkan proses masuk akar jarak jauh, dan memuat aturan baru ini sehingga MySQL segera menghargai perubahan yang telah kami buat.

Pada titik ini, sistem database Anda sekarang sudah siap dan kita dapat melanjutkan.

### Instal PHP untuk Diproses
Kami sekarang telah menginstal Nginx untuk melayani halaman kami dan MySQL diinstal untuk menyimpan dan mengelola data kami. Namun, kami masih belum memiliki apa pun yang dapat menghasilkan konten dinamis. Kita bisa menggunakan PHP untuk ini.

Karena Nginx tidak mengandung pemrosesan PHP asli seperti beberapa server web lain, kita perlu menginstal php-fpm, yang merupakan singkatan dari "fastcGI process manager". Kami akan memberi tahu Nginx untuk meneruskan permintaan PHP ke perangkat lunak ini untuk diproses.

Kita dapat menginstal modul ini dan juga akan mengambil paket pembantu tambahan yang akan memungkinkan PHP untuk berkomunikasi dengan backend basis data kami. Instalasi akan menarik file-file inti PHP yang diperlukan. Lakukan ini dengan mengetik:
```bash
sudo apt-get install php-fpm php-mysql
```
#### Konfigurasikan Prosesor PHP
Kami sekarang telah menginstal komponen PHP kami, tetapi kami perlu membuat sedikit perubahan konfigurasi untuk membuat pengaturan kami lebih aman.

Buka file konfigurasi `php-fpm` utama dengan hak akses root:
```bash
sudo nano /etc/php/7.0/fpm/php.ini
```
Apa yang kita cari dalam file ini adalah parameter yang menetapkan cgi.fix_pathinfo. Ini akan dikomentari dengan titik koma (;) dan diatur ke "1" secara default.

Ini adalah pengaturan yang sangat tidak aman karena ini memberitahu PHP untuk mencoba mengeksekusi file terdekat yang dapat ditemukan jika file PHP yang diminta tidak dapat ditemukan. Ini pada dasarnya akan memungkinkan pengguna untuk membuat permintaan PHP dengan cara yang akan memungkinkan mereka untuk mengeksekusi skrip yang seharusnya tidak diizinkan untuk dieksekusi.

Kami akan mengubah kedua kondisi ini dengan menghapus tanda komentar pada baris dan menyetelnya ke "0" seperti ini:
```php
cgi.fix_pathinfo=0
```
Simpan dan tutup file saat Anda selesai.

Sekarang, kita hanya perlu me-restart prosesor PHP kita dengan mengetik:
```bash
sudo systemctl restart php7.0-fpm
```
Ini akan menerapkan perubahan yang kami buat.

### Konfigurasi Web Server Nginx untuk Menggunakan Prosesor PHP
Sekarang, kami memiliki semua komponen yang diperlukan terpasang. Satu-satunya perubahan konfigurasi yang masih kami butuhkan adalah memberi tahu Nginx untuk menggunakan prosesor PHP kami untuk konten dinamis.

Kami melakukan ini pada tingkat blok server (blok server mirip dengan host virtual Apache). Buka file konfigurasi blok server Nginx default dengan mengetik:

```bash
sudo nano /etc/nginx/sites-available/default
```
Saat ini, dengan komentar dihapus, file blok server Nginx default terlihat seperti ini:

```nginx
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    index index.html index.htm index.nginx-debian.html;

    server_name _;

    location / {
        try_files $uri $uri/ =404;
    }
}
```
Kami perlu membuat beberapa perubahan pada file ini untuk situs kami.

  * Pertama, kita perlu menambahkan `index.php` sebagai nilai pertama dari `index` direktif kami sehingga file bernama `index.php` disajikan, jika tersedia, ketika sebuah direktori diminta.
  * Kita dapat memodifikasi direktif `server_name` untuk menunjuk ke nama domain server kami atau alamat IP publik.
  * Untuk pemrosesan PHP yang sebenarnya, kita hanya perlu menghapus tanda komentar pada segmen file yang menangani permintaan PHP dengan menghapus simbol pound (#) dari depan setiap baris. Ini akan menjadi lokasi `~\.php$` location block, termasuk `fastcgi-php.conf` snippet, dan soket yang terkait dengan `php-fpm`.
  * Kami juga akan menghapus blok lokasi yang berhubungan dengan file .htaccess menggunakan metode yang sama. Nginx tidak memproses file-file ini. Jika salah satu dari file-file ini terjadi untuk menemukan jalan mereka ke dalam document root, mereka seharusnya tidak disajikan kepada pengunjung.

Perubahan yang perlu Anda buat berwarna merah dalam teks di bawah ini:

```nginx
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    index index.php index.html index.htm index.nginx-debian.html;

    server_name server_domain_or_IP;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.0-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}
```
Ketika Anda telah membuat perubahan di atas, Anda dapat menyimpan dan menutup file.

Uji file konfigurasi Anda untuk kesalahan sintaks dengan mengetik:
```bash
sudo nginx -t
```
Jika ada kesalahan dilaporkan, kembali dan periksa kembali file Anda sebelum melanjutkan.

Saat Anda siap, muat ulang Nginx untuk membuat perubahan yang diperlukan:
```bash
sudo systemctl reload nginx
```

### Buat File PHP untuk Menguji Konfigurasi
Stack LEMP Anda sekarang harus sepenuhnya diatur. Kita dapat mengujinya untuk memvalidasi bahwa Nginx dapat dengan benar menyerahkan file `.php` ke prosesor PHP kita.

Kita bisa melakukan ini dengan membuat file pengujian PHP di document root kita. Buka file baru bernama `info.php` di dalam root dokumen Anda di editor teks Anda:
```bash
sudo nano /var/www/html/info.php
```
Ketik atau tempelkan baris berikut ke dalam file baru. Ini adalah kode PHP yang valid yang akan mengembalikan informasi tentang server kami:
```bash

<?php
phpinfo();

```
Setelah selesai, simpan dan tutup file.

Sekarang, Anda dapat mengunjungi halaman ini di browser web Anda dengan mengunjungi nama domain server Anda atau alamat IP publik yang diikuti oleh `/info.php`:

```bash
http://server_domain_or_IP/info.php
```
Anda harus melihat halaman web yang telah dibuat oleh PHP dengan informasi tentang server Anda:

![PHP Info](https://res.cloudinary.com/imans/image/upload/v1537121356/blog/php_info.png)

Jika Anda melihat halaman yang terlihat seperti ini, Anda telah mengatur pemrosesan PHP dengan Nginx berhasil.

Setelah memverifikasi bahwa Nginx merender laman dengan benar, sebaiknya hapus file yang Anda buat karena benar-benar dapat memberikan kepada pengguna yang tidak sah beberapa petunjuk tentang konfigurasi Anda yang dapat membantu mereka mencoba menerobos masuk. Anda dapat selalu meregenerasi file ini jika Anda memerlukannya nanti.

Untuk saat ini, hapus file dengan mengetik:
```bash
sudo rm /var/www/html/info.php
```
