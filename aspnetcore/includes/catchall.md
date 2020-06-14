> [!WARNING]
> Um parâmetro **catch-all** pode corresponder a rotas incorretamente devido a um [bug](https://github.com/dotnet/aspnetcore/issues/18677) no roteamento. Os aplicativos afetados por esse bug têm as seguintes características:
>
> * Uma rota catch-all, por exemplo,`{**slug}"`
> * A rota capturar tudo falha ao corresponder às solicitações que devem corresponder.
> * A remoção de outras rotas faz com que a rota capturar tudo comece a funcionar.
>
> Veja bugs do GitHub [18677](https://github.com/dotnet/aspnetcore/issues/18677) e [16579](https://github.com/dotnet/aspnetcore/issues/16579) , por exemplo, casos que atingiram esse bug.
>
> Uma correção de aceitação para esse bug está contida no [SDK do .NET Core 3.1.301 e posterior](https://dotnet.microsoft.com/download/dotnet-core/3.1). O código a seguir define uma opção interna que corrige esse bug:
>
>```
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", 
>                          true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```