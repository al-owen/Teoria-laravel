- [[#One to One|One to One]]
	- [[#One to One#Paso 1: Definir la Relación en los Modelos|Paso 1: Definir la Relación en los Modelos]]
	- [[#One to One#Paso 2: Crear las migraciones|Paso 2: Crear las migraciones]]
	- [[#One to One#Ejemplos de uso:|Ejemplos de uso:]]
- [[#One to many|One to many]]
	- [[#One to many#Paso 1: Definir la relacion en los modelos|Paso 1: Definir la relacion en los modelos]]
	- [[#One to many#Paso 2: Crear las migraciones|Paso 2: Crear las migraciones]]
	- [[#One to many#Ejemplos de uso:|Ejemplos de uso:]]
- [[#Many to many|Many to many]]
	- [[#Many to many#Paso 1: Definir la relacion en los modelos|Paso 1: Definir la relacion en los modelos]]
	- [[#Many to many#Paso 2: Crear las migraciones|Paso 2: Crear las migraciones]]
	- [[#Many to many#Guardar la Relación Many to Many en el Controlador|Guardar la Relación Many to Many en el Controlador]]
	- [[#Many to many#Explicación|Explicación]]
	- [[#Many to many#Métodos Alternativos|Métodos Alternativos]]


## One to One

Para implementar una relación uno a uno en una base de datos con laravel necesitramos:

1. **Clave Foranea**: Una tabla contiene una clave extranjera que hace referencia a la clave primaria de otra tabla. Por ejemplo, la tabla `profiles` tiene una columna `user_id` que es una clave extranjera que hace referencia a la clave primaria `id` en la tabla `users`. **Nota: La clave foránea debe ir en la tabla que pertenece a otra entidad**
2. **Restricciones Únicas**: La clave extranjera debería tener una restricción única para garantizar la unicidad de la relación.
3. Existen las **entidades débiles y fuertes**. Tenemos que determinar cual es la entidad fuerte, para generar las relaciones correctamente. La entidad debil, es la que depende de la fuerte.

En Laravel, las relaciones uno a uno se definen en los modelos Eloquent.
### Paso 1: Definir la Relación en los Modelos

Supongamos que tenemos dos modelos: `Movie` e `Isan`.

4. En el modelo `Movie`, definimos un método para el Isan:
- `hasOne` indica que la pelicula tiene un [isan](https://www.isan.org/) asociado.

 **Syntaxis**: return $this->hasOne(Isan::class, 'foreign_key', 'local_key');
 **Ejemplo**: return $this->hasOne(Isan::class, 'id', 'isan_id');
 Si se usa el id y se han usado las convenciones de laravel, no hace falta especificar las llaves, simplemente es necesaria la clase. 
 
``` php
class Movie extends Model
{
	//propiedades
	/*===================One to one===================== */
	public function isan()
	{
		//return $this->hasOne(Isan::class, 'id', 'isan_id');
		return $this->hasOne(Isan::class);
	}
}
```

5. En el modelo `isan`, definimos un método para la clase `movie`:
- `belongsTo` indica que el Isan le pertenece a una pelicula. 

``` php
class Isan extends Model
{

	protected $fillable = [
		//...
        'movie_id',
    ];
	//propiedades
	/*===================One to one===================== */
	public function Movie()
	{
		return $this->belongsTo(Movie::class);
	}
}
```
### Paso 2: Crear las migraciones

En las migraciones, debemos asegurarnos de establecer la clave extranjera y la restricción única:

``` php
return new class extends Migration
{
/**
* Run the migrations.
*/
	public function up(): void
	{
		Schema::create('isans', function (Blueprint $table) {
			$table->id();
			$table->string('code', 30);
			
			/*==========One to one=============*/
			//El campo que sera la clave foranea tiene que coincidir con la clave local de la tabla a la que hacemos referencia. En el caso del id, es unsignedBigInteger. 
			$table->unsignedBigInteger('movie_id')->unique();
			//Vinculamos el campo que será la clave foranea, con el metodo foreign
			$table->foreign('movie_id')
			->references('id')//hacemos referencia a la clave primaria
			->on('movies')//indicamos la tabla
			->onDelete('cascade') //cuando se borre, borrará el objeto
			->onUpdate('cascade');//cuando se actualice, actualizará el objeto
			
			/*Esta es una forma de hacerlo, pero se puede hacer con foreignId, siempre y cuando se hayan seguido las convenciones*/
			
			// $table->foreignId('movie_id')->constrained();
			/*==================================*/
			$table->timestamps();
		});
	}
};
```

### Ejemplos de uso:
En el controlador:
``` php
public function store(Request $request)
{
	$movie = new Movie();
	//...
	$movie->save();
	
	$isan = new Isan();
	//...
	//se llama al atributo y se le pasa el id del modelo al que pertenece
	$isan->movie_id = $movie->id;
	$isan->save();
	
	
	return redirect()->route('books.index');
}
```
En la vista:
``` php
<table class="...">
	<thead>
		<tr class="bg-gray-200">
			<th class="border border-gray-300 px-4 py-2">Isan</th>
		</tr>
	</thead>
	<tbody>
	@foreach ($movies as $movie)
	    <tr>
			<td class="border border-gray-300 px-4 py-2">
			{{ $movie->isan->code }}
			</td>
	@endforeach
	</tbody>
</table>
```
## One to many

Para implementar una relación One to Many usando la misma tabla `movies`, pero con la adición de directores donde un director puede tener muchas películas, primero necesitaremos una tabla para los directores y luego establecer la relación en los modelos de Eloquent. En este escenario, cada película tiene un solo director, pero un director puede estar asociado con muchas películas.

### Paso 1: Definir la relacion en los modelos 

Supongamos que tenemos dos modelos: `Movie` y `Director`.

2. En el modelo `Director`, definimos un método para `Movie`:
- `hasMany` indica que el director tiene una o muchas películas.

``` php
class Director extends Model
{
	//Propiedades
    /*===================One to Many===================== */
	public function movies()
	{
		return $this->hasMany(Movie::class);
	}
}
```

3. En el modelo `Movie`, definimos un método para la clase `Director`:
- `belongsTo` indica que la pelicula pertenece a un Director. 

``` php
class Movie extends Model
{
	protected $fillable = [
		//...
        'director_id',
    ];
    
    //Propiedades
    /*===================Many to one===================== */
    public function director()
    {
        return $this->belongsTo(Director::class);
    }
}
```

### Paso 2: Crear las migraciones

En las migraciones, debemos asegurarnos de establecer la clave extranjera y la restricción única:

``` php
public function up(): void
    {
        Schema::create('movies', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->text('description');
            $table->string('poster')->nullable();
            $table->year('year');
            
			/*==========One to many=============*/
            /*En todos los casos se puede hacer con foreign o foreignId,
            siempre y cuando se respeten las convenciones */
            
            $table->unsignedBigInteger('director_id')->nullable();
            $table->foreign('director_id')
            ->references('id')
            ->on('directors');
            //$table->foreignId('director_id')->constrained();
            /*==================================*/
            
            $table->timestamps();
        });
    }
```

### Ejemplos de uso:
En el controlador:
``` php
public function store(Request $request)
{
	$director = Director::find($request->input('director_id'));
	
	$movie = new Movie();
	//...
	//se llama al atributo y se le pasa el id del modelo al que pertenece
	$movie->director_id = $director->id;
	$movie->save();	
	
	return redirect()->route('books.index');
}
```
En la vista:
``` php
<table class="...">
	<thead>
		<tr class="bg-gray-200">
			<th class="border border-gray-300 px-4 py-2">Director</th>
		</tr>
	</thead>
	<tbody>
	@foreach ($movies as $movie)
	    <tr>
			<td class="border border-gray-300 px-4 py-2">
				{{ $movie->director->name }}
			</td>
	@endforeach
	</tbody>
</table>
```
## Many to many

Para establecer una relación Many-to-Many entre películas y categorías en Laravel, necesitaremos  tres tablas: `movies`, `categories`, y una tabla pivote que conecte las dos, por ejemplo, `category_movie`. Cada película puede tener varias categorías, y cada categoría puede estar asociada con varias películas.

**Nota: Para crear la tabla pivote tenemos que tener en cuenta que por convención la tabla pivote se tiene que llamar como las dos tablas que queremos relacionar. La unión de los nombres se tiene que hacer en orden alfabetico y en singular. En este caso el sinonimo de las tablas `movies` y `categories`, son `movie` y `category` respectivamente. Y el ordén alfabético es primero `Category` y después `Movie`. Por lo tanto la tabla pivote es `category_movie`.**

### Paso 1: Definir la relacion en los modelos 

Supongamos que tenemos dos modelos: `Movie` e `Category`.

4. En el modelo `Movie`, definimos un método para la `category` :
- `BelongsToMany` indica que la película pertenece a varias categorias.

``` php
class Category extends Model
{
	//Propiedades 
    /*====================Many to many==================*/
    public function movies()
    {
        return $this->belongsToMany(Movie::class);
    }
}
```

5. En el modelo `Category`, definimos un método para la clase `Movie`:
- `BelongsToMany` indica que las categorías pertenecen a varias peliculas. 

``` php
class Movie extends Model
{
	//Propiedades
    /*===================Many to Many===================== */
    public function categories()
    {
        return $this->belongsToMany(Category::class);
    }
}
```

### Paso 2: Crear las migraciones

En las migraciones, debemos asegurarnos de establecer la clave extranjera y la restricción única en la tabla pivote:

``` php
public function up(): void
{
	Schema::create('category_movie', function (Blueprint $table) {
		$table->id();
		// /*==========Many to many=============*/
		$table->foreignId('category_id')->constrained();//automaticamente detecta el campo id de category
		$table->foreignId('movie_id')->constrained();//automaticamente detecta el campo id de category
		//si es diferente, tenemos que expresar de manera especifica los campos con el método foreign
		/*=========================================*/
		/*
		$table->unsignedBigInteger('movie_id');
		$table->unsignedBigInteger('category_id');
		$table->foreign('movie_id')->references('id')->on('movies')->onDelete('cascade');
		$table->foreign('category_id')->references('id')->on('categories')->onDelete('cascade');
		$table->primary(['movie_id', 'category_id']); // Clave primaria compuesta*/
		$table->timestamps();
	});
}
```

### Guardar la Relación Many to Many en el Controlador

Imaginemos que estamos guardando una nueva película junto con sus categorías. En el controlador `MovieController`, podríamos tener un método para guardar la película y sus categorías asociadas:

``` php
use Illuminate\Http\Request;
use App\Models\Movie;
use App\Models\Category;

class MovieController extends Controller
{
	public function store(Request $request)
	{
		// Validación de datos...
	
		// Crear una nueva película
		$movie = new Movie($request->all());
		$movie->save();
		// Adjuntar categorías
		// Asumiendo que recibimos los IDs de categorías como un array en la solicitud
		$categoryIds = $request->input('categories', []); // ['1', '2', ...]
		$movie->categories()->attach($categoryIds);
		return redirect()->route('movies.index');
	}
}
```
### Explicación

- **Creación de la Película**: Primero, se crea y guarda una nueva instancia de `Movie`.
- **Adjuntamos las Categorías**: Luego, usamos el método `attach` para asociar categorías con la película. El método `attach` toma un array de IDs de categoría y los asocia con la película en la tabla intermedia `category_movie`.
- **Datos de la Solicitud**: Este ejemplo asume que los IDs de las categorías se envían como parte de la solicitud, típicamente como un array.

### Métodos Alternativos

- **Sincronizar Categorías**: Si en lugar de simplemente añadir categorías, queremos sincronizarlas (es decir, asegurarnos de que solo las categorías proporcionadas estén asociadas con la película), podemos usar `sync` en lugar de `attach`:
``` php
$movie->categories()->sync($categoryIds);
```

- **Desvincular Categorías**: Si queremos desvincular categorías, podemos usar `detach`:
``` php
$movie->categories()->detach(); // Desvincula todas las categorías
$movie->categories()->detach([1,2]); // Desvincula categorías específicas
```
