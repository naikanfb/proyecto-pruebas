# Simulación y Recuperación de Error en Git

## 1. Error Simulado

Se simuló el borrado accidental del archivo `errores.sh`, un script Bash con funciones importantes para el proyecto.

### Pasos del error:
1. Se creó el archivo `errores.sh` con contenido funcional
2. Se realizó commit del archivo
3. Se eliminó accidentalmente el archivo con `rm errores.sh`
4. Se realizó commit del borrado (simulando que no nos dimos cuenta inmediatamente)

## 2. Detección del Problema

El error se detectó al intentar ejecutar el script y descubrir que no existía en el sistema de archivos.
```bash
$ ls errores.sh
ls: cannot access 'errores.sh': No such file or directory
```

## 3. Comandos Utilizados para la Recuperación

### 3.1 Investigación del problema

Primero se verificó el historial de commits:
```bash
git log --oneline
```

Salida:
```
a1b2c3d error: borrado accidental de errores.sh
d4e5f6g feat: añadir script errores.sh con funciones de prueba
...
```

### 3.2 Consulta del reflog

Para ver el historial completo de cambios, incluyendo resets y checkouts:
```bash
git reflog
```

Esto mostró todos los estados por los que pasó el repositorio, permitiendo identificar el commit donde el archivo existía.

### 3.3 Recuperación del archivo

Se utilizó `git checkout` para recuperar el archivo del commit anterior al borrado:
```bash
git checkout HEAD~1 -- errores.sh
```

**Explicación del comando:**
- `git checkout`: Comando para cambiar entre ramas o restaurar archivos
- `HEAD~1`: Referencia al commit anterior al actual
- `--`: Separador que indica que lo siguiente es una ruta de archivo
- `errores.sh`: Archivo específico a recuperar

### 3.4 Verificación de la recuperación
```bash
ls -la errores.sh
cat errores.sh
```

El archivo fue recuperado exitosamente con todo su contenido original.

### 3.5 Commit de la recuperación
```bash
git add errores.sh
git commit -m "fix: recuperar errores.sh eliminado accidentalmente"
```

## 4. Comandos Alternativos Explorados

### Opción A: git reset (más drástico)

Si el error se detecta inmediatamente y no se ha compartido el commit:
```bash
git reset --hard HEAD~1
```

**Ventaja:** Elimina completamente el commit del borrado del historial.  
**Desventaja:** Puede causar problemas si ya se hizo push.

### Opción B: git revert (más seguro para repositorios compartidos)
```bash
git revert HEAD
```

**Ventaja:** Crea un nuevo commit que deshace los cambios, preservando el historial.  
**Desventaja:** El historial muestra el borrado y la recuperación (más verboso).

### Opción C: git checkout con hash específico
```bash
git checkout a1b2c3d -- errores.sh
```

Permite recuperar el archivo de cualquier commit específico del historial.

## 5. Aprendizajes Clave

### 5.1 Importancia del historial

Git mantiene un historial completo de todos los cambios, lo que permite recuperar prácticamente cualquier estado anterior del repositorio.

### 5.2 Herramientas de recuperación

- **git log:** Muestra commits recientes con sus mensajes
- **git reflog:** Muestra TODOS los cambios, incluso resets y checkouts
- **git checkout:** Permite restaurar archivos de commits anteriores
- **git reset:** Mueve el HEAD a un commit anterior (cuidado con --hard)
- **git revert:** Crea un commit nuevo que deshace cambios previos


## 6. Conclusión

Esta experiencia demostró que Git es una herramienta extremadamente robusta para recuperación de errores. Incluso después de borrar un archivo y hacer commit del borrado, el contenido permanece en el historial de Git y puede ser recuperado fácilmente.

La clave es conocer las herramientas disponibles (`git log`, `git reflog`, `git checkout`, `git reset`, `git revert`) y saber cuándo usar cada una según el contexto.

**Lección más importante:** Git casi nunca pierde datos permanentemente. Mientras exista en el historial, se puede recuperar.
