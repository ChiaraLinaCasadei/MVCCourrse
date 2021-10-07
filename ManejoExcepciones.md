# Generación y Captura de Excepciones
Un error es un evento inesperado en tiempo de ejecución, que evita que una solicitud complete una operación.
Cuando una linea de código provoca un error ASP.NET Core o Common Language Runtime(CLR) crea una excepción.
La excepción es un objeto de una clase que herada de la clase base System.Exception

## Excepciones no Controladas
Cuando una app no maneja explícitamente una excepción, la aplicación se detiene y el usuario ve un mensaje de error.
En las app MVC, este mensaje tiene el formato de una página web.

Si surge una excepción no cont5rolada mientras está depurando la aplicación en Visual Studio, la ejecución se rompe en la línea que generó la excepción.
Pude usar las herramientas de depuración de VS para investifar qué salió mal, aislar el problema y deporar el código.

A veces, es posible que también desee generar sus propias excepciones para alertar a los componentes de que algo salió mal.
Puede usar la palabra clave **throw** para generar errores en código C#. 

# Detecciones de Errores Try/Catch

Se ejecuta el código en el bloque **try** . 
Si alguno de ese código general una excepción, el tipo de excepción se compara con el tipo declarado en el bloque **catch**.}
Si el tipo coincide o es de un tipo de excepción derivado del declarado, se ejecuta el código en el bloque catch.

# Crear tipos de excepciones personalizados
Para crear una, deberá crear una nueva clase que herede de Exception, o cualquier otra clase que se derive de Exception.

# Trabajar con varios entornos
En ASP .NET Core, la variable de entorno **ASPNETCORE_ENVIRONMENT** debe usarse para detrminar el entorno de la aplicación.
Si no se establece, el valor predeterminado es Producción.

En una aplicación ASP .NET Core, al declarar el middleware, es posible usar el actual entorno de ejecución para tomar decisiones que afecten el comportamiento de su  aplicación.
Este middleware ehecuta una aplicación en modo de desarrollo, a menudo querrá que los errores de las páginas contengan el seguimiento de pila detallado con tanta información como sea posible sobre la excepción que ocurrió.
Ojo en donde se publica, porque ni al cliente le interesa, ni queremos que vulnerar la seguridad de la app a gente malintencionada.

Para resolver esto, se puede utilizar el servicio **IHostingEnvironment**, el cual se inyecta con frecuencia en el método Configure de la clase Startup. 
Esta interfaz expone métodos útiles.

IsDevelopment, IsStaging, IsProduction e IsEnvironment(NombreEntorno).
Se pueden utilizar para determinar el entorno que se está ejecutando actualmente.

```

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
  if (env.IsDevelopment())
  {
      app.UseDeveloperExceptionPage();
  }
  else if (env.IsStaging() || env.IsProduction() || env.IsEnvironment("ExternalProduction"))
  {
      app.UseExceptionHandler("/error");
  }
  app.UseMvcWithDefaultRoute();
  app.Run(async (context) =>
  {
    awaitcontext.Response.WriteAsync("…");
  });
}

```
## Usar Entornos en Vistas

Se pueden usar tag helpers de entorno, para ver en cuales entornos queremos que carguen ciertos componentes (estilos o scripts) de la vista.

```
<environmentinclude="Development"> </environment>

```

# Configurar el Manejo de Errores

En un entorno de desarrollo, a menudo se desea información detallada sobre errores. Esto permite extrapolar información importante sobre qué salió mal y como.

Añadiendo en Configure **UseDeveloperExceptionPage** como middleware.

```
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
  if (env.IsDevelopment())
  {
    app.UseDeveloperExceptionPage();
  }
  app.Run(async (context) =>
  {
    awaitTask.Run(() =>throw new NotImplementedException());
  });
}

```
# Página del controlador de excepciones personalizado

Se puede especificar la URL a la cual queremos que se dirija, que va a ser un html.

```
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
  if (env.IsDevelopment())
  {
      app.UseDeveloperExceptionPage();
  }
  elseif (env.IsStaging() || env.IsProduction() ||
  env.IsEnvironment("ExternalProduction"))
  {
      app.UseExceptionHandler("/error");
  }
  app.UseMvcWithDefaultRoute();
  app.Run(async (context) =>
  {
      awaitTask.Run(() =>throw new NotImplementedException());
  });
}

```

 Al crear controladores y vistas para mostrar errores, asegúrese de mantener la lógica simple.
 
 







