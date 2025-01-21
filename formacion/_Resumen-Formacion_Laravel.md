# FORMACION LARAVEL 11 🚀

---

## CRUD

> **NOTAS**
>
> -   Usar TINKER para hacer consultas a nuestros modelos y QueryBuilder
> -   Implementar en mi proyecto los FORMREQUEST para crear unas reglas de validación para compartir con datos básicos
> -   Implementar la regla exist en las reglas de validación para por ejemplo comprobar que el ID de una familia existe antes de insertar el registro de la subfamilia
> -   Podemos crear diferentes archivos de ruta, web o admin por ejemplo, podemos crear tantos archivos de ruta como paneles tengamos o perfiles muy definidos. Podemos usar prefix y lo imporante es registrarlo en providers

### Ejemplo de CRUD con POST

#### 1. Crear el controlador de tipo recurso y enlazar a modelo

```php
php artisan make:controller Admin\PostController -r --model=Post
```

#### 2. Creamos la ruta

Cremaos la ruta de tipo recurso

```php
Route::resource('/post',PostController::class)
->except('show');
```

#### 3. Indicamos las vistas a retornar en el controlador

En cada método retornaremos la vista correspondiente

```php
return view('admin.post.edit',compact('post'));
```

## OBSERVERS

Los _observers_ son observadores que ejecutan acciones específicas cuando ocurre un evento en un modelo.

```bash
php artisan make:observer NombreObserver
```

Esto creará una carpeta en HTTP con el nombre observer.
Este observador tiene dos métodos, `creating()` que es que ocurra algo antes de que se registre algo y si queremos que sea algo posterior será `created()` esto puede ser util para crear registros de logs o operaciones que deben hacerse cuando algo pasa.

En los observer hay otro metodo a utilizar llamado `updating(Post $post)` donde por ejemplo podemos preguntar si un artículo está creado y en caso de que si, por ejemplo, actualizar la fecha de publicación.

> Es una buena forma para limpiar el controlador, pero para poder usar estos observers debemos registrarlos en Providers/EventServiceProvider y en el método `boot()` registrarlo.

## MUTADORES/ACCESORES

Son modificadores, por ejemplo si queremos manipular el titulo de un post para guardarlo en Capitalize, importariamos la clase Atributte de Ilumniate/database/Eloquent.

**NOTA:** esto lo veo útil para tener muy limpio el código pero realmente no le veo tanta utilidad porque puedes hacerlo directamente en el create o index (métodos) de la clase.

Con los accesores podemos agregar porpiedades que no existen, por ejemplo podemos crear una propiedad llamada image, que mire si existe el image_path de un registro y en caso de que no, nos muestre otra imagen.

## VALIDACIONES

### Validación de Campos Únicos

Cuando vamos a validar los campos, en el controlador creamos reglas de validación por ejemplo para validar el slug debería, indica que es unico, y en la categoria que exista en la base de datos.

```
$request->validate([
    'slug' => 'required|unique:post', // Unico en la tabla post
    'category_id' => required|exists:categories,id',
]);
```

### Validaciones Condicionales

Imaginemos que tenemos unos campos que solo queremos que sean obligatorios siempre y cuando tengamos activado el registro. Para ello podemos hacer validaciones condicionales de esta forma

```php
$request->validate([
    'slug' => $request->active ? 'required|unique:post': 'nullable',tabla post
]);
```

## RUTAS

### Rutas Estáticas

```php
Route::get('/url',function(){
})
```

```php
Route::post('/url',function(){
})
```

Esto podemos unificarlo de esta manera

```php
Route::match(['get','post'],'/url',function(){
    return "hola";
});
```

### Rutas Dinámicas

```php
Route::get('/url/{curso}',function($curso){
    return "Hola" . $curso
})
```

```php
Route::get('/url/{curso}/{category?}',function($curso,$category = null){
    return "Hola" . $curso. $category
})
```

### Rutas con Expresiones Regulares

```php
Route::get('/url/{curso}/{category?}',function($curso,$category = null){
    return "Hola" . $curso. $category
})->where([
    'curso' => '[A-Za-z]+',
    'category' => '[A-Za-z]+',
    ]);
    // Otras formas
->whereAlfanumeric([
    'curso' => '[A-Za-z]+',
    'category' => '[A-Za-z]+',
    ]);
->whereIn('curso',['php','laravel'])
```

### Rutas: Comando

Para listar las rutas

```bash
php artisan route:list
php artisan r:l
```

Para listar las rutas solo creadas por nosotros

```bash
php artisan route:list --except-vendor
```

Para listar las rutas solo de terceros

```bash
php artisan route:list --only-vendor
```

Para listar las rutas solo las que tengan el siguiente path

```bash
php artisan route:list --path=cursos
```

#### Almacenar Rutas en Cache

En producción podemos hacer este proceso para que vaya todo más rápido debemos hacer este comando:

```bash
php artisan route:cache
```

En caso de querer limpiar la cache de rutas para añadir nuevas

```bash
php artisan route:clear
```

### Nombrar Rutas

Las uris podemos modificarla al gusto y laravel lo que hará es hacer uso de el name()

```php
Route::get('/cursos/{curso}/{category?}',function($curso,$category = null){
    return "Hola" . $curso. $category
})->name('cursos.info')
```

### Rutas Necesarias para CRUD

```php
// Ruta get para mostrar listado de todos los registros
    Route::get('/posts',function(){
});

// Ruta para crear un registro
    Route::get('/posts/create',function(){
});

// Ruta para guardar un registro
    Route::post('/posts',function(){
});

// Ruta para mostrar un registro
    Route::get('/posts/{$id}',function($id){
});

// Ruta para mostrar un registro para editar
    Route::get('/posts/{$id}/edit',function($id){
});

// Ruta para actualizar un registro (PUT / PATCH)
    Route::put('/posts/{$id}',function($id){
});

// Ruta para eliminar un registro
    Route::delete('/posts/{$id}',function($id){
});
```

### Rutas Especificar

Puede suceder que queramos poner el nombre editar o crear en vez de edit y create que utiliza Laravel, para ello deberemos mirar a `app/Providers` y editar el `AppServiceProvider` y en el `boot()` añadir lo siguiente:

```php
Route:resourceVerbs([
    'create' => 'crear',
    'edit' => 'editar',
])
```

Igualmente si queremos cambiar el nombre de nuestras rutas Resoures y utilizar otro nombre debremos hacerlo de esta manera:

```
    Route::resource('articulos,PostController:.class)
        ->parameters(['articulos' => 'post'])
        ->names('posts')
```

### Rutas de Grupo

No siempre vamos a trabajar con rutas de CRUD y aqui es donde entra las grupas de grupo para aagrupar y reducir el archivo de rutas, podemos utilizar de ejemplo las 7 rutas creadas anteriormente

