- [[#One to One Polimórfico|One to One Polimórfico]]
	- [[#One to One Polimórfico#Paso 1: Definir la Relación en los Modelos|Paso 1: Definir la Relación en los Modelos]]
	- [[#One to One Polimórfico#Paso 2: Crear las Migraciones|Paso 2: Crear las Migraciones]]
- [[#One to Many Polimórfico|One to Many Polimórfico]]
	- [[#One to Many Polimórfico#Paso 1: Definir la Relación en los Modelos|Paso 1: Definir la Relación en los Modelos]]
	- [[#One to Many Polimórfico#Paso 2: Crear las Migraciones|Paso 2: Crear las Migraciones]]
- [[#Many to Many Polimórfico|Many to Many Polimórfico]]
	- [[#Many to Many Polimórfico#Paso 1: Definir la Relación en los Modelos|Paso 1: Definir la Relación en los Modelos]]
	- [[#Many to Many Polimórfico#Paso 2: Crear las Migraciones|Paso 2: Crear las Migraciones]]


Las relaciones polimórficas permiten que un modelo esté relacionado con más de un modelo de otro tipo utilizando una única asociación. Esto es útil en casos donde diferentes tipos de modelos pueden compartir una misma relación.

## One to One Polimórfico

Para implementar una relación uno a uno polimórfica en una base de datos con Laravel necesitamos:

- **Campos polimórficos**: La tabla relacionada debe contener dos columnas: `morphable_id` y `morphable_type`, que almacenarán el ID del modelo y el tipo del modelo relacionado, respectivamente. También podemos usar el método `morphs` que automáticamente nos genera ambas columnas.
- **Entidades compartidas**: Estas relaciones son útiles cuando una tabla puede pertenecer a más de un tipo de modelo.

### Paso 1: Definir la Relación en los Modelos

Supongamos que tenemos tres modelos: `User`, `Post`, e `Image`. Un `usuario` y un `post` pueden tener una imagen asociada.

- En el modelo Image, definimos un método para la relación polimórfica:
	- `morphTo` indica que la imagen puede pertenecer a cualquier modelo.

```php
class Image extends Model
{
	protected fillable = [
		'url',
		'imageable_id',
	    'imageable_type'
	];
	
    /*===================One to one Polymorphic===================== */
    public function imageable()
    {
	    return $this->morphTo();
    }
}
```

- En los modelos User y Post, definimos un método para la clase Image:
	- `morphOne` indica que el modelo tiene una imagen asociada.

```php
class User extends Model
{
    /*===================One to one Polymorphic===================== */
    public function image()
    {
        return $this->morphOne(Image::class, 'imageable');
    }
}

class Post extends Model
{
    /*===================One to one Polymorphic===================== */
    public function image()
    {
        return $this->morphOne(Image::class, 'imageable');
    }
}
```

### Paso 2: Crear las Migraciones

En las migraciones, debemos asegurarnos de crear los campos `morphable_id` y `morphable_type` o `morphs` en la tabla images:

```php
return new class extends Migration
{
    public function up(): void
    {
        Schema::create('images', function (Blueprint $table) {
            $table->id();
            $table->string('url');
            
            /*==========One to one Polymorphic=============*/
            $table->unsignedBigInteger('imageable_id');
            $table->string('imageable_type');
            //$table->morphs('imageable');
            /*===========================================*/
            
            $table->timestamps();
        });
    }
};
```

Uso en el Controlador

```php
$user = User::find(1);
$image = new Image(['url' => 'user.jpg']);
$user->image()->save($image);
```

## One to Many Polimórfico

Para implementar una relación uno a muchos polimórfica, necesitamos:

- **Campos polimórficos**: Similar a la relación uno a uno, la tabla relacionada debe contener `morphable_id` y `morphable_type`.
- **Entidades compartidas**: Esta relación es útil cuando múltiples tipos de modelos pueden tener una colección de un tipo común, como comentarios en posts y videos.

### Paso 1: Definir la Relación en los Modelos

Supongamos que tenemos tres modelos: `Post`, `Video`, y `Comment`. Un post y un video pueden tener múltiples comentarios.

- En el modelo `Comment`, definimos un método para la relación polimórfica:
	- `morphTo` indica que el comentario puede pertenecer a cualquier modelo.

```php
class Comment extends Model
{
	private $fillable = [
		'commentable_id',
		'commentable_type',
		'body'
	];

    /*===================One to many Polymorphic===================== */
    public function commentable()
    {
        return $this->morphTo();
    }
}
```

- En los modelos `Post` y `Video`, definimos un método para la clase Comment:
	- `morphMany` indica que el modelo tiene muchos comentarios asociados.

```php
class Post extends Model
{
    /*===================One to many Polymorphic===================== */
    public function comments()
    {
        return $this->morphMany(Comment::class, 'commentable');
    }
}

class Video extends Model
{
    /*===================One to many Polymorphic===================== */
    public function comments()
    {
        return $this->morphMany(Comment::class, 'commentable');
    }
}
```

### Paso 2: Crear las Migraciones

En las migraciones, creamos los campos `commentable_id` y `commentable_type` en la tabla comments:

```php
return new class extends Migration
{
    public function up(): void
    {
        Schema::create('comments', function (Blueprint $table) {
            $table->id();
            $table->text('body');
            
            /*==========One to many Polymorphic=============*/
            //$table->unsignedBigInteger('commentable_id');
            //$table->string('commentable_type');
            $table->morphs('commentable');
            /*============================================*/
            
            $table->timestamps();
        });
    }
};
```

Uso en el Controlador

```php
$post = Post::find(1);
$comment = new Comment(['body' => 'Great post!']);
$post->comments()->save($comment);

$video = Video::find(1);
$comment = new Comment(['body' => 'Nice video!']);
$video->comments()->save($comment);
```

## Many to Many Polimórfico

Para implementar una relación muchos a muchos polimórfica en Laravel, necesitamos:

- Tabla pivote polimórfica: Una tabla intermedia que contiene `morphable_id`, `morphable_type`, y una clave externa que apunta a la otra tabla.
- Entidades compartidas: Esta relación es útil cuando múltiples tipos de modelos pueden estar relacionados con múltiples instancias de un modelo común, como etiquetas (tags) aplicadas a posts y videos.

### Paso 1: Definir la Relación en los Modelos

Supongamos que tenemos tres modelos: Post, Video, y Tag. Un post y un video pueden tener muchas etiquetas, y una etiqueta puede aplicarse a muchos posts o videos.

- En el modelo Tag, definimos un método para la relación polimórfica:
	- `morphedByMany` indica que la etiqueta puede pertenecer a muchos posts o videos.

```php
class Tag extends Model
{


    /*===================Many to many Polymorphic===================== */
    public function posts()
    {
        return $this->morphedByMany(Post::class, 'taggable');
    }

    public function videos()
    {
        return $this->morphedByMany(Video::class, 'taggable');
    }
}
```

- En los modelos Post y Video, definimos un método para la clase Tag:
	- `morphToMany` indica que el modelo puede tener muchas etiquetas.

```php
class Post extends Model
{
    /*===================Many to many Polymorphic===================== */
    public function tags()
    {
        return $this->morphToMany(Tag::class, 'taggable');
    }
}

class Video extends Model
{
    /*===================Many to many Polymorphic===================== */
    public function tags()
    {
        return $this->morphToMany(Tag::class, 'taggable');
    }
}
```

### Paso 2: Crear las Migraciones

En las migraciones, debemos crear la tabla pivote taggables con los campos necesarios:

```php
return new class extends Migration
{
    public function up(): void
    {
        Schema::create('taggables', function (Blueprint $table) {
            $table->id();
            
            /*==========Many to many Polymorphic=============*/
            /*$table->unsignedBigInteger('tag_id');
            $table->unsignedBigInteger('taggable_id');
            $table->string('taggable_type');
            $table->foreign('tag_id')
		            ->references('id')
			        ->on('tags')
		            ->onDelete('cascade');*/
		    /*================== or =========================*/
            $table->morphs('taggable');
            $table->foreignId('tag_id')
		            ->constrained()
		            ->onDelete('cascade');
            /*=============================================*/
            
            $table->timestamps();
        });
    }
};
```

Uso en el Controlador

```php
$post = Post::find(1);
$tag = Tag::find(1);
$post->tags()->attach($tag->id);

$video = Video::find(1);
$video->tags()->attach($tag->id);
```

Explicación

- **Tabla pivote polimórfica**: La tabla `taggables` relaciona tags con diferentes tipos de modelos (`posts`, `videos`).
- **Metodología**: Usamos `morphedByMany` en el modelo que se comparte entre diferentes entidades, y `morphToMany` en los modelos que tienen la relación con la entidad compartida.