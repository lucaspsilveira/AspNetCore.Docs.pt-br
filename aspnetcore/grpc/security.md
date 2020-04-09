---
title: Considerações de segurança no gRPC para ASP.NET Core
author: jamesnk
description: Saiba mais sobre as considerações de segurança para o gRPC para ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
uid: grpc/security
ms.openlocfilehash: f84bec0ef485b701b2be36384a2e49b9b28e473d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667317"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a>Considerações de segurança no gRPC para ASP.NET Core

Por [James Newton-King](https://twitter.com/jamesnk)

Este artigo fornece informações sobre como proteger o gRPC com o .NET Core.

## <a name="transport-security"></a>Segurança de transporte

as mensagens gRPC são enviadas e recebidas usando HTTP/2. Recomendamos:

* [O TLS (Transport Layer Security, segurança da camada de transporte)](https://tools.ietf.org/html/rfc5246) é usado para proteger mensagens em aplicativos gRPC de produção.
* Os serviços gRPC só devem ouvir e responder por portas protegidas.

O TLS está configurado em Kestrel. Para obter mais informações sobre a configuração de pontos finais do Kestrel, consulte [configuração de ponto final do Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).

## <a name="exceptions"></a>Exceções

As mensagens de exceção são geralmente consideradas dados confidenciais que não devem ser revelados a um cliente. Por padrão, o gRPC não envia os detalhes de uma exceção lançada por um serviço gRPC para o cliente. Em vez disso, o cliente recebe uma mensagem genérica indicando que ocorreu um erro. A entrega de mensagens de exceção ao cliente pode ser substituída (por exemplo, em desenvolvimento ou teste) com [EnableDetailedErrors](xref:grpc/configuration#configure-services-options). As mensagens de exceção não devem ser expostas ao cliente em aplicativos de produção.

## <a name="message-size-limits"></a>Limites de tamanho da mensagem

Mensagens recebidas para clientes e serviços gRPC são carregadas na memória. Os limites de tamanho da mensagem são um mecanismo para ajudar a evitar que o gRPC consuma recursos excessivos.

O gRPC usa limites de tamanho por mensagem para gerenciar mensagens recebidas e saídas. Por padrão, o gRPC limita as mensagens recebidas a 4 MB. Não há limite para mensagens de saída.

No servidor, os limites de mensagem gRPC podem `AddGrpc`ser configurados para todos os serviços em um aplicativo com :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 1 * 1024 * 1024; // 1 MB
        options.MaxSendMessageSize = 1 * 1024 * 1024; // 1 MB
    });
}
```

Os limites também podem ser configurados para um serviço individual usando `AddServiceOptions<TService>`. Para obter mais informações sobre a configuração dos limites de tamanho da mensagem, consulte [a configuração gRPC](xref:grpc/configuration).

## <a name="client-certificate-validation"></a>Validação do certificado do cliente

[Os certificados do cliente](https://tools.ietf.org/html/rfc5246#section-7.4.4) são inicialmente validados quando a conexão é estabelecida. Por padrão, a Kestrel não realiza validação adicional do certificado de cliente de uma conexão.

Recomendamos que os serviços gRPC protegidos por certificados de cliente saem do pacote [Microsoft.AspNetCore.Authentication.Certificate.](xref:security/authentication/certauth) ASP.NET autenticação da certificação Core realizará validação adicional em um certificado de cliente, incluindo:

* O certificado tem um uso de chave estendida válido (EKU)
* Está dentro de seu prazo de validade
* Verifique a revogação do certificado
