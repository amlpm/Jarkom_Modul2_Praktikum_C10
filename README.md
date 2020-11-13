 # Jarkom_Modul2_Praktikum_C10

### Kelompok C_10
### Asisten Dosen Bella Septina 
### Adrian Danindra 05111840000068
### Amelia Puji     05111840000147

#### Konfigurasi pada Topologi.sh

uml_switch -unix switch1 > /dev/null < /dev/null &
uml_switch -unix switch2 > /dev/null < /dev/null &

xterm -T SURABAYA -e linux ubd0=SURABAYA,jarkom umid=SURABAYA eth0=tuntap,,,10.151.76.45 eth1=daemon,,,switch2 eth2=daemon,,,switch1 mem=96M &

xterm -T MALANG -e linux ubd0=MALANG,jarkom umid=MALANG eth0=daemon,,,switch2 mem=128M &
xterm -T MOJOKERTO -e linux ubd0=MOJOKERTO,jarkom umid=MOJOKERTO eth0=daemon,,,switch2 mem=128M &
xterm -T PROBOLINGGO -e linux ubd0=PROBOLINGGO ,jarkom umid=PROBOLINGGO eth0=daemon,,,switch2 mem=128M 

xterm -T SIDOARJO -e linux ubd0=SIDOARJO,jarkom umid=SIDOARJO eth0=daemon,,,switch1 mem=96M &
xterm -T GRESIK -e linux ubd0=GRESIK,jarkom umid=GRESIK eth0=daemon,,,switch1 mem=96M &
<br /><br />


#### 1. Alamat http://semeruc10.pw yang diatur DNS-nya pada MALANG dan mengarah ke IP Server PROBOLINGGO
Jawab : <br />
**Pada UML MALANG**

1. nano /etc/bind/named.conf.local
2. Tambahkan konfigurasi di bawah lalu di save
```
zone "semeruc10.pw" {
	type master;
	file "/etc/bind/semeruc10/semeruc10.pw";
};
```

