---
id: 2301
title: 'Un buscador en la nube : Azure Search (y 3)'
date: 2016-04-01T11:14:44+01:00
author: rbermejo
layout: post
---
Finalmente llegamos al fin de esta saga de tres partes sobre **Azure Search**, no ha sido tan duro ¿no?.<!--break-->

Recopilando un poco, en mi <a href="http://www.robertbermejo.com/un-buscador-en-la-nube-azure-search-parte-1/" target="_blank" rel="noopener noreferrer">primer post </a>daba una visión general de que era y para que servía **Azure Search**. En el <a href="http://www.robertbermejo.com/un-buscador-en-la-nube-azure-search-parte-2/" target="_blank" rel="noopener noreferrer">siguiente post</a>� vimos como crear índices, añadirle documentos y operar sobre estos con el fin de obtener las búsquedas requeridas.

En esta última entrega, el objetivo es conocer dos operaciones que nos permiten sacar el máximo partido a **Azure Search**. Hoy conoceremos las� **Suggestions** y el **Scoring**.

Para continuar con el hilo de los anteriores posts, los ejemplos que os mostraré los realizaré mediante el **SDK** de **.NET** y continuando con el ejemplo del <a href="http://www.robertbermejo.com/un-buscador-en-la-nube-azure-search-parte-2/" target="_blank" rel="noopener noreferrer">post anterior</a>. Recordad que todas las operaciones� también se pueden hacer vía **RESTAPI**, podéis ver como hacerlo en los siguientes links:

**Suggestions** &#8211;� <a href="https://msdn.microsoft.com/en-us/library/azure/dn798936.aspx?f=255&MSPPError=-2147217396" target="_blank" rel="noopener noreferrer">https://msdn.microsoft.com/en-us/library/azure/dn798936.aspx?f=255&MSPPError=-2147217396</a>

**Scoring** &#8211;<a href="https://msdn.microsoft.com/en-us/library/azure/dn798928.aspx" target="_blank" rel="noopener noreferrer">� https://msdn.microsoft.com/en-us/library/azure/dn798928.aspx</a>

## <span style="text-decoration: underline;"><strong>Suggestions</strong></span>

Cuando un usuario quiere buscar algo en un _web site_ o una _app mobile_ lo primero que hacen es ir al buscador de la aplicación. Por esta razón el buscador debe ser efectivo y ayudar al usuario a encontrar lo que busca. Una forma de ayudar al usuario es mediante sugerencias o auto-complete.

Podemos ver un ejemplo en la página de amazon y poniendo en el buscador la palabra &#8220;wind&#8221;, el resultado mostrado es el siguiente:

<a href="http://www.robertbermejo.com/wp-content/uploads/2016/04/amazonSearch.png" target="_blank" rel="attachment wp-att-2601 noopener noreferrer"><img class="alignnone wp-image-2601 size-large" src="http://www.robertbermejo.com/wp-content/uploads/2016/04/amazonSearch-1024x616.png" alt="amazonSearch" width="660" height="397" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/04/amazonSearch-1024x616.png 1024w, https://www.robertbermejo.com/wp-content/uploads/2016/04/amazonSearch-300x180.png 300w, https://www.robertbermejo.com/wp-content/uploads/2016/04/amazonSearch-768x462.png 768w, https://www.robertbermejo.com/wp-content/uploads/2016/04/amazonSearch.png 1680w" sizes="(max-width: 660px) 100vw, 660px" /></a>

###### **¿Como configuramos las suggestions en Azure Search?**

Cuando definimos el índice debemos informar el campo **Suggesters**:

<pre class="brush: csharp; title: ; notranslate" title="">//Define SuggesterList
 List&lt;Suggester&gt; suggesterList = new List&lt;Suggester&gt;();
    suggesterList.Add(new Suggester() { Name = "suggester", SearchMode = SuggesterSearchMode.AnalyzingInfixMatching, SourceFields = new List&lt;string&gt;() { "Titulo", "Autores" } });
