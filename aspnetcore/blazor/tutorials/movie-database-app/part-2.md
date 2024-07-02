---
title: Build a Blazor movie database app (Part 2 - Add and scaffold a model)
author: guardrex
description: This part of the Blazor movie database app tutorial explains how to add a movie class to the app and scaffold the database and UI from the movie class.
monikerRange: '>= aspnetcore-8.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/14/2024
uid: blazor/tutorials/movie-database-app/part-2
zone_pivot_groups: tooling
---
# Build a Blazor movie database app (Part 2 - Add and scaffold a model)

<!-- UPDATE 9.0 Activate after release

[!INCLUDE[](~/includes/not-latest-version.md)]

-->

This article is the second part of the Blazor movie database app tutorial that teaches you the basics of building an ASP.NET Core Blazor Web App with features to manage a movie database.

In this part of the series:

* A class is added to the app representing a movie in a database.
* [Entity Framework Core (EF Core)](/ef/core) services and tooling create a database context and database. EF Core is an object-relational mapper (O/RM) that simplifies data access. You write model classes first, and EF Core creates the database from the model classes, which is called *scaffolding*.
* Tooling scaffolds a Razor component-based UI for interacting with the movie records in the database via the database context.

## Add a data model

:::zone pivot="vs"

In **Solution Explorer**, right-click the `BlazorWebAppMovies` project and select **Add** > **New Folder**. Name the folder `Models`.

Right-click the `Models` folder. Select **Add** > **Class**. Name the file `Movie.cs`, which creates a public class `Movie` in the file with the namespace `BlazorWebAppMovies.Models`.

Add the following properties to the `Movie` class:

```csharp
public int Id { get; set; }

public string? Title { get; set; }

public DateOnly ReleaseDate { get; set; }

public string? Genre { get; set; }

public decimal Price { get; set; }
```

After you paste the preceding properties between the braces (`{...}`) of the `Movie` class, the file contains the following code:

:::zone-end

:::zone pivot="vsc"

Add a folder to the project named `Models`.

Add a class file to the `Models` folder named `Movie.cs`.

Use the following contents for the `Movie.cs` file:

:::zone-end

:::zone pivot="cli"

Add a folder to the project named `Models`.

Add a class file to the `Models` folder named `Movie.cs`.

Use the following contents for the `Movie.cs` file:

:::zone-end

```csharp
using System.ComponentModel.DataAnnotations;

namespace BlazorWebAppMovies.Models;

public class Movie
{
    public int Id { get; set; }

    public string? Title { get; set; }

    public DateOnly ReleaseDate { get; set; }

    public string? Genre { get; set; }

    public decimal Price { get; set; }
}
```

The `Movie` class contains:

* A record identifier property (`Id`), which is required by EF Core and the database to track records. In the database, the `Id` property is the database record's primary key.
* Other properties that describe aspects of a movie: the movie's title (`Title`), release date (`ReleaseDate`), genre (`Genre`), and price (`Price`). 

The question mark on a `string` type indicates that the property is nullable (it can hold a `null` value).

The EF Core database provider selects data types based on the .NET types of the model's properties. The provider also takes into account other metadata provided by <xref:System.ComponentModel.DataAnnotations>, which are a set of attribute classes placed above a model's property with the following format, where the `{ANNOTATION}` placeholder is the annotation name:

```csharp
[{ANNOTATION}]
```

Add the following namespace to the top of the `Movie.cs` file:

```csharp
using System.ComponentModel.DataAnnotations.Schema;
```

Add the following two data annotation attributes immediately above the `Price` property to specify that it represents a currency:

```csharp
[DataType(DataType.Currency)]
[Column(TypeName = "decimal(18, 2)")]
```

The `Price` property in the `Movie` class file after adding the preceding data annotations:

```csharp
[DataType(DataType.Currency)]
[Column(TypeName = "decimal(18, 2)")]
public decimal Price { get; set; }
```

These annotations specify:

* That the property holds a currency data type.
* The database column is a decimal of 18 digits with two decimal places.

More information on data annotations, including adding data annotations for validation, is covered in a later part of the tutorial series.

:::zone pivot="vs"

