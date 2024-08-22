---
title: Rooting AVD Devices Menggunakan Magisk
date: 2024-08-19 15:00:00 +07:00
modified: 2024-08-22 15:00:00 +07:00
tags: [rooting, mobile, magisk, pentest]
description: All the services are free, a source code this site placed on github repository and intergration with netlify service, another service that you can use is github page for hosting your own static site.
---

<img src="/assets/blog-images/rooting-avds-menggunakan-magisk/img0.png" alt="magisk">

# Apa itu Magisk?

Magisk adalah alat open-source yang digunakan untuk mendapatkan akses root pada perangkat Android. Dengan akses root, pengguna mempunyai hak kontrol penuh pada sistem operasi Android. Fungsi dari root ini memungkinkan pengguna untuk mengubah dan memodifikasi hampir setiap aspek dari perangkat mereka.

Berbeda dengan metode rooting yang lainnya, Magisk menawarkan fitur "systemless root", yang berarti perubahan yang dilakukan tidak mengubah file sistem asli dari perangkat. Dengan adanya fitur ini dapat membuat perangkat lebih aman dan meminimalkan risiko mengalami kerusakan.

Selain itu, Magisk memiliki fitur "MagiskHide", yang memungkinkan pengguna menyembunyikan status root dari aplikasi tertentu, seperti aplikasi perbankan atau Google Play, yang biasanya memblokir akses jika perangkat terdeteksi telah di-root. Magisk juga dilengkapi dengan modul yang bisa diundur untuk menambahkan berbagai fitur dan fungsionalitas tambahan ke perangkat Android.

# Persiapan Sebelum Rooting

1. Buka Android Studio, pada bagian tools lalu ke Device Manager.
<img src="/assets/blog-images/rooting-avds-menggunakan-magisk/img1.png" alt="device manager">
2. Pada menu Device Manager, klik "create new device" dan pilih device yang kamu inginkan.
<img src="/assets/blog-images/rooting-avds-menggunakan-magisk/img2.png" alt="device manager">
3. Selanjutnya pada menu System Image, pilih sesuai dengan selera mu, dan pastikan jangan memilih API Level 28 (Pie) karena versi ini sudah tidak kompatibel dengan tools **rootAVD** yang akan kita gunakan nantinya.
<img src="/assets/blog-images/rooting-avds-menggunakan-magisk/img3.png" alt="system image">
4. Setelah instalasi system image, jalankan device android.
<img src="/assets/blog-images/rooting-avds-menggunakan-magisk/img4.png" alt="device manager">

