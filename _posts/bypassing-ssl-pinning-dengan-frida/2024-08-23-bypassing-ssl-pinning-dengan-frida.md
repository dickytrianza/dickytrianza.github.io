---
title: Bypass SSL Pinning dengan Frida
date: 2024-08-26 13:00:00 +07:00
modified: 2024-08-26 13:00:00 +07:00
tags: [burpsuite, mobile, pentest, android, frida]
description: All the services are free, a source code this site placed on github repository and intergration with netlify service, another service that you can use is github page for hosting your own static site.
---

# Pendahuluan

Di era digital sekarang, keamanan data menjadi aspek penting yang tidak bisa diabaikan. Proses Autentikasi user, seperti login merupakan pintu gerbang utama yang melindungi data dan akses ke dalam sistem. Namun, tidak jarang metode login yang diterapkan oleh aplikasi memiliki celah keamanan yang bisa dieksploitasi oleh pihak yang tidak bertanggung jawab (kita sebut saja Hengker).

Salah satu teknik yang sering digunakan untuk menguji keamanan login adalah dengan melakukan intercept atau pengalihan terhadap data yang dikirimkan oleh aplikasi. Nah metode ini sering kali menjadi titik awal bagi si Hengker untuk memanipulasi kredensial atau mendapat akses tanpa izin. Lalu bagaimana jika aplikasi dilindungi dengan baik sehingga teknik intercept biasa tidak lagi efektif? Disinilah tool seperti Frida masuk ke dalam game kita, mwehehe.

Frida adalah sebuah dynamic instrumentation toolkit yang memungkinkan pentester untuk menginjeksi kode ke dalam proses aplikasi, baik itu di Android, iOS, Windows, ataupun Linux. Dengan Frida, kita dapat melakukan bypass terhadap berbagai mekanisme keamanan, termasuk login intercept yang telah diperketat oleh developer.

