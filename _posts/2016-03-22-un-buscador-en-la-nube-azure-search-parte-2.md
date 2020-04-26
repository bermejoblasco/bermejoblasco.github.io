---
id: 941
title: 'Un buscador en la nube : Azure Search (Parte 2)'
date: 2016-03-22T16:08:06+01:00
author: rbermejo
layout: post
---
En mi anterior <a href="http://www.robertbermejo.com/un-buscador-en-la-nube-azure-search-parte-1/" target="_blank">post� </a>os daba una visión general de que era y como funcionaba **Azure Search**, por lo que en las siguientes lineas vamos a ver más en detalle como crear índices, añadir documentos y realizar _queries_.<!--break-->

El objetivo es ver que opciones tenemos a la hora de realizar estos pasos y ver en detalle como realizar todas estas operaciones mediante el **SDK de .Net** para **Azure Search** de forma _custom_, es decir, sin enlazar ninguna fuente da datos.

Recordar que todas las operaciones también las podéis realizar mediante la REST Api.

## <span style="text-decoration: underline;"><strong>Creación de índices</strong></span>

Un índice se compone de _fields_ que pueden ser de los tipos siguientes:

<table style="height: 722px;" width="617">
  <tr>
    <td>
      <strong>Edm.String</strong>
    </td>
    
    <td>
      Estándar string
    </td>
  </tr>
  
  <tr>
    <td>
      <strong>Collection(Edm.String)</strong>
    </td>
    
    <td>
      Lista de string. El tamaño máximo de la colección es de 16 MB.
    </td>
  </tr>
  
  <tr>
    <td>
      <strong>Edm.Boolean</strong>
    </td>
    
    <td>
      Valores: True/False
    </td>
  </tr>
  
  <tr>
    <td>
      <strong>Edm.Int32</strong>
    </td>
    
    <td>
      Entero de 32-bits
    </td>
  </tr>
  
  <tr>
    <td>
      <strong>Edm.Int64</strong>
    </td>
    
    <td>
      Entero de 64-bits
    </td>
  </tr>
  
  <tr>
    <td>
      <strong>Edm.Double</strong>
    </td>
    
    <td>
      Número con precisión doble. En .Net sería el tipo Double.
    </td>
  </tr>
  
  <tr>
    <td>
      <strong>Edm.DateTimeOffset</strong>
    </td>
    
    <td>
      Fecha representada en formato OData V4:� <span class="code">yyyy-MM-ddTHH:mm:ss.fffZ</span> or <span class="code">yyyy-MM-ddTHH:mm:ss.fff[+|-]HH:mm</span>
    </td>
  </tr>
  
  <tr>
    <td>
      <strong>Edm.GeographyPoint</strong>
    </td>
    
    <td>
      Representa un punto en el mapa mundial.
    </td>
  </tr>
</table>

Es necesario añadir atributos a los _fields_. Añadiendo esto atributos lo que hacemos en darle un comportamiento dentro del servicio de forma que este pueda interactuar� con ellos� de la forma correcta al realizar las _queries_.

Estos atributos son:

  * **Retrievable** &#8211;> El _field_ será respuesta a la _query_ lanzada.
  * **Filterable**, **Sortable**, y� **Facetable** &#8211;> Indica como se puede usar el _field_ en un filtro, ordenación o _facet_.
  * **Searchable** &#8211;> el campo se incluye en la búsqueda _full text_. Solo los campos de tipo EDM.String deberían ser de este tipo.

Tenemos tres formas de crear índices y sus _fields_:

  1. Desde el portal.
  2. Vinculando una fuente de datos.
  3. Mediante código (**SDK .Net** o **REST API**)

###### **Desde el portal**

Entramos en nuestro **Azure Search** mediante el portal y añadimos un **índice**.

