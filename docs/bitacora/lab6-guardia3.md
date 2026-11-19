## INC-603 · Bloqueo de transacciones, servicio detenido y cuenta de aplicación bloqueada

- **Detección:** 18/11/2026, entre las 18:29:31 y las 18:29:35 · Detectado mediante consultas de monitorización de sesiones, bloqueos, ASH, servicios y estado de cuentas de Oracle. No consta quién notificó inicialmente la incidencia.

- **Impacto:** La usuaria ANA_GARCIA (SID 221), desde el módulo `secretaria`, ejecutando la acción `ficha-alumno`, no podía completar su operación sobre la tabla `ACA_ESTUDIANTES` debido a un bloqueo de fila. La espera quedó registrada durante al menos 5 segundos consecutivos en ASH. Además, el servicio `academia_app` estaba detenido y la cuenta `APP_ACADEMIA` se encontraba bloqueada, lo que comprometía la disponibilidad de las conexiones de la aplicación. No se dispone de datos para determinar la duración total ni el número de usuarios afectados.

- **Diagnóstico:**
  - **Bloqueo de sesiones:** La sesión 221, correspondiente a `ANA_GARCIA`, esperaba por el evento `enq: TX - row lock contention`, de clase `Application`. La sesión 51, perteneciente a `APP_ACADEMIA`, módulo `matriculas` y acción `cierre-periodo`, era la bloqueadora.
  - **Fila afectada:** Objeto `ACA_ESTUDIANTES`, archivo 1024, bloque 779 y fila 5.
  - **Bloqueos TX:** La sesión 51 mantenía el bloqueo TX exclusivo