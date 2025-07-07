# Laravel - Guía Práctica: Consultas con Query Builder (DB::table)
#### 📌 ¿Qué es el Query Builder?
El Query Builder de Laravel proporciona una forma fluida y segura de construir consultas SQL sin escribir SQL puro. Usa la clase DB de Laravel.
 `use Illuminate\Support\Facades\DB;`
 
###✅ Ruta de prueba
Antes de probar las consultas, crea una ruta temporal para visualizar los resultados en el navegador o Postman:

```php
Route::get('/prueba', function () {
    // Aquí van las consultas
});
```
### Método: `get()`

## ¿Qué hace?

El método `get()` ejecuta la consulta construida hasta ese momento y devuelve todos los registros que cumplen con las condiciones especificadas.

El resultado es una colección (`Illuminate\Support\Collection`), que permite trabajar de forma parecida a un array, pero con métodos útiles como `map()`, `filter()`, `pluck()`, etc.

## Sintaxis
```php
DB::table('nombre_de_tabla')->get();
```

- `DB::table('nombre_de_tabla'):  //Se especifica la tabla con la que se trabajará (en este caso, nombre_de_tabla).`

- `->get(): //Se ejecuta la consulta y se devuelven todos los registros de la tabla especificada.`
### Ejemplo
```php
Route::get('/prueba',function(){
    $categories = DB::table('categories')->get();
    return $categories;
});
```
Esto seria similar a ejecutar la consulta 
```sql
SELECT * FROM categories; 
```
## Resultado esperado (JSON)
```json
[
{
"id": 1,
"name": "Tecnologia",
"slug": "tecnologia",
"created_at": "2024-03-10 12:25:00",
"updated_at": "2024-03-15 14:10:00"
},
{
"id": 2,
"name": "Desarrollo Web",
"slug": "Desarrollo-Web",
"created_at": "2023-06-17 09:45:00",
"updated_at": "2023-06-18 10:00:00"
},
{
"id": 3,
"name": "Marketing Digital",
"slug": "marketing-digital",
"created_at": "2024-01-25 18:30:00",
"updated_at": "2024-01-28 19:00:00"
},
{
"id": 4,
"name": "Educacion",
"slug": "educacion",
"created_at": "2023-11-05 15:10:00",
"updated_at": "2023-11-06 16:45:00"
}
]
```
# Laravel Query Builder - Métodos de Ordenamiento

## 4. Métodos de Ordenamiento: `orderBy()`, `orderByDesc()`

Los métodos de ordenamiento permiten controlar el orden de los resultados obtenidos en las consultas. Después de aplicar filtros, puedes usar estos métodos para organizar los datos según una o más columnas de manera ascendente o descendente.

## 🔎 ¿Qué hacen?

- **`orderBy('columna', 'asc|desc')`**: Ordena los resultados en orden ascendente (por defecto) o descendente según la columna especificada.
- **`orderByDesc('columna')`**: Es una forma más limpia y directa de aplicar orden descendente sin necesidad de especificar el parámetro 'desc'.

## 🌚 Comparación con SQL tradicional (MySQL)

| Laravel Query Builder | SQL en MySQL |
|----------------------|--------------|
| `DB::table('categories')->orderBy('created_at', 'asc')->get();` | `SELECT * FROM categories ORDER BY created_at ASC;` |
| `DB::table('categories')->orderBy('created_at', 'desc')->get();` | `SELECT * FROM categories ORDER BY created_at DESC;` |
| `DB::table('categories')->orderByDesc('created_at')->get();` | `SELECT * FROM categories ORDER BY created_at DESC;` |
| `DB::table('categories')->orderBy('name')->orderBy('created_at', 'desc')->get();` | `SELECT * FROM categories ORDER BY name ASC, created_at DESC;` |

## 🧱 Sintaxis

