# :rocket: Migraciones Personalizadas

---

Si deseas organizar tus migraciones en una tabla diferente para ejecutarlas de manera más controlada o específica, puedes lograrlo modificando la configuración predeterminada de Laravel. Aquí te explico los pasos:

## :sparkles: Notas

### Crear migración para actualizar una tabla

```bash
php artisan make:migration add_new_column_to_customers_agreement_table --table=customers_agreement --path=database/migrations/indira
```

### Ejemplos Utilizados

```bash
php artisan make:migration create_sys_familys_table --path=database/migrations/familys
php artisan migrate --path=database/migrations/indira/2024_09_30_100302_create_customer_table.php
```

Para revertir alguna migración utiliza Tinker y copia el código del `down`:

```bash
php artisan tinker
php artisan migrate:rollback --step=1 --path=database/migrations/indira/2024_09_30_100302_create_customer_table.php
php artisan migrate --path=database/migrations/indira/2024_09_30_100302_create_customer_table.php
```

## 1. Crear una Tabla Personalizada para Almacenar las Migraciones

Laravel, por defecto, usa la tabla `migrations` para rastrear cuáles migraciones ya se han ejecutado. Sin embargo, puedes cambiar el nombre de esta tabla o crear varias tablas para diferentes conjuntos de migraciones.

Para cambiar el nombre de la tabla de migraciones:

-   Abre el archivo `config/database.php`.
-   Busca la clave `migrations` y cámbiala por el nombre que prefieras. Por ejemplo:

```php
'migrations' => 'mi_migraciones_personalizadas',
```

Esto le indicará a Laravel que use la tabla `mi_migraciones_personalizadas` para almacenar las migraciones ejecutadas.

## 2. Crear un Grupo de Migraciones Personalizado

Otra opción es tener varias carpetas de migraciones y ejecutar solo las migraciones de una carpeta específica cuando lo necesites.

### Organiza tus migraciones en carpetas separadas

Puedes crear subdirectorios dentro de `database/migrations` para tener diferentes conjuntos de migraciones. Por ejemplo:

```bash
php artisan make:migration create_nombre_tabla_table --path=database/familia/migration
```

Ejemplo:

```bash
php artisan make:migration create_sys_familys_table --path=database/migrations/familys
```

Estructura de directorios:

```
database/migrations
├── general
├── sys
└── user
```

### Ejecutar migraciones de una carpeta específica

Laravel permite ejecutar migraciones de una carpeta específica utilizando el comando `--path`. Por ejemplo, si tienes migraciones dentro de la carpeta `sys`:

```bash
php artisan migrate --path=/database/migrations/sys
```

Esto ejecutará solo las migraciones dentro del subdirectorio `sys`.

## 3. Ejecutar Migraciones Manualmente

Si prefieres ejecutar migraciones específicas de manera manual, puedes utilizar el siguiente comando apuntando a un archivo específico:

```bash
php artisan migrate --path=/database/migrations/<nombre_del_archivo_migracion>.php
```

De esta forma, puedes tener control sobre cuándo y cuáles migraciones se ejecutan, mientras mantienes una tabla de migraciones separada si es necesario.

## :scroll: Resumen de Opciones

-   **Cambiar la tabla de migraciones** en `config/database.php`.
-   **Organizar migraciones en carpetas** y ejecutarlas por separado usando `--path`.
-   **Ejecutar migraciones individuales** utilizando el archivo de migración específico.
