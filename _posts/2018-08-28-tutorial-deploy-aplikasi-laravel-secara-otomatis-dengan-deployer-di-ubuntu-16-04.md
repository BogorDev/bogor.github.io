---
layout: post
title: Tutorial Deploy Aplikasi Laravel Secara Otomatis Dengan Deployer di Ubuntu 16.04
date: 2018-08-28 17:09 +0700
author: Iman Sugirman
image: https://res.cloudinary.com/wpcloudserver/image/upload/v1535740542/fancycrave-151127-unsplash.jpg
header:
    layout: hero
    vh: vh-50 vh-50-m vh-80-l
    navigation: fancy
    image: https://res.cloudinary.com/wpcloudserver/image/upload/v1535740542/fancycrave-151127-unsplash.jpg
    caption: "[Fancycrave](https://unsplash.com/@fancycrave)"
    class: bg-darken-20 bg-darken-25-l
author_bio: true
categories:
    - Cloud Server
    - Tutorial
tags:
    - Laravel
    - Deployer
---
Laravel adalah kerangka kerja web open source PHP yang dirancang untuk membuat tugas pengembangan web umum, seperti otentikasi, routing, dan caching, lebih mudah. Deployer adalah alat deployment PHP open-source dengan dukungan out-of-the-box untuk sejumlah kerangka kerja populer, termasuk Laravel, CodeIgniter, Symfony, dan Zend Framework.

<!-- ![Laravel](/assets/images/laravel56.jpg) -->

### Introduction
Deployer mengotomatiskan penerapan dengan menggandakan aplikasi dari repositori Git ke server, menginstal dependensi dengan Composer, dan mengonfigurasi aplikasi sehingga Anda tidak perlu melakukannya secara manual. Ini memungkinkan Anda menghabiskan lebih banyak waktu untuk pengembangan, alih-alih upload dan konfigurasi, dan memungkinkan Anda menerapkan lebih sering.

Dalam tutorial ini, Anda akan menggunakan aplikasi Laravel secara otomatis tanpa downtime apa pun. Untuk melakukan ini, Anda akan mempersiapkan lingkungan pengembangan lokal dari mana Anda akan menyebarkan kode dan kemudian mengkonfigurasi server produksi dengan Nginx dan database MySQL untuk melayani aplikasi.

### Prerequisites
Sebelum memulai panduan ini, Anda memerlukan hal-hal berikut:

  * Satu server Ubuntu 16.04 dengan pengguna non-root dengan hak akses sudo seperti yang dijelaskan dalam Pengaturan Server Awal dengan tutorial Ubuntu 16.04.

  * Tumpukan LEMP diinstal seperti yang dijelaskan dalam Cara Menginstal Linux, Nginx, MySQL, PHP (LEMP stack) di tutorial Ubuntu 16.04.

  * PHP, Komposer, dan Git yang diinstal di server Anda dengan mengikuti Langkah 1 dan 2 tentang Cara Menginstal dan Menggunakan Komposer pada Ubuntu 16.04.

  * Paket php-xml dan php-mbstring diinstal di server Anda. Instal ini dengan menjalankan: sudo apt-get install php7.0-mbstring php7.0-xml.

  * `Server Git`. Anda dapat menggunakan layanan seperti GitLab, Bitbucket atau GitHub. GitLab dan Bitbucket menawarkan repositori pribadi secara gratis, dan GitHub menawarkan repositori pribadi mulai dari $ 7 / bulan. Sebagai alternatif, Anda dapat membuat server Git pribadi dengan mengikuti tutorial Cara Mengatur Server Git Pribadi pada VPS.

  * Nama domain yang mengarah ke server Anda. Cara Mengatur Nama Inang dengan tutorial DigitalOcean dapat membantu Anda mengonfigurasi ini.

  * Komposer dan Git yang terpasang di komputer lokal Anda juga. Metode pemasangan yang tepat tergantung pada sistem operasi lokal Anda. Petunjuk untuk menginstal Git tersedia di halaman Unduhan proyek Git dan Anda dapat mengunduh Komposer langsung dari situs web proyek Composer.

