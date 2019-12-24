# Building an ASP.NET Core 3.1 React SPA with Entity Framework Core

#### A step-by step tutorial for building an ASP.NET Core 3.1 React SPA, using 

*   The built-in React.js template (=Create React App or CRA),
*   Pure web api back end
*   Entity Framework Core 3.1
*   From-scratch Microsoft SQL Server local database, and  
*   SQL Server Management Studio 18.2. 

In other words, we are taking the V out of the usual MVC setup of ASP.NET on the backend, and putting it entirely on the front end with a React SPA. The backend now only has M and C (=pure web api back end). No knowledge of SQL queries is required for this tutorial.

#### Installation requirements
*   Microsoft SQL Server
*   Microsoft SQL Server Management Studio 18.2
*   Visual Studio 2019 with web development options

#### Method
This tutorial is especially intended for those who already know front end JavaScript frameworks like React, but have little to no experience working with C# and an ASP.NET back end. I am taking the database-first approach because I work at a database-focused company. Hence there are three basic parts to this process:
1. Set up a server and database.
2. Connect back end to the database.
3. Connect front end to the back end.

** **
# Part 1 - Server and Database Setup

1. Create a local server instance in Microsoft SQL Server.
    *   Open command line and run this command to see what local servers you have already, if any: `sqllocaldb info`
    *   To make a new local server specifically for this project, run `sqllocaldb c [[ServerNameHere]]`, where the “c” stands for “create.” Replace [[ServerNameHere]] with the name of your choice. I will be calling mine TestServer. 
    *   Optional: run `sqllocaldb info` to see if the database you just created shows up.


