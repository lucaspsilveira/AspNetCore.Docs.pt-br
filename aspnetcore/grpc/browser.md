---
title: Usar o gRPC em aplicativos de navegador
author: jamesnk
description: Saiba como configurar os serviços gRPC no ASP.NET Core para serem callable a partir de aplicativos de navegador usando gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/16/2020
uid: grpc/browser
ms.openlocfilehash: 0bb8157525ccd32991d8925816c1b599c3d21a92
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977139"
---
# <a name="use-grpc-in-browser-apps"></a>Usar o gRPC em aplicativos de navegador

Por [James Newton-King](https://twitter.com/jamesnk)

> [!IMPORTANT]
> **o suporte gRPC-Web em .NET é experimental**
>
> gRPC-Web for .NET é um projeto experimental, não um produto comprometido. Queremos:
>
> * Teste que nossa abordagem para implementar o gRPC-Web funciona.
> * Obtenha feedback sobre se essa abordagem é útil para desenvolvedores .NET em comparação com a maneira tradicional de configurar o gRPC-Web através de um proxy.
>
> Por favor, [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) deixe o feedback para garantir que construamos algo com o qual os desenvolvedores gostem e sejam produtivos.

Não é possível chamar um serviço HTTP/2 gRPC a partir de um aplicativo baseado em navegador. [gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) é um protocolo que permite que os aplicativos JavaScript e Blazor do navegador chamem serviços gRPC. Este artigo explica como usar o gRPC-Web no .NET Core.

## <a name="configure-grpc-web-in-aspnet-core"></a>Configure gRPC-Web no núcleo ASP.NET

Os serviços gRPC hospedados no ASP.NET Core podem ser configurados para suportar o gRPC-Web ao lado do HTTP/2 gRPC. o gRPC-Web não requer alterações nos serviços. A única modificação é a configuração de inicialização.

Para habilitar o gRPC-Web com um ASP.NET serviço gRPC Core:

* Adicione uma referência ao pacote [Grpc.AspNetCore.Web.](https://www.nuget.org/packages/Grpc.AspNetCore.Web)
* Configure o aplicativo para usar o `AddGrpcWeb` gRPC-Web adicionando e `UseGrpcWeb` *Startup.cs:*

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

O código anterior:

* Adiciona o middleware gRPC-Web, `UseGrpcWeb`após o roteamento e antes dos pontos finais.
* Especifica que `endpoints.MapGrpcService<GreeterService>()` o método suporta gRPC-Web com `EnableGrpcWeb`. 

Alternativamente, configure todos os serviços para `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` suportar o gRPC-Web adicionando ao ConfigureServices.

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

> [!NOTE]
> Há um problema conhecido que faz com que o gRPC-Web falhe quando [hospedado por Http.sys](xref:fundamentals/servers/httpsys) no .NET Core 3.x.
>
> Uma solução para obter gRPC-Web trabalhando em Http.sys está disponível [aqui](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).

### <a name="grpc-web-and-cors"></a>gRPC-Web e CORS

A segurança do navegador impede que uma página da Web seja feita para um domínio diferente daquele que serviu a página da Web. Essa restrição se aplica à realização de chamadas gRPC-Web com aplicativos de navegador. Por exemplo, um aplicativo `https://www.contoso.com` de navegador atendido por é impedido de `https://services.contoso.com`chamar os serviços gRPC-Web hospedados em . O Cross Origin Resource Sharing (CORS) pode ser usado para relaxar essa restrição.

Para permitir que o aplicativo do seu navegador faça chamadas gRPC-Web de origem cruzada, configure [o CORS no ASP.NET Core](xref:security/cors). Use o suporte cors incorporado e exponha cabeçalhos específicos do gRPC com <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

O código anterior:

* Chama `AddCors` para adicionar serviços CORS e configura uma política CORS que expõe cabeçalhos específicos do gRPC.
* Chama `UseCors` para adicionar o middleware CORS após o roteamento e antes dos pontos finais.
* Especifica que `endpoints.MapGrpcService<GreeterService>()` o método suporta `RequiresCors`CORS com .

## <a name="call-grpc-web-from-the-browser"></a>Ligue para o gRPC-Web do navegador

Os aplicativos do navegador podem usar o gRPC-Web para chamar os serviços gRPC. Existem alguns requisitos e limitações ao ligar para os serviços gRPC com gRPC-Web do navegador:

* O servidor deve ter sido configurado para suportar gRPC-Web.
* Streaming de clientes e chamadas de streaming bidirecionais não são suportados. O streaming do servidor é suportado.
* Chamar serviços gRPC em um domínio diferente requer que [o CORS](xref:security/cors) seja configurado no servidor.

### <a name="javascript-grpc-web-client"></a>JavaScript gRPC-Web cliente

Há um cliente JavaScript gRPC-Web. Para obter instruções sobre como usar o gRPC-Web do JavaScript, consulte [escrever código cliente JavaScript com gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>Configure gRPC-Web com o cliente .NET gRPC

O cliente .NET gRPC pode ser configurado para fazer chamadas gRPC-Web. Isso é útil para os aplicativos [Blazor WebAssembly,](xref:blazor/index#blazor-webassembly) que estão hospedados no navegador e têm as mesmas limitações HTTP do código JavaScript. Chamar gRPC-Web com um cliente .NET é [o mesmo que HTTP/2 gRPC](xref:grpc/client). A única modificação é a forma como o canal é criado.

Para usar o gRPC-Web:

* Adicione uma referência ao pacote [Grpc.Net.Client.Web.](https://www.nuget.org/packages/Grpc.Net.Client.Web)
* Certifique-se de que a referência ao pacote [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) é 2.27.0 ou superior.
* Configure o canal `GrpcWebHandler`para usar o:

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

O código anterior:

* Configura um canal para usar gRPC-Web.
* Cria um cliente e faz uma chamada usando o canal.

O `GrpcWebHandler` tem as seguintes opções de configuração quando criado:

* **InnerHandler**: O <xref:System.Net.Http.HttpMessageHandler> subjacente que faz com que a `HttpClientHandler`solicitação gRPC HTTP, por exemplo, .
* **Modo**: Um tipo de enumeração que especifica `Content-Type` se `application/grpc-web` `application/grpc-web-text`a solicitação de solicitação gRPC HTTP é ou .
    * `GrpcWebMode.GrpcWeb`configura o conteúdo a ser enviado sem codificação. Valor padrão.
    * `GrpcWebMode.GrpcWebText`configura o conteúdo a ser codificado base64. Necessário para chamadas de streaming de servidor em navegadores.
* **HttpVersion**: `Version` Protocolo HTTP usado para definir [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) na solicitação gRPC HTTP subjacente. o gRPC-Web não requer uma versão específica e não anula o padrão, a menos que especificado.

> [!IMPORTANT]
> Os clientes gRPC gerados têm métodos de sincronização e sincronia para chamar métodos não-ários. Por exemplo, `SayHello` é `SayHelloAsync` sincronização e é assincronia. Chamar um método de sincronização em um aplicativo Blazor WebAssembly fará com que o aplicativo fique sem resposta. Os métodos de assincronismo devem ser sempre utilizados no Blazor WebAssembly.

## <a name="additional-resources"></a>Recursos adicionais

* [projeto gRPC para Clientes Web GitHub](https://github.com/grpc/grpc-web)
* <xref:security/cors>
