# Modul 5 Jaringan Komputer 2018 

## **Firewall**

Firewall adalah suatu **mekanisme untuk melindungi keamanan jaringan komputer** dengan mengizinkan lalu lintas jaringan yang dianggap aman untuk melaluinya dan mencegah lalu lintas jaringan yang tidak aman. Pengaturan lalu lintas paket tersebut berupa menyaring paket data yang keluar dan masuk di jaringan. Paket data yang ’baik’ diperbolehkan untuk melewati jaringan dan paket dapa yang  dianggap ’jahat’ tidak diperbolehkan melewati jaringan. Firewall dapat berupa perangkat lunak atau perangkat keras yang ditanam perangkat lunak untuk memfilter paket data. Umumnya, sebuah firewall diimplementasikan dalam sebuah mesin terdedikasi, yang **berjalan pada pintu gerbang (gateway)** antara jaringan lokal dan jaringan lainnya. Firewall umumnya juga digunakan untuk **mengontrol akses terhadap siapa** saja yang memiliki akses terhadap jaringan pribadi dari pihak luar.

Cara-cara firewall dalam melindungi jaringan komputer internal, antara lain :
- Menolak dan memblokir paket data yang datang berdasarkan sumber dan tujuan yang tidak diinginkan.
- Menolak dan menyaring paket data yang berasal dari jaringan internal ke internet. Misal, ketika ada pengguna jaringan internet akan mengakses situs yang tidak baik.
- Menolak dan menyaring paket data berdasakan konten yang tidak diinginkan. Misal, firewall yang terintegrasi pada suatu antivirus akan menyaring dan mencegah file yang sudah terjangkit virus memasuki jaringan internal.
- Melaporkan semua aktivitas jaringan dan kegiatan firewall (log).

Firewall mempunyai beberapa tipe untuk melindungi jaringan, yaitu :
1. **Packet-Filtering Firewall**
adalah tipe firewall yang memeriksa dan membandingkan alamat sumber dari paket lewat dengan aturan atau kebijakan  yang telah terdaftar pada filtering firewall. Pada firewall tipe ini akan diatur apakah paket data tersebut akan diperbolehkan lewat atau menolaknya. Pada Linux, packet filtering firewall ditanamkan pada kernel (sebagai modul kernel, atau digabungkan ke dalam kernel) dan dapat diatur menggunakan **IPTables** yang merupakan paket aplikasi *built-in* pada Linux. Aturan atau kebijakan pemeriksaan paket data berdasarkan informasi yang dapat ditangkap dari packet header, antara lain :
    - IP address sumber dan tujuan.
    - Nomor port TCP/UDP sumber dan tujuan.
    - Tipe ICMP message.

2. **Application-Level Gateway (Proxy)**
sering juga disebut application level firewall atau proxy firewall. Firewall ini tidak memperbolehkan paket data yang datang untuk melewati firewall sacara langsung. Application level gateway menyediakan kontrol tingkat tinggi pada traffic antara dua jaringan yang isi layanan tertentu di dalamnya dapat dimonitor dan difilter sesuai dengan kebijakan keamanan jaringan. Firewall tipe ini akan mengatur semua yang berkaitan dengan layer aplikasi, seperti ftp, telner, dll.

3. **Circuit Level Gateway**
dapat dikatakan sebagai tipe khusus dari proxy karena dapat dikonfigurasi untuk melewatkan semua informasi pengguna yang sudah terautentikasi sebagai circuit level gateway. Circuit level gateway menghandle koneksi TCP dan tidak menyediakan paket tambahan seperti processing atau filtering. Firewall jenis ini akan menyembunyikan jaringan dari pengguna ketika koneksi akan terjadi dari pengguna. Pengguna akan berhadapan langsung dengan firewall pada saat proses pembuatan koneksi dan firewall akan membentuk koneksi dengan sumber daya di jaringan yang hendak di akses oleh pengguna setelah mengubah alamat IP dari paket yang ditransmisikan oleh dua belah pihak. Firewall jenis ini bekerja pada lapisan session layer.

Kali ini kita akan mempelajari bagaimana **Packet-Filtering Firewall** menggunakan **iptables**.
Struktur pada IPTables

    iptables -> Tables -> Chains -> Rules