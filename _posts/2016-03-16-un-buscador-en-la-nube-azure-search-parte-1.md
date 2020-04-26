---
id: 491
title: 'Un buscador en la nube : Azure Search (Parte 1)'
date: 2016-03-16T12:14:21+01:00
author: rbermejo
layout: post
---
Hace muy poco, en el proyecto en el que actualmente estoy,el cliente nos pidió que quería que se pudiera buscar en el contenido de los documentos pdf&#8217;s que teníamos almacenados en el **Azure Blob Storage**, concretamente nos pido una búsqueda estilo &#8220;_Google_&#8220;.<!--break-->
Al escuchar esas palabras mi primera reacción mental fue: ¿y ahora que hago? ¿como hago esto en **Azure**?, mis miedos desaparición al terminar la reunión y sentarme a buscar una solución. A los cinco minutos había descubierto **Azure Search�** y a partir de ese momento comenzó a convertirse en uno de mis servicios favoritos� de **Azure.**

Así que voy a escribir una serie de posts explicando que es, para que sirve y como utilizarlo.

En el post de hoy voy a introducir **Azure Search.**

#### **¿Que es Azure Search?**

**Azure search** es un _cloud search-as-a-service_ (un servicio de búsqueda) que nos ayuda a integrar fácilmente búsquedas en nuestras aplicaciones (web, mobile, desktop&#8230;).

Una de las características más importantes que tiene es que está basado en _Lucene. [Lucene](https://lucene.apache.org/core/)_ es un librería que permite realizar búsquedas de texto, implementa un motor de búsqueda de alto rendimiento.

Azure Search nos permite realizar:

  * búsquedas **_full-text search_** &#8211;> realiza la búsqueda en todos los documentos existentes a partir de una palabra.
  * **_Suggestions_** &#8211;> Sugerencias para realizar el **_autocomplete_**.
  * Filtros &#8211;> Permite� refinar nuestras búsquedas.
  * Facetas (**_Facetes_**) &#8211;> Permite el refinamiento de la búsqueda mediante la selección de campos concretos. En la siguiente imagen se muestra un ejemplo de facetas.

<img class="wp-image-601 aligncenter" src="http://www.robertbermejo.com/wp-content/uploads/2016/03/facetes-300x184.png" alt="facetes" width="443" height="272" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/03/facetes-300x184.png 300w, https://www.robertbermejo.com/wp-content/uploads/2016/03/facetes-768x470.png 768w, https://www.robertbermejo.com/wp-content/uploads/2016/03/facetes-1024x627.png 1024w, https://www.robertbermejo.com/wp-content/uploads/2016/03/facetes.png 1680w" sizes="(max-width: 443px) 100vw, 443px" /> 

  * **_Highlighting_** &#8211;> Al mostrar los resultado pueden ver resaltados los campos especificados.
  * **_Geo-Spatial_** &#8211;> A partir de tu búsqueda permite mostrar las localizaciones más cercanas a tu posición que coincidan con tu búsqueda, como por ejemplo: restaurantes, tiendas&#8230;
  * **Azure Search** detecta entre 56 lenguajes el texto introducido en la búsqueda permitiendo inferir tiempos verbales, sintaxis&#8230;
  * Permite realizar consultas inteligentes con operadores: lógicos, de � sufijo o de precedencia. También y gracias _Lucene_ nos permite realizar consultas con expresiones regulares, por términos o por proximidad.
  * Cuando realizas las búsquedas puedes realizar paginación de los resultados, ordenación así como realizar ponderación sobre datos concretos con el fin de que aparezcan en los primeros puestos de los resultados.

#### **¿Como funciona?**

La forma de función es muy sencilla. Vamos a ver como funciona mediante un pequeño ejemplo:

  * Creamos el servicio en el portal.Vamos al portal, buscamos el servicio de **Azure Search** y lo creamos

.<img class="alignnone wp-image-681" src="http://www.robertbermejo.com/wp-content/uploads/2016/03/createAzureSearch1-300x183.png" alt="createAzureSearch1" width="387" height="236" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/03/createAzureSearch1-300x183.png 300w, https://www.robertbermejo.com/wp-content/uploads/2016/03/createAzureSearch1-768x468.png 768w, https://www.robertbermejo.com/wp-content/uploads/2016/03/createAzureSearch1-1024x624.png 1024w, https://www.robertbermejo.com/wp-content/uploads/2016/03/createAzureSearch1.png 1680w" sizes="(max-width: 387px) 100vw, 387px" />

Seleccionamos el plan que queremos según las necesidades que tengamos.

<img class="alignnone wp-image-691" src="http://www.robertbermejo.com/wp-content/uploads/2016/03/createAzureSearch2-300x183.png" alt="createAzureSearch2" width="382" height="233" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/03/createAzureSearch2-300x183.png 300w, https://www.robertbermejo.com/wp-content/uploads/2016/03/createAzureSearch2-768x468.png 768w, https://www.robertbermejo.com/wp-content/uploads/2016/03/createAzureSearch2-1024x624.png 1024w, https://www.robertbermejo.com/wp-content/uploads/2016/03/createAzureSearch2.png 1680w" sizes="(max-width: 382px) 100vw, 382px" /> 

Una vez seleccionamos el plan se creará el servicio.

<img class="alignnone wp-image-701" src="http://www.robertbermejo.com/wp-content/uploads/2016/03/createAzureSearch3-300x183.png" alt="createAzureSearch3" width="389" height="237" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/03/createAzureSearch3-300x183.png 300w, https://www.robertbermejo.com/wp-content/uploads/2016/03/createAzureSearch3-768x468.png 768w, https://www.robertbermejo.com/wp-content/uploads/2016/03/createAzureSearch3-1024x624.png 1024w, https://www.robertbermejo.com/wp-content/uploads/2016/03/createAzureSearch3.png 1680w" sizes="(max-width: 389px) 100vw, 389px" /> 

  * Crear los indices

Los índices es donde se guardará la información que añadiremos, sería como la base de datos del search, . La información que se guarda se llama _documents_.

Hay tres formas de crear índices:-

-Directamente en el portal.

-Por código .Net, usando la librería Microsoft.Azure.Search.

-Vía REST API.

  * Añadir documentos

Insertamos documentos, datos, a los índices de forma que podamos realizar búsquedas y ejecutar queries.

Existen dos formas de añadir o actualizar los documentos:

-Pull Model &#8211;> � Se configura para que los indices se actualicen bajo demanda o programada. Nos permite de forma sencilla indexar los cambios realizados sobre un **DocumentDB**, **SQLAzure** o **Azure Blob Storage.** Para más información :[� https://msdn.microsoft.com/library/azure/dn946891.aspx](https://msdn.microsoft.com/library/azure/dn946891.aspx)

-Push Model &#8211;> Se añaden o actualizan los documentos &#8220;manualmente&#8221; mediante el SDK de .net o REST API.

  * ¡A buscar!

Una vez añadidos los documentos en tus indices ya puedes realizar _queries_ de búsqueda via .NET SDK o REST API.

#### **Conclusiones**

Como podéis observar, **Azure Search�** es un servicio que nos da una gran potencia a la hora de añadir búsquedas inteligentes en nuestras aplicaciones sean del tipo que sean, realmente es asombroso y os ánimo a probarlo.

En el siguiente post entraré más en detalle de como crear indices, como añadirles documentos y como realizar las _queries_ correspondientes, así que ¡no os preocupéis que hay más!