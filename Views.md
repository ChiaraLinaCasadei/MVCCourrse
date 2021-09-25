# Creación de vistas con sintáxis de Razor
Las vistas es la capa de presentación de la aplicación. 

Puede haber algunos controladores que no correspondan a ninguna clase del modelo. Sin embargo, cada controlador puede tener varias
vistas. Por ejemplo, es posible que desee crear las siguientes vistas para los productos:

* Details: La vista Details puede mostrar un producto, su precio, número de catálogo y otros detalles.
* Create: La vista Create puede permitir a los usuarios agregar un nuevo producto al catálogo.
* Edit: La vista Edit puede permitir a los usuarios modificar las propiedades de un producto existente.
* Delete: La vista Delete puede permitir a los usuarios eliminar un producto del catálogo.
* Index: La vista de Index puede mostrar todos los objetos de producto del catálogo.

Por convención, todas las vistas en una aplicación MVC residen dentro de la carpeta Views del nivel superior. Dentro de esta carpeta, hay una carpeta para cada
controlador que se utiliza en la aplicación.
En el ejemplo anterior, la carpeta Views contendría una carpeta Producto y esta carpeta contendría las vistas Details, Create, Edit, Delete e Index.
Si usa el motor de vista de Razor y el lenguaje C#, las vista se crean con una extensión  *.cshtml*.

```
  Agregar >> Vista
```
Alternativamente, puede crear una vista para una acción de controlador en particular abriendo el controlador, haga clic con el botón derecho en la acción y luego haga clic
en Agregar vista. 

## Nombre de vistas
El nombre de la vista que elija debe coincide con el nombre devuelto por la acción del controlador correspondiente. Si el método de acción del controlador no especifica el
nombre de la vista a usar, MVC asume que el nombre de la vista coincide con el nombre de la acción del controlador.

## Clase de modelo
Si crea una vista fuertemente tipada, necesita especificar un modelo para enlazar a la vista.

## Plantilla
Si especifica un modelo para la vista, Visual Studio puede crear plantillas simples para Crear, Editar, Detalles, Eliminar y Listar. Si no especifica un modelo para la vista, puede
construir una vista vacía (sin modelo).

## Bibliotecas de scripts de referencia
Cuando selecciona esta casilla de verificación en MVC, las referencias a los archivos de script del lado del cliente se incluyen en el proyecto. 

## Crear como vista parcial
Una vista parcial es una sección del código de Razor que puede reutilizar en múltiples vistas en su aplicación.

## Usar una página de diseño
Se puede usar una vista de diseño y se utilizara en muchas vistas dentro de la aplicación web.

```
public class ProductController : Controller
{
   [Route("Product/Index")]
   public IActionResult Index()
   {
      return View();
   }
}

```
El siguiente código muestra el contenido de un archivo llamado Index.cshtml que se encuentra en carpeta Views/Product:

```
  <!DOCTYPE html>
    <html>
       <head>
         <meta name="viewport"
        content="width=device-width"/>
         <title>Index</title>
       </head>
       <body>
          Hello from the Index view
       </body>
    </html>

```

# Usando Razor

En la sintaxis de Razor, el símbolo @ tiene varios usos:

* Utilice @ para identificar el código C# del lado del servidor.
* Utilice @@ para representar un símbolo @ en una página HTML.
* Use @: para declarar explícitamente una línea de texto como contenido y no como código.
* Utilice <text> para declarar explícitamente varias líneas de texto como contenido y no como código.

  ## Características de la sintáxis de Razor
  
```
@* Some more Razor examples *@
<span> Price including Sale Tax: @ViewBag.Price * 1.2 </span>
<span> Price including Sale Tax: @(ViewBag.Price * 1.2) </span>
@{
   int i = 5;
   int j = 6;
   int z = i + j;
   @z
}   
  
```
  
 ## Inyección de Dependencias en vistas
  
 Puede inyectar un servicio en una vista usando la directiva  *@inject*
  
```
@inject <tipo> <nombre de instancia>
```

El **tipo** es la interfaz del servicio que desea inyectar. Para encontrar un tipo, deberá utilizar la ruta para el espacio de nombres de la interfaz.
El **nombre de la instancia** es un alias que crea para hacer referencia a un servicio. A lo largo de una vista, llamar al alias otorgará accedes a los métodos expuestos por una interfaz.

Después de inyectar una interfaz, puede acceder al servicio en cualquier momento dentro del código contenido por símbolo @.

