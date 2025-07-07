# Guía Completa de Rutas en Laravel 
## ¿Qué son las Rutas en Laravel?

Las rutas son como las **direcciones de tu aplicación web**. Cuando alguien visita tu sitio (por ejemplo: `miapp.com/contacto`), Laravel necesita saber qué mostrar. Las rutas son las que le dicen: "cuando alguien vaya a `/contacto`, muestra esto".

**Piénsalo así:** Las rutas son como los letreros en una ciudad que te dicen qué hay en cada dirección.

- Las rutas se definen en el archivo `routes/web.php`
- Cada ruta conecta una URL con una acción (mostrar una página, procesar datos, etc.)

## 1. Rutas Básicas y Estáticas

### ¿Qué son las rutas estáticas?
Son rutas que **siempre muestran lo mismo**, no cambian según quién las visite.

```php
Route::get('/', function () {
    return view('welcome');
});
```

**¿Qué hace esto?**
- `Route::get()` = "Cuando alguien visite con método GET..."
- `'/'` = "...la página principal (raíz del sitio)..."  
- `function() { ... }` = "...ejecuta esta función"
- `return view('welcome')` = "...y muestra la vista 'welcome'"

### Más ejemplos prácticos:

```php
Route::get('/contacto', function () {
    return "Hola desde la página de contactos";
});

Route::get('/nosotros', function () {
    return "Página sobre nosotros";
});
```

**¿Por qué es útil?** 
- Páginas como "Acerca de", "Contacto", "Términos y condiciones" no cambian
- Son fáciles de crear y mantener

### Métodos HTTP - ¿Qué significan?

```php
Route::get($uri, $callback);      // GET - Para OBTENER información
Route::post($uri, $callback);     // POST - Para ENVIAR datos (formularios)
Route::put($uri, $callback);      // PUT - Para ACTUALIZAR completamente
Route::patch($uri, $callback);    // PATCH - Para ACTUALIZAR parcialmente
Route::delete($uri, $callback);   // DELETE - Para ELIMINAR
```

**Analogía real:**
- **GET:** Como pedir ver el menú en un restaurante
- **POST:** Como hacer un pedido nuevo
- **PUT:** Como cambiar completamente tu pedido
- **DELETE:** Como cancelar tu pedido

## 2. Rutas con Parámetros

### ¿Por qué necesitamos parámetros?
Imagina que tienes 1000 productos en tu tienda online. ¿Vas a crear 1000 rutas diferentes? ¡No! Usas parámetros.

### Parámetros Obligatorios
```php
Route::get('/cursos/{curso}', function($curso) {
    return "Bienvenido al curso: $curso";
});
```

**¿Qué hace esto?**
- `{curso}` = "Aquí va un valor que cambia"
- `function($curso)` = "Recibe ese valor como parámetro"
- Si visitas `/cursos/php`, mostrará: "Bienvenido al curso: php"
- Si visitas `/cursos/laravel`, mostrará: "Bienvenido al curso: laravel"

### Múltiples Parámetros
```php
Route::get('/cursos/{curso}/{categoria}', function($curso, $categoria) {
    return "Curso: $curso de la categoría: $categoria";
});
```

**Ejemplo real:**
- URL: `/cursos/laravel/backend`
- Resultado: "Curso: laravel de la categoría: backend"

### Parámetros Opcionales
```php
Route::get('/cursos/{curso}/{categoria?}', function($curso, $categoria = null) {
    if ($categoria == null) {
        return "Bienvenido al curso: $curso";
    } else {
        return "Bienvenido al curso: $curso de la categoría $categoria";
    }
});
```

**¿Qué hace el `?`?**
- Hace que el parámetro sea opcional
- `/cursos/php` funcionará (sin categoría)
- `/cursos/php/backend` también funcionará (con categoría)

**¿Por qué es útil?** Flexibilidad. A veces necesitas información extra, a veces no.

## 3. Validación de Rutas - ¿Por qué es importante?

### El problema sin validación:
```php
Route::get('/usuarios/{id}', function($id) {
    return "Usuario con ID: $id";
});
```

