# CQRS and the Mediator Pattern

The MediatR library was built to facilitate two primary software architecture patterns: CQRS and the Mediator pattern.

## CQR

Command Query Responsibility Segregation.  It's all about splitting the responsibility of commands(saves) and queries(reads)
into different models.

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

## Reference:

https://code-maze.com/cqrs-mediatr-in-aspnet-core/
https://medium.com/dotnet-hub/use-mediatr-in-asp-net-or-asp-net-core-cqrs-and-mediator-in-dotnet-how-to-use-mediatr-cqrs-aspnetcore-5076e2f2880c
https://nishanc.medium.com/understanding-cqrs-pattern-using-net-core-mediatr-3658263cfb16