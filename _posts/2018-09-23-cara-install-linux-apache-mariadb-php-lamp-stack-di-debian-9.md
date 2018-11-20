---
layout: post
title: Cara Install Linux, Apache, MariaDB, PHP (LAMP) Stack di Debian 9
date: 2018-09-23 09:13 +0700
author: Iman Sugirman
cover: /assets/images/lamp-stack-debian.png
image: https://res.cloudinary.com/imans/image/upload/v1537669977/blog/fabian-grohs-423591-unsplash.jpg
header:
    layout: hero
    vh: vh-50 vh-50-m vh-80-l
    navigation: fancy
    image: https://res.cloudinary.com/imans/image/upload/v1537669977/blog/fabian-grohs-423591-unsplash.jpg
    caption: "[Fabian Grohs](https://unsplash.com/@grohsfabian)"
    class: bg-darken-20 bg-darken-25-l
author_bio: true
categories:
    - Tutorial
    - LAMP
    - Debian
tags:
    - Apache
    - PHP
    - MariaDB
---
Tumpukan "LAMP" adalah grup perangkat lunak sumber terbuka yang biasanya dipasang bersama untuk memungkinkan server menghosting situs web dinamis dan aplikasi web. Istilah ini sebenarnya merupakan akronim yang mewakili sistem operasi Linux, dengan server web Apache.

Data situs disimpan dalam database MariaDB, dan konten dinamis diproses oleh PHP. Dalam panduan ini, kami akan memasang tumpukan LAMP di server Debian 9.

### Prasyarat
Untuk menyelesaikan tutorial ini, Anda harus memiliki server Debian 9 dengan akun pengguna non-root sudo-enabled dan firewall dasar. Ini dapat dikonfigurasi menggunakan panduan [Pengaturan Awal Server untuk Debian 9](/pengaturan-awal-server-dengan-debian-9).

Langkah 1 - Menginstal Apache dan Memperbarui Firewall
Server web Apache adalah salah satu server web paling populer di dunia. Ini terdokumentasi dengan baik dan telah digunakan secara luas untuk sebagian besar sejarah web, yang menjadikannya pilihan default yang bagus untuk hosting situs web.

Instal Apache menggunakan manajer paket Debian, tepat:
```bash
sudo apt update
sudo apt install apache2
```
Karena ini adalah perintah `sudo`, operasi ini dijalankan dengan hak akses root. Ini akan meminta Anda untuk kata sandi pengguna biasa Anda untuk memverifikasi niat Anda.

Setelah Anda memasukkan kata sandi, `apt` akan memberi tahu Anda paket apa yang akan dipasang dan berapa banyak ruang disk ekstra yang akan mereka gunakan. Tekan `Y` dan tekan `ENTER` untuk melanjutkan, dan instalasi akan dilanjutkan.

Selanjutnya, dengan asumsi bahwa Anda telah mengikuti instruksi pengaturan server awal dengan menginstal dan mengaktifkan firewall UFW, pastikan bahwa firewall Anda memungkinkan lalu lintas HTTP dan HTTPS.