```
Route::prefix('post')->controller('PostController:.class)->group([
    // Ruta get para mostrar listado de todos los registros
        Route::get('/','index);

    // Ruta para crear un registro
        Route::get('/create','create')->name('otronombre');

    // Ruta para guardar un registro
        Route::post('/'),'store';

    // Ruta para mostrar un registro
        Route::get('/{$id}','show');

    // Ruta para mostrar un registro para editar
        Route::get('/{$id}/edit','edit');

    // Ruta para actualizar un registro (PUT / PATCH)
        Route::put('/{$id}','update');

    // Ruta para eliminar un registro
        Route::delete('/{$id}','delete');
]);
```

## VISTAS

### Como pasar parametros a las vistas

En el controlador en el método pondremos:

```
return view('post.show',[
    'prueba' => $post
]);

// Otra Forma si seguimos la convencion, compact nos crea un array con a anterior estructura
return view('post.show',compact('post'));
```

### Pasar parametros a todas las Vistas

Para poder enviar un mismo datoa todas las vistas deberemos ir a AppServiceProvider y en el método boot() debemos indicar

```
View:share('prueba','Este es un mensaje para todas las vistas');
```

Si este archivo de View o cualquier otro se amplia mucho el fichero puede llegar a ser muy grande y para ello es bueno crear otro provider

```
php artisan make:provider NombreServiceProvider
```

El proveedor se registrará solo desde Laravel 10 y moveremos el View:share al metodo boot() del proveedor creado
View:composer --> Sirve para poder compartir una variable con una serie de determinadas vistas, parece algo más enrevesado, revisar con Copilot o revisar de nuevo el video.

## Blade Templates

Para poder imprimir contenido HTML en una plantilla blade en vez de imprimir `{{ $variable}}`seria `{!! $varaible !!}`

#### Directiva @JSON

La directiva @json permite que los datos se muestren como formato JSON. En vez de utilizar en la vista json_encode utilizamos la directiva @json($post)

```
$post = [
    '',
    '',
    '',
];
return view('post.show',compact('post'));

En la vista usamos @json($post)
```

#### Directivas @if, @unless, @isset y @empty

La directiva @unless es utilizada es lo contrario de @if si if está esperando un valor positivo (true) unless hace lo mismo pero espera un valor (false)

```
@unless($variable)
@endless
```

Isset verifica si la variable está definida
Empty verifica que no esté vacía o no tiene valor asignado

@emty($variable)
@endempty

#### Directivas Environments

Estás directivas se utilizan para ejecutar varias cosas según en que entorno estamos (desarroll,producción)

```
// Desarrollo
@env('local')
@endenv

// Producción
@env('production')
@endenv

A lo anterior es exactamente igual usar la directiva

@production
    return "hola"
@endproduction
```

#### Directiva Forelse

Este foreach es igual al típico pero en caso de que no tenga datos el array vacío podwemos usar esta variable para hacer algo

```
@forselse ($posts as $post)
    <li></li>

@empty
    return "mensaje"
@endforelse
```

#### Directiva Continue y Break

La directiva continue lo que hace es saltarse la iteración ese elemento al encontrar coincidencia y break hace lo mismo pero saltando la iteración

```
@continue (condición si es que es necesario)
@break (condición si es que es necesario)
```

### Variable $loop

La variable $loop es una variable especial que se utiliza para rastrear el estado de los bucles @foreach en tus vistas.

```
@if ($loop->first)
    return 'primera iteracion'
@endif

@if ($loop->last)
    return 'última iteracion'
@endif

@if ($loop->last)
    return 'última iteracion'
@endif

$loop->iteration --> Muestra el nº de la iteración en la que nos encontramos
```

### Directiva Class

La directiva @class se utiliza para agregar clases CSS dinámicamente a elementos HTML en tus vistas.
Ejemplo:

```
@php
    $isActive = false;
    $hasError = true;
@endphp

<span @class([
    'p-4',
    'font-bold' => $isActive,
    'text-gray-500' => ! $isActive,
    'bg-red' => $hasError,
    'bg-warning' => $loop->first
])></span>

<span class="p-4 text-gray-500 bg-red"></span>
```

### Atributos Adicionales

```
@checked(true)
@checked(false)
@checked(old('name_input'))
```

```
@select(old('name_input'))
```

```
@disabled(true)
@disabled(false)
@readonly(true)
@readonly(false)
@required(true)
@required(false)
```

#### Directiva Include

La directiva @include de Blade en Laravel te permite incluir una vista de Blade dentro de otra vista. Al incluir una vista, todas las variables disponibles en la vista principal estarán disponibles en la vista incluida. Además, puedes pasar datos adicionales que deberían estar disponibles en la vista incluida.

Por ejemplo, para incluir una vista que no siempre está presente, utiliza la directiva @includeIf. Si quieres evaluar una expresión booleana y luego incluir una vista basada en el resultado, utiliza las directivas @includeWhen y @includeUnless. Por último, si deseas incluir la primera vista que existe de una matriz dada de vistas, utiliza la directiva @includeFirst.

Es importante tener en cuenta que si intentas incluir una vista que no existe, Laravel mostrará un error. Para evitar este problema, asegúrate de utilizar la ruta correcta de la vista que deseas incluir. Con la directiva @include, puedes estructurar tu código de Blade de manera más eficiente y reutilizar las vistas en múltiples lugares dentro de tu aplicación Laravel.

```
@include('carpeta.nombrevista')

// Si existe la vista la incluye sino no
@includeif('carpeta.nombrevista')

// Envio de Parametros y en la vista incluida podriamos imprimirla con {{ color }}
@include('carpeta.nombrevista',['color' => 'red'])

// Otros tipos son Includes Condicionales
@includeWhen(true, 'carpeta.nombrevista',['color' => 'red'])
@includeFirst(true, 'carpeta.nombrevista',['color' => 'red'])
```

### Componentes de Clase (IMPORTANTE) son los que tienen un controlador asociado

Cualquier cosa que pongamos entre la etiqueta de cierre y apertura de un componente `<x-alert>Hola</x-alert>`podremos accederla desde el componente con la variable {{$slot}} esto es el slot principal.

También podemos usar variables con nombre para ello haríamos <x-alert name="title">hola</<x-slot>> en el componente {{$title}}
Recirdar si queremos pasar codigo php desde nuestro componente al controlador del componente, debemos usar los :
En la variable {{atributtes}} quedan almacenadas aquellas variables que se están indicando en el componente y que no han sido declarados en el controlador del componente.
Podemos utilizar merge para unir clases

### Componentes Anónimos son los que no llevan controlador asociado

Para recibir valores dentro de un componente anónimo, utilizamos @props([]) dentro de un componente con el nombre de la propiedad definida
Son para hacer componentes pequeños y especificos

### La directiva @stack

Esta directiva a diferencia de yield nos permite apilar todo lo que le indiquemos dentro de las etiquetas push, ejemplo:

