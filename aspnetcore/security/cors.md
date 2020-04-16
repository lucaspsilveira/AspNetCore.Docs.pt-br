---
title: Habilite o CORS (Cross-Origin Requests, solicitação de origem cruzada) em ASP.NET Núcleo
author: rick-anderson
description: Saiba como o CORS como padrão para permitir ou rejeitar solicitações de origem cruzada em um aplicativo ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/cors
ms.openlocfilehash: e7731fd967c206679ac93209fdb84f40367bea37
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440903"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="bb1bc-103">Habilite o CORS (Cross-Origin Requests, solicitação de origem cruzada) em ASP.NET Núcleo</span><span class="sxs-lookup"><span data-stu-id="bb1bc-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bb1bc-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e Kirk [Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="bb1bc-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="bb1bc-105">Este artigo mostra como ativar o CORS em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="bb1bc-106">A segurança do navegador impede que uma página da Web seja feita para um domínio diferente daquele que serviu a página da Web.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="bb1bc-107">Essa restrição é chamada *de política de mesma origem.*</span><span class="sxs-lookup"><span data-stu-id="bb1bc-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="bb1bc-108">A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="bb1bc-109">Às vezes, você pode querer permitir que outros sites façam solicitações de origem cruzada para o seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="bb1bc-110">Para obter mais informações, consulte o artigo da [Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="bb1bc-111">[Compartilhamento de recursos de origem cruzada](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="bb1bc-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="bb1bc-112">É um padrão W3C que permite que um servidor relaxe a política de mesma origem.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="bb1bc-113">**Não** é um recurso de segurança, o CORS relaxa a segurança.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="bb1bc-114">Uma API não é mais segura ao permitir o CORS.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="bb1bc-115">Para obter mais informações, consulte [Como funciona o CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="bb1bc-116">Permite que um servidor permita explicitamente algumas solicitações de origem cruzada enquanto rejeita outros.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="bb1bc-117">É mais seguro e flexível do que as técnicas anteriores, como [jsonp](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="bb1bc-118">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bb1bc-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="bb1bc-119">Mesma origem</span><span class="sxs-lookup"><span data-stu-id="bb1bc-119">Same origin</span></span>

<span data-ttu-id="bb1bc-120">Dois URLs têm a mesma origem se tiverem esquemas, hosts e portas idênticos ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="bb1bc-121">Estes dois URLs têm a mesma origem:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="bb1bc-122">Esses URLs têm origens diferentes das duas URLs anteriores:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="bb1bc-123">`https://example.net`&ndash; Domínio diferente</span><span class="sxs-lookup"><span data-stu-id="bb1bc-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="bb1bc-124">`https://www.example.com/foo.html`&ndash; Subdomínio diferente</span><span class="sxs-lookup"><span data-stu-id="bb1bc-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="bb1bc-125">`http://example.com/foo.html`&ndash; Esquema diferente</span><span class="sxs-lookup"><span data-stu-id="bb1bc-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="bb1bc-126">`https://example.com:9000/foo.html`&ndash; Porta diferente</span><span class="sxs-lookup"><span data-stu-id="bb1bc-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="bb1bc-127">Habilitar CORS</span><span class="sxs-lookup"><span data-stu-id="bb1bc-127">Enable CORS</span></span>

<span data-ttu-id="bb1bc-128">Existem três maneiras de habilitar o CORS:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="bb1bc-129">Em middleware usando uma [diretiva nomeada](#np) ou [política padrão](#dp).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="bb1bc-130">Usando [roteamento de ponto final](#ecors).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="bb1bc-131">Com o atributo [[EnableCors].](#attr)</span><span class="sxs-lookup"><span data-stu-id="bb1bc-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="bb1bc-132">O uso do atributo [[EnableCors]](#attr) com uma diretiva nomeada fornece o melhor controle na limitação de pontos finais que suportam o CORS.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="bb1bc-133">Cada abordagem é detalhada nas seções a seguir.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-133">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="bb1bc-134">CORS com política nomeada e middleware</span><span class="sxs-lookup"><span data-stu-id="bb1bc-134">CORS with named policy and middleware</span></span>

<span data-ttu-id="bb1bc-135">O CORS Middleware lida com solicitações de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-135">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="bb1bc-136">O código a seguir aplica uma diretiva CORS a todos os pontos finais do aplicativo com as origens especificadas:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-136">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="bb1bc-137">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-137">The preceding code:</span></span>

* <span data-ttu-id="bb1bc-138">Define o nome `_myAllowSpecificOrigins`da política para .</span><span class="sxs-lookup"><span data-stu-id="bb1bc-138">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="bb1bc-139">O nome da política é arbitrário.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-139">The policy name is arbitrary.</span></span>
* <span data-ttu-id="bb1bc-140">Chama <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> o método de `_myAllowSpecificOrigins` extensão e especifica a política cors.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-140">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="bb1bc-141">`UseCors`adiciona o middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-141">`UseCors` adds the CORS middleware.</span></span>
* <span data-ttu-id="bb1bc-142">Chamadas <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> com uma [expressão lambda.](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)</span><span class="sxs-lookup"><span data-stu-id="bb1bc-142">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="bb1bc-143">A lambda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> leva um objeto.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-143">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="bb1bc-144">[As opções de configuração,](#cors-policy-options)como `WithOrigins`, são descritas posteriormente neste artigo.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-144">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="bb1bc-145">Habilita `_myAllowSpecificOrigins` a política CORS para todos os pontos finais do controlador.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-145">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="bb1bc-146">Consulte [o roteamento de ponto final](#ecors) para aplicar uma política CORS a pontos finais específicos.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-146">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="bb1bc-147">Com o roteamento de ponto final, o middleware CORS `UseEndpoints` ***deve*** ser configurado para ser executado entre as chamadas para `UseRouting` e .</span><span class="sxs-lookup"><span data-stu-id="bb1bc-147">With endpoint routing, the CORS middleware ***must*** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="bb1bc-148">Consulte [Test CORS](#testc) para obter instruções sobre o código de teste semelhante ao código anterior.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-148">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="bb1bc-149">A <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> chamada do método adiciona serviços CORS ao contêiner de serviço do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-149">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="bb1bc-150">Para obter mais informações, consulte [as opções de política](#cpo) do CORS neste documento.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-150">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="bb1bc-151">Os <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> métodos podem ser acorrentados, conforme mostrado no seguinte código:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-151">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="bb1bc-152">Nota: A URL especificada **não** deve`/`conter uma barra de arrasto ().</span><span class="sxs-lookup"><span data-stu-id="bb1bc-152">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="bb1bc-153">Se a URL `/`terminar com `false` , a comparação retorna e nenhum cabeçalho é devolvido.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-153">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="bb1bc-154">CORS com política padrão e middleware</span><span class="sxs-lookup"><span data-stu-id="bb1bc-154">CORS with default policy and middleware</span></span>

<span data-ttu-id="bb1bc-155">O código destacado a seguir permite a política CORS padrão:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-155">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="bb1bc-156">O código anterior aplica a diretiva CORS padrão a todos os pontos finais do controlador.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-156">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="bb1bc-157">Habilite cors com roteamento de ponto final</span><span class="sxs-lookup"><span data-stu-id="bb1bc-157">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="bb1bc-158">A habilitação do CORS por `RequireCors` ponto final usando atualmente ***não*** suporta [solicitações automáticas de pré-vôo](#apf).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-158">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="bb1bc-159">Para obter mais informações, consulte [este problema do GitHub](https://github.com/dotnet/aspnetcore/issues/20709) e [teste o CORS com roteamento de ponto final e [HttpOptions]](#tcer).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-159">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="bb1bc-160">Com o roteamento de ponto final, o CORS pode <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> ser habilitado por ponto final usando o conjunto de métodos de extensão:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-160">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="bb1bc-161">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-161">In the preceding code:</span></span>

* <span data-ttu-id="bb1bc-162">`app.UseCors`habilita o middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-162">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="bb1bc-163">Como uma diretiva padrão não foi `app.UseCors()` configurada, sozinha não habilita o CORS.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-163">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="bb1bc-164">Os `/echo` pontos finais do controlador permitem solicitações de origem cruzada usando a diretiva especificada.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-164">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="bb1bc-165">Os `/echo2` pontos finais e as páginas de navalha ***não*** permitem solicitações de origem cruzada porque nenhuma política padrão foi especificada.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-165">The `/echo2` and Razor Pages endpoints do ***not*** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="bb1bc-166">O atributo [[DesativarCors]](#dc) ***não*** desabilita o CORS habilitado pelo `RequireCors`roteamento de ponto final com .</span><span class="sxs-lookup"><span data-stu-id="bb1bc-166">The [[DisableCors]](#dc) attribute does ***not***  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="bb1bc-167">Consulte [Test CORS com roteamento de ponto final e [HttpOptions]](#tcer) para obter instruções sobre o código de teste semelhante ao anterior.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-167">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="bb1bc-168">Habilite o CORS com atributos</span><span class="sxs-lookup"><span data-stu-id="bb1bc-168">Enable CORS with attributes</span></span>

<span data-ttu-id="bb1bc-169">Habilitar o CORS com o atributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) e aplicar uma diretiva nomeada apenas aos pontos finais que requerem cors fornece o melhor controle.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-169">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="bb1bc-170">O atributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) fornece uma alternativa à aplicação do CORS globalmente.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-170">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="bb1bc-171">O `[EnableCors]` atributo habilita o CORS para pontos finais selecionados, em vez de todos os pontos finais:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-171">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="bb1bc-172">`[EnableCors]`especifica a política padrão.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-172">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="bb1bc-173">`[EnableCors("{Policy String}")]`especifica uma política nomeada.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-173">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="bb1bc-174">O `[EnableCors]` atributo pode ser aplicado a:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-174">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="bb1bc-175">Página navalha`PageModel`</span><span class="sxs-lookup"><span data-stu-id="bb1bc-175">Razor Page `PageModel`</span></span>
* <span data-ttu-id="bb1bc-176">Controller</span><span class="sxs-lookup"><span data-stu-id="bb1bc-176">Controller</span></span>
* <span data-ttu-id="bb1bc-177">Método de ação do controlador</span><span class="sxs-lookup"><span data-stu-id="bb1bc-177">Controller action method</span></span>

<span data-ttu-id="bb1bc-178">Diferentes políticas podem ser aplicadas a controladores, modelos de página ou métodos de ação com o atributo. `[EnableCors]`</span><span class="sxs-lookup"><span data-stu-id="bb1bc-178">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="bb1bc-179">Quando `[EnableCors]` o atributo é aplicado a um controlador, modelo de página ou método de ação, e o CORS é habilitado em middleware, ***ambas as*** políticas são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-179">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="bb1bc-180">***Recomendamos contra a combinação de políticas. Use o atributo*** `[EnableCors]` ***ou middleware, não ambos no mesmo aplicativo.***</span><span class="sxs-lookup"><span data-stu-id="bb1bc-180">***We recommend against combining policies. Use the*** `[EnableCors]` ***attribute or middleware, not both in the same app.***</span></span>

<span data-ttu-id="bb1bc-181">O código a seguir aplica uma política diferente a cada método:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-181">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="bb1bc-182">O código a seguir cria duas políticas cors:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-182">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="bb1bc-183">Para o melhor controle de limitar as solicitações do CORS:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-183">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="bb1bc-184">Use `[EnableCors("MyPolicy")]` com uma política nomeada.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-184">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="bb1bc-185">Não defina uma política padrão.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-185">Don't define a default policy.</span></span>
* <span data-ttu-id="bb1bc-186">Não use [roteamento de ponto final](#ecors).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-186">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="bb1bc-187">O código na próxima seção atende à lista anterior.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-187">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="bb1bc-188">Consulte [Test CORS](#testc) para obter instruções sobre o código de teste semelhante ao código anterior.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-188">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="bb1bc-189">Desativar o CORS</span><span class="sxs-lookup"><span data-stu-id="bb1bc-189">Disable CORS</span></span>

<span data-ttu-id="bb1bc-190">O atributo [[DesativarCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) ***não*** desabilita o CORS habilitado pelo [roteamento de ponto final](#ecors).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-190">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does ***not***  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="bb1bc-191">O código a seguir define `"MyPolicy"`a política cors:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-191">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="bb1bc-192">O código a seguir desativa o CORS para a `GetValues2` ação:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-192">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="bb1bc-193">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-193">The preceding code:</span></span>

* <span data-ttu-id="bb1bc-194">Não habilita o CORS com [roteamento de ponto final](#ecors).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-194">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="bb1bc-195">Não define uma [política padrão do CORS.](#dp)</span><span class="sxs-lookup"><span data-stu-id="bb1bc-195">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="bb1bc-196">Usa [[EnableCors("MyPolicy")]](#attr) para `"MyPolicy"` habilitar a diretiva CORS para o controlador.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-196">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="bb1bc-197">Desabilita o CORS para o `GetValues2` método.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-197">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="bb1bc-198">Consulte [O Cors de teste](#testc) para obter instruções sobre o teste do código anterior.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-198">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="bb1bc-199">Opções de política do CORS</span><span class="sxs-lookup"><span data-stu-id="bb1bc-199">CORS policy options</span></span>

<span data-ttu-id="bb1bc-200">Esta seção descreve as várias opções que podem ser definidas em uma política cors:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-200">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="bb1bc-201">Defina as origens permitidas</span><span class="sxs-lookup"><span data-stu-id="bb1bc-201">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="bb1bc-202">Defina os métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="bb1bc-202">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="bb1bc-203">Defina os cabeçalhos de solicitação permitidos</span><span class="sxs-lookup"><span data-stu-id="bb1bc-203">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="bb1bc-204">Defina os cabeçalhos de resposta expostos</span><span class="sxs-lookup"><span data-stu-id="bb1bc-204">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="bb1bc-205">Credenciais em solicitações de origem cruzada</span><span class="sxs-lookup"><span data-stu-id="bb1bc-205">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="bb1bc-206">Defina o tempo de expiração do pré-vôo</span><span class="sxs-lookup"><span data-stu-id="bb1bc-206">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="bb1bc-207"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>é chamado `Startup.ConfigureServices`dentro .</span><span class="sxs-lookup"><span data-stu-id="bb1bc-207"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bb1bc-208">Para algumas opções, pode ser útil ler a seção Como o [CORS funciona](#how-cors) primeiro.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-208">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="bb1bc-209">Defina as origens permitidas</span><span class="sxs-lookup"><span data-stu-id="bb1bc-209">Set the allowed origins</span></span>

<span data-ttu-id="bb1bc-210"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Permite solicitações do CORS de`http` todas `https`as origens com qualquer esquema (ou ).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-210"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="bb1bc-211">`AllowAnyOrigin`é inseguro porque *qualquer site* pode fazer solicitações de origem cruzada para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-211">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="bb1bc-212">Especificar `AllowAnyOrigin` `AllowCredentials` e é uma configuração insegura e pode resultar em falsificação de solicitação entre sites.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-212">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="bb1bc-213">O serviço CORS retorna uma resposta CORS inválida quando um aplicativo é configurado com ambos os métodos.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-213">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="bb1bc-214">`AllowAnyOrigin`afeta pedidos de pré-vôo e o `Access-Control-Allow-Origin` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-214">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="bb1bc-215">Para obter mais informações, consulte a seção [de solicitações de pré-vôo.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="bb1bc-215">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="bb1bc-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Define <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> a propriedade da diretiva como uma função que permite que as origens correspondam a um domínio curinga configurado ao avaliar se a origem é permitida.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="bb1bc-217">Defina os métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="bb1bc-217">Set the allowed HTTP methods</span></span>

<span data-ttu-id="bb1bc-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="bb1bc-219">Permite qualquer método HTTP:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-219">Allows any HTTP method:</span></span>
* <span data-ttu-id="bb1bc-220">Afeta as solicitações `Access-Control-Allow-Methods` de pré-vôo e o cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-220">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="bb1bc-221">Para obter mais informações, consulte a seção [de solicitações de pré-vôo.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="bb1bc-221">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="bb1bc-222">Defina os cabeçalhos de solicitação permitidos</span><span class="sxs-lookup"><span data-stu-id="bb1bc-222">Set the allowed request headers</span></span>

<span data-ttu-id="bb1bc-223">Para permitir que cabeçalhos específicos sejam enviados em uma <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> solicitação cors, chamados [cabeçalhos de solicitação do autor,](https://xhr.spec.whatwg.org/#request)ligue e especifique os cabeçalhos permitidos:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-223">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="bb1bc-224">Para permitir que todos os <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> [cabeçalhos de solicitação do autor,](https://www.w3.org/TR/cors/#author-request-headers)ligue:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-224">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="bb1bc-225">`AllowAnyHeader`afeta as solicitações de pré-vôo e o [cabeçalho access-control-Request-Headers.](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)</span><span class="sxs-lookup"><span data-stu-id="bb1bc-225">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="bb1bc-226">Para obter mais informações, consulte a seção [de solicitações de pré-vôo.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="bb1bc-226">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="bb1bc-227">Uma correspondência de diretiva CORS Middleware `WithHeaders` para cabeçalhos específicos `Access-Control-Request-Headers` especificados só é `WithHeaders`possível quando os cabeçalhos enviados correspondem exatamente aos cabeçalhos indicados em .</span><span class="sxs-lookup"><span data-stu-id="bb1bc-227">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="bb1bc-228">Por exemplo, considere um aplicativo configurado da seguinte forma:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-228">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="bb1bc-229">O CORS Middleware recusa uma solicitação de `Content-Language` pré-vôo com o cabeçalho `WithHeaders`de solicitação a seguir porque[(HeaderNames.ContentLanguage)](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)não está listado em :</span><span class="sxs-lookup"><span data-stu-id="bb1bc-229">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="bb1bc-230">O aplicativo retorna uma resposta *de 200 OK,* mas não envia os cabeçalhos cors de volta.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-230">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="bb1bc-231">Portanto, o navegador não tenta a solicitação de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-231">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="bb1bc-232">Defina os cabeçalhos de resposta expostos</span><span class="sxs-lookup"><span data-stu-id="bb1bc-232">Set the exposed response headers</span></span>

<span data-ttu-id="bb1bc-233">Por padrão, o navegador não expõe todos os cabeçalhos de resposta ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-233">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="bb1bc-234">Para obter mais informações, consulte [W3C Cross-Origin Resource Sharing (Terminologia): Simples Cabeçalho de Resposta](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-234">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="bb1bc-235">Os cabeçalhos de resposta disponíveis por padrão são:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-235">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="bb1bc-236">A especificação cors chama esses cabeçalhos *de cabeçalho simples cabeçalhos de resposta*.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-236">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="bb1bc-237">Para disponibilizar outros cabeçalhos <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>para o aplicativo, ligue para:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-237">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="bb1bc-238">Credenciais em solicitações de origem cruzada</span><span class="sxs-lookup"><span data-stu-id="bb1bc-238">Credentials in cross-origin requests</span></span>

<span data-ttu-id="bb1bc-239">As credenciais requerem tratamento especial em uma solicitação do CORS.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-239">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="bb1bc-240">Por padrão, o navegador não envia credenciais com uma solicitação de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-240">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="bb1bc-241">As credenciais incluem cookies e esquemas de autenticação HTTP.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-241">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="bb1bc-242">Para enviar credenciais com uma solicitação de `XMLHttpRequest.withCredentials` `true`origem cruzada, o cliente deve definir para .</span><span class="sxs-lookup"><span data-stu-id="bb1bc-242">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="bb1bc-243">Usando `XMLHttpRequest` diretamente:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-243">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="bb1bc-244">Usando jQuery:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-244">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="bb1bc-245">Usando a [API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="bb1bc-245">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="bb1bc-246">O servidor deve permitir as credenciais.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-246">The server must allow the credentials.</span></span> <span data-ttu-id="bb1bc-247">Para permitir credenciais de <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>origem cruzada, ligue para:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-247">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="bb1bc-248">A resposta HTTP `Access-Control-Allow-Credentials` inclui um cabeçalho, que informa ao navegador que o servidor permite credenciais para uma solicitação de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-248">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="bb1bc-249">Se o navegador enviar credenciais, mas `Access-Control-Allow-Credentials` a resposta não incluir um cabeçalho válido, o navegador não exporá a resposta ao aplicativo e a solicitação de origem cruzada falhará.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-249">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="bb1bc-250">Permitir credenciais de origem cruzada é um risco para a segurança.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-250">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="bb1bc-251">Um site em outro domínio pode enviar credenciais de um usuário de entrada para o aplicativo em nome do usuário sem o conhecimento do usuário.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-251">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="bb1bc-252">A especificação do CORS também `"*"` afirma que a configuração de `Access-Control-Allow-Credentials` origens para (todas as origens) é inválida se o cabeçalho estiver presente.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-252">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="bb1bc-253">Solicitações de pré-vôo</span><span class="sxs-lookup"><span data-stu-id="bb1bc-253">Preflight requests</span></span>

<span data-ttu-id="bb1bc-254">Para algumas solicitações do CORS, o navegador envia uma solicitação de [OPÇÕES](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) adicionais antes de fazer a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-254">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="bb1bc-255">Este pedido é chamado de [solicitação de pré-vôo.](https://developer.mozilla.org/docs/Glossary/Preflight_request)</span><span class="sxs-lookup"><span data-stu-id="bb1bc-255">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="bb1bc-256">O navegador pode ignorar a solicitação de pré-vôo se ***todas as*** seguintes condições forem verdadeiras:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-256">The browser can skip the preflight request if ***all*** the following conditions are true:</span></span>

* <span data-ttu-id="bb1bc-257">O método de solicitação é GET, HEAD ou POST.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-257">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="bb1bc-258">O aplicativo não define cabeçalhos `Content-Language`de `Content-Type`solicitação `Last-Event-ID`que não senão, `Accept` `Accept-Language`ou .</span><span class="sxs-lookup"><span data-stu-id="bb1bc-258">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="bb1bc-259">O `Content-Type` cabeçalho, se definido, tem um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-259">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="bb1bc-260">A regra sobre cabeçalhos de solicitação definidos para a `setRequestHeader` solicitação `XMLHttpRequest` do cliente se aplica aos cabeçalhos que o aplicativo define chamando o objeto.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-260">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="bb1bc-261">A especificação cors chama esses cabeçalhos [autor solicitar cabeçalhos](https://www.w3.org/TR/cors/#author-request-headers).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-261">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="bb1bc-262">A regra não se aplica aos cabeçalhos `User-Agent` `Host`que `Content-Length`o navegador pode definir, tais como , ou .</span><span class="sxs-lookup"><span data-stu-id="bb1bc-262">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="bb1bc-263">A seguir, uma resposta de exemplo semelhante à solicitação de pré-vôo feita a partir do botão **[Colocar teste]** na seção [Test CORS](#testc) deste documento.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-263">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

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

<span data-ttu-id="bb1bc-264">A solicitação de pré-vôo usa o método [HTTP OPTIONS.](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)</span><span class="sxs-lookup"><span data-stu-id="bb1bc-264">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="bb1bc-265">Pode incluir os seguintes cabeçalhos:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-265">It may include the following headers:</span></span>

* <span data-ttu-id="bb1bc-266">[Método de solicitação de controle de acesso](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): O método HTTP que será usado para a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-266">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="bb1bc-267">[Cabeçalhos de solicitação de controle de acesso](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): Uma lista de cabeçalhos de solicitação que o aplicativo define na solicitação real.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-267">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="bb1bc-268">Como dito anteriormente, isso não inclui cabeçalhos `User-Agent`que o navegador define, como .</span><span class="sxs-lookup"><span data-stu-id="bb1bc-268">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="bb1bc-269">Métodos de permitir controle de acesso</span><span class="sxs-lookup"><span data-stu-id="bb1bc-269">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="bb1bc-270">Se a solicitação de pré-vôo `200 OK` for negada, o aplicativo retorna uma resposta, mas não define os cabeçalhos cors.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-270">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="bb1bc-271">Portanto, o navegador não tenta a solicitação de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-271">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="bb1bc-272">Para obter um exemplo de solicitação de pré-vôo negada, consulte a seção [Teste CORS](#testc) deste documento.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-272">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="bb1bc-273">Usando as ferramentas f12, o aplicativo do console mostra um erro semelhante a um dos seguintes, dependendo do navegador:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-273">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="bb1bc-274">Firefox: Solicitação de origem cruzada bloqueada: a mesma política de `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`origem não permite a leitura do recurso remoto em .</span><span class="sxs-lookup"><span data-stu-id="bb1bc-274">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="bb1bc-275">(Motivo: pedido do CORS não teve sucesso).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-275">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="bb1bc-276">Saiba mais</span><span class="sxs-lookup"><span data-stu-id="bb1bc-276">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="bb1bc-277">Baseado em cromo: O acessohttps://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5para buscarhttps://cors3.azurewebsites.netem ' from origin ' foi bloqueado pela política cors: A resposta à solicitação de pré-vôo não passa pela verificação de controle de acesso: Nenhum cabeçalho 'Access-Control-Allow-Origin' está presente no recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-277">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="bb1bc-278">Se uma resposta opaca atender às suas necessidades, defina o modo da solicitação como 'no-cors' para buscar o recurso com o CORS desabilitado.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-278">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="bb1bc-279">Para permitir cabeçalhos específicos, ligue para: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*></span><span class="sxs-lookup"><span data-stu-id="bb1bc-279">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="bb1bc-280">Para permitir que todos os <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> [cabeçalhos de solicitação do autor,](https://www.w3.org/TR/cors/#author-request-headers)ligue:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-280">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="bb1bc-281">Os navegadores não são consistentes na forma como definem `Access-Control-Request-Headers`.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-281">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="bb1bc-282">Se um deles:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-282">If either:</span></span>

* <span data-ttu-id="bb1bc-283">Cabeçalhos são definidos para qualquer outra coisa que não`"*"`</span><span class="sxs-lookup"><span data-stu-id="bb1bc-283">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="bb1bc-284"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>é chamado: Incluir `Accept` `Content-Type`pelo `Origin`menos , e , além de quaisquer cabeçalhos personalizados que você deseja suportar.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-284"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="bb1bc-285">Código automático de solicitação de pré-vôo</span><span class="sxs-lookup"><span data-stu-id="bb1bc-285">Automatic preflight request code</span></span>

<span data-ttu-id="bb1bc-286">Quando a política cors é aplicada ou:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-286">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="bb1bc-287">Globalmente, `app.UseCors` `Startup.Configure`chamando.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-287">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="bb1bc-288">Usando `[EnableCors]` o atributo.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-288">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="bb1bc-289">ASP.NET Core responde à solicitação de OPÇÕES de pré-vôo.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-289">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="bb1bc-290">A ativação do CORS por `RequireCors` ponto final usando atualmente ***não*** suporta solicitações automáticas de pré-vôo.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-290">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support automatic preflight requests.</span></span>

<span data-ttu-id="bb1bc-291">A seção [Teste CORS](#testc) deste documento demonstra esse comportamento.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-291">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="bb1bc-292">[HttpOptions] atributo para solicitações de pré-vôo</span><span class="sxs-lookup"><span data-stu-id="bb1bc-292">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="bb1bc-293">Quando o CORS é habilitado com a política apropriada, ASP.NET Core geralmente responde automaticamente às solicitações de pré-vôo do CORS.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-293">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="bb1bc-294">Em alguns cenários, isso pode não ser o caso.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-294">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="bb1bc-295">Por exemplo, usar [o CORS com roteamento de ponto final](#ecors).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-295">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="bb1bc-296">O código a seguir usa o atributo [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) para criar pontos finais para solicitações de OPÇÕES:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-296">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="bb1bc-297">Consulte [Test CORS com roteamento de ponto final e [HttpOptions]](#tcer) para obter instruções sobre o teste do código anterior.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-297">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="bb1bc-298">Defina o tempo de expiração do pré-vôo</span><span class="sxs-lookup"><span data-stu-id="bb1bc-298">Set the preflight expiration time</span></span>

<span data-ttu-id="bb1bc-299">O `Access-Control-Max-Age` cabeçalho especifica quanto tempo a resposta à solicitação de pré-vôo pode ser armazenada em cache.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-299">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="bb1bc-300">Para definir este <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>cabeçalho, ligue:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-300">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="bb1bc-301">Como funciona o CORS</span><span class="sxs-lookup"><span data-stu-id="bb1bc-301">How CORS works</span></span>

<span data-ttu-id="bb1bc-302">Esta seção descreve o que acontece em uma solicitação [cors](https://developer.mozilla.org/docs/Web/HTTP/CORS) no nível das mensagens HTTP.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-302">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="bb1bc-303">Cors **não** é um recurso de segurança.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-303">CORS is **not** a security feature.</span></span> <span data-ttu-id="bb1bc-304">Cors é um padrão W3C que permite que um servidor relaxe a política de mesma origem.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-304">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="bb1bc-305">Por exemplo, um ator mal-intencionado poderia usar [o Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) contra seu site e executar uma solicitação entre sites para o seu site habilitado para cors para roubar informações.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-305">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="bb1bc-306">Uma API não é mais segura permitindo o CORS.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-306">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="bb1bc-307">Cabe ao cliente (navegador) fazer valer o CORS.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-307">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="bb1bc-308">O servidor executa a solicitação e retorna a resposta, é o cliente que retorna um erro e bloqueia a resposta.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-308">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="bb1bc-309">Por exemplo, qualquer uma das seguintes ferramentas exibirá a resposta do servidor:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-309">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="bb1bc-310">Fiddler</span><span class="sxs-lookup"><span data-stu-id="bb1bc-310">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="bb1bc-311">Postman</span><span class="sxs-lookup"><span data-stu-id="bb1bc-311">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="bb1bc-312">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="bb1bc-312">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="bb1bc-313">Um navegador da Web inserindo a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-313">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="bb1bc-314">É uma maneira de um servidor permitir que os navegadores executem um [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) de origem cruzada ou solicitação [de API de origem cruzada](https://developer.mozilla.org/docs/Web/API/Fetch_API) que de outra forma seria proibido.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-314">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="bb1bc-315">Navegadores sem CORS não podem fazer solicitações de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-315">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="bb1bc-316">Antes do CORS, [o JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) era usado para contornar essa restrição.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-316">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="bb1bc-317">JSONP não usa XHR, usa `<script>` a tag para receber a resposta.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-317">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="bb1bc-318">Os scripts podem ser carregados de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-318">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="bb1bc-319">A [especificação CORS](https://www.w3.org/TR/cors/) introduziu vários novos cabeçalhos HTTP que permitem solicitações de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-319">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="bb1bc-320">Se um navegador suportar o CORS, ele define esses cabeçalhos automaticamente para solicitações de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-320">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="bb1bc-321">O código JavaScript personalizado não é necessário para ativar o CORS.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-321">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="bb1bc-322">O [botão de teste PUT](https://cors3.azurewebsites.net/test) na [amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) implantada</span><span class="sxs-lookup"><span data-stu-id="bb1bc-322">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="bb1bc-323">A seguir, um exemplo de uma solicitação de `https://cors1.azurewebsites.net/api/values`origem cruzada do botão de teste [Valores](https://cors3.azurewebsites.net/) para .</span><span class="sxs-lookup"><span data-stu-id="bb1bc-323">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="bb1bc-324">O `Origin` cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-324">The `Origin` header:</span></span>

* <span data-ttu-id="bb1bc-325">Fornece o domínio do site que está fazendo a solicitação.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-325">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="bb1bc-326">É necessário e deve ser diferente do hospedeiro.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-326">Is required and must be different from the host.</span></span>

<span data-ttu-id="bb1bc-327">**Cabeçalhos gerais**</span><span class="sxs-lookup"><span data-stu-id="bb1bc-327">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="bb1bc-328">**Cabeçalhos de resposta**</span><span class="sxs-lookup"><span data-stu-id="bb1bc-328">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="bb1bc-329">**Cabeçalhos da solicitação**</span><span class="sxs-lookup"><span data-stu-id="bb1bc-329">**Request headers**</span></span>

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

<span data-ttu-id="bb1bc-330">Nas `OPTIONS` solicitações, o servidor define o **cabeçalho de cabeçalho de** `Access-Control-Allow-Origin: {allowed origin}` resposta na resposta.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-330">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="bb1bc-331">Por exemplo, a solicitação de botão `OPTIONS` ['Excluir ' 'EnableCors]](https://cors1.azurewebsites.net/test?number=2) de [amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)implantada contém os seguintes cabeçalhos:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-331">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="bb1bc-332">**Cabeçalhos gerais**</span><span class="sxs-lookup"><span data-stu-id="bb1bc-332">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="bb1bc-333">**Cabeçalhos de resposta**</span><span class="sxs-lookup"><span data-stu-id="bb1bc-333">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="bb1bc-334">**Cabeçalhos da solicitação**</span><span class="sxs-lookup"><span data-stu-id="bb1bc-334">**Request headers**</span></span>

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

<span data-ttu-id="bb1bc-335">Nos **cabeçalhos de resposta anteriores,** o servidor define o [cabeçalho Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) na resposta.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-335">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="bb1bc-336">O `https://cors1.azurewebsites.net` valor deste cabeçalho corresponde ao `Origin` cabeçalho da solicitação.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-336">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="bb1bc-337">Se <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> for chamado, o `Access-Control-Allow-Origin: *`valor do curinga é devolvido.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-337">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="bb1bc-338">`AllowAnyOrigin`permite qualquer origem.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-338">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="bb1bc-339">Se a resposta não `Access-Control-Allow-Origin` incluir o cabeçalho, a solicitação de origem cruzada falhará.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-339">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="bb1bc-340">Especificamente, o navegador não permite a solicitação.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-340">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="bb1bc-341">Mesmo que o servidor retorne uma resposta bem-sucedida, o navegador não disponibiliza a resposta para o aplicativo cliente.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-341">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="bb1bc-342">Solicitações de opções de exibição</span><span class="sxs-lookup"><span data-stu-id="bb1bc-342">Display OPTIONS requests</span></span>

<span data-ttu-id="bb1bc-343">Por padrão, os navegadores Chrome e Edge não mostram solicitações OPTIONS na guia de rede das ferramentas F12.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-343">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="bb1bc-344">Para exibir solicitações OPTIONS nesses navegadores:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-344">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="bb1bc-345">`chrome://flags/#out-of-blink-cors` ou `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="bb1bc-345">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="bb1bc-346">desativar a bandeira.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-346">disable the flag.</span></span>
* <span data-ttu-id="bb1bc-347">Reiniciar.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-347">restart.</span></span>

<span data-ttu-id="bb1bc-348">O Firefox mostra as solicitações OPTIONS por padrão.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-348">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="bb1bc-349">CORS no IIS</span><span class="sxs-lookup"><span data-stu-id="bb1bc-349">CORS in IIS</span></span>

<span data-ttu-id="bb1bc-350">Ao implantar no IIS, o CORS deve ser executado antes da Autenticação do Windows se o servidor não estiver configurado para permitir acesso anônimo.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-350">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="bb1bc-351">Para suportar esse cenário, o [módulo IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) precisa ser instalado e configurado para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-351">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="bb1bc-352">Testar o CORS</span><span class="sxs-lookup"><span data-stu-id="bb1bc-352">Test CORS</span></span>

<span data-ttu-id="bb1bc-353">O [download da amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) tem código para testar o CORS.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-353">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="bb1bc-354">Consulte [como baixar](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-354">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="bb1bc-355">A amostra é um projeto de API com páginas de barbear adicionadas:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-355">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="bb1bc-356">`WithOrigins("https://localhost:<port>");`só deve ser usado para testar um aplicativo de amostra semelhante ao [código de amostra de download](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-356">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="bb1bc-357">O `ValuesController` seguinte fornece os pontos finais para testes:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-357">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="bb1bc-358">[O MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) é fornecido pelo pacote [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet e exibe informações de rota.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-358">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="bb1bc-359">Teste o código de amostra anterior usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-359">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="bb1bc-360">Use o aplicativo [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)de amostra implantado em .</span><span class="sxs-lookup"><span data-stu-id="bb1bc-360">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="bb1bc-361">Não há necessidade de baixar a amostra.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-361">There is no need to download the sample.</span></span>
* <span data-ttu-id="bb1bc-362">Execute a `dotnet run` amostra usando a `https://localhost:5001`URL padrão de .</span><span class="sxs-lookup"><span data-stu-id="bb1bc-362">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="bb1bc-363">Execute a amostra do Visual Studio com a porta definida `https://localhost:44398`como 44398 para uma URL de .</span><span class="sxs-lookup"><span data-stu-id="bb1bc-363">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="bb1bc-364">Usando um navegador com as ferramentas f12:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-364">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="bb1bc-365">Selecione o botão **Valores** e revise os cabeçalhos na guia **Rede.**</span><span class="sxs-lookup"><span data-stu-id="bb1bc-365">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="bb1bc-366">Selecione o botão **de teste PUT.**</span><span class="sxs-lookup"><span data-stu-id="bb1bc-366">Select the **PUT test** button.</span></span> <span data-ttu-id="bb1bc-367">Consulte [as solicitações de opções de exibição](#options) para exibir a solicitação OPÇÕES.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-367">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="bb1bc-368">O **teste PUT** cria duas solicitações, uma solicitação de pré-vôo OPÇÕES e a solicitação PUT.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-368">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="bb1bc-369">Selecione **`GetValues2 [DisableCors]`** o botão para acionar uma solicitação CORS com falha.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-369">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="bb1bc-370">Como mencionado no documento, a resposta retorna 200 de sucesso, mas a solicitação do CORS não é feita.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-370">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="bb1bc-371">Selecione a guia **Console** para ver o erro cors.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-371">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="bb1bc-372">Dependendo do navegador, um erro semelhante ao seguinte é exibido:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-372">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="bb1bc-373">O acesso `'https://cors1.azurewebsites.net/api/values/GetValues2'` ao `'https://cors3.azurewebsites.net'` fetch at from origin foi bloqueado pela política cors: Nenhum cabeçalho 'Access-Control-Allow-Origin' está presente no recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-373">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="bb1bc-374">Se uma resposta opaca atender às suas necessidades, defina o modo da solicitação como 'no-cors' para buscar o recurso com o CORS desabilitado.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-374">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="bb1bc-375">Os pontos finais habilitados para CORS podem ser testados com uma ferramenta, como [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)ou [Carteiro](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-375">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="bb1bc-376">Ao usar uma ferramenta, a origem da `Origin` solicitação especificada pelo cabeçalho deve diferir do host que recebe a solicitação.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-376">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="bb1bc-377">Se a solicitação não *for de origem* cruzada `Origin` com base no valor do cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-377">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="bb1bc-378">Não há necessidade de o CORS Middleware processar a solicitação.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-378">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="bb1bc-379">Os cabeçalhos cors não são devolvidos na resposta.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-379">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="bb1bc-380">O comando `curl` a seguir é use para emitir uma solicitação DE OPÇÕES com informações:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-380">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="bb1bc-381">Teste o CORS com roteamento de ponto final e [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="bb1bc-381">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="bb1bc-382">A habilitação do CORS por `RequireCors` ponto final usando atualmente ***não*** suporta [solicitações automáticas de pré-vôo](#apf).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-382">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="bb1bc-383">Considere o seguinte código que usa [roteamento de ponto final para habilitar o CORS](#ecors):</span><span class="sxs-lookup"><span data-stu-id="bb1bc-383">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="bb1bc-384">O `TodoItems1Controller` seguinte fornece pontos finais para testes:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-384">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="bb1bc-385">Teste o código anterior da página de [teste](https://cors1.azurewebsites.net/test?number=1) da [amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)implantada .</span><span class="sxs-lookup"><span data-stu-id="bb1bc-385">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="bb1bc-386">Os botões **Delete [EnableCors]** e **GET [EnableCors]** são `[EnableCors]` bem sucedidos, pois os pontos finais têm e respondem às solicitações de pré-vôo.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-386">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="bb1bc-387">Os outros pontos finais falham.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-387">The other endpoints fails.</span></span> <span data-ttu-id="bb1bc-388">O botão **GET** falha, porque o [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) envia:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-388">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="bb1bc-389">O `TodoItems2Controller` seguinte fornece pontos finais semelhantes, mas inclui código explícito para responder às solicitações OPTIONS:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-389">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="bb1bc-390">Teste o código anterior da página de [teste](https://cors1.azurewebsites.net/test?number=2) da amostra implantada.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-390">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="bb1bc-391">Na lista de paradas **do Controlador,** selecione **Preflight** e, em seguida, **Defina controlador**.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-391">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="bb1bc-392">Todas as chamadas do `TodoItems2Controller` CORS para os pontos finais são bem sucedidas.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-392">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bb1bc-393">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="bb1bc-393">Additional resources</span></span>

* [<span data-ttu-id="bb1bc-394">CORS (Compartilhamento de Recursos entre Origens)</span><span class="sxs-lookup"><span data-stu-id="bb1bc-394">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="bb1bc-395">Começando com o módulo IIS CORS</span><span class="sxs-lookup"><span data-stu-id="bb1bc-395">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bb1bc-396">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bb1bc-396">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bb1bc-397">Este artigo mostra como ativar o CORS em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-397">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="bb1bc-398">A segurança do navegador impede que uma página da Web seja feita para um domínio diferente daquele que serviu a página da Web.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-398">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="bb1bc-399">Essa restrição é chamada *de política de mesma origem.*</span><span class="sxs-lookup"><span data-stu-id="bb1bc-399">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="bb1bc-400">A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-400">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="bb1bc-401">Às vezes, você pode querer permitir que outros sites façam solicitações de origem cruzada para o seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-401">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="bb1bc-402">Para obter mais informações, consulte o artigo da [Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-402">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="bb1bc-403">[Compartilhamento de recursos de origem cruzada](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="bb1bc-403">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="bb1bc-404">É um padrão W3C que permite que um servidor relaxe a política de mesma origem.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-404">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="bb1bc-405">**Não** é um recurso de segurança, o CORS relaxa a segurança.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-405">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="bb1bc-406">Uma API não é mais segura ao permitir o CORS.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-406">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="bb1bc-407">Para obter mais informações, consulte [Como funciona o CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-407">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="bb1bc-408">Permite que um servidor permita explicitamente algumas solicitações de origem cruzada enquanto rejeita outros.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-408">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="bb1bc-409">É mais seguro e flexível do que as técnicas anteriores, como [jsonp](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-409">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="bb1bc-410">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bb1bc-410">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="bb1bc-411">Mesma origem</span><span class="sxs-lookup"><span data-stu-id="bb1bc-411">Same origin</span></span>

<span data-ttu-id="bb1bc-412">Dois URLs têm a mesma origem se tiverem esquemas, hosts e portas idênticos ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-412">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="bb1bc-413">Estes dois URLs têm a mesma origem:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-413">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="bb1bc-414">Esses URLs têm origens diferentes das duas URLs anteriores:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-414">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="bb1bc-415">`https://example.net`&ndash; Domínio diferente</span><span class="sxs-lookup"><span data-stu-id="bb1bc-415">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="bb1bc-416">`https://www.example.com/foo.html`&ndash; Subdomínio diferente</span><span class="sxs-lookup"><span data-stu-id="bb1bc-416">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="bb1bc-417">`http://example.com/foo.html`&ndash; Esquema diferente</span><span class="sxs-lookup"><span data-stu-id="bb1bc-417">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="bb1bc-418">`https://example.com:9000/foo.html`&ndash; Porta diferente</span><span class="sxs-lookup"><span data-stu-id="bb1bc-418">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="bb1bc-419">O Internet Explorer não considera a porta ao comparar origens.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-419">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="bb1bc-420">CORS com política nomeada e middleware</span><span class="sxs-lookup"><span data-stu-id="bb1bc-420">CORS with named policy and middleware</span></span>

<span data-ttu-id="bb1bc-421">O CORS Middleware lida com solicitações de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-421">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="bb1bc-422">O código a seguir permite o CORS para todo o aplicativo com a origem especificada:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-422">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="bb1bc-423">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-423">The preceding code:</span></span>

* <span data-ttu-id="bb1bc-424">Define o nome\_da política como "myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="bb1bc-424">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="bb1bc-425">O nome da política é arbitrário.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-425">The policy name is arbitrary.</span></span>
* <span data-ttu-id="bb1bc-426">Chama <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> o método de extensão, que habilita o CORS.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-426">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="bb1bc-427">Chamadas <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> com uma [expressão lambda.](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)</span><span class="sxs-lookup"><span data-stu-id="bb1bc-427">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="bb1bc-428">A lambda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> leva um objeto.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-428">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="bb1bc-429">[As opções de configuração,](#cors-policy-options)como `WithOrigins`, são descritas posteriormente neste artigo.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-429">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="bb1bc-430">A <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> chamada do método adiciona serviços CORS ao contêiner de serviço do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-430">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="bb1bc-431">Para obter mais informações, consulte [as opções de política](#cpo) do CORS neste documento .</span><span class="sxs-lookup"><span data-stu-id="bb1bc-431">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="bb1bc-432">O <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> método pode acorrentar métodos, conforme mostrado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-432">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="bb1bc-433">Nota: A URL **não** deve`/`conter uma barra de arrasto ().</span><span class="sxs-lookup"><span data-stu-id="bb1bc-433">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="bb1bc-434">Se a URL `/`terminar com `false` , a comparação retorna e nenhum cabeçalho é devolvido.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-434">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="bb1bc-435">O código a seguir aplica as políticas cors a todos os pontos finais de aplicativos via CORS Middleware:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-435">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="bb1bc-436">Nota: `UseCors` deve `UseMvc`ser chamado antes .</span><span class="sxs-lookup"><span data-stu-id="bb1bc-436">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="bb1bc-437">Consulte [Ativar cors em páginas de barbear, controladores e métodos de ação](#ecors) para aplicar a política CORS no nível de página/controlador/ação.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-437">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="bb1bc-438">Consulte [Test CORS](#test) para obter instruções sobre o código de teste semelhante ao código anterior.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-438">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="bb1bc-439">Habilite o CORS com atributos</span><span class="sxs-lookup"><span data-stu-id="bb1bc-439">Enable CORS with attributes</span></span>

<span data-ttu-id="bb1bc-440">O atributo [ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) fornece uma alternativa à aplicação do CORS globalmente.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-440">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="bb1bc-441">O `[EnableCors]` atributo habilita o CORS para pontos finais selecionados, em vez de todos os pontos finais.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-441">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="bb1bc-442">Use `[EnableCors]` para especificar `[EnableCors("{Policy String}")]` a política padrão e para especificar uma diretiva.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-442">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="bb1bc-443">O `[EnableCors]` atributo pode ser aplicado a:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-443">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="bb1bc-444">Página navalha`PageModel`</span><span class="sxs-lookup"><span data-stu-id="bb1bc-444">Razor Page `PageModel`</span></span>
* <span data-ttu-id="bb1bc-445">Controller</span><span class="sxs-lookup"><span data-stu-id="bb1bc-445">Controller</span></span>
* <span data-ttu-id="bb1bc-446">Método de ação do controlador</span><span class="sxs-lookup"><span data-stu-id="bb1bc-446">Controller action method</span></span>

<span data-ttu-id="bb1bc-447">Você pode aplicar diferentes políticas ao controlador/modelo de página/ação com o atributo. `[EnableCors]`</span><span class="sxs-lookup"><span data-stu-id="bb1bc-447">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="bb1bc-448">Quando `[EnableCors]` o atributo é aplicado a um método de controle/modelo/ação de página e o CORS é habilitado em middleware, ***ambas as*** políticas são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-448">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="bb1bc-449">Recomendamos ***não*** combinar políticas.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-449">We recommend ***not*** combining policies.</span></span> <span data-ttu-id="bb1bc-450">Use `[EnableCors]` o atributo ou middleware, \***não ambos**.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-450">Use the `[EnableCors]` attribute or middleware, \***not both**.</span></span> <span data-ttu-id="bb1bc-451">Ao `[EnableCors]`usar, **não** defina uma política padrão.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-451">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="bb1bc-452">O código a seguir aplica uma política diferente a cada método:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-452">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="bb1bc-453">O código a seguir cria uma política `"AnotherPolicy"`padrão cors e uma diretiva chamada :</span><span class="sxs-lookup"><span data-stu-id="bb1bc-453">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="bb1bc-454">Desativar o CORS</span><span class="sxs-lookup"><span data-stu-id="bb1bc-454">Disable CORS</span></span>

<span data-ttu-id="bb1bc-455">O atributo [ &lbrack;&rbrack; DisableCors desativa](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) o CORS para o controlador/modelo de página/ação.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-455">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="bb1bc-456">Opções de política do CORS</span><span class="sxs-lookup"><span data-stu-id="bb1bc-456">CORS policy options</span></span>

<span data-ttu-id="bb1bc-457">Esta seção descreve as várias opções que podem ser definidas em uma política cors:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-457">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="bb1bc-458">Defina as origens permitidas</span><span class="sxs-lookup"><span data-stu-id="bb1bc-458">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="bb1bc-459">Defina os métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="bb1bc-459">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="bb1bc-460">Defina os cabeçalhos de solicitação permitidos</span><span class="sxs-lookup"><span data-stu-id="bb1bc-460">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="bb1bc-461">Defina os cabeçalhos de resposta expostos</span><span class="sxs-lookup"><span data-stu-id="bb1bc-461">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="bb1bc-462">Credenciais em solicitações de origem cruzada</span><span class="sxs-lookup"><span data-stu-id="bb1bc-462">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="bb1bc-463">Defina o tempo de expiração do pré-vôo</span><span class="sxs-lookup"><span data-stu-id="bb1bc-463">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="bb1bc-464"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>é chamado `Startup.ConfigureServices`dentro .</span><span class="sxs-lookup"><span data-stu-id="bb1bc-464"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bb1bc-465">Para algumas opções, pode ser útil ler a seção Como o [CORS funciona](#how-cors) primeiro.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-465">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="bb1bc-466">Defina as origens permitidas</span><span class="sxs-lookup"><span data-stu-id="bb1bc-466">Set the allowed origins</span></span>

<span data-ttu-id="bb1bc-467"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Permite solicitações do CORS de`http` todas `https`as origens com qualquer esquema (ou ).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-467"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="bb1bc-468">`AllowAnyOrigin`é inseguro porque *qualquer site* pode fazer solicitações de origem cruzada para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-468">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="bb1bc-469">Especificar `AllowAnyOrigin` `AllowCredentials` e é uma configuração insegura e pode resultar em falsificação de solicitação entre sites.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-469">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="bb1bc-470">Para um aplicativo seguro, especifique uma lista exata de origens se o cliente deve se autorizar a acessar recursos do servidor.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-470">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="bb1bc-471">`AllowAnyOrigin`afeta pedidos de pré-vôo e o `Access-Control-Allow-Origin` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-471">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="bb1bc-472">Para obter mais informações, consulte a seção [de solicitações de pré-vôo.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="bb1bc-472">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="bb1bc-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Define <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> a propriedade da diretiva como uma função que permite que as origens correspondam a um domínio curinga configurado ao avaliar se a origem é permitida.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="bb1bc-474">Defina os métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="bb1bc-474">Set the allowed HTTP methods</span></span>

<span data-ttu-id="bb1bc-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="bb1bc-476">Permite qualquer método HTTP:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-476">Allows any HTTP method:</span></span>
* <span data-ttu-id="bb1bc-477">Afeta as solicitações `Access-Control-Allow-Methods` de pré-vôo e o cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-477">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="bb1bc-478">Para obter mais informações, consulte a seção [de solicitações de pré-vôo.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="bb1bc-478">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="bb1bc-479">Defina os cabeçalhos de solicitação permitidos</span><span class="sxs-lookup"><span data-stu-id="bb1bc-479">Set the allowed request headers</span></span>

<span data-ttu-id="bb1bc-480">Para permitir que cabeçalhos específicos sejam enviados em uma <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> solicitação cors, chamados *cabeçalhos de solicitação do autor,* ligue e especifique os cabeçalhos permitidos:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-480">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="bb1bc-481">Para permitir que todos os <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>cabeçalhos de solicitação do autor solicitem:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-481">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="bb1bc-482">Esta configuração afeta as `Access-Control-Request-Headers` solicitações de pré-vôo e o cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-482">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="bb1bc-483">Para obter mais informações, consulte a seção [de solicitações de pré-vôo.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="bb1bc-483">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="bb1bc-484">O CORS Middleware sempre permite `Access-Control-Request-Headers` que quatro cabeçalhos no ser enviado, independentemente dos valores configurados em CorsPolicy.Headers.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-484">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="bb1bc-485">Esta lista de cabeçalhos inclui:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-485">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="bb1bc-486">Por exemplo, considere um aplicativo configurado da seguinte forma:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-486">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="bb1bc-487">O CORS Middleware responde com sucesso a uma `Content-Language` solicitação de pré-vôo com o seguinte cabeçalho de solicitação porque está sempre listado em branco:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-487">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="bb1bc-488">Defina os cabeçalhos de resposta expostos</span><span class="sxs-lookup"><span data-stu-id="bb1bc-488">Set the exposed response headers</span></span>

<span data-ttu-id="bb1bc-489">Por padrão, o navegador não expõe todos os cabeçalhos de resposta ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-489">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="bb1bc-490">Para obter mais informações, consulte [W3C Cross-Origin Resource Sharing (Terminologia): Simples Cabeçalho de Resposta](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-490">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="bb1bc-491">Os cabeçalhos de resposta disponíveis por padrão são:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-491">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="bb1bc-492">A especificação cors chama esses cabeçalhos *de cabeçalho simples cabeçalhos de resposta*.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-492">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="bb1bc-493">Para disponibilizar outros cabeçalhos <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>para o aplicativo, ligue para:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-493">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="bb1bc-494">Credenciais em solicitações de origem cruzada</span><span class="sxs-lookup"><span data-stu-id="bb1bc-494">Credentials in cross-origin requests</span></span>

<span data-ttu-id="bb1bc-495">As credenciais requerem tratamento especial em uma solicitação do CORS.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-495">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="bb1bc-496">Por padrão, o navegador não envia credenciais com uma solicitação de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-496">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="bb1bc-497">As credenciais incluem cookies e esquemas de autenticação HTTP.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-497">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="bb1bc-498">Para enviar credenciais com uma solicitação de `XMLHttpRequest.withCredentials` `true`origem cruzada, o cliente deve definir para .</span><span class="sxs-lookup"><span data-stu-id="bb1bc-498">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="bb1bc-499">Usando `XMLHttpRequest` diretamente:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-499">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="bb1bc-500">Usando jQuery:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-500">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="bb1bc-501">Usando a [API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="bb1bc-501">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="bb1bc-502">O servidor deve permitir as credenciais.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-502">The server must allow the credentials.</span></span> <span data-ttu-id="bb1bc-503">Para permitir credenciais de <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>origem cruzada, ligue para:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-503">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="bb1bc-504">A resposta HTTP `Access-Control-Allow-Credentials` inclui um cabeçalho, que informa ao navegador que o servidor permite credenciais para uma solicitação de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-504">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="bb1bc-505">Se o navegador enviar credenciais, mas `Access-Control-Allow-Credentials` a resposta não incluir um cabeçalho válido, o navegador não exporá a resposta ao aplicativo e a solicitação de origem cruzada falhará.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-505">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="bb1bc-506">Permitir credenciais de origem cruzada é um risco para a segurança.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-506">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="bb1bc-507">Um site em outro domínio pode enviar credenciais de um usuário de entrada para o aplicativo em nome do usuário sem o conhecimento do usuário.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-507">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="bb1bc-508">A especificação do CORS também `"*"` afirma que a configuração de `Access-Control-Allow-Credentials` origens para (todas as origens) é inválida se o cabeçalho estiver presente.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-508">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="bb1bc-509">Solicitações de pré-vôo</span><span class="sxs-lookup"><span data-stu-id="bb1bc-509">Preflight requests</span></span>

<span data-ttu-id="bb1bc-510">Para algumas solicitações do CORS, o navegador envia uma solicitação adicional antes de fazer a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-510">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="bb1bc-511">Este pedido é chamado de *solicitação de pré-vôo.*</span><span class="sxs-lookup"><span data-stu-id="bb1bc-511">This request is called a *preflight request*.</span></span> <span data-ttu-id="bb1bc-512">O navegador pode ignorar a solicitação de pré-vôo se as seguintes condições forem verdadeiras:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-512">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="bb1bc-513">O método de solicitação é GET, HEAD ou POST.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-513">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="bb1bc-514">O aplicativo não define cabeçalhos `Content-Language`de `Content-Type`solicitação `Last-Event-ID`que não senão, `Accept` `Accept-Language`ou .</span><span class="sxs-lookup"><span data-stu-id="bb1bc-514">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="bb1bc-515">O `Content-Type` cabeçalho, se definido, tem um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-515">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="bb1bc-516">A regra sobre cabeçalhos de solicitação definidos para a `setRequestHeader` solicitação `XMLHttpRequest` do cliente se aplica aos cabeçalhos que o aplicativo define chamando o objeto.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-516">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="bb1bc-517">A especificação cors chama esses cabeçalhos *autor solicitar cabeçalhos*.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-517">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="bb1bc-518">A regra não se aplica aos cabeçalhos `User-Agent` `Host`que `Content-Length`o navegador pode definir, tais como , ou .</span><span class="sxs-lookup"><span data-stu-id="bb1bc-518">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="bb1bc-519">A seguir, um exemplo de uma solicitação de pré-vôo:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-519">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="bb1bc-520">A solicitação de pré-vôo usa o método HTTP OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-520">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="bb1bc-521">Inclui dois cabeçalhos especiais:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-521">It includes two special headers:</span></span>

* <span data-ttu-id="bb1bc-522">`Access-Control-Request-Method`: O método HTTP que será utilizado para a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-522">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="bb1bc-523">`Access-Control-Request-Headers`: Uma lista de cabeçalhos de solicitação que o aplicativo define na solicitação real.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-523">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="bb1bc-524">Como dito anteriormente, isso não inclui cabeçalhos `User-Agent`que o navegador define, como .</span><span class="sxs-lookup"><span data-stu-id="bb1bc-524">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="bb1bc-525">Quando o CORS é habilitado com a política apropriada, ASP.NET Core geralmente responde automaticamente às solicitações de pré-vôo do CORS.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-525">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="bb1bc-526">Consulte [o atributo [HttpOptions] para solicitações de pré-vôo](#pro).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-526">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="bb1bc-527">Uma solicitação de pré-vôo `Access-Control-Request-Headers` cors pode incluir um cabeçalho, que indica para o servidor os cabeçalhos que são enviados com a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-527">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="bb1bc-528">Para permitir cabeçalhos específicos, ligue para: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*></span><span class="sxs-lookup"><span data-stu-id="bb1bc-528">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="bb1bc-529">Para permitir que todos os <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>cabeçalhos de solicitação do autor solicitem:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-529">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="bb1bc-530">Os navegadores não são totalmente consistentes na forma como definem `Access-Control-Request-Headers`.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-530">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="bb1bc-531">Se você definir cabeçalhos para qualquer outra coisa `Accept` `Content-Type`que `Origin`não seja `"*"` (ou usar), <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>você deve incluir pelo menos , e , além de quaisquer cabeçalhos personalizados que você deseja suportar.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-531">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="bb1bc-532">A seguir, uma resposta de exemplo à solicitação de pré-vôo (assumindo que o servidor permite a solicitação):</span><span class="sxs-lookup"><span data-stu-id="bb1bc-532">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="bb1bc-533">A resposta inclui `Access-Control-Allow-Methods` um cabeçalho que lista `Access-Control-Allow-Headers` os métodos permitidos e, opcionalmente, um cabeçalho, que lista os cabeçalhos permitidos.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-533">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="bb1bc-534">Se a solicitação de pré-vôo for bem sucedida, o navegador envia a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-534">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="bb1bc-535">Se a solicitação de pré-vôo for negada, o aplicativo retorna uma resposta *de 200 OK,* mas não envia os cabeçalhos cors de volta.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-535">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="bb1bc-536">Portanto, o navegador não tenta a solicitação de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-536">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="bb1bc-537">Defina o tempo de expiração do pré-vôo</span><span class="sxs-lookup"><span data-stu-id="bb1bc-537">Set the preflight expiration time</span></span>

<span data-ttu-id="bb1bc-538">O `Access-Control-Max-Age` cabeçalho especifica quanto tempo a resposta à solicitação de pré-vôo pode ser armazenada em cache.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-538">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="bb1bc-539">Para definir este <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>cabeçalho, ligue:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-539">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="bb1bc-540">Como funciona o CORS</span><span class="sxs-lookup"><span data-stu-id="bb1bc-540">How CORS works</span></span>

<span data-ttu-id="bb1bc-541">Esta seção descreve o que acontece em uma solicitação [cors](https://developer.mozilla.org/docs/Web/HTTP/CORS) no nível das mensagens HTTP.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-541">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="bb1bc-542">Cors **não** é um recurso de segurança.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-542">CORS is **not** a security feature.</span></span> <span data-ttu-id="bb1bc-543">Cors é um padrão W3C que permite que um servidor relaxe a política de mesma origem.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-543">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="bb1bc-544">Por exemplo, um ator mal-intencionado poderia usar [o Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) contra seu site e executar uma solicitação entre sites para o seu site habilitado para cors para roubar informações.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-544">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="bb1bc-545">Sua API não é mais segura permitindo cors.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-545">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="bb1bc-546">Cabe ao cliente (navegador) fazer valer o CORS.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-546">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="bb1bc-547">O servidor executa a solicitação e retorna a resposta, é o cliente que retorna um erro e bloqueia a resposta.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-547">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="bb1bc-548">Por exemplo, qualquer uma das seguintes ferramentas exibirá a resposta do servidor:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-548">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="bb1bc-549">Fiddler</span><span class="sxs-lookup"><span data-stu-id="bb1bc-549">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="bb1bc-550">Postman</span><span class="sxs-lookup"><span data-stu-id="bb1bc-550">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="bb1bc-551">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="bb1bc-551">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="bb1bc-552">Um navegador da Web inserindo a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-552">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="bb1bc-553">É uma maneira de um servidor permitir que os navegadores executem um [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) de origem cruzada ou solicitação [de API de origem cruzada](https://developer.mozilla.org/docs/Web/API/Fetch_API) que de outra forma seria proibido.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-553">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="bb1bc-554">Os navegadores (sem cors) não podem fazer solicitações de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-554">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="bb1bc-555">Antes do CORS, [o JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) era usado para contornar essa restrição.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-555">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="bb1bc-556">JSONP não usa XHR, usa `<script>` a tag para receber a resposta.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-556">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="bb1bc-557">Os scripts podem ser carregados de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-557">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="bb1bc-558">A [especificação CORS](https://www.w3.org/TR/cors/) introduziu vários novos cabeçalhos HTTP que permitem solicitações de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-558">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="bb1bc-559">Se um navegador suportar o CORS, ele define esses cabeçalhos automaticamente para solicitações de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-559">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="bb1bc-560">O código JavaScript personalizado não é necessário para ativar o CORS.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-560">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="bb1bc-561">O seguinte é um exemplo de uma solicitação de origem cruzada.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-561">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="bb1bc-562">O `Origin` cabeçalho fornece o domínio do site que está fazendo a solicitação.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-562">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="bb1bc-563">O `Origin` cabeçalho é necessário e deve ser diferente do host.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-563">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="bb1bc-564">Se o servidor permitir a `Access-Control-Allow-Origin` solicitação, ele define o cabeçalho na resposta.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-564">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="bb1bc-565">O valor deste cabeçalho corresponde ao `Origin` cabeçalho `"*"`da solicitação ou é o valor curinga, o que significa que qualquer origem é permitida:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-565">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="bb1bc-566">Se a resposta não `Access-Control-Allow-Origin` incluir o cabeçalho, a solicitação de origem cruzada falhará.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-566">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="bb1bc-567">Especificamente, o navegador não permite a solicitação.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-567">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="bb1bc-568">Mesmo que o servidor retorne uma resposta bem-sucedida, o navegador não disponibiliza a resposta para o aplicativo cliente.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-568">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="bb1bc-569">Testar o CORS</span><span class="sxs-lookup"><span data-stu-id="bb1bc-569">Test CORS</span></span>

<span data-ttu-id="bb1bc-570">Para testar o CORS:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-570">To test CORS:</span></span>

1. <span data-ttu-id="bb1bc-571">[Crie um projeto de API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-571">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="bb1bc-572">Alternativamente, você pode [baixar a amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-572">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="bb1bc-573">Habilite o CORS usando uma das abordagens deste documento.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-573">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="bb1bc-574">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-574">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="bb1bc-575">`WithOrigins("https://localhost:<port>");`só deve ser usado para testar um aplicativo de amostra semelhante ao [código de amostra de download](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-575">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="bb1bc-576">Crie um projeto de aplicativo web (Razor Pages ou MVC).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-576">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="bb1bc-577">A amostra usa páginas de barbear.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-577">The sample uses Razor Pages.</span></span> <span data-ttu-id="bb1bc-578">Você pode criar o aplicativo web na mesma solução que o projeto de API.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-578">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="bb1bc-579">Adicione o seguinte código destacado ao arquivo *Index.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="bb1bc-579">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="bb1bc-580">No código anterior, `url: 'https://<web app>.azurewebsites.net/api/values/1',` substitua-o pela URL para o aplicativo implantado.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-580">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="bb1bc-581">Implantar o projeto de API.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-581">Deploy the API project.</span></span> <span data-ttu-id="bb1bc-582">Por exemplo, [implantar no Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-582">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="bb1bc-583">Execute o aplicativo Razor Pages ou MVC na área de trabalho e clique no botão **Teste.**</span><span class="sxs-lookup"><span data-stu-id="bb1bc-583">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="bb1bc-584">Use as ferramentas F12 para revisar mensagens de erro.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-584">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="bb1bc-585">Remova a origem `WithOrigins` do host local e implante o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-585">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="bb1bc-586">Alternativamente, execute o aplicativo cliente com uma porta diferente.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-586">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="bb1bc-587">Por exemplo, executado a partir de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-587">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="bb1bc-588">Teste com o aplicativo do cliente.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-588">Test with the client app.</span></span> <span data-ttu-id="bb1bc-589">As falhas do CORS retornam um erro, mas a mensagem de erro não está disponível para JavaScript.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-589">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="bb1bc-590">Use a guia do console nas ferramentas f12 para ver o erro.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-590">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="bb1bc-591">Dependendo do navegador, você tem um erro (no console de ferramentas f12) semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-591">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="bb1bc-592">Usando o Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-592">Using Microsoft Edge:</span></span>

     <span data-ttu-id="bb1bc-593">**SEC7120: [CORS] `https://localhost:44375` A origem `https://localhost:44375` não foi encontrado no cabeçalho de resposta Access-Control-Allow-Origin para recurso de origem cruzada em`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="bb1bc-593">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="bb1bc-594">Usando o Chrome:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-594">Using Chrome:</span></span>

     <span data-ttu-id="bb1bc-595">**O acesso ao XMLHttpRequest na `https://webapi.azurewebsites.net/api/values/1` origem `https://localhost:44375` foi bloqueado pela diretiva CORS: Nenhum cabeçalho 'Access-Control-Allow-Origin' está presente no recurso solicitado.**</span><span class="sxs-lookup"><span data-stu-id="bb1bc-595">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="bb1bc-596">Os pontos finais habilitados para CORS podem ser testados com uma ferramenta, como [O Violinista](https://www.telerik.com/fiddler) ou [o Carteiro](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="bb1bc-596">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="bb1bc-597">Ao usar uma ferramenta, a origem da `Origin` solicitação especificada pelo cabeçalho deve diferir do host que recebe a solicitação.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-597">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="bb1bc-598">Se a solicitação não *for de origem* cruzada `Origin` com base no valor do cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="bb1bc-598">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="bb1bc-599">Não há necessidade de o CORS Middleware processar a solicitação.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-599">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="bb1bc-600">Os cabeçalhos cors não são devolvidos na resposta.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-600">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="bb1bc-601">CORS no IIS</span><span class="sxs-lookup"><span data-stu-id="bb1bc-601">CORS in IIS</span></span>

<span data-ttu-id="bb1bc-602">Ao implantar no IIS, o CORS deve ser executado antes da Autenticação do Windows se o servidor não estiver configurado para permitir acesso anônimo.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-602">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="bb1bc-603">Para suportar esse cenário, o [módulo IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) precisa ser instalado e configurado para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bb1bc-603">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bb1bc-604">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="bb1bc-604">Additional resources</span></span>

* [<span data-ttu-id="bb1bc-605">CORS (Compartilhamento de Recursos entre Origens)</span><span class="sxs-lookup"><span data-stu-id="bb1bc-605">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="bb1bc-606">Começando com o módulo IIS CORS</span><span class="sxs-lookup"><span data-stu-id="bb1bc-606">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
