---
title: Habilitar as solicitações entre origens (CORS) no ASP.NET Core
author: rick-anderson
description: Saiba como o CORS como um padrão para permitir ou rejeitar solicitações entre origens em um aplicativo ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cors
ms.openlocfilehash: 76e79c2d9762e242dc44857370b0ce1d13f1d1cb
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403775"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="34969-103">Habilitar as solicitações entre origens (CORS) no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="34969-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="34969-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="34969-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="34969-105">Este artigo mostra como habilitar o CORS em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="34969-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="34969-106">A segurança do navegador impede que uma página da Web faça solicitações para um domínio diferente daquele que servia a página da Web.</span><span class="sxs-lookup"><span data-stu-id="34969-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="34969-107">Essa restrição é chamada de *política de mesma origem*.</span><span class="sxs-lookup"><span data-stu-id="34969-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="34969-108">A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site.</span><span class="sxs-lookup"><span data-stu-id="34969-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="34969-109">Às vezes, talvez você queira permitir que outros sites façam solicitações entre origens para seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="34969-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="34969-110">Para obter mais informações, consulte o [artigo de CORS do Mozilla](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="34969-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="34969-111">CORS ( [compartilhamento de recursos entre origens](https://www.w3.org/TR/cors/) ):</span><span class="sxs-lookup"><span data-stu-id="34969-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="34969-112">É um padrão W3C que permite que um servidor Relaxe a política de mesma origem.</span><span class="sxs-lookup"><span data-stu-id="34969-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="34969-113">**Não** é um recurso de segurança, o CORS libera a segurança.</span><span class="sxs-lookup"><span data-stu-id="34969-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="34969-114">Uma API não é mais segura, permitindo CORS.</span><span class="sxs-lookup"><span data-stu-id="34969-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="34969-115">Para obter mais informações, consulte [como o CORS funciona](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="34969-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="34969-116">Permite que um servidor permita explicitamente algumas solicitações entre origens enquanto rejeita outras.</span><span class="sxs-lookup"><span data-stu-id="34969-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="34969-117">É mais seguro e flexível do que as técnicas anteriores, como [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="34969-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="34969-118">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="34969-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="34969-119">Mesma origem</span><span class="sxs-lookup"><span data-stu-id="34969-119">Same origin</span></span>

<span data-ttu-id="34969-120">Duas URLs têm a mesma origem se tiverem esquemas, hosts e portas idênticos ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="34969-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="34969-121">Essas duas URLs têm a mesma origem:</span><span class="sxs-lookup"><span data-stu-id="34969-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="34969-122">Essas URLs têm origens diferentes das duas URLs anteriores:</span><span class="sxs-lookup"><span data-stu-id="34969-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="34969-123">`https://example.net`: Domínio diferente</span><span class="sxs-lookup"><span data-stu-id="34969-123">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="34969-124">`https://www.example.com/foo.html`: Subdomínio diferente</span><span class="sxs-lookup"><span data-stu-id="34969-124">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="34969-125">`http://example.com/foo.html`: Esquema diferente</span><span class="sxs-lookup"><span data-stu-id="34969-125">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="34969-126">`https://example.com:9000/foo.html`: Porta diferente</span><span class="sxs-lookup"><span data-stu-id="34969-126">`https://example.com:9000/foo.html`: Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="34969-127">Habilitar CORS</span><span class="sxs-lookup"><span data-stu-id="34969-127">Enable CORS</span></span>

<span data-ttu-id="34969-128">Há três maneiras de habilitar o CORS:</span><span class="sxs-lookup"><span data-stu-id="34969-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="34969-129">No middleware usando uma [política nomeada](#np) ou uma [política padrão](#dp).</span><span class="sxs-lookup"><span data-stu-id="34969-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="34969-130">Usando o [Roteamento de ponto de extremidade](#ecors).</span><span class="sxs-lookup"><span data-stu-id="34969-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="34969-131">Com o atributo [[EnableCors]](#attr) .</span><span class="sxs-lookup"><span data-stu-id="34969-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="34969-132">O uso do atributo [[EnableCors]](#attr) com uma política nomeada fornece o melhor controle na limitação de pontos de extremidade que dão suporte a CORS.</span><span class="sxs-lookup"><span data-stu-id="34969-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

> [!WARNING]
> <span data-ttu-id="34969-133"><xref:Owin.CorsExtensions.UseCors%2A>deve ser chamado antes de <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> usar `UseResponseCaching` .</span><span class="sxs-lookup"><span data-stu-id="34969-133"><xref:Owin.CorsExtensions.UseCors%2A> must be called before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> when using `UseResponseCaching`.</span></span>

<span data-ttu-id="34969-134">Cada abordagem é detalhada nas seções a seguir.</span><span class="sxs-lookup"><span data-stu-id="34969-134">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="34969-135">CORS com política nomeada e middleware</span><span class="sxs-lookup"><span data-stu-id="34969-135">CORS with named policy and middleware</span></span>

<span data-ttu-id="34969-136">O middleware CORS lida com solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="34969-136">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="34969-137">O código a seguir aplica uma política CORS a todos os pontos de extremidade do aplicativo com as origens especificadas:</span><span class="sxs-lookup"><span data-stu-id="34969-137">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,32)]

<span data-ttu-id="34969-138">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="34969-138">The preceding code:</span></span>

* <span data-ttu-id="34969-139">Define o nome da política como `_myAllowSpecificOrigins` .</span><span class="sxs-lookup"><span data-stu-id="34969-139">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="34969-140">O nome da política é arbitrário.</span><span class="sxs-lookup"><span data-stu-id="34969-140">The policy name is arbitrary.</span></span>
* <span data-ttu-id="34969-141">Chama o <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> método de extensão e especifica a `_myAllowSpecificOrigins` política CORS.</span><span class="sxs-lookup"><span data-stu-id="34969-141">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="34969-142">`UseCors`Adiciona o middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="34969-142">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="34969-143">A chamada para `UseCors` deve ser colocada após `UseRouting` , mas antes de `UseAuthorization` .</span><span class="sxs-lookup"><span data-stu-id="34969-143">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="34969-144">Para obter mais informações, consulte [ordem de middleware](xref:fundamentals/middleware/index#middleware-order).</span><span class="sxs-lookup"><span data-stu-id="34969-144">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="34969-145">Chamadas <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> com uma [expressão lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="34969-145">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="34969-146">O lambda pega um <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objeto.</span><span class="sxs-lookup"><span data-stu-id="34969-146">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="34969-147">[Opções de configuração](#cors-policy-options), como `WithOrigins` , são descritas posteriormente neste artigo.</span><span class="sxs-lookup"><span data-stu-id="34969-147">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="34969-148">Habilita a `_myAllowSpecificOrigins` política CORS para todos os pontos de extremidade do controlador.</span><span class="sxs-lookup"><span data-stu-id="34969-148">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="34969-149">Consulte [Roteamento de ponto de extremidade](#ecors) para aplicar uma política CORS a pontos de extremidades específicos.</span><span class="sxs-lookup"><span data-stu-id="34969-149">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>
* <span data-ttu-id="34969-150">Ao usar o [middleware de cache de resposta](xref:performance/caching/middleware), chame <xref:Owin.CorsExtensions.UseCors%2A> before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> .</span><span class="sxs-lookup"><span data-stu-id="34969-150">When using [Response Caching Middleware](xref:performance/caching/middleware), call <xref:Owin.CorsExtensions.UseCors%2A> before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A>.</span></span>

<span data-ttu-id="34969-151">Com o roteamento de ponto de extremidade, o middleware CORS **deve** ser configurado para ser executado entre as chamadas para `UseRouting` e `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="34969-151">With endpoint routing, the CORS middleware **must** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="34969-152">Consulte [testar CORS](#testc) para obter instruções sobre o código de teste semelhante ao código anterior.</span><span class="sxs-lookup"><span data-stu-id="34969-152">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="34969-153">A <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> chamada de método adiciona serviços CORS ao contêiner de serviço do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="34969-153">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="34969-154">Para obter mais informações, consulte [Opções de política de CORS](#cpo) neste documento.</span><span class="sxs-lookup"><span data-stu-id="34969-154">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="34969-155">Os <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> métodos podem ser encadeados, conforme mostrado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="34969-155">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="34969-156">Observação: a URL especificada **não** deve conter uma barra à direita ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="34969-156">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="34969-157">Se a URL for encerrada com `/` , a comparação retornará `false` e nenhum cabeçalho será retornado.</span><span class="sxs-lookup"><span data-stu-id="34969-157">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="34969-158">CORS com política padrão e middleware</span><span class="sxs-lookup"><span data-stu-id="34969-158">CORS with default policy and middleware</span></span>

<span data-ttu-id="34969-159">O código realçado a seguir habilita a política CORS padrão:</span><span class="sxs-lookup"><span data-stu-id="34969-159">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="34969-160">O código anterior aplica a política CORS padrão a todos os pontos de extremidade do controlador.</span><span class="sxs-lookup"><span data-stu-id="34969-160">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="34969-161">Habilitar CORS com roteamento de ponto de extremidade</span><span class="sxs-lookup"><span data-stu-id="34969-161">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="34969-162">Habilitar o CORS em uma base por ponto de extremidade usando `RequireCors` atualmente **não** oferece suporte a solicitações de [simulação automáticas](#apf).</span><span class="sxs-lookup"><span data-stu-id="34969-162">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="34969-163">Para obter mais informações, consulte [este problema do GitHub](https://github.com/dotnet/aspnetcore/issues/20709) e [testar CORS com roteamento de ponto de extremidade e [httpoptions]](#tcer).</span><span class="sxs-lookup"><span data-stu-id="34969-163">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="34969-164">Com o roteamento de ponto de extremidade, o CORS pode ser habilitado em uma base por ponto de extremidade usando o <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> conjunto de métodos de extensão:</span><span class="sxs-lookup"><span data-stu-id="34969-164">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="34969-165">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="34969-165">In the preceding code:</span></span>

* <span data-ttu-id="34969-166">`app.UseCors`habilita o middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="34969-166">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="34969-167">Como uma política padrão não foi configurada, `app.UseCors()` sozinha não habilita o CORS.</span><span class="sxs-lookup"><span data-stu-id="34969-167">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="34969-168">Os `/echo` pontos de extremidade do controlador e permitem solicitações entre origens usando a política especificada.</span><span class="sxs-lookup"><span data-stu-id="34969-168">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="34969-169">Os `/echo2` pontos de extremidade e de Razor páginas **não** permitem solicitações entre origens porque nenhuma política padrão foi especificada.</span><span class="sxs-lookup"><span data-stu-id="34969-169">The `/echo2` and Razor Pages endpoints do **not** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="34969-170">O atributo [[DisableCors]](#dc) não **desabilita o** CORS que foi habilitado pelo roteamento de ponto de extremidade com `RequireCors` .</span><span class="sxs-lookup"><span data-stu-id="34969-170">The [[DisableCors]](#dc) attribute does **not**  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="34969-171">Consulte [testar CORS com o roteamento de ponto de extremidade e [httpoptions]](#tcer) para obter instruções sobre o código de teste semelhante ao anterior.</span><span class="sxs-lookup"><span data-stu-id="34969-171">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="34969-172">Habilitar CORS com atributos</span><span class="sxs-lookup"><span data-stu-id="34969-172">Enable CORS with attributes</span></span>

<span data-ttu-id="34969-173">Habilitar o CORS com o atributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) e aplicar uma política nomeada somente a esses pontos de extremidade que exigem CORS fornece o controle mais preciso.</span><span class="sxs-lookup"><span data-stu-id="34969-173">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="34969-174">O atributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) fornece uma alternativa para aplicar o CORS globalmente.</span><span class="sxs-lookup"><span data-stu-id="34969-174">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="34969-175">O `[EnableCors]` atributo habilita o CORS para pontos de extremidade selecionados, em vez de todos os pontos de extremidade:</span><span class="sxs-lookup"><span data-stu-id="34969-175">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="34969-176">`[EnableCors]`Especifica a política padrão.</span><span class="sxs-lookup"><span data-stu-id="34969-176">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="34969-177">`[EnableCors("{Policy String}")]`Especifica uma política nomeada.</span><span class="sxs-lookup"><span data-stu-id="34969-177">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="34969-178">O `[EnableCors]` atributo pode ser aplicado a:</span><span class="sxs-lookup"><span data-stu-id="34969-178">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="34969-179">Web`PageModel`</span><span class="sxs-lookup"><span data-stu-id="34969-179"> Page `PageModel`</span></span>
* <span data-ttu-id="34969-180">Controller</span><span class="sxs-lookup"><span data-stu-id="34969-180">Controller</span></span>
* <span data-ttu-id="34969-181">Método de ação do controlador</span><span class="sxs-lookup"><span data-stu-id="34969-181">Controller action method</span></span>

<span data-ttu-id="34969-182">Políticas diferentes podem ser aplicadas a controladores, modelos de página ou métodos de ação com o `[EnableCors]` atributo.</span><span class="sxs-lookup"><span data-stu-id="34969-182">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="34969-183">Quando o `[EnableCors]` atributo é aplicado a um controlador, modelo de página ou método de ação e o CORS é habilitado no middleware, **ambas** as políticas são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="34969-183">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="34969-184">**Recomendamos a combinação de políticas. Use o** `[EnableCors]` **atributo ou middleware, não ambos no mesmo aplicativo.**</span><span class="sxs-lookup"><span data-stu-id="34969-184">**We recommend against combining policies. Use the** `[EnableCors]` **attribute or middleware, not both in the same app.**</span></span>

<span data-ttu-id="34969-185">O código a seguir aplica uma política diferente a cada método:</span><span class="sxs-lookup"><span data-stu-id="34969-185">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="34969-186">O código a seguir cria duas políticas CORS:</span><span class="sxs-lookup"><span data-stu-id="34969-186">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="34969-187">Para o melhor controle da limitação de solicitações de CORS:</span><span class="sxs-lookup"><span data-stu-id="34969-187">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="34969-188">Use `[EnableCors("MyPolicy")]` com uma política nomeada.</span><span class="sxs-lookup"><span data-stu-id="34969-188">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="34969-189">Não defina uma política padrão.</span><span class="sxs-lookup"><span data-stu-id="34969-189">Don't define a default policy.</span></span>
* <span data-ttu-id="34969-190">Não use o [Roteamento de ponto de extremidade](#ecors).</span><span class="sxs-lookup"><span data-stu-id="34969-190">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="34969-191">O código na próxima seção atende à lista anterior.</span><span class="sxs-lookup"><span data-stu-id="34969-191">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="34969-192">Consulte [testar CORS](#testc) para obter instruções sobre o código de teste semelhante ao código anterior.</span><span class="sxs-lookup"><span data-stu-id="34969-192">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="34969-193">Desabilitar CORS</span><span class="sxs-lookup"><span data-stu-id="34969-193">Disable CORS</span></span>

<span data-ttu-id="34969-194">O atributo [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) não **desabilita o** CORS que foi habilitado pelo roteamento de ponto de [extremidade](#ecors).</span><span class="sxs-lookup"><span data-stu-id="34969-194">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does **not**  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="34969-195">O código a seguir define a política CORS `"MyPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="34969-195">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="34969-196">O código a seguir desabilita o CORS para a `GetValues2` ação:</span><span class="sxs-lookup"><span data-stu-id="34969-196">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="34969-197">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="34969-197">The preceding code:</span></span>

* <span data-ttu-id="34969-198">Não habilita o CORS com [Roteamento de ponto de extremidade](#ecors).</span><span class="sxs-lookup"><span data-stu-id="34969-198">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="34969-199">Não define uma [política CORS padrão](#dp).</span><span class="sxs-lookup"><span data-stu-id="34969-199">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="34969-200">Usa [[EnableCors ("MyPolicy")]](#attr) para habilitar a `"MyPolicy"` política CORS para o controlador.</span><span class="sxs-lookup"><span data-stu-id="34969-200">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="34969-201">Desabilita o CORS para o `GetValues2` método.</span><span class="sxs-lookup"><span data-stu-id="34969-201">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="34969-202">Consulte [testar CORS](#testc) para obter instruções sobre como testar o código anterior.</span><span class="sxs-lookup"><span data-stu-id="34969-202">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="34969-203">Opções de política de CORS</span><span class="sxs-lookup"><span data-stu-id="34969-203">CORS policy options</span></span>

<span data-ttu-id="34969-204">Esta seção descreve as várias opções que podem ser definidas em uma política CORS:</span><span class="sxs-lookup"><span data-stu-id="34969-204">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="34969-205">Definir as origens permitidas</span><span class="sxs-lookup"><span data-stu-id="34969-205">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="34969-206">Definir os métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="34969-206">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="34969-207">Definir os cabeçalhos de solicitação permitidos</span><span class="sxs-lookup"><span data-stu-id="34969-207">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="34969-208">Definir os cabeçalhos de resposta expostos</span><span class="sxs-lookup"><span data-stu-id="34969-208">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="34969-209">Credenciais em solicitações entre origens</span><span class="sxs-lookup"><span data-stu-id="34969-209">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="34969-210">Definir o tempo de expiração de simulação</span><span class="sxs-lookup"><span data-stu-id="34969-210">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="34969-211"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>é chamado em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="34969-211"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="34969-212">Para algumas opções, pode ser útil ler a seção [como o CORS funciona](#how-cors) primeiro.</span><span class="sxs-lookup"><span data-stu-id="34969-212">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="34969-213">Definir as origens permitidas</span><span class="sxs-lookup"><span data-stu-id="34969-213">Set the allowed origins</span></span>

<span data-ttu-id="34969-214"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Permite solicitações CORS de todas as origens com qualquer esquema ( `http` ou `https` ).</span><span class="sxs-lookup"><span data-stu-id="34969-214"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="34969-215">`AllowAnyOrigin`o não é seguro porque *qualquer site* pode fazer solicitações entre origens para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="34969-215">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="34969-216">Especificar `AllowAnyOrigin` e `AllowCredentials` é uma configuração insegura e pode resultar em falsificação de solicitação entre sites.</span><span class="sxs-lookup"><span data-stu-id="34969-216">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="34969-217">O serviço CORS retorna uma resposta CORS inválida quando um aplicativo é configurado com ambos os métodos.</span><span class="sxs-lookup"><span data-stu-id="34969-217">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="34969-218">`AllowAnyOrigin`afeta as solicitações de simulação e o `Access-Control-Allow-Origin` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="34969-218">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="34969-219">Para obter mais informações, consulte a seção [solicitações de simulação](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="34969-219">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="34969-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Define a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> propriedade da política como uma função que permite que as origens correspondam a um domínio curinga configurado ao avaliar se a origem é permitida.</span><span class="sxs-lookup"><span data-stu-id="34969-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="34969-221">Definir os métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="34969-221">Set the allowed HTTP methods</span></span>

<span data-ttu-id="34969-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="34969-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="34969-223">Permite qualquer método HTTP:</span><span class="sxs-lookup"><span data-stu-id="34969-223">Allows any HTTP method:</span></span>
* <span data-ttu-id="34969-224">Afeta as solicitações de simulação e o `Access-Control-Allow-Methods` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="34969-224">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="34969-225">Para obter mais informações, consulte a seção [solicitações de simulação](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="34969-225">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="34969-226">Definir os cabeçalhos de solicitação permitidos</span><span class="sxs-lookup"><span data-stu-id="34969-226">Set the allowed request headers</span></span>

<span data-ttu-id="34969-227">Para permitir que cabeçalhos específicos sejam enviados em uma solicitação CORS, chamada [criar cabeçalhos de solicitação de autor](https://xhr.spec.whatwg.org/#request), chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> e especifique os cabeçalhos permitidos:</span><span class="sxs-lookup"><span data-stu-id="34969-227">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="34969-228">Para permitir todos os [cabeçalhos de solicitação de autor](https://www.w3.org/TR/cors/#author-request-headers), chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="34969-228">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="34969-229">`AllowAnyHeader`afeta as solicitações de simulação e o cabeçalho [Access-Control-request-headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) .</span><span class="sxs-lookup"><span data-stu-id="34969-229">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="34969-230">Para obter mais informações, consulte a seção [solicitações de simulação](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="34969-230">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="34969-231">Uma política de middleware CORS correspondente a cabeçalhos específicos especificados por `WithHeaders` é possível somente quando os cabeçalhos enviados `Access-Control-Request-Headers` exatamente correspondem aos cabeçalhos indicados em `WithHeaders` .</span><span class="sxs-lookup"><span data-stu-id="34969-231">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="34969-232">Por exemplo, considere um aplicativo configurado da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="34969-232">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="34969-233">O middleware CORS recusa uma solicitação de simulação com o seguinte cabeçalho de solicitação porque `Content-Language` ([headernames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) não está listado em `WithHeaders` :</span><span class="sxs-lookup"><span data-stu-id="34969-233">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="34969-234">O aplicativo retorna uma resposta de *200 OK* , mas não envia os cabeçalhos CORS de volta.</span><span class="sxs-lookup"><span data-stu-id="34969-234">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="34969-235">Portanto, o navegador não tenta a solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="34969-235">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="34969-236">Definir os cabeçalhos de resposta expostos</span><span class="sxs-lookup"><span data-stu-id="34969-236">Set the exposed response headers</span></span>

<span data-ttu-id="34969-237">Por padrão, o navegador não expõe todos os cabeçalhos de resposta ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="34969-237">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="34969-238">Para obter mais informações, consulte [compartilhamento de recursos entre origens do W3C (terminologia): cabeçalho de resposta simples](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="34969-238">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="34969-239">Os cabeçalhos de resposta que estão disponíveis por padrão são:</span><span class="sxs-lookup"><span data-stu-id="34969-239">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="34969-240">A especificação CORS chama esses cabeçalhos *cabeçalhos de resposta simples*.</span><span class="sxs-lookup"><span data-stu-id="34969-240">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="34969-241">Para disponibilizar outros cabeçalhos para o aplicativo, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="34969-241">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="34969-242">Credenciais em solicitações entre origens</span><span class="sxs-lookup"><span data-stu-id="34969-242">Credentials in cross-origin requests</span></span>

<span data-ttu-id="34969-243">As credenciais exigem tratamento especial em uma solicitação CORS.</span><span class="sxs-lookup"><span data-stu-id="34969-243">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="34969-244">Por padrão, o navegador não envia credenciais com uma solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="34969-244">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="34969-245">As credenciais incluem cookies e esquemas de autenticação HTTP.</span><span class="sxs-lookup"><span data-stu-id="34969-245">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="34969-246">Para enviar credenciais com uma solicitação entre origens, o cliente deve definir `XMLHttpRequest.withCredentials` como `true` .</span><span class="sxs-lookup"><span data-stu-id="34969-246">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="34969-247">Usando `XMLHttpRequest` diretamente:</span><span class="sxs-lookup"><span data-stu-id="34969-247">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="34969-248">Usando o jQuery:</span><span class="sxs-lookup"><span data-stu-id="34969-248">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="34969-249">Usando a [API de busca](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="34969-249">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="34969-250">O servidor deve permitir as credenciais.</span><span class="sxs-lookup"><span data-stu-id="34969-250">The server must allow the credentials.</span></span> <span data-ttu-id="34969-251">Para permitir credenciais entre origens, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="34969-251">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="34969-252">A resposta HTTP inclui um `Access-Control-Allow-Credentials` cabeçalho, que informa ao navegador que o servidor permite credenciais para uma solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="34969-252">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="34969-253">Se o navegador enviar credenciais, mas a resposta não incluir um `Access-Control-Allow-Credentials` cabeçalho válido, o navegador não exporá a resposta ao aplicativo e a solicitação entre origens falhará.</span><span class="sxs-lookup"><span data-stu-id="34969-253">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="34969-254">Permitir credenciais entre origens é um risco de segurança.</span><span class="sxs-lookup"><span data-stu-id="34969-254">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="34969-255">Um site em outro domínio pode enviar as credenciais de um usuário conectado para o aplicativo em nome do usuário sem o conhecimento do usuário.</span><span class="sxs-lookup"><span data-stu-id="34969-255">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="34969-256">A especificação CORS também indica que a definição de origens para `"*"` (todas as origens) é inválida se o `Access-Control-Allow-Credentials` cabeçalho estiver presente.</span><span class="sxs-lookup"><span data-stu-id="34969-256">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="34969-257">Solicitações de simulação</span><span class="sxs-lookup"><span data-stu-id="34969-257">Preflight requests</span></span>

<span data-ttu-id="34969-258">Para algumas solicitações de CORS, o navegador envia uma solicitação de [Opções](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) adicional antes de fazer a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="34969-258">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="34969-259">Essa solicitação é chamada de uma [solicitação de simulação](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span><span class="sxs-lookup"><span data-stu-id="34969-259">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="34969-260">O navegador poderá ignorar a solicitação de simulação se **todas** as seguintes condições forem verdadeiras:</span><span class="sxs-lookup"><span data-stu-id="34969-260">The browser can skip the preflight request if **all** the following conditions are true:</span></span>

* <span data-ttu-id="34969-261">O método de solicitação é GET, HEAD ou POST.</span><span class="sxs-lookup"><span data-stu-id="34969-261">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="34969-262">O aplicativo não define cabeçalhos de solicitação diferentes de `Accept` , `Accept-Language` , `Content-Language` , `Content-Type` ou `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="34969-262">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="34969-263">O `Content-Type` cabeçalho, se definido, tem um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="34969-263">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="34969-264">A regra nos cabeçalhos de solicitação definidos para a solicitação do cliente se aplica aos cabeçalhos que o aplicativo define chamando `setRequestHeader` no `XMLHttpRequest` objeto.</span><span class="sxs-lookup"><span data-stu-id="34969-264">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="34969-265">A especificação CORS chama esses cabeçalhos de [solicitação de autor](https://www.w3.org/TR/cors/#author-request-headers)de cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="34969-265">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="34969-266">A regra não se aplica aos cabeçalhos que o navegador pode definir, como `User-Agent` , `Host` ou `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="34969-266">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="34969-267">Veja a seguir um exemplo de resposta semelhante à solicitação de simulação feita no botão **[Put Test]** na seção [testar CORS](#testc) deste documento.</span><span class="sxs-lookup"><span data-stu-id="34969-267">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="34969-268">A solicitação de simulação usa o método de [Opções http](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) .</span><span class="sxs-lookup"><span data-stu-id="34969-268">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="34969-269">Ele pode incluir os seguintes cabeçalhos:</span><span class="sxs-lookup"><span data-stu-id="34969-269">It may include the following headers:</span></span>

* <span data-ttu-id="34969-270">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): o método http que será usado para a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="34969-270">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="34969-271">[Access-Control-request-headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): uma lista de cabeçalhos de solicitação que o aplicativo define na solicitação real.</span><span class="sxs-lookup"><span data-stu-id="34969-271">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="34969-272">Como mencionado anteriormente, isso não inclui cabeçalhos que o navegador define, como `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="34969-272">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="34969-273">Acesso-controle-permitir-métodos</span><span class="sxs-lookup"><span data-stu-id="34969-273">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="34969-274">Se a solicitação de simulação for negada, o aplicativo retornará uma `200 OK` resposta, mas não definirá os cabeçalhos CORS.</span><span class="sxs-lookup"><span data-stu-id="34969-274">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="34969-275">Portanto, o navegador não tenta a solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="34969-275">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="34969-276">Para obter um exemplo de uma solicitação de simulação negada, consulte a seção [testar CORS](#testc) deste documento.</span><span class="sxs-lookup"><span data-stu-id="34969-276">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="34969-277">Usando as ferramentas F12, o aplicativo de console mostra um erro semelhante a um dos seguintes, dependendo do navegador:</span><span class="sxs-lookup"><span data-stu-id="34969-277">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="34969-278">Firefox: solicitação entre origens bloqueada: a mesma política de origem não permite a leitura do recurso remoto em `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` .</span><span class="sxs-lookup"><span data-stu-id="34969-278">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="34969-279">(Motivo: a solicitação CORS não teve sucesso).</span><span class="sxs-lookup"><span data-stu-id="34969-279">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="34969-280">Saiba mais</span><span class="sxs-lookup"><span data-stu-id="34969-280">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="34969-281">Baseado em Chromium: o acesso à busca em ' https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 ' da origem ' https://cors3.azurewebsites.net ' foi bloqueado pela política de CORS: a resposta à solicitação de simulação não passou na verificação de controle de acesso: nenhum cabeçalho ' Access-Control-Allow-Origin ' está presente no recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="34969-281">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="34969-282">Se uma resposta opaca atender às suas necessidades, defina o modo da solicitação como 'no-cors' para buscar o recurso com o CORS desabilitado.</span><span class="sxs-lookup"><span data-stu-id="34969-282">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="34969-283">Para permitir cabeçalhos específicos, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="34969-283">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="34969-284">Para permitir todos os [cabeçalhos de solicitação de autor](https://www.w3.org/TR/cors/#author-request-headers), chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="34969-284">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="34969-285">Os navegadores não são consistentes em como eles são definidos `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="34969-285">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="34969-286">Se:</span><span class="sxs-lookup"><span data-stu-id="34969-286">If either:</span></span>

* <span data-ttu-id="34969-287">Os cabeçalhos são definidos como algo diferente de`"*"`</span><span class="sxs-lookup"><span data-stu-id="34969-287">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="34969-288"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>é chamado: inclua pelo menos `Accept` , `Content-Type` e `Origin` , além de todos os cabeçalhos personalizados aos quais você deseja dar suporte.</span><span class="sxs-lookup"><span data-stu-id="34969-288"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="34969-289">Código de solicitação de simulação automática</span><span class="sxs-lookup"><span data-stu-id="34969-289">Automatic preflight request code</span></span>

<span data-ttu-id="34969-290">Quando a política CORS for aplicada:</span><span class="sxs-lookup"><span data-stu-id="34969-290">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="34969-291">Globalmente chamando `app.UseCors` em `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="34969-291">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="34969-292">Usando o `[EnableCors]` atributo.</span><span class="sxs-lookup"><span data-stu-id="34969-292">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="34969-293">ASP.NET Core responde à solicitação de simulação de opções.</span><span class="sxs-lookup"><span data-stu-id="34969-293">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="34969-294">Habilitar o CORS em uma base por ponto de extremidade usando `RequireCors` atualmente **não** oferece suporte a solicitações de simulação automáticas.</span><span class="sxs-lookup"><span data-stu-id="34969-294">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support automatic preflight requests.</span></span>

<span data-ttu-id="34969-295">A seção de [CORS de teste](#testc) deste documento demonstra esse comportamento.</span><span class="sxs-lookup"><span data-stu-id="34969-295">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="34969-296">[Httpoptions] atributo para solicitações de simulação</span><span class="sxs-lookup"><span data-stu-id="34969-296">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="34969-297">Quando o CORS é habilitado com a política apropriada, ASP.NET Core geralmente responde às solicitações de simulação de CORS automaticamente.</span><span class="sxs-lookup"><span data-stu-id="34969-297">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="34969-298">Em alguns cenários, esse pode não ser o caso.</span><span class="sxs-lookup"><span data-stu-id="34969-298">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="34969-299">Por exemplo, usando [CORS com roteamento de ponto de extremidade](#ecors).</span><span class="sxs-lookup"><span data-stu-id="34969-299">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="34969-300">O código a seguir usa o atributo [[httpoptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) para criar pontos de extremidade para solicitações de opções:</span><span class="sxs-lookup"><span data-stu-id="34969-300">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="34969-301">Consulte [testar CORS com o roteamento de ponto de extremidade e [httpoptions]](#tcer) para obter instruções sobre como testar o código anterior.</span><span class="sxs-lookup"><span data-stu-id="34969-301">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="34969-302">Definir o tempo de expiração de simulação</span><span class="sxs-lookup"><span data-stu-id="34969-302">Set the preflight expiration time</span></span>

<span data-ttu-id="34969-303">O `Access-Control-Max-Age` cabeçalho especifica quanto tempo a resposta para a solicitação de simulação pode ser armazenada em cache.</span><span class="sxs-lookup"><span data-stu-id="34969-303">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="34969-304">Para definir esse cabeçalho, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="34969-304">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="34969-305">Como o CORS funciona</span><span class="sxs-lookup"><span data-stu-id="34969-305">How CORS works</span></span>

<span data-ttu-id="34969-306">Esta seção descreve o que acontece em uma solicitação de [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) no nível das mensagens http.</span><span class="sxs-lookup"><span data-stu-id="34969-306">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="34969-307">O CORS **não** é um recurso de segurança.</span><span class="sxs-lookup"><span data-stu-id="34969-307">CORS is **not** a security feature.</span></span> <span data-ttu-id="34969-308">O CORS é um padrão W3C que permite que um servidor Relaxe a política de mesma origem.</span><span class="sxs-lookup"><span data-stu-id="34969-308">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="34969-309">Por exemplo, um ator mal-intencionado pode usar [XSS (script entre sites)](xref:security/cross-site-scripting) em seu site e executar uma solicitação entre sites em seu site habilitado para CORS para roubar informações.</span><span class="sxs-lookup"><span data-stu-id="34969-309">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="34969-310">Uma API não é mais segura, permitindo CORS.</span><span class="sxs-lookup"><span data-stu-id="34969-310">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="34969-311">Cabe ao cliente (navegador) impor o CORS.</span><span class="sxs-lookup"><span data-stu-id="34969-311">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="34969-312">O servidor executa a solicitação e retorna a resposta, é o cliente que retorna um erro e bloqueia a resposta.</span><span class="sxs-lookup"><span data-stu-id="34969-312">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="34969-313">Por exemplo, qualquer uma das seguintes ferramentas exibirá a resposta do servidor:</span><span class="sxs-lookup"><span data-stu-id="34969-313">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="34969-314">Fiddler</span><span class="sxs-lookup"><span data-stu-id="34969-314">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="34969-315">Postman</span><span class="sxs-lookup"><span data-stu-id="34969-315">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="34969-316">HttpClient .NET</span><span class="sxs-lookup"><span data-stu-id="34969-316">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="34969-317">Um navegador da Web digitando a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="34969-317">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="34969-318">É uma maneira de um servidor permitir que os navegadores executem uma solicitação de API de [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) ou de [busca](https://developer.mozilla.org/docs/Web/API/Fetch_API) de várias origens que, de outra forma, seriam proibidas.</span><span class="sxs-lookup"><span data-stu-id="34969-318">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="34969-319">Os navegadores sem CORS não podem fazer solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="34969-319">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="34969-320">Antes do CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) foi usado para burlar essa restrição.</span><span class="sxs-lookup"><span data-stu-id="34969-320">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="34969-321">JSONP não usa XHR, ele usa a `<script>` marca para receber a resposta.</span><span class="sxs-lookup"><span data-stu-id="34969-321">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="34969-322">Os scripts podem ser carregados entre origens.</span><span class="sxs-lookup"><span data-stu-id="34969-322">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="34969-323">A [especificação CORS](https://www.w3.org/TR/cors/) introduziu vários novos cabeçalhos HTTP que habilitam solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="34969-323">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="34969-324">Se um navegador oferecer suporte a CORS, ele definirá esses cabeçalhos automaticamente para solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="34969-324">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="34969-325">O código JavaScript personalizado não é necessário para habilitar o CORS.</span><span class="sxs-lookup"><span data-stu-id="34969-325">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="34969-326">O [botão Put Test](https://cors3.azurewebsites.net/test) no [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) implantado</span><span class="sxs-lookup"><span data-stu-id="34969-326">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="34969-327">Veja a seguir um exemplo de uma solicitação entre origens do botão testar [valores](https://cors3.azurewebsites.net/) para `https://cors1.azurewebsites.net/api/values` .</span><span class="sxs-lookup"><span data-stu-id="34969-327">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="34969-328">O `Origin` cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="34969-328">The `Origin` header:</span></span>

* <span data-ttu-id="34969-329">Fornece o domínio do site que está fazendo a solicitação.</span><span class="sxs-lookup"><span data-stu-id="34969-329">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="34969-330">É necessário e deve ser diferente do host.</span><span class="sxs-lookup"><span data-stu-id="34969-330">Is required and must be different from the host.</span></span>

<span data-ttu-id="34969-331">**Cabeçalhos gerais**</span><span class="sxs-lookup"><span data-stu-id="34969-331">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="34969-332">**Cabeçalhos de resposta**</span><span class="sxs-lookup"><span data-stu-id="34969-332">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="34969-333">**Cabeçalhos de solicitação**</span><span class="sxs-lookup"><span data-stu-id="34969-333">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

<span data-ttu-id="34969-334">Em `OPTIONS` solicitações, o servidor define o cabeçalho de **cabeçalhos de resposta** `Access-Control-Allow-Origin: {allowed origin}` na resposta.</span><span class="sxs-lookup"><span data-stu-id="34969-334">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="34969-335">Por exemplo, a solicitação de botão de [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)implantada, [delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) `OPTIONS` contém os seguintes cabeçalhos:</span><span class="sxs-lookup"><span data-stu-id="34969-335">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="34969-336">**Cabeçalhos gerais**</span><span class="sxs-lookup"><span data-stu-id="34969-336">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="34969-337">**Cabeçalhos de resposta**</span><span class="sxs-lookup"><span data-stu-id="34969-337">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="34969-338">**Cabeçalhos de solicitação**</span><span class="sxs-lookup"><span data-stu-id="34969-338">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="34969-339">Nos cabeçalhos de **resposta**anteriores, o servidor define o cabeçalho [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) na resposta.</span><span class="sxs-lookup"><span data-stu-id="34969-339">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="34969-340">O `https://cors1.azurewebsites.net` valor desse cabeçalho corresponde ao `Origin` cabeçalho da solicitação.</span><span class="sxs-lookup"><span data-stu-id="34969-340">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="34969-341">Se <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> for chamado, o `Access-Control-Allow-Origin: *` valor de curinga será retornado.</span><span class="sxs-lookup"><span data-stu-id="34969-341">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="34969-342">`AllowAnyOrigin`permite qualquer origem.</span><span class="sxs-lookup"><span data-stu-id="34969-342">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="34969-343">Se a resposta não incluir o `Access-Control-Allow-Origin` cabeçalho, a solicitação entre origens falhará.</span><span class="sxs-lookup"><span data-stu-id="34969-343">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="34969-344">Especificamente, o navegador não permite a solicitação.</span><span class="sxs-lookup"><span data-stu-id="34969-344">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="34969-345">Mesmo se o servidor retornar uma resposta bem-sucedida, o navegador não tornará a resposta disponível para o aplicativo cliente.</span><span class="sxs-lookup"><span data-stu-id="34969-345">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="34969-346">Exibir solicitações de opções</span><span class="sxs-lookup"><span data-stu-id="34969-346">Display OPTIONS requests</span></span>

<span data-ttu-id="34969-347">Por padrão, os navegadores Chrome e Edge não mostram solicitações de opções na guia rede das ferramentas F12.</span><span class="sxs-lookup"><span data-stu-id="34969-347">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="34969-348">Para exibir solicitações de opções nestes navegadores:</span><span class="sxs-lookup"><span data-stu-id="34969-348">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="34969-349">`chrome://flags/#out-of-blink-cors` ou `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="34969-349">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="34969-350">Desabilite o sinalizador.</span><span class="sxs-lookup"><span data-stu-id="34969-350">disable the flag.</span></span>
* <span data-ttu-id="34969-351">reiniciar.</span><span class="sxs-lookup"><span data-stu-id="34969-351">restart.</span></span>

<span data-ttu-id="34969-352">O Firefox mostra solicitações de opções por padrão.</span><span class="sxs-lookup"><span data-stu-id="34969-352">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="34969-353">CORS no IIS</span><span class="sxs-lookup"><span data-stu-id="34969-353">CORS in IIS</span></span>

<span data-ttu-id="34969-354">Ao implantar no IIS, o CORS precisará ser executado antes da autenticação do Windows se o servidor não estiver configurado para permitir acesso anônimo.</span><span class="sxs-lookup"><span data-stu-id="34969-354">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="34969-355">Para dar suporte a esse cenário, o [módulo CORS do IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) precisa ser instalado e configurado para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="34969-355">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="34969-356">Testar o CORS</span><span class="sxs-lookup"><span data-stu-id="34969-356">Test CORS</span></span>

<span data-ttu-id="34969-357">O [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) tem código para testar CORS.</span><span class="sxs-lookup"><span data-stu-id="34969-357">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="34969-358">Consulte [como baixar](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="34969-358">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="34969-359">O exemplo é um projeto de API com Razor páginas adicionadas:</span><span class="sxs-lookup"><span data-stu-id="34969-359">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="34969-360">`WithOrigins("https://localhost:<port>");`Só deve ser usado para testar um aplicativo de exemplo semelhante ao [código de exemplo de download](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="34969-360">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="34969-361">O seguinte `ValuesController` fornece os pontos de extremidade para teste:</span><span class="sxs-lookup"><span data-stu-id="34969-361">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="34969-362">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) é fornecido pelo [Rick.Docs. Samples. RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet Package e exibe informações de rota.</span><span class="sxs-lookup"><span data-stu-id="34969-362">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="34969-363">Teste o código de exemplo anterior usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="34969-363">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="34969-364">Use o aplicativo de exemplo implantado em [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) .</span><span class="sxs-lookup"><span data-stu-id="34969-364">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="34969-365">Não é necessário baixar o exemplo.</span><span class="sxs-lookup"><span data-stu-id="34969-365">There is no need to download the sample.</span></span>
* <span data-ttu-id="34969-366">Execute o exemplo com `dotnet run` o uso da URL padrão de `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="34969-366">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="34969-367">Execute o exemplo do Visual Studio com a porta definida como 44398 para uma URL de `https://localhost:44398` .</span><span class="sxs-lookup"><span data-stu-id="34969-367">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="34969-368">Usando um navegador com as ferramentas F12:</span><span class="sxs-lookup"><span data-stu-id="34969-368">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="34969-369">Selecione o botão **valores** e examine os cabeçalhos na guia **rede** .</span><span class="sxs-lookup"><span data-stu-id="34969-369">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="34969-370">Selecione o botão **Put Test** .</span><span class="sxs-lookup"><span data-stu-id="34969-370">Select the **PUT test** button.</span></span> <span data-ttu-id="34969-371">Consulte [Exibir opções solicitações](#options) para obter instruções sobre como exibir a solicitação de opções.</span><span class="sxs-lookup"><span data-stu-id="34969-371">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="34969-372">O **teste Put** cria duas solicitações, uma solicitação de simulação de opções e a solicitação Put.</span><span class="sxs-lookup"><span data-stu-id="34969-372">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="34969-373">Selecione o **`GetValues2 [DisableCors]`** botão para disparar uma solicitação de CORS com falha.</span><span class="sxs-lookup"><span data-stu-id="34969-373">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="34969-374">Conforme mencionado no documento, a resposta retorna 200 êxito, mas a solicitação CORS não é feita.</span><span class="sxs-lookup"><span data-stu-id="34969-374">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="34969-375">Selecione a guia **console** para ver o erro CORS.</span><span class="sxs-lookup"><span data-stu-id="34969-375">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="34969-376">Dependendo do navegador, um erro semelhante ao seguinte é exibido:</span><span class="sxs-lookup"><span data-stu-id="34969-376">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="34969-377">O acesso à busca `'https://cors1.azurewebsites.net/api/values/GetValues2'` da origem `'https://cors3.azurewebsites.net'` foi bloqueado pela política CORS: nenhum cabeçalho ' Access-Control-Allow-Origin ' está presente no recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="34969-377">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="34969-378">Se uma resposta opaca atender às suas necessidades, defina o modo da solicitação como 'no-cors' para buscar o recurso com o CORS desabilitado.</span><span class="sxs-lookup"><span data-stu-id="34969-378">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="34969-379">Os pontos de extremidade habilitados para CORS podem ser testados com uma ferramenta, como [ondulação](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)ou [postmaster](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="34969-379">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="34969-380">Ao usar uma ferramenta, a origem da solicitação especificada pelo `Origin` cabeçalho deve ser diferente do host que está recebendo a solicitação.</span><span class="sxs-lookup"><span data-stu-id="34969-380">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="34969-381">Se a solicitação não for de *origem cruzada* com base no valor do `Origin` cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="34969-381">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="34969-382">Não há necessidade de middleware de CORS para processar a solicitação.</span><span class="sxs-lookup"><span data-stu-id="34969-382">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="34969-383">Cabeçalhos CORS não são retornados na resposta.</span><span class="sxs-lookup"><span data-stu-id="34969-383">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="34969-384">O comando a seguir usa `curl` para emitir uma solicitação de opções com informações:</span><span class="sxs-lookup"><span data-stu-id="34969-384">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="34969-385">Testar CORS com roteamento de ponto de extremidade e [Httpoptions]</span><span class="sxs-lookup"><span data-stu-id="34969-385">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="34969-386">Habilitar o CORS em uma base por ponto de extremidade usando `RequireCors` atualmente **não** oferece suporte a solicitações de [simulação automáticas](#apf).</span><span class="sxs-lookup"><span data-stu-id="34969-386">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="34969-387">Considere o código a seguir, que usa o [Roteamento de ponto de extremidade para habilitar CORS](#ecors):</span><span class="sxs-lookup"><span data-stu-id="34969-387">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="34969-388">O seguinte `TodoItems1Controller` fornece pontos de extremidade para teste:</span><span class="sxs-lookup"><span data-stu-id="34969-388">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="34969-389">Teste o código anterior na [página de teste](https://cors1.azurewebsites.net/test?number=1) do [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)implantado.</span><span class="sxs-lookup"><span data-stu-id="34969-389">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="34969-390">Os botões **delete [EnableCors]** e **Get [EnableCors] são** bem-sucedidos, pois os pontos de extremidade têm `[EnableCors]` e respondem a solicitações de simulação.</span><span class="sxs-lookup"><span data-stu-id="34969-390">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="34969-391">Os outros pontos de extremidade falham.</span><span class="sxs-lookup"><span data-stu-id="34969-391">The other endpoints fails.</span></span> <span data-ttu-id="34969-392">O botão **Get** falha, porque o [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) envia:</span><span class="sxs-lookup"><span data-stu-id="34969-392">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="34969-393">O seguinte `TodoItems2Controller` fornece pontos de extremidade semelhantes, mas inclui código explícito para responder às solicitações de opções:</span><span class="sxs-lookup"><span data-stu-id="34969-393">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="34969-394">Teste o código anterior na [página de teste](https://cors1.azurewebsites.net/test?number=2) do exemplo implantado.</span><span class="sxs-lookup"><span data-stu-id="34969-394">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="34969-395">Na lista suspensa **controlador** , selecione **simulação** e, em seguida, **definir controlador**.</span><span class="sxs-lookup"><span data-stu-id="34969-395">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="34969-396">Todas as chamadas de CORS para os `TodoItems2Controller` pontos de extremidade são bem-sucedidos.</span><span class="sxs-lookup"><span data-stu-id="34969-396">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="34969-397">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="34969-397">Additional resources</span></span>

* [<span data-ttu-id="34969-398">CORS (Compartilhamento de Recursos entre Origens)</span><span class="sxs-lookup"><span data-stu-id="34969-398">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="34969-399">Introdução ao módulo CORS do IIS</span><span class="sxs-lookup"><span data-stu-id="34969-399">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="34969-400">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="34969-400">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="34969-401">Este artigo mostra como habilitar o CORS em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="34969-401">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="34969-402">A segurança do navegador impede que uma página da Web faça solicitações para um domínio diferente daquele que servia a página da Web.</span><span class="sxs-lookup"><span data-stu-id="34969-402">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="34969-403">Essa restrição é chamada de *política de mesma origem*.</span><span class="sxs-lookup"><span data-stu-id="34969-403">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="34969-404">A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site.</span><span class="sxs-lookup"><span data-stu-id="34969-404">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="34969-405">Às vezes, talvez você queira permitir que outros sites façam solicitações entre origens para seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="34969-405">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="34969-406">Para obter mais informações, consulte o [artigo de CORS do Mozilla](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="34969-406">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="34969-407">CORS ( [compartilhamento de recursos entre origens](https://www.w3.org/TR/cors/) ):</span><span class="sxs-lookup"><span data-stu-id="34969-407">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="34969-408">É um padrão W3C que permite que um servidor Relaxe a política de mesma origem.</span><span class="sxs-lookup"><span data-stu-id="34969-408">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="34969-409">**Não** é um recurso de segurança, o CORS libera a segurança.</span><span class="sxs-lookup"><span data-stu-id="34969-409">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="34969-410">Uma API não é mais segura, permitindo CORS.</span><span class="sxs-lookup"><span data-stu-id="34969-410">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="34969-411">Para obter mais informações, consulte [como o CORS funciona](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="34969-411">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="34969-412">Permite que um servidor permita explicitamente algumas solicitações entre origens enquanto rejeita outras.</span><span class="sxs-lookup"><span data-stu-id="34969-412">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="34969-413">É mais seguro e flexível do que as técnicas anteriores, como [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="34969-413">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="34969-414">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="34969-414">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="34969-415">Mesma origem</span><span class="sxs-lookup"><span data-stu-id="34969-415">Same origin</span></span>

<span data-ttu-id="34969-416">Duas URLs têm a mesma origem se tiverem esquemas, hosts e portas idênticos ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="34969-416">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="34969-417">Essas duas URLs têm a mesma origem:</span><span class="sxs-lookup"><span data-stu-id="34969-417">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="34969-418">Essas URLs têm origens diferentes das duas URLs anteriores:</span><span class="sxs-lookup"><span data-stu-id="34969-418">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="34969-419">`https://example.net`: Domínio diferente</span><span class="sxs-lookup"><span data-stu-id="34969-419">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="34969-420">`https://www.example.com/foo.html`: Subdomínio diferente</span><span class="sxs-lookup"><span data-stu-id="34969-420">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="34969-421">`http://example.com/foo.html`: Esquema diferente</span><span class="sxs-lookup"><span data-stu-id="34969-421">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="34969-422">`https://example.com:9000/foo.html`: Porta diferente</span><span class="sxs-lookup"><span data-stu-id="34969-422">`https://example.com:9000/foo.html`: Different port</span></span>

<span data-ttu-id="34969-423">O Internet Explorer não considera a porta ao comparar origens.</span><span class="sxs-lookup"><span data-stu-id="34969-423">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="34969-424">CORS com política nomeada e middleware</span><span class="sxs-lookup"><span data-stu-id="34969-424">CORS with named policy and middleware</span></span>

<span data-ttu-id="34969-425">O middleware CORS lida com solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="34969-425">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="34969-426">O código a seguir habilita o CORS para todo o aplicativo com a origem especificada:</span><span class="sxs-lookup"><span data-stu-id="34969-426">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="34969-427">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="34969-427">The preceding code:</span></span>

* <span data-ttu-id="34969-428">Define o nome da política como " \_ myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="34969-428">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="34969-429">O nome da política é arbitrário.</span><span class="sxs-lookup"><span data-stu-id="34969-429">The policy name is arbitrary.</span></span>
* <span data-ttu-id="34969-430">Chama o <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> método de extensão, que habilita o CORS.</span><span class="sxs-lookup"><span data-stu-id="34969-430">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="34969-431">Chamadas <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> com uma [expressão lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="34969-431">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="34969-432">O lambda pega um <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objeto.</span><span class="sxs-lookup"><span data-stu-id="34969-432">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="34969-433">[Opções de configuração](#cors-policy-options), como `WithOrigins` , são descritas posteriormente neste artigo.</span><span class="sxs-lookup"><span data-stu-id="34969-433">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="34969-434">A <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> chamada de método adiciona serviços CORS ao contêiner de serviço do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="34969-434">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="34969-435">Para obter mais informações, consulte [Opções de política de CORS](#cpo) neste documento.</span><span class="sxs-lookup"><span data-stu-id="34969-435">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="34969-436">O <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> método pode encadear métodos, conforme mostrado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="34969-436">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="34969-437">Observação: a URL **não** deve conter uma barra à direita ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="34969-437">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="34969-438">Se a URL for encerrada com `/` , a comparação retornará `false` e nenhum cabeçalho será retornado.</span><span class="sxs-lookup"><span data-stu-id="34969-438">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="34969-439">O código a seguir aplica políticas CORS a todos os pontos de extremidade de aplicativos por meio do middleware CORS:</span><span class="sxs-lookup"><span data-stu-id="34969-439">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
<span data-ttu-id="34969-440">Observação: `UseCors` deve ser chamado antes de `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="34969-440">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="34969-441">Consulte [habilitar CORS em Razor páginas, controladores e métodos de ação](#ecors) para aplicar a política CORS no nível de página/controlador/ação.</span><span class="sxs-lookup"><span data-stu-id="34969-441">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="34969-442">Consulte [testar CORS](#test) para obter instruções sobre o código de teste semelhante ao código anterior.</span><span class="sxs-lookup"><span data-stu-id="34969-442">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="34969-443">Habilitar CORS com atributos</span><span class="sxs-lookup"><span data-stu-id="34969-443">Enable CORS with attributes</span></span>

<span data-ttu-id="34969-444">O atributo [ &lbrack; EnableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) fornece uma alternativa para aplicar o CORS globalmente.</span><span class="sxs-lookup"><span data-stu-id="34969-444">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="34969-445">O `[EnableCors]` atributo habilita o CORS para os pontos de extremidade selecionados, em vez de todos os pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="34969-445">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="34969-446">Use `[EnableCors]` para especificar a política padrão e `[EnableCors("{Policy String}")]` para especificar uma política.</span><span class="sxs-lookup"><span data-stu-id="34969-446">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="34969-447">O `[EnableCors]` atributo pode ser aplicado a:</span><span class="sxs-lookup"><span data-stu-id="34969-447">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="34969-448">Web`PageModel`</span><span class="sxs-lookup"><span data-stu-id="34969-448"> Page `PageModel`</span></span>
* <span data-ttu-id="34969-449">Controller</span><span class="sxs-lookup"><span data-stu-id="34969-449">Controller</span></span>
* <span data-ttu-id="34969-450">Método de ação do controlador</span><span class="sxs-lookup"><span data-stu-id="34969-450">Controller action method</span></span>

<span data-ttu-id="34969-451">Você pode aplicar políticas diferentes ao controlador/página-modelo/ação com o `[EnableCors]` atributo.</span><span class="sxs-lookup"><span data-stu-id="34969-451">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="34969-452">Quando o `[EnableCors]` atributo é aplicado a um método de controlador/modelo de página/ação e o CORS é habilitado no middleware, **ambas** as políticas são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="34969-452">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="34969-453">Recomendamos **não** combinar políticas.</span><span class="sxs-lookup"><span data-stu-id="34969-453">We recommend **not** combining policies.</span></span> <span data-ttu-id="34969-454">Use o `[EnableCors]` atributo ou middleware, **não ambos**.</span><span class="sxs-lookup"><span data-stu-id="34969-454">Use the `[EnableCors]` attribute or middleware, **not both**.</span></span> <span data-ttu-id="34969-455">Ao usar `[EnableCors]` , **não** defina uma política padrão.</span><span class="sxs-lookup"><span data-stu-id="34969-455">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="34969-456">O código a seguir aplica uma política diferente a cada método:</span><span class="sxs-lookup"><span data-stu-id="34969-456">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="34969-457">O código a seguir cria uma política padrão CORS e uma política chamada `"AnotherPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="34969-457">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="34969-458">Desabilitar CORS</span><span class="sxs-lookup"><span data-stu-id="34969-458">Disable CORS</span></span>

<span data-ttu-id="34969-459">O atributo [ &lbrack; DisableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) desabilita o CORS para o controlador/página-modelo/ação.</span><span class="sxs-lookup"><span data-stu-id="34969-459">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="34969-460">Opções de política de CORS</span><span class="sxs-lookup"><span data-stu-id="34969-460">CORS policy options</span></span>

<span data-ttu-id="34969-461">Esta seção descreve as várias opções que podem ser definidas em uma política CORS:</span><span class="sxs-lookup"><span data-stu-id="34969-461">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="34969-462">Definir as origens permitidas</span><span class="sxs-lookup"><span data-stu-id="34969-462">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="34969-463">Definir os métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="34969-463">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="34969-464">Definir os cabeçalhos de solicitação permitidos</span><span class="sxs-lookup"><span data-stu-id="34969-464">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="34969-465">Definir os cabeçalhos de resposta expostos</span><span class="sxs-lookup"><span data-stu-id="34969-465">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="34969-466">Credenciais em solicitações entre origens</span><span class="sxs-lookup"><span data-stu-id="34969-466">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="34969-467">Definir o tempo de expiração de simulação</span><span class="sxs-lookup"><span data-stu-id="34969-467">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="34969-468"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>é chamado em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="34969-468"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="34969-469">Para algumas opções, pode ser útil ler a seção [como o CORS funciona](#how-cors) primeiro.</span><span class="sxs-lookup"><span data-stu-id="34969-469">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="34969-470">Definir as origens permitidas</span><span class="sxs-lookup"><span data-stu-id="34969-470">Set the allowed origins</span></span>

<span data-ttu-id="34969-471"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Permite solicitações CORS de todas as origens com qualquer esquema ( `http` ou `https` ).</span><span class="sxs-lookup"><span data-stu-id="34969-471"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="34969-472">`AllowAnyOrigin`o não é seguro porque *qualquer site* pode fazer solicitações entre origens para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="34969-472">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="34969-473">Especificar `AllowAnyOrigin` e `AllowCredentials` é uma configuração insegura e pode resultar em falsificação de solicitação entre sites.</span><span class="sxs-lookup"><span data-stu-id="34969-473">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="34969-474">Para um aplicativo seguro, especifique uma lista exata de origens se o cliente precisar se autorizar para acessar os recursos do servidor.</span><span class="sxs-lookup"><span data-stu-id="34969-474">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="34969-475">`AllowAnyOrigin`afeta as solicitações de simulação e o `Access-Control-Allow-Origin` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="34969-475">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="34969-476">Para obter mais informações, consulte a seção [solicitações de simulação](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="34969-476">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="34969-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Define a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> propriedade da política como uma função que permite que as origens correspondam a um domínio curinga configurado ao avaliar se a origem é permitida.</span><span class="sxs-lookup"><span data-stu-id="34969-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="34969-478">Definir os métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="34969-478">Set the allowed HTTP methods</span></span>

<span data-ttu-id="34969-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="34969-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="34969-480">Permite qualquer método HTTP:</span><span class="sxs-lookup"><span data-stu-id="34969-480">Allows any HTTP method:</span></span>
* <span data-ttu-id="34969-481">Afeta as solicitações de simulação e o `Access-Control-Allow-Methods` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="34969-481">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="34969-482">Para obter mais informações, consulte a seção [solicitações de simulação](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="34969-482">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="34969-483">Definir os cabeçalhos de solicitação permitidos</span><span class="sxs-lookup"><span data-stu-id="34969-483">Set the allowed request headers</span></span>

<span data-ttu-id="34969-484">Para permitir que cabeçalhos específicos sejam enviados em uma solicitação CORS, chamada *criar cabeçalhos de solicitação de autor*, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> e especifique os cabeçalhos permitidos:</span><span class="sxs-lookup"><span data-stu-id="34969-484">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="34969-485">Para permitir todos os cabeçalhos de solicitação de autor, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="34969-485">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="34969-486">Essa configuração afeta as solicitações de simulação e o `Access-Control-Request-Headers` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="34969-486">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="34969-487">Para obter mais informações, consulte a seção [solicitações de simulação](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="34969-487">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="34969-488">O middleware CORS sempre permite que quatro cabeçalhos no `Access-Control-Request-Headers` sejam enviados, independentemente dos valores configurados em CorsPolicy. Headers.</span><span class="sxs-lookup"><span data-stu-id="34969-488">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="34969-489">Essa lista de cabeçalhos inclui:</span><span class="sxs-lookup"><span data-stu-id="34969-489">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="34969-490">Por exemplo, considere um aplicativo configurado da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="34969-490">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="34969-491">O middleware CORS responde com êxito a uma solicitação de simulação com o seguinte cabeçalho de solicitação porque `Content-Language` sempre está na lista de permissões:</span><span class="sxs-lookup"><span data-stu-id="34969-491">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="34969-492">Definir os cabeçalhos de resposta expostos</span><span class="sxs-lookup"><span data-stu-id="34969-492">Set the exposed response headers</span></span>

<span data-ttu-id="34969-493">Por padrão, o navegador não expõe todos os cabeçalhos de resposta ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="34969-493">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="34969-494">Para obter mais informações, consulte [compartilhamento de recursos entre origens do W3C (terminologia): cabeçalho de resposta simples](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="34969-494">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="34969-495">Os cabeçalhos de resposta que estão disponíveis por padrão são:</span><span class="sxs-lookup"><span data-stu-id="34969-495">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="34969-496">A especificação CORS chama esses cabeçalhos *cabeçalhos de resposta simples*.</span><span class="sxs-lookup"><span data-stu-id="34969-496">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="34969-497">Para disponibilizar outros cabeçalhos para o aplicativo, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="34969-497">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="34969-498">Credenciais em solicitações entre origens</span><span class="sxs-lookup"><span data-stu-id="34969-498">Credentials in cross-origin requests</span></span>

<span data-ttu-id="34969-499">As credenciais exigem tratamento especial em uma solicitação CORS.</span><span class="sxs-lookup"><span data-stu-id="34969-499">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="34969-500">Por padrão, o navegador não envia credenciais com uma solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="34969-500">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="34969-501">As credenciais incluem cookies e esquemas de autenticação HTTP.</span><span class="sxs-lookup"><span data-stu-id="34969-501">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="34969-502">Para enviar credenciais com uma solicitação entre origens, o cliente deve definir `XMLHttpRequest.withCredentials` como `true` .</span><span class="sxs-lookup"><span data-stu-id="34969-502">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="34969-503">Usando `XMLHttpRequest` diretamente:</span><span class="sxs-lookup"><span data-stu-id="34969-503">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="34969-504">Usando o jQuery:</span><span class="sxs-lookup"><span data-stu-id="34969-504">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="34969-505">Usando a [API de busca](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="34969-505">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="34969-506">O servidor deve permitir as credenciais.</span><span class="sxs-lookup"><span data-stu-id="34969-506">The server must allow the credentials.</span></span> <span data-ttu-id="34969-507">Para permitir credenciais entre origens, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="34969-507">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="34969-508">A resposta HTTP inclui um `Access-Control-Allow-Credentials` cabeçalho, que informa ao navegador que o servidor permite credenciais para uma solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="34969-508">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="34969-509">Se o navegador enviar credenciais, mas a resposta não incluir um `Access-Control-Allow-Credentials` cabeçalho válido, o navegador não exporá a resposta ao aplicativo e a solicitação entre origens falhará.</span><span class="sxs-lookup"><span data-stu-id="34969-509">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="34969-510">Permitir credenciais entre origens é um risco de segurança.</span><span class="sxs-lookup"><span data-stu-id="34969-510">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="34969-511">Um site em outro domínio pode enviar as credenciais de um usuário conectado para o aplicativo em nome do usuário sem o conhecimento do usuário.</span><span class="sxs-lookup"><span data-stu-id="34969-511">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="34969-512">A especificação CORS também indica que a definição de origens para `"*"` (todas as origens) é inválida se o `Access-Control-Allow-Credentials` cabeçalho estiver presente.</span><span class="sxs-lookup"><span data-stu-id="34969-512">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="34969-513">Solicitações de simulação</span><span class="sxs-lookup"><span data-stu-id="34969-513">Preflight requests</span></span>

<span data-ttu-id="34969-514">Para algumas solicitações de CORS, o navegador envia uma solicitação adicional antes de fazer a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="34969-514">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="34969-515">Essa solicitação é chamada de uma *solicitação de simulação*.</span><span class="sxs-lookup"><span data-stu-id="34969-515">This request is called a *preflight request*.</span></span> <span data-ttu-id="34969-516">O navegador poderá ignorar a solicitação de simulação se as seguintes condições forem verdadeiras:</span><span class="sxs-lookup"><span data-stu-id="34969-516">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="34969-517">O método de solicitação é GET, HEAD ou POST.</span><span class="sxs-lookup"><span data-stu-id="34969-517">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="34969-518">O aplicativo não define cabeçalhos de solicitação diferentes de `Accept` , `Accept-Language` , `Content-Language` , `Content-Type` ou `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="34969-518">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="34969-519">O `Content-Type` cabeçalho, se definido, tem um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="34969-519">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="34969-520">A regra nos cabeçalhos de solicitação definidos para a solicitação do cliente se aplica aos cabeçalhos que o aplicativo define chamando `setRequestHeader` no `XMLHttpRequest` objeto.</span><span class="sxs-lookup"><span data-stu-id="34969-520">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="34969-521">A especificação CORS chama esses cabeçalhos de *solicitação de autor*de cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="34969-521">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="34969-522">A regra não se aplica aos cabeçalhos que o navegador pode definir, como `User-Agent` , `Host` ou `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="34969-522">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="34969-523">Veja a seguir um exemplo de uma solicitação de simulação:</span><span class="sxs-lookup"><span data-stu-id="34969-523">The following is an example of a preflight request:</span></span>

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

<span data-ttu-id="34969-524">A solicitação de simulação usa o método de opções HTTP.</span><span class="sxs-lookup"><span data-stu-id="34969-524">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="34969-525">Ele inclui dois cabeçalhos especiais:</span><span class="sxs-lookup"><span data-stu-id="34969-525">It includes two special headers:</span></span>

* <span data-ttu-id="34969-526">`Access-Control-Request-Method`: O método HTTP que será usado para a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="34969-526">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="34969-527">`Access-Control-Request-Headers`: Uma lista de cabeçalhos de solicitação que o aplicativo define na solicitação real.</span><span class="sxs-lookup"><span data-stu-id="34969-527">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="34969-528">Como mencionado anteriormente, isso não inclui cabeçalhos que o navegador define, como `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="34969-528">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="34969-529">Quando o CORS é habilitado com a política apropriada, ASP.NET Core geralmente responde automaticamente às solicitações de simulação de CORS.</span><span class="sxs-lookup"><span data-stu-id="34969-529">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="34969-530">Consulte o [atributo [httpoptions] para solicitações de simulação](#pro).</span><span class="sxs-lookup"><span data-stu-id="34969-530">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="34969-531">Uma solicitação de simulação de CORS pode incluir um `Access-Control-Request-Headers` cabeçalho, que indica ao servidor os cabeçalhos que são enviados com a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="34969-531">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="34969-532">Para permitir cabeçalhos específicos, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="34969-532">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="34969-533">Para permitir todos os cabeçalhos de solicitação de autor, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="34969-533">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="34969-534">Os navegadores não são totalmente consistentes em como eles são definidos `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="34969-534">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="34969-535">Se você definir cabeçalhos para algo diferente de `"*"` (ou usar <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> ), deverá incluir pelo menos `Accept` , `Content-Type` e `Origin` , além de todos os cabeçalhos personalizados aos quais deseja dar suporte.</span><span class="sxs-lookup"><span data-stu-id="34969-535">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="34969-536">Veja a seguir um exemplo de resposta para a solicitação de simulação (supondo que o servidor permita a solicitação):</span><span class="sxs-lookup"><span data-stu-id="34969-536">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

<span data-ttu-id="34969-537">A resposta inclui um `Access-Control-Allow-Methods` cabeçalho que lista os métodos permitidos e, opcionalmente `Access-Control-Allow-Headers` , um cabeçalho, que lista os cabeçalhos permitidos.</span><span class="sxs-lookup"><span data-stu-id="34969-537">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="34969-538">Se a solicitação de simulação for realizada com sucesso, o navegador enviará a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="34969-538">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="34969-539">Se a solicitação de simulação for negada, o aplicativo retornará uma resposta *200 OK* , mas não enviará os cabeçalhos CORS de volta.</span><span class="sxs-lookup"><span data-stu-id="34969-539">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="34969-540">Portanto, o navegador não tenta a solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="34969-540">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="34969-541">Definir o tempo de expiração de simulação</span><span class="sxs-lookup"><span data-stu-id="34969-541">Set the preflight expiration time</span></span>

<span data-ttu-id="34969-542">O `Access-Control-Max-Age` cabeçalho especifica quanto tempo a resposta para a solicitação de simulação pode ser armazenada em cache.</span><span class="sxs-lookup"><span data-stu-id="34969-542">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="34969-543">Para definir esse cabeçalho, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="34969-543">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="34969-544">Como o CORS funciona</span><span class="sxs-lookup"><span data-stu-id="34969-544">How CORS works</span></span>

<span data-ttu-id="34969-545">Esta seção descreve o que acontece em uma solicitação de [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) no nível das mensagens http.</span><span class="sxs-lookup"><span data-stu-id="34969-545">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="34969-546">O CORS **não** é um recurso de segurança.</span><span class="sxs-lookup"><span data-stu-id="34969-546">CORS is **not** a security feature.</span></span> <span data-ttu-id="34969-547">O CORS é um padrão W3C que permite que um servidor Relaxe a política de mesma origem.</span><span class="sxs-lookup"><span data-stu-id="34969-547">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="34969-548">Por exemplo, um ator mal-intencionado poderia usar a [prevenção de scripts entre sites (XSS)](xref:security/cross-site-scripting) em seu site e executar uma solicitação entre sites para o site habilitado para CORS para roubar informações.</span><span class="sxs-lookup"><span data-stu-id="34969-548">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="34969-549">Sua API não é mais segura, permitindo CORS.</span><span class="sxs-lookup"><span data-stu-id="34969-549">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="34969-550">Cabe ao cliente (navegador) impor o CORS.</span><span class="sxs-lookup"><span data-stu-id="34969-550">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="34969-551">O servidor executa a solicitação e retorna a resposta, é o cliente que retorna um erro e bloqueia a resposta.</span><span class="sxs-lookup"><span data-stu-id="34969-551">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="34969-552">Por exemplo, qualquer uma das seguintes ferramentas exibirá a resposta do servidor:</span><span class="sxs-lookup"><span data-stu-id="34969-552">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="34969-553">Fiddler</span><span class="sxs-lookup"><span data-stu-id="34969-553">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="34969-554">Postman</span><span class="sxs-lookup"><span data-stu-id="34969-554">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="34969-555">HttpClient .NET</span><span class="sxs-lookup"><span data-stu-id="34969-555">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="34969-556">Um navegador da Web digitando a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="34969-556">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="34969-557">É uma maneira de um servidor permitir que os navegadores executem uma solicitação de API de [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) ou de [busca](https://developer.mozilla.org/docs/Web/API/Fetch_API) de várias origens que, de outra forma, seriam proibidas.</span><span class="sxs-lookup"><span data-stu-id="34969-557">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="34969-558">Os navegadores (sem CORS) não podem fazer solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="34969-558">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="34969-559">Antes do CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) foi usado para burlar essa restrição.</span><span class="sxs-lookup"><span data-stu-id="34969-559">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="34969-560">JSONP não usa XHR, ele usa a `<script>` marca para receber a resposta.</span><span class="sxs-lookup"><span data-stu-id="34969-560">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="34969-561">Os scripts podem ser carregados entre origens.</span><span class="sxs-lookup"><span data-stu-id="34969-561">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="34969-562">A [especificação CORS](https://www.w3.org/TR/cors/) introduziu vários novos cabeçalhos HTTP que habilitam solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="34969-562">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="34969-563">Se um navegador oferecer suporte a CORS, ele definirá esses cabeçalhos automaticamente para solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="34969-563">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="34969-564">O código JavaScript personalizado não é necessário para habilitar o CORS.</span><span class="sxs-lookup"><span data-stu-id="34969-564">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="34969-565">Veja a seguir um exemplo de uma solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="34969-565">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="34969-566">O `Origin` cabeçalho fornece o domínio do site que está fazendo a solicitação.</span><span class="sxs-lookup"><span data-stu-id="34969-566">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="34969-567">O `Origin` cabeçalho é necessário e deve ser diferente do host.</span><span class="sxs-lookup"><span data-stu-id="34969-567">The `Origin` header is required and must be different from the host.</span></span>

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

<span data-ttu-id="34969-568">Se o servidor permitir a solicitação, ele definirá o `Access-Control-Allow-Origin` cabeçalho na resposta.</span><span class="sxs-lookup"><span data-stu-id="34969-568">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="34969-569">O valor desse cabeçalho corresponde ao `Origin` cabeçalho da solicitação ou é o valor curinga `"*"` , o que significa que qualquer origem é permitida:</span><span class="sxs-lookup"><span data-stu-id="34969-569">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

<span data-ttu-id="34969-570">Se a resposta não incluir o `Access-Control-Allow-Origin` cabeçalho, a solicitação entre origens falhará.</span><span class="sxs-lookup"><span data-stu-id="34969-570">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="34969-571">Especificamente, o navegador não permite a solicitação.</span><span class="sxs-lookup"><span data-stu-id="34969-571">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="34969-572">Mesmo se o servidor retornar uma resposta bem-sucedida, o navegador não tornará a resposta disponível para o aplicativo cliente.</span><span class="sxs-lookup"><span data-stu-id="34969-572">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="34969-573">Testar o CORS</span><span class="sxs-lookup"><span data-stu-id="34969-573">Test CORS</span></span>

<span data-ttu-id="34969-574">Para testar o CORS:</span><span class="sxs-lookup"><span data-stu-id="34969-574">To test CORS:</span></span>

1. <span data-ttu-id="34969-575">[Criar um projeto de API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="34969-575">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="34969-576">Como alternativa, você pode [baixar o exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="34969-576">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="34969-577">Habilite o CORS usando uma das abordagens deste documento.</span><span class="sxs-lookup"><span data-stu-id="34969-577">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="34969-578">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="34969-578">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="34969-579">`WithOrigins("https://localhost:<port>");`Só deve ser usado para testar um aplicativo de exemplo semelhante ao [código de exemplo de download](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="34969-579">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="34969-580">Criar um projeto de aplicativo Web ( Razor páginas ou Mvc).</span><span class="sxs-lookup"><span data-stu-id="34969-580">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="34969-581">O exemplo usa Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="34969-581">The sample uses Razor Pages.</span></span> <span data-ttu-id="34969-582">Você pode criar o aplicativo Web na mesma solução que o projeto de API.</span><span class="sxs-lookup"><span data-stu-id="34969-582">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="34969-583">Adicione o seguinte código realçado ao arquivo *index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="34969-583">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="34969-584">No código anterior, substitua `url: 'https://<web app>.azurewebsites.net/api/values/1',` pela URL para o aplicativo implantado.</span><span class="sxs-lookup"><span data-stu-id="34969-584">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="34969-585">Implante o projeto de API.</span><span class="sxs-lookup"><span data-stu-id="34969-585">Deploy the API project.</span></span> <span data-ttu-id="34969-586">Por exemplo, [implante no Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="34969-586">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="34969-587">Execute as Razor páginas ou o aplicativo MVC na área de trabalho e clique no botão **testar** .</span><span class="sxs-lookup"><span data-stu-id="34969-587">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="34969-588">Use as ferramentas F12 para examinar mensagens de erro.</span><span class="sxs-lookup"><span data-stu-id="34969-588">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="34969-589">Remova a origem do localhost de `WithOrigins` e implante o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="34969-589">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="34969-590">Como alternativa, execute o aplicativo cliente com uma porta diferente.</span><span class="sxs-lookup"><span data-stu-id="34969-590">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="34969-591">Por exemplo, execute do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="34969-591">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="34969-592">Teste com o aplicativo cliente.</span><span class="sxs-lookup"><span data-stu-id="34969-592">Test with the client app.</span></span> <span data-ttu-id="34969-593">As falhas de CORS retornam um erro, mas a mensagem de erro não está disponível para JavaScript.</span><span class="sxs-lookup"><span data-stu-id="34969-593">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="34969-594">Use a guia Console nas Ferramentas F12 para ver o erro.</span><span class="sxs-lookup"><span data-stu-id="34969-594">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="34969-595">Dependendo do navegador, você receberá um erro (no console de ferramentas F12) semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="34969-595">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="34969-596">Usando o Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="34969-596">Using Microsoft Edge:</span></span>

     <span data-ttu-id="34969-597">**SEC7120: [CORS] a origem `https://localhost:44375` não foi encontrada `https://localhost:44375` no cabeçalho de resposta Access-Control-Allow-Origin para o recurso entre origens em`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="34969-597">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="34969-598">Usando o Chrome:</span><span class="sxs-lookup"><span data-stu-id="34969-598">Using Chrome:</span></span>

     <span data-ttu-id="34969-599">**O acesso a XMLHttpRequest de `https://webapi.azurewebsites.net/api/values/1` origem `https://localhost:44375` foi bloqueado pela política CORS: nenhum cabeçalho ' Access-Control-Allow-Origin ' está presente no recurso solicitado.**</span><span class="sxs-lookup"><span data-stu-id="34969-599">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="34969-600">Os pontos de extremidade habilitados para CORS podem ser testados com uma ferramenta, como o [Fiddler](https://www.telerik.com/fiddler) ou o [postmaster](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="34969-600">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="34969-601">Ao usar uma ferramenta, a origem da solicitação especificada pelo `Origin` cabeçalho deve ser diferente do host que está recebendo a solicitação.</span><span class="sxs-lookup"><span data-stu-id="34969-601">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="34969-602">Se a solicitação não for de *origem cruzada* com base no valor do `Origin` cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="34969-602">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="34969-603">Não há necessidade de middleware de CORS para processar a solicitação.</span><span class="sxs-lookup"><span data-stu-id="34969-603">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="34969-604">Cabeçalhos CORS não são retornados na resposta.</span><span class="sxs-lookup"><span data-stu-id="34969-604">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="34969-605">CORS no IIS</span><span class="sxs-lookup"><span data-stu-id="34969-605">CORS in IIS</span></span>

<span data-ttu-id="34969-606">Ao implantar no IIS, o CORS precisará ser executado antes da autenticação do Windows se o servidor não estiver configurado para permitir acesso anônimo.</span><span class="sxs-lookup"><span data-stu-id="34969-606">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="34969-607">Para dar suporte a esse cenário, o [módulo CORS do IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) precisa ser instalado e configurado para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="34969-607">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="34969-608">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="34969-608">Additional resources</span></span>

* [<span data-ttu-id="34969-609">CORS (Compartilhamento de Recursos entre Origens)</span><span class="sxs-lookup"><span data-stu-id="34969-609">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="34969-610">Introdução ao módulo CORS do IIS</span><span class="sxs-lookup"><span data-stu-id="34969-610">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
