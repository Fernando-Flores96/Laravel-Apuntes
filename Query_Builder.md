# Laravel - Guía Práctica: Consultas con Query Builder (DB::table)
##### 📌 ¿Qué es el Query Builder?
El Query Builder de Laravel proporciona una forma fluida y segura de construir consultas SQL sin escribir SQL puro. Usa la clase DB de Laravel.
 `use Illuminate\Support\Facades\DB; `
✅ Ruta de prueba
Creamos una ruta básica para probar nuestras consultas:
```php
Route::get('/prueba', function () {
    // Aquí van las consultas
});```
##### 🔎 Métodos del Query Builder

### 1. `find(id)`

```php
$category = DB::table('categories')->find(4);
return $category->name;
```

🔹 Devuelve un solo registro por **clave primaria** (`id`).  
🔹 Equivalente a:

```sql
SELECT * FROM categories WHERE id = 4 LIMIT 1;
```

---

### 2. `where()` + `pluck()`

```php
$categories = DB::table('categories')
                ->where('id', '>=', 2)
                ->pluck('name', 'id');

return $categories;
```

🔹 `pluck('name', 'id')` devuelve un array asociativo:

```php
[
  2 => 'Deportes',
  3 => 'Tecnología',
  4 => 'Moda'
]
```

---

### 3. `get()`

```php
$categories = DB::table('categories')->get();
```

🔹 Devuelve una colección de todos los resultados.  
🔹 Para acceder a cada elemento:

```php
foreach ($categories as $cat) {
    echo $cat->name;
}
```

---

### 4. `first()`

```php
$category = DB::table('categories')->where('id', 3)->first();
return $category->name;
```

🔹 Devuelve solo el **primer registro** encontrado.

---

## 🔄 Transformación de resultados

### `map()`

```php
$categories = DB::table('categories')->get();

$names = $categories->map(function ($category) {
    return $category->name;
});

return $names;
```

🔹 Devuelve una nueva colección con solo los nombres.

---

## 🧪 Resumen Rápido

| Método      | Descripción                          | Tipo de Retorno           |
|-------------|--------------------------------------|----------------------------|
| `find(id)`  | Busca por clave primaria             | Objeto                     |
| `first()`   | Primer resultado con un `where()`    | Objeto                     |
| `get()`     | Todos los resultados                 | Colección                  |
| `pluck()`   | Array asociativo de dos columnas     | Array (clave => valor)     |
| `map()`     | Transforma una colección             | Nueva colección            |

---

## 🧾 Ejemplo completo en la ruta `/prueba`

```php
Route::get('/prueba', function() {
    // Recupera categorías con ID >= 2
    $categories = DB::table('categories')
                    ->where('id', '>=', 2)
                    ->pluck('name', 'id');

    return $categories; // Devuelve como JSON automáticamente
})->name('prueba');
```

---

## 💡 Tips Adicionales

- Puedes usar `orderBy()`, `limit()`, `count()` junto con `where()` y otros.
- Estas consultas son útiles cuando aún no estás usando Eloquent o quieres simplicidad.
- Usa `response()->json()` si necesitas más control sobre el formato.
