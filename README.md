# Praktikum Jaringan Komputer Modul 3 Kelompok C04

# Jarkom-Modul-3-C04-2022

| **No** | **Nama**                  | **NRP**    |
| ------ | ------------------------- | ---------- |
| 1      | Muhammad Fuad Salim | 5025201057 |
| 2      | Handitanto Herprasetyo | 5025201077 |
| 3      | Sastiara Maulikh | 5025201257 |

---

### Topologi
<img alt="topologi 3" width="659" src="https://user-images.githubusercontent.com/80630201/209267067-e1b6103c-1a85-4d37-a84a-7c49cbd6216d.png">

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
### Nomor 3 
Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.50 - [prefix IP].1.88 dan [prefix IP].1.120 - [prefix IP].1.155 (3)

#### Ubah di Westalis
edit file `/etc/dhcp/dhcpd.conf` sebagai berikut
```
subnet 192.181.2.0 netmask 255.255.255.0{
    option routers 192.181.2.1;
}
```
lalu tambahkan setingan ip range dan ip dns
```
subnet 192.181.1.0 netmask 255.255.255.0 {
        range 192.181.1.50 192.181.1.88;
        range 192.181.1.120 192.181.1.155;
        option routers 192.181.1.1;
        option broadcast-address 192.181.1.255;
        option domain-name-servers 192.181.2.2;
        default-lease-time 300;
        max-lease-time 6900;
}
```

### Nomor 4
Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.10 - [prefix IP].3.30 dan [prefix IP].3.60 - [prefix IP].3.85

#### Westalis
edit file `/etc/dhcp/dhcpd.conf` sebagai berikut
```
subnet 192.181.3.0 netmask 255.255.255.0 {
        range 192.181.3.10 192.181.3.30;
        range 192.181.3.60 192.181.3.85;
        option routers 192.181.3.1;
        option broadcast-address 192.181.3.255;
        option domain-name-servers 192.181.2.2;
        default-lease-time 600;
        max-lease-time 6900;
}
```
### Nomor 5
Client mendapatkan DNS dari WISE dan client dapat terhubung dengan internet melalui DNS tersebut

jalankan dhcp server dengan `service isc-dhcp-server restart`

##### Test Client di Kemono Park
![nomor 5 c04](https://user-images.githubusercontent.com/80630201/209267070-5fed42ab-e904-493e-ab36-72bcfea5f884.jpg)

### Nomor 6
Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 5 menit sedangkan pada client yang melalui Switch3 selama 10 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 115 menit

settingan waktu sudah ditambahkan di no 3 dan 4 yang ada di `/etc/dhcp/dhcpd.conf`
```
default-lease-time 300;
max-lease-time 6900;
```
### Nomor 7
Loid dan Franky berencana menjadikan Eden sebagai server untuk pertukaran informasi dengan alamat IP yang tetap dengan IP [prefix IP].3.13 

Tambahkan config berikut di `/etc/dhcp/dhcpd.conf`
```
host Eden {
        hardware ethernet 32:20:59:5c:25:3b;
        fixed-address 192.181.3.13;
}
```
lalu setting network config Eden seperti yang sudah dilakukan diatas
```
auto eth0
iface eth0 inet dhcp
hwaddress ether 32:20:59:5c:25:3b

## Soal Proxy
SSS, Garden, dan Eden digunakan sebagai client Proxy agar pertukaran informasi dapat terjamin keamanannya, juga untuk mencegah kebocoran data.

Pada Proxy Server di Berlint, Loid berencana untuk mengatur bagaimana Client dapat mengakses internet. Artinya setiap client harus menggunakan Berlint sebagai HTTP & HTTPS proxy.

### Rule Proxy
- Client hanya dapat mengakses internet diluar (selain) hari & jam kerja (senin-jumat 08.00 - 17.00) dan hari libur (dapat mengakses 24 jam penuh)
```acl AVAILABLE_WORKING time MTWHF 08:00-17:00```
- Saat akses internet dibuka, client dilarang untuk mengakses web tanpa HTTPS. (Contoh web HTTP: http://example.com)
```
acl Safe_ports port 443         # https
http_access allow Safe_ports
```
- Adapun pada hari dan jam kerja sesuai nomor (1), client hanya dapat mengakses domain loid-work.com dan franky-work.com (IP tujuan domain dibebaskan)
```
pada /etc/squid/restrict-sites.acl: 
loid-work.com
franky-work.com

acl WHITELISTS dstdomain "/etc/squid/restrict-sites.acl"

## Test Proxy
install lynx pada semua proxy client dengan `apt-get install lynx -y`
- pada `http://example.com`<br>
![proxy 1 c04](https://user-images.githubusercontent.com/80630201/209267073-3757fe94-610a-475b-becf-eed000ff52ea.jpg) <br>


- pada `https://example.com` <br>
![proxy 2](https://user-images.githubusercontent.com/80630201/209267075-f1b6327b-df28-4e08-8b18-2f9330435efb.jpg)
