# logrotate-log-management
Di Linux, banyak aplikasi dan layanan sistem akan menyimpan file log.  File log ini memberikan wawasan administrator Linux tentang kinerja sistem mereka, dan sangat berharga saat memecahkan masalah.  Namun, file log bisa menjadi sangat berat dengan sangat cepat.  Misalnya, jika perangkat lunak server web Anda mencatat setiap kunjungan ke situs web Anda, dan Anda mendapatkan ribuan pemirsa per hari, akan ada terlalu banyak informasi untuk dimasukkan ke dalam satu file teks.

Di situlah perintah logrotate di Linux berperan.  logrotate akan secara berkala mengambil file log saat ini, mengganti namanya, mengompresnya secara opsional, dan menghasilkan file baru yang dapat digunakan aplikasi untuk terus mengirimkan lognya.  Perintah logrotate dipanggil secara otomatis dari cron, dan sebagian besar layanan memiliki konfigurasi rotasi log sendiri yang diterapkan saat diinstal.  Konfigurasi ini memberi tahu logrotate apa yang harus dilakukan dengan file log lama.  Misalnya, berapa banyak dari mereka yang harus disimpan sebelum dihapus, haruskah memampatkan file, dll.

Administrator sistem juga dapat menggunakan utilitas logrotate untuk kebutuhan mereka sendiri.  Misalnya, jika admin Linux menyiapkan skrip untuk dijalankan, dan skrip tersebut menghasilkan log secara teratur, dimungkinkan untuk menyiapkan logrotate untuk mengelola file log bagi kami.  Dalam tutorial ini, Anda akan mempelajari lebih lanjut tentang utilitas logrotate saat kita melihat contoh konfigurasinya untuk memutar log dari layanan yang kita terapkan.

# tahap installasi

Disini saya menggunakan OS Linux CentOS 7.

```
   Static hostname: localhost.localdomain
         Icon name: computer-vm
           Chassis: vm
        Machine ID: c2a2axxxxxxxxxxxxxxxxxxxxxxxxxxx
           Boot ID: 5d7c7xxxxxxxxxxxxxxxxxxxxxxxxxxx
    Virtualization: vmware
  Operating System: CentOS Linux 7 (Core)
       CPE OS Name: cpe:/o:centos:centos:7
            Kernel: Linux 3.10.0-1160.el7.x86_64
      Architecture: x86-64
```

### prerequisite
```
System      : Any Linux distro
Software    : logrotate
Other       : Privileged access to your Linux system as root or via the sudo command.
Conventions : – requires given linux commands to be executed with root privileges either directly as a root user or by use of sudo command
              – requires given linux commands to be executed as a regular non-privileged user
```       
```bash
sudo su
```

Logrotate membutuhkan progam cron untuk bekerja, kita tidak perlu melakukan install cron karena progam itu defaultnya sudah terinstall pada system. disni Install package `logrotate`.

```bash
yum install logrotate -y
```

logrotate adalah alat yang berguna untuk administrator sistem yang ingin mengambil direktori /var/log di bawah kendali mereka.  Perintah logrotate dipanggil setiap hari oleh penjadwal cron dan membaca file-file berikut:

- File konfigurasi logrotate /etc/logrotate.conf
- File di direktori konfigurasi logrotate /etc/logrotate.d

Sebagian besar layanan (server web Apache, postgreSQL, MySql, manajer desktop KDE, dll) yang diinstal pada sistem Anda membuat file konfigurasi untuk logrotate di /etc/logrotate.d.  Sebagai contoh, berikut adalah isi dari direktori /etc/logrotate.d:

```bash
ls /etc/logrotate.d
httpd  apport  apt  dpkg  lxd  rsyslog  ufw  unattended-upgrades messages
```

Katakanlah kita menjalankan layanan bernama "httpd” yang membuat file log bernama “acess.log” di dalam direktori /var/log/httpd.  Untuk memasukkan file log “httpd” dalam rotasi log, pertama-tama kita harus membuat file konfigurasi logrotate dan kemudian menyalinnya ke direktori /etc/logrotate.d.

```bash
cd /etc/logrotate.d/ && ls -lahsZ
```

konfigurasi log access pada httpd file dengan text editor `vim` atau yang lainnya.

```bash
vi /etc/logrotate/httpd
```

file konfigurasi akan tampak seperti berikut

```
/var/log/httpd/*.log {

    missingok
    size 5M
    rotate 7
    daily
    compress
    delaycompress
    notifempty
    create 660 gethuk gethuk
}
```

