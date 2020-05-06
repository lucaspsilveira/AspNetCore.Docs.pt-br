---
title: Usar o protocolo Hub MessagePack SignalR no para ASP.NET Core
author: bradygaster
description: Adicione o protocolo Hub MessagePack a SignalRASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 8db7598d978848f13bf5b21a873340b38154e9a8
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777196"
---
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a>Usar o protocolo Hub MessagePack SignalR no para ASP.NET Core

::: moniker range=">= aspnetcore-5.0"

Este artigo pressupõe que o leitor esteja familiarizado com os tópicos abordados em [introdução](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>O que é o MessagePack?

[MessagePack](https://msgpack.org/index.html) é um formato de serialização binária rápida e compacto. É útil quando o desempenho e a largura de banda são uma preocupação, pois ele cria mensagens menores comparadas ao [JSON](https://www.json.org/). As mensagens binárias são ilegíveis ao examinar rastreamentos de rede e logs, a menos que os bytes sejam passados por um analisador MessagePack. SignalRtem suporte interno para o formato MessagePack e fornece APIs para o cliente e o servidor usarem.

## <a name="configure-messagepack-on-the-server"></a>Configurar o MessagePack no servidor

Para habilitar o protocolo de Hub do MessagePack no servidor, instale `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` o pacote em seu aplicativo. No `Startup.ConfigureServices` método, adicione `AddMessagePackProtocol` à `AddSignalR` chamada para habilitar o suporte a MessagePack no servidor.

> [!NOTE]
> O JSON é habilitado por padrão. Adicionar MessagePack habilita o suporte para clientes JSON e MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Para personalizar como o MessagePack formatará seus dados `AddMessagePackProtocol` , o usa um delegado para configurar opções. Nesse delegado, a `SerializerOptions` propriedade pode ser usada para configurar opções de serialização MessagePack. Para obter mais informações sobre como os resolvedores funcionam, visite a biblioteca MessagePack em [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp). Os atributos podem ser usados nos objetos que você deseja serializar para definir como eles devem ser manipulados.

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
> É altamente recomendável revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e aplicar os patches recomendados. Por exemplo, chamar `.WithSecurity(MessagePackSecurity.UntrustedData)` ao substituir o `SerializerOptions`.

## <a name="configure-messagepack-on-the-client"></a>Configurar o MessagePack no cliente

> [!NOTE]
> O JSON é habilitado por padrão para os clientes com suporte. Os clientes só podem dar suporte a um único protocolo. Adicionar o suporte a MessagePack substituirá todos os protocolos configurados anteriormente.

### <a name="net-client"></a>Cliente .NET

Para habilitar o MessagePack no cliente .NET, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote e chame `AddMessagePackProtocol` - `HubConnectionBuilder`o em.

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Essa `AddMessagePackProtocol` chamada usa um delegado para configurar opções assim como o servidor.

### <a name="javascript-client"></a>Cliente JavaScript

O [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) suporte do MessagePack para o cliente JavaScript é fornecido pelo pacote NPM. Instale o pacote executando o seguinte comando em um shell de comando:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Depois de instalar o pacote NPM, o módulo pode ser usado diretamente por meio de um carregador de módulo JavaScript ou importado para o navegador referenciando o seguinte arquivo:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

Em um navegador, a `msgpack5` biblioteca também deve ser referenciada. Use uma `<script>` marca para criar uma referência. A biblioteca pode ser encontrada em *node_modules \msgpack5\dist\msgpack5.js*.

> [!NOTE]
> Ao usar o `<script>` elemento, a ordem é importante. Se *signalr-Protocol-msgpack. js* for referenciado antes de *msgpack5. js*, ocorrerá um erro ao tentar se conectar com MessagePack. *signalr. js* também é necessário antes de *signalr-Protocol-msgpack. js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Adicionar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` ao `HubConnectionBuilder` irá configurar o cliente para usar o protocolo MessagePack ao se conectar a um servidor.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> Neste momento, não há nenhuma opção de configuração para o protocolo MessagePack no cliente JavaScript.

## <a name="messagepack-quirks"></a>Peculiaridades MessagePacks

Há alguns problemas a serem considerados ao usar o protocolo de Hub MessagePack.

### <a name="messagepack-is-case-sensitive"></a>MessagePack diferencia maiúsculas de minúsculas

O protocolo MessagePack diferencia maiúsculas de minúsculas. Por exemplo, considere a seguinte classe C#:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Ao enviar do cliente JavaScript, você deve usar `PascalCased` nomes de propriedade, já que os maiúsculas e minúsculas devem corresponder exatamente à classe C#. Por exemplo: 

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

O `camelCased` uso de nomes não se associará corretamente à classe C#. Você pode contornar isso usando o `Key` atributo para especificar um nome diferente para a propriedade MessagePack. Para obter mais informações, consulte [a documentação do MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime. Kind não é preservado ao serializar/desserializar

O protocolo MessagePack não fornece uma maneira de codificar o `Kind` valor de a `DateTime`. Como resultado, ao desserializar uma data, o protocolo de Hub MessagePack será convertido para o formato UTC se `DateTime.Kind` , `DateTimeKind.Local` caso contrário, ele não tocará o tempo e o passará como está. Se você estiver trabalhando com `DateTime` valores, é recomendável converter para UTC antes de enviá-los. Converta-os do UTC para a hora local quando você os receber.

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>Não há suporte para DateTime. MinValue em MessagePack em JavaScript

A biblioteca [msgpack5](https://github.com/mcollina/msgpack5) usada pelo cliente SignalR JavaScript não dá suporte ao `timestamp96` tipo em MessagePack. Esse tipo é usado para codificar valores de data muito grandes (muito cedo no passado ou muito distante no futuro). O valor de `DateTime.MinValue` é `January 1, 0001`, que deve ser codificado em um `timestamp96` valor. Por isso, não há `DateTime.MinValue` suporte para enviar para um cliente JavaScript. Quando `DateTime.MinValue` é recebido pelo cliente JavaScript, o seguinte erro é gerado:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Normalmente, `DateTime.MinValue` é usado para codificar um "ausente" ou `null` um valor. Se você precisar codificar esse valor em MessagePack, use um valor Anulável `DateTime` (`DateTime?`) ou codifique um valor separado `bool` indicando se a data está presente.

Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Suporte a MessagePack no ambiente de compilação "antes do tempo"

A biblioteca [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) usada pelo cliente .net e pelo servidor usa a geração de código para otimizar a serialização. Como resultado, ele não tem suporte por padrão em ambientes que usam a compilação "antecipada" (como o Xamarin iOS ou Unity). É possível usar o MessagePack nesses ambientes ao "gerar previamente" o código do serializador/desserializador. Para obter mais informações, consulte [a documentação do MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin). Depois de gerar os serializadores gerados previamente, você pode registrá-los usando o delegado de configuração `AddMessagePackProtocol`passado para:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>As verificações de tipo são mais estritas em MessagePack

O protocolo de Hub JSON executará conversões de tipo durante a desserialização. Por exemplo, se o objeto de entrada tiver um valor de propriedade que seja um`{ foo: 42 }`número (), mas a propriedade na classe .NET for `string`do tipo, o valor será convertido. No entanto, o MessagePack não executa essa conversão e gerará uma exceção que pode ser vista nos logs do lado do servidor (e no console do):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Recursos relacionados

* [Introdução](xref:tutorials/signalr)
* [Cliente .NET](xref:signalr/dotnet-client)
* [Cliente JavaScript](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Este artigo pressupõe que o leitor esteja familiarizado com os tópicos abordados em [introdução](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>O que é o MessagePack?

[MessagePack](https://msgpack.org/index.html) é um formato de serialização binária rápida e compacto. É útil quando o desempenho e a largura de banda são uma preocupação, pois ele cria mensagens menores comparadas ao [JSON](https://www.json.org/). As mensagens binárias são ilegíveis ao examinar rastreamentos de rede e logs, a menos que os bytes sejam passados por um analisador MessagePack. SignalRtem suporte interno para o formato MessagePack e fornece APIs para o cliente e o servidor usarem.

## <a name="configure-messagepack-on-the-server"></a>Configurar o MessagePack no servidor

Para habilitar o protocolo de Hub do MessagePack no servidor, instale `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` o pacote em seu aplicativo. No `Startup.ConfigureServices` método, adicione `AddMessagePackProtocol` à `AddSignalR` chamada para habilitar o suporte a MessagePack no servidor.

> [!NOTE]
> O JSON é habilitado por padrão. Adicionar MessagePack habilita o suporte para clientes JSON e MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Para personalizar como o MessagePack formatará seus dados `AddMessagePackProtocol` , o usa um delegado para configurar opções. Nesse delegado, a `FormatterResolvers` propriedade pode ser usada para configurar opções de serialização MessagePack. Para obter mais informações sobre como os resolvedores funcionam, visite a biblioteca MessagePack em [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp). Os atributos podem ser usados nos objetos que você deseja serializar para definir como eles devem ser manipulados.

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
> É altamente recomendável revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e aplicar os patches recomendados. Por exemplo, definir a `MessagePackSecurity.Active` propriedade estática como `MessagePackSecurity.UntrustedData`. Definir o `MessagePackSecurity.Active` requer a instalação manual [de uma versão 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3). A `MessagePack` instalação das atualizações 1.9. x SignalR é usada pela versão. Quando `MessagePackSecurity.Active` não está definido como `MessagePackSecurity.UntrustedData`, um cliente mal-intencionado pode causar uma negação de serviço. Defina `MessagePackSecurity.Active` em `Program.Main`, conforme mostrado no código a seguir:

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>Configurar o MessagePack no cliente

> [!NOTE]
> O JSON é habilitado por padrão para os clientes com suporte. Os clientes só podem dar suporte a um único protocolo. Adicionar o suporte a MessagePack substituirá todos os protocolos configurados anteriormente.

### <a name="net-client"></a>Cliente .NET

Para habilitar o MessagePack no cliente .NET, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote e chame `AddMessagePackProtocol` - `HubConnectionBuilder`o em.

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Essa `AddMessagePackProtocol` chamada usa um delegado para configurar opções assim como o servidor.

### <a name="javascript-client"></a>Cliente JavaScript

O [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) suporte do MessagePack para o cliente JavaScript é fornecido pelo pacote NPM. Instale o pacote executando o seguinte comando em um shell de comando:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Depois de instalar o pacote NPM, o módulo pode ser usado diretamente por meio de um carregador de módulo JavaScript ou importado para o navegador referenciando o seguinte arquivo:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

Em um navegador, a `msgpack5` biblioteca também deve ser referenciada. Use uma `<script>` marca para criar uma referência. A biblioteca pode ser encontrada em *node_modules \msgpack5\dist\msgpack5.js*.

> [!NOTE]
> Ao usar o `<script>` elemento, a ordem é importante. Se *signalr-Protocol-msgpack. js* for referenciado antes de *msgpack5. js*, ocorrerá um erro ao tentar se conectar com MessagePack. *signalr. js* também é necessário antes de *signalr-Protocol-msgpack. js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Adicionar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` ao `HubConnectionBuilder` irá configurar o cliente para usar o protocolo MessagePack ao se conectar a um servidor.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> Neste momento, não há nenhuma opção de configuração para o protocolo MessagePack no cliente JavaScript.

## <a name="messagepack-quirks"></a>Peculiaridades MessagePacks

Há alguns problemas a serem considerados ao usar o protocolo de Hub MessagePack.

### <a name="messagepack-is-case-sensitive"></a>MessagePack diferencia maiúsculas de minúsculas

O protocolo MessagePack diferencia maiúsculas de minúsculas. Por exemplo, considere a seguinte classe C#:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Ao enviar do cliente JavaScript, você deve usar `PascalCased` nomes de propriedade, já que os maiúsculas e minúsculas devem corresponder exatamente à classe C#. Por exemplo: 

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

O `camelCased` uso de nomes não se associará corretamente à classe C#. Você pode contornar isso usando o `Key` atributo para especificar um nome diferente para a propriedade MessagePack. Para obter mais informações, consulte [a documentação do MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime. Kind não é preservado ao serializar/desserializar

O protocolo MessagePack não fornece uma maneira de codificar o `Kind` valor de a `DateTime`. Como resultado, ao desserializar uma data, o protocolo de Hub MessagePack assume que a data de entrada está no formato UTC. Se você estiver trabalhando com `DateTime` valores na hora local, é recomendável converter para UTC antes de enviá-los. Converta-os do UTC para a hora local quando você os receber.

Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>Não há suporte para DateTime. MinValue em MessagePack em JavaScript

A biblioteca [msgpack5](https://github.com/mcollina/msgpack5) usada pelo cliente SignalR JavaScript não dá suporte ao `timestamp96` tipo em MessagePack. Esse tipo é usado para codificar valores de data muito grandes (muito cedo no passado ou muito distante no futuro). O valor de `DateTime.MinValue` é `January 1, 0001`, que deve ser codificado em um `timestamp96` valor. Por isso, não há `DateTime.MinValue` suporte para enviar para um cliente JavaScript. Quando `DateTime.MinValue` é recebido pelo cliente JavaScript, o seguinte erro é gerado:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Normalmente, `DateTime.MinValue` é usado para codificar um "ausente" ou `null` um valor. Se você precisar codificar esse valor em MessagePack, use um valor Anulável `DateTime` (`DateTime?`) ou codifique um valor separado `bool` indicando se a data está presente.

Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Suporte a MessagePack no ambiente de compilação "antes do tempo"

A biblioteca [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) usada pelo cliente .net e pelo servidor usa a geração de código para otimizar a serialização. Como resultado, ele não tem suporte por padrão em ambientes que usam a compilação "antecipada" (como o Xamarin iOS ou Unity). É possível usar o MessagePack nesses ambientes ao "gerar previamente" o código do serializador/desserializador. Para obter mais informações, consulte [a documentação do MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). Depois de gerar os serializadores gerados previamente, você pode registrá-los usando o delegado de configuração `AddMessagePackProtocol`passado para:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>As verificações de tipo são mais estritas em MessagePack

O protocolo de Hub JSON executará conversões de tipo durante a desserialização. Por exemplo, se o objeto de entrada tiver um valor de propriedade que seja um`{ foo: 42 }`número (), mas a propriedade na classe .NET for `string`do tipo, o valor será convertido. No entanto, o MessagePack não executa essa conversão e gerará uma exceção que pode ser vista nos logs do lado do servidor (e no console do):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Recursos relacionados

* [Introdução](xref:tutorials/signalr)
* [Cliente .NET](xref:signalr/dotnet-client)
* [Cliente JavaScript](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Este artigo pressupõe que o leitor esteja familiarizado com os tópicos abordados em [introdução](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>O que é o MessagePack?

[MessagePack](https://msgpack.org/index.html) é um formato de serialização binária rápida e compacto. É útil quando o desempenho e a largura de banda são uma preocupação, pois ele cria mensagens menores comparadas ao [JSON](https://www.json.org/). As mensagens binárias são ilegíveis ao examinar rastreamentos de rede e logs, a menos que os bytes sejam passados por um analisador MessagePack. SignalRtem suporte interno para o formato MessagePack e fornece APIs para o cliente e o servidor usarem.

## <a name="configure-messagepack-on-the-server"></a>Configurar o MessagePack no servidor

Para habilitar o protocolo de Hub do MessagePack no servidor, instale `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` o pacote em seu aplicativo. No `Startup.ConfigureServices` método, adicione `AddMessagePackProtocol` à `AddSignalR` chamada para habilitar o suporte a MessagePack no servidor.

> [!NOTE]
> O JSON é habilitado por padrão. Adicionar MessagePack habilita o suporte para clientes JSON e MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Para personalizar como o MessagePack formatará seus dados `AddMessagePackProtocol` , o usa um delegado para configurar opções. Nesse delegado, a `FormatterResolvers` propriedade pode ser usada para configurar opções de serialização MessagePack. Para obter mais informações sobre como os resolvedores funcionam, visite a biblioteca MessagePack em [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp). Os atributos podem ser usados nos objetos que você deseja serializar para definir como eles devem ser manipulados.

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
> É altamente recomendável revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e aplicar os patches recomendados. Por exemplo, definir a `MessagePackSecurity.Active` propriedade estática como `MessagePackSecurity.UntrustedData`. Definir o `MessagePackSecurity.Active` requer a instalação manual [de uma versão 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3). A `MessagePack` instalação das atualizações 1.9. x SignalR é usada pela versão. Quando `MessagePackSecurity.Active` não está definido como `MessagePackSecurity.UntrustedData`, um cliente mal-intencionado pode causar uma negação de serviço. Defina `MessagePackSecurity.Active` em `Program.Main`, conforme mostrado no código a seguir:

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>Configurar o MessagePack no cliente

> [!NOTE]
> O JSON é habilitado por padrão para os clientes com suporte. Os clientes só podem dar suporte a um único protocolo. Adicionar o suporte a MessagePack substituirá todos os protocolos configurados anteriormente.

### <a name="net-client"></a>Cliente .NET

Para habilitar o MessagePack no cliente .NET, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote e chame `AddMessagePackProtocol` - `HubConnectionBuilder`o em.

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Essa `AddMessagePackProtocol` chamada usa um delegado para configurar opções assim como o servidor.

### <a name="javascript-client"></a>Cliente JavaScript

O [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) suporte do MessagePack para o cliente JavaScript é fornecido pelo pacote NPM. Instale o pacote executando o seguinte comando em um shell de comando:

```bash
npm install @aspnet/signalr-protocol-msgpack
```

Depois de instalar o pacote NPM, o módulo pode ser usado diretamente por meio de um carregador de módulo JavaScript ou importado para o navegador referenciando o seguinte arquivo:

*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*

Em um navegador, a `msgpack5` biblioteca também deve ser referenciada. Use uma `<script>` marca para criar uma referência. A biblioteca pode ser encontrada em *node_modules \msgpack5\dist\msgpack5.js*.

> [!NOTE]
> Ao usar o `<script>` elemento, a ordem é importante. Se *signalr-Protocol-msgpack. js* for referenciado antes de *msgpack5. js*, ocorrerá um erro ao tentar se conectar com MessagePack. *signalr. js* também é necessário antes de *signalr-Protocol-msgpack. js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Adicionar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` ao `HubConnectionBuilder` irá configurar o cliente para usar o protocolo MessagePack ao se conectar a um servidor.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> Neste momento, não há nenhuma opção de configuração para o protocolo MessagePack no cliente JavaScript.

## <a name="messagepack-quirks"></a>Peculiaridades MessagePacks

Há alguns problemas a serem considerados ao usar o protocolo de Hub MessagePack.

### <a name="messagepack-is-case-sensitive"></a>MessagePack diferencia maiúsculas de minúsculas

O protocolo MessagePack diferencia maiúsculas de minúsculas. Por exemplo, considere a seguinte classe C#:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Ao enviar do cliente JavaScript, você deve usar `PascalCased` nomes de propriedade, já que os maiúsculas e minúsculas devem corresponder exatamente à classe C#. Por exemplo: 

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

O `camelCased` uso de nomes não se associará corretamente à classe C#. Você pode contornar isso usando o `Key` atributo para especificar um nome diferente para a propriedade MessagePack. Para obter mais informações, consulte [a documentação do MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime. Kind não é preservado ao serializar/desserializar

O protocolo MessagePack não fornece uma maneira de codificar o `Kind` valor de a `DateTime`. Como resultado, ao desserializar uma data, o protocolo de Hub MessagePack assume que a data de entrada está no formato UTC. Se você estiver trabalhando com `DateTime` valores na hora local, é recomendável converter para UTC antes de enviá-los. Converta-os do UTC para a hora local quando você os receber.

Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>Não há suporte para DateTime. MinValue em MessagePack em JavaScript

A biblioteca [msgpack5](https://github.com/mcollina/msgpack5) usada pelo cliente SignalR JavaScript não dá suporte ao `timestamp96` tipo em MessagePack. Esse tipo é usado para codificar valores de data muito grandes (muito cedo no passado ou muito distante no futuro). O valor de `DateTime.MinValue` é `January 1, 0001` que deve ser codificado em um `timestamp96` valor. Por isso, não há `DateTime.MinValue` suporte para enviar para um cliente JavaScript. Quando `DateTime.MinValue` é recebido pelo cliente JavaScript, o seguinte erro é gerado:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Normalmente, `DateTime.MinValue` é usado para codificar um "ausente" ou `null` um valor. Se você precisar codificar esse valor em MessagePack, use um valor Anulável `DateTime` (`DateTime?`) ou codifique um valor separado `bool` indicando se a data está presente.

Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Suporte a MessagePack no ambiente de compilação "antes do tempo"

A biblioteca [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) usada pelo cliente .net e pelo servidor usa a geração de código para otimizar a serialização. Como resultado, ele não tem suporte por padrão em ambientes que usam a compilação "antecipada" (como o Xamarin iOS ou Unity). É possível usar o MessagePack nesses ambientes ao "gerar previamente" o código do serializador/desserializador. Para obter mais informações, consulte [a documentação do MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). Depois de gerar os serializadores gerados previamente, você pode registrá-los usando o delegado de configuração `AddMessagePackProtocol`passado para:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>As verificações de tipo são mais estritas em MessagePack

O protocolo de Hub JSON executará conversões de tipo durante a desserialização. Por exemplo, se o objeto de entrada tiver um valor de propriedade que seja um`{ foo: 42 }`número (), mas a propriedade na classe .NET for `string`do tipo, o valor será convertido. No entanto, o MessagePack não executa essa conversão e gerará uma exceção que pode ser vista nos logs do lado do servidor (e no console do):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Recursos relacionados

* [Introdução](xref:tutorials/signalr)
* [Cliente .NET](xref:signalr/dotnet-client)
* [Cliente JavaScript](xref:signalr/javascript-client)

::: moniker-end
