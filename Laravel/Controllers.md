- [[#Controlador Vacio|Controlador Vacio]]
- [[#Controlador con métodos CRUD|Controlador con métodos CRUD]]
- [[#Forma manual|Forma manual]]


Los controladores son los encargados de la logica del programa. Normalmente son las clases encargadas de gestionar el CRUD de las aplicaciones y otros tipos de operaciones con los datos.

Existen diferentes formas de generar controladores, la primera **(y la más recomendada)** es utilizar la linea de comandos para generarlo de manera automatica mediante el comando.

## Controlador Vacio 

``` bash
php artisan make:controller NombreControlador
```

Esto nos generara un controlador vacío similar al siguiente ejemplo.

``` php
<?php
namespace App\Http\Controllers;
use Illuminate\Http\Request;

class NombreControlador extends Controller{
}
```

## Controlador con métodos CRUD

Otra manera de hacer el controlador es de manera automatica, pero que a su vez nos genere los metodos basicós de una clase **CRUD (CREATE, READ, UPDATE, DELETE)**.
Para hacer esto simplemente debemos ejecutar el mismo comando que para generar un controlador  vacio, pero con el flag `-r`

``` bash
php artisan make:controller NombreControlador -r
```

esto genererá el siguente controlador. 

``` php
<?php
namespace App\Http\Controllers;
use Illuminate\Http\Request;

class NombreControlador extends Controller
{
    /**Display a listing of the resource.*/
    public function index(){}
    
    /**Show the form for creating a new resource.*/
    public function create(){}
    
    /**Store a newly created resource in storage.*/
    public function store(Request $request){}

    /**Display the specified resource.*/
    public function show(string $id){}
    
    /**Show the form for editing the specified resource.*/
    public function edit(string $id){}
    
    /**Update the specified resource in storage.*/
    public function update(Request $request, string $id){}

    /**Remove the specified resource from storage.*/
    public function destroy(string $id){}
}
```

Esto se puede usar junto con las [[Routes]] para generar las operaciones bàsica de una página web.

## Forma manual

Tambien se puede hacer de forma manual, pero eso puede conllevar problemas. Para hacerlo correctamente hay que tener en cuenta las siguientes consideraciones.
1. El directorio donde se crea el archivo debe ser `app/Http/Controllers`.
2. Debe extender de `Controller` y estar en el namespace  `app/Http/Controllers`.
3. Es probable que necesites importar la clase `Request`.

Un ejemplo básico sería:
``` php
<?php
namespace App\Http\Controllers;
use Illuminate\Http\Request;

class LibroController extends Controller
{

}
```
