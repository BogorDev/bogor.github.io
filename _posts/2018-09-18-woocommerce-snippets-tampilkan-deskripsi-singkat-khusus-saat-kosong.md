---
layout: post
title: 'WooCommerce Snippets: Tampilkan Deskripsi Singkat Khusus Saat Kosong'
date: 2018-09-18 13:54 +0700
author: Iman Sugirman
image: https://res.cloudinary.com/imans/image/upload/v1537255649/blog/woocommerce.png
header:
    layout: hero
    vh: vh-50 vh-50-m vh-80-l
    navigation: fancy
    image: https://res.cloudinary.com/imans/image/upload/v1537255649/blog/woocommerce.png
    caption: "[Free Pik](https://www.freepik.com/)"
    class: bg-darken-20 bg-darken-25-l
author_bio: true
categories:
    - Tutorial
    - Wordpress
tags:
    - Woocommerce
    - Snippets
    - Functions
---
Deskripsi singkat pada WooCommerce dibagian produk adalah bagian konten yang muncul di sisi kanan Featured Image di atas tombol tambahkan ke keranjang. Ini tentu saja dibutuhkan dan wajib untuk diisi, kecuali kamu lupa memasukkan deskripsi singkat di bawah Produk > Edit Produk! :cyclone:

Tetapi jika kamu lupa memasukkannya, atau secara alternatif ingin menampilkan deskripsi singkat global, berikut cuplikan PHP cepat untuk kamu.

![Woocommerce Snippets](https://res.cloudinary.com/imans/image/upload/v1537255313/blog/woocommercesn.png)

#### PHP Snippet: Tampilkan Keterangan Singkat Khusus Ketika Kosong @ Halaman Produk Tunggal WooCommerce

```php
/**
 * @snippet       Show Custom Content When Short Description Empty - WooCommerce
 * @how-to        Watch tutorial @ https://imans.me/woocommerce-snippets-tampilkan-deskripsi-singkat-khusus-saat-kosong
 * @sourcecode    https://imans.me
 * @author        Nakamura Agatha
 * @compatible    WooCommerce 3.4.5
 */

add_filter( 'woocommerce_short_description', 'bbloomer_echo_short_desc_if_no_short_desc' );

function bbloomer_echo_short_desc_if_no_short_desc( $post_excerpt ) {
    if ( empty ( $post_excerpt ) ) {
        $post_excerpt = '<p class="default-short-desc">';
        $post_excerpt .= 'This is the default, global, short description.<br>It will show if <b>no short description has been entered!</b>';
        $post_excerpt .= '</p>';
    }
    return $post_excerpt;
}
```

#### Di mana untuk menambahkan kode ini?
kamu dapat menempatkan snipet PHP di bagian bawah file `function.php` Child Theme Kamu (sebelum `?>` Jika kamu memilikinya). CSS, di sisi lain, masuk dalam file style.css Child Theme kamu. Pastikan kamu tahu apa yang kamu lakukan saat mengedit file yang rumit - jika kamu membutuhkan panduan lebih lanjut, silakan lihat tutorial video Kustomisasi WooCommerce gratis dari saya.

#### Apakah potongan ini masih berfungsi?
Tolong beri tahu saya di komentar jika semuanya bekerja seperti yang diharapkan. Saya telah menguji kode ini dengan tema Storefront, versi WooCommerce yang tercantum di atas dan hosting WordPress dengan versi :elephant: PHP 7.2.
Jika kamu pikir kode ini menghemat waktu kamu. Silahkan Like and Share Tutorial ini.
