

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

