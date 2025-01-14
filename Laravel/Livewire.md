
**Laravel Livewire** es un framework de frontend que te permite construir interfaces de usuario dinámicas sin salir del entorno de Laravel. Con Livewire, podemos crear componentes interactivos utilizando PHP en lugar de JavaScript, lo cual facilita el desarrollo de aplicaciones modernas para desarrolladores que están más cómodos con el stack de Laravel.

## 1. Instalación de Livewire

### Método automático

Al instalar Laravel con el propio instalador se nos presenta las siguientes preguntas:

```sh
 Would you like to install a starter kit? [No starter kit]:
  [none     ] No starter kit
  [breeze   ] Laravel Breeze
  [jetstream] Laravel Jetstream
 > breeze

 Which Breeze stack would you like to install? [Blade with Alpine]:
  [blade              ] Blade with Alpine
  [livewire           ] Livewire (Volt Class API) with Alpine
  [livewire-functional] Livewire (Volt Functional API) with Alpine
  [react              ] React with Inertia
  [vue                ] Vue with Inertia
  [api                ] API only
 > livewire
```

si utilizamos `breeze` o `jetstream` tendremos la opción de escoger que livewire se instale al mismo tiempo, para ello solo debemos escoger la opción `livewire` y continuar con la instalación.
### Método manual

Para empezar a usar Livewire en un proyecto Laravel, debermos seguir estos pasos:

#### Paso 1: Instalar Livewire

Primero, instalamos Livewire usando Composer:

```bash
composer require livewire/livewire
```

#### Paso 2: Incluir los Scripts y Estilos de Livewire

Livewire necesita que incluyamos sus scripts y estilos en nuestra plantilla principal. Normalmente, esto se hace en la plantilla `layouts/app.blade.php`:

```html
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Laravel</title>

    @livewireStyles
</head>
<body>
    <div class="container">
        @yield('content')
    </div>

    @livewireScripts
</body>
</html>
```

- `@livewireStyles`: Incluye los estilos necesarios de Livewire.
- `@livewireScripts`: Incluye los scripts necesarios para que Livewire funcione correctamente.

## 2. Creación de un Componente Livewire

Para crear un componente Livewire, puedes usar el comando Artisan:

```bash
php artisan make:livewire MiComponente
```

Este comando creará dos archivos:
- `MiComponente.php`: La clase del componente, ubicada en `app/Http/Livewire`.
- `mi-componente.blade.php`: La vista asociada, ubicada en `resources/views/livewire`.

## 3. Estructura de un Componente Livewire

### Clase del Componente (PHP)

El archivo PHP define la lógica del componente:

```php
namespace App\Http\Livewire;
use Livewire\Component;

class MiComponente extends Component
{
    public $count = 0;
    public function increment()
    {
        $this->count++;
    }
    
    public function render()
    {
        return view('livewire.mi-componente');
    }
}
```

- **Propiedades**: `public $count` es una propiedad pública que se puede acceder desde la vista.
- **Métodos**: `increment()` es un método que incrementa el valor de count.
- **Renderizado**: `render()` devuelve la vista asociada con este componente.

### Vista del Componente (Blade)

El archivo Blade define la interfaz del usuario:

```html
<div>
    <h1>{{ $count }}</h1>
    <button wire:click="increment">Incrementar</button>
</div>
```

- `wire:click="increment"`: Esta directiva indica que cuando se haga clic en el botón, se ejecutará el método `increment` en la clase del componente.

## 4. Integrar el Componente en una Vista

Para mostrar el componente en una vista, simplemente utiliza la directiva `@livewire`:

```php
@extends('layouts.app')

@section('content')
    <livewire:mi-componente />
@endsection
```

## 5. Logica del componente

### Enlazar datos

Podemos enlazar propiedades de la clase del componente con las vistas, por ejemplo:

```php
class Post extends Component
{
    public $title, $body;

	public function save()
    {
        //
    }

    public function render()
    {
        return view('livewire.Post');
    }
}
```

En la vista lo podríamos escribir de la siguiente manera

```html
 <div>
    <h1>{{ $title }}</h1>
    <input type="text" wire:model="title">
    <button wire:click="save">Save</button>
</div>
```

Esto vinculará el valor `title` del input con la propiedad `title` del componente Post. Sin embargo, no se modificará hasta que se genere una acción, en este caso simplemente llamar a un método vacío, pero podría ser cualquier evento. Si queremos ver los cambios realizados al momento tenemos que usar el método `live`.
```html
<input type="text" wire:model.live="title">
```

