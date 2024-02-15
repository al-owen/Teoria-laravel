

## Implementación Manual de Roles de Usuarios 
 
Para implementar roles de usuarios manualmente en Laravel, debemos seguir varios pasos esenciales:

1. **Crear la estructura de la base de datos**: Necesitamos tablas para `users`, `roles`, y una tabla pivote `role_user` para la relación muchos a muchos entre usuarios y roles.
    
2. **Modelos y Relaciones**: Crear modelos para `User` y `Role`, y definir las relaciones entre ellos. Los usuarios pueden tener múltiples roles y viceversa.
    
3. **Middleware para Control de Acceso**: Crear middleware que verifique los roles de los usuarios y controle el acceso a ciertas rutas en la aplicación.
    
4. **Asignación de Roles**: Implementar una forma de asignar roles a los usuarios, ya sea desde una interfaz de usuario o mediante seeder en la base de datos.
    

### **Paso 1: Migraciones**

Primero, debemos crear las migraciones para nuestras tablas `roles`, `role_user` además de tener la de `users` que viene por defecto en Laravel.

``` php
// Creación de la tabla roles
Schema::create('roles', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->timestamps();
});

// Creación de la tabla role_user
Schema::create('role_user', function (Blueprint $table) {
    $table->foreignId('user_id')->constrained()->onDelete('cascade');
    $table->foreignId('role_id')->constrained()->onDelete('cascade');
    $table->primary(['user_id', 'role_id']);
});
```

### **Paso 2: Modelos y Relaciones**

Modelo `Role`:

``` php
class Role extends Model
{
	//parametros del modelo
    public function users()
    {
        return $this->belongsToMany(User::class);
    }
}
```

Modelo `User` (habria que actualitzar para incluir la relación con roles):

``` php
class User extends Authenticatable
{
	//parametros del modelo
    public function roles()
    {
        return $this->belongsToMany(Role::class);
    }
}
```

### **Paso 3: Middleware**

Crear un middleware llamado `CheckRole`:

``` bash
php artisan make:middleware CheckRole
```

En el middleware, verificaríamos el rol del usuario:

``` php
public function handle($request, Closure $next, ...$roles)
{
    if (!$request->user() || !$request->user()->roles()->whereIn('name', $roles)->first()) {
        // Redirigir o abortar si el usuario no tiene el rol necesario
        abort(403);
    }

    return $next($request);
}
```

### **Paso 4: Asignar Roles**

Puedes asignar roles a los usuarios directamente en tu controlador o mediante seeder:

``` php
$user->roles()->attach($roleId);
```

### Paso 5: Registrar el middleware 

En el archivo `app/Http/Kernel.php` agregamos una entrada para el middleware

``` php
protected $middleware = [
    // Otros middlewares...
    \App\Http\Middleware\CheckRole::class,
];
protected $middlewareAliases = [
    // Otros middlewares...
    'checkrole' => \App\Http\Middleware\CheckRole::class,
];
```

Este paso hace que el middleware `CheckRole` esté disponible para ser usado en las rutas con la clave `'checkrole'`.

Para protejer las rutas, lo hariamos de la siguiente forma:

``` php
Route::get('/admin/dashboard', function () {
    return view('dashboard.admin');
})->middleware('checkrole:admin');
```
## Libreria

[Installation in Laravel | laravel-permission | Spatie](https://spatie.be/docs/laravel-permission/v6/installation-laravel)

### Asignación de Roles a Usuarios

Para asignar un rol a un usuario, primero debes crear roles y permisos. Aquí tienes un ejemplo de cómo hacerlo:

``` php
use Spatie\Permission\Models\Role;
use Spatie\Permission\Models\Permission;
// Crear un rol
$adminRole = Role::create(['name' => 'admin']);
// Crear un permiso
$viewDashboardPermission = Permission::create(['name' => 'view dashboard']);
// Asignar el permiso al rol
$adminRole->givePermissionTo($viewDashboardPermission);
// Asignar el rol a un usuario
$user->assignRole('admin');
```
### Uso en Middleware

`spatie/laravel-permission` viene con dos middlewares que puedes utilizar para proteger tus rutas: `role:` y `permission:`. Para usar estos middlewares, primero debes registrarlos en `app/Http/Kernel.php`:

``` php
protected $routeMiddleware = [
    // Otros middlewares...
    'role' => \Spatie\Permission\Middleware\RoleMiddleware::class,
    'permission' => \Spatie\Permission\Middleware\PermissionMiddleware::class,
];
```

Luego, puedes aplicar estos middlewares a tus rutas. Por ejemplo, para requerir que un usuario tenga el rol `admin`:

``` php
Route::get('/admin/dashboard', function () {
    // Contenido del dashboard administrativo
})->middleware(['auth', 'role:admin']);
```

O para requerir un permiso específico:

``` php
Route::get('/admin/dashboard', function () {
    // Contenido del dashboard administrativo
})->middleware('permission:view dashboard');
```

### Uso en Blade

Para controlar la visibilidad del contenido en tus vistas Blade según los roles o permisos de los usuarios, puedes usar las directivas `@role` y `@can` que Laravel proporciona por defecto, ya que `spatie/laravel-permission` se integra perfectamente con el sistema de autorización de Laravel

``` php
@role('admin')
    <!-- Contenido solo para administradores -->
@endrole

@can('view dashboard')
    <!-- Contenido para usuarios con permiso para ver el dashboard -->
@endcan
```

### Asignar Roles y Permisos en Seeder

Puedes utilizar seeders para asignar roles y permisos a usuarios durante la fase de desarrollo o cuando preparas tu aplicación para producción. Aquí tienes un ejemplo de cómo hacerlo:

``` php
public function run()
{
    $adminRole = Role::create(['name' => 'admin']);
    $viewDashboardPermission = Permission::create(['name' => 'view dashboard']);
    $adminRole->givePermissionTo($viewDashboardPermission);

    $adminUser = User::create([/* Detalles del usuario */]);
    $adminUser->assignRole($adminRole);
}
```