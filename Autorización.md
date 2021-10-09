# La autorización dentro de la app
Por ejemplo, puede bloquear la página de aplicación de una tienda: mediante una autorización simple, puede evitar que los productos para adultos se muestren a los usuarios más jóvenes al establecer la
autorización de Claims, o puede permitir que solo los administradores accedan a los detalles financieros de su sitio web mediante la función de autorización.

## Configuración de la autorización
La clase IdentityRole está diseñada para administrar roles dentro de la aplicación, de manera similar a como IdentityUser gestiona usuarios y se utiliza para iniciar la clase
RoleManager. 
Sin embargo, a diferencia de IdentityUser, que esa menudo extendido, IdentityRole no se extiende comúnmente y la clase base se usa con mayor frecuencia.
Aunque los nombre AddDefaultIdentity y AddIdentity son similares, operan de manera muy diferente y sidesea utilizar la lógica ASP.NET Core MVC junto con la autorización, deberá utilizar AddIdentity.

```
services.AddIdentity<ClaseUsuario, ClaseRole>()
.AddEntityFrameworkStores<AuthenticationContext>();
```

## Autorización Simple

Para implementar la autorización simple, todo lo que necesita es agregar el atributo [Authorize].
El atributo [Authorize] se puede agregar a una clase de controlador, evitando que usuarios no autorizados accedan a la clase
mientras no haya iniciado sesión. Los usuarios que no hayan iniciado sesión que intenten navegar hasta el controlador serán redirigidos a la URL Login.

Este atributo no se limita a Controller, tambien se puede agregar a acciones individuales. 
El atributo [AllowAnonymous] habilita acceso a la acción en sí, mientras que el resto del controlador no es accesible. Un ejemplo de esto podría ser una página web que le permite ver datos, al tiempo que
requiere iniciar sesión para realizar cambios.

## Opciones de Autorización

### Basada en Roles
Se debe crear roles dentro del sistema. Esto puede hacerse utilizando el servicio RoleManger<NombreRole>, que es instanciado por la llamada a AddIdentity dentro de ConfigureServices.

El Role Manager será del tipo proporcionado para el rol en la llamada a AddIdentity, generalmente IdentityRole, y se puede inyectar en toda la aplicación, lo que le permite administrar roles.
  
1) Crear roles
  
 ```
  
public async void CreateRoles(RoleManager<IdentityRole> roleManager)
{
    string[] roleNames = { "Administrator", "Manager", "User" };
    foreach (varroleName in roleNames)
    {
        boolroleExists = awaitroleManager.RoleExistsAsync(roleName);
        if (!roleExists)
        {
            IdentityRole role = new IdentityRole();
            role.Name = roleName;
            awaitroleManager.CreateAsync(role);
        }
    }
}
  
 ```
  
2) Asignar roles a los usuarios
  

```
[HttpPost]
public async Task<IActionResult> Register(RegisterViewModel model)
{
  if (ModelState.IsValid)
  {
      WebsiteUseruser = new WebsiteUser
      {
        UserHandle = model.UserHandle,
        UserName = model.Username,
        Email = model.Email
      };
  
      varresult = await _userManager.CreateAsync(user, model.Password);
      if (result.Succeeded)
      {
          var addedUser = await _userManager.FindByNameAsync(model.Username);
          await _userManager.AddToRoleAsync(addedUser, "Administrator");
          await _userManager.AddToRoleAsync(addedUser, "User");
          return await Login(model);
      }
  }
  return View();
}
  
```
3) Autorizar con Roles

```
[Authorize(Roles = "Administrator")]
public async Task<IActionResult> DeleteUser(string username)
{
  varuser = await _userManager.FindByNameAsync(username);
  if (user != null)
  {
    await _userManager.DeleteAsync(user);
  }
  return View();
}

  //y si quisiera dar acceso a múltiples roles?
  
  [Authorize(Roles = "Administrator, Manager")]

```
  
### Basado en Claims
  
Un Claim es un par valor-clave que define al usuario, en lugar de los permisos del usuario. Los ejemplos de Claims incluyen correo electrónico o dirección e incluso es posible agregar claims personalizados.

La autorización basada en Claims se utiliza para determinar si el usuario tiene un tipo específico de Claim. Esto puede ser útil para cuando necesite que el usuario ingrese detalles
específicos antes de acceder a páginas específicas.
Por ejemplo, puede solicitar que el usuario proporcione una dirección de correo electrónico para permitir que se registre en un boletín o puede requerir que el usuario ingrese una dirección postal para permitir el acceso a un botón de envío.
  
1) Crear un Claim  

Ejemplo de agegar un Claim a un nuevo usuario
  
```
[HttpPost]
public async Task<IActionResult> Register(RegisterViewModel model)
{
      if (ModelState.IsValid)
      {
        WebsiteUseruser = new WebsiteUser
        {
            UserHandle = model.UserHandle,
            UserName = model.Username,
            Email = model.Email
        };
        varresult = await _userManager.CreateAsync(user, model.Password);
        if (result.Succeeded)
        {
          var added User = await _userManager.FindByNameAsync(model.Username);
          if (!string.IsNullOrWhiteSpace(model.Email))
          {
              Claim claim = new Claim(ClaimTypes.Email, model.Email);
              await _userManager.AddClaimAsync(addedUser, claim);
          }
        return await Login(model);
        }
      }
    return View();
}

```
  
  
  
