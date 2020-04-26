---
id: 5979
title: Deploy static website en Azure Storage via Azure Pipelines
date: 2019-03-18T13:13:15+01:00
author: rbermejo
layout: post
---
En mi último articulo de [](http://www.compartimoss.com/revistas/numero-39/sitios-web-estaticos-en-azure-storage)**[CompartiMOSS](http://www.compartimoss.com/revistas/numero-39/sitios-web-estaticos-en-azure-storage)**, hacía una introducción de cómo podemos hacer el hosting de nuestras aplicaciones web estáticas en **_Azure Storage_**.<!--break-->

En este articulo vamos a ver cómo podemos configurar nuestro despliegue continuo de nuestra web estática mediante**_Azure Pipelines_**.

Para poder llevar a cabo este despliegue necesitaremos usar **_Azure CLI_** que nos permitirá subir los ficheros al **_container_** del blob storage que deseamos.

Como base para este ejemplo cogeremos tanto el **_Azure Storage Static_** Web y el código creado en el articulo de **[CompartiMOSS](http://www.compartimoss.com/revistas/numero-39/sitios-web-estaticos-en-azure-storage)** que comentaba.

## Build

Lo primero que haremos es crear la **build** para compilar nuestro proyecto:<figure class="wp-block-image">

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/CIStaticWeb.png" target="_blank" rel="noreferrer noopener"><img src="https://blogrbermejostorage.blob.core.windows.net/blog/CIStaticWeb.png" alt="Build pipeline static web" /></a></figure> 

Este es un ejemplo de pipeline, donde hacemos tres acciones:

  * **npm install** &#8211;> instalamos los paquetes npm necesarios para poder realizar el build.
  * **npm run build** &#8211;> Este comando realizará la _build_ de nuestra aplicación **React** y nos dejará en la carpeta _build_ el resultado.
  * **Publish artifact** &#8211;> Publicamos el resultado de la _build_.

## Release {#mce_4}

Una vez ya tenemos definido nuestro **pipeline** de **build**, ya podemos implementar nuestro **pipeline** de **Release**.

En este punto busqué posibles **Tasks** ya implementadas para usarlas, y las que he encontrado no terminan de funcionar, principalmente porque solo están preparadas para _**Azure Storage V1**_.

Finalmente después de una fustrante búsqueda decidí usar **_Azure CLI_**, que seguro que funcionaba.

Pare ello lo primero que tenemos que hacer es crear el **pipeline** de **Release**:<figure class="wp-block-image">

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/release.png" target="_blank" rel="noreferrer noopener"><img src="https://blogrbermejostorage.blob.core.windows.net/blog/release.png" alt="Auzre Pipeline Release" /></a></figure> 

Una vez tenemos el **pipeline** añadimos la tarea de **Azure CL**I y la configuramos de la siguiente forma<figure class="wp-block-image">

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/AzurePipelineReleaseAzureCLI.png" target="_blank" rel="noreferrer noopener"><img src="https://blogrbermejostorage.blob.core.windows.net/blog/AzurePipelineReleaseAzureCLI.png" alt="Azure CLI Task" /></a></figure> 

Vamos a ver los campos que hemos informado:

  * **Display Name** &#8211;> El nombre de la tarea
  * **Azure Subscriptios** &#8211;> Seleccionamos la suscripción donde se desplegará nuestra _static web_
  * **Script Location** &#8211;> Seleccionamos _inline_ ya que el _script_ está definido en la tarea
  * **Inline Script** &#8211;> az storage blob upload-batch &#8211;account-name $(StorageAccountName) &#8211;account-key $(StorageAccountKey) &#8211;destination $(StorageAccountDestination) &#8211;source $(System.DefaultWorkingDirectory)/ALMTest-ASP.NET-Core-CI\drop

El _script_ de _inline_ es el _script_ de **_Azure CLI_** que nos permite subir ficheros a un **blob**.

Como podemos observar tenemos algunas variables que a continuación definiremos, para ello seleccionaremos el tab _variables_ y las definimos<figure class="wp-block-image">

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/staticwebvariables.png" target="_blank" rel="noreferrer noopener"><img src="https://blogrbermejostorage.blob.core.windows.net/blog/staticwebvariables.png" alt="variables que hemos definido en el script de Azure Cli" /></a></figure> 

  * **StorageAccountDestinations** &#8211;> Indicamos el _container_ donde se copiarán lo ficheros, en nuestro caso $web.
  * **StorageAccountkey** &#8211;> será la _key_ de nuestro **_Azure Storag_**e que la obtenemos del _sotrage_ creado.
  * **StorageAccountName** &#8211;> El nombre del _storage_ que hemos creado.

Ahora ya lo tenemos todo apunto para ejecutar el _pipeline_.

Antes de ejecutarlo nuestro _container_ estará así<figure class="wp-block-image">

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/webstaticempty.png" target="_blank" rel="noreferrer noopener"><img src="https://blogrbermejostorage.blob.core.windows.net/blog/webstaticempty.png" alt="" /></a><figcaption>  
</figcaption></figure> 

Ahora ejecutamos la _release_, y una vez ha finalizado vemos que se ha llenado el _container_ con nuestra _static web_<figure class="wp-block-image">

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/releasesataticwebok.png" target="_blank" rel="noreferrer noopener"><img src="https://blogrbermejostorage.blob.core.windows.net/blog/releasesataticwebok.png" alt="" /></a></figure> <figure class="wp-block-image"><a href="https://blogrbermejostorage.blob.core.windows.net/blog/containerstaticfull.png" target="_blank" rel="noreferrer noopener"><img src="https://blogrbermejostorage.blob.core.windows.net/blog/containerstaticfull.png" alt="" /></a></figure> 

Ahora si ejecutamos nuestra web<figure class="wp-block-image">

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/staticworking.png" target="_blank" rel="noreferrer noopener"><img src="https://blogrbermejostorage.blob.core.windows.net/blog/staticworking.png" alt="" /></a></figure> 

Espero os sirva de ayuda.

Happy coding!