```
@push('meta')
    <meta name="description" content="Cursos">
@endpush
```

## Migraciones

La diferencia entre migrate:rollback y migrate:fresh es que el primero solo borra las tablas creads a trave´s de migraciones y el refresh borra todas, incluso las que hemos creado directamente en phpmyadmin

```
php artisan make:migration create_NameTable_table
php artisan make:migration add_NameColum_to_NameTable_table
php artisan make:migration add_slug_to_posts_table
php artisan make:migration drop_NameTable_table
php artisan make:migration alter_to_dataNameTable_table
```

NOTA: si queremos alterar el tipo de un campo en una migración debremos poner `$table->mediumText('row')->change();` pero si ese campo tenía modificadores como defaulto nullable, también debemos meterlo aqui antes del change() porque sino se perderían.

NOTA: Si queremos alterar el nombre de una columna, debemos utilizar el método $table->renameColumn('nombre_actual','nombre_nuevo');
Puede que esto nos de un problema y debamos instalar el paquete doctrine/dbal

NOTA: Cuando creamos tablas si usamos $table->id nos ahorramos poner el binint y el autoincrement, sino deberemos especiaficarlo especificamente.

##### LLaves Foráneas

$table->foreignId('user_id)->contrained(); esto lo que hace es añadir las restricciones como si se hiciera así:
$table->foreign(ùser*id`)->references('id')->on(ùsers`);
\_ESTO ES UNA CONVECION*
Si a un campo le damos un valor null, debe ser NULLABLE ese campo

#### Borrar llaves foraneas

$table->dropForeign('NameIndex') o $table->dropForeign([]'NameRow','NameRow'])

## QUERY BUILDER

### Recuperar Registros de la base de datos (no muchos datos < 10000>)

```
$regs = DB::table('categories')
    ->where('id = ?',2)
    ->get();
    //->first();
return $regs;
```

### Recuperar Registros por Fragmentación (muchos datos > 10000) DATOS TRUNCADOS

```
$regs = DB::table('categories')
    ->orderBy('name','desc')
    //->chunkById()
    ->chunck(1000,function($users){
    foreach($users AS $user){
        echo $user->name;
    }
});
```

### Recuperar Registros por Fragmentación Perezosamente DATOS TRUNCADOS

```
// Esto lo que hace es retornarnos una collection
$regs = DB::table('categories')
    ->orderBy('id')
    //->lazyByid()
    ->lazy()->each(function($user){
        echo $user->name;
    });
```

### Agregados

```
DB:table('users')->where('id',5)->exist()
DB:table('users')->where('id',5)->dosentExist()
DB:table('users')->max('row')
DB:table('users')->min('row')
DB:table('users')->avg('id')
```

### Clausulas de Selección

```
return DB:table('users')->select('id','name AS title')->where('id',5)->exist()
```

### Expresiones sin Procesar

Esto es para crear campos que no existen en la base de datos

```
// FORMA 1
return DB:table('users')
    ->select('id','name AS title',DB::raw('CONTACT(name , " " ,surnames) AS fullname'))
    ->where('id',5)->get)

// FORMA 2
return DB:table('users')
    ->select('id','name AS title')
    ->selectRaw('CONTACT(name , " " ,surnames) AS fullname')
    ->where('id',5)->get();
```

NOTA: esta importante sentencia lo que hace es dejarnos agregar por nuestra cuenta la sql del where sin tener la convención del Query, en general es los mismos sentencias pero añadiendo "Raw"

```
return DB:table('users')
    ->select('id','name AS title')
    ->selectRaw('CONTACT(name , " " ,surnames) AS fullname')
    ->whereRaw('id >= 5')
    ->get();
```

### INNER JOIN

```
return DB:table('users')
    ->join('posts','posts.user_id','=','users.id')
    ->select('post.*','users.name as username')
    ->whereRaw('id >= 5')
    ->get();
```

### WHERE

Se puede usar de esta manera

```
return DB:table('users')
    ->where([
        ['id','>=','5'],
        ['id','<=','10'],
        ['name','=','alex'],
    ])
    ->get();
```

### WHERENOT

Esta clausula lo que hace es como agrupar where y orwhere, sirve para excluir

```
// Esto lo que hace es excluir todos los resultados que coinciden con la consulta
return DB:table('users')
    ->whereNot([
        ['id','>=','5'],
        ['id','<=','10'],
        ['name','=','alex'],
    ])
    ->get();
```

### CLAUSULAS WHERE ADICIONALES

```
// Esto lo que hace es excluir todos los resultados que coinciden con la consulta
return DB:table('users')
    ->whereBetween('id',[5,3,9])
    //->whereNotBetween('id',[5,3,9])
    //->whereIn('id',[5,3,9])
    //->whereNotIn('id',[5,3,9])
    //->whereDate('createdAt','>=','2024-11-23')
    //->whereMonth('createdAt','>=','4')
    //->whereYear('createdAt','>=','2024')
    //->whereTime('createdAt','>=','21:00')
    //->whereColumn('createdAt','>=','updateAt')
    ->get();
```

### AGRUPACIÓN LÓGICA

```
// Esto sive para agrupar consultas y las haga conjunta
return DB:table('users')
    ->where(function($query){
        $query->where('id','>', 5)
        ->orWhere('id','<', 5)
    })
    ->get();
```

### ORDENACION

Podemos hacer ordenes aleatorios con esto

```
->inRandomOrder()
// Esto quitaría lo anterior y traería los datos

Return $users->reorder()->get();
```

### LIMITE

```
// Para limitar los registros es
->take(11)

// Si queremos que se salte los 2 primeros
->skip(2)
->take(11)

// Similar a lo anterior sería
->offset(2)
->limit(11)
```

### CLAUSULAR CONDICIONALES

Esto es muy útil para agregar consultas siempre que una condición se cumpla
Ejemplo:

```
$prueba = true;
return DB::table('users')
    ->when($prueba, function($query){
        $query->where('id','>=',10);
    })
    ->get();

// Otra prueba
$prueba = 15;
return DB::table('users')
    ->when($prueba, function($query,$prueba){
        $query->where('id','>=',$prueba);
    })
    ->get();
```

### INSERTAR REGISTROS

```
// 1 Registo
DB:: table('users)->insert([
    'name' => 'alex',
    'title' => 'asdad'
]);
```

```
// Si uno de los registros falla, ignora ese registro y sigue insertando
DB:: table('users)->insertorignore([
    'name' => 'alex',
    'title' => 'asdad'
],
[
    'name' => 'alex',
    'title' => 'asdad'
]);
```

### UPSERT

Esta acción lo que hace es insertar registros y en caso de que exista lo actualiza

```php
DB:: table('users')->upsert([
        'name' => 'alex',
        'title' => 'asdad'
    ],
    [
        'name' => 'alex',
        'title' => 'asdad'
    ]);
```

### ACTUALIZAR REGISTROS

