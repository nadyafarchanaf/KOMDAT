# Aplikasi Web "Mahara"

## Sekilas Tentang Mahara
Mahara adalah aplikasi online open-source yang terdiri dari e-portfolio dan jejaring sosial dengan tampilan framework yang fleksibel. Mahara termasuk sebagai aplikasi untuk learning dan course. Mahara dalam bahasa aslinya berarti 'berpikir', Mahara dapat menampilkan dan mengatur e-portfolio dengan lebih mudah dan fleksibel sehingga dapat digunakan untuk pelajar. Mahara memiliki fitur weblog, membangun surat riwayat kerja dan pendidikan dan memiliki sistem jejaring sosial dimana pengguna juga dapat memberikan komen feedback kepada pengguna lain, menghubungkan user dan menciptakan komunitas belajar online.

## **Instalasi**
 1. **Kebutuhan Sistem**
	- Server OS: Ubuntu Linux latest LTS
	- Apache web server    
	- PHP (version 5.3.6 atau lebih tinggi lagi)    
	- MySQL(version 5.1 atau lebih tinggi lagi) yang sudah terinstal di Linux VPS
	- Web Browser: latest version of Firefox, Chrome, Safari, Internet Explorer
	- Hardware :
	
		- CPU: Semakin cepat CPU kinerja semakin baik. PHP bergantung pada performa CPU maka semakin cepat CPU waktu respon akan semakin cepat.
		
		- Memory: 256MB pada absolute m 1G atau lebih sangat disarankan.
		
		- Disk: Bergantung kepada kouta disk yang dibutuhkan pengguna. Untuk situs dengan 1000 pengguna, masing-masing 250M kouta, maka membutuhkan 50G untuk memulainya.

2. **Langkah instalasi dalam CLI :**
    - Login ke VPS via SSH

    		ssh mahara@localhost -p 2222
    - Update dan upgrade sistem

    		sudo apt update
    - Install mysql

   			sudo apt-get install -y mysql-server
    - Create database for Mahara
		
			mysql -u root -p
			CREATE DATABASE maharadb character set UTF8;
			GRANT ALL PRIVILEGES ON maharadb.* TO 'maharauser'@'localhost' IDENTIFIED BY 'linux';
			FLUSH PRIVILEGES;
			exit
    - Install Apache2 Web Server

   			sudo apt-get install apache2
    - Install PHP
		
      *Add the PPA*
			
			sudo add-apt-repository ppa:ondrej/php
			
		*Install your PHP Version*
		
			sudo apt-get update
			sudo apt-get install php5.6 libapache2-mod-php5.6 php5.6-mcrypt php5.6-mysql php5.6-gd
	-  Download and Extract Mahara
		
			sudo cd /opt && wget https://launchpad.net/mahara/16.04/16.04.1/+download/mahara-16.04.1.zip
			sudo unzip mahara-16.04.1.zip

		*jika belum ada unzip*
		
			sudo apt install unzip
			sudo mv mahara-16.04.1 /var/www/html/mahara

		*Create Mahara’s upload directory*
		
			sudo mkdir /var/www/html/mahara/upload/
	- Configure Mahara
	
		*Membuat file copy dari config-dist.php yang ada di direktori htdocs, yang diberi nama config.php*
	
			cd /var/www/html/mahara/htdocs/
			sudo cp config-dist.php config.php

		*Masuk ke open config.php dan mengubah beberapa baris.*
	
			sudo nano config.php

			$cfg->dbtype   = 'mysql';
			$cfg->dbhost   = 'localhost';
			$cfg->dbport   = null;
			$cfg->dbname   = 'mahara';
			$cfg->dbuser   = 'maharauser';
			$cfg->dbpass   = 'mahara';
			$cfg->dataroot = '/var/www/html/mahara/upload/';

		*Semua file harus dapat dibaca oleh server, maka harus dibuat akun kepemilikan yang benar*
	
			sudo chown www-data:www-data -R /var/www/html/mahara

	- Configure Apache Web Server
	
		*Membuat virtual host baru di Apache, misal nama virtual hostnya adalah mahara.conf*
		
			sudo touch /etc/apache2/sites-available/mahara.conf
			sudo ln -s /etc/apache2/sites-available/mahara.conf /etc/apache2/sites-enabled/mahara.conf
			sudo nano /etc/apache2/sites-available/mahara.conf
 
		*Kemudian tambahkan baris-baris berikut:*
		
			<VirtualHost *:80>
			ServerAdmin komdat9@gmail.com
			DocumentRoot /var/www/html/mahara/htdocs/
			ServerName localhost:8888
			ServerAlias localhost:8888
			<Directory /var/www/html/mahara/htdocs/>
			Options FollowSymLinks
			AllowOverride All
			Order allow,deny
			allow from all
			</Directory>
			ErrorLog /var/log/apache2/localhost:8888-error_log
			CustomLog /var/log/apache2/localhost:8888-access_log common
			</VirtualHost>

	- Install curl dan xml
	
			sudo apt-get install php5.6-curl -y
			sudo apt-get install php5.6-xml
	- Restart 
	
		*Restart Apache web server*
		
			sudo service apache2 restart
		
	- Buka di browser
	
			http://localhost:8888/htdocs/index.php

## **Konfigurasi**
1. **Batas upload file**

	Post dan upload size maksimal 50M
		 
2. **Plugin**

	Untuk mengecek version number dari plugin yang terinstal dapat dilakukan dengan menjalankan kode dibawah ini pada command line:
		 
		find ./ -iname readme.mahara -exec grep -H 'Version' {} \;
				
	Mahara memiliki banyak sekali plugin yang tersedia, berikut beberapa plugin yang dimiliki oleh Mahara:
	- Plugin Update
		Prosedurnya adalah sebagai berikut:
		
		- Hapus kode plugin(file) lama
			
		- Salin pada kode plugin yang baru
			
		- Pergi ke "Administration".
			
		- Proses Update
	- Plugin thumbnail images untuk konsistensi visual dengan Mahara 1.5 atau yang lebih baru
	- Search Plugins : Sphinx search
	- Blocktype Plugins: CalDAVCalendar, Facebook like/recommend,LinkedIn Profile, dan lain-lain

## **Upgrade Mahara**

Proses adalah sebagai berikut :
1. Tutup site Mahara
	
2. Saat dilakukan update, direkomendasikan untuk melakukan maintenance mode sehingga pengguna yang sedang login akan otomatis untuk logout
	
3. Buat backup data dan code yang akan dirubah
	
4. Ganti kode yang lama dengan kode yang baru
	
5. Upgrade database
	- Upgrading pada command-line
	
			sudo -u www-data /usr/bin/php/PATH/TO/YOUR/MAHARA/htdocs/admin/cli/upgrade.php

	- Upgrading a migrated database
	
		Jika database adalah hasil clone/copy, maka user pemilik database baru haruslah menjadi pemilik file cloning lainnya
	
	- Upgrading from site older than one recommended in direct upgrade
	
		Terdapat file README.md yang menunjukkan versi minimum dari yang seharusnya diupgrade, dan perhatikan juga apakah versi PHP/SQL yang digunakan kompatibel terhadap mahara versi baru atau tidak. 
6. Open your site


## **Cara Pemakaian**

- Tampilan aplikasi web
	Halaman login
	<img src="h>
