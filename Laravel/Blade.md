- [[#Directivas de Control de Flujo|Directivas de Control de Flujo]]
- [[#Directivas de Plantillas|Directivas de Plantillas]]
- [[#Directivas Misceláneas|Directivas Misceláneas]]



Blade, el motor de plantillas de Laravel, proporciona una variedad de directivas que comienzan con el símbolo `@`. Estas directivas ofrecen una manera conveniente y expresiva de controlar la renderización de las vistas y manejar la lógica de presentación.

### Directivas de Control de Flujo

1. `@if`, `@elseif`, `@else`:
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

2. `@unless`:
	Es como un `if` inverso.

``` php
@unless (condición)
    <!-- Código si la condición es falsa -->
@endunless
```

3. `@isset`:
	Verifica si una variable está definida.

``` php
@isset($variable)     
	<!-- Código si $variable está definida --> 
@endisset
```

4. `@empty`:
	Verifica si una variable está vacía.

``` php
@empty($variable)
	<!-- Código si $variable está vacía --> 
@endempty
```

5.  `@switch`, `@case`, `@break`, `@default`:
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

6. `@for - @endfor, @foreach - @endforeach, @while - @endwhile, @forelse - @empty - @endforelse`:
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

1. `@extends` y `@section`, `@endsection`, `@yield`:
	Para herencia de plantillas y definición de secciones.

``` php
@extends('layout.base')

@section('contenido')
	<!-- Código de la sección -->
@endsection
```

2. `@include`:
    Para incluir otras vistas.

``` php
@include('partials.cabecera')
```

  3. `@show`:
    Para renderizar una sección inmediatamente.

``` php
@section('nombre')
	<!-- Código de la sección --> 
@show`
```

4. `@parent`:
    Para agregar contenido a una sección de una vista padre.

``` php
@section('nombre')
@parent
<!-- Contenido adicional -->
@endsection
```

### Directivas Misceláneas

1. `@csrf`:
    Para incluir un token CSRF en un formulario.

``` php
<form method="POST">
	@csrf
	<!-- Elementos del formulario -->
</form>
```

2. `@method`:
    Para especificar métodos de formulario (PUT, PATCH, DELETE) en Laravel.
  
``` php
<form method="POST">
	@method('PUT')
	@csrf 
</form>
```

3. `@php`, @endphp:
	Para incluir código PHP directamente.

``` php
@php
	// Código PHP 
@endphp
```

4. `@verbatim`:
    Para escapar de Blade y escribir texto sin procesar.

    ``` php
@verbatim
	{{ Esto no será procesado por Blade }}
@endverbatim
```

5. `@error`:
    Para mostrar mensajes de error de validación.
``` php
@error('nombre')
    <div class="alerta">{{ $message }}</div>
@enderror
```
