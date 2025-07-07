# Guía Completa de Eloquent ORM en Laravel

## 1. Definición de Modelos y Configuración Básica

Eloquent es el ORM (Object-Relational Mapping) de Laravel que permite trabajar con la base de datos usando clases PHP en lugar de consultas SQL directas.

### 🔎 ¿Qué hacen?
* `Model` → Representa una tabla de la base de datos como una clase PHP.
* `$table` → Especifica el nombre de la tabla si no sigue la convención.
* `$fillable` → Define qué campos pueden ser asignados masivamente.
* `$guarded` → Define qué campos NO pueden ser asignados masivamente.
* `$primaryKey` → Especifica la clave primaria si no es 'id'.
* `$timestamps` → Controla si el modelo maneja created_at y updated_at.

### 🌚 Comparación con Query Builder

| Eloquent ORM | Query Builder |
|--------------|---------------|
| `Category::all()` | `DB::table('categories')->get()` |
| `Category::find(1)` | `DB::table('categories')->find(1)` |
| `Category::where('slug', 'tech')->get()` | `DB::table('categories')->where('slug', 'tech')->get()` |
| `Category::create(['name' => 'Tech'])` | `DB::table('categories')->insert(['name' => 'Tech'])` |

### 🧱 Sintaxis

```php
// Crear modelo
php artisan make:model Category

// Definición del modelo
class Category extends Model
{
    protected $table = 'categories';
    protected $fillable = ['name', 'slug', 'description'];
    protected $guarded = ['id'];
    protected $primaryKey = 'id';
    public $timestamps = true;
}
```

### 📋 Ejemplos Detallados

#### Ejemplo 1: Modelo básico
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Category extends Model
{
    // Laravel asume que la tabla es 'categories' (plural del modelo)
    protected $fillable = ['name', 'slug', 'description', 'status'];
    
    // Campos que NO pueden ser asignados masivamente
    protected $guarded = ['id', 'created_at', 'updated_at'];
}
```

#### Ejemplo 2: Modelo con configuración personalizada
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Product extends Model
{
    // Nombre de tabla personalizado
    protected $table = 'productos';
    
    // Clave primaria personalizada
    protected $primaryKey = 'product_id';
    
    // Si la clave primaria no es auto-incremental
    public $incrementing = false;
    
    // Tipo de clave primaria
    protected $keyType = 'string';
    
    // Desactivar timestamps
    public $timestamps = false;
    
    protected $fillable = [
        'name', 'description', 'price', 'stock', 'category_id'
    ];
    
    // Casting de atributos
    protected $casts = [
        'price' => 'decimal:2',
        'is_active' => 'boolean',
        'metadata' => 'array'
    ];
}
```

### 📤 Resultado esperado
```php
// Uso del modelo
$category = new Category();
$category->name = 'Tecnología';
$category->slug = 'tecnologia';
$category->save();

// O usando create()
$category = Category::create([
    'name' => 'Tecnología',
    'slug' => 'tecnologia',
    'description' => 'Categoría de tecnología'
]);
```

---

## 2. Métodos de Consulta (Equivalentes a SELECT)

### 🔎 ¿Qué hacen?
* `all()` → Obtiene todos los registros de la tabla.
* `find()` → Busca un registro por su clave primaria.
* `first()` → Obtiene el primer registro que coincida.
* `get()` → Ejecuta la consulta y obtiene una colección.
* `pluck()` → Obtiene una columna específica como array.
* `select()` → Especifica qué columnas seleccionar.
* `distinct()` → Elimina duplicados.

### 🌚 Comparación con Query Builder

| Eloquent ORM | Query Builder |
|--------------|---------------|
| `Category::all()` | `DB::table('categories')->get()` |
| `Category::find(1)` | `DB::table('categories')->find(1)` |
| `Category::select('name', 'slug')->get()` | `DB::table('categories')->select('name', 'slug')->get()` |
| `Category::distinct()->get()` | `DB::table('categories')->distinct()->get()` |
| `Category::pluck('name')` | `DB::table('categories')->pluck('name')` |

