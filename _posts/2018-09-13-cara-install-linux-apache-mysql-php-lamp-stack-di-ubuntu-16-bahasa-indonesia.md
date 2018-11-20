---
layout: post
title: Cara Install Linux, Apache, MySQL, PHP (LAMP) Stack di Ubuntu 16 Bahasa Indonesia
date: 2018-09-13 22:56 +0700
author: Iman Sugirman
image: https://res.cloudinary.com/imans/image/upload/v1536864486/blog/fabian-grohs-773959-unsplash.jpg
header:
    layout: hero
    vh: vh-50 vh-50-m vh-80-l
    navigation: fancy
    image: https://res.cloudinary.com/imans/image/upload/v1536864486/blog/fabian-grohs-773959-unsplash.jpg
    caption: "[Charles Deluvio](https://unsplash.com/@charlesdeluvio)"
    class: bg-darken-20 bg-darken-25-l
author_bio: true
categories:
    - Tutorial
    - LAMP
    - Ubuntu
tags:
    - Apache
    - MySQL
    - PHP
    - Ubuntu 16.04
---
Tumpukan "LAMP" adalah grup perangkat lunak sumber terbuka yang biasanya dipasang bersama untuk memungkinkan server menghosting situs web dinamis dan aplikasi web. Istilah ini sebenarnya merupakan akronim yang mewakili sistem operasi Linux, dengan server web Apache. Data situs disimpan dalam database MySQL, dan konten dinamis diproses oleh PHP.

Dalam panduan ini, kita akan mendapatkan tumpukan LAMP yang diinstal pada Ubuntu 16.04 Droplet. Ubuntu akan memenuhi persyaratan pertama kami: sistem operasi Linux.

### Prasyarat
Sebelum memulai dengan panduan ini, Anda harus memiliki akun pengguna non-root terpisah dengan hak sudo yang disiapkan di server Anda. Anda dapat mempelajari cara melakukannya dengan menyelesaikan langkah 1-4 di [pengaturan server awal untuk Ubuntu 16.04](/initial-server-setup-with-ubuntu-16-04).

### Install Apache dan Izinkan di Firewall
Server web Apache adalah salah satu server web paling populer di dunia. Ini terdokumentasi dengan baik, dan telah digunakan secara luas untuk sebagian besar sejarah web, yang menjadikannya pilihan default yang bagus untuk hosting situs web.

Kita dapat menginstal Apache dengan mudah menggunakan pengelola paket Ubuntu, tepat. Seorang manajer paket memungkinkan kita untuk menginstal sebagian besar perangkat lunak bebas rasa sakit dari repositori yang dikelola oleh Ubuntu. Anda dapat mempelajari lebih lanjut tentang cara menggunakan apt di sini.

Untuk tujuan kita, kita bisa mulai dengan mengetikkan perintah ini:
```bash
sudo apt-get update
sudo apt-get install apache2
```
Karena kita menggunakan perintah `sudo`, operasi ini dijalankan dengan hak akses root. Ini akan meminta Anda untuk kata sandi pengguna biasa Anda untuk memverifikasi niat Anda.

Setelah Anda memasukkan kata sandi, `apt` akan memberi tahu Anda paket apa yang akan dipasang dan berapa banyak ruang disk ekstra yang akan mereka gunakan. Tekan Y dan tekan Enter untuk melanjutkan, dan instalasi akan dilanjutkan.

### Atur ServerName Global untuk Menekan Peringatan Sintaks
Selanjutnya, kita akan menambahkan satu baris ke file `/etc/apache2/apache2.conf` untuk menekan pesan peringatan. Meskipun tidak berbahaya, jika Anda tidak mengatur `ServerName` secara global, Anda akan menerima peringatan berikut saat memeriksa konfigurasi Apache Anda untuk kesalahan sintaks:
```bash
sudo apache2ctl configtest
```
Dan Hasil Output :
```
AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1. Set the 'ServerName' directive globally to suppress this message
Syntax OK
```
Buka file konfigurasi utama dengan edit teks Anda:
```bash
sudo nano /etc/apache2/apache2.conf
```
Di dalam, di bagian bawah file, tambahkan perintah ServerName, dengan menunjuk ke nama domain primer Anda. Jika Anda tidak memiliki nama domain yang terkait dengan server Anda, Anda dapat menggunakan alamat IP publik server Anda:

Jika Anda tidak tahu alamat IP server Anda, lompat ke bagian tentang cara menemukan alamat IP publik server Anda untuk menemukannya.
{: .alert .alert--warning}

```apache
. . .
ServerName server_domain_or_IP
```
Simpan dan tutup file saat Anda selesai.