```php
DB:: table('users')->update([
    'name' => 'alex',
    'title' => 'asdad'
])->where('id',5);
```

```php
DB:: table('users')->updateOrInsert(
    ['email' => 'alex@alex.com'],
    [
    'name' => 'alex',
    'title' => 'asdad',
    'email' => 'alex@alex.com'
    ]
)->where('id',5);
```

### INCREMENTAR Y DECREMENTAR

```php
DB::table('users')
       ->where('id',5);
       ->increment('rating',5)
       ->decrement('rating',4)
```

### ELIMINAR REGISTROS

```php
DB:: table('users')->where('id',5)->delete();
```

### PAGINACION

```php
DB:: table('users')->paginate(15,['id','title']);
DB:: table('users')->simplepaginate(15,['id','title']);
```

## ELOQUENT en vez de Query Builder

Eloquent mapea una tabla de la base de datos a un MODELO en PHP.
Todos los métodos que utilizamos en QueryBuilder podremos usarlos en Eloquent.

### Asignación Masiva

```php
$data = [
    'name' => 'Pc',
    'active' => true
];
$category = Category::create($data)
```

```php
$data = [
    'name' => 'Pc',
    'active' => true
];
$category = Category::find(100);
$category = Category::update($data)
```

NOTA: Recordar que hay que activar la asignación masiva y determinar que campos deben validarse con la propiedad `protected $fillable` del MODELO, si no indicamos aquí el nombre del campo, lo ignora en el envio de formulario.

### Eliminar Registros

```php
$category = Category::find(100);
$category->delete();

// Borrar Varios Registros
$categories = [1,2,3,4];
$category = Category::whereIn('id',$categories)->delete();
```

### Factories

Las fábricas de modelos son una herramienta útil para generar datos falsos de forma automatizada en pruebas y seeding de la base de datos. Aprenderemos cómo crear una fábrica de modelos utilizando el comando make:factory de Artisan y cómo definir atributos predeterminados utilizando la biblioteca Faker.
Se ubican en `database/factories` en la wweb fakerphp.github.io tenemos varias definiciones de que nos puede generar.

Una vez creado nuestro Faker deberemos añadirlo a nuestro DatabaseSeeder para añadir el factory e indicarle el nº de registros a crear.
NOTA: no olvidar que la convencion de nombres es NombreModeloFactory
`php artisan make:factory ModeloFactory`

para ejecutar los seeder nos vamos al terminal y ejecutamos `php artisan db:seed`

## Relaciones Eloquent

### Relaciones 1 a 1

```php
hasOne(Modelo)
// Relación Inversa
belongsTo(Modelo)
```

### Relaciones 1 a Muchos

```php
return $this->hasMany(Post::class)

// Relación Inversa
return $this->belongsTo(Category::class)

// Si no sigue convenciones es
return $this->belongsTo(Category::class,'user_id','id');
```

### Relaciones 1 a 1 (3 tablas)

Es una relación Uno a Uno a través de en Laravel 10 entre tres modelos de la base de datos.

```php
return $this->hasOneThrough(Address:class,Profile::class)
```

### Relaciones 1 a Muchos (3 tablas)

```php
return $this->hasManyThrough(Address:class,Profile::class)
```

### Relaciones Muchos a Muchos (es una relación algo extraña)

Debemos crear una tabla intermedia para hacer esto y combinar los nombres en singular de las dos tablas enlazadas

```php
return $this->belongsToMany(Category::class);

// Hay varios métodos para agregar/borrar/actualizar los valores de una tabla pivote
->atach()
->detach()
->sync()
```

### RELACIONES POLIFORMICAS

ANOTACION MIA: Las relaciones poliformicas se utilizan cuando queremos usar una tabla intermedia para guardar cosas como po ejemplo comentarios, que pueden ser compartidos por diferentes modelos, para asi no tener una tabla para los comentarios de los post, otra para los de productos, etc.
Por ello creamos una tabla especial intermedia donde guardamos el id, y el modelo al que hace referencia.

### Relaciones Polifórmicas 1 a 1 (INTERESANTE)

Si estás trabajando en un proyecto de Laravel 10 y necesitas establecer relaciones Uno a Uno Polimórficas entre dos o más modelos de la base de datos, estás en el lugar correcto. En este capítulo, te enseñaremos cómo definir y acceder a esta relación en Laravel 10.

Las relaciones Uno a Uno Polimórficas se utilizan cuando tienes varios modelos que necesitan estar relacionados con otro modelo de forma individual, en lugar de tener una relación directa. En Laravel 10, puedes definir esta relación mediante el uso de métodos en los modelos correspondientes.

Para establecer una relación Uno a Uno Polimórfica en Laravel 10, debes crear una tabla de relación polimórfica que contenga las claves foráneas de ambos modelos, y establecer los métodos morphOne y morphTo en los modelos correspondientes. Por ejemplo, si tienes un modelo Comment, un modelo Post y un modelo Video, y cada uno puede tener un único Comment

Para hacer este tipo de relaciones que sería una tabla de imagenes por ejemplo de todas las tablas, tenemos que tener dos campos uno llamado imageable_id y otro imageable_type los cuales uno guarda el Id y otro el Modelo al que se referencia.

En cuanto a las migraciones, en vez de crear estos dos campos, podemos crear la instrucción `$table->morphs('imageable')`y creará dichos campos
Luego hay que ir a los modelos y crear la relación poliformica

```php
    return $this->morphOne(Image::class,'imageable');
```

### Relaciones Polifórmicas 1 a Muchos (INTERESANTE)

Para establecer una relación Uno a Muchos Polimórfica en Laravel 10, debes crear una tabla de relación polimórfica que contenga las claves foráneas de ambos modelos, y establecer los métodos morphMany y morphTo en los modelos correspondientes. Por ejemplo, si tienes un modelo Comment, un modelo Post y un modelo Video, y cada uno puede tener varios Comment

```php
    return $this->morphMany(Image::class,'imageable');
```

### Relaciones Polifórmicas Muchos a Muchos

```php
    return $this->morphedByMany(Image::class,'taggable');
```

## FORMULARIOS

### Form Request

Esto es para separar las validaciones del controlador en un archivo externo y en controlador en vez de llamar a Post llamariamos a PostRequest, además con esto podemos compartir alguna regla
común.

```php
php artisan make:request FormRequest
```

-> firstOrCreate()

## FILE STORAGE (SUUBIR IMÁGENES)

Hay que configurar el config/filesystems.php
MUY IMPORTANTE: si no tenemos el link creado en la carpeta public/storage deberemos ejecutar el comando `php artisan storage:link`
Es muy importante también configurar nuestro .env e indicar `public`si queremos que nuestros archivos sean accesibles desde la web.

### Facade Storage

