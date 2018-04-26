# Modul 5 Jaringan Komputer 2018

## **Table of Content**
- Definisi
- Mengapa perlu firewall?
- Cara Kerja Firewall
- Jenis-jenis Firewall

## **Definisi**
Berdasarkan [RFC 2828](http://www.faqs.org/rfcs/rfc2828.html)
```
(I) An internetwork gateway that restricts data communication
    traffic to and from one of the connected networks (the one said to
    be "inside" the firewall) and thus protects that network's system
    resources against threats from the other network (the one that is
    said to be "outside" the firewall). (See: guard, security
    gateway.)
```

## **Mengapa perlu firewall?**
Firewall diperlukan karena keamanan, diantara pertimbangan adanya firewall adalah:
- Pencurian data pada jaringan internal
- Pengaksesan data oleh orang yang tidak berhak
- Denial of Service


## **Firewall**

![Ilustrasi](img/illustration.jpg)
Gambar 1. Ilustrasi Firewall (sumber: https://opensourceforu.com/2016/07/implementing-a-software-defined-network-sdn-based-firewall/)

Firewall adalah suatu **mekanisme untuk melindungi keamanan jaringan komputer** dengan mengizinkan lalu lintas jaringan yang dianggap aman untuk melaluinya dan mencegah lalu lintas jaringan yang tidak aman. Pengaturan lalu lintas paket tersebut berupa menyaring paket data yang keluar dan masuk di jaringan. Paket data yang ’baik’ diperbolehkan untuk melewati jaringan dan paket dapa yang  dianggap ’jahat’ tidak diperbolehkan melewati jaringan. Firewall dapat berupa perangkat lunak atau perangkat keras yang ditanam perangkat lunak untuk memfilter paket data. Umumnya, sebuah firewall diimplementasikan dalam sebuah mesin terdedikasi, yang **berjalan pada pintu gerbang (gateway)** antara jaringan lokal dan jaringan lainnya. Firewall umumnya juga digunakan untuk **mengontrol akses terhadap siapa** saja yang memiliki akses terhadap jaringan pribadi dari pihak luar.

Cara-cara firewall dalam melindungi jaringan komputer internal, antara lain :
- Menolak dan memblokir paket data yang datang berdasarkan sumber dan tujuan yang tidak diinginkan.
- Menolak dan menyaring paket data yang berasal dari jaringan internal ke internet. Misal, ketika ada pengguna jaringan internet akan mengakses situs yang tidak baik.
- Menolak dan menyaring paket data berdasakan konten yang tidak diinginkan. Misal, firewall yang terintegrasi pada suatu antivirus akan menyaring dan mencegah file yang sudah terjangkit virus memasuki jaringan internal.
- Melaporkan semua aktivitas jaringan dan kegiatan firewall (log).

## **Jenis-jenis Firewall**

Firewall mempunyai beberapa tipe untuk melindungi jaringan, antara lain :
1. **Packet-Filtering Firewall**
adalah tipe firewall yang memeriksa dan membandingkan alamat sumber dari paket lewat dengan aturan atau kebijakan  yang telah terdaftar pada filtering firewall. Pada firewall tipe ini akan diatur apakah paket data tersebut akan diperbolehkan lewat atau menolaknya. Firewall ini bekerja pada *network layer* OSI. Pada Linux, packet filtering firewall ditanamkan pada kernel (sebagai modul kernel, atau digabungkan ke dalam kernel) dan dapat diatur menggunakan **IPTables** yang merupakan paket aplikasi *built-in* pada Linux. Aturan atau kebijakan pemeriksaan paket data berdasarkan informasi yang dapat ditangkap dari packet header, antara lain :
    - IP address sumber dan tujuan.
    - Nomor port TCP/UDP sumber dan tujuan.
    - Tipe ICMP message.

2. **Application Filter Firewall**
Firewall ini bekerja pada *application layer* OSI sehingga dapat menyaring aplikasi-aplikasi yang dipakai untuk akses ke Internet. Tipe firewall ini umumnya agak mahal harganya karena lebih kompleks.

3. **Proxy Firewall**
Fasilitas proxy server menggunakan perantara (proxy) sebagai jembatan hubungan antara LAN dan WAN/Inernet.  Proxy server umumnya berfungsi pada lapisan aplikasi, oleh sebab itu sering juga disebut application firewall. Jika packet filter hanya berfungsi menyaring paket-paket yang diterima tanpa mengubah paket-paket tersebut, proxy server menerima dan mengubah alamat paket dengan memberikan alamat proxy server. Dengan demikian, menyembunyikan alamat pengirim yang sebenarnya. Untuk itu, proxy server umumnya mempergunakan metode yang dinamakan Network Address Translation (NAT), yang berfungsi untuk menyembunyikan IP address pribadi yang dipakai oleh LAN. Selain memeriksa alamat paket, proxy server juga memeriksa isi paket.

4. **Stateful Inspection Firewall**
Firewall dengan metode stateful inspection ini bekerja di antara lapisan data link dan network referensi model OSI. Jika suatu paket data diterima, langkah pertama yang dilakukan oleh stateful inspection ini adalah memeriksa informasi header paket data dengan tabel state untuk melihat apakah sudah ada jalur yang tersedia untuk paket tersebut. Jika jalur sudah tersedia, maka stateful inspection membuat asumsi bahwa paket boleh diterima dan diteruskan ke tujuannya. Jika jalur belum tersedia, maka stateful inspection mencocokkan paket data dengan peraturan keamanan (security policy) yang telah dibuat untuk menentukan apakah paket mendapat izin untuk diteruskan. Stateful inspection terus-menerus mengawasi setipa koneksi yang terjadi dan membuat catatan pada tabel status yang dimilikinya.

Kali ini kita akan mempelajari bagaimana **Packet-Filtering Firewall** menggunakan `$ iptables`.

## **Packet-Filtering Firewall**

![Struktur IPTables pada Komputer](/img/netfilter-iptables-diagram-a.jpg)

Iptables adalah suatu tools dalam sistem operasi linux yang berfungsi sebagai alat untuk melakukan penyaringan (filter) terhadap lalu lintas (traffic) data. Secara sederhana, iptables digambarkan sebagai pengatur lalu lintas data. Aturan-aturan lalu lintas pada iptables, berada dalam sebuah tabel, dimana ***table*** adalah sekelompok ***chain*** dan ***chain*** adalah sekelompok ***rules***. Pada *high-level* iptables, memungkinkan terdapat *multiple tables* dengan *multiple chains*. Secara *default*, iptables berjalan tanpa *rules* apapun.

Struktur kerja IPTables,

    iptables -> Tables -> Chains -> Rules

dapat digambarkan dengan struktur seperti ini.

![IPTables Structure](/img/iptables-table-chain-rule-structure.png)

### IPTables Configuration
---
#### Tables and Chains

IPTables mempunyai 4 *built-in tables*.

1. **Filter Table**

    Table ini adalah tabel default pada iptables. Jadi, jika kita tidak mendefinisikan table yang kita gunakan pada iptables, maka secara default menggunakan Filter table. Filter Table memiliki *built-in chain*, yaitu :
    - **INPUT** chain – Untuk memfilter paket yang menuju jaringan lokal.
    - **OUTPUT** chain – Untuk memfilter paket yang dari jaringan lokal ke jaringan luar.
    - **FORWARD** chain – Untuk memfilter paket yang hanya akan diteruskan (melewati) firewall.
    ![Chain Illustration](/img/iptabes-tutorial-input-forward-output.jpg)

2. **NAT Table**

    NAT Table berfungsi untuk mentranslasikan jaringan lokal yang melewati firewall menuju jaringan luar. NAT Table memiliki *built-in chain*, yaitu :
    - **PREROUTING** chain – Untuk mengubah paket data sebelum paket tersebut melalui *route* tujuannya. Paket ditranslasikan langsung saat masuk sistem. Chain ini akan mentranslasikan IP address tujuan paket ke IP address yang sesuai dengan *routing* pada jaringan lokal. Chain ini digunakan untuk DNAT (destination NAT).
    - **POSTROUTING** chain – Untuk mengubah paket data setelah paket tersebut melalui *route* tujuannya. Paket ditranslasikan saat menuju ke luar sistem. Chain ini akan mentranslasikan IP address dari asalnya (jaringan lokal) paket data ke IP address yang sesuai dengan *routing* ke server tujuan (di jaringan luar). Chain ini digunakan untuk SNAT (source NAT).
    - **OUTPUT** chain – NAT untuk paket yang berasal dari jaringan lokal firewall.

3. **Mangle Table**

    Mangle Table berfungsi untuk melakukan perubahan pada paket data. Perubahan yang dilakukan pada TCP header. Mangle Table memiliki *built-in chain*, yaitu :

    - **PREROUTING** chain
    - **OUTPUT** chain
    - **FORWARD** chain
    - **INPUT** chain
    - **POSTROUTING** chain

4. **Raw Table**

    Raw Table berfungsi untuk konfigurasi   pengecualian paket yang melewati firewall. Raw Table memiliki *built-in chain*, yaitu :

    - **PREROUTING** chain
    - **OUTPUT** chain

***3 Table utama pada IPTables dapat digambarkan sebagai berikut.***

![Filter-NAT-Mangle Table](/img/iptables-filter-nat-mangle-tables.png)

#### Rules

Hal-hal yang perlu diingat untuk memberlakukan *rules* pada IPTables, yaitu :

- **Rules** mempunyai kriteria dan target.
- **Jika** kriteria sudah **sesuai**, firewall akan mengeksekusi *rules* pada target atau suatu nilai (parameter) yang disebutkan pada target.
- **Jika** kriteria **tidak sesuai**, firewall akan mengeksekusi *rule* yang selanjutnya.

Tujuan paket yang dapat di definisikan pada target, yaitu :

1. **ACCEPT** – Firewall akan mengizinkan paket data.
2. **DROP** – Firewall akan menolak paket data.
3. **RETURN** – Firewall akan berhenti mengeksekusi rangkaian *rules* pada chain untuk paket data tersebut dan aturan pada *chain* tersebut dieksekusi ulang.

#### Syntax
Secara umum, untuk memodifikasi aturan yang berlaku pada IPTables dengan menjalankan

```bash
iptables [-t table] command chain rule-specification
```

| Commands      | Description   |
| ------------- |:------------------------------------------------ |
| -A, --append  | menambahkan rules pada chain
| -C, --check   | mengecek rule apa saja yang berlaku pada chain
| zebra stripes | are neat      |