---
title: SignalRConsiderações de design de API
author: anurse
description: Saiba como criar SignalR APIs para compatibilidade entre versões do seu aplicativo.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
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
uid: signalr/api-design
ms.openlocfilehash: 9ad8d30da552d3d3084534b8c7ca57386ad111ac
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407792"
---
# <a name="signalr-api-design-considerations"></a>SignalRConsiderações de design de API

Por [Andrew Stanton-enfermaria](https://twitter.com/anurse)

Este artigo fornece diretrizes para a criação de SignalR APIs baseadas em compilação.

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a>Usar parâmetros de objeto personalizados para garantir a compatibilidade com versões anteriores

A adição de parâmetros a um SignalR método de Hub (no cliente ou no servidor) é uma *alteração significativa*. Isso significa que clientes/servidores mais antigos receberão erros quando tentarem invocar o método sem o número apropriado de parâmetros. No entanto, a adição de propriedades a um parâmetro de objeto personalizado **não** é uma alteração significativa. Isso pode ser usado para criar APIs compatíveis que são resilientes a alterações no cliente ou no servidor.

Por exemplo, considere uma API do lado do servidor como a seguinte:

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

O cliente JavaScript chama esse método usando `invoke` da seguinte maneira:

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

Se posteriormente você adicionar um segundo parâmetro ao método de servidor, os clientes mais antigos não fornecerão esse valor de parâmetro. Por exemplo:

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

Quando o cliente antigo tenta invocar esse método, ele receberá um erro como este:

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

No servidor, você verá uma mensagem de log como esta:

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

O cliente antigo enviou apenas um parâmetro, mas a API do servidor mais recente exigiu dois parâmetros. O uso de objetos personalizados como parâmetros proporciona mais flexibilidade. Vamos reprojetar a API original para usar um objeto personalizado:

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

Agora, o cliente usa um objeto para chamar o método:

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

Em vez de adicionar um parâmetro, adicione uma propriedade ao `TotalLengthRequest` objeto:

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

Quando o cliente antigo envia um único parâmetro, a `Param2` propriedade extra será deixada `null` . Você pode detectar uma mensagem enviada por um cliente mais antigo verificando `Param2` `null` e aplicando um valor padrão. Um novo cliente pode enviar ambos os parâmetros.

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

A mesma técnica funciona para métodos definidos no cliente. Você pode enviar um objeto personalizado do lado do servidor:

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

No lado do cliente, você acessa a `Message` propriedade em vez de usar um parâmetro:

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

Se você decidir posteriormente adicionar o remetente da mensagem à carga, adicione uma propriedade ao objeto:

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

Os clientes mais antigos não estarão esperando o `Sender` valor, portanto, eles vão ignorá-lo. Um novo cliente pode aceitá-lo Atualizando para ler a nova propriedade:

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

Nesse caso, o novo cliente também é tolerante a um servidor antigo que não fornece o `Sender` valor. Como o servidor antigo não fornecerá o `Sender` valor, o cliente verifica se ele existe antes de acessá-lo.
