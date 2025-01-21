# :rocket: Asignar Roles

---

Para asignar un rol a un usuario utilizando el paquete `spatie/laravel-permission`, primero asegúrate de que el modelo `User` use el trait `HasRoles`. Luego, puedes asignar roles a los usuarios utilizando el método `assignRole`

1. **Asegúrate de que el modelo `User` use el trait `HasRoles`**:

    ```php
    use Spatie\Permission\Traits\HasRoles;

    class User extends Authenticatable
    {
        use HasRoles;

        // Otras relaciones y métodos
    }
    ```

2. **Asignar un rol a un usuario**:

    Puedes asignar un rol a un usuario en cualquier parte de tu aplicación donde tengas acceso al modelo `User`. Aquí hay un ejemplo de cómo hacerlo en un controlador:

    ```php
    use App\Models\User;
    use Spatie\Permission\Models\Role;

    class UserController extends Controller
    {
        public function assignRoleToUser($userId, $roleName)
        {
            // Encuentra al usuario por su ID
            $user = User::findOrFail($userId);

            // Asigna el rol al usuario
            $user->assignRole($roleName);

            return redirect()->back()->with('success', 'Rol asignado correctamente.');
        }
    }
    ```

3. **Ejemplo de uso en una ruta o acción de controlador**:

    ```php
    Route::get('/assign-role/{userId}/{roleName}', [UserController::class, 'assignRoleToUser']);
    ```

    Con esta ruta, puedes asignar un rol a un usuario accediendo a una URL como `http://tu-dominio.com/assign-role/1/Clientes`, donde `1` es el ID del usuario y `Clientes` es el nombre del rol.

4. **Asignar roles en una seeder**:

    También puedes asignar roles a usuarios en una seeder. Aquí hay un ejemplo de cómo hacerlo en una seeder:

    ```php
    use Illuminate\Database\Seeder;
    use App\Models\User;
    use Spatie\Permission\Models\Role;

    class UserRoleSeeder extends Seeder
    {
        public function run()
        {
            // Encuentra al usuario por su ID o email
            $user = User::where('email', 'user@example.com')->first();

            // Asigna el rol al usuario
            $user->assignRole('Clientes');
        }
    }
    ```

    Luego, ejecuta la seeder con el comando:

    ```bash
    php artisan db:seed --class=UserRoleSeeder
    ```
