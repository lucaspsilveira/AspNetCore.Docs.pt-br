---
title: Registro e diagnóstico em gRPC em .NET
author: jamesnk
description: Saiba como coletar diagnósticos do seu aplicativo gRPC no .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
uid: grpc/diagnostics
ms.openlocfilehash: 131144bf7a2c637eb2c1a1d5c54990dd4d429502
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417516"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a>Registro e diagnóstico em gRPC em .NET

Por [James Newton-King](https://twitter.com/jamesnk)

Este artigo fornece orientações para coletar diagnósticos de um aplicativo gRPC para ajudar a solucionar problemas. Os tópicos abordados incluem:

* **Registro** - Registros estruturados gravados no [registro do .NET Core](xref:fundamentals/logging/index). <xref:Microsoft.Extensions.Logging.ILogger>é usado por frameworks de aplicativos para gravar logs e por usuários para seu próprio login em um aplicativo.
* **Rastreamento** - Eventos relacionados a `DiaganosticSource` uma `Activity`operação escrita usando e . Traços da fonte de diagnóstico são comumente usados para coletar telemetria de aplicativos por bibliotecas como [Insights de Aplicativos](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) e [OpenTelemetria](https://github.com/open-telemetry/opentelemetry-dotnet).
* **Métricas** - Representação de medidas de dados em intervalos de tempo, por exemplo, solicitações por segundo. As métricas são `EventCounter` emitidas usando e podem ser observadas usando a ferramenta de linha de comando [dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) ou com [o Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).

## <a name="logging"></a>Registro em log

os serviços gRPC e os registros de gravação do cliente gRPC usando [o registro do .NET Core](xref:fundamentals/logging/index). Os logs são um bom lugar para começar quando você precisa depurar comportamentos inesperados em seus aplicativos.

### <a name="grpc-services-logging"></a>registro de serviços gRPC

> [!WARNING]
> Os logs do lado do servidor podem conter informações confidenciais do seu aplicativo. **Nunca** publique logs brutos de aplicativos de produção para fóruns públicos como o GitHub.

Como os serviços gRPC estão hospedados no ASP.NET Core, ele usa o sistema de registro ASP.NET Core. Na configuração padrão, o gRPC registra muito pouca informação, mas isso pode configurar. Consulte a documentação sobre [ASP.NET registro do Core](xref:fundamentals/logging/index#configuration) para obter detalhes sobre a configuração do registro do ASP.NET Core.

gRPC adiciona logs `Grpc` na categoria. Para habilitar logs detalhados do `Grpc` gRPC, `Debug` configure os prefixos para o nível em seu arquivo `LogLevel` *appsettings.json* adicionando os seguintes itens à subseção em `Logging`:

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

Você também pode configurá-lo em *Startup.cs* com: `ConfigureLogging`

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

Se você não estiver usando a configuração baseada em JSON, defina o seguinte valor de configuração em seu sistema de configuração:

* `Logging:LogLevel:Grpc` = `Debug`

Verifique a documentação do seu sistema de configuração para determinar como especificar valores de configuração aninhados. Por exemplo, ao usar variáveis `_` de ambiente, `:` dois caracteres `Logging__LogLevel__Grpc`são usados em vez do (por exemplo, ).

Recomendamos usar `Debug` o nível ao coletar diagnósticos mais detalhados para o seu aplicativo. O `Trace` nível produz diagnósticos de baixo nível e raramente é necessário para diagnosticar problemas em seu aplicativo.

#### <a name="sample-logging-output"></a>Exemplo de saída de registro em log

Aqui está um exemplo de `Debug` saída do console ao nível de um serviço gRPC:

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
dbug: Grpc.AspNetCore.Server.ServerCallHandler[1]
      Reading message.
info: GrpcService.GreeterService[0]
      Hello World
dbug: Grpc.AspNetCore.Server.ServerCallHandler[6]
      Sending message.
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.4113ms 200 application/grpc
```

### <a name="access-server-side-logs"></a>Acesse os logs do lado do servidor

A forma como você acessa os logs do lado do servidor depende do ambiente em que você está executando.

#### <a name="as-a-console-app"></a>Como um aplicativo de console

Se você estiver executando em um aplicativo de console, o [logger console](xref:fundamentals/logging/index#console-provider) deve ser ativado por padrão. Os logs gRPC aparecerão no console.

#### <a name="other-environments"></a>Outros ambientes

Se o aplicativo for implantado em outro ambiente (por exemplo, Docker, <xref:fundamentals/logging/index> Kubernetes ou Windows Service), consulte mais informações sobre como configurar provedores de registro adequados para o ambiente.

### <a name="grpc-client-logging"></a>registro de cliente gRPC

> [!WARNING]
> Os registros do lado do cliente podem conter informações confidenciais do seu aplicativo. **Nunca** publique logs brutos de aplicativos de produção para fóruns públicos como o GitHub.

Para obter logs do cliente .NET, `GrpcChannelOptions.LoggerFactory` você pode definir a propriedade quando o canal do cliente for criado. Se você está chamando um serviço gRPC de um aplicativo ASP.NET Core, então a fábrica de logger pode ser resolvida a partir de injeção de dependência (DI):

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

Uma maneira alternativa de habilitar o registro do cliente é usar a [fábrica de clientes gRPC](xref:grpc/clientfactory) para criar o cliente. Um cliente gRPC registrado na fábrica do cliente e resolvido a partir de DI usará automaticamente o registro configurado do aplicativo.

Se o seu aplicativo não estiver usando `ILoggerFactory` DI, então você pode criar uma nova instância com [loggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*). Para acessar este método, adicione o pacote [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) ao seu aplicativo.

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a>escopos de registro de clientes gRPC

O cliente gRPC adiciona um [escopo de registro](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes) aos logs feitos durante uma chamada gRPC. O escopo tem metadados relacionados à chamada gRPC:

* **GrpcMethodType** - O tipo de método gRPC. Os valores `Grpc.Core.MethodType` possíveis são nomes do enum, por exemplo, Unary
* **GrpcUri** - O URI relativo do método gRPC, por exemplo/greet. Greeter/SayHellos

#### <a name="sample-logging-output"></a>Exemplo de saída de registro em log

Aqui está um exemplo de `Debug` saída do console ao nível de um cliente gRPC:

```console
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Starting gRPC call. Method type: 'Unary', URI: 'https://localhost:5001/Greet.Greeter/SayHello'.
dbug: Grpc.Net.Client.Internal.GrpcCall[6]
      Sending message.
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Reading message.
dbug: Grpc.Net.Client.Internal.GrpcCall[4]
      Finished gRPC call.
```

## <a name="tracing"></a>Rastreamento

Os serviços gRPC e o cliente gRPC fornecem informações sobre chamadas gRPC usando [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) e [Activity](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity).

* .NET gRPC usa uma atividade para representar uma chamada gRPC.
* Os eventos de rastreamento são escritos na fonte de diagnóstico no início e parada da atividade de chamada gRPC.
* O rastreamento não captura informações sobre quando as mensagens são enviadas ao longo da vida das chamadas de streaming gRPC.

### <a name="grpc-service-tracing"></a>rastreamento de serviço gRPC

Os serviços gRPC estão hospedados no ASP.NET Core, que relata eventos sobre solicitações HTTP recebidas. Metadados específicos do gRPC são adicionados aos diagnósticos de solicitação HTTP existentes que ASP.NET Core fornece.

* O nome `Microsoft.AspNetCore`da fonte de diagnóstico é .
* O nome `Microsoft.AspNetCore.Hosting.HttpRequestIn`da atividade é .
  * O nome do método gRPC invocado pela chamada gRPC `grpc.method`é adicionado como uma tag com o nome .
  * O código de status da chamada gRPC quando ela `grpc.status_code`estiver concluída é adicionado como uma tag com o nome .

### <a name="grpc-client-tracing"></a>rastreamento do cliente gRPC

O cliente .NET gRPC usa `HttpClient` para fazer chamadas gRPC. Embora `HttpClient` escreva eventos diagnósticos, o cliente .NET gRPC fornece uma fonte de diagnóstico personalizada, atividade e eventos para que informações completas sobre uma chamada gRPC possam ser coletadas.

* O nome `Grpc.Net.Client`da fonte de diagnóstico é .
* O nome `Grpc.Net.Client.GrpcOut`da atividade é .
  * O nome do método gRPC invocado pela chamada gRPC `grpc.method`é adicionado como uma tag com o nome .
  * O código de status da chamada gRPC quando ela `grpc.status_code`estiver concluída é adicionado como uma tag com o nome .

### <a name="collecting-tracing"></a>Coleta de rastreamento

A maneira mais `DiagnosticSource` fácil de usar é configurar uma biblioteca de telemetria, como [Insights de Aplicativos](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) ou [OpenTelemettria](https://github.com/open-telemetry/opentelemetry-dotnet) em seu aplicativo. A biblioteca processará informações sobre chamadas gRPC junto com outra telemetria de aplicativos.

O rastreamento pode ser visualizado em um serviço gerenciado como o Application Insights, ou você pode optar por executar seu próprio sistema de rastreamento distribuído. OpenTelemettry suporta a exportação de dados de rastreamento para [Jaeger](https://www.jaegertracing.io/) e [Zipkin](https://zipkin.io/).

`DiagnosticSource`pode consumir eventos de `DiagnosticListener`rastreamento em código usando . Para obter informações sobre como ouvir uma fonte de diagnóstico com código, consulte o [guia do usuário DiagnosticSource](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).

> [!NOTE]
> Bibliotecas de telemetria não capturam telemetria específica `Grpc.Net.Client.GrpcOut` gRPC atualmente. O trabalho para melhorar as bibliotecas de telemetria que capturam esse rastreamento está em andamento.

## <a name="metrics"></a>Métricas

Métricas é uma representação de medidas de dados sobre intervalos de tempo, por exemplo, solicitações por segundo. Os dados métricos permitem a observação do estado de um aplicativo em alto nível. As métricas .NET gRPC `EventCounter`são emitidas usando .

### <a name="grpc-service-metrics"></a>métricas de serviço gRPC

as métricas do servidor `Grpc.AspNetCore.Server` gRPC são relatadas na fonte do evento.

| Nome                      | Descrição                   |
| --------------------------|-------------------------------|
| `total-calls`             | Total de Chamadas                   |
| `current-calls`           | Chamadas atuais                 |
| `calls-failed`            | Falha total das chamadas            |
| `calls-deadline-exceeded` | Prazo total de chamadas excedido |
| `messages-sent`           | Total de Mensagens Enviadas           |
| `messages-received`       | Total de mensagens recebidas       |
| `calls-unimplemented`     | Total de chamadas não implementadas     |

ASP.NET Core também fornece suas `Microsoft.AspNetCore.Hosting` próprias métricas sobre a origem do evento.

### <a name="grpc-client-metrics"></a>métricas do cliente gRPC

as métricas do cliente `Grpc.Net.Client` gRPC são relatadas na fonte do evento.

| Nome                      | Descrição                   |
| --------------------------|-------------------------------|
| `total-calls`             | Total de Chamadas                   |
| `current-calls`           | Chamadas atuais                 |
| `calls-failed`            | Falha total das chamadas            |
| `calls-deadline-exceeded` | Prazo total de chamadas excedido |
| `messages-sent`           | Total de Mensagens Enviadas           |
| `messages-received`       | Total de mensagens recebidas       |

### <a name="observe-metrics"></a>Observar métricas

[dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) é uma ferramenta de monitoramento de desempenho para monitoramento de saúde ad-hoc e investigação de desempenho de primeiro nível. Monitore um aplicativo `Grpc.AspNetCore.Server` .NET com ou `Grpc.Net.Client` como o nome do provedor.

```console
> dotnet-counters monitor --process-id 1902 Grpc.AspNetCore.Server

Press p to pause, r to resume, q to quit.
    Status: Running
[Grpc.AspNetCore.Server]
    Total Calls                                 300
    Current Calls                               5
    Total Calls Failed                          0
    Total Calls Deadline Exceeded               0
    Total Messages Sent                         295
    Total Messages Received                     300
    Total Calls Unimplemented                   0
```

Outra maneira de observar as métricas gRPC é capturar dados de contador usando o [pacote Microsoft.ApplicationInsights.EventCounterCollector](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters)do Application Insights. Uma vez configurada, o Application Insights coleta contadores .NET comuns em tempo de execução. Os contadores do gRPC não são coletados por padrão, mas o App Insights pode ser [personalizado para incluir contadores adicionais](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).

Especifique os contadores gRPC para o Application Insight coletar em *Startup.cs*:

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // Configure App Insights to collect gRPC counters gRPC services hosted in an ASP.NET Core app
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "current-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "total-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "calls-failed"));
            }
        );
    }
```

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
