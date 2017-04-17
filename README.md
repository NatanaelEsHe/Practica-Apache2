# Practica - Apache2
Crear un servidor Apache con 4 virtual host:  

www.gato.com -> mostrará la imagen de un gato.  
www.mosquito.com -> mostrará la imagen de un mosquito tigre.  
www.escherichiacoli.es -> mostrará la imagen de la bacteria escherichia coli. Sólo tendrá acceso el usuario 'user01'.  
www.chip555.org -> mostrará la imagen del chip 555. sólo tendrán acceso los usuarios del fichero de password creado.  

Crear una máquina con el servidor Apache y el servidor dns necesario.  
Crear otra máquina con un entorno gráfico para comprobar el correcto funcionamiento.  

**Apache:**  
sudo mkdir -p /var/www/gato.com/html  
sudo mkdir -p /var/www/mosquito.com/html  
sudo mkdir -p /var/www/escherichiacoli.es/html  
sudo mkdir -p /var/www/chip555.org/html  

**Permisos necesarios:**  
~~~
sudo chmod -R 777 /var/www  
~~~

**Creamos el sitio web y añadimos la imagenes:**  
~~~
nano /var/www/gato.com/html/index.html  
nano /var/www/mosquito.com/html/index.html  
nano /var/www/escherichiacoli.es/html/index.html  
nano /var/www/chip555.org/html/index.html  
~~~

**Configuracion de los host:**  
~~~
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/gato.com.conf  
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/mosquito.com.conf  
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/escherichiacoli.es.conf  
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/chip555.org.conf  
~~~

**Añadimos:**  
~~~
nano /etc/apache2/sites-available/gato.com.conf  
<VirtualHost *:80>
    ServerAdmin webmaster@gato.com
    ServerName gato.com
    ServerAlias www.gato.com
    DocumentRoot /var/www/gato.com/html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
~~~

~~~
nano /etc/apache2/sites-available/mosquito.com.conf  
<VirtualHost *:80>
    ServerAdmin webmaster@mosquito.com
    ServerName mosquito.com
    ServerAlias www.mosquito.com
    DocumentRoot /var/www/mosquito.com/html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
~~~  

~~~
nano /etc/apache2/sites-available/escherichiacoli.es.conf  
<VirtualHost *:80>
    ServerAdmin admin@escherichiacoli.es
    ServerName escherichiacoli.es
    ServerAlias www.escherichiacoli.es
    DocumentRoot /var/www/escherichiacoli.es/html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
~~~  

~~~
nano /etc/apache2/sites-available/chip555.org.conf  
<VirtualHost *:80>
    ServerAdmin admin@chip555.com
    ServerName chip555.com
    ServerAlias www.chip555.com
    DocumentRoot /var/www/chip555.com/html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
~~~  

**Habilitamos los sitios web:**  
~~~
sudo a2ensite gato.com.conf
sudo a2ensite mosquito.com.conf
sudo a2ensite escherichiacoli.es.conf
sudo a2ensite chip555.com.conf
~~~  

**Desabilitamos el sitio por defecto**  
~~~
sudo a2dissite 000-default.conf  
~~~

**Reiniciamos**  
~~~
sudo service apache2 restart  
~~~

**Fichero de contraseñas para el sitio escherichiacoli:**  
~~~
sudo htpasswd -c /var/www/escherichiacoli.es/passwords user01
sudo htpasswd  /var/www/escherichiacoli.es/passwords user02
~~~

**Añadimos:**  
~~~
<Directory /var/www/escherichiacoli.es/html>
	AuthType Basic
	AuthName "ACCESO RESTRINGIDO"
	AuthUserFile /var/www/escherichiacoli.es/passwords
	Require user user01
</Directory>
<Directory /var/www/escherichiacoli.es/html>        
	Options Indexes FollowSymLinks MultiViews
	AllowOverride  none
	Order Allow,deny
	allow from user01
</Directory>
~~~

**Fichero de contraseñas para el sitio chip555:**  
~~~
sudo htpasswd -c /var/www/chip555.org/passwords user01
sudo htpasswd  /var/www/chip555.org/passwords user02
~~~

**Añadimos:**  
~~~
<Directory /var/www/chip555.org/html>
	AuthType Basic
	AuthName "ACCESO RESTRINGIDO"
	AuthUserFile /var/www/chip555.org/passwords
	Require valid-users
</Directory>
<Directory /var/www/chip555.org/html>        
	Options Indexes FollowSymLinks MultiViews
	AllowOverride  none
	Order Allow,deny
	allow from all
</Directory>
~~~