Select **Build** > **Build Solution** from the menu bar or press <kbd>F6</kbd> on the keyboard. Confirm in the **Output** panel that the build succeeded.

:::zone-end

:::zone pivot="vsc"

## Add Nuget packages and tools

To add the required NuGet packages and tools, execute the following .NET CLI commands in the **Terminal** (**Terminal** menu > **New Terminal**).

Paste all of the following commands at the prompt (`>`) of the **Terminal** at once. When you paste multiple commands, a warning appears telling you that multiple commands will execute. Dismiss any warning that appears and go ahead with pasting all of the commands.

When you paste multiple commands, ***the last command doesn't execute*** until you press <kbd>Enter</kbd> on the keyboard. When the last command appears at the prompt after the other commands have run, press <kbd>Enter</kbd>.

```dotnetcli
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool install --global dotnet-ef
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet add package Microsoft.AspNetCore.Components.QuickGrid
dotnet add package Microsoft.AspNetCore.Components.QuickGrid.EntityFrameworkAdapter
```

> [!WARNING]
> Make sure that you pressed <kbd>Enter</kbd> on the keyboard to execute the last command in the command shell (**Terminal** tab).

Ignore the following messages if they appear:

> :::no-loc text="A tool with the package Id 'dotnet-aspnet-codegenerator' could not be found.":::

> :::no-loc text="A tool with the package Id 'dotnet-ef' could not be found.":::

These messages mean that these tools haven't been installed on the system. The reason that commands to uninstall the tools are executed even if they've never been installed is to make sure that the latest tools are present for the tutorial.

The preceding commands add:

* [Command-line interface (CLI) tools for EF Core](/ef/core/miscellaneous/cli/dotnet)
* [`aspnet-codegenerator` scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator)
* Design time tools for EF Core
* The SQLite and SQL Server providers with the EF Core package as a dependency
* [`Microsoft.VisualStudio.Web.CodeGeneration.Design`](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design) for scaffolding


In the **Command Palette** (<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>P</kbd>), use the `.NET: Build` command to build the app.

Confirm that the app built successfully.

:::zone-end

:::zone pivot="cli"

## Add Nuget packages and tools

To add the required NuGet packages and tools, execute the following .NET CLI commands in a command shell opened to the project's root folder.

Paste all of the following commands into the command shell prompt at once. When you paste multiple commands into a command shell prompt, a warning appears telling you that multiple commands will execute. Dismiss any warning that appears and go ahead with pasting all of the commands.

When you paste multiple commands into a command shell, ***the last command doesn't execute*** until you press <kbd>Enter</kbd> on the keyboard. When the last command appears at the prompt after the other commands have run, press <kbd>Enter</kbd>.

```dotnetcli
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool install --global dotnet-ef
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet add package Microsoft.AspNetCore.Components.QuickGrid
dotnet add package Microsoft.AspNetCore.Components.QuickGrid.EntityFrameworkAdapter
```

> [!CAUTION]
> Make sure that you pressed <kbd>Enter</kbd> on the keyboard to execute the last command in the command shell.

Ignore the following messages if they appear:

> :::no-loc text="A tool with the package Id 'dotnet-aspnet-codegenerator' could not be found.":::

> :::no-loc text="A tool with the package Id 'dotnet-ef' could not be found.":::

These messages mean that these tools haven't been installed on the system. The reason that commands to uninstall the tools are executed even if they've never been installed is to make sure that the latest tools are present for the tutorial.

The preceding commands add:

* [Command-line interface (CLI) tools for EF Core](/ef/core/miscellaneous/cli/dotnet)
* [`aspnet-codegenerator` scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator)
* Design time tools for EF Core
* The SQLite and SQL Server providers with the EF Core package as a dependency
* [`Microsoft.VisualStudio.Web.CodeGeneration.Design`](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design) for scaffolding


In a command shell opened to the project's root folder, execute the [`dotnet build`](/dotnet/core/tools/dotnet-build) command:

```dotnetcli
dotnet build
```

Confirm that the app built successfully.

:::zone-end

## Scaffold the model

