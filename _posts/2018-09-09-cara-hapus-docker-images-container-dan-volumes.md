---
layout: post
title: Cara Hapus Docker Images, Container, dan Volumes
date: 2018-09-09 05:07 +0700
author: Iman Sugirman
image: https://res.cloudinary.com/imans/image/upload/v1536478201/blog/docker.jpg
header:
    layout: hero
    vh: vh-50 vh-50-m vh-80-l
    navigation: fancy
    image: https://res.cloudinary.com/imans/image/upload/v1536478201/blog/docker.jpg
    caption: "[Maik Jonietz](https://unsplash.com/@der_maik_)"
    class: bg-darken-20 bg-darken-25-l
author_bio: true
categories:
    - Tutorial
    - Infrastruktur
tags:
    - Docker
    - Images
    - Container
---
Docker memudahkan untuk mem-package aplikasi dan layanan Anda dalam wadah sehingga Anda dapat menjalankannya di mana saja. Ketika Kamu bekerja dengan Docker, itu juga akan mudah untuk mengumpulkan sejumlah besar image yang tidak terpakai, container, dan volume data yang mengacaukan output dan mengkonsumsi ruang disk.

Docker sudah menyediakan semua alat yang kamu butuhkan untuk membersihkan sistem Kamu dari `Command Line`. Panduan `Cheat Sheet` ini menyediakan referensi cepat untuk perintah yang berguna untuk melegakan Storage dan menjaga sistem Kamu agar lebih terorganisir dengan menghapus image, container, dan volume Docker yang tidak digunakan.

### Cara Menggunakan Panduan Ini:

Panduan ini dalam format lembar contekan dengan cuplikan baris perintah mandiri, Lompat ke bagian apa pun yang relevan dengan tugas yang Anda coba selesaikan. Sintaks perintah pengganti, perintah $ (perintah), digunakan dalam perintah tersedia dalam banyak shell populer seperti bash, zsh, dan Windows Powershell.

#### Membersihkan Semua image, Wadah, Volume, dan Jaringan yang Tidak Terpakai atau Tergantung

Docker menyediakan perintah ( command line ) yang akan membersihkan sumber daya apapun yang ada di image, container, volume, dan jaringan yang tidak terpakai :

```shell
docker system prune
```
Untuk menghapus semua container yang dihentikan dan semua image yang tidak digunakan (tidak hanya image yang menggantung), tambahkan flag -a ke perintah:

```bash
docker system prune -a
```

### Menghapus image Docker
Hapus satu atau lebih image tertentu. Gunakan perintah `docker images` pelabuhan dengan bendera `-a` untuk mencari ID dari image yang ingin Anda hapus. Ini akan menunjukkan kepada Anda setiap image, termasuk lapisan image menengah. Saat Anda menemukan image yang ingin Anda hapus, Anda dapat meneruskan ID atau tag mereka ke `docker rmi`:

List :
```bash
docker images -a
```
Hapus :
```shell
docker rmi Image Image
```
#### Hapus image yang menggantung

image Docker terdiri dari beberapa lapisan. Menggantung image adalah lapisan yang tidak memiliki hubungan dengan image apa pun yang diberi tag. Mereka tidak lagi melayani tujuan dan mengkonsumsi ruang disk. Mereka dapat ditemukan dengan menambahkan bendera filter, -f dengan nilai menggantung = true pada perintah image buruh pelabuhan. Ketika Anda yakin ingin menghapusnya, Anda dapat menggunakan perintah pembersihan image buruh pelabuhan:

Catatan: Jika Anda membuat image tanpa memberi tag, image akan muncul pada daftar image yang menggantung karena tidak ada kaitan dengan image yang ditandai. Anda dapat menghindari situasi ini dengan memberikan tag saat Anda membuat, dan Anda dapat secara retro menandai image dengan perintah tag docker.
{: .alert .alert--warning}

List :
```shell
docker images -f dangling=true
```
Hapus :
```bash
docker images purge
```
### Menghapus image sesuai dengan pola

Anda dapat menemukan semua image yang cocok dengan pola menggunakan kombinasi image buruh pelabuhan dan grep. Setelah Anda puas, Anda dapat menghapusnya dengan menggunakan awk untuk meneruskan ID ke docker rmi. Perhatikan bahwa utilitas ini tidak disediakan oleh Docker dan belum tentu tersedia di semua sistem:

List :
```bash
docker images -a |  grep "pattern"
```
Hapus :
```bash
docker images -a | grep "pattern" | awk '{print $3}' | xargs docker rmi
```
#### Hapus semua image

