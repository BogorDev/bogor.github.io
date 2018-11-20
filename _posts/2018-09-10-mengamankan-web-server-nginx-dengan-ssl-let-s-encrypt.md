---
layout: post
title: Mengamankan Web Server Nginx dengan SSL Let's Encrypt
date: 2018-09-10 01:20 +0700
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
    - Security
tags:
    - Let's Encrypt
    - SSL
    - https
---
Let's Encrypt adalah Certificate Authority (CA) yang menyediakan cara mudah untuk mendapatkan dan menginstal sertifikat TLS / SSL gratis, sehingga memungkinkan HTTPS terenkripsi di server web. Ini menyederhanakan proses dengan menyediakan klien perangkat lunak, `Certbot`, yang mencoba untuk mengotomatisasi sebagian besar (jika tidak semua) langkah-langkah yang diperlukan. Saat ini, seluruh proses mendapatkan dan memasang sertifikat sepenuhnya otomatis pada `Apache` dan `Nginx`.

Dalam tutorial ini, Anda akan menggunakan Certbot untuk mendapatkan sertifikat SSL gratis untuk Nginx di Ubuntu 16.04 dan mengatur sertifikat Anda untuk memperbarui secara otomatis.

Tutorial ini menggunakan file konfigurasi Nginx default daripada file blok server terpisah. Kami merekomendasikan membuat file blok server Nginx baru untuk setiap domain karena membantu untuk menghindari beberapa kesalahan umum dan mempertahankan file default sebagai konfigurasi fallback sebagaimana dimaksud. Jika Anda ingin mengatur SSL menggunakan blok server, Anda dapat mengikuti blok server Nginx ini dengan Let's Encrypt tutorial.

### Prasyarat
Untuk mengikuti tutorial ini, Anda akan membutuhkan:

  * Satu server Ubuntu 16.04 yang disiapkan dengan mengikuti pengaturan server awal ini untuk tutorial Ubuntu 16.04, termasuk sudo non-root user dan firewall.
  * Nama domain yang terdaftar sepenuhnya. Tutorial ini akan menggunakan example.com di seluruh. Anda dapat membeli nama domain di Namecheap, mendapatkannya secara gratis di Freenom, atau menggunakan pendaftar domain pilihan Anda.
  * Kedua catatan DNS berikut ini disiapkan untuk server Anda. Anda dapat mengikuti tutorial nama host ini untuk detail tentang cara menambahkannya.
  * Rekor A dengan example.com yang mengarah ke alamat IP publik server Anda.
  * Catatan A dengan www.example.com yang menunjuk ke alamat IP publik server Anda.
  * Nginx diinstal dengan mengikuti Cara Menginstal Nginx di Ubuntu 16.04.

### Menginstall Certbot

Langkah pertama untuk menggunakan Let's Encrypt untuk mendapatkan sertifikat SSL adalah menginstal perangkat lunak Certbot di server Anda.

Certbot dalam pengembangan yang sangat aktif, sehingga paket Certbot yang disediakan oleh Ubuntu cenderung kedaluwarsa. Namun, pengembang Certbot memelihara repositori perangkat lunak Ubuntu dengan versi terbaru, jadi kami akan menggunakan repositori itu sebagai gantinya.

Pertama, tambahkan repositori.
```bash
sudo add-apt-repository ppa:certbot/certbot
```
Anda harus menekan `ENTER` untuk menerima. Kemudian, perbarui daftar paket untuk mengambil informasi paket repositori yang baru.
```bash
sudo apt-get update
```
Dan akhirnya, instal paket Nginx Certbot dengan `apt-get`.
```bash
sudo apt-get install python-certbot-nginx
```
Certbot sekarang siap digunakan, tetapi agar dapat mengkonfigurasi SSL untuk Nginx, kita perlu memverifikasi beberapa konfigurasi Nginx.

### Menyiapkan Nginx
Certbot dapat secara otomatis mengkonfigurasi SSL untuk Nginx, tetapi ia harus dapat menemukan blok server yang benar dalam konfigurasi Anda. Ia melakukan ini dengan mencari petunjuk server_name yang cocok dengan domain yang Anda minta sertifikat untuk.

Jika Anda memulai dengan instalasi Nginx baru, Anda dapat memperbarui file konfigurasi default. Buka dengan nano atau editor teks favorit Anda.
```bash
sudo nano /etc/nginx/sites-available/default
```
Temukan baris `server_name` yang ada dan ganti underscore, `_` , dengan nama domain Anda:
```nginx
. . .
server_name example.com www.example.com;
. . .
```
Simpan file dan keluar dari editor Anda.

Kemudian, verifikasi sintaks dari suntingan konfigurasi Anda.
```bash
sudo nginx -t
```
Jika Anda mendapatkan kesalahan apa pun, buka kembali file dan periksa kesalahan ketik, lalu uji lagi.

Setelah sintaks konfigurasi Anda benar, muat ulang Nginx untuk memuat konfigurasi baru.
```bash
sudo systemctl reload nginx
```
Certbot sekarang akan dapat menemukan blok `server` yang benar dan memperbaruinya. Selanjutnya, kami akan memperbarui firewall kami untuk memungkinkan lalu lintas HTTPS.

