---
title: Usar o gRPC em aplicativos de navegador
author: jamesnk
description: Saiba como configurar os serviços gRPCs em ASP.NET Core para que possam ser chamados de aplicativos de navegador usando o gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/browser
ms.openlocfilehash: 37932e755a0ef2149fb2336d2dcef87d3347d1a4
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404750"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="becb8-103">Usar o gRPC em aplicativos de navegador</span><span class="sxs-lookup"><span data-stu-id="becb8-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="becb8-104">Por [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="becb8-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="becb8-105">Não é possível chamar um serviço gRPC HTTP/2 de um aplicativo baseado em navegador.</span><span class="sxs-lookup"><span data-stu-id="becb8-105">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="becb8-106">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) é um protocolo que permite que aplicativos e JavaScript do navegador Blazor chamem serviços gRPCs.</span><span class="sxs-lookup"><span data-stu-id="becb8-106">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="becb8-107">Este artigo explica como usar o gRPC-Web no .NET Core.</span><span class="sxs-lookup"><span data-stu-id="becb8-107">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="becb8-108">gRPC-Web em ASP.NET Core vs. Envoy</span><span class="sxs-lookup"><span data-stu-id="becb8-108">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="becb8-109">Há duas opções de como adicionar gRPC-Web a um aplicativo ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="becb8-109">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="becb8-110">Suporte a gRPC-Web juntamente com gRPC HTTP/2 em ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="becb8-110">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="becb8-111">Essa opção usa o middleware fornecido pelo `Grpc.AspNetCore.Web` pacote.</span><span class="sxs-lookup"><span data-stu-id="becb8-111">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="becb8-112">Use o suporte gRPC da Web [do Envoy proxy](https://www.envoyproxy.io/) para converter GRPC-Web em gRPC http/2.</span><span class="sxs-lookup"><span data-stu-id="becb8-112">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="becb8-113">Em seguida, a chamada traduzida é encaminhada para o aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="becb8-113">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="becb8-114">Há prós e contras em cada abordagem.</span><span class="sxs-lookup"><span data-stu-id="becb8-114">There are pros and cons to each approach.</span></span> <span data-ttu-id="becb8-115">Se você já estiver usando o Envoy como um proxy no ambiente do seu aplicativo, talvez faça sentido também usá-lo para fornecer suporte gRPC à Web.</span><span class="sxs-lookup"><span data-stu-id="becb8-115">If you're already using Envoy as a proxy in your app's environment, it might make sense to also use it to provide gRPC-Web support.</span></span> <span data-ttu-id="becb8-116">Se você quiser uma solução simples para o gRPC-Web que requer apenas ASP.NET Core, `Grpc.AspNetCore.Web` é uma boa opção.</span><span class="sxs-lookup"><span data-stu-id="becb8-116">If you want a simple solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="becb8-117">Configurar gRPC-Web no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="becb8-117">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="becb8-118">os serviços gRPCs hospedados no ASP.NET Core podem ser configurados para dar suporte a gRPC-Web juntamente com HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="becb8-118">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="becb8-119">gRPC-Web não requer nenhuma alteração nos serviços.</span><span class="sxs-lookup"><span data-stu-id="becb8-119">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="becb8-120">A única modificação é a configuração de inicialização.</span><span class="sxs-lookup"><span data-stu-id="becb8-120">The only modification is startup configuration.</span></span>

<span data-ttu-id="becb8-121">Para habilitar o gRPC-Web com um serviço de gRPC de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="becb8-121">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="becb8-122">Adicione uma referência ao pacote [Grpc. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .</span><span class="sxs-lookup"><span data-stu-id="becb8-122">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="becb8-123">Configure o aplicativo para usar o gRPC-Web adicionando `UseGrpcWeb` e `EnableGrpcWeb` ao *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="becb8-123">Configure the app to use gRPC-Web by adding `UseGrpcWeb` and `EnableGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="becb8-124">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="becb8-124">The preceding code:</span></span>

* <span data-ttu-id="becb8-125">Adiciona o gRPC-middleware da Web, `UseGrpcWeb` , após o roteamento e antes dos pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="becb8-125">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="becb8-126">Especifica o `endpoints.MapGrpcService<GreeterService>()` método que oferece suporte a gRPC-Web com `EnableGrpcWeb` .</span><span class="sxs-lookup"><span data-stu-id="becb8-126">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="becb8-127">Como alternativa, o middleware gRPC-Web pode ser configurado para que todos os serviços ofereçam suporte ao gRPC-Web por padrão e `EnableGrpcWeb` não sejam necessários.</span><span class="sxs-lookup"><span data-stu-id="becb8-127">Alternatively, the gRPC-Web middleware can be configured so all services support gRPC-Web by default and `EnableGrpcWeb` isn't required.</span></span> <span data-ttu-id="becb8-128">Especifique `new GrpcWebOptions { DefaultEnabled = true }` quando o middleware é adicionado.</span><span class="sxs-lookup"><span data-stu-id="becb8-128">Specify `new GrpcWebOptions { DefaultEnabled = true }` when the middleware is added.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> <span data-ttu-id="becb8-129">Há um problema conhecido que faz com que o gRPC falhe quando [hospedado por Http.sys](xref:fundamentals/servers/httpsys) no .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="becb8-129">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="becb8-130">Uma solução alternativa para obter gRPC de trabalho no Http.sys está disponível [aqui](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span><span class="sxs-lookup"><span data-stu-id="becb8-130">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="becb8-131">gRPC-Web e CORS</span><span class="sxs-lookup"><span data-stu-id="becb8-131">gRPC-Web and CORS</span></span>

<span data-ttu-id="becb8-132">A segurança do navegador impede que uma página da Web faça solicitações para um domínio diferente daquele que servia a página da Web.</span><span class="sxs-lookup"><span data-stu-id="becb8-132">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="becb8-133">Essa restrição se aplica a fazer chamadas gRPC com aplicativos de navegador.</span><span class="sxs-lookup"><span data-stu-id="becb8-133">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="becb8-134">Por exemplo, um aplicativo de navegador servido pelo `https://www.contoso.com` é impedido de chamar gRPC-Web Services hospedados no `https://services.contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="becb8-134">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="becb8-135">O CORS (compartilhamento de recursos entre origens) pode ser usado para relaxar essa restrição.</span><span class="sxs-lookup"><span data-stu-id="becb8-135">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="becb8-136">Para permitir que o aplicativo de navegador faça chamadas gRPC-Web entre origens, configure o [CORS no ASP.NET Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="becb8-136">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="becb8-137">Use o suporte interno a CORS e exponha cabeçalhos específicos do gRPC com <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="becb8-137">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="becb8-138">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="becb8-138">The preceding code:</span></span>

* <span data-ttu-id="becb8-139">Chamadas `AddCors` para adicionar serviços CORS e configura uma política CORS que expõe cabeçalhos específicos do gRPC.</span><span class="sxs-lookup"><span data-stu-id="becb8-139">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="becb8-140">Chamadas `UseCors` para adicionar o MIDDLEWARE CORS após o roteamento e antes dos pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="becb8-140">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="becb8-141">Especifica o `endpoints.MapGrpcService<GreeterService>()` método que oferece suporte a CORS com `RequiresCors` .</span><span class="sxs-lookup"><span data-stu-id="becb8-141">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="becb8-142">Chamar gRPC-Web do navegador</span><span class="sxs-lookup"><span data-stu-id="becb8-142">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="becb8-143">Os aplicativos de navegador podem usar gRPC-Web para chamar serviços gRPCs.</span><span class="sxs-lookup"><span data-stu-id="becb8-143">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="becb8-144">Há alguns requisitos e limitações ao chamar serviços gRPCs com o gRPC-Web a partir do navegador:</span><span class="sxs-lookup"><span data-stu-id="becb8-144">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="becb8-145">O servidor deve ter sido configurado para dar suporte a gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="becb8-145">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="becb8-146">Não há suporte para streaming de cliente e chamadas de streaming bidirecionais.</span><span class="sxs-lookup"><span data-stu-id="becb8-146">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="becb8-147">Há suporte para o streaming do servidor.</span><span class="sxs-lookup"><span data-stu-id="becb8-147">Server streaming is supported.</span></span>
* <span data-ttu-id="becb8-148">A chamada de serviços gRPCs em um domínio diferente requer que o [CORS](xref:security/cors) seja configurado no servidor.</span><span class="sxs-lookup"><span data-stu-id="becb8-148">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="becb8-149">JavaScript gRPC-cliente Web</span><span class="sxs-lookup"><span data-stu-id="becb8-149">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="becb8-150">Há um cliente JavaScript gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="becb8-150">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="becb8-151">Para obter instruções sobre como usar o gRPC-Web do JavaScript, consulte [gravar código de cliente JavaScript com gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="becb8-151">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="becb8-152">Configurar o gRPC-Web com o cliente .NET gRPC</span><span class="sxs-lookup"><span data-stu-id="becb8-152">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="becb8-153">O cliente .NET gRPC pode ser configurado para fazer chamadas gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="becb8-153">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="becb8-154">Isso é útil para [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) aplicativos, que são hospedados no navegador e têm as mesmas limitações de http do código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="becb8-154">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="becb8-155">Chamar gRPC-Web com um cliente .NET é [o mesmo que http/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="becb8-155">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="becb8-156">A única modificação é como o canal é criado.</span><span class="sxs-lookup"><span data-stu-id="becb8-156">The only modification is how the channel is created.</span></span>

<span data-ttu-id="becb8-157">Para usar o gRPC-Web:</span><span class="sxs-lookup"><span data-stu-id="becb8-157">To use gRPC-Web:</span></span>

* <span data-ttu-id="becb8-158">Adicione uma referência ao pacote [Grpc .net. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .</span><span class="sxs-lookup"><span data-stu-id="becb8-158">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="becb8-159">Verifique se a referência ao pacote [Grpc .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) é 2.29.0 ou superior.</span><span class="sxs-lookup"><span data-stu-id="becb8-159">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.29.0 or greater.</span></span>
* <span data-ttu-id="becb8-160">Configure o canal para usar `GrpcWebHandler` :</span><span class="sxs-lookup"><span data-stu-id="becb8-160">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="becb8-161">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="becb8-161">The preceding code:</span></span>

* <span data-ttu-id="becb8-162">Configura um canal para usar gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="becb8-162">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="becb8-163">Cria um cliente e faz uma chamada usando o canal.</span><span class="sxs-lookup"><span data-stu-id="becb8-163">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="becb8-164">`GrpcWebHandler`tem as seguintes opções de configuração:</span><span class="sxs-lookup"><span data-stu-id="becb8-164">`GrpcWebHandler` has the following configuration options:</span></span>

* <span data-ttu-id="becb8-165">**InnerHandler**: o subjacente <xref:System.Net.Http.HttpMessageHandler> que faz a solicitação HTTP gRPC, por exemplo, `HttpClientHandler` .</span><span class="sxs-lookup"><span data-stu-id="becb8-165">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="becb8-166">**GrpcWebMode**: um tipo de enumeração que especifica se a solicitação HTTP gRPC `Content-Type` é `application/grpc-web` ou `application/grpc-web-text` .</span><span class="sxs-lookup"><span data-stu-id="becb8-166">**GrpcWebMode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="becb8-167">`GrpcWebMode.GrpcWeb`configura o conteúdo a ser enviado sem codificação.</span><span class="sxs-lookup"><span data-stu-id="becb8-167">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="becb8-168">Valor padrão.</span><span class="sxs-lookup"><span data-stu-id="becb8-168">Default value.</span></span>
    * <span data-ttu-id="becb8-169">`GrpcWebMode.GrpcWebText`configura o conteúdo para ser codificado em base64.</span><span class="sxs-lookup"><span data-stu-id="becb8-169">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="becb8-170">Necessário para chamadas de streaming de servidor em navegadores.</span><span class="sxs-lookup"><span data-stu-id="becb8-170">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="becb8-171">**HttpVersion**: protocolo http `Version` usado para definir [HttpRequestMessage. Version](xref:System.Net.Http.HttpRequestMessage.Version) na solicitação HTTP gRPC subjacente.</span><span class="sxs-lookup"><span data-stu-id="becb8-171">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="becb8-172">gRPC-Web não requer uma versão específica e não substitui o padrão, a menos que especificado.</span><span class="sxs-lookup"><span data-stu-id="becb8-172">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="becb8-173">Clientes gRPC gerados têm métodos Sync e Async para chamar métodos unários.</span><span class="sxs-lookup"><span data-stu-id="becb8-173">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="becb8-174">Por exemplo, `SayHello` é sincronização e `SayHelloAsync` é Async.</span><span class="sxs-lookup"><span data-stu-id="becb8-174">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="becb8-175">Chamar um método de sincronização em um Blazor WebAssembly aplicativo fará com que o aplicativo fique sem resposta.</span><span class="sxs-lookup"><span data-stu-id="becb8-175">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="becb8-176">Os métodos assíncronos sempre devem ser usados no Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="becb8-176">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="becb8-177">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="becb8-177">Additional resources</span></span>

* [<span data-ttu-id="becb8-178">gRPC para o projeto GitHub de clientes Web</span><span class="sxs-lookup"><span data-stu-id="becb8-178">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
