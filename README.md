**Ejemplos JenkinsFile Multibranch**
=====================

**Este repositorio tiene como función documentar el uso de multibranch desde JenkinsFile.**

El siguiente ejemplo de Jenkinsfile tiene como stages la construcción de un contenedor docker y luego el despliegue desde una receta docker-compose.
La construcción y despliegue dependerán de la rama de la cual provenga el push que gatilló la ejecución del pipeline.

Si el push proviene de la rama develop, se creará la imagen correspondiente y se desplegará sobre un ambiente de desarrollo
Si el push proviene de la rama master, se creará la imagen correspondiente y se desplegará sobre un ambiente productivo.



Conexión ssh a las instancias
============================

En el stage  "Deploy" , es necesario conectarse a las instancias a través de ssh para realizar el " docker-compose up " 
Utilizando gcloud es posible ejecutar comandos sobre desde una instancia sin necesidad de crear una clave ssh de manera manual, ya que la conexión ssh se hace de manera automatica si esque se tienen los permisos necesarios. 
El comando es el siguiente:


        gcloud compute ssh <nombreInstancia> --zone us-central1-a --command "<comando1> && <comando2> && <comando3>"