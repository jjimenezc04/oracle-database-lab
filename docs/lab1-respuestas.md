1. ¿Cuál es la diferencia entre Working Directory, Staging Area y Local Repository? Da un ejemplo de un archivo pasando por las tres.
    Working directory nuestro escritorio en el que estamos trabajando, Staging area es donde se guardan los cambios que van a ser lanzados con el proximo add o send y Local respository es el historial de cambios lanzados.
    README.md pasa por los 3, primero esta en el working directory, se manda al staging area y al lanzarlo se almacena en el local repository

2. Si modificas un archivo pero no haces git add, ¿aparece ese cambio en tu próximo commit? Explica por qué.
    No porque el archivo no ha pasado por la Staging area asi que no aparecera en el commit.

3. ¿Por qué git status no mostraba las carpetas vacías que creaste en la Parte C? ¿Qué truco usamos para solucionarlo?
    Porque git solo muestra archivos, las carpetas al no tener ningun archivo, no aparecen. Se soluciona añadiendo un archivo vacio en cada carpeta .gitkeep.

4. Explica con tus palabras qué es HEAD.
    HEAD es un puntero que apunta a la rama que esta activa.

5. ¿Qué diferencia hay entre crear una branch con git switch -c y crear una carpeta nueva con mkdir? ¿Cómo lo comprobamos en la Parte G?
    En la primera opcion se crea una rama en git mientras que con mkdir se crea una carpeta.
    En la Parte G al crear la rama feature y hacer ls -la se no se muestra ningun archivo nuevo.

6. Durante el conflicto de la Parte H, ¿qué representaba el contenido entre <<<<<<< HEAD y =======? ¿Y entre ======= y >>>>>>>?
    Entre <<<<<<< HEAD y ======= estaba el contenido de nuestra version actual y entre ======= y >>>>>>> estaba el contenido de la otra version que Git intentaba fusionar.

7. ¿Por qué NO se debe hacer git commit --amend sobre un commit que ya se subió con git push?
    Porque amend reescribe el commit anterior y como ya se le ha hecho un push el commit modificado tiene un identificador diferente y el historial remoto ya no va a coincidir.

8. Si borras por accidente la carpeta .git de tu proyecto, ¿qué se pierde exactamente? ¿Se pierde también el código fuente que está en el disco?
    Se pierde el historial, commits y ramas pero el codigo fuente no se pierde ya que lo archivos del proyecto siguen en el disco.

9. Explica con tus propias palabras la diferencia entre Git y GitHub, sin usar la palabra "nube".
    Git es un sistema de control de versiones local que registra y gestiona cambios mientras que Github es un servicio donde se pueden almacenar los repositorios de Git.

10. ¿Por qué no se debe subir un archivo .env con contraseñas reales a un repositorio, aunque el repositorio sea privado?
    Porque cualquier usuario con acceso al repositorio puede entrar a dicho archivo y además aunque se borre o modifique el archivo, las contraseñas seguiran estando en el historial

11. Un compañero te dice: "hice push y ahora GitHub me rechaza el segundo push con 'non-fast-forward'". ¿Qué ha ocurrido probablemente y qué comando ejecutarías primero?
    Alguien hizo push de un nuevo commit a github después de que el compañero hiciera su último pull. El primer comando que ejecutaria seria git pull para añadir los cambios antes de volver a hacer push.

12. ¿Qué tipo de Conventional Commit (feat, fix, docs, test…) usarías para: añadir un índice de rendimiento a una tabla, corregir una restricción mal definida, y actualizar el README?
    Añadir indice de rendimiento: perf
    Corregir restriccion: fix
    Actualizar README: docs 
