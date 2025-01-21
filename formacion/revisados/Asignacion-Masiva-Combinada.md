# 📋 Asignación Masiva Personalizada

---

Para usar asignación masiva en Laravel y al mismo tiempo manejar campos como `uid` o `slug` que no provienen del formulario, puedes combinar ambos enfoques de la siguiente manera:

## 1. 📝 Usar asignación masiva para los campos del formulario

Primero, asegúrate de que los campos que provienen del formulario estén en la propiedad `$fillable` del modelo. La asignación masiva te permite actualizar los datos con un simple llamado a `create()` o `update()`.

```php
class YourModel extends Model
{
    protected $fillable = [
        'name', 'email', 'description', // campos que vienen del formulario
    ];
}
```

## 2. 🔧 Generar campos adicionales (como `uid` o `slug`)

Luego, puedes generar o manipular los campos que no vienen directamente del formulario (como `uid` o `slug`) antes de guardar el modelo. Esto lo puedes hacer dentro de tu controlador o en los eventos del modelo (`creating` o `saving`).

### 📂 Ejemplo en el controlador

```php
public function store(Request $request)
{
    // Asignación masiva para los campos que vienen del formulario
    $data = $request->only(['name', 'email', 'description']);

    // Generar el campo 'uid' y 'slug'
    $data['uid'] = (string) Str::uuid(); // O cualquier otra lógica
    $data['slug'] = Str::slug($data['name'], '-'); // O cualquier otra lógica

    // Crear el modelo con asignación masiva
    YourModel::create($data);

    return redirect()->route('your-model.index');
}
```

### 🔄 Alternativa: Usar los eventos del modelo (`creating`)

Si prefieres que la lógica de generación de `uid` o `slug` esté más encapsulada dentro del modelo, puedes utilizar eventos como `creating` para hacerlo automáticamente.

```php
class YourModel extends Model
{
    protected static function boot()
    {
        parent::boot();

        static::creating(function ($model) {
            // Generar automáticamente 'uid' y 'slug' si no están ya asignados
            if (!$model->uid) {
                $model->uid = (string) Str::uuid();
            }
            if (!$model->slug) {
                $model->slug = Str::slug($model->name, '-');
            }
        });
    }

    protected $fillable = [
        'name', 'email', 'description',
    ];
}
```

## 🏁 Conclusión

Puedes usar la asignación masiva para los campos del formulario y agregar campos adicionales como `uid` o `slug` directamente en el controlador antes de guardar, o usar eventos de modelo (`creating`) para automatizar la generación de estos campos al momento de la creación del modelo.