//Create Index Model
Index indexModel = new Index()
{
 Name = indexName,
 Fields = new[]
 {
 new Field("ISBN", DataType.String) { IsKey = true, IsRetrievable = true, IsFacetable = false },
 new Field("Titulo", DataType.String) {IsRetrievable = true, IsSearchable = true, IsFacetable = false },
 new Field("Autores", DataType.Collection(DataType.String)) {IsSearchable = true, IsRetrievable = true, IsFilterable = true, IsFacetable = false },
 new Field("FechaPublicacion", DataType.DateTimeOffset) { IsFilterable = true, IsRetrievable = false, IsSortable = true, IsFacetable = false },
 new Field("Categoria", DataType.String) { IsFacetable = true, IsFilterable= true, IsRetrievable = true }
 },
 //Add Suggesters
 Suggesters = suggesterList
};
</pre>

Como podéis observar **Suggesters** es una lista de tipo **Suggester**. Un **Suggester** se define de la siguiente forma:

<span style="text-decoration: underline;">Name</span>: Nombre del **Suggester**. Identifica el **Suggester** que queremos definir.

<span style="text-decoration: underline;">SearchMode</span>: que es del tipo� **SuggesterSearchMode**� que es un **enum�** con una sola� opción� **AnalyzingInfixMatching**.� **AnalyzingInfixMatching****�** realiza el &#8220;_matching_&#8221; de frases al principio o en medio de las sentencias.

<span style="text-decoration: underline;">SourceFields</span>:� Que es una lista de **strings** donde se indican los indices sobre los cuales queremos obtener sugerencias. Estos indices deben ser de los tipos **Edm.String** o **Collection(Edm.String)**

Si vamos al portal podemos ver como se ha configurado las **suggestions**.

<a href="http://www.robertbermejo.com/wp-content/uploads/2016/04/Suggesters.png" target="_blank" rel="attachment wp-att-2621 noopener noreferrer"><img class="alignnone wp-image-2621 size-large" src="http://www.robertbermejo.com/wp-content/uploads/2016/04/Suggesters-1024x616.png" alt="Suggesters" width="660" height="397" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/04/Suggesters-1024x616.png 1024w, https://www.robertbermejo.com/wp-content/uploads/2016/04/Suggesters-300x180.png 300w, https://www.robertbermejo.com/wp-content/uploads/2016/04/Suggesters-768x462.png 768w, https://www.robertbermejo.com/wp-content/uploads/2016/04/Suggesters.png 1680w" sizes="(max-width: 660px) 100vw, 660px" /></a>

###### **¿COMO Utilizarlo?**

Lo que debemos hacer es llamar al método _Suggest_ de Documents.

Esté método consta de cuatro parámetros, dos obligatorios, y dos opcioneales:

<span style="text-decoration: underline;">searchText</span>: Campo obligatorio. El texto a partir del cual queremos crear las sugerencias.

<span style="text-decoration: underline;">suggesterName</span>: Campo obligatorio. El _suggester_ que queremos utilizar. Campo Name de la clase **Suggester** anteriormente mencionada.

<span style="text-decoration: underline;">suggestParameters</span>:� Campo opcional.� De tipo� **SuggestParameters.�** Este parámetro nos permite configurar como queremos que funcione la búsqueda de las _suggestions.�_ 

<span style="text-decoration: underline;">searchRequestOptions</span>:� Campo opcional. De tipo� **SearchRequestOptions**. Este campo nos ayuda a la hora de realizar _Debugging_, permitiendo seguir las trazas a partir del GUID generado.

**Propiedades de SuggestParameters**

<span style="text-decoration: underline;">Filter</span>: Filtro a aplicar para acotar los documentos donde realizar la búsqueda de sugerencias.

<span style="text-decoration: underline;">HighlightPreTag</span>: _String�_ que se pondrá delante de las coincidencias.

<span style="text-decoration: underline;">HighlightPostTag</span>: � _String_ que se anexa a las coincidencias:

