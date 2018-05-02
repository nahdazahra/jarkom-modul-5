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
    - **INPUT** chain – Untuk memfilter paket yang menuju jaringan lokal. Contoh syntax:
      ```
      # iptables --append INPUT --source 10.151.36.0/24 --jump DROP
      # iptables -A INPUT -s 10.151.36.0/24 -j DROP
      
      Penjelasan:
      - DROP semua paket masuk (INPUT) dari subnet 10.151.36.0/24
      ```
      
    - **OUTPUT** chain – Untuk memfilter paket yang dari jaringan lokal ke jaringan luar. Contoh syntax:
      ```
      # iptables --append OUTPUT --destination 10.151.36.5 --jump DROP
      # iptables -A OUTPUT -d 10.151.36.5 -j DROP
      
      Penjelasan:
      - DROP semua paket keluar (OUTPUT) menuju 10.151.36.5
      ```
    - **FORWARD** chain – Untuk memfilter paket yang hanya akan diteruskan (melewati) firewall. Contoh syntax:
      ```
      # iptables --append FORWARD --source 10.151.36.0/24 --jump ACCEPT
      # iptables -A FORWARD -s 10.151.36.0/24 -j ACCEPT
      
      Penjelasan:
      - ACCEPT semua paket keluar yang melewati firewall yang berasal dari 10.151.36.0/24
      ```

2. **NAT Table**

    NAT Table berfungsi untuk mentranslasikan jaringan lokal yang melewati firewall menuju jaringan luar. NAT Table memiliki *built-in chain*, yaitu :
    - **PREROUTING** chain – pada chain **PREROUTING**, dijalankan DNAT (Destination NAT) yaitu ketika anda mengubah alamat tujuan dari paket pertama dengan kata lain anda merubah ke mana komunikasi terjadi. Destination NAT selalu dilakukan sebelum routing, ketika paket masuk dari jaringan. Port forwarding, load sharing dan transparent proxy semuanya adalah bentuk dari DNAT. Destination NAT dilakukan pada chain PREROUTING, ketika paket masuk, hal ini berarti semua tools di dalam router akan melihat paket akan pergi ke tujuan yang sebenarnya. Hal ini juga berarti bahwa opsi `-i` (incoming interface) bisa digunakan. Destination NAT dispesifikasikan dengan menggunakan `-j DNAT` dan opsi `--to-destination` menspesifikasikan sebuah alamat IP, range alamat IP dan range dari port (hanya untuk protokol UDP dan TCP) yang sifatnya optional.
    
      Sebagai contoh, kita memiliki jaringan LAN internal yang ingin kita amankan. Pada jaringan tersebut terdapat DMZ sebagai HTTP server yang servernya memiliki IP 10.151.73.98. Kita ingin semua HTTP request yang berasal dari jaringan luar (interface eth0) diarahkan ke DMZ tersebut.
      ```bash
      # iptables --table nat --append PREROUTING --in-interface eth0 --protocol tcp --dport 80 \
          --jump DNAT --to-destination 10.151.73.98:80

      # iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 80 -j DNAT --to-destination 10.151.73.98:80
      ```
      
    - **POSTROUTING** chain – pada chain **POSTROUTING** dijalankan SNAT (Source NAT), yaitu ketika anda mengubah alamat asal dari paket pertama dengan kata lain anda mengubah dari mana koneksi terjadi. Source NAT selalu dilakukan setelah routing, sebelum paket keluar ke jaringan. Masquerading adalah contoh dari `SNAT`. Untuk melakukan Source NAT anda harus merubah asal dari koneksi. Hal ini dilakukan di chain POSTROUTING, saat sebelum keluar. Hal ini sangat penting, dikarenakan berarti tools lain yang di dalam router itu (routing, packet filtering) akan melihat paket itu tidak berubah. Hal ini juga berarti opsi `-o` (outgoing interface) juga bisa digunakan. Source dispesifikasikan dengan menggunakan `-j SNAT`, dan juga opsi `--to-source` untuk menspesifikasikan sebuah alamat IP, range alamat IP dan port atau range port (hanya untuk protokol UDP dan TCP) yang sifatnya optional.
    
       Sebagai contoh, pada modul pengenalan UML kita telah menjalankan IP masquerading dengan syntax berikut:
       ```
       # iptables --table nat --append POSTROUTING --out-interface eth0 --jump MASQUERADE
       # iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
       ```
       Rule diatas berarti, source address dari setiap paket yang keluar (`-o`) melalui `eth0` akan diubah menjadi IP dari `eth0` (`MASQUERADE`).
       

