# Jarkom-Modul-3-IT01-2023

# Topologi
![topo3](https://github.com/Koro129/Jarkom-Modul-3-IT01-2023/assets/102176304/6a5aab15-355e-49d2-969d-33fff0a39719)

**Network Configuration :** 

**Aura - (DHCP Relay)**
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.64.1.212
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.64.2.212
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 10.64.3.212
	netmask 255.255.255.0

auto eth4
iface eth4 inet static
	address 10.64.4.212
	netmask 255.255.255.0
```

**Himmel - (DHCP Server)**
```
auto eth0
iface eth0 inet static
	address 10.64.1.1
	netmask 255.255.255.0
	gateway 10.64.1.212
```

**Heiter - (DNS Server)**
```
auto eth0
iface eth0 inet static
	address 10.64.1.2
	netmask 255.255.255.0
	gateway 10.64.1.212
```

**Denken - (Database Server)**
```
auto eth0
iface eth0 inet static
	address 10.64.2.1
	netmask 255.255.255.0
	gateway 10.64.2.212
```

**Eisen - (Load Balancer)**
```
auto eth0
iface eth0 inet static
	address 10.64.2.2
	netmask 255.255.255.0
	gateway 10.64.2.212
```

**Frieren - (Laravel Worker)**
```
auto eth0
iface eth0 inet static
	address 10.64.4.1
	netmask 255.255.255.0
	gateway 10.64.4.212
```

**Flamme - (Laravel Worker)**
```
auto eth0
iface eth0 inet static
	address 10.64.4.2
	netmask 255.255.255.0
	gateway 10.64.4.212
```

**Fern - (Laravel Worker)**
```
auto eth0
iface eth0 inet static
	address 10.64.4.3
	netmask 255.255.255.0
	gateway 10.64.4.212
```

**Lawine - (PHP Worker)**
```
auto eth0
iface eth0 inet static
	address 10.64.3.3
	netmask 255.255.255.0
	gateway 10.64.3.212
```

**Linie - (PHP Worker)**
```
auto eth0
iface eth0 inet static
	address 10.64.3.2
	netmask 255.255.255.0
	gateway 10.64.3.212
```

**Lugner - (PHP Worker)**
```
auto eth0
iface eth0 inet static
	address 10.64.3.1
	netmask 255.255.255.0
	gateway 10.64.3.212
```

**Revolte, Richter, Sein, dan Stark - (Client)**
```
auto eth0
iface eth0 inet dhcp
```

## Soal 0
```
kalian diminta untuk melakukan register domain berupa riegel.canyon.yyy.com untuk worker Laravel dan granz.channel.yyy.com untuk worker PHP (0) mengarah pada worker yang memiliki IP [prefix IP].x.1.   
Worker php dengan akhiran 1 adalah Lugner, dan worker laravel adalah frieren
```    
jalankan skrip berikut di DNS Server
```
echo 'nameserver 192.168.122.1' > /etc/resolv.conf

apt-get update
apt-get install bind9 -y

echo 'zone "riegel.canyon.it01.com" {
        type master;
        file "/etc/bind/jarkom/riegel.canyon.it01.com";
};' > /etc/bind/named.conf.local

echo 'zone "granz.channel.it01.com" {
        type master;
        file "/etc/bind/jarkom/granz.channel.it01.com";
};' >> /etc/bind/named.conf.local

mkdir /etc/bind/jarkom

echo '
;
;BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     riegel.canyon.it01.com. root.riegel.canyon.it01.com. (
                2               ; Serial
                604800          ; Refresh
                86400           ; Retry
                2419200         ; Expire
                604800 )        ; Negative Cache TTL
;
@       IN      NS      riegel.canyon.it01.com.
@       IN      A       10.64.4.1	; IP Frieren
@ 	IN 	AAAA 	::1
' > /etc/bind/jarkom/riegel.canyon.it01.com

echo '
;
;BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     granz.channel.it01.com. root.granz.channel.it01.com. (
                2               ; Serial
                604800          ; Refresh
                86400           ; Retry
                2419200         ; Expire
                604800 )        ; Negative Cache TTL
;
@       IN      NS      granz.channel.it01.com.
@       IN      A       10.64.3.1	; IP Lugner
@ 	IN 	AAAA 	::1
' > /etc/bind/jarkom/granz.channel.it01.com

