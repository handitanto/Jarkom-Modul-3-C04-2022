# Praktikum Jaringan Komputer Modul 3 Kelompok C04

# Jarkom-Modul-3-C04-2022

| **No** | **Nama**                  | **NRP**    |
| ------ | ------------------------- | ---------- |
| 1      | Muhammad Fuad Salim | 5025201057 |
| 2      | Handitanto Herprasetyo | 5025201077 |
| 3      | Sastiara Maulikh | 5025201257 |

---

## Daftar Isi

- [Setting](#setting)
- [Nomor 1](#nomor-1)
- [Nomor 2](#nomor-2)
- [Nomor 2 Lanjutan](#nomor-2-lanjutan)
- [Nomor 3](#nomor-3)
- [Nomor 4](#nomor-4)
- [Nomor 5](#nomor-5)
- [Nomor 6](#nomor-6)
- [Nomor 7](#nomor-7)
---
### Setting
Pada langkah awal dilakukan setting GNS sesuai dengan modul yang telah diberikan. Konfigurasi yang digunakan adalah sebagai berikut:

#### Ostania
```
auto eth0
iface eth0 inet dhcp
auto eth1
iface eth1 inet static
	address 192.181.1.1
	netmask 255.255.255.0
auto eth2
iface eth2 inet static
	address 192.181.2.1
	netmask 255.255.255.0
auto eth3
iface eth3 inet static
  address 192.181.3.1
	netmask 255.255.255.0
```

#### SSS
```
auto eth0
iface eth0 inet dhcp
```

#### Garden
```
auto eth0
iface eth0 inet dhcp
```

#### WISE
``` 
auto eth0
iface eth0 inet static
	address 192.181.2.2
	netmask 255.255.255.0
	gateway 192.181.2.1
```

#### Berlint
```
auto eth0
iface eth0 inet static
	address 192.181.2.3
	netmask 255.255.255.0
	gateway 192.181.2.1
```

#### Westalis
```
auto eth0
iface eth0 inet static
	address 192.181.2.4
	netmask 255.255.255.0
	gateway 192.181.2.1
```

#### Eden
```
auto eth0
iface eth0 inet dhcp
hwaddress ether 32:20:59:5c:25:3b
```

#### NewstonCastle
```
auto eth0
iface eth0 inet dhcp
```

#### KemonoPark
```
auto eth0
iface eth0 inet dhcp
```

masukkan ke `/root/.bashrc` ke setiap node
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
```

### Nomor 1 
Loid bersama Franky berencana membuat peta tersebut dengan kriteria WISE sebagai DNS Server, Westalis sebagai DHCP Server, Berlint sebagai Proxy Server (1)

#### Ostania 
masukkan ke `/root/.bashrc`
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.181.0.0/16
cat /etc/resolv.conf
echo nameserver 192.168.122.1 > /etc/resolv.conf
```

##### WISE -> DNS Server
masukkan ke `/root/.bashrc`
```
apt-get update
apt-get install bind9 -y
```

##### Westalis -> DHCP Server
masukkan ke `/root/.bashrc`
```
apt-get update
apt-get install isc-dhcp-server -y
```

##### Berlint -> Proxy Server
masukkan ke `/root/.bashrc`
```
apt-get update
apt-get install squid -y
service squid start
```
lalu cek dengan `service squid status` </br>
<img src="https://user-images.githubusercontent.com/70510279/200559998-aef4a55e-7532-423f-937e-265fda7114d3.png" alt="Squid Status" width="200"/>

</br>

### Nomor 2 
dan Ostania sebagai DHCP Relay (2). Loid dan Franky menyusun peta tersebut dengan hati-hati dan teliti.
#### Ostania 
```
apt-get update
apt-get install isc-dhcp-relay -y
```
lalu edit file ` /etc/default/isc-dhcp-relay` dengan
```
SERVER = "192.181.2.4" #IP Westalis
INTERFACES = "eth1 eth2 eth3"
OPTIONS = ""
```

### Nomor 2 Lanjutan
Semua client yang ada HARUS menggunakan konfigurasi IP dari DHCP Server

#### Konfigurasi
Ubah semua koneksi `statis` menjadi `dhcp` pada semua node client. </br>
<img src="https://user-images.githubusercontent.com/70510279/200560417-f1d89a90-90cd-4829-923e-abc8036aed25.png" alt="Ubah DHCP" width="200"/>
</br>
yang perlu diubah yaitu 
- SSS
- Garden
- Eden
- NewstonCastle
- KemonoPark

</br>
Syntax Konfigurasi untuk nomor selanjutnya 

```
subnet 'NID' netmask 'Netmask' {
    range 'IP_Awal' 'IP_Akhir';
    option routers 'iP_Gateway';
    option broadcast-address 'IP_Broadcast';
    option domain-name-servers 'DNS_yang_diinginkan';
    default-lease-time 'Waktu';
    max-lease-time 'Waktu';
}
```
