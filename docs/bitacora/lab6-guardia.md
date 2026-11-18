## INC-601 · Bloqueo de filas en ACA_ESTUDIANTES durante el cierre de periodo

- **Detección:** 18:28:17–18:28:21 del 18/11/2026 · Detectado mediante las consultas de monitorización de sesiones y ASH. No consta quién comunicó inicialmente la incidencia.

- **Impacto:** La usuaria ANA_GARCIA (SID 221), desde el módulo `secretaria` y ejecutando la acción `ficha-alumno`, no podía continuar con la operación sobre la tabla `ACA_ESTUDIANTES` debido a un bloqueo de fila. El bloqueo estuvo registrado en ASH durante al menos 5 segundos consecutivos. No se dispone de información para determinar la duración total.

- **Diagnóstico:** Las consultas de sesiones y bloqueos identificaron:
  - SID 221, usuario `ANA_GARCIA`, esperando por el evento `enq: TX - row lock contention`.
  - SID 51, usuario `APP_ACADEMIA`, módulo `matriculas`, acción `cierre-periodo`, como sesión bloqueadora.
  - La fila afectada pertenecía al objeto `ACA_ESTUDIANTES`, archivo 1024, bloque 779 y fila 5.
  - La consulta de bloqueos confirmó que la sesión 51 tenía el bloqueo TX en modo 6 (`LMODE=6`, `BLOCK=1`), mientras que la sesión 221 solicitaba ese mismo bloqueo (`REQUEST=6`).
  - La sesión bloqueadora tenía únicamente 1 bloque de UNDO y 1 registro de UNDO utilizado (`USED_UBLK=1`, `USED_UREC=1`).

- **Causa raíz:** La transacción del proceso `cierre-periodo`, ejecutado por el módulo `matriculas` de `APP_ACADEMIA`, mantenía un bloqueo exclusivo sobre una fila de `ACA_ESTUDIANTES` sin haber finalizado o confirmado la transacción. Esto impedía que la sesión de secretaría pudiera modificar o acceder a esa misma fila mediante una operación incompatible.

- **Intervención:** Finalizar la transacción bloqueadora, identificada como SID 51 y SERIAL# 3654, mediante la sentencia:

  `ALTER SYSTEM KILL SESSION '51,3654' IMMEDIATE;`

  Esta intervención actúa exclusivamente sobre la sesión bloqueadora identificada, evitando finalizar otras sesiones o reiniciar la instancia completa. Su alcance es reducido, aunque provoca la reversión de la transacción pendiente.

- **Validación:** Comprobar mediante consultas de sesiones y bloqueos que la sesión 221 deja de presentar el evento `enq: TX - row lock contention` y que ya no existe una sesión bloqueadora asociada. Finalmente, repetir la operación `ficha-alumno` desde el módulo consumidor de secretaría y verificar que finaliza correctamente. No se aporta evidencia de esta validación posterior.

- **Acción preventiva:** Incorporar una monitorización de bloqueos TX prolongados que identifique automáticamente la sesión bloqueadora, la sesión afectada, el objeto y la fila implicada. Revisar el procedimiento `cierre-periodo` para garantizar que las transacciones confirmen o reviertan sus cambios en un tiempo razonable y no mantengan bloqueos innecesariamente. Añadir un procedimiento de gestión de bloqueos con umbrales de alerta y registro de evidencias.