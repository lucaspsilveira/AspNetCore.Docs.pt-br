---
title: Testar ASP.NET Core middleware
author: tratcher
description: Saiba como testar ASP.NET Core middleware com o TestServer.
ms.author: riande
ms.custom: mvc
ms.date: 5/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/middleware
ms.openlocfilehash: ea7fc0e889ab32cbaf23257b3e866519af0727aa
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424533"
---
# <a name="test-aspnet-core-middleware"></a><span data-ttu-id="87bed-103">Testar ASP.NET Core middleware</span><span class="sxs-lookup"><span data-stu-id="87bed-103">Test ASP.NET Core middleware</span></span>

<span data-ttu-id="87bed-104">Por [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="87bed-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="87bed-105">O middleware pode ser testado isoladamente com o <xref:Microsoft.AspNetCore.TestHost.TestServer> .</span><span class="sxs-lookup"><span data-stu-id="87bed-105">Middleware can be tested in isolation with <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span> <span data-ttu-id="87bed-106">Ele permite que você:</span><span class="sxs-lookup"><span data-stu-id="87bed-106">It allows you to:</span></span>

* <span data-ttu-id="87bed-107">Crie uma instância de um pipeline de aplicativo contendo apenas os componentes que você precisa testar.</span><span class="sxs-lookup"><span data-stu-id="87bed-107">Instantiate an app pipeline containing only the components that you need to test.</span></span>
* <span data-ttu-id="87bed-108">Envie solicitações personalizadas para verificar o comportamento do middleware.</span><span class="sxs-lookup"><span data-stu-id="87bed-108">Send custom requests to verify middleware behavior.</span></span>

<span data-ttu-id="87bed-109">Vantagens:</span><span class="sxs-lookup"><span data-stu-id="87bed-109">Advantages:</span></span>

* <span data-ttu-id="87bed-110">As solicitações são enviadas na memória em vez de serem serializadas pela rede.</span><span class="sxs-lookup"><span data-stu-id="87bed-110">Requests are sent in-memory rather than being serialized over the network.</span></span>
* <span data-ttu-id="87bed-111">Isso evita preocupações adicionais, como o gerenciamento de porta e os certificados HTTPS.</span><span class="sxs-lookup"><span data-stu-id="87bed-111">This avoids additional concerns, such as port management and HTTPS certificates.</span></span>
* <span data-ttu-id="87bed-112">As exceções no middleware podem fluir diretamente de volta para o teste de chamada.</span><span class="sxs-lookup"><span data-stu-id="87bed-112">Exceptions in the middleware can flow directly back to the calling test.</span></span>
* <span data-ttu-id="87bed-113">É possível personalizar as estruturas de dados do servidor, como <xref:Microsoft.AspNetCore.Http.HttpContext> , diretamente no teste.</span><span class="sxs-lookup"><span data-stu-id="87bed-113">It's possible to customize server data structures, such as <xref:Microsoft.AspNetCore.Http.HttpContext>, directly in the test.</span></span>

## <a name="set-up-the-testserver"></a><span data-ttu-id="87bed-114">Configurar o TestServer</span><span class="sxs-lookup"><span data-stu-id="87bed-114">Set up the TestServer</span></span>

<span data-ttu-id="87bed-115">No projeto de teste, crie um teste:</span><span class="sxs-lookup"><span data-stu-id="87bed-115">In the test project, create a test:</span></span>

* <span data-ttu-id="87bed-116">Crie e inicie um host que usa o <xref:Microsoft.AspNetCore.TestHost.TestServer> .</span><span class="sxs-lookup"><span data-stu-id="87bed-116">Build and start a host that uses <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span>
* <span data-ttu-id="87bed-117">Adicione todos os serviços necessários que o middleware usa.</span><span class="sxs-lookup"><span data-stu-id="87bed-117">Add any required services that the middleware uses.</span></span>
* <span data-ttu-id="87bed-118">Configure o pipeline de processamento para usar o middleware para o teste.</span><span class="sxs-lookup"><span data-stu-id="87bed-118">Configure the processing pipeline to use the middleware for the test.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a><span data-ttu-id="87bed-119">Enviar solicitações com HttpClient</span><span class="sxs-lookup"><span data-stu-id="87bed-119">Send requests with HttpClient</span></span>
<span data-ttu-id="87bed-120">Enviar uma solicitação usando <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="87bed-120">Send a request using <xref:System.Net.Http.HttpClient>:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

<span data-ttu-id="87bed-121">Declare o resultado.</span><span class="sxs-lookup"><span data-stu-id="87bed-121">Assert the result.</span></span> <span data-ttu-id="87bed-122">Primeiro, faça uma asserção o oposto do resultado esperado.</span><span class="sxs-lookup"><span data-stu-id="87bed-122">First, make an assertion the opposite of the result that you expect.</span></span> <span data-ttu-id="87bed-123">Uma execução inicial com uma asserção falsa positiva confirma que o teste falha quando o middleware está sendo executado corretamente.</span><span class="sxs-lookup"><span data-stu-id="87bed-123">An initial run with a false positive assertion confirms that the test fails when the middleware is performing correctly.</span></span> <span data-ttu-id="87bed-124">Execute o teste e confirme se o teste falha.</span><span class="sxs-lookup"><span data-stu-id="87bed-124">Run the test and confirm that the test fails.</span></span>

<span data-ttu-id="87bed-125">No exemplo a seguir, o middleware deve retornar um código de status 404 (*não encontrado*) quando o ponto de extremidade raiz é solicitado.</span><span class="sxs-lookup"><span data-stu-id="87bed-125">In the following example, the middleware should return a 404 status code (*Not Found*) when the root endpoint is requested.</span></span> <span data-ttu-id="87bed-126">Faça a execução do primeiro teste com `Assert.NotEqual( ... );` , que deve falhar:</span><span class="sxs-lookup"><span data-stu-id="87bed-126">Make the first test run with `Assert.NotEqual( ... );`, which should fail:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

<span data-ttu-id="87bed-127">Altere a asserção para testar o middleware em condições normais de operação.</span><span class="sxs-lookup"><span data-stu-id="87bed-127">Change the assertion to test the middleware under normal operating conditions.</span></span> <span data-ttu-id="87bed-128">O teste final usa `Assert.Equal( ... );` .</span><span class="sxs-lookup"><span data-stu-id="87bed-128">The final test uses `Assert.Equal( ... );`.</span></span> <span data-ttu-id="87bed-129">Execute o teste novamente para confirmar que ele é aprovado.</span><span class="sxs-lookup"><span data-stu-id="87bed-129">Run the test again to confirm that it passes.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a><span data-ttu-id="87bed-130">Enviar solicitações com HttpContext</span><span class="sxs-lookup"><span data-stu-id="87bed-130">Send requests with HttpContext</span></span>

<span data-ttu-id="87bed-131">Um aplicativo de teste também pode enviar uma solicitação usando [SendAsync (ação \< HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span><span class="sxs-lookup"><span data-stu-id="87bed-131">A test app can also send a request using [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span></span> <span data-ttu-id="87bed-132">No exemplo a seguir, várias verificações são feitas quando `https://example.com/A/Path/?and=query` o é processado pelo middleware:</span><span class="sxs-lookup"><span data-stu-id="87bed-132">In the following example, several checks are made when `https://example.com/A/Path/?and=query` is processed by the middleware:</span></span>

```csharp
[Fact]
public async Task TestMiddleware_ExpectedResponse()
{
    using var host = await new HostBuilder()
        .ConfigureWebHost(webBuilder =>
        {
            webBuilder
                .UseTestServer()
                .ConfigureServices(services =>
                {
                    services.AddMyServices();
                })
                .Configure(app =>
                {
                    app.UseMiddleware<MyMiddleware>();
                });
        })
        .StartAsync();

    var server = host.GetTestServer();
    server.BaseAddress = new Uri("https://example.com/A/Path/");

    var context = await server.SendAsync(c =>
    {
        c.Request.Method = HttpMethods.Post;
        c.Request.Path = "/and/file.txt";
        c.Request.QueryString = new QueryString("?and=query");
    });

    Assert.True(context.RequestAborted.CanBeCanceled);
    Assert.Equal(HttpProtocol.Http11, context.Request.Protocol);
    Assert.Equal("POST", context.Request.Method);
    Assert.Equal("https", context.Request.Scheme);
    Assert.Equal("example.com", context.Request.Host.Value);
    Assert.Equal("/A/Path", context.Request.PathBase.Value);
    Assert.Equal("/and/file.txt", context.Request.Path.Value);
    Assert.Equal("?and=query", context.Request.QueryString.Value);
    Assert.NotNull(context.Request.Body);
    Assert.NotNull(context.Request.Headers);
    Assert.NotNull(context.Response.Headers);
    Assert.NotNull(context.Response.Body);
    Assert.Equal(404, context.Response.StatusCode);
    Assert.Null(context.Features.Get<IHttpResponseFeature>().ReasonPhrase);
}
```

<span data-ttu-id="87bed-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A>permite a configuração direta de um <xref:Microsoft.AspNetCore.Http.HttpContext> objeto em vez de usar as <xref:System.Net.Http.HttpClient> abstrações.</span><span class="sxs-lookup"><span data-stu-id="87bed-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> permits direct configuration of an <xref:Microsoft.AspNetCore.Http.HttpContext> object rather than using the <xref:System.Net.Http.HttpClient> abstractions.</span></span> <span data-ttu-id="87bed-134">Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> para manipular estruturas somente disponíveis no servidor, como [HttpContext. Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) ou [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span><span class="sxs-lookup"><span data-stu-id="87bed-134">Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> to manipulate structures only available on the server, such as [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) or [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span></span>

<span data-ttu-id="87bed-135">Assim como no exemplo anterior testado para uma resposta *404-não encontrada* , verifique o oposto de cada `Assert` instrução no teste anterior.</span><span class="sxs-lookup"><span data-stu-id="87bed-135">As with the earlier example that tested for a *404 - Not Found* response, check the opposite for each `Assert` statement in the preceding test.</span></span> <span data-ttu-id="87bed-136">A verificação confirma que o teste falha corretamente quando o middleware está funcionando normalmente.</span><span class="sxs-lookup"><span data-stu-id="87bed-136">The check confirms that the test fails correctly when the middleware is operating normally.</span></span> <span data-ttu-id="87bed-137">Depois de confirmar que o teste falso positivo funciona, defina as instruções finais `Assert` para as condições e os valores esperados do teste.</span><span class="sxs-lookup"><span data-stu-id="87bed-137">After you've confirmed that the false positive test works, set the final `Assert` statements for the expected conditions and values of the test.</span></span> <span data-ttu-id="87bed-138">Execute-o novamente para confirmar que o teste foi aprovado.</span><span class="sxs-lookup"><span data-stu-id="87bed-138">Run it again to confirm that the test passes.</span></span>

## <a name="testserver-limitations"></a><span data-ttu-id="87bed-139">Limitações do TestServer</span><span class="sxs-lookup"><span data-stu-id="87bed-139">TestServer limitations</span></span>

<span data-ttu-id="87bed-140">TestServer</span><span class="sxs-lookup"><span data-stu-id="87bed-140">TestServer:</span></span>

* <span data-ttu-id="87bed-141">Foi criado para replicar comportamentos de servidor para middleware de teste.</span><span class="sxs-lookup"><span data-stu-id="87bed-141">Was created to replicate server behaviors to test middleware.</span></span>
* <span data-ttu-id="87bed-142">***Não*** tenta replicar todos os <xref:System.Net.Http.HttpClient> comportamentos.</span><span class="sxs-lookup"><span data-stu-id="87bed-142">Does ***not*** try to replicate all <xref:System.Net.Http.HttpClient> behaviors.</span></span>
* <span data-ttu-id="87bed-143">O tenta conceder ao cliente acesso ao máximo de controle possível do servidor e com o máximo de visibilidade sobre o que está acontecendo no servidor o mais possível.</span><span class="sxs-lookup"><span data-stu-id="87bed-143">Attempts to give the client access to as much control over the server as possible, and with as much visibility into what's happening on the server as possible.</span></span> <span data-ttu-id="87bed-144">Por exemplo, ele pode lançar exceções normalmente não geradas pelo `HttpClient` para comunicar diretamente o estado do servidor.</span><span class="sxs-lookup"><span data-stu-id="87bed-144">For example it may throw exceptions not normally thrown by `HttpClient` in order to directly communicate server state.</span></span>
* <span data-ttu-id="87bed-145">Não define alguns cabeçalhos específicos de transporte por padrão, pois aqueles geralmente não são relevantes para o middleware.</span><span class="sxs-lookup"><span data-stu-id="87bed-145">Doesn't set some transport specific headers by default as those are not usually relevant to middleware.</span></span> <span data-ttu-id="87bed-146">Para obter mais informações, consulte a próxima seção.</span><span class="sxs-lookup"><span data-stu-id="87bed-146">For more information, see the next section.</span></span>

### <a name="content-length-and-transfer-encoding-headers"></a><span data-ttu-id="87bed-147">Cabeçalhos de comprimento de conteúdo e de codificação de transferência</span><span class="sxs-lookup"><span data-stu-id="87bed-147">Content-Length and Transfer-Encoding headers</span></span>

<span data-ttu-id="87bed-148">TestServer não ***define os*** cabeçalhos de solicitação ou resposta relacionados ao transporte, como o [comprimento do conteúdo](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) ou [a codificação de transferência](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding).</span><span class="sxs-lookup"><span data-stu-id="87bed-148">TestServer does ***not*** set transport related request or response headers such as [Content-Length](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) or [Transfer-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding).</span></span> <span data-ttu-id="87bed-149">Os aplicativos devem evitar dependendo desses cabeçalhos porque seu uso varia de acordo com o cliente, o cenário e o protocolo.</span><span class="sxs-lookup"><span data-stu-id="87bed-149">Applications should avoid depending on these headers because their usage varies by client, scenario, and protocol.</span></span> <span data-ttu-id="87bed-150">Se `Content-Length` e `Transfer-Encoding` forem necessários para testar um cenário específico, eles poderão ser especificados no teste ao compor o <xref:System.Net.Http.HttpRequestMessage> ou o <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="87bed-150">If `Content-Length` and `Transfer-Encoding` are necessary to test a specific scenario, they can be specified in the test when composing the <xref:System.Net.Http.HttpRequestMessage> or <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span> <span data-ttu-id="87bed-151">Para obter mais informações, consulte os seguintes problemas do GitHub:</span><span class="sxs-lookup"><span data-stu-id="87bed-151">For more information, see the following GitHub issues:</span></span>

* [<span data-ttu-id="87bed-152">dotnet/aspnetcore # 21677</span><span class="sxs-lookup"><span data-stu-id="87bed-152">dotnet/aspnetcore#21677</span></span>](https://github.com/dotnet/aspnetcore/issues/21677)
* [<span data-ttu-id="87bed-153">dotnet/aspnetcore # 18463</span><span class="sxs-lookup"><span data-stu-id="87bed-153">dotnet/aspnetcore#18463</span></span>](https://github.com/dotnet/aspnetcore/issues/18463)
* [<span data-ttu-id="87bed-154">dotnet/aspnetcore # 13273</span><span class="sxs-lookup"><span data-stu-id="87bed-154">dotnet/aspnetcore#13273</span></span>](https://github.com/dotnet/aspnetcore/issues/13273)
