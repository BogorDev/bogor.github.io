---
layout: post
title: Cara Install Gitlab CE ( Community Edition ) di Ubuntu 16.04
date: 2018-09-20 09:46 +0700
author: Iman Sugirman
thumbnail: https://res.cloudinary.com/imans/image/upload/v1537411705/blog/fatos-bytyqi-535528-unsplash.jpg
header:
    layout: hero
    vh: vh-50 vh-50-m vh-80-l
    navigation: fancy
    image: https://res.cloudinary.com/imans/image/upload/v1537411705/blog/fatos-bytyqi-535528-unsplash.jpg
    caption: "[Free Pik](https://www.freepik.com/)"
    class: bg-darken-20 bg-darken-25-l
author_bio: true
categories:
    - Tutorial
    - Code Hosting
    - Ubuntu
tags:
    - Gitlab
    - Community
---
GitLab CE, atau Community Edition, adalah aplikasi sumber terbuka yang terutama digunakan untuk meng-host repositori Git, dengan fitur-fitur terkait pengembangan tambahan seperti pelacakan masalah.

Perangkat ini dirancang untuk dihosting menggunakan infrastruktur Kamu sendiri, dan memberikan fleksibilitas dalam penyebaran sebagai penyimpanan repositori internal untuk tim pengembangan Kamu, secara publik sebagai cara untuk berinteraksi dengan pengguna, atau bahkan terbuka sebagai cara bagi para kontributor untuk meng-host proyek mereka sendiri.

Proyek GitLab membuatnya relatif lurus ke depan untuk mengatur instance GitLab pada perangkat keras Kamu sendiri dengan mekanisme pemasangan yang mudah. Dalam panduan ini, kami akan membahas cara menginstal dan mengkonfigurasi GitLab pada server Ubuntu 16.04.

