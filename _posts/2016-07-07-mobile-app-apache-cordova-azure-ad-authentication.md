---
id: 4081
title: 'Mobile App: Apache Cordova Azure AD Authentication'
date: 2016-07-07T07:43:53+01:00
author: rbermejo
layout: post
---
En un post anterior os explicaba cómo se podía enviar notificaciones push a través de **_[Azure Mobile Apps.](http://www.robertbermejo.com/mobile-app-apache-cordova-push-notification/)_**

Además de poder enviar notificaciones push, otra característica que tiene **Azure Mobile App**s y que me encanta, es el poder de autenticarte y autorizarte sobre varios sistemas de identidad: Microsoft Account , Google, Facebook, Twitter o Azure AD.<!--break--><!--break-->

**¿Qué es y cómo funciona App Service Authentication/Authorization?**

Esta característica nos ofrece una vía para poder realizar la autenticación de los usuarios en diversos sistemas de identidad sin que tengamos que realizar cambios significativos, ni realizar códigos específicos para cada identidad de autenticación, ya sea en nuestro _Back-End_ o en _Front-End_ en el caso de una _Single Page Application._

**Azure App Services** utiliza [_federated identity_](https://en.wikipedia.org/wiki/Federated_identity), haciendo de hub de proveedores de identidad de terceros y guardando los datos necesarios de cada uno de ellos para poder realizar la autenticación. Para más información en detalle visitar: <https://azure.microsoft.com/en-us/documentation/articles/app-service-authentication-overview/>

**¿Qué vamos a ver?**

En este post vamos a ver cómo se puede realizar la autenticación de usuarios en **Apache Cordova** contra **Azure Active Directory(Azure AD)** utilizando **Azure Mobile Apps**.

Lo primero que debemos hacer es crear una **Azure Mobile App**, para ello podéis visitar, como os comentaba anteriormente, mi post sobre notificaciones push: <http://www.robertbermejo.com/mobile-app-apache-cordova-push-notification/>

Lo primero que haremos en activar la autenticación en nuestra **Azure Mobile App** mediante el portal.

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/ActivateAuth.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/ActivateAuth.png" alt="selectexportsql" width="660" height="397" /></a>

Una vez activada, seleccionamos la opción **Azure Active Directory**

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/ActivateAD.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/ActivateAD.png" alt="selectexportsql" width="660" height="397" /></a>

Nos aparecen tres opciones:

  1. **Off**: Opción si queremos dejar de utilizar este proveedor de identidad.
  2. **Express**: Nos permite configurar la aplicación en **Azure AD** de forma rápida y sencilla.
  3. **Advanced**: La aplicacion ya ha sido asignada a un **Azure AD** y poseemos el _**Client ID**_ y _**Issuer URL**_, simplemente los introduciríamos y lo tendríamos listo.

En nuestro caso lo haremos **_express_**. Lo único que necesitamos configurar es sobre que **Active Directory(AD)** queremos actuar y si queremos crear una nueva aplicación en **AD** o si queremos utilizar una ya existente.

En nuestro caso utilizaremos el directorio predeterminado y seleccionaremos la creación de una nueva aplicación en **AD**.

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/CreateAppInAD.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/CreateAppInAD.png" alt="selectexportsql" width="660" height="397" /></a>

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/CreateAppInADSave.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/CreateAppInADSave.png" alt="selectexportsql" width="660" height="397" /></a>

Una vez creada la identidad, podemos ir a nuestro **Azure AD** y ver que se ha vinculado la app en este. Para ello debemos ir al portal antiguo e ir a **AD** sección aplicaciones.

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/VerifyAD.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/VerifyAD.png" alt="selectexportsql" width="660" height="397" /></a>

Bien, ahora necesitamos crear un proyecto de **Apache Cordova** en **Visual Studio.** Para ello, nuevamente seguiremos los pasos de mi anterior [post](http://www.robertbermejo.com/mobile-app-apache-cordova-push-notification/) excepto a la hora de añadir plugins, en este caso no necesitamos el plugin de push notifications, los que necesitamos son:

  * Azure Mobile Apps
  * InAppBrowser
  * Whitelist

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/pluginsAC.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/pluginsAC.png" alt="selectexportsql" width="660" height="397" /></a>

Una vez añadidos, vamos a index.js y añadimos el siguiente código dentro del método onDeviceReady:

<pre class="brush: jscript; title: ; notranslate" title="">var client = new WindowsAzure.MobileServiceClient('http://yoursite.azurewebsites.net/');
    
  client.login('aad').done(function (results) {        
          element.innerHTML = results.userId;
     }, function (err) {
            element.innerHTML = 'Error Auth: ' + err;
     });
</pre>

Como veréis hemos puesto &#8220;aad&#8221; como la fuente para realizar nuestro login, esto significa que atacaremos a la configuración de **Azure AD** que hemos realizado anteriormente. En la siguiente tabla se muestran las opciones y valores que deberíamos poner dependiendo del proveedor que utilizásemos:

<div class="table-responsive">
  <table  style="width:100%; "  class="easy-table easy-table-default " border="0">
    <tr>
      <th sort="false">
        Provider
      </th>
      
      <th >
        SDK Provider Name
      </th>
      
      <th >
        OAuth Host
      </th>
    </tr>
    
    <tr>
      <td >
        Azure Active Directory
      </td>
      
      <td >
        aad
      </td>
      
      <td >
        https://login.windows.net
      </td>
    </tr>
    
    <tr>
      <td >
        Facebook
      </td>
      
      <td >
        facebook
      </td>
      
      <td >
        https://www.facebook.com
      </td>
    </tr>
    
    <tr>
      <td >
        Google
      </td>
      
      <td >
        google
      </td>
      
      <td >
        https://accounts.google.com
      </td>
    </tr>
    
    <tr>
      <td >
        Microsoft
      </td>
      
      <td >
        microsoftaccount
      </td>
      
      <td >
        https://login.live.com
      </td>
    </tr>
    
    <tr>
      <td >
        Twitter
      </td>
      
      <td >
        twitter
      </td>
      
      <td >
        https://api.twitter.com
      </td>
    </tr>
  </table>
</div>

Solo nos queda un último paso que consiste en añadir un tag meta de seguridad en index.html para dar acceso a nuestro proveedor de identidad vía nuestra **Mobile App**.

Se debe añadir el siguiente tag:

<pre class="brush: xml; title: ; notranslate" title="">&lt;meta http-equiv="Content-Security-Policy" rity-Policy" content="default-src 'self'data: gap: https://login.windows.net https://yoursite.azurewebsites.net; style-src 'self'"&gt;
</pre>

Donde en _gap:_ se deberá introducir la URL� del proveedor utilizado (ver tabla anterior).

En este punto ya podemos � ejecutar nuestra aplicación.

Ejecutamos la aplicación en Windows 10:

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/Window1_1.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/Window1_1.png" alt="selectexportsql" width="660" height="397" /></a>  
Introducir el usuario y password del usuario con el que entráis en vuestro portal de Azure, que será _Global Admin_ y automáticamente se os habrá añadido acceso a la aplicación.

Siempre podéis crear usuarios en **Azure AD** y darle permisos de acceso a vuestra aplicación. **[Aquí](https://azure.microsoft.com/en-us/documentation/articles/active-directory-create-users/)** podéis ver como crear un usuario en **Azure AD.**

Una vez introducido podéis ver que ya estáis autenticados.  
<a href="https://blogrbermejostorage.blob.core.windows.net/blog/Window1_2.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/Window1_2.png" alt="selectexportsql" width="660" height="397" /></a>

Y así se vería en un dispositivo Android:

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/Android1_1.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/Android1_1.png" alt="selectexportsql" width="660" height="397" /></a>

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/Android1_2.png.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/Android1_2.png.png" alt="selectexportsql" width="660" height="397" /></a>

**CONCLUSION**

Utilizar **Azure Mobile Apps** como _hub_ de autenticación nos facilita la vida a la hora de desarrollar y mantener nuestros sistemas de identidad. El objetivo es muy similar que el servicio de Push: concentrar en un solo punto las configuraciones y donde se debe atacar para realizar las operaciones.

Podéis descargar el código desde mi github: � <https://github.com/bermejoblasco/MobileAuthAuth>

¡Hasta la próxima!

&nbsp;

&nbsp;