#### Step 1 — Menyiapkan Laravel Aplikasi di Lokal

Karena Anda akan membuat dan menerapkan aplikasi Anda dari komputer lokal Anda, mulailah dengan mengkonfigurasi lingkungan pengembangan lokal Anda. Deployer akan mengontrol seluruh proses penerapan dari mesin lokal Anda, jadi mulailah dengan menginstalnya

***
Note: If you use Windows on your local machine you should use a BASH emulator (like Git bash) to run all local commands.
{: .alert .alert--info}
***


```bash
curl -LO https://deployer.org/deployer.phar
```
Lalu Masukan Perintah

``` bash
php -r "if (hash_file('sha1', 'deployer.phar') === '35e8dcd50cf7186502f603676b972065cb68c129') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('deployer.phar'); } echo PHP_EOL;"
```
Dan Outputnya adalah :

```shell
Installer verified
```

Buat sistem Penyebar tersedia luas. Perhatikan bahwa jika Anda menjalankan Windows atau macOS pada komputer lokal Anda, Anda mungkin perlu membuat direktori `/usr/local/bin/dep` sebelum menjalankan perintah ini:

```shell
sudo mv deployer.phar /usr/local/bin/dep
```
Buatlah agar file `deployer.phar` bisa di eksekusi :

```shell
sudo chmod +x /usr/local/bin/dep
```
Selanjutnya anda harus membuat aplikasi Laravel di `Local` Computer / PC atau MAC.

Contoh disini menginstall Laravel Versi 5.5

```shell
composer create-project --prefer-dist laravel/laravel laravel-app "5.5.*"
```
Anda telah menginstal semua Aplikasi Laravel yang diperlukan di `Komputer Local atau Mac` Anda. Dengan itu di tempat, kita akan beralih ke menciptakan repositori `Git` untuk aplikasi.

### Menghubungkan ke Remote Git Repository Anda
**Deployer** dirancang untuk memungkinkan pengguna menyebarkan kode dari mana saja. Untuk memungkinkan fungsi ini, diperlukan pengguna untuk memasukkan kode ke repositori di Internet dari mana Penyebar kemudian menyalin kode ke server produksi. Kami akan menggunakan Git, sistem kontrol versi open-source, untuk mengelola kode sumber aplikasi Laravel. Anda dapat terhubung ke server Git menggunakan protokol SSH, dan untuk melakukan ini dengan aman, Anda perlu membuat kunci SSH. Ini lebih aman daripada otentikasi berbasis kata sandi dan biarkan Anda menghindari mengetik kata sandi sebelum setiap penerapan.

Jalankan perintah berikut pada `local machine` Anda untuk menghasilkan kunci SSH. Perhatikan bahwa -f menentukan nama file dari file kunci, dan Anda dapat mengganti `gitkey` dengan nama file Anda sendiri. Ini akan menghasilkan sepasang kunci SSH (bernama `gitkey` dan `gitkey.pub`) ke folder `~/.ssh/`.
```bash
ssh-keygen -t rsa -b 4096 -f  ~/.ssh/gitkey
```
Ada kemungkinan bahwa Anda memiliki kunci SSH lebih banyak di komputer lokal Anda, jadi konfigurasikan klien SSH untuk mengetahui kunci privat SSH yang akan digunakan ketika tersambung ke server Git Anda.