```php
// Sube una imagen a la carpeta post y me retorna el path
if ($request->file('nombreInout')){
    $data['image_path'] = Storage::put('post',$request->image);
}

// Si lo que queremos es cambiar la imagen, antes de hacer lo anterior deberemos borrar lo que había
if ($post->image_path){
    Storage::delete($post->image_path);
}

// Para cambiar el nombre al archivo personalizado para SEO por ejemplo
// Para ello utilizaremos el Storage::putFileAs y podemos utilizar el slug por ejemplo
$file_name = $post->slug . "." . $request

// Verificar si una imagen Existe para no machacar, agregaremos una validación en el controlador
$path = "post/prueba.png";
if (Storage::exist($path)){
    $path = str_replace('.png','-copia.png',$path);
}
```

### Request Store

Otra forma de subir archivos es con Request Store del objecto REQUEST

```php
    $data['image_path'] = $request->file('image')->store('post');
    return Storage::download($path);
```

NOTA: el paquete interventionImage es un paquete de Laravel para tratar las imágenes, redisensionar, poner filtros, etc.

## JOBS

Para crear un job deberemos ir al terminal y escribir `php artisan make:job NombreJob`esto nos crea un archivo en la carepta `Http/Jobs`esta clase creada podemos por ejemplo guardar las tareas de redimensionamiento de imágenes. Solemos usar los jobs frente onservables, cunado el código a tratar no está relacionado con un Modelo específico y cuyo código puede ser utilizado en distintas partes de la aplicación.

En el constructor de esta clase podemos pasarle un path, para ejecutar un job desde el controlador simplemente llamaos a clase `NombreJob::dispatch($path_image)`

### Queues (Colas)

Esto lo que hace es poner en cola este proceso, tal vez no haga falta para redimensionar una imagen, por ello ponemos esa tarea en cola y el proceso puede seguir adelante.
Primero devemos definir el driver para las colas, y utilizamos el driver `DATABASE` para crear las colas deberemos ejecutar `php artisan queue:table` y esto creará una nueva migración.
Luego ejecutamos la nueva migración y nos vamos a nunestro .env y en el parámetro QUEUE_CONNECTION ponemos `database`

Para ejecutar las colas, debemos ejecutar `php artisan queue:work`

## Middleware y Seguridad

Son clases intermedias. para crear un middleware es `php artisan make:middleware NombreMiddelware`. Es bueno que cuando creemos un middleware le asignemos un ALIAS
Es muy facil por ejemplo para poder autizar páginas de acceso a un usuario.

## Gates

Los Gates en Laravel son una característica de seguridad que te permiten controlar el acceso a ciertas acciones y rutas dentro de tu aplicación.
Los gates parece algo interesante para mostrar solo ciertas partes de la web a un determinado usuario por ejemplo admin, para ello deberemos irnos al fichero `Providers/AuthServiceProvider.php`y en el boot creamos un gate

```php
Gate::define('nombreGate',function($user){
    return $user->is_admin; //boolean
});

// En la pantilla blade pondremos
@can('nombreGate')
    componente o mosrtrar
@endcan
```

```php
// Parar parametros
Gate::define('nombreGate',function($user,$post){
    return $user->id === $post->user_id; //boolean
});


```

La diferencia de utilizar un GATE o un MIDDLEWARE es que si queremos que al usuario le salga un mensaje 403 de ruta no autorizada, debemos usar un GATE, si en cambio queremos que sea redireccionado deberemos usar un MIDDLEWARE

## POLICIES

`php artisan make:policy PostPoliciy` HAY QUE SEGUIR ESTA CONVENCIÓN
Esto creará una nueva carpeta y aqui podremos traspasar todo los GATES reflejados a este Modelo y aquí crearemos un método para controlar ese Gate.

## ROLES

Una vez hemos creado los roles, en las rutas podemos hacer uso de esos roles/permisos para dar permiso a secciones. Por Ejemplo:

```php
Route::resource('/categories',CategoryController::class)
    ->except('show')
    ->middleware([ca:Accesp a Categorias])
```

También podríamos hacerlo desde el controlador, añadiendo en el constructor

```php
    ->middleware([ca:Accesp a Categorias])
```

Para ocultar los enlaces podemos hacerlo de esta forma:

## SCOPES LOCALES Y GLOBALES

Optimiza tu tiempo y esfuerzo al escribir consultas repetitivas en Laravel utilizando Query Scopes globales. Con Query Scopes globales, puedes aplicar condiciones de manera global en todas las consultas de un modelo, evitando la repetición de código y mejorando la legibilidad de tu aplicación. En este tutorial, aprenderás a crear y utilizar Query Scopes globales en Laravel. Te mostraremos cómo definirlos en tus modelos, cómo aplicarlos a tus consultas y cómo encadenarlos con otros Query Scopes y consultas. Además, te mostraremos cómo utilizar los Query Scopes globales en combinación con otros elementos de Laravel, como las relaciones Eloquent, los eventos de modelo y las mutaciones de modelo. Aprenderás a ahorrar tiempo y esfuerzo en la escritura de consultas en Laravel con Query Scopes globales. ¡Sigue leyendo y descubre cómo utilizarlos!

## Eventos y Oyentes

`php artisan make:event NameEvent` para crear el evento
`php artisan make:listener NameListener` para crear el oyente

## LIVEWARE

`php artisan make:liveware NombreComponente`

La carpeta node_modules NO SE PASAS A PRODUCCIÓN

Para crear un controlador con un modelo asociado utilizando `php artisan`, la opción correcta sería usar la bandera `--model`. Aquí está el comando correcto:

```bash
php artisan make:controller Admin/ConfigOfficeController --model=Office --resource
```

### Desglose del comando:

-   `make:controller`: Comando para crear un controlador.
-   `PostController`: El nombre del controlador que deseas crear.
-   `--model=Post`: Asocia el modelo `Post` al controlador, generando los métodos que facilitan trabajar con dicho modelo (si usas la opción `--resource`).

Si también deseas generar un controlador con métodos RESTful predefinidos (index, create, store, etc.), puedes agregar la opción `--resource`:

```bash
php artisan make:controller PostController --model=Post --resource
```

Esto es útil si planeas trabajar con rutas y acciones comunes como en un CRUD.

# :page_facing_up: FORMACIÓN LARAVEL 11

---

## :sparkles: ROUTES

Las rutas se guardan en routes/web.php y se debe definir el tipo de peticion

-   GET
-   POST
-   PUT
-   PATCH

```php
Route::get('/test/{id}/{category?}', function ($id, $category = null) {
    //return "hola Alex este es el post" . request('id');
    return ($category) ? "hola Alex este es el post {$id} y la categoria es {$category}" : "hola Alex este es el post {$id}";
});

// Language Translation
Route::get('index/{locale}', [App\Http\Controllers\HomeController::class, 'lang']);

Route::get('/', [App\Http\Controllers\HomeController::class, 'root'])->name('root');
```

## :sparkles: ROUTES CON Y SIN NOMBRE

### Contexto del Ejemplo