```
 @inject Services.IFormatNumber formatNumber
 @formatNumber.GetFormattedNumber(1234535334)
  
 //Mientras tanto, en servicios está implementado todo lo que uso en las vistas
  
namespace Services
{
   public interface IFormatNumber
   {
      string GetFormattedNumber(int number);
   }
  
   public class FormatNumber : IFormatNumber
   {
      public string GetFormattedNumber(int number)
       {
          return string.Format("{0:n0}", number);
       }
   }
}

```
## Inyectar servicios en vistas
  
* Servicios para inyectar en vistas:
  - Servicios que transforman o dan formato al texto.
  - Servicios que impactan en la apariencia visual.
  - Servicios que se requieren para usos repetidos dentro de una vista.
* Servicios para inyectar en controladores:
  - Servicios que recuperan datos.
  - Servicios que dependen de recursos externos.
  - Servicios que devuelven resultados a una vista.
  
## Tag Helpers
 
HTML Helper: 
  ``` 
  @Html.ActionLink("Press me", "AnotherAction" 
  ```
Tag Helper:
  ```
  <a asp-action="AnotherAction">Press me</a>
  ```

 ## Pasar parámetros a una acción
  
  ```
    public class PhotoController : Controller
    {
       [Route("Photo/Choose")]
       public IActionResult Choose()
       {
          return View();
       }
       [Route("Photo/Display/{id}")]
       public IActionResult Display(int id)
       {
           string res = $"Photo number {id} was chosen";
           return Content(res);
       }
     }
  
  ```
  
En el siguiente ejemplo de código, el Tag Helper Anchor se usa con un atributo asp-route- {id}. Si el usuario hace clic en el enlace, se llamará a la acción Display en el controlador PhotoController, y
se pasará un valor entero al parámetro id:
  
```
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  <a asp-action="Display" asp-route-id="1">Click here to view photo 1</a>
  <a asp-action="Display" asp-route-id="2">Click here to view photo 2</a>

```
 
## Usando el archivo _ViewImports.cshtml

Si agrega la directiva @addTagHelper a una vista, los Tag Helpers solo están disponibles en esta vista. Si desea que los Tag Helpers estén disponibles para todas las vistas en la carpeta Views y sus subdirectorios. Usted
puede agregar la directiva @addTagHelper a un archivo _ViewImports.cshtml. El archivo _ViewImports.cshtml debe estar ubicado en la carpeta Views

Por ejemplo, en lugar de agregar la directiva @addTagHelper al archivo Index.cshtml:

```
  @addTagHelper *,
Microsoft.AspNetCore.Mvc.TagHelpers
<a asp-action="AnotherAction">Press me</a>
  
```
Puede agregar la directiva @addTagHelper al archivo _ViewImports.cshtml en la carpeta Views:
  
El archivo Views /_ViewImports.cshtml 

```
@addTagHelper *,
Microsoft.AspNetCore.Mvc.TagHelpers
  
```

En este caso, no necesita más para agregar la directiva @addTagHelper al archivo Index.cshtml:
La directiva @addTagHelper se elimina del archivo Index.cshtml

```
  <a asp-action="AnotherAction"> Presióname </a>  
```
  
## Usando la directiva @addTagHelper
  
Para usar tag helper, debe agregar la directiva @addTagHelper a una vista:
 ```
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
 ```
Para que el tag helper esté disponible para todas las vistas, agregue la directiva @addTagHelper al archivo _ViewImports.cshtml

## Crear componentes de vistas

Puede utilizar componentes de vista para representar contenido HTML idéntico o similar en diferentes ubicaciones.

Un componente de vista consta de dos partes:

Una clase:
  
* Debe ser público y no abstracto.
* Generalmente derivado de la clase base ViewComponent.
* Debería tener un método llamado InvokeAsync, que define su lógica.

Una vista:
  
* Se puede ubicar en una carpeta en la carpeta Views\Shared\Components
* El nombre de la carpeta debe ser el mismo que el nombre de la clase del componente de vista sin el sufijo ViewComponent.

```
public class MyViewComponent : ViewComponent
 {
     public Task<IViewComponentResult> InvokeAsync()
     {
        return Task.FromResult<IViewComponentResult>(View("Default"));
     }
 }  
  
```
Puede incluir un componente de vista en una vista mediante:
Usando el método @Component.InvokeAsync:
@await Component.InvokeAsync("My")

## Invocar componentes de vistas con parámetros

  Un componente de vista que obtiene un parámetro:
  
  
```
public async Task<IViewComponentResult> InvokeAsync(int param)
{
   int id = await SomeOperationAsync(param);
   return View("Default", id);
}
  
```
Pase un parámetro al componente de vista:
  
```
  @await Component.InvokeAsync ("Mi", 5)  
```

  
  
  
  
  
  
  
  
  
  
