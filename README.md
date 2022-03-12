# logrotate-log-management
Di Linux, banyak aplikasi dan layanan sistem akan menyimpan file log.  File log ini memberikan wawasan administrator Linux tentang kinerja sistem mereka, dan sangat berharga saat memecahkan masalah.  Namun, file log bisa menjadi sangat berat dengan sangat cepat.  Misalnya, jika perangkat lunak server web Anda mencatat setiap kunjungan ke situs web Anda, dan Anda mendapatkan ribuan pemirsa per hari, akan ada terlalu banyak informasi untuk dimasukkan ke dalam satu file teks.   Di situlah perintah logrotate di Linux berperan.  logrotate akan secara berkala mengambil file log saat ini, mengganti namanya, mengompresnya secara opsional, dan menghasilkan file baru yang dapat digunakan aplikasi untuk terus mengirimkan lognya.  Perintah logrotate dipanggil secara otomatis dari cron, dan sebagian besar layanan memiliki konfigurasi rotasi log sendiri yang diterapkan saat diinstal.  Konfigurasi ini memberi tahu logrotate apa yang harus dilakukan dengan file log lama.  Misalnya, berapa banyak dari mereka yang harus disimpan sebelum dihapus, haruskah memampatkan file, dll.   Administrator sistem juga dapat menggunakan utilitas logrotate untuk kebutuhan mereka sendiri.  Misalnya, jika admin Linux menyiapkan skrip untuk dijalankan, dan skrip tersebut menghasilkan log secara teratur, dimungkinkan untuk menyiapkan logrotate untuk mengelola file log bagi kami.  Dalam tutorial ini, Anda akan mempelajari lebih lanjut tentang utilitas logrotate saat kita melihat contoh konfigurasinya untuk memutar log dari layanan yang kita terapkan.