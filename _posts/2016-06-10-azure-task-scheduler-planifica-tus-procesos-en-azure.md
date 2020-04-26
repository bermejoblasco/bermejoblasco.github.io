---
id: 3931
title: 'Azure Task Scheduler: Planifica tus procesos en Azure'
date: 2016-06-10T10:30:41+01:00
author: rbermejo
layout: post
---
En el desarrollo de aplicaciones normalmente nos encontramos que debemos implementar tareas que deben ejecutarse periódicamente y que están fuera del entorno de nuestra aplicación.

Estas tareas necesitan ser planificadas para su ejecución, en Windows utilizaríamos el Task Scheduler de Windows, y si tuviéramos muchas tareas utilizaríamos frameworks como [**Quartz**](http://www.quartz-scheduler.net/) que nos ayudan a planificar todas las tareas desde un solo punto y no una a una.<!--break-->

En entorno **On-premise** lo tenemos claro, pero si estoy en **Azure,** ¿como lo hago?. La respuesta es **Azure Task Scheduler.**

###### **¿Que nos ofrece Azure Task Scheduler?**

  * Planificar la ejecución de tareas según sea deseado: periódicamente, una sola vez o durante un intervalo de tiempo.
  * Planificar tareas tanto que estén alojadas en **Azure** como en **On-premise**.
  * Configuración de reintento en el caso de que no se haya podido ejecutar la tarea.
  * Cinco formas de ejecución: Http, Https, Sotrage Queues, Azure Service Bus Queues y Azure Service Bus Topics.

Ahora que ya tenemos claro que nos ofrece y que podemos hacer, vamos a ver cómo crearlo y configurarlos, pero antes de ello es necesario tener claro que significan los siguientes conceptos:

  * **Job Collection** &#8211;> Colección de Jobs que permite compartir configuraciones y cuotas entre los jobs.
  * **Job** &#8211;> Define la configuración de cómo y cuándo debe ejecutarse una tarea.
  * **Job History** &#8211;> Aquí encontraremos todos los detalles de las ejecuciones de los Jobs.

###### **Creando un job  
** 

Ahora es el momento de crear nuestro primer **Job**.

  * Creamos un Scheduler en Azure

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/Create.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/Create.png" alt="selectexportsql" width="660" height="397" /></a>

  * Una vez creado nos aparecerá una pantalla donde poder configurar nuestro **Job**, en ella podremos definir el nombre del **Job**, la subscripción a la que la queremos vincular, a que **Job Collection** pertenece, que acción ejecutará para lanzar el proceso y por último como lo queremos planificar.

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/JobCollection.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/JobCollection.png" alt="selectexportsql" width="660" height="397" /></a>

Cuando configuramos la acción que desencadenará la ejecución de la tarea, según cuál escojamos nos pedirá unos parámetros u otros.  
<a href="https://blogrbermejostorage.blob.core.windows.net/blog/Action.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/Action.png" alt="selectexportsql" width="660" height="397" /></a>

Por último definiremos la periódica, si queremos que se lance solo una vez, más de una vez y en este caso el intervalo de ejecución  
<a href="https://blogrbermejostorage.blob.core.windows.net/blog/Scheduler.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/Scheduler.png" alt="selectexportsql" width="660" height="397" /></a>

Una vez creado, podemos ir a nuestra JobCollection y ver las estadísticas de nuestros Jobs, además de poder modificar configuraciones, acceder a sus Jobs y modificar la acción de ejecución o su planificación.  
<a href="https://blogrbermejostorage.blob.core.windows.net/blog/JobCollectionResult.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/JobCollectionResult.png" alt="selectexportsql" width="660" height="397" /></a>  
<a href="https://blogrbermejostorage.blob.core.windows.net/blog/JobResult.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/JobResult.png" alt="selectexportsql" width="660" height="397" /></a>

###### **conclusion**

**Azure Task Scheduler** es una forma sencilla y rápida para planificar la ejecución de nuestras tareas y poder modificar esta configuración de forma rápida y sencilla. También una cualidad importante es la configuración del reintento automática lo que nos ahorra mucho trabajo.

Podéis profundizar más en: <https://azure.microsoft.com/en-us/documentation/articles/scheduler-intro/>

&nbsp;