Semua image Docker pada sistem dapat didaftar dengan menambahkan -a ke perintah image buruh pelabuhan. Setelah Anda yakin ingin menghapus semuanya, Anda dapat menambahkan tanda -q untuk meneruskan ID image ke docker rmi:

List :
```bash
docker images -a
```
Hapus :
```bash
docker rmi $(docker images -a -q)
```
### Menghapus Container

#### Hapus satu atau lebih kontainer tertentu
Gunakan perintah ps docker dengan flag -a untuk mencari nama atau ID dari kontainer yang ingin Anda hapus:

List:
```bash
docker ps -a
```
Hapus :
```bash
docker rm ID_or_Name ID_or_Name
```
#### Hapus Container Saat Keluar
Jika Anda tahu ketika Anda membuat penampung yang tidak ingin Anda simpan setelah selesai, Anda dapat menjalankan operasi galangan --rm untuk menghapusnya secara otomatis saat keluar.

**Run and Remove:**
```bash
docker run --rm image_name
```

#### Buang semua kontainer yang dikeluarkan

Anda dapat menemukan Container menggunakan `docker ps -a` dan memfilternya berdasarkan statusnya: dibuat, memulai ulang, menjalankan, berhenti, atau keluar. Untuk meninjau daftar kontainer yang dikeluarkan, gunakan flag `-f` untuk memfilter berdasarkan status. Ketika Anda telah memverifikasi Anda ingin menghapus wadah tersebut, gunakan `-q` untuk meneruskan ID ke perintah `docker rm`.

**List :**
```bash
docker ps -a -f status=exited
```
**Cara Hapus :**
```bash
docker rm $(docker ps -a -f status=exited -f status=created -q)
```
#### Hapus wadah sesuai dengan pola

Anda dapat menemukan semua kontainer yang cocok dengan pola menggunakan kombinasi docker ps dan grep. Ketika Anda puas bahwa Anda memiliki daftar yang ingin Anda hapus, Anda dapat menggunakan awk dan xargs untuk memasok ID ke docker rmi. Perhatikan bahwa utilitas ini tidak disediakan oleh Docker dan belum tentu tersedia di semua sistem:

**List :**
```bash
docker ps -a |  grep 'pattern'
```

**Cara Hapus :**
```bash
docker ps -a | grep "pattern" | awk '{print $3}' | xargs docker rmi
```
#### Hentikan dan hapus semua kontainer

Anda dapat meninjau wadah di sistem Anda dengan docker ps. Menambahkan-bendera akan menampilkan semua kontainer. Saat Anda yakin ingin menghapusnya, Anda dapat menambahkan tanda -q untuk menyuplai ID ke docker stop dan docker rm commands:

**List :**
```bash
docker ps -a
```

**Cara Hapus :**
```bash
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)
```
### Menghapus Volume

#### Hapus satu atau lebih volume tertentu - Docker 1.9 dan lebih baru

Gunakan perintah ls volume docker untuk mencari nama atau nama volume yang ingin Anda hapus. Kemudian Anda dapat menghapus satu atau lebih volume dengan perintah rm volume docker:

**List :**
```bash
docker volume ls
```

**Cara Hapus :**
```bash
docker volume rm volume_name volume_name
```

#### Hapus volume yang menggantung - Docker 1.9 dan yang lebih baru

Karena titik volume harus ada independen dari kontainer, ketika wadah dihapus, volume tidak secara otomatis dihapus pada saat yang sama. Ketika volume ada dan tidak lagi terhubung ke kontainer apa pun, itu disebut volume yang menjuntai. Untuk mencari mereka untuk mengkonfirmasi Anda ingin menghapusnya, Anda dapat menggunakan perintah ls volume docker dengan filter untuk membatasi hasil ke volume yang menggantung. Ketika Anda puas dengan daftar tersebut, Anda dapat menghapusnya semua dengan pangkas volume docker:

**List :**
```bash
docker volume ls -f dangling=true
```

**Cara Hapus :**
```bash
docker volume prune
```
#### Remove a container and its volume

Jika Anda membuat volume yang tidak disebutkan namanya, itu dapat dihapus pada saat yang bersamaan dengan penampung dengan tanda -v. Perhatikan bahwa ini hanya berfungsi dengan volume tanpa nama. Ketika kontainer berhasil dihapus, ID-nya ditampilkan. Perhatikan bahwa tidak ada referensi yang dibuat untuk menghapus volume. Jika tidak disebutkan namanya, itu secara diam-diam dihapus dari sistem. Jika diberi nama, diam-diam tetap hadir.

**Cara Hapus :**
```bash
docker rm -v container_name
```
