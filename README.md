# Jarkom-Modul-5-A10-2021

### Berikut merupakan Topologi GNS3 nya
![image](https://user-images.githubusercontent.com/74232912/145290026-14306a23-c16d-419c-bad4-51f783009dd4.png)

### A. Melakukan Konfigurasi setiap NODE nya

#### FOOSHA

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet dhcp
hwaddress ether 26:f6:ae:d3:c3:14

auto eth1
iface eth1 inet static
address 10.4.7.146
netmask 255.255.255.252

auto eth2
iface eth2 inet static
address 10.4.7.149
netmask 255.255.255.252
```

#### WATER 7 (DHCP RELAY)

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 10.4.7.145
netmask 255.255.255.252
gateway 10.4.7.146

auto eth1
iface eth1 inet static
address 10.4.7.129
netmask 255.255.255.248

auto eth2
iface eth2 inet static
address 10.4.7.1
netmask 255.255.255.128


auto eth3
iface eth3 inet static
address 10.4.0.1
netmask 255.255.252.0
```

#### GUANHAO (DHCP RELAY)

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 10.4.7.150
netmask 255.255.255.252
gateway 10.4.7.149

auto eth1
iface eth1 inet static
address 10.4.7.137
netmask 255.255.255.248

auto eth2
iface eth2 inet static
address 10.4.4.1
netmask 255.255.254.0

auto eth3
iface eth3 inet static
address 10.4.6.1
netmask 255.255.255.0
```

#### BLUENO (DHCP CLIENT)

```
auto eth0
iface eth0 inet static
address 10.4.7.2
netmask 255.255.255.128
gateway 10.4.7.1
```

#### CIPHER (DHCP CLIENT)

```
auto eth0
iface eth0 inet static
address 10.4.0.2
netmask 255.255.252.0
gateway 10.4.0.1
```

#### ELENA (DHCP CLIENT)

```
auto eth0
iface eth0 inet static
address 10.4.4.2
netmask 255.255.254.0
gateway 10.4.4.1
```

#### FUKUROU (DHCP CLIENT)

```
auto eth0
iface eth0 inet static
address 10.4.6.2
netmask 255.255.255.0
gateway 10.4.6.1
```

#### DORIKI (DNS SERVER)

```
auto eth0
iface eth0 inet static
address 10.4.7.130
netmask 255.255.255.248
gateway 10.4.7.129
```

#### JIPANGU (DHCP SERVER)

```
auto eth0
iface eth0 inet static
address 10.4.7.131
netmask 255.255.255.248
gateway 10.4.7.129
```

#### MAINGATE (WEB SERVER)

```
auto eth0
iface eth0 inet static
address 10.4.7.139
netmask 255.255.255.248
gateway 10.4.7.137
```

#### JORGE (WEB SERVER)

```
auto eth0
iface eth0 inet static
address 10.4.7.138
netmask 255.255.255.248
gateway 10.4.7.137
```

### B. Karena kalian telah belajar subnetting dan routing, Luffy ingin meminta kalian untuk membuat topologi tersebut menggunakan teknik CIDR atau VLSM.

Kami menggunakan teknik VLSM.

![image](https://user-images.githubusercontent.com/74232912/145291917-86a4734b-c2b9-490e-97c1-5ee49d5d2fda.png)

![image](https://user-images.githubusercontent.com/74232912/145292016-7676099b-6dfa-4b45-accc-539d8c89b2a0.png)

![image](https://user-images.githubusercontent.com/74232912/145292163-5ab63e24-229f-498a-823c-bc6d3546e7fe.png)

![image](https://user-images.githubusercontent.com/74232912/145292274-16dd88a8-463d-4ec2-b496-c3eb6af32b35.png)

### C. Kalian juga diharuskan melakukan Routing agar setiap perangkat pada jaringan tersebut dapat terhubung

#### Routing dilakukan pada FOOSHA

```
route add -net 10.4.7.128 netmask 255.255.255.248 gw 10.4.7.145
route add -net 10.4.7.0 netmask 255.255.255.128 gw 10.4.7.145
route add -net 10.4.0.0 netmask 255.255.252.0 gw 10.4.7.145

route add -net 10.4.4.0 netmask 255.255.254.0 gw 10.4.7.150
route add -net 10.4.6.0 netmask 255.255.255.0 gw 10.4.7.150
route add -net 10.4.7.136  netmask 255.255.255.248 gw 10.4.7.150
```

Untuk melihat hasil routing bisa dengan perintah

```
route -n
```

![image](https://user-images.githubusercontent.com/74232912/145301291-fabeb18f-3b58-4951-9846-c819aaadab03.png)


### D. Tugas berikutnya adalah memberikan ip pada subnet Blueno, Cipher, Fukurou, dan Elena secara dinamis menggunakan bantuan DHCP server. Kemudian kalian ingat bahwa kalian harus setting DHCP Relay pada router yang menghubungkannya.

Buka ```nano /etc/network/interfaces```
Kemudian isikan 

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet dhcp

```

### 1. Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Foosha menggunakan iptables, tetapi Luffy tidak ingin menggunakan MASQUERADE.

Gunakan perintah berikut pada node FOOSHA

```
iptables -t nat -A POSTROUTING -s 10.4.0.0/21 -o eth0 -j SNAT --to-source 192.168.122.172
```

Setelah itu jalankan perintah berikut ke setiap node pada TOPOLOGI kecuali FOOSHA

```
echo nameserver 192.168.122.1 > /etc/resolv.conf
```

![image](https://user-images.githubusercontent.com/74232912/145293243-e13e9b14-2d80-4736-abc2-a8388b7f8cf1.png)

Kembali pada persoalan **D**, dikarenakan Semua node sudah bisa mengakses internet maka lakukanlah penginstalan isc-dhcp-server dan isc-dhcp-relay

#### Untuk isc-dhcp-server pada node JIPANGU

```
apt-get update
apt-get install nano -y
apt-get install isc-dhcp-server -y
```

Setelah itu buka ```nano /etc/default/isc-dhcp-server```
Kemudian pada bagian ```INTERFACES = "eth0"```

![image](https://user-images.githubusercontent.com/74232912/145302136-0c0f4585-2015-489c-b165-b6c60472a854.png)

Setelah itu buka ```nano /etc/dhcp/dhcpd.conf```
Tambahkan config berikut

```
subnet 10.4.7.0 netmask 255.255.255.128 {
        range 10.4.7.2 10.4.7.126;
        option routers 10.4.7.1;
        option broadcast-address 10.4.7.127;
        option domain-name-servers 10.4.7.130;
        default-lease-time 600;
        max-lease-time 7200;
}

subnet 10.4.0.0 netmask 255.255.252.0 {
        range 10.4.0.2 10.4.3.254;
        option routers 10.4.0.1;
        option broadcast-address 10.4.3.255;
        option domain-name-servers 10.4.7.130;
        default-lease-time 600;
        max-lease-time 7200;
}

subnet 10.4.4.0 netmask 255.255.254.0 {
        range 10.4.4.2 10.4.5.254;
        option routers 10.4.4.1;
        option broadcast-address 10.4.5.255;
        option domain-name-servers 10.4.7.130;
        default-lease-time 600;
        max-lease-time 7200;
}

subnet 10.4.6.0 netmask 255.255.255.0 {
        range 10.4.6.2 10.4.6.254;
        option routers 10.4.6.1;
        option broadcast-address 10.4.6.255;
        option domain-name-servers 10.4.7.130;
        default-lease-time 600;
        max-lease-time 7200;
}

subnet 10.4.7.128 netmask 255.255.255.248 {
        option routers 10.4.7.129;
}
```
![image](https://user-images.githubusercontent.com/74232912/145302218-873984e4-b968-421c-9ee7-c2f2f7fe22ea.png)
![image](https://user-images.githubusercontent.com/74232912/145302296-191efa3b-d681-4eca-8cc9-af2e4db80f30.png)

Setelah itu lakukan ```service isc-dhcp-server restart``` dan ```service isc-dhcp-server status```

Setelah itu, lakukan reset pada node DHCP CLIENT yaitu BLUENO, CIPHER, ELENA, dan FUKUROU melalu GNS3 nya langsung dan hidupkan kembali maka IP CLIENT akan berubah Dinamis

#### Selanjutnya install isc-dhcp-relay pada WATER 7 dan GUANHAO

Lakukan install isc-dhcp-server

```
apt-get update
apt-get install nano -y
apt-get install isc-dhcp-relay -y
```

Jika sudah install maka akan diminta beberapa hal seperti berikut dan isikan sesuai dengan yang digambar

![image](https://user-images.githubusercontent.com/74232912/145302719-5c7661f6-1206-42e5-9c5e-b556a9a1f85c.png)
![image](https://user-images.githubusercontent.com/74232912/145302763-2e931c80-7cc3-44f1-9497-a5d9fe8b9878.png)

Dan bagian ini dikosongkan saja
![image](https://user-images.githubusercontent.com/74232912/145302812-4ae3e3e2-c39a-496e-a0fd-7204e0529bb7.png)

Setelah itu lakukan ```service isc-dhcp-relay restart``` pada GUANHAO dan WATER7

#### Setelah melakukan install dan config pada node DHCP server dan relay, maka jika kita restart node DHCP client akan mendapatkan IP Dinamis

![image](https://user-images.githubusercontent.com/74232912/145303134-12f4fe9d-4747-4d11-903c-02b5fcc816ed.png)
![image](https://user-images.githubusercontent.com/74232912/145303238-194b0aae-76cf-4286-8344-44d8ab6f6d74.png)
![image](https://user-images.githubusercontent.com/74232912/145303303-40048588-b9f9-4e79-a87b-05c5bc45382a.png)
![image](https://user-images.githubusercontent.com/74232912/145303336-bba057d8-a6d6-4c5b-b42d-899004ceb7b5.png)


### 2. Kalian diminta untuk mendrop semua akses HTTP dari luar Topologi kalian pada server yang merupakan DHCP Server dan DNS Server demi menjaga keamanan.

Lakukan command dibawah ini pada DHCP SERVER (**JIPANGU**) dan WEB SERVER (**DORIKI**)

```
iptables -A FORWARD -d 10.4.7.128/29 -i eth0 -p tcp --dport 80 -j DROP
iptables -A FORWARD -d 10.4.7.128/29 -i eth0 -p tcp --dport 443 -j ACCEPT
```

![image](https://user-images.githubusercontent.com/74232912/145303658-e1a66338-4f95-4413-a2cf-55a3e0a44945.png)
![image](https://user-images.githubusercontent.com/74232912/145303742-d8e07ce2-0493-42fa-ab25-cd272fcecf67.png)

Hasilnya

![image](https://user-images.githubusercontent.com/74232912/145303891-c68a57bf-e98a-414a-81d2-fbde66a4a07b.png)


### 3. Karena kelompok kalian maksimal terdiri dari 3 orang. Luffy meminta kalian untuk membatasi DHCP dan DNS Server hanya boleh menerima maksimal 3 koneksi ICMP secara bersamaan menggunakan iptables, selebihnya didrop.

Lakukan command dibawah ini pada DORIKI dan JIPANGU

```
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```

![image](https://user-images.githubusercontent.com/74232912/145304140-69a64fa0-6393-4b88-8d94-2708c0e0b820.png)
![image](https://user-images.githubusercontent.com/74232912/145304161-2a24fbad-a696-446a-b96f-6dab3febe74c.png)


### 4 Akses dari subnet Blueno dan Cipher hanya diperbolehkan pada pukul 07.00 - 15.00 pada hari Senin sampai Kamis

Lakukan Konfigurasi berikut pada DORIKI

#### CIPHER

```
iptables -A INPUT -s 10.4.0.0/22 -d 10.4.7.128/29 -m time --timestart 07:00 --timestop 15:00 --weekdays Mon,Tue,Wed,Thu -j ACCEPT
iptables -A INPUT -s 10.4.0.0/22 -j REJECT
```

#### Blueno

```
iptables -A INPUT -s 10.4.7.0/25 -d 10.4.7.128/29 -m time --timestart 07:00 --timestop 15:00 --weekdays Mon,Tue,Wed,Thu -j ACCEPT
iptables -A INPUT -s 10.4.7.0/25 -j REJECT
```

Kemudian pada client BLUENO dan CIPHER diganti tanggal dan waktu nya

#### Untuk bisa diakses

```
date -s "6 DEC 2021 08:00:00"
```

![image](https://user-images.githubusercontent.com/74232912/145305295-b0b5a552-bfe1-4c5f-abdd-9ce22b108d59.png)

dan kemudian coba tes ping ke google.com

![image](https://user-images.githubusercontent.com/74232912/145305380-721de9ae-fd7e-433e-bbbe-b82677399c8e.png)

#### Untuk yang tidak bisa di akses

```
date -s "6 DEC 2021 18:00:00"
```
![image](https://user-images.githubusercontent.com/74232912/145305490-77a65088-8314-44cc-9830-b160382798bd.png)

dan kemudian lakukan ping ke google.com

![image](https://user-images.githubusercontent.com/74232912/145305508-57716a08-a715-41dc-bf50-fc61764d6bc6.png)


### 5. Akses dari subnet Elena dan Fukuro hanya diperbolehkan pada pukul 15.01 hingga pukul 06.59 setiap harinya.

Config dilakukan pada DORIKI

#### Elena

```
iptables -A INPUT -s 10.4.4.0/23 -m time --timestart 15:01 --timestop 06:59 -j ACCEPT
iptables -A INPUT -s 10.4.4.0/23 -j REJECT
```

#### Fukurou

```
iptables -A INPUT -s 10.4.6.0/24 -m time --timestart 15:01 --timestop 06:59 -j ACCEPT
iptables -A INPUT -s 10.4.6.0/24 -j REJECT
```

Kemudian Client ELENA dan FUKUROU diganti tanggal dan waktunya

#### Untuk bisa diakses

```
date -s "6 DEC 2021 15:12:12"
```

![image](https://user-images.githubusercontent.com/74232912/145306770-a7e4e613-6870-4501-beab-863e462fbafc.png)

dan coba ping ke google.com

![image](https://user-images.githubusercontent.com/74232912/145306693-bea3316a-83a6-4fb9-923a-9a18d149fed5.png)

#### Untuk tidak bisa diakses

```
date -s "6 DEC 2021 07:00:00"
```

![image](https://user-images.githubusercontent.com/74232912/145306815-7773b816-e428-4166-ad70-e8179d0dd344.png)

kemudain coba ping ke google.com

![image](https://user-images.githubusercontent.com/74232912/145306596-ea30a269-e880-4941-bb9d-8e83290684b2.png)


### 6. Karena kita memiliki 2 Web Server, Luffy ingin Guanhao disetting sehingga setiap request dari client yang mengakses DNS Server akan didistribusikan secara bergantian pada Jorge dan Maingate
#### Buat Jorge dan Maingate menjadi Web Server
```
apt-get install apache2 -y  

cd /var/www/html  

rm index.html  

nano index.html  
```
Isikan apa saja pada index.html. Lalu lakukan restart dengan command ```service apache2 restart```.  

#### iptables pada Guanhao
```
iptables -A PREROUTING -t nat -p tcp -d 10.4.7.128/29 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination  10.4.7.138  

iptables -A PREROUTING -t nat -p tcp -d 10.4.7.128/29 -j DNAT --to-destination 10.4.7.139  

iptables -t nat -A POSTROUTING -p tcp -d 10.4.7.138 -j SNAT --to-source 10.4.7.128  

iptables -t nat -A POSTROUTING -p tcp -d 10.4.7.139 -j SNAT --to-source 10.4.7.128  
```  

#### Testing
Gunakan command ```curl 10.4.7.128``` pada klien Elena atau Fukurou dua kali atau lebih. Nanti akan mengeluarkan isi dari index.html pada Jorge dan Maingate secara bergantian.  
![testelena](https://imgur.com/MMHiO0d.png)
![testfukurou](https://imgur.com/8kBczQM.png)