<span style="text-decoration: underline;">MinimumCoverage</span>: Campo con valor entre 0 y 100 que indica el porcentaje de indices que debe ser abarcada por la query de sugerencias para que su resultado sea satisfactorio.

<span style="text-decoration: underline;">OrderBy</span>: Orden en el cuál deben ser devueltos los resultados.

<span style="text-decoration: underline;">SearchFields</span>: Dentro del **Suggester** se define� sobre que indices operará la búsqueda de sugerencias, con este campo podemos seleccionar solo alguno de esos indices. Por defecto si no se informa busca en todos los índices parametrizados� en el **Suggester**. Por defecto su valor es� 80.

<span style="text-decoration: underline;">Select</span>: Solo devolver algunos de los campos. Hay que indicar que cuando se realiza una búsqueda de sugerencias, además de devolver las sugerencias, también devuelve todos aquellos documentos que coincides con estas sugerencias, por ello se permite realizar las operaciones de OrderBy, Filter, y Select.

<span style="text-decoration: underline;">Top</span>: El número de sugerencias a devolver.

<span style="text-decoration: underline;">UseFuzzyMatching</span>: Por defecto su valor es **false**. Si le asignamos el valor **true�** se encontrarán sugerencias aunque no coincidan todas las palabras, es decir, realizar aproximaciones en la búsqueda.

Siguiendo con el ejemplo, aquí tenemos � un ejemplo de como realizar una búsqueda de sugerencias sin fuzzy y buscando sugerencias solo en autores:

<pre class="brush: csharp; title: ; notranslate" title="">var indexName = "example";                
SearchServiceClient azureSearchService = new SearchServiceClient("YourAzureSearchServiceName", "YourAzureSearchKey"));
SearchIndexClient indexClient = azureSearchService.Indexes.GetClient(indexName);

//Suggest
Console.WriteLine("\n{0}", "Suggest by Ro without fuzzy \n");
SuggestionSearch(indexClient, suggesterText: "Ro", suggester: "suggester", fuzzy:false);

private static void SuggestionSearch(SearchIndexClient indexClient, string suggesterText, string suggester, bool fuzzy)
{
	SuggestParameters suggestParameters = new SuggestParameters()
	{                
		Top = 10,
		UseFuzzyMatching = fuzzy,                
		SearchFields = new List&amp;amp;amp;amp;amp;amp;lt;string&amp;amp;amp;amp;amp;amp;gt; { "Autores" }
	};          

	DocumentSuggestResult response = indexClient.Documents.Suggest(suggesterText, suggester, suggestParameters, searchRequestOptions: new SearchRequestOptions(Guid.NewGuid()));
	foreach (var suggestion in response.Results)
	{                
		Console.WriteLine("Suggestion: " + suggestion.Text);
	}
}
</pre>

Resultado:

<a href="http://www.robertbermejo.com/wp-content/uploads/2016/04/suggestionwithoutfuzzy.png" target="_blank" rel="attachment wp-att-2641 noopener noreferrer"><img class="alignnone wp-image-2641 size-large" src="http://www.robertbermejo.com/wp-content/uploads/2016/04/suggestionwithoutfuzzy-1024x407.png" alt="suggestionwithoutfuzzy" width="660" height="262" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/04/suggestionwithoutfuzzy-1024x407.png 1024w, https://www.robertbermejo.com/wp-content/uploads/2016/04/suggestionwithoutfuzzy-300x119.png 300w, https://www.robertbermejo.com/wp-content/uploads/2016/04/suggestionwithoutfuzzy-768x305.png 768w, https://www.robertbermejo.com/wp-content/uploads/2016/04/suggestionwithoutfuzzy.png 1650w" sizes="(max-width: 660px) 100vw, 660px" /></a>

Ahora realizamos la misma búsqueda pero con fuzzy.

<pre class="brush: csharp; title: ; notranslate" title="">var indexName = "example";                
SearchServiceClient azureSearchService = new SearchServiceClient("YourAzureSearchServiceName", "YourAzureSearchKey"));
SearchIndexClient indexClient = azureSearchService.Indexes.GetClient(indexName);