### Penjelasan
```
daily          : File log diputar setiap hari. 
weekly         : File log dirotasi jika hari kerja saat ini kurang dari hari kerja rotasi terakhir atau jika lebih dari seminggu telah berlalu sejak rotasi    
                 terakhir. Ini biasanya sama dengan memutar log pada hari pertama dalam seminggu, tetapi jika logrotate tidak dijalankan setiap malam, rotasi log 
                 akan terjadi pada kesempatan valid pertama.
monthly        : File log dirotasi saat logrotate pertama kali dijalankan dalam sebulan (biasanya pada hari pertama setiap bulan). 
notifempty     : Jangan memutar log jika kosong (ini mengesampingkan opsi ifempty). 
nocompress     : Versi lama file log tidak dikompresi. 
delaycompress  : Tunda kompresi file log sebelumnya ke siklus rotasi berikutnya. Ini hanya berpengaruh bila digunakan dalam kombinasi dengan kompres. Ini dapat 
                 digunakan ketika beberapa program tidak dapat diperintahkan untuk menutup file log-nya dan dengan demikian dapat terus menulis ke file log                        sebelumnya untuk beberapa waktu. 
compress       : Versi lama file log dikompres dengan gzip secara default. 
mail address   : Ketika log diputar keluar dari keberadaan, itu dikirimkan ke alamat. Jika tidak ada email yang harus dibuat oleh log tertentu, arahan nomail                      dapat digunakan.  
missingok      : Jika file log tidak ada, lanjutkan ke yang berikutnya tanpa mengeluarkan pesan kesalahan. 
size           : ukuran output
```

[baca lebih lanjut mengenai penjelasan](https://linux.die.net/man/8/logrotate)


# Advance usage

Setelah logrotate diimplementasikan, penjadwal cron pada sistem Anda akan menjalankan perintah secara otomatis sehingga logrotate dapat melakukan tugasnya. Biasanya, pengguna tidak perlu menjalankan logrotate secara manual. Namun, logrotate masih dapat dijalankan secara manual di terminal dan memiliki beberapa opsi berbeda yang dapat Anda gunakan. Sebagian besar ini hanya bermuara pada pengujian konfigurasi yang telah Anda terapkan. Periksa contoh di bawah ini untuk melihat cara kerjanya. 

Anda dapat bereksperimen dengan rotasi log (di luar tugas cron biasa) dengan memaksa eksekusi logrotate tanpa adanya file log untuk diputar. Untuk melakukannya, gunakan -fpilihan dan tentukan file konfigurasi yang ingin Anda gunakan.

```bash
logrotate -f /etc/logrotate.d/httpd
```
Jika Anda mengalami masalah, dan ingin men-debug, Anda dapat menggunakan opsi -d dengan logrotate . Ini akan mensimulasikan "uji coba" dan tidak benar-benar membuat perubahan apa pun. Sebaliknya, itu hanya akan menampilkan pesan debug untuk membantu pemecahan masalah. 

```bash
logrotate -d /etc/logrotate.d/httpd
```
```
reading config file httpd
Allocating hash table for state file, size 15360 B

Handling 1 logs

rotating pattern: /var/log/httpd/*.1  after 1 days (7 rotations)
empty log files are not rotated, old logs are removed
considering log /var/log/httpd/access_log.1
  log does not need rotating (log is empty)considering log /var/log/httpd/access_log.1.1
  log does not need rotating (log is empty)considering log /var/log/httpd/error_log.1
  log does not need rotating (log is empty)considering log /var/log/httpd/error_log.1.1
  log does not need rotating (log is empty)considering log /var/log/httpd/modsec_audit.log.1
  log does not need rotating (log is empty)considering log /var/log/httpd/modsec_audit.log.1.1
  log does not need rotating (log is empty)considering log /var/log/httpd/modsec_debug.log.1
```

Menggunakan pilihan opsi -v untuk mengaktifkan verbositas. Ini akan menampilkan pesan selama rotasi sehingga Anda dapat melihat dengan tepat apa yang terjadi. 

```bash
logrotate -v /etc/logrotate.d/httpd
```
```
reading config file httpd
Allocating hash table for state file, size 15360 B

Handling 1 logs

rotating pattern: /var/log/httpd/*.1  after 1 days (7 rotations)
empty log files are not rotated, old logs are removed
considering log /var/log/httpd/access_log.1
  log does not need rotating (log is empty)considering log /var/log/httpd/access_log.1.1
  log does not need rotating (log is empty)considering log /var/log/httpd/error_log.1
  log does not need rotating (log is empty)considering log /var/log/httpd/error_log.1.1
  log does not need rotating (log is empty)considering log /var/log/httpd/modsec_audit.log.1
  log does not need rotating (log is empty)considering log /var/log/httpd/modsec_audit.log.1.1
  log does not need rotating (log is empty)considering log /var/log/httpd/modsec_debug.log.1
  log does not need rotating (log is empty)set default create context
```

Dalam tutorial ini, kita belajar tentang logrotate di Linux, dan bagaimana itu digunakan untuk menjaga /var/logfile direktori di bawah kendali. Kami juga melihat cara mengatur logrotate untuk mengelola file log dari layanan kustom yang kami terapkan. Meskipun logrotate sebagian besar merupakan perintah yang bekerja di belakang layar, dipanggil secara diam-diam di cron, administrator sistem dapat memanfaatkan kekuatannya untuk menjaga file log mereka sendiri lebih teratur dan mudah dikelola. 
