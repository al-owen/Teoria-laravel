- [[#Secciones|Secciones]]
	- [[#Secciones#Conceptos Básico de Secciones|Conceptos Básico de Secciones]]
	- [[#Secciones#Ejemplo de Uso de Secciones|Ejemplo de Uso de Secciones]]
- [[#`@stack`|`@stack`]]
	- [[#`@stack`#¿Cómo Funciona `@stack`?|¿Cómo Funciona `@stack`?]]
	- [[#`@stack`#Ejemplo de Uso Común|Ejemplo de Uso Común]]
		- [[#Ejemplo de Uso Común#1. Definir una Pila con `@stack`|1. Definir una Pila con `@stack`]]
		- [[#Ejemplo de Uso Común#2. Agregar Contenido a la Pila con `@push`|2. Agregar Contenido a la Pila con `@push`]]
		- [[#Ejemplo de Uso Común#3. Renderización de la Vista|3. Renderización de la Vista]]

## Secciones

Las secciones en Laravel, utilizando el motor de plantillas [[Blade]], son una forma poderosa y flexible de organizar y reutilizar partes de la interfaz de usuario en diferentes vistas. Básicamente, las secciones permiten definir bloques de contenido que pueden ser insertados en diferentes layouts (plantillas base) o [[Views|vistas]]. 
### Conceptos Básico de Secciones

1. **Definición**:
    Una sección es un bloque de código HTML, Blade o texto que defines en una vista para ser poder reutilizarlo en diferentes sitios. Es comúnmente usada para definir diferentes partes de una página, como encabezados, pies de página, barras laterales, etc.

2. **Declaración de Secciones**:
    En una vista Blade, defines una sección utilizando la directiva `@section('nombre_de_la_seccion')`. Todo el contenido entre esta directiva y la directiva `@endsection` será parte de esa sección.

``` php
@section('title', 'Home')
@section('nombre_de_la_seccion')
	<!-- Contenido de la sección --> 
@endsection
```

 3. **Uso de Secciones en Layouts**:
    En tus layouts (plantillas base), puedes "ceder" (yield) un lugar para estas secciones usando la directiva `@yield('nombre_de_la_seccion')`. Cuando una vista que extiende este layout define una sección con el mismo nombre, el contenido de esa sección se insertará en el lugar del `@yield`.

``` php
<!-- En un layout --> 
<html>
<head>
	<!--contenido del head-->
	 <title>@yield('title')</title>
</head>
<body>
	@yield('nombre_de_la_seccion')
</body>
</html>
```

### Ejemplo de Uso de Secciones

Si tienemos un layout principal `layouts.app` y queremos definir una sección para el contenido principal de cada página.

**Layout Principal (layouts/app.blade.php):**

``` php
<!DOCTYPE html>
<html>
<head>
    <!-- ... -->
</head>
<body>
    <div class="container">
        @yield('content')
    </div>
</body>
</html>
```

Ahora, supongamos que tenemos una vista `welcome.blade.php` que extiende este layout y define la sección `content`.

**Vista (welcome.blade.php):**
``` php
@extends('layouts.app')

@section('content')
    <h1>Bienvenido a Nuestra Aplicación</h1>
    <p>Este es el contenido principal de la página.</p>
@endsection
```

En este ejemplo, cuando Laravel renderiza la vista `welcome`, insertará el contenido de la sección `content` en el lugar donde el layout `layouts.app` tiene el `@yield('content')`.

## `@stack` 

La directiva `@stack` en Blade es una herramienta poderosa que permite inyectar contenido en diferentes partes de una vista desde otros lugares de la aplicación. Es especialmente útil cuando se trabaja con layouts o plantillas maestras en Laravel, y se desea agregar contenido adicional en lugares específicos de la página (como al final de la sección `<head>` o antes del cierre del `<body>`).

### ¿Cómo Funciona `@stack`?
`@stack` funciona en conjunto con la directiva `@push`. Primero, se define una "pila" (stack) en la plantilla principal utilizando `@stack`. Luego, en cualquier vista secundaria o parcial, se puede agregar contenido a esa pila usando `@push`. Finalmente, cuando se renderiza la vista principal, todo el contenido "empujado" (pushed) a la pila se inserta donde se definió `@stack`.

### Ejemplo de Uso Común

Imaginemos que estamos trabajando con una plantilla principal (`layouts.app`) y queremos permitir que las vistas hijas agreguen scripts personalizados al final de la página, justo antes del cierre del `</body>`.

#### 1. Definir una Pila con `@stack`

En la plantilla principal (`layouts.app`), definimos la pila en el lugar donde queremos que se inserte el contenido:

``` php
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>@yield('title')</title>
    @stack('styles')
</head>
<body>
    <div class="containr">
        @yield('content')
    </div>
    <!-- Aqui se inserta el contenido de la pila de scripts -->
    @stack('scripts')
</body>
</html>
```

Aquí tenemos dos pilas definidas:
- **`@stack('styles')`**: Se utiliza para agregar css en la sección `<head>`.
- **`@stack('scripts')`**: Se utiliza para agregar scripts antes del cierre del `</body>`.

#### 2. Agregar Contenido a la Pila con `@push`

En las vistas hijas o parciales, usamos `@push` para agregar contenido a estas pilas:

``` php
@extends('layouts.app')

@section('title', 'Página con Scripts Personalizados')

@section('content')
    <p>Este es el contenido de la página.</p>
@endsection

@push('styles')
    <link rel="stylesheet" href="/css/pagina-especial.css">
@endpush

@push('scripts')
    <script src="/js/javascript1.js"></script>
@endpush

@push('scripts')
    <script src="/js/javascript2.js"></script>
@endpush
```

#### 3. Renderización de la Vista

Cuando se renderiza la vista `contenido.blade.php`, Blade combinará la plantilla principal `layouts.app` con el contenido de la vista hija, incluyendo los estilos y scripts que se inyectaron a las pilas:

``` html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Página con Scripts Personalizados</title>
    <link rel="stylesheet" href="/css/pagina-especial.css">
</head>
<body>
    <div class="container">
        <p>Este es el contenido de la página.</p>
    </div>
    
    <script src="/js/javascript1.js"></script>
    <script src="/js/javascript2.js"></script>
</body>
</html>
```
