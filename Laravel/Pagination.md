- [[#Métodos de Paginación|Métodos de Paginación]]
	- [[#Métodos de Paginación#Ejemplo con Eloquent|Ejemplo con Eloquent]]
	- [[#Métodos de Paginación#En la vista Blade:|En la vista Blade:]]
- [[#Parámetros Personalizados|Parámetros Personalizados]]
- [[#Paginación y Controladores|Paginación y Controladores]]
- [[#Personalización de la Vista de Paginación|Personalización de la Vista de Paginación]]
	- [[#Personalización de la Vista de Paginación#Ejemplo|Ejemplo]]


Laravel proporciona varios métodos de paginación que puedes utilizar directamente en tus consultas. Estos métodos crean automáticamente los enlaces de paginación y dividen los resultados en páginas más pequeñas.

## Métodos de Paginación

- `paginate()`: Este método es el más común y se utiliza para dividir los resultados en paginas de un tamaño especificado. Incluye automáticamente enlaces de paginación en la vista.
- `simplePaginate()`: Funciona de manera similar a paginate(), pero genera una paginación simplificada, que solo incluye enlaces "Anterior" y "Siguiente". Es útil cuando no necesitas conocer el número total de páginas.
- `cursorPaginate()`: Ofrece una paginación basada en cursores, que es más eficiente para conjuntos de datos grandes porque no requiere contar el número total de registros.

### Ejemplo con Eloquent
1. Paginación Básica con paginate()

```php
$usuarios = User::paginate(15);
```

- Este código devuelve 15 usuarios por página. Laravel generará automáticamente los enlaces de paginación, que puedes renderizar en tu vista.

### En la vista Blade:

```php
@foreach ($usuarios as $usuario)
    <p>{{ $usuario->name }}</p>
@endforeach

{{ $usuarios->links() }}
```

- `$usuarios->links()`: Este método renderiza los enlaces de paginación basados en Tailwind por defecto.

2. Paginación Simplificada con simplePaginate()

```php
$usuarios = User::simplePaginate(15);
```

- Este método funciona de manera similar a paginate(), pero solo muestra enlaces "Anterior" y "Siguiente" sin numeración de páginas.

3. Paginación con Cursores usando cursorPaginate()

```php
$usuarios = User::orderBy('id')->cursorPaginate(15);
```

- El método `cursorPaginate()` es ideal para paginar grandes conjuntos de datos sin necesidad de contar el número total de registros, lo que mejora el rendimiento en bases de datos grandes.

## Parámetros Personalizados

Puedes personalizar la paginación agregando parámetros adicionales a los métodos de paginación.

1. Personalizar la URL de la Página

```php
$usuarios = User::paginate(15)->withPath('custom/url/path');
```

2. Agregar Parámetros a la Query

```php
$usuarios = User::paginate(15)->appends(['sort' => 'name']);
```

Esto mantendrá el parámetro sort en todas las páginas de la paginación.
Paginación con Query Builder

3. Puedes usar los mismos métodos de paginación con el Query Builder:

```php
$posts = DB::table('posts')->paginate(10);
```

Esto funciona igual que con Eloquent, pero es útil cuando no estás trabajando con modelos.

## Paginación y Controladores

1. Es común manejar la lógica de paginación en los controladores y pasar los datos paginados a las vistas.

```php
public function index()
{
    $usuarios = User::paginate(15);
    return view('usuarios.index', compact('usuarios'));
}
```

2. En la vista usuarios/index.blade.php:

```php
@foreach ($usuarios as $usuario)
    <p>{{ $usuario->name }}</p>
@endforeach

{{ $usuarios->links() }}
```

## Personalización de la Vista de Paginación

Laravel utiliza la vista pagination::tailwind de manera predeterminada para renderizar los enlaces de paginación. Puedes personalizar esta vista o incluso crear tu propia vista de paginación.

```php
{{ $usuarios->links('nombre.de.tu.vista') }}
```

### Ejemplo 

Supongamos que estás desarrollando una aplicación que muestra una lista de artículos (posts):

1. En el controlador:

```php
public function index()
{
    $posts = Post::orderBy('created_at', 'desc')->paginate(10);
    return view('posts.index', compact('posts'));
}
```

2. En la vista posts/index.blade.php:

```php
@foreach ($posts as $post)
    <h2>{{ $post->title }}</h2>
    <p>{{ $post->excerpt }}</p>
@endforeach

{{ $posts->links() }}
```
