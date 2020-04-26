---
id: 4901
title: 'Mobile App: Xamarin Forms Push Notification'
date: 2016-08-04T19:37:47+01:00
author: rbermejo
layout: post
---
En los últimos post os estoy hablando de cómo sacar provecho a _**Azure Mobile Apps**_ tanto con **Apache Cordova** como con **Xamarin Forms**. Para cerrar el círculo nos queda ver como enviar notificaciones _push_ a través de _**Azure Mobile Apps**_ en **Xamarin Forms**.<!--break-->

En concreto veremos cómo hacerlo para a_ndroid_. Para ello necesitaremos crear una _**Azure Mobile App�**_ y habilitar **Google Cloud Messaging**, para ello seguir los pasos de mi [post](http://www.robertbermejo.com/mobile-app-apache-cordova-push-notification/) sobre notificaciones  con **Apache Cordova**.

Para seguir el siguiente el ejemplo os aconsejo que os descargáis el código de [aquí](https://github.com/bermejoblasco/XamarinFormPushMobileApp) para que podáis seguir el post de una forma más confortable.

Primero debemos crear el proyecto de **Xamarin Forms**, tenéis los pasos [aquí](http://www.robertbermejo.com/mobile-app-xamarin-forms-azure-ad-authentication/). De este post también debéis coger el código de ServiceManger.cs donde tendremos la instancia a nuestra **_Azure Mobile App_**.

Una vez creado necesitamos añadir un componente a nuestro proyecto _android_ (yourprojectname.Droid), concretamente� **Google Cloud Messaging Client.**  
<a href="https://blogrbermejostorage.blob.core.windows.net/blog/01-GetMoreComponents.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/01-GetMoreComponents.png" alt="selectexportsql" width="660" height="397" /></a>  
<a href="https://blogrbermejostorage.blob.core.windows.net/blog/02-AddGoogleCloudMEssagingClient.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/02-AddGoogleCloudMEssagingClient.png" alt="selectexportsql" width="660" height="397" /></a>  
<a href="https://blogrbermejostorage.blob.core.windows.net/blog/03-AfterAddGoogleCloudMEssagingClient.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/03-AfterAddGoogleCloudMEssagingClient.png" alt="selectexportsql" width="660" height="397" /></a>

Ahora ya podemos utilizar GCM.Client.dll que nos ayudará a implementar y controlará todo lo referente a notificaciones _push_ en android.

Lo primero que haremos es crear en MainActivity.cs una propiedad donde guardaremos la instancia actual de nuestra _activity_ ya que la necesitaremos� en el proceso de registro del dispositivo en el hub de notificaciones� push.

<pre class="brush: csharp; title: ; notranslate" title="">static MainActivity instance = null; 

// Devuleve la activity actual
public static MainActivity CurrentActivity
{
   get
   {
     return instance;
   }
}
</pre>

Ahora creamos un método llamado RegisterNoticationPush que nos servirá para registrar el dispositivo par poder recibir notificaciones _push_.

<pre class="brush: csharp; title: ; notranslate" title="">private void RegisterNotificationsPush()
{
  try
  {
   // Validamos que todo este correcto para poder aplicar notificaciones Push
    GcmClient.CheckDevice(this);
    GcmClient.CheckManifest(this);
   // Registramos el dispositivo para poder recibir notificaciones Push
    GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
  }
  catch (Java.Net.MalformedURLException)
  {
      CreateAndShowDialog("There was an error creating the client. Verify the URL.", "Error");
  }
  catch (Exception e)
  {
     CreateAndShowDialog(e.Message, "Error");
   }
}
 
private void CreateAndShowDialog(String message, String title)
{
    AlertDialog.Builder builder = new AlertDialog.Builder(this); 
    builder.SetMessage(message);
    builder.SetTitle(title);
    builder.Create().Show();
}

</pre>

Ahora vamos al método OnCreate inicializamos la instancia de MainActivity y llamamos al método anterior

<pre class="brush: csharp; title: ; notranslate" title="">protected override void OnCreate(Bundle bundle)
{            
   // Asignamos la acutal instacia de MainAcivity
   instance = this;
   //Registramos las notificaciones
   RegisterNotificationsPush();
 
   base.OnCreate(bundle);
 
   global::Xamarin.Forms.Forms.Init(this, bundle);
   LoadApplication(new App());
}
</pre>

Llegados a este punto solo nos queda implementar la clase que donde una vez registrado el dispositivo en el hub lo registremos para recibir notificaciones _push_ e implementemos los métodos donde procesar las notificaciones.

Para ello crearemos dos clases en nuestro proyecto droid.: GcmService y� PushHandlerBroadcastReceiver.

Empezaremos por crear la clase PushHandlerBroadcastReceiver

<pre class="brush: csharp; title: ; notranslate" title="">[BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
 [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
 [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
 [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
 public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase&lt;GcmService&gt;
   {
       public static string[] SENDER_IDS = new string[] { "your senderID" };
   }
</pre>

En el siguiente código nos tenemos que fijar en varios puntos:

  * En esta clase asignaremos� los senderId&#8217;s para vincularnos a GCM de google y poder recivir las notificaciones.
  * Debemos añadir los permisos que se indican y las definiciones necesarias para que funcione todo correctamente.
  * Debe derviar de la cale GcMBroadcastReceiverBase que es una clase genérica que le pasaremos el tipo de nuestra nueva clase GcmService que también implmentaremos.

Ahora implementamos la clase GcmService.

<pre class="brush: csharp; title: ; notranslate" title="">//Añadimos los permisos necesarios para utilizar notificaciones push
[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
[assembly: UsesPermission(Name = "android.permission.INTERNET")]
[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
 
namespace XamarinMobileAppPush.Droid
{
    //Indicamos que es un servicio que se ejecutará en background
    [Service]
    //Debe derivar de la clase GcmServiceBase
    public class GcmService : GcmServiceBase
    {
        public static string RegistrationID { get; private set; }
 
        //Constructor que llamará a su constructor base pasándole los sernder id's de nuestro GCM creado.
        public GcmService() : base(PushHandlerBroadcastReceiver.SENDER_IDS) { }
 
        //Sobreescribimos el método OnRegistered que se llamará una vez el dispositivo ha sido registrado
        protected override void OnRegistered(Context context, string registrationId)
        {
            try
            {
                RegistrationID = registrationId;
                //Una vez tenemos el id de regsitro podemos registrarnos a las notificaciones push de nuestra mobile app
                var push = ServiceManager.DefaultManager.CurrentClient.GetPush();
                MainActivity.CurrentActivity.RunOnUiThread(() =&gt; Register(push, null));
            }
            catch (Exception ex)
            {
                Log.Error("Exception", ex.Message);
            }
        }
 
        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable&lt;string&gt; tags)
        {
            try
            {
                await push.RegisterAsync(RegistrationID);
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }
 
        //Sobreescribimos el método OnMessage que se llamará cuando se reciba una notificación push
        protected override void OnMessage(Context context, Intent intent)
        {
            var msg = new StringBuilder();
 
            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                {
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
                }
            }
 
            var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
            var edit = prefs.Edit();
            edit.PutString("last_msg", msg.ToString());
            edit.Commit();
 
            string message = intent.Extras.GetString("message");            
            createNotification("Xamain Push", "Message: " + message);         
        }
 
        void createNotification(string title, string desc)
        {
            //Creamos la notificación
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;
 
            //Creamos el intent para mostrar el mensjae
            var uiIntent = new Intent(this, typeof(MainActivity));
 
            //Notification Builder
            NotificationCompat.Builder builder = new NotificationCompat.Builder(this);
 
           //Creamos el mensaje
            var notification = builder.SetContentIntent(PendingIntent.GetActivity(this, 0, uiIntent, 0))
                    .SetSmallIcon(Android.Resource.Drawable.SymActionEmail)
                    .SetTicker(title)
                    .SetContentTitle(title)
                    .SetContentText(desc)
                    .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))
                    .SetAutoCancel(true).Build();
 
            //Mostramos la notificación
            notificationManager.Notify(1, notification);
        }
 
        //Sobreescribimos el método OnMessage que se llamará cuando se reciba una notificación push
        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
        }
 
        //Sobreescribimos el método OnMessage que se llamará cuando se reciba una notificación push
        protected override void OnError(Context context, string errorId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
        }
    }
</pre>

A destacar del código anterior:

  * Se deben sobreescribir los métodos OnRegistered, OnMessage, OnUnRegistered y OnError.
  * OnRegistered porque una vez registrado el dispositivo en GCM debemos registrarlo para recibir notificaciones _push_ en nuestra **_Azure Mobile App._**
  * OnMessage porque es donde realizaremos la lógica necesaria una vez recibida la notificación: como queremos mostrarla, si nuestra aplicación tiene que hacer algo especifico&#8230;
  * OnUnRegistered para que una vez se ha dado de bajo el dispositivo realizar alguna acción como avisar al usuario de tal hecho.
  * OnError, para poder captar cualquier error que suceda con la comunicaicón con el GMC� poder realizar acciones correspondientes.

Ahora ya estamos listos, podéis ejecutar la aplicación y ver como se ha registrado vuestro dispositivo en vuestra _**Azure Mobile Apps**_ y probar de enviar notificaciones, en mi [post anterior](http://www.robertbermejo.com/mobile-app-apache-cordova-push-notification/) sobre notificaciones Push en **Apache Cordova** podéis ver cómo realizar esta prueba.

Este es el resultado:  
<a href="https://blogrbermejostorage.blob.core.windows.net/blog/04-Push.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/04-Push.png" alt="selectexportsql" width="660" height="397" /></a>

Si queréis ver cómo hacerlo en iOS o Windows podéis verlo [aquí](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/).

##### REferencias

<https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-forms-get-started-push>/

&nbsp;