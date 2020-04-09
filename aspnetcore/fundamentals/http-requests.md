---
title: Fazer solicitações HTTP usando IHttpClientFactory no ASP.NET Core
author: stevejgordon
description: Saiba mais sobre como usar a interface IHttpClientFactory para gerenciar instâncias de HttpClient lógicas no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
uid: fundamentals/http-requests
ms.openlocfilehash: 912be34ae0ee25837a94aab65443f15b17ab4556
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78661682"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="e8770-103">Fazer solicitações HTTP usando IHttpClientFactory no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e8770-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e8770-104">Por [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), Rick [Anderson](https://twitter.com/RickAndMSFT), e [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="e8770-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="e8770-105">É possível registrar e usar um <xref:System.Net.Http.IHttpClientFactory> para configurar e criar instâncias de <xref:System.Net.Http.HttpClient> em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e8770-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="e8770-106">`IHttpClientFactory`oferece os seguintes benefícios:</span><span class="sxs-lookup"><span data-stu-id="e8770-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="e8770-107">Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e8770-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="e8770-108">Por exemplo, um cliente chamado *github* pode ser registrado e configurado para acessar [o GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="e8770-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="e8770-109">Um cliente padrão pode ser registrado para acesso geral.</span><span class="sxs-lookup"><span data-stu-id="e8770-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="e8770-110">Codifica o conceito de middleware de saída através de `HttpClient`manipuladores delegantes em .</span><span class="sxs-lookup"><span data-stu-id="e8770-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="e8770-111">Fornece extensões para middleware baseado em Polly para tirar `HttpClient`proveito da delegar manipuladores em .</span><span class="sxs-lookup"><span data-stu-id="e8770-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="e8770-112">Gerencia o pooling e a `HttpClientMessageHandler` vida útil das instâncias subjacentes.</span><span class="sxs-lookup"><span data-stu-id="e8770-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="e8770-113">O gerenciamento automático evita problemas comuns de DNS `HttpClient` (Domain Name System) que ocorrem ao gerenciar manualmente vidas.</span><span class="sxs-lookup"><span data-stu-id="e8770-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="e8770-114">Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.</span><span class="sxs-lookup"><span data-stu-id="e8770-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="e8770-115">[Exibir ou baixar o código de amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) [(como baixar).](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="e8770-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="e8770-116">O código de exemplo <xref:System.Text.Json> nesta versão tópico é usa para desserializar o conteúdo JSON retornado em respostas HTTP.</span><span class="sxs-lookup"><span data-stu-id="e8770-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="e8770-117">Para amostras `Json.NET` que `ReadAsAsync<T>`usam e, use o seletor de versão para selecionar uma versão 2.x deste tópico.</span><span class="sxs-lookup"><span data-stu-id="e8770-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="e8770-118">Padrões de consumo</span><span class="sxs-lookup"><span data-stu-id="e8770-118">Consumption patterns</span></span>

<span data-ttu-id="e8770-119">Há várias maneiras de usar o `IHttpClientFactory` em um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e8770-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="e8770-120">Uso básico</span><span class="sxs-lookup"><span data-stu-id="e8770-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="e8770-121">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="e8770-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="e8770-122">Clientes digitados</span><span class="sxs-lookup"><span data-stu-id="e8770-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="e8770-123">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="e8770-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="e8770-124">A melhor abordagem depende das exigências do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e8770-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="e8770-125">Uso básico</span><span class="sxs-lookup"><span data-stu-id="e8770-125">Basic usage</span></span>

<span data-ttu-id="e8770-126">`IHttpClientFactory`pode ser registrado `AddHttpClient`ligando para:</span><span class="sxs-lookup"><span data-stu-id="e8770-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="e8770-127">Uma `IHttpClientFactory` pode ser solicitada usando [injeção de dependência (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e8770-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e8770-128">O código `IHttpClientFactory` a seguir `HttpClient` é criado para criar uma instância:</span><span class="sxs-lookup"><span data-stu-id="e8770-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="e8770-129">Usar `IHttpClientFactory` como no exemplo anterior é uma boa maneira de refatorar um aplicativo existente.</span><span class="sxs-lookup"><span data-stu-id="e8770-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="e8770-130">Não tem impacto `HttpClient` sobre como é usado.</span><span class="sxs-lookup"><span data-stu-id="e8770-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="e8770-131">Nos locais `HttpClient` onde as instâncias são criadas em um aplicativo <xref:System.Net.Http.IHttpClientFactory.CreateClient*>existente, substitua essas ocorrências por chamadas para .</span><span class="sxs-lookup"><span data-stu-id="e8770-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="e8770-132">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="e8770-132">Named clients</span></span>

<span data-ttu-id="e8770-133">Clientes nomeados são uma boa escolha quando:</span><span class="sxs-lookup"><span data-stu-id="e8770-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="e8770-134">O aplicativo requer muitos `HttpClient`usos distintos de .</span><span class="sxs-lookup"><span data-stu-id="e8770-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="e8770-135">Muitos `HttpClient`s têm configuração diferente.</span><span class="sxs-lookup"><span data-stu-id="e8770-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="e8770-136">A configuração `HttpClient` de um nome `Startup.ConfigureServices`pode ser especificada durante o registro em :</span><span class="sxs-lookup"><span data-stu-id="e8770-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="e8770-137">No código anterior, o cliente é configurado com:</span><span class="sxs-lookup"><span data-stu-id="e8770-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="e8770-138">O endereço `https://api.github.com/`base.</span><span class="sxs-lookup"><span data-stu-id="e8770-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="e8770-139">Dois cabeçalhos necessários para trabalhar com a API do GitHub.</span><span class="sxs-lookup"><span data-stu-id="e8770-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="e8770-140">CriarCliente</span><span class="sxs-lookup"><span data-stu-id="e8770-140">CreateClient</span></span>

<span data-ttu-id="e8770-141">Cada <xref:System.Net.Http.IHttpClientFactory.CreateClient*> vez é chamado:</span><span class="sxs-lookup"><span data-stu-id="e8770-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="e8770-142">Uma nova `HttpClient` instância de é criada.</span><span class="sxs-lookup"><span data-stu-id="e8770-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="e8770-143">A ação de configuração é chamada.</span><span class="sxs-lookup"><span data-stu-id="e8770-143">The configuration action is called.</span></span>

<span data-ttu-id="e8770-144">Para criar um cliente nomeado, `CreateClient`passe seu nome para:</span><span class="sxs-lookup"><span data-stu-id="e8770-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="e8770-145">No código anterior, a solicitação não precisa especificar um nome do host.</span><span class="sxs-lookup"><span data-stu-id="e8770-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="e8770-146">O código pode passar apenas o caminho, uma vez que o endereço base configurado para o cliente é usado.</span><span class="sxs-lookup"><span data-stu-id="e8770-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="e8770-147">Clientes com tipo</span><span class="sxs-lookup"><span data-stu-id="e8770-147">Typed clients</span></span>

<span data-ttu-id="e8770-148">Clientes com tipo:</span><span class="sxs-lookup"><span data-stu-id="e8770-148">Typed clients:</span></span>

* <span data-ttu-id="e8770-149">Fornecem as mesmas funcionalidade que os clientes nomeados sem a necessidade de usar cadeias de caracteres como chaves.</span><span class="sxs-lookup"><span data-stu-id="e8770-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="e8770-150">Fornecem a ajuda do IntelliSense e do compilador durante o consumo de clientes.</span><span class="sxs-lookup"><span data-stu-id="e8770-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="e8770-151">Fornecem um único local para configurar e interagir com um determinado `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e8770-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="e8770-152">Por exemplo, um único cliente digitado pode ser usado:</span><span class="sxs-lookup"><span data-stu-id="e8770-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="e8770-153">Para um único ponto final de backend.</span><span class="sxs-lookup"><span data-stu-id="e8770-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="e8770-154">Para encapsular toda a lógica que lida com o ponto final.</span><span class="sxs-lookup"><span data-stu-id="e8770-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="e8770-155">Trabalhe com DI e pode ser injetado quando necessário no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e8770-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="e8770-156">Um cliente digitado `HttpClient` aceita um parâmetro em seu construtor:</span><span class="sxs-lookup"><span data-stu-id="e8770-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="e8770-157">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="e8770-157">In the preceding code:</span></span>

* <span data-ttu-id="e8770-158">A configuração é movida para o cliente digitado.</span><span class="sxs-lookup"><span data-stu-id="e8770-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="e8770-159">O objeto `HttpClient` é exposto como uma propriedade pública.</span><span class="sxs-lookup"><span data-stu-id="e8770-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="e8770-160">Podem ser criados métodos específicos `HttpClient` de API que expõem a funcionalidade.</span><span class="sxs-lookup"><span data-stu-id="e8770-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="e8770-161">Por exemplo, `GetAspNetDocsIssues` o método encapsula código para recuperar problemas abertos.</span><span class="sxs-lookup"><span data-stu-id="e8770-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="e8770-162">As seguintes <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> `Startup.ConfigureServices` chamadas de código para registrar uma classe de cliente digitada:</span><span class="sxs-lookup"><span data-stu-id="e8770-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="e8770-163">O cliente com tipo é registrado como transitório com a DI.</span><span class="sxs-lookup"><span data-stu-id="e8770-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="e8770-164">No código anterior, `AddHttpClient` `GitHubService` registra-se como um serviço transitório.</span><span class="sxs-lookup"><span data-stu-id="e8770-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="e8770-165">Este registro usa um método de fábrica para:</span><span class="sxs-lookup"><span data-stu-id="e8770-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="e8770-166">Crie uma instância de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e8770-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="e8770-167">Crie uma `GitHubService`instância de , `HttpClient` passando na instância de seu construtor.</span><span class="sxs-lookup"><span data-stu-id="e8770-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="e8770-168">O cliente com tipo pode ser injetado e consumido diretamente:</span><span class="sxs-lookup"><span data-stu-id="e8770-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="e8770-169">A configuração para um cliente digitado `Startup.ConfigureServices`pode ser especificada durante o registro em , em vez de no construtor do cliente digitado:</span><span class="sxs-lookup"><span data-stu-id="e8770-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="e8770-170">O `HttpClient` pode ser encapsulado dentro de um cliente digitado.</span><span class="sxs-lookup"><span data-stu-id="e8770-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="e8770-171">Em vez de expô-la como uma propriedade, defina um método que chama a `HttpClient` instância internamente:</span><span class="sxs-lookup"><span data-stu-id="e8770-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="e8770-172">No código anterior, `HttpClient` o é armazenado em um campo privado.</span><span class="sxs-lookup"><span data-stu-id="e8770-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="e8770-173">O acesso `HttpClient` ao é `GetRepos` pelo método público.</span><span class="sxs-lookup"><span data-stu-id="e8770-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="e8770-174">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="e8770-174">Generated clients</span></span>

<span data-ttu-id="e8770-175">`IHttpClientFactory`pode ser usado em combinação com bibliotecas de terceiros, como [a Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="e8770-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="e8770-176">Refit é uma biblioteca REST para .NET.</span><span class="sxs-lookup"><span data-stu-id="e8770-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="e8770-177">Ela converte APIs REST em interfaces dinâmicas.</span><span class="sxs-lookup"><span data-stu-id="e8770-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="e8770-178">Uma implementação da interface é gerada dinamicamente pelo `RestService` usando `HttpClient` para fazer as chamadas de HTTP externas.</span><span class="sxs-lookup"><span data-stu-id="e8770-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="e8770-179">Uma interface e uma resposta são definidas para representar a API externa e sua resposta:</span><span class="sxs-lookup"><span data-stu-id="e8770-179">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="e8770-180">Um cliente com tipo pode ser adicionado usando a Refit para gerar a implementação:</span><span class="sxs-lookup"><span data-stu-id="e8770-180">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddControllers();
}
```

<span data-ttu-id="e8770-181">A interface definida pode ser consumida, quando necessário, com a implementação fornecida pela DI e pela Refit:</span><span class="sxs-lookup"><span data-stu-id="e8770-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="e8770-182">Middleware de solicitação de saída</span><span class="sxs-lookup"><span data-stu-id="e8770-182">Outgoing request middleware</span></span>

<span data-ttu-id="e8770-183">`HttpClient`tem o conceito de delegar manipuladores que podem ser ligados juntos para solicitações HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="e8770-183">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="e8770-184">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="e8770-184">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="e8770-185">Simplifica a definição dos manipuladores para solicitar cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="e8770-185">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="e8770-186">Suporta o registro e o encadeamento de vários manipuladores para construir um pipeline de middleware de solicitação de saída.</span><span class="sxs-lookup"><span data-stu-id="e8770-186">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="e8770-187">Cada um desses manipuladores é capaz de executar o trabalho antes e após a solicitação de saída.</span><span class="sxs-lookup"><span data-stu-id="e8770-187">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="e8770-188">Este padrão:</span><span class="sxs-lookup"><span data-stu-id="e8770-188">This pattern:</span></span>

  * <span data-ttu-id="e8770-189">É semelhante ao pipeline de middleware de entrada em ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e8770-189">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="e8770-190">Fornece um mecanismo para gerenciar preocupações transversais em torno de solicitações HTTP, tais como:</span><span class="sxs-lookup"><span data-stu-id="e8770-190">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="e8770-191">caching</span><span class="sxs-lookup"><span data-stu-id="e8770-191">caching</span></span>
    * <span data-ttu-id="e8770-192">tratamento de erros</span><span class="sxs-lookup"><span data-stu-id="e8770-192">error handling</span></span>
    * <span data-ttu-id="e8770-193">serialização</span><span class="sxs-lookup"><span data-stu-id="e8770-193">serialization</span></span>
    * <span data-ttu-id="e8770-194">registro em log</span><span class="sxs-lookup"><span data-stu-id="e8770-194">logging</span></span>

<span data-ttu-id="e8770-195">Para criar um manipulador de delegar:</span><span class="sxs-lookup"><span data-stu-id="e8770-195">To create a delegating handler:</span></span>

* <span data-ttu-id="e8770-196">Derivade <xref:System.Net.Http.DelegatingHandler>de .</span><span class="sxs-lookup"><span data-stu-id="e8770-196">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="e8770-197">Substitua <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span><span class="sxs-lookup"><span data-stu-id="e8770-197">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="e8770-198">Execute o código antes de passar a solicitação para o próximo manipulador no pipeline:</span><span class="sxs-lookup"><span data-stu-id="e8770-198">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="e8770-199">O código anterior `X-API-KEY` verifica se o cabeçalho está na solicitação.</span><span class="sxs-lookup"><span data-stu-id="e8770-199">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="e8770-200">Se `X-API-KEY` está <xref:System.Net.HttpStatusCode.BadRequest> faltando, é devolvido.</span><span class="sxs-lookup"><span data-stu-id="e8770-200">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="e8770-201">Mais de um manipulador pode ser `HttpClient` adicionado <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>à configuração para um com :</span><span class="sxs-lookup"><span data-stu-id="e8770-201">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="e8770-202">No código anterior, o `ValidateHeaderHandler` é registrado com a DI.</span><span class="sxs-lookup"><span data-stu-id="e8770-202">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="e8770-203">O `IHttpClientFactory` cria um escopo de injeção de dependência separado para cada manipulador.</span><span class="sxs-lookup"><span data-stu-id="e8770-203">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="e8770-204">Os manipuladores podem depender de serviços de qualquer escopo.</span><span class="sxs-lookup"><span data-stu-id="e8770-204">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="e8770-205">Os serviços dos quais os manipuladores dependem são descartados quando o manipulador é descartado.</span><span class="sxs-lookup"><span data-stu-id="e8770-205">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="e8770-206">Depois de registrado, o <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> poderá ser chamado, passando o tipo para o manipulador.</span><span class="sxs-lookup"><span data-stu-id="e8770-206">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="e8770-207">Vários manipuladores podem ser registrados na ordem em que eles devem ser executados.</span><span class="sxs-lookup"><span data-stu-id="e8770-207">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="e8770-208">Cada manipulador encapsula o próximo manipulador até que o `HttpClientHandler` final execute a solicitação:</span><span class="sxs-lookup"><span data-stu-id="e8770-208">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="e8770-209">Use uma das seguintes abordagens para compartilhar o estado por solicitação com os manipuladores de mensagens:</span><span class="sxs-lookup"><span data-stu-id="e8770-209">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="e8770-210">Passe dados para o manipulador usando [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span><span class="sxs-lookup"><span data-stu-id="e8770-210">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="e8770-211">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> para acessar a solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="e8770-211">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="e8770-212">Crie um objeto de armazenamento <xref:System.Threading.AsyncLocal`1> personalizado para passar os dados.</span><span class="sxs-lookup"><span data-stu-id="e8770-212">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="e8770-213">Usar manipuladores baseados no Polly</span><span class="sxs-lookup"><span data-stu-id="e8770-213">Use Polly-based handlers</span></span>

<span data-ttu-id="e8770-214">`IHttpClientFactory`integra-se com a biblioteca de terceiros [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="e8770-214">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="e8770-215">O Polly é uma biblioteca abrangente de tratamento de falha transitória e de resiliência para .NET.</span><span class="sxs-lookup"><span data-stu-id="e8770-215">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="e8770-216">Ela permite que os desenvolvedores expressem políticas, como Repetição, Disjuntor, Tempo Limite, Isolamento de Bulkhead e Fallback de maneira fluente e thread-safe.</span><span class="sxs-lookup"><span data-stu-id="e8770-216">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="e8770-217">Os métodos de extensão são fornecidos para habilitar o uso de políticas do Polly com instâncias de `HttpClient` configuradas.</span><span class="sxs-lookup"><span data-stu-id="e8770-217">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="e8770-218">As extensões polly suportam a adição de manipuladores baseados em Polly aos clientes.</span><span class="sxs-lookup"><span data-stu-id="e8770-218">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="e8770-219">Polly requer o pacote [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="e8770-219">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="e8770-220">Tratar falhas transitórias</span><span class="sxs-lookup"><span data-stu-id="e8770-220">Handle transient faults</span></span>

<span data-ttu-id="e8770-221">As falhas geralmente ocorrem quando chamadas HTTP externas são transitórias.</span><span class="sxs-lookup"><span data-stu-id="e8770-221">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="e8770-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>permite que uma política seja definida para lidar com erros transitórios.</span><span class="sxs-lookup"><span data-stu-id="e8770-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="e8770-223">Políticas configuradas `AddTransientHttpErrorPolicy` com lidar com as seguintes respostas:</span><span class="sxs-lookup"><span data-stu-id="e8770-223">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="e8770-224">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="e8770-224">HTTP 5xx</span></span>
* <span data-ttu-id="e8770-225">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="e8770-225">HTTP 408</span></span>

<span data-ttu-id="e8770-226">`AddTransientHttpErrorPolicy`fornece acesso `PolicyBuilder` a um objeto configurado para lidar com erros que representam uma possível falha transitória:</span><span class="sxs-lookup"><span data-stu-id="e8770-226">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="e8770-227">No código anterior, uma política `WaitAndRetryAsync` é definida.</span><span class="sxs-lookup"><span data-stu-id="e8770-227">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="e8770-228">As solicitações com falha são repetidas até três vezes com um atraso de 600 ms entre as tentativas.</span><span class="sxs-lookup"><span data-stu-id="e8770-228">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="e8770-229">Selecionar políticas dinamicamente</span><span class="sxs-lookup"><span data-stu-id="e8770-229">Dynamically select policies</span></span>

<span data-ttu-id="e8770-230">Os métodos de extensão são fornecidos para <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>adicionar manipuladores baseados em Polly, por exemplo, .</span><span class="sxs-lookup"><span data-stu-id="e8770-230">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="e8770-231">A `AddPolicyHandler` sobrecarga a seguir inspeciona a solicitação para decidir qual política aplicar:</span><span class="sxs-lookup"><span data-stu-id="e8770-231">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="e8770-232">No código anterior, se a solicitação de saída é um HTTP GET, um tempo limite de 10 segundos é aplicado.</span><span class="sxs-lookup"><span data-stu-id="e8770-232">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="e8770-233">Para qualquer outro método HTTP, é usado um tempo limite de 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="e8770-233">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="e8770-234">Adicionar vários manipuladores do Polly</span><span class="sxs-lookup"><span data-stu-id="e8770-234">Add multiple Polly handlers</span></span>

<span data-ttu-id="e8770-235">É comum aninhar as políticas da Polly:</span><span class="sxs-lookup"><span data-stu-id="e8770-235">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="e8770-236">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="e8770-236">In the preceding example:</span></span>

* <span data-ttu-id="e8770-237">Dois manipuladores são adicionados.</span><span class="sxs-lookup"><span data-stu-id="e8770-237">Two handlers are added.</span></span>
* <span data-ttu-id="e8770-238">O primeiro <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> manipulador usa para adicionar uma política de repetição.</span><span class="sxs-lookup"><span data-stu-id="e8770-238">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="e8770-239">As solicitações com falha são repetidas até três vezes.</span><span class="sxs-lookup"><span data-stu-id="e8770-239">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="e8770-240">A `AddTransientHttpErrorPolicy` segunda chamada adiciona uma política de disjuntor.</span><span class="sxs-lookup"><span data-stu-id="e8770-240">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="e8770-241">Outras solicitações externas são bloqueadas por 30 segundos se 5 tentativas fracassadas ocorrerem sequencialmente.</span><span class="sxs-lookup"><span data-stu-id="e8770-241">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="e8770-242">As políticas de disjuntor são políticas com estado.</span><span class="sxs-lookup"><span data-stu-id="e8770-242">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="e8770-243">Todas as chamadas por meio desse cliente compartilham o mesmo estado do circuito.</span><span class="sxs-lookup"><span data-stu-id="e8770-243">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="e8770-244">Adicionar políticas do registro do Polly</span><span class="sxs-lookup"><span data-stu-id="e8770-244">Add policies from the Polly registry</span></span>

<span data-ttu-id="e8770-245">Uma abordagem para gerenciar as políticas usadas com frequência é defini-las uma única vez e registrá-las em um `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="e8770-245">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="e8770-246">No seguinte código:</span><span class="sxs-lookup"><span data-stu-id="e8770-246">In the following code:</span></span>

* <span data-ttu-id="e8770-247">As polícias "regular" e "longa" são adicionadas.</span><span class="sxs-lookup"><span data-stu-id="e8770-247">The "regular" and "long" polices are added.</span></span>
* <span data-ttu-id="e8770-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>adiciona as políticas "regular" e "longa" do registro.</span><span class="sxs-lookup"><span data-stu-id="e8770-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="e8770-249">Para obter `IHttpClientFactory` mais informações sobre integrações e polly, consulte a [wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="e8770-249">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="e8770-250">HttpClient e gerenciamento de tempo de vida</span><span class="sxs-lookup"><span data-stu-id="e8770-250">HttpClient and lifetime management</span></span>

<span data-ttu-id="e8770-251">Uma nova instância de `HttpClient` é chamada, sempre que `CreateClient` é chamado na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="e8770-251">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="e8770-252">Um <xref:System.Net.Http.HttpMessageHandler> é criado por cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="e8770-252">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="e8770-253">O alocador gerencia a vida útil das instâncias do `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="e8770-253">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="e8770-254">`IHttpClientFactory` cria pools com as instâncias de `HttpMessageHandler` criadas pelo alocador para reduzir o consumo de recursos.</span><span class="sxs-lookup"><span data-stu-id="e8770-254">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="e8770-255">Uma instância de `HttpMessageHandler` poderá ser reutilizada no pool, ao criar uma nova instância de `HttpClient`, se o respectivo tempo de vida não tiver expirado.</span><span class="sxs-lookup"><span data-stu-id="e8770-255">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="e8770-256">O pooling de manipuladores é preferível porque normalmente cada manipulador gerencia as próprias conexões HTTP subjacentes.</span><span class="sxs-lookup"><span data-stu-id="e8770-256">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="e8770-257">Criar mais manipuladores do que o necessário pode resultar em atrasos de conexão.</span><span class="sxs-lookup"><span data-stu-id="e8770-257">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="e8770-258">Alguns manipuladores também mantêm as conexões abertas indefinidamente, o que pode impedir que o manipulador reamente às alterações de DNS (Domain Name System).</span><span class="sxs-lookup"><span data-stu-id="e8770-258">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="e8770-259">O tempo de vida padrão do manipulador é de 2 minutos.</span><span class="sxs-lookup"><span data-stu-id="e8770-259">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="e8770-260">O valor padrão pode ser substituído por uma base de cliente nomeada:</span><span class="sxs-lookup"><span data-stu-id="e8770-260">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="e8770-261">`HttpClient`as instâncias geralmente podem ser tratadas como objetos .NET **que não** requerem eliminação.</span><span class="sxs-lookup"><span data-stu-id="e8770-261">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="e8770-262">O descarte cancela as solicitações de saída e garante que a determinada instância de `HttpClient` não seja usada depois de chamar <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="e8770-262">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="e8770-263">`IHttpClientFactory` rastreia e descarta recursos usados pelas instâncias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e8770-263">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="e8770-264">Manter uma única instância de `HttpClient` ativa por uma longa duração é um padrão comum usado, antes do início de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="e8770-264">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="e8770-265">Esse padrão se torna desnecessário após a migração para `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="e8770-265">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="e8770-266">Alternativas para IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="e8770-266">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="e8770-267">O `IHttpClientFactory` uso em um aplicativo habilitado para DI evita:</span><span class="sxs-lookup"><span data-stu-id="e8770-267">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="e8770-268">Problemas de exaustão de recursos agrupando `HttpMessageHandler` instâncias.</span><span class="sxs-lookup"><span data-stu-id="e8770-268">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="e8770-269">Problemas de DNS `HttpMessageHandler` obsoletos por instâncias de ciclismo em intervalos regulares.</span><span class="sxs-lookup"><span data-stu-id="e8770-269">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="e8770-270">Existem maneiras alternativas de resolver os <xref:System.Net.Http.SocketsHttpHandler> problemas precedentes usando uma instância de longa duração.</span><span class="sxs-lookup"><span data-stu-id="e8770-270">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="e8770-271">Crie uma `SocketsHttpHandler` instância de quando o aplicativo começa e use-o para a vida útil do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e8770-271">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="e8770-272">Configure-se <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> a um valor apropriado com base nos tempos de atualização do DNS.</span><span class="sxs-lookup"><span data-stu-id="e8770-272">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="e8770-273">Crie `HttpClient` instâncias usando `new HttpClient(handler, disposeHandler: false)` conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="e8770-273">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="e8770-274">As abordagens anteriores resolvem `IHttpClientFactory` os problemas de gestão de recursos que resolvem de forma semelhante.</span><span class="sxs-lookup"><span data-stu-id="e8770-274">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="e8770-275">As `SocketsHttpHandler` conexões `HttpClient` de compartilhamento entre instâncias.</span><span class="sxs-lookup"><span data-stu-id="e8770-275">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="e8770-276">Esse compartilhamento evita a exaustão do soquete.</span><span class="sxs-lookup"><span data-stu-id="e8770-276">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="e8770-277">Os `SocketsHttpHandler` ciclos se `PooledConnectionLifetime` conexões de acordo com para evitar problemas de DNS obsoletos.</span><span class="sxs-lookup"><span data-stu-id="e8770-277">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="e8770-278">Cookies</span><span class="sxs-lookup"><span data-stu-id="e8770-278">Cookies</span></span>

<span data-ttu-id="e8770-279">As instâncias agrupadas resultam `HttpMessageHandler` em `CookieContainer` objetos sendo compartilhados.</span><span class="sxs-lookup"><span data-stu-id="e8770-279">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="e8770-280">O `CookieContainer` compartilhamento de objetos não previsto muitas vezes resulta em código incorreto.</span><span class="sxs-lookup"><span data-stu-id="e8770-280">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="e8770-281">Para aplicativos que exigem cookies, considere:</span><span class="sxs-lookup"><span data-stu-id="e8770-281">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="e8770-282">Desabilitando o manuseio automático de cookies</span><span class="sxs-lookup"><span data-stu-id="e8770-282">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="e8770-283">Evitando`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="e8770-283">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="e8770-284">Chamada <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para desativar o manuseio automático de cookies:</span><span class="sxs-lookup"><span data-stu-id="e8770-284">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="e8770-285">Registro em log</span><span class="sxs-lookup"><span data-stu-id="e8770-285">Logging</span></span>

<span data-ttu-id="e8770-286">Os clientes criado pelo `IHttpClientFactory` registram mensagens de log para todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="e8770-286">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="e8770-287">Habilite o nível de informações apropriado na configuração de registro para ver as mensagens de log padrão.</span><span class="sxs-lookup"><span data-stu-id="e8770-287">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="e8770-288">Os registros em log adicionais, como o registro em log dos cabeçalhos de solicitação, estão incluídos somente no nível de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="e8770-288">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="e8770-289">A categoria de log usada para cada cliente inclui o nome do cliente.</span><span class="sxs-lookup"><span data-stu-id="e8770-289">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="e8770-290">Um cliente chamado *MyNamedClient,* por exemplo, registra mensagens com uma categoria de "System.Net.Http.HttpClient. **MyNamedClient**. LogicalHandler".</span><span class="sxs-lookup"><span data-stu-id="e8770-290">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="e8770-291">Mensagens sufixadas com *LogicalHandler* ocorrem fora do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="e8770-291">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="e8770-292">Na solicitação, as mensagens são registradas em log antes que qualquer outro manipulador no pipeline a tenha processado.</span><span class="sxs-lookup"><span data-stu-id="e8770-292">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="e8770-293">Na resposta, as mensagens são registradas depois que qualquer outro manipulador do pipeline tenha recebido a resposta.</span><span class="sxs-lookup"><span data-stu-id="e8770-293">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="e8770-294">O registro em log também ocorre dentro do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="e8770-294">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="e8770-295">No exemplo *MyNamedClient,* essas mensagens são registradas com a categoria de log "System.Net.Http.HttpClient. **MyNamedClient**. ClientHandler".</span><span class="sxs-lookup"><span data-stu-id="e8770-295">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="e8770-296">Para a solicitação, isso ocorre após todos os outros manipuladores terem sido executados e imediatamente antes da solicitação ser enviada.</span><span class="sxs-lookup"><span data-stu-id="e8770-296">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="e8770-297">Na resposta, esse registro em log inclui o estado da resposta antes que ela seja retornada por meio do pipeline do manipulador.</span><span class="sxs-lookup"><span data-stu-id="e8770-297">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="e8770-298">Habilitar o registro em log dentro e fora do pipeline permite a inspeção das alterações feitas por outros manipuladores do pipeline.</span><span class="sxs-lookup"><span data-stu-id="e8770-298">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="e8770-299">Isso pode incluir alterações na solicitação de cabeçalhos ou no código de status de resposta.</span><span class="sxs-lookup"><span data-stu-id="e8770-299">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="e8770-300">A inclusão do nome do cliente na categoria log permite a filtragem de log para clientes específicos nomeados.</span><span class="sxs-lookup"><span data-stu-id="e8770-300">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="e8770-301">Configurar o HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="e8770-301">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="e8770-302">Talvez seja necessário controlar a configuração do `HttpMessageHandler` interno usado por um cliente.</span><span class="sxs-lookup"><span data-stu-id="e8770-302">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="e8770-303">Um `IHttpClientBuilder` é retornado ao adicionar clientes nomeados ou com tipo.</span><span class="sxs-lookup"><span data-stu-id="e8770-303">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="e8770-304">O método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pode ser usado para definir um representante.</span><span class="sxs-lookup"><span data-stu-id="e8770-304">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="e8770-305">O representante que é usado para criar e configurar o `HttpMessageHandler` primário usado pelo cliente:</span><span class="sxs-lookup"><span data-stu-id="e8770-305">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="e8770-306">Usar IHttpClientFactory em um aplicativo de console</span><span class="sxs-lookup"><span data-stu-id="e8770-306">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="e8770-307">Em um aplicativo de console, adicione as seguintes referências de pacote ao projeto:</span><span class="sxs-lookup"><span data-stu-id="e8770-307">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="e8770-308">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="e8770-308">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="e8770-309">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="e8770-309">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="e8770-310">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="e8770-310">In the following example:</span></span>

* <span data-ttu-id="e8770-311"><xref:System.Net.Http.IHttpClientFactory> é registrado no contêiner de serviço do [Host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="e8770-311"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="e8770-312">`MyService` cria uma instância de fábrica do cliente do serviço, que é usada para criar um `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e8770-312">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="e8770-313">`HttpClient` é usado para recuperar uma página da Web.</span><span class="sxs-lookup"><span data-stu-id="e8770-313">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="e8770-314">`Main` cria um escopo para executar o método `GetPage` do serviço e gravar os primeiros 500 caracteres do conteúdo da página da Web no console.</span><span class="sxs-lookup"><span data-stu-id="e8770-314">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="e8770-315">Middleware de propagação de cabeçalho</span><span class="sxs-lookup"><span data-stu-id="e8770-315">Header propagation middleware</span></span>

<span data-ttu-id="e8770-316">A propagação de cabeçalho é um middleware ASP.NET Core para propagar cabeçalhos HTTP da solicitação recebida para as solicitações http cliente de saída.</span><span class="sxs-lookup"><span data-stu-id="e8770-316">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="e8770-317">Para usar a propagação de cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="e8770-317">To use header propagation:</span></span>

* <span data-ttu-id="e8770-318">Consulte o pacote [Microsoft.AspNetCore.HeaderPropagation.](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation)</span><span class="sxs-lookup"><span data-stu-id="e8770-318">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="e8770-319">Configure o middleware e `HttpClient` em: `Startup`</span><span class="sxs-lookup"><span data-stu-id="e8770-319">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="e8770-320">O cliente inclui os cabeçalhos configurados nas solicitações de saída:</span><span class="sxs-lookup"><span data-stu-id="e8770-320">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="e8770-321">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e8770-321">Additional resources</span></span>

* [<span data-ttu-id="e8770-322">Usar HttpClientFactory implementar solicitações HTTP resilientes</span><span class="sxs-lookup"><span data-stu-id="e8770-322">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="e8770-323">Implementar repetições de chamadas HTTP com retirada exponencial com o HttpClientFactory e políticas da Polly</span><span class="sxs-lookup"><span data-stu-id="e8770-323">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="e8770-324">Implementar o padrão de disjuntor</span><span class="sxs-lookup"><span data-stu-id="e8770-324">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="e8770-325">Como serializar e desserializar JSON em .NET</span><span class="sxs-lookup"><span data-stu-id="e8770-325">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="e8770-326">Por [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) e [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="e8770-326">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="e8770-327">É possível registrar e usar um <xref:System.Net.Http.IHttpClientFactory> para configurar e criar instâncias de <xref:System.Net.Http.HttpClient> em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e8770-327">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="e8770-328">Ele oferece os seguintes benefícios:</span><span class="sxs-lookup"><span data-stu-id="e8770-328">It offers the following benefits:</span></span>

* <span data-ttu-id="e8770-329">Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e8770-329">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="e8770-330">Por exemplo, um cliente do *github* pode ser registrado e configurado para acessar o [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="e8770-330">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="e8770-331">Um cliente padrão pode ser registrado para outras finalidades.</span><span class="sxs-lookup"><span data-stu-id="e8770-331">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="e8770-332">Codifica o conceito de middleware de saída por meio da delegação de manipuladores no `HttpClient` e fornece extensões para que o middleware baseado no Polly possa aproveitar esse recurso.</span><span class="sxs-lookup"><span data-stu-id="e8770-332">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="e8770-333">Gerencia o pooling e o tempo de vida das instâncias de `HttpClientMessageHandler` subjacentes para evitar problemas de DNS comuns que ocorrem no gerenciamento manual de tempos de vida de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e8770-333">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="e8770-334">Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.</span><span class="sxs-lookup"><span data-stu-id="e8770-334">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="e8770-335">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e8770-335">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="e8770-336">Padrões de consumo</span><span class="sxs-lookup"><span data-stu-id="e8770-336">Consumption patterns</span></span>

<span data-ttu-id="e8770-337">Há várias maneiras de usar o `IHttpClientFactory` em um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e8770-337">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="e8770-338">Uso básico</span><span class="sxs-lookup"><span data-stu-id="e8770-338">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="e8770-339">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="e8770-339">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="e8770-340">Clientes digitados</span><span class="sxs-lookup"><span data-stu-id="e8770-340">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="e8770-341">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="e8770-341">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="e8770-342">Nenhum deles é estritamente superiores ao outro.</span><span class="sxs-lookup"><span data-stu-id="e8770-342">None of them are strictly superior to another.</span></span> <span data-ttu-id="e8770-343">A melhor abordagem depende das restrições do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e8770-343">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="e8770-344">Uso básico</span><span class="sxs-lookup"><span data-stu-id="e8770-344">Basic usage</span></span>

<span data-ttu-id="e8770-345">O `IHttpClientFactory` pode ser registrado chamando o método de extensão `AddHttpClient` em `IServiceCollection`, dentro do método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e8770-345">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="e8770-346">Depois de registrado, o código pode aceitar um `IHttpClientFactory` em qualquer lugar em que os serviços possam ser injetados com [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e8770-346">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e8770-347">O `IHttpClientFactory` pode ser usado `HttpClient` para criar uma instância:</span><span class="sxs-lookup"><span data-stu-id="e8770-347">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="e8770-348">Usar o `IHttpClientFactory` dessa forma é uma ótima maneira de refatorar um aplicativo existente.</span><span class="sxs-lookup"><span data-stu-id="e8770-348">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="e8770-349">Não há nenhum impacto na maneira em que o `HttpClient` é usado.</span><span class="sxs-lookup"><span data-stu-id="e8770-349">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="e8770-350">Nos locais em que as instâncias de `HttpClient` são criadas no momento, substitua essas ocorrências por uma chamada a <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="e8770-350">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="e8770-351">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="e8770-351">Named clients</span></span>

<span data-ttu-id="e8770-352">Quando um aplicativo exige vários usos distintos do `HttpClient`, cada um com uma configuração diferente, uma opção é usar **clientes nomeados**.</span><span class="sxs-lookup"><span data-stu-id="e8770-352">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="e8770-353">A configuração de um `HttpClient` nomeado pode ser especificada durante o registro em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e8770-353">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="e8770-354">No código anterior, `AddHttpClient` é chamado, fornecendo o nome *github*.</span><span class="sxs-lookup"><span data-stu-id="e8770-354">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="e8770-355">Esse cliente tem algumas configurações padrão aplicadas, ou seja, o endereço básico e dois cabeçalhos necessários para trabalhar com a API do GitHub.</span><span class="sxs-lookup"><span data-stu-id="e8770-355">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="e8770-356">Toda vez que o `CreateClient` é chamado, uma nova instância de `HttpClient` é criada e a ação de configuração é chamada.</span><span class="sxs-lookup"><span data-stu-id="e8770-356">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="e8770-357">Para consumir um cliente nomeado, um parâmetro de cadeia de caracteres pode ser passado para `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="e8770-357">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="e8770-358">Especifique o nome do cliente a ser criado:</span><span class="sxs-lookup"><span data-stu-id="e8770-358">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="e8770-359">No código anterior, a solicitação não precisa especificar um nome do host.</span><span class="sxs-lookup"><span data-stu-id="e8770-359">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="e8770-360">Ela pode passar apenas o caminho, pois o endereço básico configurado para o cliente é usado.</span><span class="sxs-lookup"><span data-stu-id="e8770-360">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="e8770-361">Clientes com tipo</span><span class="sxs-lookup"><span data-stu-id="e8770-361">Typed clients</span></span>

<span data-ttu-id="e8770-362">Clientes com tipo:</span><span class="sxs-lookup"><span data-stu-id="e8770-362">Typed clients:</span></span>

* <span data-ttu-id="e8770-363">Fornecem as mesmas funcionalidade que os clientes nomeados sem a necessidade de usar cadeias de caracteres como chaves.</span><span class="sxs-lookup"><span data-stu-id="e8770-363">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="e8770-364">Fornecem a ajuda do IntelliSense e do compilador durante o consumo de clientes.</span><span class="sxs-lookup"><span data-stu-id="e8770-364">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="e8770-365">Fornecem um único local para configurar e interagir com um determinado `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e8770-365">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="e8770-366">Por exemplo, um único cliente com tipo pode ser usado para um único ponto de extremidade de back-end e encapsular toda a lógica que lida com esse ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="e8770-366">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="e8770-367">Funcionam com a DI e podem ser injetados no local necessário no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e8770-367">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="e8770-368">Um cliente digitado `HttpClient` aceita um parâmetro em seu construtor:</span><span class="sxs-lookup"><span data-stu-id="e8770-368">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="e8770-369">No código anterior, a configuração é movida para o cliente com tipo.</span><span class="sxs-lookup"><span data-stu-id="e8770-369">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="e8770-370">O objeto `HttpClient` é exposto como uma propriedade pública.</span><span class="sxs-lookup"><span data-stu-id="e8770-370">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="e8770-371">É possível definir métodos específicos da API que expõem a funcionalidade `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e8770-371">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="e8770-372">O método `GetAspNetDocsIssues` encapsula o código necessário para consultar e analisar os últimos problemas em aberto de um repositório GitHub.</span><span class="sxs-lookup"><span data-stu-id="e8770-372">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="e8770-373">Para registrar um cliente com tipo, o método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> genérico pode ser usado em `Startup.ConfigureServices`, especificando a classe do cliente com tipo:</span><span class="sxs-lookup"><span data-stu-id="e8770-373">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="e8770-374">O cliente com tipo é registrado como transitório com a DI.</span><span class="sxs-lookup"><span data-stu-id="e8770-374">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="e8770-375">O cliente com tipo pode ser injetado e consumido diretamente:</span><span class="sxs-lookup"><span data-stu-id="e8770-375">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="e8770-376">Se preferir, a configuração de um cliente com tipo poderá ser especificada durante o registro em `Startup.ConfigureServices` e não no construtor do cliente com tipo:</span><span class="sxs-lookup"><span data-stu-id="e8770-376">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="e8770-377">É possível encapsular totalmente o `HttpClient` dentro de um cliente com tipo.</span><span class="sxs-lookup"><span data-stu-id="e8770-377">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="e8770-378">Em vez de o expor como uma propriedade, é possível fornecer métodos públicos que chamam a instância de `HttpClient` internamente.</span><span class="sxs-lookup"><span data-stu-id="e8770-378">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="e8770-379">No código anterior, o `HttpClient` é armazenado como um campo privado.</span><span class="sxs-lookup"><span data-stu-id="e8770-379">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="e8770-380">Todo o acesso para fazer chamadas externas passa pelo método `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="e8770-380">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="e8770-381">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="e8770-381">Generated clients</span></span>

<span data-ttu-id="e8770-382">O `IHttpClientFactory` pode ser usado em combinação com outras bibliotecas de terceiros, como a [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="e8770-382">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="e8770-383">Refit é uma biblioteca REST para .NET.</span><span class="sxs-lookup"><span data-stu-id="e8770-383">Refit is a REST library for .NET.</span></span> <span data-ttu-id="e8770-384">Ela converte APIs REST em interfaces dinâmicas.</span><span class="sxs-lookup"><span data-stu-id="e8770-384">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="e8770-385">Uma implementação da interface é gerada dinamicamente pelo `RestService` usando `HttpClient` para fazer as chamadas de HTTP externas.</span><span class="sxs-lookup"><span data-stu-id="e8770-385">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="e8770-386">Uma interface e uma resposta são definidas para representar a API externa e sua resposta:</span><span class="sxs-lookup"><span data-stu-id="e8770-386">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="e8770-387">Um cliente com tipo pode ser adicionado usando a Refit para gerar a implementação:</span><span class="sxs-lookup"><span data-stu-id="e8770-387">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("https://localhost:5001");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="e8770-388">A interface definida pode ser consumida, quando necessário, com a implementação fornecida pela DI e pela Refit:</span><span class="sxs-lookup"><span data-stu-id="e8770-388">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="e8770-389">Middleware de solicitação de saída</span><span class="sxs-lookup"><span data-stu-id="e8770-389">Outgoing request middleware</span></span>

<span data-ttu-id="e8770-390">O `HttpClient` já tem o conceito de delegar manipuladores que podem ser vinculados para solicitações HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="e8770-390">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="e8770-391">O `IHttpClientFactory` facilita a definição dos manipuladores a serem aplicados a cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="e8770-391">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="e8770-392">Ele permite o registro e o encadeamento de vários manipuladores para criar um pipeline do middleware de solicitação saída.</span><span class="sxs-lookup"><span data-stu-id="e8770-392">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="e8770-393">Cada um desses manipuladores é capaz de executar o trabalho antes e após a solicitação de saída.</span><span class="sxs-lookup"><span data-stu-id="e8770-393">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="e8770-394">Esse padrão é semelhante ao pipeline do middleware de entrada no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e8770-394">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="e8770-395">O padrão fornece um mecanismo para gerenciar interesses paralelos em relação às solicitações HTTP, incluindo o armazenamento em cache, o tratamento de erro, a serialização e o registro em log.</span><span class="sxs-lookup"><span data-stu-id="e8770-395">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="e8770-396">Para criar um manipulador, defina uma classe derivando-a de <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="e8770-396">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="e8770-397">Substitua o método `SendAsync` para executar o código antes de passar a solicitação para o próximo manipulador no pipeline:</span><span class="sxs-lookup"><span data-stu-id="e8770-397">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="e8770-398">O código anterior define um manipulador básico.</span><span class="sxs-lookup"><span data-stu-id="e8770-398">The preceding code defines a basic handler.</span></span> <span data-ttu-id="e8770-399">Ele verifica se um cabeçalho `X-API-KEY` foi incluído na solicitação.</span><span class="sxs-lookup"><span data-stu-id="e8770-399">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="e8770-400">Se o cabeçalho estiver ausente, isso poderá evitar a chamada de HTTP e retornar uma resposta adequada.</span><span class="sxs-lookup"><span data-stu-id="e8770-400">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="e8770-401">Durante o registro, um ou mais manipuladores `HttpClient`podem ser adicionados à configuração de um .</span><span class="sxs-lookup"><span data-stu-id="e8770-401">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="e8770-402">Essa tarefa é realizada por meio de métodos de extensão no <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e8770-402">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="e8770-403">No código anterior, o `ValidateHeaderHandler` é registrado com a DI.</span><span class="sxs-lookup"><span data-stu-id="e8770-403">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="e8770-404">O `IHttpClientFactory` cria um escopo de injeção de dependência separado para cada manipulador.</span><span class="sxs-lookup"><span data-stu-id="e8770-404">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="e8770-405">Os manipuladores podem depender de serviços de qualquer escopo.</span><span class="sxs-lookup"><span data-stu-id="e8770-405">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="e8770-406">Os serviços dos quais os manipuladores dependem são descartados quando o manipulador é descartado.</span><span class="sxs-lookup"><span data-stu-id="e8770-406">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="e8770-407">Depois de registrado, o <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> poderá ser chamado, passando o tipo para o manipulador.</span><span class="sxs-lookup"><span data-stu-id="e8770-407">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="e8770-408">Vários manipuladores podem ser registrados na ordem em que eles devem ser executados.</span><span class="sxs-lookup"><span data-stu-id="e8770-408">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="e8770-409">Cada manipulador encapsula o próximo manipulador até que o `HttpClientHandler` final execute a solicitação:</span><span class="sxs-lookup"><span data-stu-id="e8770-409">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="e8770-410">Use uma das seguintes abordagens para compartilhar o estado por solicitação com os manipuladores de mensagens:</span><span class="sxs-lookup"><span data-stu-id="e8770-410">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="e8770-411">Passe os dados pelo manipulador usando `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="e8770-411">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="e8770-412">Use `IHttpContextAccessor` para acessar a solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="e8770-412">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="e8770-413">Crie um objeto de armazenamento `AsyncLocal` personalizado para passar os dados.</span><span class="sxs-lookup"><span data-stu-id="e8770-413">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="e8770-414">Usar manipuladores baseados no Polly</span><span class="sxs-lookup"><span data-stu-id="e8770-414">Use Polly-based handlers</span></span>

<span data-ttu-id="e8770-415">O `IHttpClientFactory` integra-se a uma biblioteca de terceiros popular chamada [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="e8770-415">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="e8770-416">O Polly é uma biblioteca abrangente de tratamento de falha transitória e de resiliência para .NET.</span><span class="sxs-lookup"><span data-stu-id="e8770-416">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="e8770-417">Ela permite que os desenvolvedores expressem políticas, como Repetição, Disjuntor, Tempo Limite, Isolamento de Bulkhead e Fallback de maneira fluente e thread-safe.</span><span class="sxs-lookup"><span data-stu-id="e8770-417">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="e8770-418">Os métodos de extensão são fornecidos para habilitar o uso de políticas do Polly com instâncias de `HttpClient` configuradas.</span><span class="sxs-lookup"><span data-stu-id="e8770-418">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="e8770-419">As extensões do Polly:</span><span class="sxs-lookup"><span data-stu-id="e8770-419">The Polly extensions:</span></span>

* <span data-ttu-id="e8770-420">Dão suporte à adição de manipuladores baseados no Polly aos clientes.</span><span class="sxs-lookup"><span data-stu-id="e8770-420">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="e8770-421">Podem ser usadas depois de instalar o pacote do NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="e8770-421">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="e8770-422">O pacote não está incluído na estrutura ASP.NET Core compartilhada.</span><span class="sxs-lookup"><span data-stu-id="e8770-422">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="e8770-423">Tratar falhas transitórias</span><span class="sxs-lookup"><span data-stu-id="e8770-423">Handle transient faults</span></span>

<span data-ttu-id="e8770-424">As falhas mais comuns ocorrem quando as chamadas HTTP externas são transitórias.</span><span class="sxs-lookup"><span data-stu-id="e8770-424">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="e8770-425">Um método de extensão conveniente chamado `AddTransientHttpErrorPolicy` é incluído para permitir que uma política seja definido para tratar erros transitórios.</span><span class="sxs-lookup"><span data-stu-id="e8770-425">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="e8770-426">As políticas configuradas com esse método de extensão tratam `HttpRequestException`, respostas HTTP 5xx e respostas HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="e8770-426">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="e8770-427">A extensão `AddTransientHttpErrorPolicy` pode ser usada em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e8770-427">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e8770-428">A extensão fornece acesso a um objeto `PolicyBuilder` configurado para tratar erros que representam uma possível falha transitória:</span><span class="sxs-lookup"><span data-stu-id="e8770-428">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="e8770-429">No código anterior, uma política `WaitAndRetryAsync` é definida.</span><span class="sxs-lookup"><span data-stu-id="e8770-429">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="e8770-430">As solicitações com falha são repetidas até três vezes com um atraso de 600 ms entre as tentativas.</span><span class="sxs-lookup"><span data-stu-id="e8770-430">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="e8770-431">Selecionar políticas dinamicamente</span><span class="sxs-lookup"><span data-stu-id="e8770-431">Dynamically select policies</span></span>

<span data-ttu-id="e8770-432">Existem métodos de extensão adicionais que podem ser usados para adicionar manipuladores baseados no Polly.</span><span class="sxs-lookup"><span data-stu-id="e8770-432">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="e8770-433">Uma dessas extensões é a `AddPolicyHandler`, que tem várias sobrecargas.</span><span class="sxs-lookup"><span data-stu-id="e8770-433">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="e8770-434">Uma sobrecarga permite que a solicitação seja inspecionada durante a definição da política a ser aplicada:</span><span class="sxs-lookup"><span data-stu-id="e8770-434">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="e8770-435">No código anterior, se a solicitação de saída é um HTTP GET, um tempo limite de 10 segundos é aplicado.</span><span class="sxs-lookup"><span data-stu-id="e8770-435">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="e8770-436">Para qualquer outro método HTTP, é usado um tempo limite de 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="e8770-436">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="e8770-437">Adicionar vários manipuladores do Polly</span><span class="sxs-lookup"><span data-stu-id="e8770-437">Add multiple Polly handlers</span></span>

<span data-ttu-id="e8770-438">É comum aninhar políticas do Polly para fornecer funcionalidade avançada:</span><span class="sxs-lookup"><span data-stu-id="e8770-438">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="e8770-439">No exemplo anterior, dois manipuladores são adicionados.</span><span class="sxs-lookup"><span data-stu-id="e8770-439">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="e8770-440">O primeiro usa a extensão `AddTransientHttpErrorPolicy` para adicionar uma política de repetição.</span><span class="sxs-lookup"><span data-stu-id="e8770-440">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="e8770-441">As solicitações com falha são repetidas até três vezes.</span><span class="sxs-lookup"><span data-stu-id="e8770-441">Failed requests are retried up to three times.</span></span> <span data-ttu-id="e8770-442">A segunda chamada a `AddTransientHttpErrorPolicy` adiciona uma política de disjuntor.</span><span class="sxs-lookup"><span data-stu-id="e8770-442">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="e8770-443">As solicitações externas adicionais são bloqueadas por 30 segundos quando ocorrem cinco tentativas com falha consecutivamente.</span><span class="sxs-lookup"><span data-stu-id="e8770-443">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="e8770-444">As políticas de disjuntor são políticas com estado.</span><span class="sxs-lookup"><span data-stu-id="e8770-444">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="e8770-445">Todas as chamadas por meio desse cliente compartilham o mesmo estado do circuito.</span><span class="sxs-lookup"><span data-stu-id="e8770-445">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="e8770-446">Adicionar políticas do registro do Polly</span><span class="sxs-lookup"><span data-stu-id="e8770-446">Add policies from the Polly registry</span></span>

<span data-ttu-id="e8770-447">Uma abordagem para gerenciar as políticas usadas com frequência é defini-las uma única vez e registrá-las em um `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="e8770-447">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="e8770-448">É fornecido um método de extensão que permite que um manipulador seja adicionado usando uma política do registro:</span><span class="sxs-lookup"><span data-stu-id="e8770-448">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="e8770-449">No código anterior, duas políticas são registradas quando `PolicyRegistry` é adicionado ao `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="e8770-449">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="e8770-450">Para usar uma política do registro, o método `AddPolicyHandlerFromRegistry` é usado, passando o nome da política a ser aplicada.</span><span class="sxs-lookup"><span data-stu-id="e8770-450">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="e8770-451">Mais informações sobre o `IHttpClientFactory` e as integrações do Polly podem ser encontradas no [Wiki do Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="e8770-451">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="e8770-452">HttpClient e gerenciamento de tempo de vida</span><span class="sxs-lookup"><span data-stu-id="e8770-452">HttpClient and lifetime management</span></span>

<span data-ttu-id="e8770-453">Uma nova instância de `HttpClient` é chamada, sempre que `CreateClient` é chamado na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="e8770-453">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="e8770-454">Há um <xref:System.Net.Http.HttpMessageHandler> por cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="e8770-454">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="e8770-455">O alocador gerencia a vida útil das instâncias do `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="e8770-455">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="e8770-456">`IHttpClientFactory` cria pools com as instâncias de `HttpMessageHandler` criadas pelo alocador para reduzir o consumo de recursos.</span><span class="sxs-lookup"><span data-stu-id="e8770-456">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="e8770-457">Uma instância de `HttpMessageHandler` poderá ser reutilizada no pool, ao criar uma nova instância de `HttpClient`, se o respectivo tempo de vida não tiver expirado.</span><span class="sxs-lookup"><span data-stu-id="e8770-457">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="e8770-458">O pooling de manipuladores é preferível porque normalmente cada manipulador gerencia as próprias conexões HTTP subjacentes.</span><span class="sxs-lookup"><span data-stu-id="e8770-458">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="e8770-459">Criar mais manipuladores do que o necessário pode resultar em atrasos de conexão.</span><span class="sxs-lookup"><span data-stu-id="e8770-459">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="e8770-460">Alguns manipuladores também mantêm as conexões abertas indefinidamente, o que pode impedir que o manipulador reaja a alterações de DNS.</span><span class="sxs-lookup"><span data-stu-id="e8770-460">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="e8770-461">O tempo de vida padrão do manipulador é de 2 minutos.</span><span class="sxs-lookup"><span data-stu-id="e8770-461">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="e8770-462">O valor padrão pode ser substituído para cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="e8770-462">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="e8770-463">Para substituí-lo, chame <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> no `IHttpClientBuilder` que é retornado ao criar o cliente:</span><span class="sxs-lookup"><span data-stu-id="e8770-463">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="e8770-464">Não é necessário descartar o cliente.</span><span class="sxs-lookup"><span data-stu-id="e8770-464">Disposal of the client isn't required.</span></span> <span data-ttu-id="e8770-465">O descarte cancela as solicitações de saída e garante que a determinada instância de `HttpClient` não seja usada depois de chamar <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="e8770-465">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="e8770-466">`IHttpClientFactory` rastreia e descarta recursos usados pelas instâncias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e8770-466">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="e8770-467">Geralmente, as instâncias de `HttpClient` podem ser tratadas como objetos do .NET e não exigem descarte.</span><span class="sxs-lookup"><span data-stu-id="e8770-467">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="e8770-468">Manter uma única instância de `HttpClient` ativa por uma longa duração é um padrão comum usado, antes do início de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="e8770-468">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="e8770-469">Esse padrão se torna desnecessário após a migração para `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="e8770-469">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="e8770-470">Alternativas para IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="e8770-470">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="e8770-471">O `IHttpClientFactory` uso em um aplicativo habilitado para DI evita:</span><span class="sxs-lookup"><span data-stu-id="e8770-471">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="e8770-472">Problemas de exaustão de recursos agrupando `HttpMessageHandler` instâncias.</span><span class="sxs-lookup"><span data-stu-id="e8770-472">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="e8770-473">Problemas de DNS `HttpMessageHandler` obsoletos por instâncias de ciclismo em intervalos regulares.</span><span class="sxs-lookup"><span data-stu-id="e8770-473">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="e8770-474">Existem maneiras alternativas de resolver os <xref:System.Net.Http.SocketsHttpHandler> problemas precedentes usando uma instância de longa duração.</span><span class="sxs-lookup"><span data-stu-id="e8770-474">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="e8770-475">Crie uma `SocketsHttpHandler` instância de quando o aplicativo começa e use-o para a vida útil do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e8770-475">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="e8770-476">Configure-se <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> a um valor apropriado com base nos tempos de atualização do DNS.</span><span class="sxs-lookup"><span data-stu-id="e8770-476">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="e8770-477">Crie `HttpClient` instâncias usando `new HttpClient(handler, disposeHandler: false)` conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="e8770-477">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="e8770-478">As abordagens anteriores resolvem `IHttpClientFactory` os problemas de gestão de recursos que resolvem de forma semelhante.</span><span class="sxs-lookup"><span data-stu-id="e8770-478">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="e8770-479">As `SocketsHttpHandler` conexões `HttpClient` de compartilhamento entre instâncias.</span><span class="sxs-lookup"><span data-stu-id="e8770-479">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="e8770-480">Esse compartilhamento evita a exaustão do soquete.</span><span class="sxs-lookup"><span data-stu-id="e8770-480">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="e8770-481">Os `SocketsHttpHandler` ciclos se `PooledConnectionLifetime` conexões de acordo com para evitar problemas de DNS obsoletos.</span><span class="sxs-lookup"><span data-stu-id="e8770-481">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="e8770-482">Cookies</span><span class="sxs-lookup"><span data-stu-id="e8770-482">Cookies</span></span>

<span data-ttu-id="e8770-483">As instâncias agrupadas resultam `HttpMessageHandler` em `CookieContainer` objetos sendo compartilhados.</span><span class="sxs-lookup"><span data-stu-id="e8770-483">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="e8770-484">O `CookieContainer` compartilhamento de objetos não previsto muitas vezes resulta em código incorreto.</span><span class="sxs-lookup"><span data-stu-id="e8770-484">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="e8770-485">Para aplicativos que exigem cookies, considere:</span><span class="sxs-lookup"><span data-stu-id="e8770-485">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="e8770-486">Desabilitando o manuseio automático de cookies</span><span class="sxs-lookup"><span data-stu-id="e8770-486">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="e8770-487">Evitando`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="e8770-487">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="e8770-488">Chamada <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para desativar o manuseio automático de cookies:</span><span class="sxs-lookup"><span data-stu-id="e8770-488">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="e8770-489">Registro em log</span><span class="sxs-lookup"><span data-stu-id="e8770-489">Logging</span></span>

<span data-ttu-id="e8770-490">Os clientes criado pelo `IHttpClientFactory` registram mensagens de log para todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="e8770-490">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="e8770-491">Habilite o nível apropriado de informações na configuração de log para ver as mensagens de log padrão.</span><span class="sxs-lookup"><span data-stu-id="e8770-491">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="e8770-492">Os registros em log adicionais, como o registro em log dos cabeçalhos de solicitação, estão incluídos somente no nível de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="e8770-492">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="e8770-493">A categoria de log usada para cada cliente inclui o nome do cliente.</span><span class="sxs-lookup"><span data-stu-id="e8770-493">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="e8770-494">Um cliente chamado *MyNamedClient,* por exemplo, registra `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`mensagens com uma categoria de .</span><span class="sxs-lookup"><span data-stu-id="e8770-494">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="e8770-495">Mensagens sufixadas com *LogicalHandler* ocorrem fora do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="e8770-495">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="e8770-496">Na solicitação, as mensagens são registradas em log antes que qualquer outro manipulador no pipeline a tenha processado.</span><span class="sxs-lookup"><span data-stu-id="e8770-496">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="e8770-497">Na resposta, as mensagens são registradas depois que qualquer outro manipulador do pipeline tenha recebido a resposta.</span><span class="sxs-lookup"><span data-stu-id="e8770-497">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="e8770-498">O registro em log também ocorre dentro do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="e8770-498">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="e8770-499">No caso do exemplo de *MyNamedClient*, essas mensagens são registradas em log na categoria de log `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="e8770-499">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="e8770-500">Para a solicitação, isso ocorre depois que todos os outros manipuladores são executados e imediatamente antes que a solicitação seja enviada pela rede.</span><span class="sxs-lookup"><span data-stu-id="e8770-500">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="e8770-501">Na resposta, esse registro em log inclui o estado da resposta antes que ela seja retornada por meio do pipeline do manipulador.</span><span class="sxs-lookup"><span data-stu-id="e8770-501">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="e8770-502">Habilitar o registro em log dentro e fora do pipeline permite a inspeção das alterações feitas por outros manipuladores do pipeline.</span><span class="sxs-lookup"><span data-stu-id="e8770-502">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="e8770-503">Isso pode incluir, por exemplo, alterações nos cabeçalhos de solicitação ou no código de status da resposta.</span><span class="sxs-lookup"><span data-stu-id="e8770-503">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="e8770-504">Incluir o nome do cliente na categoria de log permite a filtragem de log para clientes nomeados específicos, quando necessário.</span><span class="sxs-lookup"><span data-stu-id="e8770-504">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="e8770-505">Configurar o HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="e8770-505">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="e8770-506">Talvez seja necessário controlar a configuração do `HttpMessageHandler` interno usado por um cliente.</span><span class="sxs-lookup"><span data-stu-id="e8770-506">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="e8770-507">Um `IHttpClientBuilder` é retornado ao adicionar clientes nomeados ou com tipo.</span><span class="sxs-lookup"><span data-stu-id="e8770-507">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="e8770-508">O método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pode ser usado para definir um representante.</span><span class="sxs-lookup"><span data-stu-id="e8770-508">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="e8770-509">O representante que é usado para criar e configurar o `HttpMessageHandler` primário usado pelo cliente:</span><span class="sxs-lookup"><span data-stu-id="e8770-509">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="e8770-510">Usar IHttpClientFactory em um aplicativo de console</span><span class="sxs-lookup"><span data-stu-id="e8770-510">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="e8770-511">Em um aplicativo de console, adicione as seguintes referências de pacote ao projeto:</span><span class="sxs-lookup"><span data-stu-id="e8770-511">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="e8770-512">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="e8770-512">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="e8770-513">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="e8770-513">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="e8770-514">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="e8770-514">In the following example:</span></span>

* <span data-ttu-id="e8770-515"><xref:System.Net.Http.IHttpClientFactory> é registrado no contêiner de serviço do [Host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="e8770-515"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="e8770-516">`MyService` cria uma instância de fábrica do cliente do serviço, que é usada para criar um `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e8770-516">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="e8770-517">`HttpClient` é usado para recuperar uma página da Web.</span><span class="sxs-lookup"><span data-stu-id="e8770-517">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="e8770-518">`Main` cria um escopo para executar o método `GetPage` do serviço e gravar os primeiros 500 caracteres do conteúdo da página da Web no console.</span><span class="sxs-lookup"><span data-stu-id="e8770-518">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="e8770-519">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e8770-519">Additional resources</span></span>

* [<span data-ttu-id="e8770-520">Usar HttpClientFactory implementar solicitações HTTP resilientes</span><span class="sxs-lookup"><span data-stu-id="e8770-520">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="e8770-521">Implementar repetições de chamadas HTTP com retirada exponencial com o HttpClientFactory e políticas da Polly</span><span class="sxs-lookup"><span data-stu-id="e8770-521">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="e8770-522">Implementar o padrão de disjuntor</span><span class="sxs-lookup"><span data-stu-id="e8770-522">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="e8770-523">Por [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) e [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="e8770-523">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="e8770-524">É possível registrar e usar um <xref:System.Net.Http.IHttpClientFactory> para configurar e criar instâncias de <xref:System.Net.Http.HttpClient> em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e8770-524">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="e8770-525">Ele oferece os seguintes benefícios:</span><span class="sxs-lookup"><span data-stu-id="e8770-525">It offers the following benefits:</span></span>

* <span data-ttu-id="e8770-526">Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e8770-526">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="e8770-527">Por exemplo, um cliente do *github* pode ser registrado e configurado para acessar o [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="e8770-527">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="e8770-528">Um cliente padrão pode ser registrado para outras finalidades.</span><span class="sxs-lookup"><span data-stu-id="e8770-528">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="e8770-529">Codifica o conceito de middleware de saída por meio da delegação de manipuladores no `HttpClient` e fornece extensões para que o middleware baseado no Polly possa aproveitar esse recurso.</span><span class="sxs-lookup"><span data-stu-id="e8770-529">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="e8770-530">Gerencia o pooling e o tempo de vida das instâncias de `HttpClientMessageHandler` subjacentes para evitar problemas de DNS comuns que ocorrem no gerenciamento manual de tempos de vida de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e8770-530">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="e8770-531">Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.</span><span class="sxs-lookup"><span data-stu-id="e8770-531">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="e8770-532">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e8770-532">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e8770-533">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="e8770-533">Prerequisites</span></span>

<span data-ttu-id="e8770-534">Os projetos direcionados ao .NET Framework exigem a instalação do pacote do NuGet [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/).</span><span class="sxs-lookup"><span data-stu-id="e8770-534">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="e8770-535">Os projetos destinados ao .Net Core e a referência ao [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) já incluem o pacote `Microsoft.Extensions.Http`.</span><span class="sxs-lookup"><span data-stu-id="e8770-535">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="e8770-536">Padrões de consumo</span><span class="sxs-lookup"><span data-stu-id="e8770-536">Consumption patterns</span></span>

<span data-ttu-id="e8770-537">Há várias maneiras de usar o `IHttpClientFactory` em um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e8770-537">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="e8770-538">Uso básico</span><span class="sxs-lookup"><span data-stu-id="e8770-538">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="e8770-539">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="e8770-539">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="e8770-540">Clientes digitados</span><span class="sxs-lookup"><span data-stu-id="e8770-540">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="e8770-541">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="e8770-541">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="e8770-542">Nenhum deles é estritamente superiores ao outro.</span><span class="sxs-lookup"><span data-stu-id="e8770-542">None of them are strictly superior to another.</span></span> <span data-ttu-id="e8770-543">A melhor abordagem depende das restrições do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e8770-543">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="e8770-544">Uso básico</span><span class="sxs-lookup"><span data-stu-id="e8770-544">Basic usage</span></span>

<span data-ttu-id="e8770-545">O `IHttpClientFactory` pode ser registrado chamando o método de extensão `AddHttpClient` em `IServiceCollection`, dentro do método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e8770-545">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="e8770-546">Depois de registrado, o código pode aceitar um `IHttpClientFactory` em qualquer lugar em que os serviços possam ser injetados com [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e8770-546">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e8770-547">O `IHttpClientFactory` pode ser usado `HttpClient` para criar uma instância:</span><span class="sxs-lookup"><span data-stu-id="e8770-547">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="e8770-548">Usar o `IHttpClientFactory` dessa forma é uma ótima maneira de refatorar um aplicativo existente.</span><span class="sxs-lookup"><span data-stu-id="e8770-548">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="e8770-549">Não há nenhum impacto na maneira em que o `HttpClient` é usado.</span><span class="sxs-lookup"><span data-stu-id="e8770-549">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="e8770-550">Nos locais em que as instâncias de `HttpClient` são criadas no momento, substitua essas ocorrências por uma chamada a <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="e8770-550">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="e8770-551">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="e8770-551">Named clients</span></span>

<span data-ttu-id="e8770-552">Quando um aplicativo exige vários usos distintos do `HttpClient`, cada um com uma configuração diferente, uma opção é usar **clientes nomeados**.</span><span class="sxs-lookup"><span data-stu-id="e8770-552">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="e8770-553">A configuração de um `HttpClient` nomeado pode ser especificada durante o registro em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e8770-553">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="e8770-554">No código anterior, `AddHttpClient` é chamado, fornecendo o nome *github*.</span><span class="sxs-lookup"><span data-stu-id="e8770-554">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="e8770-555">Esse cliente tem algumas configurações padrão aplicadas, ou seja, o endereço básico e dois cabeçalhos necessários para trabalhar com a API do GitHub.</span><span class="sxs-lookup"><span data-stu-id="e8770-555">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="e8770-556">Toda vez que o `CreateClient` é chamado, uma nova instância de `HttpClient` é criada e a ação de configuração é chamada.</span><span class="sxs-lookup"><span data-stu-id="e8770-556">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="e8770-557">Para consumir um cliente nomeado, um parâmetro de cadeia de caracteres pode ser passado para `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="e8770-557">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="e8770-558">Especifique o nome do cliente a ser criado:</span><span class="sxs-lookup"><span data-stu-id="e8770-558">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="e8770-559">No código anterior, a solicitação não precisa especificar um nome do host.</span><span class="sxs-lookup"><span data-stu-id="e8770-559">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="e8770-560">Ela pode passar apenas o caminho, pois o endereço básico configurado para o cliente é usado.</span><span class="sxs-lookup"><span data-stu-id="e8770-560">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="e8770-561">Clientes com tipo</span><span class="sxs-lookup"><span data-stu-id="e8770-561">Typed clients</span></span>

<span data-ttu-id="e8770-562">Clientes com tipo:</span><span class="sxs-lookup"><span data-stu-id="e8770-562">Typed clients:</span></span>

* <span data-ttu-id="e8770-563">Fornecem as mesmas funcionalidade que os clientes nomeados sem a necessidade de usar cadeias de caracteres como chaves.</span><span class="sxs-lookup"><span data-stu-id="e8770-563">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="e8770-564">Fornecem a ajuda do IntelliSense e do compilador durante o consumo de clientes.</span><span class="sxs-lookup"><span data-stu-id="e8770-564">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="e8770-565">Fornecem um único local para configurar e interagir com um determinado `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e8770-565">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="e8770-566">Por exemplo, um único cliente com tipo pode ser usado para um único ponto de extremidade de back-end e encapsular toda a lógica que lida com esse ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="e8770-566">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="e8770-567">Funcionam com a DI e podem ser injetados no local necessário no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e8770-567">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="e8770-568">Um cliente digitado `HttpClient` aceita um parâmetro em seu construtor:</span><span class="sxs-lookup"><span data-stu-id="e8770-568">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="e8770-569">No código anterior, a configuração é movida para o cliente com tipo.</span><span class="sxs-lookup"><span data-stu-id="e8770-569">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="e8770-570">O objeto `HttpClient` é exposto como uma propriedade pública.</span><span class="sxs-lookup"><span data-stu-id="e8770-570">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="e8770-571">É possível definir métodos específicos da API que expõem a funcionalidade `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e8770-571">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="e8770-572">O método `GetAspNetDocsIssues` encapsula o código necessário para consultar e analisar os últimos problemas em aberto de um repositório GitHub.</span><span class="sxs-lookup"><span data-stu-id="e8770-572">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="e8770-573">Para registrar um cliente com tipo, o método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> genérico pode ser usado em `Startup.ConfigureServices`, especificando a classe do cliente com tipo:</span><span class="sxs-lookup"><span data-stu-id="e8770-573">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="e8770-574">O cliente com tipo é registrado como transitório com a DI.</span><span class="sxs-lookup"><span data-stu-id="e8770-574">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="e8770-575">O cliente com tipo pode ser injetado e consumido diretamente:</span><span class="sxs-lookup"><span data-stu-id="e8770-575">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="e8770-576">Se preferir, a configuração de um cliente com tipo poderá ser especificada durante o registro em `Startup.ConfigureServices` e não no construtor do cliente com tipo:</span><span class="sxs-lookup"><span data-stu-id="e8770-576">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="e8770-577">É possível encapsular totalmente o `HttpClient` dentro de um cliente com tipo.</span><span class="sxs-lookup"><span data-stu-id="e8770-577">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="e8770-578">Em vez de o expor como uma propriedade, é possível fornecer métodos públicos que chamam a instância de `HttpClient` internamente.</span><span class="sxs-lookup"><span data-stu-id="e8770-578">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="e8770-579">No código anterior, o `HttpClient` é armazenado como um campo privado.</span><span class="sxs-lookup"><span data-stu-id="e8770-579">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="e8770-580">Todo o acesso para fazer chamadas externas passa pelo método `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="e8770-580">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="e8770-581">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="e8770-581">Generated clients</span></span>

<span data-ttu-id="e8770-582">O `IHttpClientFactory` pode ser usado em combinação com outras bibliotecas de terceiros, como a [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="e8770-582">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="e8770-583">Refit é uma biblioteca REST para .NET.</span><span class="sxs-lookup"><span data-stu-id="e8770-583">Refit is a REST library for .NET.</span></span> <span data-ttu-id="e8770-584">Ela converte APIs REST em interfaces dinâmicas.</span><span class="sxs-lookup"><span data-stu-id="e8770-584">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="e8770-585">Uma implementação da interface é gerada dinamicamente pelo `RestService` usando `HttpClient` para fazer as chamadas de HTTP externas.</span><span class="sxs-lookup"><span data-stu-id="e8770-585">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="e8770-586">Uma interface e uma resposta são definidas para representar a API externa e sua resposta:</span><span class="sxs-lookup"><span data-stu-id="e8770-586">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="e8770-587">Um cliente com tipo pode ser adicionado usando a Refit para gerar a implementação:</span><span class="sxs-lookup"><span data-stu-id="e8770-587">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="e8770-588">A interface definida pode ser consumida, quando necessário, com a implementação fornecida pela DI e pela Refit:</span><span class="sxs-lookup"><span data-stu-id="e8770-588">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="e8770-589">Middleware de solicitação de saída</span><span class="sxs-lookup"><span data-stu-id="e8770-589">Outgoing request middleware</span></span>

<span data-ttu-id="e8770-590">O `HttpClient` já tem o conceito de delegar manipuladores que podem ser vinculados para solicitações HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="e8770-590">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="e8770-591">O `IHttpClientFactory` facilita a definição dos manipuladores a serem aplicados a cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="e8770-591">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="e8770-592">Ele permite o registro e o encadeamento de vários manipuladores para criar um pipeline do middleware de solicitação saída.</span><span class="sxs-lookup"><span data-stu-id="e8770-592">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="e8770-593">Cada um desses manipuladores é capaz de executar o trabalho antes e após a solicitação de saída.</span><span class="sxs-lookup"><span data-stu-id="e8770-593">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="e8770-594">Esse padrão é semelhante ao pipeline do middleware de entrada no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e8770-594">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="e8770-595">O padrão fornece um mecanismo para gerenciar interesses paralelos em relação às solicitações HTTP, incluindo o armazenamento em cache, o tratamento de erro, a serialização e o registro em log.</span><span class="sxs-lookup"><span data-stu-id="e8770-595">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="e8770-596">Para criar um manipulador, defina uma classe derivando-a de <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="e8770-596">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="e8770-597">Substitua o método `SendAsync` para executar o código antes de passar a solicitação para o próximo manipulador no pipeline:</span><span class="sxs-lookup"><span data-stu-id="e8770-597">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="e8770-598">O código anterior define um manipulador básico.</span><span class="sxs-lookup"><span data-stu-id="e8770-598">The preceding code defines a basic handler.</span></span> <span data-ttu-id="e8770-599">Ele verifica se um cabeçalho `X-API-KEY` foi incluído na solicitação.</span><span class="sxs-lookup"><span data-stu-id="e8770-599">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="e8770-600">Se o cabeçalho estiver ausente, isso poderá evitar a chamada de HTTP e retornar uma resposta adequada.</span><span class="sxs-lookup"><span data-stu-id="e8770-600">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="e8770-601">Durante o registro, um ou mais manipuladores `HttpClient`podem ser adicionados à configuração de um .</span><span class="sxs-lookup"><span data-stu-id="e8770-601">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="e8770-602">Essa tarefa é realizada por meio de métodos de extensão no <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e8770-602">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="e8770-603">No código anterior, o `ValidateHeaderHandler` é registrado com a DI.</span><span class="sxs-lookup"><span data-stu-id="e8770-603">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="e8770-604">O manipulador **deve** ser registrado na injeção de dependência como serviço temporário, mas nunca com escopo.</span><span class="sxs-lookup"><span data-stu-id="e8770-604">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="e8770-605">Se o manipulador estiver registrado como um serviço com escopo e algum serviço do qual ele depende for descartável:</span><span class="sxs-lookup"><span data-stu-id="e8770-605">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="e8770-606">Os serviços do manipulador poderão ser descartados antes que ele saia do escopo.</span><span class="sxs-lookup"><span data-stu-id="e8770-606">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="e8770-607">Os serviços de manipulador descartados farão com que o manipulador falhe.</span><span class="sxs-lookup"><span data-stu-id="e8770-607">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="e8770-608">Depois de registrado, o <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> poderá ser chamado, passando no tipo do manipulador.</span><span class="sxs-lookup"><span data-stu-id="e8770-608">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="e8770-609">Vários manipuladores podem ser registrados na ordem em que eles devem ser executados.</span><span class="sxs-lookup"><span data-stu-id="e8770-609">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="e8770-610">Cada manipulador encapsula o próximo manipulador até que o `HttpClientHandler` final execute a solicitação:</span><span class="sxs-lookup"><span data-stu-id="e8770-610">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="e8770-611">Use uma das seguintes abordagens para compartilhar o estado por solicitação com os manipuladores de mensagens:</span><span class="sxs-lookup"><span data-stu-id="e8770-611">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="e8770-612">Passe os dados pelo manipulador usando `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="e8770-612">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="e8770-613">Use `IHttpContextAccessor` para acessar a solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="e8770-613">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="e8770-614">Crie um objeto de armazenamento `AsyncLocal` personalizado para passar os dados.</span><span class="sxs-lookup"><span data-stu-id="e8770-614">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="e8770-615">Usar manipuladores baseados no Polly</span><span class="sxs-lookup"><span data-stu-id="e8770-615">Use Polly-based handlers</span></span>

<span data-ttu-id="e8770-616">O `IHttpClientFactory` integra-se a uma biblioteca de terceiros popular chamada [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="e8770-616">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="e8770-617">O Polly é uma biblioteca abrangente de tratamento de falha transitória e de resiliência para .NET.</span><span class="sxs-lookup"><span data-stu-id="e8770-617">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="e8770-618">Ela permite que os desenvolvedores expressem políticas, como Repetição, Disjuntor, Tempo Limite, Isolamento de Bulkhead e Fallback de maneira fluente e thread-safe.</span><span class="sxs-lookup"><span data-stu-id="e8770-618">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="e8770-619">Os métodos de extensão são fornecidos para habilitar o uso de políticas do Polly com instâncias de `HttpClient` configuradas.</span><span class="sxs-lookup"><span data-stu-id="e8770-619">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="e8770-620">As extensões do Polly:</span><span class="sxs-lookup"><span data-stu-id="e8770-620">The Polly extensions:</span></span>

* <span data-ttu-id="e8770-621">Dão suporte à adição de manipuladores baseados no Polly aos clientes.</span><span class="sxs-lookup"><span data-stu-id="e8770-621">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="e8770-622">Podem ser usadas depois de instalar o pacote do NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="e8770-622">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="e8770-623">O pacote não está incluído na estrutura ASP.NET Core compartilhada.</span><span class="sxs-lookup"><span data-stu-id="e8770-623">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="e8770-624">Tratar falhas transitórias</span><span class="sxs-lookup"><span data-stu-id="e8770-624">Handle transient faults</span></span>

<span data-ttu-id="e8770-625">As falhas mais comuns ocorrem quando as chamadas HTTP externas são transitórias.</span><span class="sxs-lookup"><span data-stu-id="e8770-625">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="e8770-626">Um método de extensão conveniente chamado `AddTransientHttpErrorPolicy` é incluído para permitir que uma política seja definido para tratar erros transitórios.</span><span class="sxs-lookup"><span data-stu-id="e8770-626">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="e8770-627">As políticas configuradas com esse método de extensão tratam `HttpRequestException`, respostas HTTP 5xx e respostas HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="e8770-627">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="e8770-628">A extensão `AddTransientHttpErrorPolicy` pode ser usada em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e8770-628">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e8770-629">A extensão fornece acesso a um objeto `PolicyBuilder` configurado para tratar erros que representam uma possível falha transitória:</span><span class="sxs-lookup"><span data-stu-id="e8770-629">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="e8770-630">No código anterior, uma política `WaitAndRetryAsync` é definida.</span><span class="sxs-lookup"><span data-stu-id="e8770-630">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="e8770-631">As solicitações com falha são repetidas até três vezes com um atraso de 600 ms entre as tentativas.</span><span class="sxs-lookup"><span data-stu-id="e8770-631">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="e8770-632">Selecionar políticas dinamicamente</span><span class="sxs-lookup"><span data-stu-id="e8770-632">Dynamically select policies</span></span>

<span data-ttu-id="e8770-633">Existem métodos de extensão adicionais que podem ser usados para adicionar manipuladores baseados no Polly.</span><span class="sxs-lookup"><span data-stu-id="e8770-633">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="e8770-634">Uma dessas extensões é a `AddPolicyHandler`, que tem várias sobrecargas.</span><span class="sxs-lookup"><span data-stu-id="e8770-634">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="e8770-635">Uma sobrecarga permite que a solicitação seja inspecionada durante a definição da política a ser aplicada:</span><span class="sxs-lookup"><span data-stu-id="e8770-635">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="e8770-636">No código anterior, se a solicitação de saída é um HTTP GET, um tempo limite de 10 segundos é aplicado.</span><span class="sxs-lookup"><span data-stu-id="e8770-636">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="e8770-637">Para qualquer outro método HTTP, é usado um tempo limite de 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="e8770-637">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="e8770-638">Adicionar vários manipuladores do Polly</span><span class="sxs-lookup"><span data-stu-id="e8770-638">Add multiple Polly handlers</span></span>

<span data-ttu-id="e8770-639">É comum aninhar políticas do Polly para fornecer funcionalidade avançada:</span><span class="sxs-lookup"><span data-stu-id="e8770-639">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="e8770-640">No exemplo anterior, dois manipuladores são adicionados.</span><span class="sxs-lookup"><span data-stu-id="e8770-640">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="e8770-641">O primeiro usa a extensão `AddTransientHttpErrorPolicy` para adicionar uma política de repetição.</span><span class="sxs-lookup"><span data-stu-id="e8770-641">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="e8770-642">As solicitações com falha são repetidas até três vezes.</span><span class="sxs-lookup"><span data-stu-id="e8770-642">Failed requests are retried up to three times.</span></span> <span data-ttu-id="e8770-643">A segunda chamada a `AddTransientHttpErrorPolicy` adiciona uma política de disjuntor.</span><span class="sxs-lookup"><span data-stu-id="e8770-643">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="e8770-644">As solicitações externas adicionais são bloqueadas por 30 segundos quando ocorrem cinco tentativas com falha consecutivamente.</span><span class="sxs-lookup"><span data-stu-id="e8770-644">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="e8770-645">As políticas de disjuntor são políticas com estado.</span><span class="sxs-lookup"><span data-stu-id="e8770-645">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="e8770-646">Todas as chamadas por meio desse cliente compartilham o mesmo estado do circuito.</span><span class="sxs-lookup"><span data-stu-id="e8770-646">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="e8770-647">Adicionar políticas do registro do Polly</span><span class="sxs-lookup"><span data-stu-id="e8770-647">Add policies from the Polly registry</span></span>

<span data-ttu-id="e8770-648">Uma abordagem para gerenciar as políticas usadas com frequência é defini-las uma única vez e registrá-las em um `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="e8770-648">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="e8770-649">É fornecido um método de extensão que permite que um manipulador seja adicionado usando uma política do registro:</span><span class="sxs-lookup"><span data-stu-id="e8770-649">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="e8770-650">No código anterior, duas políticas são registradas quando `PolicyRegistry` é adicionado ao `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="e8770-650">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="e8770-651">Para usar uma política do registro, o método `AddPolicyHandlerFromRegistry` é usado, passando o nome da política a ser aplicada.</span><span class="sxs-lookup"><span data-stu-id="e8770-651">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="e8770-652">Mais informações sobre o `IHttpClientFactory` e as integrações do Polly podem ser encontradas no [Wiki do Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="e8770-652">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="e8770-653">HttpClient e gerenciamento de tempo de vida</span><span class="sxs-lookup"><span data-stu-id="e8770-653">HttpClient and lifetime management</span></span>

<span data-ttu-id="e8770-654">Uma nova instância de `HttpClient` é chamada, sempre que `CreateClient` é chamado na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="e8770-654">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="e8770-655">Há um <xref:System.Net.Http.HttpMessageHandler> por cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="e8770-655">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="e8770-656">O alocador gerencia a vida útil das instâncias do `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="e8770-656">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="e8770-657">`IHttpClientFactory` cria pools com as instâncias de `HttpMessageHandler` criadas pelo alocador para reduzir o consumo de recursos.</span><span class="sxs-lookup"><span data-stu-id="e8770-657">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="e8770-658">Uma instância de `HttpMessageHandler` poderá ser reutilizada no pool, ao criar uma nova instância de `HttpClient`, se o respectivo tempo de vida não tiver expirado.</span><span class="sxs-lookup"><span data-stu-id="e8770-658">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="e8770-659">O pooling de manipuladores é preferível porque normalmente cada manipulador gerencia as próprias conexões HTTP subjacentes.</span><span class="sxs-lookup"><span data-stu-id="e8770-659">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="e8770-660">Criar mais manipuladores do que o necessário pode resultar em atrasos de conexão.</span><span class="sxs-lookup"><span data-stu-id="e8770-660">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="e8770-661">Alguns manipuladores também mantêm as conexões abertas indefinidamente, o que pode impedir que o manipulador reaja a alterações de DNS.</span><span class="sxs-lookup"><span data-stu-id="e8770-661">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="e8770-662">O tempo de vida padrão do manipulador é de 2 minutos.</span><span class="sxs-lookup"><span data-stu-id="e8770-662">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="e8770-663">O valor padrão pode ser substituído para cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="e8770-663">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="e8770-664">Para substituí-lo, chame <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> no `IHttpClientBuilder` que é retornado ao criar o cliente:</span><span class="sxs-lookup"><span data-stu-id="e8770-664">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="e8770-665">Não é necessário descartar o cliente.</span><span class="sxs-lookup"><span data-stu-id="e8770-665">Disposal of the client isn't required.</span></span> <span data-ttu-id="e8770-666">O descarte cancela as solicitações de saída e garante que a determinada instância de `HttpClient` não seja usada depois de chamar <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="e8770-666">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="e8770-667">`IHttpClientFactory` rastreia e descarta recursos usados pelas instâncias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e8770-667">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="e8770-668">Geralmente, as instâncias de `HttpClient` podem ser tratadas como objetos do .NET e não exigem descarte.</span><span class="sxs-lookup"><span data-stu-id="e8770-668">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="e8770-669">Manter uma única instância de `HttpClient` ativa por uma longa duração é um padrão comum usado, antes do início de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="e8770-669">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="e8770-670">Esse padrão se torna desnecessário após a migração para `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="e8770-670">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="e8770-671">Alternativas para IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="e8770-671">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="e8770-672">O `IHttpClientFactory` uso em um aplicativo habilitado para DI evita:</span><span class="sxs-lookup"><span data-stu-id="e8770-672">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="e8770-673">Problemas de exaustão de recursos agrupando `HttpMessageHandler` instâncias.</span><span class="sxs-lookup"><span data-stu-id="e8770-673">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="e8770-674">Problemas de DNS `HttpMessageHandler` obsoletos por instâncias de ciclismo em intervalos regulares.</span><span class="sxs-lookup"><span data-stu-id="e8770-674">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="e8770-675">Existem maneiras alternativas de resolver os <xref:System.Net.Http.SocketsHttpHandler> problemas precedentes usando uma instância de longa duração.</span><span class="sxs-lookup"><span data-stu-id="e8770-675">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="e8770-676">Crie uma `SocketsHttpHandler` instância de quando o aplicativo começa e use-o para a vida útil do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e8770-676">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="e8770-677">Configure-se <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> a um valor apropriado com base nos tempos de atualização do DNS.</span><span class="sxs-lookup"><span data-stu-id="e8770-677">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="e8770-678">Crie `HttpClient` instâncias usando `new HttpClient(handler, disposeHandler: false)` conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="e8770-678">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="e8770-679">As abordagens anteriores resolvem `IHttpClientFactory` os problemas de gestão de recursos que resolvem de forma semelhante.</span><span class="sxs-lookup"><span data-stu-id="e8770-679">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="e8770-680">As `SocketsHttpHandler` conexões `HttpClient` de compartilhamento entre instâncias.</span><span class="sxs-lookup"><span data-stu-id="e8770-680">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="e8770-681">Esse compartilhamento evita a exaustão do soquete.</span><span class="sxs-lookup"><span data-stu-id="e8770-681">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="e8770-682">Os `SocketsHttpHandler` ciclos se `PooledConnectionLifetime` conexões de acordo com para evitar problemas de DNS obsoletos.</span><span class="sxs-lookup"><span data-stu-id="e8770-682">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="e8770-683">Cookies</span><span class="sxs-lookup"><span data-stu-id="e8770-683">Cookies</span></span>

<span data-ttu-id="e8770-684">As instâncias agrupadas resultam `HttpMessageHandler` em `CookieContainer` objetos sendo compartilhados.</span><span class="sxs-lookup"><span data-stu-id="e8770-684">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="e8770-685">O `CookieContainer` compartilhamento de objetos não previsto muitas vezes resulta em código incorreto.</span><span class="sxs-lookup"><span data-stu-id="e8770-685">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="e8770-686">Para aplicativos que exigem cookies, considere:</span><span class="sxs-lookup"><span data-stu-id="e8770-686">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="e8770-687">Desabilitando o manuseio automático de cookies</span><span class="sxs-lookup"><span data-stu-id="e8770-687">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="e8770-688">Evitando`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="e8770-688">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="e8770-689">Chamada <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para desativar o manuseio automático de cookies:</span><span class="sxs-lookup"><span data-stu-id="e8770-689">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="e8770-690">Registro em log</span><span class="sxs-lookup"><span data-stu-id="e8770-690">Logging</span></span>

<span data-ttu-id="e8770-691">Os clientes criado pelo `IHttpClientFactory` registram mensagens de log para todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="e8770-691">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="e8770-692">Habilite o nível apropriado de informações na configuração de log para ver as mensagens de log padrão.</span><span class="sxs-lookup"><span data-stu-id="e8770-692">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="e8770-693">Os registros em log adicionais, como o registro em log dos cabeçalhos de solicitação, estão incluídos somente no nível de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="e8770-693">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="e8770-694">A categoria de log usada para cada cliente inclui o nome do cliente.</span><span class="sxs-lookup"><span data-stu-id="e8770-694">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="e8770-695">Um cliente chamado *MyNamedClient,* por exemplo, registra `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`mensagens com uma categoria de .</span><span class="sxs-lookup"><span data-stu-id="e8770-695">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="e8770-696">Mensagens sufixadas com *LogicalHandler* ocorrem fora do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="e8770-696">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="e8770-697">Na solicitação, as mensagens são registradas em log antes que qualquer outro manipulador no pipeline a tenha processado.</span><span class="sxs-lookup"><span data-stu-id="e8770-697">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="e8770-698">Na resposta, as mensagens são registradas depois que qualquer outro manipulador do pipeline tenha recebido a resposta.</span><span class="sxs-lookup"><span data-stu-id="e8770-698">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="e8770-699">O registro em log também ocorre dentro do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="e8770-699">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="e8770-700">No caso do exemplo de *MyNamedClient*, essas mensagens são registradas em log na categoria de log `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="e8770-700">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="e8770-701">Para a solicitação, isso ocorre depois que todos os outros manipuladores são executados e imediatamente antes que a solicitação seja enviada pela rede.</span><span class="sxs-lookup"><span data-stu-id="e8770-701">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="e8770-702">Na resposta, esse registro em log inclui o estado da resposta antes que ela seja retornada por meio do pipeline do manipulador.</span><span class="sxs-lookup"><span data-stu-id="e8770-702">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="e8770-703">Habilitar o registro em log dentro e fora do pipeline permite a inspeção das alterações feitas por outros manipuladores do pipeline.</span><span class="sxs-lookup"><span data-stu-id="e8770-703">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="e8770-704">Isso pode incluir, por exemplo, alterações nos cabeçalhos de solicitação ou no código de status da resposta.</span><span class="sxs-lookup"><span data-stu-id="e8770-704">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="e8770-705">Incluir o nome do cliente na categoria de log permite a filtragem de log para clientes nomeados específicos, quando necessário.</span><span class="sxs-lookup"><span data-stu-id="e8770-705">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="e8770-706">Configurar o HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="e8770-706">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="e8770-707">Talvez seja necessário controlar a configuração do `HttpMessageHandler` interno usado por um cliente.</span><span class="sxs-lookup"><span data-stu-id="e8770-707">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="e8770-708">Um `IHttpClientBuilder` é retornado ao adicionar clientes nomeados ou com tipo.</span><span class="sxs-lookup"><span data-stu-id="e8770-708">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="e8770-709">O método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pode ser usado para definir um representante.</span><span class="sxs-lookup"><span data-stu-id="e8770-709">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="e8770-710">O representante que é usado para criar e configurar o `HttpMessageHandler` primário usado pelo cliente:</span><span class="sxs-lookup"><span data-stu-id="e8770-710">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="e8770-711">Usar IHttpClientFactory em um aplicativo de console</span><span class="sxs-lookup"><span data-stu-id="e8770-711">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="e8770-712">Em um aplicativo de console, adicione as seguintes referências de pacote ao projeto:</span><span class="sxs-lookup"><span data-stu-id="e8770-712">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="e8770-713">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="e8770-713">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="e8770-714">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="e8770-714">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="e8770-715">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="e8770-715">In the following example:</span></span>

* <span data-ttu-id="e8770-716"><xref:System.Net.Http.IHttpClientFactory> é registrado no contêiner de serviço do [Host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="e8770-716"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="e8770-717">`MyService` cria uma instância de fábrica do cliente do serviço, que é usada para criar um `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e8770-717">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="e8770-718">`HttpClient` é usado para recuperar uma página da Web.</span><span class="sxs-lookup"><span data-stu-id="e8770-718">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="e8770-719">`Main` cria um escopo para executar o método `GetPage` do serviço e gravar os primeiros 500 caracteres do conteúdo da página da Web no console.</span><span class="sxs-lookup"><span data-stu-id="e8770-719">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="e8770-720">Middleware de propagação de cabeçalho</span><span class="sxs-lookup"><span data-stu-id="e8770-720">Header propagation middleware</span></span>

<span data-ttu-id="e8770-721">A propagação de cabeçalho é um middleware suportado pela comunidade para propagar cabeçalhos HTTP da solicitação recebida para as solicitações http cliente de saída.</span><span class="sxs-lookup"><span data-stu-id="e8770-721">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="e8770-722">Para usar a propagação de cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="e8770-722">To use header propagation:</span></span>

* <span data-ttu-id="e8770-723">Consulte a porta suportada pela comunidade do pacote [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="e8770-723">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="e8770-724">ASP.NET Core 3.1 e posteriormente suporta [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="e8770-724">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="e8770-725">Configure o middleware e `HttpClient` em: `Startup`</span><span class="sxs-lookup"><span data-stu-id="e8770-725">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="e8770-726">O cliente inclui os cabeçalhos configurados nas solicitações de saída:</span><span class="sxs-lookup"><span data-stu-id="e8770-726">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="e8770-727">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e8770-727">Additional resources</span></span>

* [<span data-ttu-id="e8770-728">Usar HttpClientFactory implementar solicitações HTTP resilientes</span><span class="sxs-lookup"><span data-stu-id="e8770-728">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="e8770-729">Implementar repetições de chamadas HTTP com retirada exponencial com o HttpClientFactory e políticas da Polly</span><span class="sxs-lookup"><span data-stu-id="e8770-729">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="e8770-730">Implementar o padrão de disjuntor</span><span class="sxs-lookup"><span data-stu-id="e8770-730">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