service bind9 restart
```
Ini akan mengarahkan dns riegel.canyon.it01.com ke IP Frieren, dan granz.channel.it01.com ke IP Lugner
## Soal 1
```Lakukan konfigurasi sesuai dengan peta yang sudah diberikan.```
konfigurasi dilakukan sesuai dengan topologi dan konfigurasi jaringan diatas.   
Karena ada dhcp relay maka jalankan skrip ini pada dhcp relay
```
apt-get install isc-dhcp-relay -y
service isc-dhcp-relay start

echo 'SERVERS="10.64.1.1"  
INTERFACES="eth1 eth3 eth4"
' > /etc/default/isc-dhcp-relay

echo 'net.ipv4.ip_forward=1' > /etc/sysctl.conf

service isc-dhcp-relay restart
```
Ini membuat dhcp relay akan berjalan pada eth1 eth3 eth4
## Soal 2
```
Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.16 - [prefix IP].3.32 dan [prefix IP].3.64 - [prefix IP].3.80 (2)
```
Gunakan konfigurasi berikut pada dhcp server
```
# eth3
subnet 10.64.3.0 netmask 255.255.255.0 {
    range 10.64.3.16 10.64.3.32;
    range 10.64.3.64 10.64.3.80;
    option routers 10.64.3.212;
    option broadcast-address 10.64.3.255;
}
```
Ini akan membuat node dhcp yang ada di eth3 dari dhcp relay (switch 3) mempunyai range ip sesuai soal   
![image](https://github.com/Koro129/Jarkom-Modul-3-IT01-2023/assets/113784446/b1ae0a3b-2f6c-473b-aad5-ee871fe9d4aa)
## Soal 3
```
Client yang melalui Switch4 mendapatkan range IP dari [prefix IP].4.12 - [prefix IP].4.20 dan [prefix IP].4.160 - [prefix IP].4.168
```
Gunakan konfigurasi berikut pada dhcp server
```
# eth4
subnet 10.64.4.0 netmask 255.255.255.0 {
    range 10.64.4.12 10.64.4.20;
    range 10.64.4.160 10.64.4.168;
    option routers 10.64.4.212;
    option broadcast-address 10.64.4.255;
}
```
Ini akan membuat node dhcp yang ada di eth4 dari dhcp relay (switch 4) mempunyai range ip sesuai soal  
![image](https://github.com/Koro129/Jarkom-Modul-3-IT01-2023/assets/113784446/8c6fc083-11a8-4275-a1f1-056b271f536f)
## Soal 4
```
Client mendapatkan DNS dari Heiter dan dapat terhubung dengan internet melalui DNS tersebut
```
Tambahkan option berikut di konfig eth3 dan 4
```
option domain-name-servers 10.64.1.2;
```
Ini membuat node dhcp akan mendapatkan dns dari dns server   
![image](https://github.com/Koro129/Jarkom-Modul-3-IT01-2023/assets/113784446/b1ae0a3b-2f6c-473b-aad5-ee871fe9d4aa)
![image](https://github.com/Koro129/Jarkom-Modul-3-IT01-2023/assets/113784446/8c6fc083-11a8-4275-a1f1-056b271f536f)
## Soal 5
```
Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch3 selama 3 menit sedangkan pada client yang melalui Switch4 selama 12 menit. Dengan waktu maksimal dialokasikan untuk peminjaman alamat IP selama 96 menit
```
Tambahkan option berikut untuk konfig eth3
```
default-lease-time 180;
max-lease-time 5760;
```
dan berikut untuk eth4
```
default-lease-time 720;
max-lease-time 5760;
```
## Soal 6
```
Pada masing-masing worker PHP, lakukan konfigurasi virtual host untuk website berikut dengan menggunakan php 7.3.
```
Jalankan skrip berikut pada php worker
```
echo 'nameserver 192.168.122.1' > /etc/resolv.conf

apt-get update && apt install nginx php php-fpm -y
apt-get install htop -y
apt-get install unzip -y
apt-get install libapache2-mod-php7.3 -y

url="https://drive.google.com/uc?export=download&id=1ViSkRq7SmwZgdK64eRbr5Fm1EGCTPrU1"

