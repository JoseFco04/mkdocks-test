# Practica-03-IAW
### Para esta práctica vamos a crear una instancia de ubuntu, pulsamos en lanzar instancia y seleccionamos ubuntu 
![cap 1 p3](https://github.com/JoseFco04/practica-03-IAW/assets/145347148/37b6124d-4994-4a13-b8d9-1ba57372f835)

### Le ponemos de tamaño small la máquina.
![cap 2 p3](https://github.com/JoseFco04/practica-03-IAW/assets/145347148/41e28934-8db1-495c-b49a-b377cfeec1bc)

### Le permitimos el tráfico http y https para que nos añada los puertos de seguridad directamente 
![cap 3 p3](https://github.com/JoseFco04/practica-03-IAW/assets/145347148/17ae3a20-aa69-4628-b792-f101c0fc4a08)

### Después de esto le asignamos una ip pública o ip elástica y empezamos con los scripts.

### Tenemos tres scripts que vamos a copiar de la práctica uno: es script del install_lamp, el de configuracion y el de php.

### El script del lamp es este paso por paso :

#### Muestra todos los comandos que se van ejecutando
~~~
set -x
~~~
#### Actualizamos los repositorios
~~~
apt update
~~~
#### Actualizamos los paquetes
~~~
#### apt upgrade -y
~~~
#### instalamos el servidor web Apache
~~~
apt install apache2 -y
~~~
#### Instalamos e sistema gestor de base de datos de mysql
~~~
apt install mysql-server -y
~~~
#### mysql -u $DB_USER -p $DP_PASSWD < .../sql/database.sql

#### Instalamos  PHP
~~~
apt install php libapache2-mod-php php-mysql -y
~~~
#### Copiar el archivo de configuración de Apache 
~~~
cp ../conf/000-default.conf /etc/apache2/sites-available
~~~
#### Reiniciamos el servicio Apache
~~~
systemctl restart apache2
~~~
#### Copiamos el archivo de prueba de php
~~~
cp ../php/index.php /var/www/html
~~~
#### Modificamos el propietario y el grupo del directorio /var/www/html
~~~
chown -R www-data:www-data /var/www/html
~~~
### El archivo de cofiguracion 000-default-conf es este:
~~~
ServerSignature Off
ServerTokens Prod

<VirtualHost *:80>
#ServerName www.example.com
DocumentRoot /var/www/html

  DirectoryIndex index.php index.html

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
~~~
### El archivo de php
~~~
<?php

phpinfo();

?>
~~~
### Después tenemos nuestro archivo .env para guardar nuestras variables
~~~
# Configuramos las variables
#-------------------------------------------
DB_NAME=aplicacion
DB_USER=usuario
DB_PASSWORD=password
~~~
### Y el ultimo es el script del deploy.sh que paso por paso hace esto:

#### Muestra todos los comandos que se van ejecutando  
~~~
set -x
~~~
#### Importamos las variables 
~~~
source .env 
~~~
#### Actualizamos los repositorios 
~~~
apt  update 
~~~
#### Actualizamos todos los paquetes 
~~~
#apt upgrade -y 
~~~
#### Eliminamos descargas previas del repositorio 
~~~
rm -rf /tmp/iaw-practica-lamp
~~~
#### Clonamos el repositorio con el codigo fuente de la aplicacion
~~~
git clone https://github.com/josejuansanchez/iaw-practica-lamp /tmp/iaw-practica-lamp
~~~
#### Movemos el codigo fuente de la aplicaciona /var/www/html
~~~
mv /tmp/iaw-practica-lamp/src/* /var/www/html
~~~
#### Configuramos el archivo sql para que no de error al poner una base de datos distinata a lamp_db
~~~
sed -i "s/lamp_db/$DB_NAME/g" /tmp/iaw-practica-lamp/db/database.sql
~~~
#### Configuramos el archivo config.php de la aplicación
~~~
sed -i "s/database_name_here/$DB_NAME/" /var/www/html/config.php
sed -i "s/username_here/$DB_USER/" /var/www/html/config.php
sed -i "s/password_here/$DB_PASSWORD/" /var/www/html/config.php
~~~
#### Importamos el script de base de datos 
~~~
mysql -u root < /tmp/iaw-practica-lamp/db/database.sql
~~~
#### Creamos el usuariob de la base de datos y le asignamos privilegios
~~~
mysql -u root <<< "DROP USER IF EXISTS $DB_USER@'%'"
mysql -u root <<< "CREATE USER $DB_USER@'%' IDENTIFIED BY '$DB_PASSWORD'"
mysql -u root <<< "GRANT ALL PRIVILEGES ON $DB_NAME.* TO $DB_USER@'%'"
~~~
#### Y al ejecutar el script y menternos en el navegador en la ip elástica nos saldría la aplicación en la que podemos meter las bases de datos que queramos.
#### Así se vería la página web
![cap p32](https://github.com/JoseFco04/practica-03-IAW/assets/145347148/a3729289-5188-4a4b-847c-5becd1898216)
#### Añadimos una base de datos 
![cap p322](https://github.com/JoseFco04/practica-03-IAW/assets/145347148/c7f97637-8cff-44a6-bb25-eac944111c35)
#### Y nos saldría en la página.
![cap p323](https://github.com/JoseFco04/practica-03-IAW/assets/145347148/2fcfd9c3-e57e-4477-9007-35397fa07416)