```php
// Orden ascendente (por defecto)
DB::table('tabla')
    ->orderBy('columna')
    ->get();

// Orden ascendente explícito
DB::table('tabla')
    ->orderBy('columna', 'asc')
    ->get();

// Orden descendente
DB::table('tabla')
    ->orderBy('columna', 'desc')
    ->get();

// Orden descendente (forma corta)
DB::table('tabla')
    ->orderByDesc('columna')
    ->get();

// Múltiples ordenamientos
DB::table('tabla')
    ->orderBy('columna1', 'asc')
    ->orderBy('columna2', 'desc')
    ->get();
```

## 📋 Ejemplo básico

```php
Route::get('/categorias-ordenadas', function () {
    $categorias = DB::table('categories')
        ->select('id', 'name', 'created_at')
        ->orderBy('created_at', 'asc')  // Orden ascendente por fecha
        ->get();

    return $categorias;
    // return response()->json($categorias);
});
```

## 📋 Ejemplo con múltiples ordenamientos

```php
Route::get('/productos-ordenados', function () {
    $productos = DB::table('products')
        ->select('id', 'name', 'price', 'category_id', 'created_at')
        ->orderBy('category_id', 'asc')     // Primero por categoría
        ->orderByDesc('price')              // Luego por precio (descendente)
        ->orderBy('name', 'asc')            // Finalmente por nombre
        ->get();

    return $productos;
});
```

## 📋 Ejemplo combinando filtros y ordenamiento

```php
Route::get('/productos-filtrados', function () {
    $productos = DB::table('products')
        ->select('id', 'name', 'price', 'created_at')
        ->where('price', '>', 100)          // Filtro: precio mayor a 100
        ->whereNotNull('description')       // Filtro: descripción no nula
        ->orderByDesc('created_at')         // Orden: más recientes primero
        ->orderBy('name')                   // Orden secundario: alfabético
        ->get();

    return $productos;
});
```

## 📤 Resultado esperado (JSON)

```json
[
    {
        "id": 2,
        "name": "Desarrollo Web",
        "created_at": "2023-06-17 09:45:00"
    },
    {
        "id": 4,
        "name": "Educacion",
        "created_at": "2023-11-05 15:10:00"
    },
    {
        "id": 3,
        "name": "Marketing Digital",
        "created_at": "2024-01-25 18:30:00"
    },
    {
        "id": 1,
        "name": "Tecnologia",
        "created_at": "2024-03-10 12:25:00"
    }
]
```

## 📊 Tabla resumen de Métodos de Ordenamiento

| Método Laravel | Descripción | Equivalente en SQL | Ejemplo Laravel |
|---------------|-------------|-------------------|-----------------|
| `orderBy('col')` | Orden ascendente (por defecto) | `ORDER BY col ASC` | `orderBy('name')` |
| `orderBy('col', 'asc')` | Orden ascendente explícito | `ORDER BY col ASC` | `orderBy('price', 'asc')` |
| `orderBy('col', 'desc')` | Orden descendente | `ORDER BY col DESC` | `orderBy('created_at', 'desc')` |
| `orderByDesc('col')` | Orden descendente (forma corta) | `ORDER BY col DESC` | `orderByDesc('created_at')` |
| `orderByRaw('expresión')` | Ordenamiento personalizado con SQL crudo | `ORDER BY ...` | `orderByRaw('FIELD(status, "pending", "active")')` |

## 📌 Notas útiles

- **Orden por defecto**: Si no especificas `'asc'` o `'desc'`, Laravel aplicará orden ascendente por defecto.
- **Múltiples ordenamientos**: Puedes encadenar varios `orderBy()` para ordenar por más de una columna. El orden de los métodos importa.
- **Rendimiento**: Para tablas grandes, asegúrate de que las columnas por las que ordenas tengan índices en la base de datos.
- **Compatibilidad**: Estos métodos funcionan tanto con Query Builder como con Eloquent ORM.
- **Ordenamiento personalizado**: Usa `orderByRaw()` para casos especiales que requieren SQL personalizado.

## 🧠 Consejos adicionales

