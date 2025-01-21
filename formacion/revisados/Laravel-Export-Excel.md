# 📊 Laravel Excel

---

## 🌐 Documentación

[Laravel Excel Documentation](https://docs.laravel-excel.com/3.1/exports/exportables.html)

Laravel Excel es un paquete que utilizo para las exportaciones, donde antes de exportar puedes manipular los datos. Para crear una exportación deberemos correr el comando:

```bash
php artisan make:export UsersExport --model=User
```

Una vez creada la exportación, podremos manipular los datos. Por ejemplo:

```php
namespace App\Exports;

use App\Models\User;
use Maatwebsite\Excel\Concerns\FromCollection;
use Maatwebsite\Excel\Concerns\WithMapping;

class UsersExport implements FromCollection, WithMapping
{
    public function collection()
    {
        return User::paginate(100); // Puedes ajustar los resultados o tratarlos aquí
    }

    public function map($user): array
    {
        return [
            $user->id,
            $user->name,
            $user->email,
            $user->created_at->format('Y-m-d'), // Personalización de columnas
        ];
    }
}
```

Posteriormente, crearemos la ruta y añadiremos el método en el controlador:

```php
namespace App\Http\Controllers;

use App\Exports\UsersExport;
use Maatwebsite\Excel\Facades\Excel;

class UserController extends Controller
{
    public function export()
    {
        //$filters = $request->all(); // Captura los filtros
        //return Excel::download(new UsersExport($filters), 'users.xlsx');
        return Excel::download(new UsersExport, 'users.xlsx');
    }
}
```

## 🛤️ Ruta

```php
Route::get('/users/export', [UserController::class, 'export'])->name('users.export');
```