In this section, the `Movie` model is used to *scaffold* a database context and a UI for managing movies in the database. .NET scaffolding is a code generation framework for .NET applications. You use scaffolding to quickly add database and UI code that interacts with data models.

:::zone pivot="vs"

Right-click on the `Components/Pages` folder and select **Add** > **New Scaffolded Item**.

![New Scaffolded Item](~/blazor/tutorials/movie-database-app/part-2/_static/new-scaffolded-item.png)

With the **Add New Scaffold Item** dialog open to **Installed** > **Common** > **Razor Component**, select **Razor Components using Entity Framework (CRUD)**. Select the **Add** button.

*CRUD* is an acronym for Create, Read, Update, and Delete. The scaffolder produces create, edit, delete, details, and index components for the app.

![Scaffold item](~/blazor/tutorials/movie-database-app/part-2/_static/install-common-razor-component.png)

Complete the **Add Razor Components using Entity Framework (CRUD)** dialog:

* The **Template** drop down list includes other templates for specifically creating create, edit, delete, details, and list components. This drop down list comes in handy when you only need to create a specific type of component scaffolded to a model class. Leave the **Template** drop down list set to **CRUD** because we want to scaffold a full set of components.
* In the **Model class** dropdown list, select **Movie (BlazorWebAppMovies.Models)**.
* For **DbContext class**, select the **+** (plus sign) button.
* In the **Add Data Context** modal dialog, the class name `BlazorWebAppMovies.Data.BlazorWebAppMoviesContext` is generated. Use the default generated value. Select the **Add** button.
* After the model dialog closes, the **Database provider** dropdown list defaults to **SQL Server**. When you're building apps in the future, you can select the appropriate provider for the database that you're using. The options include SQLite, PostgreSQL, and Azure Cosmos DB. Leave the **Database provider** drop-down set to **SQL Server**.
* Select **Add**.

![Add Razor components using EF CRUD dialog](~/blazor/tutorials/movie-database-app/part-2/_static/add-razor-components-using-ef-crud.png)

:::zone-end

:::zone pivot="vsc"

In the **Terminal** (**Terminal** menu > **New Terminal**) opened to the project's root directory, execute the following command. SQLite is used as the database for users adopting VS Code tooling for this tutorial series.

```dotnetcli
dotnet aspnet-codegenerator blazor CRUD -dbProvider sqlite -dc BlazorWebAppMovies.Data.BlazorWebAppMoviesContext -m Movie -outDir Components/Pages
```

*CRUD* is an acronym for Create, Read, Update, and Delete. The `blazor` generator with the `CRUD` template produces create, edit, delete, details, and index components for the app.

The following table details the ASP.NET Core code generator options used in the preceding command:

* `-dbProvider`: Database provider to use. Options include `sqlserver` (default), `sqlite`, `cosmos`, `postgres`.
* `-dc`: The <xref:Microsoft.EntityFrameworkCore.DbContext> class to use, including the namespace (`BlazorWebAppMovies.Data`).
* `-m`: The name of the model.
* `-outDir`: The output directory for the generated components. A folder is created from the model name in the output directory to hold the components (for example, `MoviePages` in this case).

:::zone-end

:::zone pivot="cli"

In a command shell opened to the project's root folder, execute the following command. SQLite is used as the database for users adopting VS Code tooling for this tutorial series.

```dotnetcli
dotnet aspnet-codegenerator blazor CRUD -dbProvider sqlite -dc BlazorWebAppMovies.Data.BlazorWebAppMoviesContext -m Movie -outDir Components/Pages
```

*CRUD* is an acronym for Create, Read, Update, and Delete. The `blazor` generator with the `CRUD` template produces create, edit, delete, details, and index components for the app.

The following table details the ASP.NET Core code generator options used in the preceding command:

* `-dbProvider`: Database provider to use. Options include `sqlserver` (default), `sqlite`, `cosmos`, `postgres`.
* `-dc`: The <xref:Microsoft.EntityFrameworkCore.DbContext> class to use, including the namespace (`BlazorWebAppMovies.Data`).
* `-m`: The name of the model.
* `-outDir`: The output directory for the generated components. A folder is created from the model name in the output directory to hold the components (for example, `MoviePages` in this case).