<a href="http://www.robertbermejo.com/wp-content/uploads/2016/03/CreateIndex1-1.png" rel="attachment wp-att-1881"><img class="alignnone wp-image-1881 size-large" src="http://www.robertbermejo.com/wp-content/uploads/2016/03/CreateIndex1-1-1024x576.png" alt="CreateIndex1" width="660" height="371" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/03/CreateIndex1-1-1024x576.png 1024w, https://www.robertbermejo.com/wp-content/uploads/2016/03/CreateIndex1-1-300x169.png 300w, https://www.robertbermejo.com/wp-content/uploads/2016/03/CreateIndex1-1-768x432.png 768w, https://www.robertbermejo.com/wp-content/uploads/2016/03/CreateIndex1-1.png 1366w" sizes="(max-width: 660px) 100vw, 660px" /></a>

Una vez creado el **índice** añadimos sus _fields_

<a href="http://www.robertbermejo.com/wp-content/uploads/2016/03/CreateIndex2.png" rel="attachment wp-att-1751"><img class="alignnone wp-image-1751 size-large" src="http://www.robertbermejo.com/wp-content/uploads/2016/03/CreateIndex2-1024x576.png" alt="CreateIndex2" width="660" height="371" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/03/CreateIndex2-1024x576.png 1024w, https://www.robertbermejo.com/wp-content/uploads/2016/03/CreateIndex2-300x169.png 300w, https://www.robertbermejo.com/wp-content/uploads/2016/03/CreateIndex2-768x432.png 768w, https://www.robertbermejo.com/wp-content/uploads/2016/03/CreateIndex2.png 1366w" sizes="(max-width: 660px) 100vw, 660px" /></a>

A partir de ese momento ya podríamos añadir documentos a nuestro índice.

###### **Vinculando una fuente de datos**

Podemos vincular tres fuentes de datos:

  * **SQLAzure o SQLServer in VM** &#8211;> Vinculamos una tabla concreta a un índice. Nos crea los _fields_ con las columnas de la tabla y debemos sincronizar los datos mediante un _scheduler�_ � que puede lanzarse una vez, a una hora concreta, diariamente o _custom_. Más información:� <a href="https://azure.microsoft.com/en-us/documentation/articles/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28/" target="_blank">https://azure.microsoft.com/en-us/documentation/articles/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28/</a>
  * **DocumentDB** &#8211;> En el caso de **DocumentDB�** en vez de vincular una tabla de BBDD, se vincula una _collection_ de un� _database_ de **DocumentDB**. Como en el cso de SQLAzure se deben sincronizar los datos y las opciones son las mismas que para **SQLAzure**. Más información en:<a href="https://azure.microsoft.com/en-us/documentation/articles/documentdb-search-indexer/" target="_blank">� https://azure.microsoft.com/en-us/documentation/articles/documentdb-search-indexer/</a>
  * **Blob storage (en preview)** &#8211;> Igual que los anteriores, vinculas tu blob storage, crea los índices y sus _fields_ y planificas la actualización de los datos. Los tipos de ficheros que actulmente acepta son: PDF, Microsoft Office formats, HTML, XML, ZIP, EML y _Plain text files_. Más infromación:<a href="https://azure.microsoft.com/en-us/documentation/articles/search-howto-indexing-azure-blob-storage/" target="_blank">� https://azure.microsoft.com/en-us/documentation/articles/search-howto-indexing-azure-blob-storage/</a>

No entraré más en detalle en estas opciones dado que mi experiencia me indica que la mejor forma de tratar con índices y sus _fields_ es hacerlo� manualmente. De esta forma no estas vinculado a una estructura concreta y puedes actualizar los documentos con tus propios procesos manipulando aquella información que necesites para guardarla como la necesites.

###### **Mediante código**

Lo primero que debemos hacer es añadir el paquete� **NuGet** de **Azure Search**.

<a href="http://www.robertbermejo.com/wp-content/uploads/2016/03/nuget1-1.png" rel="attachment wp-att-1951"><img class="alignnone wp-image-1951 size-large" src="http://www.robertbermejo.com/wp-content/uploads/2016/03/nuget1-1-1024x618.png" alt="nuget1" width="660" height="398" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/03/nuget1-1-1024x618.png 1024w, https://www.robertbermejo.com/wp-content/uploads/2016/03/nuget1-1-300x181.png 300w, https://www.robertbermejo.com/wp-content/uploads/2016/03/nuget1-1-768x463.png 768w, https://www.robertbermejo.com/wp-content/uploads/2016/03/nuget1-1.png 1688w" sizes="(max-width: 660px) 100vw, 660px" /></a>

