---
title: Configure o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga
author: rick-anderson
description: Saiba mais sobre a configuração para aplicativos hospedados por trás de servidores proxy e balanceadores de carga, o que muitas vezes oculta informações de solicitação importantes.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: ad4c3bbb30a672dcd56b51fb949285c9da326c96
ms.sourcegitcommit: 4437f4c149f1ef6c28796dcfaa2863b4c088169c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85074340"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="1be73-103">Configure o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga</span><span class="sxs-lookup"><span data-stu-id="1be73-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="1be73-104">Por [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="1be73-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1be73-105">Na configuração recomendada para o ASP.NET Core, o aplicativo é hospedado usando IIS/Módulo do ASP.NET Core, Nginx ou Apache.</span><span class="sxs-lookup"><span data-stu-id="1be73-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="1be73-106">Servidores proxy, balanceadores de carga e outros dispositivos de rede geralmente ocultam informações sobre a solicitação antes de ela alcançar o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="1be73-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="1be73-107">Quando solicitações HTTPS são passadas por proxy por HTTP, o esquema original (HTTPS) é perdido e deve ser encaminhado em um cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="1be73-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="1be73-108">Devido a um aplicativo receber uma solicitação do proxy e não de sua origem verdadeira na Internet ou rede corporativa, o endereço IP do cliente originador também deve ser encaminhado em um cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="1be73-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="1be73-109">Essas informações podem ser importantes no processamento de solicitações, por exemplo, em redirecionamentos, autenticação, geração de link, avaliação de política e localização geográfica do cliente.</span><span class="sxs-lookup"><span data-stu-id="1be73-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="1be73-110">Cabeçalhos encaminhados</span><span class="sxs-lookup"><span data-stu-id="1be73-110">Forwarded headers</span></span>

<span data-ttu-id="1be73-111">Por convenção, os proxies encaminham informações em cabeçalhos HTTP.</span><span class="sxs-lookup"><span data-stu-id="1be73-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="1be73-112">Cabeçalho</span><span class="sxs-lookup"><span data-stu-id="1be73-112">Header</span></span> | <span data-ttu-id="1be73-113">Descrição</span><span class="sxs-lookup"><span data-stu-id="1be73-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="1be73-114">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="1be73-114">X-Forwarded-For</span></span> | <span data-ttu-id="1be73-115">Contém informações sobre o cliente que iniciou a solicitação e os proxies subsequentes em uma cadeia de proxies.</span><span class="sxs-lookup"><span data-stu-id="1be73-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="1be73-116">Esse parâmetro pode conter endereços IP (e, opcionalmente, os números de porta).</span><span class="sxs-lookup"><span data-stu-id="1be73-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="1be73-117">Em uma cadeia de servidores proxy, o primeiro parâmetro indica o cliente em que a solicitação foi feita pela primeira vez.</span><span class="sxs-lookup"><span data-stu-id="1be73-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="1be73-118">Depois, vêm os identificadores de proxy subsequentes.</span><span class="sxs-lookup"><span data-stu-id="1be73-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="1be73-119">O último proxy na cadeia não está na lista de parâmetros.</span><span class="sxs-lookup"><span data-stu-id="1be73-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="1be73-120">O endereço IP do último proxy (e opcionalmente um número da porta) estão disponíveis como o endereço IP remoto na camada de transporte.</span><span class="sxs-lookup"><span data-stu-id="1be73-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="1be73-121">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="1be73-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="1be73-122">O valor do esquema de origem (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="1be73-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="1be73-123">O valor também pode ser uma lista de esquemas se a solicitação percorreu vários proxies.</span><span class="sxs-lookup"><span data-stu-id="1be73-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="1be73-124">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="1be73-124">X-Forwarded-Host</span></span> | <span data-ttu-id="1be73-125">O valor original do campo de cabeçalho do host.</span><span class="sxs-lookup"><span data-stu-id="1be73-125">The original value of the Host header field.</span></span> <span data-ttu-id="1be73-126">Normalmente, os proxies não modificam o cabeçalho do host.</span><span class="sxs-lookup"><span data-stu-id="1be73-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="1be73-127">Veja [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) para obter informações sobre uma vulnerabilidade de elevação de privilégios que afeta os sistemas em que o proxy não valida ou restringe cabeçalhos de Host a valores válidos conhecidos.</span><span class="sxs-lookup"><span data-stu-id="1be73-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="1be73-128">O middleware de cabeçalhos encaminhados, do pacote [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/), lê esses cabeçalhos e preenche os campos associados em <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="1be73-128">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="1be73-129">As atualizações de middleware:</span><span class="sxs-lookup"><span data-stu-id="1be73-129">The middleware updates:</span></span>

* <span data-ttu-id="1be73-130">[HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): definido usando o `X-Forwarded-For` valor do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="1be73-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="1be73-131">Configurações adicionais influenciam o modo como o middleware define `RemoteIpAddress`.</span><span class="sxs-lookup"><span data-stu-id="1be73-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="1be73-132">Para obter detalhes, veja as [Opções de middleware de cabeçalhos encaminhados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="1be73-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="1be73-133">[HttpContext. Request. Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): definido usando o `X-Forwarded-Proto` valor do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="1be73-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="1be73-134">[HttpContext. Request. host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): definido usando o `X-Forwarded-Host` valor do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="1be73-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="1be73-135">As [configurações padrão](#forwarded-headers-middleware-options) de middleware de cabeçalhos encaminhados podem ser definidas.</span><span class="sxs-lookup"><span data-stu-id="1be73-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="1be73-136">As configurações padrão são:</span><span class="sxs-lookup"><span data-stu-id="1be73-136">The default settings are:</span></span>

* <span data-ttu-id="1be73-137">Há apenas *um proxy* entre o aplicativo e a origem das solicitações.</span><span class="sxs-lookup"><span data-stu-id="1be73-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="1be73-138">Somente os endereços de loopback são configurados para proxies conhecidos e redes conhecidas.</span><span class="sxs-lookup"><span data-stu-id="1be73-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="1be73-139">Os cabeçalhos encaminhados são nomeados `X-Forwarded-For` e `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="1be73-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="1be73-140">Nem todos os dispositivos de rede adicionam os cabeçalhos `X-Forwarded-For` e `X-Forwarded-Proto` sem configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="1be73-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="1be73-141">Consulte as diretrizes do fabricante do dispositivo se as solicitações de proxies não contiverem esses cabeçalhos quando atingirem o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1be73-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="1be73-142">Se o dispositivo usar nomes de cabeçalho diferente de `X-Forwarded-For` e `X-Forwarded-Proto`, defina as opções <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> e <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> para corresponder aos nomes de cabeçalho usados pelo dispositivo.</span><span class="sxs-lookup"><span data-stu-id="1be73-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="1be73-143">Para obter mais informações, consulte [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) (Opções de middleware de cabeçalhos encaminhados) e [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names) (Configuração de um proxy que usa diferentes nomes de cabeçalho).</span><span class="sxs-lookup"><span data-stu-id="1be73-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="1be73-144">O IIS/IIS Express e o Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1be73-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="1be73-145">O Middleware de Cabeçalhos Encaminhados é habilitado por padrão pelo [Middleware de integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) quando o aplicativo é hospedado [fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) atrás do IIS e do Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1be73-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="1be73-146">O middleware de cabeçalhos encaminhados é ativado para ser executado primeiro no pipeline de middleware, com uma configuração restrita específica para o Módulo do ASP.NET Core devido a questões de confiança com cabeçalhos encaminhados (por exemplo, [falsificação de IP](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="1be73-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="1be73-147">O middleware está configurado para encaminhar os cabeçalhos `X-Forwarded-For` e `X-Forwarded-Proto` e é restrito a um proxy de localhost único.</span><span class="sxs-lookup"><span data-stu-id="1be73-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="1be73-148">Se configuração adicional for necessária, veja as [Opções de middleware de cabeçalhos encaminhados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="1be73-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="1be73-149">Outros cenários de servidor proxy e balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="1be73-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="1be73-150">Além de usar a [Integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) quando hospedar [fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model), o Middleware de cabeçalhos encaminhados não é habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="1be73-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="1be73-151">O middleware de cabeçalhos encaminhados deve ser habilitado para um aplicativo para processar cabeçalhos encaminhados com <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="1be73-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="1be73-152">Após a habilitação do middleware, se nenhum <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> for especificado para o middleware, o [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) padrão será [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="1be73-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="1be73-153">Configure o middleware com <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> para encaminhar os cabeçalhos `X-Forwarded-For` e `X-Forwarded-Proto` em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1be73-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span>

<a name="fhmo"></a>

### <a name="forwarded-headers-middleware-order"></a><span data-ttu-id="1be73-154">Ordem de middleware de cabeçalhos encaminhados</span><span class="sxs-lookup"><span data-stu-id="1be73-154">Forwarded Headers Middleware order</span></span>

<span data-ttu-id="1be73-155">O middleware de cabeçalhos encaminhados deve ser executado antes de outro middleware.</span><span class="sxs-lookup"><span data-stu-id="1be73-155">Forwarded Headers Middleware should run before other middleware.</span></span> <span data-ttu-id="1be73-156">Essa ordenação garantirá que o middleware conte com informações de cabeçalhos encaminhadas que podem consumir os valores de cabeçalho para processamento.</span><span class="sxs-lookup"><span data-stu-id="1be73-156">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span> <span data-ttu-id="1be73-157">O middleware de cabeçalhos encaminhados pode ser executado após o diagnóstico e o tratamento de erros, mas deve ser executado antes de chamar `UseHsts` :</span><span class="sxs-lookup"><span data-stu-id="1be73-157">Forwarded Headers Middleware can run after diagnostics and error handling, but it must be be run before calling `UseHsts`:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup.cs?name=snippet&highlight=13-17,25,30)]

<span data-ttu-id="1be73-158">Como alternativa, chame `UseForwardedHeaders` antes do diagnóstico:</span><span class="sxs-lookup"><span data-stu-id="1be73-158">Alternatively, call `UseForwardedHeaders` before diagnostics:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup2.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="1be73-159">Se nenhum <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> for especificado no `Startup.ConfigureServices` ou diretamente no método de extensão com <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, os cabeçalhos padrão para encaminhar serão [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="1be73-159">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="1be73-160">A propriedade <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> deve ser configurada com os cabeçalhos para encaminhar.</span><span class="sxs-lookup"><span data-stu-id="1be73-160">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="1be73-161">Configuração de Nginx</span><span class="sxs-lookup"><span data-stu-id="1be73-161">Nginx configuration</span></span>

<span data-ttu-id="1be73-162">Para encaminhar os cabeçalhos `X-Forwarded-For` e `X-Forwarded-Proto`, consulte <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="1be73-162">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="1be73-163">Para obter mais informações, veja [NGINX: usando o cabeçalho encaminhado](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="1be73-163">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="1be73-164">Configuração do Apache</span><span class="sxs-lookup"><span data-stu-id="1be73-164">Apache configuration</span></span>

<span data-ttu-id="1be73-165">`X-Forwarded-For` é adicionado automaticamente (veja [mod_proxy do módulo do Apache: cabeçalhos de solicitação de proxy reverso](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="1be73-165">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="1be73-166">Para obter informações sobre como encaminhar o cabeçalho `X-Forwarded-Proto`, consulte <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="1be73-166">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="1be73-167">Opções de middleware de cabeçalhos encaminhados</span><span class="sxs-lookup"><span data-stu-id="1be73-167">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="1be73-168"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> controla o comportamento do middleware de cabeçalhos encaminhados.</span><span class="sxs-lookup"><span data-stu-id="1be73-168"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="1be73-169">O seguinte exemplo altera os valores padrão:</span><span class="sxs-lookup"><span data-stu-id="1be73-169">The following example changes the default values:</span></span>

* <span data-ttu-id="1be73-170">Limite o número de entradas nos cabeçalhos encaminhados a `2`.</span><span class="sxs-lookup"><span data-stu-id="1be73-170">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="1be73-171">Adicione um endereço de proxy conhecido de `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="1be73-171">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="1be73-172">Altere o nome do cabeçalho encaminhado do padrão `X-Forwarded-For` para `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="1be73-172">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="1be73-173">Opção</span><span class="sxs-lookup"><span data-stu-id="1be73-173">Option</span></span> | <span data-ttu-id="1be73-174">Descrição</span><span class="sxs-lookup"><span data-stu-id="1be73-174">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="1be73-175">Restringe os hosts com o cabeçalho `X-Forwarded-Host` para os valores fornecidos.</span><span class="sxs-lookup"><span data-stu-id="1be73-175">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="1be73-176">Os valores são comparados usando ordinal-ignore-case.</span><span class="sxs-lookup"><span data-stu-id="1be73-176">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="1be73-177">Os número de porta devem ser excluídos.</span><span class="sxs-lookup"><span data-stu-id="1be73-177">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="1be73-178">Se a lista estiver vazia, todos os hosts serão permitidos.</span><span class="sxs-lookup"><span data-stu-id="1be73-178">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="1be73-179">Um curinga de nível superior `*` permite todos os hosts não vazios.</span><span class="sxs-lookup"><span data-stu-id="1be73-179">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="1be73-180">Curingas de subdomínio são permitidos, mas não correspondem ao domínio raiz.</span><span class="sxs-lookup"><span data-stu-id="1be73-180">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="1be73-181">Por exemplo, `*.contoso.com` corresponde o subdomínio `foo.contoso.com`, mas não ao domínio raiz `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="1be73-181">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="1be73-182">Nomes do host Unicode são permitidos, mas são convertidos em [Punycode](https://tools.ietf.org/html/rfc3492) para correspondência.</span><span class="sxs-lookup"><span data-stu-id="1be73-182">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="1be73-183">[Endereços IPv6](https://tools.ietf.org/html/rfc4291) devem incluir colchetes delimitadores e estar no [formato convencional](https://tools.ietf.org/html/rfc4291#section-2.2) (por exemplo, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="1be73-183">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="1be73-184">Endereços IPv6 não têm caso especial para verificar se há igualdade lógica entre formatos diferentes, e nenhuma canonicalização é executada.</span><span class="sxs-lookup"><span data-stu-id="1be73-184">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="1be73-185">Falha ao restringir os hosts permitidos pode permitir que um atacante falsifique links gerados pelo serviço.</span><span class="sxs-lookup"><span data-stu-id="1be73-185">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="1be73-186">O valor padrão é um `IList<string>` vazio.</span><span class="sxs-lookup"><span data-stu-id="1be73-186">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="1be73-187">Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="1be73-187">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="1be73-188">Esta opção é usada quando o proxy/encaminhador não usa o cabeçalho `X-Forwarded-For`, mas usa algum outro cabeçalho para encaminhar as informações.</span><span class="sxs-lookup"><span data-stu-id="1be73-188">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="1be73-189">O padrão é `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="1be73-189">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="1be73-190">Identifica quais encaminhadores devem ser processados.</span><span class="sxs-lookup"><span data-stu-id="1be73-190">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="1be73-191">Veja o [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) para a lista de campos aplicáveis.</span><span class="sxs-lookup"><span data-stu-id="1be73-191">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="1be73-192">Os valores típicos atribuídos a essa propriedade são `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="1be73-192">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="1be73-193">O valor padrão é [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="1be73-193">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="1be73-194">Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="1be73-194">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="1be73-195">Esta opção é usada quando o proxy/encaminhador não usa o cabeçalho `X-Forwarded-Host`, mas usa algum outro cabeçalho para encaminhar as informações.</span><span class="sxs-lookup"><span data-stu-id="1be73-195">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="1be73-196">O padrão é `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="1be73-196">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="1be73-197">Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="1be73-197">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="1be73-198">Esta opção é usada quando o proxy/encaminhador não usa o cabeçalho `X-Forwarded-Proto`, mas usa algum outro cabeçalho para encaminhar as informações.</span><span class="sxs-lookup"><span data-stu-id="1be73-198">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="1be73-199">O padrão é `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="1be73-199">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="1be73-200">Limita o número de entradas nos cabeçalhos que são processados.</span><span class="sxs-lookup"><span data-stu-id="1be73-200">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="1be73-201">Defina para `null` para desabilitar o limite, mas isso só deve ser feito se `KnownProxies` ou `KnownNetworks` estão configurados.</span><span class="sxs-lookup"><span data-stu-id="1be73-201">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="1be73-202">A definição de um valor não `null` é feita por precaução (o que não implica em uma garantia), para proteção contra proxies mal configurados e solicitações mal-intencionadas que chegam de canais secundários na rede.</span><span class="sxs-lookup"><span data-stu-id="1be73-202">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="1be73-203">O middleware de cabeçalhos encaminhados processa cabeçalhos na ordem inversa, da direita para esquerda.</span><span class="sxs-lookup"><span data-stu-id="1be73-203">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="1be73-204">Se o valor padrão for usado (`1`), apenas o valor mais à direita dos cabeçalhos será processado, a menos que o valor de `ForwardLimit` seja aumentado.</span><span class="sxs-lookup"><span data-stu-id="1be73-204">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="1be73-205">O padrão é `1`.</span><span class="sxs-lookup"><span data-stu-id="1be73-205">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="1be73-206">Intervalos de endereços de redes conhecidas dos quais aceitar cabeçalhos encaminhados.</span><span class="sxs-lookup"><span data-stu-id="1be73-206">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="1be73-207">Forneça os intervalos de IP usando notação de CIDR (Roteamento entre Domínios sem Classificação).</span><span class="sxs-lookup"><span data-stu-id="1be73-207">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="1be73-208">O servidor usa soquetes de modo duplo e os endereços IPv4 são fornecidos em um formato IPv6 (por exemplo, `10.0.0.1` no formato IPv4 representado no formato IPv6 como `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="1be73-208">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="1be73-209">Confira [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="1be73-209">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="1be73-210">Para determinar se este formato é obrigatório, veja [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="1be73-210">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="1be73-211">Para saber mais, veja a seção [Configuração de endereços IPv4 representados como endereços IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="1be73-211">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="1be73-212">O padrão é uma `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> que contém uma única entrada para `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="1be73-212">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="1be73-213">Endereços de proxies conhecidos dos quais aceitar cabeçalhos encaminhados.</span><span class="sxs-lookup"><span data-stu-id="1be73-213">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="1be73-214">Use `KnownProxies` especificar correspondências exatas de endereço IP.</span><span class="sxs-lookup"><span data-stu-id="1be73-214">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="1be73-215">O servidor usa soquetes de modo duplo e os endereços IPv4 são fornecidos em um formato IPv6 (por exemplo, `10.0.0.1` no formato IPv4 representado no formato IPv6 como `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="1be73-215">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="1be73-216">Confira [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="1be73-216">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="1be73-217">Para determinar se este formato é obrigatório, veja [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="1be73-217">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="1be73-218">Para saber mais, veja a seção [Configuração de endereços IPv4 representados como endereços IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="1be73-218">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="1be73-219">O padrão é uma `IList`\<<xref:System.Net.IPAddress>> que contém uma única entrada para `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="1be73-219">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="1be73-220">Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="1be73-220">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="1be73-221">O padrão é `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="1be73-221">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="1be73-222">Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="1be73-222">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="1be73-223">O padrão é `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="1be73-223">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="1be73-224">Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="1be73-224">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="1be73-225">O padrão é `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="1be73-225">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="1be73-226">Exigem o número de valores de cabeçalho a serem sincronizados entre os [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) sendo processados.</span><span class="sxs-lookup"><span data-stu-id="1be73-226">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="1be73-227">O padrão no ASP.NET Core 1.x é `true`.</span><span class="sxs-lookup"><span data-stu-id="1be73-227">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="1be73-228">O padrão no ASP.NET Core 2.0 ou posterior é `false`.</span><span class="sxs-lookup"><span data-stu-id="1be73-228">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="1be73-229">Cenários e casos de uso</span><span class="sxs-lookup"><span data-stu-id="1be73-229">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="1be73-230">Quando não é possível adicionar cabeçalhos encaminhados e todas as solicitações são seguras</span><span class="sxs-lookup"><span data-stu-id="1be73-230">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="1be73-231">Em alguns casos, pode não ser possível adicionar cabeçalhos encaminhados para as solicitações passadas por proxy ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1be73-231">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="1be73-232">Se o proxy está impondo que todas as solicitações externas públicas sejam HTTPS, o esquema pode ser definido manualmente em `Startup.Configure` antes de usar qualquer tipo de middleware:</span><span class="sxs-lookup"><span data-stu-id="1be73-232">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="1be73-233">Esse código pode ser desabilitado com uma variável de ambiente ou outra definição de configuração em um ambiente de preparo ou de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="1be73-233">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="1be73-234">Lidar com o caminho base e proxies que alteram o caminho da solicitação</span><span class="sxs-lookup"><span data-stu-id="1be73-234">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="1be73-235">Alguns proxies passam o caminho intacto, mas com um caminho base de aplicativo que deve ser removido para que o roteamento funcione corretamente.</span><span class="sxs-lookup"><span data-stu-id="1be73-235">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="1be73-236">O middleware de [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) divide o caminho em [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) e o caminho base do aplicativo em [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="1be73-236">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="1be73-237">Se `/foo` é o caminho base do aplicativo para um caminho de proxy passado como `/foo/api/1`, o middleware define `Request.PathBase` para `/foo` e `Request.Path` para `/api/1` com o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="1be73-237">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="1be73-238">O caminho original e o caminho base são reaplicados quando o middleware é chamado novamente na ordem inversa.</span><span class="sxs-lookup"><span data-stu-id="1be73-238">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="1be73-239">Para obter mais informações sobre o processamento de ordem de middleware, consulte <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="1be73-239">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="1be73-240">Se o proxy cortar o caminho (por exemplo, encaminhando `/foo/api/1` para `/api/1`), corrija redirecionamentos e links definindo a propriedade [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) da solicitação:</span><span class="sxs-lookup"><span data-stu-id="1be73-240">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="1be73-241">Se o proxy estiver adicionando dados de caminho, descarte a parte do caminho para corrigir os redirecionamentos e links usando <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> e atribuindo à propriedade <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>:</span><span class="sxs-lookup"><span data-stu-id="1be73-241">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="1be73-242">Configuração de um proxy que usa diferentes nomes de cabeçalho</span><span class="sxs-lookup"><span data-stu-id="1be73-242">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="1be73-243">Se o proxy não usar cabeçalhos nomeados `X-Forwarded-For` e `X-Forwarded-Proto` para encaminhar a porta/endereço do proxy e as informações de origem do esquema, defina as opções <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> e <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> para corresponder aos nomes de cabeçalho usados pelo proxy:</span><span class="sxs-lookup"><span data-stu-id="1be73-243">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="1be73-244">Configuração de endereços IPv4 representados como endereços IPv6</span><span class="sxs-lookup"><span data-stu-id="1be73-244">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="1be73-245">O servidor usa soquetes de modo duplo e os endereços IPv4 são fornecidos em um formato IPv6 (por exemplo, `10.0.0.1` no formato IPv4 representado no formato IPv6 como `::ffff:10.0.0.1` ou `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="1be73-245">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="1be73-246">Confira [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="1be73-246">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="1be73-247">Para determinar se este formato é obrigatório, veja [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="1be73-247">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="1be73-248">No exemplo a seguir, um endereço de rede que fornece cabeçalhos encaminhados é adicionado à lista `KnownNetworks` no formato IPv6.</span><span class="sxs-lookup"><span data-stu-id="1be73-248">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="1be73-249">Endereço IPv4: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="1be73-249">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="1be73-250">Conversão em endereço IPv6: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="1be73-250">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="1be73-251">Comprimento do prefixo convertido: 104</span><span class="sxs-lookup"><span data-stu-id="1be73-251">Converted prefix length: 104</span></span>

<span data-ttu-id="1be73-252">Você pode também fornecer o endereço no formato hexadecimal (`10.11.12.1`, representado no formato IPv6 como `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="1be73-252">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="1be73-253">Quando converter um endereço IPv4 em IPv6, adicione 96 ao comprimento do prefixo CIDR (`8` no exemplo) para levar em conta o prefixo IPv6 adicional `::ffff:` (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="1be73-253">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="1be73-254">Encaminhar o esquema para proxies reversos não IIS e Linux</span><span class="sxs-lookup"><span data-stu-id="1be73-254">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="1be73-255">Os aplicativos que chamam <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> e <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> colocam um site em um loop infinito se implantado em um Serviço de Aplicativo do Linux do Azure, VM (máquina virtual) do Azure no Linux ou atrás de outro proxy reverso além do IIS.</span><span class="sxs-lookup"><span data-stu-id="1be73-255">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="1be73-256">O TLS é encerrado pelo proxy inverso e o Kestrel não é informado do esquema de solicitação correto.</span><span class="sxs-lookup"><span data-stu-id="1be73-256">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="1be73-257">OAuth e OIDC também falham nessa configuração, pois geram redirecionamentos incorretos.</span><span class="sxs-lookup"><span data-stu-id="1be73-257">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="1be73-258"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adiciona e configura o Middleware de Cabeçalhos Encaminhados quando executado atrás do IIS, mas não há nenhuma configuração automática correspondente para Linux (integração do Apache ou do Nginx).</span><span class="sxs-lookup"><span data-stu-id="1be73-258"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="1be73-259">Para encaminhar o esquema do proxy em cenários não de IIS, adicione e configure o Middleware de Cabeçalhos Encaminhados.</span><span class="sxs-lookup"><span data-stu-id="1be73-259">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="1be73-260">No `Startup.ConfigureServices`, use o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="1be73-260">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="certificate-forwarding"></a><span data-ttu-id="1be73-261">Encaminhamento de certificado</span><span class="sxs-lookup"><span data-stu-id="1be73-261">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="1be73-262">Azure</span><span class="sxs-lookup"><span data-stu-id="1be73-262">Azure</span></span>

<span data-ttu-id="1be73-263">Para configurar o serviço de Azure App para encaminhamento de certificado, consulte [Configurar a autenticação mútua TLS para o serviço Azure app](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span><span class="sxs-lookup"><span data-stu-id="1be73-263">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="1be73-264">As diretrizes a seguir pertencem à configuração do aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1be73-264">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="1be73-265">No `Startup.Configure` , adicione o seguinte código antes da chamada para `app.UseAuthentication();` :</span><span class="sxs-lookup"><span data-stu-id="1be73-265">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="1be73-266">Configure o middleware de encaminhamento de certificado para especificar o nome do cabeçalho que o Azure usa.</span><span class="sxs-lookup"><span data-stu-id="1be73-266">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="1be73-267">No `Startup.ConfigureServices` , adicione o seguinte código para configurar o cabeçalho do qual o middleware cria um certificado:</span><span class="sxs-lookup"><span data-stu-id="1be73-267">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="1be73-268">Outros proxies da Web</span><span class="sxs-lookup"><span data-stu-id="1be73-268">Other web proxies</span></span>

<span data-ttu-id="1be73-269">Se for usado um proxy que não seja o Application Request Routing do IIS ou Azure App do serviço (ARR), configure o proxy para encaminhar o certificado que ele recebeu em um cabeçalho HTTP.</span><span class="sxs-lookup"><span data-stu-id="1be73-269">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="1be73-270">No `Startup.Configure` , adicione o seguinte código antes da chamada para `app.UseAuthentication();` :</span><span class="sxs-lookup"><span data-stu-id="1be73-270">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="1be73-271">Configure o middleware de encaminhamento de certificado para especificar o nome do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="1be73-271">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="1be73-272">No `Startup.ConfigureServices` , adicione o seguinte código para configurar o cabeçalho do qual o middleware cria um certificado:</span><span class="sxs-lookup"><span data-stu-id="1be73-272">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="1be73-273">Se o proxy não estiver codificando o certificado em Base64 (como é o caso com Nginx), defina a `HeaderConverter` opção.</span><span class="sxs-lookup"><span data-stu-id="1be73-273">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="1be73-274">Considere o exemplo a seguir em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1be73-274">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddCertificateForwarding(options =>
{
    options.CertificateHeader = "YOUR_CUSTOM_HEADER_NAME";
    options.HeaderConverter = (headerValue) => 
    {
        var clientCertificate = 
           /* some conversion logic to create an X509Certificate2 */
        return clientCertificate;
    }
});
```

## <a name="troubleshoot"></a><span data-ttu-id="1be73-275">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="1be73-275">Troubleshoot</span></span>

<span data-ttu-id="1be73-276">Quando os cabeçalhos não são encaminhados conforme o esperado, habilite [registro em log](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="1be73-276">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="1be73-277">Se os logs não fornecerem informações suficientes para solucionar o problema, enumere os cabeçalhos de solicitação recebidos pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="1be73-277">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="1be73-278">Use middleware embutido para gravar cabeçalhos de solicitação para uma resposta do aplicativo ou para log dos cabeçalhos.</span><span class="sxs-lookup"><span data-stu-id="1be73-278">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="1be73-279">Para gravar os cabeçalhos de resposta do aplicativo, coloque o seguinte middleware terminal embutido imediatamente após a chamada para <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="1be73-279">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="1be73-280">Você pode gravar em logs em vez de no corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="1be73-280">You can write to logs instead of the response body.</span></span> <span data-ttu-id="1be73-281">A gravação em logs permite que o site funcione normalmente durante a depuração.</span><span class="sxs-lookup"><span data-stu-id="1be73-281">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="1be73-282">Para gravar em logs em vez de no corpo da resposta:</span><span class="sxs-lookup"><span data-stu-id="1be73-282">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="1be73-283">Injete `ILogger<Startup>` na classe `Startup` conforme descrito em [Criar logs na inicialização](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="1be73-283">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="1be73-284">Coloque o seguinte middleware embutido imediatamente após a chamada para <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="1be73-284">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="1be73-285">Quando processado, os valores `X-Forwarded-{For|Proto|Host}` são movidos para `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="1be73-285">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="1be73-286">Quando houver vários valores em um determinado cabeçalho, o middleware de cabeçalhos encaminhados os processará na ordem inversa, da direita para esquerda.</span><span class="sxs-lookup"><span data-stu-id="1be73-286">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="1be73-287">O padrão `ForwardLimit` é `ForwardLimit` (um), portanto, apenas o valor mais à direita dos cabeçalhos será processado, a menos que o valor de `1` seja aumentado.</span><span class="sxs-lookup"><span data-stu-id="1be73-287">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="1be73-288">O IP de remoto original da solicitação precisa corresponder a uma entrada nas listas `KnownProxies` ou `KnownNetworks` antes dos cabeçalhos encaminhados serem processados.</span><span class="sxs-lookup"><span data-stu-id="1be73-288">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="1be73-289">Isso limita a falsificação de cabeçalho por não aceitar encaminhadores de proxies não confiáveis.</span><span class="sxs-lookup"><span data-stu-id="1be73-289">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="1be73-290">Quando um proxy desconhecido é detectado, o registro em log indica o endereço dele:</span><span class="sxs-lookup"><span data-stu-id="1be73-290">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="1be73-291">No exemplo anterior, 10.0.0.100 é um servidor proxy.</span><span class="sxs-lookup"><span data-stu-id="1be73-291">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="1be73-292">Se o servidor for um proxy confiável, adicione o endereço IP do servidor a `KnownProxies` (ou adicione uma rede confiável a `KnownNetworks`) em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1be73-292">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1be73-293">Para obter mais informações, consulte a seção [Opções de middleware de cabeçalhos encaminhados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="1be73-293">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="1be73-294">Permitir que somente proxies e redes confiáveis encaminhem os cabeçalhos.</span><span class="sxs-lookup"><span data-stu-id="1be73-294">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="1be73-295">Caso contrário, podem ocorrer ataques de [falsificação de IP](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="1be73-295">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1be73-296">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="1be73-296">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="1be73-297">Microsoft Security Advisory CVE-2018-0787: vulnerabilidade de elevação de privilégio do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1be73-297">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1be73-298">Na configuração recomendada para o ASP.NET Core, o aplicativo é hospedado usando IIS/Módulo do ASP.NET Core, Nginx ou Apache.</span><span class="sxs-lookup"><span data-stu-id="1be73-298">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="1be73-299">Servidores proxy, balanceadores de carga e outros dispositivos de rede geralmente ocultam informações sobre a solicitação antes de ela alcançar o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="1be73-299">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="1be73-300">Quando solicitações HTTPS são passadas por proxy por HTTP, o esquema original (HTTPS) é perdido e deve ser encaminhado em um cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="1be73-300">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="1be73-301">Devido a um aplicativo receber uma solicitação do proxy e não de sua origem verdadeira na Internet ou rede corporativa, o endereço IP do cliente originador também deve ser encaminhado em um cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="1be73-301">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="1be73-302">Essas informações podem ser importantes no processamento de solicitações, por exemplo, em redirecionamentos, autenticação, geração de link, avaliação de política e localização geográfica do cliente.</span><span class="sxs-lookup"><span data-stu-id="1be73-302">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="1be73-303">Cabeçalhos encaminhados</span><span class="sxs-lookup"><span data-stu-id="1be73-303">Forwarded headers</span></span>

<span data-ttu-id="1be73-304">Por convenção, os proxies encaminham informações em cabeçalhos HTTP.</span><span class="sxs-lookup"><span data-stu-id="1be73-304">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="1be73-305">Cabeçalho</span><span class="sxs-lookup"><span data-stu-id="1be73-305">Header</span></span> | <span data-ttu-id="1be73-306">Descrição</span><span class="sxs-lookup"><span data-stu-id="1be73-306">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="1be73-307">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="1be73-307">X-Forwarded-For</span></span> | <span data-ttu-id="1be73-308">Contém informações sobre o cliente que iniciou a solicitação e os proxies subsequentes em uma cadeia de proxies.</span><span class="sxs-lookup"><span data-stu-id="1be73-308">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="1be73-309">Esse parâmetro pode conter endereços IP (e, opcionalmente, os números de porta).</span><span class="sxs-lookup"><span data-stu-id="1be73-309">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="1be73-310">Em uma cadeia de servidores proxy, o primeiro parâmetro indica o cliente em que a solicitação foi feita pela primeira vez.</span><span class="sxs-lookup"><span data-stu-id="1be73-310">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="1be73-311">Depois, vêm os identificadores de proxy subsequentes.</span><span class="sxs-lookup"><span data-stu-id="1be73-311">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="1be73-312">O último proxy na cadeia não está na lista de parâmetros.</span><span class="sxs-lookup"><span data-stu-id="1be73-312">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="1be73-313">O endereço IP do último proxy (e opcionalmente um número da porta) estão disponíveis como o endereço IP remoto na camada de transporte.</span><span class="sxs-lookup"><span data-stu-id="1be73-313">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="1be73-314">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="1be73-314">X-Forwarded-Proto</span></span> | <span data-ttu-id="1be73-315">O valor do esquema de origem (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="1be73-315">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="1be73-316">O valor também pode ser uma lista de esquemas se a solicitação percorreu vários proxies.</span><span class="sxs-lookup"><span data-stu-id="1be73-316">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="1be73-317">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="1be73-317">X-Forwarded-Host</span></span> | <span data-ttu-id="1be73-318">O valor original do campo de cabeçalho do host.</span><span class="sxs-lookup"><span data-stu-id="1be73-318">The original value of the Host header field.</span></span> <span data-ttu-id="1be73-319">Normalmente, os proxies não modificam o cabeçalho do host.</span><span class="sxs-lookup"><span data-stu-id="1be73-319">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="1be73-320">Veja [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) para obter informações sobre uma vulnerabilidade de elevação de privilégios que afeta os sistemas em que o proxy não valida ou restringe cabeçalhos de Host a valores válidos conhecidos.</span><span class="sxs-lookup"><span data-stu-id="1be73-320">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="1be73-321">O middleware de cabeçalhos encaminhados, do pacote [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/), lê esses cabeçalhos e preenche os campos associados em <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="1be73-321">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="1be73-322">As atualizações de middleware:</span><span class="sxs-lookup"><span data-stu-id="1be73-322">The middleware updates:</span></span>

* <span data-ttu-id="1be73-323">[HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): definido usando o `X-Forwarded-For` valor do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="1be73-323">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="1be73-324">Configurações adicionais influenciam o modo como o middleware define `RemoteIpAddress`.</span><span class="sxs-lookup"><span data-stu-id="1be73-324">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="1be73-325">Para obter detalhes, veja as [Opções de middleware de cabeçalhos encaminhados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="1be73-325">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="1be73-326">[HttpContext. Request. Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): definido usando o `X-Forwarded-Proto` valor do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="1be73-326">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="1be73-327">[HttpContext. Request. host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): definido usando o `X-Forwarded-Host` valor do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="1be73-327">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="1be73-328">As [configurações padrão](#forwarded-headers-middleware-options) de middleware de cabeçalhos encaminhados podem ser definidas.</span><span class="sxs-lookup"><span data-stu-id="1be73-328">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="1be73-329">As configurações padrão são:</span><span class="sxs-lookup"><span data-stu-id="1be73-329">The default settings are:</span></span>

* <span data-ttu-id="1be73-330">Há apenas *um proxy* entre o aplicativo e a origem das solicitações.</span><span class="sxs-lookup"><span data-stu-id="1be73-330">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="1be73-331">Somente os endereços de loopback são configurados para proxies conhecidos e redes conhecidas.</span><span class="sxs-lookup"><span data-stu-id="1be73-331">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="1be73-332">Os cabeçalhos encaminhados são nomeados `X-Forwarded-For` e `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="1be73-332">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="1be73-333">Nem todos os dispositivos de rede adicionam os cabeçalhos `X-Forwarded-For` e `X-Forwarded-Proto` sem configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="1be73-333">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="1be73-334">Consulte as diretrizes do fabricante do dispositivo se as solicitações de proxies não contiverem esses cabeçalhos quando atingirem o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1be73-334">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="1be73-335">Se o dispositivo usar nomes de cabeçalho diferente de `X-Forwarded-For` e `X-Forwarded-Proto`, defina as opções <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> e <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> para corresponder aos nomes de cabeçalho usados pelo dispositivo.</span><span class="sxs-lookup"><span data-stu-id="1be73-335">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="1be73-336">Para obter mais informações, consulte [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) (Opções de middleware de cabeçalhos encaminhados) e [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names) (Configuração de um proxy que usa diferentes nomes de cabeçalho).</span><span class="sxs-lookup"><span data-stu-id="1be73-336">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="1be73-337">O IIS/IIS Express e o Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1be73-337">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="1be73-338">O Middleware de Cabeçalhos Encaminhados é habilitado por padrão pelo [Middleware de integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) quando o aplicativo é hospedado [fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) atrás do IIS e do Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1be73-338">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="1be73-339">O middleware de cabeçalhos encaminhados é ativado para ser executado primeiro no pipeline de middleware, com uma configuração restrita específica para o Módulo do ASP.NET Core devido a questões de confiança com cabeçalhos encaminhados (por exemplo, [falsificação de IP](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="1be73-339">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="1be73-340">O middleware está configurado para encaminhar os cabeçalhos `X-Forwarded-For` e `X-Forwarded-Proto` e é restrito a um proxy de localhost único.</span><span class="sxs-lookup"><span data-stu-id="1be73-340">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="1be73-341">Se configuração adicional for necessária, veja as [Opções de middleware de cabeçalhos encaminhados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="1be73-341">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="1be73-342">Outros cenários de servidor proxy e balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="1be73-342">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="1be73-343">Além de usar a [Integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) quando hospedar [fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model), o Middleware de cabeçalhos encaminhados não é habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="1be73-343">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="1be73-344">O middleware de cabeçalhos encaminhados deve ser habilitado para um aplicativo para processar cabeçalhos encaminhados com <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="1be73-344">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="1be73-345">Após a habilitação do middleware, se nenhum <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> for especificado para o middleware, o [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) padrão será [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="1be73-345">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="1be73-346">Configure o middleware com <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> para encaminhar os cabeçalhos `X-Forwarded-For` e `X-Forwarded-Proto` em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1be73-346">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1be73-347">Invocar o método <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> no `Startup.Configure` antes de chamar outro middleware:</span><span class="sxs-lookup"><span data-stu-id="1be73-347">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="1be73-348">Se nenhum <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> for especificado no `Startup.ConfigureServices` ou diretamente no método de extensão com <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, os cabeçalhos padrão para encaminhar serão [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="1be73-348">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="1be73-349">A propriedade <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> deve ser configurada com os cabeçalhos para encaminhar.</span><span class="sxs-lookup"><span data-stu-id="1be73-349">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="1be73-350">Configuração de Nginx</span><span class="sxs-lookup"><span data-stu-id="1be73-350">Nginx configuration</span></span>

<span data-ttu-id="1be73-351">Para encaminhar os cabeçalhos `X-Forwarded-For` e `X-Forwarded-Proto`, consulte <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="1be73-351">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="1be73-352">Para obter mais informações, veja [NGINX: usando o cabeçalho encaminhado](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="1be73-352">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="1be73-353">Configuração do Apache</span><span class="sxs-lookup"><span data-stu-id="1be73-353">Apache configuration</span></span>

<span data-ttu-id="1be73-354">`X-Forwarded-For` é adicionado automaticamente (veja [mod_proxy do módulo do Apache: cabeçalhos de solicitação de proxy reverso](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="1be73-354">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="1be73-355">Para obter informações sobre como encaminhar o cabeçalho `X-Forwarded-Proto`, consulte <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="1be73-355">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="1be73-356">Opções de middleware de cabeçalhos encaminhados</span><span class="sxs-lookup"><span data-stu-id="1be73-356">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="1be73-357"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> controla o comportamento do middleware de cabeçalhos encaminhados.</span><span class="sxs-lookup"><span data-stu-id="1be73-357"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="1be73-358">O seguinte exemplo altera os valores padrão:</span><span class="sxs-lookup"><span data-stu-id="1be73-358">The following example changes the default values:</span></span>

* <span data-ttu-id="1be73-359">Limite o número de entradas nos cabeçalhos encaminhados a `2`.</span><span class="sxs-lookup"><span data-stu-id="1be73-359">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="1be73-360">Adicione um endereço de proxy conhecido de `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="1be73-360">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="1be73-361">Altere o nome do cabeçalho encaminhado do padrão `X-Forwarded-For` para `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="1be73-361">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="1be73-362">Opção</span><span class="sxs-lookup"><span data-stu-id="1be73-362">Option</span></span> | <span data-ttu-id="1be73-363">Descrição</span><span class="sxs-lookup"><span data-stu-id="1be73-363">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="1be73-364">Restringe os hosts com o cabeçalho `X-Forwarded-Host` para os valores fornecidos.</span><span class="sxs-lookup"><span data-stu-id="1be73-364">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="1be73-365">Os valores são comparados usando ordinal-ignore-case.</span><span class="sxs-lookup"><span data-stu-id="1be73-365">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="1be73-366">Os número de porta devem ser excluídos.</span><span class="sxs-lookup"><span data-stu-id="1be73-366">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="1be73-367">Se a lista estiver vazia, todos os hosts serão permitidos.</span><span class="sxs-lookup"><span data-stu-id="1be73-367">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="1be73-368">Um curinga de nível superior `*` permite todos os hosts não vazios.</span><span class="sxs-lookup"><span data-stu-id="1be73-368">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="1be73-369">Curingas de subdomínio são permitidos, mas não correspondem ao domínio raiz.</span><span class="sxs-lookup"><span data-stu-id="1be73-369">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="1be73-370">Por exemplo, `*.contoso.com` corresponde o subdomínio `foo.contoso.com`, mas não ao domínio raiz `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="1be73-370">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="1be73-371">Nomes do host Unicode são permitidos, mas são convertidos em [Punycode](https://tools.ietf.org/html/rfc3492) para correspondência.</span><span class="sxs-lookup"><span data-stu-id="1be73-371">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="1be73-372">[Endereços IPv6](https://tools.ietf.org/html/rfc4291) devem incluir colchetes delimitadores e estar no [formato convencional](https://tools.ietf.org/html/rfc4291#section-2.2) (por exemplo, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="1be73-372">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="1be73-373">Endereços IPv6 não têm caso especial para verificar se há igualdade lógica entre formatos diferentes, e nenhuma canonicalização é executada.</span><span class="sxs-lookup"><span data-stu-id="1be73-373">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="1be73-374">Falha ao restringir os hosts permitidos pode permitir que um atacante falsifique links gerados pelo serviço.</span><span class="sxs-lookup"><span data-stu-id="1be73-374">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="1be73-375">O valor padrão é um `IList<string>` vazio.</span><span class="sxs-lookup"><span data-stu-id="1be73-375">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="1be73-376">Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="1be73-376">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="1be73-377">Esta opção é usada quando o proxy/encaminhador não usa o cabeçalho `X-Forwarded-For`, mas usa algum outro cabeçalho para encaminhar as informações.</span><span class="sxs-lookup"><span data-stu-id="1be73-377">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="1be73-378">O padrão é `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="1be73-378">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="1be73-379">Identifica quais encaminhadores devem ser processados.</span><span class="sxs-lookup"><span data-stu-id="1be73-379">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="1be73-380">Veja o [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) para a lista de campos aplicáveis.</span><span class="sxs-lookup"><span data-stu-id="1be73-380">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="1be73-381">Os valores típicos atribuídos a essa propriedade são `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="1be73-381">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="1be73-382">O valor padrão é [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="1be73-382">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="1be73-383">Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="1be73-383">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="1be73-384">Esta opção é usada quando o proxy/encaminhador não usa o cabeçalho `X-Forwarded-Host`, mas usa algum outro cabeçalho para encaminhar as informações.</span><span class="sxs-lookup"><span data-stu-id="1be73-384">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="1be73-385">O padrão é `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="1be73-385">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="1be73-386">Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="1be73-386">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="1be73-387">Esta opção é usada quando o proxy/encaminhador não usa o cabeçalho `X-Forwarded-Proto`, mas usa algum outro cabeçalho para encaminhar as informações.</span><span class="sxs-lookup"><span data-stu-id="1be73-387">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="1be73-388">O padrão é `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="1be73-388">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="1be73-389">Limita o número de entradas nos cabeçalhos que são processados.</span><span class="sxs-lookup"><span data-stu-id="1be73-389">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="1be73-390">Defina para `null` para desabilitar o limite, mas isso só deve ser feito se `KnownProxies` ou `KnownNetworks` estão configurados.</span><span class="sxs-lookup"><span data-stu-id="1be73-390">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="1be73-391">A definição de um valor não `null` é feita por precaução (o que não implica em uma garantia), para proteção contra proxies mal configurados e solicitações mal-intencionadas que chegam de canais secundários na rede.</span><span class="sxs-lookup"><span data-stu-id="1be73-391">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="1be73-392">O middleware de cabeçalhos encaminhados processa cabeçalhos na ordem inversa, da direita para esquerda.</span><span class="sxs-lookup"><span data-stu-id="1be73-392">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="1be73-393">Se o valor padrão for usado (`1`), apenas o valor mais à direita dos cabeçalhos será processado, a menos que o valor de `ForwardLimit` seja aumentado.</span><span class="sxs-lookup"><span data-stu-id="1be73-393">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="1be73-394">O padrão é `1`.</span><span class="sxs-lookup"><span data-stu-id="1be73-394">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="1be73-395">Intervalos de endereços de redes conhecidas dos quais aceitar cabeçalhos encaminhados.</span><span class="sxs-lookup"><span data-stu-id="1be73-395">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="1be73-396">Forneça os intervalos de IP usando notação de CIDR (Roteamento entre Domínios sem Classificação).</span><span class="sxs-lookup"><span data-stu-id="1be73-396">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="1be73-397">O servidor usa soquetes de modo duplo e os endereços IPv4 são fornecidos em um formato IPv6 (por exemplo, `10.0.0.1` no formato IPv4 representado no formato IPv6 como `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="1be73-397">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="1be73-398">Confira [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="1be73-398">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="1be73-399">Para determinar se este formato é obrigatório, veja [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="1be73-399">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="1be73-400">Para saber mais, veja a seção [Configuração de endereços IPv4 representados como endereços IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="1be73-400">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="1be73-401">O padrão é uma `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> que contém uma única entrada para `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="1be73-401">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="1be73-402">Endereços de proxies conhecidos dos quais aceitar cabeçalhos encaminhados.</span><span class="sxs-lookup"><span data-stu-id="1be73-402">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="1be73-403">Use `KnownProxies` especificar correspondências exatas de endereço IP.</span><span class="sxs-lookup"><span data-stu-id="1be73-403">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="1be73-404">O servidor usa soquetes de modo duplo e os endereços IPv4 são fornecidos em um formato IPv6 (por exemplo, `10.0.0.1` no formato IPv4 representado no formato IPv6 como `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="1be73-404">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="1be73-405">Confira [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="1be73-405">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="1be73-406">Para determinar se este formato é obrigatório, veja [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="1be73-406">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="1be73-407">Para saber mais, veja a seção [Configuração de endereços IPv4 representados como endereços IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="1be73-407">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="1be73-408">O padrão é uma `IList`\<<xref:System.Net.IPAddress>> que contém uma única entrada para `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="1be73-408">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="1be73-409">Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="1be73-409">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="1be73-410">O padrão é `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="1be73-410">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="1be73-411">Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="1be73-411">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="1be73-412">O padrão é `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="1be73-412">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="1be73-413">Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="1be73-413">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="1be73-414">O padrão é `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="1be73-414">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="1be73-415">Exigem o número de valores de cabeçalho a serem sincronizados entre os [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) sendo processados.</span><span class="sxs-lookup"><span data-stu-id="1be73-415">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="1be73-416">O padrão no ASP.NET Core 1.x é `true`.</span><span class="sxs-lookup"><span data-stu-id="1be73-416">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="1be73-417">O padrão no ASP.NET Core 2.0 ou posterior é `false`.</span><span class="sxs-lookup"><span data-stu-id="1be73-417">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="1be73-418">Cenários e casos de uso</span><span class="sxs-lookup"><span data-stu-id="1be73-418">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="1be73-419">Quando não é possível adicionar cabeçalhos encaminhados e todas as solicitações são seguras</span><span class="sxs-lookup"><span data-stu-id="1be73-419">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="1be73-420">Em alguns casos, pode não ser possível adicionar cabeçalhos encaminhados para as solicitações passadas por proxy ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1be73-420">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="1be73-421">Se o proxy está impondo que todas as solicitações externas públicas sejam HTTPS, o esquema pode ser definido manualmente em `Startup.Configure` antes de usar qualquer tipo de middleware:</span><span class="sxs-lookup"><span data-stu-id="1be73-421">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="1be73-422">Esse código pode ser desabilitado com uma variável de ambiente ou outra definição de configuração em um ambiente de preparo ou de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="1be73-422">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="1be73-423">Lidar com o caminho base e proxies que alteram o caminho da solicitação</span><span class="sxs-lookup"><span data-stu-id="1be73-423">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="1be73-424">Alguns proxies passam o caminho intacto, mas com um caminho base de aplicativo que deve ser removido para que o roteamento funcione corretamente.</span><span class="sxs-lookup"><span data-stu-id="1be73-424">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="1be73-425">O middleware de [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) divide o caminho em [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) e o caminho base do aplicativo em [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="1be73-425">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="1be73-426">Se `/foo` é o caminho base do aplicativo para um caminho de proxy passado como `/foo/api/1`, o middleware define `Request.PathBase` para `/foo` e `Request.Path` para `/api/1` com o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="1be73-426">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="1be73-427">O caminho original e o caminho base são reaplicados quando o middleware é chamado novamente na ordem inversa.</span><span class="sxs-lookup"><span data-stu-id="1be73-427">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="1be73-428">Para obter mais informações sobre o processamento de ordem de middleware, consulte <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="1be73-428">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="1be73-429">Se o proxy cortar o caminho (por exemplo, encaminhando `/foo/api/1` para `/api/1`), corrija redirecionamentos e links definindo a propriedade [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) da solicitação:</span><span class="sxs-lookup"><span data-stu-id="1be73-429">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="1be73-430">Se o proxy estiver adicionando dados de caminho, descarte a parte do caminho para corrigir os redirecionamentos e links usando <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> e atribuindo à propriedade <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>:</span><span class="sxs-lookup"><span data-stu-id="1be73-430">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="1be73-431">Configuração de um proxy que usa diferentes nomes de cabeçalho</span><span class="sxs-lookup"><span data-stu-id="1be73-431">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="1be73-432">Se o proxy não usar cabeçalhos nomeados `X-Forwarded-For` e `X-Forwarded-Proto` para encaminhar a porta/endereço do proxy e as informações de origem do esquema, defina as opções <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> e <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> para corresponder aos nomes de cabeçalho usados pelo proxy:</span><span class="sxs-lookup"><span data-stu-id="1be73-432">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="1be73-433">Configuração de endereços IPv4 representados como endereços IPv6</span><span class="sxs-lookup"><span data-stu-id="1be73-433">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="1be73-434">O servidor usa soquetes de modo duplo e os endereços IPv4 são fornecidos em um formato IPv6 (por exemplo, `10.0.0.1` no formato IPv4 representado no formato IPv6 como `::ffff:10.0.0.1` ou `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="1be73-434">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="1be73-435">Confira [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="1be73-435">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="1be73-436">Para determinar se este formato é obrigatório, veja [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="1be73-436">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="1be73-437">No exemplo a seguir, um endereço de rede que fornece cabeçalhos encaminhados é adicionado à lista `KnownNetworks` no formato IPv6.</span><span class="sxs-lookup"><span data-stu-id="1be73-437">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="1be73-438">Endereço IPv4: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="1be73-438">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="1be73-439">Conversão em endereço IPv6: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="1be73-439">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="1be73-440">Comprimento do prefixo convertido: 104</span><span class="sxs-lookup"><span data-stu-id="1be73-440">Converted prefix length: 104</span></span>

<span data-ttu-id="1be73-441">Você pode também fornecer o endereço no formato hexadecimal (`10.11.12.1`, representado no formato IPv6 como `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="1be73-441">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="1be73-442">Quando converter um endereço IPv4 em IPv6, adicione 96 ao comprimento do prefixo CIDR (`8` no exemplo) para levar em conta o prefixo IPv6 adicional `::ffff:` (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="1be73-442">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="1be73-443">Encaminhar o esquema para proxies reversos não IIS e Linux</span><span class="sxs-lookup"><span data-stu-id="1be73-443">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="1be73-444">Os aplicativos que chamam <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> e <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> colocam um site em um loop infinito se implantado em um Serviço de Aplicativo do Linux do Azure, VM (máquina virtual) do Azure no Linux ou atrás de outro proxy reverso além do IIS.</span><span class="sxs-lookup"><span data-stu-id="1be73-444">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="1be73-445">O TLS é encerrado pelo proxy inverso e o Kestrel não é informado do esquema de solicitação correto.</span><span class="sxs-lookup"><span data-stu-id="1be73-445">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="1be73-446">OAuth e OIDC também falham nessa configuração, pois geram redirecionamentos incorretos.</span><span class="sxs-lookup"><span data-stu-id="1be73-446">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="1be73-447"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adiciona e configura o Middleware de Cabeçalhos Encaminhados quando executado atrás do IIS, mas não há nenhuma configuração automática correspondente para Linux (integração do Apache ou do Nginx).</span><span class="sxs-lookup"><span data-stu-id="1be73-447"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="1be73-448">Para encaminhar o esquema do proxy em cenários não de IIS, adicione e configure o Middleware de Cabeçalhos Encaminhados.</span><span class="sxs-lookup"><span data-stu-id="1be73-448">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="1be73-449">No `Startup.ConfigureServices`, use o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="1be73-449">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="troubleshoot"></a><span data-ttu-id="1be73-450">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="1be73-450">Troubleshoot</span></span>

<span data-ttu-id="1be73-451">Quando os cabeçalhos não são encaminhados conforme o esperado, habilite [registro em log](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="1be73-451">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="1be73-452">Se os logs não fornecerem informações suficientes para solucionar o problema, enumere os cabeçalhos de solicitação recebidos pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="1be73-452">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="1be73-453">Use middleware embutido para gravar cabeçalhos de solicitação para uma resposta do aplicativo ou para log dos cabeçalhos.</span><span class="sxs-lookup"><span data-stu-id="1be73-453">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="1be73-454">Para gravar os cabeçalhos de resposta do aplicativo, coloque o seguinte middleware terminal embutido imediatamente após a chamada para <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="1be73-454">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="1be73-455">Você pode gravar em logs em vez de no corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="1be73-455">You can write to logs instead of the response body.</span></span> <span data-ttu-id="1be73-456">A gravação em logs permite que o site funcione normalmente durante a depuração.</span><span class="sxs-lookup"><span data-stu-id="1be73-456">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="1be73-457">Para gravar em logs em vez de no corpo da resposta:</span><span class="sxs-lookup"><span data-stu-id="1be73-457">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="1be73-458">Injete `ILogger<Startup>` na classe `Startup` conforme descrito em [Criar logs na inicialização](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="1be73-458">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="1be73-459">Coloque o seguinte middleware embutido imediatamente após a chamada para <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="1be73-459">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="1be73-460">Quando processado, os valores `X-Forwarded-{For|Proto|Host}` são movidos para `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="1be73-460">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="1be73-461">Quando houver vários valores em um determinado cabeçalho, o middleware de cabeçalhos encaminhados os processará na ordem inversa, da direita para esquerda.</span><span class="sxs-lookup"><span data-stu-id="1be73-461">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="1be73-462">O padrão `ForwardLimit` é `ForwardLimit` (um), portanto, apenas o valor mais à direita dos cabeçalhos será processado, a menos que o valor de `1` seja aumentado.</span><span class="sxs-lookup"><span data-stu-id="1be73-462">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="1be73-463">O IP de remoto original da solicitação precisa corresponder a uma entrada nas listas `KnownProxies` ou `KnownNetworks` antes dos cabeçalhos encaminhados serem processados.</span><span class="sxs-lookup"><span data-stu-id="1be73-463">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="1be73-464">Isso limita a falsificação de cabeçalho por não aceitar encaminhadores de proxies não confiáveis.</span><span class="sxs-lookup"><span data-stu-id="1be73-464">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="1be73-465">Quando um proxy desconhecido é detectado, o registro em log indica o endereço dele:</span><span class="sxs-lookup"><span data-stu-id="1be73-465">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="1be73-466">No exemplo anterior, 10.0.0.100 é um servidor proxy.</span><span class="sxs-lookup"><span data-stu-id="1be73-466">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="1be73-467">Se o servidor for um proxy confiável, adicione o endereço IP do servidor a `KnownProxies` (ou adicione uma rede confiável a `KnownNetworks`) em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1be73-467">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1be73-468">Para obter mais informações, consulte a seção [Opções de middleware de cabeçalhos encaminhados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="1be73-468">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="1be73-469">Permitir que somente proxies e redes confiáveis encaminhem os cabeçalhos.</span><span class="sxs-lookup"><span data-stu-id="1be73-469">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="1be73-470">Caso contrário, podem ocorrer ataques de [falsificação de IP](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="1be73-470">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1be73-471">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="1be73-471">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="1be73-472">Microsoft Security Advisory CVE-2018-0787: vulnerabilidade de elevação de privilégio do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1be73-472">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end