Buat file konfigurasi SSH di **Komputer Lokal** Anda:
```bash
touch ~/.ssh/config
```
Buka file dan tambahkan pintasan ke server Git Anda. Ini harus berisi direktif `HostName` (menunjuk ke nama host server Git Anda) dan direktif `IdentityFile` (menunjuk ke jalur file dari kunci SSH yang baru Anda buat:

```bash
Host mygitserver.com
    HostName mygitserver.com
    IdentityFile ~/.ssh/gitkey
```
Simpan dan tutup file, lalu batasi izinnya:
```bash
chmod 600 ~/.ssh/config
```
Sekarang klien SSH Anda akan tahu kunci pribadi mana yang digunakan untuk terhubung ke server Git.

Tampilkan konten file kunci publik Anda dengan perintah berikut:
```bash
cat ~/.ssh/gitkey.pub
```
Salin hasilnya dan tambahkan kunci publik ke server Git Anda.

Jika Anda menggunakan layanan hosting Git, baca dokumentasinya tentang cara menambahkan kunci SSH ke akun Anda:
 * [Add SSH keys to GitHub](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/)
 * [Add SSH keys to GitLab](https://docs.gitlab.com/ee/gitlab-basics/create-your-ssh-keys.html)
 * [Add SSH keys to Bitbucket](https://confluence.atlassian.com/bitbucket/set-up-an-ssh-key-728138079.html)

Sekarang Anda akan dapat terhubung ke server Git Anda dengan mesin lokal Anda. Uji koneksi dengan perintah berikut:
```bash
ssh -T git@mygitserver.com
```
Jika perintah ini menghasilkan kesalahan, periksa bahwa Anda telah menambahkan kunci SSH Anda dengan benar dengan merujuk pada dokumentasi layanan hosting Git Anda dan coba hubungkan lagi.

Sebelum mendorong aplikasi ke repositori Git jarak jauh dan menerapkannya, pertama-tama konfigurasikan server produksi.

### Mengkonfigurasi Pengguna Penggambaran
Deployer menggunakan protokol SSH untuk secara aman menjalankan perintah di server. Karena alasan ini, langkah pertama yang akan kita ambil untuk mengonfigurasi server produksi adalah membuat pengguna yang dapat digunakan Deployer untuk masuk dan menjalankan perintah di server Anda melalui SSH.

Masuk ke server LEMP Anda dengan pengguna non-root sudo dan buat pengguna baru bernama "deployer" dengan perintah berikut:
```bash
sudo adduser deployer
```
Laravel memerlukan beberapa direktori yang dapat ditulis untuk menyimpan file dan upload yang di-cache, sehingga direktori yang dibuat oleh pengguna **deployer** harus dapat ditulis oleh server web Nginx. Tambahkan pengguna ke grup `www-data` untuk melakukan ini:

```bash
sudo usermod -aG www-data deployer
```
Izin default untuk file yang dibuat oleh pengguna deployer harus `644` untuk file dan `755` untuk direktori. Dengan cara ini, pengguna deployer akan dapat membaca dan menulis file, sementara grup dan pengguna lain akan dapat membacanya.

Lakukan ini dengan mengatur umask default deploy ke **022**:
```bash
sudo chfn -o umask=022 deployer
```
Kami akan menyimpan aplikasi di direktori `/var/www/html/`, jadi ubah kepemilikan direktori ke pengguna **deployer** dan grup **www-data**.
```bash
sudo chown deployer:www-data /var/www/html
```
Pengguna deployer harus dapat memodifikasi file dan folder dalam direktori `/var/www/html`. Karena itu, semua file dan subdirektori baru yang dibuat dalam direktori `/var/www/html` harus mewarisi id grup folder **(www-data)**. Untuk mencapai ini, atur ID grup pada direktori ini dengan perintah berikut:
```bash
sudo chmod g+s /var/www/html
```
Deployer akan mengkloning repo Git ke server produksi menggunakan SSH, jadi Anda ingin memastikan bahwa koneksi antara server LEMP Anda dan server Git aman. Kami akan menggunakan pendekatan yang sama yang kami gunakan untuk komputer lokal kami, dan kami akan menghasilkan kunci SSH untuk pengguna **deployer**.

Beralih ke pengguna **deployer** di server Anda:
```bash
su - deployer
```
Selanjutnya, buat pasangan kunci SSH sebagai pengguna deployer. Kali ini, Anda dapat menerima nama file default dari kunci SSH:
```bash
ssh-keygen -t rsa -b 4096
```
Tampilkan kunci publik:
```bash
cat ~/.ssh/id_rsa.pub
```
Salin kunci publik dan tambahkan ke server Git seperti yang Anda lakukan pada langkah sebelumnya.

Mesin lokal Anda akan berkomunikasi dengan server menggunakan SSH juga, jadi Anda harus menghasilkan kunci SSH untuk pengguna **deployer** di komputer lokal Anda dan menambahkan kunci publik ke server.

Di **Komputer Lokal** Anda jalankan perintah berikut. Jangan ragu untuk mengganti `deployerkey` dengan nama file pilihan Anda:
```bash
ssh-keygen -t rsa -b 4096 -f  ~/.ssh/deployerkey
```
Salin keluaran perintah berikut yang berisi kunci publik:
```bash
cat ~/.ssh/deployerkey.pub
```
Di **Server Anda** saat pengguna **deployer** menjalankan yang berikut:
```bash
nano ~/.ssh/authorized_keys
```
Tempel kunci publik ke editor dan tekan `CTRL-X`, `Y`, lalu `ENTER` untuk menyimpan dan keluar.

Batasi izin file:
```bash
chmod 600 ~/.ssh/authorized_keys
```
Sekarang beralih kembali ke pengguna sudo:
```bash
exit
```
Sekarang server Anda dapat terhubung ke server Git dan Anda dapat masuk ke server dengan pengguna **deployer** dari komputer lokal Anda.

Masuk dari komputer lokal Anda ke server Anda sebagai pengguna **deployer** untuk menguji koneksi:

```bash
ssh deployer@your_server_ip  -i ~/.ssh/deployerkey
```
Setelah Anda masuk sebagai deployer, uji koneksi antara server Anda dan server `Git` juga:
```bash
ssh -T git@mygitserver.com
```
Akhirnya, keluar dari server:
```bash
exit
```
Dari sini, kita dapat melanjutkan untuk mengkonfigurasi Nginx dan MySQL di server web kami.

### Mengkonfigurasi Nginx
Kami sekarang siap untuk mengkonfigurasi server web yang akan melayani aplikasi. Ini akan melibatkan konfigurasi akar dokumen dan struktur direktori yang akan kita gunakan untuk menyimpan file Laravel. Kami akan mengatur Nginx untuk melayani file kami dari direktori `/var/www/laravel`.

Pertama, kita perlu membuat file konfigurasi blok server untuk situs baru.

Masuk ke server sebagai pengguna sudo Anda dan buat file konfigurasi baru. Ingat untuk mengganti example.com dengan nama domain Anda sendiri:
```bash
sudo nano /etc/nginx/sites-available/example.com
```
Tambahkan blok server ke bagian atas file konfigurasi:
```nginx
#example.com '>/etc/nginx/sites-available/example.com

server {
        listen 80;
        listen [::]:80;

        root /var/www/html/laravel-app/current/public;
        index index.php index.html index.htm index.nginx-debian.html;

        server_name example.com www.example.com;
}
```
Kedua `listen` arahan di bagian atas memberitahu Nginx port mana untuk didengarkan, dan direktif `root` mendefinisikan document root tempat Laravel akan diinstal. `current/public` di jalur direktori root adalah tautan simbolik yang menunjuk ke rilis terbaru dari aplikasi. Dengan menambahkan direktif `index`, kami memberi tahu Nginx untuk melayani file `index.php` terlebih dahulu sebelum mencari rekan HTML mereka saat meminta lokasi direktori. Direktif `server_name` harus diikuti oleh domain Anda dan alias apa pun.

Kami juga harus memodifikasi cara Nginx menangani permintaan. Ini dilakukan melalui direktif `try_files`. Kami ingin mencoba melayani permintaan sebagai file terlebih dahulu dan, jika tidak dapat menemukan file dengan nama yang benar, harus mencoba untuk melayani file indeks default untuk direktori yang sesuai dengan permintaan. Kegagalan ini, harus melewati permintaan ke file `index.php` sebagai parameter query.

```nginx
# example.com '>/etc/nginx/sites-available/example.com

server {
        listen 80;
        listen [::]:80;

        root /var/www/html/laravel-app/current/public;
        index index.php index.html index.htm index.nginx-debian.html;

        server_name example.com www.example.com;

        location / {
                try_files $uri $uri/ /index.php?$query_string;
        }
```
Selanjutnya, kita perlu membuat blok yang menangani eksekusi sebenarnya dari setiap file PHP. Ini akan berlaku untuk semua file yang diakhiri dengan .php. Ini akan mencoba file itu sendiri dan kemudian mencoba untuk meneruskannya sebagai parameter ke file `index.php`.

Kami akan mengatur arahan `fastcgi` untuk memberi tahu Nginx untuk menggunakan jalur aplikasi yang sebenarnya (diselesaikan setelah mengikuti tautan simbolis), alih-alih tautan simbolis. Jika Anda tidak menambahkan baris ini ke konfigurasi, jalur tempat tautan simbolik akan di-cache, artinya versi lama aplikasi Anda akan dimuat setelah penyebaran. Tanpa arahan ini, Anda harus membersihkan cache secara manual setelah setiap penerapan dan permintaan ke aplikasi Anda berpotensi gagal. Selain itu, `fastcgi_pass` directive akan memastikan bahwa Nginx menggunakan soket yang digunakan php7-fpm untuk komunikasi dan file `index.php` digunakan sebagai indeks untuk operasi ini.

```nginx
# example.com '>/etc/nginx/sites-available/example.com
server {
        listen 80;
        listen [::]:80;

        root /var/www/html/laravel-app/current/public;
        index index.php index.html index.htm index.nginx-debian.html;

        server_name example.com www.example.com;

        location / {
                try_files $uri $uri/ /index.php?$query_string;
        }


        location ~ \.php$ {
                include snippets/fastcgi-php.conf;

                fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
                fastcgi_param DOCUMENT_ROOT $realpath_root;

                fastcgi_pass unix:/run/php/php7.0-fpm.sock;

        }
```
Akhirnya, kami ingin memastikan bahwa Nginx tidak mengizinkan akses ke file `.htaccess` tersembunyi. Kami akan melakukan ini dengan menambahkan satu blok lokasi tambahan yang disebut `location ~ / \ an.ht` dan, dalam blok itu, direktif yang menentukan `deny all;`.

Setelah menambahkan blok lokasi terakhir ini, file konfigurasi akan terlihat seperti ini:

```nginx
example.com '>/etc/nginx/sites-available/example.com
server {
        listen 80;
        listen [::]:80;

        root /var/www/html/laravel-app/current/public;
        index index.php index.html index.htm index.nginx-debian.html;

        server_name example.com www.example.com;

        location / {
                try_files $uri $uri/ /index.php?$query_string;
        }


        location ~ \.php$ {
                include snippets/fastcgi-php.conf;

                fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
                fastcgi_param DOCUMENT_ROOT $realpath_root;

                fastcgi_pass unix:/run/php/php7.0-fpm.sock;

        }

        location ~ /\.ht {
                deny all;
        }

}
```
Simpan dan tutup file (`CTRL-X`, `Y`, lalu `ENTER`), dan kemudian aktifkan blok server baru dengan membuat tautan simbolis ke direktori yang `sites-enabled` :
```bash
sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/
```
Uji file konfigurasi Anda untuk kesalahan sintaks:
```bash
sudo nginx -t
```
Jika Anda melihat kesalahan apa pun, kembali dan periksa kembali file Anda sebelum melanjutkan. Mulai ulang Nginx untuk mendorong perubahan yang diperlukan:
```bash
sudo systemctl restart nginx
```
Server Nginx sekarang dikonfigurasi. Selanjutnya, kita akan mengkonfigurasi database MySQL aplikasi.

### Mengkonfigurasi MySQL
Setelah instalasi, MySQL menciptakan pengguna root secara default. Pengguna ini memiliki hak yang tidak terbatas, jadi ini adalah praktik keamanan yang buruk untuk menggunakan pengguna root untuk database aplikasi Anda. Sebagai gantinya, kami akan membuat database untuk aplikasi dengan pengguna yang berdedikasi.

Masuk ke konsol MySQL sebagai root:
