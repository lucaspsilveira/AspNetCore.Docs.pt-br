### <a name="use-sqlite-for-development-sql-server-for-production"></a>Use SQLite para desenvolvimento, SQL Server para produção

Quando o SQLite é selecionado, o código gerado pelo modelo está pronto para desenvolvimento. O código a seguir <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> mostra como injetar em Startup. `IWebHostEnvironment`é injetado `ConfigureServices` para que possa usar SQLite em desenvolvimento e SQL Server em produção.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]