:::zone-end

The `appsettings.json` file is updated with the connection string used to connect to a local database. In the following example, the `{CONNECTION STRING}` is the connection string generated automatically by the scaffolder:

```json
"ConnectionStrings": {
  "BlazorWebAppMoviesContext": "{CONNECTION STRING}"
}
```

## Files created and updated by scaffolding

The scaffolding process creates the following component files and movie database context class file:

* `Components/Pages/MoviePages`
  * `Create.razor`: Creates new movie entities.
  * `Delete.razor`: Deletes a movie entity.
  * `Details.razor`: Shows movie entity details.
  * `Edit.razor`: Updates a movie entity.
  * `Index.razor`: Lists movie entities (records) in the database.
* `Data/BlazorWebAppMovieContext.cs`: Database context file (<xref:Microsoft.EntityFrameworkCore.DbContext>).

The component files in the `MoviePages` folder are described in greater detail in the next part of this tutorial. The database context is described later in this article.

ASP.NET Core is built with dependency injection, which is a software design pattern for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies. Services, such as the EF Core database context, are registered with dependency injection during application startup. These services are injected into Razor components for use by the components.

The <xref:Microsoft.AspNetCore.Components.QuickGrid> component is a Razor component for efficiently displaying data in tabular form. The scaffolder places a <xref:Microsoft.AspNetCore.Components.QuickGrid> component in the `Index` component (`Components/Pages/Index.razor`) to display movie entities. Calling <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkAdapterServiceCollectionExtensions.AddQuickGridEntityFrameworkAdapter%2A> on the service collection adds an EF Core adapter for <xref:Microsoft.AspNetCore.Components.QuickGrid> to recognize EF Core-supplied <xref:System.Linq.IQueryable%601> instances and to resolve database queries asynchronously for efficiency.

The following code is added to the `Program` file by the scaffolder:

```csharp
builder.Services.AddDbContext<BlazorWebAppMoviesContext>(options =>
    options.UseSqlServer(
        builder.Configuration.GetConnectionString("BlazorWebAppMoviesContext") ?? 
        throw new InvalidOperationException(
            "Connection string 'BlazorWebAppMoviesContext' not found.")));

builder.Services.AddQuickGridEntityFrameworkAdapter();
```

Static server-side rendering (static SSR) is enabled by calling:

* The service collection extension method <xref:Microsoft.Extensions.DependencyInjection.RazorComponentsServiceCollectionExtensions.AddRazorComponents%2A> to register services for server-side rendering (SSR) of Razor components.
* The request pipeline extension method <xref:Microsoft.AspNetCore.Builder.RazorComponentsEndpointRouteBuilderExtensions.MapRazorComponents%2A>, which maps the page components defined in the `App` component to the given assembly and renders the `App` component when a request matches a route to a component:

```csharp
builder.Services.AddRazorComponents()
    .AddInteractiveServerComponents();

...

app.MapRazorComponents<App>()
    .AddInteractiveServerRenderMode();
```

The extension methods <xref:Microsoft.Extensions.DependencyInjection.ServerRazorComponentsBuilderExtensions.AddInteractiveServerComponents%2A> and <xref:Microsoft.AspNetCore.Builder.ServerRazorComponentsEndpointConventionBuilderExtensions.AddInteractiveServerRenderMode%2A> make the app capable of adopting interactive SSR, which isn't relevant until the last part of the tutorial series on interactivity. Over the next several articles, the app's components only adopt static SSR.

## Create the initial database schema using EF Core's migration feature

The migrations feature in EF Core:

* Creates the initial database schema.
* Incrementally updates the database schema to keep it synchronized with the app's data model. Existing data in the database is preserved.

EF Core adopts the *code-first* approach for database design and maintenance:

* Entity classes are created and updated first in the app.
* The database is created and updated from the app's entity classes.

This is the reverse procedure of *database-first* approaches, where the database is designed, built, and updated first. Adopting EF Core's code-first approach speeds up the process of app development because most of the difficult and time-consuming database creation and management is handled transparently by the EF Core tooling, so you can focus on app development.

:::zone pivot="vs"