3. **Mangle Table**

    Mangle Table berfungsi untuk melakukan perubahan pada paket data. Perubahan yang dilakukan pada TCP header untuk memodifikasi QOS (*Quality of Service*) pada paket tersebut. Mangle Table memiliki *built-in chain*, yaitu :

    - **PREROUTING** chain
    - **OUTPUT** chain
    - **FORWARD** chain
    - **INPUT** chain
    - **POSTROUTING** chain

3 Table utama pada IPTables dapat digambarkan sebagai berikut.

![Filter-NAT-Mangle Table](/img/iptables-filter-nat-mangle-tables.png)

### **5.2. Rules**

Hal-hal yang perlu diingat untuk memberlakukan *rules* pada IPTables, yaitu :

- **Rules** mempunyai kriteria dan target.
- **Jika** kriteria sudah **sesuai**, firewall akan mengeksekusi *rule* pada target atau suatu nilai (parameter) yang disebutkan pada target.
- **Jika** kriteria **tidak sesuai**, firewall akan mengeksekusi *rule* yang selanjutnya.

Tujuan paket yang dapat di definisikan pada target, beberapa target yang sering digunakan adalah :

1. **ACCEPT** – Firewall akan mengizinkan paket data.
2. **DROP** – Firewall akan menolak paket data.
3. **RETURN** – Firewall akan berhenti mengeksekusi rangkaian *rules* pada chain untuk paket data tersebut dan aturan pada *chain* tersebut dieksekusi ulang.
4. **MASQUERADE** – Target yang hanya berlaku pada **NAT Table**. Digunakan untuk mendefinisikan paket diarahkan ke subnet mana. Biasanya hanya digunakan untuk IP dinamis, jika menggunakan IP statis, maka gunakan **SNAT** target.
5. **REJECT** – Firewall akan menolak paket dan mengirimkan error message.
6. **REDIRECT** – Target yang hanya berlaku pada **NAT Table**. Firewall akan mengarahkan paket ke device (mesin) yang digunakannya menggunakan *Primary IP address* (alamat localhost) interface-nya.

Untuk macam-macam target lebih lengkap, dapat dilihat pada dokumentasi extension dari iptables
```bash
man iptables-extension
```

#### Syntax

Secara umum, untuk memodifikasi aturan yang berlaku pada IPTables dengan menjalankan

```bash
iptables [-t table] command chain rule-specification
```

Beberapa command yang sering digunakan pada iptables :

| Command and Syntax                                        | Description                                                                             | Example                                         |
| --------------------------------------------------------- |:--------------------------------------------------------------------------------------- |:----------------------------------------------- |
| `-A, --append chain rule-specification`                   | menambahkan rules pada chain  | `iptables -A INPUT -s 10.151.36.0/24 -j DROP` 
| `-C, --check chain rule-specification`                    | mengecek rule apa saja yang berlaku pada chain    | `iptables -C INPUT -s 10.151.36.0/24 -j DROP`
| `-D, --delete chain {rule-specification \| rulenum}`      | menghapus rules pada chain    | `iptables -D INPUT -s 10.151.36.0/24 -j DROP`
| `-I, --insert chain [rulenum] rule-specification`         | menyisipkan rules pada urutan tertentu    | `iptables -I OUTPUT 2 -s 10.151.36.0/24 -j DROP`
| `-R, --replace chain rulenum rule-specification`          | mengganti rules pada chain tertentu   | `iptables -R OUTPUT 2 -s 10.151.36.0/24 -j DROP`
| `-L, --list [chain]`                                      | melihat daftar rules yang berlaku berdasarkan chain   | `iptables -L INPUT`
| `-S, --list-rules [chain]`                                | melihat semua rules yang berlaku pada firewall   | `iptables -S INPUT`, `iptables -n -L -v --line-numbers`
| `-F, --flush [chain]`                                     | menghilangkan semua rules pada chain tertentu (semua chain jika chain tidak disebutkan) | `iptables -F INPUT`

