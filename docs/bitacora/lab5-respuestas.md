Punto de partida y banco de medida
1. Demuestra que lab4-final incorpora cambios respecto a lab3-final, según la diferencia del paso 6 y su evidencia.
2. \(BFR = \text{filas} / \text{bloques}\). PEDIDO admite más filas por bloque por tener filas más pequeñas.
3. La primera ejecución calienta cachés y prepara el análisis; la segunda mide con menos coste de arranque.

Anatomía y tipos de índice 
4. La altura y el fan-out real se obtienen de la evidencia 02; para mil millones de filas, la altura crecería aproximadamente de forma logarítmica según el fan-out.
5. El ROWID contiene número de objeto relativo al archivo, bloque y posición de fila; no muestra directamente el número de archivo en su representación física.
6. En igualdad de condiciones, un índice selectivo suele reducir lecturas lógicas frente a un recorrido completo; las cifras exactas dependen de las tres pruebas elegidas.

Agrupamiento, equilibrio y sargabilidad 
7. FECHA agrupa físicamente filas relacionadas, mientras que CLIENTE las dispersa; un REBUILD no cambia necesariamente el orden físico de la tabla.
8. El umbral depende de las lecturas medidas en tu curva; compáralo con el coste estimado de acceder por índice frente a leer todos los bloques.
9. Gonz% permite una búsqueda por rango, pero si coincide con muchas filas, el acceso a numerosos bloques puede superar el coste de un recorrido completo.

Coste de escritura y mantenimiento 
10. Compara los bloques modificados con 4, 9 y 5 índices; rechaza los índices cuya mejora de lectura no compense el coste adicional de escritura.
11. El gráfico distingue divisiones equilibradas 50/50, divisiones sesgadas 90/10 y crecimiento por un extremo en el patrón cola.
12. Evaluaría un índice inverso o particionado, según las consultas; reduce la contención de inserciones concentradas en el extremo, pero debe validarse la compatibilidad.

SCN, control files, redo y FRA 
13. Oracle compara los SCN de los datafiles y del control file para determinar si hace falta recuperación.
14. La multiplexación en el mismo volumen protege frente a fallos de un archivo, no frente a la pérdida del volumen; en producción, usaría dispositivos y ubicaciones independientes.
15. CURRENT recibe redo, ACTIVE aún se necesita para recuperación y INACTIVE ya no es necesario; Oracle rechaza borrar manualmente los logs gestionados que aún requiere la instancia.
16. La FRA llena genera ORA-19815; al no poder liberar espacio ni archivar redo, se bloquea la reutilización de logs y pueden detenerse las operaciones de escritura.

Undo, WAL y simulacros 
17. Los lectores consultan versiones consistentes mediante undo, mientras los escritores modifican datos; ambos pueden avanzar sin bloquearse mutuamente en el caso normal.
18. DELETE conserva información para reconstruir las filas eliminadas; ROLLBACK también genera redo para que sus cambios sean recuperables tras un fallo.
19. Justo después del COMMIT, LGWR garantiza la durabilidad escribiendo redo; el checkpoint escribe bloques modificados en datafiles, pero no sustituye esa garantía.
20. El roll forward reaplica redo para recuperar los cambios, incluidos los de la fila 102; el roll back deshace transacciones no confirmadas, como la fila 103, según el alert log.
21. El RTO mide cuánto tardó la recuperación y el RPO cuántos datos se perdieron; los valores de los simulacros C y D deben calcularse con tus marcas de tiempo y datos recuperados.
22. Clasifica cada ORA del bloque 5 usando el contexto y el alert log: incidente real si revela un fallo operativo, efecto del laboratorio si es provocado y esperado, y ruido conocido solo si está documentado y no afecta al servicio.

PREGUNTAS TIPO EXAMEN
S1 - B. Un recorrido por rango del índice visitará casi un bloque distinto de la tabla por cada fila.

S2 - A. Crear un índice basado en función sobre UPPER(email) 
    C. Comparar email con LOWER(:e) si los datos se almacenan en minúsculas.

S3 - B. Oracle puede convertir la columna con TO_NUMBER, impidiendo el uso directo del índice sobre la columna sin convertir.

S4 - B. Una columna de estado con 4 valores en un almacén de datos con cargas nocturnas y consultas diurnas.

S5 - B. INDEX SKIP SCAN.

S6 - B. Marcar el índice como INVISIBLE y vigilar el rendimiento.

S7 - A. ALTER INDEX ... COALESCE 
    C. ALTER INDEX ... SHRINK SPACE.

S8 - B. Reparte las inserciones entre muchas hojas, pero impide usar el índice para rangos.

S9 - C. UPDATE de una columna que no está en ningún índice.

S10 - B. El factor de ramificación (entradas por bloque de rama), que depende del tamaño de la clave.

S11 - A. Las filas se guardan en el índice de la clave primaria 
    C. La búsqueda por clave primaria evita acceder a un segmento de tabla separado.

S12 - B. El SCN de checkpoint del control file con el de la cabecera del datafile.

S13 - C. CKPT.

S14 - B. SELECT status FROM v$instance;
        E.  SELECT name, value FROM v$parameter;

S15 - B. SHUTDOWN IMMEDIATE; STARTUP MOUNT; ALTER DATABASE ARCHIVELOG; ALTER DATABASE OPEN;.

S16 - B. ALTER DATABASE CLEAR LOGFILE GROUP n para recrearlo

S17 - A. Lectura consistente 
    C. Consultas Flashback (AS OF).

S18 - B. Porque los bloques de undo pueden perderse en una caída y hay que poder rehacerlos antes de deshacer.

S19 - A. El redo de la transacción está en el redo log online.

S20 - A. Los checkpoints incrementales son más frecuentes y la recuperación de instancia más rápida.

S21 - B. ARCn no puede archivar, por ejemplo, porque la FRA está llena.

S22 - B. ALTER DATABASE DATAFILE n OFFLINE, si se trata de un datafile recuperable de un tablespace de aplicación y la operación es admisible.

S23 - B. SHUTDOWN ABORT; STARTUP MOUNT; RESTORE DATAFILE; RECOVER DATAFILE; ALTER DATABASE OPEN;.

S24 - B. Solo se puede restaurar desde una copia en frío y se pierde lo posterior a ella.

S25 - B. Aumentar UNDO_RETENTION y el tamaño del undo; 
    D. RETENTION GUARANTEE si la consulta es crítica y se han evaluado sus riesgos.

S26 - A. V$RECOVERY_AREA_USAGE