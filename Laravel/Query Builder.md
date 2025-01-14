- [[#1. Consultas Básicas|1. Consultas Básicas]]
- [[#2. Selección de Campos|2. Selección de Campos]]
- [[#3. Filtrado de Resultados|3. Filtrado de Resultados]]
- [[#4. Ordenar y Limitar Resultados|4. Ordenar y Limitar Resultados]]
- [[#5. Agrupación y Agregación|5. Agrupación y Agregación]]
- [[#6. Uniones (Joins)|6. Uniones (Joins)]]
- [[#7. Subconsultas|7. Subconsultas]]
- [[#8. Inserción, Actualización y Eliminación|8. Inserción, Actualización y Eliminación]]
- [[#9. Otros Métodos Útiles|9. Otros Métodos Útiles]]
- [[#10. Transacciones|10. Transacciones]]
- [[#11. Consultas Raw (SQL Puro)|11. Consultas Raw (SQL Puro)]]

 
El **Query Builder** de Laravel es una herramienta poderosa y flexible que permite construir consultas SQL de manera fluida y legible sin necesidad de escribir SQL puro. 

## 1. Consultas Básicas

Obtener todos los registros de una tabla:

``` php
DB::table('users')->get();
```

Obtener un registro específico por ID:

```php
DB::table('users')->find(1);
```

Obtener el primer registro que coincide con una condición:

```php
DB::table('users')->where('email', 'john@example.com')->first();
```

Obtener un solo valor de una columna:

```php
DB::table('users')->where('name', 'John')->value('email');
```

Obtener un array de valores de una columna:

```php
DB::table('users')->pluck('name');
//o si queremos una array asociativa
DB::table('users')->pluck('name', 'id');
```

La paginación en Laravel es una característica que facilita la gestión de grandes conjuntos de datos dividiéndolos en "páginas" más pequeñas y manejables. Este método es el más común y se utiliza para dividir los resultados en páginas de un tamaño especificado. Incluye automáticamente enlaces de paginación en la vista.

``` php
$posts = DB::table('posts')->paginate(10);
```
Este código devuelve 10 posts por página. Laravel generará automáticamente los enlaces de paginación, que se puede renderizar en la vista.

Para más información ir a [[Pagination]]

## 2. Selección de Campos

Seleccionar campos específicos:

``` php
DB::table('users')->select('name', 'email')->get();
```

Agregar columnas adicionales a una selección:

``` php
DB::table('users')->select('name')->addSelect('email')->get();
```

Eliminar duplicados:

```php
DB::table('users')->distinct()->get();
```

## 3. Filtrado de Resultados

Filtrar por una condición:

``` php
DB::table('users')->where('status', 'active')->get();
```

Filtrar por múltiples condiciones:

``` php
DB::table('users')->where('status', 'active')->where('role', 'admin')->get();
```
Filtrar por un conjunto de valores (IN):

``` php
DB::table('users')->whereIn('id', [1, 2, 3])->get();
```

Filtrar excluyendo un conjunto de valores (NOT IN):

``` php
DB::table('users')->whereNotIn('id', [1, 2, 3])->get();
```

Filtrar por rango de fechas (BETWEEN):

``` php
DB::table('users')->whereBetween('created_at', ['2023-01-01', '2023-12-31'])->get();
```

Filtrar excluyendo un rango de fechas (NOT BETWEEN):

```php
DB::table('users')->whereNotBetween('created_at', ['2023-01-01', '2023-12-31'])->get();
```

Filtrar por campos nulos (IS NULL):

```php
DB::table('users')->whereNull('email_verified_at')->get();
```
Filtrar excluyendo campos nulos (IS NOT NULL):

```php
DB::table('users')->whereNotNull('email_verified_at')->get();
```

Filtrar con condiciones anidadas (where):

```php
DB::table('users')->where(function($query) {
	$query->where('status', 'active')
		->orWhere('role', 'admin');
})->get();
```

## 4. Ordenar y Limitar Resultados

Ordenar por una columna:

```php
DB::table('users')->orderBy('name', 'asc')->get();
```

Ordenar por múltiples columnas:

```php
DB::table('users')->orderBy('name')->orderBy('age', 'desc')->get();
```

Obtener un número limitado de registros (LIMIT):

```php
DB::table('users')->limit(10)->get();
```

Saltar un número específico de registros (OFFSET):

```php
DB::table('users')->skip(5)->take(10)->get();
```

## 5. Agrupación y Agregación

Agrupar resultados (GROUP BY):

```php
DB::table('users')->groupBy('status')->get();
```

Filtrar grupos (HAVING):

```php
DB::table('users')->groupBy('status')->having('count', '>', 100)->get();
```

Contar registros:

```php
DB::table('users')->count();
```

Obtener el promedio de una columna:

```php
DB::table('users')->avg('age');
```

Obtener la suma de una columna:

```php
DB::table('users')->sum('balance');
```

Obtener el valor máximo de una columna:

```php
DB::table('users')->max('salary');
```

Obtener el valor mínimo de una columna:

```php
DB::table('users')->min('salary');
```

## 6. Uniones (Joins)

Unión básica (INNER JOIN):

``` php
DB::table('users')
    ->join('contacts', 'users.id', '=', 'contacts.user_id')
    ->select('users.*', 'contacts.phone')
    ->get();
```

Unión izquierda (LEFT JOIN):

```php
DB::table('users')
    ->leftJoin('posts', 'users.id', '=', 'posts.user_id')
    ->get();
```
Unión derecha (RIGHT JOIN):

```php
DB::table('users')
    ->rightJoin('posts', 'users.id', '=', 'posts.user_id')
    ->get();
```

Unión cruzada (CROSS JOIN):

```php
DB::table('sizes')
    ->crossJoin('colors')
    ->get();
```

Unión avanzada (JOIN WHERE):

```php
DB::table('users')
	->join('contacts', function ($join) {
		$join->on('users.id', '=', 'contacts.user_id')
            ->where('contacts.is_primary', '=', 1);
        })
    ->get();

```
## 7. Subconsultas

Subconsulta en SELECT:

```php
$latestPosts = DB::table('posts')
	            ->select('user_id', DB::raw('MAX(created_at) as last_post_created'))
                 ->groupBy('user_id');

$users = DB::table('users')
	        ->joinSub($latestPosts, 'latest_posts', function($join) {
	            $join->on('users.id', '=', 'latest_posts.user_id');
        })
    ->get();
```

Subconsulta en WHERE:

```php
DB::table('users')
    ->whereExists(function ($query) {
        $query->select(DB::raw(1))
            ->from('orders')
            ->whereRaw('orders.user_id = users.id');
    })
    ->get();
```

Subconsulta en FROM:

``` php
$subquery = DB::table('posts')
	        ->select(DB::raw('COUNT(*) as post_count, user_id'))
            ->groupBy('user_id');

$users = DB::table(DB::raw("({$subquery->toSql()}) as sub"))
	        ->mergeBindings($subquery)
	        ->get();
```

## 8. Inserción, Actualización y Eliminación

Insertar un registro:

```php
DB::table('users')->insert([
    'name' => 'John Doe',
    'email' => 'john@example.com',
    'password' => bcrypt('password'),
]);
```

Insertar y obtener el ID del registro:

```php
$id = DB::table('users')->insertGetId([
    'name' => 'John Doe',
    'email' => 'john@example.com',
    'password' => bcrypt('password'),
]);
```

Actualizar un registro:

```php
DB::table('users')
    ->where('id', 1)
    ->update(['name' => 'John Smith']);
```

`upsert` es una combinación de las operaciones "insert" y "update". La idea es que si un registro con una clave única ya existe en la base de datos, entonces `upsert` actualiza ese registro con los nuevos datos. Si no existe, se inserta un nuevo registro

``` php
DB::table('tabla')->upsert(
    $values, // Array de datos a insertar/actualizar
    $uniqueBy, // Columnas que definen la unicidad
    $update // Columnas a actualizar si el registro ya existe
);
```

Ejemplo de uso: 
``` php
DB::table('products')->upsert([
    ['name' => 'Product A', 'category' => 'Category 1', 'price' => 100, 'stock' => 50],
    ['name' => 'Product B', 'category' => 'Category 1', 'price' => 150, 'stock' => 60],
    ['name' => 'Product C', 'category' => 'Category 2', 'price' => 200, 'stock' => 70],
], ['name', 'category'], ['price', 'stock']);

```

Incrementar o decrementar valores:

```php
DB::table('users')->increment('balance', 100);
DB::table('users')->decrement('balance', 50);
```

Eliminar un registro:

```php
DB::table('users')->where('id', 1)->delete();
```

Eliminar todos los registros:

```php
DB::table('users')->truncate();
```

## 9. Otros Métodos Útiles

Comprobar la existencia de un registro:

```php
DB::table('users')->where('email', 'john@example.com')->exists();
```

Comprobar la no existencia de un registro:

```php
DB::table('users')->where('email', 'john@example.com')->doesntExist();
```

Bloquear consultas para evitar conflicto de transacciones:

```php
DB::table('users')->where('id', 1)->lockForUpdate()->get();
DB::table('users')->where('id', 1)->sharedLock()->get();
```

## 10. Transacciones

Realizar una transacción:

```php
DB::transaction(function () {
    DB::table('users')->update(['balance' => 0]);
    DB::table('orders')->delete();
});
```

Control manual de transacciones:

```php
DB::beginTransaction();
try {
    DB::table('users')->update(['balance' => 0]);
    DB::table('orders')->delete();

    DB::commit();
} catch (\Exception $e) {
    DB::rollBack();
    throw $e;
}
```

## 11. Consultas Raw (SQL Puro)

Consulta SQL Raw:

```php
DB::select('SELECT * FROM users WHERE active = ?', [1]);
```

Consulta raw en cláusulas WHERE:

```php
DB::table('users')
    ->whereRaw('age > ? AND votes = 100', [25])
    ->get();
```

Consulta raw en ORDER BY:

```php
DB::table('users')
    ->orderByRaw('updated_at - created_at DESC')
    ->get();
```
