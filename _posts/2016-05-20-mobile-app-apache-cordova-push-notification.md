---
id: 3311
title: 'Mobile App: Apache Cordova Push Notification'
date: 2016-05-20T11:48:34+01:00
author: rbermejo
layout: post
---
En este post vamos a ver como podemos enviar notificaciones push desde **Azure** a nuestras aplicaciones implementadas en **Apache Cordova**. En concreto veremos como hacerlo para Android.<!--break-->

## ¿Que es y para que sirve _Azure Mobile Apps_?

**_Azure Mobile Apps_** es un servicio _Platform as a service_� (**PaaS**) que nos proporciona Azure, donde se nos ofrece una plataforma para el desarrollo de aplicaciones� móviles proporcionando una alta disponibilidad, podemos escalar fácilmente� y nos ofrece un conjunto de� herramientas que facilitan el desarrollo y despliegue de estas.

##### ¿Que podemos hacer?

  * Podemos enviar notificaciones push.
  * Podemos construir aplicaciones� offline y realizar la sincronización de los datos cuando creamos oportuno.
  * Conectarnos con la infraestructura de nuestra empresa de forma rápida y sencilla.
  * Construcción de aplicaciones móviles nativas o **C_**ross** Platform_**.

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/MobileAppGlobal.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/MobileAppGlobal.png" alt="selectexportsql" width="660" height="397" /></a>