![image](https://user-images.githubusercontent.com/57977401/99041803-93e5df00-25c6-11eb-8736-f78528b2297b.png)

3. mkdir /etc/bind/semeruc10
4. cp /etc/bind/db.local /etc/bind/semeruc10/semeruc10.pw
6. nano /etc/bind/semeruc10/semeruc10.pw
7. Tambahkan konfigurasi di bawah lalu di save
- Ubah seluruh string localhost menjadi semeruc10.pw
- Tambahkan konfigurasi dibawah
```
@	    IN	NS	semeruc10.pw.
@       IN  A   10.151.77.92 ; IP PROBOLINGGO
```

![image](https://user-images.githubusercontent.com/57977401/99041943-cdb6e580-25c6-11eb-8c99-cbd345d0ec3f.png)

8. Service bind9 restart
 
**Testing pada Klien**<br />

Pada UML GRESIK atau UML SIDOARJO, ping semeruc10.pw atau host -t A semeruc10.pw. Hasilnya adalah sebagai berikut

![image](https://user-images.githubusercontent.com/57977401/99042125-2edeb900-25c7-11eb-9f2b-be483f4ecb66.png)
<br /><br /><br />

#### 2. Alias http://www.semeruc10.pw 
Jawab : <br />
**Pada UML MALANG**
1. nano /etc/bind/semeruc10/semeruc10.pw
2. Tambahkan konfigurasi di bawah lalu di save
```
www     IN	    CNAME	   semeruc10.pw.
```

![image](https://user-images.githubusercontent.com/57977401/99041943-cdb6e580-25c6-11eb-8c99-cbd345d0ec3f.png)

3. Service bind9 restart

**Testing pada Klien**<br />
Pada UML GRESIK atau UML SIDOARJO, ping www.semeruc10.pw atau host -t CNAME www.semeruc10.pw. Hasilnya adalah sebagai berikut

![image](https://user-images.githubusercontent.com/57977401/99042351-8c730580-25c7-11eb-95cb-ce4e3dd4111c.png)
<br /><br /><br />

#### 3.Subdomain http://penanjakan.semeruc10.pw yang diatur DNS-nya pada MALANG dan mengarah ke IP Server PROBOLINGGO
Jawab : <br />
**Pada UML MALANG**

1. nano /etc/bind/semeruc10/semeruc10.pw
2. Tambahkan konfigurasi di bawah lalu di save
```
penanjakan	IN	A	10.151.77.92  ; IP PROBOLINGGO
```

![image](https://user-images.githubusercontent.com/57977401/99041943-cdb6e580-25c6-11eb-8c99-cbd345d0ec3f.png)

4. Service bind9 restart

**Testing pada Klien**<br />
Pada UML GRESIK atau UML SIDOARJO, ping penanjakan.semeruc10.pw atau host -t A penanjakan.semeruc10.pw. Hasilnya adalah sebagai 

![image](https://user-images.githubusercontent.com/57977401/99048219-3ce50780-25d0-11eb-8936-f3ed8777ee58.png)

Karena sudah mengarah pada PROBOLINGGO, maka subdomain penanjakan.semeruc10.pw berhasil dibuat
<br /><br /><br />

#### 4. Reverse domain untuk domain utama. 
Jawab : <br />
**Pada UML MALANG**
1. nano /etc/bind/named.conf.local
2. Tambahkan Tambahkan konfigurasi di bawah lalu di save
```
zone "77.151.10.in-addr.arpa" {
    type master;
    file "/etc/bind/semeruc10/77.151.10.in-addr.arpa";
};
```

![image](https://user-images.githubusercontent.com/57977401/99044206-5b480480-25ca-11eb-8f5f-b35e82835096.png)

3. cp /etc/bind/db.local /etc/bind/semeruc10/77.151.10.in-addr.arpa
4. nano /etc/bind/semeruc10/77.151.10.in-addr.arpa
5. Tambahkan konfigurasi di bawah lalu di save
- Ubah seluruh string localhost menjadi semeruc10.pw
- Tambahkan konfigurasi di bawah lalu di save
```
77.151.10.in-addr.arpa.	IN	NS      semeruc10.pw.
92                      IN  PTR     semeruc10.pw.
```

![image](https://user-images.githubusercontent.com/57977401/99044439-bc6fd800-25ca-11eb-84c3-d8edfd3af817.png)

6. Service bind9 restart

**Testing pada Klien**<br />
Pada UML GRESIK atau UML SIDOARJO, masukkan command host -t PTR 10.151.77.92. Hasilnya adalah sebagai berikut

![image](https://user-images.githubusercontent.com/57977401/99044639-08228180-25cb-11eb-82ad-ef39ab3f4f28.png)

Karena sudah mengarah ke semeruc10.pw, berarti reverese domain kita berhasil
<br /><br /><br />

#### 5. DNS Server Slave pada MOJOKERTO
Jawab : <br />
**Pada UML MALANG**
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

![image](https://user-images.githubusercontent.com/57977401/99041803-93e5df00-25c6-11eb-8736-f78528b2297b.png)

3. Service bind9 restart

**Pada UML MOJOKERTO**
1. nano /etc/bind/named.conf.local
2. Tambahkan konfigurasi di bawah lalu di save
```
zone "semeruc10.pw" {
    type slave;
    masters { 10.151.77.90; }; 
    file "/var/lib/bind/semeruc10.pw";
};
```

![image](https://user-images.githubusercontent.com/57977401/99045105-ccd48280-25cb-11eb-8e2b-d99a4b7f3d26.png)

3. Service bind9 restart

**Testing pada Klien**<br />
1. Matikan service bind9 pada UML MALANG dengan command
```
service bind9 stop
```

![image](https://user-images.githubusercontent.com/57977401/99045829-e32f0e00-25cc-11eb-8245-bd67360e3f68.png)

2. Pada Klien UML SIDOARJO atau UML GRESIK, masukkan perintah nano /etc/resolv.conf, lalu tambahkan IP MOJOKERTO

![image](https://user-images.githubusercontent.com/57977401/99046133-49b42c00-25cd-11eb-92ec-c07145ca91a9.png)

3. ping semeruc10.pw atau host -t A semeruc10.pw. Apabila hasilnya seperti yang tertera dibawah, maka DNS Slave berhasil

![image](https://user-images.githubusercontent.com/57977401/99046351-96980280-25cd-11eb-8867-5947fb3fd035.png)
<br /><br /><br />


#### 6. Subdomain dengan alamat http://gunung.semeruC10.pw yang didelegasikan pada server MOJOKERTO dan mengarah ke IP Server PROBOLINGGO.
Jawab : <br />
**Pada UML MALANG**<br />
1. nano /etc/bind/semeruc10/semeruc10.pw
2. Tambahkan konfigurasi di bawah lalu di save
```
ns1		IN	A	10.151.77.91  ; IP PROBOLINGGO
gunung	IN	NS	ns1
```

![image](https://user-images.githubusercontent.com/57977401/99041803-93e5df00-25c6-11eb-8736-f78528b2297b.png)

3. nano /etc/bind/named.conf.options
4. Tambahkan konfigurasi di bawah lalu di save
comment dnssec-validation auto; 
tambahkan allow-query{any;};
```
# dnssec-validation auto; 
allow-query{any;}; 
```

![image](https://user-images.githubusercontent.com/57977401/99046720-20e06680-25ce-11eb-8c71-852c725f07bc.png)

5. Service bind9 restart

**Pada UML MOJOKERTO**
1. nano /etc/bind/named.conf.local
2. Tambahkan konfigurasi di bawah lalu di save
```
zone "gunung.semeruc10.pw" {
    type master;
    file "/etc/bind/delegasi/gunung.semeruc10.pw";
    allow-transfer { any; };
};
```

![image](https://user-images.githubusercontent.com/57977401/99041803-93e5df00-25c6-11eb-8736-f78528b2297b.png)

3. nano /etc/bind/named.conf.options
4. Tambahkan konfigurasi di bawah lalu di save
comment dnssec-validation auto; 
tambahkan allow-query{any;};
```
# dnssec-validation auto; 
allow-query{any;};
```

![image](https://user-images.githubusercontent.com/57977401/99046888-5e44f400-25ce-11eb-91d1-2af27b72986d.png)

5. mkdir /etc/bind/delegasi
6. cp /etc/bind/db.local /etc/bind/delegasi/gunung.semeruc10.pw
7. nano /etc/bind/delegasi/gunung.semeruc10.pw
8. Tambahkan konfigurasi di bawah lalu di save
- Ubah seluruh string localhost menjadi gunung.semeruc10.pw
- Tambahkan konfigurasi di bawah lalu di save
```
@	    IN	NS	gunung.semeruc10.pw
@       IN  A   10.151.77.92 ; IP PROBOLINGGO
```

![image](https://user-images.githubusercontent.com/57977401/99046991-80d70d00-25ce-11eb-8db7-84a5f2a46974.png)

9. Service bind9 restart

**Testing pada Klien**<br />
Pada UML GRESIK atau UML SIDOARJO, ping gunung.semeruc10.pw atau host -t A gunung.semeruc10.pw. Hasilnya adalah sebagai berikut

![image](https://user-images.githubusercontent.com/57977401/99047524-49b52b80-25cf-11eb-9905-a00c89b98fd5.png)

Karena sudah mengarah ke server probolinggo, berarti Delegasi Domain berhasil
<br /><br /><br />


#### 7. Subdomain dengan nama http://naik.gunung.semeruc10.pw, domain ini diarahkan ke IP Server PROBOLINGGO. 
Jawab : <br />
**Pada UML MOJOKERTO**
1. nano /etc/bind/delegasi/gunung.semeruc10.pw
2. Tambahkan konfigurasi di bawah lalu di save
```
naik	IN	A	10.151.77.92
```

![image](https://user-images.githubusercontent.com/57977401/99046991-80d70d00-25ce-11eb-8db7-84a5f2a46974.png)    

3. Service bind9 restart

**Testing pada Klien**<br />
Pada UML GRESIK atau UML SIDOARJO, ping gunung.semeruc10.pw atau host -t A gunung.semeruc10.pw. Hasilnya adalah sebagai berikut

![image](https://user-images.githubusercontent.com/57977401/99047938-e24bab80-25cf-11eb-8045-09b7660ad752.png)

Karena sudah mengarah ke server probolinggo, berarti subdomain berhasil
<br /><br /><br />

#### 8. Domain http://semeruc10.pw memiliki DocumentRoot pada /var/www/semeruc10.pw.
Jawab : <br />
1. cd /etc/apache2/sites-available
2. cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/semeruc10.pw.conf
3. nano /etc/apache2/sites-available/semeruc10.pw.conf
4. Tambahkan konfigurasi di bawah lalu di save
- Ubah DocumentRoot menjadi /var/www/semeruc10.pw
```
ServerName semeruc10.pw
ServerAlias www.semeruc10.pw
```
5. a2ensite semeruc10.pw
6. cd /var/www
7. wget 10.151.36.202/semeru.pw.zip
8. unzip semeru.pw.zip
9. mv semeru.pw semeruc10.pw
10. service apache2 restart

#### 9. Diaktifkan mod rewrite agar urlnya menjadi http://semeruc10.pw/home.
Jawab : 
1. Jalankan perintah a2enmod rewrite untuk mengaktifkan module rewrite
2. nano cd/var/www/semeruc10.pw/.htaccess
3. Tambahkan konfigurasi di bawah lalu di save
```
RewriteEngine On

RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ index.php/$1 [L,QSA]
```
4. service apache2 restart

#### 10. Web http://penanjakan.semeruc10.pw akan digunakan untuk menyimpan assets file yang memiliki DocumentRoot pada /var/www/penanjakan.semeruc10.pw dan memiliki struktur folder sebagai berikut:
/var/www/penanjakan.semeruc10.pw

/public/javascripts
/public/css
/public/images
/errors
Jawab : 
1. cd /etc/apache2/sites-available
2. cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/penanjakan.semeruc10.pw.conf
3. nano /etc/apache2/sites-available/penanjakan.semeruc10.pw.conf
4. Tambahkan konfigurasi di bawah lalu di save
- DocumentRoot menjadi /var/www/penanjakan.semeruc10.pw 
```
ServerName penanjakan.semeruc10.pw
```
5. a2ensite penanjakan.semeruc10.pw
6. service apache2 restart
7. wget 10.151.36.202/penanjakan.semeru.pw.zip
8. unzip penanjakan.semeru.pw.zip
9. mv penanjakan.semeru.pw penanjakan.semeruc10.pw
10. service apache2 restart

#### 11. Pada folder /public dibolehkan directory listing namun untuk folder yang berada di dalamnya tidak dibolehkan.
Jawab : 
1. nano /etc/apache2/sites-available/penanjakan.semeruc10.pw.conf
2. Tambahkan konfigurasi di bawah lalu di save
```
<Directory /var/www/penanjakan.semeruc10.pw/public>
    Options +Indexes
</Directory>

<Directory /var/www/penanjakan.semeruc10.pw/public/*>
    Options -Indexes
</Directory>
```
3. service apache2 restart

#### 12. Untuk mengatasi HTTP Error code 404, disediakan file 404.html pada folder /errors untuk mengganti error default 404 dari Apache.
Jawab : 
**Untuk Sites penanjakan.semeruc10.pw.**
1. nano penanjakan.semeruc10.pw.conf 
2. Tambahkan konfigurasi di bawah lalu di save
```
ErrorDocument 404 /errors/404.html
```
3. service apache2 restart

**Untuk Sites selain penanjakan.semeruc10.pw**
- 000-default
- semeruc10.pw.
- naik.gunung.c10.pw
1. nano (file .conf yang ingin dibuka)
2. Tambahkan konfigurasi di bawah lalu di save
```
ErrorDocument 404 http://penanjakan.semeruc10.pw/errors/404.html
```
3. service apache2 restart

#### 13. Untuk mengakses file assets javascript awalnya harus menggunakan url http://penanjakan.semeruc10.pw/public/javascripts. Karena terlalu panjang maka dibuatkan konfigurasi virtual host agar ketika mengakses file assets menjadi http://penanjakan.semeruc10.pw/js.
Jawab : 
1. nano (file .conf yang ingin dibuka)
2. Tambahkan konfigurasi di bawah lalu di save
```
Alias "/js" "/var/www/penanjakan.semeruc10.pw/public/javascripts"
```
3. cd /var/www/penanjakan.semeruc10.pw/public/javascripts/
4. touch app.js
5. service apache2 restart

#### 14. Sedangkan web http://naik.gunung.semeruc10.pw sudah bisa diakses hanya dengan menggunakan port 8888. DocumentRoot web berada pada /var/www/naik.gunung.semeruc10.pw. 
Jawab : 
1. cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/naik.gunung.semeruc10.pw
2. nano /etc/apache2/sites-available/naik.gunung.semeruc10.pw
3. Tambahkan konfigurasi di bawah lalu di save 
- Mengganti port menjadi 8888 pada virtual host
- DocumentRoot menjadi /var/www/naik.gunung.semeruc10.pw
```
 ServerName naik.gunung.semeruc10.pw
 ServerAlias www.naik.gunung.semeruc10.pw
 <Directory /var/www/naik.gunung.semeruc10.pw>
     Options +Indexes
 </Directory>
```
4. Kembali ke /etc/apache2 dengan command cd ..
5. nano ports.conf
6. Tambahkan konfigurasi di bawah (dibawah Listen 80) lalu di save 
```
Listen 8888
```
7. cd /var/www/
8. wget 10.151.36.202/naik.gunung.semeru.pw.zip
9. unzip naik.gunung.semeru.pw.zip
10. mv naik.gunung.semeru.pw naik.gunung.semeruc10.pw
11. a2ensite naik.gunung.semeruc10.pw
12. service apache2 restart

#### 15. Dikarenakan web http://naik.gunung.semeruc10.pw bersifat private, harus membuat web http://naik.gunung.semeruc10.pw agar diberi autentikasi password dengan username “semeru” dan password “kuynaikgunung”
Jawab : 
1. nano /etc/apache2/sites-available/000-default.conf
2. Tambahkan konfigurasi di bawah lalu di save 
```
<Directory "/var/www/naik.gunung.semeruc10.pw">
    AuthType Basic
    AuthName "Restricted Content"
    AuthUserFile /etc/apache2/.htpasswd
    Require valid-user
</Directory>
```
3. nano /etc/apache2/apache2.conf
4. Mencari directory tag dengan directory tujuan /var/www
5. Ubah konfigurasi seperti di bawah lalu di save 
- Ubah AllowOverride dari None menjadi All
```
<Directory /var/www/>
    Options Indexes FollowSymLinks
    AllowOverride All
    Require all granted
</Directory>
```
6. Membuat username dan password dengan perintah sebagai di bawah
```
htpasswd -c .htpasswd semeru
```
Keterangan : 
- htpasswd adalah command untuk membuat basic authentication HTTP user dengan menyimpan username dan password
- -c digunakan untuk membuat file spesifik, yaitu menspesifikkan bahwa hanya ada satu user yang dapat mengakses yang di declare di akhir command
- .htpacces adalah file password tersembunyi (karena memakai .)
- semeru merupakan username yang ingin ditambahkan
7. Masukkan password dan konfirmasi password
8. nano /var/www/naik.gunung.semeruc10.pw/.htaccess
9. Tambahkan konfigurasi di bawah lalu di save 
```
AuthType Basic
AuthName "Restricted Content"
AuthUserFile /etc/apache2/.htpasswd
Require valid-user
```
10. service apache2 restart

#### 16. Karena dirasa kurang profesional, maka setiap Bibah mengunjungi IP PROBOLINGGO akan dialihkan secara otomatis ke http://semeruc10.pw.
Jawab : 
1. nano /etc/apache2/sites-available/000-default.conf
2. Tambahkan konfigurasi di bawah lalu di save 
```
Redirect / "http://semeruc10.pw/"
```

#### 17. Karena pengunjung pada /var/www/penanjakan.semeruc10.pw/public/images sangat banyak maka semua request gambar yang memiliki substring “semeru” akan diarahkan menuju semeru.jpg.
Jawab : 
1. nano /var/www/penanjakan.semeruc10.pw/.htaccess
2. Tambahkan konfigurasi di bawah lalu di save 
```
RewriteCond %{REQUEST_URI} !^/public/images/semeru.jpg
RewriteCond %{REQUEST_URI} ^/public/images/(.*)semeru(.*).jpg
RewriteRule ^ http://penanjakan.semeruc10.pw/public/images/semeru.jpg
```
3. service apache2 restart