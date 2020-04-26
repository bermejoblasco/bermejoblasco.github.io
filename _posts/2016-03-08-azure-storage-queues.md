---
id: 311
title: Azure Storage Queues
date: 2016-03-08T12:18:22+01:00
author: rbermejo
layout: post
---
_Azure storage queues_ es un servicio de azure que nos permite desacoplar aplicaciones o componentes de forma que puedan comunicarse. Los componentes pueden estar en el cloud u on-premise.<!--break-->

_Azure storage queues_ tiene las siguientes características:

  * Soporta 2000 transacciones por segundo.
  * Los mensajes tienen una vida de 7 días.
  * El tamaño máximo de un mensaje es de 64kb.

Seguramente la pregunta que os estáis haciendo en este momento es: ¿Solo 64kb? El uso de _queues_ no está diseñado para el envío de grandes volúmenes de información, sino de pequeños mensajes que envían los emisores y los receptores al recibirlos saben que acción deben realizar.

**<u>¿Cómo puedo trabar con <em>queues</em>?</u>**

Para poder trabajar con _queues_ lo podemos realizar de dos formas:

1- Vía RestAPI &#8211;> Si el _container_ al que pertenece la _queue_ es público puedes atacar directamente a la API, en caso contrario primero se debería obtener la _Shared Key Signature_ correspondiente (en mi anterior articulo escribí en cómo hacerlo para _Blobs_, sería homologo para _queues_ http://robertbermejo.com/index.php/2016/03/01/shared-access-signatures-sas/

2- Vía .Net &#8211;> Utilizando la librería Microsoft.WindowsAzure.Storage, para ello hace falta conocer la connectionstring al _storage_.

**<u>Cuando utilizar queues</u>**

El uso de _azure storage queues_ es útil cuando:

  * La tarea a realizar requiere de un tiempo alto de ejecución (alta latencia).
  * La tarea necesita de un sistema externo que no siempre está disponible.
  * La tarea necesita de un alto consumo de CPU.
  * Cuando queremos desacoplar partes de nuestro sistema.

**<u>Ciclo de vida de un mensaje</u>**

Una vez el mensaje ha sido añadido a la _queue_, entra en juego una de las partes más importantes de una _queue_, el receptor.

Cuando un receptor realiza el _&#8220;pop&#8221;_ del mensaje este no se elimina de la _queue_, esto solo sucederá cuando se llame al método _DeleteMessage_. Entonces, ¿si no se elimina el mensaje nada más recibirlo puede ser consumido por otros receptores? La respuesta es no, el mensaje se marca como invisible por lo que los demás no pueden verlo. El tiempo por defecto que el mensaje queda marcado como invisible es de 30 segundos y es configurable por el receptor según el tiempo que pueda tardar su lógica.

Una vez el receptor ha terminado su ejecución dentro del tiempo de espera (tiempo que configurado en el mensaje como invisible) el mensaje se borra, en caso contrario el mensaje vuelve a ser visible y puede ser consumido de nuevo. Esta dinámica permite que los mensajes no se pierdan.

Anteriormente he mencionado que un mensaje no podía ser consumido por más de un receptor lo cual no es del todo cierto dado que se puede realizar una configuración especial del mensaje de forma que quede visible y pueda ser consumido por más de un receptor, pero este no es su comportamiento estándar.

A continuación, vemos como un pequeño ejemplo de cómo trabajar con _queues_.

Primero tendríamos un servicio para interactuar con nuestra queue.

<pre class="">public class QueueStorageServices
{
 private CloudStorageAccount storageAccount;
 private const string QueueName = "examplequeue";public QueueStorageServices()
 {
    this.storageAccount =    CloudStorageAccount.Parse(ConfigurationManager.ConnectionStrings["Azure"].Connec tionString); 
 }
 private CloudQueueClient QueueClient
 {
   get { return this.storageAccount.CreateCloudQueueClient(); } 
 } 

 /// Add message from queue
 public void AddMessage(QueueModel model)
 {
   var queue = this.GetContainer(QueueName);
   var messageJson = JsonConvert.SerializeObject(model);
   var message = new CloudQueueMessage(messageJson);
   //Message with 60 seconds visibulity
   queue.AddMessage(message, timeToLive: TimeSpan.FromSeconds(60.0));
 } 

 /// Read message from queue
 public QueueModel GetMessage()
 {
   var queue = this.GetContainer(QueueName);
   var queueuMessage = queue.GetMessage();
   return JsonConvert.DeserializeObject&lt;QueueModel&gt;(queueuMessage.AsString); 
 }

 /// Get reference to queue. If no exist create it.
 private CloudQueue GetContainer(string queue)
 {
   var queueContainer = this.QueueClient.GetQueueReference(queue);
   queueContainer.CreateIfNotExists();
   return queueContainer;
 }
}</pre>

Ahora creamos un sender que añadirá mensajes a la _queue_ de la siguiente forma.

