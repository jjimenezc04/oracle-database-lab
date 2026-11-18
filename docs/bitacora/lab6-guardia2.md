## INC-602 · Bloqueo de filas y servicio de aplicación detenido

- **Detección:** 18/11/2026, entre las 18:28:54 y las 18:28:58 · Detectado mediante las consultas de monitorización de sesiones, bloqueos, ASH y servicios de Oracle. No consta quién notificó inicialmente la incidencia.

- **Impacto:** La usuaria ANA_GARCIA (SID 221), desde el módulo `secretaria`, ejecutando la acción `ficha-alumno`, quedó bloqueada al intentar operar sobre la tabla `ACA_ESTUDIANTES`. La espera se prolongó durante al menos 5 segundos consecutivos según ASH. Además, el servicio `academia_app` no estaba arrancado, lo que podía impedir las conexiones de los consumidores que dependieran de dicho servicio. No se dispone de información para determinar la duración total ni el número de usuarios afectados.

- **Diagnóstico:**
  - La sesión 221 (`ANA_GARCIA`) esperaba por el evento `enq: TX - row lock contention`, de tipo Application.
  - La sesión 51 (`APP_ACADEMIA`), correspondiente al módulo `matriculas` y acción `cierre-periodo`, era la bloqueadora.
  - La fila afectada pertenecía al objeto `ACA_ESTUDIANTES`, archivo 1024, bloque 779 y fila 5.
  - La consulta de bloqueos confirmó que la sesión 51 mantenía el bloqueo TX en modo exclusivo (`LMODE=6`, `BLOCK=1`), mientras que la sesión 221 lo solicitaba (`REQUEST=6`).
  - El proceso bloqueador tenía 1 bloque y 1 registro de UNDO utilizados (`USED_UBLK=1`, `USED_UREC=1`), con inicio de transacción a las 18:28:03.
  - ASH registró la espera de la sesión 221 entre las 18:28:54 y las 18:28:58, siempre con la sesión 51 como bloqueadora.
  - La consulta de servicios mostró que `FREEPDB1` estaba arrancado, pero `academia_app` aparecía como `NO`, indicando que el servicio de aplicación no estaba arrancado.

- **Causa raíz:** Se identifican dos anomalías:
  1. El proceso `cierre-periodo`, perteneciente al módulo `matriculas` de `APP_ACADEMIA`, mantenía una transacción abierta con un bloqueo exclusivo sobre una fila de `ACA_ESTUDIANTES`, impidiendo que la sesión de secretaría pudiera completar su operación.
  2. El servicio `academia_app` no estaba arrancado, aunque existía en la configuración de la base de datos, por lo que no estaba disponible para los consumidores que necesitaban conectarse a través de él.

- **Intervención:** 
  1. Para resolver el bloqueo de filas, tras confirmar que la transacción podía cancelarse, finalizar la sesión bloqueadora mediante:

     `ALTER SYSTEM KILL SESSION '51,3654' IMMEDIATE;`

     Esta sentencia actúa únicamente sobre la sesión identificada como bloqueadora y libera sus bloqueos una vez completada la reversión de la transacción.

  2. Para recuperar el servicio de aplicación, arrancarlo desde SQL*Plus con privilegios administrativos:

     `EXEC DBMS_SERVICE.START_SERVICE('academia_app');`

     Esta operación permite volver a ofrecer el servicio a los consumidores que dependen de él, sin necesidad de reiniciar toda la instancia.

- **Validación:**
  1. Consultar nuevamente las sesiones y comprobar que la sesión 221 ya no espera por `enq: TX - row lock contention` y que la sesión 51 ha dejado de bloquearla.
  2. Repetir la operación `ficha-alumno` desde el módulo `secretaria` y comprobar que finaliza correctamente.
  3. Consultar los servicios y verificar que tanto `FREEPDB1` como `academia_app` aparecen arrancados (`SI`).
  4. Comprobar que los consumidores pueden conectarse utilizando el servicio `academia_app`.

  No se aportan evidencias posteriores que confirmen la ejecución de estas intervenciones ni su validación.

- **Acción preventiva:** 
  - Implementar alertas para detectar bloqueos TX prolongados, identificando automáticamente la sesión bloqueadora, el usuario, el módulo, la acción y la fila afectada.
  - Revisar el procedimiento `cierre-periodo` para reducir el tiempo durante el cual mantiene transacciones abiertas y bloqueos exclusivos.
  - Configurar una comprobación automática del estado de los servicios Oracle, especialmente `academia_app`, y generar alertas cuando un servicio configurado no esté arrancado.
  - Incorporar al procedimiento de recuperación el arranque controlado de servicios y la validación de conexiones desde las aplicaciones consumidoras.