Dalam artikel ini, saya akan mengeksplorasi bagaimana Frida dapat digunakan untuk bypass SSL pinning, dengan contoh saya mengambil challenge 'Pinned' dari [Hack The Box](https://app.hackthebox.com/challenges/Pinned).

# Skenario Pinned dari Hack The Box

Pada challenge Pinned user diberikan aplikasi mobile yang memiliki sistem autentikasi. Pada aplikasi ini metode login yang digunakan telah dilindungi, sehingga Hengker tidak dapat dengan mudah mengintercept atau memanipulasi data yang dikirimkan. Tujuan dari challenge ini adalah untuk melakukan SSL pinning bypass, sehingga kita dapat mengintercept login request dan mendapatkan flag yang tersembunyi.

# Persiapan Awal

- Android Virtual Device atau Device Android asli (developer mode harus on)
- Burp Suite Community/Professional

# Instalasi dan Konfigurasi Frida

Ada dua komponen frida yang kita butuhkan, pertama adalah Frida tools yang dijalankan di sistem host. Kedua adalah Frida server yang dijalankan pada target host.

1. Instalasi Frida di Sistem Host.
Buka terminal atau cmd dan jalankan command berikut:
```
pip install frida-tools
```
Cek jika Frida sudah terinstal dengan benar atau belum.
```
frida --version
```
2. Instalasi Frida server di perangkat Android.
Download Frida server sesuai dengan arsitektur perangkat Android kalian dari [sini](https://github.com/frida/frida/releases).  
Copy file Frida yang telah di download ke perangkat android dengan menggunakan **adb**:
```
adb push frida-server /data/local/tmp
```
Menjalankan Frida server di Perangkat Android, dengan menggunakan command berikut.
```bash
adb shell
chmod +x /data/local/tmp/frida-server
/data/local/tmp/frida-server &
```

# Instalasi file APK "Pinned" dari Hack The Box

1. Download file APK "pinned" dari [Hack The Box](https://app.hackthebox.com/challenges/Pinned). Berdasarkan deskripsi dan judul dari challenge ini, tugas utama kita adalah bypass SSL pinning yang diterapkan dalam aplikasi. Download file `.zip` yang disediakan, dan unzip, kita menemukan sebuh file `.txt` yang memberi petunjuk bahwa API yang harus digunakan adalah <= 29. 
<img src="/assets/blog-images/bypass-ssl-pinning-login-dengan-frida/img1.png" alt="cmd"> Jika API yang digunakan > 29, maka ketika kalian menginstal APK-nya akan muncul pesan error seperti berikut.
<img src="/assets/blog-images/bypass-ssl-pinning-login-dengan-frida/img2.png" alt="error"> Tapi kalian bisa tetap melanjutkannya dengan menggunakan `zipalign`. Fungsi dari `zipalign` disini untuk menyelaraskan file dalam APK sehingga bisa diakses dengan lebih efisien. Install `zipalign` lalu jalankan command seperti berikut.
```
zipalign -p 4 pinned.apk aligned_pinned.apk
```
2. Membuat Keystore untuk signing APK. Agar APK dapat diinstal pada perangkat Android, kita perlu melakukan signing keystore pada APK menggunakan `apksigner`. Fungsi signing ini untuk memastikan integritas dan otentikasi pada aplikasi tersebut. Tapi sebelum melakukan signing kita perlu membuat keystorenya terlebih dahulu dengan `keytool`, jalankan command berikut:
```
keytool -genkey -keystore a.keystore -keyalg RSA -keysize 2048 -validity 10000
```
Lalu apply keystore ke `aligned_pinned.apk` dengan menggunakan `apksigner`.
```
apksigner sign --ks a.keystore aligned_pinned.apk
```
3. Setelah semua step diatas selesai, install APK dengan menggunakan command adb, lalu cek pada androidmu apakah APK sudah terinstal dengan benar atau belum.
```
adb install aligned_pinned.apk
```
<img src="/assets/blog-images/bypass-ssl-pinning-login-dengan-frida/img3.png" alt="android">

# Bypass SSL Pinning dengan Frida

Ketika kita mencoba login di aplikasi Pinned, muncul output "Logged in". Berdasarkan deskripsi challenge, langkah selanjutnya adalah mengintercept request login untuk mengetahui apa isi dari password yang kemungkinan merupakan flag dari challenge ini. Untuk mengintercept request tersebut, kita dapat menggunakan Burp Suite. Panduan konfigurasi Burp Suite ke Android dapat dibaca [di sini](https://dickytrianza.my.id/konfigurasi-burpsuite-ke-android-studio/). Kembali ke login intercept, kita menemukan bahwa request login pada aplikasi Pinned tidak bisa diintercept karena adanya SSL pinning yang melindungi aplikasi. Di sinilah Frida berperan penting untuk melewati mekanisme pinning tersebut.

Untuk melakukan bypass SSL pinning, kita akan menggunakan script dari Frida codeshare yang telah dikenal bisa untuk melewati mekanisme ini. Script nya bisa di copy di [Frida Codeshare](https://codeshare.frida.re/@pcipolloni/universal-android-ssl-pinning-bypass-with-frida/)

Dari codeshare tersebut kita membutuhkan binary atau identifier dari aplikasi Pinned:
```
frida --codeshare pcipolloni/universal-android-ssl-pinning-bypass-with-frida -f YOUR_BINARY
```

Untuk melihat binary atau identifier bisa menggunakan command "frida-ps -Uai
<img src="/assets/blog-images/bypass-ssl-pinning-login-dengan-frida/img4.png" alt="binary">

Setelah mengetahui binary aplikasinya, jalankan command berikut untuk menginjeksi script ke dalam aplikasi yang berjalan:
```
frida --codeshare pcipolloni/universal-android-ssl-pinning-bypass-with-frida -f com.example.pinned -U
```
<img src="/assets/blog-images/bypass-ssl-pinning-login-dengan-frida/img5.png" alt="frida">

Dengan menjalankan command di atas, Frida akan menginjeksi code yang diperlukan untuk mem-bypass SSL pinning. Kembali ke Burp Suite, buat request intercept kembali, sekarang seharusnya kita dapat melihat request login yang dikirim, dan juga termasuk password yang diperlukan untuk menyelesaikan challenge ini.

<img src="/assets/blog-images/bypass-ssl-pinning-login-dengan-frida/img6.png" alt="burp suite">


# Kesimpulan

Dari Challenge 'Pinned' kita belajar memahami mekanisme keamanan seperti SSL pinning dalam pengujian aplikasi mobile. Meskipun aplikasi tampak sederhana dengan hanya menampilkan output "Logged in," di balik layar terdapat lapisan perlindungan yang mencegah kita mengintercept data sensitif. Dengan menggunakan Frida, kita berhasil mem-bypass SSL pinning dan mendapatkan akses ke network traffic yang diperlukan untuk menyelesaikan challenge ini. Proses ini tidak hanya memperkuat skill teknis kita dalam menggunakan tool seperti Frida dan Burp Suite, tetapi juga belajar akan pentingnya ketelitian dalam menganalisis setiap langkah dalam pengujian aplikasi. 