2. Open Microsoft SQL Server Management Studio.
    *   Connect SSMS to the local server created in the previous step. 
        *   Select the following options in the dialogue box that appears:
             * Server Type: Database Engine
             * Server name: (localdb)\TestServer, or whatever you named the server.
             * Authentication: Windows Authentication
        *   Click connect. Now you have the SSMS GUI connected to the server.
    *   Create a new database within the server instance.
         * In the Object Explorer, look for “Databases” under the server connection just established (which should look like this: “(localdb)\TestServer (SQL Server…”).
        *   Right-click “Databases” and select New Database.
        *   Name it and click OK to create it. For the purpose of this tutorial I’m calling it TestDatabase.
    *   Create at least one new table in the database.
        *   Expand the newly created database, and right-click “Tables.” 
        *   Select New → Table.
        *   Name its columns and specify data types for each.
        *   DO NOT FORGET TO SET ONE COLUMN AS THE PRIMARY KEY. This is required to integrate the data with Entity Framework later, and is good practice. You can do this by right-clicking any one of the columns and selecting “Set Primary Key,” the very topmost option.
        *   Save the table with CTRL + S or File → Save Table.
        *   Give the table a name and click OK.
        *   To see the table you just created, refresh the connection, either by clicking the looping arrow symbol at the top of the Object Explorer, or by right-clicking the Tables folder and selecting Refresh. You should see a dbo.<TableName> file appear.
    *   Populate the table with data. Here you have an option. 
        *   If you know SQL, you can add data to the tables either with SQL queries.
        *   If you don’t know SQL, or don’t want to write any, you can use the GUI. To use the GUI, right-click the table you want to add data to, select Edit Top 100 Rows, and enter your data manually.

** **
# Part 2 - Back End Setup

Now that we have a server, a database, and data to work with, we need to model it in the back end of our (to-be-created) ASP.NET Core web application. This is necessary because the data is stored in SQL format (tables and columns), but needs to be converted to or represented in C# (classes and objects). We need C# models or representations of SQL tables in order to manipulate the database data in C#. In short, we need an ORM: and that is what the Entity Framework is for. It is the pipeline that transforms C# into SQL and SQL into C#, allowing data flow between the database and the back end. 

EF is to C# what Sequelize is to JavaScript (a Node package that talks to a SQL database so we don’t have to write SQL queries, but just JS). Also compare Mongoose for non-relational databases. EF, Sequlize, Mongoose are all ORMs. They automate the connection between the back end and the database.

A refresher on ORMs: Object-Relational Mappers. We have a language that deals with objects (C#), and a language that can only deal with relational databases that have no objects (SQL). But we want those languages to talk to each other, because we need to represent the relational data as object data. The ORM automates and standardizes this tedious process, abstracting away the SQL data into C# classes and objects.

1. Install and open Visual Studio 2019, because .NET Core 3 and higher requires VS 2019.


2. Set up a new React ASP.NET Core project template inside VS 2019.
    *   Create a new project → 
    *   ASP.NET Core Web Application (ensure Core 3.1 is selected) → 
    *   Name project → 
    *   Select React.js template, finish.
    *   Now we have the Create React App (CRA) template in its own ClientApp folder, configured to work via Microsoft middleware (SpaServices) with an ASP.NET C# web api backend.
        *   For more on this middleware, read the first section of [this article](https://www.infoq.com/articles/spa-asp-dotnet-core-3/)


3. Install Entity Framework and Entity Tools.
    *   Open the NuGet Package Manager (Tools → NuGet Package Manager). You then have two options: 
        *   Manage NuGet Packages for Solution (opens GUI, no console commands required), and 
        *   Package Manager Console (opens command line, uses console commands).
    *   The only package installed at this point should be Microsoft.AspNetCore.SpaServices.Extensions which lets React specifically talk to the C# back end, and comes pre-installed as part of the ASP.NET Core React template. Note that this is not the same as the Microsoft.AspNetCore.SpaServices package, which is not required for this project.
    *   Remember, the goal in this step is to get our C# back end to talk to the SQL Server database created above using Entity Framework. In order to do that, we first need the Entity Framework package installed. But there are many flavors of the Entity package. We need the one that specifically talks to SQL Server, since that’s what we’re using. This package is called Microsoft.EntityFrameworkCore.SqlServer.
        *   Install it by browsing through packages in the GUI
        *   Install it in the NuGet CLI with the command `install-Package Microsoft.EntityFrameworkCore.SqlServer`
    *   But installing that package won’t be enough to initiate the contact between EF and the existing SQL Server database. We need another package for that: Microsoft.EntityFrameworkCore.Tools.
        *   Add it by browsing through packages in the GUI
        *   Add it in the NuGet CLI with the command `Install-Package Microsoft.EntityFrameworkCore.Tools`
        *   Now we have all the means necessary to initiate contact between EF and our database. So the next step is to initiate the contact.
        
        
4. Bridge the gap between EF on the back end and the SQL Server database. See [this tutorial](https://medium.com/@kansiris87/getting-started-with-entityframework-core-on-asp-net-core-with-an-existing-database-74dd32b3d4a9) for another example of this step.
    *   First we have to create an EF Context to facilitate this contact. What is an EF Context? 
        *   It is the bridge or point of contact between the database and the back end. 
        *   Without the Context, we can’t talk to the database using C# and EF. The context represents a session with the database and facilitates CRUD operations, among other things. The beauty of the EF Context is that it does all kinds of things for us, so we don’t have to write our own Context, or any database languages. 
        *   Think of EF, and EF Contexts, as middleware between the database and the C# back end. They allow C# developers to work with data (in this case, columns and tables) at a higher level of abstraction (i.e., abstracted into C# classes and objects). But while EF is like a bare road or highway between two countries (i.e., languages), the EF Context is like setting up Border Control on that road. It does the checking in and checking out across the frontier.
    *   How do I generate an EF Context and Models?
         *   You will need a Context and a place to put the Context (and Models) generated. 
        *   First, make a place to put the files. Create a folder in the root of the project called “Models” so that it sits next to Controllers and ClientApp.
        *   Next, you’ll have to tell EF to spin up a Context class based on the database you want (also called scaffolding the DbContext). This will also create models based on individual Tables in that database (also called[ reverse engineering your models](https://ef.readthedocs.io/en/staging/platforms/aspnetcore/existing-db.html#reverse-engineer-your-model)). So, open the NuGet package manager console (I don’t think it’s possible to do this without the CLI) and run the following command:
            *   `Scaffold-DbContext [[enter connection string for server and database here]] Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models`
            *   This command has four parts.
                1.   `Scaffold-DbContext`: this orders EF to create a Context based on the following parameter, the connection string.
                2.   `[[enter connection string for server and database here]]`
                       *   Follows the template `Server=(localdb)\[[ServerName]];Database=[[DatabaseName]];Trusted_Connection=True;`. There are two important variables here, `[[ServerName]]` and `[[DatabaseName]]`.
                       *   For example, given the server and databases we’ve created in part 1, a sample connection string in its entirety would be: `“Server=(localdb)\TestServer;Database=TestDatabase;Trusted_Connection=True;”`
                3.   `Microsoft.EntityFrameworkCore.SqlServer -OutputDir`: Tells the package that you’re about to specify in the next parameter where to put the Context file after it’s been created or “scaffolded” based on the existing database.
                4.   `Models`: Specifies the Models folder of the project.
            *   Sample command in its entirety: `Scaffold-DbContext “Server=(localdb)\TestServer;Database=TestDatabase;Trusted_Connection=True;” Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models`
            *   For another sample command see [this tutorial](https://dzone.com/articles/aspnet-core-crud-with-reactjs-and-entity-framework)
        *   After a while, when all the packages have been installed (this will take a few minutes), a file named TestDatabaseContext.cs should be added to the Models folder, in addition to other .cs files for each of the Tables in the database (e.g., Users.cs, Posts.cs).
        *   Note that at this point your connection string to the server and database is hard-coded in a C# model class. Eventually this string should be moved to the appsettings.json file (see part of [this tutorial](https://www.syncfusion.com/blogs/post/build-crud-application-with-asp-net-core-entity-framework-visual-studio-2019.aspx) on how to do this).
    *   Create a Controller to manage the front end View interactions with the Models just created. 
        *   If EF is the middleware (or API) between the SQL database and the C# backend, Controllers are the middleware (API) between the C# backend and front end HTTP requests. That’s why some developers call this approach to the ASP.NET back end a “web api only” approach as opposed to full MVC pattern.
        *   Right click the Controllers folder → Add → New Controller → API Controller with actions, using Entity Framework → Add.
        *   You will now have to specify three options:
            *   Model class: match this with the name of the Table Model.cs file you want, e.g. Users.cs.
            *   Data context class: TestDatabaseContext (there should only be one option available here, since we’ve only created one Context).
            *   Name the controller, e.g. UsersController.
        *   Note that adding the Controller will also install three other NuGet packages.
        *   A fully CRUD enabled Controller class will be created. Notice in the code how it begins by setting the Context and then creating specific routes for HTTP requests. In other words, it binds together the database-as-represented-by-EF with the front end requests. It’s an API.
    *   Now we need to alert ASP.NET that we’re using an additional service (EF). Right now, EF is ready to talk to the front end, to receive and route any HTTP requests. But the ASP.NET router doesn’t know of its existence yet, so the front end can’t talk to the router. We need to tell it that it should depend on the Context and its Controllers using the Entity Framework for information about routing. The fancy way of saying this is that we need to “register the Context with dependency injection.” (For more on this, see the somewhat old tutorial [here](https://ef.readthedocs.io/en/staging/platforms/aspnetcore/existing-db.html#register-your-context-with-dependency-injection).)
        *   First, open Startup.cs in the root of the project.
        *   After all the “using” statements at the top, add these two lines: 
            *   `using [[NameOfProject]].Models;`
                *   Replace `[[NameOfProject]]` with whatever the name of your .csproj file is, the name of the root of the project. We’re trying to reference the Models folder, so we need to reference the root project name before `.Models`, since that’s the namespace where the Context file lives.
            *   `using Microsoft.EntityFrameworkCore;`
        *   Navigate to the ConfigureServices method.
        *   Replace `services.AddControllersWIthViews();` with `services.AddControllers();` 
            *   This tells the ASP.NET router that we’re adding pure web api controllers, not ones that return Views with Razor pages, since we’re letting React handle all of the View management
            *   Note that this is a [new feature for ASP.NET Core 3](https://www.carlrippon.com/whats-new-in-asp-net-core-3-0-for-react-spas/).
            *   Right after that, add this line:             “services.AddDbContext<NameOfProjectContext>();”
                *   Be sure to replace NameOfProject with the name of the .csproj file, TestServer with your server name, and TestDatabase with your database name. 
                *   Be sure to retain the <> around the name of the context.
                *   This line tells ASP.NET to use the EF Context.
                *   Now the back end is entirely set up. All we have to do is make an API call from the front end to retrieve the data.

** **
# Part 3 - Front End Setup #
1. Now let’s make a JavaScript API call to the back end and retrieve the SQL data from our database as filtered through the C# back end.  
2. You can make this call from any component, but for simplicity’s sake, I’ll just be dumping it in the console of the Home page component. The important thing is to make it JavaScript accessible.
3. Locate the Home.js file (in ClientApp → src → components).   
4. Add the following code blocks after `static displayName = Home.name` but before the `render` method:
```javascript
    componentDidMount() {
            this.makeAPICall();
        }
        async makeAPICall() {
            const response = await fetch('api/users');
            const data = await response.json();
            console.log(data);
        }
``` 
5. Debugging tip: if the API calls are unsuccessful, a helpful technique to see what the problem is is to enter the api call in the browser itself to see the error (or to use an app like Postman). For instance, if you are accessing the “users” route, add `/api/users` to the end of the URL in the browser to see what error is being thrown.
