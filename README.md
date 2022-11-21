# Learn Dotnet core 

```
dotnet new --list
dotnet new webapi -o MyAPIProject # it would create a webapi project
dotnet run  # to run the application
install-package Microsoft.EntityFrameworkCore # to install entity framework package
install-package Microsoft.EntityFrameworkCore.InMemory

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
}


```
