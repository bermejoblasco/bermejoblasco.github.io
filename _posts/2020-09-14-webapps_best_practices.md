---
title: Azure Webapps best practices
post_date: 2020-09-14 00:00:00
author: rbermejo
layout: posts
---

Cuando usamos servicios *Platform as a Service* (**PaaS**) en el cloud, estos servicios son administrados por el proveedor.<!--break-->  
Que un servicio sea administrado por el proveedor *cloud* tiene ventajas y desventajas. Mi opinión es que tiene más ventajas que desventajas, pero necesitas tener conocimiento sobre que está pasando en los servidores que alojan tus servicios, y sobre que tienes control y sobre que no. 

Hay varios puntos que se deben tener en cuenta cuando usas servicios **PaaS** en el *cloud*:
* Las máquinas virtuales (VM) donde están hospedados los servicios, que pueden reiniciarse o moverse de servidor
* Los sistemas pueden ser actualizados
* Los servidores de archivos pueden escalar
* Las instancias pueden ser reemplazadas
* El servicio escale tanto horizontalmente como verticalmente 

En este articulo vamos a comentar como hacer que tus *web apps* sean más resistentes a estos cambios y tener preparada tu aplicación e infraestructura para ejecutarse en Azure sin problemas. 

## Usar múltiples instancias 

Si solo configuras una instancia de nuestra *web app* esta única instancia de VM se convierte un *single point of failure*. 
Si configuramos más de una instancia nos aseguramos de que si una de ellas tiene un problema las *request* que vayan a las otras continuarán funcionando correctamente, evitando que nuestra aplicación falle al 100%.  
Tener más de una instancia no solo te previene de fallos, sino que si tu aplicación tarda mucho en iniciarse (*cold start*), tener más de una instancia nos permite dar servicio mientras estás se están iniciando. 
Como mínimo se aconseja tener 2 o 3 instancias. 

![Múltiple instancias](/public/images/2020/09/multipleinst.PNG)  

## Número de Web Apps por App Service 

Según el plan de *App Service* que usemos, podemos añadir más o menos *apps* en él. Se recomienda no ejecutar más de 8 *apps* en un mismo *App Service* 

## Cambios de configuración por defecto 

Cuando se crea una *web app* está se crea con una configuración por defecto. Hay dos configuraciones que se recomienda cambiar para el buen funcionamiento del servicio, estás son:

* **Always on**: Por defecto su valor es *false*. Se recomienda configurarlo a *true*, ya que de esta forma se mantienen las instancias de la VM vivas cuando no están recibiendo peticiones. Si no está activada al cabo de 20 minutos de no recibir peticiones las instancias entran en modo *cold* y al recibir la primer request tienen que levantarse. 
* **ARR afinity**: Por defecto su valor es *true*. Esta variable hace que las sesiones se conecten siempre a la misma instancia. Esto puede ser contraproducente porque no hará que las peticiones sean uniformes a través de las instancias pudiendo provocar una sobrecarga en una de ellas.  

![Cambio por defecto](/public/images/2020/09/alwayson.PNG) 

## Auto Heal 

*Auto Heal* es una opción básica dentro de las *web apps* que se encuentra dentro de *“Diagnose and Solve problems”* sección *“Diagnostic Tools”*.  
Muchos de los errores que nos encontramos se solucionan con un simple reinicio, y *Auto Heal* nos permite hacer eso de forma automática. Esta opción nos provee de diferentes configuraciones para que se ejecute *Auto Heal*. 

![Auto Heal](/public/images/2020/09/autoheal.PNG) 

## Enable Local Cache 

Las *web apps* sino se les indicas lo contrario, almacenan su contendio en *Azure Storage*, lo que hace que tengamos otro punto de fallo fuera de nuestro control, ya que si el proveedor realiza alguna acción sobre este *storage* puede repercutir en nuestra aplicación.  
Si activamos la función local cache, este contenido se lee de la VM y no de *Azure Storage*. No solo evitaremos un punto de fallo, sino que también reduciremos el número de veces que nuestra *app* se tiene que reciclar.  
Para activar esta opción se debe añadir la variable **WEBSITE_LOCAL_CACHE_OPTION** en los *settings* con valor **Always**.  Por defecto si no se indica nada el tamaño de esta caché es de **300 MB**, el máximo que se puede configurar es **2000 MB** y se puede hacer mediante la variable **WEBSITE_LOCAL_CACHE_SIZEINMB**.  

![Local Cahe](/public/images/2020/09/localcahce.PNG)  

## Deployment Slots 

Con los slots tenemos la oportunidad de probar lo que queremos poner en producción en el mismo entorno, pero sin interferir en producción.  
Podemos desplegar nuestro código en el *slot* y luego hacer el *swap* y lo que tenemos desplegado en el *slot* pasa a ser producción lo que teníamos en producción pasa al *slot*. 

Usar Slots nos da los siguientes beneficios: 

* Probar los cambios antes de ponerlo en producción   
* Al hacer el *swap* nos aseguramos de que no hay downtime durante el cambio   
* Una vez hemos hecho el *swap* lo que había en producción pasa a estar en el slot y en caso de tener que hacer rollback solo hay que volver ha hacer *swap* 

![Deployment Slots](/public/images/2020/09/deploymentslots.PNG) 

Para finalizar dos puntos más interesantes:
1.	Activar *Application Insights* --> De esta forma podremos tener todla la información que está pasando tanto en nuestra aplicación como a nivel de infraestructura: número peticiones, tiempo respuesta, cpu consumida, memoria….
2.	Activar *Health Check* --> Está opción está en *preview* todavía, pero nos permite configurar un *ednpoint* y el *App Service* realizará el *health check* sobre las instancias y cuando encuentre un error en una de ellas no le enviará tráfico. Podéis ver más información aquí: https://github.com/projectkudu/kudu/wiki/Health-Check-(Preview)

Source:
https://azure.github.io/AppService/2020/05/15/Robust-Apps-for-the-cloud.html