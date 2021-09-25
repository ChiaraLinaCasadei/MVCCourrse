# Controllers
Cuando una aplicación web MVC recibe una solicitud desde un navegador web, crea un objeto instancia del controlador para responder a la solicitud. Luego determina el método de acción que debe ser llamado en el objeto Controller.
La aplicación utiliza una carpeta Models para determinar los valores a pasar a la acción como parámetros. A menudo, la acción crea una nueva instancia de la clase modelo.

## Métodos de acción en el controlador
El método de acción debe ser público en la clase controlador.
Cada uno de los métodos públicos en una clase Controller es considerada una Action.
Las acciones pueden devolver objetos que implementan la interfaz IActionResult

Las acciones o llamadas qe realiza el usuario se hacen mediante una URL, donde la estructura es:

*Dominio/Controlador/Acción*
Una acción posee diferentes funciones dependiendo el método de llamada HTTP, como por ejemplo GET o POST.
El método de llamada HTTP por defecto es GET, por lo que no hace falta especificarlo.
Los demás métodos se especifican por medio de atributos antes de declarar el método de acción.

En C# no se puede declarar 2 funciones en una misma clase con el mismo nombre y tipos de parámetros, deben tener diferentes tipos para reconocer la **sobrecarga**.

```

//GET: Home
public ActionResult Index()
{
    return View();
}

[HttpPost]
public ActionResult Index(int Id)
{
    return View();
}
```

Puede pasar que exista una acción con dos métodos de llamada HTTP distintos y requieran el mismo tipo de parámetro.
Para solucionar el problema de las sobrecargas de funciones, se puede cambiar el nombre de la función para el método Post y vincularlo a la acción Eliminar, como por ejemplo ConfirmarEliminar.

Si se accede a Dominio/Controlador/Eliminar mediante POST, la función que se va a ejecutar es ConfirmarEliminar.

```
  public ActionResult Eliminar (int Id)
  {
    ...
  }

  [HttpPost, ActionName("Eliminar")]
  public ActionResult ConfirmarEliminar (int Id)
  {
    ...
  }
```

# Comunicación con la Vista

Cada controlador posee una carpeta en Views con su mismo nombre par alojar las vistas de las acciones que lo requieran. Estas vistas tienen el mismo nombre que la acción a la que pertenecen para vincularlas automáticamente.

En la acción del controlador con el código *return View();* busca la vista con su mismo nombre.
Si embargo se puede cambiar la vista ingresando el nombre como parámetro de la función View.

```
  public ActionResult Index()
  {
    return View("About");
  }
```

Incluso se puede vincular una vista que esté en otra carpeta. Se debe especificar la ruta completa.
En este ejemplo accedemos a una carpeta donde hay vistas genéricas compartidas por todos los controladores.

```
public ActionResult Index()
{
  return View("~/Views/Common/List.cshtml");
}

```
# Tipos de resultados del método de acción
* ActionResults: Una clase base para todos los resultados de la acción.
* IActionResults: Es una Interfaz. Eso define el contrato que representa el resultado de un método de acción.

## Lista de resultados de acciones

* ViewResult: Retornará  la una vista.
* ContentResult: Retornará una cadena.
* RedirectToResult: Redirigirá a la URL específica.
* RedirectToRouteResult: Redirigirá a un método de acción diferente o un método de acción de controlador diferente.
* PartialViewResult: Retornará una vista parcial.
* ViewComponentResult: Retornará un componente de vista.
* FileResult: Retornará un archivo.
* EmptyResult: no Retornará nada.
* JsonResult: Retornará datos en formato JSON.
* HttpNotFoundResult: Retornará la  página de error 404 no encontrada.
* HttpStatusCodeResult
* HttpUnauthorizedResult: Retornará el código de estado HTTP 403.
* JavascriptResult: Retornará un scripts para su ejecución.
* FileStreamResult, FilePathResult:, FileContentResult: Retornará el contenido del archivo.

# Uso de parámetros

En la ruta, y pueden ser nullables:

```
Ejemplo de ruta
public void Configure(IApplicationBuilder app)
{
app.UseMvc(routes =>
{
 routes.MapRoute(
 name: "default",
 template: "{controller}/{action}/{id?}",
 defaults: new { controller = "Home", action = "Index" });
});
}

//Así es como el enlace del modelo ubica los parámetros

public IActionResult Index(string id)
{
    return Content(id);    
}

```

