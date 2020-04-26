---
id: 4571
title: 'Mobile App: Xamarin Forms Azure AD Authentication'
date: 2016-07-26T09:07:24+01:00
author: rbermejo
layout: post
---
En mi anterior [post](http://www.robertbermejo.com/mobile-app-apache-cordova-azure-ad-authentication/) vimos como realizar la autenticación en una aplicación de **Apache Cordova** mediante _**Azure Mobile Apps**_. En este post vamos a ver como hacerlo en **Xamarin Forms** pero solo en Android.<!--break-->

Para empezar necesitamos tener creado un servicio� _**Azure Mobile Apps**_ con la Autenticación de AD activada, en mi anterior [post](http://www.robertbermejo.com/mobile-app-apache-cordova-azure-ad-authentication/)� podéis ver como crearlo paso a paso.

Para seguir el ejemplo con facilidad os recomiendo os bajéis primero el código del [Github](https://github.com/bermejoblasco/XamarinFormsAuthAzureMobileApp).

Una vez creado, creamos un proyecto de **Xamarin Forms** en VS2015 Update 3.

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/CreateProjects.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/CreateProjects.png" alt="selectexportsql" width="660" height="397" /></a>

Una vez creado el proyecto añadimos la referencia de� _Azure Mobile Client SDK_ 

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/AddMobileClientSDK.png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/AddMobileClientSDK.png" alt="selectexportsql" width="660" height="397" /></a>

Para cada proyecto necesitaremos implementar su propio método de autenticación, para ello, lo que hacemos es lo siguiente:

En **App.cs (portable class)** creamos un método _static_ **Init**� donde le pasaremos una _Interface_ que implementaran todos los proyectos:

<pre class="brush: csharp; title: ; notranslate" title="">public class App : Application
{
   public static IAuthenticate Authenticator { get; private set; }
 
   public static void Init(IAuthenticate authenticator)
   {
       Authenticator = authenticator;
   }
   ....
}
</pre>

La _Interface_ **IAuthenticate** tiene la siguiente definición:

<pre class="brush: csharp; title: ; notranslate" title="">public Interface IAuthenticate
{
    Task&lt;bool&gt; Authenticate();
}
</pre>

Ahora añadimos un _button_ e implementamos el evento _Clicked,_ para el ejemplo yo he implementado una clase **ButtonCodePage**.

<pre class="brush: csharp; title: ; notranslate" title="">class ButtonCodePage : ContentPage
{
   public ButtonCodePage()
   {
      Button button = new Button
      {
        Text = String.Format("Login Azure ADd"),
      };
      button.Clicked += async (sender, args) =&gt;
      {
        if (App.Authenticator != null)
        {
           var auth = await App.Authenticator.Authenticate();
        }
      }; 
            this.Content = button;
   }
 }
</pre>

Ahora lo siguiente que debemos hacer es ir al proyecto de Android a **MainActivicy.cs**, hacer que implemente la _Interface_ **IAuthenticate�** e implementar el método de autenticación:

<pre class="brush: csharp; title: ; notranslate" title="">public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
{  
  private MobileServiceUser user;
 
  public async Task&lt;bool&gt; Authenticate()
  {
    var success = false;
    var message = string.Empty;
    try
    {
         // Sign in with Azure AD login using a server-managed flow. 
         user = await ServiceManager.DefaultManager.CurrentClient.LoginAsync(this, MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory);
         if (user != null)
         {
        message = string.Format("you are now signed-in is {0}.", user.UserId);
          }
    }
    catch (Exception ex)
    {
       message = ex.Message;
    }
 
    // Display the success or failure message.
    AlertDialog.Builder builder = new AlertDialog.Builder(this);
    builder.SetMessage(message);
    builder.SetTitle("Sign-in result");
    builder.Create().Show();
 
    return success;
 }
 
 protected override void OnCreate(Bundle bundle)
 {
    base.OnCreate(bundle);
    Xamarin.Forms.Forms.Init(this, bundle);
    //Inicializamos nuestra Xamarin App con la autenticación de Android
    App.Init((IAuthenticate)this);
    LoadApplication(new App());
 }
}
</pre>

En el código anterior hay varios puntos a aclarar:

  * ServiceManager &#8211;> es una clase en la librería _portable_ que nos ayuda a crear y consumir el cliente de **MobileServiceClient**.

<pre class="brush: csharp; title: ; notranslate" title="">public class ServiceManager
{
   static ServiceManager defaultInstance = new ServiceManager();
   MobileServiceClient client;
 
   private ServiceManager()
   {
      this.client = new MobileServiceClient("https://yourmobileapp.azurewebsites.net");
   }
 
   public MobileServiceClient CurrentClient
   {
      get { return client; }
   }
 
   public static ServiceManager DefaultManager
   {
       get
       {
          return defaultInstance;
       }
       private set
       {
          defaultInstance = value;
        }
    } 
 }
</pre>

  * Otro punto a comentar es :

<pre class="brush: csharp; title: ; notranslate" title="">user = await ServiceManager.DefaultManager.CurrentClient.LoginAsync(this, MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory);
</pre>

Como podéis observar en **MobileServiceAuthenticationProvider** hemos seleccionado la opción deseada, en este caso **WindowsAzureActiveDirectory**.

  * Por último en el método **OnCreate** hemos añadido la siguiente instrucción:

<pre class="brush: csharp; title: ; notranslate" title="">App.Init((IAuthenticate)this); 
</pre>

Lo que estamos haciendo es iniciar la autenticación� de nuestra App con la configuración de Android.

Ahora podemos ver los resultados ejecutando nuestra aplicación:

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/ButtonLoginAzureAD[1].png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/ButtonLoginAzureAD[1].png" alt="selectexportsql" width="660" height="397" /></a>

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/LoginzureAD[1].png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/LoginzureAD[1].png" alt="selectexportsql" width="660" height="397" /></a>

<a href="https://blogrbermejostorage.blob.core.windows.net/blog/LoginOK[1].png" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="https://blogrbermejostorage.blob.core.windows.net/blog/LoginOK[1].png" alt="selectexportsql" width="660" height="397" /></a>

###### Conclusiones

Como habéis podido ver es muy fácil poder utilizar la autenticación� de _**Azure Mobile Apps**_ para nuestra apps.

Cierto es, que requiere un poco más de trabajo que en **Apache Cordova**, dado que en Xamarin se debe implementar plataforma a plataforma, pero al final son muy pocas� lineas de código que no tienen un impacto notable en el desarrollo y el mantenimiento.

¡Espero os haya gustado!

Referencias:

https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-forms-get-started-users/

https://developer.xamarin.com/guides/xamarin-forms/web-services/authentication/azure/