### 🧱 Sintaxis

```php
// Obtener todos los registros
Category::all()

// Seleccionar columnas específicas
Category::select('columna1', 'columna2')->get()

// Obtener primer registro
Category::first()

// Buscar por ID
Category::find($id)
```

### 📋 Ejemplos Detallados

#### Ejemplo 1: Obtener todos los registros
```php
Route::get('/categories', function () {
    $categories = Category::all();
    return $categories;
});
```

#### Ejemplo 2: Seleccionar columnas específicas
```php
Route::get('/categories-select', function () {
    $categories = Category::select('id', 'name', 'slug')->get();
    return $categories;
});
```

#### Ejemplo 3: Obtener primer registro
```php
Route::get('/first-category', function () {
    $category = Category::first();
    return $category;
});
```

#### Ejemplo 4: Buscar por ID
```php
Route::get('/category/{id}', function ($id) {
    $category = Category::find($id);
    
    if (!$category) {
        return response()->json(['error' => 'Categoría no encontrada'], 404);
    }
    
    return $category;
});
```

#### Ejemplo 5: Obtener columna específica
```php
Route::get('/category-names', function () {
    $names = Category::pluck('name');
    return $names;
});
```

#### Ejemplo 6: Eliminar duplicados
```php
Route::get('/unique-categories', function () {
    $categories = Category::select('name')->distinct()->get();
    return $categories;
});
```

### 📤 Resultado esperado (JSON)

**Ejemplo básico:**
```json
[
    {
        "id": 1,
        "name": "Tecnología",
        "slug": "tecnologia",
        "description": "Categoría de tecnología",
        "created_at": "2024-01-15T10:30:00.000000Z",
        "updated_at": "2024-01-15T10:30:00.000000Z"
    },
    {
        "id": 2,
        "name": "Educación",
        "slug": "educacion",
        "description": "Categoría de educación",
        "created_at": "2024-01-15T11:00:00.000000Z",
        "updated_at": "2024-01-15T11:00:00.000000Z"
    }
]
```

---

## 3. Métodos de Filtro (Equivalentes a WHERE)

### 🔎 ¿Qué hacen?
* `where()` → Aplica una condición simple.
* `orWhere()` → Agrega una condición alternativa (OR).
* `whereIn()` / `whereNotIn()` → Verifica si un valor está (no está) en una lista.
* `whereNull()` / `whereNotNull()` → Evalúa si una columna es (no) nula.
* `whereBetween()` → Evalúa si un valor está dentro de un rango.
* `whereDate()`, `whereYear()`, `whereMonth()` → Para trabajar con fechas.
* `whereHas()` → Filtra por relaciones existentes.

### 🌚 Comparación con Query Builder

| Eloquent ORM | Query Builder |
|--------------|---------------|
| `Category::where('slug', 'tech')->get()` | `DB::table('categories')->where('slug', 'tech')->get()` |
| `Category::whereIn('id', [1,2,3])->get()` | `DB::table('categories')->whereIn('id', [1,2,3])->get()` |
| `Category::whereNull('deleted_at')->get()` | `DB::table('categories')->whereNull('deleted_at')->get()` |
| `Category::whereDate('created_at', '2024-01-15')->get()` | `DB::table('categories')->whereDate('created_at', '2024-01-15')->get()` |

### 🧱 Sintaxis

```php
Category::where('columna', 'valor')
    ->orWhere('columna', 'valor')
    ->whereIn('columna', [valores])
    ->whereNull('columna')
    ->whereBetween('columna', [min, max])
    ->get()
```

### 📋 Ejemplos Detallados

#### Ejemplo 1: Filtro básico con `where()`
```php
Route::get('/categories/active', function () {
    $categories = Category::where('status', 'active')->get();
    return $categories;
});
```

#### Ejemplo 2: Múltiples condiciones
```php
Route::get('/categories/tech', function () {
    $categories = Category::where('slug', 'tecnologia')
        ->where('status', 'active')
        ->get();
    return $categories;
});
```