Si alguien visita `/usuarios/hola`, tu aplicación intentará buscar un usuario con ID "hola". ¡Esto puede causar errores!

### La solución - Validación:

#### Método 1: Expresiones regulares
```php
Route::get('/usuarios/{id}', function($id) {
    return "Usuario con ID: $id";
})->where('id', '[0-9]+');
```

**¿Qué hace `[0-9]+`?**
- `[0-9]` = "Solo números del 0 al 9"
- `+` = "Uno o más números"
- Resultado: Solo acepta números como 1, 123, 4567

#### Método 2: Validaciones predefinidas (más fáciles)
```php
// Solo números
Route::get('/usuarios/{id}', function($id) {
    return "Usuario ID: $id";
})->whereNumber('id');

// Solo letras
Route::get('/categorias/{nombre}', function($nombre) {
    return "Categoría: $nombre";
})->whereAlpha('nombre');

// Letras y números
Route::get('/productos/{codigo}', function($codigo) {
    return "Producto: $codigo";
})->whereAlphaNumeric('codigo');
```

#### Método 3: Valores específicos
```php
Route::get('/cursos/{curso}', function($curso) {
    return "Bienvenido al curso $curso";
})->whereIn('curso', ['php', 'laravel', 'vue', 'react']);
```

**¿Qué hace esto?**
- Solo acepta exactamente estos valores: php, laravel, vue, react
- `/cursos/php` ✅ Funciona
- `/cursos/python` ❌ No funciona (error 404)

### Validación Global
```php
// En App\Providers\RouteServiceProvider.php
public function boot(): void
{
    Route::pattern('id', '[0-9]+');
    Route::pattern('slug', '[a-z0-9-]+');
}
```

**¿Para qué sirve?**
- Aplica la validación a TODOS los parámetros llamados 'id' o 'slug'
- No tienes que escribir `->whereNumber('id')` en cada ruta
- Mantiene tu código limpio y consistente

## 4. Comandos para Gestionar Rutas

### ¿Por qué necesitas ver tus rutas?
Cuando tu aplicación crece, puedes tener 50, 100 o más rutas. Necesitas una forma de verlas todas.

```bash
# Ver todas las rutas
php artisan route:list
```

**¿Qué te muestra?**
- Todas las rutas de tu aplicación
- Qué método HTTP usa cada una (GET, POST, etc.)
- Qué controlador o función maneja cada ruta
- El nombre de cada ruta (si lo tiene)

### Comandos útiles:
```bash
# Solo TUS rutas (sin las de Laravel)
php artisan route:list --except-vendor

# Buscar rutas específicas
php artisan route:list --path=cursos

# Forma corta
php artisan r:l
```

**¿Cuándo usar cada uno?**
- `--except-vendor`: Cuando solo quieres ver las rutas que tú creaste
- `--path=cursos`: Cuando buscas rutas específicas de cursos

## 5. Nombres de Rutas - ¿Por qué son importantes?

### El problema sin nombres:
```php
// En tu vista
<a href="/cursos/create">Crear curso</a>

// Si después cambias la URL a /materias/create
// Tienes que cambiar TODOS los enlaces en TODAS las vistas
```

### La solución con nombres:
```php
Route::get('/cursos/create', function() {
    return 'Crear curso';
})->name('cursos.create');
```

```php
// En tu vista
<a href="{{ route('cursos.create') }}">Crear curso</a>

// Si cambias la URL, solo cambias la ruta, no todos los enlaces
```

### Nombres con parámetros:
```php
Route::get('/cursos/{id}', function($id) {
    return "Curso: $id";
})->name('cursos.show');
```

```php
// En tu vista
<a href="{{ route('cursos.show', ['id' => 5]) }}">Ver curso 5</a>
// Genera: /cursos/5
```

**¿Por qué es útil?**
- **Mantenimiento fácil:** Cambias una ruta, se actualiza en toda la aplicación
- **Menos errores:** No tienes que recordar URLs complejas
- **Código más limpio:** `route('cursos.show')` es más claro que `'/cursos/5'`

## 6. Cache de Rutas - Optimización para Producción

