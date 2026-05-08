Laboratorio: Persistencia en Bases de Datos
I. Introducción: ¿Por qué necesitamos Volúmenes?
Cuando empezamos a usar Docker, descubrimos algo muy importante: los contenedores tienen "mala memoria". Por defecto, un contenedor es temporal. Si creamos una base de datos, guardamos información en ella y luego borramos el contenedor por accidente (o para actualizarlo), toda nuestra información desaparece para siempre.

El objetivo de esta práctica es aprender a usar los Volúmenes de Docker. Un volumen es básicamente una "caja fuerte" que vive fuera del contenedor. Así, aunque nuestro contenedor se destruya, la caja fuerte y todo lo que hay adentro sobrevive intacto.
II. Desarrollo Paso a Paso
Paso 1: El problema (El contenedor que olvida todo)
Primero, comprobamos qué pasa cuando no usamos una caja fuerte. Creamos una base de datos normal (efímera) y comprobamos que al destruirla, perdemos todo.

Comandos que usamos:

Para crear el contenedor temporal:
docker run -d --name db-efimera -e MYSQL_ROOT_PASSWORD=123 mariadb

Para destruirlo y simular la pérdida de datos:
docker rm -f db-efimera

Paso 2: La Solución (Creando nuestra "caja fuerte")
Para proteger nuestros datos, le pedimos a Docker que cree un volumen (nuestra caja fuerte) y luego conectamos un nuevo contenedor de base de datos directamente a ella.

Comandos que usamos:

Creamos el volumen llamado data-segura:
docker volume create data-segura

Levantamos la base de datos y la conectamos al volumen:
docker run -d --name db-persistente -v data-segura:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123 mariadb

Paso 3: Comprobando que funciona
Para estar seguros de que hicimos todo bien, entramos a la base de datos, creamos información, destruimos el contenedor y vimos si los datos sobrevivían.

Comandos que usamos:

Entramos a la base de datos:
docker exec -it db-persistente mariadb -u root -p123

Creamos un dato de prueba (escribiendo esto dentro de MariaDB):
CREATE DATABASE mi_prueba_resiliencia;

Salimos de la base de datos escribiendo exit.

Destruimos el contenedor:
docker rm -f db-persistente

Lo volvimos a levantar usando nuestra caja fuerte (el mismo comando del Paso 2):
docker run -d --name db-persistente -v data-segura:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123 mariadb

Volvimos a entrar y escribimos SHOW DATABASES;. Y ahí estaba la información a salvo.

Paso 4: La Evidencia
Para terminar, le pedimos a Docker que nos mostrara los detalles internos de nuestra caja fuerte para confirmar dónde está guardando las cosas realmente en nuestra computadora.

Comando que usamos:
docker volume inspect data-segura

III. Conclusión
En esta práctica aprendimos que nunca debemos guardar información importante directamente dentro de un contenedor, porque si este se borra, perdemos todo. Gracias a los Volúmenes de Docker, podemos separar nuestros datos del programa que los usa. Es como tener un disco duro externo: no importa si cambias de computadora (o de contenedor), tu información siempre estará segura y lista para usarse.
<img width="1366" height="768" alt="Captura de pantalla (1400)" src="https://github.com/user-attachments/assets/9bc8c836-f3e0-48e5-928b-1440fcbbd8df" />