# La propiedad RouteData
Esta propiedad encapsula la información sobre la ruta. Contiene una propiedad denominada Values, que se puede utilizar para obtener el valor del parámetro.

```
public class HomeController : Controller
{
   public IActionResult Index()
   {
       string id =
      (string)RouteData.Values["id"];
       return Content(id);
   }
}

```
Cuando el usuario escribe la URL http://host/Home/Index?title=someTitle, la solicitud invoca el método de acción Index de la clase HomeController y envía el parámetro title con el valor someTitle. Luego el navegador muestra un título como la salida

```
  public class HomeController : Controller
  {
       public IActionResult Index(string title)
       {
           return Content(title);
       }
  }

```
# Uso de ViewBag y ViewData para pasar información a la vistas


Para pasar información desde el servidor a la vista se utilizan las propiedades ViewBag y ViewData.

## ViewBag

La propiedad ViewBag es un objeto contenedor dinámico que forma parte de la clase base Controller. Es un objeto dinámico, puede agregarle valores de cualquier tipo en el método de acción. En la vista, puede utilizar la propiedad ViewBag para obtener los
valores agregados en el método de acción.

```
public class HomeController : Controller
{
   public IActionResult Index()
   {
       ViewBag.Message = "some text";
       ViewBag.ServerTime = DateTime.Now;
       return View();
   }
}

```
Y así se vería el mismo ejemplo, pero con la sintáxis de Razor

```
<p>
Message is: @ViewBag.Message
</p>
<p>
Server time is: @ViewBag.ServerTime.ToString()
</p>

```

## ViewData
Puede pasar datos adicionales a las vistas mediante la propiedad ViewData. Esta función está disponible para desarrolladores que prefieren utilizar un diccionario de objetos. De hecho, ViewBag es un contenedor dinámico y ViewData es un diccionario. Esto significa que
puede asignar un valor en una acción del controlador utilizando ViewBag y leer el mismo valor en la vista utilizando ViewData.

```
<p>
Message is: @ViewData[“Message”]
</p>
```

# Filtros de Acción

Son atributos declarativos que se agregan a los métodos de acción.
Los filtros son clases de MVC que se pueden utilizar para gestionar diferentes intereses en su aplicación web, como la autorización a los recursos del sistema. Usted puede aplicar un filtro de acción en un controlador anotando el atributo apropiado en el método de acción. También puede aplicar un filtro a cada acción anotando el atributo en la clase del controlador.

## Tipos de filtros

* Filtros de autorización
 
Los filtros de autorización se ejecutan antes que cualquier otro filtro y antes del código en el método de acción. Se utilizan para comprobar los derechos de acceso de un usuario a la acción.
* Filtros de recursos
Aparecen en la canalización de filtros después de los filtros de autorización y antes cualquier otro filtro. Se pueden utilizar por motivos de rendimiento.
Los filtros de recursos implementan Interfaz IResouceFilter o IAsyncResourceFilter.
* Filtros de acción

Los filtros de acción se ejecutan antes y después del código en el método de acción. Puedes usar la acción filtros para manipular los parámetros que recibe una acción. También se pueden utilizar para manipular resultado devuelto por una acción. Los filtros de acción implementan la interfaz IActionFilter o la Interfaz IAsyncActionFilter.
* Filtros de excepción

Los filtros de excepción se ejecutan solo si un método de acción u otro filtro arroja una excepción. Estas clases de filtro se utilizan para manejar errores.
Los filtros de excepción implementan la interfaz IExceptionFilter o la interfaz IAsyncExceptionFilter.
* Filtros de resultados

Los filtros de resultados se ejecutan antes y después de ejecutar el resultado de una acción.
Los filtros de resultados implementan la interfaz IResultFilter o la interfaz IAsyncResultFilter

---

Puede crear sus propias clases de filtros o utilizar clases de filtros existentes. Los siguientes son ejemplos de clases de filtro que puede utilizar:

### La clase de filtro ResponseCacheAttribute 
Anotar una acción con el atributo ResponseCache almacenará en caché la respuesta al cliente. El atributo ResponseCache contiene varias propiedades. Uno de ellos es la propiedad Duration, que obtiene o establece la duración, en segundos, para la cual la respuesta se almacena en caché.
### La clase de filtro AllowAnonymousAttribute 
Anotar una acción con el atributo AllowAnonymous permitirá a los usuarios acceder a una acción sin iniciar sesión.
### La clase de filtro ValidateAntiForgeryTokenAttribute
Anotar una acción con el atributo ValidateAntiForgeryToken ayudará a prevenir ataques de falsificación de solicitudes entre sitios.

