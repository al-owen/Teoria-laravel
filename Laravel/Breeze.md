Para instalar breeze, usamos los siguientes comandos:

## Manera manual (en un proyecto existente)

``` php
composer require laravel/breeze --dev

php artisan breeze:install
 
php artisan migrate
npm install
npm run dev
```

## Manera automática (Creación limpia)

Primero declaramos que usaremos el instalador de Laravel

```sh
composer global require laravel/installer
```

Creamos nuestro proyecto

```sh
laravel new Proyecto
```

Se nos harán diversas preguntas:
En la primera pregunta especificamos `breeze`
```sh
   _                               _
  | |                             | |
  | |     __ _ _ __ __ ___   _____| |
  | |    / _` | '__/ _` \ \ / / _ \ |
  | |___| (_| | | | (_| |\ V /  __/ |
  |______\__,_|_|  \__,_| \_/ \___|_|


 Would you like to install a starter kit? [No starter kit]:
  [none     ] No starter kit
  [breeze   ] Laravel Breeze
  [jetstream] Laravel Jetstream
 > breeze
```

En la siguiente pregunta dependerá del front-end que vayamos a usar, por defecto usaremos `blade`.

```sh
 Which Breeze stack would you like to install? [Blade with Alpine]:
  [blade              ] Blade with Alpine
  [livewire           ] Livewire (Volt Class API) with Alpine
  [livewire-functional] Livewire (Volt Functional API) with Alpine
  [react              ] React with Inertia
  [vue                ] Vue with Inertia
  [api                ] API only
 > blade
```

Se nos preguntará si queremos soporte para modo oscuro, aquí dependerá de cada uno y el proyecto que estemos desarrollando

```sh
Would you like dark mode support? (yes/no) [no]:
> yes
```

Se nos preguntará por el framework de testing que queremos usar, por defecto se utiliza `Pest`

```sh
Which testing framework do you prefer? [Pest]:
  [0] Pest
  [1] PHPUnit
>0
```

Lo siguiente es si queremos tener un repositorio de git o no.

``` sh
Would you like to initialize a Git repository? (yes/no) [no]:
> yes
```

Una vez acabada la creación del proyecto y la instalación de las dependencias se nos preguntará que base de datos queremos utilizar, por lo general utilizaremos MySql, pero depende del programador y del proyecto.

```sh
 Which database will your application use? [SQLite]:
  [sqlite ] SQLite
  [mysql  ] MySQL
  [mariadb] MariaDB
  [pgsql  ] PostgreSQL (Missing PDO extension)
  [sqlsrv ] SQL Server (Missing PDO extension)
 > mysql
```

Finalmente se nos pregunta si queremos hacer las migraciones, sin embargo, *en el caso de que tengamos que modificar el fichero `.env` para que coincidan las credenciales*, tendremos problemas a la hora de realizar las migraciones, por lo tanto es recomendable, primero modificar el fichero y después hacer las migraciones.

```sh
Default database updated. Would you like to run the default database migrations? (yes/no) [yes]:
> no 
```