#### Ejemplo 3: Condiciones OR
```php
Route::get('/categories/tech-or-edu', function () {
    $categories = Category::where('slug', 'tecnologia')
        ->orWhere('slug', 'educacion')
        ->get();
    return $categories;
});
```

#### Ejemplo 4: Filtrar por lista de valores
```php
Route::get('/categories/by-ids', function () {
    $categories = Category::whereIn('id', [1, 2, 3, 4])
        ->whereNotIn('status', ['deleted', 'inactive'])
        ->get();
    return $categories;
});
```

#### Ejemplo 5: Filtros con valores nulos
```php
Route::get('/categories/with-description', function () {
    $categories = Category::whereNotNull('description')
        ->whereNull('deleted_at')
        ->get();
    return $categories;
});
```

#### Ejemplo 6: Filtros por rangos
```php
Route::get('/products/price-range', function () {
    $products = Product::whereBetween('price', [100, 500])
        ->whereNotBetween('created_at', ['2023-01-01', '2023-06-30'])
        ->get();
    return $products;
});
```

#### Ejemplo 7: Filtros por fechas
```php
Route::get('/categories/created-today', function () {
    $categories = Category::whereDate('created_at', today())
        ->whereYear('created_at', 2024)
        ->whereMonth('created_at', 1)
        ->get();
    return $categories;
});
```

#### Ejemplo 8: Búsquedas con LIKE
```php
Route::get('/categories/search/{term}', function ($term) {
    $categories = Category::where('name', 'LIKE', "%{$term}%")
        ->orWhere('description', 'LIKE', "%{$term}%")
        ->get();
    return $categories;
});
```

---

## 4. Métodos de Ordenamiento

### 🔎 ¿Qué hacen?
* `orderBy()` → Ordena por una columna (ASC/DESC).
* `orderByDesc()` → Ordena descendentemente.
* `latest()` → Ordena por created_at descendente.
* `oldest()` → Ordena por created_at ascendente.
* `inRandomOrder()` → Ordena aleatoriamente.

### 🌚 Comparación con Query Builder

| Eloquent ORM | Query Builder |
|--------------|---------------|
| `Category::orderBy('name')->get()` | `DB::table('categories')->orderBy('name')->get()` |
| `Category::latest()->get()` | `DB::table('categories')->latest()->get()` |
| `Category::inRandomOrder()->get()` | `DB::table('categories')->inRandomOrder()->get()` |

### 📋 Ejemplos Detallados

#### Ejemplo 1: Ordenamiento básico
```php
Route::get('/categories/ordered', function () {
    $categories = Category::orderBy('name', 'asc')->get();
    return $categories;
});
```

#### Ejemplo 2: Ordenamiento múltiple
```php
Route::get('/categories/multi-order', function () {
    $categories = Category::orderBy('status', 'desc')
        ->orderBy('name', 'asc')
        ->get();
    return $categories;
});
```

#### Ejemplo 3: Métodos de conveniencia
```php
Route::get('/categories/latest', function () {
    $categories = Category::latest()->get();           // Por created_at DESC
    $categories = Category::latest('updated_at')->get(); // Por updated_at DESC
    $categories = Category::oldest()->get();            // Por created_at ASC
    return $categories;
});
```

---

## 5. Métodos de Límite y Paginación

### 🔎 ¿Qué hacen?
* `limit()` / `take()` → Limita el número de resultados.
* `offset()` / `skip()` → Omite un número de registros.
* `paginate()` → Paginación automática con links.
* `simplePaginate()` → Paginación simple (solo siguiente/anterior).

### 📋 Ejemplos Detallados

#### Ejemplo 1: Limitar resultados
```php
Route::get('/categories/limited', function () {
    $categories = Category::limit(5)->get();
    // O usando take()
    $categories = Category::take(5)->get();
    return $categories;
});
```

#### Ejemplo 2: Paginación
```php
Route::get('/categories/paginated', function () {
    $categories = Category::paginate(10);
    return $categories;
});
```