//Suggest
Console.WriteLine("\n{0}", "Suggest by Ro with fuzzy \n");
SuggestionSearch(indexClient, suggesterText: "Ro", suggester: "suggester", fuzzy: true);

private static void SuggestionSearch(SearchIndexClient indexClient, string suggesterText, string suggester, bool fuzzy)
{
	SuggestParameters suggestParameters = new SuggestParameters()
	{                
		Top = 10,
		UseFuzzyMatching = fuzzy,                
		SearchFields = new List&amp;amp;amp;amp;amp;amp;lt;string&amp;amp;amp;amp;amp;amp;gt; { "Autores" }
	};          

	DocumentSuggestResult response = indexClient.Documents.Suggest(suggesterText, suggester, suggestParameters, searchRequestOptions: new SearchRequestOptions(Guid.NewGuid()));
	foreach (var suggestion in response.Results)
	{                
		Console.WriteLine("Suggestion: " + suggestion.Text);
	}
}
</pre>

Vemos que nos devuelve muchos más resultados.

<a href="http://www.robertbermejo.com/wp-content/uploads/2016/04/suggestionwithfuzzy.png" target="_blank" rel="attachment wp-att-2631 noopener noreferrer"><img class="alignnone wp-image-2631 size-large" src="http://www.robertbermejo.com/wp-content/uploads/2016/04/suggestionwithfuzzy-1024x407.png" alt="suggestionwithfuzzy" width="660" height="262" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/04/suggestionwithfuzzy-1024x407.png 1024w, https://www.robertbermejo.com/wp-content/uploads/2016/04/suggestionwithfuzzy-300x119.png 300w, https://www.robertbermejo.com/wp-content/uploads/2016/04/suggestionwithfuzzy-768x305.png 768w, https://www.robertbermejo.com/wp-content/uploads/2016/04/suggestionwithfuzzy.png 1650w" sizes="(max-width: 660px) 100vw, 660px" /></a>

Como podéis observar es muy fácil obtener sugerencias y enriquecer nuestras aplicaciones de forma rápida y sencilla.

## <span style="text-decoration: underline;"><strong>Scoring</strong></span>

En algunos casos, necesitamos que algunos resultados salgan antes que otros. Por ejemplo, imaginemos una tienda on-line que quiere promocionar un nuevo producto, para ello le interesa que salga en las primeras posiciones cuando los usuarios realicen búsquedas, pues **Azure Search** nos da esta opción mediante el **scoring**.

**Scoring profiles**� nos permite personalizar el cálculo de **scoring** en las búsquedas.

Si realizamos una búsqueda vemos que los resultados devuelven un campo llamado� **Score**( si la búsqueda la hacemos desde el portal el campo se llama **@search.score**), pues al añadir un **Scoring profil**e lo que estamos haciendo es darle peso a este campo, de forma que primero muestra los que más peso tienen.

<pre class="brush: csharp; title: ; notranslate" title="">SearchServiceClient azureSearchService = new SearchServiceClient(ConfigurationManager.AppSettings["AzureSearchServiceName"], new SearchCredentials(ConfigurationManager.AppSettings["AzureSearchKey"]));
SearchIndexClient indexClient = azureSearchService.Indexes.GetClient(indexName);
var listBooks = new BookModel().GetBooks();

if (azureSearchService.Indexes.Exists(indexName))
{
	azureSearchService.Indexes.Delete(indexName);
}

