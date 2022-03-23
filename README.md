# Taller Openshift
[![NPM](https://nodei.co/npm/discord.js.png?compact=true)](https://nodei.co/npm/discord.js/)
![ibm watson version](https://img.shields.io/badge/ibm--watson-6.0.3-blue)
![discord.js version](https://img.shields.io/badge/discord.js-12.5.3v-green)
![dotenv version](https://img.shields.io/badge/dotenv-6.2.0v-yellow)

## Resumen
Este tutorial está pensado para que te puedas familiarizar con las capacidades más interesantes de Redhat Openshift, mediante la realización de un par de ejemplos, donde desplegaras y gestionarás aplicaciones dentro de la plataforma de orquestación de contenedores.

## Tecnologias

- [Node.js](https://nodejs.org/en/docs/guides/getting-started-guide/)
- [Watson Assistant](https://cloud.ibm.com/docs/assistant?topic=assistant-getting-started)
- [Kubernetes](https://cloud.ibm.com/docs/containers?topic=containers-getting-started)
- [Openshift](https://cloud.ibm.com/docs/openshift?topic=openshift-getting-started)

Tiempo estimado: 30 a 45 minutos.

### Prerequisitos
- Contar con una cuenta de [IBM Cloud](https://cloud.ibm.com/). Consulta con el instructor la manera de acceder a la cuenta para poder realizar las prácticas.
- Disponer de un cluster de Openshift desplegado. Consulta con el instructor para saber cómo crear y/o acceder a tu cluster de Openshift.
- Tener desplegado un skill en [Watson Assistant](https://cloud.ibm.com/catalog/services/watson-assistant) con las intenciones, los diálogos y las entidades configuradas. 

## Pasos
0. Acceder al Cluster de Openshift
1. Creación de una aplicación a partir de una imagen Docker. 
2. Escala de la aplicación a través de replicas. 
3. Actualización de la aplicación
4. Rollback de la aplicación. 


## Acceder al Cluster de Openshift
1. Accede a tu cuenta IBM Cloud.
2. Desde la barra lateral dirigete a **Openshift > Clusters**
 <img width="1439" alt="Captura de pantalla 2022-03-23 a las 16 13 53" src="https://user-images.githubusercontent.com/102157561/159732439-aa400fba-ec93-4510-ad64-088bda57644d.png">
3. Selecciona el Cluster asignado y te redirigirá a una consola como la siguiente: 
<img width="1432" alt="Captura de pantalla 2022-03-23 a las 16 16 24" src="https://user-images.githubusercontent.com/102157561/159733000-1573a6d5-60f9-426f-87ee-20e996fcba4f.png">
4. Selecciona en la esquina superior derecha **Openshift Web Console**
5. En la esquina superior derecha pincha en **IAM#user_email** y posteriormente en **Copy Login Command**
<img width="1438" alt="Captura de pantalla 2022-03-23 a las 16 18 37" src="https://user-images.githubusercontent.com/102157561/159733503-01753d58-ad79-499d-9a92-fdc36febdfef.png">

Copia el comando que se muestra debajo de **Log in with this token**

6. En la consola de IBM Cloud, accede a IBM Cloud Shell desde la esquina superior derecha: 
<img width="1439" alt="Captura de pantalla 2022-03-23 a las 16 21 15" src="https://user-images.githubusercontent.com/102157561/159734050-85a9c484-b22e-45b0-ae8d-c7f6126ed8fb.png">
7. Ejecuta el comando copiado en el paso 5. 

## Cración de una aplicación a partir de una imagen Docker

1. En IBM Cloud Shell, crea un nuevo proyecto: 
`oc new-project guestbook-project`
<img width="752" alt="Captura de pantalla 2022-03-23 a las 16 44 18" src="https://user-images.githubusercontent.com/102157561/159739082-4f286bf3-a932-4f9d-99ea-a26c0df98cec.png">


2. Crea un nuevo despliegue haciendo uso de la imagen [ibmcom/guestbook:v1](https://hub.docker.com/r/ibmcom/guestbook/tags):  
`oc create deployment myguestbook --image=ibmcom/guestbook:v1`
<img width="680" alt="Captura de pantalla 2022-03-23 a las 16 44 36" src="https://user-images.githubusercontent.com/102157561/159739155-75c87dd1-c129-4d08-a0d7-af5804a555ef.png">

3. El despliegue crea el pod, se puede comprobar que el mismo ya se encuentra en estado "Running" a través del siguiente comando: 
`oc get pods`
<img width="498" alt="Captura de pantalla 2022-03-23 a las 16 44 52" src="https://user-images.githubusercontent.com/102157561/159739237-ba9dffc8-7a41-47cc-a8e2-2898906e8203.png">

4. Para exponer el despliegue, se hará uso del puerto 3000: `oc create deployment myguestbook --image=ibmcom/guestbook:v1`
<img width="688" alt="Captura de pantalla 2022-03-23 a las 16 45 28" src="https://user-images.githubusercontent.com/102157561/159739355-ebbfe874-cd38-4b6f-8b67-95a385217861.png">

5. Para ver el servicio que acabamos de exponer, utiliza el siguiente comando `oc get service`
<img width="577" alt="Captura de pantalla 2022-03-23 a las 16 46 01" src="https://user-images.githubusercontent.com/102157561/159739470-2f980b16-845c-4856-af2b-53eb6821f10e.png">
Se puede acceder al servicio dentro del pod a través del <NodeIP>:<NodePort>, pero en el caso de OpenShift en IBM Cloud, el NodeIP no es accesible de manera pública. 

 6. Para exponer el servicio de manera pública,es necesario crear un router público. Para ello, en la consola de Openshift Container Platform nos dirigimos a **Networking > Routes** desde la perspectiva de administrador, luego pulsamos **Create Route**

 7. Rellena los campos de la siguiente manera, considerando principalmente: 
 - **Name:** myguestbook
 - **Service:** myguestbook
 - **Target Port:** 3000→3000(TCP)
  
![image](https://user-images.githubusercontent.com/102157561/159722837-8ea13985-84a0-4b6c-96f0-3c6565773c51.png)

Una vez creada la ruta, nos redirigirá a nuestra Route Overview, desde ese apartado, es posible acceder a la ruta externa haciendo uso de la URL debajo de **Location**
  
![image](https://user-images.githubusercontent.com/102157561/159723214-a6bf40a9-7630-46bd-a44b-2a6ffacdc21c.png)

La URL debería rediriginirnos a una página similar a la siguiente
  
<img width="807" alt="Captura de pantalla 2022-03-23 a las 15 36 16" src="https://user-images.githubusercontent.com/102157561/159724371-14654c4b-6863-49d8-a21e-87adff6d127b.png">

## Escala la aplicación utilizando replicas
En esta sección, vamos a escalar la aplicación creando réplicas de los pod que creamos previamente. El tener múltiples replicas de un mismo pod, nos permite asegurar que el despliegue cuenta con los recursos disponibles para soportar un incremento de carga en la aplicación. 


 1. En IBM Cloud Shell, incrementamos la capacidad de una única instancia de Guestbook, a 5 instancias: 
 ` oc scale --replicas=5 deployment/myguestbook`
 <img width="556" alt="Captura de pantalla 2022-03-23 a las 16 46 29" src="https://user-images.githubusercontent.com/102157561/159739579-caa59490-491f-459f-88e4-e1456d80c346.png">


 2. Comprueba el estado del despliegue
 ` oc rollout status deployment myguestbook`
 <img width="735" alt="Captura de pantalla 2022-03-23 a las 16 46 48" src="https://user-images.githubusercontent.com/102157561/159739652-6ac1aec4-6fdb-4445-aa38-e9d2f93403e1.png">

 3. Verifica que ahora se tienen 5 pods en estado "Running" después de escalarlo. 
  `oc get pods -n guestbook-project`
<img width="497" alt="Captura de pantalla 2022-03-23 a las 16 47 15" src="https://user-images.githubusercontent.com/102157561/159739778-6b0000b6-e41c-47c4-8ffc-b999cfd213d2.png">
 
## Actualiza la aplicación
Con Openshift, es posible realizar una actualización continua de la aplicación a una nueva imagen. Con esta modalidad, es posible actualizar la imagen y deshacer los cambios si se encuentra un problema durante el despliegue. 
  
A continuación, actualizatemos la imagen con la tag v1, a una nueva versión v2. 
  
 1. En IBM Cloud Shell, actualizamos el despliegue utilizando la imagen v2 (dicha actualización también es conocida como Rollout):`oc set image deployment/myguestbook guestbook=ibmcom/guestbook:v2`
 <img width="710" alt="Captura de pantalla 2022-03-23 a las 16 47 42" src="https://user-images.githubusercontent.com/102157561/159739900-e4ac765d-369f-494f-84f4-71cba0485237.png">

 2. Comprueba el estado del rollout:`oc rollout status deployment/myguestbook`
<img width="529" alt="Captura de pantalla 2022-03-23 a las 16 48 07" src="https://user-images.githubusercontent.com/102157561/159739980-e8f273d1-4783-4128-abe2-aa38b1526bc6.png">

 3. Obtén una lista de los nuevos pods desplegados como parte del cambio de imagen
  `oc get pods -n guestbook-project`
<img width="520" alt="Captura de pantalla 2022-03-23 a las 16 48 19" src="https://user-images.githubusercontent.com/102157561/159740025-7656d630-bf64-44bb-8b71-b60006543680.png">

 4. Copia el nombre de uno de los pods y confirma que está utilizando la imagen v2 a través del siguiente comando:
`oc describe pod <pod-name>` 
 <img width="754" alt="Captura de pantalla 2022-03-23 a las 16 48 59" src="https://user-images.githubusercontent.com/102157561/159740167-309ce8df-784e-4bbf-9252-3db81bec6ced.png">

 5. Actualiza la URL para notar que el Pod está utilizando la imagen v2 de la aplicación Guestbook. En caso que continúe apareciendo la versión antigua, es necesario forzar la actualización de la página, en la mayoría de los navegadores se realiza con `ctrl+F5`
![image](https://user-images.githubusercontent.com/102157561/159728270-4d7a05f1-f10d-4a74-a4cc-227075c07755.png)

## Rollback de la aplicación
Cuando se realiza una actualización continua, es posible ver referencias a las antiguas replicas y a las nuevas. En el proyecto, las antiguas replicas son los 5 pods originales desplegados en la sección 2. Las replicas nuevas son las que se han creado con la nueva imagen. 
  
Todos los pods pertenecen al despliegue, quien se encarga de gestionarlos a través de un recurso llamado ReplicaSet. 

1. Para ver las ReplicaSet que tenemos, utiliza el siguiente comando: 
` oc  get replicasets -l app=myguestbook`
 <img width="493" alt="Captura de pantalla 2022-03-23 a las 16 49 28" src="https://user-images.githubusercontent.com/102157561/159740261-79de67cf-87ad-451e-8b03-d653da898a42.png">

2. Haz un rollback con el siguiente comando: 
` oc rollout undo deployment/myguestbook`
<img width="502" alt="Captura de pantalla 2022-03-23 a las 16 49 52" src="https://user-images.githubusercontent.com/102157561/159740331-b258404c-50ea-41e7-86dc-7080de2320af.png">

 3. Obtén el estado del despliegue para ver los nuevo pods creados como parte de deshacer el rollout.
` oc rollout status deployment/myguestbook`
<img width="510" alt="Captura de pantalla 2022-03-23 a las 16 50 28" src="https://user-images.githubusercontent.com/102157561/159740465-af015592-26ad-42a7-b28c-60a3d86bd64f.png">

 4. Obtén una lista de los nuevos pods creados como parte de deshacer el rollout: 
`oc get pods`
<img width="507" alt="Captura de pantalla 2022-03-23 a las 16 50 42" src="https://user-images.githubusercontent.com/102157561/159740524-7976eb7c-bd3e-44d7-bff9-b7dbac469932.png">

 5. Copia el nombre de uno de los pods y comprueba la versión de la imagen que utiliza: 
 ` oc describe pod <pod-name>`
<img width="774" alt="Captura de pantalla 2022-03-23 a las 16 51 04" src="https://user-images.githubusercontent.com/102157561/159740605-0b4dc815-53fe-44fa-98ba-5f88b19a6b8c.png">

 6. Después de deshacer el rollout, comprueba las ReplicaSets para notar que el replicaSet antiguo está ahora activo y gestiona 5 pods:
 ` oc get replicasets -l app=myguestbook`
<img width="514" alt="Captura de pantalla 2022-03-23 a las 16 51 21" src="https://user-images.githubusercontent.com/102157561/159740697-036918c0-2371-47e8-8953-017ab410e374.png">

 7. Actualiza la URL para notar que el Pod está utilizando la imagen v2 de la aplicación Guestbook. En caso que continúe apareciendo la versión antigua, es necesario forzar la actualización de la página, en la mayoría de los navegadores se realiza con `ctrl+F5`
![image](https://user-images.githubusercontent.com/102157561/159731051-3df3b7c7-00d4-4e38-a6f1-c0e822ca1d38.png)
  
## Resumen
Ahora sabemos cómo desplegar, escalar, actualizar y deshacer actualizaciones (rollback) aplicaciones en Openshift. Con este conocimiento es posible desplegar aplicaciones a partir de imágenes Docker, escalarlas para una determinada carga de trabajo, actualizarlas a nuevas versiones y volver a antiguas versiones en caso de bugs o problemas sobre las nuevas versiones. 

## Taller 1 - Crea, escala y actualiza tu primera app en Openshift

En este primer taller, veremos cómo gestionar un ciclo de vida básico de una aplicación en Openshift.
Aprenderás a crear y desplegar una aplicación a partir de una imagen Docker, escalarla, atualizar a una versión más moderna y finalmente, hacer rollback a una versión anterior.

Este ejemplo está basado en el tutorial que puedes encontrar aquí [Create, scale, upgrade, and rollback an application on Red Hat OpenShift](https://developer.ibm.com/tutorials/create-scale-upgrade-and-rollback-an-application-in-red-hat-openshift/)

### 1.1 Accede a la IBM Cloud Shell y logéate en tu cluster de Openshift

Consulta con el instructor los pasos a seguir para acceder a través de linea de comando a tu cluster de Openshift

### 1.2 Crea la aplicación a partir de una imagen Docker

1. Desde la IBM Cloud Shell, crea un nuevo proyecto utilizando el siguiente comando:

```
oc new-project guestbook-project
```

2. Crea un nuevo recurso Deplyment utilizando la [imagen Docker](https://hub.docker.com/r/ibmcom/guestbook/tags) que almacenamos en el registro público de imágenes de Docker. 

```
oc create deployment myguestbook --image=ibmcom/guestbook:v1
```
Este despliegue crea un pod y lo pone en ejecución. Con el siguiente comando podrás ver la lista de pods en tu namespace (o proyecto en terminología Openshift).

```
oc get pods
```
3. Expón el deployment en el puerto 3000. Esto creará un recurso de tipo Service que nos dará acceso al pod.

```
oc expose deployment myguestbook --type="NodePort" --port=3000
```

Para ver el servicio que acabas de exponer, utiliza el siguiente comando:

```
oc get service
```


Note that you can access the service inside the pod using the <Node IP>:<NodePort>, but in the case of OpenShift on IBM Cloud, the NodeIP is not publicly accessible. You can use the built-in Kubernetes terminal available in IBM Cloud, which spawns a Kubernetes shell that is part of the OpenShift cluster network; you can access NodeID:NodePort from that shell.

To make the exposed service publicly accessible, you need to create a public router. First, go to Networking > Routes from the Administrator Perspective on the web console, and then click Create Route.

Fill in the information as follows and click Create (you can leave all but the following fields empty):

Name: myguestbook
Service: myguestbook
Target Port: 3000→3000(TCP)

create route

Once you create the route, you are redirected to the Route Overview, where you can access the external route from the URL under Location as shown in the following screenshot. You have created the Route successfully, and you have a publicly accessible endpoint URL that you can use to access your guestbook application.

Access the external route

If you click on the URL, you are redirected to a page that looks like the following:

guestbook v1 app

Now that you have successfully deployed the application using the existing Docker image, you are ready to scale and rollback your application.

## Taller 2 - Despliega una aplicación en Openshift e intégrala con un asistente virtual

En este segundo taller, vamos a desplegar una aplicación usando la interface gráfica de Openshift en IBM Cloud. Desde ahí crearemos nuestra aplicación de chat en Openshift, que integraremos con un asistente virtual desplegado también en IBM Cloud.
Para ello usaremos un recurso de Kubernetes llamado ConfigMap que nos permitirá configurar las variables de entorno que usará el pod desplegado. 

### 2.1 Accede a la consola de Openshift

Desde la consola web de IBM Cloud, acceder a tu cluster.
Una vez dentro accede a la consola de Openshift

<p align="center">
  <img src="images/accesoopenshift.png" width="75%"></img>
</p>

### 2.2 Crea un nuevo proyecto

Dentro de la perspectiva de Administrador, selecciona "Proyectos" y haz click en Crear 

<p align="center">
  <img src="images/crearproyecto.png" width="75%"></img>
</p>

Con esto, ya tenemos nuestro namespace (proyecto en terminología Openshift) listo para albergar nuestras aplicaciones.

### 2.3 Crea la aplicación a partir de una imagen Docker

Ahora crearemos la aplicación a partir de una imagen Docker que tenemos preparada previamente en nuestro Dockerhub.
Esta aplicación está preparada para conectarse a un servicio de Watson Assistant e interactuar con él mediante una interface web, en la que también se podrá visualizar la información en formato json que devuelve en asistente en cada interacción.
Lo primero que haremos será crear una nueva aplicación a partir de una imagen. Para ello, desde la perspectiva de Desarrollador, selecciona crear nueva aplicación y elige Imagen de Contenedor de entre todas las diferentes opciones que ofrece Openshift.

<p align="center">
  <img src="images/crearappimage.png" width="75%"></img>
</p>

Esto nos lleva a un formulario de creación de aplicaciones donde tendremos que indicar la imagen de contenedor correspondiente.
No hace falta indicar nada más. Openshift creará por defecto todos los recursos necesarios (pods, replicaset, deployment, servcicios) para que la aplicación se ponga en marcha.


<p align="center">
  <img src="images/crearapp.png" width="75%"></img>
</p>

### 2.4 Revisar estado del pod

Cuando Openshift intente arrancar la apliación, obtendrá un error de acceso a Watson Assistant debido a que las credenciales no están configuradas todavía. Puedes ver el estado del pod y los logs accediendo a la vista de Topología y desde ahí a la aplicación.

<p align="center">
  <img src="images/podlogerror.png" width="75%"></img>
</p>

Si entramos en los logs, veremos que efectívamente, 


```
apiVersion: v1
kind: ConfigMap
metadata:
  name: watson-bot-cm
  namespace: watson-bot
data:
  CONVERSATION_IAM_APIKEY: <your_apikey>
  CONVERSATION_URL: <your_url>
  WORKSPACE_ID: <your_workspace>

```




 cómo gestionar un ciclo de vida básico de una aplicación en Openshift.
Aprenderás a crear y desplegar una aplicación a partir de una imagen Docker, escalarla, atualizar a una versión más moderna y finalmente, hacer rollback a una versión anterior.

Este ejemplo está basado en el tutorial que puedes encontrar aquí [Create, scale, upgrade, and rollback an application on Red Hat OpenShift](https://developer.ibm.com/tutorials/create-scale-upgrade-and-rollback-an-application-in-red-hat-openshift/)

### 1.1 Accede a la IBM Cloud Shell y logéate en tu cluster de Openshift

Consulta con el instructor los pasos a seguir para acceder a través de linea de comando a tu cluster de Openshift

## Flow

<p align="center">
  <img src="images/flow-diagram.jpg" width="70%"></img>
</p>

-	El usuario interactúa con el bot en la plataforma Discord.
-	El bot utiliza la aplicación Node.js para recibir los comandos.
- La aplicación se conteineriza y despliega como un pod en un cluster de Kubernetes
-	Dentro de la app Node.js se realizan las llamadas a la APIs de los servicios de Watson
-	Las respuestas de las APIs son recibidas y acomodadas por la app.
-	El bot entrega las respuestas al usuario en Discord.

Nota.- A diferencia de como viene en el diagrama, al final del presente lab llevaremos también nuestra aplicación Node a IBM Cloud, desplegándola en un cluster de Kubernetes 

## Instrucciones y Pasos

### 1. Registra un nuevo Bot en Discord Developers.

Para crear nuestro bot primero debemos registrar un nuevo bot en el sitio de Discord developers, esto permitirá generar un bot con nombre, descripción e imagen que represente lo que queremos crear, además de darle una entidad dentro de los servidores de Discord, para esto:

Primero, debes dirigirte a [Discord Developer Portal](https://discord.com/developers/applications) e iniciar sesión en el sitio, dentro de este deberías ver una opción para "New Application", haz clic en él como se aprecia en la imagen de abajo recalcado en el círculo rojo.

<p align="center">
  <img src="images/crear-nueva-app.jpg" width="75%"></img>
</p>

Dale el nombre que quieras y haz clic en "Create".

<p align="center">
  <img src="images/dale-nombre-ycrea.jpg" width="75%"></img>
</p>

Ahora deberías estar dentro de los settings de tu aplicación, en nuestro caso, el uso de esta aplicación será el marco de nuestro bot, por lo tanto, podemos dar una descripción de lo que hará nuestro bot dentro de la descripción de la aplicación, luego para continuar, haz clic dentro de la opción "Bot".

<p align="center">
  <img src="images/opcion-bot.jpg" width="75%"></img>
</p>

Haz click en "Add Bot".

<p align="center">
  <img src="images/anadir-bot.jpg" width="75%"></img>
</p>

Ahora ya cuentas con un bot registrado, dale el nombre que quieras (puede ser el mismo nombre que el de la aplicación, la verdad no hay mucha diferencia), coloca el icono que quieras y guarda los cambios. Para pasos posteriores, es **importante que guardes tu token personal del bot**, este token es el que va a permitir controlar y manejar tu bot, es confidencial solo para ti.

<p align="center">
  <img src="images/token-bot.jpg" width="75%"></img>
</p>

------------------------------------------------------------------------------------------------------------

### 2. Añade el Bot a un servidor.

Aún nuestro bot no hace nada, pero pronto lo hará, por ahora ya que tenemos registrado el bot podemos añadirlo a un servidor para comenzar a darle características, puedes agregarlo al servidor que tú quieras. Recuerda que deberás crear un servidor usando el interface de Discord. 
Una vez creado dicho servidor podrás agregar el bot, para ello, debemos hacer lo siguiente:

Visita la [Calculadora de Permisos de Discord](https://discordapi.com/permissions.html), dentro de ellas seleccionaremos todos los permisos, no te preocupes por temas de seguridad y privacidad, nuestro bot solo realizará respuestas a consultas, no tendrá facultades para realizar más acciones que solo responder a textos (aunque él lo quiera).

<p align="center">
  <img src="images/permisos.jpg" width="75%"></img>
</p>

Luego, vuelve al sitio de los settings de tu aplicación en el Discord Developer Portal, dirígete a "General Information" y copia tu "APPLICATION ID"

<p align="center">
  <img src="images/cliente-id.jpg" width="75%"></img>
</p>

Ahora pega este ID dentro de la Calculadora de Permisos de Discord.

<p align="center">
  <img src="images/cliente.jpg" width="75%"></img>
</p>

Hecho esto, ya tienes listo tu enlace propio (y enlace para compartir) para permitir que tu bot entre a tu servidor y al que tú quieras, ingresa al enlace, selecciona el servidor que quieras, y dale a "Continuar"

<p align="center">
  <img src="images/ingresa-servidor.jpg" width="75%"></img>
</p>

A continuación, acepta los permisos otorgados al bot haciendo clic en "Autorizar" (Recuerda que el bot solo puede responder comandos que ya están programados, y esto solo contempla llamadas a APIs, por lo tanto, no puede comprometer la privacidad ni seguridad de ningún servidor)

<p align="center">
  <img src="images/autorizar-bot.jpg" width="75%"></img>
</p>

Si hiciste todo lo anterior, felicidades, ¡ya tienes tu bot (vacío aún) dentro de tu servidor!

<p align="center">
  <img src="images/autorizado.jpg" width="75%"></img>
</p>

Puedes revisar este video que recopila todo el proceso: [Code Your Own Discord Bot](https://www.youtube.com/watch?v=j_sD9udZnCk) créditos al canal de Codelyon en Youtube.

------------------------------------------------------------------------------------------------------------

### 3. Configura el bot con Node.js

Ahora vamos a configurar nuestro bot para que funcione como un chatbot inteligente, para esto debes hacer lo siguiente:

Clona este repositorio en una consola de comandos o bash:

```
git clone https://github.com/luisreyesoliva/watson-discord.git
```
Entra en la carpeta del proyecto:

```
cd watson-discord 
```
Ya dentro de esta carpeta encontramos todo lo necesario para dar vida al bot, tenemos el archivo "main.js" desde el cual se orquesta las funciones del bot, todo escrito en Node.js, tenemos también la carpeta "commands" en los cuales encontramos los comandos base e iniciales como "ping.js" y los de Watson como "lang_translator.js", "nlu_analyzer_cat.js", "nlu_analyzer_con.js", "tone_analyzer.js" y "watson_assistant.js".

Además hemos preparado el Dockerfile que permitirá construir una imagen con todo lo necesario para ejecutar nuestro bot, ¡Sin tener que programar nada!

Las funciones inteligentes (traducción, NLU, etc.) ya están entrenadas en IBM Cloud, solo debes darle acceso a ellas a través de los servicios que ya tienes creados como se detallan en los **Prerequisitos** de este code pattern.

Para conectar con estos servicios tendrás que especificar las credenciales correspondientes y los endpoints de cada servicio. Para ello:
1. Abre y modifica el archivo ".env_sample" el cual es un template de todos los accesos que necesita el bot.
2. Copia todas las API Keys y Service Url de tus recursos de Watson y pegalos
3. En el caso de Watson Assistant, recuerda debes tener creado y configurado un asistente previamente, con sus intenciones, entidades y diálogos. Para configurar las conexiones al servicio selecciona el asistente adecuado y elige la opción settings del menú desplegable, donde tendrás acceso a: 
- API Key
- Assistant URL
- Assistant ID
4. Por último, tendrás que copiar y pegar el token de tu bot que se muestra en el paso **1. Registra un nuevo Bot en Discord Developers**, si te queda alguna duda de cómo conseguir estos accesos, ingresa a la documentación en **Tecnologías**.

<p align="center">
  <img src="images/foto-env-sample.jpg" width="75%"></img>
</p>

A continuación, renombra el archivo anterior a solo ".env".

Teniendo todo lo anterior listo, podemos proceder a construir la imagen completa de los recursos de nuestro bot con Docker, como ves, no hay nada de programación hasta ahora, y tampoco habrá, ya que los comandos ya están programados anteriormente, de los cuales te contaré más después de ejecutar el bot.

Para construir la imagen del bot, en la raíz del proyecto clonado ejecutaremos lo siguiente.
Tagea el nombre de la imagen con el formato 'repositorio/imagen:versión' donde 'repositorio' es un repositorio válido de Dockerhub donde subiremos la imagen:

```
docker build -t <NOMBRE-IMAGEN-BOT> .
```

Esperamos su construcción, y lo subimos al Dockerhub. 
Si no tienes cuenta en Docker, ahora es buen momento para crearala. Accede a https://hub.docker.com/para registrarte y crear un repositorio donde almacenar tus imágenes. 

```
docker push <NOMBRE-IMAGEN-BOT>
```
Ya tenemos nuestra imagen creada con todo lo que necesita el bot para ejecutarse. Ahora lo probaremos antes de desplegarlo en Kubernetes.

### 4. Testeando el bot

Prueba a ejecutar el contenedor en tu localhost para testear que todo está correcto.

```
docker run <NOMBRE-IMAGEN-BOT>
```
Si todo salió bien, deberías ver que tu bot se encuentra en línea, viendo esto en tu consola o bash:

```
Watson AI Bot is online!
```

Si ves algo distinto a esto, procura revisar si los accesos copiados al archivo ".env" son los correctos para tus servicios de Watson.

Ahora, con nuestro bot en línea, deberíamos ver al bot en estado de conectado en el servidor de Discord, como se ve en esta imagen.

<p align="center">
  <img src="images/bot-conectado.jpg" width="85%"></img>
</p>

Llegando a esta parte, el bot se encuentra completamente listo para ser usado, prueba escribiéndole el comando "!help" comenzado con el prejifo "!".

<p align="center">
  <img src="images/diferentes-comandos.jpg" width="65%"></img>
</p>

¡Prueba a utilizar alguno de esos comandos! 
- El comando **!mood** permite conocer alguna emoción o sentimiento encontrado en una oración o párrafo
- El comando **!related** te dará una buena idea sobre de que trata un texto de información o sitio web, sin tener que leerlo por completo
- El comando **!translate** permitirá traducir tu español, portugués o chino al inglés para así no toparse con la barrera del idioma
- El comando **!wa** te permitirá establecer un diálogo inteligente orquestado por Watson Assistant
- Existen otros comandos divertidos a probar, prueba con revisar la carpeta de "commands" del proyecto y en el programa "main.js" para encontrar los otros comandos... (pista: prueba con "gato" en inglés), en la carpeta "images" encontrarás el logo de Watson si es lo que deseas poner en tu bot.

Estas son algunas imagenes capturadas desde Discord, ¡Disfruta tu bot!

<img src="images/comando-uno.jpg" width="47%"></img>
<img src="images/comando-dos.jpg" width="43%"></img>
<p align="center"> <img src="images/comando-tres.jpg" width="50%"></img> </p>

Realizadas las pruebas, procede a desactivar el bot. Pare ello tendrás que parar el contenedor que se está ejecutando en tu máquina.

Abre un terminal y ejecuta el comando Docker ps para ver la información del contenedor en ejecución.

```
docker ps
```
Copia el identificador y úsalo para parar el contendor con este comando:

```
docker stop <ID_CONTENEDOR>
```

Si ahora intentas interacturar con tu bot veras que ovbiamente se ha desconectado y no responderá a los comandos que envíes desde Discord. 

### 4. Desplegando la aplicación en Kubernetes

Ahora vamos a desplegar la aplicación en Kubernetes para que nuestro bot esté permanente activo en Discord y no se nos caiga, además podemos escalarlo en caso de que la demanda de interacciones crezca mucho. 

Para ello debemos contar previamente con un cluster de Kubernetes ya aprovisionado en IBM Cloud.

Primero configuraremos el acceso, para ello:

- Logate en [IBM Cloud](https://cloud.ibm.com) para acceder al Dasshboardo.
- Haz click en el icono de terminal (arriba a la derecha) para lanzar **IBM Cloud Shell**.

```text
ibmcloud ks clusters
```
- Configura la cli de `kubectl` para que acceda a tu cluster (indica el nombre de tu cluster).

```text
ibmcloud ks cluster config --cluster <tu cluster>
```

- Verifica que tienes accesso al API de Kubernetes.

```text
kubectl get namespace
```

Debería obtener todos los namespaces activos si todo ha ido bien.

```text
NAME              STATUS   AGE
default           Active   125m
ibm-cert-store    Active   121m
ibm-system        Active   124m
kube-node-lease   Active   125m
kube-public       Active   125m
kube-system       Active   125m
```

Ahora crearemos los recursos necesarios en K8s para que nuestra aplicación se ejecute y con ello arranque el bot en Discord. 

Para ello modifica el archivo "deployment.yaml" para indicar el mombre de la imagen que creaste en el paso anterior y que ya deberías tener subida al registro de Docker. 
Sustituye en el campo 'image' <NOMBRE-IMAGEN-BOT> por el nombre de la imagen que has creado.

```
image: <NOMBRE-IMAGEN-BOT>
```

Ya estamos casi! solo te queda crear el deployment y el servicio en K8s.

- Crea el deployment `watson-discord` 

```text
kubectl create -f deployment.yaml
```

- Y por último crea el servicio `watson-discord` para exponer nuestro pod hacia fuera del cluster.

```text
kubectl create -f service.yaml
```

Si todo salió bien, deberías ver que tu bot se encuentra de nuevo en estado de conectado en el servidor de Discord, como se ve en esta imagen.

<p align="center">
  <img src="images/bot-conectado.jpg" width="85%"></img>
</p>

Ahora nuesto bot se encuentra completamente listo para ser usado.

Prueba tus dialogos y disfruta de tu nuevo bot! 


## Conclusiones

Mediante este proyecto pudiste conocer tecnologías que ofrece Discord para los desarrolladores, Node.js para el back-end de una aplicación, un pequeño puñado de las capacidades inteligentes que se ofrecen en IBM Cloud/Watson y lo sencillo de construir una imagen de una aplicación a través de Docker, todo esto registrando, integrando y desplegando un bot en la plataforma de Discord, el cual, puedes personalizarlo y mejorarlo para mostrar a clientes y colaboradores.

------------------------------------------------------------------------------------------------------------

## Contenido Relacionado

- [Natural Language Understanding](https://cloud.ibm.com/docs/natural-language-understanding) y [API](https://cloud.ibm.com/apidocs/natural-language-understanding)
- [Language Translator](https://cloud.ibm.com/docs/language-translator) y [API](https://cloud.ibm.com/apidocs/language-translator)
- [Tone Analyzer](https://cloud.ibm.com/docs/tone-analyzer) y [API](https://cloud.ibm.com/apidocs/tone-analyzer)
- [Documentación de Desarrolladores de Discord](https://discord.com/developers/docs/intro)
- [Como usar una REST API](https://discordjs.guide/additional-info/rest-api.html#making-http-requests-with-node)
- [Docs de APIs de servicios de Watson](https://cloud.ibm.com/docs?tab=api-docs)
- [Proyecto en inglés: Chatbot para WhatsApp](https://developer.ibm.com/events/update-your-chatbot-on-whatsapp-with-ibm-watson-assistant/)
- [Servicio de Watson Assistant](https://developer.ibm.com/learningpaths/get-started-watson-assistant/)

## Licencia

Este Code Pattern se encuentra licenciado bajo Apache License, Version 2. Objetos de código de terceros invocados en dentro de este Code Pattern se encuentran licenciados bajo sus respectivos proveedores en conformidad con los términos de sus correspondientes licencias. Todas las contribuciones se encuentran sujetas al [Developer Certificate of Origin, Version 1.1](https://developercertificate.org/) y la [Apache License, Version 2](https://www.apache.org/licenses/LICENSE-2.0.txt).

[Preguntas frecuentes sobre Apache License](https://www.apache.org/foundation/license-faq.html#WhatDoesItMEAN)
