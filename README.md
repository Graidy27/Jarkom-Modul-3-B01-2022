# Jarkom-Modul-3-B01-2022

Anggota :
> Ichsanul Aulia - 05111840007001\
> Alfin Indrawan - 5025201199\
> Graidy Megananda - 5025201188

---
## Tabel Konten
- [Pembagian tugas](#pembagian-tugas)
- [Soal 1](#nomor-1)
- [Soal 2](#nomor-2)
- [Soal 3](#nomor-3)
- [Soal 4](#nomor-4)
- [Soal 5](#nomor-5)
- [Soal 6](#nomor-6)
- [Soal 7](#nomor-7)
- [Soal 8](#nomor-8)
- [Soal 9](#nomor-9)
- [Soal 10](#nomor-10)
- [Soal 11](#nomor-11)
- [Soal 12](#nomor-12)
- [Soal 13](#nomor-13)
- [Soal 14](#nomor-14)
- [Soal 15](#nomor-15)
- [Soal 16](#nomor-16)
- [Soal 17](#nomor-17)
- [Kesulitan](#kesulitan)

## Pembagian Tugas
> Ichsanul Aulia - 7 hingga 11\
> Alfin Indrawan - 1 hingga 6\
> Graidy Megananda - 12 hingga

## Nomor 1
### Soal
Loid bersama Franky berencana membuat peta tersebut dengan kriteria WISE sebagai DNS Server, Westalis sebagai DHCP Server, Berlint sebagai Proxy Server

### Penyelesaian
* Wise
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt update
apt install bind9
echo ‘options {
        directory "/var/cache/bind";

        forwarders {
                192.168.122.1;
        };

        // dnssec-validation auto;
        allow-query{any;};
        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
}; ’ > /etc/bind/named.conf.options
service bind9 start

```

* Westalis
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt update
apt install isc-dhcp-server -y
echo ‘INTERFACES="eth0"‘ > /etc/default/isc-dhcp-server
```
* Berlint
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt update
apt install squid -y
mv /etc/squid/squid.conf /etc/squid/squid.conf.bak

echo ‘include /etc/squid/acl.conf
http_port 8080
http_access deny UNAVAILABLE_WORKING
visible_hostname Berlint’ > /etc/squid/squid.conf

echo ‘acl UNAVAILABLE_WORKING time MTWHF 08:00-17:00
acl AVAILABLE_WORKING time AS 00:00-24:00’ > /etc/squid/acl.conf 
```


## nomor 2
### Soal
Dan ostania sebagai dshcp relay

```
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt update
apt install isc-dhcp-relay -y
service isc-dhcp-relay start
echo ‘SERVERS="192.173.2.4"
INTERFACES="eth1 eth2 eth3"
OPTIONS=
’ > /etc/default/isc-dhcp-relay
echo ‘net.ipv4.ip_forward=1’ > /etc/sysctl.conf
service isc-dhcp-relay start


```

## nomor 3
### Soal
Semua client yang ada HARUS menggunakan konfigurasi IP dari DHCP Server.
Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.50 - [prefix IP].1.88 dan [prefix IP].1.120 - [prefix IP].1.155 


untuk menggantikan ip dari dhcp server saya melakukan manual di edit configuration
auto eth0
iface eth0 inet dhcp

* Westalis
```
echo ‘subnet 192.173.1.0 netmask 255.255.255.0 {
    range 192.173.1.50 192.173.1.88;
    range 192.173.1.120 192.173.1.155;
    option routers 192.173.1.1;
    option broadcast-address 192.173.1.255;
    option domain-name-servers 192.173.2.2;
    default-lease-time 300;
    max-lease-time 6900;
}
subnet 192.173.2.0 netmask 255.255.255.0 {
}

service isc-dhcp-server restart
```

## nomor 4
### Soal
Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.10 - [prefix IP].3.30 dan [prefix IP].3.60 - [prefix IP].3.85 
* Westalis
```
echo ‘subnet 192.173.1.0 netmask 255.255.255.0 {
    range 192.173.1.50 192.173.1.88;
    range 192.173.1.120 192.173.1.155;
    option routers 192.173.1.1;
    option broadcast-address 192.173.1.255;
    option domain-name-servers 192.173.2.2;
    default-lease-time 300;
    max-lease-time 6900;
}
subnet 192.173.2.0 netmask 255.255.255.0 {
}
subnet 192.173.3.0 netmask 255.255.255.0 {
    range 192.173.3.10 192.173.3.30;
    range 192.173.3.60 192.173.3.85;
    option routers 192.173.3.1;
    option broadcast-address 192.173.3.255;
    option domain-name-servers 192.173.2.2;
    default-lease-time 600;
    max-lease-time 6900;
}


service isc-dhcp-server restart
```
## nomor 5
### Soal
Client mendapatkan DNS dari WISE dan client dapat terhubung dengan internet melalui DNS tersebut. 

* Wise
```
echo ‘options {
        directory "/var/cache/bind";

        forwarders {
                192.168.122.1;
        };

        // dnssec-validation auto;
        allow-query{any;};
        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
}; ’ > /etc/bind/named.conf.options
service bind9 start
```

* Eden Configuration
```
auto eth0
iface eth0 inet dhcp
hwaddress ether 22:80:5a:6c:27:4c
```
* Westalis
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt update
apt install isc-dhcp-server -y
echo ‘INTERFACES="eth0"‘ > /etc/default/isc-dhcp-server
echo ‘subnet 192.173.1.0 netmask 255.255.255.0 {
    range 192.173.1.50 192.173.1.88;
    range 192.173.1.120 192.173.1.155;
    option routers 192.173.1.1;
    option broadcast-address 192.173.1.255;
    option domain-name-servers 192.173.2.2;
    default-lease-time 300;
    max-lease-time 6900;
}
subnet 192.173.2.0 netmask 255.255.255.0 {
}
subnet 192.173.3.0 netmask 255.255.255.0 {
    range 192.173.3.10 192.173.3.30;
    range 192.173.3.60 192.173.3.85;
    option routers 192.173.3.1;
    option broadcast-address 192.173.3.255;
    option domain-name-servers 192.173.2.2;
    default-lease-time 600;
    max-lease-time 6900;
}
host Eden {
    hardware ethernet 22:80:5a:6c:27:4c;
    fixed-address 192.173.3.13;
}’ > /etc/dhcp/dhcpd.conf
service isc-dhcp-server restart
```

## nomor 6
### Soal
Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 5 menit sedangkan pada client yang melalui Switch3 selama 10 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 115 menit. 

* Westalis (sudah terdapat di script sebelumnya)
```
echo ‘subnet 192.173.1.0 netmask 255.255.255.0 {
    range 192.173.1.50 192.173.1.88;
    range 192.173.1.120 192.173.1.155;
    option routers 192.173.1.1;
    option broadcast-address 192.173.1.255;
    option domain-name-servers 192.173.2.2;
    default-lease-time 300; # Switch 1
    max-lease-time 6900;
}
subnet 192.173.2.0 netmask 255.255.255.0 {
}
subnet 192.173.3.0 netmask 255.255.255.0 {
    range 192.173.3.10 192.173.3.30;
    range 192.173.3.60 192.173.3.85;
    option routers 192.173.3.1;
    option broadcast-address 192.173.3.255;
    option domain-name-servers 192.173.2.2;
    default-lease-time 600; # Switch 3
    max-lease-time 6900;
}
```
