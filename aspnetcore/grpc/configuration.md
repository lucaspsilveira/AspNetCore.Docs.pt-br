---
title: configuração do gRPC para .NET
author: jamesnk
description: Saiba como configurar o gRPC para aplicativos .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 05/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/configuration
ms.openlocfilehash: a90735c6cd99de30f168fb1498e705de2d6887ca
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106111"
---
# <a name="grpc-for-net-configuration"></a>configuração do gRPC para .NET

## <a name="configure-services-options"></a>Configurar opções de serviços

os serviços gRPCs são configurados com o `AddGrpc` no *Startup.cs*. A tabela a seguir descreve as opções para configurar os serviços gRPCs:

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| MaxSendMessageSize | `null` | O tamanho máximo da mensagem em bytes que pode ser enviado do servidor. A tentativa de enviar uma mensagem que exceda o tamanho máximo da mensagem configurada resultará em uma exceção. Quando definido como `null` , o tamanho da mensagem é ilimitado. |
| MaxReceiveMessageSize | 4 MB | O tamanho máximo da mensagem em bytes que pode ser recebido pelo servidor. Se o servidor receber uma mensagem que exceda esse limite, ele lançará uma exceção. Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode afetar negativamente o consumo de memória. Quando definido como `null` , o tamanho da mensagem é ilimitado. |
| EnableDetailedErrors | `false` | Se `true` , as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de serviço. O padrão é `false`. `EnableDetailedErrors`A configuração para `true` pode vazar informações confidenciais. |
| CompressionProviders | gzip | Uma coleção de provedores de compactação usados para compactar e descompactar mensagens. Os provedores de compactação personalizados podem ser criados e adicionados à coleção. Os provedores configurados padrão dão suporte à compactação **gzip** . |
| <span style="word-break:normal;word-wrap:normal">ResponseCompressionAlgorithm</span> | `null` | O algoritmo de compactação usado para compactar mensagens enviadas do servidor. O algoritmo deve corresponder a um provedor de compactação no `CompressionProviders` . Para que o algoritmo compacte uma resposta, o cliente deve indicar que ele dá suporte ao algoritmo enviando-o no cabeçalho **grpc-Accept-Encoding** . |
| ResponseCompressionLevel | `null` | O nível de compactação usado para compactar mensagens enviadas do servidor. |
| Interceptadores | Nenhum | Uma coleção de interceptores que são executados com cada chamada gRPC. Os interceptores são executados na ordem em que estão registrados. Os interceptores configurados globalmente são executados antes de interceptadores configurados para um único serviço. Para obter mais informações sobre os interceptores gRPC, consulte [interceptores do gRPC vs. middleware](xref:grpc/migration#grpc-interceptors-vs-middleware). |
| IgnoreUnknownServices | `false` | Se `true` , as chamadas para serviços e métodos desconhecidos não retornarão um status não **implementado** e a solicitação passará para o próximo middleware registrado no ASP.NET Core. |

As opções podem ser configuradas para todos os serviços, fornecendo um delegado de opções para a `AddGrpc` chamada em `Startup.ConfigureServices` :

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

As opções para um único serviço substituem as opções globais fornecidas no `AddGrpc` e podem ser configuradas usando `AddServiceOptions<TService>` :

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>Configurar opções do cliente

a configuração do cliente gRPC está definida em `GrpcChannelOptions` . A tabela a seguir descreve as opções para configurar os canais gRPC:

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| HttpHandler | Nova instância | O `HttpMessageHandler` usado para fazer chamadas gRPC. Um cliente pode ser definido para configurar um personalizado `HttpClientHandler` ou adicionar manipuladores adicionais ao pipeline http para chamadas gRPC. Se não `HttpMessageHandler` for especificado, uma nova `HttpClientHandler` instância será criada para o canal com descarte automático. |
| HttpClient | `null` | O `HttpClient` usado para fazer chamadas gRPC. Essa configuração é uma alternativa para `HttpHandler` . |
| DisposeHttpClient | `false` | Se for definido como `true` e um `HttpMessageHandler` ou `HttpClient` for especificado, o `HttpHandler` ou `HttpClient` , respectivamente, será Descartado quando o `GrpcChannel` for descartado. |
| LoggerFactory | `null` | O `LoggerFactory` usado pelo cliente para registrar informações sobre chamadas gRPC. Uma `LoggerFactory` instância pode ser resolvida da injeção de dependência ou criada usando `LoggerFactory.Create` . Para obter exemplos de configuração de registro em log, consulte <xref:grpc/diagnostics#grpc-client-logging> . |
| MaxSendMessageSize | `null` | O tamanho máximo da mensagem em bytes que pode ser enviado do cliente. A tentativa de enviar uma mensagem que exceda o tamanho máximo da mensagem configurada resultará em uma exceção. Quando definido como `null` , o tamanho da mensagem é ilimitado. |
| <span style="word-break:normal;word-wrap:normal">MaxReceiveMessageSize</span> | 4 MB | O tamanho máximo da mensagem em bytes que pode ser recebido pelo cliente. Se o cliente receber uma mensagem que exceda esse limite, ele lançará uma exceção. Aumentar esse valor permite que o cliente receba mensagens maiores, mas pode afetar negativamente o consumo de memória. Quando definido como `null` , o tamanho da mensagem é ilimitado. |
| Credenciais | `null` | Uma instância `ChannelCredentials`. As credenciais são usadas para adicionar metadados de autenticação a chamadas gRPC. |
| CompressionProviders | gzip | Uma coleção de provedores de compactação usados para compactar e descompactar mensagens. Os provedores de compactação personalizados podem ser criados e adicionados à coleção. Os provedores configurados padrão dão suporte à compactação **gzip** . |

O seguinte código:

* Define o tamanho máximo de mensagens de envio e recebimento no canal.
* Cria um cliente.

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
