## Introduction to MVC

**[What is the MVC pattern?](https://docs.asp.net/en/latest/mvc/overview.html)**

![Alt Text](https://github.com/LadyNaggaga/ASP.NETCoreMVA/blob/master/Images/MVC.png)

*The illustration above is a simple description of the MVC model.*

The following things happen when you visit a website
    ** Let's go through an everyday example to understand MVC first; I want to buy an item on Amazon, first the request is made (Reaquest), the request is known to the controller, the controller sends the request to the model, then again the controller reviews it (Response) and sends it to the view layer after checking it for the last time. And if the pen is in stock, it says there is a model, the controller takes it, directs it to the view layer, and sends it to us in the UI layer, which is the final version.

- You request to view a page by entering a URL.
- The **Controller** receives the page request.
- The **Controller** sends the request to the **Model** to receive all the requested data.
- **Model** stores and packages data to be presented to you in **View**

![Alt Text](https://github.com/LadyNaggaga/ASP.NETCoreMVA/blob/master/Images/MVCPattern.png)

### Create a new Web Application
- New Project File -> .NET Core -> ASP.NET Core -> Web Application
- Open "csproj" file and add "Microsoft.AspNetCore.Mvc" in "dependencies" section and save:

    XML
    <Item Group>
      <PackageReference Include="Microsoft.AspNetCore.Mvc" Version="1.1.1" />
    </ItemGroup>
    ```
- Add a "Controllers" folder to your app
- Create a new class named "HomeController" in the new folder and add the following code:

``c#
Using Microsoft.AspNetCore.Mvc;

public class HomeController
{
    [HttpGet("/")]
    public string Index() => "Hello from MVC!";
}
```
- Replace the Routing middleware from the previous step with the middleware in MVC services and 'Startup.cs' as shown:


    ``C#
    public invalid ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
    }
  
    public invalid Configuration(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        loggerFactory.AddConsole();

        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseMvc();
    }
```
- Run the site and verify that the message is returned from your MVC controller
- If you have time, try the following:

    - Modify the controller to render a view instead of returning a string directly
    - Play with the "[HttpGet("/")]" attribute to change the path that the action method will match

Addendum: Internal Routing


#### Using the Routing Middleware
## Startup file imported into program.cs!!!(program.cs)
- Open the Startup.cs file
- Add a routing service to the ConfigureServices method in Startup.cs
``C#
          public invalid ConfigureServices(IServiceCollection services)
          {
              services.AddRouting();
          }
```

- In the "Configure" method, create a "RouteBuilder" with a handler for the root of the site and add it to the middleware pipeline:
  
    ``C#
    Using Microsoft.AspNetCore.Routing;
    ...
    public invalid Configuration(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        loggerFactory.AddConsole();

        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        var routeBuilder = new RouteBuilder(application);

        routeBuilder.MapGet("", context => context.Response.WriteAsync("Hello from redirect!"));

        app.UseRouter(routeBuilder.Build());
    }
    ```
    - Run the site and verify that your middleware is accessed via redirect (Ctrl+F5)
    - Add another route that matches a sub-route:

    ``c#
    routeBuilder.MapGet("alt", context => context.Response.WriteAsync("Hello from bottom!"));
    ```
  
### Capture and Use Data
- Add another way that captures the name of an item from the URL, e.g. "item/{itemName}" and displays in the response:
  
    ``c#
    routeBuilder.MapGet("item/{itemName}", context => context.Response.WriteAsync($"Item: {context.GetRouteValue("itemName")}"));
    ```
- Run the site and verify that your new route is working. Browsing to "/item/monkey" should display the message "Item: monkey".
- To add a route restriction to the captured segment, change the route and force it to be a number:
  
    ``c#
    routeBuilder.MapGet("item/{id:int}", context => context.Response.WriteAsync($"ItemID: {context.GetRouteValue("id")}"));
    ```
- Run the site again and see that the route is mapped only when the segment captured is a valid number. Browsing to "/item/5" will work, but browsing to "/item/monkey" will now show a missing page (HTTP 404) error.
- Change the router to include both versions of the above route (with and without route restriction)
- Try changing the order in which the routes are added and which route is matched for a particular URL
