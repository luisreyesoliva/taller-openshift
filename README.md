# Taller Openshift
![ibm watson version](https://img.shields.io/badge/ibm--watson-6.0.3-blue)
![openshift version](https://img.shields.io/badge/openshift--container--platform-4.8.31-red)
![dotenv version](https://img.shields.io/badge/dotenv-6.2.0v-yellow)

## Resumen
Este tutorial está pensado para que te puedas familiarizar con las capacidades más interesantes de Redhat Openshift, mediante la realización de un par de ejemplos, donde desplegarás y gestionarás aplicaciones dentro de la plataforma de orquestación de contenedores.

## Tecnologías

- [Kubernetes](https://cloud.ibm.com/docs/containers?topic=containers-getting-started)
- [Openshift](https://cloud.ibm.com/docs/openshift?topic=openshift-getting-started)
- [Watson Assistant](https://cloud.ibm.com/docs/assistant?topic=assistant-getting-started)
- [Node.js](https://nodejs.org/en/docs/guides/getting-started-guide/)


Tiempo estimado: 30 a 45 minutos.

## Prerequisitos
- Contar con una cuenta de [IBM Cloud](https://cloud.ibm.com/). 
- Disponer de un cluster de Openshift desplegado.
- Tener desplegado un skill en [Watson Assistant](https://cloud.ibm.com/catalog/services/watson-assistant) con las intenciones, los diálogos y las entidades configuradas. 

## Índice
- Taller 1 - [Crea, escala y actualiza tu primera app en Openshift](https://github.com/luisreyesoliva/taller-openshift#taller-1---crea-escala-y-actualiza-tu-primera-app-en-openshift)
- Taller 2 - [Despliega una aplicación en Openshift e intégrala con un asistente virtual](https://github.com/luisreyesoliva/taller-openshift#taller-2---despliega-una-aplicaci%C3%B3n-en-openshift-e-int%C3%A9grala-con-un-asistente-virtual)

## Taller 1 - Crea, escala y actualiza tu primera app en Openshift

En este primer taller, veremos cómo gestionar un ciclo de vida básico de una aplicación en Openshift.
Aprenderás a crear y desplegar una aplicación a partir de una imagen Docker, escalarla, actualizar a una versión más moderna y finalmente, hacer rollback a una versión anterior.

Este ejemplo está basado en el tutorial que puedes encontrar aquí [Create, scale, upgrade, and rollback an application on Red Hat OpenShift](https://developer.ibm.com/tutorials/create-scale-upgrade-and-rollback-an-application-in-red-hat-openshift/)

### 1.1 Accede al Cluster de Openshift
1. Accede a tu cuenta IBM Cloud.

2. Desde la barra lateral dirigete a **Openshift > Clusters**
 <img width="1439" alt="Captura de pantalla 2022-03-23 a las 16 13 53" src="https://user-images.githubusercontent.com/102157561/159732439-aa400fba-ec93-4510-ad64-088bda57644d.png">

3. Selecciona el Cluster asignado y te redirigirá a una consola como la siguiente: 
<img width="1432" alt="Captura de pantalla 2022-03-23 a las 16 16 24" src="https://user-images.githubusercontent.com/102157561/159733000-1573a6d5-60f9-426f-87ee-20e996fcba4f.png">

4. Selecciona en la esquina superior derecha **Openshift Web Console** (*)

5. En la esquina superior derecha pincha en **IAM#user_email** y posteriormente en **Copy Login Command**
<img width="1438" alt="Captura de pantalla 2022-03-23 a las 16 18 37" src="https://user-images.githubusercontent.com/102157561/159733503-01753d58-ad79-499d-9a92-fdc36febdfef.png">

Copia el comando que se muestra debajo de **Log in with this token**

6. En la consola de IBM Cloud, accede a IBM Cloud Shell desde la esquina superior derecha: 
<img width="1439" alt="Captura de pantalla 2022-03-23 a las 16 21 15" src="https://user-images.githubusercontent.com/102157561/159734050-85a9c484-b22e-45b0-ae8d-c7f6126ed8fb.png">

7. Ejecuta el comando copiado en el paso 5. 

(*) **En caso de que no se abriera la consola de Openshift, podría ser un problema con el acceso al puerto, verifica que el puerto esté abierto y permita la salida a internet**
### 1.2 Crea una aplicación a partir de una imagen Docker

1. En IBM Cloud Shell, crea un nuevo proyecto: 

```
oc new-project guestbook-project
```

<img width="752" alt="Captura de pantalla 2022-03-23 a las 16 44 18" src="https://user-images.githubusercontent.com/102157561/159739082-4f286bf3-a932-4f9d-99ea-a26c0df98cec.png">


2. Crea un nuevo despliegue haciendo uso de la imagen [ibmcom/guestbook:v1](https://hub.docker.com/r/ibmcom/guestbook/tags): 

```
oc create deployment myguestbook --image=ibmcom/guestbook:v1
```

<img width="680" alt="Captura de pantalla 2022-03-23 a las 16 44 36" src="https://user-images.githubusercontent.com/102157561/159739155-75c87dd1-c129-4d08-a0d7-af5804a555ef.png">

3. El despliegue crea el pod, se puede comprobar que el mismo ya se encuentra en estado "Running" a través del siguiente comando: 

```
oc get pods
```

<img width="498" alt="Captura de pantalla 2022-03-23 a las 16 44 52" src="https://user-images.githubusercontent.com/102157561/159739237-ba9dffc8-7a41-47cc-a8e2-2898906e8203.png">

4. Para exponer el despliegue, se crear un Servicio, que expondrá el puerto 3000 del contenedor en un puerto del nodo (NodePort): 

```
oc expose deployment myguestbook --type="NodePort" --port=3000
```

<img width="688" alt="Captura de pantalla 2022-03-23 a las 16 45 28" src="https://user-images.githubusercontent.com/102157561/159739355-ebbfe874-cd38-4b6f-8b67-95a385217861.png">

5. Para ver el servicio que acabamos de exponer, utiliza el siguiente comando:
 
```
oc get service
```

<img width="577" alt="Captura de pantalla 2022-03-23 a las 16 46 01" src="https://user-images.githubusercontent.com/102157561/159739470-2f980b16-845c-4856-af2b-53eb6821f10e.png">

Se podría acceder al servicio usando <NodeIP:NodePort>, pero en el caso de OpenShift en IBM Cloud, el NodeIP no es accesible de manera pública. 

 6. Para exponer el servicio de manera pública, es necesario crear una ruta pública. Para ello:
 - En la consola de Openshift Container Platform, desde la perspectiva de Administrador, accedemos al proyecto que nos acabamos de crear desde linea de comando **Home > Projects** 
 - Una vez en el proyecto, entramos en **Networking > Routes**, luego pulsamos **Create Route**

 7. Rellena los campos de la siguiente manera, considerando principalmente: 
 - **Name:** myguestbook
 - **Service:** myguestbook
 - **Target Port:** 3000→3000(TCP)
  
![image](https://user-images.githubusercontent.com/102157561/159722837-8ea13985-84a0-4b6c-96f0-3c6565773c51.png)

Una vez creada la ruta, nos redirigirá a nuestra Route Overview, desde ese apartado, es posible acceder a la ruta externa haciendo uso de la URL debajo de **Location**
  
![image](https://user-images.githubusercontent.com/102157561/159723214-a6bf40a9-7630-46bd-a44b-2a6ffacdc21c.png)

La URL debería rediriginirnos a una página similar a la siguiente
  
<img width="807" alt="Captura de pantalla 2022-03-23 a las 15 36 16" src="https://user-images.githubusercontent.com/102157561/159724371-14654c4b-6863-49d8-a21e-87adff6d127b.png">

### 1.3 Escala la aplicación replicando los pods
En esta sección, vamos a escalar la aplicación creando réplicas de los pod que creamos previamente. El tener múltiples replicas de un mismo pod nos permite asegurar que el despliegue cuenta con los recursos disponibles para soportar un incremento de carga en la aplicación. 


 1. En IBM Cloud Shell, incrementamos la capacidad de una única instancia de Guestbook, a 5 instancias: 
 
```
oc scale --replicas=5 deployment/myguestbook
```
 
 <img width="556" alt="Captura de pantalla 2022-03-23 a las 16 46 29" src="https://user-images.githubusercontent.com/102157561/159739579-caa59490-491f-459f-88e4-e1456d80c346.png">


 2. Comprueba el estado del despliegue

```
oc rollout status deployment myguestbook
```

 <img width="735" alt="Captura de pantalla 2022-03-23 a las 16 46 48" src="https://user-images.githubusercontent.com/102157561/159739652-6ac1aec4-6fdb-4445-aa38-e9d2f93403e1.png">

 3. Verifica que ahora se tienen 5 pods en estado "Running" después de escalarlo. 
 
```
oc get pods -n guestbook-project
```

<img width="497" alt="Captura de pantalla 2022-03-23 a las 16 47 15" src="https://user-images.githubusercontent.com/102157561/159739778-6b0000b6-e41c-47c4-8ffc-b999cfd213d2.png">
 
### 1.4 Actualiza la aplicación
Con Openshift, es posible realizar una actualización continua de la aplicación a una nueva imagen. Con esta modalidad, es posible actualizar la imagen y deshacer los cambios si se encuentra un problema durante el despliegue. 
  
A continuación, actualizatemos la imagen con la tag v1, a una nueva versión v2. 
  
 1. En IBM Cloud Shell, actualizamos el despliegue utilizando la imagen v2 (dicha actualización también es conocida como Rollout):
 
```
oc set image deployment/myguestbook guestbook=ibmcom/guestbook:v2
```

 <img width="710" alt="Captura de pantalla 2022-03-23 a las 16 47 42" src="https://user-images.githubusercontent.com/102157561/159739900-e4ac765d-369f-494f-84f4-71cba0485237.png">

 2. Comprueba el estado del rollout:
 
```
oc rollout status deployment/myguestbook
```
<img width="529" alt="Captura de pantalla 2022-03-23 a las 16 48 07" src="https://user-images.githubusercontent.com/102157561/159739980-e8f273d1-4783-4128-abe2-aa38b1526bc6.png">

 3. Obtén una lista de los nuevos pods desplegados como parte del cambio de imagen:

```
oc get pods -n guestbook-project
```
<img width="520" alt="Captura de pantalla 2022-03-23 a las 16 48 19" src="https://user-images.githubusercontent.com/102157561/159740025-7656d630-bf64-44bb-8b71-b60006543680.png">

 4. Copia el nombre de uno de los pods y confirma que está utilizando la imagen v2 a través del siguiente comando:

```
oc describe pod <pod-name>
```
 <img width="754" alt="Captura de pantalla 2022-03-23 a las 16 48 59" src="https://user-images.githubusercontent.com/102157561/159740167-309ce8df-784e-4bbf-9252-3db81bec6ced.png">

 5. Actualiza la URL y verás los cambios en la aplicación. El Pod está utilizando la imagen v2 de la aplicación Guestbook. 

![image](https://user-images.githubusercontent.com/102157561/159728270-4d7a05f1-f10d-4a74-a4cc-227075c07755.png)

En caso que continúe apareciendo la versión antigua, es necesario forzar la actualización de la página para eliminar la caché.
 - En la mayoría de los navegadores en Windows se realiza con `ctrl+F5`
 - En Mac, el hard refresh es con `mays+cmd+r`

 <p align="center">
  <img src="images/hardrefresh.png" width="75%"></img>
</p>

### 1.5 Haz rollback de la aplicación
Cuando se realiza una actualización en el deployment, es posible ver referencias a las antiguas replicas y a las nuevas. En el proyecto, las antiguas replicas son los 5 pods originales desplegados en la sección 2. Las replicas nuevas son las que se han creado con la nueva imagen. 
Kubernetes siempre permite navegar entre las replicas y recuperar estados previos.
  
Todos los pods pertenecen al despliegue, y es quién se encarga de gestionarlos a través de un recurso llamado ReplicaSet. 

1. Para ver las ReplicaSet que tenemos, utiliza el siguiente comando: 

```
oc  get replicasets -l app=myguestbook
```

 <img width="493" alt="Captura de pantalla 2022-03-23 a las 16 49 28" src="https://user-images.githubusercontent.com/102157561/159740261-79de67cf-87ad-451e-8b03-d653da898a42.png">

2. Haz un rollback con el siguiente comando: 

```
oc rollout undo deployment/myguestbook
```

<img width="502" alt="Captura de pantalla 2022-03-23 a las 16 49 52" src="https://user-images.githubusercontent.com/102157561/159740331-b258404c-50ea-41e7-86dc-7080de2320af.png">

 3. Obtén el estado del despliegue para ver los nuevo pods creados como parte de deshacer el rollout.

```
oc rollout status deployment/myguestbook
```

<img width="510" alt="Captura de pantalla 2022-03-23 a las 16 50 28" src="https://user-images.githubusercontent.com/102157561/159740465-af015592-26ad-42a7-b28c-60a3d86bd64f.png">

 4. Obtén una lista de los nuevos pods creados como consecuencia de deshacer el rollout: 

```
oc get pods
```

<img width="507" alt="Captura de pantalla 2022-03-23 a las 16 50 42" src="https://user-images.githubusercontent.com/102157561/159740524-7976eb7c-bd3e-44d7-bff9-b7dbac469932.png">

 5. Copia el nombre de uno de los pods y comprueba la versión de la imagen que utiliza: 

 ```
 oc describe pod <pod-name>
```

<img width="774" alt="Captura de pantalla 2022-03-23 a las 16 51 04" src="https://user-images.githubusercontent.com/102157561/159740605-0b4dc815-53fe-44fa-98ba-5f88b19a6b8c.png">

 6. Después de deshacer el rollout, comprueba las ReplicaSets para notar que el replicaSet antiguo está ahora activo y gestiona 5 pods:


 ```
 oc get replicasets -l app=myguestbook
```

<img width="514" alt="Captura de pantalla 2022-03-23 a las 16 51 21" src="https://user-images.githubusercontent.com/102157561/159740697-036918c0-2371-47e8-8953-017ab410e374.png">

 7. Actualiza la URL para notar que el Pod está utilizando de nuevo la imagen v1 de la aplicación Guestbook. 
  
### 1.6 Resumen
Ahora sabemos cómo desplegar, escalar, actualizar y deshacer actualizaciones (rollback) aplicaciones en Openshift. Con este conocimiento es posible desplegar aplicaciones a partir de imágenes Docker, escalarlas para una determinada carga de trabajo, actualizarlas a nuevas versiones y volver a antiguas versiones en caso de bugs o problemas sobre las nuevas versiones. 

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

Damos un nombre a nuestro proyecto y le damos a crear.

<p align="center">
  <img src="images/watsonbotapp.png" width="75%"></img>
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

```
luisreyes/watson-bot:1.0
```

Por sencillez, hemos dejado la imagen disponible en el Dockerhub, pero en entornos empresariales usaremos siempre el registro privado de contenedores que proporciona Openshift Container Platform para asegurar que nuestras imágenes cumplen con todos los requistos de seguridad.

No hace falta indicar nada más. Openshift creará por defecto todos los recursos necesarios (pods, replicaset, deployment, servcicios) para que la aplicación se ponga en marcha.


<p align="center">
  <img src="images/crearapp.png" width="75%"></img>
</p>

### 2.4 Revisar estado del pod

Cuando Openshift intente arrancar la apliación, obtendrá un error de acceso a Watson Assistant debido a que las credenciales no están configuradas todavía. Puedes ver el estado del pod y los logs accediendo a la vista de Topología y desde ahí a la aplicación.

<p align="center">
  <img src="images/podlogerror.png" width="75%"></img>
</p>

Si entramos en los logs veremos que, efectívamente, la aplicación espera un parámetro que no encuentra en las variables de entorno. 

<p align="center">
  <img src="images/apikeyerror.png" width="75%"></img>
</p>

Vamos a resolver este problema, para ello usaremos un ConfigMap.

### 2.5 Crear un ConfigMap e inyectarlo en el pod

Ahora crearemos un recurso específico de Kubernetes para la gestión de variables de entorno. El ConfigMap. 
Volvemos a la perspectiva de Administrador y desde ahí vamos a la opción de menú **Workloads > ConfigMap**. Creamos un nuevo ConfigMap y sobreescribimos la definición por defecto con el yaml que tenéis a continuación:

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
Sustituye los valores para cada clave con las credenciales del servicio de Watson Assistant.
Para obtener las credenciales de acceso al servicio desplegado, accede a dicho servicio y copia los valores correspondientes a API_KEY y SERVICE_URL que puedes encontrar en la pantalla principal del servicio desplegado.

<p align="center">
  <img src="images/credencialeswa.png" width="75%"></img>
</p>

También tendrás que acceder al skill concreto y a los detalles del API para obtener el valor al parámetro WORKSPACE_ID


<p align="center">
  <img src="images/accessskill.png" width="75%"></img>
</p>


Copia el campo SkillID

<p align="center">
  <img src="images/copiarskill.png" width="75%"></img>
</p>


Una vez configurado el ConfigMap, dale al boton crear. 
Ya tenemos nuestro ConfigMap preparado con los parámetros y los valores que espera la aplicación.

Ahora tenemos que "inyectar" ese ConfigMap en los pods desplegados. Para ello, volvemos de nuevo a la perspectiva de Desarrollador, a la vista de Topología, y desde ahí Editamos el Deployment. 

<p align="center">
  <img src="images/editdeployment.png" width="75%"></img>
</p>

Dentro del formulario de edición, iremos a la sección de Variables de Entorno y crearemos las variables necesarias que está esperando leer la aplicación.
- CONVERSATION_IAM_APIKEY
- CONVERSATION_URL
- WORKSPACE_ID

Para cada una de estas variables, seleccionaremos el ConfigMap creado anteriormente y la clave que corresponde a dichas variables. 

<p align="center">
  <img src="images/inyectarconfigmap.png" width="75%"></img>
</p>

Esto es todo. Salva el Deployment. 

### 2.6 Comprobar que la aplicación funciona correctamente

Openshift volverá a regenerar el pod en cuanto salvemos la nueva versión del Deployment.
Si todo ha ido bien, el Pod ahora sí se ejecutará correctamente (comprueba de nuevo el Log). 
Ahora, simplemente, accede a la Ruta que Openshift ha creado para acceder desde un navegador, y prepárate a disfrutar con tu nuevo Bot.

<p align="center">
  <img src="images/accesoapp.png" width="75%"></img>
</p>

Ya podemos interactuar con Watson Assistant!

<p align="center">
  <img src="images/appfuncionando.png" width="75%"></img>
</p>

Este taller y el código que usamos está basado en el tutorial que puedes encontrar aquí [Watson Assitant Slots Intro](https://github.com/IBM/watson-assistant-slots-intro)

## Licencia

Este tutorial se encuentra licenciado bajo Apache License, Version 2. Objetos de código de terceros invocados en dentro de este Code Pattern se encuentran licenciados bajo sus respectivos proveedores en conformidad con los términos de sus correspondientes licencias. Todas las contribuciones se encuentran sujetas al [Developer Certificate of Origin, Version 1.1](https://developercertificate.org/) y la [Apache License, Version 2](https://www.apache.org/licenses/LICENSE-2.0.txt).

[Preguntas frecuentes sobre Apache License](https://www.apache.org/foundation/license-faq.html#WhatDoesItMEAN)
