---
layout: post
title: Cara Install Docker di Ubuntu 16.04
date: 2018-09-09 17:04 +0700
author: Iman Sugirman
image: https://res.cloudinary.com/imans/image/upload/v1536487734/blog/fabian-grohs-674717-unsplash.jpg
header:
    layout: hero
    vh: vh-50 vh-50-m vh-80-l
    navigation: fancy
    image: https://res.cloudinary.com/imans/image/upload/v1536487734/blog/fabian-grohs-674717-unsplash.jpg
    caption: "[Fabian Grohs](https://unsplash.com/@grohsfabian)"
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
Docker adalah aplikasi yang membuatnya sederhana dan mudah untuk menjalankan proses aplikasi dalam sebuah wadah, yang seperti mesin virtual, hanya lebih portabel, lebih ramah sumber daya, dan lebih bergantung pada sistem operasi host. Untuk pengantar rinci tentang berbagai komponen kontainer Docker, periksa The Docker Ecosystem: Suatu Pengantar Komponen Umum.

Ada dua metode untuk menginstal Docker pada Ubuntu 16.04. Salah satu metode melibatkan menginstalnya pada instalasi sistem operasi yang ada. Yang lainnya melibatkan memintal server dengan alat bernama Docker Machine yang menginstal Docker secara otomatis di atasnya.

Dalam tutorial ini, Anda akan belajar cara menginstal dan menggunakannya pada instalasi Ubuntu 16.04 yang sudah ada.

### Prasyarat
Untuk mengikuti tutorial ini, Anda akan memerlukan hal-hal berikut:
  * Server Ubuntu 16.04 64-bit
  * Pengguna non-root dengan hak sudo [Initial Server Ubuntu 16.04](/initial-server-setup-with-ubuntu-16-04) menjelaskan cara mengatur ini.

Semua perintah dalam tutorial ini harus dijalankan sebagai pengguna non-root. Jika akses root diperlukan untuk perintah, itu akan didahului oleh sudo. [Initial Server Ubuntu 16.04](/initial-server-setup-with-ubuntu-16-04) menjelaskan cara menambahkan pengguna dan memberi mereka akses sudo.

### Install Docker
Paket instalasi Docker yang tersedia di repositori resmi Ubuntu 16.04 mungkin bukan versi terbaru. Untuk mendapatkan versi terbaru dan terbaik, instal Docker dari repositori Docker resmi. Bagian ini menunjukkan kepada Anda bagaimana melakukan hal itu.

Pertama, tambahkan kunci GPG untuk repositori Docker resmi ke sistem:
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
Tambahkan repositori Docker ke sumber `apt` :
```bash
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```
Selanjutnya, perbarui paket database dengan paket Docker dari repo yang baru ditambahkan:
```bash
sudo apt-get update
```
Pastikan Anda akan menginstal dari repo Docker bukan dari default Ubuntu 16.04 repo:

```bash
apt-cache policy docker-ce
```
Anda seharusnya melihat keluaran yang serupa dengan yang diikuti:

```
docker-ce:
  Installed: (none)
  Candidate: 17.03.1~ce-0~ubuntu-xenial
  Version table:
     17.03.1~ce-0~ubuntu-xenial 500
        500 https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages
     17.03.0~ce-0~ubuntu-xenial 500
        500 https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages
```
Perhatikan bahwa docker-ce tidak diinstal, tetapi kandidat untuk instalasi berasal dari repositori Docker untuk Ubuntu 16.04. Nomor versi docker-ce mungkin berbeda.

Akhirnya, instal Docker:
```bash
sudo apt-get install -y docker-ce
```
Docker sekarang harus diinstal, daemon dimulai, dan proses diaktifkan untuk memulai saat boot. Periksa apakah ini berjalan:

```bash
sudo systemctl status docker
```
Output harus serupa dengan yang berikut ini, menunjukkan bahwa layanan ini aktif dan berjalan:

```apache2
● docker.service - Docker Application Container Engine
   Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
   Active: active (running) since Sun 2016-05-01 06:53:52 CDT; 1 weeks 3 days ago
     Docs: https://docs.docker.com
 Main PID: 749 (docker)
```
Instalasi Docker sekarang memberi Anda bukan hanya layanan Docker (daemon) tetapi juga utilitas baris perintah docker, atau klien Docker. Kami akan mengeksplorasi cara menggunakan perintah docker nanti di tutorial ini.

### Melaksanakan Perintah Docker Tanpa Sudo (Opsional)

Secara default, menjalankan perintah docker membutuhkan hak akses root - yaitu, Anda harus awalan perintah dengan sudo. Ini juga dapat dijalankan oleh pengguna di grup docker, yang dibuat secara otomatis selama pemasangan Docker. Jika Anda mencoba menjalankan perintah docker tanpa mengawali dengan sudo atau tanpa di grup docker, Anda akan mendapatkan output seperti ini:

```
docker: Cannot connect to the Docker daemon. Is the docker daemon running on this host?.
See 'docker run --help'.
```
Jika Anda ingin menghindari mengetik sudo setiap kali Anda menjalankan perintah docker, tambahkan nama pengguna Anda ke grup buruh pelabuhan:

```bash
sudo usermod -aG docker ${USER}
```
Untuk menerapkan keanggotaan grup baru, Anda dapat logout dari server dan masuk kembali, atau Anda dapat mengetikkan yang berikut:
```bash
su - ${USER}
```
Lalu :
```bash
id -nG
```
Outputnya akan seperti ini :
```
sammy sudo docker
```
Jika Anda perlu menambahkan pengguna ke grup buruh pelabuhan yang tidak Anda masuki, nyatakan bahwa nama pengguna secara eksplisit menggunakan:

