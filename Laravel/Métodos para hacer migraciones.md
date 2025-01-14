- [[#Métodos para Definir Columnas|Métodos para Definir Columnas]]
- [[#Métodos para Definir Relaciones y Claves Foráneas|Métodos para Definir Relaciones y Claves Foráneas]]
- [[#Métodos para Modificar Columnas|Métodos para Modificar Columnas]]
- [[#Métodos para Definir Índices|Métodos para Definir Índices]]
- [[#Métodos de Estructura de la Tabla|Métodos de Estructura de la Tabla]]

### Métodos para Definir Columnas

1. **`$table->id();`**
    - Crea una columna `id` de clave primaria autoincremental. Es un atajo para `bigIncrements('id')`.
      
2. **`$table->bigIncrements('id');`**
    - Crea una columna `BIGINT` autoincremental como clave primaria.
      
3. **`$table->increments('id');`**
    - Crea una columna `INT` autoincremental como clave primaria.
      
4. **`$table->string('nombre', 255);`**
    - Crea una columna de tipo `VARCHAR` con una longitud opcional (por defecto 255).
      
5. **`$table->text('descripcion');`**
    - Crea una columna de tipo `TEXT`.
      
6. **`$table->integer('edad');`**
    - Crea una columna de tipo `INT`.
      
7. **`$table->bigInteger('visitas');`**
    - Crea una columna de tipo `BIGINT`.
      
8. **`$table->boolean('activo');`**
    - Crea una columna de tipo `BOOLEAN`.
      
9. **`$table->date('fecha_nacimiento');`**
    - Crea una columna de tipo `DATE`.
      
10. **`$table->dateTime('publicado_en');`**
    - Crea una columna de tipo `DATETIME`.
      
11. **`$table->timestamp('creado_en');`**
    - Crea una columna de tipo `TIMESTAMP`.
      
12. **`$table->timestamps();`**
    - Crea columnas `created_at` y `updated_at` que Laravel manejará automáticamente.
      
13. **`$table->softDeletes();`**
    - Crea una columna `deleted_at` para manejar el borrado suave (soft deletes).
      
14. **`$table->float('precio', 8, 2);`**
    - Crea una columna de tipo `FLOAT` con precisión y escala opcionales.
      
15. **`$table->decimal('balance', 8, 2);`**
    - Crea una columna de tipo `DECIMAL` con precisión y escala opcionales.
      
16. **`$table->enum('estado', ['activo', 'inactivo']);`**
    - Crea una columna `ENUM` con valores específicos.
      
17. **`$table->json('datos');`**
    - Crea una columna de tipo `JSON`.
      
18. **`$table->uuid('uuid');`**
    - Crea una columna de tipo `UUID` (identificador único universal).
      
19. **`$table->binary('archivo');`**
    - Crea una columna de tipo `BLOB` para almacenar datos binarios.
      
20. **`$table->char('codigo', 4);`**
    - Crea una columna de tipo `CHAR` con una longitud específica.

### Métodos para Definir Relaciones y Claves Foráneas

1. **`$table->foreignId('usuario_id');`**
    - Crea una columna `BIGINT` no firmado para claves foráneas. Es un atajo para `unsignedBigInteger`.
      
2. **`$table->foreignId('usuario_id')->constrained();`**
    - Crea una columna de clave foránea que automáticamente referencia la columna `id` de la tabla `usuarios`.
      
3. **`$table->foreign('usuario_id')->references('id')->on('usuarios');`**
    - Define una clave foránea de manera explícita.
      
4. **`$table->unsignedBigInteger('usuario_id');`**
    - Crea una columna `BIGINT` no firmado para una clave foránea.
      
5. **`$table->unsignedInteger('producto_id');`**
    - Crea una columna `INT` no firmado para una clave foránea.
      
6. **`$table->unsignedTinyInteger('nivel');`**
    - Crea una columna `TINYINT` no firmado.
      
7. **`$table->morphs('imagenable');`**
    - Crea dos columnas (`imagenable_id` y `imagenable_type`) para una relación polimórfica.
      
8. **`$table->nullableMorphs('imagenable');`**
    - Crea dos columnas (`imagenable_id` y `imagenable_type`) para una relación polimórfica, permitiendo valores `NULL`.

### Métodos para Modificar Columnas

1. **`$table->nullable();`**
    - Permite que una columna acepte valores `NULL`.
    
``` php
$table->string('nombre')->nullable();
```

2. **`$table->default('valor');`**
	- Establece un valor predeterminado para una columna.
```php
$table->integer('edad')->default(18);
```

3. **`$table->change();`**
	- Modifica una columna existente. Debe usarse en una migración separada.
```php
$table->string('nombre', 100)->change();
```

4. **`$table->unique('email');`**
	- Establece una restricción de unicidad en una columna o grupo de columnas.
```php
$table->string('email')->unique();
```

5. **`$table->index('apellido');`**
	- Crea un índice en una columna para optimizar las consultas.
```php
$table->index('apellido');
```

6. **`$table->dropColumn('columna');`**
	- Elimina una columna de la tabla.
```php
$table->dropColumn('nombre');
```

7. **`$table->dropUnique(['email']);`**
	- Elimina una restricción de unicidad.
```php
$table->dropUnique(['email']);
```

8. **`$table->dropForeign(['usuario_id']);`**
	- Elimina una clave foránea.
```php
$table->dropForeign(['usuario_id']);
```

9. **`$table->dropIfExists('nombre');`**
	- Elimina una columna si existe.
```php
$table->dropIfExists('nombre');
```

### Métodos para Definir Índices

1. **`$table->primary('id');`**
	- Define la columna como clave primaria.
```php
$table->primary('id');
```

2. **`$table->unique('email');`**
	- Define un índice único para una columna.
```php
$table->unique('email');
```

3. **`$table->index(['apellido', 'nombre']);`**
	- Define un índice compuesto.
```php
$table->index(['apellido', 'nombre']);
```

4. **`$table->fullText('contenido');`**
	- Crea un índice de texto completo en una columna.
```php
$table->fullText('contenido');
```

### Métodos de Estructura de la Tabla

1. **`$table->engine = 'InnoDB';`**    
    - Especifica el motor de almacenamiento para la tabla (como `InnoDB` o `MyISAM`).
      
2. **`$table->charset = 'utf8mb4';`**
    - Establece el conjunto de caracteres de la tabla.
      
3. **`$table->collation = 'utf8mb4_unicode_ci';`**
    - Establece la collation de la tabla.