Declaramos el índice y sus _fields._

<pre class="brush: csharp; title: ; notranslate" title="">//Index Name
var indexName = "example";
//Azure search credentials
SearchServiceClient azureSearchService = new SearchServiceClient("your azure search name","your azure search key");
//Get azureSearch client for our index
SearchIndexClient indexClient = azureSearchService.Indexes.GetClient(indexName);

//Delete index if exist 
if (azureSearchService.Indexes.Exists(indexName))
{
   azureSearchService.Indexes.Delete(indexName);
}
 
//Create Index Model
Index indexModel = new Index()
{
  Name = indexName,
  Fields = new[]
{
 new Field("ISBN", DataType.String) { IsKey = true, IsRetrievable = true, IsFacetable = false },
new Field("Titulo", DataType.String) {IsRetrievable = true, IsSearchable = true, IsFacetable = false },
new Field("Autores", DataType.Collection(DataType.String)) {IsSearchable = true, IsRetrievable = true, IsFilterable = true, IsFacetable = false },
new Field("FechaPublicacion", DataType.DateTimeOffset) { IsFilterable = true, IsRetrievable = false, IsSortable = true, IsFacetable = false },
new Field("Categoria", DataType.String) { IsFacetable = true, IsRetrievable = true }
 }
};
</pre>

&nbsp;

Ahora creamos el índice:

<pre class="brush: csharp; title: ; notranslate" title="">//Create Index in AzureSearch
var resultIndex = azureSearchService.Indexes.Create(indexModel);
</pre>

<p class="">
  Ahora ya tenemos creado el índice como podemos ver en el portal:
</p>

<p class="">
  <a href="http://www.robertbermejo.com/wp-content/uploads/2016/03/createindex3-1.png" rel="attachment wp-att-1901"><img class="alignnone wp-image-1901 size-large" src="http://www.robertbermejo.com/wp-content/uploads/2016/03/createindex3-1-1024x576.png" alt="createindex3" width="660" height="371" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/03/createindex3-1-1024x576.png 1024w, https://www.robertbermejo.com/wp-content/uploads/2016/03/createindex3-1-300x169.png 300w, https://www.robertbermejo.com/wp-content/uploads/2016/03/createindex3-1-768x432.png 768w, https://www.robertbermejo.com/wp-content/uploads/2016/03/createindex3-1.png 1366w" sizes="(max-width: 660px) 100vw, 660px" /></a>
</p>

<p class="">
  <a href="http://www.robertbermejo.com/wp-content/uploads/2016/03/createindex4-1.png" rel="attachment wp-att-1911"><img class="alignnone wp-image-1911 size-large" src="http://www.robertbermejo.com/wp-content/uploads/2016/03/createindex4-1-1024x546.png" alt="createindex4" width="660" height="352" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/03/createindex4-1-1024x546.png 1024w, https://www.robertbermejo.com/wp-content/uploads/2016/03/createindex4-1-300x160.png 300w, https://www.robertbermejo.com/wp-content/uploads/2016/03/createindex4-1-768x409.png 768w, https://www.robertbermejo.com/wp-content/uploads/2016/03/createindex4-1.png 1366w" sizes="(max-width: 660px) 100vw, 660px" /></a>
</p>

## <span style="text-decoration: underline;"><strong>Añadiendo documentos</strong></span>

Una vez ya tenemos creado el índice y parametrizados sus índices podemos añadir documentos al índice.

Lo primero que debemos hacer es crear una clase con los mismos campos y tipos que los _fields�_ de mi índice, en nuestro ejemplo.

<pre class="brush: csharp; title: ; notranslate" title="">public class BookModel
{
 public string ISBN { get; set; }
 public string Titulo { get; set; }
 public List&lt;string&gt; Autores { get; set; }
 public DateTimeOffset FechaPublicacion { get; set; }
 public string Categoria { get; set; }
}
</pre>

Añadimos documentos a nuestro índice.