**Penjelasan :**

- `rule-specification` = `[matches...] [target]`
- `match` = `-m matchname [per-match-options]`
- `target` = `-j targetname [per-target-options]`
- `[]` = syntax tersebut bersifat opsional

Beberapa parameter yang perlu diketahui :

| Parameter                         | Descripton                        |
| --------------------------------- | :-------------------------------- |
| `-m, --match match`                 | mendefinisikan kesesuaian rule untuk tujuannya ke mana
| `-j, --jump target`                 | mendefinisikan rule akan menggunakan taeget yang mana
| `[!] -i, --in-interface name`       | mendefinisikan opsi interface yang dilihat masuk paketnya
| `[!] -o, --out-interface name`      | mendefinisikan opsi interface yang dilihat keluar paketnya
| `--line-numbers`                    | untuk melihat nomor urutan rules pada table
| `--to`                              | untuk mendefinisikan alamat tujuan paket
| `--from`                            | untuk mendefinisikan alamat asal paket

Untuk opsi command maupun parameter lebih lengkap dapat dilihat pada dokumentasi `iptables`, 

```bash
man iptables-extension
```

### **Praktik**

#### List default policy untuk setiap chain
```bash
iptables -L | grep policy
```
#### 
#### Change default policy for a Chain
```bash
iptables --policy {ACCEPT | DROP | REJECT}
```

Example :

```bash
iptables --policy FORWARD ACCEPT
```

#### ACCEPT koneksi dari sebuah alamat IP

```bash
iptables -A INPUT -s 10.10.10.10 -j ACCEPT

# Penjelasan : 
# ACCEPTS semua koneksi yang berasal dari IP 10.10.10.10
# -A <CHAIN>  : menambahakan rule yang dispesifikasikan
# -s <SOURCE> : SOURCE - alamat asal dari sebuah koneksi 
# -j <ACTION> : (jump) - mendefinisikan apa yang harus dilakukan ketika paket sesuai dengan rule tersebut.
```

#### DROP koneksi dari sebuah subnet

```bash
iptables -A INPUT -s 10.10.10.0/24 -j DROP

# Penjelasan :
# BLOK semua koneksi yang berasal dari subnet 10.10.10.0/24 
# -A <CHAIN>  : menambahakan rule yang dispesifikasikan
# -s <SOURCE> : SOURCE - alamat asal dari sebuah koneksi 
# -j <ACTION> : (jump) - mendefinisikan apa yang harus dilakukan ketika paket sesuai dengan rule tersebut.
```

#### REJECT OUTBOUND Connections for an IP on a Specific Port (SSH)

```bash
iptables -A OUTPUT -p 22 --dport ssh -s 10.10.10.10 -j REJECT

# Penjelasan :
# REJECT semua koneksi dari alamat IP 10.10.10.10 pada port 22 (TCP).
# -A <CHAIN>  : menambahakan rule yang dispesifikasikan
# -s <SOURCE> : SOURCE - alamat asal dari sebuah koneksi 
# -j <ACTION> : (jump) - mendefinisikan apa yang harus dilakukan ketika paket sesuai dengan rule tersebut.
```

#### DROP All OUTGOING Connections; ALLOW only CONNECTIONS to 192.168.1.1

```bash
iptables --policy OUTPUT DROP
# Penjelasan :
# DROP semua koneksi yang keluar

iptables -A OUTPUT -d 192.168.1.1 -j ACCEPT
# Penjelasan :
# ALLOW koneksi yang menuju alamat IP 192.168.1.1
```

#### Menyimpan Rules IPTables

```bash
sudo /sbin/iptables-save
```

#### Menghapus semua Rules pada IPTables

```bash
iptables -F
```

### References

https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/4/html/Security_Guide/s1-firewall-ipt-fwd.html

## SOAL LATIHAN

1. Komputer di subnet MENANGGAL tidak diizinkan mengakses server BENOWO
2. Komputer di subnet KERTAJAYA tidak dapat mengakses komputer di subnet MENANGGAL pada pukul 10.00 - 19.00
3. Block port 80 agar BENOWO tidak bisa mengakses HTTP
