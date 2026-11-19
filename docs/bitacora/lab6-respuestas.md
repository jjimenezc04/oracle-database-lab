Partes A-C · Rol operativo, parámetros y estados
1. El alert log registró 3 errores ORA- en las últimas 24 horas. Ese número no es una alarma por sí solo porque hay que analizar la gravedad, frecuencia e impacto de cada error.

2. Durante la ventana de mantenimiento  ella se ejecutan tareas automáticas de estadísticas, mantenimiento y optimización de la base de datos.

3. Tras SCOPE=SPFILE, V$SPPARAMETER mostraba el nuevo valor persistente, mientras que V$PARAMETER seguía mostrando el valor activo. Sin RESET, el valor antiguo habría seguido guardado y se habría aplicado en el siguiente reinicio.

4. RESET era correcto para db_files porque eliminaba una modificación que no debía mantenerse en el SPFILE. Para processes no habría sido correcto porque debía conservarse el valor establecido en el PFILE.

5. El error mostraba «Database opened» porque la base de datos estaba abierta, aunque eso no garantizaba que el usuario tuviera los permisos necesarios.

Partes D-E · Servicios, sesiones y diccionario

6. MODULE, ACTION y CLIENT_IDENTIFIER identifican el componente, la operación y el usuario final, proporcionando más contexto que USERNAME.

7. Tras CANCEL SQL había 3 transacciones abiertas y tras KILL SESSION. La fila 101 quedó en estado abierto porque cancelar la sentencia no equivale necesariamente a revertir toda la transacción.

8. El valor de common_user_prefix establece el prefijo obligatorio para los nombres de usuarios comunes, normalmente C##.

9. La diferencia entre ALL_TABLES, DBA_USERS, CDB_USERS y V$DATAFILE se debe a que cada vista ofrece información desde un ámbito distinto: usuario, base de datos, contenedor o archivos físicos.

10. La relación entre DB time y DB CPU permite conocer cuánto tiempo se dedicó realmente a CPU frente al tiempo total de actividad de las sesiones.

Partes F-G · Espacio y acceso

11. TBS_ACADEMIA_HIST utiliza el espacio libre respecto al máximo para establecer alertas de capacidad y anticipar el agotamiento del tablespace.

12. Ana García realizó la inserción y soportó el error ORA-01950 por falta de cuota. Ampliar el tablespace no habría solucionado el ORA-01536 porque este error está relacionado con el límite de cuota del usuario.

13. Oracle dejó constancia del tiempo suspendido en el alert log mediante un mensaje de suspensión y otro de reanudación de la operación.

14. DELETE no devolvió automáticamente el espacio porque liberó espacio interno de los bloques, pero no necesariamente redujo los segmentos.

15. En PRF_PERSONAS justificaría FAILED_LOGIN_ATTEMPTS para proteger la cuenta y IDLE_TIME para evitar sesiones abandonadas.

16. Ana pudo leer ACA_MATRICULAS porque recibió privilegios mediante un rol. Al intentar cambiar el nombre de un alumno obtuvo el error por no disponer del privilegio de modificación correspondiente.

Partes H-I · Objetos, diagnóstico y guardia

17. La columna nueva no invalidó objetos porque no modificó las columnas existentes ni sus dependencias.

18. Según los advisors, duplicar la SGA mejoraría el DB time. 

20. La evidencia aparece en la línea de la traza o TKPROF identificada como consistent gets.
- Incidente 1: Evidencia: bloqueo TX entre SID 51 y SID 221; intervención: finalizar la sesión bloqueadora; validación: comprobar que desaparece la espera y funciona ficha-alumno.
- Incidente 2: Evidencia: servicio academia_app detenido; intervención: arrancar el servicio; validación: comprobar que aparece iniciado y permite conexiones.
- Incidente 3: Evidencia: cuenta APP_ACADEMIA en estado LOCKED; intervención: desbloquear la cuenta; validación: comprobar estado OPEN y conexión correcta.