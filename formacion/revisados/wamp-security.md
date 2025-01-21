# Securizar WAMP para Producción

Para securizar WAMP (Windows, Apache, MySQL, PHP) en un entorno de producción, es importante configurar el servidor para reducir riesgos de seguridad y garantizar un rendimiento óptimo. Aquí tienes una lista detallada de acciones:

---

### **1. Configuración de Apache**

1.  **Desactivar módulos innecesarios**:

    -   Edita el archivo `httpd.conf` y desactiva los módulos no requeridos comentando las líneas correspondientes (`#LoadModule ...`).

2.  **Ocultar información del servidor**:

    -   Configura estas directivas en `httpd.conf`:
        ```apache
        ServerTokens Prod
        ServerSignature Off
        ```

3.  **Configurar directorios y permisos**:

    -   Asegúrate de que los directorios solo permitan acceso a los archivos necesarios:
        ```apache
        <Directory />
            Options None
            AllowOverride None
            Require all denied
        </Directory>
        ```

4.  **Usar HTTPS**:

    -   Instala un certificado SSL (por ejemplo, de Let's Encrypt) y configura el uso de HTTPS en los hosts virtuales.
        <br>

5.  **Activar un cortafuegos en Apache**:

    -   Usa módulos como `mod_security` o `mod_evasive` para protegerte contra ataques comunes.

    <br>

6.  **Restringir métodos HTTP**:
    -   Limita los métodos permitidos, como GET y POST:
        ```apache
        <LimitExcept GET POST>
            Require all denied
        </LimitExcept>
        ```

---

### **2. Configuración de MySQL**

1.  **Usar contraseñas fuertes para usuarios**:

    -   Asegúrate de que todas las cuentas tengan contraseñas seguras.

2.  **Eliminar usuarios y bases de datos de prueba**:

    -   Borra el usuario `root` sin contraseña y las bases de datos predeterminadas no usadas:
        ```sql
        DROP DATABASE test;
        DELETE FROM mysql.user WHERE User='root' AND Host!='localhost';
        ```

3.  **Configurar permisos estrictos**:

    -   Da a cada aplicación solo los permisos mínimos necesarios para operar.

4.  **Desactivar acceso remoto al servidor MySQL**:

    -   Edita el archivo `my.ini` y comenta o configura `bind-address` para permitir conexiones solo desde localhost:
        ```
        bind-address = 127.0.0.1
        ```

5.  **Registrar actividades sospechosas**:
    -   Activa los logs de consultas y errores en MySQL para monitorear accesos no autorizados.

---

### **3. Configuración de PHP**

1. **Desactivar errores en pantalla**:

    - Edita `php.ini` para evitar mostrar errores en producción:
        ```ini
        display_errors = Off
        log_errors = On
        error_log = "C:\path\to\php-error.log"
        ```

2. **Limitar funciones peligrosas**:

    - Deshabilita funciones potencialmente peligrosas:
        ```ini
        disable_functions = exec,passthru,shell_exec,system
        ```

3. **Configurar `open_basedir`**:

    - Restringe el acceso de PHP a directorios específicos:
        ```ini
        open_basedir = "C:\path\to\your\project\"
        ```

4. **Usar sesiones seguras**:
    - Configura `session.cookie_secure` y `session.cookie_httponly`:
        ```ini
        session.cookie_secure = 1
        session.cookie_httponly = 1
        ```

---

### **4. Seguridad del sistema operativo (Windows)**

1. **Configurar permisos de carpetas**:

    - Asegúrate de que los archivos de WAMP solo sean accesibles por usuarios autorizados.

2. **Actualizar regularmente**:

    - Mantén Windows, Apache, MySQL y PHP actualizados con los últimos parches de seguridad.

3. **Usar un cortafuegos**:

    - Configura el firewall de Windows para permitir solo los puertos necesarios, como el 80 (HTTP) y 443 (HTTPS).

4. **Desactivar servicios innecesarios**:

    - Apaga cualquier servicio de Windows no requerido para la producción.

5. **Configurar copias de seguridad**:
    - Implementa un sistema de respaldo regular para bases de datos y archivos críticos.

---

### **5. Monitoreo y pruebas de seguridad**

1. **Realizar escaneos de vulnerabilidades**:

    - Usa herramientas como [Nikto](https://cirt.net/Nikto2) o [OWASP ZAP](https://www.zaproxy.org/) para analizar posibles problemas.

2. **Configurar logs centralizados**:

    - Configura logs para Apache, PHP y MySQL y revisa regularmente eventos sospechosos.

3. **Pruebas regulares**:
    - Realiza pruebas de penetración o usa plataformas como Nessus para verificar la seguridad del servidor.

---

Estas medidas, junto con un monitoreo constante, pueden transformar un entorno WAMP en una solución suficientemente robusta para producción.