### Memungkinkan HTTPS Melalui Firewall

Jika Anda mengaktifkan firewall ufw, seperti yang disarankan oleh panduan prasyarat, Anda harus menyesuaikan pengaturan untuk memungkinkan lalu lintas HTTPS. Untungnya, Nginx mendaftarkan beberapa profil dengan ufw saat instalasi.

Anda dapat melihat pengaturan saat ini dengan mengetik:
```bash
sudo ufw status
```
Ini mungkin akan terlihat seperti ini, artinya hanya lalu lintas HTTP yang diizinkan ke server web:
```
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere                  
Nginx HTTP                 ALLOW       Anywhere                  
OpenSSH (v6)               ALLOW       Anywhere (v6)             
Nginx HTTP (v6)            ALLOW       Anywhere (v6)
```
Untuk menambahkan lalu lintas HTTPS, kita dapat memperbolehkan Nginx Profil lengkap dan kemudian menghapus tunjangan profil HTTP Nginx yang berlebihan:
```bash
sudo ufw allow 'Nginx Full'
sudo ufw delete allow 'Nginx HTTP'
```
Status Anda akan terlihat seperti ini sekarang:
```bash
sudo ufw status
```
Output :
```
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere
Nginx Full                 ALLOW       Anywhere
OpenSSH (v6)               ALLOW       Anywhere (v6)
Nginx Full (v6)            ALLOW       Anywhere (v6)
```
Kami sekarang siap untuk menjalankan Certbot dan mengambil sertifikat kami.

### Mendapatkan Sertifikat SSL
Certbot menyediakan berbagai cara untuk mendapatkan sertifikat SSL, melalui berbagai plugin. Plugin Nginx akan mengatur konfigurasi ulang Nginx dan reload konfigurasi jika diperlukan:
```bash
sudo certbot --nginx -d example.com -d www.example.com
```
Ini menjalankan `certbot` dengan plugin `--nginx`, menggunakan `-d` untuk menentukan nama yang kami inginkan agar sertifikat valid.

Jika ini adalah kali pertama Anda menjalankan `certbot`, Anda akan diminta memasukkan alamat email dan menyetujui persyaratan layanan. Setelah melakukannya, `certbot` akan berkomunikasi dengan server Let's Encrypt, kemudian jalankan tantangan untuk memverifikasi bahwa Anda mengendalikan domain yang Anda minta sertifikat.

Jika itu berhasil, `certbot` akan menanyakan bagaimana Anda ingin mengkonfigurasi pengaturan HTTPS Anda.
```
Please choose whether or not to redirect HTTP traffic to HTTPS, removing HTTP access.
-------------------------------------------------------------------------------
1: No redirect - Make no further changes to the webserver configuration.
2: Redirect - Make all requests redirect to secure HTTPS access. Choose this for
new sites, or if you're confident your site works on HTTPS. You can undo this
change by editing your web server's configuration.
-------------------------------------------------------------------------------
Select the appropriate number [1-2] then [enter] (press 'c' to cancel):
```
Pilih pilihan Anda lalu tekan `ENTER`. Konfigurasi akan diperbarui, dan Nginx akan memuat ulang untuk mengambil pengaturan baru. `certbot` akan membungkus dengan pesan yang memberitahukan Anda bahwa prosesnya berhasil dan di mana sertifikat Anda disimpan:
```
**IMPORTANT NOTES:**

 - Congratulations! Your certificate and chain have been saved at
   /etc/letsencrypt/live/example.com/fullchain.pem. Your cert will
   expire on 2017-10-23. To obtain a new or tweaked version of this
   certificate in the future, simply run certbot again with the
   "certonly" option. To non-interactively renew *all* of your
   certificates, run "certbot renew"
 - Your account credentials have been saved in your Certbot
   configuration directory at /etc/letsencrypt. You should make a
   secure backup of this folder now. This configuration directory will
   also contain certificates and private keys obtained by Certbot so
   making regular backups of this folder is ideal.
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
```
Sertifikat Anda diunduh, dipasang, dan dimuat. Coba muat ulang situs web Anda menggunakan https: // dan perhatikan indikator keamanan browser Anda. Ini harus menunjukkan bahwa situs tersebut diamankan dengan benar, biasanya dengan ikon kunci hijau. Jika Anda menguji server Anda menggunakan Uji Labs Server SSL, itu akan mendapat nilai A.

Mari kita selesaikan dengan menguji proses pembaruan.

### Memverifikasi Pembaruan-Otomatis Certbot

Sertifikat Let's Encrypt hanya berlaku selama sembilan puluh hari. Ini untuk mendorong pengguna untuk mengotomatiskan proses pembaruan sertifikat mereka. Paket certbot yang kami pasang menangani ini untuk kami dengan menjalankan 'certbot renew' dua kali sehari melalui timer sistemd. Pada distribusi non-sistemd, fungsi ini disediakan oleh skrip yang ditempatkan di /etc/cron.d. Tugas ini berjalan dua kali sehari dan akan memperbarui sertifikat apa pun yang dalam waktu tiga puluh hari kedaluwarsa.

Untuk menguji proses pembaruan, Anda dapat melakukan proses kering dengan certbot:
```bash
sudo certbot renew --dry-run
```