##### ¿QUE nos ofrece?

  * **Autenticación y autorización**: Podemos autenticar nuestra aplicaciones contra: facebook, google, microsoft, AzureAD o Twitter mediante [OAuth2](http://oauth.net/2/)
  * **Acceso a datos**: tenemos la posibilidad de conectarnos de forma sencilla a BBDD _SQLAzure, SQL on-premises, DocumentDB, MongoDB_ y _Table Storage_ de forma sencilla� y rápida.
  * Además de como hemos comentado anteriormente el envío de notificaciones push y de trabajo offline con sincronización.

Además como A_zure Mobile App_ está incorporado dentro de _Azure App Service_, junto a _logic apps, api apps y web apps_, también puede beneficiarse de los servicios que ofrece como:

  * **Auto escalado**.
  * **Staging**: Podemos tener varias versiones de nuestro _site_ e intercambiarlos cuando lo creemos oportuno. Normalmente se utiliza para realizar pruebas antes de una subida a producción, y si el entorno funciona se intercambian los entornos.
  * **Red Virtual**: Podemos conectarnos con entornos _on-premise._
  * **Despliegue continuo**.

En este punto tenemos una visión global de que nos ofrece y que podemos hacer con **_Azure Mobile Apps.�_** Ahora vamos a entrar en más en detalle que son y como utilizar las notificaciones push.

## Notificaciones Push

Una notificación push es aquella notificación de una aplicación que nos llega a nuestro dispositivo móvil.

Lo más destacado de las notifiaciones push es que no hace falta que tengamos abierta nuestra aplicación, cada vez que el servidor push correspondiente reciba una petición está la enviará a los dispositivos de forma inmediata, por tanto la inmediatez es otro de los puntos fuertes de las notificaciones push.

#### ¿Como funcionan las notificaciones push?

Cada plataforma tiene su propio **PNS** (_platform notification system_) para enviar las notificaciones, por ejemplo:

  * Windows Stroe apps: WNS (Windows notification server)
  * Apple: APNS (Apple push notification server)
  * Android: GCM (Google Cloud Messaging)

El ciclo de vida sería:

  1. La app contacta con el PNS para saber de que forma debe trabajar con el, es decir, con que� _handler_ debe trabajar_._
  2. Registramos en el back-end de la aplicación� este _handler._
  3. Para enviar una notificación push, nuestro back-end usa el handler obtenido y envía la información necesaría al PNS.
  4. El PNS envía la notificación correspondiente a partir de la información recibida.

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/notification-hub-diagram.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/notification-hub-diagram.png" alt="selectexportsql" width="660" height="397" /></a>

Como podemos ver para cada tipo de dispositivo deberíamos tratar con su sistema de notificación, lo cuál aumenta exponencialmente la complejidad y el tiempo de desarrollo.

¿Como **_Azure Mobile Apps_**� nos ayuda? � Mediante otro servicio de Azure denominado **_Notification Hub_**.

** _Notification Hub_** lo que nos permite es unificar en un único punto todos los tipos de servicios de notificación, y nos hace de pasarela con estos sistemas, de esta forma nuestra aplicación solo debe entenderse con un sistema y este es el que se encarga de comunicarse con los demás.

_**Azure Mobile Apps**_ nos hace de pasarela hacía el **_Notification Hub_**, haciendo más sencillo todo este proceso.

Vamos a verlo en acción, como ya hemos comentado haremos un ejemplo con una aplicación realizada en_**� Apache Cordova**_.

  * Creamos nuestro _**Azure Mobile App.**_

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/CrateMobielApp1.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/CrateMobielApp1.png" alt="selectexportsql" width="660" height="397" /></a>

  * � Una vez creado la� _**Azure Mobile App**_, le añadimos un _**Notification Hub.**_

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/CreateNotificationHub1.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/CreateNotificationHub1.png" alt="selectexportsql" width="660" height="397" /></a>  
<a href="https://blogrbermejostorage.blob.core.windows.net/blog/CreateNotificationHub2.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/CreateNotificationHub2.png" alt="selectexportsql" width="660" height="397" /></a>

  * Una vez creado el� _**Notification Hub�**_ podemos ver los tipos de notificaciones que podemos añadir.

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/CreateNotificationHub3.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/CreateNotificationHub3.png" alt="selectexportsql" width="660" height="397" /></a>

En este punto deberíamos habilitar� en los sistemas que quisiéramos las notificaciones push y configurar nuestro� _**Notification Hub.**_

Como ya he comentado vamos hacerlo para Android. Así que vamos� a ver cuales son los� pasos a seguir para habilitar� **Google Cloud Messaging:**

  * Hacemos login en [Google Cloud Console](https://console.developers.google.com/project "").
  * Creamos un proyecto

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/GCM1.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/GCM1.png" alt="selectexportsql" width="660" height="397" /></a>

  * Ahora� en dashboard vamos a**_� utilities and More_,�** click en_� **Project Information**_. Debemos apuntarnos el valor del campo� **Project Number** ya que será el valor que deberemos informar en el campo� _SenderId�_ cuando estemos configurando en nuestra aplicación mobile las notificaciones push

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/GCM2.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/GCM2.png" alt="selectexportsql" width="660" height="397" /></a>

  * Ahora, en el dashboard de nuevo, debajo de� **Mobile APIs**, click **Google Cloud Messaging** y en la siguiente página click en� � **Enable**.
  * Ahora� en dashboard de nuevo** _Click **Credentials** > **Create Credential** > **API Key**._**

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/GCN3.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/GCN3.png" alt="selectexportsql" width="660" height="397" /></a>

  * Seleccionamos� **Create a new key**, y creamos una un **key** de tipo **Server Key.**
  * Nos guardamos el valor de� **API KEY** ya que este valor es el que debemos añadir en nuestro� _**Notification Hub.**_

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/GCN4.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/GCN4.png" alt="selectexportsql" width="660" height="397" /></a>

#### <span style="text-decoration: underline;">Creación de la aplicación Apache Cordova y envío notificaciones push.</span>

Llegados a este punto vamos a crear una aplicación A**pache Cordova** en **VS2015**.

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/VS20151.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/VS20151.png" alt="selectexportsql" width="660" height="397" /></a>

Una vez creada añadimos el plugin de **[Azure Mobile Apps](https://github.com/Azure/azure-mobile-services-cordova),�** el plugin de** [Push](https://github.com/phonegap/phonegap-plugin-push.git)�** y el plugin** [Device](https://github.com/apache/cordova-plugin-device).**

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/VS20152.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/VS20152.png" alt="selectexportsql" width="660" height="397" /></a>

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/VS20153.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/VS20153.png" alt="selectexportsql" width="660" height="397" /></a>

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/VS20154.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/VS20154.png" alt="selectexportsql" width="660" height="397" /></a>

Ahora añadiríamos el código en nuestra aplicación para registrar nuestro dispositivo y poder manejar las notificaciones.

<pre class="brush: jscript; title: ; notranslate" title="">//Inicilizamos nuestro plugin de Mobile Apps
var client = new WindowsAzure.MobileServiceClient('https://xxx.azurewebsites.net');
//Configuramos las notificaciones
var pushOptions = {
    android: {
        senderID: 'your project number'                            
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
 
//Inicializamos el plugin de push con las opciones configuradas
var pushHandler = PushNotification.init(pushOptions); 

//Método de callback cuando el registro haya sido satisfactorio 
pushHandler.on('registration', function (data) {                        
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
//registrmos en nuestro Notification Hub
    client.push.register(name, data.registrationId);            
});
//Callback cuando llegue una notificación 
pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
    alert("Notification: " + data.message)
});
//Callback cuando llegue un error. 
pushHandler.on('error', function (error) {
    // Handle errors
    alert("error push: " + error)
});
</pre>

Ejecutamos la aplicación.

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/AC1.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/AC1.png" alt="selectexportsql" width="660" height="397" /></a>

En este ejemplo, al arrancar la aplicación registramos el dispositivo.

###### ¿Como podemos ver si hemos registrado el dispositivo y enviar una push de prueba?

Se puede hacer desde [Visual Studio con las Azure tools](https://www.visualstudio.com/es-es/features/azure-tools-vs.aspx).

En Server Explorer conectamos nuestra cuenta de azure, navegamos hata notification hub y hacemos doble click. Aparece una pantalla con dos pestañas,:

  * **Test Send:** Desde podremos enviar notificaciones push para realizar pruebas.
  * **Device Registratio**n: Donde podemos ver la lista de dispositivos registrados y de que tipo de push se han registrado, en la imagen siguiente se puede ver el resultado del registro al lanzar nuestra aplicación.

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/AC2.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/AC2.png" alt="selectexportsql" width="660" height="397" /></a>

Ahora desde la pestaña Test Send enviamos una notificación:

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/AC3.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/AC3.png" alt="selectexportsql" width="660" height="397" /></a>

Ahora podemos ver como ha llegado a nuestro dispositivo.

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/AC4.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/AC4.png" alt="selectexportsql" width="660" height="397" /></a>

¡Ya tenemos nuestro sistema de notificaciones push funcionando en Azure!

En el siguiente [link](https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-overview/#integration-with-app-service-mobile-apps)� podéis indagar más sobre el tema, y como hacer lo mismo para iOS y Windows.

Podéis bajaros el código de [aquí](https://github.com/bermejoblasco/Mobile-App-Apache-Cordova-Push-Notification).

Hasta la próxima.