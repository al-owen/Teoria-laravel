- [[#Validación mediante el método validate en controladores|Validación mediante el método validate en controladores]]
- [[#Validación con lógica adicional|Validación con lógica adicional]]
- [[#Uso de Form Requests para validaciones|Uso de Form Requests para validaciones]]
	- [[#Uso de Form Requests para validaciones#Definiendo reglas en el Form Request|Definiendo reglas en el Form Request]]
	- [[#Uso de Form Requests para validaciones#Usando el Form Request en el controlador|Usando el Form Request en el controlador]]
	- [[#Uso de Form Requests para validaciones#Ventajas de usar Form Requests|Ventajas de usar Form Requests]]
- [[#Reglas de validación disponibles|Reglas de validación disponibles]]
- [[#Mensajes de error personalizados|Mensajes de error personalizados]]
	- [[#Mensajes de error personalizados#Especificando mensajes en el controlador o Form Request|Especificando mensajes en el controlador o Form Request]]


Las validaciones son una parte esencial de cualquier aplicación web, ya que garantizan que los datos ingresados por los usuarios cumplan con ciertos criterios antes de ser procesados o almacenados. Laravel proporciona un sistema de validación robusto y sencillo de usar que facilita este proceso.

## Validación mediante el método validate en controladores

La forma más sencilla de realizar validaciones en Laravel es utilizando el método validate disponible en las clases de controlador. Este método automáticamente redirige al usuario de vuelta con los mensajes de error si la validación falla.

**Ejemplo básico:** 

```php
namespace App\Http\Controllers;
use Illuminate\Http\Request;

class UserController extends Controller
{
    public function store(Request $request)
    {
        $validatedData = $request->validate([
            'name' => 'required|string|max:255',
            'email' => 'required|email|unique:users,email',
            'password' => 'required|min:8|confirmed',
        ]);

        // Si la validación pasa, puedes continuar con la lógica
        // por ejemplo, crear un nuevo usuario
        User::create($validatedData);

        return redirect()->route('users.index')->with('success', 'Usuario creado exitosamente.');
    }
}
```

Explicación:

- El método $request->validate() recibe un array de reglas de validación.
- Si la validación falla, Laravel redirige automáticamente al usuario de vuelta a la página anterior con los errores y la entrada antigua.
- Si la validación es exitosa, los datos validados se devuelven y pueden ser utilizados para procesar la solicitud.

## Validación con lógica adicional

Puedes agregar lógica adicional antes o después de la validación si es necesario.

```php
public function update(Request $request, User $user)
{
    $validatedData = $request->validate([
        'name' => 'required|string|max:255',
        'email' => "required|email|unique:users,email",
        'password' => 'nullable|min:8|confirmed',
    ]);

    if (empty($validatedData['password'])) {
        unset($validatedData['password']);
    } else {
        $validatedData['password'] = bcrypt($validatedData['password']);
    }

    $user->update($validatedData);

    return redirect()->route('users.show', $user)->with('success', 'Usuario actualizado exitosamente.');
}
```

Explicación:

- La contraseña es opcional (nullable), y solo se actualiza si se proporciona.
- Puedes manipular los datos validados antes de usarlos, como encriptar la contraseña.

## Uso de Form Requests para validaciones

Los Form Requests son clases personalizadas que encapsulan la lógica de validación, permitiendo mantener los controladores limpios y organizados.
Creación de un Form Request

Puedes crear un Form Request usando el comando Artisan:

```bash
php artisan make:request StoreUserRequest
```

Esto creará una clase en app/Http/Requests/StoreUserRequest.php.
### Definiendo reglas en el Form Request

```php
namespace App\Http\Requests;
use Illuminate\Foundation\Http\FormRequest;

class StoreUserRequest extends FormRequest
{
    public function authorize()
    {
        // Puedes agregar lógica de autorización aquí
        return true;
    }

    public function rules()
    {
        return [
            'name' => 'required|string|max:255',
            'email' => 'required|email|unique:users,email',
            'password' => 'required|min:8|confirmed',
        ];
    }

    public function messages()
    {
        return [
            'name.required' => 'El nombre es obligatorio.',
            'email.required' => 'El correo electrónico es obligatorio.',
            'password.required' => 'La contraseña es obligatoria.',
            // Otros mensajes personalizados
        ];
    }
}
```

Explicación:

- `authorize`: Permite controlar si el usuario está autorizado para hacer esta solicitud.
- `rules`: Define las reglas de validación.
- `messages`: (Opcional) Define mensajes de error personalizados.

### Usando el Form Request en el controlador

```php
namespace App\Http\Controllers;
use App\Http\Requests\StoreUserRequest;

class UserController extends Controller
{
    public function store(StoreUserRequest $request)
    {
        User::create($request->validated());
        return redirect()->route('users.index')->with('success', 'Usuario creado exitosamente.');
    }
}
```

Explicación:

- Inyectas el Form Request en el método del controlador.
- El Form Request se encarga de validar los datos antes de que el método sea ejecutado.
- Puedes acceder a los datos validados usando $request->validated().

### Ventajas de usar Form Requests

- Reutilización: Puedes reutilizar la misma clase de validación en diferentes lugares.
- Organización: Mantiene los controladores limpios y enfoca la lógica de validación en una clase dedicada.
- Personalización: Facilita la personalización de mensajes de error y lógica de autorización.

## Reglas de validación disponibles

Laravel ofrece una amplia variedad de reglas de validación integradas que cubren la mayoría de los casos comunes. Algunas de las más utilizadas son las siguientes:

- `required`: El campo es obligatorio.
- `email`: El campo debe ser una dirección de correo electrónico válida.
- `min:value` y `max:value:` El campo debe tener un valor mínimo/máximo.
- `between:min,max:` El valor debe estar entre un rango específico.
- `confirmed`: El campo debe tener un campo de confirmación correspondiente (ejemplo: `password` y `password_confirmation`).
- `unique:table,column:` El valor debe ser único en una tabla/columna de la base de datos.
- `exists:table,column:` El valor debe existir en una tabla/columna de la base de datos.
- `date`, `after:date`, `before:date`: Validaciones relacionadas con fechas.
- `numeric`, `integer`, `string`, `boolean`: Validan el tipo de dato.
- `array`, `json`: Validan estructuras de datos complejas.

Para obtener una lista más extensa y completa de todas las validaciones podéis acceder a la [página de Laravel](https://laravel.com/docs/11.x/validation#available-validation-rules).

Ejemplo de uso de múltiples reglas

``` php
$rules = [
    'username' => 'required|string|min:3|max:20|unique:users,username',
    'age' => 'nullable|integer|min:18|max:99',
    'bio' => 'nullable|string|max:500',
    'profile_picture' => 'nullable|image|mimes:jpg,jpeg,png|max:2048',
];
```

Explicación:

- `username`: Obligatorio, cadena de texto entre 3 y 20 caracteres, y único en la tabla users.
- `age`: Opcional, entero entre 18 y 99.
- `bio`: Opcional, cadena de texto hasta 500 caracteres.
- `profile_picture`: Opcional, debe ser una imagen de tipo JPG o PNG y no superar 2MB.

## Mensajes de error personalizados

Por defecto, Laravel proporciona mensajes de error en inglés. Puedes personalizar estos mensajes de varias maneras.

### Especificando mensajes en el controlador o Form Request

```php
$request->validate([
    'email' => 'required|email|unique:users,email',
], [
    'email.required' => 'El correo electrónico es obligatorio.',
    'email.email' => 'Debe proporcionar un correo electrónico válido.',
    'email.unique' => 'Este correo electrónico ya está registrado.',
]);
```

En Form Request:

```php
public function messages()
{
    return [
        'email.required' => 'El correo electrónico es obligatorio.',
        'email.email' => 'Debe proporcionar un correo electrónico válido.',
        'email.unique' => 'Este correo electrónico ya está registrado.',
    ];
}
```