<pre class="">var model = new QueueModel() { Message = "Example queue", CreateDateMessage = DateTime.Now };
QueueStorageServices queueService = new QueueStorageServices();
queueService.AddMessage(model);
Console.WriteLine("Message send to queue");</pre>

Para finalizar tendríamos un reciver que recogería el objeto de la _queue_.

<pre class="">QueueStorageServices queueService = new QueueStorageServices();
var queueModel = queueService.GetMessage();
Console.WriteLine($"Message from queue: {queueModel.Message} - Creation message date: {queueModel.CreateDateMessage.ToString()}");</pre>

Si ejecutamos el sender:

<img class="alignnone wp-image-371" src="http://robertbermejo.azurewebsites.net/wp-content/uploads/2016/03/Sender1-300x201.jpg" alt="Sender1" width="527" height="353" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/03/Sender1-300x201.jpg 300w, https://www.robertbermejo.com/wp-content/uploads/2016/03/Sender1.jpg 499w" sizes="(max-width: 527px) 100vw, 527px" /> 

Vemos que se ha añadido el mensaje en la _queue_:

<img class="alignnone wp-image-381" src="http://robertbermejo.azurewebsites.net/wp-content/uploads/2016/03/VSQueue-300x180.png" alt="VSQueue" width="515" height="309" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/03/VSQueue-300x180.png 300w, https://www.robertbermejo.com/wp-content/uploads/2016/03/VSQueue-768x462.png 768w, https://www.robertbermejo.com/wp-content/uploads/2016/03/VSQueue-1024x616.png 1024w, https://www.robertbermejo.com/wp-content/uploads/2016/03/VSQueue.png 1687w" sizes="(max-width: 515px) 100vw, 515px" /> 

Finalmente lanzamos el reciver:

<img class="alignnone wp-image-361" src="http://robertbermejo.azurewebsites.net/wp-content/uploads/2016/03/Reciver-300x201.png" alt="Reciver" width="516" height="346" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/03/Reciver-300x201.png 300w, https://www.robertbermejo.com/wp-content/uploads/2016/03/Reciver.png 499w" sizes="(max-width: 516px) 100vw, 516px" /> 

Con esta solución, el proceso debería estar constantemente preguntando por si hay más mensajes para poder procesarlos, para no tener que realizar esto existe QueueTrigger que cuando hay un cambio en la _queue_ automáticamente coge el mensaje y se procesa.

Para ello creamos un WebJob de Azure y haríamos que estuviera constantemente corriendo

<pre class="">class Program
{
  // Please set the following connection strings in app.config for this WebJob to run:
  // AzureWebJobsDashboard and AzureWebJobsStorage
  static void Main()
  {
     var host = new JobHost();
    // The following code ensures that the WebJob will be running continuously
host.RunAndBlock();
  }
}</pre>

Se nos crea la clase Functions con el método ProcessQueueMessage, aquí implementaríamos los métodos con los queuetriggers a las _queues_ que tuviéramos.

<pre class="">public class Functions
{
  // This function will get triggered/executed when a new message is written 
  // on an Azure Queue called queue.
  private const string QueueName = "examplequeue";  

  /// QueueTrigger: When add message to queue execute this method.
  public static void ProcessQueueMessage([QueueTrigger(QueueName)] QueueModel queueModel, TextWriter log)
  {
     log.WriteLine($"Message from queue: {queueModel.Message} - Creation message  date: {queueModel.CreateDateMessage.ToString()}");
  }

  /// When message fails in queue goes to poision queue
  public static void ProcessQueuePoisonNotificationMessage([QueueTrigger(QueueName)] QueueModel queueModel, TextWriter log)
  {
     log.WriteLine($"Poison: Message from queue: {queueModel.Message} - Creation message date: {queueModel.CreateDateMessage.ToString()}");
  }
}</pre>

Ejecutamos el webjob como si fuera un proceso normal y obtenemos el siguiente resultado:

<img class="alignnone wp-image-391" src="http://robertbermejo.azurewebsites.net/wp-content/uploads/2016/03/WwebJob-300x201.png" alt="WwebJob" width="540" height="362" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/03/WwebJob-300x201.png 300w, https://www.robertbermejo.com/wp-content/uploads/2016/03/WwebJob.png 499w" sizes="(max-width: 540px) 100vw, 540px" /> 

Para más información sobre _queues_ con WebJobs consultar: <https://azure.microsoft.com/en-us/documentation/articles/websites-dotnet-webjobs-sdk-storage-queues-how-to/>

**<u>Conclusión</u>**

_Azure storage queues_ es una buena solución cuando queremos que nuestro sistema no tenga dependencias entre diferentes módulos de forma que este desacoplado y necesitamos una forma rápida de comunicación entre ellos. Si quisiéramos una solución más compleja deberíamos utilizar _Azure Service Bus_.

En GitHub podéis encontrar el código: <https://github.com/bermejoblasco/AzureStorageQueues>

Hasta la próxima.