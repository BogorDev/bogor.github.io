---
layout: post
title: Develop Wordpress Dengan Roots
date: 2018-09-24 05:48 +0700
author: Iman Sugirman
cover: /assets/images/develop-wp-root.png
image: https://res.cloudinary.com/imans/image/upload/v1537743267/blog/ash-edmonds-510566-unsplash.jpg
header:
    layout: hero
    vh: vh-50 vh-50-m vh-80-l
    navigation: fancy
    image: https://res.cloudinary.com/imans/image/upload/v1537743267/blog/ash-edmonds-510566-unsplash.jpg
    caption: "[Ash Edmonds](https://unsplash.com/@badashproducts)"
    class: bg-darken-20 bg-darken-25-l
author_bio: true
categories:
    - Tutorial
    - Wordpress
    - Roots
tags:
    - Vagrant
    - Virtualbox
    - Trellis
    - Bedrock
    - Ansible
---
Roots adalah Open Source untuk pengembangan aplikasi WordPress. Anda bisa flexible untuk Develop Website Wordpress menggunakan `Roots` karena alurnya termanage dan bisa menggunakan **Trellis** juga **Bedrock**. 

#### Trellis

Pengembangan WordPress dan server produksi dilakukan dengan benar. Secara otomatis membuat dan menyediakan server untuk meng-host situs WordPress.

#### Bedrock

WordPress boilerplate untuk mengatur proyek WordPress Anda lebih baik, meningkatkan file konfigurasi, dan mengelola plugin dengan Composer.

#### Roots

WordPress starter theme yang memungkinkan Anda membangun tema yang lebih baik lebih cepat dengan alat pengembangan yang lebih baik.

### Sejarah dari Roots

Roots telah menyediakan pengembang dengan alat untuk membantu membangun situs WordPress yang lebih baik sejak 2011. Dipercaya oleh beberapa merek pemikiran paling maju.

 * 100.000+ situs Sage di web 5.000 proyek baru dibuat sebulan
 * 20.000+ Bedrock menginstal di web 4.000 proyek baru dibuat sebulan

Trellis bergantung pada beberapa perangkat lunak lain. Pastikan semua dependensi telah dipasang sebelum melanjutkan:

  * Virtualbox >= 4.3.10
  * Vagrant >= 1.8.5

Ini adalah persyaratan minimum untuk server pengembangan. Vagrant otomatis menangani ketergantungan Ansible bagi kita secara default.

Untuk mempercepat penyediaan VM dev masa mendatang, atau untuk server jarak jauh, Anda harus menginstal Ansible secara lokal di mesin host Anda.

### Memulai Proyek WordPress
Buat direktori untuk wordpress development anda di Localhost :
```bash
mkdir example.com && cd example.com
```
Clone Repositori dari Trellis ke Direktori Trellis:
```bash
git clone --depth=1 git@github.com:roots/trellis.git && rm -rf trellis/.git
```
Clone Repositori dari Bedrock ke Direktori Bedrock:
```bash
git clone --depth=1 git@github.com:roots/bedrock.git site && rm -rf site/.git
```

Situs-situs ini dikonfigurasi dalam file-file YAML untuk setiap lingkungan seperti `group_vars/development/wordpress_sites.yml`.

Ada dua komponen dan tempat untuk mengonfigurasi situs:

  * Pengaturan normal di `group_vars/development/wordpress_sites.yml`
  * Sandi / rahasia di `group_vars/development/vault.yml`

### Konfigurasi Normal
`wordpress_sites` adalah kamus top-level yang digunakan untuk mendefinisikan semua situs yang Anda inginkan. Berikut ini adalah situs kosong minimal sebagai contoh untuk pengembangan:
```yaml
# group_vars/development/wordpress_sites.yml
wordpress_sites:
  example.com:
    site_hosts:
      - canonical: example.test
    local_path: ../site # path targeting local Bedrock site directory (relative to Ansible root)
    admin_email: admin@example.test
    multisite:
      enabled: false
    ssl:
      enabled: false
    cache:
      enabled: false
```