curl -kLo "granz.channel.it01.com.zip" "$url" && unzip -q "granz.channel.it01.com.zip" && rm "granz.channel.it01.com.zip"

mv modul-3 granz.channel.it01.com

rm -r /var/www/granz.channel.it01.com

mv granz.channel.it01.com /var/www/

echo '	 server {

 	listen 80;

 	root /var/www/granz.channel.it01.com;

 	index index.php index.html index.htm;
 	server_name _;

 	location / {
 			try_files $uri $uri/ /index.php?$query_string;
 	}

 	# pass PHP scripts to FastCGI server
 	location ~ \.php$ {
 	include snippets/fastcgi-php.conf;
 	fastcgi_pass unix:/var/run/php/php7.3-fpm.sock;
 	}

 location ~ /\.ht {
 			deny all;
 	}

 	error_log /var/log/nginx/jarkom_error.log;
 	access_log /var/log/nginx/jarkom_access.log;
 }' > /etc/nginx/sites-available/granz.channel.it01

 ln -s /etc/nginx/sites-available/granz.channel.it01 /etc/nginx/sites-enabled

 rm -rf /etc/nginx/sites-enabled/default 
 service nginx restart
 service php7.3-fpm start
```
Ini akan mendownoad resouce dari link tertera, dan deploy dengan menggunakan nginx.   
Sehingga ketika curl ke ip tiap worker akan menampilkan websitenya
![image](https://github.com/Koro129/Jarkom-Modul-3-IT01-2023/assets/113784446/28c8e8fc-b185-435e-9bc8-79ab55bab5b2)
![image](https://github.com/Koro129/Jarkom-Modul-3-IT01-2023/assets/113784446/1ef666dc-b088-4e97-b4b9-a4b8a4d86422)

## Soal 7
```
Kepala suku dari Bredt Region memberikan resource server sebagai berikut:
Lawine, 4GB, 2vCPU, dan 80 GB SSD.
Linie, 2GB, 2vCPU, dan 50 GB SSD.
Lugner 1GB, 1vCPU, dan 25 GB SSD.
aturlah agar Eisen dapat bekerja dengan maksimal, lalu lakukan testing dengan 1000 request dan 100 request/second.
```
Karena diberikan resource yang berbeda, maka pada eisen digunakan algoritma weight round robin.   
Gunakan konfigurasi berikut pada load balancer
```
upstream wroundrobin {
    server 10.64.3.1 weight=1; #IP Lugner
    server 10.64.3.2 weight=2; #IP Linie
    server 10.64.3.3 weight=4; #IP Lawine
}
server {
    listen 82;
    server_name _;

    location / {
        proxy_pass http://wroundrobin;
    }

    location ~ /\.ht {
	deny all;
    }
}
```
Ini akan membuat port 82 pada digunakan untuk load balancer ke 3 worker terebut, dengan weight sesuai soal.
![image](https://github.com/Koro129/Jarkom-Modul-3-IT01-2023/assets/113784446/a4dcd7c1-ae99-45cd-ab5e-217eaa35d25a)
![image](https://github.com/Koro129/Jarkom-Modul-3-IT01-2023/assets/113784446/e9267a6c-e02a-44ac-9a86-18230f731982)
## Soal 8
```
Karena diminta untuk menuliskan grimoire, buatlah analisis hasil testing dengan 200 request dan 10 request/second masing-masing algoritma Load Balancer dengan ketentuan sebagai berikut:
Nama Algoritma Load Balancer
Report hasil testing pada Apache Benchmark
Grafik request per second untuk masing masing algoritma. 
Analisis 
```
Berikut konfigurasi untuk menggunakan algoritma Roundrobin, weight round robin, least conn, ip hash, dan generic hash
```
upstream roundrobin {
    server 10.64.3.1; #IP Lugner
    server 10.64.3.2; #IP Linie
    server 10.64.3.3; #IP Lawine
}
upstream wroundrobin {
    server 10.64.3.1 weight=1; #IP Lugner
    server 10.64.3.2 weight=2; #IP Linie
    server 10.64.3.3 weight=4; #IP Lawine
}

upstream leastconn {
    least_conn;
    server 10.64.3.1; #IP Lugner
    server 10.64.3.2; #IP Linie
    server 10.64.3.3; #IP Lawine
}

