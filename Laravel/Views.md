- [[#Vistas en Laravel (Usando Blade)|Vistas en Laravel (Usando Blade)]]
- [[#Ejemplo Básico en Laravel|Ejemplo Básico en Laravel]]
- [[#Parámetros cargados por defecto|Parámetros cargados por defecto]]
	- [[#Parámetros cargados por defecto#View composers|View composers]]
- [[#Mostrar los Errores de Validación en las Vistas|Mostrar los Errores de Validación en las Vistas]]
- [[#Mostrar los Errores de Validación en las Vistas#Ejemplo de Uso|Ejemplo de Uso]]
- [[#Mostrar los Errores de Validación en las Vistas#Mostrar Todos los Errores de Validación|Mostrar Todos los Errores de Validación]]
- [[#Mostrar los Errores de Validación en las Vistas#Uso del Método old|Uso del Método old]]
	- [[#Uso del Método old#Ejemplo:|Ejemplo:]]


Las "views" (vistas) son un componente esencial en el patrón de diseño Modelo-Vista-Controlador (MVC). Las vistas son responsables de presentar los datos a los usuarios, son la interfaz de usuario de las aplicación web. 
### Vistas en Laravel (Usando Blade)

En Laravel, las vistas generalmente se escriben utilizando Blade, un motor de plantillas que proporciona una sintaxis más limpia y una serie de características útiles:

1. **Archivos Blade**:
    Las vistas de Blade se guardan como archivos `.blade.php` en el directorio `resources/views` de una aplicación Laravel.
2. **Herencia de Plantillas**:
    Blade permite la herencia de plantillas, lo que significa que puedes tener una plantilla base (layout) que define una estructura común (como cabecera, pie de página, etc.) y luego extender esta plantilla en tus vistas específicas.
3. **Directivas y Control de Flujo**:
    Blade ofrece directivas como `@if`, `@foreach`, y `@yield` para controlar el flujo y la lógica de presentación dentro de las vistas.
4. **Datos Dinámicos**:
	 Las vistas pueden recibir datos dinámicos, como variables o colecciones, desde los controladores. Estos datos pueden ser mostrados y manipulados dentro de las vistas.
5. **Componentes y Slots**:
	 Blade también permite la creación de componentes y slots, que facilitan la reutilización de elementos comunes de la interfaz de usuario.
6. **Direcciones**:
	Si tenemos una [[Views|vista]] metida dentro de una carpeta, podemos referenciarlo usando el `nombre_directorio.nombre_vista`

### Ejemplo Básico en Laravel

Un ejemplo simple de una vista en Laravel podría ser un archivo `welcome.blade.php` que muestra un mensaje de bienvenida:

``` html
<!DOCTYPE html>
<html>
	<head>
	    <title>Bienvenido</title>
	</head>
	<body>
	    <h1>{{ $mensaje }}</h1>
	</body>
</html>
```

En este ejemplo, `{{ $mensaje }}` es una variable dinámica que se pasaría desde un controlador a la vista.

``` php
public function welcome()
{
	$msg = "Hola mundo";
	return view('welcome', ['mensaje' => $msg]);
}
```

- **Compact:** El método `compact()` en PHP es una función que se utiliza para crear un array asociativo a partir de variables existentes. Esta función toma los nombres de las variables como cadenas de texto y genera un array donde las claves son los nombres de las variables y los valores son los contenidos de esas variables.

``` php
public function welcome()
{
	$mensaje = "Hola mundo";
	$version = 1;
	return view('welcome', compact('mensaje', 'version')); //esto crea una array asociativa ['mensaje' => "Hola mundo", 'version' => 1]
}
```

### Parámetros cargados por defecto

En algunas ocasiones es posible necesitar enviar a todas las vistas (o solo a algunas especificas) valores para que se pueda usar en cualquier momento. Para ello, contamos con los [[Service Providers |providers]] ya que nos permite hacer diferentes operaciones al inicio de la aplicación. Por ejemplo, podríamos pasar un valor de una variable a todas las rutas al inicio de la aplicación para evitar tener que declararlo en el controlador multiples veces.

``` php
namespace App\Providers;
use Illuminate\Support\ServiceProvider;
use Illuminate\Support\Facades\View;

class MiServiceProvider extends ServiceProvider
{
    /**
     * Inicia servicios después de que todos los providers han sido registrados.
     *
     * @return void
     */
    public function boot()
    {
        View::share('msg' => 'Hola Mundo!');
    }
}
```

Esto, combinado con los view composers nos permite evitar duplicidad del código, cargar datos globales, etc. 
#### View composers

Los **View Composers** en Laravel son una herramienta que nos permite asociar lógica o datos a una vista específica o un grupo de vistas. Es decir, los View Composers actúan como "escuchadores" o "callbacks" que se ejecutan cuando se está preparando una vista para ser renderizada. Esto nos ayuda a mantener nuestro código más limpio y organizado, evitando tener que pasar los mismos datos repetidamente desde los controladores.

``` php
use Illuminate\Support\Facades\View;

public function boot()
{
    View::composer(['profile', 'dashboard'], function ($view) {
        $view->with('user', auth()->user());
    });
}
```

En este código, especificamos como primer parámetro las vistas donde queremos que se envié las variables y como segundo parámetro una función "callback" que reciba una vista, dentro de la función declaramos los valores de las variables.

## Mostrar los Errores de Validación en las Vistas

Cuando ocurre una validación fallida en Laravel, los errores se envían automáticamente a la vista de redirección (generalmente la misma vista desde la que se envió el formulario). Se pueden mostrar estos errores de validación en la vista utilizando el objeto especial $errors que Laravel nos proporciona en las vistas Blade.

### Ejemplo de Uso

Supongamos que tenemos un formulario para crear un usuario en una vista Blade:

``` html
<form action="{{ route('users.store') }}" method="POST">
    @csrf

    <!-- Campo Nombre -->
    <div>
        <label for="name">Nombre:</label>
        <input type="text" id="name" name="name" value="{{ old('name') }}">
        @error('name')
            <div class="error">{{ $message }}</div>
        @enderror
    </div>

    <!-- Campo Email -->
    <div>
        <label for="email">Email:</label>
        <input type="email" id="email" name="email" value="{{ old('email') }}">
        @error('email')
            <div class="error">{{ $message }}</div>
        @enderror
    </div>

    <!-- Botón Enviar -->
    <button type="submit">Crear Usuario</button>
</form>
```

Explicación:
- `$errors`: Este objeto está disponible automáticamente en todas las vistas. Contiene los errores de validación devueltos por la última solicitud.
- `@error('campo')`: Esta directiva es un atajo para verificar si existe un error para un campo específico. Si hay un error, ejecuta el código dentro del bloque @error, donde {{ $message }} muestra el mensaje de error.
- `old('campo')`: El método old se usa para mostrar el valor ingresado anteriormente en el campo del formulario, lo que permite al usuario corregir su entrada sin tener que volver a escribir todo en caso de un error de validación.

### Mostrar Todos los Errores de Validación

También puedes optar por mostrar todos los errores de validación al mismo tiempo:

```php
@if ($errors->any())
    <div class="alert alert-danger">
        <ul>
            @foreach ($errors->all() as $error)
                <li>{{ $error }}</li>
            @endforeach
        </ul>
    </div>
@endif
```

Explicación:
- $errors->any(): Verifica si hay algún error de validación presente.
- $errors->all(): Devuelve una lista de todos los mensajes de error, que luego se pueden mostrar en una lista `<ul>`.

### Uso del Método old

El método `old` es útil para mantener la entrada del usuario en los campos del formulario después de que se haya producido un error de validación. Esto evita que el usuario tenga que volver a llenar el formulario desde cero.
#### Ejemplo:

```html
<form action="{{ route('users.store') }}" method="POST">
    @csrf

    <div>
        <label for="name">Nombre:</label>
        <input type="text" id="name" name="name" value="{{ old('name') }}">
        @error('name')
            <div class="error">{{ $message }}</div>
        @enderror
    </div>

    <div>
        <label for="email">Email:</label>
        <input type="email" id="email" name="email" value="{{ old('email') }}">
        @error('email')
            <div class="error">{{ $message }}</div>
        @enderror
    </div>

    <button type="submit">Crear Usuario</button>
</form>
```

Explicación del Método old:

- **Propósito**: `old('campo')` recuerda el valor anterior que el usuario ingresó en el campo de formulario y, si ocurre un error, por lo que el formulario restaura automáticamente estos valores cuando se redirige de vuelta a la vista.