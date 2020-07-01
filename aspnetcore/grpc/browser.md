---
title: Usar o gRPC em aplicativos de navegador
author: jamesnk
description: Saiba como configurar os serviços gRPCs em ASP.NET Core para que possam ser chamados de aplicativos de navegador usando o gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 06/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/browser
ms.openlocfilehash: 20f72deb9895111a6e691eb1ee5cd7419c8c4cb4
ms.sourcegitcommit: 895e952aec11c91d703fbdd3640a979307b8cc67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85793506"
---
# <a name="use-grpc-in-browser-apps"></a>Usar o gRPC em aplicativos de navegador

Por [James Newton – King](https://twitter.com/jamesnk)

 Saiba como configurar um serviço gRPC existente do ASP.NET Core para ser chamado de aplicativos de navegador, usando o protocolo [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) . gRPC-Web permite que aplicativos e JavaScript do navegador Blazor chamem serviços gRPCs. Não é possível chamar um serviço gRPC HTTP/2 de um aplicativo baseado em navegador. os serviços gRPCs hospedados no ASP.NET Core podem ser configurados para dar suporte a gRPC-Web juntamente com HTTP/2 gRPC.


Para obter instruções sobre como adicionar um serviço gRPC a um aplicativo ASP.NET Core existente, consulte [Adicionar serviços gRPC a um aplicativo ASP.NET Core](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).

Para obter instruções sobre como criar um projeto gRPC, consulte <xref:tutorials/grpc/grpc-start> .

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a>gRPC-Web em ASP.NET Core vs. Envoy

Há duas opções de como adicionar gRPC-Web a um aplicativo ASP.NET Core:

* Suporte a gRPC-Web juntamente com gRPC HTTP/2 em ASP.NET Core. Essa opção usa o middleware fornecido pelo `Grpc.AspNetCore.Web` pacote.
* Use o suporte gRPC da Web [do Envoy proxy](https://www.envoyproxy.io/) para converter GRPC-Web em gRPC http/2. Em seguida, a chamada traduzida é encaminhada para o aplicativo ASP.NET Core.

Há prós e contras em cada abordagem. Se o ambiente de um aplicativo já estiver usando o Envoy como um proxy, talvez faça sentido também usar o Envoy para fornecer suporte de gRPC para a Web. Para uma solução básica para o gRPC-Web que requer apenas ASP.NET Core, `Grpc.AspNetCore.Web` é uma boa opção.

## <a name="configure-grpc-web-in-aspnet-core"></a>Configurar gRPC-Web no ASP.NET Core

os serviços gRPCs hospedados no ASP.NET Core podem ser configurados para dar suporte a gRPC-Web juntamente com HTTP/2 gRPC. gRPC-Web não requer nenhuma alteração nos serviços. A única modificação é a configuração de inicialização.

Para habilitar o gRPC-Web com um serviço de gRPC de ASP.NET Core:

* Adicione uma referência ao pacote [Grpc. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .
* Configure o aplicativo para usar o gRPC-Web adicionando `UseGrpcWeb` e `EnableGrpcWeb` ao *Startup.cs*:

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

O código anterior:

* Adiciona o gRPC-middleware da Web, `UseGrpcWeb` , após o roteamento e antes dos pontos de extremidade.
* Especifica o `endpoints.MapGrpcService<GreeterService>()` método que oferece suporte a gRPC-Web com `EnableGrpcWeb` . 

Como alternativa, o middleware gRPC-Web pode ser configurado para que todos os serviços ofereçam suporte ao gRPC-Web por padrão e `EnableGrpcWeb` não sejam necessários. Especifique `new GrpcWebOptions { DefaultEnabled = true }` quando o middleware é adicionado.

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> Há um problema conhecido que faz com que o gRPC falhe quando [hospedado por Http.sys](xref:fundamentals/servers/httpsys) no .NET Core 3. x.
>
> Uma solução alternativa para obter gRPC de trabalho no Http.sys está disponível [aqui](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).

### <a name="grpc-web-and-cors"></a>gRPC-Web e CORS

A segurança do navegador impede que uma página da Web faça solicitações para um domínio diferente daquele que servia a página da Web. Essa restrição se aplica a fazer chamadas gRPC com aplicativos de navegador. Por exemplo, um aplicativo de navegador servido pelo `https://www.contoso.com` é impedido de chamar gRPC-Web Services hospedados no `https://services.contoso.com` . O CORS (compartilhamento de recursos entre origens) pode ser usado para relaxar essa restrição.

Para permitir que um aplicativo de navegador faça chamadas gRPC-Web entre origens, configure o [CORS no ASP.NET Core](xref:security/cors). Use o suporte interno a CORS e exponha cabeçalhos específicos do gRPC com <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A> .

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

O código anterior:

* Chamadas `AddCors` para adicionar serviços CORS e configura uma política CORS que expõe cabeçalhos específicos do gRPC.
* Chamadas `UseCors` para adicionar o MIDDLEWARE CORS após o roteamento e antes dos pontos de extremidade.
* Especifica o `endpoints.MapGrpcService<GreeterService>()` método que oferece suporte a CORS com `RequiresCors` .

## <a name="call-grpc-web-from-the-browser"></a>Chamar gRPC-Web do navegador

Os aplicativos de navegador podem usar gRPC-Web para chamar serviços gRPCs. Há alguns requisitos e limitações ao chamar serviços gRPCs com o gRPC-Web a partir do navegador:

* O servidor deve ter sido configurado para dar suporte a gRPC-Web.
* Não há suporte para streaming de cliente e chamadas de streaming bidirecionais. Há suporte para o streaming do servidor.
* A chamada de serviços gRPCs em um domínio diferente requer que o [CORS](xref:security/cors) seja configurado no servidor.

### <a name="javascript-grpc-web-client"></a>JavaScript gRPC-cliente Web

Há um cliente JavaScript gRPC-Web. Para obter instruções sobre como usar o gRPC-Web do JavaScript, consulte [gravar código de cliente JavaScript com gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>Configurar o gRPC-Web com o cliente .NET gRPC

O cliente .NET gRPC pode ser configurado para fazer chamadas gRPC-Web. Isso é útil para [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) aplicativos, que são hospedados no navegador e têm as mesmas limitações de http do código JavaScript. Chamar gRPC-Web com um cliente .NET é [o mesmo que http/2 gRPC](xref:grpc/client). A única modificação é como o canal é criado.

Para usar o gRPC-Web:

* Adicione uma referência ao pacote [Grpc .net. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .
* Verifique se a referência ao pacote [Grpc .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) é 2.29.0 ou superior.
* Configure o canal para usar `GrpcWebHandler` :

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

O código anterior:

* Configura um canal para usar gRPC-Web.
* Cria um cliente e faz uma chamada usando o canal.

`GrpcWebHandler`tem as seguintes opções de configuração:

* **InnerHandler**: o subjacente <xref:System.Net.Http.HttpMessageHandler> que faz a solicitação HTTP gRPC, por exemplo, `HttpClientHandler` .
* **GrpcWebMode**: um tipo de enumeração que especifica se a solicitação HTTP gRPC `Content-Type` é `application/grpc-web` ou `application/grpc-web-text` .
    * `GrpcWebMode.GrpcWeb`configura o conteúdo a ser enviado sem codificação. Valor padrão.
    * `GrpcWebMode.GrpcWebText`configura o conteúdo para ser codificado em base64. Necessário para chamadas de streaming de servidor em navegadores.
* **HttpVersion**: protocolo http `Version` usado para definir [HttpRequestMessage. Version](xref:System.Net.Http.HttpRequestMessage.Version) na solicitação HTTP gRPC subjacente. gRPC-Web não requer uma versão específica e não substitui o padrão, a menos que especificado.

> [!IMPORTANT]
> Clientes gRPC gerados têm métodos Sync e Async para chamar métodos unários. Por exemplo, `SayHello` é sincronização e `SayHelloAsync` é Async. Chamar um método de sincronização em um Blazor WebAssembly aplicativo fará com que o aplicativo fique sem resposta. Os métodos assíncronos sempre devem ser usados no Blazor WebAssembly .

## <a name="additional-resources"></a>Recursos adicionais

* [gRPC para o projeto GitHub de clientes Web](https://github.com/grpc/grpc-web)
* <xref:security/cors>