upstream ip_hash {
    ip_hash;
    server 10.64.3.1; #IP Lugner
    server 10.64.3.2; #IP Linie
    server 10.64.3.3; #IP Lawine
}

upstream generic_hash {
    hash $request_uri consistent;
    server 10.64.3.1; #IP Lugner
    server 10.64.3.2; #IP Linie
    server 10.64.3.3; #IP Lawine
}

server {
    listen 81;
    server_name _;

    location / {
        proxy_pass http://roundrobin;
    }
}

server {
    listen 82;
    server_name _;

    location / {
        proxy_pass http://wroundrobin;
    }

    location ~ /\.ht {
	deny all;
    }
}

server {
    listen 83;
    server_name _;

    location / {
        proxy_pass http://leastconn;
    }
}

server {
    listen 84;
    server_name _;

    location / {
        proxy_pass http://ip_hash;
    }
}

server {
    listen 85;
    server_name _;

    location / {
        proxy_pass http://generic_hash;
    }
}
```
Untuk analisis ada pada file grimoire yang tertera. ini merupakan contoh pengujiannya pada roundrobin
![image](https://github.com/Koro129/Jarkom-Modul-3-IT01-2023/assets/113784446/01b865ca-34e5-4fd3-89a9-80cd4db21965)
## Soal 9
```
Dengan menggunakan algoritma Round Robin, lakukan testing dengan menggunakan 3 worker, 2 worker, dan 1 worker sebanyak 100 request dengan 10 request/second, kemudian tambahkan grafiknya pada grimoire.
```
Tambahkan konfigurasi load balancer berikut
```
upstream roundrobin {
    server 10.64.3.1; #IP Lugner
    server 10.64.3.2; #IP Linie
    server 10.64.3.3; #IP Lawine
}


upstream roundrobin2 {
    server 10.64.3.1; #IP Lugner
    server 10.64.3.2; #IP Linie
}

upstream roundrobin1 {
    server 10.64.3.1; #IP Lugner
}
server {
    listen 81;
    server_name _;

    location / {
        proxy_pass http://roundrobin;
    }
}
server {
    listen 892;
    server_name _;

    location / {
        proxy_pass http://roundrobin2;
    }
}

server {
    listen 891;
    server_name _;

    location / {
        proxy_pass http://roundrobin1;
    }
}
```
Analisis ada pada file grimoire. Berikut contohnya pengujiannya	
![image](https://github.com/Koro129/Jarkom-Modul-3-IT01-2023/assets/113784446/95948564-f86f-49ca-ad1a-fb878fef8993)
## Soal 10
```
Selanjutnya coba tambahkan konfigurasi autentikasi di LB dengan dengan kombinasi username: “netics” dan password: “ajkyyy”, dengan yyy merupakan kode kelompok. Terakhir simpan file “htpasswd” nya di /etc/nginx/rahasisakita/
```
Jalankan skrip berikut untuk membuat passwordnya
```
apt install -y apache2-utils

