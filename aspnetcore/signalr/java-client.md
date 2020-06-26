---
title: ASP.NET Core SignalR cliente Java
author: mikaelm12
description: Saiba como usar o SignalR cliente Java ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/java-client
ms.openlocfilehash: 27ab8cc1b6e419b59aadb97a8a1fbdddc3579276
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408793"
---
# <a name="aspnet-core-signalr-java-client"></a><span data-ttu-id="a3c35-103">ASP.NET Core SignalR cliente Java</span><span class="sxs-lookup"><span data-stu-id="a3c35-103">ASP.NET Core SignalR Java client</span></span>

<span data-ttu-id="a3c35-104">Por [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="a3c35-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="a3c35-105">O cliente Java permite conectar-se a um SignalR servidor ASP.NET Core do código Java, incluindo aplicativos Android.</span><span class="sxs-lookup"><span data-stu-id="a3c35-105">The Java client enables connecting to an ASP.NET Core SignalR server from Java code, including Android apps.</span></span> <span data-ttu-id="a3c35-106">Assim como o cliente [JavaScript](xref:signalr/javascript-client) e o [cliente .net](xref:signalr/dotnet-client), o cliente Java permite que você receba e envie mensagens para um Hub em tempo real.</span><span class="sxs-lookup"><span data-stu-id="a3c35-106">Like the [JavaScript client](xref:signalr/javascript-client) and the [.NET client](xref:signalr/dotnet-client), the Java client enables you to receive and send messages to a hub in real time.</span></span> <span data-ttu-id="a3c35-107">O cliente Java está disponível no ASP.NET Core 2,2 e posterior.</span><span class="sxs-lookup"><span data-stu-id="a3c35-107">The Java client is available in ASP.NET Core 2.2 and later.</span></span>

<span data-ttu-id="a3c35-108">O aplicativo de console Java de exemplo referenciado neste artigo usa o SignalR cliente Java.</span><span class="sxs-lookup"><span data-stu-id="a3c35-108">The sample Java console app referenced in this article uses the SignalR Java client.</span></span>

<span data-ttu-id="a3c35-109">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a3c35-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-java-client-package"></a><span data-ttu-id="a3c35-110">Instalar o SignalR pacote do cliente Java</span><span class="sxs-lookup"><span data-stu-id="a3c35-110">Install the SignalR Java client package</span></span>

<span data-ttu-id="a3c35-111">O arquivo *signalr-1.0.0* jar permite que os clientes se conectem a SignalR hubs.</span><span class="sxs-lookup"><span data-stu-id="a3c35-111">The *signalr-1.0.0* JAR file allows clients to connect to SignalR hubs.</span></span> <span data-ttu-id="a3c35-112">Para localizar o número de versão mais recente do arquivo JAR, consulte os [resultados da pesquisa do Maven](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span><span class="sxs-lookup"><span data-stu-id="a3c35-112">To find the latest JAR file version number, see the [Maven search results](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span></span>

<span data-ttu-id="a3c35-113">Se estiver usando gradle, adicione a seguinte linha à `dependencies` seção do arquivo *Build. gradle* :</span><span class="sxs-lookup"><span data-stu-id="a3c35-113">If using Gradle, add the following line to the `dependencies` section of your *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

<span data-ttu-id="a3c35-114">Se estiver usando o Maven, adicione as seguintes linhas dentro do `<dependencies>` elemento do seu arquivo de *pom.xml* :</span><span class="sxs-lookup"><span data-stu-id="a3c35-114">If using Maven, add the following lines inside the `<dependencies>` element of your *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a><span data-ttu-id="a3c35-115">Conectar-se a um hub</span><span class="sxs-lookup"><span data-stu-id="a3c35-115">Connect to a hub</span></span>

<span data-ttu-id="a3c35-116">Para estabelecer um `HubConnection` , o `HubConnectionBuilder` deve ser usado.</span><span class="sxs-lookup"><span data-stu-id="a3c35-116">To establish a `HubConnection`, the `HubConnectionBuilder` should be used.</span></span> <span data-ttu-id="a3c35-117">A URL do Hub e o nível de log podem ser configurados durante a criação de uma conexão.</span><span class="sxs-lookup"><span data-stu-id="a3c35-117">The hub URL and log level can be configured while building a connection.</span></span> <span data-ttu-id="a3c35-118">Configure as opções necessárias chamando qualquer um dos `HubConnectionBuilder` métodos antes `build` .</span><span class="sxs-lookup"><span data-stu-id="a3c35-118">Configure any required options by calling any of the `HubConnectionBuilder` methods before `build`.</span></span> <span data-ttu-id="a3c35-119">Inicie a conexão com `start` .</span><span class="sxs-lookup"><span data-stu-id="a3c35-119">Start the connection with `start`.</span></span>

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="a3c35-120">Métodos do hub de chamadas do cliente</span><span class="sxs-lookup"><span data-stu-id="a3c35-120">Call hub methods from client</span></span>

<span data-ttu-id="a3c35-121">Uma chamada para `send` invoca um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="a3c35-121">A call to `send` invokes a hub method.</span></span> <span data-ttu-id="a3c35-122">Passe o nome do método de Hub e quaisquer argumentos definidos no método de Hub para `send` .</span><span class="sxs-lookup"><span data-stu-id="a3c35-122">Pass the hub method name and any arguments defined in the hub method to `send`.</span></span>

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> <span data-ttu-id="a3c35-123">Se você estiver usando SignalR o serviço do Azure no *modo sem servidor*, não será possível chamar os métodos de Hub de um cliente.</span><span class="sxs-lookup"><span data-stu-id="a3c35-123">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="a3c35-124">Para obter mais informações, consulte a [ SignalR documentação do serviço](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="a3c35-124">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="a3c35-125">Chamar métodos de cliente do Hub</span><span class="sxs-lookup"><span data-stu-id="a3c35-125">Call client methods from hub</span></span>

<span data-ttu-id="a3c35-126">Use `hubConnection.on` para definir métodos no cliente que o Hub pode chamar.</span><span class="sxs-lookup"><span data-stu-id="a3c35-126">Use `hubConnection.on` to define methods on the client that the hub can call.</span></span> <span data-ttu-id="a3c35-127">Defina os métodos após a compilação, mas antes de iniciar a conexão.</span><span class="sxs-lookup"><span data-stu-id="a3c35-127">Define the methods after building but before starting the connection.</span></span>

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a><span data-ttu-id="a3c35-128">Adicionar registro em log</span><span class="sxs-lookup"><span data-stu-id="a3c35-128">Add logging</span></span>

<span data-ttu-id="a3c35-129">O SignalR cliente Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="a3c35-129">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a3c35-130">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="a3c35-130">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a3c35-131">O trecho de código a seguir mostra como usar `java.util.logging` o com o SignalR cliente Java.</span><span class="sxs-lookup"><span data-stu-id="a3c35-131">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a3c35-132">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="a3c35-132">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a3c35-133">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="a3c35-133">This can safely be ignored.</span></span>

## <a name="android-development-notes"></a><span data-ttu-id="a3c35-134">Notas de desenvolvimento do Android</span><span class="sxs-lookup"><span data-stu-id="a3c35-134">Android development notes</span></span>

<span data-ttu-id="a3c35-135">Com relação à compatibilidade SDK do Android para os SignalR recursos do cliente, considere os seguintes itens ao especificar sua versão de SDK do Android de destino:</span><span class="sxs-lookup"><span data-stu-id="a3c35-135">With regards to Android SDK compatibility for the SignalR client features, consider the following items when specifying your target Android SDK version:</span></span>

* <span data-ttu-id="a3c35-136">O SignalR cliente Java será executado no nível 16 da API do Android e posterior.</span><span class="sxs-lookup"><span data-stu-id="a3c35-136">The SignalR Java Client will run on Android API Level 16 and later.</span></span>
* <span data-ttu-id="a3c35-137">A conexão por meio do serviço do Azure SignalR exigirá o nível 20 da API do Android e posterior, pois o [ SignalR serviço do Azure](/azure/azure-signalr/signalr-overview) requer o TLS 1,2 e não oferece suporte a conjuntos de codificação baseados em SHA-1.</span><span class="sxs-lookup"><span data-stu-id="a3c35-137">Connecting through the Azure SignalR Service will require Android API Level 20 and later because the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) requires TLS 1.2 and doesn't support SHA-1-based cipher suites.</span></span> <span data-ttu-id="a3c35-138">O Android [adicionou suporte para pacotes de criptografia SHA-256 (e superior)](https://developer.android.com/reference/javax/net/ssl/SSLSocket) no nível de API 20.</span><span class="sxs-lookup"><span data-stu-id="a3c35-138">Android [added support for SHA-256 (and above) cipher suites](https://developer.android.com/reference/javax/net/ssl/SSLSocket) in API Level 20.</span></span>

## <a name="configure-bearer-token-authentication"></a><span data-ttu-id="a3c35-139">Configurar autenticação de token de portador</span><span class="sxs-lookup"><span data-stu-id="a3c35-139">Configure bearer token authentication</span></span>

<span data-ttu-id="a3c35-140">No SignalR cliente Java, você pode configurar um token de portador a ser usado para autenticação, fornecendo uma "fábrica de token de acesso" ao [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="a3c35-140">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an "access token factory" to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a3c35-141">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer um [RxJava](https://github.com/ReactiveX/RxJava) [único \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="a3c35-141">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a3c35-142">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="a3c35-142">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a><span data-ttu-id="a3c35-143">Limitações conhecidas</span><span class="sxs-lookup"><span data-stu-id="a3c35-143">Known limitations</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="a3c35-144">Somente o protocolo JSON tem suporte.</span><span class="sxs-lookup"><span data-stu-id="a3c35-144">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="a3c35-145">O fallback de transporte e o transporte de eventos do servidor enviados não têm suporte.</span><span class="sxs-lookup"><span data-stu-id="a3c35-145">Transport fallback and the Server Sent Events transport aren't supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="a3c35-146">Somente o protocolo JSON tem suporte.</span><span class="sxs-lookup"><span data-stu-id="a3c35-146">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="a3c35-147">Somente o transporte WebSockets tem suporte.</span><span class="sxs-lookup"><span data-stu-id="a3c35-147">Only the WebSockets transport is supported.</span></span>
* <span data-ttu-id="a3c35-148">Não há suporte para streaming ainda.</span><span class="sxs-lookup"><span data-stu-id="a3c35-148">Streaming isn't supported yet.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="a3c35-149">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a3c35-149">Additional resources</span></span>

* [<span data-ttu-id="a3c35-150">Referência da API Java</span><span class="sxs-lookup"><span data-stu-id="a3c35-150">Java API reference</span></span>](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* <span data-ttu-id="a3c35-151">[Documentação sem servidor do serviço do Azure SignalR](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="a3c35-151">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
