# CQRS and the Mediator Pattern

The MediatR library was built to facilitate two primary software architecture patterns: CQRS and the Mediator pattern.

## CQR

Command Query Responsibility Segregation.  It's all about splitting the responsibility of commands(saves) and queries(reads)
into different models.

CQRS pattern offers the following advantages:

* Separation of Concern – We have separate models for read and write operations which not only gives us flexibility but also keeps our models simple and easy to maintain. Normally, the write models have most of the complex business logic whereas the read models are normally simple.
* Better Scalability – Reads operations often occur way more than writes so keeping queries separate than commands makes our applications highly scalable. Both read and write models can be scaled independently even by two different developers or teams without any fear of breaking anything.
* Better performance – We can use a separate database or a fast cache e.g. Redis for read operations which can improve application performance significantly.
* Optimized Data Models – The read models can use a schema or pre-calculated data sources that are optimized for queries. Similarly, the write models can use a schema that is optimized for data updates.

## Configure MediatR in ASP.NET Core application

1. NuGet: To use MediatR, you need to install below NuGet packages.

```bash
PM> Install-Package MediatR
PM> Install-Package MediatR.Extensions.Microsoft.DependencyInjection
```

2. Configuration: 

MediatR has no dependencies. You need to configure a single factory delegate, used to instantiate all handlers, pipeline behaviors, and pre/post-processors. Then let MediatR know in what assemblies are those profiles defined by calling the IServiceCollection extension method AddMediatR at startup. This extension method, allowing you to register all handlers and pre/post-processors in a given assembly.

```c#
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        // Register MediatR services
        services.AddMediatR(typeof(Startup));

        // Way-2
        //services.AddMediatR(Assembly.GetExecutingAssembly());

        // Register MediatR services from multiple assembly.
        //services.AddMediatR(Assembly.GetExecutingAssembly(), typeof(ICustomerService).Assembly);
    }
}
```

## Reference

* https://code-maze.com/cqrs-mediatr-in-aspnet-core/
* https://medium.com/dotnet-hub/use-mediatr-in-asp-net-or-asp-net-core-cqrs-and-mediator-in-dotnet-how-to-use-mediatr-cqrs-aspnetcore-5076e2f2880c
* https://nishanc.medium.com/understanding-cqrs-pattern-using-net-core-mediatr-3658263cfb16
* https://www.ezzylearning.net/tutorial/implement-cqrs-pattern-in-asp-net-core-5
* https://www.ezzylearning.net/tutorial/mediator-design-pattern-in-asp-net-core