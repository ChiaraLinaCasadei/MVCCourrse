# Entity Framework

Usando Entity Framework, puede acceder objetos utilizando código fuertemente tipado y consultarlos utilizando LINQ.

## Proveedores de bases de datos
EF Core es una capa entre su código y una base de datos.
Se utiliza para conectar su código a la base de datos.
Un proveedor de base de datos es una biblioteca que EF Core lo usa para conectarse a una base de datos. Los proveedores de bases de datos se distribuyen como Paquetes NuGet. 

# Configuración en EF Core

EF Core usa convenciones para construir una base de datos basada en el contenido del contexto de Entity Framework.
Una forma de especificar la configuración en EF Core es mediante Fluent API. Cuando usas Fluent API, escribes la configuración en el método OnModelCreating de la clase
de contexto de Entity Framework.
Por ejemplo, en EF Core puede usar Fluent API para determinar el nombre de una tabla que se crea en una base de datos. Para determinar el nombre de la tabla en la base
de datos, puede usar el método ToTable.
El siguiente ejemplo de código muestra cómo crear una tabla denominada Personas en la base de datos que ser mapeado a la propiedad Candidates de la clase HrContext:

```
public class HrContext : DbContext
{
  public HrContext(DbContextOptions<HrContext> options) : base(options)
  {
  }
  public DbSet<Person> Candidates { get; set; }
  protected override void OnModelCreating(ModelBuilder modelBuilder)
  {
    modelBuilder.Entity<Person>().ToTable("People");
  }
}

```