---
Puede crear un filtro de acción personalizado o un filtro de resultados personalizado. Puede crear filtros personalizados mediante la implementación de la interfaz IActionFilter o la interfaz IResultFilter.
Sin embargo, la clase base ActionFilterAttribute implementa interfaces IActionFilter como el IResultFilter. Al derivar su filtro de la clase base ActionFilterAttribute, puede crear una filtro
único que puede ejecutar código tanto antes como después de ejecutar una acción, y tanto antes como después de que se devuelve el resultado

```
 public class SimpleActionFilter : ActionFilterAttribute
{
     public override void
    OnActionExecuting(ActionExecutingContext filterContext)
     {
         Debug.WriteLine("This Event Fired: OnActionExecuting");
     }
     public override void
    OnActionExecuted(ActionExecutedContext filterContext)
     {
         Debug.WriteLine("This Event Fired: OnActionExecuted");
     }
     public override void
    OnResultExecuting(ResultExecutingContext filterContext)
     {
         Debug.WriteLine("This Event Fired: OnResultExecuting");
     }
     public override void
    OnResultExecuted(ResultExecutedContext filterContext)
     { 
         Debug.WriteLine("This Event Fired:
        OnResultExecuted");
    }
}
```
Utilizando el filtro que creamos

```
public class MyController : Controller
{
     [SimpleActionFilter]
     public IActionResult Index()
     {
        return Content("some text");
     }
}

```

El código muestra cómo se puede recuperar el nombre de la acción en OnActionExecuting y en OnActionExecuted:

```
public class SimpleActionFilter : ActionFilterAttribute
{
     public override void OnActionExecuting(ActionExecutingContext filterContext)
     {
         string actionName = filterContext.ActionDescriptor.RouteValues["action"];
         Debug.WriteLine(actionName + " started");
     }
     public override void OnActionExecuted(ActionExecutedContext filterContext)
     {
         string actionName = filterContext.ActionDescriptor.RouteValues["action"];
         Debug.WriteLine(actionName + " finished");
     }
}

```
El siguiente código muestra cómo el contenido devuelto al navegador se puede recuperar en el controlador de eventos OnResultExecuted

```
public class SimpleActionFilter : ActionFilterAttribute
{
 public override void OnResultExecuted(ResultExecutedContext filterContext)
 {
     ContentResult result = (ContentResult)filterContext.Result;
     Debug.WriteLine("Result: " + result.Content);
 }
}

```

El siguiente código muestra cómo puede cambiar el contenido de la clase SimpleActionFilter para que escriba la salida en un archivo de registro que se encuentra en **c:\logs\log.txt**

```

public class SimpleActionFilter : ActionFilterAttribute
{
     public override void OnActionExecuting(ActionExecutingContext filterContext)
     {
         string actionName = filterContext.ActionDescriptor.RouteValues["action"];
         using (FileStream fs = new FileStream("c:\\logs\\log.txt", FileMode.Create))
         {
             using (StreamWriter sw = new StreamWriter(fs))
             {
                 sw.WriteLine(actionName + " started");
             }
         }
     }

     public override void OnActionExecuted(ActionExecutedContext filterContext)
     {
         string actionName = filterContext.ActionDescriptor.RouteValues["action"];
         using (FileStream fs = new FileStream("c:\\logs\\log.txt", FileMode.Append))
         {
             using (StreamWriter sw = new StreamWriter(fs))
             {
                 sw.WriteLine(actionName + " finished");
             }
         }
      }

     public override void OnResultExecuting(ResultExecutingContext filterContext)
     {
         using (FileStream fs = new FileStream("c:\\logs\\log.txt", FileMode.Append))
         {
             using (StreamWriter sw = new StreamWriter(fs))
             {
                 sw.WriteLine("OnResultExecuting");
             }
         }
     }

     public override void OnResultExecuted(ResultExecutedContext filterContext)
     {
         ContentResult result = (ContentResult)filterContext.Result;
         using (FileStream fs = new FileStream("c:\\logs\\log.txt", FileMode.Append))
         {
             using (StreamWriter sw = new StreamWriter(fs))
             {
                 sw.WriteLine("Result: " + result.Content);
             }
         }
     }
}
 

```
Cuando se realice la navega en la acción Index en el controlador MyController, se creara un archivo
llamado log.txt

````
Index started

Index finished

OnResultExecuting

Result: some text
```
