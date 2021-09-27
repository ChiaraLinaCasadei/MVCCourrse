# Modelos MVC

Cuando escribe una aplicación web Model-View-Controller (MVC), dentro del modelo, se crea un modelo clase para cada tipo de objeto. La clase modelo describe las propiedades de
cada tipo de objeto y puede incluir lógica empresarial que coincida con los procesos empresariales. Por tanto, el modelo es un pilar fundamental en una aplicación MVC.

## ¿Qué es?

Un modelo MVC es una colección de clases. Cuando crea una clase de modelo, define las propiedades y métodos que son necesarios para el tipo de objeto que describe la clase de
modelo. Necesitas saber cómo crear y describir modelos, y cómo modificar la forma en que un MVC crea una clase de modelo instancias cuando ejecuta su aplicación web.
También es importante saber cómo los controladores pasan modelos a vistas y cómo las vistas pueden representar los datos almacenados en un modelo en el navegador.

## Pasar modelos a vistas

Cuando una aplicación web ASP.NET Core MVC recibe una solicitud de un navegador web, un objeto instancia del controlador responde a la solicitud. A continuación, la aplicación web ASP.NET Core MVC determina el método
de acción que debe llamarse en el objeto controlador. A menudo, la acción crea una nueva instancia de una clase modelo, que se puede pasar a una vista para mostrar resultados para el usuario.

Los objetos pasan del modelo a la vista. Sin embargo, para aplicaciones grandes, esta no es una práctica recomendada. Para aplicaciones grandes, recomendamos que utilice ViewModels
para separar la presentación del dominio.

```
namespace ModelNamespace
{
   public class SomeModel
   {
     public string Text { get; set; }
   }
}

//luego, en el controlador

public class HomeController : Controller
{
   [Route("Home/Index")]
   public IActionResult Index()
   {
     SomeModel model = new SomeModel() { Text = "some text" };
     return View(model);
   }
}

```
Puede utilizar el operador Razor @model para especificar el tipo de modelo de dominio fuertemente tipado de la vista. Acceder al valor de una propiedad en el objeto de dominio, utilice @Model.PropertyName.

El siguiente código muestra una vista que recibe un modelo del tipo SomeModel y lo usa para generar una respuesta:

```
  @model ModelNamespace.SomeModel
  @Model.Text

```
Si un usuario solicita la URL relativa, /Home/Index, se muestra la siguiente cadena en la pantalla: “some text”.

## Pasar una colección de elementos

```
public class HomeController : Controller
{
   [Route("Home/Index")]
   public IActionResult Index()
   {
     SomeModel item1 = new SomeModel() { Text = "first item" };
     SomeModel item2 = new SomeModel() { Text = "second item" };
     List<SomeModel> items = new List<SomeModel>() { item1, item2 };
     return View(items);
   }
}

```

El siguiente código muestra una vista que recibe una colección de elementos y la usa para generar una respuesta:

```
      @model IEnumerable<ModelNamespace.SomeModel>
      @foreach (var item in @Model)
      {
        <div>@item.Text</div>
      }

```
## Pasar un modelo a una vista diferente

El método Controller.View está sobrecargado. Los ejemplos anteriores utilizaron una versión de este método que obtuvo solo una instancia de un modelo como parámetro. Cuando usa esta versión de método Controller.View, no tiene que
especificar el nombre de la vista porque es implícitamente idéntico al nombre de la acción.
Sin embargo, puede utilizar otra versión del método Controller.View para llamar a una vista cuyo el nombre es diferente del nombre de la acción. Cuando usa esta versión, debe especificar explícitamente el nombre de la vista.

El siguiente código muestra un controlador que crea un modelo y lo pasa a una vista. El nombre de la acción es Index, mientras que el nombre de la vista es Display:

```
public class HomeController : Controller
{
   [Route("Home/Index")]
   public IActionResult Index()
   {
     SomeModel model = new SomeModel() { Text = "some text" };
     return View(“Display”, model);
   }
}

```
El siguiente código muestra una vista que recibe un modelo del tipo SomeModel y lo usa para generar la respuesta:

El archivo Display.cshtml
```
@model ModelNamespace.SomeModel
@Model.Text
```

## Vinculación de Vistas a clases de modelo y visualización de datos

La aplicación ASP.NET Core MVC usa vistas para definir la interfaz de usuario.

### Vistas fuertemente tipadas
Diseñadas para mostrar propiedades de una clase de modelo.
Incluye una declaración de la clase modelo. 
Cuando declara la clase de modelo en la vista, Microsoft Visual Studio proporciona IntelliSense y comprobación de errores mientras escribe el código para comprobar las propiedades de la clase modelo.
Esto también simplifica la resolución de problemas de errores en tiempo de ejecución.
Por lo tanto, siempre que pueda, cree vistas fuertemente tipadas, porque estas funciones adicionales del IntelliSense y de verificación de errores pueden ayudarlo a reducir errores de codificación. 
Una vista fuertemente tipada, solo funciona con un objeto de la clase modelo en su declaración.
En los archivos de vista, puede acceder a las propiedades del objeto de modelo utilizando la palabra clave Model. Para acceder a una propiedad en un modelo, utilice:
@Model.PropertyName.

Ej: Display(Product)

### Vistas dinámicas
No se pueden vincular a una clase de modelo específica.
Una vista dinámica no incluye la declaración @model en la parte superior de la página. Más tarde, puede optar por agregar la declaración @model para cambiar la vista a una vista fuertemente tipada.
Acá se es propenso a errores, porque Intellisense no nos ayuda.

