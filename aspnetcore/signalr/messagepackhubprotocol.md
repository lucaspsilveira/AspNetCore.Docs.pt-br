---
title: Use o protocolo SignalR do MessagePack Hub para ASP.NET núcleo
author: bradygaster
description: Adicione o protocolo MessagePack SignalRHub ao ASP.NET Core .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
no-loc:
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: bbc34d790387a96bb3b6f75e841b45685eb137ce
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277230"
---
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a><span data-ttu-id="fe4b6-103">Use o protocolo SignalR do MessagePack Hub para ASP.NET núcleo</span><span class="sxs-lookup"><span data-stu-id="fe4b6-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="fe4b6-104">Este artigo assume que o leitor está familiarizado com os tópicos [abordados](xref:tutorials/signalr)em Get Started .</span><span class="sxs-lookup"><span data-stu-id="fe4b6-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="fe4b6-105">O que é O Pacote de Mensagens?</span><span class="sxs-lookup"><span data-stu-id="fe4b6-105">What is MessagePack?</span></span>

<span data-ttu-id="fe4b6-106">[MessagePack](https://msgpack.org/index.html) é um formato de serialização binária rápido e compacto.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="fe4b6-107">É útil quando o desempenho e a largura de banda são uma preocupação porque cria mensagens menores em comparação com [json](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="fe4b6-108">As mensagens binárias são ilegíveis ao olhar para os traços e logs de rede, a menos que os bytes sejam passados através de um analisador MessagePack.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="fe4b6-109">tem suporte integrado para o formato MessagePack e fornece APIs para o cliente e servidor usarem.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-109"> has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="fe4b6-110">Configurar o MessagePack no servidor</span><span class="sxs-lookup"><span data-stu-id="fe4b6-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="fe4b6-111">Para ativar o Protocolo do MessagePack `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` hub no servidor, instale o pacote em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="fe4b6-112">No `Startup.ConfigureServices` método, `AddMessagePackProtocol` adicione `AddSignalR` à chamada para ativar o suporte ao MessagePack no servidor.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="fe4b6-113">O JSON é habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-113">JSON is enabled by default.</span></span> <span data-ttu-id="fe4b6-114">A adição do MessagePack permite o suporte para clientes JSON e MessagePack.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="fe4b6-115">Para personalizar como o MessagePack `AddMessagePackProtocol` formatará seus dados, é necessário um delegado para configurar opções.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="fe4b6-116">Nesse delegado, `SerializerOptions` a propriedade pode ser usada para configurar opções de serialização do MessagePack.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="fe4b6-117">Para obter mais informações sobre como os resolvers funcionam, visite a biblioteca MessagePack no [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="fe4b6-118">Os atributos podem ser usados nos objetos que você deseja serializar para definir como eles devem ser tratados.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> <span data-ttu-id="fe4b6-119">Recomendamos fortemente a revisão do [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e a aplicação dos patches recomendados.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="fe4b6-120">Por exemplo, `.WithSecurity(MessagePackSecurity.UntrustedData)` chamar ao `SerializerOptions`substituir o .</span><span class="sxs-lookup"><span data-stu-id="fe4b6-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="fe4b6-121">Configurar o MessagePack no cliente</span><span class="sxs-lookup"><span data-stu-id="fe4b6-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="fe4b6-122">O JSON é habilitado por padrão para os clientes suportados.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="fe4b6-123">Os clientes só podem suportar um único protocolo.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="fe4b6-124">A adição do suporte ao MessagePack substituirá quaisquer protocolos previamente configurados.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="fe4b6-125">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="fe4b6-125">.NET client</span></span>

<span data-ttu-id="fe4b6-126">Para ativar o MessagePack no cliente `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, instale o pacote e ligue `AddMessagePackProtocol` `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="fe4b6-127">Esta `AddMessagePackProtocol` chamada leva um delegado para configurar opções como o servidor.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="fe4b6-128">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="fe4b6-128">JavaScript client</span></span>

<span data-ttu-id="fe4b6-129">O suporte ao MessagePack para o [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) cliente JavaScript é fornecido pelo pacote npm.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="fe4b6-130">Instale o pacote executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="fe4b6-131">Depois de instalar o pacote npm, o módulo pode ser usado diretamente através de um carregador de módulo JavaScript ou importado para o navegador fazendo referência ao seguinte arquivo:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="fe4b6-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="fe4b6-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="fe4b6-133">Em um navegador, a `msgpack5` biblioteca também deve ser referenciada.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="fe4b6-134">Use `<script>` uma tag para criar uma referência.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="fe4b6-135">A biblioteca pode ser encontrada em *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="fe4b6-136">Ao usar `<script>` o elemento, a ordem é importante.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="fe4b6-137">Se *signalr-protocol-msgpack.js* for referenciado antes *de msgpack5.js,* um erro ocorrerá ao tentar se conectar com o MessagePack.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="fe4b6-138">*signalr.js* também é necessário antes *do signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="fe4b6-139">A `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` adição à `HubConnectionBuilder` vontade configurará o cliente para usar o protocolo MessagePack ao se conectar a um servidor.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="fe4b6-140">No momento, não há opções de configuração para o protocolo MessagePack no cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="fe4b6-141">Peculiaridades do MessagePack</span><span class="sxs-lookup"><span data-stu-id="fe4b6-141">MessagePack quirks</span></span>

<span data-ttu-id="fe4b6-142">Existem alguns problemas a serem informados ao usar o Protocolo do MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="fe4b6-143">MessagePack é sensível a maiúsculas e minúsculas</span><span class="sxs-lookup"><span data-stu-id="fe4b6-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="fe4b6-144">O protocolo MessagePack é sensível a maiúsculas e minúsculas.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="fe4b6-145">Por exemplo, considere a seguinte classe C#:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="fe4b6-146">Ao enviar do cliente JavaScript, `PascalCased` você deve usar nomes de propriedade, já que o invólucro deve corresponder exatamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="fe4b6-147">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="fe4b6-148">O `camelCased` uso de nomes não se liga corretamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="fe4b6-149">Você pode contornar isso `Key` usando o atributo para especificar um nome diferente para a propriedade MessagePack.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="fe4b6-150">Para obter mais informações, consulte [a documentação MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="fe4b6-151">DateTime.Kind não é preservado ao serializar/desserializar</span><span class="sxs-lookup"><span data-stu-id="fe4b6-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="fe4b6-152">O protocolo MessagePack não fornece uma maneira `Kind` de `DateTime`codificar o valor de um .</span><span class="sxs-lookup"><span data-stu-id="fe4b6-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="fe4b6-153">Como resultado, ao desserializar uma data, o MessagePack Hub Protocol `DateTime.Kind` `DateTimeKind.Local` será convertido para o formato UTC se o caso não tocar no tempo e passá-lo como está.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="fe4b6-154">Se você está `DateTime` trabalhando com valores, recomendamos converter-se para UTC antes de enviá-los.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="fe4b6-155">Converta-os de UTC para o horário local quando você recebê-los.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="fe4b6-156">DateTime.MinValue não é suportado pelo MessagePack no JavaScript</span><span class="sxs-lookup"><span data-stu-id="fe4b6-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="fe4b6-157">A biblioteca [msgpack5](https://github.com/mcollina/msgpack5) SignalR usada pelo cliente JavaScript `timestamp96` não suporta o tipo no MessagePack.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="fe4b6-158">Este tipo é usado para codificar valores de data muito grandes (seja muito cedo no passado ou muito longe no futuro).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="fe4b6-159">O valor `DateTime.MinValue` `January 1, 0001`é , que deve ser `timestamp96` codificado em um valor.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="fe4b6-160">Por causa disso, o envio `DateTime.MinValue` para um cliente JavaScript não é suportado.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="fe4b6-161">Quando `DateTime.MinValue` é recebido pelo cliente JavaScript, o seguinte erro é jogado:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="fe4b6-162">Normalmente, `DateTime.MinValue` é usado para codificar `null` um "ausente" ou valor.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="fe4b6-163">Se você precisar codificar esse valor no MessagePack, use um `DateTime` valor anulado (`DateTime?`) ou codifique um valor separado `bool` indicando se a data está presente.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="fe4b6-164">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [aspnet/#2228SignalR](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="fe4b6-165">Suporte ao MessagePack em ambiente de compilação "antecipada"</span><span class="sxs-lookup"><span data-stu-id="fe4b6-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="fe4b6-166">A [biblioteca MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) usada pelo cliente e servidor .NET usa a geração de código para otimizar a serialização.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="fe4b6-167">Como resultado, ele não é suportado por padrão em ambientes que usam compilação "antecipadamente" (como Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="fe4b6-168">É possível usar o MessagePack nesses ambientes "pré-gerando" o código serializador/desserializador.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="fe4b6-169">Para obter mais informações, consulte [a documentação MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="fe4b6-170">Depois de ter pré-gerado os serializadores, você pode `AddMessagePackProtocol`registrá-los usando o delegado de configuração passado para:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        StaticCompositeResolver.Instance.Register(
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        );
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(StaticCompositeResolver.Instance)
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="fe4b6-171">Verificações de tipo são mais rigorosas no MessagePack</span><span class="sxs-lookup"><span data-stu-id="fe4b6-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="fe4b6-172">O Protocolo Do Hub JSON realizará conversões de tipo durante a desserialização.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="fe4b6-173">Por exemplo, se o objeto de entrada tiver`{ foo: 42 }`um valor de propriedade que seja `string`um número ( ) mas a propriedade na classe .NET for do tipo, o valor será convertido.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="fe4b6-174">No entanto, o MessagePack não executa essa conversão e lançará uma exceção que pode ser vista em logs do lado do servidor (e no console):</span><span class="sxs-lookup"><span data-stu-id="fe4b6-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="fe4b6-175">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [aspnet/#2937SignalR](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="fe4b6-176">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="fe4b6-176">Related resources</span></span>

* [<span data-ttu-id="fe4b6-177">Introdução</span><span class="sxs-lookup"><span data-stu-id="fe4b6-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="fe4b6-178">.NET cliente</span><span class="sxs-lookup"><span data-stu-id="fe4b6-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="fe4b6-179">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="fe4b6-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="fe4b6-180">Este artigo assume que o leitor está familiarizado com os tópicos [abordados](xref:tutorials/signalr)em Get Started .</span><span class="sxs-lookup"><span data-stu-id="fe4b6-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="fe4b6-181">O que é O Pacote de Mensagens?</span><span class="sxs-lookup"><span data-stu-id="fe4b6-181">What is MessagePack?</span></span>

<span data-ttu-id="fe4b6-182">[MessagePack](https://msgpack.org/index.html) é um formato de serialização binária rápido e compacto.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="fe4b6-183">É útil quando o desempenho e a largura de banda são uma preocupação porque cria mensagens menores em comparação com [json](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="fe4b6-184">As mensagens binárias são ilegíveis ao olhar para os traços e logs de rede, a menos que os bytes sejam passados através de um analisador MessagePack.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="fe4b6-185">tem suporte integrado para o formato MessagePack e fornece APIs para o cliente e servidor usarem.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-185"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="fe4b6-186">Configurar o MessagePack no servidor</span><span class="sxs-lookup"><span data-stu-id="fe4b6-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="fe4b6-187">Para ativar o Protocolo do MessagePack `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` hub no servidor, instale o pacote em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="fe4b6-188">No `Startup.ConfigureServices` método, `AddMessagePackProtocol` adicione `AddSignalR` à chamada para ativar o suporte ao MessagePack no servidor.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="fe4b6-189">O JSON é habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-189">JSON is enabled by default.</span></span> <span data-ttu-id="fe4b6-190">A adição do MessagePack permite o suporte para clientes JSON e MessagePack.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="fe4b6-191">Para personalizar como o MessagePack `AddMessagePackProtocol` formatará seus dados, é necessário um delegado para configurar opções.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="fe4b6-192">Nesse delegado, `FormatterResolvers` a propriedade pode ser usada para configurar opções de serialização do MessagePack.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="fe4b6-193">Para obter mais informações sobre como os resolvers funcionam, visite a biblioteca MessagePack no [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="fe4b6-194">Os atributos podem ser usados nos objetos que você deseja serializar para definir como eles devem ser tratados.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="fe4b6-195">Recomendamos fortemente a revisão do [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e a aplicação dos patches recomendados.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="fe4b6-196">Por exemplo, `MessagePackSecurity.Active` definindo `MessagePackSecurity.UntrustedData`a propriedade estática para .</span><span class="sxs-lookup"><span data-stu-id="fe4b6-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="fe4b6-197">A `MessagePackSecurity.Active` configuração da configuração requer a instalação manual de uma [versão 1.9.x do MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="fe4b6-198">A `MessagePack` instalação do 1.9.x SignalR atualiza a versão que a versão usa.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="fe4b6-199">Quando `MessagePackSecurity.Active` não está `MessagePackSecurity.UntrustedData`definido para, um cliente mal-intencionado pode causar uma negação de serviço.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-199">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="fe4b6-200">Configurado, `MessagePackSecurity.Active` `Program.Main`conforme mostrado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-200">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="fe4b6-201">Configurar o MessagePack no cliente</span><span class="sxs-lookup"><span data-stu-id="fe4b6-201">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="fe4b6-202">O JSON é habilitado por padrão para os clientes suportados.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-202">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="fe4b6-203">Os clientes só podem suportar um único protocolo.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-203">Clients can only support a single protocol.</span></span> <span data-ttu-id="fe4b6-204">A adição do suporte ao MessagePack substituirá quaisquer protocolos previamente configurados.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-204">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="fe4b6-205">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="fe4b6-205">.NET client</span></span>

<span data-ttu-id="fe4b6-206">Para ativar o MessagePack no cliente `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, instale o pacote e ligue `AddMessagePackProtocol` `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-206">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="fe4b6-207">Esta `AddMessagePackProtocol` chamada leva um delegado para configurar opções como o servidor.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-207">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="fe4b6-208">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="fe4b6-208">JavaScript client</span></span>

<span data-ttu-id="fe4b6-209">O suporte ao MessagePack para o [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) cliente JavaScript é fornecido pelo pacote npm.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-209">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="fe4b6-210">Instale o pacote executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-210">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="fe4b6-211">Depois de instalar o pacote npm, o módulo pode ser usado diretamente através de um carregador de módulo JavaScript ou importado para o navegador fazendo referência ao seguinte arquivo:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-211">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="fe4b6-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="fe4b6-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="fe4b6-213">Em um navegador, a `msgpack5` biblioteca também deve ser referenciada.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-213">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="fe4b6-214">Use `<script>` uma tag para criar uma referência.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-214">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="fe4b6-215">A biblioteca pode ser encontrada em *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-215">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="fe4b6-216">Ao usar `<script>` o elemento, a ordem é importante.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-216">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="fe4b6-217">Se *signalr-protocol-msgpack.js* for referenciado antes *de msgpack5.js,* um erro ocorrerá ao tentar se conectar com o MessagePack.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-217">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="fe4b6-218">*signalr.js* também é necessário antes *do signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-218">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="fe4b6-219">A `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` adição à `HubConnectionBuilder` vontade configurará o cliente para usar o protocolo MessagePack ao se conectar a um servidor.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-219">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="fe4b6-220">No momento, não há opções de configuração para o protocolo MessagePack no cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-220">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="fe4b6-221">Peculiaridades do MessagePack</span><span class="sxs-lookup"><span data-stu-id="fe4b6-221">MessagePack quirks</span></span>

<span data-ttu-id="fe4b6-222">Existem alguns problemas a serem informados ao usar o Protocolo do MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-222">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="fe4b6-223">MessagePack é sensível a maiúsculas e minúsculas</span><span class="sxs-lookup"><span data-stu-id="fe4b6-223">MessagePack is case-sensitive</span></span>

<span data-ttu-id="fe4b6-224">O protocolo MessagePack é sensível a maiúsculas e minúsculas.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-224">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="fe4b6-225">Por exemplo, considere a seguinte classe C#:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-225">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="fe4b6-226">Ao enviar do cliente JavaScript, `PascalCased` você deve usar nomes de propriedade, já que o invólucro deve corresponder exatamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-226">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="fe4b6-227">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-227">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="fe4b6-228">O `camelCased` uso de nomes não se liga corretamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-228">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="fe4b6-229">Você pode contornar isso `Key` usando o atributo para especificar um nome diferente para a propriedade MessagePack.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-229">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="fe4b6-230">Para obter mais informações, consulte [a documentação MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-230">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="fe4b6-231">DateTime.Kind não é preservado ao serializar/desserializar</span><span class="sxs-lookup"><span data-stu-id="fe4b6-231">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="fe4b6-232">O protocolo MessagePack não fornece uma maneira `Kind` de `DateTime`codificar o valor de um .</span><span class="sxs-lookup"><span data-stu-id="fe4b6-232">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="fe4b6-233">Como resultado, ao desserializar uma data, o MessagePack Hub Protocol assume que a data de entrada está no formato UTC.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-233">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="fe4b6-234">Se você está `DateTime` trabalhando com valores em horário local, recomendamos converter para UTC antes de enviá-los.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-234">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="fe4b6-235">Converta-os de UTC para o horário local quando você recebê-los.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-235">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="fe4b6-236">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-236">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="fe4b6-237">DateTime.MinValue não é suportado pelo MessagePack no JavaScript</span><span class="sxs-lookup"><span data-stu-id="fe4b6-237">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="fe4b6-238">A biblioteca [msgpack5](https://github.com/mcollina/msgpack5) SignalR usada pelo cliente JavaScript `timestamp96` não suporta o tipo no MessagePack.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-238">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="fe4b6-239">Este tipo é usado para codificar valores de data muito grandes (seja muito cedo no passado ou muito longe no futuro).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-239">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="fe4b6-240">O valor `DateTime.MinValue` `January 1, 0001`é , que deve ser `timestamp96` codificado em um valor.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-240">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="fe4b6-241">Por causa disso, o envio `DateTime.MinValue` para um cliente JavaScript não é suportado.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-241">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="fe4b6-242">Quando `DateTime.MinValue` é recebido pelo cliente JavaScript, o seguinte erro é jogado:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-242">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="fe4b6-243">Normalmente, `DateTime.MinValue` é usado para codificar `null` um "ausente" ou valor.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-243">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="fe4b6-244">Se você precisar codificar esse valor no MessagePack, use um `DateTime` valor anulado (`DateTime?`) ou codifique um valor separado `bool` indicando se a data está presente.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-244">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="fe4b6-245">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [aspnet/#2228SignalR](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-245">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="fe4b6-246">Suporte ao MessagePack em ambiente de compilação "antecipada"</span><span class="sxs-lookup"><span data-stu-id="fe4b6-246">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="fe4b6-247">A [biblioteca MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) usada pelo cliente e servidor .NET usa a geração de código para otimizar a serialização.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-247">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="fe4b6-248">Como resultado, ele não é suportado por padrão em ambientes que usam compilação "antecipadamente" (como Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-248">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="fe4b6-249">É possível usar o MessagePack nesses ambientes "pré-gerando" o código serializador/desserializador.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-249">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="fe4b6-250">Para obter mais informações, consulte [a documentação MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-250">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="fe4b6-251">Depois de ter pré-gerado os serializadores, você pode `AddMessagePackProtocol`registrá-los usando o delegado de configuração passado para:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-251">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="fe4b6-252">Verificações de tipo são mais rigorosas no MessagePack</span><span class="sxs-lookup"><span data-stu-id="fe4b6-252">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="fe4b6-253">O Protocolo Do Hub JSON realizará conversões de tipo durante a desserialização.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-253">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="fe4b6-254">Por exemplo, se o objeto de entrada tiver`{ foo: 42 }`um valor de propriedade que seja `string`um número ( ) mas a propriedade na classe .NET for do tipo, o valor será convertido.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-254">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="fe4b6-255">No entanto, o MessagePack não executa essa conversão e lançará uma exceção que pode ser vista em logs do lado do servidor (e no console):</span><span class="sxs-lookup"><span data-stu-id="fe4b6-255">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="fe4b6-256">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [aspnet/#2937SignalR](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="fe4b6-257">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="fe4b6-257">Related resources</span></span>

* [<span data-ttu-id="fe4b6-258">Introdução</span><span class="sxs-lookup"><span data-stu-id="fe4b6-258">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="fe4b6-259">.NET cliente</span><span class="sxs-lookup"><span data-stu-id="fe4b6-259">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="fe4b6-260">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="fe4b6-260">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fe4b6-261">Este artigo assume que o leitor está familiarizado com os tópicos [abordados](xref:tutorials/signalr)em Get Started .</span><span class="sxs-lookup"><span data-stu-id="fe4b6-261">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="fe4b6-262">O que é O Pacote de Mensagens?</span><span class="sxs-lookup"><span data-stu-id="fe4b6-262">What is MessagePack?</span></span>

<span data-ttu-id="fe4b6-263">[MessagePack](https://msgpack.org/index.html) é um formato de serialização binária rápido e compacto.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-263">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="fe4b6-264">É útil quando o desempenho e a largura de banda são uma preocupação porque cria mensagens menores em comparação com [json](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-264">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="fe4b6-265">As mensagens binárias são ilegíveis ao olhar para os traços e logs de rede, a menos que os bytes sejam passados através de um analisador MessagePack.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-265">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="fe4b6-266">tem suporte integrado para o formato MessagePack e fornece APIs para o cliente e servidor usarem.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-266"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="fe4b6-267">Configurar o MessagePack no servidor</span><span class="sxs-lookup"><span data-stu-id="fe4b6-267">Configure MessagePack on the server</span></span>

<span data-ttu-id="fe4b6-268">Para ativar o Protocolo do MessagePack `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` hub no servidor, instale o pacote em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-268">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="fe4b6-269">No `Startup.ConfigureServices` método, `AddMessagePackProtocol` adicione `AddSignalR` à chamada para ativar o suporte ao MessagePack no servidor.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-269">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="fe4b6-270">O JSON é habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-270">JSON is enabled by default.</span></span> <span data-ttu-id="fe4b6-271">A adição do MessagePack permite o suporte para clientes JSON e MessagePack.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-271">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="fe4b6-272">Para personalizar como o MessagePack `AddMessagePackProtocol` formatará seus dados, é necessário um delegado para configurar opções.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-272">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="fe4b6-273">Nesse delegado, `FormatterResolvers` a propriedade pode ser usada para configurar opções de serialização do MessagePack.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-273">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="fe4b6-274">Para obter mais informações sobre como os resolvers funcionam, visite a biblioteca MessagePack no [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-274">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="fe4b6-275">Os atributos podem ser usados nos objetos que você deseja serializar para definir como eles devem ser tratados.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-275">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="fe4b6-276">Recomendamos fortemente a revisão do [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e a aplicação dos patches recomendados.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-276">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="fe4b6-277">Por exemplo, `MessagePackSecurity.Active` definindo `MessagePackSecurity.UntrustedData`a propriedade estática para .</span><span class="sxs-lookup"><span data-stu-id="fe4b6-277">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="fe4b6-278">A `MessagePackSecurity.Active` configuração da configuração requer a instalação manual de uma [versão 1.9.x do MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-278">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="fe4b6-279">A `MessagePack` instalação do 1.9.x SignalR atualiza a versão que a versão usa.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-279">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="fe4b6-280">Quando `MessagePackSecurity.Active` não está `MessagePackSecurity.UntrustedData`definido para, um cliente mal-intencionado pode causar uma negação de serviço.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-280">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="fe4b6-281">Configurado, `MessagePackSecurity.Active` `Program.Main`conforme mostrado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-281">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="fe4b6-282">Configurar o MessagePack no cliente</span><span class="sxs-lookup"><span data-stu-id="fe4b6-282">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="fe4b6-283">O JSON é habilitado por padrão para os clientes suportados.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-283">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="fe4b6-284">Os clientes só podem suportar um único protocolo.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-284">Clients can only support a single protocol.</span></span> <span data-ttu-id="fe4b6-285">A adição do suporte ao MessagePack substituirá quaisquer protocolos previamente configurados.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-285">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="fe4b6-286">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="fe4b6-286">.NET client</span></span>

<span data-ttu-id="fe4b6-287">Para ativar o MessagePack no cliente `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, instale o pacote e ligue `AddMessagePackProtocol` `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-287">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="fe4b6-288">Esta `AddMessagePackProtocol` chamada leva um delegado para configurar opções como o servidor.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-288">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="fe4b6-289">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="fe4b6-289">JavaScript client</span></span>

<span data-ttu-id="fe4b6-290">O suporte ao MessagePack para o [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) cliente JavaScript é fornecido pelo pacote npm.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-290">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="fe4b6-291">Instale o pacote executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-291">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="fe4b6-292">Depois de instalar o pacote npm, o módulo pode ser usado diretamente através de um carregador de módulo JavaScript ou importado para o navegador fazendo referência ao seguinte arquivo:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-292">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="fe4b6-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="fe4b6-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="fe4b6-294">Em um navegador, a `msgpack5` biblioteca também deve ser referenciada.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-294">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="fe4b6-295">Use `<script>` uma tag para criar uma referência.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-295">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="fe4b6-296">A biblioteca pode ser encontrada em *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-296">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="fe4b6-297">Ao usar `<script>` o elemento, a ordem é importante.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-297">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="fe4b6-298">Se *signalr-protocol-msgpack.js* for referenciado antes *de msgpack5.js,* um erro ocorrerá ao tentar se conectar com o MessagePack.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-298">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="fe4b6-299">*signalr.js* também é necessário antes *do signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-299">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="fe4b6-300">A `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` adição à `HubConnectionBuilder` vontade configurará o cliente para usar o protocolo MessagePack ao se conectar a um servidor.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-300">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="fe4b6-301">No momento, não há opções de configuração para o protocolo MessagePack no cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-301">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="fe4b6-302">Peculiaridades do MessagePack</span><span class="sxs-lookup"><span data-stu-id="fe4b6-302">MessagePack quirks</span></span>

<span data-ttu-id="fe4b6-303">Existem alguns problemas a serem informados ao usar o Protocolo do MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-303">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="fe4b6-304">MessagePack é sensível a maiúsculas e minúsculas</span><span class="sxs-lookup"><span data-stu-id="fe4b6-304">MessagePack is case-sensitive</span></span>

<span data-ttu-id="fe4b6-305">O protocolo MessagePack é sensível a maiúsculas e minúsculas.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-305">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="fe4b6-306">Por exemplo, considere a seguinte classe C#:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-306">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="fe4b6-307">Ao enviar do cliente JavaScript, `PascalCased` você deve usar nomes de propriedade, já que o invólucro deve corresponder exatamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-307">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="fe4b6-308">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-308">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="fe4b6-309">O `camelCased` uso de nomes não se liga corretamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-309">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="fe4b6-310">Você pode contornar isso `Key` usando o atributo para especificar um nome diferente para a propriedade MessagePack.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-310">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="fe4b6-311">Para obter mais informações, consulte [a documentação MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-311">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="fe4b6-312">DateTime.Kind não é preservado ao serializar/desserializar</span><span class="sxs-lookup"><span data-stu-id="fe4b6-312">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="fe4b6-313">O protocolo MessagePack não fornece uma maneira `Kind` de `DateTime`codificar o valor de um .</span><span class="sxs-lookup"><span data-stu-id="fe4b6-313">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="fe4b6-314">Como resultado, ao desserializar uma data, o MessagePack Hub Protocol assume que a data de entrada está no formato UTC.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-314">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="fe4b6-315">Se você está `DateTime` trabalhando com valores em horário local, recomendamos converter para UTC antes de enviá-los.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-315">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="fe4b6-316">Converta-os de UTC para o horário local quando você recebê-los.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-316">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="fe4b6-317">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-317">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="fe4b6-318">DateTime.MinValue não é suportado pelo MessagePack no JavaScript</span><span class="sxs-lookup"><span data-stu-id="fe4b6-318">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="fe4b6-319">A biblioteca [msgpack5](https://github.com/mcollina/msgpack5) SignalR usada pelo cliente JavaScript `timestamp96` não suporta o tipo no MessagePack.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-319">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="fe4b6-320">Este tipo é usado para codificar valores de data muito grandes (seja muito cedo no passado ou muito longe no futuro).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-320">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="fe4b6-321">O valor `DateTime.MinValue` `January 1, 0001` é que deve ser `timestamp96` codificado em um valor.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-321">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="fe4b6-322">Por causa disso, o envio `DateTime.MinValue` para um cliente JavaScript não é suportado.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-322">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="fe4b6-323">Quando `DateTime.MinValue` é recebido pelo cliente JavaScript, o seguinte erro é jogado:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-323">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="fe4b6-324">Normalmente, `DateTime.MinValue` é usado para codificar `null` um "ausente" ou valor.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-324">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="fe4b6-325">Se você precisar codificar esse valor no MessagePack, use um `DateTime` valor anulado (`DateTime?`) ou codifique um valor separado `bool` indicando se a data está presente.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-325">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="fe4b6-326">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [aspnet/#2228SignalR](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-326">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="fe4b6-327">Suporte ao MessagePack em ambiente de compilação "antecipada"</span><span class="sxs-lookup"><span data-stu-id="fe4b6-327">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="fe4b6-328">A [biblioteca MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) usada pelo cliente e servidor .NET usa a geração de código para otimizar a serialização.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-328">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="fe4b6-329">Como resultado, ele não é suportado por padrão em ambientes que usam compilação "antecipadamente" (como Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-329">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="fe4b6-330">É possível usar o MessagePack nesses ambientes "pré-gerando" o código serializador/desserializador.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-330">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="fe4b6-331">Para obter mais informações, consulte [a documentação MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-331">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="fe4b6-332">Depois de ter pré-gerado os serializadores, você pode `AddMessagePackProtocol`registrá-los usando o delegado de configuração passado para:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-332">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="fe4b6-333">Verificações de tipo são mais rigorosas no MessagePack</span><span class="sxs-lookup"><span data-stu-id="fe4b6-333">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="fe4b6-334">O Protocolo Do Hub JSON realizará conversões de tipo durante a desserialização.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-334">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="fe4b6-335">Por exemplo, se o objeto de entrada tiver`{ foo: 42 }`um valor de propriedade que seja `string`um número ( ) mas a propriedade na classe .NET for do tipo, o valor será convertido.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-335">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="fe4b6-336">No entanto, o MessagePack não executa essa conversão e lançará uma exceção que pode ser vista em logs do lado do servidor (e no console):</span><span class="sxs-lookup"><span data-stu-id="fe4b6-336">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="fe4b6-337">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [aspnet/#2937SignalR](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="fe4b6-338">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="fe4b6-338">Related resources</span></span>

* [<span data-ttu-id="fe4b6-339">Introdução</span><span class="sxs-lookup"><span data-stu-id="fe4b6-339">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="fe4b6-340">.NET cliente</span><span class="sxs-lookup"><span data-stu-id="fe4b6-340">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="fe4b6-341">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="fe4b6-341">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