#### Ejemplo 3: Paginación con filtros
```php
Route::get('/categories/active-paginated', function () {
    $categories = Category::where('status', 'active')
        ->orderBy('name')
        ->paginate(15);
    return $categories;
});
```

---

## 6. Métodos de Inserción y Actualización

### 🔎 ¿Qué hacen?
* `create()` → Crea un nuevo registro.
* `save()` → Guarda cambios en un modelo.
* `update()` → Actualiza registros existentes.
* `updateOrCreate()` → Actualiza o crea si no existe.
* `firstOrCreate()` → Obtiene el primer registro o lo crea.

### 🌚 Comparación con Query Builder

| Eloquent ORM | Query Builder |
|--------------|---------------|
| `Category::create(['name' => 'Tech'])` | `DB::table('categories')->insert(['name' => 'Tech'])` |
| `$category->update(['name' => 'New Name'])` | `DB::table('categories')->where('id', $id)->update(['name' => 'New Name'])` |
| `Category::where('id', 1)->delete()` | `DB::table('categories')->where('id', 1)->delete()` |

### 📋 Ejemplos Detallados

#### Ejemplo 1: Crear registros
```php
Route::post('/categories', function (Request $request) {
    // Método 1: create()
    $category = Category::create([
        'name' => $request->name,
        'slug' => $request->slug,
        'description' => $request->description
    ]);
    
    // Método 2: new + save()
    $category = new Category();
    $category->name = $request->name;
    $category->slug = $request->slug;
    $category->save();
    
    return $category;
});
```

#### Ejemplo 2: Actualizar registros
```php
Route::put('/categories/{id}', function (Request $request, $id) {
    $category = Category::find($id);
    
    if (!$category) {
        return response()->json(['error' => 'Categoría no encontrada'], 404);
    }
    
    // Método 1: update()
    $category->update([
        'name' => $request->name,
        'description' => $request->description
    ]);
    
    // Método 2: asignación + save()
    $category->name = $request->name;
    $category->description = $request->description;
    $category->save();
    
    return $category;
});
```

#### Ejemplo 3: Crear o actualizar
```php
Route::post('/categories/upsert', function (Request $request) {
    $category = Category::updateOrCreate(
        ['slug' => $request->slug], // Condición de búsqueda
        [
            'name' => $request->name,
            'description' => $request->description
        ]
    );
    
    return $category;
});
```

---

## 7. Métodos de Eliminación

### 🔎 ¿Qué hacen?
* `delete()` → Elimina registros.
* `destroy()` → Elimina por ID(s).
* `forceDelete()` → Eliminación permanente (con SoftDeletes).
* `restore()` → Restaura registros eliminados suavemente.

### 📋 Ejemplos Detallados

#### Ejemplo 1: Eliminar registros
```php
Route::delete('/categories/{id}', function ($id) {
    $category = Category::find($id);
    
    if (!$category) {
        return response()->json(['error' => 'Categoría no encontrada'], 404);
    }
    
    $category->delete();
    
    return response()->json(['message' => 'Categoría eliminada']);
});
```

#### Ejemplo 2: Eliminar múltiples registros
```php
Route::delete('/categories/bulk', function (Request $request) {
    $ids = $request->ids; // [1, 2, 3, 4]
    
    Category::destroy($ids);
    
    return response()->json(['message' => 'Categorías eliminadas']);
});
```

---

## 8. Relaciones (Relationships)

### 🔎 ¿Qué hacen?
* `hasOne()` → Relación uno a uno.
* `hasMany()` → Relación uno a muchos.
* `belongsTo()` → Relación inversa (muchos a uno).
* `belongsToMany()` → Relación muchos a muchos.
* `with()` → Eager Loading (carga ansiosa).
* `whereHas()` → Filtrar por relaciones.

### 📋 Ejemplos Detallados

#### Ejemplo 1: Definir relaciones en los modelos
```php
// Model Category
class Category extends Model
{
    public function products()
    {
        return $this->hasMany(Product::class);
    }
}

// Model Product
class Product extends Model
{
    public function category()
    {
        return $this->belongsTo(Category::class);
    }
    
    public function tags()
    {
        return $this->belongsToMany(Tag::class);
    }
}
```

