# :scroll: Listado de Comandos + Utilizados

Aquí tienes una lista de comandos de `php artisan` agrupados por categorías, junto con una breve explicación de cada uno:

### 🌐 Global

-   **`php artisan list`**: Muestra una lista de todos los comandos disponibles.
-   **`php artisan help [command]`**: Muestra ayuda para un comando específico.

### 🔐 Auth

-   **`php artisan make:auth`**: Genera el scaffolding de autenticación (Laravel 6.x y anteriores).

### 🗃️ Cache

-   **`php artisan cache:clear`**: Limpia la caché de la aplicación.
-   **`php artisan cache:forget [key]`**: Elimina un ítem específico de la caché.
-   **`php artisan cache:table`**: Crea una migración para la tabla de caché de la base de datos.

### ⚙️ Config

-   **`php artisan config:cache`**: Crea un archivo de caché para la configuración.
-   **`php artisan config:clear`**: Limpia la caché de configuración.

### 🗄️ Database

-   **`php artisan db:seed`**: Ejecuta los seeders de la base de datos.
-   **`php artisan migrate`**: Ejecuta las migraciones pendientes.
-   **`php artisan migrate:rollback`**: Revierte la última migración.
-   **`php artisan migrate:reset`**: Revierte todas las migraciones.
-   **`php artisan migrate:refresh`**: Revierte y vuelve a ejecutar todas las migraciones.
-   **`php artisan migrate:status`**: Muestra el estado de las migraciones.
-   **`php artisan make:migration [name]`**: Crea una nueva migración.
-   **`php artisan make:seeder [name]`**: Crea un nuevo seeder.
-   **`php artisan make:factory [name]`**: Crea una nueva fábrica de modelos.
-   **`php artisan make:model [name]`**: Crea un nuevo modelo.

### 📅 Event

-   **`php artisan event:generate`**: Genera eventos y listeners para las clases definidas en `EventServiceProvider`.
-   **`php artisan make:event [name]`**: Crea una nueva clase de evento.
-   **`php artisan make:listener [name]`**: Crea una nueva clase de listener.

### 🔑 Key

-   **`php artisan key:generate`**: Genera una nueva clave de aplicación.

### 🛠️ Make

-   **`php artisan make:controller [name]`**: Crea un nuevo controlador.
-   **`php artisan make:middleware [name]`**: Crea un nuevo middleware.
-   **`php artisan make:request [name]`**: Crea una nueva clase de solicitud.
-   **`php artisan make:job [name]`**: Crea una nueva clase de trabajo.
-   **`php artisan make:command [name]`**: Crea un nuevo comando de consola.
-   **`php artisan make:policy [name]`**: Crea una nueva política.
-   **`php artisan make:provider [name]`**: Crea un nuevo proveedor de servicios.
-   **`php artisan make:rule [name]`**: Crea una nueva regla de validación.

### 📋 Queue

-   **`php artisan queue:work`**: Procesa trabajos en la cola.
-   **`php artisan queue:listen`**: Escucha trabajos en la cola y los procesa.
-   **`php artisan queue:restart`**: Reinicia los trabajos en cola después de que finalicen.
-   **`php artisan queue:failed`**: Muestra una lista de trabajos fallidos.
-   **`php artisan queue:retry [id]`**: Reintenta un trabajo fallido.
-   **`php artisan queue:table`**: Crea una migración para la tabla de trabajos en cola.

### 🛤️ Route

-   **`php artisan route:list`**: Muestra una lista de todas las rutas registradas.
-   **`php artisan route:cache`**: Crea un archivo de caché para las rutas.
-   **`php artisan route:clear`**: Limpia la caché de rutas.

### 🗓️ Schedule

-   **`php artisan schedule:run`**: Ejecuta los comandos programados.
-   **`php artisan schedule:work`**: Ejecuta los comandos programados en segundo plano.

### 🗄️ Storage

-   **`php artisan storage:link`**: Crea un enlace simbólico desde `public/storage` a

### 🧪 Test

-   **`php artisan test`**: Ejecuta las pruebas de la aplicación.

### 👁️ View

-   **`php artisan view:cache`**: Crea un archivo de caché para las vistas.
-   **`php artisan view:clear`**: Limpia la caché de vistas.

Esta lista cubre los comandos más comunes y útiles de `php artisan`. Puedes obtener más detalles sobre cada comando utilizando `php artisan list o php artisan help [command]`.