Supongamos que estás desarrollando una aplicación de administración de productos. Tienes un controlador llamado `ProductController` que maneja las operaciones relacionadas con los productos, como listar todos los productos, mostrar un producto específico, crear, editar y eliminar productos.

### Ejemplo Explicado

Aquí tienes una ruta para listar todos los productos y una para mostrar un producto específico por su ID:

```php
// Ruta para listar todos los productos
Route::get('/products', [ProductController::class, 'index'])->name('products.index');

// Ruta para mostrar un producto específico
Route::get('/products/{id}', [ProductController::class, 'show'])->name('products.show');
```

### Detalles del Ejemplo

1. **Ruta para Listar Productos:**

    ```php
    Route::get('/products', [ProductController::class, 'index'])->name('products.index');
    ```

    - **URL:** La ruta responde a una solicitud HTTP GET en la URL `/products`.
    - **Controlador y Método:** Está asociado al método `index` del `ProductController`, que se encarga de listar todos los productos.
    - **Nombre de la Ruta:** Se le asigna el nombre `'products.index'`, lo que permite referirse a esta ruta en otras partes de la aplicación usando ese nombre en lugar de la URL directa.

    **Ejemplo de uso:**

    ```php
    // Generar la URL para listar productos
    $url = route('products.index'); // Resultado: "/products"

    // Redireccionar a la página de productos
    return redirect()->route('products.index');
    ```

2. **Ruta para Mostrar un Producto Específico:**

    ```php
    Route::get('/products/{id}', [ProductController::class, 'show'])->name('products.show');
    ```

    - **URL:** La ruta responde a una solicitud HTTP GET en la URL `/products/{id}`, donde `{id}` es un parámetro dinámico que representa el ID del producto.
    - **Controlador y Método:** Está asociado al método `show` del `ProductController`, que se encarga de mostrar los detalles de un producto específico.
    - **Nombre de la Ruta:** Se le asigna el nombre `'products.show'`.

    **Ejemplo de uso:**

    ```php
    // Generar la URL para mostrar un producto con ID 5
    $url = route('products.show', ['id' => 5]); // Resultado: "/products/5"

    // Redireccionar a la página del producto con ID 5
    return redirect()->route('products.show', ['id' => 5]);
    ```

### Beneficios de Usar Nombres de Ruta

1. **Mantenibilidad:** Si cambias la URL de la ruta, no necesitas actualizar todos los lugares donde se usa la ruta. Solo cambias la URL en la definición de la ruta.
2. **Claridad:** Usar nombres de rutas hace que el código sea más claro y fácil de leer, ya que indica la intención del código.
3. **Conveniencia:** Generar URLs y redirecciones es más sencillo y menos propenso a errores cuando se usan nombres de rutas.

### Resumen

-   **Con `name('products.index')`:** Puedes referirte a la ruta que lista todos los productos usando `'products.index'`.
-   **Con `name('products.show')`:** Puedes referirte a la ruta que muestra un producto específico usando `'products.show'`.

Esto ayuda a que el código sea más limpio y fácil de mantener.

## Route Resources

> El comando php artisan route:list o php artisan r:l --> Lo que hace es listar todas las rutas que tenemos definidas en nuestro archivo
> La ruta resource lo que hace es crear el listado de rutas en 1 sola linea, por ejemplo todas estas lineas de rutas puedes ser sustituidas por una sola
> Ejemplo:

```php
// Familias
Route::get('/familias/listar', [FamilyController::class, 'index'])->name('familys.list');
Route::get('/familias/detalles/{id}', [FamilyController::class, 'show'])->name('familys.details');
Route::get('/familias/crear', [FamilyController::class, 'create'])->name('familys.create');
Route::post('/familias/crear', [FamilyController::class, 'store'])->name('familys.store');
Route::get('/familias/editar/{id}', [FamilyController::class, 'edit'])->name('familys.edit');
Route::post('/familias/editar/{id}', [FamilyController::class, 'update'])->name('familys.update');
Route::get('/familias/eliminar/{id}', [FamilyController::class, 'delete'])->name('familys.delete');
```

```php
Route::resource ('familias',FamilyController::class);
```

Esto creará todas las rutas, pero sino queremos que no se cree alguna ruta tan solo debemos añadir esto

```php
Route::resource ('familias',FamilyController::class)->except(['destroy','edit']);
```

O solo querememos que nos cree unas en especifico

```php
Route::resource ('familias',FamilyController::class)->only(['create','update']);
```

> Hay un total de 7 rutas para hacer el CRUD

Además podemos ver con que nombre se nombrarán nuestras rutas.

```php
Route::resource ('familias',FamilyController::class)->only(['create','update'])->names('post');
```

También puedo especificar parametros en esta ruta de recurso

```php
Route::resource('familias',FamilyController::class)
->only(['create','update'])
->parameters(['familia'=>'family'])
->names('post');
```

> Esto lo que hace es cambiar el nomnbre del parametro a recibir de familias por family

En cuanto a las rutas de API tan solo tienen 5 métodos de CRUD

```php
Route::apiResource('familias',FamilyController::class)
```

## Routes Model Binding

Las rutas de modelo lo que hace es simplificar el código en blade podemos ir a un registro de la siguiente manera

```php
// Tan solo indicando el objecto $post, LARAVEL ya sabe que debe utilizar la ID no hace falta poner $post->id
{{ route('familys.edit',$post) }}
```

En cuanto al controlador hacemos un cambio para ahorrar 1 linea pasamos de esto

```php
    public function show($id)
    {
        $family = SysFamily::find($id);

        return response()->json($family);
    }
```

a

```php
    public function show(SysFamily $id)
    {
        //$family = SysFamily::find($id);

        return response()->json($family);
    }
```

Indicando solo en el controlador el Modelo a utilizar, y ya sabe LARAVEL que debe utilizar ID

Ahora bien, si queremos que se utilice otro tipo de valor que no sea el ID deberemos ir al método y crear un método public para decile que utilice otro valor

```php
public function getRouteKeyName(){

    return 'slug';
}
```

## :sparkles: CONTROLLERS

En vez de hacer los procesos en las rutas, esto lo hacemos con los controladores

```bash
php artisan make:controller ItemsController
```

### Método \_Invoke

Sí, el método `__invoke` sigue utilizándose en Laravel 11. En Laravel, el método `__invoke` es una característica de PHP que permite a los objetos ser llamados como si fueran funciones. Esto es útil para crear controladores de una sola acción, middleware, y otros componentes que necesitan comportarse como funciones pero aún así mantener la flexibilidad de las clases.

Por ejemplo, en Laravel puedes definir un controlador de una sola acción así:

```php
namespace App\Http\Controllers;

class MyController
{
    public function __invoke()
    {
        return 'Hello, world!';
    }
}
```

Y luego lo registras en las rutas como una ruta de controlador de una sola acción:

```php
use App\Http\Controllers\MyController;

Route::get('/hello', MyController::class);
```

En este ejemplo, cuando se hace una solicitud a `/hello`, Laravel invoca el método `__invoke` de `MyController`.

Laravel sigue soportando este patrón en versiones recientes como Laravel 11, ya que es una técnica conveniente y elegante para manejar rutas o middleware de una sola acción.

## :sparkles: Modelos y Migraciones

Para generar una migración y su modelo asociado al mismo tiempo en Laravel, puedes usar el siguiente comando:

```bash
php artisan make:model NombreModelo -m --path=database/familia/migration
```

Donde:

-   `NombreModelo` es el nombre del modelo que deseas crear.
-   La opción `-m` le indica a Laravel que cree también una migración junto con el modelo.
-   La opción `--path=database/familia/migration` le indica a Laravel que coloque la migración en la carpeta personalizada `familia/migration`.

### Ejemplo

Si deseas crear un modelo llamado `Producto` con su migración en la carpeta `familia/migration`, el comando sería:

```bash
php artisan make:model Producto -m --path=database/familia/migration
```

### Resumen de pasos:

1. **Crea el modelo y la migración juntos**:

    ```bash
    php artisan make:model NombreModelo -m --path=database/familia/migration
    ```

2. **Ejecuta las migraciones de la carpeta personalizada**:

    ```bash
    php artisan migrate --path=/database/familia/migration
    ```

    php artisan make:migration add_new_column_to_customers_agreement_table --table=customers_agreement --path=database/migrations/indira

Con esto, Laravel generará tanto el modelo como la migración en la ubicación que especificaste.

## :sparkles: Vistas

`php artisan make:view \_test/create`

Para crear una vista utilizamos esta nomenclatura si queremos meterlo o crear un directorio, ponemos le nombre del directorio seguido del nombre de la vista.

**NOTA:** utilizar el nombre de la vista con el nombre del método nos ayudará a tener mejor organizado todo

## Envio de variables a las vistas

```php
public function show($id)
{
    return view(
        '_test.show',
        ['id' =>  $id]
    );
}
esto de aqui es igual a esto
    public function show($id)
{
    return view('_test.show',compact($id));
}
```

> El capitulo 05 explica los componentes

## :sparkles: Componentes

> Los componentes pueden ser de clases como el que está más abajo que se crea con la instrucción de php artisan y que crea el componente y la clase, y otra forma es crear de forma manual el componente en su carpeta correspondiente. Todo depende de la forma o lógica que llevará dicho componente.

`php artisan make:component <nombre componente>`

> Esto lo que hace es crear 2 archivos por un lado el componente en resources/views/components para ser reutilizado y por otro lado en App/View/Components para poder crear un clase que maneje dicho controlador

Para llamar a un componente desde una plantilla blade utilziamos la zintaxis (desde Blade 11) <x-nombredelcomponete> ejemplo

```
 <x-alert type="info" title="¡Error!" subtitle="Algo salió mal. Por favor, intenta de nuevo.">
</x-alert>
```

e indicamos los atributos

La expresión que mencionas:

```blade
class="alert {{ $attributes->merge(['class' => $alertClass]) }}"
```

es utilizada en Laravel Blade para asignar dinámicamente clases CSS a un elemento HTML. Vamos a desglosarla:

### Desglose de la Expresión

1. **`class="alert"`**:  
   Esta parte establece una clase CSS fija, en este caso, `alert`, que siempre estará presente en el elemento HTML.

2. **`{{ ... }}`**:  
   Los dobles corchetes `{{ }}` son utilizados en Blade para imprimir el valor de una variable o una expresión PHP dentro del HTML. Todo lo que está dentro de los corchetes se evalúa y se inserta en el lugar correspondiente.

3. **`$attributes->merge(['class' => $alertClass])`**:
    - **`$attributes`**: Es una variable especial disponible en los componentes de Blade que contiene todos los atributos HTML adicionales que puedan pasarse al componente cuando se llama.
    - **`merge(['class' => $alertClass])`**: Esta función combina cualquier clase adicional que se pase a través de `$attributes` con la clase dinámica `alertClass`, que normalmente depende de alguna lógica en la clase del componente.

### Ejemplo en Contexto

Supongamos que tienes un componente `Alert` que espera un tipo de mensaje (`danger`, `warning`, `success`, etc.) y, en base a eso, define una clase CSS específica:

**`alert.blade.php`**:

```blade
<div class="alert {{ $attributes->merge(['class' => $alertClass]) }}">
    {{ $slot }}
</div>
```

### Llamada del Componente

Luego, cuando llames al componente en una vista, podrías hacerlo así:

```blade
<x-alert type="danger" class="extra-class">
    Este es un mensaje de peligro.
</x-alert>
```

### ¿Qué Sucede?

-   **`$alertClass`**: Podría ser algo como `alert-danger`, dependiendo del valor de `type` en el componente.
-   **`$attributes->merge(['class' => $alertClass])`**: Combina `extra-class` (pasada en el llamado) con `alert-danger`, resultando en algo como `alert alert-danger extra-class`.

### Resultado Final

El HTML generado sería algo como:

```html
<div class="alert alert-danger extra-class">Este es un mensaje de peligro.</div>
```

Esto permite que las clases CSS sean flexibles y se puedan combinar de manera dinámica según el contexto en el que se utilice el componente.

## :sparkles: Layouts

Creamos una plantilla y ponemos esta etiqueta para decirle que el contenido es variable

```blade
@yield('content')
```

Desde la vista extendemos

```blade
@extends('layouts.nombre-plantilla)
```

y para meter contenido en la plantilla deberemos llamar a la directiva

```blade
@section('content)
contenido que será renderizado en la plantilla
@endsection
```

## :sparkles: Migraciones

```
// Ejecuta las migraciones
php artisan migrate

// Deshace migraciones
php artisan migrate:rollback

// Deshace la última migración
php artisan migrate:rollback --step=1

// Hace todos los down y luego todos los up de las migraciones, pero es un método destructivo porque elimina los registros
php artisan migration:refresh

// Para crear tablas. te genera la estructura base para crear una tabla
php artisan make:migration create\_<nombre_migracion>\_table

// Crear migración
php artisan make:migration <nombre_migracion>

```

> Esto es para migrar solo una parte de las migraciones cuando están organizadas por carpetas, con esto evitamos crear un comando para ello
> php artisan migrate --path=database/migrations/base

### Update Table

```
// Craar update de una migración
php artisan make:migration add*<nombre>\_to*<nombretbla>\_table

// Ejemplo
php artisan make:migration add_new_to_sys_items_table
php artisan make:migration change_id_to_uuid_in_table_name
```

Esta forma no es agresiva ya que no elimina datos

### :sparkles: Modelos con ELOQUENT

```
// Crear Modelo
php artisan make:model Items
```