mkdir /etc/nginx/rahasisakita
htpasswd -b -c /etc/nginx/rahasisakita/.htpasswd netics ajkit01
```
Dan berikut konfigurasi autentikasinya
```
server {
    listen 810;
    server_name _;

    location / {
        proxy_pass http://roundrobin;
	auth_basic "Restricted Content";
        auth_basic_user_file /etc/nginx/rahasisakita/.htpasswd;
    }
}
```
Ini membuat jika mengakses lb pada port 810, perlu melakukan autentikasi.
![image](https://github.com/Koro129/Jarkom-Modul-3-IT01-2023/assets/113784446/0ba52594-f5a7-4a02-8ec1-6150ee5e4320)
## Soal 11
```
Lalu buat untuk setiap request yang mengandung /its akan di proxy passing menuju halaman https://www.its.ac.id.
```
Berikut konfigurasi yang digunakan
```
server {
    listen 811;
    server_name _;

    location / {
        proxy_pass http://roundrobin;
    }

    location ~ /its {
        proxy_pass https://www.its.ac.id;
        proxy_set_header Host www.its.ac.id;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
Ini membuat jika mengakses lb pada port 811 dengan tambahan /its, akan diarahkan ke website sesuai soal
![image](https://github.com/Koro129/Jarkom-Modul-3-IT01-2023/assets/113784446/cfe6d9a5-b4fd-4d20-8eb4-2d2c60dfc58e)
## Soal 12
```
Selanjutnya LB ini hanya boleh diakses oleh client dengan IP [Prefix IP].3.69, [Prefix IP].3.70, [Prefix IP].4.167, dan [Prefix IP].4.168.
```
Tambahkan konfigurasi berikut
```
server {
    listen 812;
    server_name _;

    location / {
        proxy_pass http://roundrobin;

        allow 10.64.3.69;
        allow 10.64.3.70;
        allow 10.64.4.167;
        allow 10.64.4.168;
	deny all;

    }
}
```
Ini akan membuat selain ip yang diizinkan di forbidden. Untuk melakukan pengecekan pada ip yang bisa, maka bisa menambahkan ip client saat ini, ke list ip yang diperbolehkan
```
allow [ip client saat ini]
```
![image](https://github.com/Koro129/Jarkom-Modul-3-IT01-2023/assets/113784446/a2946bfa-08ad-4ab0-b986-4768238ce3fb)
## Soal 13
```Semua data yang diperlukan, diatur pada Denken dan harus dapat diakses oleh Frieren, Flamme, dan Fern.```
Jalankan skrip berikut pada Database Server
```
echo 'nameserver 192.168.122.1' > /etc/resolv.conf

apt update && apt-get install mariadb-server -y

service mysql start

mysql -u root -p -e "CREATE USER 'kelompokit01'@'%' IDENTIFIED BY 'passwordit01';"
mysql -u root -p -e "CREATE USER 'kelompokit01'@'localhost' IDENTIFIED BY 'passwordit01';"
mysql -u root -p -e "CREATE DATABASE dbkelompokit01;"
mysql -u root -p -e "GRANT ALL PRIVILEGES ON *.* TO 'kelompokit01'@'%';"
mysql -u root -p -e "GRANT ALL PRIVILEGES ON *.* TO 'kelompokit01'@'localhost';"
mysql -u root -p -e "FLUSH PRIVILEGES;"

echo '
[client-server] 

!includedir /etc/mysql/conf.d/ 
!includedir /etc/mysql/mariadb.conf.d/ 

[mysqld]
skip-networking=0
skip-bind-address' > /etc/mysql/my.cnf

# Cari baris yang berisi bind-address
bind_address_line=$(grep -n "bind-address" /etc/mysql/mariadb.conf.d/50-server.cnf | cut -d: -f1)

# Ganti nilai bind-address dengan 0.0.0.0
sed -i "${bind_address_line}s/127.0.0.1/0.0.0.0/" /etc/mysql/mariadb.conf.d/50-server.cnf

service mysql restart
```
Ini akan membuat user, dan database.   
kemudian install mariadb client pada worker
```
apt-get install mariadb-client -y
```
kemudian akses database dengan command
```
mariadb --host=10.64.2.1 --port=3306 --user=kelompokit01 --password
```
![image](https://github.com/Koro129/Jarkom-Modul-3-IT01-2023/assets/113784446/7b5da969-ebd9-424d-87ae-e2fdabfa512a)
## Soal 14
```
Frieren, Flamme, dan Fern memiliki Riegel Channel sesuai dengan quest guide berikut. Jangan lupa melakukan instalasi PHP8.0 dan Composer
```
Jalankan skrip berikut oada tiap worker
```
echo 'nameserver 192.168.122.1' > /etc/resolv.conf

apt-get purge php* -y

apt-get update
apt-get install wget -y
apt-get install unzip -y
apt-get install htop -y
apt-get install mariadb-client -y

apt-get install -y lsb-release ca-certificates apt-transport-https software-properties-common gnupg2

curl -sSLo /usr/share/keyrings/deb.sury.org-php.gpg https://packages.sury.org/php/apt.gpg
sh -c 'echo "deb [signed-by=/usr/share/keyrings/deb.sury.org-php.gpg] https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list'

apt-get install php8.0-mbstring php8.0-xml php8.0-cli php8.0-common php8.0-intl php8.0-opcache php8.0-readline php8.0-mysql php8.0-fpm php8.0-curl -y
apt-get install nginx -y

service php8.0-fpm start

rm /usr/local/bin/composer

wget https://getcomposer.org/download/2.0.13/composer.phar
chmod +x composer.phar
mv composer.phar /usr/local/bin/composer

apt-get install git -y

rm -r /var/www/laravel-praktikum-jarkom

cd /var/www/ && git clone https://github.com/martuafernando/laravel-praktikum-jarkom.git

echo '
APP_NAME=Laravel
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://localhost

LOG_CHANNEL=stack
LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug

DB_CONNECTION=mysql
DB_HOST=10.64.2.1
DB_PORT=3306
DB_DATABASE=dbkelompokit01
DB_USERNAME=kelompokit01
DB_PASSWORD=passwordit01

BROADCAST_DRIVER=log
CACHE_DRIVER=file
FILESYSTEM_DISK=local
QUEUE_CONNECTION=sync
SESSION_DRIVER=file
SESSION_LIFETIME=120

MEMCACHED_HOST=127.0.0.1

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_MAILER=smtp
MAIL_HOST=mailpit
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS="hello@example.com"
MAIL_FROM_NAME="${APP_NAME}"

AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=
AWS_USE_PATH_STYLE_ENDPOINT=false

PUSHER_APP_ID=
PUSHER_APP_KEY=
PUSHER_APP_SECRET=
PUSHER_HOST=
PUSHER_PORT=443
PUSHER_SCHEME=https
PUSHER_APP_CLUSTER=mt1

VITE_PUSHER_APP_KEY="${PUSHER_APP_KEY}"
VITE_PUSHER_HOST="${PUSHER_HOST}"
VITE_PUSHER_PORT="${PUSHER_PORT}"
VITE_PUSHER_SCHEME="${PUSHER_SCHEME}"
VITE_PUSHER_APP_CLUSTER="${PUSHER_APP_CLUSTER}"' > /var/www/laravel-praktikum-jarkom/.env

rm /var/www/laravel-praktikum-jarkom/.env.example

cd /var/www/laravel-praktikum-jarkom && composer update

cd /var/www/laravel-praktikum-jarkom && php artisan migrate:fresh
cd /var/www/laravel-praktikum-jarkom && php artisan db:seed
cd /var/www/laravel-praktikum-jarkom && php artisan key:generate
cd /var/www/laravel-praktikum-jarkom && php artisan jwt:secret
apt install nginx

echo '	 server {

 	listen 80;

 	root /var/www/laravel-praktikum-jarkom/public;

 	index index.php index.html index.htm;
 	server_name _;

 	location / {
 			try_files $uri $uri/ /index.php?$query_string;
 	}

 	# pass PHP scripts to FastCGI server
 	location ~ \.php$ {
 	include snippets/fastcgi-php.conf;
 	fastcgi_pass unix:/var/run/php/php8.0-fpm.sock;
 	}

 location ~ /\.ht {
 			deny all;
 	}

 	error_log /var/log/nginx/jarkom_error.log;
 	access_log /var/log/nginx/jarkom_access.log;
 }' > /etc/nginx/sites-available/riegel.canyon.it01

unlink /etc/nginx/sites-enabled/default
ln -s /etc/nginx/sites-available/riegel.canyon.it01 /etc/nginx/sites-enabled/

chown -R www-data.www-data /var/www/laravel-praktikum-jarkom/storage

service php8.0-fpm restart
service nginx restart
```
Ini akan mendeploy website pada tiap worker.   
Tes dengan ```lynx http://riegel.canyon.it01.com```
![image](https://github.com/Koro129/Jarkom-Modul-3-IT01-2023/assets/113784446/6de01316-4ec6-467c-a61d-ec8a7f1b2226)
## Soal 15
```
POST /auth/register
```
Buat file register.json dari client yang berisi
```
{
  "username": "tesit01",
  "password": "tesit01"
}
```
Kemudian lakukan test dengan ```curl -X POST -H "Content-Type: application/json" -d @register.json http://riegel.canyon.it01.com/api/auth/register```
![image](https://github.com/Koro129/Jarkom-Modul-3-IT01-2023/assets/113784446/83eb79d9-c0ab-4fe3-9733-72be7399d262)
Untuk testing benchmark ada di file grimoire
## Soal 16
```
POST /auth/login
```
lakukan test dengan
```
curl -X POST -H "Content-Type: application/json" -d @register.json http://riegel.canyon.it01.com/api/auth/login
```
![image](https://github.com/Koro129/Jarkom-Modul-3-IT01-2023/assets/113784446/b0141143-d774-469c-82c3-0972ac7c7be7)
token bisa disimpan dengan menambahkan command berikut pada saat curl login ```| grep -o '"token": *"[^"]*"' | cut -d\" -f4 > token.txt```
Untuk testing benchmark ada di file grimoire
## Soal 17
```
GET /me
```
lakukan test dengan
```
curl -H "Authorization: Bearer $(cat token.txt)" http://riegel.canyon.it01.com/api/me
```
![image](https://github.com/Koro129/Jarkom-Modul-3-IT01-2023/assets/113784446/7cf5c4a2-10e9-46ed-8297-a4a675532e72)
Untuk testing benchmark ada di file grimoire
## Soal 18
```
Untuk memastikan ketiganya bekerja sama secara adil untuk mengatur Riegel Channel maka implementasikan Proxy Bind pada Eisen untuk mengaitkan IP dari Frieren, Flamme, dan Fern.
```
Gunakan konfigurasi berikut pada Eisen
```
upstream laravel {
    server 10.64.4.1; #IP Frieren
    server 10.64.4.2; #IP Flamme
    server 10.64.4.3; #IP Fern
}
server {
    listen 818;
    server_name _;

    location / {
        proxy_pass http://laravel;
    }
}
```
Ini membuat lb pada port 818 akan menuju website riegen channel
![image](https://github.com/Koro129/Jarkom-Modul-3-IT01-2023/assets/113784446/01065bef-6c9e-4531-a4bb-e972698c32e4)
## Soal 19
```
Untuk meningkatkan performa dari Worker, coba implementasikan PHP-FPM pada Frieren, Flamme, dan Fern. Untuk testing kinerja naikkan 
- pm.max_children
- pm.start_servers
- pm.min_spare_servers
- pm.max_spare_servers
sebanyak tiga percobaan dan lakukan testing sebanyak 100 request dengan 10 request/second kemudian berikan hasil analisisnya pada Grimoire
```
Jalankan skrip berikut pada worker
```
echo '[fpm1]
user = www-data
group = www-data
listen = /run/php/php8.0-fpm-fpm1.sock
listen.owner = www-data
listen.group = www-data
php_admin_value[disable_functions] = exec,passthru,shell_exec,system
php_admin_flag[allow_url_fopen] = off

; Choose how the process manager will control the number of child processes.

pm = dynamic
pm.max_children = 8
pm.start_servers = 4
pm.min_spare_servers = 2
pm.max_spare_servers = 6' > /etc/php/8.0/fpm/pool.d/fpm1.conf


echo '[fpm2]
user = www-data
group = www-data
listen = /run/php/php8.0-fpm-fpm2.sock
listen.owner = www-data
listen.group = www-data
php_admin_value[disable_functions] = exec,passthru,shell_exec,system
php_admin_flag[allow_url_fopen] = off

; Choose how the process manager will control the number of child processes.

pm = dynamic
pm.max_children = 16
pm.start_servers = 8
pm.min_spare_servers = 4
pm.max_spare_servers = 12' > /etc/php/8.0/fpm/pool.d/fpm2.conf


echo '[fpm3]
user = www-data
group = www-data
listen = /run/php/php8.0-fpm-fpm3.sock
listen.owner = www-data
listen.group = www-data
php_admin_value[disable_functions] = exec,passthru,shell_exec,system
php_admin_flag[allow_url_fopen] = off

; Choose how the process manager will control the number of child processes.

pm = dynamic
pm.max_children = 32
pm.start_servers = 10
pm.min_spare_servers = 6
pm.max_spare_servers = 24' > /etc/php/8.0/fpm/pool.d/fpm3.conf

echo '	 server {

 	listen 81;

 	root /var/www/laravel-praktikum-jarkom/public;

 	index index.php index.html index.htm;
 	server_name _;

 	location / {
 			try_files $uri $uri/ /index.php?$query_string;
 	}

 	# pass PHP scripts to FastCGI server
 	location ~ \.php$ {
 	include snippets/fastcgi-php.conf;
 	fastcgi_pass unix:/var/run/php/php8.0-fpm-fpm1.sock;
 	}

 location ~ /\.ht {
 			deny all;
 	}

 	error_log /var/log/nginx/jarkom_error.log;
 	access_log /var/log/nginx/jarkom_access.log;
 }' > /etc/nginx/sites-available/fpm1.riegel.canyon.it01

echo '	 server {

 	listen 82;

 	root /var/www/laravel-praktikum-jarkom/public;

 	index index.php index.html index.htm;
 	server_name _;

 	location / {
 			try_files $uri $uri/ /index.php?$query_string;
 	}

 	# pass PHP scripts to FastCGI server
 	location ~ \.php$ {
 	include snippets/fastcgi-php.conf;
 	fastcgi_pass unix:/var/run/php/php8.0-fpm-fpm2.sock;
 	}

 location ~ /\.ht {
 			deny all;
 	}

 	error_log /var/log/nginx/jarkom_error.log;
 	access_log /var/log/nginx/jarkom_access.log;
 }' > /etc/nginx/sites-available/fpm2.riegel.canyon.it01

echo '	 server {

 	listen 83;

 	root /var/www/laravel-praktikum-jarkom/public;

 	index index.php index.html index.htm;
 	server_name _;

 	location / {
 			try_files $uri $uri/ /index.php?$query_string;
 	}

 	# pass PHP scripts to FastCGI server
 	location ~ \.php$ {
 	include snippets/fastcgi-php.conf;
 	fastcgi_pass unix:/var/run/php/php8.0-fpm-fpm3.sock;
 	}

 location ~ /\.ht {
 			deny all;
 	}

 	error_log /var/log/nginx/jarkom_error.log;
 	access_log /var/log/nginx/jarkom_access.log;
 }' > /etc/nginx/sites-available/fpm3.riegel.canyon.it01

ln -s /etc/nginx/sites-available/fpm1.riegel.canyon.it01 /etc/nginx/sites-enabled/
ln -s /etc/nginx/sites-available/fpm2.riegel.canyon.it01 /etc/nginx/sites-enabled/
ln -s /etc/nginx/sites-available/fpm3.riegel.canyon.it01 /etc/nginx/sites-enabled/

service php8.0-fpm restart
service nginx restart
```
Ini akan membuat 3 fpm sekaligus, dan mendeploy nya dengan port yang berbeda    
Tambahkan konfigurasi berikut pada LB
```
upstream laravelfpm1 {
    server 10.64.4.1:81; #IP Frieren
    server 10.64.4.2:81; #IP Flamme
    server 10.64.4.3:81; #IP Fern
}

upstream laravelfpm2 {
    server 10.64.4.1:82; #IP Frieren
    server 10.64.4.2:82; #IP Flamme
    server 10.64.4.3:82; #IP Fern
}

upstream laravelfpm3 {
    server 10.64.4.1:83; #IP Frieren
    server 10.64.4.2:83; #IP Flamme
    server 10.64.4.3:83; #IP Fern
}

server {
    listen 818;
    server_name _;

    location / {
        proxy_pass http://laravel;
    }
}

server {
    listen 8191;
    server_name _;

    location / {
        proxy_pass http://laravelfpm1;
    }
}

server {
    listen 8192;
    server_name _;

    location / {
        proxy_pass http://laravelfpm2;
    }
}

server {
    listen 8193;
    server_name _;

    location / {
        proxy_pass http://laravelfpm3;
    }
}
```
Ini akan menghubungkan worker tadi ke lb dengan portnya masing masing    
Hasil testing benchmark ada di file grimoire
## Soal 20
Tambahkan konfigurasi berikut pada LB
```Nampaknya hanya menggunakan PHP-FPM tidak cukup untuk meningkatkan performa dari worker maka implementasikan Least-Conn pada Eisen. Untuk testing kinerja dari worker tersebut dilakukan sebanyak 100 request dengan 10 request/second.```
```
upstream laravel_leastconn{
    least_conn;
    server 10.64.4.1; #IP Frieren
    server 10.64.4.2; #IP Flamme
    server 10.64.4.3; #IP Fern
}
server {
    listen 820;
    server_name _;

    location / {
        proxy_pass http://laravel_leastconn;
    }
}
```
Ini akan menggunakan leastconn pada lb jika diakses pada port 820   
Hasil testing benchmark ada di file grimoire
