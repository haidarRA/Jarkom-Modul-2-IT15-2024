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
Setelah menjalankan command di atas, cek apakah router bisa terhubung dengan internet dengan menggunakan command **ping google.com**.
![image](https://github.com/user-attachments/assets/6cf6c02d-ff22-4262-8b0b-ad835b4e8731)
Di sini dapat terlihat bahwa router berhasil terhubung dengan internet.

Selanjutnya, agar DNS (dalam kasus ini, Sriwijaya) juga terhubung dengan internet, tambahkan nameserver IP router (192.168.122.1) ke /etc/resolv.conf pada node DNS.
```
nameserver 192.168.122.1
```

![image](https://github.com/user-attachments/assets/46d8f157-0a20-4790-a0b2-974166162d73)

Setelah menambahkan nameserver IP router ke DNS, cek apakah DNS bisa terhubung ke internet dengan menggunakan command **ping google.com**
![image](https://github.com/user-attachments/assets/6d890198-9796-4592-b529-1bd402f8558f)
Di sini dapat terlihat bahwa router berhasil terhubung dengan internet sehingga bind9 dapat di-install di node DNS Sriwijaya.
