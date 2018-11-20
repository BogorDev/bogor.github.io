---
layout: post
title: Deploy Full Otomatis Git-Based Website Static Dalam 5 Menit
subtitle: Adding subtitles couldn't be easier
date: 2018-08-27 14:03 +0700
author: Iman Sugirman
thumbnail: https://res.cloudinary.com/wpcloudserver/image/upload/v1535558863/pankaj-patel-515219-unsplash.jpg
header:
    layout: hero
    vh: vh-50 vh-50-m vh-80-l
    navigation: fancy
    image: https://res.cloudinary.com/wpcloudserver/image/upload/v1535558863/pankaj-patel-515219-unsplash.jpg
    caption: "[Pankaj Patel](https://unsplash.com/@pankajpatel)"
author_bio: true
categories:
    - Cloud Server
tags:
    - Static website
    - Hexo
---
Terkadang Anda hanya ingin membuat situs web statis aktif dan berjalan secepat mungkin, baik itu situs web yang sebenarnya, tempat penampung, atau laman landas dasar. Baru-baru ini saya mulai menggunakan `Caddy`, server web modern yang berfokus pada kesederhanaan dan keamanan. Ini termasuk dukungan asli untuk Git dan `Let's Encrypt` berkat arsitektur berbasis plugin-nya.

Saya suka betapa mudahnya menggunakan `Caddy`, dan saya ingin berbagi tutorial tentang cara kami menerapkan situs web statis yang disinkronkan dengan repositori Git dalam waktu kurang dari 5 menit — semua pada Tetesan yang berputar dalam 55 detik.

### Prasyarat

Server Ubuntu 16.04 dikonfigurasi sesuai dengan panduan Pengaturan Server Awal kami. Meskipun tujuannya adalah untuk membuat situs web dengan cepat, kami tidak ingin berhemat pada keamanan. Panduan ini akan menyiapkan lingkungan yang aman untuk Caddy. Lewati langkah 7 dari panduan karena kami akan menggunakan Firewall Cloud alih-alih perangkat lunak firewall yang dipasang di Droplet.

Nama domain yang Anda miliki, yang akan digunakan untuk situs web.

### Step 1 — Install Caddy
Caddy menyediakan biner `pra-instal` di situs webnya. Unduh Caddy di Droplet Anda:
```shell
wget -O caddy.tar.gz "https://caddyserver.com/download/linux/amd64?plugins=http.git&license=personal"  
```
Perintah ini akan mengunduh biner Caddy dengan pengaturan berikut:

Platform: `Linux 64-bit`
Plugin: `http.git`
Lisensi: `Pribadi`

Perlu diingat bahwa lisensi pribadi hanya tersedia untuk penggunaan non-komersial.

Ekstrak arsip yang diunduh ke dalam direktori baru dan cd ke dalamnya:

```shell
mkdir caddy  
tar vxf caddy.tar.gz -C caddy  
cd caddy  
```
The archive contains the Caddy binary and a Systemd service file. We will use both in this guide. First, install the binary:

```shell
sudo cp caddy /usr/local/bin  
sudo chown root:root /usr/local/bin/caddy  
sudo chmod 755 /usr/local/bin/caddy  
```
Karena Caddy akan berfungsi sebagai server web kita yang menghadap ke depan, maka akan perlu untuk dapat mendengarkan pada port `80` dan `443`. Linux membutuhkan binari untuk dijalankan sebagai root agar dapat didengarkan pada port apa pun di bawah `1024`. Namun dimungkinkan untuk mengizinkan binari tertentu untuk melakukannya tanpa hak root penuh:

```shell
sudo setcap 'cap_net_bind_service=+ep' /usr/local/bin/caddy  
```
Kemudian, buat direktori konfigurasi Caddy dan tetapkan izin yang tepat:

```shell
sudo mkdir /etc/caddy  
sudo chown -R root:www-data /etc/caddy  
sudo mkdir /etc/ssl/caddy  
sudo chown -R www-data:root /etc/ssl/caddy  
sudo chmod 0770 /etc/ssl/caddy  
```
Terakhir, instal file layanan Systemd:

```shell
sudo cp init/linux-systemd/caddy.service /etc/systemd/system/  
sudo chown root:root /etc/systemd/system/caddy.service  
sudo chmod 644 /etc/systemd/system/caddy.service  
sudo systemctl daemon-reload  
```

### Step 2 — Configure DNS

Sebelum mengkonfigurasi dan memulai Caddy, kami ingin mengatur DNS agar Caddy dapat mengeluarkan sertifikat SSL melalui Let's Encrypt.

Tambahkan nama domain Anda di halaman Domain. Kami akan membuat dua catatan DNS yang menunjuk ke Droplet: satu untuk IPv4 dan satu untuk IPv6.

Yang pertama akan bertipe A. Di bidang hostname, masukkan `@`. Pilih Droplet Anda di kolom Will Direct To dan tambahkan catatan. Rekor kedua akan memiliki pengaturan yang sama tetapi dengan tipe AAAA.

### Step 3 — Configure Caddy

Untuk keperluan panduan ini, kami akan menggunakan situs web contoh berikut:

