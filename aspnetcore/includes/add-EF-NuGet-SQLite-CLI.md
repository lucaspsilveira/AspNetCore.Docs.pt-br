Execute os seguintes comandos da CLI do .NET Core:

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

Os comandos anteriores adicionam:

* A [ferramenta de andaimes aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).
* As Ferramentas de Núcleo framework da entidade para o .NET Core CLI.
* O provedor do EF Core SQLite, que instala o pacote EF Core como uma dependência.
* Pacotes necessários para scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` e `Microsoft.EntityFrameworkCore.SqlServer`.

Para obter orientações sobre a configuração de vários ambientes <xref:fundamentals/environments#environment-based-startup-class-and-methods>que permite que um aplicativo configure seus contextos de banco de dados por ambiente, consulte .

[!INCLUDE[](~/includes/scaffoldTFM.md)]