In this section, [Visual Studio Connected Services](/visualstudio/azure/overview-connected-services) are used to issue EF Core commands that:

* Add an initial migration.
* Update the database with the initial migration.

In Visual Studio **Solution Explorer**, double-click **Connected Services**. In the **Service Dependencies** area, select the ellipsis (`...`) followed by **Add migration** in the **SQL Server Express LocalDB** area:

![UI showing the 'Add migration' option in the contextual menu opened from selecting the ellipsis next to 'SQL Server Express LocalDB'](~/blazor/tutorials/movie-database-app/part-2/_static/add-migration-menu-item.png)

Give the migration a **Migration name** of `InitialCreate`, which is a name that describes the migration. Wait for the database context to load in the **DbContext class names** field. Select **Finish** to create the migration:

![Add a new EF migration dialog showing the migration name and database context](~/blazor/tutorials/movie-database-app/part-2/_static/new-ef-migration-dialog.png)

Adding a migration generates code to create the initial database schema. The schema is based on the model specified in <xref:Microsoft.EntityFrameworkCore.DbContext>.

After the preceding command completes, update the database. Select the ellipsis (`...`) again followed by the **Update database** command:

![UI showing the 'Update database' option in the contextual menu opened from selecting the ellipsis next to 'SQL Server Express LocalDB'](~/blazor/tutorials/movie-database-app/part-2/_static/update-database-menu-item.png)

The **Update database with the latest migration** dialog opens. Wait for the **DbContext class names** field to update and for prior migrations to load. Select the **Finish** button:

![Update database with the latest migration dialog showing the database context](~/blazor/tutorials/movie-database-app/part-2/_static/update-database-dialog.png)

The update database command executes the `Up` method migrations that haven't been applied in a migration code file created by the scaffolder. In this case, the command executes the `Up` method in the `Migrations/{TIME STAMP}_InitialCreate.cs` file, which creates the database. The `{TIME STAMP}` placeholder in the preceding example is a time stamp.

:::zone-end

:::zone pivot="vsc"

Right-click the `BlazorWebAppMovies` project file (`BlazorWebAppMovies.csproj`) and select **Open in Integrated Terminal**.

The **Terminal** window opens with a command prompt at the project directory, which contains the `Program` file and the app's project file (`.csproj`).

Execute the following .NET CLI command to add an initial migration. The `migrations` command generates code to create the initial database schema. The schema is based on the model specified in <xref:Microsoft.EntityFrameworkCore.DbContext>. The `InitialCreate` argument is used to name the migration. Any name can be used, but the convention is to use a name that describes the migration.

```dotnetcli
dotnet ef migrations add InitialCreate
```

After the preceding command completes, update the database with the `update` command. The `update` command executes the `Up` method on migrations that haven't been applied in a migration code file created by the scaffolder. In this case, the command executes the `Up` method in the `Migrations/{TIME STAMP}_InitialCreate.cs` file, which creates the database. The `{TIME STAMP}` placeholder in the preceding example is a time stamp.

```dotnetcli
dotnet ef database update
```

:::zone-end

:::zone pivot="cli"

From the project's root folder, execute the following .NET CLI command to add an initial migration. The `migrations` command generates code to create the initial database schema. The schema is based on the model specified in <xref:Microsoft.EntityFrameworkCore.DbContext>. The `InitialCreate` argument is used to name the migration. Any name can be used, but the convention is to use a name that describes the migration.

```dotnetcli
dotnet ef migrations add InitialCreate
```

After the preceding command completes, update the database with the `update` command. The `update` command executes the `Up` method on migrations that haven't been applied in a migration code file created by the scaffolder. In this case, the command executes the `Up` method in the `Migrations/{TIME STAMP}_InitialCreate.cs` file, which creates the database. The `{TIME STAMP}` placeholder in the preceding example is a time stamp.

```dotnetcli
dotnet ef database update
```

:::zone-end

The database context `BlazorWebAppMovieContext`:

* Derives from <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.
* Specifies which entities are included in the data model.
* Coordinates EF Core functionality, such as CRUD operations, for the `Movie` model.
* Contains a <xref:Microsoft.EntityFrameworkCore.DbSet%601> property for the `Movie` entity set. In EF terminology, an entity set typically corresponds to a database table. An entity corresponds to a row in the table.

