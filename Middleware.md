# Middleware

```c#
app.UseMiddleware<EnvironmentMiddleWare>();

using System.Diagnostics;

internal class EnvironmentMiddleWare
{
	public RequestDelegate Next { get; private set; }
	public string EnvironmentName { get; private set; }

	public EnvironmentMiddleWare(RequestDelegate next, IWebHostEnvironment env)
	{
		Next = next;
		EnvironmentName = env.EnvironmentName;
	}
	public async Task Invoke(HttpContext context)
	{
		var timer = Stopwatch.StartNew();
		context.Response.Headers.Add("X-HostingEnvironmentName", new[] { EnvironmentName });

		await Next(context);

		if(context.Response.ContentType != null && context.Response.ContentType.Contains("application/json"))
		{
			await context.Response.WriteAsync($"<p>From {EnvironmentName} in {timer.ElapsedMilliseconds} ms</p>");
		}
	}
}
```