- Combina ordenamientos con `limit()` y `offset()` para implementar paginación eficiente.
- Usa `latest()` y `oldest()` como atajos para `orderBy('created_at', 'desc')` y `orderBy('created_at', 'asc')` respectivamente.
- Para ordenamientos complejos, considera usar `selectRaw()` con expresiones SQL personalizadas.

## 5. Métodos de Agrupamiento: `groupBy()`

El método `groupBy()` es fundamental cuando necesitas agrupar los resultados de tu consulta según una o varias columnas. Es especialmente útil para generar estadísticas, reportes y análisis de datos, como contar registros por categoría o calcular sumas por grupo.

## 🔎 ¿Qué hace?

El método `groupBy()` agrupa los resultados por una o más columnas especificadas. Se utiliza comúnmente junto con **funciones agregadas** como `count()`, `sum()`, `avg()`, `min()` o `max()` para obtener estadísticas y resúmenes por grupo.

## 🌚 Comparación con SQL tradicional (MySQL)

| Laravel Query Builder | SQL en MySQL |
|----------------------|--------------|
| `DB::table('categories')->select('slug')->groupBy('slug')->get();` | `SELECT slug FROM categories GROUP BY slug;` |
| `DB::table('categories')->select('slug', DB::raw('count(*) as total'))->groupBy('slug')->get();` | `SELECT slug, COUNT(*) AS total FROM categories GROUP BY slug;` |
| `DB::table('products')->select('category_id', DB::raw('sum(price) as total_price'))->groupBy('category_id')->get();` | `SELECT category_id, SUM(price) AS total_price FROM products GROUP BY category_id;` |
| `DB::table('orders')->select('status', DB::raw('avg(total) as promedio'))->groupBy('status')->get();` | `SELECT status, AVG(total) AS promedio FROM orders GROUP BY status;` |

## 🧱 Sintaxis

```php
// Agrupamiento básico
DB::table('tabla')
    ->select('columna')
    ->groupBy('columna')
    ->get();

// Agrupamiento con funciones agregadas
DB::table('tabla')
    ->select('columna', DB::raw('función_agregada AS alias'))
    ->groupBy('columna')
    ->get();

// Agrupamiento por múltiples columnas
DB::table('tabla')
    ->select('col1', 'col2', DB::raw('count(*) as total'))
    ->groupBy('col1', 'col2')
    ->get();
```

## 📋 Ejemplo básico - Conteo por categoría

```php
Route::get('/categorias-agrupadas', function () {
    $agrupados = DB::table('categories')
        ->select('slug', DB::raw('count(*) as total'))
        ->groupBy('slug')
        ->get();

    return $agrupados;
    // return response()->json($agrupados);
});
```

## 📋 Ejemplo avanzado - Múltiples funciones agregadas

```php
Route::get('/productos-estadisticas', function () {
    $estadisticas = DB::table('products')
        ->select('category_id', 
                DB::raw('count(*) as total_productos'),
                DB::raw('sum(price) as precio_total'),
                DB::raw('avg(price) as precio_promedio'),
                DB::raw('min(price) as precio_minimo'),
                DB::raw('max(price) as precio_maximo'))
        ->groupBy('category_id')
        ->orderBy('total_productos', 'desc')
        ->get();

    return $estadisticas;
});
```

## 📋 Ejemplo con agrupamiento múltiple

```php
Route::get('/ventas-por-mes-categoria', function () {
    $ventas = DB::table('orders')
        ->select('category_id', 
                DB::raw('MONTH(created_at) as mes'),
                DB::raw('YEAR(created_at) as año'),
                DB::raw('count(*) as total_ordenes'),
                DB::raw('sum(total) as ingresos'))
        ->groupBy('category_id', 'mes', 'año')
        ->orderBy('año', 'desc')
        ->orderBy('mes', 'desc')
        ->get();

    return $ventas;
});
```

## 📋 Ejemplo con filtros y agrupamiento

