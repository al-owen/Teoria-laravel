Las **sesiones** son un mecanismo que permite almacenar datos del usuario a través de múltiples solicitudes HTTP. Se utilizan comúnmente para guardar información temporal, como el ID de usuario después de iniciar sesión, notificaciones temporales, preferencias del usuario o cualquier dato que necesite persistir durante la interacción de un usuario con la aplicación.

Laravel tiene una interfaz sencilla para gestionar estas sesiones.

## Uso básico de sesiones:

### Guardar datos en sesión:

``` php
session(['key' => 'value']);
```

o con el helper:
``` php
Session::put('key', 'value');
```
### Recuperar datos de sesión
``` php
$value = session('key');
```

o:

``` php
$value = Session::get('key');
```
### Eliminar datos de sesión
``` php
Session::forget('key');
```
### Limpiar toda la sesión:
``` php
Session::flush();
```
### Mensajes flash (duran una sola solicitud):
``` php
Session::flash('status', 'Operación exitosa');
```