#### Ejemplo 2: Usar relaciones
```php
Route::get('/categories/with-products', function () {
    // Eager Loading - Carga las relaciones de una vez
    $categories = Category::with('products')->get();
    
    return $categories;
});
```

#### Ejemplo 3: Filtrar por relaciones
```php
Route::get('/categories/has-products', function () {
    // Solo categorías que tienen productos
    $categories = Category::has('products')->get();
    
    // Con condiciones específicas
    $categories = Category::whereHas('products', function ($query) {
        $query->where('status', 'active');
    })->get();
    
    return $categories;
});
```

---

## 9. Métodos de Agregación

### 🔎 ¿Qué hacen?
* `count()` → Cuenta registros.
* `sum()` → Suma valores de una columna.
* `avg()` → Promedio de una columna.
* `max()` / `min()` → Valor máximo/mínimo.
* `exists()` → Verifica si existen registros.

### 📋 Ejemplos Detallados

#### Ejemplo 1: Contar registros
```php
Route::get('/categories/stats', function () {
    $totalCategories = Category::count();
    $activeCategories = Category::where('status', 'active')->count();
    
    return [
        'total' => $totalCategories,
        'active' => $activeCategories
    ];
});
```

#### Ejemplo 2: Funciones agregadas
```php
Route::get('/products/stats', function () {
    $stats = [
        'total_products' => Product::count(),
        'average_price' => Product::avg('price'),
        'max_price' => Product::max('price'),
        'min_price' => Product::min('price'),
        'total_value' => Product::sum('price')
    ];
    
    return $stats;
});
```

---

## 10. Scopes (Ámbitos)

### 🔎 ¿Qué hacen?
* `scope` → Define consultas reutilizables.
* Local Scopes → Métodos en el modelo.
* Global Scopes → Se aplican automáticamente.

### 📋 Ejemplos Detallados

#### Ejemplo 1: Local Scopes
```php
// En el modelo Category
class Category extends Model
{
    // Scope para categorías activas
    public function scopeActive($query)
    {
        return $query->where('status', 'active');
    }
    
    // Scope para búsqueda por nombre
    public function scopeSearch($query, $term)
    {
        return $query->where('name', 'LIKE', "%{$term}%");
    }
}

// Uso de los scopes
Route::get('/categories/active', function () {
    $categories = Category::active()->get();
    return $categories;
});

Route::get('/categories/search/{term}', function ($term) {
    $categories = Category::search($term)->active()->get();
    return $categories;
});
```

---

## 11. Ejemplo Completo Combinado

```php
Route::get('/advanced-query', function () {
    $results = Category::select('id', 'name', 'slug')
        ->with(['products' => function ($query) {
            $query->where('status', 'active')
                  ->orderBy('name');
        }])
        ->where('status', 'active')
        ->whereHas('products', function ($query) {
            $query->where('price', '>', 50);
        })
        ->whereIn('id', [1, 2, 3, 4])
        ->whereNotNull('description')
        ->orderBy('name')
        ->paginate(10);
    
    return $results;
});
```

### 🧠 Notas útiles
* **Rendimiento**: Usa Eager Loading (`with()`) para evitar el problema N+1.
* **Seguridad**: Siempre define `$fillable` o `$guarded` en tus modelos.
* **Convenciones**: Laravel asume nombres de tabla en plural y claves foráneas con el patrón `model_id`.
* **Mutators y Accessors**: Puedes definir métodos para modificar atributos automáticamente.
* **Casting**: Usa `$casts` para convertir automáticamente tipos de datos.

### 🚀 Ventajas de Eloquent vs Query Builder
* **Legibilidad**: Código más expresivo y fácil de entender.
* **Relaciones**: Manejo automático de relaciones entre modelos.
* **Eventos**: Hooks automáticos (creating, created, updating, updated, etc.).
* **Mutators/Accessors**: Transformación automática de datos.
* **Soft Deletes**: Eliminación suave integrada.
* **Timestamps**: Manejo automático de created_at y updated_at.
