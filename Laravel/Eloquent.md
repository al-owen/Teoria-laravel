- [[#Características Principales de Eloquent|Características Principales de Eloquent]]
- [[#Métodos básicos|Métodos básicos]]
	- [[#Métodos básicos#1. Métodos de Consulta|1. Métodos de Consulta]]
	- [[#Métodos básicos#2. Métodos de Inserción y Creación|2. Métodos de Inserción y Creación]]
	- [[#Métodos básicos#3. Métodos de Actualización|3. Métodos de Actualización]]
	- [[#Métodos básicos#4. Métodos de Eliminación|4. Métodos de Eliminación]]
	- [[#Métodos básicos#5. Métodos de Paginación|5. Métodos de Paginación]]
	- [[#Métodos básicos#6. Métodos de Eager Loading|6. Métodos de Eager Loading]]
	- [[#Métodos básicos#7. Scopes|7. Scopes]]
	- [[#Métodos básicos#8. Otros Métodos Útiles|8. Otros Métodos Útiles]]


**Eloquent** es el **ORM** (Object-Relational Mapping) de Laravel. Un ORM es una técnica de programación que permite mapear, relacionar y trabajar con bases de datos relacionales a través de objetos en el lenguaje de programación. En lugar de escribir consultas SQL directamente, podemos interactuar con la base de datos usando clases y métodos en PHP, lo que hace que el trabajo con bases de datos sea más intuitivo y orientado a objetos.

## Características Principales de Eloquent

1. **Modelos**: En Eloquent, cada tabla de la base de datos está representada por un "modelo" de PHP. Este modelo es una clase que hereda de  `Illuminate\Database\Eloquent\Model`, y cada instancia de este modelo representa una fila en la tabla.

```php
class User extends Model
{
    // El modelo User se asocia con la tabla 'users'
}
```

2. **Convenios de Nombres**: Eloquent sigue convenciones de nombres predefinidas para que no tengas que configurarlo todo manualmente. Por ejemplo, asume que la tabla asociada con un modelo se llama en plural (ejemplo: el modelo `User` corresponde a la tabla `users`). **Esto es muy importante tenerlo en cuenta, ya que Laravel funciona muy bien cuando se respeta las convenciones, pero si no, puede dar problemas.**

3. **Consultas ORM**: Eloquent permite realizar consultas a la base de datos utilizando métodos de PHP en lugar de escribir SQL. Puedes seleccionar, insertar, actualizar y eliminar registros de manera fluida.

```php
$users = User::where('status', 'active')->get();
```

4. **[[Relaciones]]**: Eloquent maneja las relaciones entre tablas de manera natural, como relaciones uno a uno, uno a muchos, muchos a muchos, y relaciones polimórficas. Estas relaciones se definen como métodos en el modelo.

```php
class Post extends Model
{
	public function user()
	{
		return $this->belongsTo(User::class);
	}
}
```

5. **Carga Perezosa y Eager Loading**: Eloquent permite cargar relaciones automáticamente (`eager loading`), o puede cargar relaciones solo cuando se necesiten (`lazy loading`).

```php
// Eager loading
$posts = Post::with('user')->get();
foreach ($posts as $post) {
    echo $post->user->name;
}
// Carga perezosa (lazy loading)
$posts = Post::all();
foreach ($posts as $post) {
    echo $post->user->name;
}
```

6. **Mutadores y Accesores**: Eloquent permite definir mutadores y accesores para manipular atributos de los modelos antes de que sean guardados o después de que sean recuperados.

```php
class User extends Model
{
    // Accesor: Modifica el formato del nombre al recuperarlo
    public function getNameAttribute($value)
    {
		return ucfirst($value);
    }

    // Mutador: Siempre almacena la contraseña encriptada
    public function setPasswordAttribute($value)
    {
		$this->attributes['password'] = bcrypt($value);
    }
}
```

7. **Timestamps Automáticos**: Eloquent maneja automáticamente las columnas `created_at` y `updated_at`, que se actualizan cada vez que se crea o modifica un registro.

8. **Scopes Globales y Locales**: Eloquent permite definir "scopes" o alcances para reutilizar fragmentos de consultas.

```php
// Scope Local
class User extends Model
{
    public function scopeActive($query)
    {
        return $query->where('status', 'active');
    }
}
// Uso del Scope
$activeUsers = User::active()->get();
```

9. Serialización: Eloquent permite serializar los modelos a JSON o arrays fácilmente, lo que es muy útil para APIs.

```php
$user = User::find(1);
return $user->toJson();
```

## Métodos básicos

### 1. Métodos de Consulta

`all()`: Recupera todos los registros de la tabla asociada al modelo.

```php
$users = User::all();
```

`find($id)`: Recupera un registro por su clave primaria.

```php
$user = User::find(1);
```

`findOrFail($id):` Recupera un registro por su clave primaria o lanza una excepción si no lo encuentra.

```php
$user = User::findOrFail(1);
```

`first():` Recupera el primer registro que coincide con la consulta.

```php
$user = User::where('status', 'active')->first();
```

`firstOrFail():` Recupera el primer registro que coincide con la consulta o lanza una excepción si no lo encuentra.

```php
$user = User::where('status', 'active')->firstOrFail();
```

`get():` Recupera todos los registros que coinciden con la consulta.

```php
$activeUsers = User::where('status', 'active')->get();
```

`pluck($column):` Recupera los valores de una sola columna de todos los registros que coinciden con la consulta.

```php
$emails = User::pluck('email');
```

`select($columns):` Especifica las columnas que se deben recuperar.

```php
$users = User::select('name', 'email')->get();
```

`count():` Cuenta el número de registros que coinciden con la consulta.

```php
$count = User::where('status', 'active')->count();
```

`max($column):` Recupera el valor máximo de una columna.

```php
$maxSalary = Employee::max('salary');
```

`min($column):` Recupera el valor mínimo de una columna.

```php
$minSalary = Employee::min('salary');
```

`avg($column):` Calcula el promedio de una columna.

```php
$averageSalary = Employee::avg('salary');
```

`sum($column):` Calcula la suma de una columna.

```php
$totalSalary = Employee::sum('salary');
```

`exists():` Verifica si existen registros que coincidan con la consulta.

```php
$exists = User::where('email', 'john@example.com')->exists();
```

`dosentExist():` Verifica si no existen registros que coincidan con la consulta.

```php
$doesntExist = User::where('email', 'john@example.com')->doesntExist();
```

### 2. Métodos de Inserción y Creación

`create(array $attributes):` Crea y guarda un nuevo registro en la base de datos.

```php
$user = User::create([
	'name' => 'John Doe', 
	'email' => 'john@example.com'
]);
```

`firstOrCreate(array $attributes, array $values = []):` Recupera el primer registro que coincide con los atributos, o lo crea si no existe.

```php
$user = User::firstOrCreate(
	['email' => 'john@example.com'], 
	['name' => 'John Doe']
);
```

`firstOrNew(array $attributes, array $values = []):` Recupera el primer registro que coincide con los atributos, o crea una nueva instancia sin guardarla.

```php
$user = User::firstOrNew(
	['email' => 'john@example.com'], 
	['name' => 'John Doe']
);
$user->save();
```

`updateOrCreate(array $attributes, array $values = []):` Actualiza un registro existente o lo crea si no existe.

```php
$user = User::updateOrCreate(
	['email' => 'john@example.com'], 
	['name' => 'John Smith']
);
```

### 3. Métodos de Actualización

`update(array $attributes):` Actualiza uno o más registros en la base de datos.

```php
User::where('status', 'inactive')->update(['status' => 'active']);
```

`increment($column, $amount = 1, array $extra = []):` Incrementa el valor de una columna para uno o más registros.

```php
User::where('id', 1)->increment('points');
```

`decrement($column, $amount = 1, array $extra = []):` Decrementa el valor de una columna para uno o más registros.

```php
User::where('id', 1)->decrement('points');
```

### 4. Métodos de Eliminación

`delete():` Elimina uno o más registros que coinciden con la consulta.

```php
User::where('status', 'inactive')->delete();
```

`destroy($ids):` Elimina registros por su clave primaria.

```php
User::destroy([1, 2, 3]);
```

`forceDelete():` Elimina uno o más registros permanentemente, incluso si se están utilizando eliminaciones suaves (soft deletes).

```php
User::where('status', 'inactive')->forceDelete();
```

### 5. Métodos de Paginación

 `paginate($perPage = null, $columns = ['*'], $pageName = 'page', $page = null):` Paginación de resultados.

```php
$users = User::paginate(15);
```

`simplePaginate($perPage = null, $columns = ['*'], $pageName = 'page', $page = null): `Paginación simplificada sin enlaces de numeración de página.

```php
$users = User::simplePaginate(15);
```

`cursorPaginate($perPage = null, $columns = ['*'], $cursorName = 'cursor', $cursor = null):` Paginación basada en cursores.

```php
$users = User::orderBy('id')->cursorPaginate(15);
```

### 6. Métodos de Eager Loading

`with($relations):` Carga ansiosa de relaciones para evitar el problema de la consulta N+1.

```php
$users = User::with('posts')->get();
```

`load($relations):` Carga ansiosa de relaciones en una instancia existente de un modelo.

```php
$user->load('posts');
```

### 7. Scopes

`scopeNombre($query):` Define un "scope" local que puede ser reutilizado en consultas.

```php
class User extends Model {
    public function scopeActive($query) {
        return $query->where('status', 'active');
    }
}

// Uso del scope
$activeUsers = User::active()->get();
```

`withoutGlobalScope($scope):` Excluye un "scope" global en una consulta específica.

```php
User::withoutGlobalScope(ActiveScope::class)->get();
```

`withGlobalScope($identifier, $scope):` Aplica un "scope" global adicional a la consulta.

```php
User::withGlobalScope('age', function (Builder $builder) {
	$builder->where('age', '>=', 18);
})->get();
```

### 8. Otros Métodos Útiles

`fresh():` Recarga el modelo desde la base de datos.

```php
$user->fresh();
```

`refresh():` Recarga el modelo y todas sus relaciones cargadas desde la base de datos.

```php
$user->refresh();
```

`touch():` Actualiza las columnas created_at y updated_at del modelo.

```php
$user->touch();
```

`toArray():` Convierte el modelo y sus relaciones en un array.

```php
$userArray = $user->toArray();
```

`toJson():` Convierte el modelo y sus relaciones en JSON.

```php
return $user->toJson();
```
