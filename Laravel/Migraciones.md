- [[#¿Qué son las Migraciones en Laravel?|¿Qué son las Migraciones en Laravel?]]
- [[#¿Cómo Funcionan las Migraciones?|¿Cómo Funcionan las Migraciones?]]
- [[#Crear una Migración|Crear una Migración]]
- [[#Estructura de una Migración|Estructura de una Migración]]
	- [[#Estructura de una Migración#Explicación de la Estructura|Explicación de la Estructura]]
	- [[#Estructura de una Migración#Ejecutar Migraciones|Ejecutar Migraciones]]
	- [[#Estructura de una Migración#Revertir Migraciones|Revertir Migraciones]]
	- [[#Estructura de una Migración#Migraciones Avanzadas|Migraciones Avanzadas]]
		- [[#Migraciones Avanzadas#1. **Modificación de Tablas Existentes:**|1. **Modificación de Tablas Existentes:**]]
		- [[#Migraciones Avanzadas#2. **Deshacer y Resetear Todas las Migraciones:**|2. **Deshacer y Resetear Todas las Migraciones:**]]
	- [[#Estructura de una Migración#Seeds y Migraciones|Seeds y Migraciones]]

## ¿Qué son las Migraciones en Laravel?

Las migraciones en Laravel son una herramienta que permite gestionar y versionar la estructura de la base de datos de una aplicación de manera organizada y controlada. Las migraciones actúan como un control de versiones para la base de datos, similar a cómo el control de versiones como Git gestiona el código fuente.

## ¿Cómo Funcionan las Migraciones?

Las migraciones se escriben en archivos PHP que definen las operaciones que se deben realizar en la base de datos. Cada archivo de migración contiene dos métodos principales:

- **`up()`**: Define las operaciones que se deben realizar cuando se ejecuta la migración (normalmente, la creación de tablas o la adición de columnas).
- **`down()`**: Define cómo revertir esas operaciones, permitiendo deshacer la migración si es necesario (por ejemplo, eliminando la tabla o columna).
## Crear una Migración

Laravel permite la creación de migraciones utilizando Artisan en la terminal. Para crear una nueva migración, podemos usar el siguiente comando:

``` sh
php artisan make:migration create_users_table
```

Este comando genera un archivo de migración en el directorio `database/migrations` con un nombre basado en la fecha y hora, seguido del nombre que le pasamos (`create_users_table`). Si seguimos las convenciones establecidas por laravel, este nos generará ayudas o archivos construidos con los datos mínimos, por ejemplo, en este caso si escribimos `create_nombreTabla_table` nos creará una estructura básica de una tabla, en caso contrario solo crearia el archivo con las 2 funciones.

## Estructura de una Migración

Un archivo de migración generado se verá algo así:
[[Métodos para hacer migraciones|Aquí se puede ver un listado]] de todos los métodos que se pueden usar para hacer migraciones. 

``` php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateUsersTable extends Migration
{
    /**
     * Ejecutar la migración.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('users', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('email')->unique();
            $table->timestamp('email_verified_at')->nullable();
            $table->string('password');
            $table->rememberToken();
            $table->timestamps();
        });
    }

    /**
     * Revertir la migración.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('users');
    }
}
```

### Explicación de la Estructura

- **`Schema::create`**: Crea una nueva tabla en la base de datos. El primer argumento es el nombre de la tabla, y el segundo es una Closure que recibe una instancia de `Blueprint` para definir las columnas de la tabla.
- **`$table->id()`**: Crea una columna `id` como clave primaria autoincremental.
- **`$table->string('name')`**: Crea una columna de tipo `VARCHAR` para almacenar el nombre del usuario.
- **`$table->string('email')->unique()`**: Crea una columna de tipo `VARCHAR` para el correo electrónico del usuario, asegurando que sea único.
- **`$table->timestamps()`**: Crea las columnas `created_at` y `updated_at`, que Laravel maneja automáticamente.
- **`Schema::dropIfExists('users')`**: Elimina la tabla `users` si existe. Esto se usa en el método `down()` para revertir la migración.

### Ejecutar Migraciones

Para ejecutar todas las migraciones pendientes (que no se han ejecutado previamente), usamos el comando:

``` sh
php artisan migrate
```
Esto ejecuta el método `up()` de todas las migraciones, aplicando los cambios a la base de datos.

### Revertir Migraciones

Si necesitas revertir una migración (deshacer los cambios), puedes usar:
```sh
php artisan migrate:rollback
```
Esto ejecutará el método `down()` de las migraciones más recientes que se ejecutaron.

### Migraciones Avanzadas

#### 1. **Modificación de Tablas Existentes:**

Si necesitas modificar una tabla existente, puedes crear una nueva migración para hacerlo:
```sh
php artisan make:migration add_profile_photo_to_users_table --table=users
```
Esto generará una migración que puede agregar o modificar columnas en la tabla `users`.

```php
public function up()
{
    Schema::table('users', function (Blueprint $table) {
        $table->string('profile_photo')->nullable();
    });
}

public function down()
{
    Schema::table('users', function (Blueprint $table) {
        $table->dropColumn('profile_photo');
    });
}
```

#### 2. **Deshacer y Resetear Todas las Migraciones:**

Si quieres revertir todas las migraciones y luego volver a ejecutarlas, puedes usar:
```sh
php artisan migrate:reset
php artisan migrate
```

O todo en un solo paso:
```sh
php artisan migrate:refresh
```

### Seeds y Migraciones

Laravel permite ejecutar [[Seeders|_seeders_]] junto con las migraciones, que son clases para llenar la base de datos con datos de prueba o datos iniciales:
```sh
php artisan migrate --seed
```

