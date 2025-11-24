# **Bash Backup Project**

Este script de Bash es una herramienta de respaldo automatizada diseñada para identificar, comprimir y archivar **únicamente los archivos que han sido modificados o creados en las últimas 24 horas** dentro de un directorio específico.

Es ideal para realizar copias de seguridad diarias de trabajos en curso sin duplicar archivos antiguos que no han cambiado.

## **🚀 Funcionalidades Clave**

* **Filtrado por Tiempo:** Calcula la fecha actual y selecciona solo los archivos con una fecha de modificación posterior a hace 24 horas (currentTS \- 24h).  
* **Validación de Entradas:** Verifica que se pasen exactamente dos argumentos y que ambos sean directorios válidos antes de ejecutarse.  
* **Nombres Únicos:** Genera archivos de respaldo con formato backup-\[TIMESTAMP\].tar.gz (usando tiempo Unix) para evitar sobrescrituras.  
* **Manejo de Rutas:** Gestiona rutas absolutas y relativas internamente para asegurar que el archivo comprimido se mueva al destino correcto sin importar desde dónde se ejecute el script.

## **📋 Requisitos**

* Entorno Unix/Linux/macOS.  
* Intérprete **Bash**.  
* Herramienta de compresión **tar**.

## **💻 Uso Manual**

El script requiere dos argumentos obligatorios: el directorio de origen (qué revisar) y el directorio de destino (dónde guardar el backup).

./backup.sh \<directorio\_origen\> \<directorio\_destino\>

### **Ejemplo**

Supongamos que tienes una carpeta proyectos y quieres guardar los cambios de hoy en backups:

./backup.sh ./proyectos ./backups

## **🤖 Automatización (CronJob)**

Para que este script sea realmente útil, puedes configurarlo para que se ejecute automáticamente cada 24 horas usando cron.

1. Mueve el script a una carpeta del sistema (opcional pero recomendado):  
   sudo cp backup.sh /usr/local/bin/

2. Abre el editor de tareas programadas:  
   crontab \-e

3. Añade la siguiente línea al final del archivo para ejecutar el backup **todos los días a medianoche**:  
   0 0 \* \* \* /usr/local/bin/backup.sh /ruta/a/mis\_datos /ruta/a/mis\_backups

*Nota: Asegúrate de usar rutas absolutas (ej: /home/usuario/docs) tanto para el script como para las carpetas en el comando de cron.*

## **⚙️ Explicación Técnica del Código**

El script realiza los siguientes pasos lógicos:

1. **Validación:** Comprueba si NumArgs \== 2 y si los directorios existen (-d).  
2. **Marcas de Tiempo:** Obtiene el tiempo actual en formato Unix (date \+%s) y calcula el umbral de 24 horas atrás.  
3. **Resolución de Rutas:** Guarda la ruta absoluta del destino para poder mover el archivo final correctamente después de cambiar de directorio con cd.  
4. **Bucle de Selección:**  
   * Entra al directorio objetivo.  
   * Itera sobre cada archivo (for file in \*).  
   * Compara la fecha de modificación del archivo con la variable $yesterdayTS.  
   * Si es reciente, lo añade al array toBackup.  
5. **Compresión:** Ejecuta tar solo con los archivos listados en el array toBackup.

## **⚠️ Notas Importantes**

* Si ningún archivo ha sido modificado en las últimas 24 horas, el comando tar podría generar un error o crear un archivo vacío (dependiendo de la versión de tar), ya que el array de archivos estará vacío.  
* El script no es recursivo por defecto en la selección (el bucle for file in \* revisa archivos en la raíz del directorio objetivo, pero tar sí incluirá carpetas si estas fueron modificadas recientemente).
