---
title: ASP.NET SignalR cliente JavaScript do Núcleo
author: bradygaster
description: Visão geral do SignalR ASP.NET cliente JavaScript do Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: 43b2cacf9f415ec422a00b28246f30c8ad74de29
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440851"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a><span data-ttu-id="9e109-103">ASP.NET SignalR cliente JavaScript do Núcleo</span><span class="sxs-lookup"><span data-stu-id="9e109-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="9e109-104">Por [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="9e109-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="9e109-105">A biblioteca SignalR do cliente JavaScript ASP.NET permite que os desenvolvedores chamem o código do hub do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="9e109-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="9e109-106">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9e109-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-opno-locsignalr-client-package"></a><span data-ttu-id="9e109-107">Instale SignalR o pacote cliente</span><span class="sxs-lookup"><span data-stu-id="9e109-107">Install the SignalR client package</span></span>

<span data-ttu-id="9e109-108">A SignalR biblioteca cliente JavaScript é entregue como um pacote [npm.](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="9e109-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="9e109-109">As seções a seguir descrevem diferentes maneiras de instalar a biblioteca do cliente.</span><span class="sxs-lookup"><span data-stu-id="9e109-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="9e109-110">Instale com npm</span><span class="sxs-lookup"><span data-stu-id="9e109-110">Install with npm</span></span>

<span data-ttu-id="9e109-111">Se estiver usando o Visual Studio, execute os seguintes comandos do **Console do Gerenciador** de Pacotes enquanto estiver na pasta raiz.</span><span class="sxs-lookup"><span data-stu-id="9e109-111">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="9e109-112">Para visual studio code, execute os seguintes comandos a partir do **Terminal Integrado**.</span><span class="sxs-lookup"><span data-stu-id="9e109-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="9e109-113">npm instala o conteúdo do pacote na pasta \*node_modules.\\ \*</span><span class="sxs-lookup"><span data-stu-id="9e109-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="9e109-114">Crie uma nova pasta chamada *signalr* sob a pasta *wwwroot\\lib.*</span><span class="sxs-lookup"><span data-stu-id="9e109-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="9e109-115">Copie o arquivo *signalr.js* para a pasta *wwwroot\lib\signalr.*</span><span class="sxs-lookup"><span data-stu-id="9e109-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="9e109-116">npm instala o conteúdo do pacote na pasta \*node_modules.\\ \*</span><span class="sxs-lookup"><span data-stu-id="9e109-116">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="9e109-117">Crie uma nova pasta chamada *signalr* sob a pasta *wwwroot\\lib.*</span><span class="sxs-lookup"><span data-stu-id="9e109-117">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="9e109-118">Copie o arquivo *signalr.js* para a pasta *wwwroot\lib\signalr.*</span><span class="sxs-lookup"><span data-stu-id="9e109-118">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

<span data-ttu-id="9e109-119">Consulte SignalR o cliente JavaScript no `<script>` elemento.</span><span class="sxs-lookup"><span data-stu-id="9e109-119">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="9e109-120">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="9e109-120">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="9e109-121">Use uma CDN (Content Delivery Network, rede de entrega de conteúdo)</span><span class="sxs-lookup"><span data-stu-id="9e109-121">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="9e109-122">Para usar a biblioteca do cliente sem o pré-requisito npm, consulte uma cópia hospedada em CDN da biblioteca do cliente.</span><span class="sxs-lookup"><span data-stu-id="9e109-122">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="9e109-123">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="9e109-123">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="9e109-124">A biblioteca do cliente está disponível nas seguintes CDNs:</span><span class="sxs-lookup"><span data-stu-id="9e109-124">The client library is available on the following CDNs:</span></span>

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="9e109-125">cdnjs</span><span class="sxs-lookup"><span data-stu-id="9e109-125">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="9e109-126">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="9e109-126">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="9e109-127">unpkg</span><span class="sxs-lookup"><span data-stu-id="9e109-127">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="9e109-128">cdnjs</span><span class="sxs-lookup"><span data-stu-id="9e109-128">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="9e109-129">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="9e109-129">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="9e109-130">unpkg</span><span class="sxs-lookup"><span data-stu-id="9e109-130">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a><span data-ttu-id="9e109-131">Instale com libman</span><span class="sxs-lookup"><span data-stu-id="9e109-131">Install with LibMan</span></span>

<span data-ttu-id="9e109-132">[O LibMan](xref:client-side/libman/index) pode ser usado para instalar arquivos específicos da biblioteca de clientes da biblioteca de clientes hospedada no CDN.</span><span class="sxs-lookup"><span data-stu-id="9e109-132">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="9e109-133">Por exemplo, adicione apenas o arquivo JavaScript minizado ao projeto.</span><span class="sxs-lookup"><span data-stu-id="9e109-133">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="9e109-134">Para obter detalhes sobre essa abordagem, consulte [Adicionar a biblioteca do SignalR cliente](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="9e109-134">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="9e109-135">Conecte-se a um hub</span><span class="sxs-lookup"><span data-stu-id="9e109-135">Connect to a hub</span></span>

<span data-ttu-id="9e109-136">O código a seguir cria e inicia uma conexão.</span><span class="sxs-lookup"><span data-stu-id="9e109-136">The following code creates and starts a connection.</span></span> <span data-ttu-id="9e109-137">O nome do hub é caso insensível.</span><span class="sxs-lookup"><span data-stu-id="9e109-137">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a><span data-ttu-id="9e109-138">Conexões de origem cruzada</span><span class="sxs-lookup"><span data-stu-id="9e109-138">Cross-origin connections</span></span>

<span data-ttu-id="9e109-139">Normalmente, os navegadores carregam conexões do mesmo domínio da página solicitada.</span><span class="sxs-lookup"><span data-stu-id="9e109-139">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="9e109-140">No entanto, há ocasiões em que uma conexão com outro domínio é necessária.</span><span class="sxs-lookup"><span data-stu-id="9e109-140">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="9e109-141">Para evitar que um site mal-intencionado leia dados confidenciais de outro site, [as conexões de origem cruzada](xref:security/cors) são desativadas por padrão.</span><span class="sxs-lookup"><span data-stu-id="9e109-141">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="9e109-142">Para permitir uma solicitação de origem `Startup` cruzada, habilite-a na classe.</span><span class="sxs-lookup"><span data-stu-id="9e109-142">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="9e109-143">Métodos de call hub do cliente</span><span class="sxs-lookup"><span data-stu-id="9e109-143">Call hub methods from client</span></span>

<span data-ttu-id="9e109-144">Os clientes JavaScript chamam métodos públicos em hubs através do método de [invocação](/javascript/api/%40aspnet/signalr/hubconnection#invoke) do [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="9e109-144">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="9e109-145">O `invoke` método aceita dois argumentos:</span><span class="sxs-lookup"><span data-stu-id="9e109-145">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="9e109-146">O nome do método hub.</span><span class="sxs-lookup"><span data-stu-id="9e109-146">The name of the hub method.</span></span> <span data-ttu-id="9e109-147">No exemplo a seguir, o nome `SendMessage`do método no hub é .</span><span class="sxs-lookup"><span data-stu-id="9e109-147">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="9e109-148">Quaisquer argumentos definidos no método do hub.</span><span class="sxs-lookup"><span data-stu-id="9e109-148">Any arguments defined in the hub method.</span></span> <span data-ttu-id="9e109-149">No exemplo a seguir, `message`o nome do argumento é .</span><span class="sxs-lookup"><span data-stu-id="9e109-149">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="9e109-150">O código de exemplo usa sintaxe de função seta que é suportada nas versões atuais de todos os principais navegadores, exceto o Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="9e109-150">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="9e109-151">Se você estiver usando SignalR o Azure Service no *modo Sem Servidor,* não poderá chamar métodos de hub de um cliente.</span><span class="sxs-lookup"><span data-stu-id="9e109-151">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="9e109-152">Para obter mais informações, consulte a [ SignalR documentação](/azure/azure-signalr/signalr-concept-serverless-development-config)do Serviço .</span><span class="sxs-lookup"><span data-stu-id="9e109-152">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="9e109-153">O `invoke` método retorna uma [promessa](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)JavaScript .</span><span class="sxs-lookup"><span data-stu-id="9e109-153">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="9e109-154">O `Promise` é resolvido com o valor de devolução (se houver) quando o método no servidor retorna.</span><span class="sxs-lookup"><span data-stu-id="9e109-154">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="9e109-155">Se o método no servidor lançar `Promise` um erro, o é rejeitado com a mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="9e109-155">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="9e109-156">Use `then` os `catch` métodos `Promise` em si para `await` lidar com esses casos (ou sintaxe).</span><span class="sxs-lookup"><span data-stu-id="9e109-156">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="9e109-157">O `send` método retorna `Promise`um JavaScript .</span><span class="sxs-lookup"><span data-stu-id="9e109-157">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="9e109-158">O `Promise` é resolvido quando a mensagem foi enviada para o servidor.</span><span class="sxs-lookup"><span data-stu-id="9e109-158">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="9e109-159">Se houver um erro no `Promise` envio da mensagem, o é rejeitado com a mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="9e109-159">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="9e109-160">Use `then` os `catch` métodos `Promise` em si para `await` lidar com esses casos (ou sintaxe).</span><span class="sxs-lookup"><span data-stu-id="9e109-160">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="9e109-161">O `send` uso não espera até que o servidor receba a mensagem.</span><span class="sxs-lookup"><span data-stu-id="9e109-161">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="9e109-162">Consequentemente, não é possível retornar dados ou erros do servidor.</span><span class="sxs-lookup"><span data-stu-id="9e109-162">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="9e109-163">Ligue para os métodos do cliente a partir do hub</span><span class="sxs-lookup"><span data-stu-id="9e109-163">Call client methods from hub</span></span>

<span data-ttu-id="9e109-164">Para receber mensagens do hub, defina um `HubConnection`método usando o método [on](/javascript/api/%40aspnet/signalr/hubconnection#on) do .</span><span class="sxs-lookup"><span data-stu-id="9e109-164">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="9e109-165">O nome do método cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9e109-165">The name of the JavaScript client method.</span></span> <span data-ttu-id="9e109-166">No exemplo a seguir, `ReceiveMessage`o nome do método é .</span><span class="sxs-lookup"><span data-stu-id="9e109-166">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="9e109-167">Argumentos o hub passa para o método.</span><span class="sxs-lookup"><span data-stu-id="9e109-167">Arguments the hub passes to the method.</span></span> <span data-ttu-id="9e109-168">No exemplo a seguir, `message`o valor do argumento é .</span><span class="sxs-lookup"><span data-stu-id="9e109-168">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="9e109-169">O código `connection.on` anterior é executado quando o código do lado do servidor o chama usando o método [SendAsync.](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync)</span><span class="sxs-lookup"><span data-stu-id="9e109-169">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR<span data-ttu-id="9e109-170">determina qual método de cliente chamar, combinando o `SendAsync` `connection.on`nome do método e os argumentos definidos em e .</span><span class="sxs-lookup"><span data-stu-id="9e109-170"> determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="9e109-171">Como uma prática recomendada, chame `HubConnection` o `on`método [de início](/javascript/api/%40aspnet/signalr/hubconnection#start) no after .</span><span class="sxs-lookup"><span data-stu-id="9e109-171">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="9e109-172">Isso garante que seus manipuladores estejam registrados antes que todas as mensagens sejam recebidas.</span><span class="sxs-lookup"><span data-stu-id="9e109-172">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="9e109-173">Registro em log e tratamento de erros</span><span class="sxs-lookup"><span data-stu-id="9e109-173">Error handling and logging</span></span>

<span data-ttu-id="9e109-174">Acorrente um `catch` método `start` até o final do método para lidar com erros do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="9e109-174">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="9e109-175">Use `console.error` para output erros no console do navegador.</span><span class="sxs-lookup"><span data-stu-id="9e109-175">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="9e109-176">Configure o rastreamento de log do lado do cliente passando por um logger e tipo de evento para registrar quando a conexão for feita.</span><span class="sxs-lookup"><span data-stu-id="9e109-176">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="9e109-177">As mensagens são registradas com o nível de registro especificado e superior.</span><span class="sxs-lookup"><span data-stu-id="9e109-177">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="9e109-178">Os níveis de registro disponíveis são os seguintes:</span><span class="sxs-lookup"><span data-stu-id="9e109-178">Available log levels are as follows:</span></span>

* <span data-ttu-id="9e109-179">`signalR.LogLevel.Error`&ndash; Mensagens de erro.</span><span class="sxs-lookup"><span data-stu-id="9e109-179">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="9e109-180">Registra `Error` apenas mensagens.</span><span class="sxs-lookup"><span data-stu-id="9e109-180">Logs `Error` messages only.</span></span>
* <span data-ttu-id="9e109-181">`signalR.LogLevel.Warning`&ndash; Mensagens de aviso sobre possíveis erros.</span><span class="sxs-lookup"><span data-stu-id="9e109-181">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="9e109-182">Logs `Warning`e `Error` mensagens.</span><span class="sxs-lookup"><span data-stu-id="9e109-182">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="9e109-183">`signalR.LogLevel.Information`&ndash; Mensagens de status sem erros.</span><span class="sxs-lookup"><span data-stu-id="9e109-183">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="9e109-184">Logs `Information` `Warning`e `Error` mensagens.</span><span class="sxs-lookup"><span data-stu-id="9e109-184">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="9e109-185">`signalR.LogLevel.Trace`&ndash; Rastreie mensagens.</span><span class="sxs-lookup"><span data-stu-id="9e109-185">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="9e109-186">Registra tudo, incluindo dados transportados entre hub e cliente.</span><span class="sxs-lookup"><span data-stu-id="9e109-186">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="9e109-187">Use o método [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) no [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) para configurar o nível de log.</span><span class="sxs-lookup"><span data-stu-id="9e109-187">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="9e109-188">As mensagens são registradas no console do navegador.</span><span class="sxs-lookup"><span data-stu-id="9e109-188">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="9e109-189">Reconecte clientes</span><span class="sxs-lookup"><span data-stu-id="9e109-189">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="9e109-190">Reconecte-se automaticamente</span><span class="sxs-lookup"><span data-stu-id="9e109-190">Automatically reconnect</span></span>

<span data-ttu-id="9e109-191">O cliente JavaScript para SignalR pode ser configurado `withAutomaticReconnect` para reconectar automaticamente usando o método no [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="9e109-191">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="9e109-192">Ele não se reconectará automaticamente por padrão.</span><span class="sxs-lookup"><span data-stu-id="9e109-192">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="9e109-193">Sem quaisquer `withAutomaticReconnect()` parâmetros, configura o cliente para esperar 0, 2, 10 e 30 segundos, respectivamente, antes de tentar cada tentativa de reconexão, parando após quatro tentativas fracassadas.</span><span class="sxs-lookup"><span data-stu-id="9e109-193">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="9e109-194">Antes de iniciar qualquer `HubConnection` tentativa de `HubConnectionState.Reconnecting` reconexão, `onreconnecting` a vontade de fazer `Disconnected` a transição `onclose` para o estado `HubConnection` e disparar seus retornos de chamada em vez de fazer a transição para o estado e acionar seus retornos de chamada como um sem reconexão automática configurada.</span><span class="sxs-lookup"><span data-stu-id="9e109-194">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="9e109-195">Isso oferece uma oportunidade para avisar os usuários de que a conexão foi perdida e desativar elementos de IU.</span><span class="sxs-lookup"><span data-stu-id="9e109-195">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="9e109-196">Se o cliente se reconectar com sucesso `HubConnection` em suas `Connected` quatro primeiras `onreconnected` tentativas, o fará a transição de volta para o estado e disparará seus retornos de chamada.</span><span class="sxs-lookup"><span data-stu-id="9e109-196">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="9e109-197">Isso oferece uma oportunidade de informar aos usuários que a conexão foi restabelecida.</span><span class="sxs-lookup"><span data-stu-id="9e109-197">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="9e109-198">Uma vez que a conexão parece `connectionId` inteiramente nova para `onreconnected` o servidor, uma nova será fornecida ao retorno de chamada.</span><span class="sxs-lookup"><span data-stu-id="9e109-198">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="9e109-199">O `onreconnected` parâmetro do `connectionId` retorno de chamada será `HubConnection` indefinido se o foi configurado para pular a [negociação](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="9e109-199">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="9e109-200">`withAutomaticReconnect()`não configurará as `HubConnection` falhas iniciais de inicializar para tentar novamente, portanto, as falhas de início precisam ser tratadas manualmente:</span><span class="sxs-lookup"><span data-stu-id="9e109-200">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("connected");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

<span data-ttu-id="9e109-201">Se o cliente não se reconectar com sucesso `HubConnection` em suas `Disconnected` quatro primeiras tentativas, a transição para o estado e dispara seus retornos de chamada [em close.](/javascript/api/%40aspnet/signalr/hubconnection#onclose)</span><span class="sxs-lookup"><span data-stu-id="9e109-201">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="9e109-202">Isso oferece uma oportunidade de informar aos usuários que a conexão foi perdida permanentemente e recomendar a atualização da página:</span><span class="sxs-lookup"><span data-stu-id="9e109-202">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="9e109-203">Para configurar um número personalizado de tentativas de reconexão antes `withAutomaticReconnect` de desconectar ou alterar o tempo de reconexão, aceita uma matriz de números representando o atraso em milissegundos para esperar antes de iniciar cada tentativa de reconexão.</span><span class="sxs-lookup"><span data-stu-id="9e109-203">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="9e109-204">O exemplo anterior `HubConnection` configura o início de tentativa de reconexão imediatamente após a conexão ser perdida.</span><span class="sxs-lookup"><span data-stu-id="9e109-204">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="9e109-205">Isso também é verdade para a configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="9e109-205">This is also true for the default configuration.</span></span>

<span data-ttu-id="9e109-206">Se a primeira tentativa de reconexão falhar, a segunda tentativa de reconexão também será iniciada imediatamente em vez de esperar 2 segundos como seria na configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="9e109-206">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="9e109-207">Se a segunda tentativa de reconexão falhar, a terceira tentativa de reconexão começará em 10 segundos, o que é novamente como a configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="9e109-207">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="9e109-208">O comportamento personalizado então diverge novamente do comportamento padrão, parando após a terceira falha de tentativa de reconexão em vez de tentar mais uma tentativa de reconexão em outros 30 segundos, como seria na configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="9e109-208">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="9e109-209">Se você quiser ainda mais controle sobre o tempo `withAutomaticReconnect` e o número `IRetryPolicy` de tentativas automáticas de `nextRetryDelayInMilliseconds`reconexão, aceita um objeto implementando a interface, que tem um único método chamado .</span><span class="sxs-lookup"><span data-stu-id="9e109-209">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="9e109-210">`nextRetryDelayInMilliseconds`tem um único argumento `RetryContext`com o tipo .</span><span class="sxs-lookup"><span data-stu-id="9e109-210">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="9e109-211">As `RetryContext` três `previousRetryCount`propriedades: `elapsedMilliseconds` `retryReason` , e `number`que `number` são `Error` a , a e uma respectivamente.</span><span class="sxs-lookup"><span data-stu-id="9e109-211">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="9e109-212">Antes da primeira tentativa `previousRetryCount` de `elapsedMilliseconds` reconexão, `retryReason` ambos e será zero, e o será o Erro que fez com que a conexão fosse perdida.</span><span class="sxs-lookup"><span data-stu-id="9e109-212">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="9e109-213">Após cada tentativa de `previousRetryCount` repetição falhada, `elapsedMilliseconds` será incrementada por uma, será atualizada para refletir o `retryReason` tempo gasto reconectando-se até agora em milissegundos, e o será o Erro que causou a última tentativa de reconexão a falhar.</span><span class="sxs-lookup"><span data-stu-id="9e109-213">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="9e109-214">`nextRetryDelayInMilliseconds`deve retornar um número representando o número de milissegundos para `null` esperar `HubConnection` antes da próxima tentativa de reconexão ou se a devida parada de reconectar.</span><span class="sxs-lookup"><span data-stu-id="9e109-214">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

<span data-ttu-id="9e109-215">Alternativamente, você pode escrever código que reconectará seu cliente manualmente, conforme demonstrado em [Reconectar manualmente](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="9e109-215">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="9e109-216">Reconecte manualmente</span><span class="sxs-lookup"><span data-stu-id="9e109-216">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="9e109-217">Antes do 3.0, o SignalR cliente JavaScript para não se reconectar automaticamente.</span><span class="sxs-lookup"><span data-stu-id="9e109-217">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="9e109-218">Você deve escrever código que reconectará seu cliente manualmente.</span><span class="sxs-lookup"><span data-stu-id="9e109-218">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="9e109-219">O código a seguir demonstra uma abordagem típica de reconexão manual:</span><span class="sxs-lookup"><span data-stu-id="9e109-219">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="9e109-220">Uma função (neste caso, a `start` função) é criada para iniciar a conexão.</span><span class="sxs-lookup"><span data-stu-id="9e109-220">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="9e109-221">Ligue `start` para a função `onclose` no manipulador de eventos da conexão.</span><span class="sxs-lookup"><span data-stu-id="9e109-221">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="9e109-222">Uma implementação do mundo real usaria um back-off exponencial ou tentaria novamente um número especificado de vezes antes de desistir.</span><span class="sxs-lookup"><span data-stu-id="9e109-222">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9e109-223">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="9e109-223">Additional resources</span></span>

* [<span data-ttu-id="9e109-224">Referência da API de JavaScript</span><span class="sxs-lookup"><span data-stu-id="9e109-224">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="9e109-225">Tutorial javaScript</span><span class="sxs-lookup"><span data-stu-id="9e109-225">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="9e109-226">Tutorial do WebPack e TypeScript</span><span class="sxs-lookup"><span data-stu-id="9e109-226">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="9e109-227">Hubs</span><span class="sxs-lookup"><span data-stu-id="9e109-227">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="9e109-228">.NET cliente</span><span class="sxs-lookup"><span data-stu-id="9e109-228">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="9e109-229">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="9e109-229">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="9e109-230">Solicitações de Origem Cruzada (CORS)</span><span class="sxs-lookup"><span data-stu-id="9e109-230">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* <span data-ttu-id="9e109-231">[Documentação SignalR sem servidor do Azure Service](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="9e109-231">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
