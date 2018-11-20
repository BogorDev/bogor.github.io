---
layout: post
title: Pengaturan Awal Server dengan Debian 9
date: 2018-09-22 14:39 +0700
author: Iman Sugirman
cover: /assets/images/initial-server.png
image: https://res.cloudinary.com/imans/image/upload/v1537604076/blog/fabian-grohs-589017-unsplash.jpg
header:
    layout: hero
    vh: vh-50 vh-50-m vh-80-l
    navigation: fancy
    image: https://res.cloudinary.com/imans/image/upload/v1537604076/blog/fabian-grohs-589017-unsplash.jpg
    caption: "[Fabian Grohs](https://unsplash.com/@grohsfabian)"
    class: bg-darken-20 bg-darken-25-l
author_bio: true
categories:
    - Tutorial
    - Operating System
    - Debian
tags:
    - Debian 9
    - Setup
---
Saat pertama kali membuat server Debian 9 baru, ada beberapa langkah konfigurasi yang harus Anda ambil sejak awal sebagai bagian dari pengaturan dasar. Ini akan meningkatkan keamanan dan kegunaan server Anda dan akan memberi Anda dasar yang kuat untuk tindakan selanjutnya.

### Masuk sebagai Root
Untuk masuk ke server Anda, Anda harus mengetahui alamat IP publik server Anda. Anda juga akan memerlukan kata sandi atau, jika Anda menginstal kunci SSH untuk otentikasi, kunci privat untuk akun pengguna root. Jika Anda belum masuk ke server Anda, Anda mungkin ingin mengikuti panduan kami tentang cara menyambungkan ke Droplet Anda dengan SSH, yang mencakup proses ini secara detail.

Jika Anda belum terhubung ke server Anda, lanjutkan dan masuk sebagai pengguna root menggunakan perintah berikut (ganti bagian yang disorot dari perintah dengan alamat IP publik server Anda):
```bash
ssh root@your_server_ip
```
Terima peringatan tentang keaslian host jika muncul. Jika Anda menggunakan otentikasi kata sandi, berikan kata sandi root Anda untuk login. Jika Anda menggunakan kunci SSH yang dilindungi kata sandi, Anda mungkin diminta memasukkan kata sandi pertama kali Anda menggunakan kunci setiap sesi. Jika ini pertama kalinya Anda masuk ke server dengan kata sandi, Anda mungkin juga diminta untuk mengubah kata sandi root.

#### Tentang Root
Pengguna root adalah pengguna administratif di lingkungan Linux yang memiliki hak yang sangat luas. Karena hak istimewa yang tinggi dari akun root, Anda tidak disarankan menggunakannya secara teratur. Ini karena bagian dari kekuatan yang melekat pada akun root adalah kemampuan untuk membuat perubahan yang sangat merusak, bahkan karena kecelakaan.

Langkah selanjutnya adalah membuat akun pengguna alternatif dengan lingkup pengaruh yang berkurang untuk pekerjaan sehari-hari. Kami akan mengajari Anda cara mendapatkan peningkatan hak istimewa pada saat Anda membutuhkannya.

### Membuat Pengguna Baru
Setelah Anda masuk sebagai root, kami siap menambahkan akun pengguna baru yang akan kami gunakan untuk masuk dari sekarang.

Catatan: Di beberapa lingkungan, paket bernama unscd dapat diinstal secara default untuk mempercepat permintaan ke server nama seperti LDAP. Versi terbaru yang saat ini tersedia di Debian berisi bug yang menyebabkan perintah tertentu (seperti perintah adduser di bawah) untuk menghasilkan output tambahan yang terlihat seperti ini:
sent invalidate(passwd) request, exiting
sent invalidate(group) request, exiting
Pesan-pesan ini tidak berbahaya, tetapi jika Anda ingin menghindarinya, adalah aman untuk menghapus paket tidak tetap jika Anda tidak berencana menggunakan sistem seperti LDAP untuk informasi pengguna:
apt remove unscd
{: .alert .alert--warning}

Contoh ini membuat pengguna baru bernama **asep**, tetapi Anda harus menggantinya dengan nama pengguna yang Anda sukai:
```bash
adduser asep
```
Anda akan ditanya beberapa pertanyaan, dimulai dengan kata sandi akun.

Masukkan kata sandi yang kuat dan, secara opsional, isi salah satu informasi tambahan jika Anda mau. Ini tidak diperlukan dan Anda cukup menekan `ENTER` di bidang apa pun yang ingin Anda lewati.

### Pemberian Hak Istimewa Administratif
Sekarang, kami memiliki akun pengguna baru dengan hak istimewa akun reguler. Namun, kadang-kadang kita perlu melakukan tugas-tugas administratif.

