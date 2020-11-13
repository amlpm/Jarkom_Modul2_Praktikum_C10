 # Jarkom_Modul2_Praktikum_C10

### Kelompok C_10
#### Asisten Dosen Bella Septina 
#### Adrian Danindra 05111840000068
#### Amelia Puji     05111840000147 

<br />

#### Konfigurasi pada Topologi.sh

`#` Switch<br />
uml_switch -unix switch1 > /dev/null < /dev/null &<br />
uml_switch -unix switch2 > /dev/null < /dev/null &<br />

`#` Router<br />
xterm -T SURABAYA -e linux ubd0=SURABAYA,jarkom umid=SURABAYA eth0=tuntap,,,10.151.76.45 eth1=daemon,,,switch2 eth2=daemon,,,switch1 mem=96M &<br />

`#` Server<br />
xterm -T MALANG -e linux ubd0=MALANG,jarkom umid=MALANG eth0=daemon,,,switch2 mem=128M &<br />
xterm -T MOJOKERTO -e linux ubd0=MOJOKERTO,jarkom umid=MOJOKERTO eth0=daemon,,,switch2 mem=128M &<br />
xterm -T PROBOLINGGO -e linux ubd0=PROBOLINGGO ,jarkom umid=PROBOLINGGO eth0=daemon,,,switch2 mem=128M &<br />

`#` Klien<br />
xterm -T SIDOARJO -e linux ubd0=SIDOARJO,jarkom umid=SIDOARJO eth0=daemon,,,switch1 mem=96M &<br />
xterm -T GRESIK -e linux ubd0=GRESIK,jarkom umid=GRESIK eth0=daemon,,,switch1 mem=96M &<br />

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

<br /><br />
 
**Testing pada Klien**<br />

Pada UML GRESIK atau UML SIDOARJO, ping semeruc10.pw atau host -t A semeruc10.pw. Hasilnya adalah sebagai berikut

