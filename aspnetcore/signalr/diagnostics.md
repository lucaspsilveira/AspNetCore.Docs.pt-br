---
<span data-ttu-id="4b588-101">Título: ' log e diagnóstico no ASP.NET Core SignalR ' autor: Descrição: ' saiba como coletar diagnósticos do seu SignalR aplicativo ASP.NET Core. '</span><span class="sxs-lookup"><span data-stu-id="4b588-101">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="4b588-102">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4b588-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b588-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b588-103">'Blazor'</span></span>
- <span data-ttu-id="4b588-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b588-104">'Identity'</span></span>
- <span data-ttu-id="4b588-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b588-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b588-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b588-106">'Razor'</span></span>
- <span data-ttu-id="4b588-107">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="4b588-107">'SignalR' uid:</span></span> 

---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a><span data-ttu-id="4b588-108">Registro em log e diagnóstico no ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="4b588-108">Logging and diagnostics in ASP.NET Core SignalR</span></span>

<span data-ttu-id="4b588-109">Por [Andrew Stanton-enfermaria](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="4b588-109">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="4b588-110">Este artigo fornece orientação para a coleta de diagnósticos do seu SignalR aplicativo ASP.NET Core para ajudar a solucionar problemas.</span><span class="sxs-lookup"><span data-stu-id="4b588-110">This article provides guidance for gathering diagnostics from your ASP.NET Core SignalR app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="4b588-111">Registro em log do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="4b588-111">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="4b588-112">Os logs do lado do servidor podem conter informações confidenciais do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4b588-112">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="4b588-113">**Nunca** poste logs brutos de aplicativos de produção em fóruns públicos, como o github.</span><span class="sxs-lookup"><span data-stu-id="4b588-113">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="4b588-114">Como SignalR o faz parte do ASP.NET Core, ele usa o sistema de registro em log de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4b588-114">Since SignalR is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="4b588-115">Na configuração padrão, o SignalR registra informações muito poucas, mas isso pode ser configurado.</span><span class="sxs-lookup"><span data-stu-id="4b588-115">In the default configuration, SignalR logs very little information, but this can configured.</span></span> <span data-ttu-id="4b588-116">Consulte a documentação em [log de ASP.NET Core](xref:fundamentals/logging/index#configuration) para obter detalhes sobre como configurar o log de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4b588-116">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

SignalR<span data-ttu-id="4b588-117">usa duas categorias de agente:</span><span class="sxs-lookup"><span data-stu-id="4b588-117"> uses two logger categories:</span></span>

* <span data-ttu-id="4b588-118">`Microsoft.AspNetCore.SignalR`: Para logs relacionados a protocolos de Hub, ativação de hubs, invocação de métodos e outras atividades relacionadas ao Hub.</span><span class="sxs-lookup"><span data-stu-id="4b588-118">`Microsoft.AspNetCore.SignalR`: For logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="4b588-119">`Microsoft.AspNetCore.Http.Connections`: Para logs relacionados a transportes, como WebSockets, sondagem longa, eventos enviados pelo servidor e infraestrutura de nível baixo SignalR .</span><span class="sxs-lookup"><span data-stu-id="4b588-119">`Microsoft.AspNetCore.Http.Connections`: For logs related to transports, such as WebSockets, Long Polling, Server-Sent Events, and low-level SignalR infrastructure.</span></span>

<span data-ttu-id="4b588-120">Para habilitar logs detalhados do SignalR , configure os dois prefixos anteriores para o `Debug` nível em seu arquivo *appSettings. JSON* adicionando os seguintes itens à `LogLevel` subseção em `Logging` :</span><span class="sxs-lookup"><span data-stu-id="4b588-120">To enable detailed logs from SignalR, configure both of the preceding prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="4b588-121">Você também pode configurar isso no código em seu `CreateWebHostBuilder` método:</span><span class="sxs-lookup"><span data-stu-id="4b588-121">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="4b588-122">Se você não estiver usando a configuração baseada em JSON, defina os seguintes valores de configuração em seu sistema de configuração:</span><span class="sxs-lookup"><span data-stu-id="4b588-122">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="4b588-123">Verifique a documentação do seu sistema de configuração para determinar como especificar valores de configuração aninhados.</span><span class="sxs-lookup"><span data-stu-id="4b588-123">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="4b588-124">Por exemplo, ao usar variáveis de ambiente, dois `_` caracteres são usados em vez do `:` (por exemplo, `Logging__LogLevel__Microsoft.AspNetCore.SignalR` ).</span><span class="sxs-lookup"><span data-stu-id="4b588-124">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span></span>

<span data-ttu-id="4b588-125">É recomendável usar o `Debug` nível ao coletar diagnósticos mais detalhados para seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4b588-125">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="4b588-126">O `Trace` nível produz diagnósticos de nível muito baixo e raramente é necessário para diagnosticar problemas em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4b588-126">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="4b588-127">Acessar logs do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="4b588-127">Access server-side logs</span></span>

<span data-ttu-id="4b588-128">A maneira como você acessa os logs do lado do servidor depende do ambiente no qual você está executando o.</span><span class="sxs-lookup"><span data-stu-id="4b588-128">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="4b588-129">Como um aplicativo de console fora do IIS</span><span class="sxs-lookup"><span data-stu-id="4b588-129">As a console app outside IIS</span></span>

<span data-ttu-id="4b588-130">Se você estiver executando o em um aplicativo de console, o [agente de log do console](xref:fundamentals/logging/index#console) deverá ser habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="4b588-130">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> SignalR<span data-ttu-id="4b588-131">os logs serão exibidos no console do.</span><span class="sxs-lookup"><span data-stu-id="4b588-131"> logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="4b588-132">Dentro de IIS Express do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b588-132">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="4b588-133">O Visual Studio exibe a saída de log na janela **saída** .</span><span class="sxs-lookup"><span data-stu-id="4b588-133">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="4b588-134">Selecione a opção de menu suspenso do **ASP.NET Core Web Server** .</span><span class="sxs-lookup"><span data-stu-id="4b588-134">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="4b588-135">Serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="4b588-135">Azure App Service</span></span>

<span data-ttu-id="4b588-136">Habilite a opção de **log do aplicativo (Filesystem)** na seção **logs de diagnóstico** do portal do serviço Azure app e configure o **nível** como `Verbose` .</span><span class="sxs-lookup"><span data-stu-id="4b588-136">Enable the **Application Logging (Filesystem)** option in the **Diagnostics logs** section of the Azure App Service portal and configure the **Level** to `Verbose`.</span></span> <span data-ttu-id="4b588-137">Os logs devem estar disponíveis no serviço de **streaming de log** e nos logs no sistema de arquivos do serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4b588-137">Logs should be available from the **Log streaming** service and in logs on the file system of the App Service.</span></span> <span data-ttu-id="4b588-138">Para obter mais informações, consulte [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span><span class="sxs-lookup"><span data-stu-id="4b588-138">For more information, see [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="4b588-139">Outros ambientes</span><span class="sxs-lookup"><span data-stu-id="4b588-139">Other environments</span></span>

<span data-ttu-id="4b588-140">Se o aplicativo for implantado em outro ambiente (por exemplo, Docker, kubernetes ou serviço do Windows), consulte <xref:fundamentals/logging/index> para obter mais informações sobre como configurar provedores de log adequados para o ambiente.</span><span class="sxs-lookup"><span data-stu-id="4b588-140">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="4b588-141">Log de cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="4b588-141">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="4b588-142">Os logs do lado do cliente podem conter informações confidenciais do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4b588-142">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="4b588-143">**Nunca** poste logs brutos de aplicativos de produção em fóruns públicos, como o github.</span><span class="sxs-lookup"><span data-stu-id="4b588-143">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="4b588-144">Ao usar o cliente JavaScript, você pode configurar as opções de log usando o `configureLogging` método em `HubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="4b588-144">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="4b588-145">Para desabilitar completamente o registro em log, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="4b588-145">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="4b588-146">A tabela a seguir mostra os níveis de log disponíveis para o cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4b588-146">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="4b588-147">Definir o nível de log como um desses valores habilita o registro em log nesse nível e todos os níveis acima dele na tabela.</span><span class="sxs-lookup"><span data-stu-id="4b588-147">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="4b588-148">Nível</span><span class="sxs-lookup"><span data-stu-id="4b588-148">Level</span></span> | <span data-ttu-id="4b588-149">Descrição</span><span class="sxs-lookup"><span data-stu-id="4b588-149">Description</span></span> |
| ----- | ---
<span data-ttu-id="4b588-150">Título: ' log e diagnóstico no ASP.NET Core SignalR ' autor: Descrição: ' saiba como coletar diagnósticos do seu SignalR aplicativo ASP.NET Core. '</span><span class="sxs-lookup"><span data-stu-id="4b588-150">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="4b588-151">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4b588-151">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b588-152">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b588-152">'Blazor'</span></span>
- <span data-ttu-id="4b588-153">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b588-153">'Identity'</span></span>
- <span data-ttu-id="4b588-154">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b588-154">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b588-155">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b588-155">'Razor'</span></span>
- <span data-ttu-id="4b588-156">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="4b588-156">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b588-157">Título: ' log e diagnóstico no ASP.NET Core SignalR ' autor: Descrição: ' saiba como coletar diagnósticos do seu SignalR aplicativo ASP.NET Core. '</span><span class="sxs-lookup"><span data-stu-id="4b588-157">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="4b588-158">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4b588-158">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b588-159">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b588-159">'Blazor'</span></span>
- <span data-ttu-id="4b588-160">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b588-160">'Identity'</span></span>
- <span data-ttu-id="4b588-161">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b588-161">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b588-162">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b588-162">'Razor'</span></span>
- <span data-ttu-id="4b588-163">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="4b588-163">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b588-164">Título: ' log e diagnóstico no ASP.NET Core SignalR ' autor: Descrição: ' saiba como coletar diagnósticos do seu SignalR aplicativo ASP.NET Core. '</span><span class="sxs-lookup"><span data-stu-id="4b588-164">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="4b588-165">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4b588-165">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b588-166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b588-166">'Blazor'</span></span>
- <span data-ttu-id="4b588-167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b588-167">'Identity'</span></span>
- <span data-ttu-id="4b588-168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b588-168">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b588-169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b588-169">'Razor'</span></span>
- <span data-ttu-id="4b588-170">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="4b588-170">'SignalR' uid:</span></span> 

<span data-ttu-id="4b588-171">------ | | `None` | Nenhuma mensagem é registrada em log.</span><span class="sxs-lookup"><span data-stu-id="4b588-171">------ | | `None` | No messages are logged.</span></span> <span data-ttu-id="4b588-172">| | `Critical` | Mensagens que indicam uma falha em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4b588-172">| | `Critical` | Messages that indicate a failure in the entire app.</span></span> <span data-ttu-id="4b588-173">| | `Error` | Mensagens que indicam uma falha na operação atual.</span><span class="sxs-lookup"><span data-stu-id="4b588-173">| | `Error` | Messages that indicate a failure in the current operation.</span></span> <span data-ttu-id="4b588-174">| | `Warning` | Mensagens que indicam um problema não fatal.</span><span class="sxs-lookup"><span data-stu-id="4b588-174">| | `Warning` | Messages that indicate a non-fatal problem.</span></span> <span data-ttu-id="4b588-175">| | `Information` | Mensagens informativas.</span><span class="sxs-lookup"><span data-stu-id="4b588-175">| | `Information` | Informational messages.</span></span> <span data-ttu-id="4b588-176">| | `Debug` | Mensagens de diagnóstico úteis para depuração.</span><span class="sxs-lookup"><span data-stu-id="4b588-176">| | `Debug` | Diagnostic messages useful for debugging.</span></span> <span data-ttu-id="4b588-177">| | `Trace` | Mensagens de diagnóstico muito detalhadas projetadas para diagnosticar problemas específicos.</span><span class="sxs-lookup"><span data-stu-id="4b588-177">| | `Trace` | Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="4b588-178">Depois de configurar o detalhamento, os logs serão gravados no console do navegador (ou na saída padrão em um aplicativo NodeJS).</span><span class="sxs-lookup"><span data-stu-id="4b588-178">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="4b588-179">Se você quiser enviar logs para um sistema de registro em log personalizado, poderá fornecer um objeto JavaScript que implementa a `ILogger` interface.</span><span class="sxs-lookup"><span data-stu-id="4b588-179">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="4b588-180">O único método que precisa ser implementado é `log` , que usa o nível do evento e a mensagem associada ao evento.</span><span class="sxs-lookup"><span data-stu-id="4b588-180">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="4b588-181">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="4b588-181">For example:</span></span>

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="4b588-182">Log de cliente .NET</span><span class="sxs-lookup"><span data-stu-id="4b588-182">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="4b588-183">Os logs do lado do cliente podem conter informações confidenciais do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4b588-183">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="4b588-184">**Nunca** poste logs brutos de aplicativos de produção em fóruns públicos, como o github.</span><span class="sxs-lookup"><span data-stu-id="4b588-184">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="4b588-185">Para obter logs do cliente .NET, você pode usar o `ConfigureLogging` método em `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="4b588-185">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="4b588-186">Isso funciona da mesma maneira que o `ConfigureLogging` método em `WebHostBuilder` e `HostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="4b588-186">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="4b588-187">Você pode configurar os mesmos provedores de log usados no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4b588-187">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="4b588-188">No entanto, você precisa instalar e habilitar manualmente os pacotes NuGet para os provedores de log individuais.</span><span class="sxs-lookup"><span data-stu-id="4b588-188">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

### <a name="console-logging"></a><span data-ttu-id="4b588-189">Log de console</span><span class="sxs-lookup"><span data-stu-id="4b588-189">Console logging</span></span>

<span data-ttu-id="4b588-190">Para habilitar o log do console, adicione o pacote [Microsoft. Extensions. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) .</span><span class="sxs-lookup"><span data-stu-id="4b588-190">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="4b588-191">Em seguida, use o `AddConsole` método para configurar o agente de log do console:</span><span class="sxs-lookup"><span data-stu-id="4b588-191">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="4b588-192">Depurar log da janela de saída</span><span class="sxs-lookup"><span data-stu-id="4b588-192">Debug output window logging</span></span>

<span data-ttu-id="4b588-193">Você também pode configurar logs para ir para a janela de **saída** no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4b588-193">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="4b588-194">Instale o pacote [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) e use o `AddDebug` método:</span><span class="sxs-lookup"><span data-stu-id="4b588-194">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="4b588-195">Outros provedores de log</span><span class="sxs-lookup"><span data-stu-id="4b588-195">Other logging providers</span></span>

SignalR<span data-ttu-id="4b588-196">dá suporte a outros provedores de log, como Serilog, Seq, NLog ou qualquer outro sistema de registro em log que se integre ao `Microsoft.Extensions.Logging` .</span><span class="sxs-lookup"><span data-stu-id="4b588-196"> supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="4b588-197">Se o sistema de registro em log fornecer um `ILoggerProvider` , você poderá registrá-lo com `AddProvider` :</span><span class="sxs-lookup"><span data-stu-id="4b588-197">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="4b588-198">Detalhes do controle</span><span class="sxs-lookup"><span data-stu-id="4b588-198">Control verbosity</span></span>

<span data-ttu-id="4b588-199">Se você estiver fazendo logon de outros locais em seu aplicativo, alterar o nível padrão para `Debug` pode ser muito detalhado.</span><span class="sxs-lookup"><span data-stu-id="4b588-199">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="4b588-200">Você pode usar um filtro para configurar o nível de log para SignalR logs.</span><span class="sxs-lookup"><span data-stu-id="4b588-200">You can use a Filter to configure the logging level for SignalR logs.</span></span> <span data-ttu-id="4b588-201">Isso pode ser feito no código, praticamente da mesma forma que no servidor:</span><span class="sxs-lookup"><span data-stu-id="4b588-201">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="4b588-202">Rastreamentos de rede</span><span class="sxs-lookup"><span data-stu-id="4b588-202">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="4b588-203">Um rastreamento de rede contém todo o conteúdo de cada mensagem enviada pelo seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4b588-203">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="4b588-204">**Nunca** poste rastreamentos de rede brutos de aplicativos de produção para fóruns públicos, como o github.</span><span class="sxs-lookup"><span data-stu-id="4b588-204">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="4b588-205">Se você encontrar um problema, um rastreamento de rede pode, às vezes, fornecer muitas informações úteis.</span><span class="sxs-lookup"><span data-stu-id="4b588-205">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="4b588-206">Isso é particularmente útil se você pretende arquivar um problema em nosso Issue Tracker.</span><span class="sxs-lookup"><span data-stu-id="4b588-206">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="4b588-207">Coletar um rastreamento de rede com o Fiddler (opção preferencial)</span><span class="sxs-lookup"><span data-stu-id="4b588-207">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="4b588-208">Esse método funciona para todos os aplicativos.</span><span class="sxs-lookup"><span data-stu-id="4b588-208">This method works for all apps.</span></span>

<span data-ttu-id="4b588-209">O Fiddler é uma ferramenta muito poderosa para coletar rastreamentos HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b588-209">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="4b588-210">Instale-o em [Telerik.com/Fiddler](https://www.telerik.com/fiddler), inicie-o e execute o aplicativo e reproduza o problema.</span><span class="sxs-lookup"><span data-stu-id="4b588-210">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="4b588-211">O Fiddler está disponível para o Windows e há versões beta para macOS e Linux.</span><span class="sxs-lookup"><span data-stu-id="4b588-211">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="4b588-212">Se você se conectar usando HTTPS, haverá algumas etapas adicionais para garantir que o Fiddler possa descriptografar o tráfego HTTPS.</span><span class="sxs-lookup"><span data-stu-id="4b588-212">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="4b588-213">Para obter mais detalhes, consulte a [documentação do Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span><span class="sxs-lookup"><span data-stu-id="4b588-213">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="4b588-214">Depois de coletar o rastreamento, você pode exportar o rastreamento escolhendo **arquivo**  >  **salvar**  >  **todas as sessões** na barra de menus.</span><span class="sxs-lookup"><span data-stu-id="4b588-214">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions** from the menu bar.</span></span>

![Exportando todas as sessões do Fiddler](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="4b588-216">Coletar um rastreamento de rede com tcpdump (somente macOS e Linux)</span><span class="sxs-lookup"><span data-stu-id="4b588-216">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="4b588-217">Esse método funciona para todos os aplicativos.</span><span class="sxs-lookup"><span data-stu-id="4b588-217">This method works for all apps.</span></span>

<span data-ttu-id="4b588-218">Você pode coletar rastreamentos TCP brutos usando tcpdump executando o comando a seguir de um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="4b588-218">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="4b588-219">Talvez seja necessário ser `root` ou prefixar o comando com `sudo` se você receber um erro de permissões:</span><span class="sxs-lookup"><span data-stu-id="4b588-219">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="4b588-220">Substitua `[interface]` pela interface de rede que você deseja capturar.</span><span class="sxs-lookup"><span data-stu-id="4b588-220">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="4b588-221">Normalmente, isso é algo como `/dev/eth0` (para a sua interface Ethernet padrão) ou `/dev/lo0` (para tráfego de localhost).</span><span class="sxs-lookup"><span data-stu-id="4b588-221">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="4b588-222">Para obter mais informações, consulte a `tcpdump` página do manual no sistema host.</span><span class="sxs-lookup"><span data-stu-id="4b588-222">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="4b588-223">Coletar um rastreamento de rede no navegador</span><span class="sxs-lookup"><span data-stu-id="4b588-223">Collect a network trace in the browser</span></span>

<span data-ttu-id="4b588-224">Esse método só funciona para aplicativos baseados em navegador.</span><span class="sxs-lookup"><span data-stu-id="4b588-224">This method only works for browser-based apps.</span></span>

<span data-ttu-id="4b588-225">A maioria dos Ferramentas para Desenvolvedores de navegador tem uma guia de "rede" que permite capturar a atividade de rede entre o navegador e o servidor.</span><span class="sxs-lookup"><span data-stu-id="4b588-225">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="4b588-226">No entanto, esses rastreamentos não incluem o WebSocket e as mensagens de evento enviadas pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="4b588-226">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="4b588-227">Se você estiver usando esses transportes, usar uma ferramenta como Fiddler ou TcpDump (descrita abaixo) é uma abordagem melhor.</span><span class="sxs-lookup"><span data-stu-id="4b588-227">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="4b588-228">Microsoft Edge e Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="4b588-228">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="4b588-229">(As instruções são as mesmas para o Edge e para o Internet Explorer)</span><span class="sxs-lookup"><span data-stu-id="4b588-229">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="4b588-230">Pressione F12 para abrir as ferramentas de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="4b588-230">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="4b588-231">Clique na guia rede</span><span class="sxs-lookup"><span data-stu-id="4b588-231">Click the Network Tab</span></span>
3. <span data-ttu-id="4b588-232">Atualizar a página (se necessário) e reproduzir o problema</span><span class="sxs-lookup"><span data-stu-id="4b588-232">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="4b588-233">Clique no ícone salvar na barra de ferramentas para exportar o rastreamento como um arquivo "HAR":</span><span class="sxs-lookup"><span data-stu-id="4b588-233">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![O ícone salvar na guia rede de ferramentas de desenvolvimento do Microsoft Edge](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="4b588-235">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="4b588-235">Google Chrome</span></span>

1. <span data-ttu-id="4b588-236">Pressione F12 para abrir as ferramentas de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="4b588-236">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="4b588-237">Clique na guia rede</span><span class="sxs-lookup"><span data-stu-id="4b588-237">Click the Network Tab</span></span>
3. <span data-ttu-id="4b588-238">Atualizar a página (se necessário) e reproduzir o problema</span><span class="sxs-lookup"><span data-stu-id="4b588-238">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="4b588-239">Clique com o botão direito do mouse em qualquer lugar na lista de solicitações e escolha "salvar como HAR com conteúdo":</span><span class="sxs-lookup"><span data-stu-id="4b588-239">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Opção "salvar como HAR com conteúdo" na guia rede de ferramentas de desenvolvimento do Google Chrome](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="4b588-241">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="4b588-241">Mozilla Firefox</span></span>

1. <span data-ttu-id="4b588-242">Pressione F12 para abrir as ferramentas de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="4b588-242">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="4b588-243">Clique na guia rede</span><span class="sxs-lookup"><span data-stu-id="4b588-243">Click the Network Tab</span></span>
3. <span data-ttu-id="4b588-244">Atualizar a página (se necessário) e reproduzir o problema</span><span class="sxs-lookup"><span data-stu-id="4b588-244">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="4b588-245">Clique com o botão direito do mouse em qualquer lugar na lista de solicitações e escolha "salvar tudo como HAR"</span><span class="sxs-lookup"><span data-stu-id="4b588-245">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Opção "salvar tudo como HAR" na guia de rede de ferramentas de desenvolvimento do Mozilla Firefox](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="4b588-247">Anexar arquivos de diagnóstico a problemas do GitHub</span><span class="sxs-lookup"><span data-stu-id="4b588-247">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="4b588-248">Você pode anexar arquivos de diagnóstico a problemas do GitHub renomeando-os para que eles tenham uma `.txt` extensão e, em seguida, arrastando-os e soltando-os no problema.</span><span class="sxs-lookup"><span data-stu-id="4b588-248">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="4b588-249">Não Cole o conteúdo dos arquivos de log ou dos rastreamentos de rede em um problema do GitHub.</span><span class="sxs-lookup"><span data-stu-id="4b588-249">Please don't paste the content of log files or network traces into a GitHub issue.</span></span> <span data-ttu-id="4b588-250">Esses logs e rastreamentos podem ser bem grandes, e o GitHub geralmente os trunca.</span><span class="sxs-lookup"><span data-stu-id="4b588-250">These logs and traces can be quite large, and GitHub usually truncates them.</span></span>

![Arrastando arquivos de log para um problema do GitHub](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a><span data-ttu-id="4b588-252">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="4b588-252">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