Untuk menghindari keluar dari pengguna normal dan masuk kembali sebagai akun root, kita dapat mengatur apa yang dikenal sebagai "superuser" atau hak akses root untuk akun normal kita. Ini akan memungkinkan pengguna normal kami untuk menjalankan perintah dengan hak akses administratif dengan meletakkan kata sudo di depan setiap perintah.

Untuk menambahkan hak istimewa ini ke pengguna baru kami, kami perlu menambahkan pengguna baru ke grup sudo. Secara default, pada Debian 9, pengguna yang termasuk dalam grup sudo diizinkan untuk menggunakan perintah sudo.

Sebagai root, jalankan perintah ini untuk menambahkan pengguna baru Anda ke grup sudo (gantikan kata yang disorot dengan pengguna baru Anda):

```bash
usermod -aG sudo asep
```
Sekarang, saat masuk sebagai pengguna biasa Anda, Anda bisa mengetikkan `sudo` sebelum perintah untuk melakukan tindakan dengan hak superuser.

### Menyiapkan Firewall Dasar
Server Debian dapat menggunakan firewall untuk memastikan hanya koneksi ke layanan tertentu yang diizinkan. Meskipun firewall `iptables` diinstal secara default, Debian tidak merekomendasikan firewall tertentu. Dalam panduan ini, kami akan menginstal dan menggunakan firewall `UFW` untuk membantu mengatur kebijakan dan mengelola pengecualian.

Kita dapat menggunakan manajer paket `apt` untuk menginstal `UFW`. Perbarui indeks lokal untuk mengambil informasi terbaru tentang paket yang tersedia dan kemudian instal firewall dengan mengetik:
```bash
apt update
apt install ufw
```
Catatan: Jika server Anda berjalan di DigitalOcean, Anda dapat secara opsional menggunakan Firewall DigitalOcean Cloud daripada firewall UFW. Kami menyarankan untuk menggunakan hanya satu firewall pada satu waktu untuk menghindari aturan yang bertentangan yang mungkin sulit untuk didebug.
{: .alert .alert--warning}

Profil firewall memungkinkan UFW mengelola serangkaian aturan firewall untuk aplikasi berdasarkan nama. Profil untuk beberapa perangkat lunak umum dibundel dengan UFW secara default dan paket dapat mendaftarkan profil tambahan dengan UFW selama proses instalasi. OpenSSH, layanan yang memungkinkan kita untuk terhubung ke server kami sekarang, memiliki profil firewall yang bisa kita gunakan.

Anda dapat melihat ini dengan mengetik:
```bash
ufw app list
```
Outputnya :
```
Output
Available applications:
  . . .
  OpenSSH
  . . .
```
Kita perlu memastikan bahwa firewall memungkinkan koneksi SSH sehingga kita dapat masuk kembali di lain waktu. Kami dapat mengizinkan koneksi ini dengan mengetik:
```bash
ufw allow OpenSSH
```
Setelah itu, kita dapat mengaktifkan firewall dengan mengetik:
```bash
ufw enable
```
Ketik "**Y**" dan tekan `ENTER` untuk melanjutkan. Anda dapat melihat bahwa koneksi SSH masih diizinkan dengan mengetik:
```bash
ufw status
```
Dan Outputnya :
```
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere
OpenSSH (v6)               ALLOW       Anywhere (v6)
```
Karena **firewall saat ini memblokir semua koneksi kecuali SSH**, jika Anda menginstal dan mengkonfigurasi layanan tambahan, Anda perlu menyesuaikan pengaturan firewall untuk memungkinkan lalu lintas yang masuk. Anda dapat mempelajari beberapa operasi UFW umum dalam panduan ini.

### Mengaktifkan Akses Eksternal untuk Pengguna Reguler Anda
Sekarang kita memiliki pengguna biasa untuk penggunaan sehari-hari, kita perlu memastikan bahwa kita dapat SSH ke akun secara langsung.

Catatan: Sampai memverifikasi bahwa Anda dapat masuk dan menggunakan sudo dengan pengguna baru Anda, kami sarankan tetap masuk sebagai root. Dengan cara ini, jika Anda memiliki masalah, Anda dapat memecahkan masalah dan membuat perubahan yang diperlukan sebagai root. Jika Anda menggunakan Tetesan DigitalOcean dan mengalami masalah dengan koneksi SSH root Anda, Anda dapat masuk ke Droplet menggunakan Konsol DigitalOcean.
{: .alert .alert--warning}

Proses untuk mengkonfigurasi akses SSH untuk pengguna baru Anda tergantung pada apakah akun root server Anda menggunakan kata sandi atau kunci SSH untuk otentikasi.