//Define SuggesterList
List&amp;amp;lt;Suggester&amp;amp;gt; suggesterList = new List&amp;amp;lt;Suggester&amp;amp;gt;();
suggesterList.Add(new Suggester() { Name = "suggester", SearchMode = SuggesterSearchMode.AnalyzingInfixMatching, SourceFields = new List&amp;amp;lt;string&amp;amp;gt;() { "Titulo", "Autores" } });
//Create Index Model
Index indexModel = new Index()
{
	Name = indexName,
	Fields = new[]
	{
		new Field("ISBN", DataType.String) { IsKey = true, IsRetrievable = true, IsFacetable = false },
		new Field("Titulo", DataType.String) {IsRetrievable = true, IsSearchable = true, IsFacetable = false },
		new Field("Autores", DataType.Collection(DataType.String)) {IsSearchable = true, IsRetrievable = true, IsFilterable = true, IsFacetable = false },
		new Field("FechaPublicacion", DataType.DateTimeOffset) { IsFilterable = true, IsRetrievable = false, IsSortable = true, IsFacetable = false },
		new Field("Categoria", DataType.String) { IsFacetable = true, IsFilterable= true, IsRetrievable = true }

	},
	//Add Suggesters
	Suggesters = suggesterList
};

//Create Index in AzureSearch
var resultIndex = azureSearchService.Indexes.Create(indexModel);
//Add documents in our Index
azureSearchService.Indexes.GetClient(indexName).Documents.Index(IndexBatch.MergeOrUpload&amp;amp;lt;BookModel&amp;amp;gt;(listBooks));
//Search by word
Console.WriteLine("{0}", "Searching documents 'Cloud'...\n");
Search(indexClient, searchText: "Cloud");

 private static void Search(SearchIndexClient indexClient, string searchText, string filter = null, List&amp;amp;lt;string&amp;amp;gt; order = null, List&amp;amp;lt;string&amp;amp;gt; facets = null)
{
	// Execute search based on search text and optional filter
	var sp = new SearchParameters();

	//Add Filter
	if (!String.IsNullOrEmpty(filter))
	{
		sp.Filter = filter;
	}

	//Order
	if(order!=null &amp;amp;amp;&amp;amp;amp; order.Count&amp;amp;gt;0)
	{
		sp.OrderBy = order;
	}

	//facets
	if (facets != null &amp;amp;amp;&amp;amp;amp; facets.Count &amp;amp;gt; 0)
	{
		sp.Facets = facets;
	}            

	//Search
	DocumentSearchResult&amp;amp;lt;BookModel&amp;amp;gt; response = indexClient.Documents.SearchAsync&amp;amp;lt;BookModel&amp;amp;gt;(searchText, sp).Result;
	foreach (SearchResult&amp;amp;lt;BookModel&amp;amp;gt; result in response.Results)
	{                
		Console.WriteLine(result.Document + " Score: " + result.Score);
	}
	if (response.Facets != null)
	{
		foreach (var facet in response.Facets)
		{
			Console.WriteLine("Facet Name: " + facet.Key);
			foreach(var value in facet.Value)
			{
				Console.WriteLine("Value :" + value.Value + " - Count: " + value.Count);
			}
		}
	}
}
</pre>

Si aplicamos el código anterior el resultado mostrado es:

<a href="http://www.robertbermejo.com/wp-content/uploads/2016/04/defaultScore.png" target="_blank" rel="attachment wp-att-2651 noopener noreferrer"><img class="alignnone wp-image-2651 size-large" src="http://www.robertbermejo.com/wp-content/uploads/2016/04/defaultScore-1024x616.png" alt="defaultScore" width="660" height="397" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/04/defaultScore-1024x616.png 1024w, https://www.robertbermejo.com/wp-content/uploads/2016/04/defaultScore-300x180.png 300w, https://www.robertbermejo.com/wp-content/uploads/2016/04/defaultScore-768x462.png 768w, https://www.robertbermejo.com/wp-content/uploads/2016/04/defaultScore.png 1678w" sizes="(max-width: 660px) 100vw, 660px" /></a>

En este caso no hemos definido ningún **Scoring profile** pero vemos que se ha aplicado un **scoring** a los resultados. Esto es debido a que se aplica un **scoring** por defecto, que basa su cálculo en una aproximación de cálculo llamada **<a href="http://www.tfidf.com/" target="_blank" rel="noopener noreferrer">TF-IDF</a>** ( _item frequency-inverse document frequency_).