```php
Route::get('/productos-activos-resumen', function () {
    $resumen = DB::table('products')
        ->select('category_id', 
                DB::raw('count(*) as productos_activos'),
                DB::raw('avg(price) as precio_promedio'))
        ->where('status', 'active')                    // Filtro: solo activos
        ->whereNotNull('price')                        // Filtro: con precio
        ->groupBy('category_id')                       // Agrupamiento
        ->having(DB::raw('count(*)'), '>', 5)          // Filtro post-agrupamiento
        ->orderBy('productos_activos', 'desc')         // Ordenamiento
        ->get();

    return $resumen;
});
```

## 📤 Resultado esperado (JSON)

```json
[
    {
        "slug": "tecnologia",
        "total": 3
    },
    {
        "slug": "educacion", 
        "total": 1
    },
    {
        "slug": "marketing",
        "total": 2
    }
]
```

## 📤 Resultado avanzado (JSON)

```json
[
    {
        "category_id": 1,
        "total_productos": 15,
        "precio_total": 2500.50,
        "precio_promedio": 166.70,
        "precio_minimo": 50.00,
        "precio_maximo": 499.99
    },
    {
        "category_id": 2,
        "total_productos": 8,
        "precio_total": 1200.00,
        "precio_promedio": 150.00,
        "precio_minimo": 25.00,
        "precio_maximo": 350.00
    }
]
```

## 📊 Tabla resumen de Funciones Agregadas

| Función Laravel | Descripción | Equivalente en SQL | Ejemplo Laravel |
|----------------|-------------|-------------------|-----------------|
| `DB::raw('count(*) as total')` | Cuenta registros por grupo | `COUNT(*) AS total` | `->select('category', DB::raw('count(*) as total'))` |
| `DB::raw('sum(columna) as suma')` | Suma valores por grupo | `SUM(columna) AS suma` | `->select('category', DB::raw('sum(price) as total_price'))` |
| `DB::raw('avg(columna) as promedio')` | Promedio por grupo | `AVG(columna) AS promedio` | `->select('category', DB::raw('avg(price) as avg_price'))` |
| `DB::raw('min(columna) as minimo')` | Valor mínimo por grupo | `MIN(columna) AS minimo` | `->select('category', DB::raw('min(price) as min_price'))` |
| `DB::raw('max(columna) as maximo')` | Valor máximo por grupo | `MAX(columna) AS maximo` | `->select('category', DB::raw('max(price) as max_price'))` |

## 📊 Tabla resumen de Métodos de Agrupamiento

| Método Laravel | Descripción | Equivalente en SQL | Ejemplo Laravel |
|---------------|-------------|-------------------|-----------------|
| `groupBy('col')` | Agrupa por una columna | `GROUP BY col` | `groupBy('category_id')` |
| `groupBy('col1', 'col2')` | Agrupa por múltiples columnas | `GROUP BY col1, col2` | `groupBy('category_id', 'status')` |
| `having('col', '>', val)` | Filtra grupos después del agrupamiento | `HAVING col > val` | `having('total', '>', 5)` |
| `havingRaw('expresión')` | Filtro personalizado para grupos | `HAVING ...` | `havingRaw('count(*) > 5')` |

## 📌 Notas útiles

- **Funciones agregadas**: Siempre usa `DB::raw()` para aplicar funciones como `count()`, `sum()`, `avg()`, `min()`, `max()`.
- **Múltiples columnas**: Puedes agrupar por más de una columna: `groupBy('col1', 'col2', 'col3')`.
- **Filtros post-agrupamiento**: Usa `having()` para filtrar resultados después del agrupamiento, no `where()`.
- **Orden de ejecución**: Los métodos se ejecutan en este orden: `WHERE` → `GROUP BY` → `HAVING` → `ORDER BY`.
- **Rendimiento**: Para tablas grandes, asegúrate de que las columnas de agrupamiento tengan índices.

## 🧠 Consejos adicionales

