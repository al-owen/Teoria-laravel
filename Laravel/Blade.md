- [[#Directivas de Control de Flujo|Directivas de Control de Flujo]]
	- [[#Directivas de Control de Flujo#1. `@if`, `@elseif`, `@else`:|1. `@if`, `@elseif`, `@else`:]]
	- [[#Directivas de Control de Flujo#2. `@unless`:|2. `@unless`:]]
	- [[#Directivas de Control de Flujo#3. `@isset`:|3. `@isset`:]]
	- [[#Directivas de Control de Flujo#4. `@empty`:|4. `@empty`:]]
	- [[#Directivas de Control de Flujo#5.  `@switch`, `@case`, `@break`, `@default`:|5.  `@switch`, `@case`, `@break`, `@default`:]]
	- [[#Directivas de Control de Flujo#6. `@for - @endfor, @foreach - @endforeach, @while - @endwhile, @forelse - @empty - @endforelse`:|6. `@for - @endfor, @foreach - @endforeach, @while - @endwhile, @forelse - @empty - @endforelse`:]]
- [[#Directivas de Plantillas|Directivas de Plantillas]]
	- [[#Directivas de Plantillas#1. `@extends` y `@section`, `@endsection`, `@yield`:|1. `@extends` y `@section`, `@endsection`, `@yield`:]]
	- [[#Directivas de Plantillas#2. `@include`:|2. `@include`:]]
		- [[#2. `@include`:#`@IncludeIf`|`@IncludeIf`]]
		- [[#2. `@include`:#`@includeWhen`|`@includeWhen`]]
		- [[#2. `@include`:#`@includeUnless`|`@includeUnless`]]
		- [[#2. `@include`:#`@includeFirst`|`@includeFirst`]]
		- [[#2. `@include`:#**Envio de parámetros:**|**Envio de parámetros:**]]
	- [[#Directivas de Plantillas#3. `@show`:|3. `@show`:]]
	- [[#Directivas de Plantillas#4. `@parent`:|4. `@parent`:]]
- [[#Directivas Misceláneas|Directivas Misceláneas]]
	- [[#Directivas Misceláneas#1. `@csrf`:|1. `@csrf`:]]
	- [[#Directivas Misceláneas#2. `@method`:|2. `@method`:]]
	- [[#Directivas Misceláneas#3. `@php`, @endphp:|3. `@php`, @endphp:]]
	- [[#Directivas Misceláneas#4. `@verbatim`:|4. `@verbatim`:]]
	- [[#Directivas Misceláneas#5. `@error`:|5. `@error`:]]
	- [[#Directivas Misceláneas#6. Renderizar html sin escapar blade:|6. Renderizar html sin escapar blade:]]
		- [[#6. Renderizar html sin escapar blade:#Paso 1: Preparar el HTML en el Controlador|Paso 1: Preparar el HTML en el Controlador]]
		- [[#6. Renderizar html sin escapar blade:#Paso 2: Renderizar el HTML en la Vista|Paso 2: Renderizar el HTML en la Vista]]
	- [[#Directivas Misceláneas#7. `@JSON`:|7. `@JSON`:]]
	- [[#Directivas Misceláneas#8.`@env`:|8.`@env`:]]
- [[#Atributos adicionales de formularios|Atributos adicionales de formularios]]
	- [[#Atributos adicionales de formularios#1. `old()`|1. `old()`]]
	- [[#Atributos adicionales de formularios#2. `@checked`, `@selected`, y `@disabled`|2. `@checked`, `@selected`, y `@disabled`]]



Blade, el motor de plantillas de Laravel, proporciona una variedad de directivas que comienzan con el símbolo `@`. Estas directivas ofrecen una manera conveniente y expresiva de controlar la renderización de las vistas y manejar la lógica de presentación.

### Directivas de Control de Flujo

#### 1. `@if`, `@elseif`, `@else`:
	Utilizadas para condicionales.
   
``` php
@if (condición)
    <!-- Código si la condición es verdadera -->
@elseif (otra condición)
    <!-- Código si la otra condición es verdadera -->
@else
    <!-- Código si las condiciones anteriores son falsas -->
@endif
```

#### 2. `@unless`:
	Es como un `if` inverso.

``` php
@unless (condición)
    <!-- Código si la condición es falsa -->
@endunless
```

#### 3. `@isset`:
	Verifica si una variable está definida.

``` php
@isset($variable)     
	<!-- Código si $variable está definida --> 
@endisset
```

#### 4. `@empty`:
	Verifica si una variable está vacía.

``` php
@empty($variable)
	<!-- Código si $variable está vacía --> 
@endempty
```

#### 5.  `@switch`, `@case`, `@break`, `@default`:
    Para estructuras de control switch-case.
  
``` php
@switch($variable)     
	@case(1)
		<!-- Código para caso 1 -->
		@break
	@case(2)
		<!-- Código para caso 2 --> 
		@break
	@default
		<!-- Código por defecto --> 
@endswitch
```

#### 6. `@for - @endfor, @foreach - @endforeach, @while - @endwhile, @forelse - @empty - @endforelse`:
    Bucles y ciclos.

``` php
@foreach ($array as $elemento)
    <!-- Código para cada elemento -->
    <p>Elemento {{$elemento}}</p>
@endforeach

@for ($i = 0; $i < 10; $i++)
    El valor es {{$i}}
    <!-- Código para cada elemento -->
@endfor

@forelse ($users as $user)
    <li><!-- Código para cada elemento --></li>
@empty
	<!-- Código en caso de que la lista este vacia -->
    <p>No hay usuarios</p>
@endforelse

@while ($contador>0)
	<!-- Código para cada elemento -->
	<p>El numero es {{$contador--}}</p>
@endwhile
```

### Directivas de Plantillas

#### 1. `@extends` y `@section`, `@endsection`, `@yield`:
	Para herencia de plantillas y definición de secciones.

``` php
@extends('layout.base')

@section('contenido')
	<!-- Código de la sección -->
@endsection
```

#### 2. `@include`:
   Para incluir otras vistas.
``` php
@include('partials.header')
```

##### `@IncludeIf`
También podemos usar includeIf que solo incluye el archivo en caso de que exista, en caso contrario el flujo del programa continua sin mostrar ningún error.
``` php
@includeIf('partials.header')
```
##### `@includeWhen`
Este método incluye una vista solo si se cumple una condición. Es útil para mostrar partes de la vista basadas en ciertas condiciones lógicas.
``` php
@includeWhen($usuario->admin, 'partials.admin-menu')
```

##### `@includeUnless`
Este método es el opuesto de `@includeWhen`. Incluye una vista solo si una condición **no** se cumple.
``` php
@includeUnless($usuario->admin, 'partials.user-menu')
```

##### `@includeFirst`
Este método intenta incluir la primera vista que exista en un array de vistas. Si ninguna de las vistas existe, se lanzará un error. Es útil cuando tenemos varias opciones de vistas y queremos cargar la primera que esté disponible.
``` php
@includeFirst(['partials.custom-menu', 'partials.default-menu'])
```

##### **Envio de parámetros:**
También es posible enviar parámetros a las vistas incluidas, por ejemplo:
``` php
@include('partials.header', ['color' => 'red'])
```

#### 3. `@show`:
    Para renderizar una sección inmediatamente.
``` php
@section('nombre')
	<!-- Código de la sección --> 
@show`
```

#### 4. `@parent`:
    Para agregar contenido a una sección de una vista padre.
``` php
@section('nombre')
@parent
<!-- Contenido adicional -->
@endsection
```


### Directivas Misceláneas

#### 1. `@csrf`:
    Para incluir un token CSRF en un formulario.

``` php
<form method="POST">
	@csrf
	<!-- Elementos del formulario -->
</form>
```

#### 2. `@method`:
    Para especificar métodos de formulario (PUT, PATCH, DELETE) en Laravel.
  
``` php
<form method="POST">
	@method('PUT')
	@csrf 
</form>
```

#### 3. `@php`, @endphp:
	Para incluir código PHP directamente.

``` php
@php
	// Código PHP 
@endphp
```

#### 4. `@verbatim`:
    Para escapar de Blade y escribir texto sin procesar.
``` php
@verbatim
	{{ Esto no será procesado por Blade }}
@endverbatim
```

#### 5. `@error`:
    Para mostrar mensajes de error de validación.
``` php
@error('nombre')
    <div class="alerta">{{ $message }}</div>
@enderror
```

#### 6. Renderizar html sin escapar blade:
   Para enviar contenido HTML desde un controlador a una vista en Laravel utilizando Blade, simplemente pasamos el HTML como una variable desde el controlador y, en la vista, utilizamos la función `{!! !!}` de Blade para renderizar el HTML sin escaparlo. Esto es útil cuando necesitamos insertar contenido HTML dinámico en una vista.
   
##### Paso 1: Preparar el HTML en el Controlador
En el controlador, creamos una variable que contenga el HTML que deseamos enviar a la vista.

``` php
public function mostrarVista()
{
    $html = "<h1>Bienvenido a Laravel</h1>
    <p>Este es un parrafo en HTML enviado desde el controlador.</p>";

    return view('my-view', compact('html'));
}
```

Aquí, estamos creando una variable `$html` que contiene código HTML y luego la pasamos a la vista `my-view`.

##### Paso 2: Renderizar el HTML en la Vista

En la vista `my-view.blade.php`, utilizamos la sintaxis de Blade `{!! !!}` para imprimir el contenido HTML sin escaparlo.

``` php
<!DOCTYPE html>
<html>
<head>
    <title>Miy View</title>
</head>
<body>
    <div>
        {!! $html !!}
    </div>
</body>
</html>
```
- **`{!! $html !!}`**: Blade normalmente escapa el contenido de las variables para prevenir ataques XSS (Cross-Site Scripting). Sin embargo, utilizando la sintaxis `{!! !!}`, indicamos a Blade que queremos renderizar el contenido tal como es, sin escape, permitiendo que se muestre el HTML.
#### 7. `@JSON`:
La directiva `@json` toma una variable o un valor de PHP y lo convierte a una representación JSON válida. Esta conversión es útil cuando necesitamos pasar datos desde Laravel a una aplicación JavaScript dentro de una vista Blade.  
	
La sintaxis es la siguiente:
``` php
@json($variable, $opciones, $profundidad)
```
- **$variable:** Es la variable o valor en PHP que queremos convertir a JSON.
- **$opciones (opcional):** Son opciones adicionales para la función `json_encode` de PHP, como `JSON_PRETTY_PRINT` o `JSON_UNESCAPED_UNICODE`.
- **$profundidad (opcional):** Define la profundidad máxima a la que se codificará el array o el objeto.

- Supongamos que tenemos un array de datos en el controlador que queremos pasar a una vista y luego usarlo en un script JavaScript:

``` php
// En el controlador
public function mostrarVista()
{
    $datos = [
        'nombre' => 'Juan',
        'edad' => 25,
        'ocupaciones' => ['Desarrollador', 'Diseñador']
    ];
    return view('my-view', compact('datos'));
}
```

En la vista `my-view.blade.php`, podemos utilizar `@json` para convertir este array en JSON:

``` html
<script>
    let datosUsuario = @json($datos);
    console.log(datosUsuario);
</script>
```

#### 8.`@env`: 
La directiva `@env` en Laravel es una herramienta que permite ejecutar código condicionalmente en las vistas Blade dependiendo del entorno en el que se esté ejecutando la aplicación. Esto es útil cuando queremos mostrar contenido específico o aplicar configuraciones diferentes según el entorno (como `local`, `production`, `staging`, etc.).
	1. La directiva `@env` verifica el entorno de la aplicación (definido en el archivo `.env` con la variable `APP_ENV`) y permite que el código contenido dentro de la directiva solo se ejecute si la aplicación está en el entorno especificado.

``` php
@env(['local', 'staging'])
    <div class="alert alert-warning">
        Estás en un entorno de desarrollo o pre-producción.
    </div>
@else 
	<p>Esta es la versión en producción de la aplicación.</p>
@endenv
```
### Atributos adicionales de formularios

#### 1. `old()`
   Recupera y muestra el valor anterior de un campo de formulario después de una solicitud fallida, útil para mantener los datos del formulario cuando hay errores de validación.

``` php
<form action="/guardar" method="POST">
    @csrf
    <input type="text" name="nombre" value="{{ old('nombre') }}">
    <button type="submit">Enviar</button>
</form>
```

Si el formulario falla en la validación, el campo "nombre" se rellenará automáticamente con el valor anterior que el usuario ingresó.

#### 2. `@checked`, `@selected`, y `@disabled`
Estas directivas se utilizan para manejar el estado de los elementos del formulario como checkboxes, radios, opciones en un select, o si un campo debe estar deshabilitado.

`@checked`: Marca una checkbox o radio si la condición es verdadera.
`@selected:` Selecciona una opción en un menú desplegable si la condición es verdadera.
`@disabled:` Deshabilita un campo si la condición es verdadera.

``` php
<form action="/guardar" method="POST">
    @csrf
    <label>
        <input type="checkbox" name="activo" value="1" @checked(old('activo', $usuario->activo))>
        Activo
    </label>

    <select name="rol">
        <option value="admin" @selected(old('rol', $usuario->rol) == 'admin')>Administrador</option>
        <option value="usuario" @selected(old('rol', $usuario->rol) == 'usuario')>Usuario</option>
    </select>

    <input type="text" name="nombre" @disabled($usuario->rol == 'admin')>
    <button type="submit">Guardar</button>
</form>

```