**¿COMO CONFIGURAMOS SCORING PROFILES� EN AZURE SEARCH?**

Igual que en el caso de los� **Suggesters**, el **Scoring Profile** se configura cuando definimos el índice.

<pre class="brush: csharp; title: ; notranslate" title="">//Define ScoringList
List&amp;amp;lt;ScoringProfile&amp;amp;gt; scoringsList = new List&amp;amp;lt;ScoringProfile&amp;amp;gt;();
Dictionary&amp;amp;lt;string, double&amp;amp;gt; textWieghtsDictionary = new Dictionary&amp;amp;lt;string, double&amp;amp;gt;();
textWieghtsDictionary.Add("Autores", 1.5);
scoringsList.Add(new ScoringProfile()
{
	Name = "ScoringTest",
	TextWeights = new TextWeights(textWieghtsDictionary)
}
);
//Create Index Model
Index indexModel = new Index()
{
	Name = indexName,
	Fields = new[]
	{
		new Field("ISBN", DataType.String) { IsKey = true, IsRetrievable = true, IsFacetable = false },
		new Field("Titulo", DataType.String) {IsRetrievable = true, IsSearchable = true, IsFacetable = false },
		new Field("Autores", DataType.Collection(DataType.String)) {IsSearchable = true, IsRetrievable = true, IsFilterable = true, IsFacetable = false },
		new Field("FechaPublicacion", DataType.DateTimeOffset) { IsFilterable = true, IsRetrievable = false, IsSortable = true, IsFacetable = false },
		new Field("Categoria", DataType.String) { IsFacetable = true, IsFilterable= true, IsRetrievable = true }

	},
	//Add Suggesters
	Suggesters = suggesterList,
	//Add Scorings
	ScoringProfiles = scoringsList
};
</pre>

Como podéis observar **ScoringProfiles�** es una lista de tipo **ScoringProfile**. Un **ScoringProfile�** se define de la siguiente forma:

<u>Name</u>: Campo obligatorio.

<span style="text-decoration: underline;">Functions</span>:� Campo� opcional. Lista de **ScoringFunctions**. Las _functions_ nos permiten definir como queremos que aumente la puntuación, es decir parametrizamos como queremos que este aumento funcione: podemos definir el peso, durante cuanto tiempo está activo&#8230;.

Un ejemplo de **ScoringFunctions** sería:

<pre class="brush: csharp; title: ; notranslate" title="">ScoringFunction sp = new FreshnessScoringFunction()
{
	Boost = 10,
	FieldName = "Autores",
&amp;nbsp;       Interpolation = ScoringFunctionInterpolation.Linear,
	Parameters = new FreshnessScoringParameters()
	{
		BoostingDuration = DateTime.Now.Add(20).TimeOfDay;
	}
}
</pre>

Lo que estamos haciendo es añadir una función que cuando se hagan búsquedas sobre el índice Autores le añadirá un peso de 10 de forma linear� durante 20 días solo.

Para más información sobre las **Functions** y � los tipos de **ScoringFunctions** podéis visitar este� <a href="https://msdn.microsoft.com/en-us/library/azure/dn798928.aspx?f=255&MSPPError=-2147217396" target="_blank" rel="noopener noreferrer">link</a>.

<span style="text-decoration: underline;">FunctionAggregation</span>:� Campo� opcional. Valor que indica como los resultados de las funciones definidas debes combinarse. Sus posibles valores son� _&#8216;sum&#8217;, &#8216;average&#8217;, &#8216;minimum&#8217;, &#8216;maximum&#8217;, &#8216;firstMatching&#8217;._ Su valor por defecto se &#8216;_sum_&#8216; y solo se aplica si hay alguna _function_ definida.

<span style="text-decoration: underline;">TextWeights:� </span>Campo� opcional. Cuando existen coincidencias de texto, a la puntuación obtenida se le suma el valor asignado en este campo.

###### **¿COMO UTILIZARLO?**