### ¿Qué es el cache de rutas?
Laravel revisa todas tus rutas en cada solicitud. Si tienes muchas rutas, esto puede ser lento.

### En desarrollo (NO uses cache):
```bash
# NO hagas esto en desarrollo
php artisan route:cache  # ❌
```

**¿Por qué NO?**
- Cada vez que cambias una ruta, tienes que limpiar el cache
- Es molesto y lento para desarrollar

### En producción (SÍ usa cache):
```bash
# Cachear rutas (solo en producción)
php artisan route:cache

# Si necesitas cambiar algo, limpia primero
php artisan route:clear
```

**¿Cuándo usar?**
- ✅ Cuando subes tu aplicación a producción
- ✅ Cuando ya no cambias rutas frecuentemente
- ❌ Nunca en desarrollo

## 7. CRUD - Las 7 Rutas Esenciales

### ¿Qué es CRUD?
**C**rear, **R**ead (Leer), **U**pdate (Actualizar), **D**elete (Eliminar). Son las 4 operaciones básicas en cualquier aplicación.

### ¿Por qué 7 rutas si CRUD son 4 operaciones?
Porque necesitas formularios:
- 1 ruta para mostrar el formulario de crear
- 1 ruta para procesar el formulario de crear
- 1 ruta para mostrar el formulario de editar
- 1 ruta para procesar el formulario de editar
- + las rutas de listar, mostrar y eliminar

### Las 7 rutas explicadas:

```php
// 1. LISTAR - Mostrar todos los posts
Route::get('/posts', function() {
    return 'Lista de todos los posts';
});
```
**¿Cuándo se usa?** Cuando el usuario quiere ver todos los artículos, productos, etc.

```php
// 2. CREAR - Mostrar formulario para crear nuevo post
Route::get('/posts/create', function() {
    return 'Formulario para crear un nuevo post';
});
```
**¿Cuándo se usa?** Cuando el usuario hace clic en "Crear nuevo post"

```php
// 3. GUARDAR - Procesar el formulario de crear
Route::post('/posts', function() {
    return 'Aquí se guarda el post en la base de datos';
});
```
**¿Cuándo se usa?** Cuando el usuario envía el formulario de crear

```php
// 4. MOSTRAR - Ver un post específico
Route::get('/posts/{post}', function($post) {
    return "Mostrando el post: $post";
});
```
**¿Cuándo se usa?** Cuando el usuario hace clic en un post específico

```php
// 5. EDITAR - Mostrar formulario para editar
Route::get('/posts/{post}/edit', function($post) {
    return "Formulario para editar el post: $post";
});
```
**¿Cuándo se usa?** Cuando el usuario hace clic en "Editar" en un post

```php
// 6. ACTUALIZAR - Procesar el formulario de editar
Route::put('/posts/{post}', function($post) {
    return "Actualizando el post: $post";
});
```
**¿Cuándo se usa?** Cuando el usuario envía el formulario de editar

```php
// 7. ELIMINAR - Borrar un post
Route::delete('/posts/{post}', function($post) {
    return "Eliminando el post: $post";
});
```
**¿Cuándo se usa?** Cuando el usuario hace clic en "Eliminar"

### Forma rápida - Route Resource:
```php
Route::resource('posts', PostController::class);
```

**¿Qué hace esto?**
- Crea automáticamente las 7 rutas
- Las conecta con métodos en PostController
- Usa nombres estándar (posts.index, posts.create, etc.)

**¿Por qué es útil?**
- Ahorras tiempo escribiendo
- Sigues convenciones estándar
- Código más limpio y predecible

## 8. Redirecciones - Guiando a tus usuarios

### ¿Cuándo necesitas redirecciones?

#### Problema común:
```
Usuario visita: miapp.com/contactanos
Pero tu ruta real es: miapp.com/contacto
```

#### Solución:
```php
Route::redirect('/contactanos', '/contacto');
```

**¿Qué hace?**
- Cualquiera que visite `/contactanos` va automáticamente a `/contacto`
- El usuario no ve error, se redirige automáticamente

### Casos de uso reales:

