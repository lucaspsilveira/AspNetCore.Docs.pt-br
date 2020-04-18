---
title: Habilite o CORS (Cross-Origin Requests, solicitação de origem cruzada) em ASP.NET Núcleo
author: rick-anderson
description: Saiba como o CORS como padrão para permitir ou rejeitar solicitações de origem cruzada em um aplicativo ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
uid: security/cors
ms.openlocfilehash: 56a339d9018f619af38aecc6f4c2ff40c3c43d2f
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642693"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="01008-103">Habilite o CORS (Cross-Origin Requests, solicitação de origem cruzada) em ASP.NET Núcleo</span><span class="sxs-lookup"><span data-stu-id="01008-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="01008-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e Kirk [Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="01008-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="01008-105">Este artigo mostra como ativar o CORS em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="01008-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="01008-106">A segurança do navegador impede que uma página da Web seja feita para um domínio diferente daquele que serviu a página da Web.</span><span class="sxs-lookup"><span data-stu-id="01008-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="01008-107">Essa restrição é chamada *de política de mesma origem.*</span><span class="sxs-lookup"><span data-stu-id="01008-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="01008-108">A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site.</span><span class="sxs-lookup"><span data-stu-id="01008-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="01008-109">Às vezes, você pode querer permitir que outros sites façam solicitações de origem cruzada para o seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="01008-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="01008-110">Para obter mais informações, consulte o artigo da [Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="01008-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="01008-111">[Compartilhamento de recursos de origem cruzada](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="01008-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="01008-112">É um padrão W3C que permite que um servidor relaxe a política de mesma origem.</span><span class="sxs-lookup"><span data-stu-id="01008-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="01008-113">**Não** é um recurso de segurança, o CORS relaxa a segurança.</span><span class="sxs-lookup"><span data-stu-id="01008-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="01008-114">Uma API não é mais segura ao permitir o CORS.</span><span class="sxs-lookup"><span data-stu-id="01008-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="01008-115">Para obter mais informações, consulte [Como funciona o CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="01008-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="01008-116">Permite que um servidor permita explicitamente algumas solicitações de origem cruzada enquanto rejeita outros.</span><span class="sxs-lookup"><span data-stu-id="01008-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="01008-117">É mais seguro e flexível do que as técnicas anteriores, como [jsonp](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="01008-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="01008-118">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="01008-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="01008-119">Mesma origem</span><span class="sxs-lookup"><span data-stu-id="01008-119">Same origin</span></span>

<span data-ttu-id="01008-120">Dois URLs têm a mesma origem se tiverem esquemas, hosts e portas idênticos ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="01008-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="01008-121">Estes dois URLs têm a mesma origem:</span><span class="sxs-lookup"><span data-stu-id="01008-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="01008-122">Esses URLs têm origens diferentes das duas URLs anteriores:</span><span class="sxs-lookup"><span data-stu-id="01008-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="01008-123">`https://example.net`&ndash; Domínio diferente</span><span class="sxs-lookup"><span data-stu-id="01008-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="01008-124">`https://www.example.com/foo.html`&ndash; Subdomínio diferente</span><span class="sxs-lookup"><span data-stu-id="01008-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="01008-125">`http://example.com/foo.html`&ndash; Esquema diferente</span><span class="sxs-lookup"><span data-stu-id="01008-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="01008-126">`https://example.com:9000/foo.html`&ndash; Porta diferente</span><span class="sxs-lookup"><span data-stu-id="01008-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="01008-127">Habilitar CORS</span><span class="sxs-lookup"><span data-stu-id="01008-127">Enable CORS</span></span>

<span data-ttu-id="01008-128">Existem três maneiras de habilitar o CORS:</span><span class="sxs-lookup"><span data-stu-id="01008-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="01008-129">Em middleware usando uma [diretiva nomeada](#np) ou [política padrão](#dp).</span><span class="sxs-lookup"><span data-stu-id="01008-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="01008-130">Usando [roteamento de ponto final](#ecors).</span><span class="sxs-lookup"><span data-stu-id="01008-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="01008-131">Com o atributo [[EnableCors].](#attr)</span><span class="sxs-lookup"><span data-stu-id="01008-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="01008-132">O uso do atributo [[EnableCors]](#attr) com uma diretiva nomeada fornece o melhor controle na limitação de pontos finais que suportam o CORS.</span><span class="sxs-lookup"><span data-stu-id="01008-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="01008-133">Cada abordagem é detalhada nas seções a seguir.</span><span class="sxs-lookup"><span data-stu-id="01008-133">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="01008-134">CORS com política nomeada e middleware</span><span class="sxs-lookup"><span data-stu-id="01008-134">CORS with named policy and middleware</span></span>

<span data-ttu-id="01008-135">O CORS Middleware lida com solicitações de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="01008-135">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="01008-136">O código a seguir aplica uma diretiva CORS a todos os pontos finais do aplicativo com as origens especificadas:</span><span class="sxs-lookup"><span data-stu-id="01008-136">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="01008-137">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="01008-137">The preceding code:</span></span>

* <span data-ttu-id="01008-138">Define o nome `_myAllowSpecificOrigins`da política para .</span><span class="sxs-lookup"><span data-stu-id="01008-138">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="01008-139">O nome da política é arbitrário.</span><span class="sxs-lookup"><span data-stu-id="01008-139">The policy name is arbitrary.</span></span>
* <span data-ttu-id="01008-140">Chama <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> o método de `_myAllowSpecificOrigins` extensão e especifica a política cors.</span><span class="sxs-lookup"><span data-stu-id="01008-140">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="01008-141">`UseCors`adiciona o middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="01008-141">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="01008-142">A chamada `UseCors` deve ser `UseRouting`feita `UseAuthorization`depois, mas antes .</span><span class="sxs-lookup"><span data-stu-id="01008-142">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="01008-143">Para obter mais informações, consulte [middleware order](xref:fundamentals/middleware/index#middleware-order).</span><span class="sxs-lookup"><span data-stu-id="01008-143">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="01008-144">Chamadas <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> com uma [expressão lambda.](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)</span><span class="sxs-lookup"><span data-stu-id="01008-144">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="01008-145">A lambda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> leva um objeto.</span><span class="sxs-lookup"><span data-stu-id="01008-145">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="01008-146">[As opções de configuração,](#cors-policy-options)como `WithOrigins`, são descritas posteriormente neste artigo.</span><span class="sxs-lookup"><span data-stu-id="01008-146">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="01008-147">Habilita `_myAllowSpecificOrigins` a política CORS para todos os pontos finais do controlador.</span><span class="sxs-lookup"><span data-stu-id="01008-147">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="01008-148">Consulte [o roteamento de ponto final](#ecors) para aplicar uma política CORS a pontos finais específicos.</span><span class="sxs-lookup"><span data-stu-id="01008-148">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="01008-149">Com o roteamento de ponto final, o middleware CORS `UseEndpoints` ***deve*** ser configurado para ser executado entre as chamadas para `UseRouting` e .</span><span class="sxs-lookup"><span data-stu-id="01008-149">With endpoint routing, the CORS middleware ***must*** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="01008-150">Consulte [Test CORS](#testc) para obter instruções sobre o código de teste semelhante ao código anterior.</span><span class="sxs-lookup"><span data-stu-id="01008-150">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="01008-151">A <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> chamada do método adiciona serviços CORS ao contêiner de serviço do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="01008-151">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="01008-152">Para obter mais informações, consulte [as opções de política](#cpo) do CORS neste documento.</span><span class="sxs-lookup"><span data-stu-id="01008-152">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="01008-153">Os <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> métodos podem ser acorrentados, conforme mostrado no seguinte código:</span><span class="sxs-lookup"><span data-stu-id="01008-153">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="01008-154">Nota: A URL especificada **não** deve`/`conter uma barra de arrasto ().</span><span class="sxs-lookup"><span data-stu-id="01008-154">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="01008-155">Se a URL `/`terminar com `false` , a comparação retorna e nenhum cabeçalho é devolvido.</span><span class="sxs-lookup"><span data-stu-id="01008-155">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="01008-156">CORS com política padrão e middleware</span><span class="sxs-lookup"><span data-stu-id="01008-156">CORS with default policy and middleware</span></span>

<span data-ttu-id="01008-157">O código destacado a seguir permite a política CORS padrão:</span><span class="sxs-lookup"><span data-stu-id="01008-157">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="01008-158">O código anterior aplica a diretiva CORS padrão a todos os pontos finais do controlador.</span><span class="sxs-lookup"><span data-stu-id="01008-158">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="01008-159">Habilite cors com roteamento de ponto final</span><span class="sxs-lookup"><span data-stu-id="01008-159">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="01008-160">A habilitação do CORS por `RequireCors` ponto final usando atualmente ***não*** suporta [solicitações automáticas de pré-vôo](#apf).</span><span class="sxs-lookup"><span data-stu-id="01008-160">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="01008-161">Para obter mais informações, consulte [este problema do GitHub](https://github.com/dotnet/aspnetcore/issues/20709) e [teste o CORS com roteamento de ponto final e [HttpOptions]](#tcer).</span><span class="sxs-lookup"><span data-stu-id="01008-161">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="01008-162">Com o roteamento de ponto final, o CORS pode <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> ser habilitado por ponto final usando o conjunto de métodos de extensão:</span><span class="sxs-lookup"><span data-stu-id="01008-162">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="01008-163">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="01008-163">In the preceding code:</span></span>

* <span data-ttu-id="01008-164">`app.UseCors`habilita o middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="01008-164">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="01008-165">Como uma diretiva padrão não foi `app.UseCors()` configurada, sozinha não habilita o CORS.</span><span class="sxs-lookup"><span data-stu-id="01008-165">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="01008-166">Os `/echo` pontos finais do controlador permitem solicitações de origem cruzada usando a diretiva especificada.</span><span class="sxs-lookup"><span data-stu-id="01008-166">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="01008-167">Os `/echo2` pontos finais e as páginas de navalha ***não*** permitem solicitações de origem cruzada porque nenhuma política padrão foi especificada.</span><span class="sxs-lookup"><span data-stu-id="01008-167">The `/echo2` and Razor Pages endpoints do ***not*** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="01008-168">O atributo [[DesativarCors]](#dc) ***não*** desabilita o CORS habilitado pelo `RequireCors`roteamento de ponto final com .</span><span class="sxs-lookup"><span data-stu-id="01008-168">The [[DisableCors]](#dc) attribute does ***not***  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="01008-169">Consulte [Test CORS com roteamento de ponto final e [HttpOptions]](#tcer) para obter instruções sobre o código de teste semelhante ao anterior.</span><span class="sxs-lookup"><span data-stu-id="01008-169">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="01008-170">Habilite o CORS com atributos</span><span class="sxs-lookup"><span data-stu-id="01008-170">Enable CORS with attributes</span></span>

<span data-ttu-id="01008-171">Habilitar o CORS com o atributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) e aplicar uma diretiva nomeada apenas aos pontos finais que requerem cors fornece o melhor controle.</span><span class="sxs-lookup"><span data-stu-id="01008-171">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="01008-172">O atributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) fornece uma alternativa à aplicação do CORS globalmente.</span><span class="sxs-lookup"><span data-stu-id="01008-172">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="01008-173">O `[EnableCors]` atributo habilita o CORS para pontos finais selecionados, em vez de todos os pontos finais:</span><span class="sxs-lookup"><span data-stu-id="01008-173">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="01008-174">`[EnableCors]`especifica a política padrão.</span><span class="sxs-lookup"><span data-stu-id="01008-174">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="01008-175">`[EnableCors("{Policy String}")]`especifica uma política nomeada.</span><span class="sxs-lookup"><span data-stu-id="01008-175">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="01008-176">O `[EnableCors]` atributo pode ser aplicado a:</span><span class="sxs-lookup"><span data-stu-id="01008-176">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="01008-177">Página navalha`PageModel`</span><span class="sxs-lookup"><span data-stu-id="01008-177">Razor Page `PageModel`</span></span>
* <span data-ttu-id="01008-178">Controller</span><span class="sxs-lookup"><span data-stu-id="01008-178">Controller</span></span>
* <span data-ttu-id="01008-179">Método de ação do controlador</span><span class="sxs-lookup"><span data-stu-id="01008-179">Controller action method</span></span>

<span data-ttu-id="01008-180">Diferentes políticas podem ser aplicadas a controladores, modelos de página ou métodos de ação com o atributo. `[EnableCors]`</span><span class="sxs-lookup"><span data-stu-id="01008-180">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="01008-181">Quando `[EnableCors]` o atributo é aplicado a um controlador, modelo de página ou método de ação, e o CORS é habilitado em middleware, ***ambas as*** políticas são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="01008-181">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="01008-182">***Recomendamos contra a combinação de políticas. Use o atributo*** `[EnableCors]` ***ou middleware, não ambos no mesmo aplicativo.***</span><span class="sxs-lookup"><span data-stu-id="01008-182">***We recommend against combining policies. Use the*** `[EnableCors]` ***attribute or middleware, not both in the same app.***</span></span>

<span data-ttu-id="01008-183">O código a seguir aplica uma política diferente a cada método:</span><span class="sxs-lookup"><span data-stu-id="01008-183">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="01008-184">O código a seguir cria duas políticas cors:</span><span class="sxs-lookup"><span data-stu-id="01008-184">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="01008-185">Para o melhor controle de limitar as solicitações do CORS:</span><span class="sxs-lookup"><span data-stu-id="01008-185">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="01008-186">Use `[EnableCors("MyPolicy")]` com uma política nomeada.</span><span class="sxs-lookup"><span data-stu-id="01008-186">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="01008-187">Não defina uma política padrão.</span><span class="sxs-lookup"><span data-stu-id="01008-187">Don't define a default policy.</span></span>
* <span data-ttu-id="01008-188">Não use [roteamento de ponto final](#ecors).</span><span class="sxs-lookup"><span data-stu-id="01008-188">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="01008-189">O código na próxima seção atende à lista anterior.</span><span class="sxs-lookup"><span data-stu-id="01008-189">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="01008-190">Consulte [Test CORS](#testc) para obter instruções sobre o código de teste semelhante ao código anterior.</span><span class="sxs-lookup"><span data-stu-id="01008-190">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="01008-191">Desativar o CORS</span><span class="sxs-lookup"><span data-stu-id="01008-191">Disable CORS</span></span>

<span data-ttu-id="01008-192">O atributo [[DesativarCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) ***não*** desabilita o CORS habilitado pelo [roteamento de ponto final](#ecors).</span><span class="sxs-lookup"><span data-stu-id="01008-192">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does ***not***  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="01008-193">O código a seguir define `"MyPolicy"`a política cors:</span><span class="sxs-lookup"><span data-stu-id="01008-193">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="01008-194">O código a seguir desativa o CORS para a `GetValues2` ação:</span><span class="sxs-lookup"><span data-stu-id="01008-194">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="01008-195">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="01008-195">The preceding code:</span></span>

* <span data-ttu-id="01008-196">Não habilita o CORS com [roteamento de ponto final](#ecors).</span><span class="sxs-lookup"><span data-stu-id="01008-196">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="01008-197">Não define uma [política padrão do CORS.](#dp)</span><span class="sxs-lookup"><span data-stu-id="01008-197">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="01008-198">Usa [[EnableCors("MyPolicy")]](#attr) para `"MyPolicy"` habilitar a diretiva CORS para o controlador.</span><span class="sxs-lookup"><span data-stu-id="01008-198">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="01008-199">Desabilita o CORS para o `GetValues2` método.</span><span class="sxs-lookup"><span data-stu-id="01008-199">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="01008-200">Consulte [O Cors de teste](#testc) para obter instruções sobre o teste do código anterior.</span><span class="sxs-lookup"><span data-stu-id="01008-200">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="01008-201">Opções de política do CORS</span><span class="sxs-lookup"><span data-stu-id="01008-201">CORS policy options</span></span>

<span data-ttu-id="01008-202">Esta seção descreve as várias opções que podem ser definidas em uma política cors:</span><span class="sxs-lookup"><span data-stu-id="01008-202">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="01008-203">Defina as origens permitidas</span><span class="sxs-lookup"><span data-stu-id="01008-203">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="01008-204">Defina os métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="01008-204">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="01008-205">Defina os cabeçalhos de solicitação permitidos</span><span class="sxs-lookup"><span data-stu-id="01008-205">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="01008-206">Defina os cabeçalhos de resposta expostos</span><span class="sxs-lookup"><span data-stu-id="01008-206">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="01008-207">Credenciais em solicitações de origem cruzada</span><span class="sxs-lookup"><span data-stu-id="01008-207">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="01008-208">Defina o tempo de expiração do pré-vôo</span><span class="sxs-lookup"><span data-stu-id="01008-208">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="01008-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>é chamado `Startup.ConfigureServices`dentro .</span><span class="sxs-lookup"><span data-stu-id="01008-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="01008-210">Para algumas opções, pode ser útil ler a seção Como o [CORS funciona](#how-cors) primeiro.</span><span class="sxs-lookup"><span data-stu-id="01008-210">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="01008-211">Defina as origens permitidas</span><span class="sxs-lookup"><span data-stu-id="01008-211">Set the allowed origins</span></span>

<span data-ttu-id="01008-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Permite solicitações do CORS de`http` todas `https`as origens com qualquer esquema (ou ).</span><span class="sxs-lookup"><span data-stu-id="01008-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="01008-213">`AllowAnyOrigin`é inseguro porque *qualquer site* pode fazer solicitações de origem cruzada para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="01008-213">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="01008-214">Especificar `AllowAnyOrigin` `AllowCredentials` e é uma configuração insegura e pode resultar em falsificação de solicitação entre sites.</span><span class="sxs-lookup"><span data-stu-id="01008-214">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="01008-215">O serviço CORS retorna uma resposta CORS inválida quando um aplicativo é configurado com ambos os métodos.</span><span class="sxs-lookup"><span data-stu-id="01008-215">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="01008-216">`AllowAnyOrigin`afeta pedidos de pré-vôo e o `Access-Control-Allow-Origin` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="01008-216">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="01008-217">Para obter mais informações, consulte a seção [de solicitações de pré-vôo.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="01008-217">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="01008-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Define <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> a propriedade da diretiva como uma função que permite que as origens correspondam a um domínio curinga configurado ao avaliar se a origem é permitida.</span><span class="sxs-lookup"><span data-stu-id="01008-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="01008-219">Defina os métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="01008-219">Set the allowed HTTP methods</span></span>

<span data-ttu-id="01008-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="01008-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="01008-221">Permite qualquer método HTTP:</span><span class="sxs-lookup"><span data-stu-id="01008-221">Allows any HTTP method:</span></span>
* <span data-ttu-id="01008-222">Afeta as solicitações `Access-Control-Allow-Methods` de pré-vôo e o cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="01008-222">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="01008-223">Para obter mais informações, consulte a seção [de solicitações de pré-vôo.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="01008-223">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="01008-224">Defina os cabeçalhos de solicitação permitidos</span><span class="sxs-lookup"><span data-stu-id="01008-224">Set the allowed request headers</span></span>

<span data-ttu-id="01008-225">Para permitir que cabeçalhos específicos sejam enviados em uma <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> solicitação cors, chamados [cabeçalhos de solicitação do autor,](https://xhr.spec.whatwg.org/#request)ligue e especifique os cabeçalhos permitidos:</span><span class="sxs-lookup"><span data-stu-id="01008-225">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="01008-226">Para permitir que todos os <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> [cabeçalhos de solicitação do autor,](https://www.w3.org/TR/cors/#author-request-headers)ligue:</span><span class="sxs-lookup"><span data-stu-id="01008-226">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="01008-227">`AllowAnyHeader`afeta as solicitações de pré-vôo e o [cabeçalho access-control-Request-Headers.](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)</span><span class="sxs-lookup"><span data-stu-id="01008-227">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="01008-228">Para obter mais informações, consulte a seção [de solicitações de pré-vôo.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="01008-228">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="01008-229">Uma correspondência de diretiva CORS Middleware `WithHeaders` para cabeçalhos específicos `Access-Control-Request-Headers` especificados só é `WithHeaders`possível quando os cabeçalhos enviados correspondem exatamente aos cabeçalhos indicados em .</span><span class="sxs-lookup"><span data-stu-id="01008-229">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="01008-230">Por exemplo, considere um aplicativo configurado da seguinte forma:</span><span class="sxs-lookup"><span data-stu-id="01008-230">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="01008-231">O CORS Middleware recusa uma solicitação de `Content-Language` pré-vôo com o cabeçalho `WithHeaders`de solicitação a seguir porque[(HeaderNames.ContentLanguage)](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)não está listado em :</span><span class="sxs-lookup"><span data-stu-id="01008-231">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="01008-232">O aplicativo retorna uma resposta *de 200 OK,* mas não envia os cabeçalhos cors de volta.</span><span class="sxs-lookup"><span data-stu-id="01008-232">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="01008-233">Portanto, o navegador não tenta a solicitação de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="01008-233">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="01008-234">Defina os cabeçalhos de resposta expostos</span><span class="sxs-lookup"><span data-stu-id="01008-234">Set the exposed response headers</span></span>

<span data-ttu-id="01008-235">Por padrão, o navegador não expõe todos os cabeçalhos de resposta ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="01008-235">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="01008-236">Para obter mais informações, consulte [W3C Cross-Origin Resource Sharing (Terminologia): Simples Cabeçalho de Resposta](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="01008-236">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="01008-237">Os cabeçalhos de resposta disponíveis por padrão são:</span><span class="sxs-lookup"><span data-stu-id="01008-237">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="01008-238">A especificação cors chama esses cabeçalhos *de cabeçalho simples cabeçalhos de resposta*.</span><span class="sxs-lookup"><span data-stu-id="01008-238">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="01008-239">Para disponibilizar outros cabeçalhos <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>para o aplicativo, ligue para:</span><span class="sxs-lookup"><span data-stu-id="01008-239">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="01008-240">Credenciais em solicitações de origem cruzada</span><span class="sxs-lookup"><span data-stu-id="01008-240">Credentials in cross-origin requests</span></span>

<span data-ttu-id="01008-241">As credenciais requerem tratamento especial em uma solicitação do CORS.</span><span class="sxs-lookup"><span data-stu-id="01008-241">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="01008-242">Por padrão, o navegador não envia credenciais com uma solicitação de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="01008-242">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="01008-243">As credenciais incluem cookies e esquemas de autenticação HTTP.</span><span class="sxs-lookup"><span data-stu-id="01008-243">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="01008-244">Para enviar credenciais com uma solicitação de `XMLHttpRequest.withCredentials` `true`origem cruzada, o cliente deve definir para .</span><span class="sxs-lookup"><span data-stu-id="01008-244">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="01008-245">Usando `XMLHttpRequest` diretamente:</span><span class="sxs-lookup"><span data-stu-id="01008-245">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="01008-246">Usando jQuery:</span><span class="sxs-lookup"><span data-stu-id="01008-246">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="01008-247">Usando a [API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="01008-247">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="01008-248">O servidor deve permitir as credenciais.</span><span class="sxs-lookup"><span data-stu-id="01008-248">The server must allow the credentials.</span></span> <span data-ttu-id="01008-249">Para permitir credenciais de <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>origem cruzada, ligue para:</span><span class="sxs-lookup"><span data-stu-id="01008-249">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="01008-250">A resposta HTTP `Access-Control-Allow-Credentials` inclui um cabeçalho, que informa ao navegador que o servidor permite credenciais para uma solicitação de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="01008-250">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="01008-251">Se o navegador enviar credenciais, mas `Access-Control-Allow-Credentials` a resposta não incluir um cabeçalho válido, o navegador não exporá a resposta ao aplicativo e a solicitação de origem cruzada falhará.</span><span class="sxs-lookup"><span data-stu-id="01008-251">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="01008-252">Permitir credenciais de origem cruzada é um risco para a segurança.</span><span class="sxs-lookup"><span data-stu-id="01008-252">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="01008-253">Um site em outro domínio pode enviar credenciais de um usuário de entrada para o aplicativo em nome do usuário sem o conhecimento do usuário.</span><span class="sxs-lookup"><span data-stu-id="01008-253">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="01008-254">A especificação do CORS também `"*"` afirma que a configuração de `Access-Control-Allow-Credentials` origens para (todas as origens) é inválida se o cabeçalho estiver presente.</span><span class="sxs-lookup"><span data-stu-id="01008-254">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="01008-255">Solicitações de pré-vôo</span><span class="sxs-lookup"><span data-stu-id="01008-255">Preflight requests</span></span>

<span data-ttu-id="01008-256">Para algumas solicitações do CORS, o navegador envia uma solicitação de [OPÇÕES](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) adicionais antes de fazer a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="01008-256">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="01008-257">Este pedido é chamado de [solicitação de pré-vôo.](https://developer.mozilla.org/docs/Glossary/Preflight_request)</span><span class="sxs-lookup"><span data-stu-id="01008-257">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="01008-258">O navegador pode ignorar a solicitação de pré-vôo se ***todas as*** seguintes condições forem verdadeiras:</span><span class="sxs-lookup"><span data-stu-id="01008-258">The browser can skip the preflight request if ***all*** the following conditions are true:</span></span>

* <span data-ttu-id="01008-259">O método de solicitação é GET, HEAD ou POST.</span><span class="sxs-lookup"><span data-stu-id="01008-259">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="01008-260">O aplicativo não define cabeçalhos `Content-Language`de `Content-Type`solicitação `Last-Event-ID`que não senão, `Accept` `Accept-Language`ou .</span><span class="sxs-lookup"><span data-stu-id="01008-260">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="01008-261">O `Content-Type` cabeçalho, se definido, tem um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="01008-261">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="01008-262">A regra sobre cabeçalhos de solicitação definidos para a `setRequestHeader` solicitação `XMLHttpRequest` do cliente se aplica aos cabeçalhos que o aplicativo define chamando o objeto.</span><span class="sxs-lookup"><span data-stu-id="01008-262">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="01008-263">A especificação cors chama esses cabeçalhos [autor solicitar cabeçalhos](https://www.w3.org/TR/cors/#author-request-headers).</span><span class="sxs-lookup"><span data-stu-id="01008-263">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="01008-264">A regra não se aplica aos cabeçalhos `User-Agent` `Host`que `Content-Length`o navegador pode definir, tais como , ou .</span><span class="sxs-lookup"><span data-stu-id="01008-264">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="01008-265">A seguir, uma resposta de exemplo semelhante à solicitação de pré-vôo feita a partir do botão **[Colocar teste]** na seção [Test CORS](#testc) deste documento.</span><span class="sxs-lookup"><span data-stu-id="01008-265">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

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

<span data-ttu-id="01008-266">A solicitação de pré-vôo usa o método [HTTP OPTIONS.](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)</span><span class="sxs-lookup"><span data-stu-id="01008-266">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="01008-267">Pode incluir os seguintes cabeçalhos:</span><span class="sxs-lookup"><span data-stu-id="01008-267">It may include the following headers:</span></span>

* <span data-ttu-id="01008-268">[Método de solicitação de controle de acesso](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): O método HTTP que será usado para a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="01008-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="01008-269">[Cabeçalhos de solicitação de controle de acesso](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): Uma lista de cabeçalhos de solicitação que o aplicativo define na solicitação real.</span><span class="sxs-lookup"><span data-stu-id="01008-269">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="01008-270">Como dito anteriormente, isso não inclui cabeçalhos `User-Agent`que o navegador define, como .</span><span class="sxs-lookup"><span data-stu-id="01008-270">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="01008-271">Métodos de permitir controle de acesso</span><span class="sxs-lookup"><span data-stu-id="01008-271">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="01008-272">Se a solicitação de pré-vôo `200 OK` for negada, o aplicativo retorna uma resposta, mas não define os cabeçalhos cors.</span><span class="sxs-lookup"><span data-stu-id="01008-272">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="01008-273">Portanto, o navegador não tenta a solicitação de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="01008-273">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="01008-274">Para obter um exemplo de solicitação de pré-vôo negada, consulte a seção [Teste CORS](#testc) deste documento.</span><span class="sxs-lookup"><span data-stu-id="01008-274">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="01008-275">Usando as ferramentas f12, o aplicativo do console mostra um erro semelhante a um dos seguintes, dependendo do navegador:</span><span class="sxs-lookup"><span data-stu-id="01008-275">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="01008-276">Firefox: Solicitação de origem cruzada bloqueada: a mesma política de `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`origem não permite a leitura do recurso remoto em .</span><span class="sxs-lookup"><span data-stu-id="01008-276">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="01008-277">(Motivo: pedido do CORS não teve sucesso).</span><span class="sxs-lookup"><span data-stu-id="01008-277">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="01008-278">Saiba mais</span><span class="sxs-lookup"><span data-stu-id="01008-278">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="01008-279">Baseado em cromo: O acessohttps://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5para buscarhttps://cors3.azurewebsites.netem ' from origin ' foi bloqueado pela política cors: A resposta à solicitação de pré-vôo não passa pela verificação de controle de acesso: Nenhum cabeçalho 'Access-Control-Allow-Origin' está presente no recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="01008-279">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="01008-280">Se uma resposta opaca atender às suas necessidades, defina o modo da solicitação como 'no-cors' para buscar o recurso com o CORS desabilitado.</span><span class="sxs-lookup"><span data-stu-id="01008-280">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="01008-281">Para permitir cabeçalhos específicos, ligue para: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*></span><span class="sxs-lookup"><span data-stu-id="01008-281">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="01008-282">Para permitir que todos os <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> [cabeçalhos de solicitação do autor,](https://www.w3.org/TR/cors/#author-request-headers)ligue:</span><span class="sxs-lookup"><span data-stu-id="01008-282">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="01008-283">Os navegadores não são consistentes na forma como definem `Access-Control-Request-Headers`.</span><span class="sxs-lookup"><span data-stu-id="01008-283">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="01008-284">Se um deles:</span><span class="sxs-lookup"><span data-stu-id="01008-284">If either:</span></span>

* <span data-ttu-id="01008-285">Cabeçalhos são definidos para qualquer outra coisa que não`"*"`</span><span class="sxs-lookup"><span data-stu-id="01008-285">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="01008-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>é chamado: Incluir `Accept` `Content-Type`pelo `Origin`menos , e , além de quaisquer cabeçalhos personalizados que você deseja suportar.</span><span class="sxs-lookup"><span data-stu-id="01008-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="01008-287">Código automático de solicitação de pré-vôo</span><span class="sxs-lookup"><span data-stu-id="01008-287">Automatic preflight request code</span></span>

<span data-ttu-id="01008-288">Quando a política cors é aplicada ou:</span><span class="sxs-lookup"><span data-stu-id="01008-288">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="01008-289">Globalmente, `app.UseCors` `Startup.Configure`chamando.</span><span class="sxs-lookup"><span data-stu-id="01008-289">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="01008-290">Usando `[EnableCors]` o atributo.</span><span class="sxs-lookup"><span data-stu-id="01008-290">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="01008-291">ASP.NET Core responde à solicitação de OPÇÕES de pré-vôo.</span><span class="sxs-lookup"><span data-stu-id="01008-291">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="01008-292">A ativação do CORS por `RequireCors` ponto final usando atualmente ***não*** suporta solicitações automáticas de pré-vôo.</span><span class="sxs-lookup"><span data-stu-id="01008-292">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support automatic preflight requests.</span></span>

<span data-ttu-id="01008-293">A seção [Teste CORS](#testc) deste documento demonstra esse comportamento.</span><span class="sxs-lookup"><span data-stu-id="01008-293">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="01008-294">[HttpOptions] atributo para solicitações de pré-vôo</span><span class="sxs-lookup"><span data-stu-id="01008-294">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="01008-295">Quando o CORS é habilitado com a política apropriada, ASP.NET Core geralmente responde automaticamente às solicitações de pré-vôo do CORS.</span><span class="sxs-lookup"><span data-stu-id="01008-295">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="01008-296">Em alguns cenários, isso pode não ser o caso.</span><span class="sxs-lookup"><span data-stu-id="01008-296">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="01008-297">Por exemplo, usar [o CORS com roteamento de ponto final](#ecors).</span><span class="sxs-lookup"><span data-stu-id="01008-297">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="01008-298">O código a seguir usa o atributo [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) para criar pontos finais para solicitações de OPÇÕES:</span><span class="sxs-lookup"><span data-stu-id="01008-298">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="01008-299">Consulte [Test CORS com roteamento de ponto final e [HttpOptions]](#tcer) para obter instruções sobre o teste do código anterior.</span><span class="sxs-lookup"><span data-stu-id="01008-299">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="01008-300">Defina o tempo de expiração do pré-vôo</span><span class="sxs-lookup"><span data-stu-id="01008-300">Set the preflight expiration time</span></span>

<span data-ttu-id="01008-301">O `Access-Control-Max-Age` cabeçalho especifica quanto tempo a resposta à solicitação de pré-vôo pode ser armazenada em cache.</span><span class="sxs-lookup"><span data-stu-id="01008-301">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="01008-302">Para definir este <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>cabeçalho, ligue:</span><span class="sxs-lookup"><span data-stu-id="01008-302">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="01008-303">Como funciona o CORS</span><span class="sxs-lookup"><span data-stu-id="01008-303">How CORS works</span></span>

<span data-ttu-id="01008-304">Esta seção descreve o que acontece em uma solicitação [cors](https://developer.mozilla.org/docs/Web/HTTP/CORS) no nível das mensagens HTTP.</span><span class="sxs-lookup"><span data-stu-id="01008-304">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="01008-305">Cors **não** é um recurso de segurança.</span><span class="sxs-lookup"><span data-stu-id="01008-305">CORS is **not** a security feature.</span></span> <span data-ttu-id="01008-306">Cors é um padrão W3C que permite que um servidor relaxe a política de mesma origem.</span><span class="sxs-lookup"><span data-stu-id="01008-306">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="01008-307">Por exemplo, um ator mal-intencionado poderia usar [o Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) contra seu site e executar uma solicitação entre sites para o seu site habilitado para cors para roubar informações.</span><span class="sxs-lookup"><span data-stu-id="01008-307">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="01008-308">Uma API não é mais segura permitindo o CORS.</span><span class="sxs-lookup"><span data-stu-id="01008-308">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="01008-309">Cabe ao cliente (navegador) fazer valer o CORS.</span><span class="sxs-lookup"><span data-stu-id="01008-309">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="01008-310">O servidor executa a solicitação e retorna a resposta, é o cliente que retorna um erro e bloqueia a resposta.</span><span class="sxs-lookup"><span data-stu-id="01008-310">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="01008-311">Por exemplo, qualquer uma das seguintes ferramentas exibirá a resposta do servidor:</span><span class="sxs-lookup"><span data-stu-id="01008-311">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="01008-312">Fiddler</span><span class="sxs-lookup"><span data-stu-id="01008-312">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="01008-313">Postman</span><span class="sxs-lookup"><span data-stu-id="01008-313">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="01008-314">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="01008-314">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="01008-315">Um navegador da Web inserindo a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="01008-315">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="01008-316">É uma maneira de um servidor permitir que os navegadores executem um [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) de origem cruzada ou solicitação [de API de origem cruzada](https://developer.mozilla.org/docs/Web/API/Fetch_API) que de outra forma seria proibido.</span><span class="sxs-lookup"><span data-stu-id="01008-316">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="01008-317">Navegadores sem CORS não podem fazer solicitações de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="01008-317">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="01008-318">Antes do CORS, [o JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) era usado para contornar essa restrição.</span><span class="sxs-lookup"><span data-stu-id="01008-318">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="01008-319">JSONP não usa XHR, usa `<script>` a tag para receber a resposta.</span><span class="sxs-lookup"><span data-stu-id="01008-319">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="01008-320">Os scripts podem ser carregados de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="01008-320">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="01008-321">A [especificação CORS](https://www.w3.org/TR/cors/) introduziu vários novos cabeçalhos HTTP que permitem solicitações de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="01008-321">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="01008-322">Se um navegador suportar o CORS, ele define esses cabeçalhos automaticamente para solicitações de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="01008-322">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="01008-323">O código JavaScript personalizado não é necessário para ativar o CORS.</span><span class="sxs-lookup"><span data-stu-id="01008-323">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="01008-324">O [botão de teste PUT](https://cors3.azurewebsites.net/test) na [amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) implantada</span><span class="sxs-lookup"><span data-stu-id="01008-324">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="01008-325">A seguir, um exemplo de uma solicitação de `https://cors1.azurewebsites.net/api/values`origem cruzada do botão de teste [Valores](https://cors3.azurewebsites.net/) para .</span><span class="sxs-lookup"><span data-stu-id="01008-325">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="01008-326">O `Origin` cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="01008-326">The `Origin` header:</span></span>

* <span data-ttu-id="01008-327">Fornece o domínio do site que está fazendo a solicitação.</span><span class="sxs-lookup"><span data-stu-id="01008-327">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="01008-328">É necessário e deve ser diferente do hospedeiro.</span><span class="sxs-lookup"><span data-stu-id="01008-328">Is required and must be different from the host.</span></span>

<span data-ttu-id="01008-329">**Cabeçalhos gerais**</span><span class="sxs-lookup"><span data-stu-id="01008-329">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="01008-330">**Cabeçalhos de resposta**</span><span class="sxs-lookup"><span data-stu-id="01008-330">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="01008-331">**Cabeçalhos da solicitação**</span><span class="sxs-lookup"><span data-stu-id="01008-331">**Request headers**</span></span>

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

<span data-ttu-id="01008-332">Nas `OPTIONS` solicitações, o servidor define o **cabeçalho de cabeçalho de** `Access-Control-Allow-Origin: {allowed origin}` resposta na resposta.</span><span class="sxs-lookup"><span data-stu-id="01008-332">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="01008-333">Por exemplo, a solicitação de botão `OPTIONS` ['Excluir ' 'EnableCors]](https://cors1.azurewebsites.net/test?number=2) de [amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)implantada contém os seguintes cabeçalhos:</span><span class="sxs-lookup"><span data-stu-id="01008-333">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="01008-334">**Cabeçalhos gerais**</span><span class="sxs-lookup"><span data-stu-id="01008-334">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="01008-335">**Cabeçalhos de resposta**</span><span class="sxs-lookup"><span data-stu-id="01008-335">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="01008-336">**Cabeçalhos da solicitação**</span><span class="sxs-lookup"><span data-stu-id="01008-336">**Request headers**</span></span>

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

<span data-ttu-id="01008-337">Nos **cabeçalhos de resposta anteriores,** o servidor define o [cabeçalho Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) na resposta.</span><span class="sxs-lookup"><span data-stu-id="01008-337">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="01008-338">O `https://cors1.azurewebsites.net` valor deste cabeçalho corresponde ao `Origin` cabeçalho da solicitação.</span><span class="sxs-lookup"><span data-stu-id="01008-338">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="01008-339">Se <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> for chamado, o `Access-Control-Allow-Origin: *`valor do curinga é devolvido.</span><span class="sxs-lookup"><span data-stu-id="01008-339">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="01008-340">`AllowAnyOrigin`permite qualquer origem.</span><span class="sxs-lookup"><span data-stu-id="01008-340">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="01008-341">Se a resposta não `Access-Control-Allow-Origin` incluir o cabeçalho, a solicitação de origem cruzada falhará.</span><span class="sxs-lookup"><span data-stu-id="01008-341">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="01008-342">Especificamente, o navegador não permite a solicitação.</span><span class="sxs-lookup"><span data-stu-id="01008-342">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="01008-343">Mesmo que o servidor retorne uma resposta bem-sucedida, o navegador não disponibiliza a resposta para o aplicativo cliente.</span><span class="sxs-lookup"><span data-stu-id="01008-343">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="01008-344">Solicitações de opções de exibição</span><span class="sxs-lookup"><span data-stu-id="01008-344">Display OPTIONS requests</span></span>

<span data-ttu-id="01008-345">Por padrão, os navegadores Chrome e Edge não mostram solicitações OPTIONS na guia de rede das ferramentas F12.</span><span class="sxs-lookup"><span data-stu-id="01008-345">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="01008-346">Para exibir solicitações OPTIONS nesses navegadores:</span><span class="sxs-lookup"><span data-stu-id="01008-346">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="01008-347">`chrome://flags/#out-of-blink-cors` ou `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="01008-347">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="01008-348">desativar a bandeira.</span><span class="sxs-lookup"><span data-stu-id="01008-348">disable the flag.</span></span>
* <span data-ttu-id="01008-349">Reiniciar.</span><span class="sxs-lookup"><span data-stu-id="01008-349">restart.</span></span>

<span data-ttu-id="01008-350">O Firefox mostra as solicitações OPTIONS por padrão.</span><span class="sxs-lookup"><span data-stu-id="01008-350">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="01008-351">CORS no IIS</span><span class="sxs-lookup"><span data-stu-id="01008-351">CORS in IIS</span></span>

<span data-ttu-id="01008-352">Ao implantar no IIS, o CORS deve ser executado antes da Autenticação do Windows se o servidor não estiver configurado para permitir acesso anônimo.</span><span class="sxs-lookup"><span data-stu-id="01008-352">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="01008-353">Para suportar esse cenário, o [módulo IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) precisa ser instalado e configurado para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="01008-353">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="01008-354">Testar o CORS</span><span class="sxs-lookup"><span data-stu-id="01008-354">Test CORS</span></span>

<span data-ttu-id="01008-355">O [download da amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) tem código para testar o CORS.</span><span class="sxs-lookup"><span data-stu-id="01008-355">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="01008-356">Consulte [como baixar](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="01008-356">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="01008-357">A amostra é um projeto de API com páginas de barbear adicionadas:</span><span class="sxs-lookup"><span data-stu-id="01008-357">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="01008-358">`WithOrigins("https://localhost:<port>");`só deve ser usado para testar um aplicativo de amostra semelhante ao [código de amostra de download](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="01008-358">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="01008-359">O `ValuesController` seguinte fornece os pontos finais para testes:</span><span class="sxs-lookup"><span data-stu-id="01008-359">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="01008-360">[O MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) é fornecido pelo pacote [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet e exibe informações de rota.</span><span class="sxs-lookup"><span data-stu-id="01008-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="01008-361">Teste o código de amostra anterior usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="01008-361">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="01008-362">Use o aplicativo [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)de amostra implantado em .</span><span class="sxs-lookup"><span data-stu-id="01008-362">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="01008-363">Não há necessidade de baixar a amostra.</span><span class="sxs-lookup"><span data-stu-id="01008-363">There is no need to download the sample.</span></span>
* <span data-ttu-id="01008-364">Execute a `dotnet run` amostra usando a `https://localhost:5001`URL padrão de .</span><span class="sxs-lookup"><span data-stu-id="01008-364">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="01008-365">Execute a amostra do Visual Studio com a porta definida `https://localhost:44398`como 44398 para uma URL de .</span><span class="sxs-lookup"><span data-stu-id="01008-365">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="01008-366">Usando um navegador com as ferramentas f12:</span><span class="sxs-lookup"><span data-stu-id="01008-366">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="01008-367">Selecione o botão **Valores** e revise os cabeçalhos na guia **Rede.**</span><span class="sxs-lookup"><span data-stu-id="01008-367">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="01008-368">Selecione o botão **de teste PUT.**</span><span class="sxs-lookup"><span data-stu-id="01008-368">Select the **PUT test** button.</span></span> <span data-ttu-id="01008-369">Consulte [as solicitações de opções de exibição](#options) para exibir a solicitação OPÇÕES.</span><span class="sxs-lookup"><span data-stu-id="01008-369">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="01008-370">O **teste PUT** cria duas solicitações, uma solicitação de pré-vôo OPÇÕES e a solicitação PUT.</span><span class="sxs-lookup"><span data-stu-id="01008-370">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="01008-371">Selecione **`GetValues2 [DisableCors]`** o botão para acionar uma solicitação CORS com falha.</span><span class="sxs-lookup"><span data-stu-id="01008-371">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="01008-372">Como mencionado no documento, a resposta retorna 200 de sucesso, mas a solicitação do CORS não é feita.</span><span class="sxs-lookup"><span data-stu-id="01008-372">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="01008-373">Selecione a guia **Console** para ver o erro cors.</span><span class="sxs-lookup"><span data-stu-id="01008-373">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="01008-374">Dependendo do navegador, um erro semelhante ao seguinte é exibido:</span><span class="sxs-lookup"><span data-stu-id="01008-374">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="01008-375">O acesso `'https://cors1.azurewebsites.net/api/values/GetValues2'` ao `'https://cors3.azurewebsites.net'` fetch at from origin foi bloqueado pela política cors: Nenhum cabeçalho 'Access-Control-Allow-Origin' está presente no recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="01008-375">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="01008-376">Se uma resposta opaca atender às suas necessidades, defina o modo da solicitação como 'no-cors' para buscar o recurso com o CORS desabilitado.</span><span class="sxs-lookup"><span data-stu-id="01008-376">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="01008-377">Os pontos finais habilitados para CORS podem ser testados com uma ferramenta, como [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)ou [Carteiro](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="01008-377">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="01008-378">Ao usar uma ferramenta, a origem da `Origin` solicitação especificada pelo cabeçalho deve diferir do host que recebe a solicitação.</span><span class="sxs-lookup"><span data-stu-id="01008-378">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="01008-379">Se a solicitação não *for de origem* cruzada `Origin` com base no valor do cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="01008-379">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="01008-380">Não há necessidade de o CORS Middleware processar a solicitação.</span><span class="sxs-lookup"><span data-stu-id="01008-380">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="01008-381">Os cabeçalhos cors não são devolvidos na resposta.</span><span class="sxs-lookup"><span data-stu-id="01008-381">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="01008-382">O comando `curl` a seguir é use para emitir uma solicitação DE OPÇÕES com informações:</span><span class="sxs-lookup"><span data-stu-id="01008-382">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="01008-383">Teste o CORS com roteamento de ponto final e [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="01008-383">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="01008-384">A habilitação do CORS por `RequireCors` ponto final usando atualmente ***não*** suporta [solicitações automáticas de pré-vôo](#apf).</span><span class="sxs-lookup"><span data-stu-id="01008-384">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="01008-385">Considere o seguinte código que usa [roteamento de ponto final para habilitar o CORS](#ecors):</span><span class="sxs-lookup"><span data-stu-id="01008-385">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="01008-386">O `TodoItems1Controller` seguinte fornece pontos finais para testes:</span><span class="sxs-lookup"><span data-stu-id="01008-386">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="01008-387">Teste o código anterior da página de [teste](https://cors1.azurewebsites.net/test?number=1) da [amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)implantada .</span><span class="sxs-lookup"><span data-stu-id="01008-387">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="01008-388">Os botões **Delete [EnableCors]** e **GET [EnableCors]** são `[EnableCors]` bem sucedidos, pois os pontos finais têm e respondem às solicitações de pré-vôo.</span><span class="sxs-lookup"><span data-stu-id="01008-388">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="01008-389">Os outros pontos finais falham.</span><span class="sxs-lookup"><span data-stu-id="01008-389">The other endpoints fails.</span></span> <span data-ttu-id="01008-390">O botão **GET** falha, porque o [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) envia:</span><span class="sxs-lookup"><span data-stu-id="01008-390">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="01008-391">O `TodoItems2Controller` seguinte fornece pontos finais semelhantes, mas inclui código explícito para responder às solicitações OPTIONS:</span><span class="sxs-lookup"><span data-stu-id="01008-391">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="01008-392">Teste o código anterior da página de [teste](https://cors1.azurewebsites.net/test?number=2) da amostra implantada.</span><span class="sxs-lookup"><span data-stu-id="01008-392">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="01008-393">Na lista de paradas **do Controlador,** selecione **Preflight** e, em seguida, **Defina controlador**.</span><span class="sxs-lookup"><span data-stu-id="01008-393">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="01008-394">Todas as chamadas do `TodoItems2Controller` CORS para os pontos finais são bem sucedidas.</span><span class="sxs-lookup"><span data-stu-id="01008-394">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="01008-395">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="01008-395">Additional resources</span></span>

* [<span data-ttu-id="01008-396">CORS (Compartilhamento de Recursos entre Origens)</span><span class="sxs-lookup"><span data-stu-id="01008-396">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="01008-397">Começando com o módulo IIS CORS</span><span class="sxs-lookup"><span data-stu-id="01008-397">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="01008-398">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="01008-398">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="01008-399">Este artigo mostra como ativar o CORS em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="01008-399">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="01008-400">A segurança do navegador impede que uma página da Web seja feita para um domínio diferente daquele que serviu a página da Web.</span><span class="sxs-lookup"><span data-stu-id="01008-400">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="01008-401">Essa restrição é chamada *de política de mesma origem.*</span><span class="sxs-lookup"><span data-stu-id="01008-401">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="01008-402">A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site.</span><span class="sxs-lookup"><span data-stu-id="01008-402">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="01008-403">Às vezes, você pode querer permitir que outros sites façam solicitações de origem cruzada para o seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="01008-403">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="01008-404">Para obter mais informações, consulte o artigo da [Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="01008-404">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="01008-405">[Compartilhamento de recursos de origem cruzada](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="01008-405">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="01008-406">É um padrão W3C que permite que um servidor relaxe a política de mesma origem.</span><span class="sxs-lookup"><span data-stu-id="01008-406">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="01008-407">**Não** é um recurso de segurança, o CORS relaxa a segurança.</span><span class="sxs-lookup"><span data-stu-id="01008-407">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="01008-408">Uma API não é mais segura ao permitir o CORS.</span><span class="sxs-lookup"><span data-stu-id="01008-408">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="01008-409">Para obter mais informações, consulte [Como funciona o CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="01008-409">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="01008-410">Permite que um servidor permita explicitamente algumas solicitações de origem cruzada enquanto rejeita outros.</span><span class="sxs-lookup"><span data-stu-id="01008-410">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="01008-411">É mais seguro e flexível do que as técnicas anteriores, como [jsonp](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="01008-411">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="01008-412">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="01008-412">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="01008-413">Mesma origem</span><span class="sxs-lookup"><span data-stu-id="01008-413">Same origin</span></span>

<span data-ttu-id="01008-414">Dois URLs têm a mesma origem se tiverem esquemas, hosts e portas idênticos ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="01008-414">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="01008-415">Estes dois URLs têm a mesma origem:</span><span class="sxs-lookup"><span data-stu-id="01008-415">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="01008-416">Esses URLs têm origens diferentes das duas URLs anteriores:</span><span class="sxs-lookup"><span data-stu-id="01008-416">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="01008-417">`https://example.net`&ndash; Domínio diferente</span><span class="sxs-lookup"><span data-stu-id="01008-417">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="01008-418">`https://www.example.com/foo.html`&ndash; Subdomínio diferente</span><span class="sxs-lookup"><span data-stu-id="01008-418">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="01008-419">`http://example.com/foo.html`&ndash; Esquema diferente</span><span class="sxs-lookup"><span data-stu-id="01008-419">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="01008-420">`https://example.com:9000/foo.html`&ndash; Porta diferente</span><span class="sxs-lookup"><span data-stu-id="01008-420">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="01008-421">O Internet Explorer não considera a porta ao comparar origens.</span><span class="sxs-lookup"><span data-stu-id="01008-421">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="01008-422">CORS com política nomeada e middleware</span><span class="sxs-lookup"><span data-stu-id="01008-422">CORS with named policy and middleware</span></span>

<span data-ttu-id="01008-423">O CORS Middleware lida com solicitações de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="01008-423">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="01008-424">O código a seguir permite o CORS para todo o aplicativo com a origem especificada:</span><span class="sxs-lookup"><span data-stu-id="01008-424">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="01008-425">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="01008-425">The preceding code:</span></span>

* <span data-ttu-id="01008-426">Define o nome\_da política como "myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="01008-426">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="01008-427">O nome da política é arbitrário.</span><span class="sxs-lookup"><span data-stu-id="01008-427">The policy name is arbitrary.</span></span>
* <span data-ttu-id="01008-428">Chama <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> o método de extensão, que habilita o CORS.</span><span class="sxs-lookup"><span data-stu-id="01008-428">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="01008-429">Chamadas <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> com uma [expressão lambda.](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)</span><span class="sxs-lookup"><span data-stu-id="01008-429">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="01008-430">A lambda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> leva um objeto.</span><span class="sxs-lookup"><span data-stu-id="01008-430">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="01008-431">[As opções de configuração,](#cors-policy-options)como `WithOrigins`, são descritas posteriormente neste artigo.</span><span class="sxs-lookup"><span data-stu-id="01008-431">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="01008-432">A <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> chamada do método adiciona serviços CORS ao contêiner de serviço do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="01008-432">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="01008-433">Para obter mais informações, consulte [as opções de política](#cpo) do CORS neste documento .</span><span class="sxs-lookup"><span data-stu-id="01008-433">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="01008-434">O <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> método pode acorrentar métodos, conforme mostrado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="01008-434">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="01008-435">Nota: A URL **não** deve`/`conter uma barra de arrasto ().</span><span class="sxs-lookup"><span data-stu-id="01008-435">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="01008-436">Se a URL `/`terminar com `false` , a comparação retorna e nenhum cabeçalho é devolvido.</span><span class="sxs-lookup"><span data-stu-id="01008-436">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="01008-437">O código a seguir aplica as políticas cors a todos os pontos finais de aplicativos via CORS Middleware:</span><span class="sxs-lookup"><span data-stu-id="01008-437">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="01008-438">Nota: `UseCors` deve `UseMvc`ser chamado antes .</span><span class="sxs-lookup"><span data-stu-id="01008-438">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="01008-439">Consulte [Ativar cors em páginas de barbear, controladores e métodos de ação](#ecors) para aplicar a política CORS no nível de página/controlador/ação.</span><span class="sxs-lookup"><span data-stu-id="01008-439">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="01008-440">Consulte [Test CORS](#test) para obter instruções sobre o código de teste semelhante ao código anterior.</span><span class="sxs-lookup"><span data-stu-id="01008-440">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="01008-441">Habilite o CORS com atributos</span><span class="sxs-lookup"><span data-stu-id="01008-441">Enable CORS with attributes</span></span>

<span data-ttu-id="01008-442">O atributo [ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) fornece uma alternativa à aplicação do CORS globalmente.</span><span class="sxs-lookup"><span data-stu-id="01008-442">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="01008-443">O `[EnableCors]` atributo habilita o CORS para pontos finais selecionados, em vez de todos os pontos finais.</span><span class="sxs-lookup"><span data-stu-id="01008-443">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="01008-444">Use `[EnableCors]` para especificar `[EnableCors("{Policy String}")]` a política padrão e para especificar uma diretiva.</span><span class="sxs-lookup"><span data-stu-id="01008-444">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="01008-445">O `[EnableCors]` atributo pode ser aplicado a:</span><span class="sxs-lookup"><span data-stu-id="01008-445">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="01008-446">Página navalha`PageModel`</span><span class="sxs-lookup"><span data-stu-id="01008-446">Razor Page `PageModel`</span></span>
* <span data-ttu-id="01008-447">Controller</span><span class="sxs-lookup"><span data-stu-id="01008-447">Controller</span></span>
* <span data-ttu-id="01008-448">Método de ação do controlador</span><span class="sxs-lookup"><span data-stu-id="01008-448">Controller action method</span></span>

<span data-ttu-id="01008-449">Você pode aplicar diferentes políticas ao controlador/modelo de página/ação com o atributo. `[EnableCors]`</span><span class="sxs-lookup"><span data-stu-id="01008-449">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="01008-450">Quando `[EnableCors]` o atributo é aplicado a um método de controle/modelo/ação de página e o CORS é habilitado em middleware, ***ambas as*** políticas são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="01008-450">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="01008-451">Recomendamos ***não*** combinar políticas.</span><span class="sxs-lookup"><span data-stu-id="01008-451">We recommend ***not*** combining policies.</span></span> <span data-ttu-id="01008-452">Use `[EnableCors]` o atributo ou middleware, \***não ambos**.</span><span class="sxs-lookup"><span data-stu-id="01008-452">Use the `[EnableCors]` attribute or middleware, \***not both**.</span></span> <span data-ttu-id="01008-453">Ao `[EnableCors]`usar, **não** defina uma política padrão.</span><span class="sxs-lookup"><span data-stu-id="01008-453">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="01008-454">O código a seguir aplica uma política diferente a cada método:</span><span class="sxs-lookup"><span data-stu-id="01008-454">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="01008-455">O código a seguir cria uma política `"AnotherPolicy"`padrão cors e uma diretiva chamada :</span><span class="sxs-lookup"><span data-stu-id="01008-455">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="01008-456">Desativar o CORS</span><span class="sxs-lookup"><span data-stu-id="01008-456">Disable CORS</span></span>

<span data-ttu-id="01008-457">O atributo [ &lbrack;&rbrack; DisableCors desativa](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) o CORS para o controlador/modelo de página/ação.</span><span class="sxs-lookup"><span data-stu-id="01008-457">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="01008-458">Opções de política do CORS</span><span class="sxs-lookup"><span data-stu-id="01008-458">CORS policy options</span></span>

<span data-ttu-id="01008-459">Esta seção descreve as várias opções que podem ser definidas em uma política cors:</span><span class="sxs-lookup"><span data-stu-id="01008-459">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="01008-460">Defina as origens permitidas</span><span class="sxs-lookup"><span data-stu-id="01008-460">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="01008-461">Defina os métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="01008-461">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="01008-462">Defina os cabeçalhos de solicitação permitidos</span><span class="sxs-lookup"><span data-stu-id="01008-462">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="01008-463">Defina os cabeçalhos de resposta expostos</span><span class="sxs-lookup"><span data-stu-id="01008-463">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="01008-464">Credenciais em solicitações de origem cruzada</span><span class="sxs-lookup"><span data-stu-id="01008-464">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="01008-465">Defina o tempo de expiração do pré-vôo</span><span class="sxs-lookup"><span data-stu-id="01008-465">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="01008-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>é chamado `Startup.ConfigureServices`dentro .</span><span class="sxs-lookup"><span data-stu-id="01008-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="01008-467">Para algumas opções, pode ser útil ler a seção Como o [CORS funciona](#how-cors) primeiro.</span><span class="sxs-lookup"><span data-stu-id="01008-467">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="01008-468">Defina as origens permitidas</span><span class="sxs-lookup"><span data-stu-id="01008-468">Set the allowed origins</span></span>

<span data-ttu-id="01008-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Permite solicitações do CORS de`http` todas `https`as origens com qualquer esquema (ou ).</span><span class="sxs-lookup"><span data-stu-id="01008-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="01008-470">`AllowAnyOrigin`é inseguro porque *qualquer site* pode fazer solicitações de origem cruzada para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="01008-470">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="01008-471">Especificar `AllowAnyOrigin` `AllowCredentials` e é uma configuração insegura e pode resultar em falsificação de solicitação entre sites.</span><span class="sxs-lookup"><span data-stu-id="01008-471">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="01008-472">Para um aplicativo seguro, especifique uma lista exata de origens se o cliente deve se autorizar a acessar recursos do servidor.</span><span class="sxs-lookup"><span data-stu-id="01008-472">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="01008-473">`AllowAnyOrigin`afeta pedidos de pré-vôo e o `Access-Control-Allow-Origin` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="01008-473">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="01008-474">Para obter mais informações, consulte a seção [de solicitações de pré-vôo.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="01008-474">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="01008-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Define <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> a propriedade da diretiva como uma função que permite que as origens correspondam a um domínio curinga configurado ao avaliar se a origem é permitida.</span><span class="sxs-lookup"><span data-stu-id="01008-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="01008-476">Defina os métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="01008-476">Set the allowed HTTP methods</span></span>

<span data-ttu-id="01008-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="01008-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="01008-478">Permite qualquer método HTTP:</span><span class="sxs-lookup"><span data-stu-id="01008-478">Allows any HTTP method:</span></span>
* <span data-ttu-id="01008-479">Afeta as solicitações `Access-Control-Allow-Methods` de pré-vôo e o cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="01008-479">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="01008-480">Para obter mais informações, consulte a seção [de solicitações de pré-vôo.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="01008-480">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="01008-481">Defina os cabeçalhos de solicitação permitidos</span><span class="sxs-lookup"><span data-stu-id="01008-481">Set the allowed request headers</span></span>

<span data-ttu-id="01008-482">Para permitir que cabeçalhos específicos sejam enviados em uma <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> solicitação cors, chamados *cabeçalhos de solicitação do autor,* ligue e especifique os cabeçalhos permitidos:</span><span class="sxs-lookup"><span data-stu-id="01008-482">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="01008-483">Para permitir que todos os <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>cabeçalhos de solicitação do autor solicitem:</span><span class="sxs-lookup"><span data-stu-id="01008-483">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="01008-484">Esta configuração afeta as `Access-Control-Request-Headers` solicitações de pré-vôo e o cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="01008-484">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="01008-485">Para obter mais informações, consulte a seção [de solicitações de pré-vôo.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="01008-485">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="01008-486">O CORS Middleware sempre permite `Access-Control-Request-Headers` que quatro cabeçalhos no ser enviado, independentemente dos valores configurados em CorsPolicy.Headers.</span><span class="sxs-lookup"><span data-stu-id="01008-486">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="01008-487">Esta lista de cabeçalhos inclui:</span><span class="sxs-lookup"><span data-stu-id="01008-487">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="01008-488">Por exemplo, considere um aplicativo configurado da seguinte forma:</span><span class="sxs-lookup"><span data-stu-id="01008-488">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="01008-489">O CORS Middleware responde com sucesso a uma `Content-Language` solicitação de pré-vôo com o seguinte cabeçalho de solicitação porque está sempre listado em branco:</span><span class="sxs-lookup"><span data-stu-id="01008-489">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="01008-490">Defina os cabeçalhos de resposta expostos</span><span class="sxs-lookup"><span data-stu-id="01008-490">Set the exposed response headers</span></span>

<span data-ttu-id="01008-491">Por padrão, o navegador não expõe todos os cabeçalhos de resposta ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="01008-491">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="01008-492">Para obter mais informações, consulte [W3C Cross-Origin Resource Sharing (Terminologia): Simples Cabeçalho de Resposta](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="01008-492">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="01008-493">Os cabeçalhos de resposta disponíveis por padrão são:</span><span class="sxs-lookup"><span data-stu-id="01008-493">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="01008-494">A especificação cors chama esses cabeçalhos *de cabeçalho simples cabeçalhos de resposta*.</span><span class="sxs-lookup"><span data-stu-id="01008-494">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="01008-495">Para disponibilizar outros cabeçalhos <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>para o aplicativo, ligue para:</span><span class="sxs-lookup"><span data-stu-id="01008-495">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="01008-496">Credenciais em solicitações de origem cruzada</span><span class="sxs-lookup"><span data-stu-id="01008-496">Credentials in cross-origin requests</span></span>

<span data-ttu-id="01008-497">As credenciais requerem tratamento especial em uma solicitação do CORS.</span><span class="sxs-lookup"><span data-stu-id="01008-497">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="01008-498">Por padrão, o navegador não envia credenciais com uma solicitação de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="01008-498">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="01008-499">As credenciais incluem cookies e esquemas de autenticação HTTP.</span><span class="sxs-lookup"><span data-stu-id="01008-499">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="01008-500">Para enviar credenciais com uma solicitação de `XMLHttpRequest.withCredentials` `true`origem cruzada, o cliente deve definir para .</span><span class="sxs-lookup"><span data-stu-id="01008-500">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="01008-501">Usando `XMLHttpRequest` diretamente:</span><span class="sxs-lookup"><span data-stu-id="01008-501">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="01008-502">Usando jQuery:</span><span class="sxs-lookup"><span data-stu-id="01008-502">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="01008-503">Usando a [API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="01008-503">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="01008-504">O servidor deve permitir as credenciais.</span><span class="sxs-lookup"><span data-stu-id="01008-504">The server must allow the credentials.</span></span> <span data-ttu-id="01008-505">Para permitir credenciais de <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>origem cruzada, ligue para:</span><span class="sxs-lookup"><span data-stu-id="01008-505">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="01008-506">A resposta HTTP `Access-Control-Allow-Credentials` inclui um cabeçalho, que informa ao navegador que o servidor permite credenciais para uma solicitação de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="01008-506">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="01008-507">Se o navegador enviar credenciais, mas `Access-Control-Allow-Credentials` a resposta não incluir um cabeçalho válido, o navegador não exporá a resposta ao aplicativo e a solicitação de origem cruzada falhará.</span><span class="sxs-lookup"><span data-stu-id="01008-507">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="01008-508">Permitir credenciais de origem cruzada é um risco para a segurança.</span><span class="sxs-lookup"><span data-stu-id="01008-508">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="01008-509">Um site em outro domínio pode enviar credenciais de um usuário de entrada para o aplicativo em nome do usuário sem o conhecimento do usuário.</span><span class="sxs-lookup"><span data-stu-id="01008-509">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="01008-510">A especificação do CORS também `"*"` afirma que a configuração de `Access-Control-Allow-Credentials` origens para (todas as origens) é inválida se o cabeçalho estiver presente.</span><span class="sxs-lookup"><span data-stu-id="01008-510">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="01008-511">Solicitações de pré-vôo</span><span class="sxs-lookup"><span data-stu-id="01008-511">Preflight requests</span></span>

<span data-ttu-id="01008-512">Para algumas solicitações do CORS, o navegador envia uma solicitação adicional antes de fazer a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="01008-512">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="01008-513">Este pedido é chamado de *solicitação de pré-vôo.*</span><span class="sxs-lookup"><span data-stu-id="01008-513">This request is called a *preflight request*.</span></span> <span data-ttu-id="01008-514">O navegador pode ignorar a solicitação de pré-vôo se as seguintes condições forem verdadeiras:</span><span class="sxs-lookup"><span data-stu-id="01008-514">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="01008-515">O método de solicitação é GET, HEAD ou POST.</span><span class="sxs-lookup"><span data-stu-id="01008-515">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="01008-516">O aplicativo não define cabeçalhos `Content-Language`de `Content-Type`solicitação `Last-Event-ID`que não senão, `Accept` `Accept-Language`ou .</span><span class="sxs-lookup"><span data-stu-id="01008-516">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="01008-517">O `Content-Type` cabeçalho, se definido, tem um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="01008-517">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="01008-518">A regra sobre cabeçalhos de solicitação definidos para a `setRequestHeader` solicitação `XMLHttpRequest` do cliente se aplica aos cabeçalhos que o aplicativo define chamando o objeto.</span><span class="sxs-lookup"><span data-stu-id="01008-518">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="01008-519">A especificação cors chama esses cabeçalhos *autor solicitar cabeçalhos*.</span><span class="sxs-lookup"><span data-stu-id="01008-519">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="01008-520">A regra não se aplica aos cabeçalhos `User-Agent` `Host`que `Content-Length`o navegador pode definir, tais como , ou .</span><span class="sxs-lookup"><span data-stu-id="01008-520">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="01008-521">A seguir, um exemplo de uma solicitação de pré-vôo:</span><span class="sxs-lookup"><span data-stu-id="01008-521">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="01008-522">A solicitação de pré-vôo usa o método HTTP OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="01008-522">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="01008-523">Inclui dois cabeçalhos especiais:</span><span class="sxs-lookup"><span data-stu-id="01008-523">It includes two special headers:</span></span>

* <span data-ttu-id="01008-524">`Access-Control-Request-Method`: O método HTTP que será utilizado para a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="01008-524">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="01008-525">`Access-Control-Request-Headers`: Uma lista de cabeçalhos de solicitação que o aplicativo define na solicitação real.</span><span class="sxs-lookup"><span data-stu-id="01008-525">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="01008-526">Como dito anteriormente, isso não inclui cabeçalhos `User-Agent`que o navegador define, como .</span><span class="sxs-lookup"><span data-stu-id="01008-526">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="01008-527">Quando o CORS é habilitado com a política apropriada, ASP.NET Core geralmente responde automaticamente às solicitações de pré-vôo do CORS.</span><span class="sxs-lookup"><span data-stu-id="01008-527">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="01008-528">Consulte [o atributo [HttpOptions] para solicitações de pré-vôo](#pro).</span><span class="sxs-lookup"><span data-stu-id="01008-528">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="01008-529">Uma solicitação de pré-vôo `Access-Control-Request-Headers` cors pode incluir um cabeçalho, que indica para o servidor os cabeçalhos que são enviados com a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="01008-529">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="01008-530">Para permitir cabeçalhos específicos, ligue para: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*></span><span class="sxs-lookup"><span data-stu-id="01008-530">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="01008-531">Para permitir que todos os <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>cabeçalhos de solicitação do autor solicitem:</span><span class="sxs-lookup"><span data-stu-id="01008-531">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="01008-532">Os navegadores não são totalmente consistentes na forma como definem `Access-Control-Request-Headers`.</span><span class="sxs-lookup"><span data-stu-id="01008-532">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="01008-533">Se você definir cabeçalhos para qualquer outra coisa `Accept` `Content-Type`que `Origin`não seja `"*"` (ou usar), <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>você deve incluir pelo menos , e , além de quaisquer cabeçalhos personalizados que você deseja suportar.</span><span class="sxs-lookup"><span data-stu-id="01008-533">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="01008-534">A seguir, uma resposta de exemplo à solicitação de pré-vôo (assumindo que o servidor permite a solicitação):</span><span class="sxs-lookup"><span data-stu-id="01008-534">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="01008-535">A resposta inclui `Access-Control-Allow-Methods` um cabeçalho que lista `Access-Control-Allow-Headers` os métodos permitidos e, opcionalmente, um cabeçalho, que lista os cabeçalhos permitidos.</span><span class="sxs-lookup"><span data-stu-id="01008-535">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="01008-536">Se a solicitação de pré-vôo for bem sucedida, o navegador envia a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="01008-536">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="01008-537">Se a solicitação de pré-vôo for negada, o aplicativo retorna uma resposta *de 200 OK,* mas não envia os cabeçalhos cors de volta.</span><span class="sxs-lookup"><span data-stu-id="01008-537">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="01008-538">Portanto, o navegador não tenta a solicitação de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="01008-538">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="01008-539">Defina o tempo de expiração do pré-vôo</span><span class="sxs-lookup"><span data-stu-id="01008-539">Set the preflight expiration time</span></span>

<span data-ttu-id="01008-540">O `Access-Control-Max-Age` cabeçalho especifica quanto tempo a resposta à solicitação de pré-vôo pode ser armazenada em cache.</span><span class="sxs-lookup"><span data-stu-id="01008-540">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="01008-541">Para definir este <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>cabeçalho, ligue:</span><span class="sxs-lookup"><span data-stu-id="01008-541">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="01008-542">Como funciona o CORS</span><span class="sxs-lookup"><span data-stu-id="01008-542">How CORS works</span></span>

<span data-ttu-id="01008-543">Esta seção descreve o que acontece em uma solicitação [cors](https://developer.mozilla.org/docs/Web/HTTP/CORS) no nível das mensagens HTTP.</span><span class="sxs-lookup"><span data-stu-id="01008-543">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="01008-544">Cors **não** é um recurso de segurança.</span><span class="sxs-lookup"><span data-stu-id="01008-544">CORS is **not** a security feature.</span></span> <span data-ttu-id="01008-545">Cors é um padrão W3C que permite que um servidor relaxe a política de mesma origem.</span><span class="sxs-lookup"><span data-stu-id="01008-545">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="01008-546">Por exemplo, um ator mal-intencionado poderia usar [o Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) contra seu site e executar uma solicitação entre sites para o seu site habilitado para cors para roubar informações.</span><span class="sxs-lookup"><span data-stu-id="01008-546">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="01008-547">Sua API não é mais segura permitindo cors.</span><span class="sxs-lookup"><span data-stu-id="01008-547">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="01008-548">Cabe ao cliente (navegador) fazer valer o CORS.</span><span class="sxs-lookup"><span data-stu-id="01008-548">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="01008-549">O servidor executa a solicitação e retorna a resposta, é o cliente que retorna um erro e bloqueia a resposta.</span><span class="sxs-lookup"><span data-stu-id="01008-549">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="01008-550">Por exemplo, qualquer uma das seguintes ferramentas exibirá a resposta do servidor:</span><span class="sxs-lookup"><span data-stu-id="01008-550">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="01008-551">Fiddler</span><span class="sxs-lookup"><span data-stu-id="01008-551">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="01008-552">Postman</span><span class="sxs-lookup"><span data-stu-id="01008-552">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="01008-553">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="01008-553">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="01008-554">Um navegador da Web inserindo a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="01008-554">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="01008-555">É uma maneira de um servidor permitir que os navegadores executem um [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) de origem cruzada ou solicitação [de API de origem cruzada](https://developer.mozilla.org/docs/Web/API/Fetch_API) que de outra forma seria proibido.</span><span class="sxs-lookup"><span data-stu-id="01008-555">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="01008-556">Os navegadores (sem cors) não podem fazer solicitações de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="01008-556">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="01008-557">Antes do CORS, [o JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) era usado para contornar essa restrição.</span><span class="sxs-lookup"><span data-stu-id="01008-557">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="01008-558">JSONP não usa XHR, usa `<script>` a tag para receber a resposta.</span><span class="sxs-lookup"><span data-stu-id="01008-558">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="01008-559">Os scripts podem ser carregados de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="01008-559">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="01008-560">A [especificação CORS](https://www.w3.org/TR/cors/) introduziu vários novos cabeçalhos HTTP que permitem solicitações de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="01008-560">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="01008-561">Se um navegador suportar o CORS, ele define esses cabeçalhos automaticamente para solicitações de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="01008-561">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="01008-562">O código JavaScript personalizado não é necessário para ativar o CORS.</span><span class="sxs-lookup"><span data-stu-id="01008-562">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="01008-563">O seguinte é um exemplo de uma solicitação de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="01008-563">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="01008-564">O `Origin` cabeçalho fornece o domínio do site que está fazendo a solicitação.</span><span class="sxs-lookup"><span data-stu-id="01008-564">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="01008-565">O `Origin` cabeçalho é necessário e deve ser diferente do host.</span><span class="sxs-lookup"><span data-stu-id="01008-565">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="01008-566">Se o servidor permitir a `Access-Control-Allow-Origin` solicitação, ele define o cabeçalho na resposta.</span><span class="sxs-lookup"><span data-stu-id="01008-566">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="01008-567">O valor deste cabeçalho corresponde ao `Origin` cabeçalho `"*"`da solicitação ou é o valor curinga, o que significa que qualquer origem é permitida:</span><span class="sxs-lookup"><span data-stu-id="01008-567">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="01008-568">Se a resposta não `Access-Control-Allow-Origin` incluir o cabeçalho, a solicitação de origem cruzada falhará.</span><span class="sxs-lookup"><span data-stu-id="01008-568">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="01008-569">Especificamente, o navegador não permite a solicitação.</span><span class="sxs-lookup"><span data-stu-id="01008-569">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="01008-570">Mesmo que o servidor retorne uma resposta bem-sucedida, o navegador não disponibiliza a resposta para o aplicativo cliente.</span><span class="sxs-lookup"><span data-stu-id="01008-570">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="01008-571">Testar o CORS</span><span class="sxs-lookup"><span data-stu-id="01008-571">Test CORS</span></span>

<span data-ttu-id="01008-572">Para testar o CORS:</span><span class="sxs-lookup"><span data-stu-id="01008-572">To test CORS:</span></span>

1. <span data-ttu-id="01008-573">[Crie um projeto de API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="01008-573">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="01008-574">Alternativamente, você pode [baixar a amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="01008-574">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="01008-575">Habilite o CORS usando uma das abordagens deste documento.</span><span class="sxs-lookup"><span data-stu-id="01008-575">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="01008-576">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="01008-576">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="01008-577">`WithOrigins("https://localhost:<port>");`só deve ser usado para testar um aplicativo de amostra semelhante ao [código de amostra de download](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="01008-577">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="01008-578">Crie um projeto de aplicativo web (Razor Pages ou MVC).</span><span class="sxs-lookup"><span data-stu-id="01008-578">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="01008-579">A amostra usa páginas de barbear.</span><span class="sxs-lookup"><span data-stu-id="01008-579">The sample uses Razor Pages.</span></span> <span data-ttu-id="01008-580">Você pode criar o aplicativo web na mesma solução que o projeto de API.</span><span class="sxs-lookup"><span data-stu-id="01008-580">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="01008-581">Adicione o seguinte código destacado ao arquivo *Index.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="01008-581">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="01008-582">No código anterior, `url: 'https://<web app>.azurewebsites.net/api/values/1',` substitua-o pela URL para o aplicativo implantado.</span><span class="sxs-lookup"><span data-stu-id="01008-582">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="01008-583">Implantar o projeto de API.</span><span class="sxs-lookup"><span data-stu-id="01008-583">Deploy the API project.</span></span> <span data-ttu-id="01008-584">Por exemplo, [implantar no Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="01008-584">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="01008-585">Execute o aplicativo Razor Pages ou MVC na área de trabalho e clique no botão **Teste.**</span><span class="sxs-lookup"><span data-stu-id="01008-585">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="01008-586">Use as ferramentas F12 para revisar mensagens de erro.</span><span class="sxs-lookup"><span data-stu-id="01008-586">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="01008-587">Remova a origem `WithOrigins` do host local e implante o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="01008-587">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="01008-588">Alternativamente, execute o aplicativo cliente com uma porta diferente.</span><span class="sxs-lookup"><span data-stu-id="01008-588">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="01008-589">Por exemplo, executado a partir de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="01008-589">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="01008-590">Teste com o aplicativo do cliente.</span><span class="sxs-lookup"><span data-stu-id="01008-590">Test with the client app.</span></span> <span data-ttu-id="01008-591">As falhas do CORS retornam um erro, mas a mensagem de erro não está disponível para JavaScript.</span><span class="sxs-lookup"><span data-stu-id="01008-591">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="01008-592">Use a guia do console nas ferramentas f12 para ver o erro.</span><span class="sxs-lookup"><span data-stu-id="01008-592">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="01008-593">Dependendo do navegador, você tem um erro (no console de ferramentas f12) semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="01008-593">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="01008-594">Usando o Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="01008-594">Using Microsoft Edge:</span></span>

     <span data-ttu-id="01008-595">**SEC7120: [CORS] `https://localhost:44375` A origem `https://localhost:44375` não foi encontrado no cabeçalho de resposta Access-Control-Allow-Origin para recurso de origem cruzada em`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="01008-595">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="01008-596">Usando o Chrome:</span><span class="sxs-lookup"><span data-stu-id="01008-596">Using Chrome:</span></span>

     <span data-ttu-id="01008-597">**O acesso ao XMLHttpRequest na `https://webapi.azurewebsites.net/api/values/1` origem `https://localhost:44375` foi bloqueado pela diretiva CORS: Nenhum cabeçalho 'Access-Control-Allow-Origin' está presente no recurso solicitado.**</span><span class="sxs-lookup"><span data-stu-id="01008-597">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="01008-598">Os pontos finais habilitados para CORS podem ser testados com uma ferramenta, como [O Violinista](https://www.telerik.com/fiddler) ou [o Carteiro](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="01008-598">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="01008-599">Ao usar uma ferramenta, a origem da `Origin` solicitação especificada pelo cabeçalho deve diferir do host que recebe a solicitação.</span><span class="sxs-lookup"><span data-stu-id="01008-599">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="01008-600">Se a solicitação não *for de origem* cruzada `Origin` com base no valor do cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="01008-600">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="01008-601">Não há necessidade de o CORS Middleware processar a solicitação.</span><span class="sxs-lookup"><span data-stu-id="01008-601">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="01008-602">Os cabeçalhos cors não são devolvidos na resposta.</span><span class="sxs-lookup"><span data-stu-id="01008-602">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="01008-603">CORS no IIS</span><span class="sxs-lookup"><span data-stu-id="01008-603">CORS in IIS</span></span>

<span data-ttu-id="01008-604">Ao implantar no IIS, o CORS deve ser executado antes da Autenticação do Windows se o servidor não estiver configurado para permitir acesso anônimo.</span><span class="sxs-lookup"><span data-stu-id="01008-604">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="01008-605">Para suportar esse cenário, o [módulo IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) precisa ser instalado e configurado para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="01008-605">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="01008-606">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="01008-606">Additional resources</span></span>

* [<span data-ttu-id="01008-607">CORS (Compartilhamento de Recursos entre Origens)</span><span class="sxs-lookup"><span data-stu-id="01008-607">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="01008-608">Começando com o módulo IIS CORS</span><span class="sxs-lookup"><span data-stu-id="01008-608">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
