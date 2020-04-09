---
title: Serviços do gRPC com o ASP.NET Core
author: juntaoluo
description: Aprenda os conceitos básicos ao escrever serviços gRPC com ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/03/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 6107704a4b4d9c629a7abe907efd5b1932019130
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667625"
---
# <a name="grpc-services-with-aspnet-core"></a>Serviços do gRPC com o ASP.NET Core

Este documento mostra como começar com os serviços gRPC usando ASP.NET Core.

## <a name="prerequisites"></a>Pré-requisitos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a>Introdução ao serviço de gRPC no ASP.NET Core

[Exibir ou baixar o código de amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) [(como baixar).](xref:index#how-to-download-a-sample)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Consulte [Comece com os serviços gRPC](xref:tutorials/grpc/grpc-start) para obter instruções detalhadas sobre como criar um projeto gRPC.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

Da linha de comando, execute `dotnet new grpc -o GrpcGreeter`.

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a>Adicione serviços gRPC a um aplicativo ASP.NET Core

gRPC requer o pacote [Grpc.AspNetCore.](https://www.nuget.org/packages/Grpc.AspNetCore)

### <a name="configure-grpc"></a>Configurar gRPC

Em *Startup.cs:*

* o gRPC é `AddGrpc` habilitado com o método.
* Cada serviço gRPC é adicionado ao `MapGrpcService` pipeline de roteamento através do método.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

ASP.NET Os middlewares e recursos do Core compartilham o pipeline de roteamento, portanto, um aplicativo pode ser configurado para atender manipuladores de solicitação adicionais. Os manipuladores de solicitação adicionais, como os controladores MVC, trabalham em paralelo com os serviços gRPC configurados.

### <a name="configure-kestrel"></a>Configurar Kestrel

Pontos finais do Kestrel gRPC:

* Requeira HTTP/2.
* Deve ser protegido com [TLS (Transport Layer Security, segurança da camada de transporte)](https://tools.ietf.org/html/rfc5246).

#### <a name="http2"></a>HTTP/2

gRPC requer HTTP/2. gRPC para ASP.NET Core valida `HTTP/2` [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) is .

O Kestrel [suporta HTTP/2](xref:fundamentals/servers/kestrel#http2-support) na maioria dos sistemas operacionais modernos. Os pontos finais do Kestrel são configurados para suportar conexões HTTP/1.1 e HTTP/2 por padrão.

#### <a name="tls"></a>TLS

Os pontos finais kestrel usados para gRPC devem ser fixados com TLS. Em desenvolvimento, um ponto final protegido com TLS é criado automaticamente quando `https://localhost:5001` o certificado de desenvolvimento ASP.NET Core está presente. Nenhuma configuração é necessária. Um `https` prefixo verifica se o ponto final do Kestrel está usando TLS.

Na produção, o TLS deve ser explicitamente configurado. No exemplo de *appsettings.json* a seguir, um ponto final HTTP/2 protegido com TLS é fornecido:

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

Alternativamente, os pontos finais do Kestrel podem ser configurados em *Program.cs*:

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a>Negociação de protocolo

O TLS é usado para mais do que garantir a comunicação. O aperto de mão do TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) é usado para negociar o protocolo de conexão entre o cliente e o servidor quando um ponto final suporta vários protocolos. Esta negociação determina se a conexão usa HTTP/1.1 ou HTTP/2.

Se um ponto final HTTP/2 estiver configurado sem TLS, os [protocolos ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) do ponto final devem ser definidos como `HttpProtocols.Http2`. Um ponto final com vários protocolos (por exemplo) `HttpProtocols.Http1AndHttp2`não pode ser usado sem O TLS porque não há negociação. Todas as conexões ao padrão de ponto final não seguro para HTTP/1.1 e chamadas gRPC falham.

Para obter mais informações sobre como ativar HTTP/2 e TLS com Kestrel, consulte [configuração de ponto final do Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!NOTE]
> O macOS não é compatível com gRPC do ASP.NET Core com TLS. É necessária uma configuração adicional para executar com êxito os serviços gRPC no macOS. Para obter mais informações, confira [Não é possível iniciar o aplicativo ASP.NET Core gRPC no macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

## <a name="integration-with-aspnet-core-apis"></a>Integração com ASP.NET PRINCIPAIS APIs

Os serviços gRPC têm acesso total aos recursos do ASP.NET Core, como [Injeção de Dependência](xref:fundamentals/dependency-injection) (DI) e [Registro](xref:fundamentals/logging/index). Por exemplo, a implementação do serviço pode resolver um serviço de logger a partir do contêiner DI através do construtor:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

Por padrão, a implementação do serviço gRPC pode resolver outros serviços DI com qualquer vida útil (Singleton, Scoped ou Transient).

### <a name="resolve-httpcontext-in-grpc-methods"></a>Resolver httpcontext em métodos gRPC

A API gRPC fornece acesso a alguns dados de mensagem HTTP/2, como o método, host, cabeçalho e reboques. O acesso `ServerCallContext` é através do argumento passado para cada método gRPC:

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

`ServerCallContext`não fornece acesso `HttpContext` total a todas as APIs ASP.NET. O `GetHttpContext` método de extensão `HttpContext` fornece acesso total à mensagem HTTP/2 subjacente em ASP.NET APIs:

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
