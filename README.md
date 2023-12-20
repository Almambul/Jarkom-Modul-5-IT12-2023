# Jarkom-Modul-5-IT12-2023
## Anggota Kelompok
| Nama                   | NRP        |
| ---------------------- | ---------- |
| Raditya Pratama        | 5027211047 |
| Salmaa Satifha Dewi     | 5027211011 |

## Topologi
![image](https://github.com/Almambul/Jarkom-Modul-5-IT12-2023/assets/107543354/b408c850-9ec6-4209-8b8e-162a1a152cfd)

## Pembagian Subnet
![image](https://github.com/Almambul/Jarkom-Modul-5-IT12-2023/assets/107543354/148b435e-e5e5-427c-87ee-3632934b0042)

## Tree VLSM
![WhatsApp Image 2023-12-19 at 21 28 02](https://github.com/Almambul/Jarkom-Modul-5-IT12-2023/assets/107543354/b76d4fca-ed35-4435-846d-4f0c2c31f4ff)

## Soal
###   Nomor 1
Dilakukan konfigurasi iptables pada Aura untuk bisa mengakses keluar tanpa menggunakan MASQUERADE, adapun konfigurasi yang dimasukkan adalah 
```
iptables -t nat -A POSTROUTING -s 192.239.0.0/20 -o eth0 -j SNAT --to-source 192.168.122.1
```
Dilakukan testing ping google.com pada Sein dengan command
```
ping google.com
```

###   Nomor 2
Untuk melakukan drop pada TCP dan UDP kecuali port 8080 (TCP) maka perlu melakukan konfigurasi iptables pada SchewerMountain dengan command
```
iptables -F
iptables -A INPUT -p tcp --dport 8080 -j ACCEPT
iptables -A INPUT -p tcp ! --dport 8080 -j DROP
iptables -A INPUT -p udp -j DROP
```

###   Nomor 3
Untuk membatasi DHCP dan DNS Server yang dapat melakukan ping sebanyak 3 device secara bersamaan, maka perlu dilakukan konfigurasi iptables pada revolte dan richter dengan command
```
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```
Kemudian dilanjutkan dengan testing pada revolte dan richter menggunakan ip masing masing

###   Nomor 4
Untuk membatasi koneksi SSH di web server yang dilakukan pada GrobeForest, maka perlu dilakukan konfigurasi iptables pada sein dan stark dengan command
```
iptables -A INPUT -p tcp --dport 22 -s 192.239.0.0 -j ACCEPT
iptables -A INPUT -p tcp --dport 22 -j DROP
```
Kemudian dilakukan testing pada sein dan stark

###   Nomor 5
Untuk membatasi akses menuju web server yang hanya diperbolehkan akses saat jam kerja maka perlu dilakukan konfigurasi iptables pada sein dan stark dengan command
```
iptables -A INPUT -p tcp --dport 80 -m time --timestart 08:00 --timestop 16:00 --weekdays Mon,Tue,Wed,Thu,Fri -j ACCEPT

iptables -A INPUT -p tcp --dport 80 -j DROP
```
Selanjutnya dilakukan testing pada sein dan stark berupa ```nc -l -p 80``` dan aura berupa ```curl 192.239.0.0 -v```

###   Nomor 6
Menambahkan konfigurasi pada sein dan stark agar dihari senin-kamis pukul 12.00 - 13.00 serta jumat pada pukul 11.00 - 13.00 tidak dapat diakses berupa command
```
iptables -A INPUT -p tcp --dport 80 -m time --timestart 12:00 --timestop 13:00 --weekdays Mon,Tue,Wed,Thu -j DROP

iptables -A INPUT -p tcp --dport 80 -m time --timestart 11:00 --timestop 13:00 --weekdays Fri -j DROP
```

###   Nomor 7
```
# sein
iptables -A PREROUTING -t nat -p tcp -d 192.239.0.0 --dport 80 -m statistics --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.239.0.0:80

iptables -A PREROUTING -t nat -p tcp -d 192.239.0.0 --dport 80 -j DNAT --to-destination 192.239.0.0:80

#stark
iptables -A PREROUTING -t nat -p tcp -d 192.239.0.0 --dport 443 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.239.0.0:443

iptables -A PREROUTING -t nat tcp -d 192.239.0.0 --dport 443 -j DNAT --to-destination 192.239.0.0:443
```

###   Nomor 8
```
iptables -A INPUT -p tcp -s 192.239.0.0 --dport 80 -m time --datestart 2023-10-19T00:00 --datestop 2024-02-15T00:00 -j DROP

iptables -A INPUT -s 192.239.0.0 -m time --datestart 2023-10-19T00:00 --datestop 2024-02-15T00:00 -j DROP
```

###   Nomor 9
```
#sein
iptables -A INPUT -p tcp --syn -m recent --name portscan --set
iptables -A INPUT -p tcp --syn -m recent --name portscan --rcheck --seconds 600 --hitcount  20  -j  DROP

#stark
iptables -N PORTSCAN
iptables -A PORTSCAN -m recent --set --name portscan
iptables -A PORTSCAN -m recent --update --seconds 600 --hitcount 20 --name portscan -j LOG --log-prefix "Portscan Detected: " --log-level 4
iptables -A PORTSCAN -m recent --update --seconds 600 --hitcount 20 --name portscan -j DROP
iptables -A INPUT -p tcp --tcp-flags SYN,ACK,FIN,RST RST -m limit --limit 2/s -j ACCEPT
iptables -A INPUT -p tcp --tcp-flags SYN,ACK,FIN,RST RST -j PORTSCAN
```

###   Nomor 10
Menambahkan logging pada seluruh server dan router berupa
```
iptables -A INPUT -j LOG --log-level debug --log-prefix "Dropped Packet: " -m limit --limit 1/second --limit-burst 10
```