```bash
sudo usermod -aG docker username
```
Bagian lain dari artikel ini mengasumsikan Anda menjalankan perintah docker sebagai pengguna dalam grup pengguna docker. Jika Anda memilih untuk tidak, silakan tambahkan perintah dengan sudo.

### Menggunakan Perintah Docker
Dengan Docker terpasang dan berfungsi, sekarang adalah waktunya untuk terbiasa dengan utilitas baris perintah. Menggunakan buruh pelabuhan terdiri dari melewatkannya rantai opsi dan perintah diikuti dengan argumen. Sintaksis mengambil bentuk ini:
```bash
docker [option] [command] [arguments]
```
Untuk melihat semua sub-perintah yang tersedia, ketik:
```bash
docker
```
Pada Docker 1.11.1, daftar lengkap dari subkomand yang tersedia meliputi:

```
attach    Attach to a running container
build     Build an image from a Dockerfile
commit    Create a new image from a container's changes
cp        Copy files/folders between a container and the local filesystem
create    Create a new container
diff      Inspect changes on a container's filesystem
events    Get real time events from the server
exec      Run a command in a running container
export    Export a container's filesystem as a tar archive
history   Show the history of an image
images    List images
import    Import the contents from a tarball to create a filesystem image
info      Display system-wide information
inspect   Return low-level information on a container or image
kill      Kill a running container
load      Load an image from a tar archive or STDIN
login     Log in to a Docker registry
logout    Log out from a Docker registry
logs      Fetch the logs of a container
network   Manage Docker networks
pause     Pause all processes within a container
port      List port mappings or a specific mapping for the CONTAINER
ps        List containers
pull      Pull an image or a repository from a registry
push      Push an image or a repository to a registry
rename    Rename a container
restart   Restart a container
rm        Remove one or more containers
rmi       Remove one or more images
run       Run a command in a new container
save      Save one or more images to a tar archive
search    Search the Docker Hub for images
start     Start one or more stopped containers
stats     Display a live stream of container(s) resource usage statistics
stop      Stop a running container
tag       Tag an image into a repository
top       Display the running processes of a container
unpause   Unpause all processes within a container
update    Update configuration of one or more containers
version   Show the Docker version information
volume    Manage Docker volumes
wait      Block until a container stops, then print its exit code
```
Untuk melihat switch yang tersedia untuk perintah spesifik, ketik:
```bash
docker docker-subcommand --help
```
Untuk melihat informasi sistem-lebar tentang Docker, gunakan:
```bash
docker info
```
### Bekerja dengan Image Docker
Container Docker dijalankan dari `image` Docker. Secara default, ia menarik image-image ini dari Docker Hub, registri Docker yang dikelola oleh Docker, perusahaan di belakang proyek Docker. Siapa saja dapat membangun dan menyimpan `image` Docker mereka di Docker Hub, sehingga sebagian besar aplikasi dan distribusi Linux yang Anda perlukan untuk menjalankan kontainer Docker memiliki `image` yang di-host di Docker Hub.

Untuk memeriksa apakah Anda dapat mengakses dan mengunduh `image` dari Docker Hub, ketik:

```bash
docker run hello-world
```
Output, yang harus mencakup hal-hal berikut, harus menunjukkan bahwa Docker bekerja dengan benar:

```
Hello from Docker.
This message shows that your installation appears to be working correctly.
...
```
Anda dapat mencari gambar yang tersedia di Docker Hub dengan menggunakan perintah docker dengan perintah pencarian. Misalnya, untuk mencari gambar Ubuntu, ketik:

```bash
docker search ubuntu
```
Skrip akan merayapi Docker Hub dan mengembalikan daftar semua gambar yang namanya cocok dengan string pencarian. Dalam hal ini, hasilnya akan serupa dengan ini:

```
NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
ubuntu                            Ubuntu is a Debian-based Linux operating s...   3808      [OK]       
ubuntu-upstart                    Upstart is an event-based replacement for ...   61        [OK]       
torusware/speedus-ubuntu          Always updated official Ubuntu docker imag...   25                   [OK]
rastasheep/ubuntu-sshd            Dockerized SSH service, built on top of of...   24                   [OK]
ubuntu-debootstrap                debootstrap --variant=minbase --components...   23        [OK]       
nickistre/ubuntu-lamp             LAMP server on Ubuntu                           6                    [OK]
nickistre/ubuntu-lamp-wordpress   LAMP on Ubuntu with wp-cli installed            5                    [OK]
nuagebec/ubuntu                   Simple always updated Ubuntu docker images...   4                    [OK]
nimmis/ubuntu                     This is a docker images different LTS vers...   4                    [OK]
maxexcloo/ubuntu                  Docker base image built on Ubuntu with Sup...   2                    [OK]
admiringworm/ubuntu               Base ubuntu images based on the official u...   1                    [OK]

...
```
Di kolom RESMI, OK menunjukkan gambar yang dibuat dan didukung oleh perusahaan di belakang proyek. Setelah Anda mengidentifikasi gambar yang ingin Anda gunakan, Anda dapat mengunduhnya ke komputer Anda menggunakan perintah tarik, seperti:

```bash
docker pull ubuntu
```
Setelah gambar diunduh, Anda dapat menjalankan penampung menggunakan gambar yang diunduh dengan perintah lari. Jika gambar belum diunduh ketika buruh pelabuhan dieksekusi dengan perintah lari, klien Docker akan terlebih dahulu mengunduh gambar, lalu menjalankan penampung yang menggunakannya:

```bash
docker run ubuntu
```
Untuk melihat gambar yang telah diunduh ke komputer Anda, ketik:

```bash
docker images
```