#### 1. URLs que cambiaron:
```php
// Cambió de /inicio a /
Route::redirect('/inicio', '/');

// Cambió de /productos a /tienda
Route::redirect('/productos', '/tienda');
```

#### 2. Redirección permanente (SEO):
```php
Route::permanentRedirect('/old-url', '/new-url');
```

**¿Cuál es la diferencia?**
- `redirect()` = redirección temporal (código 302)
- `permanentRedirect()` = redirección permanente (código 301)
- Google prefiere 301 para SEO

## 9. Respuestas Personalizadas

### ¿Por qué no siempre HTML?
A veces necesitas responder con diferentes tipos de contenido:

#### JSON (para APIs):
```php
Route::get('/api/posts', function() {
    return response()->json([
        'posts' => ['Post 1', 'Post 2', 'Post 3'],
        'total' => 3
    ]);
});
```

**¿Cuándo usar?**
- Cuando tu frontend es React, Vue, etc.
- Cuando creas una API
- Cuando necesitas datos en JavaScript

#### Texto plano:
```php
Route::get('/robots.txt', function() {
    return response('User-agent: *\nDisallow: /admin/', 200)
           ->header('Content-Type', 'text/plain');
});
```

**¿Cuándo usar?**
- Archivos como robots.txt, sitemap.xml
- Cuando necesitas respuestas muy simples

#### Descargas:
```php
Route::get('/descargar-reporte', function() {
    return response()->download(storage_path('app/reporte.pdf'));
});
```

**¿Cuándo usar?**
- Cuando el usuario necesita descargar archivos
- Reportes, PDFs, imágenes

## 10. Agrupación de Rutas - Organización inteligente

### ¿Por qué agrupar rutas?
Cuando tienes muchas rutas similares, la agrupación te ayuda a no repetir código.

### Problema sin agrupación:
```php
Route::get('/admin/usuarios', function() { ... })->middleware('auth');
Route::get('/admin/productos', function() { ... })->middleware('auth');
Route::get('/admin/pedidos', function() { ... })->middleware('auth');
// Repites 'admin' y 'middleware' en cada ruta
```

### Solución con agrupación:
```php
Route::prefix('admin')->middleware('auth')->group(function () {
    Route::get('/usuarios', function() { ... });    // /admin/usuarios
    Route::get('/productos', function() { ... });   // /admin/productos
    Route::get('/pedidos', function() { ... });     // /admin/pedidos
});
```

### Tipos de agrupación:

#### Por prefijo:
```php
Route::prefix('api/v1')->group(function () {
    Route::get('/users', function() { ... });  // /api/v1/users
    Route::get('/posts', function() { ... });  // /api/v1/posts
});
```

#### Por middleware (seguridad):
```php
Route::middleware(['auth', 'admin'])->group(function () {
    Route::get('/dashboard', function() { ... });
    Route::get('/settings', function() { ... });
});
```

#### Por nombres:
```php
Route::name('admin.')->group(function () {
    Route::get('/users', function() { ... })->name('users');  // Nombre: admin.users
    Route::get('/posts', function() { ... })->name('posts');  // Nombre: admin.posts
});
```

## 11. Middleware - Seguridad y filtros

### ¿Qué es middleware?
Es como un **filtro** que se ejecuta antes de llegar a tu ruta.

**Analogía:** Es como un guardia de seguridad en un edificio. Antes de entrar, verifica si tienes permiso.

### Ejemplos prácticos:

#### Verificar si el usuario está logueado:
```php
Route::get('/dashboard', function () {
    return 'Panel de control';
})->middleware('auth');
```

**¿Qué hace?**
- Si el usuario está logueado → muestra el dashboard
- Si NO está logueado → lo redirige al login

#### Múltiples middlewares:
```php
Route::get('/admin/settings', function () {
    return 'Configuración de admin';
})->middleware(['auth', 'admin']);
```

**¿Qué hace?**
- Primero verifica si está logueado
- Luego verifica si es administrador
- Solo entonces muestra la página

### Middleware común:
- `auth` - Usuario debe estar logueado
- `guest` - Usuario NO debe estar logueado (para login/registro)
- `admin` - Usuario debe ser administrador
- `throttle:60,1` - Limita a 60 requests por minuto

