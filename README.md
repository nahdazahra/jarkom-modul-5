# Modul 5 Jaringan Komputer 2018

## **Table of Content**
1. Definisi
2. Mengapa perlu firewall?
3. Cara Kerja Firewall
4. Jenis-jenis Firewall
5. IPTables

## **1. Definisi**
Berdasarkan [RFC 2828](http://www.faqs.org/rfcs/rfc2828.html)

```
(I) An internetwork gateway that restricts data communication
    traffic to and from one of the connected networks (the one said to
    be "inside" the firewall) and thus protects that network's system
    resources against threats from the other network (the one that is
    said to be "outside" the firewall). (See: guard, security
    gateway.)
```

## **2. Mengapa perlu firewall?**
Firewall diperlukan karena keamanan, diantara pertimbangan adanya firewall adalah:
- Pencurian data pada jaringan internal
- Pengaksesan data oleh orang yang tidak berhak
- Denial of Service


## **3. Firewall**

![Ilustrasi](img/illustration.jpg)
Gambar 1. Ilustrasi Firewall (sumber: https://opensourceforu.com/2016/07/implementing-a-software-defined-network-sdn-based-firewall/)

Firewall adalah suatu **mekanisme untuk melindungi keamanan jaringan komputer** dengan mengizinkan lalu lintas jaringan yang dianggap aman untuk melaluinya dan mencegah lalu lintas jaringan yang tidak aman. Pengaturan lalu lintas paket tersebut berupa menyaring paket data yang keluar dan masuk di jaringan. Paket data yang ’baik’ diperbolehkan untuk melewati jaringan dan paket dapa yang  dianggap ’jahat’ tidak diperbolehkan melewati jaringan. Firewall dapat berupa perangkat lunak atau perangkat keras yang ditanam perangkat lunak untuk memfilter paket data. Umumnya, sebuah firewall diimplementasikan dalam sebuah mesin terdedikasi, yang **berjalan pada pintu gerbang (gateway)** antara jaringan lokal dan jaringan lainnya. Firewall umumnya juga digunakan untuk **mengontrol akses terhadap siapa** saja yang memiliki akses terhadap jaringan pribadi dari pihak luar.

Cara-cara firewall dalam melindungi jaringan komputer internal, antara lain :
- Menolak dan memblokir paket data yang datang berdasarkan sumber dan tujuan yang tidak diinginkan.
- Menolak dan menyaring paket data yang berasal dari jaringan internal ke internet. Misal, ketika ada pengguna jaringan internet akan mengakses situs yang tidak baik.
- Menolak dan menyaring paket data berdasakan konten yang tidak diinginkan. Misal, firewall yang terintegrasi pada suatu antivirus akan menyaring dan mencegah file yang sudah terjangkit virus memasuki jaringan internal.
- Melaporkan semua aktivitas jaringan dan kegiatan firewall (log).

## **4. Jenis-jenis Firewall**

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
Firewall dengan metode stateful inspection ini bekerja di antara lapisan data link dan network referensi model OSI. Jika suatu paket data diterima, langkah pertama yang dilakukan oleh stateful inspection ini adalah memeriksa informasi header paket data dengan tabel state untuk melihat apakah sudah ada jalur yang tersedia untuk paket tersebut. Jika jalur sudah tersedia, maka stateful inspection membuat asumsi bahwa paket boleh diterima dan diteruskan ke tujuannya. Jika jalur belum tersedia, maka stateful inspection mencocokkan paket data dengan peraturan keamanan (security policy) yang telah dibuat untuk menentukan apakah paket mendapat izin untuk diteruskan. Stateful inspection terus-menerus mengawasi setiap koneksi yang terjadi dan membuat catatan pada tabel status yang dimilikinya.

Kali ini kita akan mempelajari bagaimana **Packet-Filtering Firewall** menggunakan `$ iptables`. Sekarang kita perhatikan dokumentasinya pada `$ man iptables`.

## **5. IPTables**

![Struktur IPTables pada Komputer](/img/netfilter-iptables-diagram-a.jpg)

Iptables adalah suatu tools dalam sistem operasi linux yang berfungsi sebagai alat untuk melakukan penyaringan (filter) terhadap lalu lintas (traffic) data. Secara sederhana, iptables digambarkan sebagai pengatur lalu lintas data. Aturan-aturan lalu lintas pada iptables, berada dalam sebuah tabel, dimana ***table*** adalah sekelompok ***chain*** dan ***chain*** adalah sekelompok ***rules***. Pada *high-level* iptables, memungkinkan terdapat *multiple tables* dengan *multiple chains*. Secara *default*, iptables berjalan tanpa *rules* apapun.

Struktur kerja IPTables,

    iptables -> Tables -> Chains -> Rules

dapat digambarkan dengan struktur seperti ini.

![IPTables Structure](/img/iptables-table-chain-rule-structure.png)

### **5.1. Tables and Chains**

IPTables mempunyai 4 *built-in tables*.

1. **Filter Table**

    Table ini adalah tabel default pada iptables. Jadi, jika kita tidak mendefinisikan table yang kita gunakan pada iptables, maka secara default menggunakan Filter table. Filter Table memiliki *built-in chain*, yaitu :
    - **INPUT** chain – Untuk memfilter paket yang menuju jaringan lokal.
    - **OUTPUT** chain – Untuk memfilter paket yang dari jaringan lokal ke jaringan luar.
    - **FORWARD** chain – Untuk memfilter paket yang hanya akan diteruskan (melewati) firewall.

2. **NAT Table**

    NAT Table berfungsi untuk mentranslasikan jaringan lokal yang melewati firewall menuju jaringan luar. NAT Table memiliki *built-in chain*, yaitu :
    - **PREROUTING** chain – pada chain **PREROUTING**, dijalankan DNAT (Destination NAT) yaitu ketika anda mengubah alamat tujuan dari paket pertama dengan kata lain anda merubah ke mana komunikasi terjadi. Destination NAT selalu dilakukan sebelum routing, ketika paket masuk dari jaringan. Port forwarding, load sharing dan transparent proxy semuanya adalah bentuk dari DNAT. Destination NAT dilakukan pada chain PREROUTING, pas ketika paket masuk, hal ini berarti semua tools di dalam router akan melihat paket akn pergi ke tujuan yang sebenarnya . Hal ini juga berarti bahwa opsi '-i' (incoming interface) bisa digunakan. Destination NAT dispesifikasikan dengan menggunakan '-j DNAT' dan opsi '--to-destination' menspesifikasikan sebuah alamat IP, range alamat IP dan range dari port (hanya untuk protokol UDP dan TCP) yang sifatnya optional.
    - **POSTROUTING** chain – pada chain **POSTROUTING** dijalankan SNAT (Source NAT), yaitu ketika anda mengubah alamat asal dari paket pertama dengan kata lain anda mengubah dari mana koneksi terjadi. Source NAT selalu dilakukan setelah routing, sebelum paket keluar ke jaringan. Masquerading adalah contoh dari SNAT. Untuk melakukan Source NAT anda harus merubah asal dari koneksi. Hal ini dilakukan di chain POSTROUTING, saat sebelum keluar. Hal ini sangat penting, dikarenakan berarti tools lain yang di dalam router itu (routing, packet filtering) akan melihat paket itu tidak berubah. Hal ini juga berarti opsi '-o' (outgoing interface) juga bisa digunakan. Source dispesifikasikan dengan menggunakan '-j SNAT', dan juga opsi '--to-source' untuk menspesifikasikan sebuah alamat IP, range alamat IP dan port atau range port (hanya untuk protokol UDP dan TCP) yang sifatnya optional.

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

3 Table utama pada IPTables dapat digambarkan sebagai berikut.
![Filter-NAT-Mangle Table](/img/iptables-filter-nat-mangle-tables.png)

### **5.2. Rules**

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

Beberapa command yang sering digunakan pada iptables :
| Commands, Syntax                                        | Description                                      |
| ------------------------------------------------------- |:------------------------------------------------ |
| -A or --append chain rule-specification                 | menambahkan rules pada chain
| -C or --check chain rule-specification                  | mengecek rule apa saja yang berlaku pada chain
| -D or --delete chain {rule-specification \| rulenum}    | menghapus rules pada chain
| -I or --insert chain [rulenum] rule-specification       | menyisipkan rules pada urutan tertentu
| -R or --replace chain rulenum rule-specification        | mengganti rules pada chain tertentu
| -L or --list [chain]                                    | melihat daftar rules yang berlaku berdasarkan chain
| -S or --list-rules [chain]                              | melihat semua rules yang berlaku pada chain
| -F or --flush [chain]                                   | menghilangkan semua rules pada chain tertentu (semua chain jika chain tidak disebutkan)

Beberapa parameter yang perlu diketahui :
| Parameter                         | Descripton                        |
| --------------------------------- | :-------------------------------- |
| -m, --match match                 | 
| -j, --jump target                 | 
| -g, --goto chain                  | 
| [!] -i, --in-interface name       | 
| [!] -o, --out-interface name      | 
| -c, --set-counters packets bytes  | 
| --line-numbers                    | 
| --to                              | 
| --from                            | 

Untuk opsi command maupun parameter lebih lengkap dapat dilihat pada dokumentasi `iptables`, dengan menjalankan

```bash
man iptables
```

## SOAL LATIHAN

1. Komputer di subnet MENANGGAL tidak diizinkan mengakses server BENOWO
2. Komputer di subnet KERTAJAYA tidak dapat mengakses komputer di subnet MENANGGAL pada pukul 10.00 - 19.00
3. Block port 80 agar BENOWO tidak bisa mengakses HTTP