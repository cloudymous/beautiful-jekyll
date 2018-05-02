---
layout: post
title: Pengalaman Install Firebird 1.5 di Ubuntu Server 14.04 64 Bit
tags:
  - Kerjaan
  - Server
  - Database
published: true
---

Menurut [wikipedia](https://en.wikipedia.org/wiki/Firebird_(database_server))

> Firebird is an open source SQL relational database management system that runs on Linux, Microsoft Windows, Mac OS X and a variety of Unix. The database forked from Borland's open source edition of InterBase in 2000, but since Firebird 1.5 the code has been largely rewritten

Kalau boleh jujur, ini adalah pertama kali saya tahu kalau ada RDMS lain selain MySQL dan MariaDB. Kantor saya menggunakan aplikasi yang sistem databasenya menggunakan Firebird. Entah apa yang jadi pertimbangan untuk menggunakan Firebird.

Versi Firebird yang digunakan pun terbilang lawas, Firebird 1,5. Sedangkan yang ada di repo ubuntu cuma versi 2.5. Saya juga belum mencoba apakah sistem di kantor bisa berjalan dengan versi 2.5 atau tidak. Tapi saya tidak mau ambil resiko. Lagipula sistem di hotel harus up 24 jam. Tidak berani saya untuk coba-coba.

Sebelumnya saya tampilkan spesifikasi server:
-__Ubuntu Server 14.04.5 64 Bit__
-__Intel(R) Xeon(R) CPU X3430 @ 2.40 GHz, 4 Cores__
-__RAM 4 GB__
-__Untuk Penyimpanan saya menggunakan 2 HDD 500 GB yang disetup RAID 0__

Oke kita langsung saja.

Pertama-tama kita install paket **lib32ncurses5** dan **openbsd-inetd**.

~~~
root@host:/# apt-get install lib32ncurses5 openbsd-inetd
~~~

Setelah itu kita install paket libstdc untuk 32 bit.

~~~
root@host:/# apt-get install libstdc++6:i386
~~~


Untuk menginstall Firebird, kita butuh versi 5.x untuk http://gcc.gnu.org/libstdc++/. Menurut sumber yang saya baca, versi ini sudah tidak diinclude di Ubuntu versi 8.04 >. Kita butuh sedikit trik untuk memasukkan librarynya.
Pertama, Download file **libstdc++5_3.3.6-17ubuntu1_i386.deb** secara manual di

> old.releases.ubuntu.com/ubuntu/pool/universe/g/gcc-3.3/

Setelah terdownload, extract dengan perintah

~~~
root@host:~# ar xv libstdc+5_3.3.6-17ubuntu1_i386.deb
root@host:~# tar xzvf data.tar.gz
~~~

Perintah diatas akan membuat libstdc menjadi data.tar.gz yang kemudian di-untar(extract) kembali

Masuk ke directory yang tadi di-extract. Pastikan kalau paketnya 32 bit.

~~~
root@host:~# cd usr/lib
root@host:/usr/lib# file libstdc++.so.5.0.7
libstdc++.so.5.0.7: ELF 32-bit LSB shared object, Intel 80386, version 1 (SYSV), dynamically linked, stripped
~~~

Dari pesan diatas dipastikan bahwa paket adalah 32-bit.

Copy file dalam folder lib tadi ke /usr/lib dengan perintah

~~~
root@host:~/home/{username}/{extract folder}/usr/lib#cp * /usr/lib
~~~

Pastikan file libstdc++.so.5.0.7 berada di /usr/lib

~~~
root@host:~#ls -la /usr/lib
~~~

Jika libstdc sudah berada di folder /usr/lib artinya semua lib sudah terpenuhi.

Sekarang semua lib yang diperlukan telah selesai. Waktunya untuk install Firebird

**Install Firebird**

Buka website [Firebird](http://www.firebirdsql.org) dan Download [FirebirdCS-1.5.6.5026-0.i686.tar.gz](http://sourceforge.net/projects/firebird/files/firebird-linux-i386/1.5.6-Release/FirebirdCS-1.5.6.5026-0.i686.tar.gz/download). Simpan di host dan extract.

~~~
root@host:~# tar xzvf FirebirdCS-1.5.6.5026-0.i686.tar.gz
root@host:~# cd FirebirdCS-1.5.6.5026-0.i686
root@host:~# ./install.sh
~~~

Proses install akan berlangsung, masukkan password untuk SYSDBA. Saya menggunakan password default __masterkey__. 

Langkah terakhir tambahkan Firebird di **PATH**. Buka file **/etc/profile** dan tambahkan.

> PATH=$PATH:/opt/firebird/bin


Seharusnya firebird sudah jalan cek dengan

~~~
root@host:~# service firebird start
~~~

Atau coba dengan membuka database testing Firebird

~~~
root@host:~# isql -user SYSDBA -pass PASSWORDKAMU localhost:employees

Atau

root@host:~# telnet localhost 3050
~~~

Selamat mencoba, semoga bermanfaat.

**Referensi.**

- https://www.gefoo.org/install-firebird-1-5-classic-server-on-ubuntu-64bit.html
- http://askubuntu.com/questions/522372/installing-32-bit-libraries-on-ubuntu-14-04-lts-64-bit
