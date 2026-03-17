# Recuperar / Reparar MySQL en XAMPP (Windows)

Guía paso a paso para solucionar errores cuando MySQL no inicia en XAMPP y recuperar bases de datos.

---

## Problema

MySQL no inicia y muestra errores como:

```
MySQL shutdown unexpectedly
MySQL could not be started
```

---

## Requisitos

* XAMPP instalado en `C:\xamppp` (ajustar si tu ruta es diferente)
* Acceso a consola (CMD)

---

## Fase 1: Diagnóstico

### 1. Verificar si MySQL está en ejecución

Ejecuta en CMD:

```bat
tasklist | findstr mysqld
```

* Si aparece `mysqld.exe`: MySQL ya está corriendo
* Si no aparece: continuar

---

### 2. Verificar si el puerto 3306 está en uso

```bat
netstat -ano | findstr :3306
```

Si aparece un proceso (PID):

```bat
taskkill /PID <PID> /F
```

Reemplaza `<PID>` por el número correspondiente.

---

## Fase 2: Eliminación de archivos corruptos

Ir a la carpeta de datos:

```bat
cd C:\xamppp\mysql\data
```

Eliminar archivos de logs:

```bat
del ib_logfile0
del ib_logfile1
del *.pid
```

---

## Fase 3: Forzar recuperación (opcional)

Abrir archivo de configuración:

```bat
notepad C:\xamppp\mysql\bin\my.ini
```

Agregar temporalmente:

```ini
innodb_force_recovery=1
```

Si no funciona, probar valores del 1 al 6 (uno a la vez).

---

## Fase 4: Restauración completa (solución principal)

### 1. Renombrar carpeta de datos

```bat
ren C:\xamppp\mysql\data data_old
```

---

### 2. Restaurar estructura limpia

```bat
xcopy C:\xamppp\mysql\backup C:\xamppp\mysql\data /s /e /y
```

Cuando el sistema pregunte:

```
¿Archivo o directorio?
```

Responder:

```
D
```

---

### 3. Iniciar MySQL

```bat
C:\xamppp\mysql_start.bat
```

---

### 4. Verificar ejecución

```bat
tasklist | findstr mysqld
```

Si aparece el proceso, MySQL está funcionando.

---

## Fase 5: Recuperar bases de datos

Ir a la carpeta antigua:

```bat
cd C:\xamppp\mysql\data_old
```

Listar contenido:

```bat
dir
```

---

### Copiar bases específicas

Ejemplo:

```bat
xcopy cafeteria C:\xamppp\mysql\data\cafeteria /s /e /y
xcopy deportesdb C:\xamppp\mysql\data\deportesdb /s /e /y
```

Cuando pregunte:

```
¿Archivo o directorio?
```

Responder:

```
D
```

---

### Reiniciar MySQL

```bat
C:\xamppp\mysql_start.bat
```

---

### Verificar en navegador

Abrir:

```
http://localhost/phpmyadmin
```

---

## Errores comunes

### 1. Rutas incorrectas

Incorrecto:

```bat
cd C:\xampppmysql_start.bat
```

Correcto:

```bat
cd C:\xamppp
mysql_start.bat
```

---

### 2. Ejecutar rutas como comandos

Incorrecto:

```bat
C:\xamppp\mysql\data_old
```

Correcto:

```bat
cd C:\xamppp\mysql\data_old
```

---

### 3. Copiar toda la carpeta data_old

No copiar:

* mysql
* performance_schema
* phpmyadmin
* ibdata1

Solo copiar bases de datos propias.

---

### 4. Seleccionar opción incorrecta en xcopy

Siempre responder:

```
D
```

---

### 5. Configuración incorrecta en my.ini

Incorrecto:

```ini
innodb_force_recovery=1
innodb_force_recovery=2
```

Correcto:

```ini
innodb_force_recovery=1
```

---

## Recomendación

Después de recuperar el sistema, realizar un respaldo completo:

```bat
mysqldump -u root -p --all-databases > backup.sql
```

---

## Conclusión

Cuando MySQL no inicia y no hay conflictos de puertos ni procesos activos, el problema suele estar en la carpeta `data`.

La solución consiste en:

1. Renombrar la carpeta dañada
2. Restaurar una copia limpia
3. Recuperar manualmente las bases de datos necesarias

---
