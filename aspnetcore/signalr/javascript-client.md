---
title: ASP.NET Core SignalR cliente JavaScript
author: bradygaster
description: Visão geral de ASP.NET Core SignalR cliente JavaScript.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: cb2b3ddc3eba2d6e1ea91c1e7f6715ffa9ad1b08
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2020
ms.locfileid: "84756009"
---
# <a name="aspnet-core-signalr-javascript-client"></a><span data-ttu-id="d9b43-103">ASP.NET Core SignalR cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="d9b43-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="d9b43-104">Por [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="d9b43-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="d9b43-105">A SignalR biblioteca de cliente ASP.NET Core JavaScript permite que os desenvolvedores chamem o código de Hub do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="d9b43-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="d9b43-106">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d9b43-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-client-package"></a><span data-ttu-id="d9b43-107">Instalar o SignalR pacote do cliente</span><span class="sxs-lookup"><span data-stu-id="d9b43-107">Install the SignalR client package</span></span>

<span data-ttu-id="d9b43-108">A SignalR biblioteca de cliente JavaScript é entregue como um pacote [NPM](https://www.npmjs.com/) .</span><span class="sxs-lookup"><span data-stu-id="d9b43-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="d9b43-109">As seções a seguir descrevem diferentes maneiras de instalar a biblioteca de cliente.</span><span class="sxs-lookup"><span data-stu-id="d9b43-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="d9b43-110">Instalar com NPM</span><span class="sxs-lookup"><span data-stu-id="d9b43-110">Install with npm</span></span>

<span data-ttu-id="d9b43-111">Se estiver usando o Visual Studio, execute os seguintes comandos no **console do Gerenciador de pacotes** enquanto estiver na pasta raiz.</span><span class="sxs-lookup"><span data-stu-id="d9b43-111">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="d9b43-112">Para Visual Studio Code, execute os seguintes comandos do **terminal integrado**.</span><span class="sxs-lookup"><span data-stu-id="d9b43-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="d9b43-113">NPM instala o conteúdo do pacote na *pasta \\ @microsoft\signalr\dist\browser node_modules* .</span><span class="sxs-lookup"><span data-stu-id="d9b43-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="d9b43-114">Crie uma nova pasta chamada *signalr* na pasta *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="d9b43-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="d9b43-115">Copie o arquivo de *signalr.js* para a pasta *wwwroot\lib\signalr* .</span><span class="sxs-lookup"><span data-stu-id="d9b43-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="d9b43-116">NPM instala o conteúdo do pacote na *pasta \\ @aspnet\signalr\dist\browser node_modules* .</span><span class="sxs-lookup"><span data-stu-id="d9b43-116">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="d9b43-117">Crie uma nova pasta chamada *signalr* na pasta *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="d9b43-117">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="d9b43-118">Copie o arquivo de *signalr.js* para a pasta *wwwroot\lib\signalr* .</span><span class="sxs-lookup"><span data-stu-id="d9b43-118">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

<span data-ttu-id="d9b43-119">Referencie o SignalR cliente JavaScript no `<script>` elemento.</span><span class="sxs-lookup"><span data-stu-id="d9b43-119">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="d9b43-120">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="d9b43-120">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="d9b43-121">Usar uma CDN (rede de distribuição de conteúdo)</span><span class="sxs-lookup"><span data-stu-id="d9b43-121">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="d9b43-122">Para usar a biblioteca de cliente sem o pré-requisito NPM, faça referência a uma cópia hospedada em CDN da biblioteca de cliente.</span><span class="sxs-lookup"><span data-stu-id="d9b43-122">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="d9b43-123">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="d9b43-123">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="d9b43-124">A biblioteca de cliente está disponível no seguinte CDNs:</span><span class="sxs-lookup"><span data-stu-id="d9b43-124">The client library is available on the following CDNs:</span></span>

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="d9b43-125">cdnjs</span><span class="sxs-lookup"><span data-stu-id="d9b43-125">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="d9b43-126">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="d9b43-126">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="d9b43-127">unpkg</span><span class="sxs-lookup"><span data-stu-id="d9b43-127">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="d9b43-128">cdnjs</span><span class="sxs-lookup"><span data-stu-id="d9b43-128">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="d9b43-129">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="d9b43-129">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="d9b43-130">unpkg</span><span class="sxs-lookup"><span data-stu-id="d9b43-130">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a><span data-ttu-id="d9b43-131">Instalar com LibMan</span><span class="sxs-lookup"><span data-stu-id="d9b43-131">Install with LibMan</span></span>

<span data-ttu-id="d9b43-132">[LibMan](xref:client-side/libman/index) pode ser usado para instalar arquivos de biblioteca de cliente específicos da biblioteca de cliente hospedada na CDN.</span><span class="sxs-lookup"><span data-stu-id="d9b43-132">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="d9b43-133">Por exemplo, adicione apenas o arquivo JavaScript reduzidos ao projeto.</span><span class="sxs-lookup"><span data-stu-id="d9b43-133">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="d9b43-134">Para obter detalhes sobre essa abordagem, consulte [Adicionar a SignalR biblioteca de cliente](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="d9b43-134">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="d9b43-135">Conectar-se a um hub</span><span class="sxs-lookup"><span data-stu-id="d9b43-135">Connect to a hub</span></span>

<span data-ttu-id="d9b43-136">O código a seguir cria e inicia uma conexão.</span><span class="sxs-lookup"><span data-stu-id="d9b43-136">The following code creates and starts a connection.</span></span> <span data-ttu-id="d9b43-137">O nome do Hub não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="d9b43-137">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="d9b43-138">Conexões entre origens</span><span class="sxs-lookup"><span data-stu-id="d9b43-138">Cross-origin connections</span></span>

<span data-ttu-id="d9b43-139">Normalmente, os navegadores carregam conexões do mesmo domínio que a página solicitada.</span><span class="sxs-lookup"><span data-stu-id="d9b43-139">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="d9b43-140">No entanto, há ocasiões em que uma conexão com outro domínio é necessária.</span><span class="sxs-lookup"><span data-stu-id="d9b43-140">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="d9b43-141">Para impedir que um site mal-intencionado leia dados confidenciais de outro site, [as conexões entre origens](xref:security/cors) são desabilitadas por padrão.</span><span class="sxs-lookup"><span data-stu-id="d9b43-141">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="d9b43-142">Para permitir uma solicitação entre origens, habilite-a na `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="d9b43-142">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="d9b43-143">Métodos do hub de chamadas do cliente</span><span class="sxs-lookup"><span data-stu-id="d9b43-143">Call hub methods from client</span></span>

<span data-ttu-id="d9b43-144">Os clientes JavaScript chamam métodos públicos em hubs por meio do método [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) de [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="d9b43-144">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="d9b43-145">O `invoke` método aceita dois argumentos:</span><span class="sxs-lookup"><span data-stu-id="d9b43-145">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="d9b43-146">O nome do método de Hub.</span><span class="sxs-lookup"><span data-stu-id="d9b43-146">The name of the hub method.</span></span> <span data-ttu-id="d9b43-147">No exemplo a seguir, o nome do método no Hub é `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="d9b43-147">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="d9b43-148">Quaisquer argumentos definidos no método Hub.</span><span class="sxs-lookup"><span data-stu-id="d9b43-148">Any arguments defined in the hub method.</span></span> <span data-ttu-id="d9b43-149">No exemplo a seguir, o nome do argumento é `message` .</span><span class="sxs-lookup"><span data-stu-id="d9b43-149">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="d9b43-150">O código de exemplo usa a sintaxe de função de seta que tem suporte nas versões atuais de todos os principais navegadores, exceto o Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="d9b43-150">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="d9b43-151">Se você estiver usando SignalR o serviço do Azure no *modo sem servidor*, não será possível chamar os métodos de Hub de um cliente.</span><span class="sxs-lookup"><span data-stu-id="d9b43-151">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="d9b43-152">Para obter mais informações, consulte a [ SignalR documentação do serviço](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="d9b43-152">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="d9b43-153">O `invoke` método retorna uma [promessa](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d9b43-153">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="d9b43-154">O `Promise` é resolvido com o valor de retorno (se houver) quando o método no servidor retorna.</span><span class="sxs-lookup"><span data-stu-id="d9b43-154">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="d9b43-155">Se o método no servidor gerar um erro, o `Promise` será rejeitado com a mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="d9b43-155">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="d9b43-156">Use os `then` `catch` métodos e na `Promise` própria para lidar com esses casos (ou `await` sintaxe).</span><span class="sxs-lookup"><span data-stu-id="d9b43-156">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="d9b43-157">O `send` método retorna um JavaScript `Promise` .</span><span class="sxs-lookup"><span data-stu-id="d9b43-157">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="d9b43-158">O `Promise` é resolvido quando a mensagem é enviada ao servidor.</span><span class="sxs-lookup"><span data-stu-id="d9b43-158">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="d9b43-159">Se houver um erro ao enviar a mensagem, o `Promise` será rejeitado com a mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="d9b43-159">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="d9b43-160">Use os `then` `catch` métodos e na `Promise` própria para lidar com esses casos (ou `await` sintaxe).</span><span class="sxs-lookup"><span data-stu-id="d9b43-160">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="d9b43-161">`send`O uso de não aguarda até que o servidor tenha recebido a mensagem.</span><span class="sxs-lookup"><span data-stu-id="d9b43-161">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="d9b43-162">Consequentemente, não é possível retornar dados ou erros do servidor.</span><span class="sxs-lookup"><span data-stu-id="d9b43-162">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="d9b43-163">Chamar métodos de cliente do Hub</span><span class="sxs-lookup"><span data-stu-id="d9b43-163">Call client methods from hub</span></span>

<span data-ttu-id="d9b43-164">Para receber mensagens do Hub, defina um método usando o método [on](/javascript/api/%40aspnet/signalr/hubconnection#on) do `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="d9b43-164">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="d9b43-165">O nome do método de cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d9b43-165">The name of the JavaScript client method.</span></span> <span data-ttu-id="d9b43-166">No exemplo a seguir, o nome do método é `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="d9b43-166">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="d9b43-167">Argumentos que o Hub passa para o método.</span><span class="sxs-lookup"><span data-stu-id="d9b43-167">Arguments the hub passes to the method.</span></span> <span data-ttu-id="d9b43-168">No exemplo a seguir, o valor do argumento é `message` .</span><span class="sxs-lookup"><span data-stu-id="d9b43-168">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="d9b43-169">O código anterior em `connection.on` é executado quando o código do lado do servidor o chama usando o método [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) .</span><span class="sxs-lookup"><span data-stu-id="d9b43-169">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR<span data-ttu-id="d9b43-170">determina qual método de cliente chamar correspondendo o nome do método e os argumentos definidos em `SendAsync` e `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="d9b43-170"> determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="d9b43-171">Como prática recomendada, chame o método [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) no `HubConnection` After `on` .</span><span class="sxs-lookup"><span data-stu-id="d9b43-171">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="d9b43-172">Isso garante que seus manipuladores sejam registrados antes que todas as mensagens sejam recebidas.</span><span class="sxs-lookup"><span data-stu-id="d9b43-172">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="d9b43-173">Registro em log e tratamento de erros</span><span class="sxs-lookup"><span data-stu-id="d9b43-173">Error handling and logging</span></span>

<span data-ttu-id="d9b43-174">Encadear um `catch` método ao final do `start` método para manipular erros do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="d9b43-174">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="d9b43-175">Use `console.error` para gerar erros de saída para o console do navegador.</span><span class="sxs-lookup"><span data-stu-id="d9b43-175">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="d9b43-176">Configure o rastreamento de log no lado do cliente passando um agente e um tipo de evento para registrar quando a conexão é feita.</span><span class="sxs-lookup"><span data-stu-id="d9b43-176">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="d9b43-177">As mensagens são registradas com o nível de log especificado e superior.</span><span class="sxs-lookup"><span data-stu-id="d9b43-177">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="d9b43-178">Os níveis de log disponíveis são os seguintes:</span><span class="sxs-lookup"><span data-stu-id="d9b43-178">Available log levels are as follows:</span></span>

* <span data-ttu-id="d9b43-179">`signalR.LogLevel.Error`: Mensagens de erro.</span><span class="sxs-lookup"><span data-stu-id="d9b43-179">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="d9b43-180">Registra `Error` somente mensagens.</span><span class="sxs-lookup"><span data-stu-id="d9b43-180">Logs `Error` messages only.</span></span>
* <span data-ttu-id="d9b43-181">`signalR.LogLevel.Warning`: Mensagens de aviso sobre erros potenciais.</span><span class="sxs-lookup"><span data-stu-id="d9b43-181">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="d9b43-182">Logs `Warning` e `Error` mensagens.</span><span class="sxs-lookup"><span data-stu-id="d9b43-182">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="d9b43-183">`signalR.LogLevel.Information`: Mensagens de status sem erros.</span><span class="sxs-lookup"><span data-stu-id="d9b43-183">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="d9b43-184">Logs `Information` , `Warning` e `Error` mensagens.</span><span class="sxs-lookup"><span data-stu-id="d9b43-184">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="d9b43-185">`signalR.LogLevel.Trace`: Rastrear mensagens.</span><span class="sxs-lookup"><span data-stu-id="d9b43-185">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="d9b43-186">Registra tudo, incluindo dados transportados entre o Hub e o cliente.</span><span class="sxs-lookup"><span data-stu-id="d9b43-186">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="d9b43-187">Use o método [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) no [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) para configurar o nível de log.</span><span class="sxs-lookup"><span data-stu-id="d9b43-187">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="d9b43-188">As mensagens são registradas no console do navegador.</span><span class="sxs-lookup"><span data-stu-id="d9b43-188">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="d9b43-189">Reconectar clientes</span><span class="sxs-lookup"><span data-stu-id="d9b43-189">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="d9b43-190">Reconectar automaticamente</span><span class="sxs-lookup"><span data-stu-id="d9b43-190">Automatically reconnect</span></span>

<span data-ttu-id="d9b43-191">O cliente JavaScript para SignalR pode ser configurado para reconectar-se automaticamente usando o `withAutomaticReconnect` método em [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="d9b43-191">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="d9b43-192">Ele não se reconectará automaticamente por padrão.</span><span class="sxs-lookup"><span data-stu-id="d9b43-192">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="d9b43-193">Sem parâmetros, `withAutomaticReconnect()` o configura o cliente para aguardar 0, 2, 10 e 30 segundos, respectivamente, antes de tentar cada tentativa de reconexão, parando após quatro tentativas com falha.</span><span class="sxs-lookup"><span data-stu-id="d9b43-193">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="d9b43-194">Antes de iniciar qualquer tentativa de reconexão, o `HubConnection` fará a transição para o `HubConnectionState.Reconnecting` estado e acionará seus `onreconnecting` retornos de chamada em vez de fazer a transição para o `Disconnected` estado e disparar seus `onclose` retornos de chamada como um `HubConnection` sem reconexão automática configurada.</span><span class="sxs-lookup"><span data-stu-id="d9b43-194">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="d9b43-195">Isso fornece uma oportunidade para avisar os usuários de que a conexão foi perdida e para desabilitar os elementos da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="d9b43-195">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="d9b43-196">Se o cliente se reconectar com êxito dentro de suas primeiras quatro tentativas, o `HubConnection` fará a transição de volta para o `Connected` estado e acionará seus `onreconnected` retornos de chamada.</span><span class="sxs-lookup"><span data-stu-id="d9b43-196">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="d9b43-197">Isso fornece uma oportunidade de informar aos usuários que a conexão foi restabelecida.</span><span class="sxs-lookup"><span data-stu-id="d9b43-197">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="d9b43-198">Como a conexão é totalmente nova no servidor, um novo `connectionId` será fornecido para o retorno de `onreconnected` chamada.</span><span class="sxs-lookup"><span data-stu-id="d9b43-198">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="d9b43-199">O `onreconnected` parâmetro do retorno de chamada `connectionId` será indefinido se o `HubConnection` tiver sido configurado para [ignorar a negociação](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="d9b43-199">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="d9b43-200">`withAutomaticReconnect()`não configurará o `HubConnection` para tentar falhas de início inicial, portanto, as falhas de início precisam ser manipuladas manualmente:</span><span class="sxs-lookup"><span data-stu-id="d9b43-200">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="d9b43-201">Se o cliente não se reconectar com êxito dentro de suas primeiras quatro tentativas, o `HubConnection` fará a transição para o `Disconnected` estado e acionará seus retornos de chamada [fechamento](/javascript/api/%40aspnet/signalr/hubconnection#onclose) .</span><span class="sxs-lookup"><span data-stu-id="d9b43-201">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="d9b43-202">Isso fornece uma oportunidade de informar aos usuários que a conexão foi permanentemente perdida e recomenda atualizar a página:</span><span class="sxs-lookup"><span data-stu-id="d9b43-202">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="d9b43-203">Para configurar um número personalizado de tentativas de reconexão antes de desconectar ou alterar o tempo de reconexão, o `withAutomaticReconnect` aceita uma matriz de números que representa o atraso em milissegundos para aguardar antes de iniciar cada tentativa de reconexão.</span><span class="sxs-lookup"><span data-stu-id="d9b43-203">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="d9b43-204">O exemplo anterior configura o `HubConnection` para iniciar a tentativa de reconectar imediatamente após a perda da conexão.</span><span class="sxs-lookup"><span data-stu-id="d9b43-204">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="d9b43-205">Isso também é verdadeiro para a configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="d9b43-205">This is also true for the default configuration.</span></span>

<span data-ttu-id="d9b43-206">Se a primeira tentativa de reconexão falhar, a segunda tentativa de reconexão também será iniciada imediatamente, em vez de esperar 2 segundos, como seria na configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="d9b43-206">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="d9b43-207">Se a segunda tentativa de reconexão falhar, a terceira tentativa de reconexão será iniciada em 10 segundos, o que é novamente como a configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="d9b43-207">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="d9b43-208">O comportamento personalizado, em seguida, deriva novamente do comportamento padrão ao parar após a terceira tentativa de reconexão, em vez de tentar mais uma tentativa de reconexão em outros 30 segundos, como seria na configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="d9b43-208">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="d9b43-209">Se você quiser ainda mais controle sobre o tempo e o número de tentativas de reconexão automática, o `withAutomaticReconnect` aceitará um objeto que implementa a `IRetryPolicy` interface, que tem um único método chamado `nextRetryDelayInMilliseconds` .</span><span class="sxs-lookup"><span data-stu-id="d9b43-209">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="d9b43-210">`nextRetryDelayInMilliseconds`usa um único argumento com o tipo `RetryContext` .</span><span class="sxs-lookup"><span data-stu-id="d9b43-210">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="d9b43-211">O `RetryContext` tem três propriedades: `previousRetryCount` , `elapsedMilliseconds` e `retryReason` que são a `number` , a `number` e uma `Error` respectivamente.</span><span class="sxs-lookup"><span data-stu-id="d9b43-211">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="d9b43-212">Antes da primeira tentativa de reconexão, `previousRetryCount` e `elapsedMilliseconds` será zero, e o `retryReason` será o erro que causou a perda da conexão.</span><span class="sxs-lookup"><span data-stu-id="d9b43-212">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="d9b43-213">Após cada tentativa de repetição com falha, o `previousRetryCount` será incrementado em um, `elapsedMilliseconds` será atualizado para refletir a quantidade de tempo gasto reconectando até agora em milissegundos, e o `retryReason` será o erro que causou a falha da última tentativa de reconexão.</span><span class="sxs-lookup"><span data-stu-id="d9b43-213">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="d9b43-214">`nextRetryDelayInMilliseconds`deve retornar um número que representa o número de milissegundos a aguardar antes da próxima tentativa de reconexão ou `null` se o `HubConnection` deve parar de reconectar.</span><span class="sxs-lookup"><span data-stu-id="d9b43-214">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
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

<span data-ttu-id="d9b43-215">Como alternativa, você pode escrever um código que reconectará o cliente manualmente, conforme demonstrado na [reconexão manual](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="d9b43-215">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="d9b43-216">Reconectar manualmente</span><span class="sxs-lookup"><span data-stu-id="d9b43-216">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="d9b43-217">Antes de 3,0, o cliente JavaScript para SignalR não se reconectar automaticamente.</span><span class="sxs-lookup"><span data-stu-id="d9b43-217">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="d9b43-218">Você deve escrever um código que reconectará o cliente manualmente.</span><span class="sxs-lookup"><span data-stu-id="d9b43-218">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="d9b43-219">O código a seguir demonstra uma abordagem de reconexão manual típica:</span><span class="sxs-lookup"><span data-stu-id="d9b43-219">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="d9b43-220">Uma função (nesse caso, a `start` função) é criada para iniciar a conexão.</span><span class="sxs-lookup"><span data-stu-id="d9b43-220">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="d9b43-221">Chame a `start` função no manipulador de eventos da conexão `onclose` .</span><span class="sxs-lookup"><span data-stu-id="d9b43-221">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="d9b43-222">Uma implementação do mundo real usaria um retirada exponencial ou tentaria um número especificado de vezes antes de desistir.</span><span class="sxs-lookup"><span data-stu-id="d9b43-222">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d9b43-223">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d9b43-223">Additional resources</span></span>

* [<span data-ttu-id="d9b43-224">Referência da API JavaScript</span><span class="sxs-lookup"><span data-stu-id="d9b43-224">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="d9b43-225">Tutorial do JavaScript</span><span class="sxs-lookup"><span data-stu-id="d9b43-225">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="d9b43-226">Tutorial do webpack e TypeScript</span><span class="sxs-lookup"><span data-stu-id="d9b43-226">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="d9b43-227">Hubs</span><span class="sxs-lookup"><span data-stu-id="d9b43-227">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="d9b43-228">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="d9b43-228">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="d9b43-229">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="d9b43-229">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="d9b43-230">Solicitações entre origens (CORS)</span><span class="sxs-lookup"><span data-stu-id="d9b43-230">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* <span data-ttu-id="d9b43-231">[Documentação sem servidor do serviço do Azure SignalR](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="d9b43-231">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