Selanjutnya, periksa kesalahan sintaks dengan mengetik:
```bash
sudo apache2ctl configtest
```
Karena kami menambahkan perintah ServerName global, Anda hanya harus melihat:

Output :
```
Syntax OK
```
Restart Apache untuk menerapkan perubahan Anda:
```bash
sudo systemctl restart apache2
```
Anda sekarang dapat mulai menyesuaikan firewall.

### Sesuaikan Firewall untuk Izinkan Lalu Lintas Web
Selanjutnya, dengan asumsi bahwa Anda telah mengikuti instruksi pengaturan server awal untuk mengaktifkan firewall UFW, pastikan bahwa firewall Anda memungkinkan lalu lintas HTTP dan HTTPS. Anda dapat memastikan bahwa UFW memiliki profil aplikasi untuk Apache seperti:
```bash
sudo ufw app list
```
dan Outputnya :
```
Available applications:
  Apache
  Apache Full
  Apache Secure
  OpenSSH
```
Jika Anda melihat profil `Apache Full`, itu harus menunjukkan bahwa itu memungkinkan lalu lintas ke port 80 dan 443:
```bash
sudo ufw app info "Apache Full"
```
Output :
```
Profile: Apache Full
Title: Web Server (HTTP,HTTPS)
Description: Apache v2 is the next generation of the omnipresent Apache web
server.

Ports:
  80,443/tcp
```
Izinkan lalu lintas masuk untuk profil ini:
```bash
sudo ufw allow in "Apache Full"
```
Anda dapat melakukan pemeriksaan langsung untuk memverifikasi bahwa semuanya berjalan sesuai rencana dengan mengunjungi alamat IP publik server Anda di browser web Anda (lihat catatan di bawah judul berikutnya untuk mencari tahu alamat IP publik Anda jika Anda tidak memiliki informasi ini sudah):
```
http://your_server_IP_address
```
Anda akan melihat halaman web default Ubuntu 16.04 Apache, yang ada untuk tujuan informasi dan pengujian. Seharusnya terlihat seperti ini:
![Apache Ubuntu 16.04](https://res.cloudinary.com/imans/image/upload/v1536855186/blog/Tripal_tutorial_2.0_Ubu14.04_Apache1.png)
Jika Anda melihat halaman ini, maka server web Anda sekarang sudah terpasang dengan benar dan dapat diakses melalui firewall Anda.

### Cara Menemukan Alamat IP Publik Server Anda
Jika Anda tidak tahu alamat IP publik server Anda, ada sejumlah cara untuk menemukannya. Biasanya, ini adalah alamat yang Anda gunakan untuk terhubung ke server Anda melalui SSH.

Dari baris perintah, Anda dapat menemukan ini beberapa cara. Pertama, Anda dapat menggunakan alat iproute2 untuk mendapatkan alamat Anda dengan mengetik ini:
```bash
ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'
```
Ini akan memberi Anda dua atau tiga baris kembali. Mereka semua adalah alamat yang benar, tetapi komputer Anda mungkin hanya dapat menggunakan salah satunya, jadi silakan mencoba masing-masing.

Metode alternatif adalah menggunakan utilitas `curl` untuk menghubungi pihak luar untuk memberi tahu Anda bagaimana ia melihat server Anda. Anda dapat melakukan ini dengan menanyakan server khusus apa alamat IP Anda:
```bash
sudo apt-get install curl
curl http://icanhazip.com
```
Terlepas dari metode yang Anda gunakan untuk mendapatkan alamat IP Anda, Anda dapat mengetikkannya ke bilah alamat browser web Anda untuk masuk ke server Anda.

### Instal MySQL
Sekarang kita memiliki server web kami dan berjalan, sekarang saatnya untuk menginstal MySQL. MySQL adalah sistem manajemen basis data. Pada dasarnya, ini akan mengatur dan menyediakan akses ke database di mana situs kami dapat menyimpan informasi.

Sekali lagi, kita dapat menggunakan apt untuk memperoleh dan menginstal perangkat lunak kami. Kali ini, kami juga akan menginstal beberapa paket "helper" lain yang akan membantu kami dalam mendapatkan komponen kami untuk berkomunikasi satu sama lain:


```bash
sudo apt-get install mysql-server
```
Catatan: Dalam hal ini, Anda tidak perlu menjalankan sudo apt-get update sebelum perintah. Ini karena kami baru-baru ini menjalankannya di perintah di atas untuk menginstal Apache. Indeks paket di komputer kita seharusnya sudah mutakhir.
{: .alert .alert--warning}

Sekali lagi, Anda akan diperlihatkan daftar paket yang akan diinstal, bersama dengan jumlah ruang disk yang akan mereka gunakan. Masukkan Y untuk melanjutkan.

Selama instalasi, server Anda akan meminta Anda untuk memilih dan mengkonfirmasi kata sandi untuk pengguna "root" MySQL. Ini adalah akun administratif di MySQL yang telah meningkatkan hak istimewa. Anggap saja mirip dengan akun root untuk server itu sendiri (yang Anda konfigurasikan sekarang adalah akun khusus MySQL). Pastikan ini adalah kata sandi yang kuat dan unik, dan jangan biarkan kosong.

Ketika instalasi selesai, kami ingin menjalankan skrip keamanan sederhana yang akan menghapus beberapa default berbahaya dan mengunci akses ke sistem database kami sedikit. Mulai skrip interaktif dengan menjalankan:
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
Anda akan diminta untuk memilih tingkat validasi kata sandi. Perlu diingat bahwa jika Anda memasukkan 2, untuk level terkuat, Anda akan menerima kesalahan saat mencoba mengatur kata sandi apa pun yang tidak mengandung angka, huruf besar dan huruf kecil, dan karakter khusus, atau yang didasarkan pada kata-kata kamus umum.

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

### Instal PHP
PHP adalah komponen dari pengaturan kami yang akan memproses kode untuk menampilkan konten dinamis. Ini dapat menjalankan skrip, terhubung ke database MySQL kami untuk mendapatkan informasi, dan menyerahkan konten yang telah diproses ke server web kami untuk ditampilkan.

Kami dapat sekali lagi memanfaatkan sistem apt untuk menginstal komponen kami. Kami akan menyertakan beberapa paket pembantu juga, sehingga kode PHP dapat berjalan di bawah server Apache dan berbicara dengan database MySQL kami:
```bash
sudo apt-get install php libapache2-mod-php php-mcrypt php-mysql
```
Ini harus menginstal PHP tanpa masalah. Kami akan menguji ini sebentar lagi.

Dalam banyak kasus, kami ingin memodifikasi cara Apache menyajikan file ketika sebuah direktori diminta. Saat ini, jika pengguna meminta direktori dari server, Apache akan mencari file yang disebut index.html. Kami ingin memberitahu server web kami untuk memilih file PHP, jadi kami akan membuat Apache mencari file index.php terlebih dahulu.

Untuk melakukan ini, ketik perintah ini untuk membuka file dir.conf dalam editor teks dengan hak akses root:
```bash
sudo nano /etc/apache2/mods-enabled/dir.conf
```
Akan terlihat seperti ini:
```apache
<IfModule mod_dir.c>
    DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
</IfModule>
```
Kami ingin memindahkan file indeks PHP yang disorot di atas ke posisi pertama setelah spesifikasi DirectoryIndex, seperti ini:

```apache
<IfModule mod_dir.c>
    DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
```
Setelah selesai, simpan dan tutup file dengan menekan Ctrl-X. Anda harus mengonfirmasi penyimpanan dengan mengetik Y lalu menekan Enter untuk mengonfirmasi lokasi penyimpanan file.

Setelah ini, kita perlu me-restart server web Apache agar perubahan kita diakui. Anda dapat melakukan ini dengan mengetik ini:
```bash
sudo systemctl restart apache2
```
Kami juga dapat memeriksa status layanan apache2 menggunakan systemctl:
```bash
sudo systemctl status apache2
```
Output :
```
● apache2.service - LSB: Apache2 web server
   Loaded: loaded (/etc/init.d/apache2; bad; vendor preset: enabled)
  Drop-In: /lib/systemd/system/apache2.service.d
           └─apache2-systemd.conf
   Active: active (running) since Wed 2016-04-13 14:28:43 EDT; 45s ago
     Docs: man:systemd-sysv-generator(8)
  Process: 13581 ExecStop=/etc/init.d/apache2 stop (code=exited, status=0/SUCCESS)
  Process: 13605 ExecStart=/etc/init.d/apache2 start (code=exited, status=0/SUCCESS)
    Tasks: 6 (limit: 512)
   CGroup: /system.slice/apache2.service
           ├─13623 /usr/sbin/apache2 -k start
           ├─13626 /usr/sbin/apache2 -k start
           ├─13627 /usr/sbin/apache2 -k start
           ├─13628 /usr/sbin/apache2 -k start
           ├─13629 /usr/sbin/apache2 -k start
           └─13630 /usr/sbin/apache2 -k start

Apr 13 14:28:42 ubuntu-16-lamp systemd[1]: Stopped LSB: Apache2 web server.
Apr 13 14:28:42 ubuntu-16-lamp systemd[1]: Starting LSB: Apache2 web server...
Apr 13 14:28:42 ubuntu-16-lamp apache2[13605]:  * Starting Apache httpd web server apache2
Apr 13 14:28:42 ubuntu-16-lamp apache2[13605]: AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1. Set the 'ServerNam
Apr 13 14:28:43 ubuntu-16-lamp apache2[13605]:  *
Apr 13 14:28:43 ubuntu-16-lamp systemd[1]: Started LSB: Apache2 web server.
```
### Instal Modul PHP
Untuk meningkatkan fungsionalitas PHP, kita dapat menginstal beberapa modul tambahan secara opsional.

Untuk melihat opsi yang tersedia untuk modul dan pustaka PHP, Anda dapat menyalurkan hasil `apt-cache search` ke dalam jumlah yang lebih sedikit, pager yang memungkinkan Anda menggulir output dari perintah lain:
```bash
apt-cache search php- | less
```
Gunakan tombol panah untuk menggulir ke atas dan ke bawah, dan q untuk keluar.

Hasilnya adalah semua komponen opsional yang dapat Anda pasang. Ini akan memberi Anda deskripsi singkat untuk masing-masing:
```
libnet-libidn-perl - Perl bindings for GNU Libidn
php-all-dev - package depending on all supported PHP development packages
php-cgi - server-side, HTML-embedded scripting language (CGI binary) (default)
php-cli - command-line interpreter for the PHP scripting language (default)
php-common - Common files for PHP packages
php-curl - CURL module for PHP [default]
php-dev - Files for PHP module development (default)
php-gd - GD module for PHP [default]
php-gmp - GMP module for PHP [default]
…
:
```
Untuk mendapatkan informasi lebih lanjut tentang apa yang masing-masing modul lakukan, Anda dapat mencari di internet, atau Anda dapat melihat deskripsi panjang paket tersebut dengan mengetik:
```bash
apt-cache show package_name
```
Akan ada banyak output, dengan satu bidang yang disebut `Description-id` yang akan memiliki penjelasan lebih panjang tentang fungsionalitas yang disediakan modul.

Misalnya, untuk mengetahui apa yang dilakukan oleh modul `php-cli`, kita dapat mengetik ini:
```bash
apt-cache show php-cli
```
Seiring dengan sejumlah besar informasi lainnya, Anda akan menemukan sesuatu yang terlihat seperti ini:

```
…
Description-en: command-line interpreter for the PHP scripting language (default)
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
Jika, setelah meneliti, Anda memutuskan Anda ingin menginstal paket, Anda dapat melakukannya dengan menggunakan perintah `apt-get install` seperti yang telah kami lakukan untuk perangkat lunak kami yang lain.

Jika kami memutuskan bahwa `php-cli` adalah sesuatu yang kami butuhkan, kami dapat mengetik:

```bash
sudo apt-get install php-cli
```
Jika Anda ingin menginstal lebih dari satu modul, Anda dapat melakukannya dengan mendaftar masing-masing, dipisahkan oleh spasi, mengikuti perintah `apt-get install`, seperti ini:
```bash
sudo apt-get install package1 package2 ...
```
Pada titik ini, tumpukan LAMP Anda dipasang dan dikonfigurasi. Kami masih harus menguji PHP kami.

### Test PHP Processing on your Web Server

Untuk menguji bahwa sistem kami dikonfigurasi dengan benar untuk PHP, kita dapat membuat skrip PHP yang sangat dasar.

Kami akan memanggil skrip ini info.php. Agar Apache dapat menemukan file dan menyajikannya dengan benar, itu harus disimpan ke direktori yang sangat spesifik, yang disebut "root web".

Di Ubuntu 16.04, direktori ini terletak di `/var/www/html/`. Kita dapat membuat file di lokasi tersebut dengan mengetik:
```bash
sudo nano /var/www/html/info.php
```
Ini akan membuka file kosong. Kami ingin menempatkan teks berikut, yang merupakan kode PHP yang valid, di dalam file:

```php
<?php
phpinfo();
?>
```
Setelah selesai, simpan dan tutup file.

Sekarang kita dapat menguji apakah server web kami dapat menampilkan konten yang dihasilkan oleh skrip PHP dengan benar. Untuk mencoba ini, kita hanya perlu mengunjungi halaman ini di browser web kami. Anda akan memerlukan alamat IP publik server Anda lagi.

Alamat yang ingin Anda kunjungi adalah:

```php
http://your_server_IP_address/info.php
```
Halaman yang Anda datangi harus terlihat seperti ini:
