#   Guía: Consultas con Query Builder en Laravel
🧭 Ruta de ejemplo

El comando para crear una migración es `php artisan make:migration`.
```php
Route::get('/prueba', function() {
    $categories = DB::table('categories')
                    ->where('id', '>=', 2)
                    ->pluck('name', 'id');
    return $categories;
});
