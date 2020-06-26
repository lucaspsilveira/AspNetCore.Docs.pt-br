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
# <a name="aspnet-core-signalr-java-client"></a>ASP.NET Core SignalR cliente Java

Por [Mikael Mengistu](https://twitter.com/MikaelM_12)

O cliente Java permite conectar-se a um SignalR servidor ASP.NET Core do código Java, incluindo aplicativos Android. Assim como o cliente [JavaScript](xref:signalr/javascript-client) e o [cliente .net](xref:signalr/dotnet-client), o cliente Java permite que você receba e envie mensagens para um Hub em tempo real. O cliente Java está disponível no ASP.NET Core 2,2 e posterior.

O aplicativo de console Java de exemplo referenciado neste artigo usa o SignalR cliente Java.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="install-the-signalr-java-client-package"></a>Instalar o SignalR pacote do cliente Java

O arquivo *signalr-1.0.0* jar permite que os clientes se conectem a SignalR hubs. Para localizar o número de versão mais recente do arquivo JAR, consulte os [resultados da pesquisa do Maven](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).

Se estiver usando gradle, adicione a seguinte linha à `dependencies` seção do arquivo *Build. gradle* :

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

Se estiver usando o Maven, adicione as seguintes linhas dentro do `<dependencies>` elemento do seu arquivo de *pom.xml* :

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a>Conectar-se a um hub

Para estabelecer um `HubConnection` , o `HubConnectionBuilder` deve ser usado. A URL do Hub e o nível de log podem ser configurados durante a criação de uma conexão. Configure as opções necessárias chamando qualquer um dos `HubConnectionBuilder` métodos antes `build` . Inicie a conexão com `start` .

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a>Métodos do hub de chamadas do cliente

Uma chamada para `send` invoca um método de Hub. Passe o nome do método de Hub e quaisquer argumentos definidos no método de Hub para `send` .

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> Se você estiver usando SignalR o serviço do Azure no *modo sem servidor*, não será possível chamar os métodos de Hub de um cliente. Para obter mais informações, consulte a [ SignalR documentação do serviço](/azure/azure-signalr/signalr-concept-serverless-development-config).

## <a name="call-client-methods-from-hub"></a>Chamar métodos de cliente do Hub

Use `hubConnection.on` para definir métodos no cliente que o Hub pode chamar. Defina os métodos após a compilação, mas antes de iniciar a conexão.

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a>Adicionar registro em log

O SignalR cliente Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log. É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica. O trecho de código a seguir mostra como usar `java.util.logging` o com o SignalR cliente Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Isso pode ser ignorado com segurança.

## <a name="android-development-notes"></a>Notas de desenvolvimento do Android

Com relação à compatibilidade SDK do Android para os SignalR recursos do cliente, considere os seguintes itens ao especificar sua versão de SDK do Android de destino:

* O SignalR cliente Java será executado no nível 16 da API do Android e posterior.
* A conexão por meio do serviço do Azure SignalR exigirá o nível 20 da API do Android e posterior, pois o [ SignalR serviço do Azure](/azure/azure-signalr/signalr-overview) requer o TLS 1,2 e não oferece suporte a conjuntos de codificação baseados em SHA-1. O Android [adicionou suporte para pacotes de criptografia SHA-256 (e superior)](https://developer.android.com/reference/javax/net/ssl/SSLSocket) no nível de API 20.

## <a name="configure-bearer-token-authentication"></a>Configurar autenticação de token de portador

No SignalR cliente Java, você pode configurar um token de portador a ser usado para autenticação, fornecendo uma "fábrica de token de acesso" ao [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer um [RxJava](https://github.com/ReactiveX/RxJava) [único \<String> ](https://reactivex.io/documentation/single.html). Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a>Limitações conhecidas

::: moniker range=">= aspnetcore-3.0"

* Somente o protocolo JSON tem suporte.
* O fallback de transporte e o transporte de eventos do servidor enviados não têm suporte.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Somente o protocolo JSON tem suporte.
* Somente o transporte WebSockets tem suporte.
* Não há suporte para streaming ainda.

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* [Referência da API Java](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [Documentação sem servidor do serviço do Azure SignalR](/azure/azure-signalr/signalr-concept-serverless-development-config)
