
## One to One

Para implementar una relación uno a uno en una base de datos con laravel necesitramos:

1. **Clave Foranea**: Una tabla contiene una clave extranjera que hace referencia a la clave primaria de otra tabla. Por ejemplo, la tabla `profiles` tiene una columna `user_id` que es una clave extranjera que hace referencia a la clave primaria `id` en la tabla `users`.
2. **Restricciones Únicas**: La clave extranjera debería tener una restricción única para garantizar la unicidad de la relación.
3. Existen las entidades debiles y fuertes. Tenemos que determinar cual es la entidad fuerte, para generar las relaciones correctamente. La entidad debil, es la que depende de la fuerte.

En Laravel, las relaciones uno a uno se definen en los modelos Eloquent.
### Paso 1: Definir la Relación en los Modelos

Supongamos que tenemos dos modelos: `Movie` e `Isan`.

1. En el modelo `Movie`, definimos un método para el Isan:
- `hasOne` indica que la pelicula tiene un [isan](https://www.isan.org/) asociado.

 **Syntaxis**: return $this->hasOne(Isan::class, 'foreign_key', 'local_key');
 **Ejemplo**: return $this->hasOne(Isan::class, 'id', 'isan_id');
 Si se usa el id y se han usado las convenciones de laravel, no hace falta especificar las llaves, simplemente es necesaria la clase. 
 
``` php
class Movie extends Model
{
	//propiedades
	/*===================One to one===================== */
	public function ISAN()
	{
		//return $this->hasOne(Isan::class, 'id', 'isan_id');
		return $this->hasOne(Isan::class);
	}
}
```

2. En el modelo `isan`, definimos un método para la clase `movie`:
- `belongsTo` indica que el Isan le pertenece a una pelicula. 

``` php
class Isan extends Model
{
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
		Schema::create('movies', function (Blueprint $table) {
			$table->id();
			$table->string('name', 30);
			$table->text('description');
			$table->string('poster')->nullable();
			$table->year('year');
			
			/*==========One to one=============*/
			//El campo que sera la clave foranea tiene que coincidir con la clave local de la tabla a la que hacemos referencia. En el caso del id, es unsignedBigInteger. 
			$table->unsignedBigInteger('isan_id')->unique();
			//Vinculamos el campo que será la clave foranea, con el metodo foreign
			$table->foreign('isan_id')
			->references('id')//hacemos referencia a la clave primaria
			->on('isans')//indicamos la tabla
			->onDelete('cascade') //cuando se borre, borrará el objeto
			->onUpdate('cascade');//cuando se actualice, actualizará el objeto
			
			/*Esta es una forma de hacerlo, pero se puede hacer con foreignId, siempre y cuando se hayan seguido las convenciones*/
			
			// $table->foreignId('isan_id')->constrained();
			/*==================================*/
			$table->timestamps();
		});
	}
};
```


## One to many

Para implementar una relación One to Many usando la misma tabla `movies`, pero con la adición de directores donde un director puede tener muchas películas, primero necesitaremos una tabla para los directores y luego establecer la relación en los modelos de Eloquent. En este escenario, cada película tiene un solo director, pero un director puede estar asociado con muchas películas.

### Paso 1: Definir la relacion en los modelos 

Supongamos que tenemos dos modelos: `Movie` y `Director`.

1. En el modelo `Director`, definimos un método para `Movie`:
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

2. En el modelo `Movie`, definimos un método para la clase `Director`:
- `belongsTo` indica que la pelicula pertenece a un Director. 

``` php
class Movie extends Model
{
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
            $table->foreignId('isan_id')->constrained();//One to one
        
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

## Many to many

Para establecer una relación Many-to-Many entre películas y categorías en Laravel, necesitaremos  tres tablas: `movies`, `categories`, y una tabla pivote que conecte las dos, por ejemplo, `category_movie`. Cada película puede tener varias categorías, y cada categoría puede estar asociada con varias películas.

**Nota: Para crear la tabla pivote tenemos que tener en cuenta que por convención la tabla pivote se tiene que llamar como las dos tablas que queremos relacionar. La unión de los nombres se tiene que hacer en orden alfabetico y en singular. En este caso el sinonimo de las tablas `movies` y `categories`, son `movie` y `category` respectivamente. Y el ordén alfabético es primero `Category` y después `Movie`. Por lo tanto la tabla pivote es `category_movie`.**

### Paso 1: Definir la relacion en los modelos 

Supongamos que tenemos dos modelos: `Movie` e `Category`.

1. En el modelo `Movie`, definimos un método para la `category` :
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

2. En el modelo `Category`, definimos un método para la clase `Movie`:
- `BelongsToMany` indica que las categorias pertenecen a varias peliculas. 

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

		$table->unsignedBigInteger('movie_id');
		$table->unsignedBigInteger('category_id');
		$table->foreign('movie_id')->references('id')->on('movies')->onDelete('cascade');
		$table->foreign('category_id')->references('id')->on('categories')->onDelete('cascade');
		$table->primary(['movie_id', 'category_id']); // Clave primaria compuesta
		$table->timestamps();
	});
}
```