```csharp
public class BlazorWebAppMoviesContext : DbContext
{
    public BlazorWebAppMoviesContext(DbContextOptions<BlazorWebAppMoviesContext> options)
        : base(options)
    {
    }

    public DbSet<BlazorWebAppMovies.Models.Movie> Movie { get; set; } = default!;
}
```

The name of the connection string is passed in to the context by calling a method on a <xref:Microsoft.EntityFrameworkCore.DbContextOptions> object. For local development, the connection string is read from the `appsettings.json` file.

## Test the app

Run the app.

Add `/movies` to the URL in the browser's address bar to navigate to the movies `Index` page.

After the `Index` page loads, select the **`Create New`** link.

Add a movie to the database. In the following example, the classic sci-fi movie [*The Matrix*](https://www.warnerbros.com/movies/matrix) (&copy;1999 [Warner Bros. Entertainment Inc.](https://www.warnerbros.com/)) is added as the first movie entry. Selecting the **`Create`** button adds the movie to the database:

![Adding The Matrix movie to the database with the 'Create' component](~/blazor/tutorials/movie-database-app/part-2/_static/create-new.png)

When you select the **Create** button, the movie data is posted to the server and saved in the database. When the app returns to the `Index` page, the added entity appears:

![The Matrix movie shown in the movies 'Index' page](~/blazor/tutorials/movie-database-app/part-2/_static/movie-added.png)

Test the `Edit`, `Details` pages.

Examine the `Delete` page, but don't delete *The Matrix* movie from the database. The presence of this movie record is valuable in the next step of the tutorial where rendered HTML is studied and some enhancements are made to the data displayed. If you already deleted the movie, re-add it using the `Create` page before proceeding to the next part of this series.

## Stop the app

:::zone pivot="vs"

Stop the app using either of the following approaches:

* Close the browser window.
* In Visual Studio, either:
  * Use the Stop button in Visual Studio's menu bar:

    ![Stop button in Visual Studio's menu bar](~/blazor/tutorials/movie-database-app/part-1/_static/stop-button.png)

  * Press <kbd>Shift</kbd>+<kbd>F5</kbd> on the keyboard.

:::zone-end

:::zone pivot="vsc"

Stop the app using the following approach:

1. Close the browser window.
1. In VS Code, either:
   * From the **Run** menu, select **Stop Debugging**.
   * Press <kbd>Shift</kbd>+<kbd>F5</kbd> on the keyboard.

:::zone-end

:::zone pivot="cli"

Stop the app using the following approach:

1. Close the browser window.
2. From the command shell, press <kbd>Ctrl</kbd>+<kbd>C</kbd> (Windows) or <kbd>⌘</kbd>+<kbd>C</kbd> (macOS).

:::zone-end

## Troubleshoot with the completed sample

[!INCLUDE[](~/blazor/tutorials/movie-database-app/includes/troubleshoot.md)]

## Additional resources

EF Core documentation:

* [Entity Framework Core](/ef/core/)
* [Entity Framework Core tools reference - .NET Core CLI](/ef/core/cli/dotnet)
* [Data Types](/ef/core/modeling/relational/data-types)
* <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>: The API document includes basic information on how entities are saved and change detection.
* [Environment-based `Startup` class and methods](xref:fundamentals/environments#environment-based-startup-class-and-methods)
* [`dotnet aspnet-codegenerator`](xref:fundamentals/tools/dotnet-aspnet-codegenerator)
* <xref:fundamentals/dependency-injection>
* <xref:blazor/components/quickgrid>

## Legal

[*The Matrix*](https://www.warnerbros.com/movies/matrix) is a trademark and copyright of [Warner Bros. Entertainment Inc.](https://www.warnerbros.com/).

## Next steps

> [!div class="step-by-step"]
> [Previous: Create a Blazor Web App](xref:blazor/tutorials/movie-database-app/part-1)
> [Next: Learn about Razor components](xref:blazor/tutorials/movie-database-app/part-3)