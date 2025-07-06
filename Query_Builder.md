# Laravel - Guía Práctica: Consultas con Query Builder (DB::table)
##### 📌 ¿Qué es el Query Builder?
El Query Builder de Laravel proporciona una forma fluida y segura de construir consultas SQL sin escribir SQL puro. Usa la clase DB de Laravel.
 `use Illuminate\Support\Facades\DB; `
✅ Ruta de prueba
Creamos una ruta básica para probar nuestras consultas:
```php
Route::get('/prueba', function () {
    // Aquí van las consultas
});
```

```php

```



#####  Explicación de métodos
```php
find(valor_id)
```
$category = DB::table('categories')->find(4);
Busca un registro por su ID primario.
```sql
Equivalente a: SELECT * FROM categories WHERE id = 4 LIMIT 1
```
Devuelve un objeto (no una colección).

Se accede así: $category->name, $category->id

#### where(campo, operador, valor)
```php
$categories = DB::table('categories')
    ->where('id', '>=', 2)
    ->get();
```
Aplica una condición tipo WHERE en SQL.
Puedes encadenar múltiples where().





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
