---
layout: post
title: Tutorial Bagaimana Custom Devise Path dan Controller pada Ruby on Rails
date: 2018-08-28 17:06 +0700
author: Iman Sugirman
image: https://res.cloudinary.com/wpcloudserver/image/upload/v1535556842/customdeviserails.jpg
header:
    layout: hero
    vh: vh-50 vh-50-m vh-80-l
    navigation: fancy
    image: https://res.cloudinary.com/wpcloudserver/image/upload/v1535556842/customdeviserails.jpg
    caption: "[NESA by Makers](https://unsplash.com/@nesabymakers)"
author_bio: true
categories:
    - Tutorial
tags:
    - MacOS
    - Ruby
    - Ruby on Rail
---
Ruby on Rails telah dikembangkan pada tahun 2003 oleh David Heinemeier Hannson dan sejak saat itu diperluas oleh Rails Core Team dan lebih dari 3700 kontributor.

Ruby on Rails menyediakan beberapa `gem` untuk Autentikasi. Contohnya adalah :
* [Devise](https://github.com/plataformatec/devise) Github Star : 19.013
* [Sorcery Gems](https://github.com/Sorcery/sorcery) Github Star : 565
* [Clearance](https://github.com/thoughtbot/clearance) Github Star : 2.927

Dan yang paling banyak digunakan adalah `Devise`, Selain Mudah `Devise` jg bisa dikatakan sangat gampang untuk dikembangkan. Bayangkan saya ketika anda ingin mengintegrasikan dengan Platform lain misalnya Social Media bisa diintegrasikan dengan [OmniOuth](https://github.com/omniauth/omniauth)

### Cara Menginstall Devise di Ruby on Rails 5.2

Mudah saja, hanya menambahkan baris
```ruby
...
gem "devise"
...

```
pada `Gemfile` anda. Dan masukan perintah
```bash
bundle install
```
Jangan Lupa install Devise dengan cara memasukan perintah :

```bash
rails g devise:install
```
Generate User Model Default dari Devise, biasanya dengan model `User` :
```bash
rails g devise User
```
Sesuaikan `Model` anda sesuai dengan kebutuhan dan `Database` anda sesuai dengan kebutuhan. Biasanya saya aktifkan mode Konfirmasi Email untuk setiap pendaftar :

Ubah Model User
```ruby
class User < ActiveRecord::Base
  devise :database_authenticatable,
    :registerable,
    :confirmable,
    :recoverable,
    :rememberable,
    :trackable,
    :validatable
end
```
Ubah File Database

```ruby
## Confirmable
t.string   :confirmation_token
t.datetime :confirmed_at
t.datetime :confirmation_sent_at
t.string   :unconfirmed_email # Only if using reconfirmable

## Lockable
t.integer  :failed_attempts, default: 0, null: false # Only if lock strategy is :failed_attempts
t.string   :unlock_token # Only if unlock strategy is :email or :both
t.datetime :locked_at
```
maka uncomment semua baris diatas

Lalu migrasikan database dengan cara :

```bash
rails db:migrate
```

Generate View dari Setelan Default `Devise`

```bash
rails g devise:views
```
Ubah `routes.rb` di folder `config` dengan menuliskan beberapa baris seperti :

```ruby
devise_for :users,
  :path => "",
  :path_names => {
    :sign_in => 'login',
    :sign_out => 'logout',
    :password => 'secret',
    :confirmation => 'verification',
    :unlock => 'unblock',
    :registration => 'register',
    :sign_up => 'cmon_let_me_in' }
```
