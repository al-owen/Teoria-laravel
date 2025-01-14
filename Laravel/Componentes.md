
- [[#¿Qué son los Componentes en Laravel?|¿Qué son los Componentes en Laravel?]]
- [[#Ventajas de Usar Componentes|Ventajas de Usar Componentes]]
- [[#¿Cómo Funcionan?|¿Cómo Funcionan?]]
- [[#Crear un Componente|Crear un Componente]]
	- [[#Crear un Componente#Usar un Componente|Usar un Componente]]
	- [[#Crear un Componente#Clase del Componente|Clase del Componente]]
		- [[#Clase del Componente#Atributos Adicionales:|Atributos Adicionales:]]
			- [[#Atributos Adicionales:#No definidos:|No definidos:]]
			- [[#Atributos Adicionales:#Slots Anidados y Slots Nombrados|Slots Anidados y Slots Nombrados]]


## ¿Qué son los Componentes en Laravel?

En Laravel, un componente puede ser tan simple como un fragmento de código Blade que incluye un formulario de búsqueda, una tarjeta de usuario o un pie de página. También puede ser más complejo e incluir lógica de PHP asociada para preparar datos para la vista. Los componentes de Blade ayudan a mantener las vistas limpias, evitar la repetición de código y promover un desarrollo más modular.

## Ventajas de Usar Componentes

1. **Reutilización de Código**: Facilita la reutilización de fragmentos de interfaz de usuario en diferentes partes de tu aplicación.
2. **Organización**: Ayuda a mantener tus vistas organizadas al separar la lógica de diferentes partes de la interfaz de usuario en componentes individuales.
3. **Mantenimiento**: Hace que sea más fácil mantener y actualizar la interfaz de usuario, ya que los cambios en un componente se reflejan en todas las partes de la aplicación donde se utiliza.

## ¿Cómo Funcionan?

Los componentes de Blade se definen mediante archivos de plantilla en la carpeta `resources/views/components` de tu aplicación Laravel. También puedes generarlos mediante Artisan CLI, que opcionalmente crea una clase asociada para manejar la lógica del componente.

## Crear un Componente
Para crear un componente de Blade, puedes usar el comando Artisan:

``` bash
php artisan make:component NombreDelComponente
```

Esto crea dos archivos:

- Una clase de componente en `app/View/Components/NombreDelComponente.php`.
- Una vista de Blade en `resources/views/components/nombre-del-componente.blade.php`.
***Nota:** Si ponemos `directorio/NombreDelComponente` se nos creará una carpeta dentro de la carpeta de componentes con el nombre del directorio y se guardará el componente dentro.* 

```sh
php artisan make:component input/TextInput
```
Esto creará la carpeta `app/View/Components/input/TextInput.php` y `resources/views/components/input/text-input.blade.php`

### Usar un Componente
Para usar el componente en una vista, simplemente lo invocas por su nombre:

``` php
<x-nombre-del-componente />
```
Y si quieres pasar datos al componente, puedes hacerlo así:

``` php
<x-nombre-del-componente dato="Cualquier dato" />
//o también se puede enviar variables poniendo ":" en frente de la variable
$dato = "Cualquier dato";
<x-nombre-del-componente :dato="$dato" />
```

### Clase del Componente

La clase del componente te permite definir propiedades públicas que estarán disponibles en la vista del componente. También puedes realizar cualquier procesamiento necesario antes de que la vista sea renderizada.

``` php
namespace App\View\Components;
use Illuminate\View\Component;

class NombreDelComponente extends Component
{
    public $dato;
    public function __construct($dato)
    {
        $this->dato = $dato;
    }

    public function render()
    {
        return view('components.nombre-del-componente');
    }
}
```

Los datos se envían como si de una vista normal se tratase y dado que las variables declaradas en la clase son publicas y rellenadas en el constructor podemos acceder a estos valores desde la vista.

```php
<div class="alert">
    {{"el dato es: $dato"}}
</div>
```
#### Atributos Adicionales:

##### No definidos:
Podemos pasar atributos adicionales a un componente que no estén definidos explícitamente como propiedades. Por ejemplo, si queremos hacer un botón que nos muestre un texto, por defecto podemos llamar al contenido dentro de las etiquetas del componente mediante la variable `$slot`, esto nos devolverá todo lo que se encuentra dentro de la etiqueta del componente `<x-nombre-del-componente>` y `<x-nombre-del-componente />`.

``` php
<x-boton tipo="primary">
    Hacer algo
</x-boton>
```
Esto, si lo llamamos con la variable `$slot` en la vista del componente
``` php
<button class="btn btn-{{ $tipo }}">
    {{ $slot }}
</button>
```
se vera de la siguiente forma:
```html
<button class="btn btn-primary">
    Hacer algo
</button>
```

##### Slots Anidados y Slots Nombrados

Blade también soporta **slots anidados** y **slots nombrados**, lo que permite estructuras más complejas, al permitirnos generar slots con nombres, sin tener que declararlos en la clase del componente.

``` html
<x-card>
    <x-slot name="footer">
        Pie de página aquí.
    </x-slot>

    Contenido principal aquí.
</x-card>
```
Si se envia al siguiente componente...
``` php
<div>
    {{ $slot }}
    {{ $footer }}
</div>
```
generara la siguiente vista: 
``` html
<div>
	Contenido principal aquí.
    Pie de página aquí.
</div>
```

##### Atributos Adicionales:

Podemos pasar atributos adicionales a un componente que no estén definidos explícitamente como propiedades, mediante la variable `$attributes` que, por defecto,  guardará cada uno de los atributos pasados al componente. 

``` php
<x-boton tipo="primary" id="mi-boton" class="extra-clase">
    Hacer algo
</x-boton>
```
La modificación en el Componente Anónimo quedaria así:

``` php
<button {{ $attributes->merge(['class' => 'btn btn-' . $tipo]) }}>
    {{ $slot }}
</button>
```

Esto permitirá que el botón tenga clases adicionales, ID u otros atributos que se pasen desde la vista. Por lo tanto, quedaría así en el html.

``` html
<button class="btn btn-success extra-clase" id="submit-button">
    Enviar
</button>
```