- **Combina con joins**: Puedes usar `groupBy()` junto con `join()` para agrupar datos de múltiples tablas.
- **Usa alias descriptivos**: Nombra tus funciones agregadas con alias claros como `total_productos`, `precio_promedio`.
- **Validación de datos**: Considera usar `whereNotNull()` antes de agrupar para evitar valores nulos en los cálculos.
- **Formateado de números**: Para resultados monetarios, considera usar `ROUND()` en tus funciones agregadas.
- **Paginación**: Los resultados agrupados también pueden ser paginados usando `paginate()` en lugar de `get()`.

## 🎯 Casos de uso comunes

1. **Reportes de ventas**: Agrupar pedidos por mes/año para análisis de tendencias
2. **Inventario**: Contar productos por categoría o estado
3. **Análisis de usuarios**: Agrupar usuarios por fecha de registro o actividad
4. **Estadísticas de contenido**: Contar artículos por autor o categoría
5. **Métricas de rendimiento**: Calcular promedios por período o departamento

## 7. Métodos de Unión: `join()`, `leftJoin()`, `rightJoin()`

Los métodos de unión (joins) son fundamentales cuando necesitas combinar información de múltiples tablas relacionadas. Laravel ofrece métodos que funcionan de manera similar a las cláusulas `JOIN` en SQL tradicional, permitiendo crear consultas complejas de manera elegante.

### 🔎 ¿Qué hacen?

- **`join()`** → Realiza una unión interna (`INNER JOIN`). Solo devuelve registros que tienen coincidencias en ambas tablas.
- **`leftJoin()`** → Realiza una unión izquierda (`LEFT JOIN`). Muestra todos los registros de la tabla principal, incluso si no tienen coincidencias en la tabla secundaria.
- **`rightJoin()`** → Realiza una unión derecha (`RIGHT JOIN`). Muestra todos los registros de la tabla secundaria, incluso si no tienen coincidencias en la tabla principal.

### 🌚 Comparación con SQL tradicional (MySQL)

| Laravel Query Builder | SQL en MySQL |
|----------------------|--------------|
| `DB::table('categories')->join('posts', 'categories.id', '=', 'posts.category_id')->get();` | `SELECT * FROM categories INNER JOIN posts ON categories.id = posts.category_id;` |
| `DB::table('categories')->leftJoin('posts', 'categories.id', '=', 'posts.category_id')->get();` | `SELECT * FROM categories LEFT JOIN posts ON categories.id = posts.category_id;` |
| `DB::table('categories')->rightJoin('posts', 'categories.id', '=', 'posts.category_id')->get();` | `SELECT * FROM categories RIGHT JOIN posts ON categories.id = posts.category_id;` |
| `DB::table('users')->join('profiles', 'users.id', '=', 'profiles.user_id')->join('roles', 'users.role_id', '=', 'roles.id')->get();` | `SELECT * FROM users INNER JOIN profiles ON users.id = profiles.user_id INNER JOIN roles ON users.role_id = roles.id;` |

### 🧱 Sintaxis

```php
// Unión interna (INNER JOIN)
DB::table('tabla_principal')
    ->join('otra_tabla', 'tabla_principal.columna', '=', 'otra_tabla.columna')
    ->get();

// Unión izquierda (LEFT JOIN)
DB::table('tabla_principal')
    ->leftJoin('otra_tabla', 'tabla_principal.columna', '=', 'otra_tabla.columna')
    ->get();

// Unión derecha (RIGHT JOIN)
DB::table('tabla_principal')
    ->rightJoin('otra_tabla', 'tabla_principal.columna', '=', 'otra_tabla.columna')
    ->get();

// Múltiples uniones
DB::table('tabla_principal')
    ->join('tabla2', 'tabla_principal.id', '=', 'tabla2.principal_id')
    ->leftJoin('tabla3', 'tabla2.id', '=', 'tabla3.tabla2_id')
    ->get();
```

### 📋 Ejemplo básico - Unión interna

