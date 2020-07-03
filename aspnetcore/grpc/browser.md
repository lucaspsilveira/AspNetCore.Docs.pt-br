---
title: Usar o gRPC em aplicativos de navegador
author: jamesnk
description: Saiba como configurar os serviços gRPCs em ASP.NET Core para que possam ser chamados de aplicativos de navegador usando o gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 06/30/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/browser
ms.openlocfilehash: 05ff343f7116509128b7370a50bcfa3c67ffb9fe
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944226"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="626b6-103">Usar o gRPC em aplicativos de navegador</span><span class="sxs-lookup"><span data-stu-id="626b6-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="626b6-104">Por [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="626b6-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

 <span data-ttu-id="626b6-105">Saiba como configurar um serviço gRPC existente do ASP.NET Core para ser chamado de aplicativos de navegador, usando o protocolo [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) .</span><span class="sxs-lookup"><span data-stu-id="626b6-105">Learn how to configure an existing ASP.NET Core gRPC service to be callable from browser apps, using the [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) protocol.</span></span> <span data-ttu-id="626b6-106">gRPC-Web permite que aplicativos e JavaScript do navegador Blazor chamem serviços gRPCs.</span><span class="sxs-lookup"><span data-stu-id="626b6-106">gRPC-Web allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="626b6-107">Não é possível chamar um serviço gRPC HTTP/2 de um aplicativo baseado em navegador.</span><span class="sxs-lookup"><span data-stu-id="626b6-107">It's not possible to call an HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="626b6-108">os serviços gRPCs hospedados no ASP.NET Core podem ser configurados para dar suporte a gRPC-Web juntamente com HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="626b6-108">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span>


<span data-ttu-id="626b6-109">Para obter instruções sobre como adicionar um serviço gRPC a um aplicativo ASP.NET Core existente, consulte [Adicionar serviços gRPC a um aplicativo ASP.NET Core](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).</span><span class="sxs-lookup"><span data-stu-id="626b6-109">For instructions on adding a gRPC service to an existing ASP.NET Core app, see [Add gRPC services to an ASP.NET Core app](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).</span></span>

<span data-ttu-id="626b6-110">Para obter instruções sobre como criar um projeto gRPC, consulte <xref:tutorials/grpc/grpc-start> .</span><span class="sxs-lookup"><span data-stu-id="626b6-110">For instructions on creating a gRPC project, see <xref:tutorials/grpc/grpc-start>.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="626b6-111">gRPC-Web em ASP.NET Core vs. Envoy</span><span class="sxs-lookup"><span data-stu-id="626b6-111">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="626b6-112">Há duas opções de como adicionar gRPC-Web a um aplicativo ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="626b6-112">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="626b6-113">Suporte a gRPC-Web juntamente com gRPC HTTP/2 em ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="626b6-113">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="626b6-114">Essa opção usa o middleware fornecido pelo `Grpc.AspNetCore.Web` pacote.</span><span class="sxs-lookup"><span data-stu-id="626b6-114">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="626b6-115">Use o suporte gRPC da Web [do Envoy proxy](https://www.envoyproxy.io/) para converter GRPC-Web em gRPC http/2.</span><span class="sxs-lookup"><span data-stu-id="626b6-115">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="626b6-116">Em seguida, a chamada traduzida é encaminhada para o aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="626b6-116">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="626b6-117">Há prós e contras em cada abordagem.</span><span class="sxs-lookup"><span data-stu-id="626b6-117">There are pros and cons to each approach.</span></span> <span data-ttu-id="626b6-118">Se o ambiente de um aplicativo já estiver usando o Envoy como um proxy, talvez faça sentido também usar o Envoy para fornecer suporte de gRPC para a Web.</span><span class="sxs-lookup"><span data-stu-id="626b6-118">If an app's environment is already using Envoy as a proxy, it might make sense to also use Envoy to provide gRPC-Web support.</span></span> <span data-ttu-id="626b6-119">Para uma solução básica para o gRPC-Web que requer apenas ASP.NET Core, `Grpc.AspNetCore.Web` é uma boa opção.</span><span class="sxs-lookup"><span data-stu-id="626b6-119">For a basic solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="626b6-120">Configurar gRPC-Web no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="626b6-120">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="626b6-121">os serviços gRPCs hospedados no ASP.NET Core podem ser configurados para dar suporte a gRPC-Web juntamente com HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="626b6-121">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="626b6-122">gRPC-Web não requer nenhuma alteração nos serviços.</span><span class="sxs-lookup"><span data-stu-id="626b6-122">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="626b6-123">A única modificação é a configuração de inicialização.</span><span class="sxs-lookup"><span data-stu-id="626b6-123">The only modification is startup configuration.</span></span>

<span data-ttu-id="626b6-124">Para habilitar o gRPC-Web com um serviço de gRPC de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="626b6-124">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="626b6-125">Adicione uma referência ao pacote [Grpc. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .</span><span class="sxs-lookup"><span data-stu-id="626b6-125">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="626b6-126">Configure o aplicativo para usar o gRPC-Web adicionando `UseGrpcWeb` e `EnableGrpcWeb` ao *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="626b6-126">Configure the app to use gRPC-Web by adding `UseGrpcWeb` and `EnableGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="626b6-127">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="626b6-127">The preceding code:</span></span>

* <span data-ttu-id="626b6-128">Adiciona o gRPC-middleware da Web, `UseGrpcWeb` , após o roteamento e antes dos pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="626b6-128">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="626b6-129">Especifica o `endpoints.MapGrpcService<GreeterService>()` método que oferece suporte a gRPC-Web com `EnableGrpcWeb` .</span><span class="sxs-lookup"><span data-stu-id="626b6-129">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="626b6-130">Como alternativa, o middleware gRPC-Web pode ser configurado para que todos os serviços ofereçam suporte ao gRPC-Web por padrão e `EnableGrpcWeb` não sejam necessários.</span><span class="sxs-lookup"><span data-stu-id="626b6-130">Alternatively, the gRPC-Web middleware can be configured so all services support gRPC-Web by default and `EnableGrpcWeb` isn't required.</span></span> <span data-ttu-id="626b6-131">Especifique `new GrpcWebOptions { DefaultEnabled = true }` quando o middleware é adicionado.</span><span class="sxs-lookup"><span data-stu-id="626b6-131">Specify `new GrpcWebOptions { DefaultEnabled = true }` when the middleware is added.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> <span data-ttu-id="626b6-132">Há um problema conhecido que faz com que o gRPC falhe quando [hospedado por Http.sys](xref:fundamentals/servers/httpsys) no .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="626b6-132">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="626b6-133">Uma solução alternativa para obter gRPC de trabalho no Http.sys está disponível [aqui](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span><span class="sxs-lookup"><span data-stu-id="626b6-133">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="626b6-134">gRPC-Web e CORS</span><span class="sxs-lookup"><span data-stu-id="626b6-134">gRPC-Web and CORS</span></span>

<span data-ttu-id="626b6-135">A segurança do navegador impede que uma página da Web faça solicitações para um domínio diferente daquele que servia a página da Web.</span><span class="sxs-lookup"><span data-stu-id="626b6-135">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="626b6-136">Essa restrição se aplica a fazer chamadas gRPC com aplicativos de navegador.</span><span class="sxs-lookup"><span data-stu-id="626b6-136">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="626b6-137">Por exemplo, um aplicativo de navegador servido pelo `https://www.contoso.com` é impedido de chamar gRPC-Web Services hospedados no `https://services.contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="626b6-137">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="626b6-138">O CORS (compartilhamento de recursos entre origens) pode ser usado para relaxar essa restrição.</span><span class="sxs-lookup"><span data-stu-id="626b6-138">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="626b6-139">Para permitir que um aplicativo de navegador faça chamadas gRPC-Web entre origens, configure o [CORS no ASP.NET Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="626b6-139">To allow a browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="626b6-140">Use o suporte interno a CORS e exponha cabeçalhos específicos do gRPC com <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A> .</span><span class="sxs-lookup"><span data-stu-id="626b6-140">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="626b6-141">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="626b6-141">The preceding code:</span></span>

* <span data-ttu-id="626b6-142">Chamadas `AddCors` para adicionar serviços CORS e configura uma política CORS que expõe cabeçalhos específicos do gRPC.</span><span class="sxs-lookup"><span data-stu-id="626b6-142">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="626b6-143">Chamadas `UseCors` para adicionar o MIDDLEWARE CORS após o roteamento e antes dos pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="626b6-143">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="626b6-144">Especifica o `endpoints.MapGrpcService<GreeterService>()` método que oferece suporte a CORS com `RequiresCors` .</span><span class="sxs-lookup"><span data-stu-id="626b6-144">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

### <a name="grpc-web-and-streaming"></a><span data-ttu-id="626b6-145">gRPC-Web e streaming</span><span class="sxs-lookup"><span data-stu-id="626b6-145">gRPC-Web and streaming</span></span>

<span data-ttu-id="626b6-146">O gRPC tradicional sobre HTTP/2 dá suporte a streaming em todas as direções.</span><span class="sxs-lookup"><span data-stu-id="626b6-146">Traditional gRPC over HTTP/2 supports streaming in all directions.</span></span> <span data-ttu-id="626b6-147">gRPC-Web oferece suporte limitado para streaming:</span><span class="sxs-lookup"><span data-stu-id="626b6-147">gRPC-Web offers limited support for streaming:</span></span>

* <span data-ttu-id="626b6-148">gRPC-os clientes do navegador da Web não dão suporte à chamada de métodos de streaming bidirecional e transmissão de cliente.</span><span class="sxs-lookup"><span data-stu-id="626b6-148">gRPC-Web browser clients don't support calling client streaming and bidirectional streaming methods.</span></span>
* <span data-ttu-id="626b6-149">ASP.NET Core serviços gRPC hospedados no serviço Azure App e no IIS não dão suporte a streaming bidirecional.</span><span class="sxs-lookup"><span data-stu-id="626b6-149">ASP.NET Core gRPC services hosted on Azure App Service and IIS don't support bidirectional streaming.</span></span>

<span data-ttu-id="626b6-150">Ao usar gRPC-Web, recomendamos apenas o uso de métodos unários e métodos de transmissão de servidor.</span><span class="sxs-lookup"><span data-stu-id="626b6-150">When using gRPC-Web, we only recommend the use of unary methods and server streaming methods.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="626b6-151">Chamar gRPC-Web do navegador</span><span class="sxs-lookup"><span data-stu-id="626b6-151">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="626b6-152">Os aplicativos de navegador podem usar gRPC-Web para chamar serviços gRPCs.</span><span class="sxs-lookup"><span data-stu-id="626b6-152">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="626b6-153">Há alguns requisitos e limitações ao chamar serviços gRPCs com o gRPC-Web a partir do navegador:</span><span class="sxs-lookup"><span data-stu-id="626b6-153">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="626b6-154">O servidor deve ter sido configurado para dar suporte a gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="626b6-154">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="626b6-155">Não há suporte para streaming de cliente e chamadas de streaming bidirecionais.</span><span class="sxs-lookup"><span data-stu-id="626b6-155">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="626b6-156">Há suporte para o streaming do servidor.</span><span class="sxs-lookup"><span data-stu-id="626b6-156">Server streaming is supported.</span></span>
* <span data-ttu-id="626b6-157">A chamada de serviços gRPCs em um domínio diferente requer que o [CORS](xref:security/cors) seja configurado no servidor.</span><span class="sxs-lookup"><span data-stu-id="626b6-157">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="626b6-158">JavaScript gRPC-cliente Web</span><span class="sxs-lookup"><span data-stu-id="626b6-158">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="626b6-159">Há um cliente JavaScript gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="626b6-159">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="626b6-160">Para obter instruções sobre como usar o gRPC-Web do JavaScript, consulte [gravar código de cliente JavaScript com gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="626b6-160">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="626b6-161">Configurar o gRPC-Web com o cliente .NET gRPC</span><span class="sxs-lookup"><span data-stu-id="626b6-161">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="626b6-162">O cliente .NET gRPC pode ser configurado para fazer chamadas gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="626b6-162">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="626b6-163">Isso é útil para [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) aplicativos, que são hospedados no navegador e têm as mesmas limitações de http do código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="626b6-163">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="626b6-164">Chamar gRPC-Web com um cliente .NET é [o mesmo que http/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="626b6-164">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="626b6-165">A única modificação é como o canal é criado.</span><span class="sxs-lookup"><span data-stu-id="626b6-165">The only modification is how the channel is created.</span></span>

<span data-ttu-id="626b6-166">Para usar o gRPC-Web:</span><span class="sxs-lookup"><span data-stu-id="626b6-166">To use gRPC-Web:</span></span>

* <span data-ttu-id="626b6-167">Adicione uma referência ao pacote [Grpc .net. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .</span><span class="sxs-lookup"><span data-stu-id="626b6-167">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="626b6-168">Verifique se a referência ao pacote [Grpc .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) é 2.29.0 ou superior.</span><span class="sxs-lookup"><span data-stu-id="626b6-168">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.29.0 or greater.</span></span>
* <span data-ttu-id="626b6-169">Configure o canal para usar `GrpcWebHandler` :</span><span class="sxs-lookup"><span data-stu-id="626b6-169">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="626b6-170">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="626b6-170">The preceding code:</span></span>

* <span data-ttu-id="626b6-171">Configura um canal para usar gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="626b6-171">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="626b6-172">Cria um cliente e faz uma chamada usando o canal.</span><span class="sxs-lookup"><span data-stu-id="626b6-172">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="626b6-173">`GrpcWebHandler`tem as seguintes opções de configuração:</span><span class="sxs-lookup"><span data-stu-id="626b6-173">`GrpcWebHandler` has the following configuration options:</span></span>

* <span data-ttu-id="626b6-174">**InnerHandler**: o subjacente <xref:System.Net.Http.HttpMessageHandler> que faz a solicitação HTTP gRPC, por exemplo, `HttpClientHandler` .</span><span class="sxs-lookup"><span data-stu-id="626b6-174">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="626b6-175">**GrpcWebMode**: um tipo de enumeração que especifica se a solicitação HTTP gRPC `Content-Type` é `application/grpc-web` ou `application/grpc-web-text` .</span><span class="sxs-lookup"><span data-stu-id="626b6-175">**GrpcWebMode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="626b6-176">`GrpcWebMode.GrpcWeb`configura o conteúdo a ser enviado sem codificação.</span><span class="sxs-lookup"><span data-stu-id="626b6-176">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="626b6-177">Valor padrão.</span><span class="sxs-lookup"><span data-stu-id="626b6-177">Default value.</span></span>
    * <span data-ttu-id="626b6-178">`GrpcWebMode.GrpcWebText`configura o conteúdo para ser codificado em base64.</span><span class="sxs-lookup"><span data-stu-id="626b6-178">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="626b6-179">Necessário para chamadas de streaming de servidor em navegadores.</span><span class="sxs-lookup"><span data-stu-id="626b6-179">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="626b6-180">**HttpVersion**: protocolo http `Version` usado para definir [HttpRequestMessage. Version](xref:System.Net.Http.HttpRequestMessage.Version) na solicitação HTTP gRPC subjacente.</span><span class="sxs-lookup"><span data-stu-id="626b6-180">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="626b6-181">gRPC-Web não requer uma versão específica e não substitui o padrão, a menos que especificado.</span><span class="sxs-lookup"><span data-stu-id="626b6-181">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="626b6-182">Clientes gRPC gerados têm métodos Sync e Async para chamar métodos unários.</span><span class="sxs-lookup"><span data-stu-id="626b6-182">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="626b6-183">Por exemplo, `SayHello` é sincronização e `SayHelloAsync` é Async.</span><span class="sxs-lookup"><span data-stu-id="626b6-183">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="626b6-184">Chamar um método de sincronização em um Blazor WebAssembly aplicativo fará com que o aplicativo fique sem resposta.</span><span class="sxs-lookup"><span data-stu-id="626b6-184">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="626b6-185">Os métodos assíncronos sempre devem ser usados no Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="626b6-185">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="626b6-186">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="626b6-186">Additional resources</span></span>

* [<span data-ttu-id="626b6-187">gRPC para o projeto GitHub de clientes Web</span><span class="sxs-lookup"><span data-stu-id="626b6-187">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