Ketika diinstal pada Debian 9, UFW hadir dimuat dengan profil aplikasi yang dapat Anda gunakan untuk men-tweak pengaturan firewall Anda. Lihat daftar lengkap profil aplikasi dengan menjalankan:
```bash
sudo ufw app list
```
Profil `WWW` digunakan untuk mengelola port yang digunakan oleh server web:
```
Available applications:
. . .
  WWW
  WWW Cache
  WWW Full
  WWW Secure
. . .
```
Jika Anda memeriksa `WWW` Profil lengkap, ini menunjukkan bahwa ia memungkinkan lalu lintas ke port `80` dan `443`:
```bash
sudo ufw app info "WWW Full"
```
Outputnya :
```
Profile: WWW Full
Title: Web Server (HTTP,HTTPS)
Description: Web Server (HTTP,HTTPS)

Ports:
  80,443/tcp
```
Izinkan traffic HTTP dan HTTPS yang masuk untuk profil ini:
```bash
sudo ufw allow in “WWW Full”
```
Anda dapat melakukan pemeriksaan langsung untuk memverifikasi bahwa semuanya berjalan sesuai rencana dengan mengunjungi alamat IP publik server Anda di peramban web Anda:
```bash
http://your_server_ip
```
Anda akan melihat halaman web default Debian 9 Apache, yang ada untuk tujuan informasi dan pengujian. Seharusnya terlihat seperti ini:
![Debian Apache](https://res.cloudinary.com/imans/image/upload/v1537669431/blog/small_apache_default_debian9.png)
Jika Anda melihat halaman ini, maka server web Anda sekarang sudah terpasang dengan benar dan dapat diakses melalui firewall Anda.

Jika Anda tidak tahu alamat IP publik server Anda, ada sejumlah cara untuk menemukannya. Biasanya, ini adalah alamat yang Anda gunakan untuk terhubung ke server Anda melalui SSH.

Ada beberapa cara berbeda untuk melakukan ini dari baris perintah. Pertama, Anda bisa menggunakan alat `iproute2` untuk mendapatkan alamat IP Anda dengan mengetik ini:
```bash
ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'
```
Ini akan memberi Anda dua atau tiga baris kembali. Mereka semua adalah alamat yang benar, tetapi komputer Anda mungkin hanya dapat menggunakan salah satunya, jadi silakan mencoba masing-masing.

Metode alternatif adalah menggunakan utilitas `curl` untuk menghubungi pihak luar untuk memberi tahu Anda bagaimana ia melihat server Anda. Ini dilakukan dengan menanyakan server khusus apa alamat IP Anda:
```bash
sudo apt install curl
curl http://icanhazip.com
```
Terlepas dari metode yang Anda gunakan untuk mendapatkan alamat IP Anda, ketikkan ke bilah alamat browser web Anda untuk melihat halaman Apache default.

### Install MariaDB di Debian 9
Sekarang setelah server web Anda aktif dan berjalan, sekarang saatnya menginstal MariaDB. MariaDB adalah sistem manajemen basis data. Pada dasarnya, ini akan mengatur dan menyediakan akses ke database di mana situs Anda dapat menyimpan informasi.

MariaDB adalah garpu buatan MySQL. Di Debian 9, server default MySQL adalah MariaDB 10.1, dan paket `mysql-server`, yang biasanya digunakan untuk menginstal MySQL, adalah paket transisi yang benar-benar akan menginstal MariaDB. Namun, sebaiknya Anda menginstal MariaDB menggunakan paket aktual program, `mariadb-server`.

Sekali lagi, gunakan `apt` untuk memperoleh dan menginstal perangkat lunak ini:
```bash
sudo apt install mariadb-server
```
Catatan: Dalam kasus ini, Anda tidak perlu menjalankan pembaruan sudo apt sebelum perintah. Ini karena Anda baru-baru ini menjalankannya di perintah di atas untuk menginstal Apache, dan indeks paket di komputer Anda seharusnya sudah mutakhir.
{: .alert .alert--warning}

Perintah ini juga akan menunjukkan daftar paket yang akan diinstal, bersama dengan jumlah ruang disk yang akan mereka gunakan. Masukkan `Y` untuk melanjutkan.

Ketika instalasi selesai, jalankan skrip keamanan sederhana yang sudah diinstal sebelumnya dengan MariaDB yang akan menghapus beberapa pengaturan default yang tidak aman dan mengunci akses ke sistem database Anda. Mulai skrip interaktif dengan menjalankan:
```bash
sudo mysql_secure_installation
```
Ini akan membawa Anda melalui serangkaian petunjuk di mana Anda dapat membuat beberapa perubahan pada opsi keamanan instalasi MariaDB Anda. Prompt pertama akan meminta Anda memasukkan kata sandi basis data root saat ini. Ini adalah akun administratif di MariaDB yang telah meningkatkan hak istimewa. Anggap saja mirip dengan akun root untuk server itu sendiri (meskipun yang Anda konfigurasikan sekarang adalah akun khusus MariaDB). Karena Anda baru saja memasang MariaDB dan belum membuat perubahan konfigurasi apa pun, kata sandi ini akan kosong, jadi cukup tekan `ENTER` pada prompt.

Prompt berikutnya menanyakan apakah Anda ingin membuat kata sandi root database. Ketik `N` lalu tekan `ENTER`. Di Debian, akun root untuk MariaDB terkait erat dengan pemeliharaan sistem otomatis, jadi kita tidak boleh mengubah metode otentikasi yang dikonfigurasi untuk akun itu. Melakukan hal itu akan memungkinkan pembaruan paket untuk memutus sistem basis data dengan menghapus akses ke akun administratif. Nanti, kami akan membahas cara opsional mengatur akun administratif tambahan untuk akses kata sandi jika otentikasi soket tidak sesuai untuk kasus penggunaan Anda.

Dari sana, Anda dapat menekan `Y` dan kemudian `ENTER` untuk menerima default untuk semua pertanyaan berikutnya. Ini akan menghapus beberapa pengguna anonim dan basis data uji, menonaktifkan proses masuk akar jarak jauh, dan memuat aturan baru ini sehingga MariaDB segera menghargai perubahan yang Anda buat.

Dalam instalasi baru pada sistem Debian, pengguna root MariaDB diatur untuk mengotentikasi menggunakan plugin `unix_socket` secara default daripada dengan kata sandi. Ini memungkinkan beberapa keamanan dan kegunaan yang lebih besar dalam banyak kasus, tetapi juga dapat mempersulit hal-hal ketika Anda perlu mengizinkan program eksternal (misalnya, phpMyAdmin) hak administratif.

Karena server menggunakan akun root untuk tugas-tugas seperti rotasi log dan memulai dan menghentikan server, sebaiknya tidak mengubah detail otentikasi akun root. Mengubah kredensial akun di `/etc/mysql/debian.cnf` dapat berfungsi pada awalnya, tetapi pembaruan paket berpotensi menimpa perubahan tersebut. Daripada memodifikasi akun root, pengelola paket merekomendasikan membuat akun administratif terpisah jika Anda perlu mengatur akses berbasis kata sandi.

Untuk melakukannya, kita akan membuat akun baru yang disebut `admin` dengan kemampuan yang sama dengan akun root, tetapi dikonfigurasi untuk otentikasi kata sandi. Untuk melakukan ini, buka prompt MariaDB dari terminal Anda:
```bash
sudo mariadb
```
Sekarang, kita dapat membuat pengguna baru dengan hak akses root dan akses berbasis kata sandi. Ubah nama pengguna dan kata sandi untuk menyesuaikan preferensi Anda:
```bash
GRANT ALL ON *.* TO 'admin'@'localhost' IDENTIFIED BY 'password' WITH GRANT OPTION;
```
Lalu Ketik :
```bash
FLUSH PRIVILEGES;
```
Setelah ini, keluar dari shell MariaDB:
```bash
exit
```
Sekarang, kapan saja Anda ingin mengakses basis data Anda sebagai pengguna administratif baru Anda, Anda harus mengotentikasi sebagai pengguna dengan kata sandi yang baru saja Anda tetapkan menggunakan perintah berikut:
```bash
mariadb -u admin -p
```
Pada titik ini, sistem database Anda sudah diatur dan Anda dapat melanjutkan untuk menginstal PHP, komponen terakhir dari tumpukan LAMP.

### Install PHP
PHP adalah komponen dari pengaturan Anda yang akan memproses kode untuk menampilkan konten dinamis. Ini dapat menjalankan script, terhubung ke database MariaDB Anda untuk mendapatkan informasi, dan menyerahkan konten yang sudah diproses ke server web Anda untuk ditampilkan.

Sekali lagi, gunakan sistem `apt` untuk menginstal PHP. Selain itu, sertakan beberapa paket pembantu saat ini sehingga kode PHP dapat berjalan di bawah server Apache dan berbicara dengan basis data MariaDB Anda:
```
sudo apt install php libapache2-mod-php php-mysql
```
Ini harus menginstal PHP tanpa masalah. Kami akan menguji ini sebentar lagi.

Dalam kebanyakan kasus, Anda akan ingin memodifikasi cara Apache menyajikan file ketika sebuah direktori diminta. Saat ini, jika pengguna meminta direktori dari server, Apache akan mencari file yang disebut `index.html`. Kami ingin memberitahu server web untuk memilih file PHP di atas yang lain, jadi buat Apache mencari file `index.php` terlebih dahulu.

Untuk melakukan ini, ketik perintah ini untuk membuka file `dir.conf` dalam editor teks dengan hak akses root:
```bash
sudo nano /etc/apache2/mods-enabled/dir.conf
```
Akan terlihat seperti ini:
```apache
<IfModule mod_dir.c>
    DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
</IfModule>
```
Pindahkan file indeks PHP (disorot di atas) ke posisi pertama setelah spesifikasi DirectoryIndex, seperti ini:
```apache
<IfModule mod_dir.c>
    DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
```
Ketika Anda selesai, simpan dan tutup file dengan menekan `CTRL + X`. Konfirmasikan penyimpanan dengan mengetik `Y` lalu tekan `ENTER` untuk memverifikasi lokasi penyimpanan file.

Setelah ini, restart server web Apache agar perubahan Anda diakui. Lakukan ini dengan mengetik ini:
```bash
sudo systemctl restart apache2
```
Anda juga dapat memeriksa status layanan `apache2` menggunakan `systemctl`:
```bash
sudo systemctl status apache2
```
Outputnya :
```
● apache2.service - The Apache HTTP Server
   Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor preset: enabled)
   Active: active (running) since Tue 2018-09-04 18:23:03 UTC; 9s ago
  Process: 22209 ExecStop=/usr/sbin/apachectl stop (code=exited, status=0/SUCCESS)
  Process: 22216 ExecStart=/usr/sbin/apachectl start (code=exited, status=0/SUCCESS)
 Main PID: 22221 (apache2)
    Tasks: 6 (limit: 4915)
   CGroup: /system.slice/apache2.service
           ├─22221 /usr/sbin/apache2 -k start
           ├─22222 /usr/sbin/apache2 -k start
           ├─22223 /usr/sbin/apache2 -k start
           ├─22224 /usr/sbin/apache2 -k start
           ├─22225 /usr/sbin/apache2 -k start
           └─22226 /usr/sbin/apache2 -k start
```
Untuk meningkatkan fungsionalitas PHP, Anda memiliki opsi untuk menginstal beberapa modul tambahan. Untuk melihat opsi-opsi yang tersedia untuk modul dan pustaka PHP, susun hasil pencarian apt menjadi kurang, sebuah pager yang memungkinkan Anda menggulir output dari perintah lain:
```bash
apt search php- | less
```
Gunakan tombol panah untuk menggulir ke atas dan ke bawah, dan tekan `Q` untuk keluar.

Hasilnya adalah semua komponen opsional yang dapat Anda pasang. Ini akan memberi Anda deskripsi singkat untuk masing-masing:
```
Sorting...
Full Text Search...
bandwidthd-pgsql/stable 2.0.1+cvs20090917-10 amd64
  Tracks usage of TCP/IP and builds html files with graphs

bluefish/stable 2.2.9-1+b1 amd64
  advanced Gtk+ text editor for web and software development

cacti/stable 0.8.8h+ds1-10 all
  web interface for graphing of monitoring systems

cakephp-scripts/stable 2.8.5-1 all
  rapid application development framework for PHP (scripts)

ganglia-webfrontend/stable 3.6.1-3 all
  cluster monitoring toolkit - web front-end

haserl/stable 0.9.35-2+b1 amd64
  CGI scripting program for embedded environments

kdevelop-php-docs/stable 5.0.3-1 all
  transitional package for kdevelop-php

kdevelop-php-docs-l10n/stable 5.0.3-1 all
  transitional package for kdevelop-php-l10n
…
:
```
Untuk mempelajari lebih lanjut tentang apa yang masing-masing modul lakukan, Anda dapat mencari di internet untuk informasi lebih lanjut tentang mereka. Atau, lihat deskripsi panjang paket dengan mengetik:
```bash
apt show package_name
```
Akan ada banyak output, dengan satu bidang yang disebut Deskripsi yang akan memiliki penjelasan lebih panjang tentang fungsionalitas yang disediakan modul.

Misalnya, untuk mengetahui apa yang dilakukan oleh modul `php-cli`, Anda bisa mengetik ini:
```bash
apt show php-cli
```
Seiring dengan sejumlah besar informasi lainnya, Anda akan menemukan sesuatu yang terlihat seperti ini:
```
…
Description: command-line interpreter for the PHP scripting language (default)
 This package provides the /usr/bin/php command interpreter, useful for
 testing PHP scripts from a shell or performing general shell scripting tasks.
 .
 PHP (recursive acronym for PHP: Hypertext Preprocessor) is a widely-used
 open source general-purpose scripting language that is especially suited
 for web development and can be embedded into HTML.
 .
 This package is a dependency package, which depends on Debian's default
 PHP version (currently 7.0).
…
```
Jika, setelah meneliti, Anda memutuskan Anda ingin menginstal paket, Anda dapat melakukannya dengan menggunakan perintah `apt install` seperti yang telah Anda lakukan untuk perangkat lunak lain.

Jika Anda memutuskan bahwa `php-cli` adalah sesuatu yang Anda butuhkan, Anda dapat mengetik:
```bash
sudo apt install php-cli
```
Jika Anda ingin menginstal lebih dari satu modul, Anda dapat melakukannya dengan mendaftar masing-masing, dipisahkan oleh spasi, mengikuti perintah `apt install`, seperti ini:
```bash
sudo apt install package1 package2 ...
```
Pada titik ini, tumpukan LAMP Anda dipasang dan dikonfigurasi. Sebelum membuat perubahan lebih lanjut atau menerapkan aplikasi, akan sangat membantu untuk secara proaktif menguji konfigurasi PHP Anda jika ada masalah yang harus ditangani.

### Menguji Pengolahan PHP di Server Web Anda
In order to test that your system is configured properly for PHP, create a very basic PHP script called `info.php`. In order for Apache to find this file and serve it correctly, it must be saved to a very specific directory called the web root.

In Debian 9, this directory is located at `/var/www/html/`. Create the file at that location by running:
```bash
sudo nano /var/www/html/info.php
```
Ini akan membuka file kosong. Tambahkan teks berikut, yang merupakan kode PHP yang valid, di dalam file:
```
<?php
phpinfo();
?>
```
Setelah selesai, simpan dan tutup file.

Sekarang Anda dapat menguji apakah server web Anda dapat menampilkan konten yang dihasilkan oleh skrip PHP ini dengan benar. Untuk mencoba ini, kunjungi halaman ini di browser web Anda. Anda akan memerlukan alamat IP publik server Anda lagi.

Alamat yang ingin Anda kunjungi adalah:
```
http://your_server_ip/info.php
```
Halaman yang Anda datangi harus terlihat seperti ini:
![PHP Info](https://res.cloudinary.com/imans/image/upload/v1537671414/blog/small_php_info_debian9.png)