```php
Route::get('/categorias-con-posts', function () {
    $datos = DB::table('categories')
        ->join('posts', 'categories.id', '=', 'posts.category_id')
        ->select('categories.name as categoria', 'posts.title as titulo', 'posts.created_at')
        ->get();

    return $datos;
    // return response()->json($datos);
});
```

### 📋 Ejemplo avanzado - Múltiples uniones

```php
Route::get('/posts-completos', function () {
    $posts = DB::table('posts')
        ->join('categories', 'posts.category_id', '=', 'categories.id')
        ->join('users', 'posts.user_id', '=', 'users.id')
        ->leftJoin('tags', 'posts.id', '=', 'tags.post_id')
        ->select('posts.title', 
                'posts.content',
                'categories.name as categoria',
                'users.name as autor',
                'tags.name as etiqueta',
                'posts.created_at')
        ->orderBy('posts.created_at', 'desc')
        ->get();

    return $posts;
});
```

### 📋 Ejemplo con unión izquierda

```php
Route::get('/categorias-con-conteo', function () {
    $categorias = DB::table('categories')
        ->leftJoin('posts', 'categories.id', '=', 'posts.category_id')
        ->select('categories.name', 
                'categories.slug',
                DB::raw('COUNT(posts.id) as total_posts'))
        ->groupBy('categories.id', 'categories.name', 'categories.slug')
        ->orderBy('total_posts', 'desc')
        ->get();

    return $categorias;
});
```

### 📤 Resultado esperado (JSON)

```json
[
    {
        "categoria": "Tecnologia",
        "titulo": "Laravel para principiantes",
        "created_at": "2024-03-15 10:30:00"
    },
    {
        "categoria": "Educacion",
        "titulo": "Aprender SQL en 10 días",
        "created_at": "2024-03-14 14:20:00"
    },
    {
        "categoria": "Marketing",
        "titulo": "Estrategias digitales 2024",
        "created_at": "2024-03-13 09:15:00"
    }
]
```

---

## 8. Métodos de Paginación: `paginate()`, `simplePaginate()`

Laravel facilita la paginación de resultados con métodos integrados que devuelven objetos con información completa para generar vistas paginadas automáticamente. Esto es especialmente útil para optimizar el rendimiento en tablas con muchos registros.

### 🔎 ¿Qué hacen?

- **`paginate(n)`** → Devuelve `n` resultados por página con información completa de paginación (total de registros, páginas, enlaces).
- **`simplePaginate(n)`** → Similar a `paginate()` pero sin contar todos los registros (más rápido para tablas grandes).

### 🌚 Comparación con SQL tradicional (MySQL)

| Laravel Query Builder | SQL en MySQL |
|----------------------|--------------|
| `DB::table('categories')->paginate(10);` | `SELECT * FROM categories LIMIT 10 OFFSET x;` + `SELECT COUNT(*) FROM categories;` |
| `DB::table('categories')->simplePaginate(10);` | `SELECT * FROM categories LIMIT 10 OFFSET x;` (sin COUNT total) |
| `DB::table('categories')->where('active', 1)->paginate(5);` | `SELECT * FROM categories WHERE active = 1 LIMIT 5 OFFSET x;` |

### 🧱 Sintaxis

```php
// Paginación completa
DB::table('tabla')->paginate(10);

// Paginación simple (más rápida)
DB::table('tabla')->simplePaginate(10);

// Paginación con filtros
DB::table('tabla')
    ->where('columna', 'valor')
    ->paginate(15);

// Paginación personalizada
DB::table('tabla')->paginate(
    $perPage = 10, 
    $columns = ['*'], 
    $pageName = 'page', 
    $page = null
);
```

### 📋 Ejemplo básico

```php
Route::get('/categorias-paginadas', function () {
    $categorias = DB::table('categories')
        ->select('id', 'name', 'slug', 'created_at')
        ->orderBy('created_at', 'desc')
        ->paginate(5);

    return $categorias;
    // return response()->json($categorias);
});
```

### 📋 Ejemplo avanzado con filtros

