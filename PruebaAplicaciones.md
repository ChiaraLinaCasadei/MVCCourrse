# Prueba de Aplicaciones
## Prueba unitaria
Procedimiento que instancia un componente que ha escrito, llama a un aspecto de las funcionalidades del componente, y comprueba que el mismo responde correctamente a las pruebas.

En O.O.P. , las pruebas unitarias crean una instancia de una clase y llamar a uno de sus métodos.
En una app MVC, las pruebas unitarias pueden crear instancias de clases de modelo o controladores, y llamar a sus métodos y acciones.
Las pruebas verifican aspectos individuales de su código, sin depender de servidores de bases de datos, conexiones de red y otra infraestructura. Esto se debe a que las pruebas se centran en el código que escribe.

Al definir muchas pruebas unitarias para su proyecto, puede asegurarse de que cubran todos los aspectos funcionales de su aplicación web.

## Cómo ayuda una prueba unitaria a diagnosticar errores?
Las pruebas verifican un aspecto pequeño y específico del código, es fácil diagnosticar el problema cuando las pruebas fallan.
Utilizar la inyección de dependencias, crear clases falsas e implementar varios métodos como sean necesarios con los resultados esperados. Esto permite realizar pruebas completas para una clase, sin depender de otras entidades.

## Escribir pruebas unitarias para componentes MVC

### Modelos
Son simples de probar porque son clases independientes que puede instanciar y configurar durante la fase de organización de una prueba.
### Controladores
Son clases simples, pero es complejo probarlos con datos **inmemory** en lugar de utilizar una base de datos. Es recomendable probar con datos en memoria, y crear una clase doble de prueba (*repositorio falso*). Los objetos de esta clase se pueden completar con probar datos en la memoria sin consultar una base de datos. Debe comprender cómo escribir dobles de prueba y como escribir una prueba típica para clases MVC.

---

Un repositorio es un patrón de diseño común que se utiliza para separar el código de lógica empresarial de la recuperación de datos.
Normalmente, el repositorio maneja las distintas operaciones CRUD sobre los datos.
En las aplicaciones ASP.NET Core MVC, el repositorio se maneja creando un Servicio.
En general, querrá un repositorio por entidad. Los repositorios son muy útiles para realizar pruebas de los controladores porque los modelos generalmente no usan datos externos.

---

### Implementación de un doble prueba de repositorio
Esta implementación utiliza datos en memoria y una colección de objetos con clave para funcionar como un Entity Framework, pero evita trabajar con una base de datos.

```
publicclassFakeProductRepository : IProductRepository
{
    privateIQueryable<Product> _products;
    
    publicFakeProductRepository()
    {
      List<Product>products = new List<Product>();
      _products = products.AsQueryable();
    }
    publicIQueryable<Product>Products
    {
      get { return _products.AsQueryable(); }
      set { _products = value; }
    }
    publicProductAdd(Productproduct)
    {
      List<Product>products = _products.ToList();
      products.Add(product);
      _products = products.AsQueryable();
      returnproduct;
    }
    publicProductDelete(Productproduct)
    {
      List<Product>products = _products.ToList();
      products.Remove(product);
      _products = products.AsQueryable();
      returnproduct;
    }
    publicProductFindProductById(int id)
    {
      return _products.First(product =>product.Id == id);
    }
}

```