#### Jika Akun Root Menggunakan Otentikasi Kata Sandi
Jika Anda masuk ke akun root Anda menggunakan kata sandi, maka otentikasi kata sandi diaktifkan untuk SSH. Anda dapat SSH ke akun pengguna baru Anda dengan membuka sesi terminal baru dan menggunakan SSH dengan nama pengguna baru Anda:
```bash
ssh asep@your_server_ip
```
Setelah memasukkan kata sandi pengguna biasa Anda, Anda akan masuk. Ingat, jika Anda perlu menjalankan perintah dengan hak akses administratif, ketik sudo sebelum seperti ini:
```bash
sudo command_to_run
```
Anda akan diminta untuk kata sandi pengguna biasa Anda ketika menggunakan sudo untuk pertama kalinya setiap sesi (dan secara berkala sesudahnya).

Untuk meningkatkan keamanan server Anda, **kami sangat menyarankan untuk mengatur kunci SSH daripada menggunakan otentikasi kata sandi**. Ikuti panduan kami tentang cara mengatur kunci SSH di Debian 9 untuk mempelajari cara mengkonfigurasi otentikasi berbasis kunci.

### Jika Akun Root Menggunakan SSH Key Authentication
Jika Anda masuk ke akun root Anda menggunakan kunci SSH, maka otentikasi kata sandi dinonaktifkan untuk SSH. Anda perlu menambahkan salinan kunci publik lokal Anda ke file `~/.ssh/authorized_keys` pengguna baru untuk masuk dengan sukses.

Karena kunci publik Anda sudah ada di file `~/.ssh/authorized_keys` akun root pada server, kita dapat menyalin file dan struktur direktori tersebut ke akun pengguna baru kita di sesi yang ada dengan perintah cp. Setelah itu, kita dapat menyesuaikan kepemilikan file menggunakan perintah chown.

Pastikan untuk mengubah bagian perintah yang disorot di bawah ini untuk mencocokkan nama pengguna biasa Anda:

```bash
cp -r ~/.ssh /home/asep
chown -R asep:asep /home/asep/.ssh
```
Sekarang, buka sesi terminal baru dan gunakan SSH dengan nama pengguna baru Anda:
```bash
ssh asep@your_server_ip
```
Anda harus masuk ke akun pengguna baru tanpa menggunakan kata sandi. Ingat, jika Anda perlu menjalankan perintah dengan hak akses administratif, ketik sudo sebelum seperti ini:
```bash
sudo command_to_run
```
Anda akan diminta untuk kata sandi pengguna biasa Anda ketika menggunakan sudo untuk pertama kalinya setiap sesi (dan secara berkala sesudahnya).

### Menyelesaikan Konfigurasi Opsional
Sekarang kita memiliki konfigurasi dasar yang kuat, kita dapat mempertimbangkan beberapa langkah opsional untuk membuat sistem lebih mudah diakses. Bagian berikut ini mencakup beberapa tambahan tweak yang difokuskan pada kegunaan.

#### Install Halaman manual
Debian menyediakan manual yang luas untuk sebagian besar perangkat lunak dalam bentuk halaman manual. Namun, perintah man tidak selalu disertakan secara default pada instalasi minimal.

Instal paket `man-db` untuk menginstal perintah `man` dan database manual:
```bash
sudo apt install man-db
```
Sekarang, untuk melihat manual untuk komponen, Anda dapat mengetik:
```bash
man command
```
Misalnya, untuk melihat manual untuk perintah teratas, ketik:
```bash
man top
```
Kebanyakan paket di repositori Debian termasuk halaman manual sebagai bagian dari instalasi mereka.

### Mengubah Editor Default
Debian menawarkan berbagai macam editor teks, beberapa di antaranya termasuk dalam sistem dasar. Perintah dengan dukungan editor terintegrasi, seperti pengeditan `visudo` dan `systemctl edit`, kirim teks ke perintah `editor`, yang dipetakan ke editor default sistem. Mengatur editor default sesuai dengan preferensi Anda dapat membantu Anda mengkonfigurasi sistem Anda lebih mudah dan menghindari frustrasi.

Jika editor pilihan Anda tidak diinstal secara default, gunakan `apt` to install it first:
```bash
sudo apt install your_preferred_editor
```
Selanjutnya, Anda dapat melihat default saat ini dan mengubah pilihan menggunakan perintah update-alternate:
```bash
sudo update-alternatives --config editor
```
Perintah menampilkan tabel editor yang diketahui dengan prompt untuk mengubah default:
```
There are 8 choices for the alternative editor (providing /usr/bin/editor).

  Selection    Path                Priority   Status
------------------------------------------------------------
* 0            /usr/bin/joe         70        auto mode
  1            /bin/nano            40        manual mode
  2            /usr/bin/jmacs       50        manual mode
  3            /usr/bin/joe         70        manual mode
  4            /usr/bin/jpico       50        manual mode
  5            /usr/bin/jstar       50        manual mode
  6            /usr/bin/rjoe        25        manual mode
  7            /usr/bin/vim.basic   30        manual mode
  8            /usr/bin/vim.tiny    15        manual mode

Press <enter> to keep the current choice[*], or type selection number:
```