### Prasyarat
Tutorial ini akan mengasumsikan bahwa Kamu memiliki akses ke server Ubuntu 16.04 yang baru. [Persyaratan perangkat keras GitLab yang dipublikasikan](http://docs.gitlab.com/ee/install/requirements.html#hardware-requirements) menyarankan penggunaan server dengan:

 * 2 core
 * 4GB RAM

Meskipun Kamu mungkin bisa bertahan dengan mengganti beberapa ruang swap untuk RAM, itu tidak disarankan. Untuk panduan ini kami akan menganggap Kamu memiliki sumber daya di atas seminimal mungkin.

Untuk memulai, Kamu memerlukan pengguna non-root dengan akses sudo yang dikonfigurasi di server. Ini juga merupakan ide yang baik untuk mengatur firewall dasar untuk memberikan lapisan keamanan tambahan. Kamu dapat mengikuti langkah-langkah di [Panduan Penyiapan awal server Ubuntu 16.04](/initial-server-setup-with-ubuntu-16-04) kami untuk mendapatkan pengaturan ini.

Setelah Kamu memenuhi prasyarat di atas, lanjutkan untuk memulai prosedur instalasi.

### Install Dependensi
Sebelum kita dapat menginstal GitLab sendiri, penting untuk menginstal beberapa perangkat lunak yang digunakan selama instalasi dan secara berkelanjutan. Untungnya, semua perangkat lunak yang diperlukan dapat dengan mudah diinstal dari repositori paket bawaan Ubuntu.

Karena ini adalah pertama kalinya kami menggunakan `apt` selama sesi ini, kami dapat menyegarkan indeks paket lokal dan kemudian menginstal dependensi dengan mengetik:

```bash
sudo apt-get update
sudo apt-get install ca-certificates curl openssh-server postfix
```
Kamu mungkin sudah menginstal beberapa perangkat lunak ini. Untuk instalasi `postfix`, pilih `Situs Internet` ketika diminta. Pada layar berikutnya, masukkan nama domain server atau alamat IP Kamu untuk mengonfigurasi bagaimana sistem akan mengirim email.

### Install GitLab
Sekarang dependensi sudah ada, kita dapat menginstal GitLab sendiri. Ini adalah proses lurus ke depan yang memanfaatkan skrip instalasi untuk mengonfigurasi sistem Kamu dengan repositori GitLab.

Pindah ke direktori `/tmp` lalu unduh skrip instalasi:
```bash
cd /tmp
curl -LO https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh
```
Jangan ragu untuk memeriksa skrip yang diunduh untuk memastikan Kamu merasa nyaman dengan tindakan yang akan dilakukan. Kamu juga dapat menemukan versi naskah yang dihosting [Disini](https://packages.gitlab.com/gitlab/gitlab-ce/install) :
```bash
less /tmp/script.deb.sh
```
Setelah Kamu puas dengan keamanan skrip, jalankan penginstal:
```bash
sudo bash /tmp/script.deb.sh
```
Skrip akan mengatur server Anda untuk menggunakan repositori yang dipelihara GitLab. Ini memungkinkan Anda mengelola GitLab dengan alat manajemen paket yang sama yang Anda gunakan untuk paket sistem lainnya. Setelah ini selesai, Anda dapat menginstal aplikasi GitLab yang sebenarnya dengan apt:
```bash
sudo apt-get install gitlab-ce
```
Ini akan menginstal komponen yang diperlukan pada sistem Anda.

### Menyesuaikan Aturan Firewall
Sebelum Anda mengkonfigurasi GitLab, Anda harus memastikan bahwa aturan firewall Anda cukup permisif untuk memungkinkan lalu lintas web. Jika Anda mengikuti panduan yang ditautkan dalam prasyarat, Anda akan mengaktifkan firewall ufw.

Lihat status terkini dari firewall aktif Anda dengan mengetik:
```bash
sudo ufw status
```
Outputnya :
```
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere                  
OpenSSH (v6)               ALLOW       Anywhere (v6)
```
Seperti yang Anda lihat, aturan saat ini memungkinkan lalu lintas SSH melalui, tetapi akses ke layanan lain dibatasi. Karena GitLab adalah aplikasi web, kita harus mengizinkan akses HTTP. Jika Anda memiliki nama domain yang terkait dengan server GitLab Anda, GitLab juga dapat meminta dan mengaktifkan sertifikat TLS / SSL gratis dari [Proyek Let's Encrypt](https://letsencrypt.org/) untuk mengamankan instalasi Anda. Kami juga ingin mengizinkan akses HTTPS dalam kasus ini.

Karena pemetaan protokol ke port untuk HTTP dan HTTPS tersedia di file / etc / services, kita dapat mengizinkan lalu lintas masuk berdasarkan nama. Jika Anda belum mengaktifkan lalu lintas OpenSSH, Anda harus memperbolehkan lalu lintas itu sekarang juga:
```bash
sudo ufw allow http
sudo ufw allow https
sudo ufw allow OpenSSH
```
Jika Anda memeriksa kembali perintah `status ufw`, Anda akan melihat akses yang dikonfigurasi untuk setidaknya dua layanan ini:
```bash
sudo ufw status
```
dan Outputnya :
```
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere                  
80                         ALLOW       Anywhere                  
443                        ALLOW       Anywhere                  
OpenSSH (v6)               ALLOW       Anywhere (v6)             
80 (v6)                    ALLOW       Anywhere (v6)             
443 (v6)                   ALLOW       Anywhere (v6)
```
kami mengkonfigurasi aplikasi.

### Mengedit File Konfigurasi GitLab
Sebelum Anda dapat menggunakan aplikasi, Anda perlu memperbarui satu file konfigurasi dan menjalankan perintah konfigurasi ulang. Pertama, buka file konfigurasi Gitlab:
```bash
sudo nano /etc/gitlab/gitlab.rb
```
Di dekat bagian atas adalah baris konfigurasi `external_url`. Perbarui untuk mencocokkan domain Anda sendiri atau alamat IP. Jika Anda memiliki domain, ubah `http` ke `https` sehingga GitLab akan secara otomatis mengalihkan pengguna ke situs yang dilindungi oleh sertifikat Let's Encrypt yang akan kami minta.

```
# If your GitLab server does not have a domain name, you will need to use an IP
# address instead of a domain and keep the protocol as `http`.
external_url 'https://yourdomain'
```
Selanjutnya, jika server GitLab Anda memiliki nama domain, cari file untuk pengaturan `letsencrypt ['enable']`. Hapus tanda komentar dan atur ke true. Ini akan memberi tahu GitLab untuk meminta sertifikat Let's Encrypt untuk domain GitLab Anda dan mengonfigurasi aplikasi untuk melayani lalu lintas dengannya.

Di bawah itu, cari pengaturan `letsencrypt ['contact_emails']`. Pengaturan ini menentukan daftar alamat email yang dapat digunakan proyek Let's Encrypt untuk menghubungi Anda jika ada masalah dengan domain Anda. Sebaiknya hapus tanda komentar dan isi ini juga agar Anda tahu masalah apa pun:
```nginx
letsencrypt['enable'] = true
letsencrypt['contact_emails'] = ['asep@yourdomain.com']
```
Simpan dan tutup file. Sekarang, jalankan perintah berikut untuk mengkonfigurasi ulang Gitlab:
```bash
sudo gitlab-ctl reconfigure
```
Ini akan menginisialisasi GitLab menggunakan informasi yang dapat ditemukan tentang server Anda. Ini adalah proses yang sepenuhnya otomatis, jadi Anda tidak perlu menjawab setiap permintaan. Jika Anda mengaktifkan integrasi Let's Encrypt, sertifikat harus dikonfigurasi untuk domain Anda.

### Melakukan Konfigurasi Awal Melalui Antarmuka Web
Sekarang setelah GitLab berjalan dan akses diizinkan, kami dapat melakukan beberapa konfigurasi awal aplikasi melalui antarmuka web.

#### Masuk untuk Pertama Kalinya
Kunjungi nama domain server GitLab Anda di browser web Anda:

```
http://gitlab_domain_or_IP
```
Jika Anda mengaktifkan `Let's Encrypt` dan menggunakan https di `external_url` Anda, Anda harus dialihkan ke koneksi HTTPS yang aman.

Pada kunjungan pertama Anda, Anda akan melihat prompt awal untuk menetapkan kata sandi untuk akun administratif:

![Gitlab Preview](https://res.cloudinary.com/imans/image/upload/v1537434354/blog/gitlab_initial_password2.png)

Di prompt kata sandi awal, berikan dan konfirmasikan kata sandi aman untuk akun administratif. Klik tombol Ubah kata sandi Anda setelah Anda selesai.

Anda akan dialihkan ke halaman login GitLab konvensional:

![Gitlab Konvensional](https://res.cloudinary.com/imans/image/upload/v1537434429/blog/gitlab_first_signin2.png)

Di sini, Anda dapat masuk dengan kata sandi yang baru saja Anda setel. Kredensial adalah:

 * Nama pengguna: root
 * Kata Sandi: [kata sandi yang Anda atur]

Masukkan nilai-nilai ini ke dalam bidang untuk pengguna yang ada dan klik tombol Masuk. Anda akan masuk ke aplikasi dan dibawa ke halaman landing yang meminta Anda untuk mulai menambahkan proyek:

![Gitlab Landing Page](https://res.cloudinary.com/imans/image/upload/v1537434514/blog/landing_page2.png)

Anda sekarang dapat membuat beberapa perubahan sederhana untuk mengatur GitLab sesuai keinginan Anda.

### Menyesuaikan Pengaturan Profil Anda
Salah satu hal pertama yang harus Anda lakukan setelah instalasi baru adalah membuat profil Anda menjadi lebih baik. GitLab memilih beberapa default yang masuk akal, tetapi ini biasanya tidak tepat setelah Anda mulai menggunakan perangkat lunak.

Untuk membuat modifikasi yang diperlukan, klik ikon pengguna di sudut kanan atas antarmuka. Di menu tarik-turun yang muncul, pilih Pengaturan:

![Profile Setting](https://res.cloudinary.com/imans/image/upload/v1537434653/blog/profile_settings_button2.png)

Anda akan dibawa ke bagian Profil pada pengaturan Anda:
![Profile Setting 2](https://res.cloudinary.com/imans/image/upload/v1537434868/blog/profile_settings2.png)

Sesuaikan Nama dan alamat Email dari "Administrator" dan "admin@example.com" menjadi sesuatu yang lebih akurat. Nama yang Anda pilih akan ditampilkan kepada pengguna lain, sementara email akan digunakan untuk deteksi avatar default, pemberitahuan, tindakan Git melalui antarmuka, dll.

Klik tombol Pengaturan Pembaruan Profil di bagian bawah saat Anda selesai:

![Update Profile](https://res.cloudinary.com/imans/image/upload/v1537434919/blog/update_profile_settings_button2.png)

Email konfirmasi akan dikirim ke alamat yang Anda berikan. Ikuti petunjuk dalam email untuk mengonfirmasi akun Anda sehingga Anda dapat mulai menggunakannya dengan GitLab.

### Mengubah Nama Akun Anda
Selanjutnya, klik pada item Akun di bilah menu sebelah kiri:
![Profile Gitlab Setting](https://res.cloudinary.com/imans/image/upload/v1537435033/blog/account_menu_item2.png)

Di sini, Anda dapat menemukan token API pribadi Anda atau mengonfigurasi autentikasi dua faktor. Namun, fungsi yang kami minati saat ini adalah bagian Ubah nama pengguna.

Secara default, akun administratif pertama diberi root nama. Karena ini adalah nama akun yang dikenal, akan lebih aman untuk mengubah nama yang berbeda ini. Anda masih memiliki hak administratif; satu-satunya hal yang akan berubah adalah nama:
![Ganti Username Gitlab](https://res.cloudinary.com/imans/image/upload/v1537435099/blog/change_username2.png)

Klik tombol Perbarui nama pengguna untuk melakukan perubahan.

Lain kali Anda masuk ke GitLab, ingatlah untuk menggunakan nama pengguna baru Anda.

### Menambahkan Kunci SSH ke Akun Anda
Dalam kebanyakan kasus, Anda akan ingin menggunakan kunci SSH dengan Git untuk berinteraksi dengan proyek GitLab Anda. Untuk melakukan ini, Anda perlu menambahkan kunci publik SSH Anda ke akun GitLab Anda.

Jika Anda sudah memiliki pasangan kunci SSH yang dibuat di komputer lokal Anda, Anda biasanya dapat melihat kunci publik dengan mengetik:
```bash
Local
cat ~/.ssh/id_rsa.pub
```
Anda harus melihat banyak teks, seperti ini:
```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDMuyMtMl6aWwqBCvQx7YXvZd7bCFVDsyln3yh5/8Pu23LW88VXfJgsBvhZZ9W0rPBGYyzE/TDzwwITvVQcKrwQrvQlYxTVbqZQDlmsC41HnwDfGFXg+QouZemQ2YgMeHfBzy+w26/gg480nC2PPNd0OG79+e7gFVrTL79JA/MyePBugvYqOAbl30h7M1a7EHP3IV5DQUQg4YUq49v4d3AvM0aia4EUowJs0P/j83nsZt8yiE2JEYR03kDgT/qziPK7LnVFqpFDSPC3MR3b8B354E9Af4C/JHgvglv2tsxOyvKupyZonbyr68CqSorO2rAwY/jWFEiArIaVuDiR9YM5 asep@mydesktop
```
Salin teks ini dan kembali ke halaman Pengaturan Profil di antarmuka web GitLab.

Jika, sebaliknya, Anda mendapatkan pesan yang terlihat seperti ini, Anda belum memiliki sepasang kunci SSH yang dikonfigurasi pada mesin Anda:
```bash
cat: /home/asep/.ssh/id_rsa.pub: No such file or directory
```
Jika ini kasusnya, Anda dapat membuat pasangan kunci SSH dengan mengetik:
```bash
Local
ssh-keygen
```
Terima default dan berikan kata sandi secara opsional untuk mengamankan kunci secara lokal:
```
Generating public/private rsa key pair.
Enter file in which to save the key (/home/sammy/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/sammy/.ssh/id_rsa.
Your public key has been saved in /home/sammy/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:I8v5/M5xOicZRZq/XRcSBNxTQV2BZszjlWaIHi5chc0 sammy@gitlab.docsthat.work
The key's randomart image is:
+---[RSA 2048]----+
|          ..%o==B|
|           *.E =.|
|        . ++= B  |
|         ooo.o . |
|      . S .o  . .|
|     . + .. .   o|
|      +   .o.o ..|
|       o .++o .  |
|        oo=+     |
+----[SHA256]-----+
```
Setelah Anda memiliki ini, Anda dapat menampilkan kunci publik Anda seperti di atas dengan mengetik:
```bash
Local
cat ~/.ssh/id_rsa.pub
```
Outputnya :
```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDMuyMtMl6aWwqBCvQx7YXvZd7bCFVDsyln3yh5/8Pu23LW88VXfJgsBvhZZ9W0rPBGYyzE/TDzwwITvVQcKrwQrvQlYxTVbqZQDlmsC41HnwDfGFXg+QouZemQ2YgMeHfBzy+w26/gg480nC2PPNd0OG79+e7gFVrTL79JA/MyePBugvYqOAbl30h7M1a7EHP3IV5DQUQg4YUq49v4d3AvM0aia4EUowJs0P/j83nsZt8yiE2JEYR03kDgT/qziPK7LnVFqpFDSPC3MR3b8B354E9Af4C/JHgvglv2tsxOyvKupyZonbyr68CqSorO2rAwY/jWFEiArIaVuDiR9YM5 sammy@mydesktop
```
Salin blok teks yang ditampilkan dan kembali ke Pengaturan Profil Anda di antarmuka web GitLab.

Klik pada item Kunci SSH di menu sebelah kiri:
![Gitlab SSH](https://res.cloudinary.com/imans/image/upload/v1537435420/blog/ssh_keys_menu_item2.png)
Di ruang yang disediakan, tempelkan kunci publik yang Anda salin dari mesin lokal Anda. Beri judul yang deskriptif, dan klik tombol Tambahkan kunci:

![Gitlab SSH](https://res.cloudinary.com/imans/image/upload/v1537435519/blog/add_ssh_key2.png)

Anda sekarang harus dapat mengelola proyek dan repositori GitLab Anda dari mesin lokal tanpa harus memberikan kredensial akun GitLab Anda.

### Membatasi atau Menonaktifkan Pendaftaran Publik (Opsional)
Anda mungkin telah memperhatikan bahwa ada kemungkinan bagi siapa saja untuk mendaftar untuk sebuah akun ketika Anda mengunjungi halaman landing instance GitLab Anda. Ini mungkin yang Anda inginkan jika Anda ingin menjadi tuan rumah proyek publik. Namun, banyak kali, pengaturan yang lebih membatasi yang diinginkan.

Untuk memulai, buat jalan Anda ke area administratif dengan mengklik ikon kunci inggris di bilah menu utama di bagian atas halaman:

![Gitlab Admin Area](https://res.cloudinary.com/imans/image/upload/v1537452395/blog/admin_area_button2.png)

Pada halaman berikut, Anda dapat melihat gambaran umum dari contoh GitLab Anda secara keseluruhan. Untuk menyesuaikan **Pengaturan**, klik pada item Pengaturan di bagian bawah menu sebelah kiri.

![Admin Setting Gitlab](https://res.cloudinary.com/imans/image/upload/v1537452480/blog/admin_settings_button2.png)

Anda akan dibawa ke pengaturan global untuk instance GitLab Anda. Di sini, Anda dapat menyesuaikan sejumlah pengaturan yang memengaruhi apakah pengguna baru dapat mendaftar dan berapa tingkat aksesnya.

### Menonaktifkan Pendaftaran
Jika Anda ingin menonaktifkan pendaftaran sepenuhnya (Anda masih dapat membuat akun untuk pengguna baru secara manual), gulir ke bawah ke bagian Batasan Pendaftaran.

Hapus centang pada kotak centang Mendaftar yang diaktifkan:
![Singups Disable](https://res.cloudinary.com/imans/image/upload/v1537452542/blog/deselect_sign-ups_enabled.png)

Gulir ke bawah ke bawah dan klik tombol **Simpan**

Bagian sign-up sekarang harus dihapus dari halaman landing GitLab.

### Membatasi Pendaftaran Berdasarkan Domain
Jika Anda menggunakan GitLab sebagai bagian dari organisasi yang memberikan alamat email yang terkait dengan domain, Anda dapat membatasi pendaftaran oleh domain, bukan sepenuhnya menonaktifkannya.

Di bagian **Batasan Pendaftaran**, pertama-tama pilih **Kirim email konfirmasi** pada kotak daftar hanya mengizinkan pengguna untuk masuk setelah mereka mengonfirmasi email mereka.

Selanjutnya, tambahkan domain atau domain Anda ke domain **Daftar Putih untuk mendaftar**, satu per baris. Anda dapat menggunakan tanda bintang `*` untuk menentukan domain wildcard:

![Gitlab Screenshoot](https://res.cloudinary.com/imans/image/upload/v1537452673/blog/restrict_sign-ups_by_domain.png)

Gulir ke bawah ke bawah dan klik tombol **Simpan**

Bagian sign-up sekarang harus dihapus dari halaman landing GitLab.

### Membatasi Penciptaan Proyek
Secara default, pengguna baru dapat membuat hingga 10 proyek. Jika Anda ingin mengizinkan pengguna baru dari luar untuk visibilitas dan partisipasi, tetapi ingin membatasi akses mereka untuk membuat proyek baru, Anda dapat melakukannya di bagian **Akun dan Batasi Pengaturan**.

Di dalam, Anda dapat mengubah batas proyek Default menjadi 0 untuk sepenuhnya **menonaktifkan pengguna baru dari membuat proyek**:

Pengguna baru masih dapat ditambahkan ke proyek secara manual dan akan memiliki akses ke proyek internal atau publik yang dibuat oleh pengguna lain.

Gulir ke bawah ke bawah dan klik tombol **Simpan**

Pengguna baru sekarang dapat membuat akun, tetapi tidak dapat membuat proyek.

### Membuat Cron Job Untuk Secara Otomatis Memperbarui Let's Encrypt Certificates
Secara desain, sertifikat Let's Encrypt hanya berlaku selama 90 hari. Jika Anda mengaktifkan Let's Encrypt untuk domain GitLab Anda sebelumnya, Anda harus memastikan bahwa sertifikat Anda diperbarui secara berkala untuk menghindari gangguan layanan. GitLab menyediakan perintah `gitlab-ctl renew-le-certs` untuk meminta sertifikat baru ketika aset Anda mendekati kedaluwarsa.

Untuk mengotomatiskan proses ini, kita dapat membuat tugas cron untuk menjalankan perintah ini secara otomatis. Perintah hanya akan memperbarui sertifikat ketika hampir habis, jadi kita dapat menjalankannya secara aman.

Untuk memulai, buat dan buka file di `/etc/cron.daily/gitlab-le` di editor teks Anda:
```bash
sudo nano /etc/cron.daily/gitlab-le
```
Di dalam, tempelkan skrip berikut:
```bash
#!/bin/bash

set -e

/usr/bin/gitlab-ctl renew-le-certs > /dev/null
```
Simpan dan tutup file saat Anda selesai.

Tandai file sebagai dieksekusi dengan mengetik:
```bash
sudo chmod +x /etc/cron.daily/gitlab-le
```
Sekarang, GitLab harus secara otomatis memeriksa setiap hari jika sertifikat Let's Encryptnya perlu diperbarui. Jika ya, perintah akan memperbarui sertifikat secara otomatis.
