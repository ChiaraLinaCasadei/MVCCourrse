
La autenticación se utiliza para verificar credenciales de usuario y proporcionar acceso a la aplicación.
Es el proceso para determinar la identidad de un usuario dentro de la aplicación, el cual proporciona un nombre de usuario único junto con una contraseña.

Crear un entorno de autenticación es crucial, ya que puede agregar una capa importante de seguridad a sus aplicaciones.
La mayoría de la autenticación estos días está basada en **tokens** .
Se realiza una autenticación inicial, el servidor genera un token y lo envía al cliente.
El cliente luego envía nuevamente el token en cada solicitud futura, lo que permite al servidor confirmar que es el mismo usuario.
Si el token es incorrecto o vencido, el usuario deberá autenticarse nuevamente.

# Configuración de la identidad de ASP .NET Core

ASP .NET Core Identity es una solución para manejar el inicio de sesión dentro de aplicaciones ASP .NET Core.
La infraestructura Entity Framework se usa para gestionar la configuración e interactuar con los datos del usuario.
Esto en la mayoría de los casos quiere decir que se puede usar la misma base de datos que otras partes de app, independientemente del tipo para manejar el inicio y cierre de sesión, ahorrando el requisito de configurar una infraestructura adicional.

Identity también tiene servicios que puede inyectar en toda su aplicación, para afministrar los usuarios del sistema y realizar operaciones de inicio y cierre de sesión.

##  Configurar la clase de usuario
Para configurar Identity, primero debe decidirse qué propiedades desea almacenar sobre los usuarios de su aplicación.
De forma predeterminada, propiedades como la dirección de correo electrólico, el nombre de usuario y el hash de contraseña se almacenan como parte de la clase predeterminada IdentitUser, que se expone desde el namespace **Microsoft.AspNetCore.Identity**

Si necesita alguna propiedad adicional, deberá crear su propia case y hacerla heredar de IdentityUser. Si no, puede usar directamente ésta última.

```
public class WebsiteUser : IdentityUser
{
public string UserHandle { get; set; } //Esta propiedad podría usarse para determinar como se muestra el nombre del usuario durante las operaciones en linea, sin revelar el nombre de usuario utilizado para iniciar sesion
}

```

## Configurar el contexto de la base de datos
Hay que configurar la clase DBContext para admitir la identidad. Para esto, la clase de contexto deberá heredar de la clase IdentityDbContext en lugar de DbContext.
También deberá proporcionar la clase de identidad de usuario elegida como un tipo genérico para la clase IdentityDbContext. Deberá proporcionar la clase que desea utilizar, de lo contrario no podrá accederse a las propiedades.

Una ventaja de IdentityDbContext es que no necesitará agregar una instancia de DbSet para administrar las colecciones de usuarios.
La lógica para esto está integrada como parte del propio IdentityDbContext.

```
publicclassAuthenticationContext : IdentityDbContext<WebsiteUser>
{
    public AuthenticationContext(DbContextOptions<AuthenticationContext>options) :
    base(options)
    {
    }
}
```

## Configurar la identidad en Startup
Una vez estableclido el DbContext, se deben agregar las llamadas a servicios ASP .NET Core Identity y middleware.
Dentro del método ConfigureServices de Startup, deberá llamar a AddDbContext en el parámetro IServiceCollection con una clase que hereda de IdentityDbContext.
Instanciando la misma como cualquier otro DbContext, con los mismo parámetros.
También deberá agregar una llamada al método **AddDefaultIdentity<ClaseUsuario>**
Este último método espera la clase que se utilizará para administrar usuarios en la aplicación. Puede ser una clase personalizada que hereda de IdentityUser, o el propio IdentityUser.

Hay que agregar al pipelina una llamada a **AddEntityFrameworkStores<NombreDelDBContext>**() inmediatamente después de AddIdentity.

```
public void ConfigureServices(IServiceCollection services)
{
  services.AddMvc();
  services.AddDbContext<AuthenticationContext>(options =>
  options.UseSqlite("Data Source=user.db"));
  services.AddDefaultIdentity<WebsiteUser>()
  .AddEntityFrameworkStores<AuthenticationContext>();
}

```

