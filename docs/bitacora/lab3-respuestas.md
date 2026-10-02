Docker
1. ¿Qué diferencia hay entre una imagen y un contenedor? Usa como ejemplo lo que hiciste en los ejercicios
G2 y G4.
Una imagen es una plantilla inmutable con el software; un contenedor es una instancia en ejecución de esa imagen
2. En el Ejercicio G5 el archivo nota.txt desapareció y en el G6 no. Explica por qué.
En G5, nota.txt estaba dentro del sistema de archivos efímero del contenedor, por eso desapareció al eliminarlo. En G6 se usó un volumen, que almacena los datos fuera del ciclo de vida del contenedor
3. ¿Qué diferencia hay entre docker ps y docker ps -a, y qué significa STATUS = Exited (0)?
docker ps muestra solo contenedores en ejecución; docker ps -a muestra también los detenidos. Exited (0) significa que el proceso principal terminó correctamente
4. En -p 8181:8181, ¿qué número corresponde a tu equipo y cuál al contenedor? ¿Qué pasaría con -p 80:8080
en el ejercicio de nginx?
En -p 8181:8181, el primer 8181 es el puerto del equipo (host) y el segundo el puerto del contenedor. Con -p 80:8080, accederías al puerto 80 del equipo y Docker lo dirigiría al 8080 del contenedor nginx
5. ¿Por qué un contenedor de Oracle se queda en marcha y el de hello-world termina solo?
Oracle necesita mantener procesos de base de datos activos, por eso el contenedor permanece ejecutándose, hello-world solo ejecuta un programa que muestra un mensaje y termina; al terminar su proceso principal, termina el contenedor
6. ¿Qué es el digest de una imagen y por qué lo registramos si ya sabemos que usamos :latest?
Es el identificador criptográfico del contenido exacto de una imagen
7. ¿Qué comando borraría realmente los datos de Oracle? ¿Por qué docker rm oralab-26ai no lo hace?
docker rm -v oralab-26ai, docker rm oralab-26ai elimina solo el contenedor; el volumen persiste

Git, organización y evidencia
8. ¿Por qué este laboratorio se hace dentro del repositorio oracle-database-lab, con Issue, branch y Pull
Request, en vez de en una carpeta aparte?
Trabajar dentro de oracle-database-lab permite controlar cambios con Git, relacionarlos con una Issue, aislarlos en una branch, revisar el trabajo mediante un Pull Request y conservar un historial reproducible
9. ¿Qué diferencia hay entre source 00-config.sh y bash 00-config.sh? ¿Por qué usamos source?
source 00-config.sh ejecuta el script en la shell actual, por lo que sus export quedan disponibles después. bash 00-config.sh lo ejecuta en una shell hija y sus variables no permanecen en la shell actual. Usamos source para cargar la configuración
10. Explica cada parte del nombre 20260915T091230Z02-docker.script.log.
20260915: fecha, 15/09/2026.
T: separador fecha/hora.
091230: 09:12:30.
Z: hora UTC.
02-docker: ejercicio/script.
script.log: evidencia de ejecución en formato log.
11. ¿Para qué sirve .gitattributes y qué error evita?
.gitattributes define cómo Git debe tratar determinados archivos, por ejemplo los finales de línea. Evita problemas de CRLF/LF, especialmente al trabajar entre Windows, WSL y Linux
12. ¿Por qué en este Pull Request elegimos Create a merge commit en lugar de Squash and merge?
Create a merge commit conserva explícitamente la historia de la branch y el momento de integración mediante un commit de merge. Squash and merge comprime varios commits en uno, perdiendo esa estructura detallada de la historia

Seguridad
13. Describe las cuatro capas de la estrategia de contraseñas (Parte D) y qué pasaría si te saltas la primera.

14. ¿Por qué no escribimos la contraseña directamente en el comando docker run, aunque el script no se suba
a Git?
Porque la contraseña podría quedar expuesta en el historial de comandos, procesos, logs o capturas, aunque el script no se suba a Git.
15. Si descubres tu contraseña en un commit ya publicado, ¿basta con borrarla en un commit nuevo? ¿Qué
debes hacer?
No. Hay que cambiar/revocar la contraseña y eliminar el secreto del historial de Git si procede. Borrarlo en un commit nuevo no elimina el secreto de los commits anteriores.

Oracle y herramientas
16. ¿Por qué no usamos SPOOL ni @archivo.sql con sqlplus dentro del contenedor, y qué hicimos en su lugar?
Para separar la ejecución y la evidencia del SQL del contenedor. En su lugar, enviamos el archivo por stdin con docker exec -i y capturamos la salida con tee.
17. ¿Qué hace WHENEVER SQLERROR EXIT SQL.SQLCODE al inicio de V000 y V001, y qué pasaría sin esa
línea?
Hace que SQLPlus termine con error si una sentencia SQL falla. Sin ella, podría continuar ejecutando las siguientes sentencias y ocultar un fallo de la migración
18. ¿Qué es una migración y por qué V000 y V001 no se deben editar una vez aplicadas?
Una migración es un cambio versionado aplicado a la base de datos. V000 y V001 no se editan después de aplicarlas porque se perdería la trazabilidad y reproducibilidad; los cambios nuevos deben ir en otra migración
19. ¿Por qué en SQL Developer se usa el servicio FREEPDB1 y no FREE ni un SID?
Porque FREEPDB1 es el servicio de la PDB donde trabajamos. FREE corresponde al servicio de la base de datos contenedora y un SID identifica una instancia, no el servicio concreto de la PDB
20. ¿Qué aporta SQLcl frente a SQLPlus, y por qué un DBA debe dominar ambas?
SQLcl aporta una CLI más moderna y funcionalidades adicionales sobre SQLPlus. Un DBA debe dominar ambas porque SQLPlus sigue siendo habitual en scripts y administración, mientras SQLcl facilita tareas modernas y automatización.

Entorno de trabajo
21. ¿Por qué el curso pasa de Git Bash a Ubuntu en WSL 2? Da al menos dos problemas concretos de Git Bash
que desaparecen en Ubuntu.
Porque Ubuntu en WSL 2 ofrece un entorno Linux más real y compatible. Desaparecen problemas como rutas/permisos de Windows y diferencias de finales de línea CRLF/LF que pueden afectar a los scripts.
22. ¿Por qué clonamos el repositorio en ~/oracle-database-lab y no trabajamos sobre la carpeta de Windows
(/mnt/c/...)? ¿Y por qué recomendamos bash frente a zsh para los scripts del curso?


