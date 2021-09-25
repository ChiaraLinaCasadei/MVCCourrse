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

