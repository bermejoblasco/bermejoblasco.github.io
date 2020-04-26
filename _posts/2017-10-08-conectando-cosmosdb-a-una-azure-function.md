---
id: 5937
title: Conectando CosmosDB a una Azure Function
date: 2017-10-08T18:26:15+01:00
author: rbermejo
layout: post
---
Vamos a ver como conectar **CosmosDB** a una **Azure Function** de forma sencilla.

Primero debemos tener creada una **Azure Function.**<!--break-->

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/blogfunction1.png" target="_blank" rel="attachment wp-att-2962 noopener"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/blogfunction1.png" alt="Creando una FunctionApp" width="660" height="397" /></a>

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/blogfunction2.png" target="_blank" rel="attachment wp-att-2962 noopener"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/blogfunction2.png" alt="FunctionApp creada" width="660" height="397" /></a>

Una vez creada creamos nuestro **CosmosDB**, en este caso elegimos **DocumentDB** API como modelo.

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/blogfunction3.png" target="_blank" rel="attachment wp-att-2962 noopener"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/blogfunction3.png" alt="Creando CosmosDB DocumentDB Api" width="660" height="397" /></a>

Creamos una _Database_ y una _Collection_

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/blogfunction4.png" target="_blank" rel="attachment wp-att-2962 noopener"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/blogfunction4.png" alt="Creando Collection & Database" width="660" height="397" /></a>

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/blogfunction5.png" target="_blank" rel="attachment wp-att-2962 noopener"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/blogfunction5.png" alt="Creando Collection & Database" width="660" height="397" /></a>

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/blogfunction6.png" target="_blank" rel="attachment wp-att-2962 noopener"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/blogfunction6.png" alt="Collection & Database creadas" width="660" height="397" /></a>

Ahora conectamos nuestro **CosmosDB** a una **Azure Function**.

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/blogfunction7.png" target="_blank" rel="attachment wp-att-2962 noopener"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/blogfunction7.png" alt="Conectando FunctionAPP to CosmosDB" width="660" height="397" /></a>

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/blogfunction8.png" target="_blank" rel="attachment wp-att-2962 noopener"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/blogfunction8.png" alt="Conectando FunctionAPP to CosmosDB" width="660" height="397" /></a>

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/blogfunction9.png" target="_blank" rel="attachment wp-att-2962 noopener"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/blogfunction9.png" alt="Conectando FunctionAPP to CosmosDB" width="660" height="397" /></a>

Ahora añadimos un elemento en nuestra _collection_ y podemos ver como se ejecuta nuestra **function**.

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/blogfunction12.png" target="_blank" rel="attachment wp-att-2962 noopener"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/blogfunction12.png" alt="Añadiendo elementos a DocumentDB" width="660" height="397" /></a>

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/blogfunction10.png" target="_blank" rel="attachment wp-att-2962 noopener"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/blogfunction10.png" alt="Ver Logs" width="660" height="397" /></a>

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/blogfunction11.png" target="_blank" rel="attachment wp-att-2962 noopener"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/blogfunction11.png" alt="Verificando" width="660" height="397" /></a>

¿Fácil verdad? A partir de aquí a implementar vuestra **function**.

¡Espero os sirva de ayuda!

&nbsp;

&nbsp;