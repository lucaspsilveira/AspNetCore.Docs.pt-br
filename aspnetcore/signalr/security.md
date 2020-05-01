---
title: Considerações de segurança no ASP.NET CoreSignalR
author: bradygaster
description: Saiba como usar a autenticação e a autorização no SignalRASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- SignalR
uid: signalr/security
ms.openlocfilehash: 5a804425752a26cc5f3185f2b43dd9219dec312d
ms.sourcegitcommit: f9a5069577e8f7c53f8bcec9e13e117950f4f033
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82558971"
---
# <a name="security-considerations-in-aspnet-core-signalr"></a><span data-ttu-id="b4f82-103">Considerações de segurança no ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="b4f82-103">Security considerations in ASP.NET Core SignalR</span></span>

<span data-ttu-id="b4f82-104">Por [Andrew Stanton-enfermaria](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="b4f82-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="b4f82-105">Este artigo fornece informações sobre como proteger SignalRo.</span><span class="sxs-lookup"><span data-stu-id="b4f82-105">This article provides information on securing SignalR.</span></span>

## <a name="cross-origin-resource-sharing"></a><span data-ttu-id="b4f82-106">Compartilhamento de recursos entre origens</span><span class="sxs-lookup"><span data-stu-id="b4f82-106">Cross-origin resource sharing</span></span>

<span data-ttu-id="b4f82-107">O [CORS (compartilhamento de recursos entre origens)](https://www.w3.org/TR/cors/) pode ser usado para permitir conexões entre SignalR origens no navegador.</span><span class="sxs-lookup"><span data-stu-id="b4f82-107">[Cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) can be used to allow cross-origin SignalR connections in the browser.</span></span> <span data-ttu-id="b4f82-108">Se o código JavaScript estiver hospedado em um domínio diferente do SignalR aplicativo, o [middleware CORS](xref:security/cors) deverá ser habilitado para permitir que o JavaScript se conecte ao SignalR aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b4f82-108">If JavaScript code is hosted on a different domain from the SignalR app, [CORS middleware](xref:security/cors) must be enabled to allow the JavaScript to connect to the SignalR app.</span></span> <span data-ttu-id="b4f82-109">Permitir solicitações entre origens somente de domínios nos quais você confia ou controla.</span><span class="sxs-lookup"><span data-stu-id="b4f82-109">Allow cross-origin requests only from domains you trust or control.</span></span> <span data-ttu-id="b4f82-110">Por exemplo: </span><span class="sxs-lookup"><span data-stu-id="b4f82-110">For example:</span></span>

* <span data-ttu-id="b4f82-111">Seu site está hospedado em`http://www.example.com`</span><span class="sxs-lookup"><span data-stu-id="b4f82-111">Your site is hosted on `http://www.example.com`</span></span>
* <span data-ttu-id="b4f82-112">Seu SignalR aplicativo está hospedado em`http://signalr.example.com`</span><span class="sxs-lookup"><span data-stu-id="b4f82-112">Your SignalR app is hosted on `http://signalr.example.com`</span></span>

<span data-ttu-id="b4f82-113">O CORS deve ser configurado no SignalR aplicativo para permitir somente a origem `www.example.com`.</span><span class="sxs-lookup"><span data-stu-id="b4f82-113">CORS should be configured in the SignalR app to only allow the origin `www.example.com`.</span></span>

<span data-ttu-id="b4f82-114">Para obter mais informações sobre como configurar o CORS, consulte [habilitar solicitações entre origens (CORS)](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="b4f82-114">For more information on configuring CORS, see [Enable Cross-Origin Requests (CORS)](xref:security/cors).</span></span> SignalR<span data-ttu-id="b4f82-115">**requer** as seguintes políticas de CORS:</span><span class="sxs-lookup"><span data-stu-id="b4f82-115"> **requires** the following CORS policies:</span></span>

* <span data-ttu-id="b4f82-116">Permitir as origens esperadas específicas.</span><span class="sxs-lookup"><span data-stu-id="b4f82-116">Allow the specific expected origins.</span></span> <span data-ttu-id="b4f82-117">Permitir qualquer origem é possível, mas **não** é seguro ou recomendado.</span><span class="sxs-lookup"><span data-stu-id="b4f82-117">Allowing any origin is possible but is **not** secure or recommended.</span></span>
* <span data-ttu-id="b4f82-118">Os métodos `GET` http `POST` e devem ser permitidos.</span><span class="sxs-lookup"><span data-stu-id="b4f82-118">HTTP methods `GET` and `POST` must be allowed.</span></span>
* <span data-ttu-id="b4f82-119">As credenciais devem ser permitidas para que as sessões adesivas baseadas em cookie funcionem corretamente.</span><span class="sxs-lookup"><span data-stu-id="b4f82-119">Credentials must be allowed in order for cookie-based sticky sessions to work correctly.</span></span> <span data-ttu-id="b4f82-120">Eles devem ser habilitados mesmo quando a autenticação não é usada.</span><span class="sxs-lookup"><span data-stu-id="b4f82-120">They must be enabled even when authentication isn't used.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b4f82-121">No entanto, no 5,0, fornecemos uma opção no cliente TypeScript para não usar credenciais.</span><span class="sxs-lookup"><span data-stu-id="b4f82-121">However, in 5.0 we have provided an option in the TypeScript client to not use credentials.</span></span>
<span data-ttu-id="b4f82-122">A opção de não usar credenciais só deve ser usada quando você souber 100% de que as credenciais como cookies não são necessárias em seu aplicativo (cookies são usados pelo serviço de aplicativo do Azure ao usar vários servidores para sessões adesivas).</span><span class="sxs-lookup"><span data-stu-id="b4f82-122">The option to not use credentials should only be used when you know 100% that credentials like Cookies are not needed in your app (cookies are used by azure app service when using multiple servers for sticky sessions).</span></span>

::: moniker-end

<span data-ttu-id="b4f82-123">Por exemplo, a seguinte política de CORS permite SignalR que um cliente de `https://example.com` navegador hospedado no SignalR acesse o `https://signalr.example.com`aplicativo hospedado em:</span><span class="sxs-lookup"><span data-stu-id="b4f82-123">For example, the following CORS policy allows a SignalR browser client hosted on `https://example.com` to access the SignalR app hosted on `https://signalr.example.com`:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // ... other middleware ...

    // Make sure the CORS middleware is ahead of SignalR.
    app.UseCors(builder =>
    {
        builder.WithOrigins("https://example.com")
            .AllowAnyHeader()
            .WithMethods("GET", "POST")
            .AllowCredentials();
    });

    // ... other middleware ...
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chatHub");
    });

    // ... other middleware ...
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

::: moniker-end

## <a name="websocket-origin-restriction"></a><span data-ttu-id="b4f82-124">Restrição de origem do WebSocket</span><span class="sxs-lookup"><span data-stu-id="b4f82-124">WebSocket Origin Restriction</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b4f82-125">As proteções fornecidas pelo CORS não se aplicam ao WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b4f82-125">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="b4f82-126">Para a restrição de origem em WebSockets, leia a [restrição de origem de WebSockets](xref:fundamentals/websockets#websocket-origin-restriction).</span><span class="sxs-lookup"><span data-stu-id="b4f82-126">For origin restriction on WebSockets, read [WebSockets origin restriction](xref:fundamentals/websockets#websocket-origin-restriction).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="b4f82-127">As proteções fornecidas pelo CORS não se aplicam ao WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b4f82-127">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="b4f82-128">Navegadores **não**:</span><span class="sxs-lookup"><span data-stu-id="b4f82-128">Browsers do **not**:</span></span>

* <span data-ttu-id="b4f82-129">Executam solicitações de simulação de CORS.</span><span class="sxs-lookup"><span data-stu-id="b4f82-129">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="b4f82-130">Respeitam as restrições especificadas em cabeçalhos `Access-Control` ao fazer solicitações de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b4f82-130">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="b4f82-131">No entanto, os navegadores enviam o cabeçalho `Origin` ao emitir solicitações de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b4f82-131">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="b4f82-132">Os aplicativos devem ser configurados para validar esses cabeçalhos e garantir que apenas WebSockets provenientes de origens esperadas sejam permitidos.</span><span class="sxs-lookup"><span data-stu-id="b4f82-132">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="b4f82-133">No ASP.NET Core 2,1 e posterior, a validação de cabeçalho pode ser obtida usando um middleware personalizado **colocado `UseSignalR`antes e middleware de autenticação** no `Configure`:</span><span class="sxs-lookup"><span data-stu-id="b4f82-133">In ASP.NET Core 2.1 and later, header validation can be achieved using a custom middleware placed **before `UseSignalR`, and authentication middleware** in `Configure`:</span></span>

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> <span data-ttu-id="b4f82-134">O cabeçalho `Origin` é controlado pelo cliente e, como o cabeçalho `Referer`, pode ser falsificado.</span><span class="sxs-lookup"><span data-stu-id="b4f82-134">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="b4f82-135">Esses cabeçalhos **não** devem ser usados como um mecanismo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="b4f82-135">These headers should **not** be used as an authentication mechanism.</span></span>

::: moniker-end

## <a name="connectionid"></a><span data-ttu-id="b4f82-136">ConnectionId</span><span class="sxs-lookup"><span data-stu-id="b4f82-136">ConnectionId</span></span>

<span data-ttu-id="b4f82-137">A exposição `ConnectionId` pode levar à representação mal-intencionada se a SignalR versão do servidor ou do cliente for ASP.NET Core 2,2 ou anterior.</span><span class="sxs-lookup"><span data-stu-id="b4f82-137">Exposing `ConnectionId` can lead to malicious impersonation if the SignalR server or client version is ASP.NET Core 2.2 or earlier.</span></span> <span data-ttu-id="b4f82-138">Se a SignalR versão do servidor e do cliente for ASP.NET Core 3,0 ou posterior `ConnectionToken` , o em `ConnectionId` vez de o deve ser mantido em segredo.</span><span class="sxs-lookup"><span data-stu-id="b4f82-138">If the SignalR server and client version are ASP.NET Core 3.0 or later, the `ConnectionToken` rather than the `ConnectionId` must be kept secret.</span></span> <span data-ttu-id="b4f82-139">O `ConnectionToken` é intencionalmente não exposto em nenhuma API.</span><span class="sxs-lookup"><span data-stu-id="b4f82-139">The `ConnectionToken` is purposely not exposed in any API.</span></span>  <span data-ttu-id="b4f82-140">Pode ser difícil garantir que os clientes mais SignalR antigos não se conectem ao servidor, portanto, mesmo SignalR que a versão do servidor seja ASP.NET Core 3,0 ou `ConnectionId` posterior, o não deve ser exposto.</span><span class="sxs-lookup"><span data-stu-id="b4f82-140">It can be difficult to ensure that older SignalR clients aren't connecting to the server, so even if your SignalR server version is ASP.NET Core 3.0 or later, the `ConnectionId` shouldn't be exposed.</span></span>

## <a name="access-token-logging"></a><span data-ttu-id="b4f82-141">Log de token de acesso</span><span class="sxs-lookup"><span data-stu-id="b4f82-141">Access token logging</span></span>

<span data-ttu-id="b4f82-142">Ao usar Websockets ou eventos enviados pelo servidor, o cliente de navegador envia o token de acesso na cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="b4f82-142">When using WebSockets or Server-Sent Events, the browser client sends the access token in the query string.</span></span> <span data-ttu-id="b4f82-143">O recebimento do token de acesso por meio da cadeia de caracteres de consulta `Authorization` é geralmente seguro com o uso do cabeçalho padrão.</span><span class="sxs-lookup"><span data-stu-id="b4f82-143">Receiving the access token via query string is generally secure as using the standard `Authorization` header.</span></span> <span data-ttu-id="b4f82-144">Sempre use HTTPS para garantir uma conexão segura de ponta a ponta entre o cliente e o servidor.</span><span class="sxs-lookup"><span data-stu-id="b4f82-144">Always use HTTPS to ensure a secure end-to-end connection between the client and the server.</span></span> <span data-ttu-id="b4f82-145">Muitos servidores Web registram a URL para cada solicitação, incluindo a cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="b4f82-145">Many web servers log the URL for each request, including the query string.</span></span> <span data-ttu-id="b4f82-146">O registro em log das URLs pode registrar o token de acesso.</span><span class="sxs-lookup"><span data-stu-id="b4f82-146">Logging the URLs may log the access token.</span></span> <span data-ttu-id="b4f82-147">O ASP.NET Core registra em log a URL para cada solicitação por padrão, o que incluirá a cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="b4f82-147">ASP.NET Core logs the URL for each request by default, which will include the query string.</span></span> <span data-ttu-id="b4f82-148">Por exemplo: </span><span class="sxs-lookup"><span data-stu-id="b4f82-148">For example:</span></span>

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

<span data-ttu-id="b4f82-149">Se você tiver dúvidas sobre como registrar esses dados nos logs do servidor, poderá desabilitar esse log totalmente Configurando `Microsoft.AspNetCore.Hosting` o agente para `Warning` o nível ou acima (essas mensagens são gravadas no `Info` nível).</span><span class="sxs-lookup"><span data-stu-id="b4f82-149">If you have concerns about logging this data with your server logs, you can disable this logging entirely by configuring the `Microsoft.AspNetCore.Hosting` logger to the `Warning` level or above (these messages are written at `Info` level).</span></span> <span data-ttu-id="b4f82-150">Para obter mais informações, consulte [filtragem de log](xref:fundamentals/logging/index#log-filtering) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="b4f82-150">For more information, see [Log Filtering](xref:fundamentals/logging/index#log-filtering) for more information.</span></span> <span data-ttu-id="b4f82-151">Se você ainda quiser registrar determinadas informações de solicitação, poderá [escrever um middleware](xref:fundamentals/middleware/write) para registrar os dados necessários e filtrar o valor da cadeia de `access_token` caracteres de consulta (se houver).</span><span class="sxs-lookup"><span data-stu-id="b4f82-151">If you still want to log certain request information, you can [write a middleware](xref:fundamentals/middleware/write) to log the data you require and filter out the `access_token` query string value (if present).</span></span>

## <a name="exceptions"></a><span data-ttu-id="b4f82-152">Exceções</span><span class="sxs-lookup"><span data-stu-id="b4f82-152">Exceptions</span></span>

<span data-ttu-id="b4f82-153">As mensagens de exceção geralmente são consideradas dados confidenciais que não devem ser revelados a um cliente.</span><span class="sxs-lookup"><span data-stu-id="b4f82-153">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="b4f82-154">Por padrão, SignalR o não envia os detalhes de uma exceção gerada por um método de Hub para o cliente.</span><span class="sxs-lookup"><span data-stu-id="b4f82-154">By default, SignalR doesn't send the details of an exception thrown by a hub method to the client.</span></span> <span data-ttu-id="b4f82-155">Em vez disso, o cliente recebe uma mensagem genérica indicando que ocorreu um erro.</span><span class="sxs-lookup"><span data-stu-id="b4f82-155">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="b4f82-156">A entrega de mensagem de exceção ao cliente pode ser substituída (por exemplo, em desenvolvimento ou teste) com [EnableDetailedErrors](xref:signalr/configuration#configure-server-options).</span><span class="sxs-lookup"><span data-stu-id="b4f82-156">Exception message delivery to the client can be overridden (for example in development or test) with [EnableDetailedErrors](xref:signalr/configuration#configure-server-options).</span></span> <span data-ttu-id="b4f82-157">As mensagens de exceção não devem ser expostas ao cliente em aplicativos de produção.</span><span class="sxs-lookup"><span data-stu-id="b4f82-157">Exception messages should not be exposed to the client in production apps.</span></span>

## <a name="buffer-management"></a><span data-ttu-id="b4f82-158">Gerenciamento de buffer</span><span class="sxs-lookup"><span data-stu-id="b4f82-158">Buffer management</span></span>

SignalR<span data-ttu-id="b4f82-159">usa buffers por conexão para gerenciar mensagens de entrada e saída.</span><span class="sxs-lookup"><span data-stu-id="b4f82-159"> uses per-connection buffers to manage incoming and outgoing messages.</span></span> <span data-ttu-id="b4f82-160">Por padrão, SignalR o limita esses buffers a 32 KB.</span><span class="sxs-lookup"><span data-stu-id="b4f82-160">By default, SignalR limits these buffers to 32 KB.</span></span> <span data-ttu-id="b4f82-161">A maior mensagem que um cliente ou servidor pode enviar é 32 KB.</span><span class="sxs-lookup"><span data-stu-id="b4f82-161">The largest message a client or server can send is 32 KB.</span></span> <span data-ttu-id="b4f82-162">A memória máxima consumida por uma conexão para mensagens é de 32 KB.</span><span class="sxs-lookup"><span data-stu-id="b4f82-162">The maximum memory consumed by a connection for messages is 32 KB.</span></span> <span data-ttu-id="b4f82-163">Se suas mensagens forem sempre menores que 32 KB, você poderá reduzir o limite, que:</span><span class="sxs-lookup"><span data-stu-id="b4f82-163">If your messages are always smaller than 32 KB, you can reduce the limit, which:</span></span>

* <span data-ttu-id="b4f82-164">Impede que um cliente possa enviar uma mensagem maior.</span><span class="sxs-lookup"><span data-stu-id="b4f82-164">Prevents a client from being able to send a larger message.</span></span>
* <span data-ttu-id="b4f82-165">O servidor nunca precisará alocar buffers grandes para aceitar mensagens.</span><span class="sxs-lookup"><span data-stu-id="b4f82-165">The server will never need to allocate large buffers to accept messages.</span></span>

<span data-ttu-id="b4f82-166">Se suas mensagens forem maiores que 32 KB, você poderá aumentar o limite.</span><span class="sxs-lookup"><span data-stu-id="b4f82-166">If your messages are larger than 32 KB, you can increase the limit.</span></span> <span data-ttu-id="b4f82-167">Aumentar esse limite significa:</span><span class="sxs-lookup"><span data-stu-id="b4f82-167">Increasing this limit means:</span></span>

* <span data-ttu-id="b4f82-168">O cliente pode fazer com que o servidor aloque buffers de memória grandes.</span><span class="sxs-lookup"><span data-stu-id="b4f82-168">The client can cause the server to allocate large memory buffers.</span></span>
* <span data-ttu-id="b4f82-169">A alocação de servidor de buffers grandes pode reduzir o número de conexões simultâneas.</span><span class="sxs-lookup"><span data-stu-id="b4f82-169">Server allocation of large buffers may reduce the number of concurrent connections.</span></span>

<span data-ttu-id="b4f82-170">Há limites para mensagens de entrada e saída, ambas podem ser configuradas no objeto [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) configurado em `MapHub`:</span><span class="sxs-lookup"><span data-stu-id="b4f82-170">There are limits for incoming and outgoing messages, both can be configured on the [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) object configured in `MapHub`:</span></span>

* <span data-ttu-id="b4f82-171">`ApplicationMaxBufferSize`representa o número máximo de bytes do cliente que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="b4f82-171">`ApplicationMaxBufferSize` represents the maximum number of bytes from the client that the server buffers.</span></span> <span data-ttu-id="b4f82-172">Se o cliente tentar enviar uma mensagem maior que esse limite, a conexão poderá ser fechada.</span><span class="sxs-lookup"><span data-stu-id="b4f82-172">If the client attempts to send a message larger than this limit, the connection may be closed.</span></span>
* <span data-ttu-id="b4f82-173">`TransportMaxBufferSize`representa o número máximo de bytes que o servidor pode enviar.</span><span class="sxs-lookup"><span data-stu-id="b4f82-173">`TransportMaxBufferSize` represents the maximum number of bytes the server can send.</span></span> <span data-ttu-id="b4f82-174">Se o servidor tentar enviar uma mensagem (incluindo valores de retorno dos métodos de Hub) maior que esse limite, uma exceção será lançada.</span><span class="sxs-lookup"><span data-stu-id="b4f82-174">If the server attempts to send a message (including return values from hub methods) larger than this limit, an exception will be thrown.</span></span>

<span data-ttu-id="b4f82-175">Definir o limite para `0` desabilitar o limite.</span><span class="sxs-lookup"><span data-stu-id="b4f82-175">Setting the limit to `0` disables the limit.</span></span> <span data-ttu-id="b4f82-176">A remoção do limite permite que um cliente envie uma mensagem de qualquer tamanho.</span><span class="sxs-lookup"><span data-stu-id="b4f82-176">Removing the limit allows a client to send a message of any size.</span></span> <span data-ttu-id="b4f82-177">Clientes mal-intencionados que enviam mensagens grandes podem causar o excesso de memória a ser alocada.</span><span class="sxs-lookup"><span data-stu-id="b4f82-177">Malicious clients sending large messages can cause excess memory to be allocated.</span></span> <span data-ttu-id="b4f82-178">O uso excessivo de memória pode reduzir significativamente o número de conexões simultâneas.</span><span class="sxs-lookup"><span data-stu-id="b4f82-178">Excess memory usage can significantly reduce the number of concurrent connections.</span></span>
