---
id: 6031
title: Como obtener las variables de Azure Devops de una release
date: 2020-03-29T15:39:44+01:00
author: rbermejo
layout: post
published: true
---
En ocasiones nos encontramos que tenemos que ver todas la variables de una release de **Azure Devops** y verlo desde el portal puede ser una tarea complicada y tediosa, dado que la cantidad de variables que tienes es el número de variables multiplicado por el número de entornos que tengas.<!--break-->

Imaginaros 30 variables por 5 entornos, hablamos de 150 variables, por lo que mirarlo en el portal puede ser un poco tedioso

Para ello, una opción es bajarse estas variables y poder ver sus valores en el formatos que más nos interese.

En este post vamos a ver como podemos bajar las variables de una release mediante **Azure CLI**.

Lo primero que tenemos que hacer es instalarnos la extensión de **Azure Devops** para **Azure CLI**, para ello ejecutaremos la siguiente instrucción:

<pre class="wp-block-preformatted">az extension add --name azure-devops</pre>

Después haremos login:

<pre class="wp-block-preformatted">az login</pre>

Por último ejecutamos la siguiente intrucción:

<pre class="wp-block-preformatted">az pipelines release definition show --id {id} --query environments[?@.name=='{entorno}'].variables</pre>

Donde debemos sustituir {id} por el id de la release, se obtiene de la url de **Azure Devops**, y {entorno} por el nombre del entorno que queremos obtener.

Si utilizáis Powershell y queréis guardar estas variable en un fichero la sintaxis cambia un poco, quedaría así:

<pre class="wp-block-preformatted">az pipelines release definition show --id {id} --query environments[?"@.name=='{entorno}'"].variables | out-file C:\Projects\enviroments.json</pre>

Espero os pueda ayudar.