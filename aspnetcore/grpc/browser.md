---
title: Usar o gRPC em aplicativos de navegador
author: jamesnk
description: Saiba como configurar os serviços gRPC no ASP.NET Core para serem callable a partir de aplicativos de navegador usando gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 04/15/2020
uid: grpc/browser
ms.openlocfilehash: a20e604488b1fb919f18932599ba690bfa308f0c
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440760"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="3aa5b-103">Usar o gRPC em aplicativos de navegador</span><span class="sxs-lookup"><span data-stu-id="3aa5b-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="3aa5b-104">Por [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="3aa5b-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="3aa5b-105">**o suporte gRPC-Web em .NET é experimental**</span><span class="sxs-lookup"><span data-stu-id="3aa5b-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="3aa5b-106">gRPC-Web for .NET é um projeto experimental, não um produto comprometido.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="3aa5b-107">Queremos:</span><span class="sxs-lookup"><span data-stu-id="3aa5b-107">We want to:</span></span>
>
> * <span data-ttu-id="3aa5b-108">Teste que nossa abordagem para implementar o gRPC-Web funciona.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="3aa5b-109">Obtenha feedback sobre se essa abordagem é útil para desenvolvedores .NET em comparação com a maneira tradicional de configurar o gRPC-Web através de um proxy.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="3aa5b-110">Por favor, [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) deixe o feedback para garantir que construamos algo com o qual os desenvolvedores gostem e sejam produtivos.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="3aa5b-111">Não é possível chamar um serviço HTTP/2 gRPC a partir de um aplicativo baseado em navegador.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="3aa5b-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) é um protocolo que permite que os aplicativos JavaScript e Blazor do navegador chamem serviços gRPC.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="3aa5b-113">Este artigo explica como usar o gRPC-Web no .NET Core.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="3aa5b-114">gRPC-Web em ASP.NET Core vs. Envoy</span><span class="sxs-lookup"><span data-stu-id="3aa5b-114">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="3aa5b-115">Existem duas opções de como adicionar gRPC-Web a um aplicativo ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="3aa5b-115">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="3aa5b-116">Suporte gRPC-Web ao lado do gRPC HTTP/2 no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-116">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="3aa5b-117">Esta opção usa middleware `Grpc.AspNetCore.Web` fornecido pelo pacote.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-117">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="3aa5b-118">Use o suporte gRPC-Web [do proxy envoy](https://www.envoyproxy.io/) para traduzir gRPC-Web para gRPC HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-118">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="3aa5b-119">A chamada traduzida é então encaminhada para o aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-119">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="3aa5b-120">Há prós e contras em cada abordagem.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-120">There are pros and cons to each approach.</span></span> <span data-ttu-id="3aa5b-121">Se você já está usando o Envoy como proxy no ambiente do seu aplicativo, pode fazer sentido também usá-lo para fornecer suporte gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-121">If you're already using Envoy as a proxy in your app's environment, it might make sense to also use it to provide gRPC-Web support.</span></span> <span data-ttu-id="3aa5b-122">Se você quer uma solução simples para gRPC-Web que só requer ASP.NET Core, `Grpc.AspNetCore.Web` é uma boa escolha.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-122">If you want a simple solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="3aa5b-123">Configure gRPC-Web no núcleo ASP.NET</span><span class="sxs-lookup"><span data-stu-id="3aa5b-123">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="3aa5b-124">Os serviços gRPC hospedados no ASP.NET Core podem ser configurados para suportar o gRPC-Web ao lado do HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-124">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="3aa5b-125">o gRPC-Web não requer alterações nos serviços.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-125">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="3aa5b-126">A única modificação é a configuração de inicialização.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-126">The only modification is startup configuration.</span></span>

<span data-ttu-id="3aa5b-127">Para habilitar o gRPC-Web com um ASP.NET serviço gRPC Core:</span><span class="sxs-lookup"><span data-stu-id="3aa5b-127">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="3aa5b-128">Adicione uma referência ao pacote [Grpc.AspNetCore.Web.](https://www.nuget.org/packages/Grpc.AspNetCore.Web)</span><span class="sxs-lookup"><span data-stu-id="3aa5b-128">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="3aa5b-129">Configure o aplicativo para usar o `AddGrpcWeb` gRPC-Web adicionando e `UseGrpcWeb` *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="3aa5b-129">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="3aa5b-130">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="3aa5b-130">The preceding code:</span></span>

* <span data-ttu-id="3aa5b-131">Adiciona o middleware gRPC-Web, `UseGrpcWeb`após o roteamento e antes dos pontos finais.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-131">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="3aa5b-132">Especifica que `endpoints.MapGrpcService<GreeterService>()` o método suporta gRPC-Web com `EnableGrpcWeb`.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-132">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="3aa5b-133">Alternativamente, configure todos os serviços para `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` suportar o gRPC-Web adicionando ao ConfigureServices.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-133">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

> [!NOTE]
> <span data-ttu-id="3aa5b-134">Há um problema conhecido que faz com que o gRPC-Web falhe quando [hospedado por Http.sys](xref:fundamentals/servers/httpsys) no .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-134">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="3aa5b-135">Uma solução para obter gRPC-Web trabalhando em Http.sys está disponível [aqui](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span><span class="sxs-lookup"><span data-stu-id="3aa5b-135">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="3aa5b-136">gRPC-Web e CORS</span><span class="sxs-lookup"><span data-stu-id="3aa5b-136">gRPC-Web and CORS</span></span>

<span data-ttu-id="3aa5b-137">A segurança do navegador impede que uma página da Web seja feita para um domínio diferente daquele que serviu a página da Web.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-137">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="3aa5b-138">Essa restrição se aplica à realização de chamadas gRPC-Web com aplicativos de navegador.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-138">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="3aa5b-139">Por exemplo, um aplicativo `https://www.contoso.com` de navegador atendido por é impedido de `https://services.contoso.com`chamar os serviços gRPC-Web hospedados em .</span><span class="sxs-lookup"><span data-stu-id="3aa5b-139">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="3aa5b-140">O Cross Origin Resource Sharing (CORS) pode ser usado para relaxar essa restrição.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-140">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="3aa5b-141">Para permitir que o aplicativo do seu navegador faça chamadas gRPC-Web de origem cruzada, configure [o CORS no ASP.NET Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="3aa5b-141">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="3aa5b-142">Use o suporte cors incorporado e exponha cabeçalhos específicos do gRPC com <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-142">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="3aa5b-143">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="3aa5b-143">The preceding code:</span></span>

* <span data-ttu-id="3aa5b-144">Chama `AddCors` para adicionar serviços CORS e configura uma política CORS que expõe cabeçalhos específicos do gRPC.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-144">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="3aa5b-145">Chama `UseCors` para adicionar o middleware CORS após o roteamento e antes dos pontos finais.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-145">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="3aa5b-146">Especifica que `endpoints.MapGrpcService<GreeterService>()` o método suporta `RequiresCors`CORS com .</span><span class="sxs-lookup"><span data-stu-id="3aa5b-146">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="3aa5b-147">Ligue para o gRPC-Web do navegador</span><span class="sxs-lookup"><span data-stu-id="3aa5b-147">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="3aa5b-148">Os aplicativos do navegador podem usar o gRPC-Web para chamar os serviços gRPC.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-148">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="3aa5b-149">Existem alguns requisitos e limitações ao ligar para os serviços gRPC com gRPC-Web do navegador:</span><span class="sxs-lookup"><span data-stu-id="3aa5b-149">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="3aa5b-150">O servidor deve ter sido configurado para suportar gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-150">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="3aa5b-151">Streaming de clientes e chamadas de streaming bidirecionais não são suportados.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-151">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="3aa5b-152">O streaming do servidor é suportado.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-152">Server streaming is supported.</span></span>
* <span data-ttu-id="3aa5b-153">Chamar serviços gRPC em um domínio diferente requer que [o CORS](xref:security/cors) seja configurado no servidor.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-153">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="3aa5b-154">JavaScript gRPC-Web cliente</span><span class="sxs-lookup"><span data-stu-id="3aa5b-154">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="3aa5b-155">Há um cliente JavaScript gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-155">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="3aa5b-156">Para obter instruções sobre como usar o gRPC-Web do JavaScript, consulte [escrever código cliente JavaScript com gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="3aa5b-156">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="3aa5b-157">Configure gRPC-Web com o cliente .NET gRPC</span><span class="sxs-lookup"><span data-stu-id="3aa5b-157">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="3aa5b-158">O cliente .NET gRPC pode ser configurado para fazer chamadas gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-158">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="3aa5b-159">Isso é útil para os aplicativos [Blazor WebAssembly,](xref:blazor/index#blazor-webassembly) que estão hospedados no navegador e têm as mesmas limitações HTTP do código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-159">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="3aa5b-160">Chamar gRPC-Web com um cliente .NET é [o mesmo que HTTP/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="3aa5b-160">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="3aa5b-161">A única modificação é a forma como o canal é criado.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-161">The only modification is how the channel is created.</span></span>

<span data-ttu-id="3aa5b-162">Para usar o gRPC-Web:</span><span class="sxs-lookup"><span data-stu-id="3aa5b-162">To use gRPC-Web:</span></span>

* <span data-ttu-id="3aa5b-163">Adicione uma referência ao pacote [Grpc.Net.Client.Web.](https://www.nuget.org/packages/Grpc.Net.Client.Web)</span><span class="sxs-lookup"><span data-stu-id="3aa5b-163">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="3aa5b-164">Certifique-se de que a referência ao pacote [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) é 2.27.0 ou superior.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-164">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.27.0 or greater.</span></span>
* <span data-ttu-id="3aa5b-165">Configure o canal `GrpcWebHandler`para usar o:</span><span class="sxs-lookup"><span data-stu-id="3aa5b-165">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="3aa5b-166">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="3aa5b-166">The preceding code:</span></span>

* <span data-ttu-id="3aa5b-167">Configura um canal para usar gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-167">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="3aa5b-168">Cria um cliente e faz uma chamada usando o canal.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-168">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="3aa5b-169">O `GrpcWebHandler` tem as seguintes opções de configuração quando criado:</span><span class="sxs-lookup"><span data-stu-id="3aa5b-169">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="3aa5b-170">**InnerHandler**: O <xref:System.Net.Http.HttpMessageHandler> subjacente que faz com que a `HttpClientHandler`solicitação gRPC HTTP, por exemplo, .</span><span class="sxs-lookup"><span data-stu-id="3aa5b-170">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="3aa5b-171">**Modo**: Um tipo de enumeração que especifica `Content-Type` `application/grpc-web` se `application/grpc-web-text`a solicitação gRPC HTTP é ou .</span><span class="sxs-lookup"><span data-stu-id="3aa5b-171">**Mode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="3aa5b-172">`GrpcWebMode.GrpcWeb`configura o conteúdo a ser enviado sem codificação.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-172">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="3aa5b-173">Valor padrão.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-173">Default value.</span></span>
    * <span data-ttu-id="3aa5b-174">`GrpcWebMode.GrpcWebText`configura o conteúdo a ser codificado base64.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-174">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="3aa5b-175">Necessário para chamadas de streaming de servidor em navegadores.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-175">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="3aa5b-176">**HttpVersion**: `Version` Protocolo HTTP usado para definir [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) na solicitação gRPC HTTP subjacente.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-176">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="3aa5b-177">o gRPC-Web não requer uma versão específica e não anula o padrão, a menos que especificado.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-177">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="3aa5b-178">Os clientes gRPC gerados têm métodos de sincronização e sincronia para chamar métodos não-ários.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-178">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="3aa5b-179">Por exemplo, `SayHello` é `SayHelloAsync` sincronização e é assincronia.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-179">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="3aa5b-180">Chamar um método de sincronização em um aplicativo Blazor WebAssembly fará com que o aplicativo fique sem resposta.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-180">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="3aa5b-181">Os métodos de assincronismo devem ser sempre utilizados no Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="3aa5b-181">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3aa5b-182">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="3aa5b-182">Additional resources</span></span>

* [<span data-ttu-id="3aa5b-183">projeto gRPC para Clientes Web GitHub</span><span class="sxs-lookup"><span data-stu-id="3aa5b-183">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