## 12. Rutas con Controladores - Organización profesional

### ¿Por qué usar controladores?
Cuando tu aplicación crece, poner toda la lógica en las rutas se vuelve un desastre.

### Problema con funciones anónimas:
```php
Route::get('/posts', function() {
    // 50 líneas de código aquí
    // Validaciones, consultas a BD, etc.
    // Se vuelve inmanejable
});
```

### Solución con controladores:
```php
// En routes/web.php
Route::get('/posts', [PostController::class, 'index']);
```

```php
// En app/Http/Controllers/PostController.php
class PostController extends Controller
{
    public function index()
    {
        // Toda la lógica aquí
        $posts = Post::all();
        return view('posts.index', compact('posts'));
    }
}
```

### Ventajas:
- **Código organizado:** Cada controlador maneja una cosa
- **Reutilizable:** Puedes usar el mismo método en diferentes lugares
- **Testeable:** Es más fácil hacer pruebas
- **Mantenible:** Más fácil de encontrar y cambiar código

### Crear un controlador:
```bash
# Controlador básico
php artisan make:controller PostController

# Controlador con métodos CRUD
php artisan make:controller PostController --resource
```

## 13. Subdominios - Organización avanzada

### ¿Cuándo usar subdominios?
Cuando tienes diferentes secciones de tu aplicación:
- `miapp.com` - Sitio principal
- `admin.miapp.com` - Panel de administración
- `api.miapp.com` - API
- `blog.miapp.com` - Blog

### Subdominio fijo:
```php
Route::domain('admin.miapp.com')->group(function () {
    Route::get('/', function () {
        return 'Panel de administración';
    });
    Route::get('/users', function () {
        return 'Usuarios del admin';
    });
});
```

### Subdominio dinámico:
```php
Route::domain('{tenant}.miapp.com')->group(function () {
    Route::get('/', function ($tenant) {
        return "Bienvenido a la empresa: $tenant";
    });
});
```

**¿Qué hace esto?**
- `empresa1.miapp.com` → "Bienvenido a la empresa: empresa1"
- `empresa2.miapp.com` → "Bienvenido a la empresa: empresa2"

**¿Cuándo usar?**
- Aplicaciones multi-tenant (múltiples clientes)
- SaaS (Software as a Service)
- Tiendas online con múltiples marcas

## 14. Mejores Prácticas - Consejos de expertos

### 1. Orden importa
```php
// ✅ Correcto: específico antes que general
Route::get('/cursos/gratis', function() {
    return 'Cursos gratuitos';
});

Route::get('/cursos/{curso}', function($curso) {
    return "Curso: $curso";
});
```

```php
// ❌ Incorrecto: general antes que específico
Route::get('/cursos/{curso}', function($curso) {
    return "Curso: $curso";  // Captura TODO, incluso 'gratis'
});

Route::get('/cursos/gratis', function() {
    return 'Cursos gratuitos';  // NUNCA se ejecuta
});
```

### 2. Siempre valida parámetros
```php
// ✅ Bueno
Route::get('/usuarios/{id}', [UserController::class, 'show'])
     ->whereNumber('id');

// ❌ Malo
Route::get('/usuarios/{id}', [UserController::class, 'show']);
// Alguien puede visitar /usuarios/hack-attempt
```

### 3. Usa nombres descriptivos
```php
// ✅ Bueno
Route::get('/posts', [PostController::class, 'index'])->name('posts.index');
Route::get('/posts/create', [PostController::class, 'create'])->name('posts.create');

// ❌ Malo
Route::get('/posts', [PostController::class, 'index'])->name('p1');
Route::get('/posts/create', [PostController::class, 'create'])->name('p2');
```

### 4. Agrupa rutas relacionadas
```php
// ✅ Bueno
Route::prefix('admin')->middleware('auth')->name('admin.')->group(function () {
    Route::resource('posts', PostController::class);
    Route::resource('users', UserController::class);
});

// ❌ Malo
Route::get('/admin/posts', [PostController::class, 'index'])->middleware('auth')->name('admin.posts.index');
Route::get('/admin/posts/create', [PostController::class, 'create'])->middleware('auth')->name('admin.posts.create');
// ... repitiendo lo mismo una y otra vez
```

