


Las rutas en Laravel sirven para definir cómo se manejan las solicitudes HTTP entrantes. Al definir rutas, asocias URL con acciones específicas o vistas en tu aplicación. Cada ruta puede apuntar a un controlador y una función específicos o devolver una vista directamente.

## Rutas básicas

Ejemplo de una ruta básica en el archivo `routes/web.php`:
``` php
Route::get('/home', function () {
	return 'Welcome Home!'; 
});
```

Este código especifica que cuando un usuario visite `http://tuapp.com/home`, Laravel devolverá el texto "Welcome Home!". Las rutas son fundamentales para dirigir el tráfico en tu aplicación hacia el contenido o funcionalidad correcta.

Las rutas pueden apuntar a controladores:

``` php
Route::get('/user/', [UserController::class, 'showAll']);
Route::get('/movies/{movie}', [MovieController::class, "show"]);
```

Existen varios tipos de rutas con diferentes metodos:

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

## Grupo de rutas

Muchas veces nos encontraremos con que tenemos varias rutas que apuntan al mismo controlador, si bien, podemos crear las rutas una debajo de otra a veces es interesante agrupar las rutas, esto hace el codigo más legible, y a su vez más organizado. Para ello debemos usar el metodo `controller` y después agruparlo con el metodo `group`. 

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

de esta manera. nos ahorramos tener que indicar el controlador en cada ruta, nos limitamos a expresar solo la ruta y el metodo necesario.

## Resources

Dado que Laravel se utiliiza habitualmente con CRUD existen una serie de [[Rutas CRUD|rutas predeterminadas]] que podemos simplificar en una linea:
``` php
Route::resource('movies', MovieController::class);
```

esta linea genera automaticamente las rutas necesarias para hacer un CRUD basico y además si usamos el [[Controllers |controlador]] con el flag `-r` entonces ya tenemos creado tanto los metodos como las [[Rutas CRUD]].

## Nombres de ruta

En Laravel, el método `name()` utilizado en la definición de rutas, sirve para asignar un "nombre" a la ruta. Estos nombres de ruta nos dan una manera facil y conveniente de referenciar rutas en diferentes partes de una aplicación, como en las vistas, los controladores o al generar URLs.

### Ventajas de Usar Nombres de Ruta

1. **Claridad y Legibilidad**: Los nombres de ruta pueden hacer que el código sea más legible. Al ver un nombre de ruta, es más fácil entender a qué parte de la aplicación se está haciendo referencia.
    
2. **Mantenimiento**: Si necesitamos cambiar la URL de una ruta, podemos hacerlo en el archivo de rutas sin tener que modificar todas las referencias a esa ruta en la aplicación. Mientras el nombre de la ruta se mantenga igual, todas las referencias a ella seguirán funcionando.
    
3. **Generación de URLs**: Puedes usar la función helper `route()` de Laravel para generar URLs a tus rutas nombradas. Esto es útil en las vistas para crear enlaces, en los controladores para redireccionamientos, o incluso en archivos de configuración o servicios.
    
4. **Consistencia**: Los nombres de ruta ayudan a mantener la consistencia en la forma en que se generan y se manejan las URLs en toda la aplicación.

### Ejemplo de Uso

#### Definición de la Ruta

En el archivo de rutas (`web.php`), podriamos tener algo como esto:

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
 Si queremos "traducir" las rutas generadas con resource, vamos al fichero `App\Providers\RouteServiceProvider` y mediante el método `Route::resourceVerbs` podemos modificar el valor que viene por defecto. Por ejemplo en el caso de las rutas `create` y `edit`, si lo queremos pasar a español, tendriamos que ir al fichero anterior y en el método `boot` añadimos nuestras traducciones.

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