# Diseño de Controllers
En una aplicación web ASP.NET Core MVC suele haber un controlador por cada clase de modelo.
Siempre siguen la convención de nombres "Modelo" --> "ModeloController".

En un controlador puede haber muchas acciones, cada una de ellas se implementa como un método, y generalmente devuelve una vista.
Se necesitan acciones separadas para GET y POST.

# Diseño de Vistas

Se debe considerar las partes de la interfaz gráfica de usuario que aparecen en todas las páginas.
Por ejemplo: el logotipo de la empresa, el menú principal del sitio, los enlaces a la información legal y los controles de inicio de sesión pueden necesitar aparecer en todas las páginas. Diseñando un LAYOUT.

# Arquitectura de Información

Es una estructura lógica para los objetos que administra su aplicación web.
Se debe diseñar de forma que los usuarios puedan encontrar contenido rápidamente, sin comprender ningún aspecto de su aplicación web.

# Presentar una jerarquía en URL

```
  https:\\site\\InstallationManual\Details\3654       //Controller/Action/Parameter
```
# Trabajo con Archivos Estáticos

Los archivos estáticos son los que no cambian en tiempo de ejecución.
De forma predeterminada, todos los archivos estáticos de una app web deben estar ubicados en la carpeta raíz web ***wwwroot***
La aplicación ASP.NET Core no tienen configurado el acceso de forma predeterminada a esta carpeta, lo que se hace es indicarle que la consuma mediante el uso del middleware UseStaticFiles.

Deberá agregar el middleware StaticFiles en el método *Configure()* del archivo startup.
El método *app.UseStaticFiles()* agrega el middleware StaticFiles a la canalización de solicitudes.

## Establecer archivo predeterminado

Para configurar default.html en la solicitud raíz http://localhost:<port>, llame al método *UseDefaultFiles()* antes *UseStaticFiles()* en el método Configure.

*UseDefaultFiles* configura el middleware DefaultFiles, que es una parte de middleware StaticFiles. Esto establece automáticamente un archivo html llamado default.html, default.htm, index.html o index.htm en la
solicitud http.

*El orden del middleware es muy importante. app.UseDefaultFiles() debe agregarse antes app.UseStaticFiles() en el pipeline de solicitudes.*
  
### FileServer

Combina las funcionalidades del middleware UseDefaultFiles y UseStaticFiles. 
Entonces, en lugar de usar ambos middlware, simplemente use UseFileServer en el método Configure.

## Y si los quiero desde una carpeta diferente?
  
Especificamos la ruta en el middleware, dentro del Startup. Suponiendo que queremos acceder a los archivos estáticos desde la carpeta Admin deltro del proyecto.
  
  ```
  public void Configure (IApplicationBuilder app){
  
    app.UseStaticFiles();
    app.UseStaticFiles(new StatisFileOptions()
    {
      FileProvider = new PhysicalFileProvider(Path.Combine
          (Directory.GetCurrentDirectory(), "Content")),
        RequestPath = new PathString("/Admin")
    } );
  
  }
  
  ```

