"#crudapi" 

Documentation 

=API=

=API.1=CREATE APP, CREATE DATABASE AND CONNECT THEM TOGETHER
Steps to recreate:
1.Make a ASP.NET Core Web Api (3.1) project in VS named "Inventory Service", delete Weather API
2.Open Package Manager Console and run the following:
vvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvv
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design -Version 3.1.4
Install-Package Microsoft.EntityFrameworkCore.Tools -Version 3.1.8
Install-Package Microsoft.EntityFrameworkCore.SqlServer -Version 3.1.8
Install-Package System.IdentityModel.Tokens.Jwt -Version 5.6.0
Install-Package Microsoft.AspNetCore.Authentication.JwtBearer -Version 3.1.8
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
3.Create a new database in SSMS named "Inventory" and run a query to create two tables:
vvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvv
Create Table Products
ProductId Int Identity(1,1) Primary Key,
Name Varchar(100) Not Null,
Category Varchar(100),
Color Varchar(20),
UnitPrice Decimal Not Null,
AvailableQuantity Int Not Null)
GO
Create Table UserInfo(
UserId Int Identity(1,1) Not null Primary Key,
FirstName Varchar(30) Not null,
LastName Varchar(30) Not null,
UserName Varchar(30) Not null,
Email Varchar(50) Not null,
Password Varchar(20) Not null,
CreatedDate DateTime Default(GetDate()) Not Null)
GO
Insert Into UserInfo(FirstName, LastName, UserName, Email, Password) 
Values ('Inventory', 'Admin', 'InventoryAdmin', 'InventoryAdmin@abc.com', '$admin@2017')
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
4. Run a scaffold command in NPM:
vvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvv
Scaffold-DbContext “Server=******;Database=Inventory;Integrated Security=True” Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
This creates InventoryContext, go into it and delete the constructor "public InventoryContext{}" and method "protected override void OnConfiguring(){}"
Add a connection string to appsettings.json under "AllowedHosts"
vvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvv
"AllowedHosts":"*", <-- already there
"AllowedHosts":"*","ConnectionStrings": {"InventoryDatabase":"Server=*****;Database=Inventory;Trusted_Connection=True;"}
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
5.Register database context service "InventoryContext" during startup aka in "Startup.cs" inside "public void ConfigureServices(IServiceCollection services)" above "AddControllers"
vvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvv
var connection = Configuration.GetConnectionString("InventoryDatabase");
services.AddDbContextPool<InventoryContext>(options => options.UseSqlServer(connection));
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^           
In "Startup.cs" add these using statements:
vvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvv
using InventoryService.Models;
using Microsoft.EntityFrameworkCore;
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

=API.2=BUILD REST APIs
vvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvv
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
1.In your InventoryService app from =API.1=, right click on the controllers folder, add a controller with api actions using entity framework 
Model class: Products
Data context class: InventoryContext
Controller name: Products Controller
*Click add* and it will scaffold using ASP.NET CORE scaffolding (If errors, go to InventoryCOntext and delete previously said methods)
This creates APIs: GET all products, GET product details, PUT/update product details, POST/create product, DELETE product
Each operation gets its own endpoint
1.b. Add the using statement:
vvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvv
using Microsoft.AspNetCore.Authorization;
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

