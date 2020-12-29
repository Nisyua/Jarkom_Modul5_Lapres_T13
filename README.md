# Jarkom_Modul5_Lapres_T13

* Anis Saidatur Rochma 05311840000002
* Desya Ananda Puspita Dewi 05311840000046

### Topologi
Metode yang digunakan yaitu dengan menggunakan VLSM, dengan pengelompokan seperti gambar berikut : 
  ![](/img/topo.JPG)
  
  |SUBNET |	JUMLAH IP	|NETMASK	|
  |-------|-----------|--------|
  |  A1	  |      2	   |   /30  |	
  |  A2	  |      2	   |   /30  |
  |  A3	  |      3	   |   /29  |	
  |  A4	  |      3	   |   /29  |	
  |  A5	  |     211	  |   /24	 |
  |  A6   |     201	  |   /24	 |
  |**TOTAL**|  **419**  | **/23**|
  
Didapatkan pohon VLSM sebagai berikut:
  ![](/img/vlsm.png)

  * Setting pada Putty dengan file topo.sh berisi :
  ```
  # Switch
  uml_switch -unix switch0 > /dev/null < /dev/null &
  uml_switch -unix switch1 > /dev/null < /dev/null &
  uml_switch -unix switch2 > /dev/null < /dev/null &
  uml_switch -unix switch3 > /dev/null < /dev/null &
  uml_switch -unix switch4 > /dev/null < /dev/null &
  uml_switch -unix switch5 > /dev/null < /dev/null &

  # Router
  xterm -T SURABAYA -e linux ubd0=SURABAYA,jarkom umid=SURABAYA eth0=tuntap,,,'10.151.76.76' eth1=daemon,,,switch0 eth2=daemon,,,switch1 mem=96M &
  xterm -T KEDIRI -e linux ubd0=KEDIRI,jarkom umid=KEDIRI eth0=daemon,,,switch0 eth1=daemon,,,switch4 eth2=daemon,,,switch2 mem=96M &
  xterm -T BATU -e linux ubd0=BATU,jarkom umid=BATU eth0=daemon,,,switch1 eth1=daemon,,,switch5 eth2=daemon,,,switch3 mem=96M &

  # Server
  xterm -T MALANG -e linux ubd0=MALANG,jarkom umid=MALANG eth0=daemon,,,switch3 mem=128M &
  xterm -T MOJOKERTO -e linux ubd0=MOJOKERTO,jarkom umid=MOJOKERTO eth0=daemon,,,switch3 mem=128M &
  xterm -T PROBOLINGGO -e linux ubd0=PROBOLINGGO,jarkom umid=PROBOLINGGO eth0=daemon,,,switch2 mem=128M &
  xterm -T MADIUN -e linux ubd0=MADIUN,jarkom umid=MADIUN eth0=daemon,,,switch2 mem=128M &

  # Klien
  xterm -T SIDOARJO -e linux ubd0=SIDOARJO,jarkom umid=SIDOARJO eth0=daemon,,,switch5 mem=96M &
  xterm -T GRESIK -e linux ubd0=GRESIK,jarkom umid=GRESIK eth0=daemon,,,switch4 mem=96M &
  ```
  * Jalankan perintah `bash topo.sh` untuk menjalankan UML

