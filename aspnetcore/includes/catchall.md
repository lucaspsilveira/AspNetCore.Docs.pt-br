> [!WARNING]
> <span data-ttu-id="0d591-101">Um parâmetro **catch-all** pode corresponder a rotas incorretamente devido a um [bug](https://github.com/dotnet/aspnetcore/issues/18677) no roteamento.</span><span class="sxs-lookup"><span data-stu-id="0d591-101">A **catch-all** parameter may match routes incorrectly due to a [bug](https://github.com/dotnet/aspnetcore/issues/18677) in routing.</span></span> <span data-ttu-id="0d591-102">Os aplicativos afetados por esse bug têm as seguintes características:</span><span class="sxs-lookup"><span data-stu-id="0d591-102">Apps impacted by this bug have the following characteristics:</span></span>
>
> * <span data-ttu-id="0d591-103">Uma rota catch-all, por exemplo,`{**slug}"`</span><span class="sxs-lookup"><span data-stu-id="0d591-103">A catch-all route, for example, `{**slug}"`</span></span>
> * <span data-ttu-id="0d591-104">A rota capturar tudo falha ao corresponder às solicitações que devem corresponder.</span><span class="sxs-lookup"><span data-stu-id="0d591-104">The catch-all route fails to match requests it should match.</span></span>
> * <span data-ttu-id="0d591-105">A remoção de outras rotas faz com que a rota capturar tudo comece a funcionar.</span><span class="sxs-lookup"><span data-stu-id="0d591-105">Removing other routes makes catch-all route start working.</span></span>
>
> <span data-ttu-id="0d591-106">Veja bugs do GitHub [18677](https://github.com/dotnet/aspnetcore/issues/18677) e [16579](https://github.com/dotnet/aspnetcore/issues/16579) , por exemplo, casos que atingiram esse bug.</span><span class="sxs-lookup"><span data-stu-id="0d591-106">See GitHub bugs [18677](https://github.com/dotnet/aspnetcore/issues/18677) and [16579](https://github.com/dotnet/aspnetcore/issues/16579) for example cases that hit this bug.</span></span>
>
> <span data-ttu-id="0d591-107">Uma correção de aceitação para esse bug está planejada.</span><span class="sxs-lookup"><span data-stu-id="0d591-107">An opt-in fix for this bug is planned.</span></span> <span data-ttu-id="0d591-108">Este documento será atualizado quando o patch for lançado.</span><span class="sxs-lookup"><span data-stu-id="0d591-108">This doc will be updated when the patch is released.</span></span> <span data-ttu-id="0d591-109">Quando o patch for liberado, o código a seguir definirá um comutador interno que corrige esse bug:</span><span class="sxs-lookup"><span data-stu-id="0d591-109">When the patch is released, the following code will set an internal switch that fixes this bug:</span></span>
>
>```
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```