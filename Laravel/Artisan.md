- [[#Serve:|Serve:]]
- [[#List:|List:]]
- [[#Migrate:|Migrate:]]
- [[#Make:|Make:]]
	- [[#Make:#make:controller|make:controller]]
	- [[#Make:#make:middleware|make:middleware]]
	- [[#Make:#make:migration|make:migration]]
	- [[#Make:#make:model|make:model]]
	- [[#Make:#make:model junto a la migración|make:model junto a la migración]]
	- [[#Make:#make:model junto a la migración y controlador|make:model junto a la migración y controlador]]
	- [[#Make:#make:resource|make:resource]]
	- [[#Make:#make:view|make:view]]
- [[#Tinker|Tinker]]

## Serve: 
Inicia el servidor de desarrollo.
``` bash
php artisan serve
```
## List: 
Muestra todos los comandos disponibles.
``` bash
php artisan list
```

## Route:
Muestra información relacionada a las rutas
### :list
``` sh
php artisan route:list 
#o también se puede usar 
php artisan r:l 
```
También nos permite añadir diversas flags para filtrar los resultados
Por ejemplo:
1. **`--except-vendor`:** nos permite ver solo las rutas creadas por nosotros.
2. **`--only-vendor`:** nos permite ver solo las rutas creadas por librerías.
3. **`--path=`*NombreRuta*:** nos permite filtrar solo las rutas que contengan el nombre establecido. 
### :clear
Borra la cache del fichero de rutas
``` sh
php artisan route:clear
```

## Migrate: 
Hace las operaciones relacionadas con las migraciones de las bases de datos

Ejecuta las migraciones de base de datos.
``` bash
php artisan migrate
```
### :Reset
Hace un drop de las tablas de la base de datos.
``` bash
php artisan migrate:reset
```
### :Refresh
Hace un drop de las tablas de la base de datos y después ejecuta una migración.
``` bash
php artisan migrate:refresh
```

## Make: 
Permite crear diferentes componentes 
### :controller
Crea una nueva clase de controlador
``` bash
php artisan make:controller [Nombre]
```
### :middleware
Crea un middleware
``` bash
php artisan make:middleware [Nombre]
```
### :migration
Crea un nuevo fichero de migración para generar la base de datos 
``` bash
php artisan make:migration [Nombre]
```
### :model
Crea un nuevo modelo 
``` bash
php artisan make:model [Nombre]
```
### :model junto a la migración
Crea un nuevo modelo 
``` bash
php artisan make:model [Nombre] -m
```
### :model junto a la migración y controlador 
Crea un nuevo modelo 
``` bash
php artisan make:model [Nombre] -m -r
```

### :model junto a la migración, controlador y factory 
Crea un nuevo modelo 
``` bash
php artisan make:model [Nombre] -m -r -f
##o podemos usar el flag 'a' que hace lo mismo
php artisan make:model [Nombre] -a
```

### :resource
Crea un nuevo recurso
``` bash
php artisan make:resource [Nombre]
```
### :view
Crea una nueva vista
``` bash
php artisan make:view [Nombre]
```

## Tinker
Permite usar una consola de tinker
``` bash
php artisan tinker
```