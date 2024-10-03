# Jarkom-Modul-2-IT15-2024

## ***KELOMPOK IT15***
| Nama      | NRP         |
|-----------|-------------|
| M. Abhinaya Al Faruqi | 5027231011  |  
| Haidar Rafi Aqyla | 5027231029   |

## TOPOLOGI
![3](https://github.com/user-attachments/assets/56dae7b3-9595-481b-9116-60a1fa37e7a1)
![image](https://github.com/user-attachments/assets/9dff7c83-10ea-43d3-96ff-30dcc4a98303)

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
![image](https://github.com/user-attachments/assets/9efd6d81-e2ed-4780-acc8-08b52234c6bd)

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
![image](https://github.com/user-attachments/assets/1f9fcbe5-3736-4685-ae44-7bebe13541c7)
![image](https://github.com/user-attachments/assets/c57a60e1-8227-4dea-a830-902f0debea49)
![image](https://github.com/user-attachments/assets/84201858-2afe-46c3-8c4f-ba9ca2ee2565)


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
![image](https://github.com/user-attachments/assets/940aeb1f-8fa4-4e25-be7b-e69ec069c956)

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
![image](https://github.com/user-attachments/assets/6bbb8ac0-a13c-4e8b-9640-91765a506b61)

# No. 10
Soal:
> Markas juga meminta catatan kapan saja meme brain rot akan dijatuhkan, maka buatlah subdomain baru di subdomain panah yaitu log.panah.pasopati.xxxx.com serta aliasnya www.log.panah.pasopati.xxxx.com yang juga mengarah ke Kotalingga.

- lakukan setting pada majapahit di /etc/bind/panah/panah.pasopati.it15.com
![Screenshot 2024-10-03 165848](https://github.com/user-attachments/assets/dc0e282e-d8c5-43ba-8eea-d2afb3ffb2fa)

- restart bind9
```
service bind9 restart
```

- lakukan testing dari client dengan melakukan ping ke log.panah.pasopati.it15.com
![image](https://github.com/user-attachments/assets/b2832d94-13fb-4d3d-b051-01c5af9bce9a)

# No. 11
Soal:
> Setelah pertempuran mereda, warga IT dapat kembali mengakses jaringan luar dan menikmati meme brainrot terbaru, tetapi hanya warga Majapahit saja yang dapat mengakses jaringan luar secara langsung. Buatlah konfigurasi agar warga IT yang berada diluar Majapahit dapat mengakses jaringan luar melalui DNS Server Majapahit.

- lakukan editing di file /etc/bind/named.conf.options di dns Majapahit
![image](https://github.com/user-attachments/assets/49aa3d58-0b65-4ff7-b47e-276d63a376de)

- restart bind9
```
service bind9 restart
```
setelah melakukan setting di atas DNS Server Majapahit dapat melakukan akses ke jaringan luar, menggunakan Google Public DNS.
![image](https://github.com/user-attachments/assets/eea7ce97-be8d-4248-a501-126de2b5ef05)

# No. 12
Soal:
> Karena pusat ingin sebuah laman web yang ingin digunakan untuk memantau kondisi kota lainnya maka deploy laman web ini (cek resource yg lb) pada Kotalingga menggunakan apache.

Pertama, download semua kebutuhan yang diperlukan untuk mendeploy apache2 pada web server Kotalingga.
```
apt-get install unzip wget apache2 libapache2-mod-php7.0 -y
```

Setelah selesai menginstall kebutuhan, edit file ```000-default.conf``` dengan command ```nano /etc/apache2/sites-available/000-default.conf``` dengan konfigurasi berikut.
```
<VirtualHost *:80>
	ServerName pasopati.it15.com

	ServerAdmin webmaster@localhost
	DocumentRoot /var/www/html
</VirtualHost>
```
![image](https://github.com/user-attachments/assets/c05a8b87-cbb7-48cd-85ed-d4869a4eef77)

Setelah konfigurasi file ```000-default.conf```, download file lb.zip dari resource jarkom dengan command berikut.
```
wget --no-check-certificate 'https://docs.google.com/uc?export=download&id=1Sqf0TIiybYyUp5nyab4twy9svkgq8bi7' -O lb.zip
```

Kemudian unzip file lb.zip dan pindahkan index.php dari hasil unzip ke directory ```/var/www/html```.
```
unzip lb.zip -d lb
cp lb/worker/index.php /var/www/html/index.php
```

Kemudian restart apache2.
```
service apache2 restart
```

Setelah restart apache2 pada web server Kotalingga, web apache2 yang telah di-deploy dapat dites melalui salah satu client yang ada.
![image](https://github.com/user-attachments/assets/f014bc39-4b61-4c2b-84b4-5efba5085a38)
![image](https://github.com/user-attachments/assets/4a741671-d8c6-4059-bf3e-55316e6505a7)

# No. 13
Soal:
> Karena Sriwijaya dan Majapahit memenangkan pertempuran ini dan memiliki banyak uang dari hasil penjarahan (sebanyak 35 juta, belum dipotong pajak) maka pusat meminta kita memasang load balancer untuk membagikan uangnya pada web nya, dengan Kotalingga, Bedahulu, Tanjungkulai sebagai worker dan Solok sebagai Load Balancer menggunakan apache sebagai web server nya dan load balancer nya.

Di load balancer Solok, install kebutuhan load balancer dan enable modul - modul yang dibutuhkan pada config apache2.
```
apt-get update
apt-get install apache2 libapache2-mod-php7.0 -y

a2enmod proxy
a2enmod proxy_http
a2enmod proxy_balancer
a2enmod lbmethod_byrequests
```

Setelah itu, ubah konfigurasi pada ```000-default.conf``` menjadi konfigurasi berikut.
```
<VirtualHost *:80>
        <Proxy balancer://mycluster>
                BalancerMember http://10.71.2.4
                BalancerMember http://10.71.2.5
		BalancerMember http://10.71.2.6
		ProxySet lbmethod=byrequests
        </Proxy>

	ProxyPass / balancer://mycluster/
	ProxyPassReverse / balancer://mycluster/
        ProxyPreserveHost On

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
```
![image](https://github.com/user-attachments/assets/18571248-2a69-44e0-a0c4-5af5a8bf08fb)

Untuk web server Bedahulu dan Tanjungkulai, lakukan hal yang sama seperti web server Kotalingga di Soal no. 12.

Untuk mengetes load balancer Solok, dapat mengakses IP dari Solok menggunakan command ```lynx http://10.71.2.2/index.php```
![image](https://github.com/user-attachments/assets/91ad1461-5da6-4245-a706-b879c7ba6833)
![image](https://github.com/user-attachments/assets/2111829c-99e8-4d84-bd59-3f964e3bee78)
![image](https://github.com/user-attachments/assets/3f7a13d7-9445-462b-8d0e-a6bc2bda707b)

Setiap kali IP Solok diakses menggunakan lynx, isi dari index.php akan bergantian.

# No. 14
Soal:
>Selama melakukan penjarahan mereka melihat bagaimana web server luar negeri, hal ini membuat mereka iri, dengki, sirik dan ingin flexing sehingga meminta agar web server dan load balancer nya diubah menjadi nginx.

**Load Balancer (Solok)**
Pertama, hentikan apache2 dan install kebutuhan nginx.
```
service apache2 stop
apt-get update
apt-get install nginx -y
```

Kemudian, buat konfigurasi berikut pada ```/etc/nginx/sites-available/jarkom``` dengan command ```nano /etc/nginx/sites-available/jarkom```.
```
upstream node {
    server 10.71.2.4;
    server 10.71.2.5;
    server 10.71.2.6;
}

server {
    listen 80;
    server_name _;
    location / {
        proxy_pass http://node;
    }
}
```
![image](https://github.com/user-attachments/assets/f00afe3a-586b-4be5-b933-bce7ff6cf34e)

Kemudian, buat simlink dan hapus template default nginx.
```
ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled/jarkom

rm /etc/nginx/sites-enabled/default
```

Setelah itu, restart nginx dengan command ```service nginx restart```.

**Worker (Kotalingga, Bedahulu, Tanjungkulai**
Sama seperti load balancer, hentikan apache2 dan install kebutuhan nginx.
```
apt-get update
apt-get install nginx php-fpm -y
```

Setelah itu, buat directory jarkom di dalam ```/var/www/``` dan copy file php.
```
mkdir /var/www/jarkom
cp /var/www/html/index.php /var/www/jarkom/index.php
```

Kemudian, buat konfigurasi berikut pada ```/etc/nginx/sites-available/jarkom``` dengan command ```nano /etc/nginx/sites-available/jarkom```.
```
server {

	listen 80;

	root /var/www/jarkom;

	index index.php index.html index.htm;
	server_name _;

	location / {
 		try_files $uri $uri/ /index.php?$query_string;
	}

	# pass PHP scripts to FastCGI server
	location ~ \.php$ {
		include snippets/fastcgi-php.conf;
		fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
	}

	location ~ /\.ht {
		deny all;
	}
}
```
![image](https://github.com/user-attachments/assets/855e9307-63fd-46fb-a215-7c84210cf2d2)


Kemudian, buat simlink dan hapus template default nginx.
```
ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled/jarkom

rm /etc/nginx/sites-enabled/default
```

Setelah itu, restart nginx dengan command ```service nginx restart``` dan start service php-fpm dengan command ```service php7.0-fpm start```.

Untuk mengetes load balancer Solok, sama seperti no. 14, dapat mengakses IP dari Solok menggunakan command ```lynx http://10.71.2.2/index.php```
![image](https://github.com/user-attachments/assets/15ba9249-d6cd-402b-ad0d-f6680feafc80)
![image](https://github.com/user-attachments/assets/d59c7c61-d1e5-4df0-8b0e-74dee7901b1a)
![image](https://github.com/user-attachments/assets/a63e7724-f994-4450-bacb-5fbc3f5a110d)

# No. 15
Soal:
>Markas pusat meminta laporan hasil benchmark dengan menggunakan apache benchmark dari load balancer dengan 2 web server yang berbeda tersebut dan meminta secara detail dengan ketentuan:
> - Nama Algoritma Load Balancer
> - Report hasil testing apache benchmark 
> - Grafik request per second untuk masing masing algoritma. 
> - Analisis
> - Meme terbaik kalian (terserah ( ͡° ͜ʖ ͡°)) 🤓

Sebelum membuat laporan hasil benchmark, install apache2-utils terlebih dahulu.
```
sudo apt install apache2-utils
```

Setelah itu, dapat membuat laporan hasil benchmark dengan apache benchmark menggunakan command ```ab -n 1000 -c 100 http://10.71.2.2``` untuk masing - masing algoritma load balancer.

## Round Robin

**1. General**

![image](https://github.com/user-attachments/assets/3a753c3f-b747-4897-b86d-29cab52140f6)
![image](https://github.com/user-attachments/assets/e4c539e1-6936-4740-a596-328419af33bd)

**2. Kotalingga**

![image](https://github.com/user-attachments/assets/84eff3ec-d595-4a74-a598-7065fc27813c)
![image](https://github.com/user-attachments/assets/e27c4a76-ed09-4384-a541-4a2aff232931)

**3. Bedahulu**

![image](https://github.com/user-attachments/assets/bb15d9c4-f908-4cfe-8943-4d0524ece808)
![image](https://github.com/user-attachments/assets/58eeebac-2676-492b-94d9-06b4d11a1fd6)

**4. Tanjungkulai**

![image](https://github.com/user-attachments/assets/bc76aa5c-f536-4851-aa79-72eb821e05f5)
![image](https://github.com/user-attachments/assets/afc24f02-5881-4a1f-8a21-6689781fbfcf)

## Least Connection

**1. General**

![image](https://github.com/user-attachments/assets/595d7e4f-aa69-4c17-90b5-0a7a2e0b825f)
![image](https://github.com/user-attachments/assets/19bd1da4-2df0-46b5-a403-afb064071245)

**2. Kotalingga**

![image](https://github.com/user-attachments/assets/2f3caee7-2c07-4f6f-a7de-51b86c89b23c)
![image](https://github.com/user-attachments/assets/173c49d8-9737-4628-8c28-1acee45c5ec4)

**3. Bedahulu**

![image](https://github.com/user-attachments/assets/d2b1273f-1d5a-4387-a512-d6e1bcdf65dd)
![image](https://github.com/user-attachments/assets/c948c66a-7fd3-447f-90c9-d0ee292da431)

**4. Tanjungkulai**

![image](https://github.com/user-attachments/assets/e2becb3c-7fc1-41fd-8eef-d25947061d5a)
![image](https://github.com/user-attachments/assets/49a76646-fad4-4b5a-afdb-e2beab1ea744)

## IP Hash

**1. General**

![image](https://github.com/user-attachments/assets/2323493c-eca9-4175-b9c8-8d2f14b018ef)
![image](https://github.com/user-attachments/assets/378d0164-36ca-4624-ab95-744755ddc4c0)

**2. Kotalingga**

![image](https://github.com/user-attachments/assets/4f65d8f8-794f-422e-b282-3ed3f7301810)
![image](https://github.com/user-attachments/assets/81c10c32-ad1e-4a66-b586-ac3d85e3ca26)

**3. Bedahulu**

![image](https://github.com/user-attachments/assets/a03ffc21-26ff-46cc-aa0c-51350d9d2b94)
![image](https://github.com/user-attachments/assets/fb75c9e4-9bbf-481f-9097-bcdb0e2d9683)

**4. Tanjungkulai**

![image](https://github.com/user-attachments/assets/3848a599-513d-4a92-a4c4-2e5e598f101c)
![image](https://github.com/user-attachments/assets/2ea42134-bd80-4a6b-ae97-7f73c5260b0f)

## Generic Hash

**1. General**

![image](https://github.com/user-attachments/assets/5f935355-ac70-4a1c-86c3-de4176f54287)
![image](https://github.com/user-attachments/assets/8f21b456-0bb7-4261-8cc7-7a6eb5c20cef)

**2. Kotalingga**

![image](https://github.com/user-attachments/assets/c7d226a7-0a95-4c6c-b84f-228f3ad9f0a2)
![image](https://github.com/user-attachments/assets/2eecae95-04ef-41c5-8457-0ba027edbf00)

**3. Bedahulu**

![image](https://github.com/user-attachments/assets/0dbd6e32-797e-455c-9f77-07b769fdee3f)
![image](https://github.com/user-attachments/assets/4ea3b611-39a6-4216-b2c1-57bc185d814b)

**4. Tanjungkulai**

![image](https://github.com/user-attachments/assets/16db1bc5-5713-4d47-afc6-087802444326)
![image](https://github.com/user-attachments/assets/9046b000-a26f-4961-bbe5-cfdec321e786)

## Laporan Hasil Benchmark
```
1. general
**round robin**
completed request: 1000
failed request: 666
time taken for test: 1.628s
request per second (rps): 614.07

**least conn**
completed request: 1000
failed request: 682
time taken for test: 1.780s
request per second (rps): 561.71

**ip hash**
completed request: 1000
failed request: 0
time taken for test: 1.583s
request per second (rps): 631.59

**generic hash**
completed request: 1000
failed request: 0
time taken for test: 1.376s
request per second (rps): 726.75

2. kotalingga
**round robin**
completed request: 1000
failed request: 0
time taken for test: 0.383s
request per second (rps): 2608.88

**least conn**
completed request: 1000
failed request: 0
time taken for test: 0.382s
request per second (rps): 2620.40

**ip hash**
completed request: 1000
failed request: 0
time taken for test: 0.394s
request per second (rps): 2538.41

**generic hash**
completed request: 1000
failed request: 0
time taken for test: 0.380s
request per second (rps): 2633.76

3. bedahulu
**round robin**
completed request: 1000
failed request: 0
time taken for test: 0.523
request per second (rps): 1911.69

**least conn**
completed request: 1000
failed request: 0
time taken for test: 0.626s
request per second (rps): 1597.74

**ip hash**
completed request: 1000
failed request: 0
time taken for test: 1.566s
request per second (rps): 638.74

**generic hash**
completed request: 1000
failed request: 0
time taken for test: 0.509s
request per second (rps) 1965

4. tanjungkulai
**round robin**
completed request: 1000
failed request: 0
time taken for test: 0.615
request per second (rps): 1625.24

**least conn**
completed request: 1000
failed request: 0
time taken for test: 0.456
request per second (rps): 2194.71

**ip hash**
completed request: 1000
failed request: 0
time taken for test: 0.664
request per second (rps): 1506.16

**generic hash**
completed request: 1000
failed request: 0
time taken for test: 0.413s
request per second (rps): 2420.66s
```

## Grafik RPS:

![grafik jarkom modul 2 laporan analisa](https://github.com/user-attachments/assets/9dadf14a-2a89-452f-9ffb-e696cce3312f)


## Hasil Analisis:
Dari hasil benchmarking, dapat disimpulkan bahwa algoritma load balancing **generic hash** adalah yang terbaik karena mempunyai request per second yang tinggi dengan waktu yang lebih cepat dibandingkan dengan algoritma load balancing lainnya. Sedangkan untuk web server dengan performa terbaik adalah **Kotalingga** dengan rps dan time taken for test yang lebih unggul.

Meme:

![image](https://github.com/user-attachments/assets/063951cb-ef7d-4bd1-9695-de7e6763638b)

# No. 16
Soal:
>Karena dirasa kurang aman dari brainrot karena masih memakai IP, markas ingin akses ke Solok memakai solok.xxxx.com dengan alias www.solok.xxxx.com (sesuai web server terbaik hasil analisis kalian).

Karena pada DNS Sriwijaya sudah ada bind9, maka tidak perlu menginstall bind9 lagi dan langsung menambahkan konfigurasi pada ```/etc/bind/named.conf.local```.
```
zone "solok.it15.com" {
	type master;
	file "/etc/bind/jarkom/solok.it15.com";
};
```
![image](https://github.com/user-attachments/assets/ba725b44-71b2-4744-83de-8c4423f32534)

Di directory ```/etc/bind/jarkom``` yang telah dibuat sebelumnya, copy template default dari bind9 dengan command ```cp /etc/bind/db.local /etc/bind/jarkom/solok.it15.com```

Setelah copy template dari bind9, ubah konfigurasi pada ```/etc/bind/jarkom/solok.it15.com``` hingga sesuai dengan konfigurasi di bawah. Untuk IPnya adalah IP Tanjungkulai (10.71.2.6)
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     solok.it15.com. root.solok.it15.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      solok.it15.com.
@       IN      A       10.71.2.6
www     IN      CNAME   solok.it15.com.
@       IN      AAAA    ::1
```
![image](https://github.com/user-attachments/assets/dbf3f545-61a3-4a2f-9968-5ff81f73ccd7)

Domain solok.it15.com beserta aliasnya (www.solok.it15.com) sudah bisa dites.
![image](https://github.com/user-attachments/assets/84511951-4ffe-4126-afb7-f8da08310945)
![image](https://github.com/user-attachments/assets/4bd3a6e3-5edc-4ff1-9574-51effcf91780)
![image](https://github.com/user-attachments/assets/b9355dde-800b-40b7-b820-c3699e4e4a28)
