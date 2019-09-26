# <a name="aspnet-core-middleware-extensibility-sample"></a><span data-ttu-id="04e54-101">Amostra de extensibilidade do Middleware do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="04e54-101">ASP.NET Core Middleware Extensibility Sample</span></span>

<span data-ttu-id="04e54-102">Esta amostra apresenta os cenários descritos em [Ativação de middleware baseada em alocador no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/middleware-extensibility).</span><span class="sxs-lookup"><span data-stu-id="04e54-102">This sample demonstrates the scenarios described in [Factory-based middleware activation in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/middleware-extensibility).</span></span>

<span data-ttu-id="04e54-103">O aplicativo de exemplo demonstra o middleware ativado por:</span><span class="sxs-lookup"><span data-stu-id="04e54-103">The sample app demonstrates middleware activated by:</span></span>

* <span data-ttu-id="04e54-104">Convenção.</span><span class="sxs-lookup"><span data-stu-id="04e54-104">Convention.</span></span> <span data-ttu-id="04e54-105">Para obter mais informações sobre a ativação de middleware convencional, consulte o tópico [Middleware](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/).</span><span class="sxs-lookup"><span data-stu-id="04e54-105">For more information on conventional middleware activation, see the [Middleware](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/) topic.</span></span>
* <span data-ttu-id="04e54-106">Uma implementação de [IMiddleware](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddleware).</span><span class="sxs-lookup"><span data-stu-id="04e54-106">An [IMiddleware](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddleware) implementation.</span></span> <span data-ttu-id="04e54-107">A classe [IMiddlewareFactory](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) padrão ativa o middleware.</span><span class="sxs-lookup"><span data-stu-id="04e54-107">The default [IMiddlewareFactory](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) class activates the middleware.</span></span>

<span data-ttu-id="04e54-108">As implementações de middleware funcionam de forma idêntica e registram o valor fornecido por um parâmetro de cadeia de caracteres de consulta (`key`).</span><span class="sxs-lookup"><span data-stu-id="04e54-108">The middleware implementations function identically and record the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="04e54-109">Os middlewares usam um contexto de banco de dados injetado (um serviço com escopo) para registrar o valor de cadeia de caracteres de consulta em um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="04e54-109">The middlewares use an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>