Si queremos aplicar un **scoring profile**� en nuestra búsqueda lo que debemos hacer es indicar que _profile_ queremos utilizar:

<pre class="brush: csharp; title: ; notranslate" title="">var sp = new SearchParameters();
sp.ScoringProfile = "scoringName";

//Search
DocumentSearchResult&amp;amp;lt;BookModel&amp;amp;gt; response = indexClient.Documents.SearchAsync&amp;amp;lt;BookModel&amp;amp;gt;(searchText, sp).Result;
</pre>

Y obtenemos el siguiente resultado:

<a href="http://www.robertbermejo.com/wp-content/uploads/2016/04/CustomScoring.png" rel="attachment wp-att-2691"><img class="alignnone size-large wp-image-2691" src="http://www.robertbermejo.com/wp-content/uploads/2016/04/CustomScoring-1024x616.png" alt="CustomScoring" width="660" height="397" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/04/CustomScoring-1024x616.png 1024w, https://www.robertbermejo.com/wp-content/uploads/2016/04/CustomScoring-300x180.png 300w, https://www.robertbermejo.com/wp-content/uploads/2016/04/CustomScoring-768x462.png 768w, https://www.robertbermejo.com/wp-content/uploads/2016/04/CustomScoring.png 1678w" sizes="(max-width: 660px) 100vw, 660px" /></a>

En el caso que se hubiera definido una **ScoringFunction�** en el **scoring**, ademas de definir que **ScoringProfile** se debe definir **ScoringParameters** que es una lista de **ScoringParameter** donde defines para la **ScoringFunction** que deseas que valor quieres aplicar.

Por ejemplo, para la� **FreshnessScoringFunction** que hemos definido anteriormente la contemporización de la llamada quedaría de la siguiente forma:

<pre class="brush: csharp; title: ; notranslate" title="">List&amp;amp;lt;ScoringParameter&amp;amp;gt; scoringParameterlList = new List&amp;amp;lt;ScoringParameter&amp;amp;gt;();
scoringParameterlList.Add(new ScoringParameter("Autores", "5"));
sp.ScoringProfile = scoringName;
sp.ScoringParameters = scoringParameterlList;

//Search
DocumentSearchResult&amp;amp;lt;BookModel&amp;amp;gt; response = indexClient.Documents.SearchAsync&amp;amp;lt;BookModel&amp;amp;gt;(searchText, sp).Result;
</pre>

# **Conclusiones**

Después de esta trilogía de posts podemos ver la potencia que nos da **Azure Search** y como podemos trabajar con él. Hemos podido aprender como crearlo, configurarlo y como atacarlo según nuestras necesidades.

Os ánimo a que entréis más en detalle y podáis ver lo asombroso que es este servicio.

Podéis descargaros el código� <a href="https://github.com/bermejoblasco/AzureSearch" target="_blank" rel="noopener noreferrer">aquí</a>.

**Referencias:**

<a href="https://azure.microsoft.com/en-us/blog/announcing-new-enhancements-to-azure-search-suggestions/" target="_blank" rel="noopener noreferrer">https://azure.microsoft.com/en-us/blog/announcing-new-enhancements-to-azure-search-suggestions/</a>

<a href="https://msdn.microsoft.com/en-Us/library/azure/mt131377.aspx" target="_blank" rel="noopener noreferrer">https://msdn.microsoft.com/en-Us/library/azure/mt131377.aspx</a>

<a href="https://azure.microsoft.com/en-us/documentation/articles/search-get-started-scoring-profiles/" target="_blank" rel="noopener noreferrer">https://azure.microsoft.com/en-us/documentation/articles/search-get-started-scoring-profiles/</a>

<a href="http://social.technet.microsoft.com/wiki/contents/articles/26706.what-are-scoring-profiles-in-azure-search.aspx" target="_blank" rel="noopener noreferrer">http://social.technet.microsoft.com/wiki/contents/articles/26706.what-are-scoring-profiles-in-azure-search.aspx</a>

&nbsp;