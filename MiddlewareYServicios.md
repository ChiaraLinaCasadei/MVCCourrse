# Startup
* ConfigureServices: Se usa para configurar cualquier servicio personalizado (no default) en nuestra aplicación.
* Configure: Se usa para configurar todos los middleware utilizados en nuestra aplicación.

# Como es el manejo dentro de .NET Core?

1) La app se inicia dentro de Program.cs
2) Una vez completada la configuración inicial, se inicia Startup.cs . Podemos usar su constructor para inyectar algunos de los servicios internos y configurarlos, si es necesario.
3) Se accede a ConfigureServices, dentro de Startup, donde definimos los servicios que utilizará nuestra aplicación, incluído el propio MVC.
4) Se accede al método Configure, donde utilizamos IApplicationBuilder para configurar los middleware.
5) Completada la inicializacion de los middleware, nuestra app está lista para procesar solicitudes.
6) Cuando una solicitud llega a nuestro servidor, es el middleware el que se hace cargo y la maneja.

## Middleware
Es un componente (clase) que se ejecuta ante cada solocitud en la aplicación ASP.NET Core.
Normalmente, habrá varios middleware en la aplicación web ASP.NET Core. Puede ser middleware proporcionado por el Framework, agregado a través de NuGet, o su propio middleware personalizado.

Mediante el uso de middleware, puede inspeccionar la solicicitud y tomar decisiones importantes sobre cómo manejar la solicitud actual. En cualquier momento, si es necesario, puede salir del middleware pipeline y devolver una respuesta final.

El flujo de middlewares y su ordeb determinan cómo se ejecuta la apllicación.
Podemos establecer el orden de ejecución del middleware en el pipeline.

![alt text](https://github.com/[ChiaraLinaCasadei/[MVCCourse]/blob/[main]/middlewarePipeline.png?raw=true)

