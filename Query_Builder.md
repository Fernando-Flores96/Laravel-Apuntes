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
### Método: first()

## ¿Qué hace?

Devuelve solo el primer resultado de la consulta. Ideal cuando esperas una sola fila.

## Sintaxis
```php
DB::table('tabla')->where('condicion')->first();
```
## Ejemplo
```php
Route::get('/prueba', function () {
    $category = DB::table('categories')
            ->where('id', 1)
   ->first();
    return $category;
});
```
📾 Equivalente en SQL







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