```php
Route::get('/posts-paginados', function (Request $request) {
    $query = DB::table('posts')
        ->join('categories', 'posts.category_id', '=', 'categories.id')
        ->join('users', 'posts.user_id', '=', 'users.id')
        ->select('posts.title', 
                'posts.excerpt',
                'categories.name as categoria',
                'users.name as autor',
                'posts.created_at');

    // Filtros opcionales
    if ($request->filled('categoria')) {
        $query->where('categories.slug', $request->categoria);
    }

    if ($request->filled('autor')) {
        $query->where('users.id', $request->autor);
    }

    $posts = $query->orderBy('posts.created_at', 'desc')
                  ->paginate(10);

    return $posts;
});
```

### 📤 Resultado esperado (estructura)

```json
{
    "current_page": 1,
    "data": [
        {
            "id": 1,
            "name": "Tecnologia",
            "slug": "tecnologia",
            "created_at": "2024-03-15 10:30:00"
        },
        {
            "id": 2,
            "name": "Educacion",
            "slug": "educacion",
            "created_at": "2024-03-14 14:20:00"
        }
    ],
    "first_page_url": "http://localhost/categorias-paginadas?page=1",
    "from": 1,
    "last_page": 3,
    "last_page_url": "http://localhost/categorias-paginadas?page=3",
    "next_page_url": "http://localhost/categorias-paginadas?page=2",
    "path": "http://localhost/categorias-paginadas",
    "per_page": 5,
    "prev_page_url": null,
    "to": 5,
    "total": 13
}
```

---

## 9. Métodos de Ejecución: `get()`, `first()`, `pluck()`, `find()`

Una vez que has construido tu consulta con filtros, uniones o agrupamientos, debes ejecutarla usando uno de los métodos de ejecución que devuelven los datos en diferentes formatos según tus necesidades.

### 🔎 ¿Qué hacen?

- **`get()`** → Devuelve una colección con todos los registros que coinciden con la consulta.
- **`first()`** → Devuelve solo el primer registro que coincide con la consulta.
- **`pluck()`** → Extrae los valores de una sola columna y los devuelve como array.
- **`find(id)`** → Busca un registro por su clave primaria (ID).

### 🌚 Comparación con SQL tradicional (MySQL)

| Laravel Query Builder | SQL en MySQL |
|----------------------|--------------|
| `DB::table('categories')->get();` | `SELECT * FROM categories;` |
| `DB::table('categories')->first();` | `SELECT * FROM categories LIMIT 1;` |
| `DB::table('categories')->pluck('name');` | `SELECT name FROM categories;` |
| `DB::table('categories')->find(1);` | `SELECT * FROM categories WHERE id = 1 LIMIT 1;` |
| `DB::table('categories')->pluck('name', 'id');` | `SELECT id, name FROM categories;` (como array asociativo) |

### 🧱 Sintaxis

```php
// Obtener todos los registros
DB::table('tabla')->get();

// Obtener el primer registro
DB::table('tabla')->first();

// Obtener valores de una columna
DB::table('tabla')->pluck('columna');

// Obtener por ID
DB::table('tabla')->find(1);

// Obtener array asociativo
DB::table('tabla')->pluck('nombre', 'id');

// Verificar si existe
DB::table('tabla')->exists();

// Contar registros
DB::table('tabla')->count();
```

### 📋 Ejemplos prácticos

```php
// Obtener todas las categorías
Route::get('/todas-categorias', function () {
    $categorias = DB::table('categories')
        ->select('id', 'name', 'slug')
        ->get();
    
    return $categorias;
});

// Obtener una categoría específica
Route::get('/primera-categoria', function () {
    $categoria = DB::table('categories')
        ->where('active', 1)
        ->orderBy('created_at', 'desc')
        ->first();
    
    return $categoria;
});

// Obtener solo los nombres de las categorías
Route::get('/nombres-categorias', function () {
    $nombres = DB::table('categories')
        ->where('active', 1)
        ->pluck('name');
    
    return $nombres;
});

// Obtener array asociativo para selects
Route::get('/categorias-select', function () {
    $categorias = DB::table('categories')
        ->where('active', 1)
        ->pluck('name', 'id');
    
    return $categorias;
});

// Buscar por ID
Route::get('/categoria/{id}', function ($id) {
    $categoria = DB::table('categories')->find($id);
    
    if (!$categoria) {
        return response()->json(['error' => 'Categoría no encontrada'], 404);
    }
    
    return $categoria;
});
```