Y en Configure se necesita cargar el middleware para manejar el proceso de autenticación y habilitar la identidad.
Es importante que llame a UseAuthentication antes de llamar a UseMvc.

```
public void Configure(IApplicationBuilder app, AuthenticationContext authContext)
{
authContext.Database.EnsureDeleted();
authContext.Database.EnsureCreated();
app.UseAuthentication();
app.UseMvcWithDefaultRoute();
app.Run(async (context) =>
{
awaitcontext.Response.WriteAsync("");
});
}

```

## Ajustes de Usuario

Se utilizan para determinar una configuraciones respecto al registro de usuarios en el sistema. 
Se puede usar para requerir una dirección de correo única para cada usuario configurado, o para determinar qué letras y símbolos se pueden usar en la aplicación.

```
services.AddDefaultIdentity<WebsiteUser>(options =>
{
    options.User.RequireUniqueEmail = true;
    options.User.AllowedUserNameCharacters =
    "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789 -._@+";
})
.AddEntityFrameworkStores<AuthenticationContext>();
```
## Configuración de Bloqueo

Cuando la aplicación detecta varios intentos de iniciar sesión con el mismo nombre de usuario pero contraseñas no válidas, se puede configurar para bloquear al usuario para futuros inicios de sesión durante un período de tiempo.
Para aplicar el bloqueo, deberá asegurarse de que el parámetro lockoutOnFailure en la llamada al método PasswordSignInAsync es verdadero o se ignorará la configuración
de bloqueo.
Puede utilizar la propiedad Lockout de IdentityOptions para configurar los ajustes relacionados con el bloqueo.

```
varresult = await _signInManager.PasswordSignInAsync(model.Username, model.Password, true, true);

```
ejemplo de la configuración de bloqueo en Identity

```
services.AddDefaultIdentity<WebsiteUser>(options =>
{
    options.Lockout.AllowedForNewUsers = true;
    options.Lockout.MaxFailedAccessAttempts = 3;
    options.Lockout.DefaultLockoutTimeSpan = TimeSpan.FromMinutes(1);
})
.AddEntityFrameworkStores<AuthenticationContext>();

```


## Configuraciones de Contraseña

```

services.AddDefaultIdentity<WebsiteUser>(options =>
{
  options.Password.RequiredLength = 10;
  options.Password.RequiredUniqueChars = 3;
  options.Password.RequireDigit = true;
  options.Password.RequireNonAlphanumeric = true;
  options.Password.RequireUppercase = true;
  options.Password.RequireLowercase = false;
})
.AddEntityFrameworkStores<AuthenticationContext>();

```
## Registrarse

La propiedad SignIn de IdentityOptions se puede utilizar para solicitar al usuario que confirme su correo electrónico o número de teléfono en la aplicación. No será posible iniciar sesión hasta que se confirme correctamente.
## Configuración de Cookies
Llamando a ConfigureApplicationCookie en ConfigureServices, pede utiliar un parámetro CookieAuthenticationOptions para establecer varias propiedades para las cookies en su app.

Estas configuraciones incluyen el nombre de la cookie y el tiempo de vencimiento de las mismas.

## Personalizar proveedores con ASP.NET Core
De vez en cuando se necesitan opciones de negocio adicionales a EntityFramework.
Los requisitos pueden dictar la necesidad de utilizar la infraestructura interna del directorio activo para iniciar sesión o es posible que desee permitir usuarios de otros Frameworks para usar su aplicación, o incluso no pueda usar SQL Server para almacenamiento de datos.


## Autenticación de Windows

Al utilizarla, el directorio activo se utilizará para determinar las credenciales de usuario y permisos, permitiendo que los usuarios sean administrados y creados por administradores internos. Esto ayuda a crear apliaciones y elimina la necesidad de administrar usuarios manualmente.

## Uso de proveedores externos

Podes registrarte usando, por ejemplo, tu cuenta Google. Así el proceso se vuelve mas rápido.

Como resultado de un inicio de sesion exitoso, su aplicación recibirá un token de éxito que luego se puede usar para obtener información adicional sobre el usuario.

## Uso de proveedores de almacenamiento alternativos
 Por defecto se usa EntityFramework, pero se puede usar Azure, MongoDB, Redis, etc.

