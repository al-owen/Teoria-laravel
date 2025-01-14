- [[#Funciones de Composer|Funciones de Composer]]
- [[#Instalación|Instalación]]
	- [[#Instalación#Windows|Windows]]
	- [[#Instalación#Linux|Linux]]
- [[#Creación de proyectos Laravel|Creación de proyectos Laravel]]
	- [[#Creación de proyectos Laravel#Opcion 1 (Recomendada)|Opcion 1 (Recomendada)]]
	- [[#Creación de proyectos Laravel#Opción 2|Opción 2]]
	- [[#Creación de proyectos Laravel#Ejecutar el servicio|Ejecutar el servicio]]


Composer es un gestor de dependencias para PHP. Facilita la gestión de bibliotecas y paquetes necesarios para un proyecto PHP, asegurando que todas las dependencias requeridas estén disponibles y actualizadas.

## Funciones de Composer

1. **Instalación de Paquetes**: Composer permite instalar y actualizar bibliotecas de terceros fácilmente mediante el uso de un archivo llamado `composer.json`, que define las dependencias del proyecto.
2. **Autoloading**: Automatiza el proceso de carga de clases, lo que elimina la necesidad de incluir manualmente los archivos PHP en el proyecto.
3. **Versionado**: Administra versiones de las dependencias, asegurando que se utilicen las versiones compatibles y evitando conflictos.
4. **Repositorio Central**: Utiliza Packagist como el repositorio principal de paquetes, donde se pueden encontrar y publicar librerías PHP.

## Instalación

### Windows
Para instalarlo en Windows debemos ir a la siguiente página: [Composer](https://getcomposer.org/download/) descargarlo e instalarlo.
### Linux
En caso de que lo queramos instalar en sistemas UNIX, usamos los siguientes comandos:
``` sh
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('sha384', 'composer-setup.php') === 'dac665fdc30fdd8ec78b38b9800061b4150413ff2e3b6f88543c636f7cd84f6db9189d43a81e5503cda447da73c7e5b6') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"
```

y finalmente:

``` sh
sudo mv composer.phar /usr/local/bin/composer
```
## Creación de proyectos Laravel

Para poder crear un proyecto de Laravel existen 2 opciones principales.

### Opción 1 (Recomendada)

En esta opción se instala el instalador de Laravel y después se genera el proyecto. La principal ventaja es que en esta opción al momento de crear el proyecto te pregunta si quieres añadir otras librerías y, por lo tanto, te genera un proyecto con las herramientas que vayamos a utilizar.

**Instalar el instalador**

``` sh
composer global require laravel/installer
```

**Crear el proyecto**

``` sh
laravel new [Nombre-proyecto]
```

### Opción 2

En el caso de querer crear un proyecto limpio (sin ninguna librería extra) usamos el siguiente comando:

``` bash
composer create-project laravel/laravel [Nombre-proyecto]
```

### Ejecutar el servicio
Una vez creado el proyecto entramos en el directorio del proyecto y ejecutamos el servicio (en caso de no usar XAMPP).

``` bash 
cd example-app 
php artisan serve
```