### 📤 Resultados esperados

**`get()` - Colección completa:**
```json
[
    {"id": 1, "name": "Tecnologia", "slug": "tecnologia"},
    {"id": 2, "name": "Educacion", "slug": "educacion"}
]
```

**`first()` - Primer registro:**
```json
{
    "id": 1,
    "name": "Tecnologia", 
    "slug": "tecnologia"
}
```

**`pluck('name')` - Array de valores:**
```json
[
    "Tecnologia",
    "Educacion",
    "Marketing"
]
```

**`pluck('name', 'id')` - Array asociativo:**
```json
{
    "1": "Tecnologia",
    "2": "Educacion", 
    "3": "Marketing"
}
```

## 📊 Tabla resumen de Métodos de Ejecución

| Método Laravel | Descripción | Equivalente en SQL | Tipo de Retorno |
|---------------|-------------|-------------------|----------------|
| `get()` | Obtiene todos los registros | `SELECT * FROM tabla` | Colección |
| `first()` | Obtiene el primer registro | `SELECT * FROM tabla LIMIT 1` | Objeto o null |
| `pluck('col')` | Extrae valores de una columna | `SELECT col FROM tabla` | Array |
| `pluck('col', 'id')` | Array asociativo | `SELECT id, col FROM tabla` | Array asociativo |
| `find(id)` | Busca por clave primaria | `SELECT * FROM tabla WHERE id = ?` | Objeto o null |
| `exists()` | Verifica si existen registros | `SELECT EXISTS(SELECT 1 FROM tabla ...)` | Boolean |
| `count()` | Cuenta registros | `SELECT COUNT(*) FROM tabla` | Integer |
| `sum('col')` | Suma valores de una columna | `SELECT SUM(col) FROM tabla` | Number |
| `avg('col')` | Promedio de una columna | `SELECT AVG(col) FROM tabla` | Number |
| `min('col')` | Valor mínimo | `SELECT MIN(col) FROM tabla` | Mixed |
| `max('col')` | Valor máximo | `SELECT MAX(col) FROM tabla` | Mixed |

## 📌 Notas útiles

### Para Joins:
- **Especifica columnas**: Usa `select()` para evitar conflictos de nombres entre tablas.
- **Alias de tablas**: Considera usar alias para consultas complejas: `DB::table('posts as p')`
- **Rendimiento**: Los `leftJoin()` pueden ser más lentos que `join()` en tablas grandes.

### Para Paginación:
- **Performance**: Usa `simplePaginate()` para tablas muy grandes donde no necesitas el conteo total.
- **Personalización**: Puedes personalizar las vistas de paginación en Laravel.
- **URLs**: La paginación preserva automáticamente los parámetros de consulta.

### Para Ejecución:
- **Memoria**: `get()` carga todos los registros en memoria. Para tablas grandes, considera usar `chunk()`.
- **Validación**: Siempre verifica si `first()` o `find()` devuelven `null`.
- **Optimización**: Usa `pluck()` cuando solo necesites valores específicos.

## 🧠 Consejos adicionales

1. **Combina métodos**: Puedes usar joins + filtros + paginación en la misma consulta.
2. **Índices**: Asegúrate de tener índices en las columnas de unión para mejor rendimiento.
3. **N+1 Problem**: Los joins pueden ayudar a evitar el problema N+1 en consultas relacionadas.
4. **Debugging**: Usa `toSql()` para ver la consulta SQL generada: `DB::table('users')->toSql()`
