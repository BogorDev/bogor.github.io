---
layout: post
title: Cara Menggunakan Digitalocean Space Object Storage
date: 2018-09-02 23:36 +0700
author: Iman Sugirman
image: https://res.cloudinary.com/imans/image/upload/v1535913932/patryk-gradys-128898-unsplash.jpg
header:
    layout: hero
    vh: vh-50 vh-50-m vh-80-l
    navigation: fancy
    image: https://res.cloudinary.com/imans/image/upload/v1535913932/patryk-gradys-128898-unsplash.jpg
    caption: "[Maik Jonietz](https://unsplash.com/@der_maik_)"
    class: bg-darken-20 bg-darken-25-l
author_bio: true
categories:
    - Object Storage
    - Digitalocean
tags:
    - Konfigurasi
    - Management
    - Storage
---
Digitalocean Space adalah salah satu produk dari Digitalocean yang memberikan layanan Object Storage dengan harga Flat $5 Perbulan dengan Kapasitas 250GB dan 1TB Bandwith output. Jika melebihi limit, kamu akan dikenakan $ 0,01 / GB. Sedangkan Bandwith yang masuk tidak dikenakan biaya alias gratis :blush:

Digitalocean Space tersedia di beberapa lokasi diantaranya NYC3, SG, dll. Ketika kamu mendaftar menggunakan tombol Memulai pada halaman Spaces di Control Panel, Anda secara otomatis mendapatkan langganan 60 hari gratis, yang mencakup tarif dasar (tapi tidak terlalu berlebihan).

#### Ketersediaan Wilayah

Digitalocean Space tersedia di beberapa lokasi diantaranya di NYC3, AMS3, dan SGP1. Walaupun Sebenarnya Digitalocean Space bisa diakses diluar dari area tersebut :cloud:

#### Fitur

* Semua Spaces mendukung HTTPS. :lock:
* Sudah Beradaptasi dengan S3 :zap:

#### Aksesibilitas
Biasanya untuk object storage pasti sudah mancakup sub domain dari perusahaan yang memberikan layanan Object Storage contohnya saja AWS yang memiliki sub domain s3.amazonaws.com sedangkan di Digitalocean Space biasanya berpola spacename.region.digitaloceanspaces.com atau region.digitaloceanspaces.com/spacename, di mana spacename adalah nama Space dan wilayah Anda adalah wilayah Space Kamu.

#### Batas

Untuk menghindari batasan sementara pada tingkat permintaan Anda, pastikan bahwa aplikasi Anda dirancang untuk mencoba kembali pada error 503. Jika Anda berencana untuk mendorong lebih dari 200 permintaan / detik ke Spaces, silakan buka tiket dukungan untuk mempersiapkan beban kerja.

### Memulai Dengan s3cmd untuk Konfigurasi

s3cmd adalah cli untuk memanage `Object Storage` sebut saja AWS Amazon S3, dan sekarang Digitalocean Space bisa menggunakannya. Baiklah sebelumnya mari kita Download ``s3cmd`` dan untuk mendownload bisa mengunjungi situsnya di [s3cmd Website](https://s3tools.org) tetapi ada baiknya cek juga [Github s3cmd](https://github.com/s3tools/s3cmd) dan Mengecek Log Releasenya [Release s3cmd](https://github.com/s3tools/s3cmd/releases) dan saat ini adalah versi `2.0.2`

### Install Python dan pip Alternatif
Untuk menginstall s3cmd harus menginstall pip dan Python

```shell
sudo yum install python-pip
```
Install Menggunakan pip
```shell
sudo pip install s3cmd
```

#### Cara Downloadnya :

Menggunakan Git
```shell
git clone https://github.com/s3tools/s3cmd.git
```
Jika menggunakan Git Clone kamu bisa langsung ke folder `s3cmd`
atau bisa juga menggunakan curl
```shell
curl -O L https://github.com/s3tools/s3cmd/releases/download/v2.0.2/s3cmd-2.0.2.zip
```
Jika menggunakan `curl` kamu bisa unzip file yang sudah di download.



### Install Dari Source github
```shell
cd s3cmd-2.0.2
python setup.py install
```

#### Konfirgurasi s3cmd
```shell
s3cmd --configure
```
Mengisi di Konfigurasi shell
```shell
Enter new values or accept defaults in brackets with Enter.
Refer to user manual for detailed description of all options.
Access key and Secret key are your identifiers for Amazon S3. Leave them empty for using the env variables.
Access Key []: EXAMPLE7UQOTHDTF3GK4
Secret Key []: exampleb8e1ec97b97bff326955375c5
Default Region [US]:
```
Lalu untuk Region dan Endpoint diisi :
```shell
Use "s3.amazonaws.com" for S3 Endpoint and not modify it to the target Amazon S3.
S3 Endpoint [s3.amazonaws.com]: nyc3.digitaloceanspaces.com
```
Karena Spaces mendukung bucket berbasis DNS, pada prompt berikutnya, berikan nilai bucket dalam format yang diperlukan: `%(bucket)s.nyc3.digitaloceanspaces.com`

```shell
%(bucket)s.nyc3.digitaloceanspaces.com
```

*Optional Set an Encryption Password*

```shell
Encryption password:
Path to GPG program [/usr/bin/gpg]:
```
Gunakan HTTPS
```shell
Use HTTPS protocol [Yes]: Yes
```
Gunakan Proxy jika ingin ditambahkan
```shell
HTTP Proxy server name:
```
Dan jika sudah selesai akan muncul Preview seperti ini :
```bash
New settings:
 Access Key: EXAMPLES7UQOTHDTF3GK4
 Secret Key: b8e1ec97b97bff326955375c5example
 Default Region: US
 S3 Endpoint: nyc3.digitaloceanspaces.com
 DNS-style bucket+hostname:port template for accessing a bucket: %(bucket)s.n
yc3.digitaloceanspaces.com
 Encryption password: secure_password
 Path to GPG program: /usr/bin/gpg
 Use HTTPS protocol: True
 HTTP Proxy server name:
 HTTP Proxy server port: 0

Test access with supplied credentials? [Y/n] Y
```
Dan Simpan pengaturan kamu :
```shell
Save settings? [y/N] Y
```
Dan Konfigurasi disimpan di
```bash
Configuration saved to '/home/asep/nyc3'
```
### Menggunakan s3cmd

Cara Membuat Bucket :
```bash
s3cmd mb s3://spacename s3://secondspace
```
Untuk Melihat File dan Folder isi dari bucket
```shell
s3cmd ls
```
Untuk Mengupload file bisa menggunakan :
```shell
s3cmd put file.txt s3://spacename/path/
```
Cara Mengupload Folder yang saat ini di terminal :
```shell
pwd
s3cmd put * s3://spacename/path/ --recursive
```
Untuk Mendownload File yang ada di Digitalocean Space :
```shell
s3cmd get s3://spacename/path/to/file.txt
```
Cara Mengatur Private dan Public Bucket Digitalocean Space :

```shell
s3cmd setacl s3://spacename/ --acl-public
s3cmd setacl s3://spacename/ --acl-private
```
Untuk lebih lengkap kamu bisa lihat dokumentasi s3cmd di [website s3cmd](https://s3tools.org/usage)