### Routing
  * Kemudian settting interfaces tiap uml seperti berikut :
  ```
  SURABAYA (Sebagai Router)
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 10.151.76.78
netmask 255.255.255.252
gateway 10.151.76.77

auto eth1
iface eth1 inet static
address 192.168.0.1
netmask 255.255.255.252
post-up route add -net 192.168.1.0 netmask 255.255.255.0 gw 192.168.0.2
post-down route del -net 192.168.1.0 netmask 255.255.255.0 gw 192.168.0.2
post-up route add -net 192.168.0.16 netmask 255.255.255.248 gw 192.168.0.2
post-down route del -net 192.168.0.16 netmask 255.255.255.248 gw 192.168.0.2

auto eth2
iface eth2 inet static
address 192.168.0.5
netmask 255.255.255.252
post-up route add -net 192.168.2.0 netmask 255.255.255.0 gw 192.168.0.6
post-down route del -net 192.168.2.0 netmask 255.255.255.0 gw 192.168.0.6
post-up route add -net 10.151.77.152 netmask 255.255.255.248 gw 192.168.0.6
post-down route del -net 10.151.77.152 netmask 255.255.255.248 gw 192.168.0.6

KEDIRI (Sebagai Router)
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.0.2
netmask 255.255.255.252
gateway 192.168.0.1
post-up route add -net 0.0.0.0 netmask 0.0.0.0 gw 192.168.0.1
post-down route del -net 0.0.0.0 netmask 0.0.0.0 gw 192.168.0.1

auto eth1
iface eth1 inet static
address 192.168.1.1
netmask 255.255.255.0

auto eth2
iface eth2 inet static
address 192.168.0.9
netmask 255.255.255.248

BATU (Sebagai Router)
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.0.6
netmask 255.255.255.252
gateway 192.168.0.5
post-up route add -net 0.0.0.0 netmask 0.0.0.0 gw 192.168.0.5
post-down route del -net 0.0.0.0 netmask 0.0.0.0 gw 192.168.0.5

auto eth1
iface eth1 inet static
address 192.168.2.1
netmask 255.255.255.0

auto eth2
iface eth2 inet static
address 10.151.77.153
netmask 255.255.255.248

MALANG (Sebagai DNS Server)
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 10.151.77.154
netmask 255.255.255.248
gateway 10.151.77.153

MOJOKERTO (Sebagai DHCP Server)
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 10.151.77.156
netmask 255.255.255.248
gateway 10.151.77.153

SIDOARJO (Sebagai Klien)
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.2.2
netmask 255.255.255.0
gateway 192.168.2.1

GRESIK (Sebagai Klien)
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.1.2
netmask 255.255.255.0
gateway 192.168.1.1

PROBOLINGGO (Sebagai Web Server)
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.0.11
netmask 255.255.255.248
gateway 192.168.0.9

MADIUN (Sebagai Web Server)
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.0.10
netmask 255.255.255.248
gateway 192.168.0.9
  ```
  
### DHCP Server

### DHCP Relay

### No. 1 
Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi SURABAYA menggunakan iptables, namun Bibah tidak ingin kalian menggunakan MASQUERADE.
  * Pada UML Surabaya tambahkan perintah :
  ```
  iptables -t nat -A POSTROUTING -o eth0 -j SNAT --to-source 10.151.76.78 -s 192.168.0.0
  ```
  * Testing :

### No.2 
Kalian diminta untuk mendrop semua akses SSH dari luar Topologi (UML) Kalian pada server yang memiliki ip DMZ (DHCP dan DNS SERVER) pada SURABAYA demi menjaga keamanan.
  * Pada UML Surabaya tambahkan perintah :
  ```
  iptables -A FORWARD -p tcp --dport 22 -d 10.151.77.152/29 -i eth0 -j DROP
  ```
  * Testing :

### No. 3
Karena tim kalian maksimal terdiri dari 3 orang, Bibah meminta kalian untuk membatasi DHCP dan DNS server hanya boleh menerima maksimal 3 koneksi ICMP secara bersamaan yang berasal dari mana saja menggunakan iptables pada masing masing server, selebihnya akan di DROP.
  * Pada UML Mojokerto dan UML Malang tambahkan perintah :
  ```
  iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
  ```
  * Testing : 
  

### No. 4
Akses dari subnet SIDOARJO hanya diperbolehkan pada pukul 07.00 - 17.00 pada hari Senin sampai Jumat.
  * Pada UML Malang ditambahkan : 
  ```
  iptables -A INPUT -s 192.168.2.0/24 -m time --timestart 07:00 --timestop 17:00 --weekdays Mon,Tue,Wed,Thu,Fri -j ACCEPT
  iptables -A INPUT -s 192.168.2.0/24 -j REJECT
  ```
  * Testing :
  
### No. 5
Akses dari subnet GRESIK hanya diperbolehkan pada pukul 17.00 hingga pukul 07.00 setiap harinya.
  * Pada UML Malang ditambahkan :
  ```
  iptables -A INPUT -s 192.168.1.0/24 -m time --timestart 07:00 --timestop 17:00
  iptables -A INPUT -s 192.168.1.0/24 -m time --timestart 07:00 --timestop 17:01
  iptables -A INPUT -s 192.168.1.0/24 -m time --timestart 17:01 --timestop 06:59
  ```
  * Testing :
