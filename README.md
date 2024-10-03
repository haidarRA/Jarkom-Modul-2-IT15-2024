# Jarkom-Modul-2-IT15-2024

## ***KELOMPOK IT15***
| Nama      | NRP         |
|-----------|-------------|
| M. Abhinaya Al Faruqi | 5027231011  |  
| Haidar Rafi Aqyla | 5027231029   |

## TOPOLOGI
![3](https://github.com/user-attachments/assets/56dae7b3-9595-481b-9116-60a1fa37e7a1)

## IP Address Tiap Node
| Node           | IP Address         |
|----------------|--------------------|
| Nusantara      | 192.168.122.1/24   |
| Majapahit      | 10.71.1.2          |
| Mulawarman     | 10.71.1.3          |
| GrahamBell     | 10.71.1.4          |
| Samaratungga   | 10.71.1.5          |
| Solok          | 10.71.2.2          |
| Srikandi       | 10.71.2.3          |
| Kotalingga     | 10.71.2.4          |
| Bedahulu       | 10.71.2.5          |
| Tanjungkulai   | 10.71.2.6          |
| Sriwijaya      | 10.71.2.7          |

Semua node menggunakan netmask 255.255.255.0.

# No. 1
Soal:
> Untuk mempersiapkan peperangan World War MMXXIV (Iya sebanyak itu), Sriwijaya membuat dua kotanya menjadi web server yaitu Tanjungkulai, dan Bedahulu, serta Sriwijaya sendiri akan menjadi DNS Master. Kemudian karena merasa terdesak, Majapahit memberikan bantuan dan menjadikan kerajaannya (Majapahit) menjadi DNS Slave.

Pertama, dilakukan konfigurasi pada network tiap node.
## Router
### 1. Nusantara
```
auto eth0
iface eth0 inet dhcp

auto eth1 #switch1
iface eth1 inet static
    address 10.71.1.1
    netmask 255.255.255.0

auto eth2 #switch2
iface eth2 inet static
    address 10.71.2.1
    netmask 255.255.255.0
```

## DNS
### 1. Sriwijaya
```
auto eth0
iface eth0 inet static
    address 10.71.2.7
    netmask 255.255.255.0
    gateway 10.71.2.1
```

### 2. Majapahit
```
auto eth0
iface eth0 inet static
    address 10.71.1.2
    netmask 255.255.255.0
    gateway 10.71.1.1
```

## Client
### 1. Mulawarman
```
auto eth0
iface eth0 inet static
    address 10.71.1.3
    netmask 255.255.255.0
    gateway 10.71.1.1
```

### 2. GrahamBell
```
auto eth0
iface eth0 inet static
    address 10.71.1.4
    netmask 255.255.255.0
    gateway 10.71.1.1
```

### 3. Samaratungga
```
auto eth0
iface eth0 inet static
    address 10.71.1.5
    netmask 255.255.255.0
    gateway 10.71.1.1
```

### 4. Srikandi
```
auto eth0
iface eth0 inet static
    address 10.71.2.3
    netmask 255.255.255.0
    gateway 10.71.2.1
```

## Web Server
### 1. Kotalingga
```
auto eth0
iface eth0 inet static
    address 10.71.2.4
    netmask 255.255.255.0
    gateway 10.71.2.1
```

### 2. Bedahulu
```
auto eth0
iface eth0 inet static
    address 10.71.2.5
    netmask 255.255.255.0
    gateway 10.71.2.1
```

### 3. Tanjungkulai
```
auto eth0
iface eth0 inet static
    address 10.71.2.6
    netmask 255.255.255.0
    gateway 10.71.2.1
```

## Load Balancer
### 1. Solok
```
auto eth0
iface eth0 inet static
    address 10.71.2.2
    netmask 255.255.255.0
    gateway 10.71.2.1
```
Setelah selesai setup network configuration setiap node, jalankan command ini agar router dapat terhubung dengan internet.
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.71.0.0/16
```
Setelah menjalankan command di atas, cek apakah router bisa terhubung dengan internet dengan menggunakan command ```ping google.com```.
![image](https://github.com/user-attachments/assets/6cf6c02d-ff22-4262-8b0b-ad835b4e8731)
Di sini dapat terlihat bahwa router berhasil terhubung dengan internet.

Selanjutnya, agar DNS (dalam kasus ini, Sriwijaya) juga terhubung dengan internet, tambahkan nameserver IP router (192.168.122.1) ke /etc/resolv.conf pada node DNS.
```
nameserver 192.168.122.1
```

![image](https://github.com/user-attachments/assets/46d8f157-0a20-4790-a0b2-974166162d73)

Setelah menambahkan nameserver IP router ke DNS, cek apakah DNS bisa terhubung ke internet dengan menggunakan command ```ping google.com```.
![image](https://github.com/user-attachments/assets/6d890198-9796-4592-b529-1bd402f8558f)
Di sini dapat terlihat bahwa router berhasil terhubung dengan internet sehingga bind9 dapat di-install di node DNS Sriwijaya.

# No. 2
Soal:
> Karena para pasukan membutuhkan koordinasi untuk melancarkan serangannya, maka buatlah sebuah domain yang mengarah ke Solok dengan alamat sudarsana.xxxx.com dengan alias www.sudarsana.xxxx.com, dimana xxxx merupakan kode kelompok. Contoh: sudarsana.it01.com.

Sebelum membuat domain sudarsana.it15.com beserta dengan aliasnya, perlu menginstall bind9 terlebih dahulu pada DNS Sriwijaya.
```
apt-get update
```
```
apt-get install bind9 -y
```

Setelah menginstall bind9, masukkan konfigurasi berikut ke ```/etc/bind/named.conf.local```.
```
zone "sudarsana.it15.com" {
	type master;
	file "/etc/bind/jarkom/sudarsana.it15.com";
};
```
![image](https://github.com/user-attachments/assets/72095089-bf17-406c-aa0d-5636ec28867b)

Setelah itu, buatlah directory baru untuk domain bind9 dengan command ```mkdir /etc/bind/jarkom```.

Setelah membuat directory baru, copy template default dari bind9 dengan command ```cp /etc/bind/db.local /etc/bind/jarkom/sudarsana.it15.com```

Setelah copy template dari bind9, ubah konfigurasi pada ```/etc/bind/jarkom/sudarsana.it15.com``` hingga sesuai dengan konfigurasi di bawah.
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     sudarsana.it15.com. root.sudarsana.it15.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      sudarsana.it15.com.
@       IN      A       10.71.2.2
www     IN      CNAME   sudarsana.it15.com.
@       IN      AAAA    ::1
```
![image](https://github.com/user-attachments/assets/063caa94-743e-4f6c-8cc9-469d9c62a05d)

Setelah selesai mengatur konfigurasi, bind9 dapat di-restart pada DNS.
```
service bind9 restart
```

Domain sudarsono.it15.com beserta aliasnya sudah dapat dites. Namun, sebelum mengetes domain yang telah dibuat, masukkan ```nameserver 10.71.2.7``` (IP DNS Sriwijaya) agar client dapat mengakses domain yang telah dibuat.
![image](https://github.com/user-attachments/assets/56a9f914-3b3d-4d3f-b293-28244554eb8c)

Setelah menambahkan nameserver baru, domain dapat dites.
![image](https://github.com/user-attachments/assets/50a45ed8-9117-4210-84c2-384241f778c0)

Di sini, dapat terlihat bahwa konfigurasi domain ```sudarsana.it15.com``` berhasil.

# No.3
Soal:
> Para pasukan juga perlu mengetahui mana titik yang akan diserang, sehingga dibutuhkan domain lain yaitu pasopati.xxxx.com dengan alias www.pasopati.xxxx.com yang mengarah ke Kotalingga.

Karena pada DNS Sriwijaya sudah ada bind9, maka tidak perlu menginstall bind9 lagi dan langsung menambahkan konfigurasi pada ```/etc/bind/named.conf.local```.
```
zone "pasopati.it15.com" {
	type master;
	file "/etc/bind/jarkom/pasopati.it15.com";
};
```
![image](https://github.com/user-attachments/assets/1da41e06-46b7-45c1-bbd7-499f18773c46)

Di directory ```/etc/bind/jarkom``` yang telah dibuat sebelumnya, copy template default dari bind9 dengan command ```cp /etc/bind/db.local /etc/bind/jarkom/pasopati.it15.com```

Setelah copy template dari bind9, ubah konfigurasi pada ```/etc/bind/jarkom/pasopati.it15.com``` hingga sesuai dengan konfigurasi di bawah.
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     pasopati.it15.com. root.pasopati.it15.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      pasopati.it15.com.
@       IN      A       10.71.2.4
www     IN      CNAME   pasopati.it15.com.
@       IN      AAAA    ::1
```
![image](https://github.com/user-attachments/assets/4073c2ae-5a70-4cdd-b076-adb05e84e7ec)

Setelah selesai mengatur konfigurasi, bind9 dapat di-restart pada DNS.
```
service bind9 restart
```

Domain pasopati.it15.com beserta aliasnya sudah dapat dites. Namun, sebelum mengetes domain yang telah dibuat, masukkan ```nameserver 10.71.2.7``` (IP DNS Sriwijaya) agar client dapat mengakses domain yang telah dibuat. Karena client yang akan digunakan untuk testing adalah Srikandi, maka tidak perlu menambahkan nameserver DNS lagi dan domain bisa langsung dites.

![image](https://github.com/user-attachments/assets/3b16e3bb-331e-42ed-81fc-b69df4f74eb6)

Di sini, dapat terlihat bahwa konfigurasi domain ```pasopati.it15.com``` berhasil.

# No. 4
Soal:
> Markas pusat meminta dibuatnya domain khusus untuk menaruh informasi persenjataan dan suplai yang tersebar. Informasi dan suplai meme terbaru tersebut mengarah ke Tanjungkulai dan domain yang ingin digunakan adalah rujapala.xxxx.com dengan alias www.rujapala.xxxx.com.

Karena pada DNS Sriwijaya sudah ada bind9, maka tidak perlu menginstall bind9 lagi dan langsung menambahkan konfigurasi pada ```/etc/bind/named.conf.local```.
```
zone "rujapala.it15.com" {
	type master;
	file "/etc/bind/jarkom/rujapala.it15.com";
};
```
![image](https://github.com/user-attachments/assets/6cbaa1bb-24ea-4ac0-950d-3a291b9bb359)

Di directory ```/etc/bind/jarkom``` yang telah dibuat sebelumnya, copy template default dari bind9 dengan command ```cp /etc/bind/db.local /etc/bind/jarkom/rujapala.it15.com```

Setelah copy template dari bind9, ubah konfigurasi pada ```/etc/bind/jarkom/rujapala.it15.com``` hingga sesuai dengan konfigurasi di bawah.
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     rujapala.it15.com. root.rujapala.it15.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      rujapala.it15.com.
@       IN      A       10.71.2.6
www     IN      CNAME   rujapala.it15.com.
@       IN      AAAA    ::1
```
![image](https://github.com/user-attachments/assets/7abba151-581e-402e-8e31-fb3b81c19d25)

Setelah selesai mengatur konfigurasi, bind9 dapat di-restart pada DNS.
```
service bind9 restart
```

Domain rujapala.it15.com beserta aliasnya sudah dapat dites. Namun, sebelum mengetes domain yang telah dibuat, masukkan ```nameserver 10.71.2.7``` (IP DNS Sriwijaya) agar client dapat mengakses domain yang telah dibuat. Karena client yang akan digunakan untuk testing adalah Srikandi, maka tidak perlu menambahkan nameserver DNS lagi dan domain bisa langsung dites.

![image](https://github.com/user-attachments/assets/5d1f2a2d-cfbb-4231-b5d2-c8b1b798cb13)

Di sini, dapat terlihat bahwa konfigurasi domain ```rujapala.it15.com``` berhasil.

# No. 5
Soal:
> Pastikan domain-domain tersebut dapat diakses oleh seluruh komputer (client) yang berada di Nusantara.

Agar seluruh client dapat mengakses domain-domain yang telah dibuat tadi, gunakan command ```echo nameserver 10.71.2.7 > /etc/resolv.conf``` untuk menambahkan nameserver DNS Sriwijaya ke setiap client (Mulawarman, GrahamBell, Samaratungga, dan Srikandi).

![image](https://github.com/user-attachments/assets/777395d7-643f-4908-81f2-f3095af3a4d1)
![image](https://github.com/user-attachments/assets/2e18289c-6d50-4c8d-869e-c0202d918c49)
![image](https://github.com/user-attachments/assets/49c8eba5-861c-4340-9795-77bdc22d27eb)

# No. 6
Soal:
> Beberapa daerah memiliki keterbatasan yang menyebabkan hanya dapat mengakses domain secara langsung melalui alamat IP domain tersebut. Karena daerah tersebut tidak diketahui secara spesifik, pastikan semua komputer (client) dapat mengakses domain pasopati.xxxx.com melalui alamat IP Kotalingga (Notes: menggunakan pointer record).

Untuk mengakses domain ```pasopati.it15.com```` melalui IP Kotalingga (10.71.2.4), digunakan Reverse DNS pada DNS Sriwijaya.

Pertama, tambahkan konfigurasi berikut ke ```/etc/bind/named.conf.local```.
```
zone "2.71.10.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/2.71.10.in-addr.arpa";
};
```
![image](https://github.com/user-attachments/assets/1b0a1bd0-fc57-4fa9-9f32-06a855b24679)

Lalu, lakukan hal yang sama seperti saat membuat domain, yaitu copy template default dari bind9 dengan command ```cp /etc/bind/db.local /etc/bind/jarkom/2.71.10.in-addr.arpa``` di directory ```/etc/bind/jarkom```
yang telah dibuat sebelumnya.

Setelah copy template dari bind9, ubah konfigurasi pada ```/etc/bind/jarkom/2.71.10.in-addr.arpa``` hingga sesuai dengan konfigurasi di bawah.
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     pasopati.it15.com. root.pasopati.it15.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
2.71.10.in-addr.arpa	IN	NS	pasopati.it15.com.
4			IN	PTR	pasopati.it15.com.
```
![image](https://github.com/user-attachments/assets/8f2540ee-c655-4253-9ddc-395e2b0e94e3)

Setelah selesai mengatur konfigurasi, bind9 dapat di-restart pada DNS.
```
service bind9 restart
```

Sebelum mengecek konfigurasi DNS reverse, ada baiknya menginstall dnsutils terlebih dahulu agar bisa mengetes DNS reverse.
```
apt-get update
```
```
apt-get install dnsutils
```

Setelah selesai install dnsutils, dapat menggunakan command berikut untuk menguji DNS reverse.
```
host -t PTR 10.71.2.4
```

# No. 7
Soal:
> Akhir-akhir ini seringkali terjadi serangan brainrot ke DNS Server Utama, sebagai tindakan antisipasi kamu diperintahkan untuk membuat DNS Slave di Majapahit untuk semua domain yang sudah dibuat sebelumnya yang mengarah ke Sriwijaya.

- Setting pada sriwijaya di /etc/bind/named.conf.local
![Screenshot 2024-10-03 153254](https://github.com/user-attachments/assets/83cb7bd9-b3cc-48c9-8f0d-48cf4f24d0a1)

- lakukan restart bind9
```
service bind9 restart
```

- setting pada majapahit di /etc/bind/named.conf.local
![Screenshot 2024-10-03 153629](https://github.com/user-attachments/assets/18886a88-df9b-4ecb-8f17-d4bc434d1ae8)

- lakukan restart bind9
```
service bind9 restart
```

- testing
pada sriwijaya matikan bind9 "service bind9 stop", lalu pada client masukkan nameserver ke dua dns tersebut lalu ujilah dengan melakukan ping ke domain-domain yang sudah ada


# No. 8
Soal:
> Kamu juga diperintahkan untuk membuat subdomain khusus melacak kekuatan tersembunyi di Ohio dengan subdomain cakra.sudarsana.xxxx.com yang mengarah ke Bedahulu.

- Setting pada sriwijaya di /etc/bind/jarkom/sudarsana.it15.com
![Screenshot 2024-10-03 163313](https://github.com/user-attachments/assets/93192aae-98c5-4478-8de9-145d65578a75)

- lakukan restart bind9 di sriwijaya
```
service bind9 restart
```
- testing
coba lakukan ping di client dengan ping ke domain cakra.sudarsana.it15.com
```
ping cakra.sudarsana.it15.com
```

# No. 9
Soal:
> Karena terjadi serangan DDOS oleh shikanoko nokonoko koshitantan (NUN), sehingga sistem komunikasinya terhalang. Untuk melindungi warga, kita diperlukan untuk membuat sistem peringatan dari siren man oleh Frekuensi Freak dan memasukkannya ke subdomain panah.pasopati.xxxx.com dalam folder panah dan pastikan dapat diakses secara mudah dengan menambahkan alias www.panah.pasopati.xxxx.com dan mendelegasikan subdomain tersebut ke Majapahit dengan alamat IP menuju radar di Kotalingga.

- Setting pada sriwijaya di /etc/bind/jarkom/pasopati.it15.com
![Screenshot 2024-10-03 163855](https://github.com/user-attachments/assets/b0aef7e3-f3da-44b0-a8c5-f7392a706a4b)

- Setting pada sriwijaya di /etc/bind/named.conf.options
![Screenshot 2024-10-03 164201](https://github.com/user-attachments/assets/47a97a68-270a-4e35-9211-fe6c279b1dd2)

- setting pada sriwijaya di /etc/bind/named.conf.local
![Screenshot 2024-10-03 164320](https://github.com/user-attachments/assets/8101dc88-2273-4e90-888c-309e2d9d8c64)

- lakukan restart bind9 di sriwijaya
```
service bind9 restart
```

- lakukan setting majapahit di /etc/bind/named.conf.options
![Screenshot 2024-10-03 164558](https://github.com/user-attachments/assets/b6baef30-c866-424b-80e8-f9f79c678772)

- lakukan setting pada majapahit di /etc/bind/named.conf.local
![Screenshot 2024-10-03 164718](https://github.com/user-attachments/assets/4d73cb31-81e8-41e7-bfd7-d91ef421773e)

- Copy db.local ke direktori tersebut dan edit namanya menjadi panah.pasopati.it15.com
```
mkdir /etc/bind/panah
cp /etc/bind/db.local /etc/bind/panah/panah.pasopati.it15.com
```

- lakukan setting panah.pasopati.it15.com di majapahit sesuai dengan directory yang sudah didefinisikan (/etc/bind/panah/panah.pasopati.it15.com)
![Screenshot 2024-10-03 165059](https://github.com/user-attachments/assets/89bcab9d-42f6-4ea7-9105-8bf6643346bf)

- restart bind9 di majapahit
```
service bind9 restart
```

- testing
Lakukan ping ke domain panah.pasopati.it15.com dari client


# No. 10
Soal:
> Markas juga meminta catatan kapan saja meme brain rot akan dijatuhkan, maka buatlah subdomain baru di subdomain panah yaitu log.panah.pasopati.xxxx.com serta aliasnya www.log.panah.pasopati.xxxx.com yang juga mengarah ke Kotalingga.

- lakukan setting pada majapahit di /etc/bind/panah/panah.pasopati.it15.com
![Screenshot 2024-10-03 165848](https://github.com/user-attachments/assets/dc0e282e-d8c5-43ba-8eea-d2afb3ffb2fa)

- restart bind9
```
service bind9 restart
```

- lakukan testing ke semua klien dengan melakukan ping ke log.panah.pasopati.it15.com

# No. 11
Soal:
> Setelah pertempuran mereda, warga IT dapat kembali mengakses jaringan luar dan menikmati meme brainrot terbaru, tetapi hanya warga Majapahit saja yang dapat mengakses jaringan luar secara langsung. Buatlah konfigurasi agar warga IT yang berada diluar Majapahit dapat mengakses jaringan luar melalui DNS Server Majapahit.

- lakukan editing di file /etc/bind/named.conf.options di dns sriwijaya
![Screenshot 2024-10-03 170213](https://github.com/user-attachments/assets/cb6cdf5b-2a9a-4296-bfcf-e1fb15c113e5)

- restart bind9
```
service bind9 restart
```









