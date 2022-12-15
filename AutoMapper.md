# Auto Mapper

## What is this?

AutoMapper is a simple library which helps us to transform one object type into another. 
It is convention-based object to object mapper that requires very little configuration

## Installation

```bash
Install-Package AutoMapper.Extensions.Microsoft.DependencyInjection
```
If we install the AutoMapper.Extensions.Microsoft.DependencyInjection package, it will automatically install the AutoMapper package for us since it references it.

## Configuration in startup.cs or program.cs

in .net 5 (startup.cs)

```c#..
public void ConfigureServices(IServiceCollection services)
{
    services.AddAutoMapper(typeof(Startup));
    services.AddControllersWithViews();
}
```

in .net 6 (program.cs)

```c#
builder.Services.AddAutoMapper(typeof(Program)); 
builder.Services.AddControllersWithViews();
```

## Usage

```c#
// Model
public class User
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string Email { get; set; }
    public string Address { get; set; }
}

// View model
public class UserViewModel
{
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string Email { get; set; }
}
```

## Profiles

A good way to organize our mapping configurations is with Profiles. We need to create classes that inherit from Profile class and put the configuration in the constructor:

```c#
public UserProfile()
{
    CreateMap<User, UserViewModel>();
}
```
UserProfile class creates the mapping between our User domain object and UserViewModel. As soon as our application starts and initializes AutoMapper, AutoMapper will scan our application and look for classes that inherit from the Profile class and load their mapping configurations.

Now, let’s define a Controller and use the Auto-Mapping capabilities that we just added:

```c#
public class UserController : Controller
{
    private readonly IMapper _mapper;

    public UserController(IMapper mapper)
    {
        _mapper = mapper;
    }

    public IActionResult Index()
    {
        // Populate the user details from DB
        var user = GetUserDetails();

        UserViewModel userViewModel = _mapper.Map<UserViewModel>(user);

        return View(userViewModel);
    }
}
```
here, we inject the mapper object into the controller. Then, we call the Map() method, which maps
the User object to the UserViewModel object. Furthermore, pay attention to a local method GetUserDetails
that we use for the local data storag which return user model and it's get mapped to UserViewModel simply.

## Mapping to Properties with different Names

But what if we have different property names in our source and destination objects. Let’s take a look at how to do the mapping in these cases.

Let’s modify the property names in UserViewModel class:

```c#
public class UserViewModel
{
    public string FName { get; set; }
    public string LName { get; set; }
    public string Email { get; set; }
}
```

Here we need to map the properties from the User domain object to our UserViewModel:

User.FirstName -> UserViewModel.FName
User.LastName -> UserViewModel.LName
User.EMail -> UserViewModel.Email

So, let’s change the mapping in the UserProfile class:

```c#
public UserProfile()
{
    CreateMap<User, UserViewModel>()
        .ForMember(dest =>
            dest.FName,
            opt => opt.MapFrom(src => src.FirstName))
        .ForMember(dest =>
            dest.LName,
            opt => opt.MapFrom(src => src.LastName))
}
```

## What happened in behind

AutoMapper uses a programming concept called Reflection to retrieve the type metadata of objects.
We can use reflection to dynamically get the type from an existing object and invoke its methods or
access its fields and properties. 

Then, based on the conventions and configurations defined, we can easily map the properties of the two
types. AutoMapper was built around this concept.

## Best Practices:

As with every other component that we use in our application, there are certain usage guidelines and best practices that we need to follow while using AutoMapper.

Do’s 

* Always use the AutoMapper.Extensions.Microsoft.DependencyInjection package in ASP.NET Core with services.AddAutoMapper(assembly[]). This package will perform all the scanning and dependency injection registration. We only need to declare the Profile configurations.
* Always organize configuration into Profiles. Profiles allow us to group a common configuration and organize mappings by usage. This lets us put mapping configuration closer to where it is used, instead of a single file of configuration that becomes difficult to edit/maintain.
* Always use configuration options supported by LINQ over their counterparts as LINQ query extensions have the best performance of any mapping strategy.
* Always flatten DTOs. AutoMapper can handle mapping properties A.B.C into ABC. By flattening our model, we create a more simplified object that won’t require a lot of navigation to get at data.
* Always put common simple computed properties into the source model. Similarly, we need to place computed properties specific to the destination model in the destination model.

Don’ts

* Do not call CreateMap() on each request. It is not a good practice to create the configuration for each mapping request. Mapping configuration should be done once at startup.
* Do not use inline maps. Inline maps may seem easier for very simple scenarios, but we lose the ease of configuration.
* If we have to write a complex mapping behavior, it might be better to avoid using AutoMapper for that scenario.
* Do not put any logic that is not strictly mapping behavior into the configuration. AutoMapper should not perform any business logic, it should only handle the mapping.
* Avoid sharing DTOs across multiple maps. Model your DTOs around individual actions, and if you need to change it, you only affect that action.
* Do not create DTOs with circular associations. AutoMapper does support it, but it’s confusing and can result in quite bad performance. Instead, we can create separate DTOs for each level of the hierarchy we want.

## Reference

https://code-maze.com/automapper-net-core/
