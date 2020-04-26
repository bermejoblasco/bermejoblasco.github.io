---
id: 91
title: Shared Access Signatures (SAS)
date: 2016-03-01T21:00:11+01:00
author: rbermejo
layout: post
---
Aquí esta mi primer Post, espero os guste.

Cuando estamos usando el Storage de Azure no siempre queremos que los datos que tenemos almacenados sean públicos, pero tampoco queremos que sean siempre privados, en algunas ocasiones queremos dar acceso limitado a los recursos.<!--break-->

Para poder dar solución a esta problemática Azure nos da la opción de crear Shared Acces Signatures (SAS). SAS nos ofrece la posibilidad de dar acceso a recursos de forma limitada durante un intervalo de tiempo concreto sin tener que exponer las claves privadas de acceso.

¿Cuándo utilizar SAS?. Cuando se cumplan estas dos condiciones:

1-No queremos compartir nuestra private key.

2-No queremos que nuestro Back-End sea quien realice las comunicaciones y transacciones con el storage.

Lo que buscamos es atacar directamente al storage desde nuestro Front-End, para ello pediremos al Back-End la SAS correspondiente y llamaremos al Storage vía REST Api.

<img class="alignnone wp-image-111" src="http://robertbermejo.azurewebsites.net/wp-content/uploads/2016/03/SAS-300x73.jpg" alt="SAS" width="678" height="165" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/03/SAS-300x73.jpg 300w, https://www.robertbermejo.com/wp-content/uploads/2016/03/SAS.jpg 623w" sizes="(max-width: 678px) 100vw, 678px" /> 

Hay dos tipos de SAS:

  * Account SAS --> Se da acceso a toda la cuenta de storage.
  * Service SAS --> Se da acceso a un recurso de almacenamiento: Blob, Queue, Table o File.

En este post os daré un ejemplo de como trabar con Service SAS para dar acceso a un archivo concreto de un blob storage.

Un ejemplo de URI SAS sería:

<pre>https://myaccount.blob.core.windows.net/document/text.mp4?sv=2012-02-12&st=2016-02-29T11%3A57%3A21Z&se=2016-02-29T20%3A32%3A21Z&sr=c&sp=rw&sip=10.1.0.100-10.1.0.254&spr=https&sig=3%2BN5lQscy%2B9TGvWrDRFHrg4lXSQzthDFKrC9ylrxsac%3D</pre>

Donde:

sv --> Índica la versión del storage

st --> Fecha y hora de inicio.

se- -> Fecha y hora de expericaión

sr --> recurso

sp --> permiso

sip --> Intervalos de ip's que tienen acceso

spr --> Protocolo de acceso.

sig --> _Signature_ de acceso

A continuación os muestro un pequeño ejemplo de como crear la SAS. Lo haremos a través de una aplicación de consola. Pasos:

1 – Nos crearemos una storage account en Azure. Si no sabes como hacerlo puedes seguir los pasos del siguiente link: <https://azure.microsoft.com/en-gb/documentation/articles/storage-create-storage-account/>

2 – Creamos una aplicación de consola y añadimos el paquete nuget MicrosoftAzure.Storage

<img class="alignnone wp-image-121" src="http://robertbermejo.azurewebsites.net/wp-content/uploads/2016/03/Nuget-300x181.jpg" alt="Nuget" width="819" height="494" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/03/Nuget-300x181.jpg 300w, https://www.robertbermejo.com/wp-content/uploads/2016/03/Nuget-768x463.jpg 768w, https://www.robertbermejo.com/wp-content/uploads/2016/03/Nuget-1024x618.jpg 1024w, https://www.robertbermejo.com/wp-content/uploads/2016/03/Nuget.jpg 1688w" sizes="(max-width: 819px) 100vw, 819px" /> 

3 -- Creamos el cliente de conexión con el blob storage

<pre style="text-align: left;" class="">var storageAccount = CloudStorageAccount.Parse("Your Blob Storage Connection String");
var CloudBloblClient = storageAccount.CreateCloudBlobClient();</pre>

4 -- Generamos la referencia al container que contiene nuestro blob, y en el caso de existir se crea.

<pre class="">var blobContainer = cloudBlobClient.GetContainerReference("ContainerName");
blobContainer.CreateIfNotExists();</pre>

5 -- Generamos la referencia al fichero que queremos tratar

<pre>var blockBlob =  blobContainer.GetBlockBlobReference("filename.extension");</pre>

6- Por último generamos la SAS

<pre>var sasToken = blockBlob.Container.GetSharedAccessSignature(new SharedAccessBlobPolicy(){Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write,SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-15),SharedAccessExpiryTime = DateTime.UtcNow.AddMinutes(15),});</pre>

En este caso solo le daremos al usuario permisos de lectura y escritura, y podrá acceder al enlace durante 15 minutos. Como podeis observar en el campo SharedAccesStartTime se pone un valor negativo, esto se hace por los posibles descuadres horarios que puede haber.

Los valores posibles en Permisions son: None, Read, Write, Delete, List, Add y Create.

El resultado es el siguiente:

<img class="alignnone wp-image-101" src="http://robertbermejo.azurewebsites.net/wp-content/uploads/2016/03/Result-300x183.jpg" alt="Result" width="680" height="415" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/03/Result-300x183.jpg 300w, https://www.robertbermejo.com/wp-content/uploads/2016/03/Result-1024x623.jpg 1024w, https://www.robertbermejo.com/wp-content/uploads/2016/03/Result.jpg 1671w" sizes="(max-width: 680px) 100vw, 680px" /> 

Una vez obtenida está SAS podemos llamar directamente al blob sotrage de la forma anteriormente mencionada para realizar las operaciones a las que se le ha dado permiso al usuario.

Como podéis ver se pueden tener nuestros archivos protegidos en el cloud y poder interactuar con ellos de una forma controlada.

Puedes bajar el código de: <https://github.com/bermejoblasco/SasExample>

Hasta el próximo post.