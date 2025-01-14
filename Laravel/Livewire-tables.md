La documentación relacionada se encuentra aquí: [Rappasoft | Documentation | Configuration | laravel-livewire-tables](https://rappasoft.com/docs/laravel-livewire-tables/v2/start/configuration)

Para poder utilizar las tablas de livewire primero debemos instalar el paquete via composer

```sh
composer require rappasoft/laravel-livewire-tables
```

## Publicar assets

```sh 
php artisan vendor:publish --provider="Rappasoft\LaravelLivewireTables\LaravelLivewireTablesServiceProvider" --tag=livewire-tables-config
```

```sh
php artisan vendor:publish --provider="Rappasoft\LaravelLivewireTables\LaravelLivewireTablesServiceProvider" --tag=livewire-tables-translations
```

**Nota:** Segun la propia página no se recomienda publicar las vistas a menos que sea realmente vayamos a modificarlas, por lo tanto el siguiente comando exceptuando si es necesario, no se debería hacer:
```sh
php artisan vendor:publish --provider="Rappasoft\LaravelLivewireTables\LaravelLivewireTablesServiceProvider" --tag=livewire-tables-views
```

## Purga de tailwind

Si tailwind empieza a purgar algunos estilos debemos indicarle que no lo haga dirigiéndonos a y especificándole lo siguiente

```php
// V3
module.exports = {
    content: [
        ...
	        './vendor/rappasoft/laravel-livewire-tables/resources/views/**/*.blade.php',
    ],
    ...
};
```

# Si no funciona probar esto:
## Publicamos las tablas de configuración

```sh
php artisan vendor:publish --tag="livewire-tables-config"
```

## Modificar el fichero de configuración

```php
/**
 * Cache Rappasoft Frontend Assets
 */
'cache_assets' => false,

/**
 * Enable or Disable automatic injection of core assets
 */
'inject_core_assets_enabled' => false,

/**
 * Enable or Disable automatic injection of third-party assets
 */
'inject_third_party_assets_enabled' => false,

/**
 * Enable Blade Directives (Not required if automatically injecting or using bundler approaches)
 */
'enable_blade_directives' => false,
```

## Empaquetar los assets

```sh
import '../../vendor/rappasoft/laravel-livewire-tables/resources/imports/laravel-livewire-tables-all.js';
```