1- El área working directory es como el area de trabajo, allí puedes observar los cambios que se producen en los ficheros, pero sin guardar nada.
El área de staging es como un buffer que te permite gestionar los cambios antes de guardarlos.
En el Local Repository se guarda una instantanea definitiva del estado del proyecto.

2-No, porque no ha pasado por la staging area, todavía no se ha registrado el cambio, aunque en local el archivo si haya cambiado.

3-Git solo gestiona archivos, una carpeta vacía no deja ningún rastro que git pueda registrar, para conservarla, podemos colocar un archivo placeholder dentro.

4-Es un puntero que apunta a la rama en la que estás trabajando, algo así como un marcapáginas.

5-Son equivalentes, pero switch está pensado específicamente para breanches, checkout también sirve para restaurar archivos.

6-Representa el contenido que entra en conflicto entre versiones al fusionar la nueva branch.

7-Porque el commit anterior desaparece y es reemplazado por uno nuevo, alguien podría haber descargado la versión antigua con ese commit ahora inexistente, haciendo que sea incompatible, se debe hacer un commit nuevo en su lugar, para así mantener la coherencia.

8-Se pierden los datos de configuración de Git, osea, el historial de versiones y las branches, pero no se pierden los archivos en disco. 

9-Git lleva el registro de versiones en local, si desarrollas un proyecto solo y los archivos no necesitan salir de tu ordenador es suficiente. GitHub es la plataforma web que permite compartir esos ficheros con otros y gestionar las versiones de forma común.

10-Porque incluso si borraras ese archivo, persistiría en el historial de versiones, a futuro, si el repositorio se filtrara, ese archivo podría ser accesible para cualquiera.

11-Probablemente se hayan introducido cambios nuevos que su máquina todavía no ha percibido, usaría git pull para asimilar los nuevos cambios para que el próximo commit no genere conflictos.

12-Actualizar una tabla: feat. Corregir una restricción: fix. Actualizar el README: docs.