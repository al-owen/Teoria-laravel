1. Primero hay que comprimir la carpeta entera de nuestro proyecto de Laravel.
2. Exportar la base de datos a un fichero sql.
3. Ir al servidor a la carpeta donde se tienen que subir los ficheros, si quieres un subdominio, primero crea el subdominio y después entra a la carpeta.
4. Sube el fichero zip y descomprímelo en el directorio donde quieres que este el proyecto.
5. Crea una base de datos y guarda los datos: nombre de la base de datos, usuario y contraseña.
6. Dirígete al archivo `.env` de tu app laravel e ingresa los datos en los campos correspondientes:
```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=db           #Cambiar el nombre de la base de datos
DB_USERNAME=user         #Cambiar el nombre de usuario
DB_PASSWORD=********     #Cambiar la contraseña
```
7. Crea en el fichero de la app laravel, un fichero llamado `.htaccess` y escribe lo siguiente:
``` htacces
<IfModule mod_rewrite.c>
	RewriteEngine On
	RewriteCond %{REQUEST_URI} !^public
	RewriteRule ^(.*)$ public/$1 [L]
</IfModule>
```
8. Importa los datos (del sql que exportaste), en la base de datos de el servicio de hosting.
9. Dirígete a la URL y debería estar funcionando