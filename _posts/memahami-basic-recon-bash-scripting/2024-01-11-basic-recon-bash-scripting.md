---
title: Basic Recon Bash Scripting
date: 2024-01-18 21:53:00 +07:00
modified: 2024-01-18 21:53:00 +07:00
tags: [bash, programming, linux]
description: All the services are free, a source code this site placed on github repository and intergration with netlify service, another service that you can use is github page for hosting your own static site.
---

# Basic Recon Bash Scripting

Di postingan kali ini saya akan membahas tentang cara recon dengan bash scripting. Sebelumnya, apa sih bash scripting itu? Jadi, bash scripting adalah sebuah file yang berisikan urutan urutan perintah program komputer yang ditulis baris demi baris. Kegunaan bash scripting ini untuk mempermudah kita dalam melakukan tindakan-tindakan, seperti menavigasi ke direktori tertentu, membuat folder, atau membuat perintah untuk menjalakankan suatu program. Dengan membuat script ini, kita dapat mengulangi urutan langkah dalam menjalankan program yang sama beberapa kali dengan efektif.

## Memulai dengan script yang sederhana

Disini saya akan memberikan contoh cara membuat recon script. Kita mulai dengan membuka text editor apa saja yang menurut kalian nyaman. Pertama kita mulai dengan menuliskan dengan tanda pagar atau hash mark '#' dan juga tanda seru atau exclamation mark '!'. Tujuan dari kedua simbol tersebut itu untuk mengizinkan *plaintext*
dan juga memberi tahu ke komputer bahwa kita menggunakan bash.

Saya akan mencoba ketika kita ingin mengeksekusi dua command sekaligus. Sebagai contoh, saya akan menggunakan mesin hackthebox yang sudah saya bahas sebelumnya, jika ingin membaca atau melihatnya terlebih dahulu bisa klik link disini [bizness](/dickytrianza.github.io/_posts/htb-writeups-bizness/2024-01-11-htb-writeup-bizness.md). Langkah selanjutnya, buat file dengan nama apa saja , disini saya menggunakan nama ``recon.sh`` dengan file yg berisikan seperti ini:

```bash
#!/bin/bash
nmap bizness.htb
dirsearch -u bizness.htb
```

Ketika akan menjalankan script pasti kalian akan menemui hal semacam ini ``permission denied ./recon.sh``, hal ini karena user bisa tidak mempunyai hak akses untuk mengeksekusi  script tersebut. Tapi hal ini dapat diatasi dengan cara memberikan hak akses untuk ke semua user dengan menjalankan perintah ``chmod +x recon.sh``. Selanjutnya kita dapat menjalankan script dengan seperti biasa. Namun, script tersebut masih kurang efektif karena hanya bisa memindai sesuai target atau domain yang ditentukan di dalam script, jika kita ingin mengganti target, maka kita juga harus memodifikasi script tersebut. Salah satu cara agar lebih efisien adalah dengan membuat input arguments ke dalam bash script agar user bisa dengan leluasa mengganti target sesuai yang diinginkan. 

Hal selanjutnya yang akan kita tambahkan ke dalam syntax sebelumnya adalah `$1` pada argumen pertama, dan `$2` untuk argumen kedua, dan seterusnya. Atau kalian juga bisa menggunakan `$@` yang berarti mengirim ke semua argumen, ada juga `$#` yang menandakan total jumlah dari argumen. Sebagai contohnya seperti berikut:

```bash
#!/bin/bash
nmap $1
dirsearch -u $1
```

Script tersebut akan mengirim sesuai target atau domain yang kalian tentukan. Langsung saja ketik perintah berikut:
``./recon.sh bizness.htb``

<img src="/assets/blog-images/basic-recon-bash-scripting/a1.png" alt="a1">

maka akan terlihat command tereksekusi dengan baik dengan nama domain `bizness.htb`. Sekarang kita akan sedikit lebih kreatif dengan memodifikasi script kita sebelumnya dengan menambahkan beberapa elemen. Dan Sebagai tambahan lagi, agar kita bisa menganalisis lebih lanjut dari output script yang kita buat, kita akan menyimpan output tersebut dalam bentuk file.

```bash           
#!/bin/bash
# Command-Line arguments
domain=$1
directory_name=$2
# Step 1: Membuat directory untuk menyimpan hasil scan
echo "Creating directory $directory_name" 
mkdir "$directory_name" 

# Step 2: Menjalankan nmap untuk melakukan network scanning
echo "Running nmap on $domain" 
nmap "$domain" > "$directory_name/nmap" # Menyimpan hasil scan nmap ke directory
echo "Nmap results saved to $directory_name/nmap" 

# Step 3: Menjalankan dirsearch untuk mencari directory dan enumerasi file
echo "Running dirsearch on $domain" 
dirsearch -u "$domain" --output="$directory_name/dirsearch" # Menyimpan hasil dirsearh ke directory
echo "Dirsearch results saved to $directory_name/dirsearch"
```

Untuk command eksekusinya sama saja seperti sebelumnya akan tetapi kita harus menambahkan nama file yang ingin kita buat seperti berikut.
``./recon.sh bizness.htb bizness``

<img src="/assets/blog-images/basic-recon-bash-scripting/a2.png" alt="a2">

<img src="/assets/blog-images/basic-recon-bash-scripting/a3.png" alt="a3">

Bisa dilihat dari script tersebut kita telah menyimpan hasil file dari nmap dan juga dirsearch sesuai dengan nama yang kita tentukan yang ada didalam script.

Kita dapat memodifikasi script tersebut sesuai dengan keinginanan kita, seperti menambahkan tanggal, atau menambahkan opsi tools apa saja yang ingin dijalankan. Berikut penambahan script dan penjelasan singkatnya:

```bash
#!/bin/bash
# Memberikan tangggal
TODAY=$(date)
echo "Scan dilakukan pada tanggal $TODAY"

# Command-Line arguments
domain=$1
directory_name=$2
tool=$3

# Membuat directory untuk menyimpan hasil scan
echo "Creating directory $directory_name"
mkdir "$directory_name"

# Menambahkan opsi untuk memilih tools yang ingin dijalankan
if [ "$tool" == "nmap-only" ]; then
    sudo nmap -p- -sC -sV -A --min-rate 5000 "$domain" > "$directory_name/nmap"
    echo "Nmap results saved to $directory_name/nmap"
elif [ "$tool" == "dirsearch-only" ]; then
    dirsearch -u "$domain" --output="$directory_name/dirsearch"
    echo "Dirsearch results saved to $directory_name/dirsearch"
else 
	echo "Nmap results saved to $directory_name/nmap"
    nmap -p- -sC -sV -A --min-rate 5000 "$domain" > "$directory_name/nmap"
    echo "Dirsearch results saved to $directory_name/dirsearch"
    dirsearch -u "$domain" --output="$directory_name/dirsearch"
fi
```

di modifikasi code ini, saya menambahkan argumen perintah baru yang bernama tool, yang mana mengizinkan kita untuk menjalankan spesifik program yang ingin dijalankan. Opsi yang tersedia:
- `nmap-only` untuk menjalankan Nmap saja
- `dirsearch-only` untuk menjalankan Dirsearch saja

## Konklusi

Pentingnya bash scripting terletak pada kemampuannya untuk menyerdehanakan tindakan-tindakan kompleks menjadi file script yang dapat dieksekusi dengan mudah. Dalam contoh recon script yang telah dibuat, kita dapat dengan cepat melakukan scanning terhadap target tertentu. Melalui pembahasan ini, saya harap tulisan ini dapat memberi manfaat dalam efisiensi dan flesibilitas dalam otomatisasi.