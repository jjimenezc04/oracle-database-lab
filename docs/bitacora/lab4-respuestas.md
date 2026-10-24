PREGUNTAS TIPO EXAMEN
P1
A. SHUTDOWN IMMEDIATE cierra la base de datos de forma consistente. Si se copian todos los datafiles, control files y redo logs después del cierre completo, no se necesita recuperación de instancia al restaurarlos.
P2
B. Es la secuencia correcta
P3
A.V$INSTANCE.STARTUPTIME indica la fecha y hora del último arranque de la instancia; cambia cada vez que se reinicia.
C.V$DATABASE.CREATED indica cuándo se creó la base de datos, no cuándo se arrancó la instancia.
P4
A. V$INSTANCE.STARTUPTIME indica la fecha y hora del último arranque de la instancia; cambia cada vez que se reinicia.
C. V$DATABASE.CREATED indica cuándo se creó la base de datos, no cuándo se arrancó la instancia.
P5
B. Para ejecutar ALTER DATABASE ARCHIVELOG, la base de datos debe estar montada, pero no abierta.
P6
B. El cambio se aplica en memoria y en el SPFILE.
P7
C. PROCESSES es un parámetro de instancia, no un parámetro configurable individualmente para FREEPDB1.
P8
A. Permite arrancar la instancia usando un PFILE válido, evitando el SPFILE defectuoso.
C. Permite recrear el SPFILE a partir de un PFILE válido para corregir el problema en futuros arranques.
P9
B. ASMM gestiona la SGA y el buffer cache nunca bajará de 512 MB.
P10
A. El area de ordenación (sort area) es memoria privada de la PGA utilizada para operaciones como ORDER BY.
D. En una conexión con servidor dedicado, la información de sesión (session memory) reside en la PGA del proceso servidor.
P11
C. LGWR (Log Writer) escribe los registros de redo del redo log buffer al archivo de redo log online. Para que COMMIT devuelva el control al usuario, LGWR debe confirmar la escritura del registro de commit en el redo log.
P12
B. PMON: supervisa procesos y realiza tareas de limpieza cuando falla un proceso.
D. SMON: realiza la recuperación de la instancia tras un fallo y otras tareas de mantenimiento.

RESPALDO
1. La copia de la Parte B es consistente porque se realiza con la base de datos detenida, evitando cambios durante la copia. 
2. manifiesto.txt documenta el contenido de la copia y SHA256SUMS permite verificar su integridad. restaurar-estado.sh comprueba los archivos, sus hashes y las condiciones previas antes de modificar el entorno.
3. RPO es la cantidad máxima de datos que se acepta perder; RTO es el tiempo máximo aceptable para recuperar el servicio. En tu copia, el RPO depende del momento de la copia y el RTO del tiempo que tardaste en restaurarla.
DIAGNOSTICO Y CORRECCIONES
4. Oracle creó los datafiles en la ubicación predeterminada configurada para la base de datos, porque V000 no especificaba otra ruta. El parámetro DBCREATEFILEDEST habría permitido definir la ubicación prevista.
5. Actualiza la referencia al archivo en Oracle, pero no mueve físicamente el archivo en el sistema operativo. Hay que moverlo y actualizar correctamente la referencia.
6. No se edita V000 porque una migración aplicada debe permanecer inmutable y reproducible; los cambios nuevos se implementan en otra migración. Invalidar una contraseña impide seguir utilizándola, mientras que borrarla del archivo no elimina las copias que puedan existir en el historial de Git.
7. La prueba demuestra que los datos persisten después de reiniciar o recrear el contenedor. Se ejecuta tras reubicar los archivos para comprobar que la nueva ubicación conserva los datos y que Oracle puede utilizarlos.
INSTANCIA Y ARRANQUE
9. ORA-01507 ocurre al consultar V$DATABASE antes de montar la base; ORA-01219 aparece al consultar objetos del diccionario antes de abrirla.
10. FREEPDB1 se abre automáticamente porque su estado de apertura quedó guardado mediante SAVE STATE. Si no lo hiciera, ejecutaría ALTER PLUGGABLE DATABASE FREEPDB1 OPEN y, si procede, ALTER PLUGGABLE DATABASE FREEPDB1 SAVE STATE.
11. La clasificación exacta de ORA-00800 y ORA-20013 depende del mensaje completo del alert log y del contexto. ORA-00800 suele indicar una recomendación o condición relacionada con la configuración; ORA-20013 puede proceder de código de aplicación o de un proceso que genera errores propios. No conviene clasificarlos definitivamente como ruido sin revisar el texto y comprobar si afectan al funcionamiento.
PARAMETROS
12. V$PARAMETER muestra el valor efectivo actual del parámetro; V$SPPARAMETER muestra el valor almacenado en el SPFILE para futuros arranques. En el caso de DBFILES, pueden diferir si el cambio se aplicó solo en memoria o quedó pendiente para el reinicio.
13. SCOPE=BOTH aplica el cambio tanto en memoria como en el SPFILE. Si se usa un SPFILE y se omite SCOPE, el valor predeterminado normalmente es BOTH.
14. PROCESSES requiere reiniciar la instancia para aplicar el cambio. Aunque V$PARAMETER indique IMMEDIATE, existen restricciones específicas del parámetro que impiden modificarlo dinámicamente.
15. Versionar el PFILE permite documentar y revisar la configuración de Oracle. Configuration drift es la diferencia que aparece entre la configuración documentada o esperada y la configuración real del sistema.
MEMORIA Y PROCESOS
16. Hay que comprobar la configuración real de tu instancia. Si utiliza gestión automática de memoria, parámetros como MEMORYTARGET, SGATARGET y PGAAGGREGATETARGET permiten justificarlo; si MEMORYTARGET es cero, podría estar usando gestión automática de la SGA y la PGA por separado.
17. Las lecturas lógicas son bloques atendidos desde la caché; las físicas son bloques que hubo que leer desde almacenamiento. La tasa de acierto mide qué proporción de las lecturas se resolvió sin lectura física; para calcularla se necesitan las cifras de tu experimento.
18. El RSS de los procesos puede incluir memoria compartida, como la SGA, contabilizada en varios procesos. Sumarlo puede duplicar la memoria compartida y sobreestimar el consumo real.
19. El SPID de LGWR en V$PROCESS identifica el proceso del sistema operativo dentro del entorno donde se ejecuta Oracle. /proc permite consultar ese proceso desde ese mismo entorno. docker top puede mostrar un PID distinto porque presenta la identificación del proceso desde el host, no necesariamente el PID interno del contenedor.
20. PMON: limpia recursos de procesos fallidos y recupera recursos asociados; por ejemplo, libera recursos tras la desconexión anómala de una sesión.
SMON: realiza tareas de recuperación de instancia y mantenimiento; por ejemplo, aplica recuperación tras una caída cuando es necesario.
SEGURIDAD Y TRABAJO PROFESIONAL
21. Se utiliza porque es una cuenta de observación con permisos limitados, siguiendo el principio de mínimo privilegio. A diferencia de SYSTEM, no debería poder administrar la base, modificar esquemas ni cambiar parámetros; sus acciones dependen de los privilegios que se le hayan concedido.
22. El laboratorio corrige dos vías de exposición: contraseñas escritas directamente en comandos o capturadas en logs, y secretos incluidos en archivos versionados o en el historial de Git. Se utilizan variables de entorno y archivos locales excluidos del repositorio, y se evita registrar los valores secretos en evidencias.
