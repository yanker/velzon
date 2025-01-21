# Generación de Factoría para datos de ejemplo

---

## :zap: Creamos la Factoría

```bash
php artisan make:factory CustomerFactory --model=Customer
```

Dentro de este archivo, defines los datos falsos que generará la factory:

```php
namespace Database\Factories;

use App\Models\Customer;
use App\Models\User;
use Illuminate\Database\Eloquent\Factories\Factory;

/**
 * @extends \Illuminate\Database\Eloquent\Factories\Factory<\App\Models\Model>
 */
class CustomerFactory extends Factory
{
    /**
     * Define the model's default state.
     *
     * @return array<string, mixed>
     */

    protected $model = Customer::class;

    public function definition(): array
    {
        // Obtener un usuario aleatorio o null si no hay usuarios
        $commercialUser = User::inRandomOrder()->first()->id ?? null;

        return [

            // Faker de Clientes
            // Ejecutar Faker
            'id' => $this->faker->uuid(),
            'title' => $this->faker->title(),
            'slug' => $this->faker->slug(),
            'active' => $this->faker->boolean(),
            'stitle' => $this->faker->title(20),
            'address' => $this->faker->address(),
            'phone' => $this->faker->phoneNumber(),
            'email' => $this->faker->email(),
            'location_id' => $this->faker->numberBetween(10, 1000),
            'commercial_user' => $commercialUser,
        ];
    }
}

```

## :zap: Creamos el Seeder

```php
php artisan make:seeder FakesTest/CustomerSeeder
```

### Usamos la Factory con el Seeder

```php
namespace Database\Seeders;

use Illuminate\Database\Seeder;
use App\Models\Customer;

class CustomerSeeder extends Seeder
{
    public function run()
    {
        // Generar 10 registros de customers usando la factory
        Customer::factory()->count(10)->create();
    }
}

```

### Ejecutamos el Seeder

```php
php artisan db:seed --class=CustomerSeeder
php artisan db:seed --class="Database\Seeders\FakesTest\CustomersFake" --> Si va en una subcarpeta
```

### Ejecutar el Seeder junto a otros Seeder (Opcional)

Si deseas que este seeder se ejecute junto con otros seeders cuando ejecutas el comando php artisan db:seed, asegúrate de registrarlo en el archivo DatabaseSeeder.php. Este archivo se encuentra en database/seeders/DatabaseSeeder.php y puedes agregar el seeder así:

```php
namespace Database\Seeders;

use Illuminate\Database\Seeder;

class DatabaseSeeder extends Seeder
{
    public function run()
    {
        $this->call(CustomerSeeder::class);
        // Aquí puedes llamar otros seeders si tienes más
    }
}
```

```php
php artisan db:seed
```

### :scroll: NOTAS

He tenido problemasp porque mi tabla no trabaja con id sino con uuid y por tanto en el modelo debemos indicar lo siguiente:
protected $keyType = 'string'; // Asegúrate de que sea string
public $incrementing = false; // No usar auto-incremento