### Eventos y acciones

#### 1. `wire:click`

- **Descripción**: Se activa cuando el usuario hace clic en un elemento.
- **Uso común**: Ejecutar acciones como enviar formularios, incrementar contadores, o realizar cualquier otra acción desencadenada por un clic.

```html
<button wire:click="increment">Incrementar</button>
```

#### 2. `wire:submit.prevent`

- **Descripción**: Se utiliza para interceptar el envío de un formulario y ejecutar una acción Livewire en lugar del envío tradicional.
- **Uso común**: Validación y procesamiento de formularios sin recargar la página.

```html
<form wire:submit.prevent="save">
    <!-- Campos del formulario -->
    <button type="submit">Guardar</button>
</form>
```

#### 3. `wire:model`

- **Descripción**: Enlaza una propiedad de Livewire a un campo de entrada, actualizando en tiempo real.
- **Uso común**: Sincronizar datos del formulario con las propiedades del componente.

```html
<input type="text" wire:model="name">
```

#### 4. `wire:keydown`

- **Descripción**: Se dispara cuando se presiona una tecla específica mientras el usuario interactúa con un elemento.
- **Uso común**: Captura de eventos de teclado, como enviar un formulario cuando se presiona Enter.

```html
<input type="text" wire:keydown.enter="submitForm">
```

#### 5. `wire:init`

- **Descripción**: Se ejecuta cuando el componente se inicializa en la página, antes de que se renderice.
- **Uso común**: Inicializar datos o realizar configuraciones cuando el componente carga por primera vez.

```html
<div wire:init="loadData">
    <!-- Contenido -->
</div>
```

#### 6. `wire:poll`

- **Descripción**: Ejecuta periódicamente una función en el componente, refrescando la vista.
- **Uso común**: Actualización automática de la UI, como listas de tareas o notificaciones en tiempo real.

```html
<div wire:poll.10s="refreshData">
    <!-- Datos en tiempo real -->
</div>
```

#### 7. `wire:dirty` / `wire:dirty.class`

- **Descripción**: Se dispara cuando un campo ha sido modificado pero no guardado.
- **Uso común**: Indicar visualmente que un campo ha cambiado pero no ha sido guardado.

```html
<input type="text" wire:model="name" wire:dirty.class="is-dirty">
```

#### 8. `wire:loading` / `wire:loading.class`

- **Descripción**: Se activa cuando hay una solicitud en curso, útil para mostrar indicadores de carga.
- **Uso común**: Mostrar spinners o deshabilitar botones durante una operación en curso.

```html
<button wire:click="save" wire:loading.attr="disabled">
    Guardar
</button>
<div wire:loading>
    Cargando...
</div>
```

#### 9. `wire:target`

- **Descripción**: Especifica el elemento objetivo para los modificadores loading, dirty, etc.
- **Uso común**: Controlar estados de carga o sucios para elementos específicos.

```html
<button wire:click="save" wire:loading.attr="disabled" wire:target="save">
    Guardar
</button>
```

#### 10. `wire:ignore`

- **Descripción**: Le dice a Livewire que ignore un elemento específico y no lo re-renderice.
- **Uso común**: Prevenir que Livewire sobrescriba interacciones JavaScript personalizadas.

```html
<div wire:ignore>
    <!-- Contenido que no se renderiza -->
</div>
```

#### 11. `wire:emit`

- **Descripción**: Emite un evento de Livewire que otros componentes o scripts pueden escuchar.
- **Uso común**: Comunicación entre componentes o ejecutar scripts personalizados.

```php
$this->emit('eventName', $data);
```

#### 12. `wire:listen`

- **Descripción**: Escucha un evento emitido por otro componente Livewire.
- **Uso común**: Responder a eventos emitidos desde otros componentes.

```php
@this->on('eventName', 'handlerMethod')
```

#### 13. `wire:key`

- **Descripción**: Ayuda a Livewire a identificar componentes en listas dinámicas para evitar problemas de reordenamiento.
- **Uso común**: Gestión de componentes repetidos dentro de un @foreach.

```php
@foreach($items as $item)
    <div wire:key="item-{{ $item->id }}">
        {{ $item->name }}
    </div>
@endforeach
```
