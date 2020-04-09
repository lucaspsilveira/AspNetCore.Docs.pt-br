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
# <a name="logging-and-diagnostics-in-grpc-on-net"></a><span data-ttu-id="148aa-103">Registro e diagnóstico em gRPC em .NET</span><span class="sxs-lookup"><span data-stu-id="148aa-103">Logging and diagnostics in gRPC on .NET</span></span>

<span data-ttu-id="148aa-104">Por [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="148aa-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="148aa-105">Este artigo fornece orientações para coletar diagnósticos de um aplicativo gRPC para ajudar a solucionar problemas.</span><span class="sxs-lookup"><span data-stu-id="148aa-105">This article provides guidance for gathering diagnostics from a gRPC app to help troubleshoot issues.</span></span> <span data-ttu-id="148aa-106">Os tópicos abordados incluem:</span><span class="sxs-lookup"><span data-stu-id="148aa-106">Topics covered include:</span></span>

* <span data-ttu-id="148aa-107">**Registro** - Registros estruturados gravados no [registro do .NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="148aa-107">**Logging** - Structured logs written to [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="148aa-108"><xref:Microsoft.Extensions.Logging.ILogger>é usado por frameworks de aplicativos para gravar logs e por usuários para seu próprio login em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="148aa-108"><xref:Microsoft.Extensions.Logging.ILogger> is used by app frameworks to write logs, and by users for their own logging in an app.</span></span>
* <span data-ttu-id="148aa-109">**Rastreamento** - Eventos relacionados a `DiaganosticSource` uma `Activity`operação escrita usando e .</span><span class="sxs-lookup"><span data-stu-id="148aa-109">**Tracing** - Events related to an operation written using `DiaganosticSource` and `Activity`.</span></span> <span data-ttu-id="148aa-110">Traços da fonte de diagnóstico são comumente usados para coletar telemetria de aplicativos por bibliotecas como [Insights de Aplicativos](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) e [OpenTelemetria](https://github.com/open-telemetry/opentelemetry-dotnet).</span><span class="sxs-lookup"><span data-stu-id="148aa-110">Traces from diagnostic source are commonly used to collect app telemetry by libraries such as [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) and [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span></span>
* <span data-ttu-id="148aa-111">**Métricas** - Representação de medidas de dados em intervalos de tempo, por exemplo, solicitações por segundo.</span><span class="sxs-lookup"><span data-stu-id="148aa-111">**Metrics** - Representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="148aa-112">As métricas são `EventCounter` emitidas usando e podem ser observadas usando a ferramenta de linha de comando [dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) ou com [o Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span><span class="sxs-lookup"><span data-stu-id="148aa-112">Metrics are emitted using `EventCounter` and can be observed using [dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) command line tool or with [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span></span>

## <a name="logging"></a><span data-ttu-id="148aa-113">Registro em log</span><span class="sxs-lookup"><span data-stu-id="148aa-113">Logging</span></span>

<span data-ttu-id="148aa-114">os serviços gRPC e os registros de gravação do cliente gRPC usando [o registro do .NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="148aa-114">gRPC services and the gRPC client write logs using [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="148aa-115">Os logs são um bom lugar para começar quando você precisa depurar comportamentos inesperados em seus aplicativos.</span><span class="sxs-lookup"><span data-stu-id="148aa-115">Logs are a good place to start when you need to debug unexpected behavior in your apps.</span></span>

### <a name="grpc-services-logging"></a><span data-ttu-id="148aa-116">registro de serviços gRPC</span><span class="sxs-lookup"><span data-stu-id="148aa-116">gRPC services logging</span></span>

> [!WARNING]
> <span data-ttu-id="148aa-117">Os logs do lado do servidor podem conter informações confidenciais do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="148aa-117">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="148aa-118">**Nunca** publique logs brutos de aplicativos de produção para fóruns públicos como o GitHub.</span><span class="sxs-lookup"><span data-stu-id="148aa-118">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="148aa-119">Como os serviços gRPC estão hospedados no ASP.NET Core, ele usa o sistema de registro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="148aa-119">Since gRPC services are hosted on ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="148aa-120">Na configuração padrão, o gRPC registra muito pouca informação, mas isso pode configurar.</span><span class="sxs-lookup"><span data-stu-id="148aa-120">In the default configuration, gRPC logs very little information, but this can configured.</span></span> <span data-ttu-id="148aa-121">Consulte a documentação sobre [ASP.NET registro do Core](xref:fundamentals/logging/index#configuration) para obter detalhes sobre a configuração do registro do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="148aa-121">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="148aa-122">gRPC adiciona logs `Grpc` na categoria.</span><span class="sxs-lookup"><span data-stu-id="148aa-122">gRPC adds logs under the `Grpc` category.</span></span> <span data-ttu-id="148aa-123">Para habilitar logs detalhados do `Grpc` gRPC, `Debug` configure os prefixos para o nível em seu arquivo `LogLevel` *appsettings.json* adicionando os seguintes itens à subseção em `Logging`:</span><span class="sxs-lookup"><span data-stu-id="148aa-123">To enable detailed logs from gRPC, configure the `Grpc` prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

<span data-ttu-id="148aa-124">Você também pode configurá-lo em *Startup.cs* com: `ConfigureLogging`</span><span class="sxs-lookup"><span data-stu-id="148aa-124">You can also configure this in *Startup.cs* with `ConfigureLogging`:</span></span>

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

<span data-ttu-id="148aa-125">Se você não estiver usando a configuração baseada em JSON, defina o seguinte valor de configuração em seu sistema de configuração:</span><span class="sxs-lookup"><span data-stu-id="148aa-125">If you aren't using JSON-based configuration, set the following configuration value in your configuration system:</span></span>

* `Logging:LogLevel:Grpc` = `Debug`

<span data-ttu-id="148aa-126">Verifique a documentação do seu sistema de configuração para determinar como especificar valores de configuração aninhados.</span><span class="sxs-lookup"><span data-stu-id="148aa-126">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="148aa-127">Por exemplo, ao usar variáveis `_` de ambiente, `:` dois caracteres `Logging__LogLevel__Grpc`são usados em vez do (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="148aa-127">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Grpc`).</span></span>

<span data-ttu-id="148aa-128">Recomendamos usar `Debug` o nível ao coletar diagnósticos mais detalhados para o seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="148aa-128">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="148aa-129">O `Trace` nível produz diagnósticos de baixo nível e raramente é necessário para diagnosticar problemas em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="148aa-129">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="148aa-130">Exemplo de saída de registro em log</span><span class="sxs-lookup"><span data-stu-id="148aa-130">Sample logging output</span></span>

<span data-ttu-id="148aa-131">Aqui está um exemplo de `Debug` saída do console ao nível de um serviço gRPC:</span><span class="sxs-lookup"><span data-stu-id="148aa-131">Here is an example of console output at the `Debug` level of a gRPC service:</span></span>

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

### <a name="access-server-side-logs"></a><span data-ttu-id="148aa-132">Acesse os logs do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="148aa-132">Access server-side logs</span></span>

<span data-ttu-id="148aa-133">A forma como você acessa os logs do lado do servidor depende do ambiente em que você está executando.</span><span class="sxs-lookup"><span data-stu-id="148aa-133">How you access server-side logs depends on the environment in which you're running.</span></span>

#### <a name="as-a-console-app"></a><span data-ttu-id="148aa-134">Como um aplicativo de console</span><span class="sxs-lookup"><span data-stu-id="148aa-134">As a console app</span></span>

<span data-ttu-id="148aa-135">Se você estiver executando em um aplicativo de console, o [logger console](xref:fundamentals/logging/index#console-provider) deve ser ativado por padrão.</span><span class="sxs-lookup"><span data-stu-id="148aa-135">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console-provider) should be enabled by default.</span></span> <span data-ttu-id="148aa-136">Os logs gRPC aparecerão no console.</span><span class="sxs-lookup"><span data-stu-id="148aa-136">gRPC logs will appear in the console.</span></span>

#### <a name="other-environments"></a><span data-ttu-id="148aa-137">Outros ambientes</span><span class="sxs-lookup"><span data-stu-id="148aa-137">Other environments</span></span>

<span data-ttu-id="148aa-138">Se o aplicativo for implantado em outro ambiente (por exemplo, Docker, <xref:fundamentals/logging/index> Kubernetes ou Windows Service), consulte mais informações sobre como configurar provedores de registro adequados para o ambiente.</span><span class="sxs-lookup"><span data-stu-id="148aa-138">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

### <a name="grpc-client-logging"></a><span data-ttu-id="148aa-139">registro de cliente gRPC</span><span class="sxs-lookup"><span data-stu-id="148aa-139">gRPC client logging</span></span>

> [!WARNING]
> <span data-ttu-id="148aa-140">Os registros do lado do cliente podem conter informações confidenciais do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="148aa-140">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="148aa-141">**Nunca** publique logs brutos de aplicativos de produção para fóruns públicos como o GitHub.</span><span class="sxs-lookup"><span data-stu-id="148aa-141">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="148aa-142">Para obter logs do cliente .NET, `GrpcChannelOptions.LoggerFactory` você pode definir a propriedade quando o canal do cliente for criado.</span><span class="sxs-lookup"><span data-stu-id="148aa-142">To get logs from the .NET client, you can set the `GrpcChannelOptions.LoggerFactory` property when the client's channel is created.</span></span> <span data-ttu-id="148aa-143">Se você está chamando um serviço gRPC de um aplicativo ASP.NET Core, então a fábrica de logger pode ser resolvida a partir de injeção de dependência (DI):</span><span class="sxs-lookup"><span data-stu-id="148aa-143">If you are calling a gRPC service from an ASP.NET Core app then the logger factory can be resolved from dependency injection (DI):</span></span>

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

<span data-ttu-id="148aa-144">Uma maneira alternativa de habilitar o registro do cliente é usar a [fábrica de clientes gRPC](xref:grpc/clientfactory) para criar o cliente.</span><span class="sxs-lookup"><span data-stu-id="148aa-144">An alternative way to enable client logging is to use the [gRPC client factory](xref:grpc/clientfactory) to create the client.</span></span> <span data-ttu-id="148aa-145">Um cliente gRPC registrado na fábrica do cliente e resolvido a partir de DI usará automaticamente o registro configurado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="148aa-145">A gRPC client registered with the client factory and resolved from DI will automatically use the app's configured logging.</span></span>

<span data-ttu-id="148aa-146">Se o seu aplicativo não estiver usando `ILoggerFactory` DI, então você pode criar uma nova instância com [loggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span><span class="sxs-lookup"><span data-stu-id="148aa-146">If your app isn't using DI then you can create a new `ILoggerFactory` instance with [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span></span> <span data-ttu-id="148aa-147">Para acessar este método, adicione o pacote [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) ao seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="148aa-147">To access this method add the [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) package to your app.</span></span>

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a><span data-ttu-id="148aa-148">escopos de registro de clientes gRPC</span><span class="sxs-lookup"><span data-stu-id="148aa-148">gRPC client log scopes</span></span>

<span data-ttu-id="148aa-149">O cliente gRPC adiciona um [escopo de registro](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes) aos logs feitos durante uma chamada gRPC.</span><span class="sxs-lookup"><span data-stu-id="148aa-149">The gRPC client adds a [logging scope](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes) to logs made during a gRPC call.</span></span> <span data-ttu-id="148aa-150">O escopo tem metadados relacionados à chamada gRPC:</span><span class="sxs-lookup"><span data-stu-id="148aa-150">The scope has metadata related to the gRPC call:</span></span>

* <span data-ttu-id="148aa-151">**GrpcMethodType** - O tipo de método gRPC.</span><span class="sxs-lookup"><span data-stu-id="148aa-151">**GrpcMethodType** - The gRPC method type.</span></span> <span data-ttu-id="148aa-152">Os valores `Grpc.Core.MethodType` possíveis são nomes do enum, por exemplo, Unary</span><span class="sxs-lookup"><span data-stu-id="148aa-152">Possible values are names from `Grpc.Core.MethodType` enum, e.g. Unary</span></span>
* <span data-ttu-id="148aa-153">**GrpcUri** - O URI relativo do método gRPC, por exemplo/greet. Greeter/SayHellos</span><span class="sxs-lookup"><span data-stu-id="148aa-153">**GrpcUri** - The relative URI of the gRPC method, e.g. /greet.Greeter/SayHellos</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="148aa-154">Exemplo de saída de registro em log</span><span class="sxs-lookup"><span data-stu-id="148aa-154">Sample logging output</span></span>

<span data-ttu-id="148aa-155">Aqui está um exemplo de `Debug` saída do console ao nível de um cliente gRPC:</span><span class="sxs-lookup"><span data-stu-id="148aa-155">Here is an example of console output at the `Debug` level of a gRPC client:</span></span>

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

## <a name="tracing"></a><span data-ttu-id="148aa-156">Rastreamento</span><span class="sxs-lookup"><span data-stu-id="148aa-156">Tracing</span></span>

<span data-ttu-id="148aa-157">Os serviços gRPC e o cliente gRPC fornecem informações sobre chamadas gRPC usando [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) e [Activity](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity).</span><span class="sxs-lookup"><span data-stu-id="148aa-157">gRPC services and the gRPC client provide information about gRPC calls using [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) and [Activity](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity).</span></span>

* <span data-ttu-id="148aa-158">.NET gRPC usa uma atividade para representar uma chamada gRPC.</span><span class="sxs-lookup"><span data-stu-id="148aa-158">.NET gRPC uses an activity to represent a gRPC call.</span></span>
* <span data-ttu-id="148aa-159">Os eventos de rastreamento são escritos na fonte de diagnóstico no início e parada da atividade de chamada gRPC.</span><span class="sxs-lookup"><span data-stu-id="148aa-159">Tracing events are written to the diagnostic source at the start and stop of the gRPC call activity.</span></span>
* <span data-ttu-id="148aa-160">O rastreamento não captura informações sobre quando as mensagens são enviadas ao longo da vida das chamadas de streaming gRPC.</span><span class="sxs-lookup"><span data-stu-id="148aa-160">Tracing doesn't capture information about when messages are sent over the lifetime of gRPC streaming calls.</span></span>

### <a name="grpc-service-tracing"></a><span data-ttu-id="148aa-161">rastreamento de serviço gRPC</span><span class="sxs-lookup"><span data-stu-id="148aa-161">gRPC service tracing</span></span>

<span data-ttu-id="148aa-162">Os serviços gRPC estão hospedados no ASP.NET Core, que relata eventos sobre solicitações HTTP recebidas.</span><span class="sxs-lookup"><span data-stu-id="148aa-162">gRPC services are hosted on ASP.NET Core which reports events about incoming HTTP requests.</span></span> <span data-ttu-id="148aa-163">Metadados específicos do gRPC são adicionados aos diagnósticos de solicitação HTTP existentes que ASP.NET Core fornece.</span><span class="sxs-lookup"><span data-stu-id="148aa-163">gRPC specific metadata is added to the existing HTTP request diagnostics that ASP.NET Core provides.</span></span>

* <span data-ttu-id="148aa-164">O nome `Microsoft.AspNetCore`da fonte de diagnóstico é .</span><span class="sxs-lookup"><span data-stu-id="148aa-164">Diagnostic source name is `Microsoft.AspNetCore`.</span></span>
* <span data-ttu-id="148aa-165">O nome `Microsoft.AspNetCore.Hosting.HttpRequestIn`da atividade é .</span><span class="sxs-lookup"><span data-stu-id="148aa-165">Activity name is `Microsoft.AspNetCore.Hosting.HttpRequestIn`.</span></span>
  * <span data-ttu-id="148aa-166">O nome do método gRPC invocado pela chamada gRPC `grpc.method`é adicionado como uma tag com o nome .</span><span class="sxs-lookup"><span data-stu-id="148aa-166">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="148aa-167">O código de status da chamada gRPC quando ela `grpc.status_code`estiver concluída é adicionado como uma tag com o nome .</span><span class="sxs-lookup"><span data-stu-id="148aa-167">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="grpc-client-tracing"></a><span data-ttu-id="148aa-168">rastreamento do cliente gRPC</span><span class="sxs-lookup"><span data-stu-id="148aa-168">gRPC client tracing</span></span>

<span data-ttu-id="148aa-169">O cliente .NET gRPC usa `HttpClient` para fazer chamadas gRPC.</span><span class="sxs-lookup"><span data-stu-id="148aa-169">The .NET gRPC client uses `HttpClient` to make gRPC calls.</span></span> <span data-ttu-id="148aa-170">Embora `HttpClient` escreva eventos diagnósticos, o cliente .NET gRPC fornece uma fonte de diagnóstico personalizada, atividade e eventos para que informações completas sobre uma chamada gRPC possam ser coletadas.</span><span class="sxs-lookup"><span data-stu-id="148aa-170">Although `HttpClient` writes diagnostic events, the .NET gRPC client provides a custom diagnostic source, activity and events so that complete information about a gRPC call can be collected.</span></span>

* <span data-ttu-id="148aa-171">O nome `Grpc.Net.Client`da fonte de diagnóstico é .</span><span class="sxs-lookup"><span data-stu-id="148aa-171">Diagnostic source name is `Grpc.Net.Client`.</span></span>
* <span data-ttu-id="148aa-172">O nome `Grpc.Net.Client.GrpcOut`da atividade é .</span><span class="sxs-lookup"><span data-stu-id="148aa-172">Activity name is `Grpc.Net.Client.GrpcOut`.</span></span>
  * <span data-ttu-id="148aa-173">O nome do método gRPC invocado pela chamada gRPC `grpc.method`é adicionado como uma tag com o nome .</span><span class="sxs-lookup"><span data-stu-id="148aa-173">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="148aa-174">O código de status da chamada gRPC quando ela `grpc.status_code`estiver concluída é adicionado como uma tag com o nome .</span><span class="sxs-lookup"><span data-stu-id="148aa-174">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="collecting-tracing"></a><span data-ttu-id="148aa-175">Coleta de rastreamento</span><span class="sxs-lookup"><span data-stu-id="148aa-175">Collecting tracing</span></span>

<span data-ttu-id="148aa-176">A maneira mais `DiagnosticSource` fácil de usar é configurar uma biblioteca de telemetria, como [Insights de Aplicativos](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) ou [OpenTelemettria](https://github.com/open-telemetry/opentelemetry-dotnet) em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="148aa-176">The easiest way to use `DiagnosticSource` is to configure a telemetry library such as [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) or [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) in your app.</span></span> <span data-ttu-id="148aa-177">A biblioteca processará informações sobre chamadas gRPC junto com outra telemetria de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="148aa-177">The library will process information about gRPC calls along-side other app telemetry.</span></span>

<span data-ttu-id="148aa-178">O rastreamento pode ser visualizado em um serviço gerenciado como o Application Insights, ou você pode optar por executar seu próprio sistema de rastreamento distribuído.</span><span class="sxs-lookup"><span data-stu-id="148aa-178">Tracing can be viewed in a managed service like Application Insights, or you can choose to run your own distributed tracing system.</span></span> <span data-ttu-id="148aa-179">OpenTelemettry suporta a exportação de dados de rastreamento para [Jaeger](https://www.jaegertracing.io/) e [Zipkin](https://zipkin.io/).</span><span class="sxs-lookup"><span data-stu-id="148aa-179">OpenTelemetry supports exporting tracing data to [Jaeger](https://www.jaegertracing.io/) and [Zipkin](https://zipkin.io/).</span></span>

<span data-ttu-id="148aa-180">`DiagnosticSource`pode consumir eventos de `DiagnosticListener`rastreamento em código usando .</span><span class="sxs-lookup"><span data-stu-id="148aa-180">`DiagnosticSource` can consume tracing events in code using `DiagnosticListener`.</span></span> <span data-ttu-id="148aa-181">Para obter informações sobre como ouvir uma fonte de diagnóstico com código, consulte o [guia do usuário DiagnosticSource](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span><span class="sxs-lookup"><span data-stu-id="148aa-181">For information about listening to a diagnostic source with code, see the [DiagnosticSource user's guide](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span></span>

> [!NOTE]
> <span data-ttu-id="148aa-182">Bibliotecas de telemetria não capturam telemetria específica `Grpc.Net.Client.GrpcOut` gRPC atualmente.</span><span class="sxs-lookup"><span data-stu-id="148aa-182">Telemetry libraries do not capture gRPC specific `Grpc.Net.Client.GrpcOut` telemetry currently.</span></span> <span data-ttu-id="148aa-183">O trabalho para melhorar as bibliotecas de telemetria que capturam esse rastreamento está em andamento.</span><span class="sxs-lookup"><span data-stu-id="148aa-183">Work to improve telemetry libraries capturing this tracing is ongoing.</span></span>

## <a name="metrics"></a><span data-ttu-id="148aa-184">Métricas</span><span class="sxs-lookup"><span data-stu-id="148aa-184">Metrics</span></span>

<span data-ttu-id="148aa-185">Métricas é uma representação de medidas de dados sobre intervalos de tempo, por exemplo, solicitações por segundo.</span><span class="sxs-lookup"><span data-stu-id="148aa-185">Metrics is a representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="148aa-186">Os dados métricos permitem a observação do estado de um aplicativo em alto nível.</span><span class="sxs-lookup"><span data-stu-id="148aa-186">Metrics data allows observation of the state of an app at a high-level.</span></span> <span data-ttu-id="148aa-187">As métricas .NET gRPC `EventCounter`são emitidas usando .</span><span class="sxs-lookup"><span data-stu-id="148aa-187">.NET gRPC metrics are emitted using `EventCounter`.</span></span>

### <a name="grpc-service-metrics"></a><span data-ttu-id="148aa-188">métricas de serviço gRPC</span><span class="sxs-lookup"><span data-stu-id="148aa-188">gRPC service metrics</span></span>

<span data-ttu-id="148aa-189">as métricas do servidor `Grpc.AspNetCore.Server` gRPC são relatadas na fonte do evento.</span><span class="sxs-lookup"><span data-stu-id="148aa-189">gRPC server metrics are reported on `Grpc.AspNetCore.Server` event source.</span></span>

| <span data-ttu-id="148aa-190">Nome</span><span class="sxs-lookup"><span data-stu-id="148aa-190">Name</span></span>                      | <span data-ttu-id="148aa-191">Descrição</span><span class="sxs-lookup"><span data-stu-id="148aa-191">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="148aa-192">Total de Chamadas</span><span class="sxs-lookup"><span data-stu-id="148aa-192">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="148aa-193">Chamadas atuais</span><span class="sxs-lookup"><span data-stu-id="148aa-193">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="148aa-194">Falha total das chamadas</span><span class="sxs-lookup"><span data-stu-id="148aa-194">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="148aa-195">Prazo total de chamadas excedido</span><span class="sxs-lookup"><span data-stu-id="148aa-195">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="148aa-196">Total de Mensagens Enviadas</span><span class="sxs-lookup"><span data-stu-id="148aa-196">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="148aa-197">Total de mensagens recebidas</span><span class="sxs-lookup"><span data-stu-id="148aa-197">Total Messages Received</span></span>       |
| `calls-unimplemented`     | <span data-ttu-id="148aa-198">Total de chamadas não implementadas</span><span class="sxs-lookup"><span data-stu-id="148aa-198">Total Calls Unimplemented</span></span>     |

<span data-ttu-id="148aa-199">ASP.NET Core também fornece suas `Microsoft.AspNetCore.Hosting` próprias métricas sobre a origem do evento.</span><span class="sxs-lookup"><span data-stu-id="148aa-199">ASP.NET Core also provides its own metrics on `Microsoft.AspNetCore.Hosting` event source.</span></span>

### <a name="grpc-client-metrics"></a><span data-ttu-id="148aa-200">métricas do cliente gRPC</span><span class="sxs-lookup"><span data-stu-id="148aa-200">gRPC client metrics</span></span>

<span data-ttu-id="148aa-201">as métricas do cliente `Grpc.Net.Client` gRPC são relatadas na fonte do evento.</span><span class="sxs-lookup"><span data-stu-id="148aa-201">gRPC client metrics are reported on `Grpc.Net.Client` event source.</span></span>

| <span data-ttu-id="148aa-202">Nome</span><span class="sxs-lookup"><span data-stu-id="148aa-202">Name</span></span>                      | <span data-ttu-id="148aa-203">Descrição</span><span class="sxs-lookup"><span data-stu-id="148aa-203">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="148aa-204">Total de Chamadas</span><span class="sxs-lookup"><span data-stu-id="148aa-204">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="148aa-205">Chamadas atuais</span><span class="sxs-lookup"><span data-stu-id="148aa-205">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="148aa-206">Falha total das chamadas</span><span class="sxs-lookup"><span data-stu-id="148aa-206">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="148aa-207">Prazo total de chamadas excedido</span><span class="sxs-lookup"><span data-stu-id="148aa-207">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="148aa-208">Total de Mensagens Enviadas</span><span class="sxs-lookup"><span data-stu-id="148aa-208">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="148aa-209">Total de mensagens recebidas</span><span class="sxs-lookup"><span data-stu-id="148aa-209">Total Messages Received</span></span>       |

### <a name="observe-metrics"></a><span data-ttu-id="148aa-210">Observar métricas</span><span class="sxs-lookup"><span data-stu-id="148aa-210">Observe metrics</span></span>

<span data-ttu-id="148aa-211">[dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) é uma ferramenta de monitoramento de desempenho para monitoramento de saúde ad-hoc e investigação de desempenho de primeiro nível.</span><span class="sxs-lookup"><span data-stu-id="148aa-211">[dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="148aa-212">Monitore um aplicativo `Grpc.AspNetCore.Server` .NET com ou `Grpc.Net.Client` como o nome do provedor.</span><span class="sxs-lookup"><span data-stu-id="148aa-212">Monitor a .NET app with either `Grpc.AspNetCore.Server` or `Grpc.Net.Client` as the provider name.</span></span>

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

<span data-ttu-id="148aa-213">Outra maneira de observar as métricas gRPC é capturar dados de contador usando o [pacote Microsoft.ApplicationInsights.EventCounterCollector](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters)do Application Insights.</span><span class="sxs-lookup"><span data-stu-id="148aa-213">Another way to observe gRPC metrics is to capture counter data using Application Insights's [Microsoft.ApplicationInsights.EventCounterCollector package](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span></span> <span data-ttu-id="148aa-214">Uma vez configurada, o Application Insights coleta contadores .NET comuns em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="148aa-214">Once setup, Application Insights collects common .NET counters at runtime.</span></span> <span data-ttu-id="148aa-215">Os contadores do gRPC não são coletados por padrão, mas o App Insights pode ser [personalizado para incluir contadores adicionais](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span><span class="sxs-lookup"><span data-stu-id="148aa-215">gRPC's counters are not collected by default, but App Insights can be [customized to include additional counters](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span></span>

<span data-ttu-id="148aa-216">Especifique os contadores gRPC para o Application Insight coletar em *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="148aa-216">Specify the gRPC counters for Application Insight to collect in *Startup.cs*:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="148aa-217">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="148aa-217">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
