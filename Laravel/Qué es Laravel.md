 
 Laravel es un framework de aplicación web con una sintaxis expresiva y elegante. Se ha establecido como uno de los frameworks de PHP más populares y utilizados. Laravel facilita tareas comunes en la mayoría de los proyectos web, como la autenticación, el enrutamiento, las sesiones y el caching. Está diseñado para ser poderoso y accesible, proporcionando herramientas necesarias para aplicaciones grandes y robustas. 
## Características Principales

- **Eloquent ORM**: Un ORM (Object-Relational Mapper) simple y elegante que facilita la interacción con la base de datos.
- **Migraciones de base de datos:** Control de versiones para la base de datos que permite fácilmente revertir y modificar esquemas de base de datos.     
- **Blade:** Motor de plantillas simple pero poderoso para crear layouts con lenguaje PHP de forma elegante.     
- **Middleware:** Mecanismo para filtrar y manejar solicitudes HTTP.     Autenticación y **Autorización:** Funcionalidades integradas para la gestión de usuarios y permisos. 
## Primeros Pasos     

1. **Instalación:**        
	- Asegúrate de tener Composer, el manejador de dependencias de PHP.
	- Instala Laravel mediante Composer ejecutando composer create-project --prefer-dist laravel/laravel nombreProyecto.     
2. Estructura de Directorios:         
	- **/app:** Contiene el código fuente de tu aplicación, incluyendo modelos, controladores y comandos.
	- **/resources/views:** Aquí se almacenan las plantillas Blade.         
	- **/routes:** Los archivos de ruta definen las rutas de tu aplicación web.         
	- **/config:** Contiene archivos de configuración.     
3. Servidor de Desarrollo:
	- Laravel viene con un servidor de desarrollo integrado. Puedes iniciarlo con php artisan serve desde tu terminal.
4. Rutas y Controladores: 
	- Las rutas controlan a qué controladores y funciones responden ciertas URLs. Puedes definir rutas en los archivos dentro de la carpeta /routes.
	- Los controladores se almacenan en /app/Http/Controllers y son clases que organizan la lógica de manejo de peticiones.     
5. Vistas y Blade:
	- Las vistas son archivos Blade en /resources/views que contienen HTML y pueden integrar datos dinámicos.
	- Blade permite insertar PHP de una forma más limpia y legible.
6. Migraciones y Eloquent:
	- Define estructuras de base de datos y relaciones con migraciones y modelos Eloquent. 
	- Usa migraciones para crear y modificar tablas.
	- Eloquent permite interactuar con la base de datos a través de objetos y relaciones de forma intuitiva. 

## ¿Cómo Funciona Laravel? 
Laravel sigue el patrón de arquitectura MVC (Modelo-Vista-Controlador), que separa la lógica de la aplicación en tres componentes interconectados:    

**Modelo:** Maneja la conexión de datos y la lógica de negocios.     
**Vista:** Presenta la interfaz de usuario y representa la salida de datos.
**Controlador:** Interpreta la entrada del usuario, convirtiéndola en comandos para el modelo o la vista. 

Laravel mejora este patrón con características adicionales como Eloquent, Blade, y paquetes extensibles, permitiendo un desarrollo web más rápido, mantenible y escalable. 