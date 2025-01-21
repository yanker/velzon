# Laravel Backups

---

Se ha instalado un paquete para la gestión de backups.

**NOTA:** Las copias de seguridad se guardan en la ruta:

> 📁 `storage -> app -> APP_NAME (INDIRA)`

## Instalación

```bash
composer require spatie/laravel-backup
```

```bash
php artisan vendor:publish --provider="Spatie\Backup\BackupServiceProvider"
```

By default, the backup will be saved into the `storage/app/Laravel/` directory of your Laravel application. We recommend that you create a disk named `backups` (you can use any name you prefer) in `filesystems.php` and specify that name in the `disk` key of the `backup.php` config file.

## Taking Backups

### Backup Completo

```bash
php artisan backup:run
```

### Backup a un Disco Específico

```bash
php artisan backup:run --only-to-disk=name-of-your-disk
```

### Solo Base de Datos

```bash
php artisan backup:run --only-db
```

### Solo Archivos

```bash
php artisan backup:run --only-files
```

## Limpiar Backups Antiguos

```bash
php artisan backup:clean
```
