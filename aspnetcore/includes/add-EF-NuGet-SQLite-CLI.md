<span data-ttu-id="53441-101">Execute os seguintes comandos da CLI do .NET Core:</span><span class="sxs-lookup"><span data-stu-id="53441-101">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="53441-102">Os comandos anteriores adicionam:</span><span class="sxs-lookup"><span data-stu-id="53441-102">The preceding commands add:</span></span>

* <span data-ttu-id="53441-103">A [ferramenta de andaimes aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="53441-103">The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
* <span data-ttu-id="53441-104">As Ferramentas de Núcleo framework da entidade para o .NET Core CLI.</span><span class="sxs-lookup"><span data-stu-id="53441-104">The Entity Framework Core Tools for the .NET Core CLI.</span></span>
* <span data-ttu-id="53441-105">O provedor do EF Core SQLite, que instala o pacote EF Core como uma dependência.</span><span class="sxs-lookup"><span data-stu-id="53441-105">The EF Core SQLite provider, which installs the EF Core package as a dependency.</span></span>
* <span data-ttu-id="53441-106">Pacotes necessários para scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` e `Microsoft.EntityFrameworkCore.SqlServer`.</span><span class="sxs-lookup"><span data-stu-id="53441-106">Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.</span></span>

<span data-ttu-id="53441-107">Para obter orientações sobre a configuração de vários ambientes <xref:fundamentals/environments#environment-based-startup-class-and-methods>que permite que um aplicativo configure seus contextos de banco de dados por ambiente, consulte .</span><span class="sxs-lookup"><span data-stu-id="53441-107">For guidance on multiple environment configuration that permits an app to configure its database contexts by environment, see <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span></span>

[!INCLUDE[](~/includes/scaffoldTFM.md)]