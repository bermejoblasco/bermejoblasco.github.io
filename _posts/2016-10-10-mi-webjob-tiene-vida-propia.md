---
id: 5322
title: ¡Mi webjob tiene vida propia!
date: 2016-10-10T08:11:18+01:00
author: rbermejo
layout: post
---
Hace unos días uno de mis compañeros se me acercó y me dijo: &#8220;¡Mi _webjob_ tiene vida propia!&#8221;, os podéis imaginar mi cara de sorpresa ante tal afirmación.

Lo que le estaba sucediendo es que tenía un** _run a continuous webjob_** que cuando se ejecutaba lo hacía más de una vez. A mí� nunca me había sucedido tal cosa, o al menos no me había dado cuenta o he tenido mucha suerte de que no me sucediera que es lo más probable.<!--break-->

Después de estar investigando un rato, vimos que el problema estaba en que si tú **Azure Web App�** está corriendo en múltiples instancias el _webjob�_ corre en todas ellas. Lo más curioso es que esto solo sucede para este tipo de _webjobs_, si el _webjob_ es del tipo **_Triggered_** se ejecuta solo en una de las instancias elegida al azar.

Para solucionar esto hay dos alternativas:

  * Si creas el _webjob_ desde el portal de **azure** y subes el paquete, en la opción _scale_ debes seleccionar** _single instance._**

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/portalAzureWebJob.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/portalAzureWebJob.png" alt="portalAzureWebJob" width="660" height="397" /></a>

  * Si lo despliegas directamente de Visual Studio debes añadir un archivo en la raíz del proyecto del _webjob_ llamado� **settings.job** que debe contener la siguiente información:

<pre class="csharpcode">{ <span class="str">"is_singleton"</span>: <span class="kwrd">true</span> }</pre>

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/vsAzureWebJob.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/vsAzureWebJob.png" alt="vsAzureWebJob" width="660" height="397" /></a>

Así que vigilar vuestros _webjobs_ de cerca porque a veces son como los Gremlins y se reproducen.

Gracias a **[Roberto Grassi](https://twitter.com/robyenbarcelona)** por dar con el problema y dar vida a esta post.