### 5. Usa Resource Routes para CRUD
```php
// ✅ Bueno
Route::resource('posts', PostController::class);

// ❌ Malo (mucho código repetitivo)
Route::get('/posts', [PostController::class, 'index']);
Route::get('/posts/create', [PostController::class, 'create']);
Route::post('/posts', [PostController::class, 'store']);
// ... 4 rutas más
```

## 15. Debugging y Herramientas Útiles

### Ver información de la ruta actual:
```php
// En un controlador
$route = Route::current();
$name = Route::currentRouteName();
$action = Route::currentRouteAction();

// En una vista
{{ Route::currentRouteName() }}
```

### Verificar si existe una ruta:
```php
if (Route::has('posts.show')) {
    // La ruta existe
    $url = route('posts.show', ['post' => 1]);
}
```

### Generar URLs:
```php
// URL completa
$url = route('posts.show', ['post' => 1]);
// Resultado: https://miapp.com/posts/1

// Solo el path
$path = route('posts.show', ['post' => 1], false);
// Resultado: /posts/1
```

## 16. Errores Comunes y Cómo Evitarlos

### Error 1: Ruta no encontrada (404)
```php
// Problema: Parámetro no válido
Route::get('/users/{id}', function($id) {
    return "User: $id";
})->whereNumber('id');

// Visitar: /users/abc → 404 Error
```

**Solución:** Siempre valida parámetros y ten rutas de fallback.

### Error 2: Orden incorrecto
```php
// ❌ Malo
Route::get('/posts/{slug}', function($slug) { ... });
Route::get('/posts/create', function() { ... });  // Nunca se ejecuta

// ✅ Bueno
Route::get('/posts/create', function() { ... });
Route::get('/posts/{slug}', function($slug) { ... });
```

### Error 3: Middleware en lugar incorrecto
```php
// ❌ Malo: middleware aplicado a rutas públicas
Route::middleware('auth')->group(function () {
    Route::get('/', function() { ... });  // Página principal requiere login
});

// ✅ Bueno: middleware solo donde se necesita
Route::get('/', function() { ... });  // Página principal pública
Route::middleware('auth')->group(function () {
    Route::get('/dashboard', function() { ... });  // Solo dashboard requiere login
});
```

## 17. Comandos de Referencia Rápida

```bash
# Gestión de rutas
php artisan route:list                 # Ver todas las rutas
php artisan route:list --except-vendor # Solo rutas propias
php artisan route:cache                # Cachear rutas (solo producción)
php artisan route:clear                # Limpiar cache de rutas

# Crear controladores
php artisan make:controller PostController              # Controlador básico
php artisan make:controller PostController --resource   # Con métodos CRUD

# Otros comandos útiles
php artisan serve                      # Servidor de desarrollo
php artisan tinker                     # Consola interactiva
```

## 18. Ejercicios Prácticos

### Ejercicio 1: Blog básico
Crea las rutas para un blog con:
- Página principal
- Lista de posts
- Ver post individual
- Formulario de contacto

### Ejercicio 2: Tienda online
Crea las rutas para:
- Catálogo de productos
- Ver producto individual (con validación numérica para ID)
- Carrito de compras
- Página de pago (protegida con middleware)

### Ejercicio 3: Panel de administración
Crea un grupo de rutas para administración que:
- Tenga prefijo 'admin'
- Esté protegido con middleware
- Incluya gestión de usuarios y productos

---

## Conclusión

Las rutas son la **puerta de entrada** a tu aplicación Laravel. Una buena estructura de rutas hace que:

- Tu código sea más **organizado** y **mantenible**
- Los usuarios tengan una **experiencia fluida**
- Tu aplicación sea más **segura** y **eficiente**
- Otros desarrolladores puedan **entender** tu código fácilmente

**Recuerda:** Empieza simple y ve agregando complejidad gradualmente. No trates de usar todas las características desde el principio.

¡Practica con ejemplos reales y verás cómo todo cobra sentido!
