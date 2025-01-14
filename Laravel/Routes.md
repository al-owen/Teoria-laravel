- [[#Rutas básicas|Rutas básicas]]
	- [[#Rutas básicas#Rutas de multiples métodos|Rutas de multiples métodos]]
- [[#Grupo de rutas|Grupo de rutas]]
	- [[#Grupo de rutas#Características de los Grupos de Rutas|Características de los Grupos de Rutas]]
- [[#Resources|Resources]]
	- [[#Resources#Personalización de Rutas de Recursos:|Personalización de Rutas de Recursos:]]
- [[#Nombres de ruta|Nombres de ruta]]
	- [[#Nombres de ruta#Ventajas de Usar Nombres de Ruta|Ventajas de Usar Nombres de Ruta]]
	- [[#Nombres de ruta#Ejemplo de Uso|Ejemplo de Uso]]
		- [[#Ejemplo de Uso#Definición de la Ruta|Definición de la Ruta]]
		- [[#Ejemplo de Uso#Uso del Nombre de Ruta|Uso del Nombre de Ruta]]
	- [[#Nombres de ruta#Modificación de rutas|Modificación de rutas]]
- [[#Limitar las rutas|Limitar las rutas]]
	- [[#Limitar las rutas#Expresiones regulares|Expresiones regulares]]
		- [[#Expresiones regulares#Ejemplos:|Ejemplos:]]
	- [[#Limitar las rutas#Métodos|Métodos]]
	- [[#Limitar las rutas#Uso avanzado:|Uso avanzado:]]

Las rutas en Laravel sirven para definir cómo se manejan las solicitudes HTTP entrantes. Al definir rutas, asocias URL con acciones específicas o vistas en tu aplicación. Cada ruta puede apuntar a un controlador y una función específicos o devolver una vista directamente.

## Rutas básicas

Ejemplo de una ruta básica en el archivo `routes/web.php`:
``` php
//Ruta basica sin parámetros
Route::get('/home', function () {
	return 'Welcome Home!'; 
});

//Ruta con parámetros
Route::get('/movies/{movie}', function ($movie) {
	return "The book selected is: $movie"; 
});

//Ruta con parámetros opcionales "?" 
[Route::get('/movies/{movie}/{category?}', function ($movie, $category = null) {
	return "The book selected is: $movie and its category is: $category"; 
});](<Route::get('/movies/{movie}/{category?}', function ($movie, $category = null) {
    if ($category) {
        return "The book selected is: $movie and its category is: $category";
    }
	return "The book selected is: $movie";
});>)
```

Este código especifica que cuando un usuario visite `http://tuapp.com/home`, Laravel devolverá el texto "Welcome Home!". Las rutas son fundamentales para dirigir el tráfico en tu aplicación hacia el contenido o funcionalidad correcta.

Las rutas suelen apuntar a [[Controllers|controladores]], para separar la lógica de la aplicación:

``` php
Route::get('/user', [UserController::class, 'showAll']);
Route::get('/movies/{movie}', [MovieController::class, "show"]);
```

Existen varios tipos de rutas con diferentes métodos:

- `get`: Recuperar recursos (lectura).
``` php
Route::get($uri, $callback);
```
- `post`: Crear un recurso (escritura).
``` php
Route::post($uri, $callback); 
```
- `put`: Actualizar un recurso completamente.
``` php
Route::put($uri, $callback);
```
- `patch`: Actualizar parcialmente un recurso.
``` php
Route::patch($uri, $callback);
```
- `delete`: Eliminar un recurso.
``` php
Route::delete($uri, $callback);
```
- `options`: Obtener información sobre las capacidades de comunicación del recurso o servidor.
``` php
Route::options($uri, $callback);
```

### Rutas de multiples métodos

`Route::match` es un método que se utiliza en Laravel para definir una ruta que responda a múltiples métodos HTTP. Es útil cuando necesitamos que una misma ruta responda a diferentes tipos de solicitudes, como `GET` y `POST`, por ejemplo.

``` php
Route::match(['GET', 'POST'], '/ruta', [Controlador::class, 'metodo']);
```

- **Primer parámetro:** Un array que contiene los métodos HTTP a los que la ruta responderá (como `GET`, `POST`, `PUT`, etc.).
- **Segundo parámetro:** La URI (la ruta) que se desea registrar.
- **Tercer parámetro:** El controlador o Closure que manejará la solicitud cuando se acceda a esa ruta.

## Grupo de rutas

Muchas veces nos encontraremos con que tenemos varias rutas que apuntan al mismo controlador, si bien, podemos crear las rutas una debajo de otra a veces es interesante agrupar las rutas, esto hace el código más legible, y a su vez más organizado. Para ello debemos usar el método `controller` y después agruparlo con el método `group`. 

``` php
Route::controller(MovieController::class)->group(function(){
	Route::get('/movies', "index");
	Route::get('/movies/create', "create");
	Route::post('/movies', "store");
	Route::get('/movies/{movie}', "show");
	Route::get('/movies/{movie}/edit', "edit");
    Route::put('/movies/{movie}', "update");
	Route::delete('/movies/{movie}', "destroy");
});
```
  
de esta manera. nos ahorramos tener que indicar el controlador en cada ruta, nos limitamos a expresar solo la ruta y el método necesario.
### Características de los Grupos de Rutas

1. **Middleware:** Podemos aplicar uno o varios middleware a un grupo de rutas para asegurar que ciertas condiciones o comportamientos se apliquen a todas las rutas dentro del grupo.
   
``` php
Route::middleware(['auth', 'verified'])->group(function () {
    Route::get('/movies', [PerfilController::class, 'index']);
    Route::post('/movies', [PerfilController::class, 'store']);
});
```

- En este ejemplo, las rutas `/perfil` tanto para `GET` como `POST` solo serán accesibles para usuarios autenticados y verificados, ya que ambas rutas comparten los middleware `auth` y `verified`.

2. **Prefijo de URL:** Podemos agregar un prefijo común a todas las rutas dentro del grupo. Esto es útil, por ejemplo, para agrupar rutas de la API o rutas de un panel administrativo.

``` php
Route::prefix('admin')->group(function () {
    Route::get('/dashboard', [AdminController::class, 'dashboard']);
    Route::get('/usuarios', [AdminController::class, 'usuarios']);
});
```
- Las rutas generadas serán `/admin/dashboard` y `/admin/usuarios`.

3. **Nombre de Rutas:** Es posible aplicar un prefijo común a los nombres de las rutas dentro de un grupo. Esto es útil para organizar las rutas en diferentes secciones de la aplicación.

``` php
Route::name('admin.')->group(function () {
    Route::get('/dashboard', [AdminController::class, 'dashboard'])
	    ->name('dashboard');
    Route::get('/usuarios', [AdminController::class, 'usuarios'])
	    ->name('usuarios');
});
```
- Las rutas ahora se llamarán `admin.dashboard` y `admin.usuarios`.

## Resources

Dado que Laravel se utiliiza habitualmente con CRUD existen una serie de [[Rutas CRUD|rutas predeterminadas]] que podemos simplificar en una linea:
``` php
Route::resource("movies", MovieController::class);
```

esta línia genera automáticamente las rutas necesarias para hacer un CRUD básico y además si usamos el [[Controllers |controlador]] con el flag `-r` entonces ya tenemos creado tanto los métodos como las [[Rutas CRUD]].

Por lo tanto, este comando generará automáticamente las siguientes rutas:

- **GET** `/movies` - Muestra una lista de todos los posts (método `index`).
- **GET** `/movies/create` - Muestra un formulario para crear un nuevo post (método `create`).
- **POST** `/movies` - Almacena un nuevo post (método `store`).
- **GET** `/movies/{movie}` - Muestra un post específico (método `show`).
- **GET** `/movies/{movie}/edit` - Muestra un formulario para editar un post existente (método `edit`).
- **PUT/PATCH** `/movies/{movie}` - Actualiza un post existente (método `update`).
- **DELETE** `/movies/{movie}` - Elimina un post específico (método `destroy`).

### Personalización de Rutas de Recursos:

1. **Limitar las Acciones de un Recurso:

	En el caso de que no queramos generar todas las rutas, podemos hacerlo simplemente especificando que rutas queremos obviar con el método `only()` o `except()`, por ejemplo:

``` php
Route::resource('movies', MovieController::class)->only(['index', 'show']);
``` 

Esto solo generará las rutas para las acciones `index` y `show`.
O bien, podemos excluir algunas rutas:

``` php
Route::resource('movies', MovieController::class)->except(['destroy']);
```

aquí se generarán todas las rutas excepto la de `destroy`.

2. **Personalizar Nombres de Rutas:**

	Laravel también nos permite personalizar los nombres de las rutas generadas por `resources`.

``` php
Route::resource('movies', MovieController::class)->names([
    'create' => 'movies.build',
    'edit' => 'movies.modify',
]);
```

En este caso, la ruta `/posts/create` se llamará `posts.build` y la ruta `/posts/{post}/edit` se llamará `posts.modify`.

3. **Especificar Parámetros:**
	Podemos especificar qué nombre tendrá el parámetro dinámico `{post}` en las rutas generadas.

``` php
Route::resource('posts', PostController::class)->parameters([
    'posts' => 'post_slug'
]);
```

Ahora, el parámetro en las rutas será `post_slug` en lugar de `post`.

## Nombres de ruta

En Laravel, el método `name()` utilizado en la definición de rutas, sirve para asignar un "nombre" a la ruta. Estos nombres de ruta nos dan una manera fácil y conveniente de referenciar rutas en diferentes partes de una aplicación, como en las vistas, los controladores o al generar URLs.

### Ventajas de Usar Nombres de Ruta

1. **Claridad y Legibilidad**: Los nombres de ruta pueden hacer que el código sea más legible. Al ver un nombre de ruta, es más fácil entender a qué parte de la aplicación se está haciendo referencia.
2. **Mantenimiento**: Si necesitamos cambiar la URL de una ruta, podemos hacerlo en el archivo de rutas sin tener que modificar todas las referencias a esa ruta en la aplicación. Mientras el nombre de la ruta se mantenga igual, todas las referencias a ella seguirán funcionando.
3. **Generación de URLs**: Puedes usar la función helper `route()` de Laravel para generar URLs a tus rutas nombradas. Esto es útil en las vistas para crear enlaces, en los controladores para redireccionamientos, o incluso en archivos de configuración o servicios.
4. **Consistencia**: Los nombres de ruta ayudan a mantener la consistencia en la forma en que se generan y se manejan las URLs en toda la aplicación.

### Ejemplo de Uso

#### Definición de la Ruta

En el archivo de rutas (`web.php`), podríamos tener algo como esto:

``` php
Route::get('/movies/{id}', [MovieController::class, 'show'])->name('movies.show');
```

Aquí, la ruta que lleva al método `show` del `MovieController` se nombra como `movies.show`.
#### Uso del Nombre de Ruta
Luego, podemos referenciar esta ruta por su nombre en cualquier lugar de la aplicación:

- **En una Vista Blade para Crear un Enlace**:
``` html
<a href="{{ route('movies.show', ['id' => $movie->id]) }}">Ver Película</a>
```
- **En un Controlador para una Redirección**:
``` php
return redirect()->route('movies.show', ['id' => $movie->id]);
```

### Modificación de rutas
 Si queremos "traducir" las rutas generadas con resource, vamos al fichero `App\Providers\RouteServiceProvider` y mediante el método `Route::resourceVerbs` podemos modificar el valor que viene por defecto. Por ejemplo en el caso de las rutas `create` y `edit`, si lo queremos pasar a español, tendríamos que ir al fichero anterior y en el método `boot` añadimos nuestras traducciones.

``` php
/**
 * Define your route model bindings, pattern filters, etc.
 */
public function boot(): void
{
    Route::resourceVerbs([
        'create' => 'crear',
        'edit' => 'editar',
    ]);
    // ...
}
```

## Limitar las rutas

### Expresiones regulares
En Laravel, podemos limitar las rutas utilizando [[Expresiones regulares]] a través del método `where` en las rutas. Esto es útil cuando necesitamos asegurar que los parámetros pasados en la URL cumplan con un formato específico, como asegurarnos de que un ID sea numérico o que un nombre solo contenga letras.

``` php
Route::get('/ruta/{parametro}', [Controlador::class, 'metodo'])
    ->where('parametro', 'expresión_regular');
```

- **Primer parámetro (`parametro`):** El nombre del parámetro de la ruta al que queremos aplicar la restricción.
- **Segundo parámetro (`expresión_regular`):** La expresión regular que define las restricciones para el valor del parámetro.

En caso de querer limitar más de un parámetro debemos meterlo dentro de una array asociativa.

``` php
Route::get('/ruta/{parametro}', [Controlador::class, 'metodo'])
    ->where([
	    'parametro' => 'expresión_regular',
	    'parametro' => 'expresión_regular'
    ]);
```
#### Ejemplos:

1. **Limitar un parámetro a solo números:** Si queremos que un parámetro sea exclusivamente numérico:
``` php
Route::get('/user/{id}', [UserController::class, 'show'])
    ->where('id', '[0-9]+');
```

2. **Limitar un parámetro a solo letras:** Si queremos que un parámetro solo contenga letras (mayúsculas o minúsculas):

```php
Route::get('/user/{name}', [UserController::class, 'showByName'])
    ->where('name', '[a-zA-Z]+');
```
### Métodos

Sin embargo, en Laravel existen otros métodos de `where` que nos permiten limitar los parámetros de las rutas de manera más específica sin necesidad de escribir expresiones regulares manualmente. Estos métodos son:

1. **`whereNumber`**: Este método limita el parámetro a solo números. Es útil cuando queremos asegurarnos de que el valor del parámetro sea un número entero.
``` php
Route::get('/user/{id}', [UserController::class, 'show'])
    ->whereNumber('id');
```

2. **`whereAlpha`**: Este método limita el parámetro a solo caracteres alfabéticos, es decir, letras mayúsculas y minúsculas sin espacios ni otros caracteres.
``` php
Route::get('/user/{name}', [UserController::class, 'showByName'])
    ->whereAlpha('name');
```

3. **`whereAlphaNumeric`**: Este método permite que el parámetro acepte solo caracteres alfanuméricos, es decir, letras y números sin espacios ni otros caracteres especiales.
``` php
Route::get('/codes/{code}', [CodeController::class, 'validate'])
    ->whereAlphaNumeric('code');
```

4. **`whereIn`**: Este método permite restringir el parámetro a un conjunto específico de valores permitidos. Es útil cuando queremos que el parámetro solo acepte valores predefinidos.
``` php
Route::get('/states/{state}', [StateController::class, 'show'])
    ->whereIn('state', ['active', 'inactive', 'hold']);
```

### Uso avanzado:

Laravel también permite aplicar restricciones globales a través del método `pattern` en la clase `RouteServiceProvider`. Esto es útil si queremos aplicar una restricción a un parámetro en todas las rutas donde se utiliza.

``` php
use Illuminate\Support\Facades\Route; //importar esta clase en concreto

public function boot()
{
    Route::pattern('id', '[0-9]+');
}
```

Esto asegurará que cualquier parámetro llamado `id` en cualquier ruta solo acepte valores numéricos.
