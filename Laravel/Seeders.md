
Los "seeders" en Laravel son clases que permiten poblar bases de datos con datos iniciales o de prueba. Son especialmente útiles durante el desarrollo para generar datos que te ayuden a probar y visualizar cómo funcionará tu aplicación con contenido realista. También se utilizan para inicializar la base de datos con datos necesarios para que la aplicación funcione correctamente después de su instalación, como roles de usuario, configuraciones iniciales, entre otros.

### ¿Cómo Funcionan los Seeders?

1. **Creación de Seeder**:

- Utilizamos el comando Artisan de Laravel para crear un seeder. Por ejemplo:
- Esto crea una nueva clase seeder en el directorio `database/seeders`.
``` bash
php artisan make:seeder UserSeeder
```


2. **Escribir Lógica de Seeder**:
   
- Dentro del seeder, escribimos el código que insertará datos en la base de datos. Pidemos utilitzar el constructor de consultas de Laravel o Eloquent para hacer eso.  
- Ejemplo:

    ``` php
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Hash;

class UserSeeder extends Seeder {

public function run(){         
	DB::table('users')->insert([
	'name' => 'John Doe',
	'email' => 'john@example.com',
	'password' => Hash::make('password'),
	]);     
} }
```

3. **Ejecutar Seeders**: 
   
- Para ejecutar todos los seeders, podemos usar el comando Artisan:
``` bash
php artisan db:seed
```    

- Si queremos ejecutar un seeder específico, podemos hacerlo usando:
``` bash
php artisan db:seed --class=UserSeeder
```

- Si queremos ejecutar todos los seeders después de hacer las [[Migraciones]] simplemente debemos añadir `--seed` despues de la miración:
``` bash
php artisan migrate:fresh --seed
```

4. **Llamar a otros Seeders**:
    - Dentro del método `run` del seeder `DatabaseSeeder`, puedes llamar a otros seeders para ejecutarlos en un orden específico ya que `db:seed` solo ejecuta dicho archivo.
    - Ejemplo:
```php
public function run()
{
    $this->call([
        UserSeeder::class,
        PostSeeder::class,
        // otros seeders...
    ]);
}
```
