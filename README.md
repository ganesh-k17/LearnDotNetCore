# Learn Dotnet core 


```bash
dotnet new --list
dotnet new webapi -o MyAPIProject # it would create a webapi project
dotnet run  # to run the application
install-package Microsoft.EntityFrameworkCore # to install entity framework package
install-package Microsoft.EntityFrameworkCore.InMemory
```

```c#
public class shopContext : DbContext
{
  public ShopContext(DbContextOptions<ShopContext> options) : base(options)
  {
  }
  
  public override void OnModelCreating(ModelBuilder modelBuilder)
  {
    modelBuilder.Entity<Category>()
      .HasMany(c => c.Products)
      .WithOne(a => a.Category)
      .HasForeignKey(a => a.CategoryId);
  }
  
  modelBuilder.Seed();  // Fill the sample data
  
  public DbSet<Product> Products {get;set;}
  public DbSet<Category> Catogories {get;set;}
}

// Below lines should be added in program.cs to configure database for entity framework

var builder = WebApplication.CreateBuilder(args);
builder.Services.AddContext<ShopContext>(options => options.UseInMemoryDatabase("Shop"));

```

```c#
[Route("api/[controller]")]
[ApiController]
public class ProductController : ControllerBase
{ 
  private readonly ShopContext _context;
  
  public ProductsController (ShopContext context) 
  {
    _context = context;
    _context.Database.EnsureCreated(); // It would confirm the seeding happen
  }
  
  [HttpGet]
  public IEnumerable<Product> GetAllProducts()
  {
    return _context.Products.ToArray();
  }
  
  // or
  
  [HttpGet]
  public ActionResult<IEnumerable<Product>> GetAllProducts()
  {
    var products = _context.Products.ToArray();
    return Ok(products);  // return products with 200 OK status
  }
  
  
  [HttpGet, Route("api/products/{id}")] or  [HttpGet, Route("{id}")] // since the route is in controller (or ) [HttpGet("{id}")]
  public Product Getproduct (int id)
  {
    var product = _context.Products.Find(id);
    return product;
  }
  
  // or 
  
  [HttpGet, Route("/products/{id}")]
  public ActionResult Getproduct (int id)
  {
    var product = _context.Products.Find(id);
    return Ok(product);
  }
}


```

## Error handling

```c#
 [HttpGet, Route("/products/{id}")]
  public ActionResult Getproduct (int id)
  {
    var product = _context.Products.Find(id);
    
    if(product ==  null)
      return NotFound();
      
    return Ok(product);
  }
```

## Asynchroous Action

```c#
[HttpGet, Route("/products/{id}")]
  public async Task<ActionResult> Getproduct (int id)
  {
    var product = await _context.Products.FindAsync(id);
    
    if(product ==  null)
      return NotFound();
      
    return Ok(product);
  }
```

## Model Binding

```
[FromBody] - Data from the body of the HTTP requeest, mostly POST/PUT
[FromRoute] - Data from the route template
[FromQuery] - Data from the URL

```

Adding Items:

```c#
[HttpPost]
public async Task<ActionResult<Product>> PostProduct(Product product)
{
  _context.Products.Add(product);
  await _context.SaveChangesAsync();
  
  return CreatedAtAction(   // call the GetProduct action
    "GetProduct",
    new { id = product.Id },
    product);
}

```

Model Validation:

[Required] - attribute to use in model for validation

```c#
builder.Services.AddControllers()
  .ConfiugreApiBehaviorOptions(options => 
  {
    options.SuppressModelStateInvalidFilter = true;  // This option is to stop validate on controller level.      
                                                     // So we have to validate in action level as in the next code block
  });
```
```c#
 // In action method
 
 if(!ModelState.IsValid){
    return BadRequest();  
 }
```
