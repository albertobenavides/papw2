# Programación de aplicaciones para la Web 2

## Universidad Autónoma de Nuevo León
## Facultad de Ciencias Físico Matemáticas
## Multimedia y Animación Digital

### Alberto Benavides
### Agosto a diciembre 2018

## Qué es [Laravel](https://laravel.com/)

* *Framework* PHP = Marco de trabajo en PHP
* Conjunto de programas, métodos, instrucciones... que realizan una tarea específica
* Tarea específica: Crear una aplicación web

<center><img src="https://nordicapis.com/wp-content/uploads/Laravel-logo.png"></center>

---

## Requisitos
* [Git](https://git-scm.com/): Software de control de versiones
* [Composer](https://getcomposer.org/): Herramienta para manipulación de dependencias de PHP

<center><img src="https://git-scm.com/images/logos/downloads/Git-Icon-1788C.png" width="300px"> <img src="https://getcomposer.org/img/logo-composer-transparent4.png"></center>

---

## Instalación

* El símbolo `^` permite escribir una instrucción de CMD en diferentes líneas
```
composer global require "laravel/installer"
laravel new nombreApp
```

```
composer create-project --prefer-dist ^
laravel/laravel nombreApp
```

---

## Servidor local

```
php artisan serve

rem Entrar a http://localhost:8000
```

---

## Archivo `.env`

* (Revisar) Renombrar archivo `.env.example` a `.env`
* Contiene la información del entorno de la aplicación
* Obtener variables de entorno
```php
// El segundo parámetro de env() es el valor por defecto
'debug' => env('APP_DEBUG', false), // en config/app.php

$environment = App::environment();
```

* ¡No subir al servidor! (incluir en `.gitignore`)

---

## `.gitignore`

* Especifica rutas o archivos que Git ignorará
* Se coloca en la raíz del repositorio
* Para [Laravel](https://github.com/laravel/laravel/blob/master/.gitignore):
```
*node_modules
*vendor
Homestead.json
Homestead.yaml
npm-debug.log
yarn-error.log
.env
.phpunit.result.cache
```

---


## Llave de la aplicación

Permite asegurar sesiones y datos encriptados

1. Generar llave de la aplicación

```
php artisan key:generate
```

2. Agregar la llave al archivo `.env`
```
APP_KEY=base64:...
```

---

## Carpeta `config/app.php`

* Contiene la configuración de variables de la aplicación

```php
// Obtener valores del archivo
$value = config('app.timezone');

// Establecer valores del archivo (en tiempo de ejecución)
config(['app.timezone' => 'America/Chicago']);
```
---


## Instalación del paquete de [Laravel en español](https://github.com/Laraveles/spanish)

1. Descargar paquete
```
composer require laraveles/spanish
```

2. En `config/app.php`
```
'providers' => [
    Laraveles\Spanish\SpanishServiceProvider::class,
];
```
3. Actualizar traducciones
```
php artisan laraveles:install-lang
```
4. Elegir idioma deseado en `config/app.php`
```
'locale' => 'es',
```

## Carpetas principales

* `app`: Modelos de tablas (Usuario)
* `config`: Todas las configuraciones de la aplicación
* `database`: 
	* `migrations`: Especificación de tablas (usuarios)
	* `seeds`: Registros predeterminados de tablas
* `public`:
	* `css`, `js`: Archivos de estilo y scripts
	* `index.php`: Llegada de peticiones de usuarios
* `resources`:
	* `lang`: Contiene los idiomas instalados
	* `views`: Contiene las vistas (páginas) de la aplicación
* `routes/web.php`: Contiene las redirecciones de la aplicación
* `storage`: Archivos generados por cada sesión
	* `storage/app/public`: Usada generalmente para almacenar archivos generados por el usuario

---

## Configuración de autentificación

```
rem cd carpeta/del/proyecto

rem Prepara vistas, modelos y migrations para auth
php artisan make:auth

rem Corre las migraciones
php artisan migrate
```

---

## Ver cambios en el repositorio

* `app`
* `database/migrations`
* `resources/views`

---

## Introducción a `blades`

* Padres
	* `@yield('variable')`: Variable que recupera del "hijo"
```php
@section('parte')
  <p>Contenido del padre</p>
@show
```

* Hijos
	* `@extends('carpeta.nombre')`: `blade` de la que hereda
	* `@section('variable', 'Valor')`: Valor para de `@yield`
```php
@section('parte')
    @parent <!-- Agrega contenido del padre (prepend) -->
    <p>Se añade esto tras contenido del padre (append).</p>
@endsection
```

## Introducción

* Ubicación: `resources/views`
* Extensión: `.blade.php`
* Utilizan lenguaje `PHP`
* Se dividen en:
	* "Padres": Estructura base
	* "Hijos": Heredan estructura del padre
---

## Estructura básica de "padre"

```
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, 
      initial-scale=1, shrink-to-fit=no">
    <title>Padre @yield('title')</title>
    <link href="{{asset('css/app.css')}}" rel="stylesheet">
  </head>
  <body>
    @section('section1')
    <h2>Sección 1</h2>
    <p>Muestra esta línea aunque no haya hijo.<p>
    @show
    
    @yield('section2')
    
    @include('otroHijo')
  </body>
</html>
```

---

## Estructura básica del "hijo" y del "otroHijo"

```
<!-- Hereda estructura de blade layouts/padre.blade.php -->
@extends('layouts.padre')

{{-- Variable y valor --}}
@section('title', 'de Hijo')

@section('section1')
  @parent {{-- Hereda del padre --}}

  <p>Agrega este párrafo al padre.</p>
@endsection

@section('section2')
  <h2>Sección 2</h2>
  <p>Contenido de la sección 2. Wow.</p>
@endsection
```

```
<p>Soy el otro hijo menos importante</p>
```

---

## Añadir la ruta (mapeo)

* Entrar al archvo `routes/web.php`
* Agregar la ruta:

```
Route::get('rutaGet', function () {
    return view('nombreBlade');
});
```

---

## Enviar información a una ruta

Agregar un modelo a una ruta[ * ](https://stackoverflow.com/questions/28607829/how-to-call-models-in-laravel-5):

```
use App\User; // Especifica que se usará el modelo User

Route::get('hijoInformado', function () {
  return view(
    'hijo',  
    ['users' => User::all()] // Nombre y Parámetro enviado
  );
});
```
O en el Controller ubicado en `app/Http/Controllers`, por ejemplo:
```php
public function index(){
  return view(
    'home',
    ['users' => User::all()]
  );
}
```

---

## Recibir información y manipularla

```
@isset($users) <!-- Si existe la variable $users -->
  <h3>Todos los usuarios</h3>
  <ul>
    @foreach ($users as $u) {{-- Por cada elemento --}}
      {{-- {{ nombreVariable }} muestra en HTML --}}
      <li>{{ $u->name }}</li>
    @endforeach
  </ul>
@endisset
```

* Para evitar compilar variables desde Laravel

```
@{{ nombreVariable }} {{-- Framework de llaves dobles --}}

@verbatim
{{ muchas }}
{{ variables }}
{{ de otro framework que use llaves }}
@endverbatim
```

---

## Condicionales

```
@if ($condicion))
  Se cumplió la condición
@elseif ($otraCondicion)
  Se cumplió otra condición
@else
  No se cumplió ninguna de las condiciones anteriores :c
@endif
```

```
@unless (Auth::check())
    No se ha iniciado sesión
@endunless
```

```
@auth
  El usuario ha iniciado sesión
@endauth

@guest
  El usuario es un invitado
@endguest
```
---

## Ciclos

```
@for ($i = 0; $i < 10; $i++)
  {{ $users[$i]->name }}
@endfor

@foreach ($users as $u) {{-- ¡Al revés! --}}
  <p>{{ $u->email }}</p>
@endforeach

@foreach ($users as $u) <!-- ¡Al revés! -->
  @if ($u->email == "un@correo.com")
    {{-- Salta al siguiente índice --}}
    @continue
  @else@if ($u->email == "otro@correo.com")
    @break {{-- Termina el ciclo --}}
  @endif
  <p>{{ $u->email }}</p>
@endforeach
```

---

## Entorno `@php`

```
@php
    // Aquí puedes escribir código php puro
@endphp
```

---

## Agregar `CSS` o `javascript`

```html
<link href="{{secure_asset('css/estilo.css')}}" 
  rel="stylesheet">
```

---

## Middlewares

* Mecanismo de filtrado de peticiones `HTTP`
* Equivalente a capas de filtrado
* Ubicación: `app/Http/Middleware`

---

## Crear middleware

```
php artisan make:middleware Nombre
```

```php
public function handle($request, Closure $next){
  
  // Si la petición tiene el valor de la variable
  if ($request->variable == "valor") {
    // Redirige a la vista home
    return redirect('home');
  }

  // Pasa a la siguiente petición
  return $next($request);
}
```

---

## Antes o después de la petición

```
public function handle($request, Closure $next){
  // Antes de la petición
  
  return $next($request);
}
```

```
public function handle($request, Closure $next){
  $response = $next($request);
 
  // Después de la petición
  
  return $response;
}
```

---

## Middleware globales

Se registran en `app/Http/Kernel.php`

```php
protected $middleware = [
  // [...]
  \App\Http\Middleware\Nombre::class,
];
```

---

## Middleware para rutas

Se asignan en `app/Http/Kernel.php` a:

* Un middleware
```php
protected $routeMiddleware  = [
  // [...]
  'nombre' => \App\Http\Middleware\Nombre::class,
];
```

* Grupos de middleware

```php
protected $middlewareGroups   = [
  'grupo' => [
    'uno:parametro1, parametro2',
    'dos',
  ],
];
```

---

## Parámetros

```php
public function handle($request, Closure $next, $role){
  if ($request->user()->hasRole($role)) {
    // Alguna acción
  }

  return $next($request);
}
```

---

## Asignar a una ruta

```php
Route::get('ruta', function () {
  // Un middleware
})->middleware('nombre');

Route::get('ruta', function () {
  // Varios middleware
})->middleware('uno', 'dos');

Route::get('ruta', function () {
  // Varios middleware
})->middleware('grupo');

Route::get('ruta', function () {
  // Un middleware
})->middleware('nombre:variable');
```

---

## Asignar a un controlador

Ubicado en `app/Http/Controllers`
```php
public function __construct(){
  $this->middleware('nombre');
}
```

---

## Definición CSRF

* **C**ross-**s**ite **R**equest **F**orgery
* Intento de ataques por parte de usuarios no autorizados
* Por cada usuario autorizado, se genera un token CSRF
* `VerifyCsrfToken `: *Middleware* que solicita CSRF
	* Peticiones `POST`
	* Peticiones `X-CSRF-TOKEN` (p. e., encabezados de `ajax`)

---

## Usos

* Formularios

```html
<form method="POST" action="/profile">
  @csrf
</form>
```

* Encabezados de `ajax`
```html
<meta name="csrf-token" content="{{ csrf_token() }}">
```

```
$.ajaxSetup({
  headers: {
    'X-CSRF-TOKEN': 
      $('meta[name="csrf-token"]').attr('content')
  }
});
``` 

---

## Definición Controladores

* Clases 
* Organizan el comportamiento de peticiones
* Ubicación: `app/Http/Controllers`
* Los controladores se pueden llamar desde una ruta

---

## Estructura básica

```php
// app/Http/Controllers/UserController.php
namespace App\Http\Controllers;
use App\User;
use App\Http\Controllers\Controller;

class UserController extends Controller{
  public function show($id){
    return view(
      'user.profile', 
      ['user' => User::findOrFail($id)]
    );
  }
}
```

```php
// routes/web.php
Route::get('user/{id}', 'UserController@show');
```

---

## Controladores de una acción

```cmd
php artisan make:controller ShowProfile --invokable
```

```php
// app/Http/Controllers/UserController.php
namespace App\Http\Controllers;
use App\User;
use App\Http\Controllers\Controller;
class ShowProfile extends Controller{
  public function __invoke($id){
    return view(
    	'user.profile', ['user' => User::findOrFail($id)]
    );
  }
}
```

```php
// routes/web.php
Route::get('user/{id}', 'ShowProfile');
```

---

## Añadir `Middlewares` a `Controllers`

### Método 1. A través de la ruta

```php
Route::get(
  'profile', 
  'UserController@show'
)->middleware('auth');
```

### Método 2. A través del constructor del `Controller`

```php
class UserController extends Controller{
  public function __construct(){
    $this->middleware('auth'); // A todos los métodos
    $this->middleware('log')->only('index'); // A index
    // Todos los métodos excepto store
    $this->middleware('subscribed')->except('store');
  }
}
```

---

## Controlador de recursos

* Controladores que permiten controlar un modelo [**CRUD**](https://www.codecademy.com/articles/what-is-crud):
	* **C**reate
	* **R**ead
	* **U**pdate
	* **D**elete

---

## Crear controlador de recursos

```cmd
rem Crear controlador de recursos de artículos
php artisan make:controller ArticleController --resource
```

Petición|URI|Método
---|---|---
GET|`/articles`|`index()`
GET|`/articles/create`|`create()`
POST|`/articles`|`store()`
GET|`/articles/{id}`|`show()`
GET|`/articles/{id}/edit`|`edit()`
PUT/PATCH|`/articles/{id}`|`update()`
DELETE|`/articles/{id}`|`destroy()`

---

## Almacenamiento de archivos en Laravel

* Los archivos se pueden guardar en:
    * Carpeta `storage/app/public`: Desde el navegador
    * Carpeta `storage/app`: Desde la aplicación web
    * Nube (Amazon) `s3`: Desde servidor externo
* Crear un enlace simbólico a `public/storage`
```cmd
php artisan storage:link
```
* Ruta del archivo `ejemplo.txt`
```php
asset('storage/ejemplo.txt')
```
* Heroku borra archivos almacenados cada que se despliega la aplicación

---

## Crear formulario de archivos

```php
<form action="/fotos" 
  enctype="multipart/form-data" method="post">
  @csrf
  <p>
    <label for="foto">
      <input type="file" name="foto" id="foto">
    </label>
    </p>
    <input type="submit" value="Enviar">
</form>
```

---

## Crear ruta para guardar archivo

```php
// Agregar Request
use Illuminate\Http\Request;

// Declarar ruta para petición post
Route::post('fotos', function (Request $request) {
  // Obtener la dirección donde se guarda
  $dir =
    // De la petición 
    $request->
    // Obtener el archivo con name=foto
    file('foto')->
    // Y almacenarlo en la carpeta fotos
    
    store('fotos'); 
    // store('fotos', 'public');
    // storeAs('fotos', 'foto.jpg', 'public');
    
  // Retorna la vista welcome con path
  return view('welcome', ['path' => $path]);
});
```

---

## Validación

* Comprueba subida correcta de archivos
* Evita ataques

```php
$validation = $request->validate([
  'foto' => 'required|file|image|mimes:jpeg,png|max:2048'
]);

// Obtiene el archivo validado
$file = $validation['foto'];

// Obtiene la extensión del archivo
$ext = $file->getClientOriginalExtension();

// Crea el nombre e archivo
$nombreArchivo  = 'foto' . time() . '.' . $extension;
```

---

## Métodos útiles

```php
// Descargar el archivo
Storage::download('ejemplo.txt');

// Obtener todos los archivos
Storage::files

// Obtener todos los archivos de una carpeta
Storage::allFiles($unaCarpeta);

// Obtener tamaño del archivo
Storage::size('file.txt');

// Borrar archivo
Storage::delete('file.txt');

// Obtener url pública del archivo
Storage::url('ejemplo.txt');

// Obtener url temporal del archivo
Storage::temporaryUrl('ejemplo.txt', now()->addMinutes(5));
```

---

## Laravel *sessions*

Crear una tabla de sesiones

```java
Schema::create('sessions', function ($table) {
  $table->string('id')->unique();
  $table->unsignedInteger('user_id')->nullable();
  $table->string('ip_address', 45)->nullable();
  $table->text('user_agent')->nullable();
  $table->text('payload');
  $table->integer('last_activity');
});
```

Atajo:

```cmd
php artisan session:table
```

---

## Guardar elementos en la sesión

```php
// En petición
$request->session()->put('llave', 'valor');

// De manera global
session(['llave' => 'valor']);

// Guardar elemento de un arreglo
$request->session()->push('arreglo', 'elemento');
```

---

## Utilizar sesiones desde petición

```php
$valor = $request->session()->get('llave');

$value = $request->session()->
  get('llave', 'valorDefecto');

$value = $request->session()->get('llave', function () {
  return 'valorDefecto';
});

// Obtiene el valor y borra la llave
$value = $request->session()->pull('llave','valorDefecto');
```

---

## Valores efímeros

```java
// Vive sólo la siguiente petición
$request->session()->flash('estado', 'Moribundo');

// Mantiene vivos los flash una petición más
$request->session()->reflash();

// Mantiene los flash especificados
$request->session()->keep(['este', 'otro']);
```

---

## Utilizar sesiones globales

```php
$value = session('llave');

$value = session('llave', 'valorDefecto');

session(['llave' => 'valorDefecto']);
```

---

## Instricciones útiles

```php
// Obtener todas las llaves
$data = $request->session()->all();

if ($request->session()->has('llave')) {
    // Si tiene llave
}

// Borrar llave
$request->session()->forget('llave');

// Borrar todas las llaves
$request->session()->flush();
```

---

# Fuentes

* https://laravel.com/docs/5.6
* https://github.com/Laraveles/spanish
* https://laravel.com/docs/5.6
* https://laravel.com/docs/5.6/authentication
* https://laravel.com/docs/5.6/blade#template-inheritance
* https://laravel.com/docs/5.6/blade
* https://laravel.com/docs/5.6/csrf
* https://laravel.com/docs/5.6/middleware
* https://scotch.io/tutorials/understanding-laravel-middleware
* https://laravel.com/docs/5.7/controllers
* https://scotch.io/tutorials/a-guide-to-using-eloquent-orm-in-
* https://scotch.io/tutorials/understanding-and-working-with-files-in-laravel
* https://laravel.com/docs/5.7/filesystem
* https://laravel.com/docs/5.7/requests#files
* https://laracasts.com/discuss/channels/requests/error-when-uploading-a-file-call-to-undefined-method-illuminatesupportfacadesrequestfile
* https://laravel.com/docs/5.7/session