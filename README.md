# Jarkom_Modul2_Praktikum_C10

## Kelompok C_10
## Asisten Dosen Bella Septina 
## Adrian Danindra 05111840000068
## Amelia Puji     05111840000147

### Konfigurasi pada Topologi.sh
\# Switch

uml_switch -unix switch1 > /dev/null < /dev/null &
uml_switch -unix switch2 > /dev/null < /dev/null &

\# Router

xterm -T SURABAYA -e linux ubd0=SURABAYA,jarkom umid=SURABAYA eth0=tuntap,,,10.151.76.45 eth1=daemon,,,switch2 eth2=daemon,,,switch1 mem=96M &

\# Server

xterm -T MALANG -e linux ubd0=MALANG,jarkom umid=MALANG eth0=daemon,,,switch2 mem=128M &
xterm -T MOJOKERTO -e linux ubd0=MOJOKERTO,jarkom umid=MOJOKERTO eth0=daemon,,,switch2 mem=128M &
xterm -T PROBOLINGGO -e linux ubd0=PROBOLINGGO ,jarkom umid=PROBOLINGGO eth0=daemon,,,switch2 mem=128M 

\# Klien

xterm -T SIDOARJO -e linux ubd0=SIDOARJO,jarkom umid=SIDOARJO eth0=daemon,,,switch1 mem=96M &
xterm -T GRESIK -e linux ubd0=GRESIK,jarkom umid=GRESIK eth0=daemon,,,switch1 mem=96M &


### 1. Alamat http://semeruc10.pw yang diatur DNS-nya pada MALANG dan mengarah ke IP Server PROBOLINGGO
Jawab : 
#### Pada UML MALANG
1. nano /etc/bind/named.conf.local
2. Tambahkan 
```
zone "semeruc10.pw" {
	type master;
	file "/etc/bind/semeruc10/semeruc10.pw";
};
```
3. mkdir /etc/bind/semeruc10
4. cp /etc/bind/db.local /etc/bind/semeruc10/semeruc10.pw
6. nano /etc/bind/semeruc10/semeruc10.pw
7. Tambahkan konfigurasi di bawah lalu di save
- Ubah seluruh string localhost menjadi semeruc10.pw
```
@	IN	NS	semeruc10.pw.
@   IN  A   10.151.77.92 ; IP PROBOLINGGO
```
8. Service bind9 restart

### 2. Alias http://www.semeruc10.pw 
Jawab : 
#### Pada UML MALANG
1. nano /etc/bind/semeruc10/semeruc10.pw
2. Tambahkan konfigurasi di bawah lalu di save
```
www		IN	CNAME	semeruc10.pw.
```
3. Service bind9 restart

### 3.subdomain http://penanjakan.semeruc10.pw yang diatur DNS-nya pada MALANG dan mengarah ke IP Server PROBOLINGGO
Jawab : 
#### Pada UML MALANG
1. nano /etc/bind/semeruc10/semeruc10.pw
2. Tambahkan konfigurasi di bawah lalu di save
```
penanjakan	IN	A	10.151.77.92  ; IP PROBOLINGGO
```
4. Service bind9 restart

### 4. Reverse domain untuk domain utama. 
Jawab : 
#### Pada UML MALANG
1. nano /etc/bind/named.conf.local
2. Tambahkan 
```
zone "77.151.10.in-addr.arpa" {
    type master;
    file "/etc/bind/semeruc10/77.151.10.in-addr.arpa";
};
```
3. cp /etc/bind/db.local /etc/bind/semeruc10/77.151.10.in-addr.arpa
4. nano /etc/bind/semeruc10/77.151.10.in-addr.arpa
5. Tambahkan konfigurasi di bawah lalu di save
- Ubah seluruh string localhost menjadi semeruc10.pw
```
77.151.10.in-addr.arpa.	IN	NS      semeruc10.pw.
92                      IN  PTR     semeruc10.pw.
```
6. Service bind9 restart

### 5. DNS Server Slave pada MOJOKERTO
Jawab : 
#### Pada UML MALANG
1. nano /etc/bind/named.conf.local
2. Tambahkan konfigurasi di bawah lalu di save
```
zone "semeruc10.pw" {
	type master;
    notify yes;
	also-notify { 10.151.77.91; };
	allow-transfer { 10.151.77.91; };
	file "/etc/bind/semeruc10/semeruc10.pw";
};
```
3. Service bind9 restart
#### Pada UML MOJOKERTO
1. nano /etc/bind/named.conf.local
2. Tambahkan konfigurasi di bawah lalu di save
```
zone "semeruc10.pw" {
    type slave;
    masters { 10.151.77.90; }; 
    file "/var/lib/bind/semeruc10.pw";
};
```
3. cp /etc/bind/db.local /etc/bind/semeruc10/semeruc10.pw
4. nano /etc/bind/semeruc10/semeruc10.pw
5. Tambahkan konfigurasi di bawah lalu di save
- Ubah seluruh string localhost menjadi semeruc10.pw
```
@	IN	NS	semeruc10.pw.
@   IN  A   10.151.77.92 ; IP PROBOLINGGO
```
6. Service bind9 restart

