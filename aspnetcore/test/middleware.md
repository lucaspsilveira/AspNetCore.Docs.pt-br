---
title: Testar ASP.NET Core middleware
author: tratcher
description: Saiba como testar ASP.NET Core middleware com o TestServer.
ms.author: riande
ms.custom: mvc
ms.date: 5/6/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/middleware
ms.openlocfilehash: 06ff7167e32fbd613c18709e31ecd078b3dfc926
ms.sourcegitcommit: 30fcf69556b6b6ec54a3879e280d5f61f018b48f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82876433"
---
# <a name="test-aspnet-core-middleware"></a><span data-ttu-id="c273f-103">Testar ASP.NET Core middleware</span><span class="sxs-lookup"><span data-stu-id="c273f-103">Test ASP.NET Core middleware</span></span>

<span data-ttu-id="c273f-104">Por [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="c273f-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="c273f-105">O middleware pode ser testado isoladamente com <xref:Microsoft.AspNetCore.TestHost.TestServer>o.</span><span class="sxs-lookup"><span data-stu-id="c273f-105">Middleware can be tested in isolation with <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span> <span data-ttu-id="c273f-106">Ele permite que você:</span><span class="sxs-lookup"><span data-stu-id="c273f-106">It allows you to:</span></span>

* <span data-ttu-id="c273f-107">Crie uma instância de um pipeline de aplicativo contendo apenas os componentes que você precisa testar.</span><span class="sxs-lookup"><span data-stu-id="c273f-107">Instantiate an app pipeline containing only the components that you need to test.</span></span>
* <span data-ttu-id="c273f-108">Envie solicitações personalizadas para verificar o comportamento do middleware.</span><span class="sxs-lookup"><span data-stu-id="c273f-108">Send custom requests to verify middleware behavior.</span></span>

<span data-ttu-id="c273f-109">Vantagens:</span><span class="sxs-lookup"><span data-stu-id="c273f-109">Advantages:</span></span>

* <span data-ttu-id="c273f-110">As solicitações são enviadas na memória em vez de serem serializadas pela rede.</span><span class="sxs-lookup"><span data-stu-id="c273f-110">Requests are sent in-memory rather than being serialized over the network.</span></span>
* <span data-ttu-id="c273f-111">Isso evita preocupações adicionais, como o gerenciamento de porta e os certificados HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c273f-111">This avoids additional concerns, such as port management and HTTPS certificates.</span></span>
* <span data-ttu-id="c273f-112">As exceções no middleware podem fluir diretamente de volta para o teste de chamada.</span><span class="sxs-lookup"><span data-stu-id="c273f-112">Exceptions in the middleware can flow directly back to the calling test.</span></span>
* <span data-ttu-id="c273f-113">É possível personalizar as estruturas de dados do servidor, como <xref:Microsoft.AspNetCore.Http.HttpContext>, diretamente no teste.</span><span class="sxs-lookup"><span data-stu-id="c273f-113">It's possible to customize server data structures, such as <xref:Microsoft.AspNetCore.Http.HttpContext>, directly in the test.</span></span>

## <a name="set-up-the-testserver"></a><span data-ttu-id="c273f-114">Configurar o TestServer</span><span class="sxs-lookup"><span data-stu-id="c273f-114">Set up the TestServer</span></span>

<span data-ttu-id="c273f-115">No projeto de teste, crie um teste:</span><span class="sxs-lookup"><span data-stu-id="c273f-115">In the test project, create a test:</span></span>

* <span data-ttu-id="c273f-116">Crie e inicie um host que usa <xref:Microsoft.AspNetCore.TestHost.TestServer>o.</span><span class="sxs-lookup"><span data-stu-id="c273f-116">Build and start a host that uses <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span>
* <span data-ttu-id="c273f-117">Adicione todos os serviços necessários que o middleware usa.</span><span class="sxs-lookup"><span data-stu-id="c273f-117">Add any required services that the middleware uses.</span></span>
* <span data-ttu-id="c273f-118">Configure o pipeline de processamento para usar o middleware para o teste.</span><span class="sxs-lookup"><span data-stu-id="c273f-118">Configure the processing pipeline to use the middleware for the test.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a><span data-ttu-id="c273f-119">Enviar solicitações com HttpClient</span><span class="sxs-lookup"><span data-stu-id="c273f-119">Send requests with HttpClient</span></span>
<span data-ttu-id="c273f-120">Enviar uma solicitação usando <xref:System.Net.Http.HttpClient>:</span><span class="sxs-lookup"><span data-stu-id="c273f-120">Send a request using <xref:System.Net.Http.HttpClient>:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

<span data-ttu-id="c273f-121">Declare o resultado.</span><span class="sxs-lookup"><span data-stu-id="c273f-121">Assert the result.</span></span> <span data-ttu-id="c273f-122">Primeiro, faça uma asserção o oposto do resultado esperado.</span><span class="sxs-lookup"><span data-stu-id="c273f-122">First, make an assertion the opposite of the result that you expect.</span></span> <span data-ttu-id="c273f-123">Uma execução inicial com uma asserção falsa positiva confirma que o teste falha quando o middleware está sendo executado corretamente.</span><span class="sxs-lookup"><span data-stu-id="c273f-123">An initial run with a false positive assertion confirms that the test fails when the middleware is performing correctly.</span></span> <span data-ttu-id="c273f-124">Execute o teste e confirme se o teste falha.</span><span class="sxs-lookup"><span data-stu-id="c273f-124">Run the test and confirm that the test fails.</span></span>

<span data-ttu-id="c273f-125">No exemplo a seguir, o middleware deve retornar um código de status 404 (*não encontrado*) quando o ponto de extremidade raiz é solicitado.</span><span class="sxs-lookup"><span data-stu-id="c273f-125">In the following example, the middleware should return a 404 status code (*Not Found*) when the root endpoint is requested.</span></span> <span data-ttu-id="c273f-126">Faça a execução do primeiro teste `Assert.NotEqual( ... );`com, que deve falhar:</span><span class="sxs-lookup"><span data-stu-id="c273f-126">Make the first test run with `Assert.NotEqual( ... );`, which should fail:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

<span data-ttu-id="c273f-127">Altere a asserção para testar o middleware em condições normais de operação.</span><span class="sxs-lookup"><span data-stu-id="c273f-127">Change the assertion to test the middleware under normal operating conditions.</span></span> <span data-ttu-id="c273f-128">O teste final usa `Assert.Equal( ... );`.</span><span class="sxs-lookup"><span data-stu-id="c273f-128">The final test uses `Assert.Equal( ... );`.</span></span> <span data-ttu-id="c273f-129">Execute o teste novamente para confirmar que ele é aprovado.</span><span class="sxs-lookup"><span data-stu-id="c273f-129">Run the test again to confirm that it passes.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a><span data-ttu-id="c273f-130">Enviar solicitações com HttpContext</span><span class="sxs-lookup"><span data-stu-id="c273f-130">Send requests with HttpContext</span></span>

<span data-ttu-id="c273f-131">Um aplicativo de teste também pode enviar uma solicitação usando [SendAsync (\<ação HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span><span class="sxs-lookup"><span data-stu-id="c273f-131">A test app can also send a request using [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span></span> <span data-ttu-id="c273f-132">No exemplo a seguir, várias verificações são feitas quando `https://example.com/A/Path/?and=query` o é processado pelo middleware:</span><span class="sxs-lookup"><span data-stu-id="c273f-132">In the following example, several checks are made when `https://example.com/A/Path/?and=query` is processed by the middleware:</span></span>

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

<span data-ttu-id="c273f-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A>permite a configuração direta de <xref:Microsoft.AspNetCore.Http.HttpContext> um objeto em vez de <xref:System.Net.Http.HttpClient> usar as abstrações.</span><span class="sxs-lookup"><span data-stu-id="c273f-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> permits direct configuration of an <xref:Microsoft.AspNetCore.Http.HttpContext> object rather than using the <xref:System.Net.Http.HttpClient> abstractions.</span></span> <span data-ttu-id="c273f-134">Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> para manipular estruturas somente disponíveis no servidor, como [HttpContext. Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) ou [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span><span class="sxs-lookup"><span data-stu-id="c273f-134">Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> to manipulate structures only available on the server, such as [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) or [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span></span>

<span data-ttu-id="c273f-135">Assim como no exemplo anterior testado para uma resposta *404-não encontrada* , verifique o oposto de cada `Assert` instrução no teste anterior.</span><span class="sxs-lookup"><span data-stu-id="c273f-135">As with the earlier example that tested for a *404 - Not Found* response, check the opposite for each `Assert` statement in the preceding test.</span></span> <span data-ttu-id="c273f-136">A verificação confirma que o teste falha corretamente quando o middleware está funcionando normalmente.</span><span class="sxs-lookup"><span data-stu-id="c273f-136">The check confirms that the test fails correctly when the middleware is operating normally.</span></span> <span data-ttu-id="c273f-137">Depois de confirmar que o teste falso positivo funciona, defina as instruções finais `Assert` para as condições e os valores esperados do teste.</span><span class="sxs-lookup"><span data-stu-id="c273f-137">After you've confirmed that the false positive test works, set the final `Assert` statements for the expected conditions and values of the test.</span></span> <span data-ttu-id="c273f-138">Execute-o novamente para confirmar que o teste foi aprovado.</span><span class="sxs-lookup"><span data-stu-id="c273f-138">Run it again to confirm that the test passes.</span></span>