[Static Website Sample](https://github.com/kamaln7/basic-static-website)

File konfigurasi Caddy akan ditempatkan di `/etc/caddy/Caddyfile`. Buka file dalam editor teks (nano, vim, dll.) Dan masukkan yang berikut:

{% highlight shell %}
example.com {  
    tls you@example.com
    internal /.git
    git https://github.com/kamaln7/basic-static-website.git {
        interval 300
    }
    gzip
    redir 301 {
        if {scheme} is http
        /  https://{host}{uri}
    }
}
{% endhighlight %}

Ganti `example.com` dengan nama domain Anda dan you@example.com dengan alamat email Anda. Alamat email ini akan digunakan untuk menerbitkan sertifikat Let's Encrypt untuk domain Anda, jadi pastikan untuk memasukkan yang valid yang dapat Anda akses.

Ini mengonfigurasi default dasar yang waras: kompresi gzip akan digunakan ketika cocok dan semua lalu lintas HTTP akan dialihkan ke HTTPS.

Bagian utama dari konfigurasi ini adalah blok `git`. Ini akan mengonfigurasi Caddy untuk menggunakan konten repositori Git sebagai file situs web, memeriksa pembaruan setiap 5 menit.

### Step 4 — Start Caddy
Mulai Caddy, dan aktifkan untuk memulai saat boot:

```bash
sudo systemctl start caddy  
sudo systemctl enable caddy  
```
Mungkin butuh beberapa detik bagi Caddy untuk menerima sertifikat dari Let's Encrypt dan mengkloning repositori Anda, tetapi Anda seharusnya sudah dapat menjelajahi nama domain Anda dan melihat situs web Anda.

Sekarang, setiap perubahan yang Anda buat dalam repositori Git Anda akan diterapkan secara otomatis.

### Langkah 5 - Konfigurasikan Firewall

DigitalOcean Cloud Firewalls membuatnya sangat mudah untuk mengkonfigurasi firewall yang aman. Browse ke halaman Firewalls dan klik tombol Create Firewall. Jika Anda sudah memiliki satu atau lebih Firewalls di akun Anda, Anda dapat mengakses halaman Buat Firewall melalui menu Buat di bagian atas halaman.

Untuk aturan masuk, kami akan mengizinkan lalu lintas SSH, HTTP, dan HTTPS. Jaga aturan keluar sebagaimana adanya. Pilih Droplet Anda dan buat firewall.

Untuk petunjuk lebih lanjut tentang Cloud Firewalls, lihat tutorial kami Cara Membuat Firewall Cloud DigitalOcean Pertama Anda.

### Optional: Step 6 — Enable Instant Deployments using Webhooks

Caddy akan memeriksa repositori Git untuk perubahan setiap lima menit secara default. Meskipun Anda dapat mengatur interval ke nilai yang lebih rendah, solusi yang lebih baik adalah mengkonfigurasi GitHub untuk mendorong perubahan ke Caddy. Ini akan memungkinkan pembaruan hampir instan.

Caddy membuat proses ini sangat mudah juga. Webhook akan membutuhkan rahasia — Anda dapat menggunakan apa pun yang Anda inginkan. Program uuidgen adalah alat yang mudah digunakan yang memungkinkan Anda dengan mudah menghasilkan string aman acak. Cukup jalankan uuidgen dan salin keluarannya.

Edit Caddyfile dan tambahkan baris berikut di dalam blok Git, ganti rahasia dengan rahasia Anda:

```bash
hook /github_hook secret  
```
Restart Caddy to apply the changes:
```bash
sudo systemctl restart caddy  
```
Kemudian, konfigurasikan Github untuk menggunakan endpoint webhook yang baru: telusuri ke halaman pengaturan repositori Anda dan klik Webhooks. Tambahkan webhook baru dan setel URL Payload ke https://domain.com/github_hook. Setel jenis Konten ke aplikasi / json dan masukkan rahasia Anda dan klik Tambahkan Webhook.

Sekarang, setiap kali Anda mendorong perubahan ke repositori Git Anda, itu akan tercermin di situs web Anda dalam hitungan detik. Misalnya, jika Anda menggunakan situs web contoh yang disebutkan di atas, lanjutkan dan ubah warna sorotan menjadi biru dengan mengganti b-gold dengan b-blue. Komit file yang diperbarui dan muat ulang halaman!

### Kesimpulan

Dengan mengikuti panduan ini, Anda akan menerapkan situs web pemeliharaan rendah yang sepenuhnya otomatis menggunakan teknologi modern seperti HTTP / 2 dan Let's Encrypt. Caddy adalah server web serba guna dan mendukung banyak fitur hebat seperti rendering Markdown on-the-fly. Anda dapat menemukan daftar plugin dan fitur di halaman Fitur di situs webnya. Jelajahi [Dokumentasi](https://caddyserver.com/docs) untuk melihat fitur apa yang mungkin ingin Anda aktifkan dan bagaimana melakukannya.

Kamal Nasser adalah Advokat Pengembang di DigitalOcean. Dia juga seorang mahasiswa Ilmu Komputer dengan hasrat untuk rekayasa perangkat lunak dan alpukat. Anda dapat menemukannya di Twitter @ kamaln7.