![image](https://user-images.githubusercontent.com/57977401/99042125-2edeb900-25c7-11eb-9f2b-be483f4ecb66.png)
<br /><br /><br /><br />

#### 2. Alias http://www.semeruc10.pw 
Jawab : <br />
**Pada UML MALANG**
1. nano /etc/bind/semeruc10/semeruc10.pw
2. Tambahkan konfigurasi di bawah lalu di save
```
www     IN	    CNAME	   semeruc10.pw.
```

![image](https://user-images.githubusercontent.com/57977401/99041943-cdb6e580-25c6-11eb-8c99-cbd345d0ec3f.png)

3. Service bind9 restart<br /><br />

**Testing pada Klien**<br />
Pada UML GRESIK atau UML SIDOARJO, ping www.semeruc10.pw atau host -t CNAME www.semeruc10.pw. Hasilnya adalah sebagai berikut

![image](https://user-images.githubusercontent.com/57977401/99042351-8c730580-25c7-11eb-95cb-ce4e3dd4111c.png)
<br /><br /><br /><br />

#### 3.Subdomain http://penanjakan.semeruc10.pw yang diatur DNS-nya pada MALANG dan mengarah ke IP Server PROBOLINGGO
Jawab : <br />
**Pada UML MALANG**

1. nano /etc/bind/semeruc10/semeruc10.pw
2. Tambahkan konfigurasi di bawah lalu di save
```
penanjakan	IN	A	10.151.77.92  ; IP PROBOLINGGO
```

![image](https://user-images.githubusercontent.com/57977401/99041943-cdb6e580-25c6-11eb-8c99-cbd345d0ec3f.png)

4. Service bind9 restart<br /><br />

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

6. Service bind9 restart<br /><br />

**Testing pada Klien**<br />
Pada UML GRESIK atau UML SIDOARJO, masukkan command host -t PTR 10.151.77.92. Hasilnya adalah sebagai berikut

![image](https://user-images.githubusercontent.com/57977401/99044639-08228180-25cb-11eb-82ad-ef39ab3f4f28.png)

Karena sudah mengarah ke semeruc10.pw, berarti reverese domain kita berhasil
<br /><br /><br /><br />

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

3. Service bind9 restart<br /><br />

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
<br /><br /><br /><br />


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
4. Tambahkan konfigurasi di bawah lalu di save<br />
comment dnssec-validation auto; <br />
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
4. Tambahkan konfigurasi di bawah lalu di save<br />
comment dnssec-validation auto; <br />
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
@	    IN  NS	gunung.semeruc10.pw
@       IN  A   10.151.77.92 ; IP PROBOLINGGO
```

![image](https://user-images.githubusercontent.com/57977401/99046991-80d70d00-25ce-11eb-8db7-84a5f2a46974.png)

9. Service bind9 restart<br /><br />

**Testing pada Klien**<br />
Pada UML GRESIK atau UML SIDOARJO, ping gunung.semeruc10.pw atau host -t A gunung.semeruc10.pw. Hasilnya adalah sebagai berikut

![image](https://user-images.githubusercontent.com/57977401/99047524-49b52b80-25cf-11eb-9905-a00c89b98fd5.png)

Karena sudah mengarah ke server probolinggo, berarti Delegasi Domain berhasil
<br /><br /><br /><br />


#### 7. Subdomain dengan nama http://naik.gunung.semeruc10.pw, domain ini diarahkan ke IP Server PROBOLINGGO. 
Jawab : <br />
**Pada UML MOJOKERTO**
1. nano /etc/bind/delegasi/gunung.semeruc10.pw
2. Tambahkan konfigurasi di bawah lalu di save
```
naik	IN	A	10.151.77.92
```

![image](https://user-images.githubusercontent.com/57977401/99046991-80d70d00-25ce-11eb-8db7-84a5f2a46974.png)    

3. Service bind9 restart<br /><br />

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

![image](https://user-images.githubusercontent.com/57977401/99056203-f1812800-25d4-11eb-8a8e-0686215a728e.png)

5. a2ensite semeruc10.pw
6. cd /var/www
7. wget 10.151.36.202/semeru.pw.zip untuk mendapatkan folder semeru.pw.zip
8. unzip semeru.pw.zip
9. Rename semeru.pw menjadi semeruc10.pw dengan command mv semeru.pw semeruc10.pw

![image](https://user-images.githubusercontent.com/57977401/99056293-11b0e700-25d5-11eb-8de8-0dee7112222d.png)

10. service apache2 restart<br /><br />

**Testing**<br />
Buka semeruc10.pw, akan muncul halaman seperti dibawah

![image](https://user-images.githubusercontent.com/57977401/99056459-4a50c080-25d5-11eb-93f0-d7307d870380.png)
<br /><br /><br /><br />

#### 9. Diaktifkan mod rewrite agar urlnya menjadi http://semeruc10.pw/home.
Jawab : <br />
1. Jalankan perintah a2enmod rewrite untuk mengaktifkan module rewrite
2. nano cd/var/www/semeruc10.pw/.htaccess
3. Tambahkan konfigurasi di bawah lalu di save
```
RewriteEngine On

RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ index.php/$1 [L,QSA]
```

![image](https://user-images.githubusercontent.com/57977401/99056589-78ce9b80-25d5-11eb-8414-f70ea7bb8db4.png)

4. service apache2 restart<br /><br />

**Testing**<br />
Buka semeruc10.pw/home, akan muncul halaman seperti dibawah

![image](https://user-images.githubusercontent.com/57977401/99056660-9a2f8780-25d5-11eb-9b87-2e7f0d38ab37.png)

<br /><br /><br /><br />

#### 10. Web http://penanjakan.semeruc10.pw akan digunakan untuk menyimpan assets file yang memiliki DocumentRoot pada /var/www/penanjakan.semeruc10.pw dan memiliki struktur folder sebagai berikut:
/var/www/penanjakan.semeruc10.pw<br />
/public/javascripts<br />
/public/css<br />
/public/images<br />
/errors<br />
Jawab : <br />
1. cd /etc/apache2/sites-available
2. cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/penanjakan.semeruc10.pw.conf
3. nano /etc/apache2/sites-available/penanjakan.semeruc10.pw.conf
4. Tambahkan konfigurasi di bawah lalu di save
- DocumentRoot menjadi /var/www/penanjakan.semeruc10.pw 
```
ServerName penanjakan.semeruc10.pw
```

![image](https://user-images.githubusercontent.com/57977401/99056777-c3501800-25d5-11eb-907e-a9115559f65d.png)

5. a2ensite penanjakan.semeruc10.pw
6. service apache2 restart
7. wget 10.151.36.202/penanjakan.semeru.pw.zip untuk mendapatkan folder penanjakan.semeru.pw.zip
8. unzip penanjakan.semeru.pw.zip
9. Rename penanjakan.semeru.pw menjadi penanjakan.semeruc10.pw dengan command mv penanjakan.semeru.pw penanjakan.semeruc10.pw 

![image](https://user-images.githubusercontent.com/57977401/99056293-11b0e700-25d5-11eb-8de8-0dee7112222d.png)

10. service apache2 restart<br /><br />

**Testing**<br />
Buka penanjakan.semeruc10.pw, akan muncul halaman seperti dibawah

![image](https://user-images.githubusercontent.com/57977401/99070638-9444a100-25eb-11eb-9b8a-68ee0a0022d9.png)

<br /><br /><br /><br />

#### 11. Pada folder /public dibolehkan directory listing namun untuk folder yang berada di dalamnya tidak dibolehkan.
Jawab : <br />
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

![image](https://user-images.githubusercontent.com/57977401/99060812-9141b480-25db-11eb-8ce7-17cab1bac3d9.png)

Keterangan :<br />
- ```<Directory /x> ... </Directory>``` Untuk mengatur directory pada sebuah web<br />
- Apabila di dalam tag directory berupa ```Options +Indexes```, maka semua folder dan file di dalam directory tersebut dapat diakses<br />
- Apabila di dalam tag directory berupa ```Options -Indexes```, maka semua folder dan file di dalam directory tersebut dapat diakses<br />

3. service apache2 restart<br /><br />

**Testing**<br />
1. Buka penanjakan.semeruc10.pw/public/css

![image](https://user-images.githubusercontent.com/57977401/99061219-2c3a8e80-25dc-11eb-92f7-75711880a4d2.png)

2. Buka penanjakan.semeruc10.pw/public/images

![image](https://user-images.githubusercontent.com/57977401/99061252-3e1c3180-25dc-11eb-8c22-b59c360623fa.png)

3. Buka penanjakan.semeruc10.pw/public/javascripts

![image](https://user-images.githubusercontent.com/57977401/99061257-407e8b80-25dc-11eb-9b20-55a5ef92685f.png)

apabila ketiganya tidak dapat diakses, maka konfigurasi selesai

4. Buka penanjakan.semeruc10.pw/public/

![image](https://user-images.githubusercontent.com/57977401/99061400-71f75700-25dc-11eb-98f3-8014d235ad10.png)

Apabila folder public dapat diakses, maka maka konfigurasi selesai
<br /><br /><br /><br />

#### 12. Untuk mengatasi HTTP Error code 404, disediakan file 404.html pada folder /errors untuk mengganti error default 404 dari Apache.
Jawab : <br />
**Untuk Sites penanjakan.semeruc10.pw.**
1. nano penanjakan.semeruc10.pw.conf 
2. Tambahkan konfigurasi di bawah lalu di save
```
ErrorDocument 404 /errors/404.html
```

![image](https://user-images.githubusercontent.com/57977401/99071303-d3bfbd00-25ec-11eb-9680-96961612109d.png)

3. service apache2 restart

**Untuk Sites selain penanjakan.semeruc10.pw**
1. nano (file .conf yang ingin dibuka)
2. Tambahkan konfigurasi di bawah lalu di save
```
ErrorDocument 404 http://penanjakan.semeruc10.pw/errors/404.html
```
Gambar untuk konfigurasi Error 404 dapat dilihat dibawah : <br />
- 000-default

![image](https://user-images.githubusercontent.com/57977401/99072520-2bf7be80-25ef-11eb-9498-cf0d7367699d.png)

- semeruc10.pw.

![image](https://user-images.githubusercontent.com/57977401/99071450-1c777600-25ed-11eb-9590-f1baf3cb9b06.png)

- naik.gunung.c10.pw

![image](https://user-images.githubusercontent.com/57977401/99071355-f356e580-25ec-11eb-9716-7ebe7dd2679d.png)


3. service apache2 restart<br /><br />

**Testing**<br />
1. Buka semeruc10.pw/hehehe
2. Buka penanjakan.semeruc10.pw/aaa
3. Buka naik.gunung.semeruc10.pw/aaa

![image](https://user-images.githubusercontent.com/57977401/99061613-cb5f8600-25dc-11eb-98fc-00b44f092d5d.png)

Ketiganya menghasilkan halaman error yang sama seperti yang ditampilkan diatas, maka konfigurasi sudah terhitung benar
<br /><br /><br /><br />

#### 13. Untuk mengakses file assets javascript awalnya harus menggunakan url http://penanjakan.semeruc10.pw/public/javascripts. Karena terlalu panjang maka dibuatkan konfigurasi virtual host agar ketika mengakses file assets menjadi http://penanjakan.semeruc10.pw/js.
Jawab : <br />
1. nano penanjakan.semeruc10.pw.conf
2. Tambahkan konfigurasi di bawah lalu di save
```
Alias "/js" "/var/www/penanjakan.semeruc10.pw/public/javascripts"
```

![image](https://user-images.githubusercontent.com/57977401/99072637-606b7a80-25ef-11eb-8cf2-94c96fbe07db.png)

3. cd /var/www/penanjakan.semeruc10.pw/public/javascripts/
4. Masukkan command touch app.js
5. service apache2 restart<br /><br />

**Testing**<br />
1. nano penanjakan.semeruc10.pw.conf
2. Ganti konfigurasi speerti di bawah agar direktori public/javascripts dapat diakses
``` 
<Directory /var/www/penanjakan.semeruc10.pw/public/*>
    Options -Indexes
</Directory>
```
Menjadi 
```
<Directory /var/www/penanjakan.semeruc10.pw/public/*>
    Options +Indexes
</Directory>
```
3. Service apache2 restart
4. Ketikkan penanjakan.semeruc10.pw/js pada chrome
5. Hasilnya dapat dilihat seperti dibawah, bahwa direktori pulic/javascripts dapat dibuka menggunakan Alias /js, maka konfigurasi sudah benar

![image](https://user-images.githubusercontent.com/57977401/99062691-52612e00-25de-11eb-82a3-747831ded7d9.png)
<br /><br /><br /><br />

#### 14. Sedangkan web http://naik.gunung.semeruc10.pw sudah bisa diakses hanya dengan menggunakan port 8888. DocumentRoot web berada pada /var/www/naik.gunung.semeruc10.pw. 
Jawab : <br />
1. cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/naik.gunung.semeruc10.pw
2. nano /etc/apache2/sites-available/naik.gunung.semeruc10.pw
3. Tambahkan konfigurasi di bawah lalu di save 
- Mengganti port menjadi 8888 pada virtual host
- Mengganti DocumentRoot menjadi /var/www/naik.gunung.semeruc10.pw
```
 ServerName naik.gunung.semeruc10.pw
 ServerAlias www.naik.gunung.semeruc10.pw

 <Directory /var/www/naik.gunung.semeruc10.pw>
     Options +Indexes
 </Directory>
```

![image](https://user-images.githubusercontent.com/57977401/99062769-702e9300-25de-11eb-92ff-47664a635d5e.png)

4. Kembali ke /etc/apache2 dengan command cd ..
5. nano ports.conf
6. Tambahkan konfigurasi di bawah (dibawah Listen 80) lalu di save 
```
Listen 8888
```

![image](https://user-images.githubusercontent.com/57977401/99072762-9d377180-25ef-11eb-8a9f-965c5b5bf90b.png)

7. cd /var/www/
8. wget 10.151.36.202/naik.gunung.semeru.pw.zip  untuk mendapatkan folder naik.gunung.semeru.pw
9. unzip naik.gunung.semeru.pw.zip
10. Rename naik.gunung.semeru.pw menjadi naik.gunung.semeruc10.pw dengan command mv naik.gunung.semeru.pw naik.gunung.semeruc10.pw 

![image](https://user-images.githubusercontent.com/57977401/99063613-b33d3600-25df-11eb-8c23-782e5d58828c.png)

11. a2ensite naik.gunung.semeruc10.pw untuk mengaktifkan site naik.gunung.semeruc10.pw
12. service apache2 restart<br /><br />

**Testing**<br />
1. Buka naik.gunung.semeruc10.pw:8888
2. Apabila tampilan site sudah seperti dibawah, maka konfigurasi berhasil

![image](https://user-images.githubusercontent.com/57977401/99069151-c7396580-25e8-11eb-892c-c80e16ce83c6.png)

- Kami menambahkan directory listing pada konfigurasi naik.gunung.semeruc10.pw.conf dengan command sebagai berikut. Tujuannya karena ingin melihat directory di dalam naik.gunung.semeruc10.pw saja tanpa ada tujuan lain

![image](https://user-images.githubusercontent.com/57977401/99071853-e2f33a80-25ed-11eb-8e14-12820c39e85a.png)

<br /><br /><br /><br />

#### 15. Dikarenakan web http://naik.gunung.semeruc10.pw bersifat private, harus membuat web http://naik.gunung.semeruc10.pw agar diberi autentikasi password dengan username “semeru” dan password “kuynaikgunung”
Jawab : <br />
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

![image](https://user-images.githubusercontent.com/57977401/99068772-18952500-25e8-11eb-9a3b-f8cf80403437.png)

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

![image](https://user-images.githubusercontent.com/57977401/99072965-fc958180-25ef-11eb-97ad-82537cfba063.png)

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

![image](https://user-images.githubusercontent.com/57977401/99068984-7164bd80-25e8-11eb-88dc-9fa02c8cdca6.png)

![image](https://user-images.githubusercontent.com/57977401/99069003-7fb2d980-25e8-11eb-8bc1-99c414009e5b.png)

8. nano /var/www/naik.gunung.semeruc10.pw/.htaccess
9. Tambahkan konfigurasi di bawah lalu di save 
```
AuthType Basic
AuthName "Restricted Content"
AuthUserFile /etc/apache2/.htpasswd
Require valid-user
```

![image](https://user-images.githubusercontent.com/57977401/99069096-affa7800-25e8-11eb-93d4-4d563f9a5a96.png)

- Keterangan mengenai konfigurasi dibawah (pada /var/www/naik.gunung.semeruc10.pw/.htaccess dan /etc/apache2/sites-available/000-default.conf) :
```
AuthType Basic
AuthName "Restricted Content"
AuthUserFile /etc/apache2/.htpasswd
Require valid-user
```
- AuthType Basic merupakan pemberitahuan bahwa authentication bersifat basic <br />
- AuthName merupakan pemilihan nama realm yang akan ditampilkan kepada pengguna saat meminta kredensial<br />
- AuthUserFile merupakan path dari file password yang sudah kita buat, yaitu di /etc/apache2/.htpasswd. Ini untuk mengarahkan Apache ke file kata sandi yang kita buat<br />
- Require valid-user menunjukkan bahwa kita membutuhkan pengguna yang valid untuk mengakses sumber daya ini, yang berarti siapa pun yang dapat memverifikasi identitas mereka dengan kata sandi akan diizinkan masuk<br /><br />

**Testing**<br />
1. Buka naik.gunung.semeruc10.pw:8888
2. Apabila tampilan site sudah seperti dibawah, maka konfigurasi untuk authentifikasi berhasil

![image](https://user-images.githubusercontent.com/57977401/99069331-1e3f3a80-25e9-11eb-9be9-ba32515404cf.png)

3. Masukkan semeru sebagai username dan kuynaikgunung sebagai password
4. Apabila tampilan site sudah seperti dibawah (bisa masuk), maka authentifikasi berhasil

![image](https://user-images.githubusercontent.com/57977401/99069151-c7396580-25e8-11eb-892c-c80e16ce83c6.png)

10. service apache2 restart

<br /><br /><br /><br />

#### 16. Karena dirasa kurang profesional, maka setiap Bibah mengunjungi IP PROBOLINGGO akan dialihkan secara otomatis ke http://semeruc10.pw.
Jawab : <br />
1. nano /etc/apache2/sites-available/000-default.conf
2. Tambahkan konfigurasi di bawah lalu di save 
```
Redirect / "http://semeruc10.pw/"
```

![image](https://user-images.githubusercontent.com/57977401/99069570-91e14780-25e9-11eb-9f24-fdc6974e1667.png)

**Testing**<br />
1. Ketik IP PROBOLINGGO C10 pada chrome (10.151.77.92)
2. Apabila tampilan site sudah seperti dibawah, maka konfigurasi redirect berhasil

![image](https://user-images.githubusercontent.com/57977401/99069689-d0770200-25e9-11eb-8acc-47510df62e9d.png)

<br /><br /><br /><br />

#### 17. Karena pengunjung pada /var/www/penanjakan.semeruc10.pw/public/images sangat banyak maka semua request gambar yang memiliki substring “semeru” akan diarahkan menuju semeru.jpg.
Jawab : <br />
1. nano /var/www/penanjakan.semeruc10.pw/.htaccess
2. Tambahkan konfigurasi di bawah lalu di save 
```
RewriteCond %{REQUEST_URI} !^/public/images/semeru.jpg
RewriteCond %{REQUEST_URI} ^/public/images/(.*)semeru(.*).jpg
RewriteRule ^ http://penanjakan.semeruc10.pw/public/images/semeru.jpg
```

![image](https://user-images.githubusercontent.com/57977401/99069816-0916db80-25ea-11eb-8d67-9e99737e31fc.png)

3. service apache2 restart<br /><br />

**Testing**<br />
1. Masuk pada penanjakan.semeruc10.pw/public/images
2. Klik pada gambar yang miliki substring semeru (dalam hal ini hanya bukansemeruaja.jpg)
3. Apabila mengarah pada gambar semeru.jpg seperti di bawah, makan konfigurasi .htaccess berhasil

![image](https://user-images.githubusercontent.com/57977401/99069977-57c47580-25ea-11eb-955d-ba5f477e0d1d.png)