**Conveciones de modelos:**
Los modelos deben ser en singular y la tabla de la DB en plural asi no hará falta crear la variable
`protected $table=<nombretabla>` porque tomará como base el nombre y el plural en la base de datos, ejemplo

> Si tenemos una tabla llamada sys_items y el modelo se llama SysItem no hará falta indicar la tabla pero por ejemplo si hacemos lo mismo con la tabla sys_familys, dará un error porque ELOQUENT/LARAVEL buscará la tabla sys_families

```php
Route::get('/prueba', function () {

    $fam = SysItem::find(1);
    $fam->title = 'Prueba3333';
    //$fam->type = 'Casetas';
    $fam->active = 1;
    $fam->save();

    return $fam;

});
```

## :sparkles: Mutadores y Accesores

Esto es para normalizar, prevenir y validar los datos de entrada en los formularios

```php
protected function title(): Attribute
{

        return Attribute::make(
            // Mutador
            set: fn($value) => strtoupper($value),

            // Accesor
            get: fn($value) => (
                strtolower($value) . 'hola'
            ),
        );
        // return Attribute::make(
        //     set: function($value){
        //         return strtoupper($value);
        //     }
        // );
    }

    protected function description(): Attribute
    {


        return Attribute::make(
            set: fn($value) => strtolower($value),
        );
```

## :sparkles: SEEDERS

Están dentro de la carpeta /database.

Sirve para insertar datos de ejemplo o sistema de datos base

```
// Ejecutar los seeder
php artisan db:seed

// Para ejecutar el seeder en concreto
php artisan db:seed --class=<nombreseed>

// Elimina/Crea las tablas y ejecuta los seeders
php artisan migrate:fresh -- seed

// Para crear un SEEDER debemos hacer
php artisan make:seeder <NombreSeeder>
php artisan make:seeder FamilysSeed
```

NOTAS: podemos meter los seeders en una carpeta para organizarlos, ya que lo importante es que en el raiz de la carpeta database/seeders este el archivo DatabaseSeeder.php, aqui debemos definir las rutas donde están alojados los seeder a ejecutar.

Hay que fijarse bien en los namespaces para definir las rutas, de los seeder creados.

## :sparkles: FACTORIES

Están dentro de la carpeta /database y sirve para crear datos de ejemplo

```
// Crear un seeder
php artisan make:factory <NombremodeloFactory>

Y esto se pone en el seeder pra ejecutarlo
SysFamily::factory(10)->create();
```

```php
<?php

namespace Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;

/**
 * @extends \Illuminate\Database\Eloquent\Factories\Factory<\App\Models\SysFamily>
 */
class SysFamilyFactory extends Factory
{
    /**
     * Define the model's default state.
     *
     * @return array<string, mixed>
     */
    public function definition(): array
    {
        return [
            //
            'title' => $this->faker->sentence(),
            'type' => $this->faker->randomElement(['Casetas', 'Jardinería', 'Maquinaria']),
            'active' => $this->faker->randomElement([1, 0]),
        ];
    }
}
```

## :sparkles: ASIGNACIÓN MASIVA

```php
    $family = new SysFamily();
    $family->title = $request->title;
    $family->type = $request->type;
    $family->active = $request->active;
    $family->save();
```

esto podriamos conseguir poner asi

```php
    Sysamily::create([
    $family->title = $request->title;
    $family->type = $request->type;
    $family->active = $request->active;
    ]);
```

y esto otro podriamos resumirlo a, pero esto es alo inseguro porque pueden editarnos el formulario y meterse mal todo.

```php
    SysFamily::create($request->all());
```

Para evitar eso debemos hacer esto, deberemos ir al controlador y crear una propiedad protegida con los nombres los campos que podrán usarse para asignación masiva

```php
    protected $fillable = ['title', 'type', 'description', 'active'];
```

También podemos hacer lo contrario, podemos poner que campos NO queremos que se guarden

```php
    protected $guarded = ['active'];
```

Si queremos usar asignación masiva en el método update podremos hacerlo de la siguiente manera

```php
    $sysFamily->update($request->all);
```

## :sparkles: VALIDACIONES

Las validaciones de las entradas de formulario se deben hacer en el método store, y es asi

```php
// Validaciones en la creación
public function store (Request $request){
    $errors = $request->validate([
        'title' => 'required|min:5|max:10', // Forma más corriente
        'slug' => ['required','min:5','max:10'], // Forma más compleja
        'token' => 'required|unique:SysFamily',
    ]);
}

// Validaciones en la edición
public function edit (Request $request){
    $errors = $request->validate([
        'title' => 'required|min:5|max:10', // Forma más corriente
        'slug' => ['required','min:5','max:10'], // Forma más compleja
        'token' => 'required|unique:SysFamily,token,{$family->id}', // Con esto conseguimos que excluya el registro que estamos editando
    ]);
}
```

Además en la plantilla de Blade podemos agregar un div que nos muestre los errores del formulario

```php
@if ($errors->any())
    <div class="alert alert-danger">
        <ul>
            @foreach ($errors->all() as $error)
                <li>{{ $error }}</li>
            @endforeach
        </ul>
    </div>
@endif
```

Además para no perder los datos que no se han validado en ese formulario en el campo <input> debemos poner en el campo value esto

```php
{{old('title')}}
```

> Nota si queremos hacerlo en el formulario de edición debemos hacerlo asi para actualizar los cambios

```php
{{old('title',$family->title)}}
```

Para mostrar el mensaje debajo del campo usamos esto

```php
<form action="{{ route('ruta.de.envio') }}" method="POST">
    @csrf

    <!-- Campo para el nombre -->
    <div class="form-group">
        <label for="name">Nombre</label>
        <input type="text" name="name" class="form-control @error('name') is-invalid @enderror" value="{{ old('name') }}">

        <!-- Mostrar el error del campo "name" -->
        @error('name')
            <div class="invalid-feedback">
                {{ $message }}
            </div>
        @enderror
    </div>
</form>
```

Para ver todas las reglas de validación podemos ir a: https://laravel.com/docs/11.x/validation#available-validation-rules

## :sparkles: VALIDACIONES (Form Request)

'php artisan make:request <StoreCLASERequest>` esto creará el archivo dentro de App\Http\Request

Para ello en nuestro controlador deberemos llamar al objecto Family deberemos hacerlo a StoreFamilyRequest que hará la validación previa

> Revisar este video: https://www.youtube.com/watch?v=CnsaIC9JR4Q&list=PLZ2ovOgdI-kVtF2yQ2kiZetWWTmOQoUSG&index=22

### Traducción de las Validaciones

Usaremos el paquete:
https://laravel-lang.com/basic-usage.html#installation

## :sparkles: LOG DE ERRORES

He instalado el paquete log-viewer y ahora para poder acceder es
For example: http://my-app.test/log-viewer
Para configurar los permisos, creamos un archivo de configuración en config/log-viewer.php donde indico los perfiles??? del middlewer auth utilizado
