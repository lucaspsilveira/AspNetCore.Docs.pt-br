---
title: gRPC para configuração .NET
author: jamesnk
description: Saiba como configurar o gRPC para aplicativos .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 02/26/2020
uid: grpc/configuration
ms.openlocfilehash: 4c13c45ce745643c3cb089a1c984d2ef599db48b
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440825"
---
# <a name="grpc-for-net-configuration"></a>gRPC para configuração .NET

## <a name="configure-services-options"></a>Configurar opções de serviços

Os serviços gRPC `AddGrpc` são configurados com *Startup.cs*. A tabela a seguir descreve opções para configurar serviços gRPC:

| Opção | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| MaxSendMessageSize | `null` | O tamanho máximo da mensagem em bytes que podem ser enviados do servidor. Tentar enviar uma mensagem que exceda o tamanho máximo da mensagem configurado resulta em uma exceção. Quando definido `null`para , o tamanho da mensagem é ilimitado. |
| MaxReceiveMessageSize | 4 MB | O tamanho máximo da mensagem em bytes que podem ser recebidos pelo servidor. Se o servidor receber uma mensagem que exceda esse limite, ele será uma exceção. Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode impactar negativamente o consumo de memória. Quando definido `null`para , o tamanho da mensagem é ilimitado. |
| Habilitarerros detalhados | `false` | Se `true`, mensagens de exceção detalhadas são devolvidas aos clientes quando uma exceção é lançada em um método de serviço. O padrão é `false`. `EnableDetailedErrors` Configuração `true` para pode vazar informações confidenciais. |
| Provedores de compactação | gzip | Uma coleção de provedores de compressão usados para comprimir e descomprimir mensagens. Provedores de compressão personalizados podem ser criados e adicionados à coleção. Os provedores configurados padrão suportam compactação **gzip.** |
| <span style="word-break:normal;word-wrap:normal">Algoritmo de compressão de resposta</span> | `null` | O algoritmo de compactação usado para compactar mensagens enviadas do servidor. O algoritmo deve corresponder a `CompressionProviders`um provedor de compressão em . Para que o algoritmo comprima uma resposta, o cliente deve indicar que ele suporta o algoritmo enviando-o no cabeçalho **de codificação grpc-accept.** |
| Nível de compressão de resposta | `null` | O nível de compactar usado para comprimir mensagens enviadas do servidor. |
| Interceptadores | Nenhum | Uma coleção de interceptadores que são executados a cada chamada gRPC. Interceptadores são executados na ordem em que estão registrados. Interceptadores configurados globalmente são executados antes que os interceptadores sejam configurados para um único serviço. Para obter mais informações sobre interceptadores gRPC, consulte [gRPC Interceptors vs. Middleware](xref:grpc/migration#grpc-interceptors-vs-middleware). |

As opções podem ser configuradas para todos `AddGrpc` os `Startup.ConfigureServices`serviços, fornecendo um delegado de opções para a chamada:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

As opções para um único serviço `AddGrpc` sobrepõem as opções globais fornecidas e podem ser configuradas usando `AddServiceOptions<TService>`:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>Configurar opções de cliente

a configuração do cliente `GrpcChannelOptions`gRPC está configurada . A tabela a seguir descreve opções para configurar canais gRPC:

| Opção | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| HttpClient | Nova instância | O `HttpClient` usado para fazer chamadas gRPC. Um cliente pode ser configurado `HttpClientHandler`para configurar um personalizado ou adicionar manipuladores adicionais ao pipeline HTTP para chamadas gRPC. Se `HttpClient` não for especificado, `HttpClient` uma nova instância será criada para o canal. Ele será automaticamente descartado. |
| DescartehttpClient | `false` | Se `true`, `HttpClient` e um for `HttpClient` especificado, a instância `GrpcChannel` será descartada quando a disposição for descartada. |
| Fábrica de Madeireiros | `null` | O `LoggerFactory` usado pelo cliente para registrar informações sobre chamadas gRPC. Uma `LoggerFactory` instância pode ser resolvida a partir `LoggerFactory.Create`da injeção de dependência ou criada usando . Para exemplos de configuração <xref:grpc/diagnostics#grpc-client-logging>de registro, consulte . |
| MaxSendMessageSize | `null` | O tamanho máximo da mensagem em bytes que podem ser enviados do cliente. Tentar enviar uma mensagem que exceda o tamanho máximo da mensagem configurado resulta em uma exceção. Quando definido `null`para , o tamanho da mensagem é ilimitado. |
| <span style="word-break:normal;word-wrap:normal">MaxReceiveMessageSize</span> | 4 MB | O tamanho máximo da mensagem em bytes que podem ser recebidos pelo cliente. Se o cliente receber uma mensagem que exceda esse limite, ele abre uma exceção. Aumentar esse valor permite que o cliente receba mensagens maiores, mas pode impactar negativamente o consumo de memória. Quando definido `null`para , o tamanho da mensagem é ilimitado. |
| Credenciais | `null` | Uma instância `ChannelCredentials`. As credenciais são usadas para adicionar metadados de autenticação a chamadas gRPC. |
| Provedores de compactação | gzip | Uma coleção de provedores de compressão usados para comprimir e descomprimir mensagens. Provedores de compressão personalizados podem ser criados e adicionados à coleção. Os provedores configurados padrão suportam compactação **gzip.** |

O seguinte código:

* Define o tamanho máximo de envio e recebimento de mensagens no canal.
* Cria um cliente.

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
