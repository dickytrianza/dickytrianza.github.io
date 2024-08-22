---
title: Konfigurasi Burp Suit ke Android Studio
date: 2024-08-22 13:00:00 +07:00
modified: 2024-08-22 13:00:00 +07:00
tags: [burpsuite, mobile, pentest, android]
description: All the services are free, a source code this site placed on github repository and intergration with netlify service, another service that you can use is github page for hosting your own static site.
---

# Pendahuluan

Dalam pengembangan aplikasi Android, keamanan merupakan aspek penting yang tidak boleh ditinggalkan. Setiap aplikasi yang berkomunikasi dengan server melalui jaringan harus dipastikan kemanannya agar data pengguna tetap terlindungi dari potensi ancaman. Disinilah peran penting tools keamanan salah satunya adalah Burp Suite.

Dalam artikel kali ini, saya akan membahas bagaimana melakukan konfigurasi antara Burp Suite dengan Android Studio. Mulai dari pengaturan proxy, import certificates, hingga troubleshooting.

# Persiapan Awal

**Instalasi Android Studio**
- Membuat device baru dengan API Level < 28 [(Mengapa downgrade?)](#mengapa-harus-downgrade-emulator).
- Android Emulator (32.1.13) Stable [(Mengapa downgrade?)](#mengapa-harus-downgrade-emulator).

**Instalasi Burp Suite**
- Community atau
- Professional.

# Pengaturan Proxy Burp Suite

Setelah semua persiapan awal telah siap, langkah selanjutnya adalah mengkonfigurasi Burp Suite agar dapat berfungsi sebagai proxy untuk Android Studio. Fungsi dari proxy disini memungkinkan Burp Suite menangkap dan manganalisis semua network traffic yang terjadi antara aplikasi Android dan server. Berikut adalah langkah-langkah untuk mengkonfigurasi Burp Suite:
1. Navigasi ke tab "Proxy", lalu pilih "Options". Di bawah "Proxy Listeners", secara default, Burp Suite sudah ada listener di localhost ('127.0.0.1') pada port '8080'. Jika port ini sudah digunakan di aplikasi yang lain, kalian bisa mengubahnya ke port yang berbeda.
2. Klik "Add" jika ingin membuat listener baru, atau klik "Edit" jika ingin mengubah listener yang telah dibuat.
3. Lalu pada opsi Bind to address pilih "Loopback Only" karena kita menggunakan Android virtual device atau emulator. Jika menggunakan physical address kita bisa menggunakan "All interfaces". 
4. Lalu Klik tombol OK untuk menyimpan settings.
5. selanjutnya kita perlu menambahkan CA certificate di Android. dengan cara klik tombol "Import/export CA certificate", pilih format DER dan simpan certificate tersebut dengan extensi `.crt`.
<img src="/assets/blog-images/konfigurasi-burpsuite-ke-android-studio/img1.png" alt="burp suite">
6. Transfer CA certificate yang sudah kita download sebelumnya ke emulator (misalnya dengan drag & drop file dari komputer ke emulator). Atau dengan command adb berikut:
```
adb push burp.crt /sdcard/download
```
7. Buka "Settings" > "Security" > "Encryption & credentials" > "Install a certificate" > "CA certificate". 
8. Pilih file certificate yang telah ditransfer dan instal.
<img src="/assets/blog-images/konfigurasi-burpsuite-ke-android-studio/gif4.gif" alt="android emulator">

# Pengaturan Proxy Emulator Android Studio

Setelah Burp Suite dikonfigurasi sebagai proxy, langkah selanjutnya adalah setting dari Android Studio agar network traffic dari emulator bisa terkoneksi dengan Burp Suite. Berikut adalah langkah-langkah untuk mengatur proxy di Android Studio:
1. Langkah pertama adalah navigasi ke "file" lalu klik "Settings".
2. Di sisi kiri, terdapat berbagai opsi pengaturan, pilih "Tools.".
3. Di tab Tools, akan ada beberapa opsi lagi, pilih "Emulator" dan hapus centang pada "Launch in the Running Devices tool window". Kemudian klik apply.
<img src="/assets/blog-images/konfigurasi-burpsuite-ke-android-studio/img2.png" alt="android studio">
4. Jalankan emulator, kemudian klik ikon tiga titik di sebelah kanan bawah tombol home. 
5. Tab "Extended Controls" akan terbuka, lalu pilih opsi "Settings".
6. Ke tab "Proxy", dalam settingan default settingan ini masih no proxy.
7. Pilih "Manual proxy configuration," lalu masukkan hostname ('127.0.0.1') dan port ('8080') sesuai dengan certificate Burp Suite yang telah kita buat.
8. Setelah itu, klik tombol "Apply". Jika Proxy Status menunjukkan "Success," maka konfigurasi berhasil. 
<img src="/assets/blog-images/konfigurasi-burpsuite-ke-android-studio/img3.png" alt="burp suite">
9. Sekarang coba lakukan intercept dengan Burp Suite, jika semuanya dilakukan dengan benar, Burp Suite pasti akan menerima network traffic dari request yang kita buat.
<img src="/assets/blog-images/konfigurasi-burpsuite-ke-android-studio/img5.png" alt="burp suite">

# Mengatasi Masalah yang Sering Muncul

Saat mengkonfigurasi Burp Suite dengan Android Studio, saya menemui beberapa masalah/error yang mungkin sangat umum, terutama terkait dengan pengaturan proxy, versi emulator, kompatibilitas API level pada emulator. Berikut adalah solusi untuk beberapa masalah yang sering ditemui:

**1. Masalah "Proxy is unreachble"**

Masalah ini sering muncul ketika kita mencoba mengarahkan network traffic aplikasi Android melalui Burp Suite, tetapi tidak ada koneksi yang berhasil.

**Penyebab Umum:**
- Setting proxy di emulator kurang benar.
- Port yang digunakan oleh Burp Suite diblokir oleh firewall atau digunakan oleh aplikasi lain.
- Masalah pada versi android emulator.

**Solusi:**
- Pastikan pengaturan proxy pada perangkat Android sesuai dengan pengaturan di Burp Suite (hostname: 127.0.0.1, port: 8080 atau port yang Anda atur).
- Pastikan listener di Burp Suite aktif dan mendengarkan pada port yang benar. Anda dapat memverifikasinya di tab "Proxy" > "Options" di Burp Suite.
- Pastikan tidak ada firewall atau software keamanan lain yang memblokir port yang digunakan oleh Burp Suite.
- Downgrade versi emulator, sebelumnnya saya menggunakan android emulator (34.2.16), setelah saya downgrade ke versi Android (32.1.13) Stable, status proxy berubah menjadi success. Source: [link](https://stackoverflow.com/questions/77878300/android-emulator-proxy-is-unreachable).


**2. API Level Harus Kurang dari versi 28**

Setelah Android 9 (API Level 28), Google membuat perubahan keamanan menjadi lebih ketat, yang membuat proses network testing menjadi lebih sulit. Salah satu perubahan tersebut adalah pembatasan pada penggunaan CA certificate yang tidak dipercaya secara default, seperti yang digunakan oleh Burp Suite.

**Penyebab Umum:**
- Emulator yang menggunakan API level 28 atau lebih tinggi, yang memerlukan langkah tambahan untuk menerima certificate dari Burp Suite, seperti menggunakan **openssl** untuk mengubah format dari PEM untuk mendapatkan hash. Nah, untuk mentransfer file tersebut dibutuhkan akses `adb root` karena kita perlu mengirim filenya ke directory `/system/etc/security/cacerts/`. Lagi-lagi saya mendapatkan error disini, seperti "adbd cannot run as root in production builds", padahal emulator sudah dalam keadaan root, tetapi tidak ada permission writable untuk directory `/system`.

**Solusi**
- Jika memungkinkan, gunakan emulator atau perangkat dengan API level di bawah 28 untuk menghindari pembatasan baru yang diperkenalkan pada versi Android yang lebih baru.
- Downgrade versi emulator.

# Mengapa harus Downgrade versi Emulator dan API level?
Menggunakan emulator dengan versi yang lebih lama, terutama dengan API level di bawah 28, dapat membantu mengatasi berbagai masalah yang muncul karena pembaruan keamanan di versi Android yang lebih baru.

**Alasan Downgrade:**
- Versi Android yang lebih lama cenderung lebih kompatibel dengan metode pengujian yang digunakan Burp Suite, karena tidak menerapkan pembatasan terhadap penggunaan CA certificates Burp Suite.
- Pada versi Android yang lebih baru, ada lebih banyak langkah yang harus diambil untuk memungkinkan Burp Suite bekerja sebagai proxy, terutama dalam hal menangani network traffic HTTPS. Singkatnya dengan menggunakan versi yang lebih lama menghindari kita dari step-step tambahan yang banyak dan ribet (kalo ada cara yang gampang, kenapa harus dibuat susah, ya ga?).

**Cara Downgrade Emulator**
- Buka Android Studio dan gunakan AVD Manager untuk membuat emulator baru dengan API level yang lebih rendah (misalnya, API 27 atau lebih rendah).
- Jika sudah memiliki emulator yang berjalan di API level tinggi, buat instalan emulator baru dengan versi yang lebih rendah, download [disini](https://developer.android.com/studio/emulator_archive), atau gunakan image yang sudah tersedia di AVD Manager. 
- Setelah download, extract files ke Android SDK directory di `~/Android/Sdk/` jika menggunakan (Linux/MacOS) atau `C:\Users\namalu\AppData\Local\Android\Sdk` jika menggunakan (Windows).

# Kesimpulan

Konfigurasi Burp Suite dengan Android Studio adalah langkah penting dalam pengujian kemanan aplikasi Android. Hal ini berguna untuk pentester menganalisis network traffic yang dihasilkan oleh aplikasi client. Namun, proses ini tidak selalu sederhana, terutama ketika menggunakan emulator atau perangkat Android modern.

Supaya lebih efektif, kita harus memahami dan mengatasi berbagai macam masalah seperti konfigurasi proxy yang belum benar, dan penanganan pembatasan-pembatasan security pada Android versi terbaru. Karena kehidupan ini tidak ada jalan yang selalu mulus semua butuh proses, dibalik proses itu ada yang namannya usaha, seperti halnya kita melakukan troubleshooting pada instalasi kali ini karena di balik setiap kesulitan, pasti ada kemudahan yang menunggu dengan senyum manis. 



