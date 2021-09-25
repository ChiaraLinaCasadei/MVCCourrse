# Crear un Diseño

Para crea una vista de diseño:

* Debe almacenar los archivos de diseño en la carpeta \Views\Shared
* Puede utilizar el método @RenderBody para colocar el contenido de una vista en el diseño.
* Puede usar la propiedad ViewBag para pasar información entre una vista y el diseño.
* Vinculación de vistas y diseños.

```
<!DOCTYPE html>
 <html>
 <head>

  <title>@ViewBag.Title</title>
 </head>
   <body>
     <div>
        @RenderBody()
     </div>
   </body>
 </html>

```
## Para vincular vistas y diseños

Puede agregar la directiva Layout en la parte superior del archivo de vista.
Puede utilizar el archivo _ViewStart.cshtml para definir el diseño.
Agregue el archivo _ViewStart.cshtml a la carpeta \Views de su proyecto.mj

## Utilizar secciones en un diseño

Utilice el método @RenderSection en un diseño

```
  @RenderSection("section1")
  @RenderBody()
  @RenderSection("section2", false)

```
Utilice la directiva @section en una vista

```
@section section1
 {
    <div>This is section1 content</div>
 }
 
 <div>This is the body</div>
 
```
## Usando CSS - Importar estilos

Después de importar el archivo CSS:
Debe modificar el diseño de la aplicación web utilizando el elemento <link>
