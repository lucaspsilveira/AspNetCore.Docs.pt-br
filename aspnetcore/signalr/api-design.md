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
# <a name="signalr-api-design-considerations"></a>SignalR<span data-ttu-id="5c906-103">Considerações de design de API</span><span class="sxs-lookup"><span data-stu-id="5c906-103"> API design considerations</span></span>

<span data-ttu-id="5c906-104">Por [Andrew Stanton-enfermaria](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="5c906-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="5c906-105">Este artigo fornece diretrizes para a criação de SignalR APIs baseadas em compilação.</span><span class="sxs-lookup"><span data-stu-id="5c906-105">This article provides guidance for building SignalR-based APIs.</span></span>

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a><span data-ttu-id="5c906-106">Usar parâmetros de objeto personalizados para garantir a compatibilidade com versões anteriores</span><span class="sxs-lookup"><span data-stu-id="5c906-106">Use custom object parameters to ensure backwards-compatibility</span></span>

<span data-ttu-id="5c906-107">A adição de parâmetros a um SignalR método de Hub (no cliente ou no servidor) é uma *alteração significativa*.</span><span class="sxs-lookup"><span data-stu-id="5c906-107">Adding parameters to a SignalR hub method (on either the client or the server) is a *breaking change*.</span></span> <span data-ttu-id="5c906-108">Isso significa que clientes/servidores mais antigos receberão erros quando tentarem invocar o método sem o número apropriado de parâmetros.</span><span class="sxs-lookup"><span data-stu-id="5c906-108">This means older clients/servers will get errors when they try to invoke the method without the appropriate number of parameters.</span></span> <span data-ttu-id="5c906-109">No entanto, a adição de propriedades a um parâmetro de objeto personalizado **não** é uma alteração significativa.</span><span class="sxs-lookup"><span data-stu-id="5c906-109">However, adding properties to a custom object parameter is **not** a breaking change.</span></span> <span data-ttu-id="5c906-110">Isso pode ser usado para criar APIs compatíveis que são resilientes a alterações no cliente ou no servidor.</span><span class="sxs-lookup"><span data-stu-id="5c906-110">This can be used to design compatible APIs that are resilient to changes on the client or the server.</span></span>

<span data-ttu-id="5c906-111">Por exemplo, considere uma API do lado do servidor como a seguinte:</span><span class="sxs-lookup"><span data-stu-id="5c906-111">For example, consider a server-side API like the following:</span></span>

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

<span data-ttu-id="5c906-112">O cliente JavaScript chama esse método usando `invoke` da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="5c906-112">The JavaScript client calls this method using `invoke` as follows:</span></span>

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

<span data-ttu-id="5c906-113">Se posteriormente você adicionar um segundo parâmetro ao método de servidor, os clientes mais antigos não fornecerão esse valor de parâmetro.</span><span class="sxs-lookup"><span data-stu-id="5c906-113">If you later add a second parameter to the server method, older clients won't provide this parameter value.</span></span> <span data-ttu-id="5c906-114">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="5c906-114">For example:</span></span>

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

<span data-ttu-id="5c906-115">Quando o cliente antigo tenta invocar esse método, ele receberá um erro como este:</span><span class="sxs-lookup"><span data-stu-id="5c906-115">When the old client tries to invoke this method, it will get an error like this:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

<span data-ttu-id="5c906-116">No servidor, você verá uma mensagem de log como esta:</span><span class="sxs-lookup"><span data-stu-id="5c906-116">On the server, you'll see a log message like this:</span></span>

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

<span data-ttu-id="5c906-117">O cliente antigo enviou apenas um parâmetro, mas a API do servidor mais recente exigiu dois parâmetros.</span><span class="sxs-lookup"><span data-stu-id="5c906-117">The old client only sent one parameter, but the newer server API required two parameters.</span></span> <span data-ttu-id="5c906-118">O uso de objetos personalizados como parâmetros proporciona mais flexibilidade.</span><span class="sxs-lookup"><span data-stu-id="5c906-118">Using custom objects as parameters gives you more flexibility.</span></span> <span data-ttu-id="5c906-119">Vamos reprojetar a API original para usar um objeto personalizado:</span><span class="sxs-lookup"><span data-stu-id="5c906-119">Let's redesign the original API to use a custom object:</span></span>

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

<span data-ttu-id="5c906-120">Agora, o cliente usa um objeto para chamar o método:</span><span class="sxs-lookup"><span data-stu-id="5c906-120">Now, the client uses an object to call the method:</span></span>

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

<span data-ttu-id="5c906-121">Em vez de adicionar um parâmetro, adicione uma propriedade ao `TotalLengthRequest` objeto:</span><span class="sxs-lookup"><span data-stu-id="5c906-121">Instead of adding a parameter, add a property to the `TotalLengthRequest` object:</span></span>

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

<span data-ttu-id="5c906-122">Quando o cliente antigo envia um único parâmetro, a `Param2` propriedade extra será deixada `null` .</span><span class="sxs-lookup"><span data-stu-id="5c906-122">When the old client sends a single parameter, the extra `Param2` property will be left `null`.</span></span> <span data-ttu-id="5c906-123">Você pode detectar uma mensagem enviada por um cliente mais antigo verificando `Param2` `null` e aplicando um valor padrão.</span><span class="sxs-lookup"><span data-stu-id="5c906-123">You can detect a message sent by an older client by checking the `Param2` for `null` and apply a default value.</span></span> <span data-ttu-id="5c906-124">Um novo cliente pode enviar ambos os parâmetros.</span><span class="sxs-lookup"><span data-stu-id="5c906-124">A new client can send both parameters.</span></span>

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

<span data-ttu-id="5c906-125">A mesma técnica funciona para métodos definidos no cliente.</span><span class="sxs-lookup"><span data-stu-id="5c906-125">The same technique works for methods defined on the client.</span></span> <span data-ttu-id="5c906-126">Você pode enviar um objeto personalizado do lado do servidor:</span><span class="sxs-lookup"><span data-stu-id="5c906-126">You can send a custom object from the server side:</span></span>

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

<span data-ttu-id="5c906-127">No lado do cliente, você acessa a `Message` propriedade em vez de usar um parâmetro:</span><span class="sxs-lookup"><span data-stu-id="5c906-127">On the client side, you access the `Message` property rather than using a parameter:</span></span>

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

<span data-ttu-id="5c906-128">Se você decidir posteriormente adicionar o remetente da mensagem à carga, adicione uma propriedade ao objeto:</span><span class="sxs-lookup"><span data-stu-id="5c906-128">If you later decide to add the sender of the message to the payload, add a property to the object:</span></span>

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

<span data-ttu-id="5c906-129">Os clientes mais antigos não estarão esperando o `Sender` valor, portanto, eles vão ignorá-lo.</span><span class="sxs-lookup"><span data-stu-id="5c906-129">The older clients won't be expecting the `Sender` value, so they'll ignore it.</span></span> <span data-ttu-id="5c906-130">Um novo cliente pode aceitá-lo Atualizando para ler a nova propriedade:</span><span class="sxs-lookup"><span data-stu-id="5c906-130">A new client can accept it by updating to read the new property:</span></span>

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

<span data-ttu-id="5c906-131">Nesse caso, o novo cliente também é tolerante a um servidor antigo que não fornece o `Sender` valor.</span><span class="sxs-lookup"><span data-stu-id="5c906-131">In this case, the new client is also tolerant of an old server that doesn't provide the `Sender` value.</span></span> <span data-ttu-id="5c906-132">Como o servidor antigo não fornecerá o `Sender` valor, o cliente verifica se ele existe antes de acessá-lo.</span><span class="sxs-lookup"><span data-stu-id="5c906-132">Since the old server won't provide the `Sender` value, the client checks to see if it exists before accessing it.</span></span>