Ej: Index()




## HTML Helpers

### Editor for Model

El HTML Helper @Html.EditorForModel devuelve un elemento de entrada HTML para cada propiedad del modelo.

```
public class PersonController : Controller
{
  [Route("Person/GetName")]
  public IActionResult GetName()
  {
      return View();
  }
}


//Usando el HTML Helper @Html.EditorForModel en una vista para mostrar todas las propiedades en el modelo:

@model ModelNamespace.Person
 <form action="/Person/GetName" method="post">
 @Html.EditorForModel()
 <input type="submit" value="Submit my name" />
</form>

```
### Begin Form

Se utiliza para crear un formulario en HTML, debe comenzar con un elemento <form> en la página web HTML y todas las etiquetas y los controles de entrada deben estar dentro del elemento <form>. En una vista MVC, puede usar Html.BeginForm para representar este elemento
y establecer la acción del controlador a la que el formulario envía datos.
También puede especificar el método HTTP que utiliza el formulario para enviar datos. Si el formulario usa el método POST, que es el predeterminado, el navegador envía los valores del formulario al servidor web en el cuerpo del formulario. Si el formulario utiliza el método GET, el navegador
envía los valores del formulario al servidor web en la cadena de consulta en la URL.

```
@model ModelNamespace.Person
@using (Html.BeginForm())
{
 @Html.EditorForModel()
 <input type="submit" value="Submit my
name" />
}

```
En el HTML renderizado, el elemento <form> debe cerrarse con una etiqueta </form>. En las vistas de Razor, puede asegúrese de que el elemento del formulario esté cerrado con un bloque de código Razor @using que representa la etiqueta </form>  

```
 @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@model ModelNamespace.Person
<h1>Get person details</h1>
@using (Html.BeginForm("ShowDetails", "Person"))
{
 <label asp-for="FirstName"></label>
 <input asp-for="FirstName" />
 <br />
 <label asp-for="LastName"></label>
 <input asp-for="LastName" />
 <br />
 <label asp-for="ContactMe"></label>
 <input asp-for="ContactMe" />
 <br />
 <input type="submit" value="Submit my details" />
}
  
  // O tambien:
  
  
@model ModelNamespace.Person
<h1>Person details</h1>
 @Html.DisplayNameFor(model => model.FirstName)
 @Html.DisplayFor(model => model.FirstName)
<br />
 @Html.DisplayNameFor(model => model.LastName)
 @Html.DisplayFor(model => model.LastName)
<br />
 @Html.DisplayNameFor(model => model.ContactMe)
 @Html.DisplayFor(model => model.ContactMe)
  
  
```

# Data Annotations

## Display Attribute y DataType Attribute

En el modelo:

```
public class Person
{
 [Display(Name="My Name")]
 public string Name { get; set; }
 [DataType(DataType.Password)]
 public string Password { get; set; }
 [DataType(DataType.Date)]
 public DateTime Birthdate { get; set; }
 [Display(Name="Email Address")]
 public string EmailAddress { get; set; }
 [DataType (DataType.MultilineText)]
 public string Description { get; set; }
}

```
En el controlador:

```

public class PersonController : Controller
{
 [Route("Person/GetDetails")]
 public IActionResult GetDetails()
 {
 return View();
 }
}
```
En la vista:

```
@model ModelNamespace.Person
@Html.EditorForModel()

```
## Display Name For

El texto representado por el Helper Html.DisplayNameFor depende de la clase modelo. Si usaste un Atributo DisplayAttribute para dar un nombre más descriptivo a una propiedad, Html.DisplayNameFor será utilice el valor del parámetro Nombre. De lo contrario, mostrará el nombre de
la propiedad.

El Helper Html.DisplayFor genera diferentes tags de HTML según el tipo de datos de la propiedad que se está presentando. Por ejemplo, si la propiedad es de tipo bool, representa un elemento de entrada HTML con una casilla de verificación 

```
@model ModelNamespace.Person
<h1>Person details</h1>
 @Html.DisplayNameFor(model => model.FirstName)
 @Html.DisplayFor(model => model.FirstName)
<br />
 @Html.DisplayNameFor(model => model.LastName)
 @Html.DisplayFor(model => model.LastName)
<br />
 @Html.DisplayNameFor(model => model.ContactMe)
 @Html.DisplayFor(model => model.ContactMe)

```
## Html.Label For

El HTML Helper Html.LabelFor es similar al Html.DisplayNameFor porque representa el nombre de la propiedad que especifique, considerando el atributo DisplayAttribute si está especificado en el modelo.
Sin embargo, a diferencia del HTML Helper Html.DisplayNameFor, Html.LabelFor representa un elemento <label>.

## Html.Editor For
Representa los elementos de entrada HTML más apropiados y otros controles de formulario para cada tipo de datos de la propiedad en un modelo. 
Por ejemplo, el HTML Helper Html.EditorFor representa <input type = "text"> para una propiedad del tipo cadena.
Si la propiedad cadena está anotada con la decoración [DataType (DataType.MultilineText)], el Helper Html.EditorFor representa un elemento <textarea> en lugar.

```
 @model ModelNamespace.Person
<h1>Get person details</h1>
 @Html.LabelFor(p => p.FirstName)
 @Html.EditorFor(p => p.FirstName)
<br />
 @Html.LabelFor(p => p.LastName)
 @Html.EditorFor(p => p.LastName)
<br />
 @Html.LabelFor(p => p.ContactMe)
 @Html.EditorFor(p => p.ContactMe) 
  
```


