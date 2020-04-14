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
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a>Use o protocolo SignalR do MessagePack Hub para ASP.NET núcleo

::: moniker range=">= aspnetcore-5.0"

Este artigo assume que o leitor está familiarizado com os tópicos [abordados](xref:tutorials/signalr)em Get Started .

## <a name="what-is-messagepack"></a>O que é O Pacote de Mensagens?

[MessagePack](https://msgpack.org/index.html) é um formato de serialização binária rápido e compacto. É útil quando o desempenho e a largura de banda são uma preocupação porque cria mensagens menores em comparação com [json](https://www.json.org/). As mensagens binárias são ilegíveis ao olhar para os traços e logs de rede, a menos que os bytes sejam passados através de um analisador MessagePack. SignalRtem suporte integrado para o formato MessagePack e fornece APIs para o cliente e servidor usarem.

## <a name="configure-messagepack-on-the-server"></a>Configurar o MessagePack no servidor

Para ativar o Protocolo do MessagePack `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` hub no servidor, instale o pacote em seu aplicativo. No `Startup.ConfigureServices` método, `AddMessagePackProtocol` adicione `AddSignalR` à chamada para ativar o suporte ao MessagePack no servidor.

> [!NOTE]
> O JSON é habilitado por padrão. A adição do MessagePack permite o suporte para clientes JSON e MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Para personalizar como o MessagePack `AddMessagePackProtocol` formatará seus dados, é necessário um delegado para configurar opções. Nesse delegado, `SerializerOptions` a propriedade pode ser usada para configurar opções de serialização do MessagePack. Para obter mais informações sobre como os resolvers funcionam, visite a biblioteca MessagePack no [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Os atributos podem ser usados nos objetos que você deseja serializar para definir como eles devem ser tratados.

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
> Recomendamos fortemente a revisão do [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e a aplicação dos patches recomendados. Por exemplo, `.WithSecurity(MessagePackSecurity.UntrustedData)` chamar ao `SerializerOptions`substituir o .

## <a name="configure-messagepack-on-the-client"></a>Configurar o MessagePack no cliente

> [!NOTE]
> O JSON é habilitado por padrão para os clientes suportados. Os clientes só podem suportar um único protocolo. A adição do suporte ao MessagePack substituirá quaisquer protocolos previamente configurados.

### <a name="net-client"></a>Cliente .NET

Para ativar o MessagePack no cliente `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, instale o pacote e ligue `AddMessagePackProtocol` `HubConnectionBuilder`.

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Esta `AddMessagePackProtocol` chamada leva um delegado para configurar opções como o servidor.

### <a name="javascript-client"></a>Cliente JavaScript

O suporte ao MessagePack para o [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) cliente JavaScript é fornecido pelo pacote npm. Instale o pacote executando o seguinte comando em um shell de comando:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Depois de instalar o pacote npm, o módulo pode ser usado diretamente através de um carregador de módulo JavaScript ou importado para o navegador fazendo referência ao seguinte arquivo:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

Em um navegador, a `msgpack5` biblioteca também deve ser referenciada. Use `<script>` uma tag para criar uma referência. A biblioteca pode ser encontrada em *node_modules\msgpack5\dist\msgpack5.js*.

> [!NOTE]
> Ao usar `<script>` o elemento, a ordem é importante. Se *signalr-protocol-msgpack.js* for referenciado antes *de msgpack5.js,* um erro ocorrerá ao tentar se conectar com o MessagePack. *signalr.js* também é necessário antes *do signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

A `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` adição à `HubConnectionBuilder` vontade configurará o cliente para usar o protocolo MessagePack ao se conectar a um servidor.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> No momento, não há opções de configuração para o protocolo MessagePack no cliente JavaScript.

## <a name="messagepack-quirks"></a>Peculiaridades do MessagePack

Existem alguns problemas a serem informados ao usar o Protocolo do MessagePack Hub.

### <a name="messagepack-is-case-sensitive"></a>MessagePack é sensível a maiúsculas e minúsculas

O protocolo MessagePack é sensível a maiúsculas e minúsculas. Por exemplo, considere a seguinte classe C#:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Ao enviar do cliente JavaScript, `PascalCased` você deve usar nomes de propriedade, já que o invólucro deve corresponder exatamente à classe C#. Por exemplo:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

O `camelCased` uso de nomes não se liga corretamente à classe C#. Você pode contornar isso `Key` usando o atributo para especificar um nome diferente para a propriedade MessagePack. Para obter mais informações, consulte [a documentação MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind não é preservado ao serializar/desserializar

O protocolo MessagePack não fornece uma maneira `Kind` de `DateTime`codificar o valor de um . Como resultado, ao desserializar uma data, o MessagePack Hub Protocol `DateTime.Kind` `DateTimeKind.Local` será convertido para o formato UTC se o caso não tocar no tempo e passá-lo como está. Se você está `DateTime` trabalhando com valores, recomendamos converter-se para UTC antes de enviá-los. Converta-os de UTC para o horário local quando você recebê-los.

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue não é suportado pelo MessagePack no JavaScript

A biblioteca [msgpack5](https://github.com/mcollina/msgpack5) SignalR usada pelo cliente JavaScript `timestamp96` não suporta o tipo no MessagePack. Este tipo é usado para codificar valores de data muito grandes (seja muito cedo no passado ou muito longe no futuro). O valor `DateTime.MinValue` `January 1, 0001`é , que deve ser `timestamp96` codificado em um valor. Por causa disso, o envio `DateTime.MinValue` para um cliente JavaScript não é suportado. Quando `DateTime.MinValue` é recebido pelo cliente JavaScript, o seguinte erro é jogado:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Normalmente, `DateTime.MinValue` é usado para codificar `null` um "ausente" ou valor. Se você precisar codificar esse valor no MessagePack, use um `DateTime` valor anulado (`DateTime?`) ou codifique um valor separado `bool` indicando se a data está presente.

Para obter mais informações sobre essa limitação, consulte o problema do GitHub [aspnet/#2228SignalR](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Suporte ao MessagePack em ambiente de compilação "antecipada"

A [biblioteca MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) usada pelo cliente e servidor .NET usa a geração de código para otimizar a serialização. Como resultado, ele não é suportado por padrão em ambientes que usam compilação "antecipadamente" (como Xamarin iOS ou Unity). É possível usar o MessagePack nesses ambientes "pré-gerando" o código serializador/desserializador. Para obter mais informações, consulte [a documentação MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin). Depois de ter pré-gerado os serializadores, você pode `AddMessagePackProtocol`registrá-los usando o delegado de configuração passado para:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Verificações de tipo são mais rigorosas no MessagePack

O Protocolo Do Hub JSON realizará conversões de tipo durante a desserialização. Por exemplo, se o objeto de entrada tiver`{ foo: 42 }`um valor de propriedade que seja `string`um número ( ) mas a propriedade na classe .NET for do tipo, o valor será convertido. No entanto, o MessagePack não executa essa conversão e lançará uma exceção que pode ser vista em logs do lado do servidor (e no console):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Para obter mais informações sobre essa limitação, consulte o problema do GitHub [aspnet/#2937SignalR](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Recursos relacionados

* [Introdução](xref:tutorials/signalr)
* [.NET cliente](xref:signalr/dotnet-client)
* [Cliente JavaScript](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Este artigo assume que o leitor está familiarizado com os tópicos [abordados](xref:tutorials/signalr)em Get Started .

## <a name="what-is-messagepack"></a>O que é O Pacote de Mensagens?

[MessagePack](https://msgpack.org/index.html) é um formato de serialização binária rápido e compacto. É útil quando o desempenho e a largura de banda são uma preocupação porque cria mensagens menores em comparação com [json](https://www.json.org/). As mensagens binárias são ilegíveis ao olhar para os traços e logs de rede, a menos que os bytes sejam passados através de um analisador MessagePack. SignalRtem suporte integrado para o formato MessagePack e fornece APIs para o cliente e servidor usarem.

## <a name="configure-messagepack-on-the-server"></a>Configurar o MessagePack no servidor

Para ativar o Protocolo do MessagePack `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` hub no servidor, instale o pacote em seu aplicativo. No `Startup.ConfigureServices` método, `AddMessagePackProtocol` adicione `AddSignalR` à chamada para ativar o suporte ao MessagePack no servidor.

> [!NOTE]
> O JSON é habilitado por padrão. A adição do MessagePack permite o suporte para clientes JSON e MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Para personalizar como o MessagePack `AddMessagePackProtocol` formatará seus dados, é necessário um delegado para configurar opções. Nesse delegado, `FormatterResolvers` a propriedade pode ser usada para configurar opções de serialização do MessagePack. Para obter mais informações sobre como os resolvers funcionam, visite a biblioteca MessagePack no [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Os atributos podem ser usados nos objetos que você deseja serializar para definir como eles devem ser tratados.

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
> Recomendamos fortemente a revisão do [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e a aplicação dos patches recomendados. Por exemplo, `MessagePackSecurity.Active` definindo `MessagePackSecurity.UntrustedData`a propriedade estática para . A `MessagePackSecurity.Active` configuração da configuração requer a instalação manual de uma [versão 1.9.x do MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3). A `MessagePack` instalação do 1.9.x SignalR atualiza a versão que a versão usa. Quando `MessagePackSecurity.Active` não está `MessagePackSecurity.UntrustedData`definido para, um cliente mal-intencionado pode causar uma negação de serviço. Configurado, `MessagePackSecurity.Active` `Program.Main`conforme mostrado no código a seguir:

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>Configurar o MessagePack no cliente

> [!NOTE]
> O JSON é habilitado por padrão para os clientes suportados. Os clientes só podem suportar um único protocolo. A adição do suporte ao MessagePack substituirá quaisquer protocolos previamente configurados.

### <a name="net-client"></a>Cliente .NET

Para ativar o MessagePack no cliente `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, instale o pacote e ligue `AddMessagePackProtocol` `HubConnectionBuilder`.

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Esta `AddMessagePackProtocol` chamada leva um delegado para configurar opções como o servidor.

### <a name="javascript-client"></a>Cliente JavaScript

O suporte ao MessagePack para o [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) cliente JavaScript é fornecido pelo pacote npm. Instale o pacote executando o seguinte comando em um shell de comando:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Depois de instalar o pacote npm, o módulo pode ser usado diretamente através de um carregador de módulo JavaScript ou importado para o navegador fazendo referência ao seguinte arquivo:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

Em um navegador, a `msgpack5` biblioteca também deve ser referenciada. Use `<script>` uma tag para criar uma referência. A biblioteca pode ser encontrada em *node_modules\msgpack5\dist\msgpack5.js*.

> [!NOTE]
> Ao usar `<script>` o elemento, a ordem é importante. Se *signalr-protocol-msgpack.js* for referenciado antes *de msgpack5.js,* um erro ocorrerá ao tentar se conectar com o MessagePack. *signalr.js* também é necessário antes *do signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

A `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` adição à `HubConnectionBuilder` vontade configurará o cliente para usar o protocolo MessagePack ao se conectar a um servidor.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> No momento, não há opções de configuração para o protocolo MessagePack no cliente JavaScript.

## <a name="messagepack-quirks"></a>Peculiaridades do MessagePack

Existem alguns problemas a serem informados ao usar o Protocolo do MessagePack Hub.

### <a name="messagepack-is-case-sensitive"></a>MessagePack é sensível a maiúsculas e minúsculas

O protocolo MessagePack é sensível a maiúsculas e minúsculas. Por exemplo, considere a seguinte classe C#:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Ao enviar do cliente JavaScript, `PascalCased` você deve usar nomes de propriedade, já que o invólucro deve corresponder exatamente à classe C#. Por exemplo:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

O `camelCased` uso de nomes não se liga corretamente à classe C#. Você pode contornar isso `Key` usando o atributo para especificar um nome diferente para a propriedade MessagePack. Para obter mais informações, consulte [a documentação MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind não é preservado ao serializar/desserializar

O protocolo MessagePack não fornece uma maneira `Kind` de `DateTime`codificar o valor de um . Como resultado, ao desserializar uma data, o MessagePack Hub Protocol assume que a data de entrada está no formato UTC. Se você está `DateTime` trabalhando com valores em horário local, recomendamos converter para UTC antes de enviá-los. Converta-os de UTC para o horário local quando você recebê-los.

Para obter mais informações sobre essa limitação, consulte o problema do GitHub [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue não é suportado pelo MessagePack no JavaScript

A biblioteca [msgpack5](https://github.com/mcollina/msgpack5) SignalR usada pelo cliente JavaScript `timestamp96` não suporta o tipo no MessagePack. Este tipo é usado para codificar valores de data muito grandes (seja muito cedo no passado ou muito longe no futuro). O valor `DateTime.MinValue` `January 1, 0001`é , que deve ser `timestamp96` codificado em um valor. Por causa disso, o envio `DateTime.MinValue` para um cliente JavaScript não é suportado. Quando `DateTime.MinValue` é recebido pelo cliente JavaScript, o seguinte erro é jogado:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Normalmente, `DateTime.MinValue` é usado para codificar `null` um "ausente" ou valor. Se você precisar codificar esse valor no MessagePack, use um `DateTime` valor anulado (`DateTime?`) ou codifique um valor separado `bool` indicando se a data está presente.

Para obter mais informações sobre essa limitação, consulte o problema do GitHub [aspnet/#2228SignalR](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Suporte ao MessagePack em ambiente de compilação "antecipada"

A [biblioteca MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) usada pelo cliente e servidor .NET usa a geração de código para otimizar a serialização. Como resultado, ele não é suportado por padrão em ambientes que usam compilação "antecipadamente" (como Xamarin iOS ou Unity). É possível usar o MessagePack nesses ambientes "pré-gerando" o código serializador/desserializador. Para obter mais informações, consulte [a documentação MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). Depois de ter pré-gerado os serializadores, você pode `AddMessagePackProtocol`registrá-los usando o delegado de configuração passado para:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Verificações de tipo são mais rigorosas no MessagePack

O Protocolo Do Hub JSON realizará conversões de tipo durante a desserialização. Por exemplo, se o objeto de entrada tiver`{ foo: 42 }`um valor de propriedade que seja `string`um número ( ) mas a propriedade na classe .NET for do tipo, o valor será convertido. No entanto, o MessagePack não executa essa conversão e lançará uma exceção que pode ser vista em logs do lado do servidor (e no console):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Para obter mais informações sobre essa limitação, consulte o problema do GitHub [aspnet/#2937SignalR](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Recursos relacionados

* [Introdução](xref:tutorials/signalr)
* [.NET cliente](xref:signalr/dotnet-client)
* [Cliente JavaScript](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Este artigo assume que o leitor está familiarizado com os tópicos [abordados](xref:tutorials/signalr)em Get Started .

## <a name="what-is-messagepack"></a>O que é O Pacote de Mensagens?

[MessagePack](https://msgpack.org/index.html) é um formato de serialização binária rápido e compacto. É útil quando o desempenho e a largura de banda são uma preocupação porque cria mensagens menores em comparação com [json](https://www.json.org/). As mensagens binárias são ilegíveis ao olhar para os traços e logs de rede, a menos que os bytes sejam passados através de um analisador MessagePack. SignalRtem suporte integrado para o formato MessagePack e fornece APIs para o cliente e servidor usarem.

## <a name="configure-messagepack-on-the-server"></a>Configurar o MessagePack no servidor

Para ativar o Protocolo do MessagePack `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` hub no servidor, instale o pacote em seu aplicativo. No `Startup.ConfigureServices` método, `AddMessagePackProtocol` adicione `AddSignalR` à chamada para ativar o suporte ao MessagePack no servidor.

> [!NOTE]
> O JSON é habilitado por padrão. A adição do MessagePack permite o suporte para clientes JSON e MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Para personalizar como o MessagePack `AddMessagePackProtocol` formatará seus dados, é necessário um delegado para configurar opções. Nesse delegado, `FormatterResolvers` a propriedade pode ser usada para configurar opções de serialização do MessagePack. Para obter mais informações sobre como os resolvers funcionam, visite a biblioteca MessagePack no [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Os atributos podem ser usados nos objetos que você deseja serializar para definir como eles devem ser tratados.

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
> Recomendamos fortemente a revisão do [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e a aplicação dos patches recomendados. Por exemplo, `MessagePackSecurity.Active` definindo `MessagePackSecurity.UntrustedData`a propriedade estática para . A `MessagePackSecurity.Active` configuração da configuração requer a instalação manual de uma [versão 1.9.x do MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3). A `MessagePack` instalação do 1.9.x SignalR atualiza a versão que a versão usa. Quando `MessagePackSecurity.Active` não está `MessagePackSecurity.UntrustedData`definido para, um cliente mal-intencionado pode causar uma negação de serviço. Configurado, `MessagePackSecurity.Active` `Program.Main`conforme mostrado no código a seguir:

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>Configurar o MessagePack no cliente

> [!NOTE]
> O JSON é habilitado por padrão para os clientes suportados. Os clientes só podem suportar um único protocolo. A adição do suporte ao MessagePack substituirá quaisquer protocolos previamente configurados.

### <a name="net-client"></a>Cliente .NET

Para ativar o MessagePack no cliente `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, instale o pacote e ligue `AddMessagePackProtocol` `HubConnectionBuilder`.

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Esta `AddMessagePackProtocol` chamada leva um delegado para configurar opções como o servidor.

### <a name="javascript-client"></a>Cliente JavaScript

O suporte ao MessagePack para o [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) cliente JavaScript é fornecido pelo pacote npm. Instale o pacote executando o seguinte comando em um shell de comando:

```bash
npm install @aspnet/signalr-protocol-msgpack
```

Depois de instalar o pacote npm, o módulo pode ser usado diretamente através de um carregador de módulo JavaScript ou importado para o navegador fazendo referência ao seguinte arquivo:

*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*

Em um navegador, a `msgpack5` biblioteca também deve ser referenciada. Use `<script>` uma tag para criar uma referência. A biblioteca pode ser encontrada em *node_modules\msgpack5\dist\msgpack5.js*.

> [!NOTE]
> Ao usar `<script>` o elemento, a ordem é importante. Se *signalr-protocol-msgpack.js* for referenciado antes *de msgpack5.js,* um erro ocorrerá ao tentar se conectar com o MessagePack. *signalr.js* também é necessário antes *do signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

A `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` adição à `HubConnectionBuilder` vontade configurará o cliente para usar o protocolo MessagePack ao se conectar a um servidor.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> No momento, não há opções de configuração para o protocolo MessagePack no cliente JavaScript.

## <a name="messagepack-quirks"></a>Peculiaridades do MessagePack

Existem alguns problemas a serem informados ao usar o Protocolo do MessagePack Hub.

### <a name="messagepack-is-case-sensitive"></a>MessagePack é sensível a maiúsculas e minúsculas

O protocolo MessagePack é sensível a maiúsculas e minúsculas. Por exemplo, considere a seguinte classe C#:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Ao enviar do cliente JavaScript, `PascalCased` você deve usar nomes de propriedade, já que o invólucro deve corresponder exatamente à classe C#. Por exemplo:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

O `camelCased` uso de nomes não se liga corretamente à classe C#. Você pode contornar isso `Key` usando o atributo para especificar um nome diferente para a propriedade MessagePack. Para obter mais informações, consulte [a documentação MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind não é preservado ao serializar/desserializar

O protocolo MessagePack não fornece uma maneira `Kind` de `DateTime`codificar o valor de um . Como resultado, ao desserializar uma data, o MessagePack Hub Protocol assume que a data de entrada está no formato UTC. Se você está `DateTime` trabalhando com valores em horário local, recomendamos converter para UTC antes de enviá-los. Converta-os de UTC para o horário local quando você recebê-los.

Para obter mais informações sobre essa limitação, consulte o problema do GitHub [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue não é suportado pelo MessagePack no JavaScript

A biblioteca [msgpack5](https://github.com/mcollina/msgpack5) SignalR usada pelo cliente JavaScript `timestamp96` não suporta o tipo no MessagePack. Este tipo é usado para codificar valores de data muito grandes (seja muito cedo no passado ou muito longe no futuro). O valor `DateTime.MinValue` `January 1, 0001` é que deve ser `timestamp96` codificado em um valor. Por causa disso, o envio `DateTime.MinValue` para um cliente JavaScript não é suportado. Quando `DateTime.MinValue` é recebido pelo cliente JavaScript, o seguinte erro é jogado:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Normalmente, `DateTime.MinValue` é usado para codificar `null` um "ausente" ou valor. Se você precisar codificar esse valor no MessagePack, use um `DateTime` valor anulado (`DateTime?`) ou codifique um valor separado `bool` indicando se a data está presente.

Para obter mais informações sobre essa limitação, consulte o problema do GitHub [aspnet/#2228SignalR](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Suporte ao MessagePack em ambiente de compilação "antecipada"

A [biblioteca MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) usada pelo cliente e servidor .NET usa a geração de código para otimizar a serialização. Como resultado, ele não é suportado por padrão em ambientes que usam compilação "antecipadamente" (como Xamarin iOS ou Unity). É possível usar o MessagePack nesses ambientes "pré-gerando" o código serializador/desserializador. Para obter mais informações, consulte [a documentação MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). Depois de ter pré-gerado os serializadores, você pode `AddMessagePackProtocol`registrá-los usando o delegado de configuração passado para:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Verificações de tipo são mais rigorosas no MessagePack

O Protocolo Do Hub JSON realizará conversões de tipo durante a desserialização. Por exemplo, se o objeto de entrada tiver`{ foo: 42 }`um valor de propriedade que seja `string`um número ( ) mas a propriedade na classe .NET for do tipo, o valor será convertido. No entanto, o MessagePack não executa essa conversão e lançará uma exceção que pode ser vista em logs do lado do servidor (e no console):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Para obter mais informações sobre essa limitação, consulte o problema do GitHub [aspnet/#2937SignalR](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Recursos relacionados

* [Introdução](xref:tutorials/signalr)
* [.NET cliente](xref:signalr/dotnet-client)
* [Cliente JavaScript](xref:signalr/javascript-client)

::: moniker-end