# Langkah-langkah Instalasi Magisk
1. Disini seperti yang saya bilang sebelumnya kita akan menggunakan tool [rootAVD](https://gitlab.com/newbit/rootAVD), yang merupakan script untuk menjalakan root pada AVDs dengan emulator dari Android Studio.
<img src="/assets/blog-images/rooting-avds-menggunakan-magisk/img5.png" alt="gitlab rootavd">
2. Clone repository rootAVD dengan command seperti berikut:
```
git clone https://gitlab.com/newbit/rootAVD.git
```
```bash
total 11210
drwxr-xr-x 1 dadit 197609        0 Aug 19 10:59 ./
drwxr-xr-x 1 dadit 197609        0 Aug 19 10:33 ../
drwxr-xr-x 1 dadit 197609        0 Aug 19 10:34 .git/
-rw-r--r-- 1 dadit 197609       16 Aug 19 10:34 .gitattributes
-rw-r--r-- 1 dadit 197609       67 Aug 19 10:34 .gitignore
drwxr-xr-x 1 dadit 197609        0 Aug 19 10:59 Apps/
-rw-r--r-- 1 dadit 197609     6644 Aug 19 10:34 CompatibilityChart.md
-rw-r--r-- 1 dadit 197609    35823 Aug 19 10:34 LICENSE
-rw-r--r-- 1 dadit 197609 11278270 Aug 19 10:34 Magisk.zip
-rw-r--r-- 1 dadit 197609    36285 Aug 19 10:34 README.md
-rw-r--r-- 1 dadit 197609    19013 Aug 19 10:34 rootAVD.bat
-rwxr-xr-x 1 dadit 197609    82859 Aug 19 10:34 rootAVD.sh*
```
3. Jalankan perintah berikut jika menggunakan Linux/MacOS.
```bash
Command Examples:
./rootAVD.sh
./rootAVD.sh ListAllAVDs
./rootAVD.sh InstallApps
./rootAVD.sh system-images/android-33/google_apis_playstore/x86_64/ramdisk.img
./rootAVD.sh system-images/android-33/google_apis_playstore/x86_64/ramdisk.img FAKEBOOTIMG
./rootAVD.sh system-images/android-33/google_apis_playstore/x86_64/ramdisk.img DEBUG PATCHFSTAB GetUSBHPmodZ
./rootAVD.sh system-images/android-33/google_apis_playstore/x86_64/ramdisk.img restore
./rootAVD.sh system-images/android-33/google_apis_playstore/x86_64/ramdisk.img InstallKernelModules
./rootAVD.sh system-images/android-33/google_apis_playstore/x86_64/ramdisk.img InstallPrebuiltKernelModules
./rootAVD.sh system-images/android-33/google_apis_playstore/x86_64/ramdisk.img InstallPrebuiltKernelModules GetUSBHPmodZ PATCHFSTAB DEBUG
./rootAVD.sh system-images/android-33/google_apis_playstore/x86_64/ramdisk.img AddRCscripts
```
4. Jalankan perintah berikut jika menggunakan Windows.
```bash
Command Examples:
rootAVD.bat
rootAVD.bat ListAllAVDs
rootAVD.bat InstallApps
rootAVD.bat system-images\android-33\google_apis_playstore\x86_64\ramdisk.img
rootAVD.bat system-images\android-33\google_apis_playstore\x86_64\ramdisk.img FAKEBOOTIMG
rootAVD.bat system-images\android-33\google_apis_playstore\x86_64\ramdisk.img DEBUG PATCHFSTAB GetUSBHPmodZ
rootAVD.bat system-images\android-33\google_apis_playstore\x86_64\ramdisk.img restore
rootAVD.bat system-images\android-33\google_apis_playstore\x86_64\ramdisk.img InstallKernelModules
rootAVD.bat system-images\android-33\google_apis_playstore\x86_64\ramdisk.img InstallPrebuiltKernelModules
rootAVD.bat system-images\android-33\google_apis_playstore\x86_64\ramdisk.img InstallPrebuiltKernelModules GetUSBHPmodZ PATCHFSTAB DEBUG
```
5. Karena disini saya menggunakan Windows maka perintah yang akan saya gunakan adalah: 
```
rootAVD.bat system-images\android-34\google_apis\x86_64\ramdisk.img
```
<img src="/assets/blog-images/rooting-avds-menggunakan-magisk/img6.png" alt="gitlab rootavd">
6. Setelah itu, AVDs akan otomatis tertutup , dan saat menjalankannya kembali, Magisk sudah terinstall pada perangkat android.
<img src="/assets/blog-images/rooting-avds-menggunakan-magisk/img7.png" alt="android emulator">

# Cara Memeriksa Status Root 

1. Menggunakan aplikasi RootChecker.
<img src="/assets/blog-images/rooting-avds-menggunakan-magisk/img8.png" alt="root checker">
2. Menggunakan adb shell.

<img src="/assets/blog-images/rooting-avds-menggunakan-magisk/img9.png" alt="root checker">

# Kesimpulan

Rooting memberikan akses penuh ke sistem file dan pengaturan yang biasanya dibatasi. Hal ini sangat penting bagi pentester untuk mengeksplorasi dan mengidentifikasi potensi kerentanan pada level sistem yang tidak dapat diakses tanpa hak root. Beberapa jenis kerentanan seperti privilege escalation, memerlukan akses root untuk dieksploitasi.

Rooting memungkinkan pentester untuk memodifikasi aplikasi yang terinstal, seperti memanipulasi data aplikasi atau menguji keamanan aplikasi yang membutuhkan privilege lebih. Dalam pentest keputusan untuk melakukan rooting harus diambil dengan hati-hati, dengan pemahaman yang jelas tentang tujuan pengujian, serta dengan langkah-langkah untuk memitigasi risiko dan menjaga keamanan data yang diuji.






 