<pre class="brush: csharp; title: ; notranslate" title="">//De nuestro modelo obtenemos varios objetos a añadir.
var listBooks = new BookModel().GetBooks();
//Add documents in our Index
azureSearchService.Indexes.GetClient(indexName).Documents.Index(IndexBatch.MergeOrUpload&lt;BookModel&gt;(listBooks));

</pre>

Si entramos en el portal y lanzamos una query veremos los documentos.

[<img class="alignnone wp-image-1531 size-large" src="http://www.robertbermejo.com/wp-content/uploads/2016/03/searchportal-1024x576.png" alt="searchportal" width="660" height="371" />](http://www.robertbermejo.com/wp-content/uploads/2016/03/searchportal.png)

## <span style="text-decoration: underline;"><strong>Realizando queries</strong></span>

Como hemos visto en el apartado anterior, podemos lazar _queries_ desde el portal de forma que podemos realizar validación de datos.

Para realizar búsquedas necesitamos dos elementos:

  * El texto a buscar. Se buscará el texto en aquellos _fields<del></del>_ que hayamos indicado que son _Searchables_
  * Parámetros de búsqueda --> Valores de filtro, _facet_, ordenación, _skip_, _take, select..._En el siguiente link teneis toda la información de que podeis hacer:� <a href="https://msdn.microsoft.com/en-us/library/azure/dn798927.aspx" target="_blank">https://msdn.microsoft.com/en-us/library/azure/dn798927.aspx</a>

En el siguiente código vemos como añadir filtros, facets y ordenar por un campo.

<pre class="brush: csharp; title: ; notranslate" title="">// Execute search based on search text and optional filter
var sp = new SearchParameters();
//Add Filter
if (!String.IsNullOrEmpty(filter))
{
	sp.Filter = filter;
}
//Order
if(order!=null  && order.Count &gt; 0)
{
	sp.OrderBy = order;
}
//facets
if (facets != null && facets.Count &gt;; 0)
{
	sp.Facets = facets;
}
//Search
DocumentSearchResult&lt;BookModel&gt; response = indexClient.Documents.Search&lt;BookModel&gt;;(searchText, sp);
</pre>

&nbsp;

Por ejemplo si buscamos la palabra cloud obtenemos� el siguiente resultado:

[<img class="alignnone wp-image-1422 size-large" src="http://www.robertbermejo.com/wp-content/uploads/2016/03/searchcloud-1024x576.png" alt="searchcloud" width="660" height="371" />](http://www.robertbermejo.com/wp-content/uploads/2016/03/searchcloud.png)

Si la palabra de búsqueda es todo (*) y filtramos por un autor:

[<img class="alignnone wp-image-1441 size-large" src="http://www.robertbermejo.com/wp-content/uploads/2016/03/filterautor-1024x576.png" alt="filterautor" width="660" height="371" />](http://www.robertbermejo.com/wp-content/uploads/2016/03/filterautor.png)

Si la palabra de búsqueda es todo (*) y � ordenamos por Fecha de publicación:

[<img class="alignnone wp-image-1431 size-large" src="http://www.robertbermejo.com/wp-content/uploads/2016/03/fechapublicacion-1024x546.png" alt="fechapublicacion" width="660" height="352" />](http://www.robertbermejo.com/wp-content/uploads/2016/03/fechapublicacion.png)

Si la palabra de búsqueda es todo (*) y traemos la facet de categorias

[<img class="alignnone wp-image-1401 size-large" src="http://www.robertbermejo.com/wp-content/uploads/2016/03/categorias-1024x576.png" alt="categorias" width="660" height="371" />](http://www.robertbermejo.com/wp-content/uploads/2016/03/categorias.png)

### Conclusiones

La idea de este post es que os hagáis una idea de toda la potencia que tiene **Azure Search** y mostraros una pequeñas pinceladas de lo que se puede llegar ha hacer. Como habéis podido comprobar es un servicio fácil de configurar y de trabajar con él con una gran variedad de opciones y configuraciones para poder ajustarlo a nuestras necesidades.

En el próximo post realizaré la última entrada sobre **Azure Search** donde entraremos más en detalle� _scoring_ y _suggestions_.

El código lo podéis descargar y ver de:� <https://github.com/bermejoblasco/AzureSearch>