# Registro de Excepciones
Para agregar el registro, deberá agregar una llamada al método ConfigureLogging, como parte del pipeline CreateDefaultBuilder.

Este método expone 2 parámetros, uno del tipo WebHostBuilderContext, que puede usarse para recuperar el IHostingEnvironment, y un parámetro ILoggingBuilder, que se usa para configurar los proveedores y las configuraciones del registrador.

En el método ConfigureLogging deberá agregar el proveedor de registro, que habilitará el registro. Puede iniciar sesión en más de un proveedor al mismo tiemmpo.

ASP .NET Core tiene varios proveedores integrados, que incluyen:

* Console: Registra el mensaje en la ventana de consola de la aplicación
* Debug: Registra el mensaje en la ventana de debug de la aplicación.
* EventSource: Utiliza la API de seguimiento de eventos para almacenar el evento, el comportamiento difiere entre los sistemas operativos, y puede que no funcione en todas las plataformas.
* EventLog: Registra el mensaje en el registro de eventos de windows. Es exclusivo de windows.
* TraceSource: Crea una fuente de seguimiento que se puede escuchar con una variedad de detectores de seguimiento.
* AzureAppServices: Crea registros que se integran con la plataforma Azure.

```
WebHost.CreateDefaultBuilder(args)
.ConfigureLogging((hostingContext, logging) =>
{
  if (hostingContext.HostingEnvironment.IsDevelopment())
  {
      logging.AddConsole();
  }
})
.UseStartup<Startup>();

```
# Método de Registro
Una vez tenemos el logger, podemos agregar registros a componentes individuales, inyectando un logger dentro del constructor de ese componente.
El logger que inyecte debe ser del tipo ILogger<NombreClase>.
 
```
 public class HomeController : Controller
{
  private ILogger _logger;
  public HomeController(ILogger<HomeController>logger)
  {
      _logger = logger;
  }
  public IActionResultIndex()
  {
      _logger.LogInformation("Adding an entry to the logger.");
      return Content("Resultfromcontroller");
  }
} 
  
```
  
Como integrar un logger en el método Configure

```
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILogger<Startup> logger)
{
    logger.LogCritical("CriticalMessagefromlogger");
    
    if (env.IsDevelopment())
    {
      app.UseDeveloperExceptionPage();
    }
    app.UseMvcWithDefaultRoute();
    app.Run(async (context) =>
    {
      awaitcontext.Response.WriteAsync("Hello World!");
    });
} 
  
```