### 6. Subdomain dengan alamat http://gunung.semeruC10.pw yang didelegasikan pada server MOJOKERTO dan mengarah ke IP Server PROBOLINGGO.
Jawab : 
#### Pada UML MALANG
4. nano /etc/bind/semeruc10/semeruc10.pw
5. Tambahkan konfigurasi di bawah lalu di save
```
ns1		    IN	A	10.151.77.91  ; IP PROBOLINGGO
gunung		IN	NS	ns1
```
6. nano /etc/bind/named.conf.options
7. Tambahkan konfigurasi di bawah lalu di save
comment dnssec-validation auto; 
tambahkan allow-query{any;};
```
# dnssec-validation auto; 
allow-query{any;};
```
6. Service bind9 restart
#### Pada UML MOJOKERTO
1. nano /etc/bind/named.conf.local
2. Tambahkan konfigurasi di bawah lalu di save
```
zone "gunung.semeruc10.pw" {
    type master;
    file "/etc/bind/delegasi/gunung.semeruc10.pw";
    allow-transfer { any; };
};
```
3. nano /etc/bind/named.conf.options
4. Tambahkan konfigurasi di bawah lalu di save
comment dnssec-validation auto; 
tambahkan allow-query{any;};
```
# dnssec-validation auto; 
allow-query{any;};
```
5. mkdir /etc/bind/delegasi
6. cp /etc/bind/db.local /etc/bind/delegasi/gunung.semeruc10.pw
7. nano /etc/bind/delegasi/gunung.semeruc10.pw
8. Tambahkan konfigurasi di bawah lalu di save
- Ubah seluruh string localhost menjadi gunung.semeruc10.pw
```
@	    IN	NS	gunung.semeruc10.pw
@       IN  A   10.151.77.92 ; IP PROBOLINGGO
```
9. Service bind9 restart

### 7. Subdomain dengan nama http://naik.gunung.semeruc10.pw, domain ini diarahkan ke IP Server PROBOLINGGO. 
Jawab : 
1. nano /etc/bind/delegasi/gunung.semeruc10.pw
2. Tambahkan konfigurasi di bawah lalu di save
```
naik	IN	A	10.151.77.92
```
3. Service bind9 restart

### 8. Domain http://semeruc10.pw memiliki DocumentRoot pada /var/www/semeruc10.pw.
Jawab : 

### 9. Diaktifkan mod rewrite agar urlnya menjadi http://semeruc10.pw/home.
Jawab : 

### 10. Web http://penanjakan.semeruc10.pw akan digunakan untuk menyimpan assets file yang memiliki DocumentRoot pada /var/www/penanjakan.semeruc10.pw dan memiliki struktur folder sebagai berikut:
/var/www/penanjakan.semeruc10.pw

/public/javascripts
/public/css
/public/images
/errors
Jawab : 

### 11. Pada folder /public dibolehkan directory listing namun untuk folder yang berada di dalamnya tidak dibolehkan.
Jawab : 

### 12. Untuk mengatasi HTTP Error code 404, disediakan file 404.html pada folder /errors untuk mengganti error default 404 dari Apache.
Jawab : 

### 13. Untuk mengakses file assets javascript awalnya harus menggunakan url http://penanjakan.semeruc10.pw/public/javascripts. Karena terlalu panjang maka dibuatkan konfigurasi virtual host agar ketika mengakses file assets menjadi http://penanjakan.semeruc10.pw/js.
Jawab : 

### 14. Sedangkan web http://naik.gunung.semeruc10.pw sudah bisa diakses hanya dengan menggunakan port 8888. DocumentRoot web berada pada /var/www/naik.gunung.semeruc10.pw. Dikarenakan web http://naik.gunung.semeruc10.pw bersifat private
Jawab : 

### 15. Membuat web http://naik.gunung.semeruc10.pw agar diberi autentikasi password dengan username “semeru” dan password “kuynaikgunung”
Jawab : 

### 16. Karena dirasa kurang profesional, maka setiap Bibah mengunjungi IP PROBOLINGGO akan dialihkan secara otomatis ke http://semeruc10.pw.
Jawab : 

### 17. Karena pengunjung pada /var/www/penanjakan.semeruc10.pw/public/images sangat banyak maka semua request gambar yang memiliki substring “semeru” akan diarahkan menuju semeru.jpg.
Jawab : 