---
title: Usar o gRPC em aplicativos de navegador
author: jamesnk
description: Saiba como configurar os serviços gRPCs em ASP.NET Core para que possam ser chamados de aplicativos de navegador usando o gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/browser
ms.openlocfilehash: a74f7acb54b4601a0c30ff1a39dc30231e2b5a78
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774737"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="a44e6-103">Usar o gRPC em aplicativos de navegador</span><span class="sxs-lookup"><span data-stu-id="a44e6-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="a44e6-104">Por [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="a44e6-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a44e6-105">**gRPC-o suporte da Web no .NET é experimental**</span><span class="sxs-lookup"><span data-stu-id="a44e6-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="a44e6-106">gRPC-Web para .NET é um projeto experimental, não um produto confirmado.</span><span class="sxs-lookup"><span data-stu-id="a44e6-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="a44e6-107">Queremos:</span><span class="sxs-lookup"><span data-stu-id="a44e6-107">We want to:</span></span>
>
> * <span data-ttu-id="a44e6-108">Teste a nossa abordagem para implementar o gRPC-Web Works.</span><span class="sxs-lookup"><span data-stu-id="a44e6-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="a44e6-109">Obtenha comentários sobre se essa abordagem é útil para os desenvolvedores do .NET em comparação com a maneira tradicional de configurar o gRPC-Web por meio de um proxy.</span><span class="sxs-lookup"><span data-stu-id="a44e6-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="a44e6-110">Deixe comentários em [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) para garantir que criamos algo que os desenvolvedores gostam e que são produtivos com o.</span><span class="sxs-lookup"><span data-stu-id="a44e6-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="a44e6-111">Não é possível chamar um serviço gRPC HTTP/2 de um aplicativo baseado em navegador.</span><span class="sxs-lookup"><span data-stu-id="a44e6-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="a44e6-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) é um protocolo que permite que aplicativos e Blazor JavaScript do navegador chamem serviços gRPCs.</span><span class="sxs-lookup"><span data-stu-id="a44e6-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="a44e6-113">Este artigo explica como usar o gRPC-Web no .NET Core.</span><span class="sxs-lookup"><span data-stu-id="a44e6-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="a44e6-114">gRPC-Web em ASP.NET Core vs. Envoy</span><span class="sxs-lookup"><span data-stu-id="a44e6-114">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="a44e6-115">Há duas opções de como adicionar gRPC-Web a um aplicativo ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="a44e6-115">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="a44e6-116">Suporte a gRPC-Web juntamente com gRPC HTTP/2 em ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a44e6-116">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="a44e6-117">Essa opção usa o `Grpc.AspNetCore.Web` middleware fornecido pelo pacote.</span><span class="sxs-lookup"><span data-stu-id="a44e6-117">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="a44e6-118">Use o suporte gRPC da Web [do Envoy proxy](https://www.envoyproxy.io/) para converter GRPC-Web em gRPC http/2.</span><span class="sxs-lookup"><span data-stu-id="a44e6-118">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="a44e6-119">Em seguida, a chamada traduzida é encaminhada para o aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a44e6-119">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="a44e6-120">Há prós e contras em cada abordagem.</span><span class="sxs-lookup"><span data-stu-id="a44e6-120">There are pros and cons to each approach.</span></span> <span data-ttu-id="a44e6-121">Se você já estiver usando o Envoy como um proxy no ambiente do seu aplicativo, talvez faça sentido também usá-lo para fornecer suporte gRPC à Web.</span><span class="sxs-lookup"><span data-stu-id="a44e6-121">If you're already using Envoy as a proxy in your app's environment, it might make sense to also use it to provide gRPC-Web support.</span></span> <span data-ttu-id="a44e6-122">Se você quiser uma solução simples para o gRPC-Web que requer apenas ASP.NET Core `Grpc.AspNetCore.Web` , é uma boa opção.</span><span class="sxs-lookup"><span data-stu-id="a44e6-122">If you want a simple solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="a44e6-123">Configurar gRPC-Web no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a44e6-123">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="a44e6-124">os serviços gRPCs hospedados no ASP.NET Core podem ser configurados para dar suporte a gRPC-Web juntamente com HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="a44e6-124">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="a44e6-125">gRPC-Web não requer nenhuma alteração nos serviços.</span><span class="sxs-lookup"><span data-stu-id="a44e6-125">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="a44e6-126">A única modificação é a configuração de inicialização.</span><span class="sxs-lookup"><span data-stu-id="a44e6-126">The only modification is startup configuration.</span></span>

<span data-ttu-id="a44e6-127">Para habilitar o gRPC-Web com um serviço de gRPC de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="a44e6-127">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="a44e6-128">Adicione uma referência ao pacote [Grpc. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .</span><span class="sxs-lookup"><span data-stu-id="a44e6-128">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="a44e6-129">Configure o aplicativo para usar o gRPC-Web adicionando `AddGrpcWeb` e `UseGrpcWeb` ao *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="a44e6-129">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="a44e6-130">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="a44e6-130">The preceding code:</span></span>

* <span data-ttu-id="a44e6-131">Adiciona o gRPC-middleware da Web, `UseGrpcWeb`, após o roteamento e antes dos pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="a44e6-131">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="a44e6-132">Especifica o `endpoints.MapGrpcService<GreeterService>()` método que oferece suporte a GRPC `EnableGrpcWeb`-Web com.</span><span class="sxs-lookup"><span data-stu-id="a44e6-132">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="a44e6-133">Como alternativa, configure todos os serviços para dar suporte a gRPC- `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` Web adicionando a configuraservices.</span><span class="sxs-lookup"><span data-stu-id="a44e6-133">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

> [!NOTE]
> <span data-ttu-id="a44e6-134">Há um problema conhecido que faz com que o gRPC falhe quando [hospedado pelo http. sys](xref:fundamentals/servers/httpsys) no .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="a44e6-134">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="a44e6-135">Uma solução alternativa para obter gRPC funcionando em http. sys está disponível [aqui](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span><span class="sxs-lookup"><span data-stu-id="a44e6-135">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="a44e6-136">gRPC-Web e CORS</span><span class="sxs-lookup"><span data-stu-id="a44e6-136">gRPC-Web and CORS</span></span>

<span data-ttu-id="a44e6-137">A segurança do navegador impede que uma página da Web faça solicitações para um domínio diferente daquele que servia a página da Web.</span><span class="sxs-lookup"><span data-stu-id="a44e6-137">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="a44e6-138">Essa restrição se aplica a fazer chamadas gRPC com aplicativos de navegador.</span><span class="sxs-lookup"><span data-stu-id="a44e6-138">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="a44e6-139">Por exemplo, um aplicativo de navegador servido `https://www.contoso.com` pelo é impedido de chamar GRPC-Web Services `https://services.contoso.com`hospedados no.</span><span class="sxs-lookup"><span data-stu-id="a44e6-139">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="a44e6-140">O CORS (compartilhamento de recursos entre origens) pode ser usado para relaxar essa restrição.</span><span class="sxs-lookup"><span data-stu-id="a44e6-140">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="a44e6-141">Para permitir que o aplicativo de navegador faça chamadas gRPC-Web entre origens, configure o [CORS no ASP.NET Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="a44e6-141">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="a44e6-142">Use o suporte interno a CORS e exponha cabeçalhos específicos do gRPC com <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="a44e6-142">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="a44e6-143">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="a44e6-143">The preceding code:</span></span>

* <span data-ttu-id="a44e6-144">Chamadas `AddCors` para adicionar serviços CORS e configura uma política CORS que expõe cabeçalhos específicos do gRPC.</span><span class="sxs-lookup"><span data-stu-id="a44e6-144">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="a44e6-145">Chamadas `UseCors` para adicionar o middleware CORS após o roteamento e antes dos pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="a44e6-145">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="a44e6-146">Especifica o `endpoints.MapGrpcService<GreeterService>()` método que oferece suporte `RequiresCors`a CORS com.</span><span class="sxs-lookup"><span data-stu-id="a44e6-146">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="a44e6-147">Chamar gRPC-Web do navegador</span><span class="sxs-lookup"><span data-stu-id="a44e6-147">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="a44e6-148">Os aplicativos de navegador podem usar gRPC-Web para chamar serviços gRPCs.</span><span class="sxs-lookup"><span data-stu-id="a44e6-148">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="a44e6-149">Há alguns requisitos e limitações ao chamar serviços gRPCs com o gRPC-Web a partir do navegador:</span><span class="sxs-lookup"><span data-stu-id="a44e6-149">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="a44e6-150">O servidor deve ter sido configurado para dar suporte a gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="a44e6-150">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="a44e6-151">Não há suporte para streaming de cliente e chamadas de streaming bidirecionais.</span><span class="sxs-lookup"><span data-stu-id="a44e6-151">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="a44e6-152">Há suporte para o streaming do servidor.</span><span class="sxs-lookup"><span data-stu-id="a44e6-152">Server streaming is supported.</span></span>
* <span data-ttu-id="a44e6-153">A chamada de serviços gRPCs em um domínio diferente requer que o [CORS](xref:security/cors) seja configurado no servidor.</span><span class="sxs-lookup"><span data-stu-id="a44e6-153">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="a44e6-154">JavaScript gRPC-cliente Web</span><span class="sxs-lookup"><span data-stu-id="a44e6-154">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="a44e6-155">Há um cliente JavaScript gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="a44e6-155">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="a44e6-156">Para obter instruções sobre como usar o gRPC-Web do JavaScript, consulte [gravar código de cliente JavaScript com gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="a44e6-156">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="a44e6-157">Configurar o gRPC-Web com o cliente .NET gRPC</span><span class="sxs-lookup"><span data-stu-id="a44e6-157">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="a44e6-158">O cliente .NET gRPC pode ser configurado para fazer chamadas gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="a44e6-158">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="a44e6-159">Isso é útil para [ Blazor aplicativos Webassembly](xref:blazor/index#blazor-webassembly) , que são hospedados no navegador e têm as mesmas limitações de http do código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a44e6-159">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="a44e6-160">Chamar gRPC-Web com um cliente .NET é [o mesmo que http/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="a44e6-160">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="a44e6-161">A única modificação é como o canal é criado.</span><span class="sxs-lookup"><span data-stu-id="a44e6-161">The only modification is how the channel is created.</span></span>

<span data-ttu-id="a44e6-162">Para usar o gRPC-Web:</span><span class="sxs-lookup"><span data-stu-id="a44e6-162">To use gRPC-Web:</span></span>

* <span data-ttu-id="a44e6-163">Adicione uma referência ao pacote [Grpc .net. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .</span><span class="sxs-lookup"><span data-stu-id="a44e6-163">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="a44e6-164">Verifique se a referência ao pacote [Grpc .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) é 2.27.0 ou superior.</span><span class="sxs-lookup"><span data-stu-id="a44e6-164">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.27.0 or greater.</span></span>
* <span data-ttu-id="a44e6-165">Configure o canal para usar `GrpcWebHandler`:</span><span class="sxs-lookup"><span data-stu-id="a44e6-165">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="a44e6-166">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="a44e6-166">The preceding code:</span></span>

* <span data-ttu-id="a44e6-167">Configura um canal para usar gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="a44e6-167">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="a44e6-168">Cria um cliente e faz uma chamada usando o canal.</span><span class="sxs-lookup"><span data-stu-id="a44e6-168">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="a44e6-169">O `GrpcWebHandler` tem as seguintes opções de configuração quando criado:</span><span class="sxs-lookup"><span data-stu-id="a44e6-169">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="a44e6-170">**InnerHandler**: o subjacente <xref:System.Net.Http.HttpMessageHandler> que faz a solicitação HTTP gRPC, por exemplo, `HttpClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="a44e6-170">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="a44e6-171">**Mode**: um tipo de enumeração que especifica se a solicitação `Content-Type` http gRPC `application/grpc-web` é `application/grpc-web-text`ou.</span><span class="sxs-lookup"><span data-stu-id="a44e6-171">**Mode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="a44e6-172">`GrpcWebMode.GrpcWeb`configura o conteúdo a ser enviado sem codificação.</span><span class="sxs-lookup"><span data-stu-id="a44e6-172">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="a44e6-173">Valor padrão.</span><span class="sxs-lookup"><span data-stu-id="a44e6-173">Default value.</span></span>
    * <span data-ttu-id="a44e6-174">`GrpcWebMode.GrpcWebText`configura o conteúdo para ser codificado em base64.</span><span class="sxs-lookup"><span data-stu-id="a44e6-174">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="a44e6-175">Necessário para chamadas de streaming de servidor em navegadores.</span><span class="sxs-lookup"><span data-stu-id="a44e6-175">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="a44e6-176">**HttpVersion**: protocolo `Version` http usado para definir [HTTPREQUESTMESSAGE. Version](xref:System.Net.Http.HttpRequestMessage.Version) na solicitação HTTP gRPC subjacente.</span><span class="sxs-lookup"><span data-stu-id="a44e6-176">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="a44e6-177">gRPC-Web não requer uma versão específica e não substitui o padrão, a menos que especificado.</span><span class="sxs-lookup"><span data-stu-id="a44e6-177">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a44e6-178">Clientes gRPC gerados têm métodos Sync e Async para chamar métodos unários.</span><span class="sxs-lookup"><span data-stu-id="a44e6-178">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="a44e6-179">Por exemplo, `SayHello` é sincronização e `SayHelloAsync` é Async.</span><span class="sxs-lookup"><span data-stu-id="a44e6-179">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="a44e6-180">Chamar um método de sincronização em Blazor um aplicativo Webassembly fará com que o aplicativo fique sem resposta.</span><span class="sxs-lookup"><span data-stu-id="a44e6-180">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="a44e6-181">Os métodos assíncronos sempre devem ser Blazor usados no Webassembly.</span><span class="sxs-lookup"><span data-stu-id="a44e6-181">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a44e6-182">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a44e6-182">Additional resources</span></span>

* [<span data-ttu-id="a44e6-183">gRPC para o projeto GitHub de clientes Web</span><span class="sxs-lookup"><span data-stu-id="a44e6-183">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
