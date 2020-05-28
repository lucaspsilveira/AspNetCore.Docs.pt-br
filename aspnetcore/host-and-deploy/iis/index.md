---
<span data-ttu-id="dc286-101">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-102">'Blazor'</span></span>
- <span data-ttu-id="dc286-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-103">'Identity'</span></span>
- <span data-ttu-id="dc286-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-105">'Razor'</span></span>
- <span data-ttu-id="dc286-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-106">'SignalR' uid:</span></span> 

---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="dc286-107">Hospedar o ASP.NET Core no Windows com o IIS</span><span class="sxs-lookup"><span data-stu-id="dc286-107">Host ASP.NET Core on Windows with IIS</span></span>

<!-- 

    NOTE FOR 5.0
    
    When making the 5.0 version of this topic, remove the Hosting Bundle
    direct download section from the (new) <5.0 & >2.2 version and modify 
    the text and heading for the *Earlier versions of the installer* 
    section. See the 2.2 version for an example.
    
-->

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dc286-108">Para uma experiência de tutorial sobre como publicar um aplicativo de ASP.NET Core em um servidor IIS, confira <xref:tutorials/publish-to-iis>.</span><span class="sxs-lookup"><span data-stu-id="dc286-108">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="dc286-109">Instalar o pacote de hospedagem do .NET Core</span><span class="sxs-lookup"><span data-stu-id="dc286-109">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="dc286-110">Sistemas operacionais compatíveis</span><span class="sxs-lookup"><span data-stu-id="dc286-110">Supported operating systems</span></span>

<span data-ttu-id="dc286-111">Há suporte para os seguintes sistemas operacionais:</span><span class="sxs-lookup"><span data-stu-id="dc286-111">The following operating systems are supported:</span></span>

* <span data-ttu-id="dc286-112">Windows 7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="dc286-112">Windows 7 or later</span></span>
* <span data-ttu-id="dc286-113">Windows Server 2012 R2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="dc286-113">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="dc286-114">O [servidor HTTP.sys](xref:fundamentals/servers/httpsys) (anteriormente chamado de WebListener) não funciona em uma configuração de proxy reverso com o IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-114">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="dc286-115">Use o [servidor Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="dc286-115">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="dc286-116">Para obter mais informações sobre hospedagem no Azure, consulte <xref:host-and-deploy/azure-apps/index>.</span><span class="sxs-lookup"><span data-stu-id="dc286-116">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="dc286-117">Para obter as diretrizes de solução de problemas, consulte <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="dc286-117">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="dc286-118">Plataformas compatíveis</span><span class="sxs-lookup"><span data-stu-id="dc286-118">Supported platforms</span></span>

<span data-ttu-id="dc286-119">Aplicativos publicados para implantação de 32 bits (x86) ou 64 bits (x64) têm suporte.</span><span class="sxs-lookup"><span data-stu-id="dc286-119">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="dc286-120">Implantar um aplicativo de 32 bits com um SDK do .NET Core de 32 bits (x86), a menos que o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="dc286-120">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="dc286-121">Exija o maior espaço de endereço de memória virtual disponível para um aplicativo de 64 bits.</span><span class="sxs-lookup"><span data-stu-id="dc286-121">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="dc286-122">Exija o maior tamanho de pilha do IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-122">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="dc286-123">Tenha dependências nativas de 64 bits.</span><span class="sxs-lookup"><span data-stu-id="dc286-123">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="dc286-124">Os aplicativos publicados para 32 bits (x86) devem ter 32 bits habilitado para seus pools de aplicativos do IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-124">Apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="dc286-125">Para obter mais informações, consulte a seção [criar o site do IIS](#create-the-iis-site) .</span><span class="sxs-lookup"><span data-stu-id="dc286-125">For more information, see the [Create the IIS site](#create-the-iis-site) section.</span></span>

<span data-ttu-id="dc286-126">Use um SDK do .NET Core de 64 bits (x64) para publicar um aplicativo de 64 bits.</span><span class="sxs-lookup"><span data-stu-id="dc286-126">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="dc286-127">Um runtime de 64 bits deve estar presente no sistema host.</span><span class="sxs-lookup"><span data-stu-id="dc286-127">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="dc286-128">Modelos de hospedagem</span><span class="sxs-lookup"><span data-stu-id="dc286-128">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="dc286-129">Modelo de hospedagem em processo</span><span class="sxs-lookup"><span data-stu-id="dc286-129">In-process hosting model</span></span>

<span data-ttu-id="dc286-130">Usando uma hospedagem em processo, um aplicativo ASP.NET Core é executado no mesmo processo que seu processo de trabalho do IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-130">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="dc286-131">A hospedagem em processo oferece desempenho melhor em hospedagem fora do processo porque as solicitações não são transmitidas por proxy pelo adaptador de loopback, um adaptador de rede que retorna o tráfego de rede de saída para o mesmo computador.</span><span class="sxs-lookup"><span data-stu-id="dc286-131">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="dc286-132">O IIS manipula o gerenciamento de processos com o [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="dc286-132">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="dc286-133">O [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="dc286-133">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="dc286-134">Executa a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-134">Performs app initialization.</span></span>
  * <span data-ttu-id="dc286-135">Carrega o [CoreCLR](/dotnet/standard/glossary#coreclr).</span><span class="sxs-lookup"><span data-stu-id="dc286-135">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="dc286-136">Chama `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="dc286-136">Calls `Program.Main`.</span></span>
* <span data-ttu-id="dc286-137">Manipula o tempo de vida da solicitação nativa do IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-137">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="dc286-138">O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo hospedado em processo:</span><span class="sxs-lookup"><span data-stu-id="dc286-138">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![Módulo do ASP.NET Core no cenário de hospedagem em processo](index/_static/ancm-inprocess.png)

1. <span data-ttu-id="dc286-140">A solicitação chega da Web para o driver do HTTP.sys no modo kernel.</span><span class="sxs-lookup"><span data-stu-id="dc286-140">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="dc286-141">O driver roteia as solicitações nativas ao IIS na porta configurada do site, normalmente, a 80 (HTTP) ou a 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="dc286-141">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="dc286-142">O módulo ASP.NET Core recebe a solicitação nativa e a passa para o servidor HTTP IIS ( `IISHttpServer` ).</span><span class="sxs-lookup"><span data-stu-id="dc286-142">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="dc286-143">O servidor HTTP do IIS é uma implementação de servidor em processo do IIS que converte a solicitação de nativa para gerenciada.</span><span class="sxs-lookup"><span data-stu-id="dc286-143">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="dc286-144">Depois que o servidor HTTP IIS processar a solicitação:</span><span class="sxs-lookup"><span data-stu-id="dc286-144">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="dc286-145">A solicitação é enviada para o pipeline de middleware ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dc286-145">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="dc286-146">O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-146">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="dc286-147">A resposta do aplicativo é retornada ao IIS por meio do Servidor HTTP do IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-147">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="dc286-148">O IIS enviará a resposta ao cliente que iniciou a solicitação.</span><span class="sxs-lookup"><span data-stu-id="dc286-148">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="dc286-149">A hospedagem em processo é opcional para aplicativos existentes.</span><span class="sxs-lookup"><span data-stu-id="dc286-149">In-process hosting is opt-in for existing apps.</span></span> <span data-ttu-id="dc286-150">Os modelos da Web ASP.NET Core usam o modelo de hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="dc286-150">The ASP.NET Core web templates use the in-process hosting model.</span></span>

<span data-ttu-id="dc286-151">`CreateDefaultBuilder` adiciona uma instância <xref:Microsoft.AspNetCore.Hosting.Server.IServer> chamando o método <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> para inicializar o [CoreCLR](/dotnet/standard/glossary#coreclr) e hospedar o aplicativo no processo de trabalho do IIS (*w3wp.exe* ou *iisexpress.exe*).</span><span class="sxs-lookup"><span data-stu-id="dc286-151">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="dc286-152">Os testes de desempenho indicam que hospedar um aplicativo em processo do .NET Core oferece uma de taxa de transferência de solicitação significativamente mais elevada em comparação a hospedar o aplicativo fora do processo e enviar por proxy as solicitações para o [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="dc286-152">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="dc286-153">Aplicativos publicados como um único arquivo executável não podem ser carregados pelo modelo de hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="dc286-153">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="dc286-154">Modelo de hospedagem de fora do processo</span><span class="sxs-lookup"><span data-stu-id="dc286-154">Out-of-process hosting model</span></span>

<span data-ttu-id="dc286-155">Como ASP.NET Core aplicativos são executados em um processo separado do processo de trabalho do IIS, o módulo ASP.NET Core lida com o gerenciamento de processos.</span><span class="sxs-lookup"><span data-stu-id="dc286-155">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="dc286-156">O módulo inicia o processo para o aplicativo ASP.NET Core quando a primeira solicitação chega e reinicia o aplicativo se ele é desligado ou falha.</span><span class="sxs-lookup"><span data-stu-id="dc286-156">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="dc286-157">Isso é basicamente o mesmo comportamento que o dos aplicativos que são executados dentro do processo e são gerenciados pelo [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="dc286-157">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="dc286-158">O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo hospedado de fora d processo:</span><span class="sxs-lookup"><span data-stu-id="dc286-158">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Módulo do ASP.NET Core no cenário de hospedagem fora do processo](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="dc286-160">As solicitações chegam da Web para o driver do HTTP.sys no modo kernel.</span><span class="sxs-lookup"><span data-stu-id="dc286-160">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="dc286-161">O driver roteia as solicitações para o IIS na porta configurada do site.</span><span class="sxs-lookup"><span data-stu-id="dc286-161">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="dc286-162">A porta configurada geralmente é 80 (HTTP) ou 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="dc286-162">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="dc286-163">O módulo encaminha as solicitações para Kestrel em uma porta aleatória para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-163">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="dc286-164">A porta aleatória não é 80 ou 443.</span><span class="sxs-lookup"><span data-stu-id="dc286-164">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="dc286-165">O módulo ASP.NET Core especifica a porta por meio de uma variável de ambiente na inicialização.</span><span class="sxs-lookup"><span data-stu-id="dc286-165">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="dc286-166">A <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extensão configura o servidor a ser escutado `http://localhost:{PORT}` .</span><span class="sxs-lookup"><span data-stu-id="dc286-166">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="dc286-167">Outras verificações são executadas e as solicitações que não se originam do módulo são rejeitadas.</span><span class="sxs-lookup"><span data-stu-id="dc286-167">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="dc286-168">O módulo não dá suporte ao encaminhamento de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="dc286-168">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="dc286-169">As solicitações são encaminhadas por HTTP, mesmo se recebidas pelo IIS sobre HTTPS.</span><span class="sxs-lookup"><span data-stu-id="dc286-169">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="dc286-170">Depois que o Kestrel pega a solicitação do módulo, a solicitação é encaminhada para o pipeline de middleware ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dc286-170">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="dc286-171">O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-171">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="dc286-172">O middleware adicionado pela integração do IIS atualiza o esquema, o IP remoto e pathbase para encaminhar a solicitação para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="dc286-172">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="dc286-173">A resposta do aplicativo é passada de volta para o IIS, que o encaminha de volta para o cliente HTTP que iniciou a solicitação.</span><span class="sxs-lookup"><span data-stu-id="dc286-173">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="dc286-174">Para orientação sobre a configuração do Módulo do ASP.NET Core, consulte <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="dc286-174">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="dc286-175">Para saber mais sobre hospedagem, confira [Host no ASP.NET Core](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="dc286-175">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="dc286-176">Configuração de aplicativo</span><span class="sxs-lookup"><span data-stu-id="dc286-176">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="dc286-177">Habilitar os componentes de IISIntegration</span><span class="sxs-lookup"><span data-stu-id="dc286-177">Enable the IISIntegration components</span></span>

<span data-ttu-id="dc286-178">Ao criar um host no `CreateHostBuilder` (*Program.cs*), chame <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> para habilitar a integração do IIS:</span><span class="sxs-lookup"><span data-stu-id="dc286-178">When building a host in `CreateHostBuilder` (*Program.cs*), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="dc286-179">Para obter mais informações sobre o `CreateDefaultBuilder`, consulte <xref:fundamentals/host/generic-host#default-builder-settings>.</span><span class="sxs-lookup"><span data-stu-id="dc286-179">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="dc286-180">Opções do IIS</span><span class="sxs-lookup"><span data-stu-id="dc286-180">IIS options</span></span>

<span data-ttu-id="dc286-181">**Modelo de hospedagem em processo**</span><span class="sxs-lookup"><span data-stu-id="dc286-181">**In-process hosting model**</span></span>

<span data-ttu-id="dc286-182">Para configurar as opções do Servidor IIS, inclua uma configuração de serviço para <xref:Microsoft.AspNetCore.Builder.IISServerOptions> em <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span><span class="sxs-lookup"><span data-stu-id="dc286-182">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="dc286-183">O exemplo a seguir desabilita AutomaticAuthentication:</span><span class="sxs-lookup"><span data-stu-id="dc286-183">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="dc286-184">Opção</span><span class="sxs-lookup"><span data-stu-id="dc286-184">Option</span></span>                         | <span data-ttu-id="dc286-185">Padrão</span><span class="sxs-lookup"><span data-stu-id="dc286-185">Default</span></span> | <span data-ttu-id="dc286-186">Configuração</span><span class="sxs-lookup"><span data-stu-id="dc286-186">Setting</span></span> |
| ---
<span data-ttu-id="dc286-187">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-187">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-188">'Blazor'</span></span>
- <span data-ttu-id="dc286-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-189">'Identity'</span></span>
- <span data-ttu-id="dc286-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-191">'Razor'</span></span>
- <span data-ttu-id="dc286-192">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-193">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-193">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-194">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-194">'Blazor'</span></span>
- <span data-ttu-id="dc286-195">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-195">'Identity'</span></span>
- <span data-ttu-id="dc286-196">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-196">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-197">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-197">'Razor'</span></span>
- <span data-ttu-id="dc286-198">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-198">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-199">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-199">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-200">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-200">'Blazor'</span></span>
- <span data-ttu-id="dc286-201">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-201">'Identity'</span></span>
- <span data-ttu-id="dc286-202">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-203">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-203">'Razor'</span></span>
- <span data-ttu-id="dc286-204">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-204">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-205">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-205">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-206">'Blazor'</span></span>
- <span data-ttu-id="dc286-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-207">'Identity'</span></span>
- <span data-ttu-id="dc286-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-209">'Razor'</span></span>
- <span data-ttu-id="dc286-210">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-211">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-211">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-212">'Blazor'</span></span>
- <span data-ttu-id="dc286-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-213">'Identity'</span></span>
- <span data-ttu-id="dc286-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-215">'Razor'</span></span>
- <span data-ttu-id="dc286-216">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-217">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-217">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-218">'Blazor'</span></span>
- <span data-ttu-id="dc286-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-219">'Identity'</span></span>
- <span data-ttu-id="dc286-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-221">'Razor'</span></span>
- <span data-ttu-id="dc286-222">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-222">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-223">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-223">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-224">'Blazor'</span></span>
- <span data-ttu-id="dc286-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-225">'Identity'</span></span>
- <span data-ttu-id="dc286-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-227">'Razor'</span></span>
- <span data-ttu-id="dc286-228">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-229">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-230">'Blazor'</span></span>
- <span data-ttu-id="dc286-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-231">'Identity'</span></span>
- <span data-ttu-id="dc286-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-233">'Razor'</span></span>
- <span data-ttu-id="dc286-234">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-235">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-236">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-236">'Blazor'</span></span>
- <span data-ttu-id="dc286-237">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-237">'Identity'</span></span>
- <span data-ttu-id="dc286-238">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-238">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-239">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-239">'Razor'</span></span>
- <span data-ttu-id="dc286-240">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-240">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-241">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-242">'Blazor'</span></span>
- <span data-ttu-id="dc286-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-243">'Identity'</span></span>
- <span data-ttu-id="dc286-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-245">'Razor'</span></span>
- <span data-ttu-id="dc286-246">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-246">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-247">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-248">'Blazor'</span></span>
- <span data-ttu-id="dc286-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-249">'Identity'</span></span>
- <span data-ttu-id="dc286-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-251">'Razor'</span></span>
- <span data-ttu-id="dc286-252">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-253">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-254">'Blazor'</span></span>
- <span data-ttu-id="dc286-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-255">'Identity'</span></span>
- <span data-ttu-id="dc286-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-257">'Razor'</span></span>
- <span data-ttu-id="dc286-258">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-259">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-260">'Blazor'</span></span>
- <span data-ttu-id="dc286-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-261">'Identity'</span></span>
- <span data-ttu-id="dc286-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-263">'Razor'</span></span>
- <span data-ttu-id="dc286-264">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-264">'SignalR' uid:</span></span> 

<span data-ttu-id="dc286-265">--------------- | :-----: | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-265">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-266">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-266">'Blazor'</span></span>
- <span data-ttu-id="dc286-267">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-267">'Identity'</span></span>
- <span data-ttu-id="dc286-268">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-268">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-269">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-269">'Razor'</span></span>
- <span data-ttu-id="dc286-270">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-270">'SignalR' uid:</span></span> 

<span data-ttu-id="dc286-271">---- | | `AutomaticAuthentication`      | `true`  | Se `true` , o servidor IIS definirá a `HttpContext.User` autenticação autenticada pelo [Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="dc286-271">---- | | `AutomaticAuthentication`      | `true`  | If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="dc286-272">Se `false`, o servidor fornecerá apenas uma identidade para `HttpContext.User` e responderá a desafios quando explicitamente solicitado pelo `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="dc286-272">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="dc286-273">A autenticação do Windows deve estar habilitada no IIS para que o `AutomaticAuthentication` funcione.</span><span class="sxs-lookup"><span data-stu-id="dc286-273">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="dc286-274">Para obter mais informações, veja [Autenticação do Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="dc286-274">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="dc286-275">| | `AuthenticationDisplayName`    | `null`  | Define o nome de exibição mostrado para os usuários nas páginas de logon.</span><span class="sxs-lookup"><span data-stu-id="dc286-275">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="dc286-276">| | `AllowSynchronousIO`           | `false` | Se a e/s síncrona é permitida para o `HttpContext.Request` e o `HttpContext.Response` .</span><span class="sxs-lookup"><span data-stu-id="dc286-276">| | `AllowSynchronousIO`           | `false` | Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> <span data-ttu-id="dc286-277">| | `MaxRequestBodySize`           | `30000000`  | Obtém ou define o tamanho máximo do corpo da solicitação para o `HttpRequest` .</span><span class="sxs-lookup"><span data-stu-id="dc286-277">| | `MaxRequestBodySize`           | `30000000`  | Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="dc286-278">Observe que o próprio IIS tem o limite `maxAllowedContentLength` que será processado antes de `MaxRequestBodySize` definido no `IISServerOptions`.</span><span class="sxs-lookup"><span data-stu-id="dc286-278">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="dc286-279">Alterar `MaxRequestBodySize` não afetará `maxAllowedContentLength`.</span><span class="sxs-lookup"><span data-stu-id="dc286-279">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="dc286-280">Para aumentar `maxAllowedContentLength`, adicione uma entrada em *web.config* para definir `maxAllowedContentLength` para um valor mais alto.</span><span class="sxs-lookup"><span data-stu-id="dc286-280">To increase `maxAllowedContentLength`, add an entry in the *web.config* to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="dc286-281">Para obter mais detalhes, confira [Configuração](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span><span class="sxs-lookup"><span data-stu-id="dc286-281">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

<span data-ttu-id="dc286-282">**Modelo de hospedagem de fora do processo**</span><span class="sxs-lookup"><span data-stu-id="dc286-282">**Out-of-process hosting model**</span></span>

<span data-ttu-id="dc286-283">Para configurar opções do IIS, inclua uma configuração de serviço para <xref:Microsoft.AspNetCore.Builder.IISOptions> em <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span><span class="sxs-lookup"><span data-stu-id="dc286-283">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="dc286-284">O exemplo a seguir impede que o aplicativo preencha `HttpContext.Connection.ClientCertificate`:</span><span class="sxs-lookup"><span data-stu-id="dc286-284">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="dc286-285">Opção</span><span class="sxs-lookup"><span data-stu-id="dc286-285">Option</span></span>                         | <span data-ttu-id="dc286-286">Padrão</span><span class="sxs-lookup"><span data-stu-id="dc286-286">Default</span></span> | <span data-ttu-id="dc286-287">Configuração</span><span class="sxs-lookup"><span data-stu-id="dc286-287">Setting</span></span> |
| ---
<span data-ttu-id="dc286-288">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-289">'Blazor'</span></span>
- <span data-ttu-id="dc286-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-290">'Identity'</span></span>
- <span data-ttu-id="dc286-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-292">'Razor'</span></span>
- <span data-ttu-id="dc286-293">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-294">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-294">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-295">'Blazor'</span></span>
- <span data-ttu-id="dc286-296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-296">'Identity'</span></span>
- <span data-ttu-id="dc286-297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-298">'Razor'</span></span>
- <span data-ttu-id="dc286-299">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-300">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-301">'Blazor'</span></span>
- <span data-ttu-id="dc286-302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-302">'Identity'</span></span>
- <span data-ttu-id="dc286-303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-304">'Razor'</span></span>
- <span data-ttu-id="dc286-305">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-306">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-307">'Blazor'</span></span>
- <span data-ttu-id="dc286-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-308">'Identity'</span></span>
- <span data-ttu-id="dc286-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-310">'Razor'</span></span>
- <span data-ttu-id="dc286-311">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-312">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-313">'Blazor'</span></span>
- <span data-ttu-id="dc286-314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-314">'Identity'</span></span>
- <span data-ttu-id="dc286-315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-316">'Razor'</span></span>
- <span data-ttu-id="dc286-317">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-318">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-319">'Blazor'</span></span>
- <span data-ttu-id="dc286-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-320">'Identity'</span></span>
- <span data-ttu-id="dc286-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-322">'Razor'</span></span>
- <span data-ttu-id="dc286-323">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-324">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-325">'Blazor'</span></span>
- <span data-ttu-id="dc286-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-326">'Identity'</span></span>
- <span data-ttu-id="dc286-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-328">'Razor'</span></span>
- <span data-ttu-id="dc286-329">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-330">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-331">'Blazor'</span></span>
- <span data-ttu-id="dc286-332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-332">'Identity'</span></span>
- <span data-ttu-id="dc286-333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-334">'Razor'</span></span>
- <span data-ttu-id="dc286-335">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-336">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-337">'Blazor'</span></span>
- <span data-ttu-id="dc286-338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-338">'Identity'</span></span>
- <span data-ttu-id="dc286-339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-340">'Razor'</span></span>
- <span data-ttu-id="dc286-341">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-342">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-343">'Blazor'</span></span>
- <span data-ttu-id="dc286-344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-344">'Identity'</span></span>
- <span data-ttu-id="dc286-345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-346">'Razor'</span></span>
- <span data-ttu-id="dc286-347">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-348">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-349">'Blazor'</span></span>
- <span data-ttu-id="dc286-350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-350">'Identity'</span></span>
- <span data-ttu-id="dc286-351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-352">'Razor'</span></span>
- <span data-ttu-id="dc286-353">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-354">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-355">'Blazor'</span></span>
- <span data-ttu-id="dc286-356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-356">'Identity'</span></span>
- <span data-ttu-id="dc286-357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-358">'Razor'</span></span>
- <span data-ttu-id="dc286-359">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-360">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-361">'Blazor'</span></span>
- <span data-ttu-id="dc286-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-362">'Identity'</span></span>
- <span data-ttu-id="dc286-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-364">'Razor'</span></span>
- <span data-ttu-id="dc286-365">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-365">'SignalR' uid:</span></span> 

<span data-ttu-id="dc286-366">--------------- | :-----: | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-366">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-367">'Blazor'</span></span>
- <span data-ttu-id="dc286-368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-368">'Identity'</span></span>
- <span data-ttu-id="dc286-369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-370">'Razor'</span></span>
- <span data-ttu-id="dc286-371">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-371">'SignalR' uid:</span></span> 

<span data-ttu-id="dc286-372">---- | | `AutomaticAuthentication`      | `true`  | Se `true` , o [middleware de integração do IIS](#enable-the-iisintegration-components) define a `HttpContext.User` [autenticação](xref:security/authentication/windowsauth)autenticada pelo Windows.</span><span class="sxs-lookup"><span data-stu-id="dc286-372">---- | | `AutomaticAuthentication`      | `true`  | If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="dc286-373">Se `false`, o middleware fornecerá apenas uma identidade para `HttpContext.User` e responderá a desafios quando explicitamente solicitado pelo `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="dc286-373">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="dc286-374">A autenticação do Windows deve estar habilitada no IIS para que o `AutomaticAuthentication` funcione.</span><span class="sxs-lookup"><span data-stu-id="dc286-374">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="dc286-375">Saiba mais no tópico [Autenticação do Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="dc286-375">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> <span data-ttu-id="dc286-376">| | `AuthenticationDisplayName`    | `null`  | Define o nome de exibição mostrado para os usuários nas páginas de logon.</span><span class="sxs-lookup"><span data-stu-id="dc286-376">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="dc286-377">| | `ForwardClientCertificate`     | `true`  | Se `true` e o `MS-ASPNETCORE-CLIENTCERT` cabeçalho da solicitação estiver presente, o `HttpContext.Connection.ClientCertificate` será preenchido.</span><span class="sxs-lookup"><span data-stu-id="dc286-377">| | `ForwardClientCertificate`     | `true`  | If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="dc286-378">Servidor proxy e cenários de balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="dc286-378">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="dc286-379">O [middleware de integração do IIS](#enable-the-iisintegration-components) e o módulo ASP.NET Core são configurados para encaminhar o:</span><span class="sxs-lookup"><span data-stu-id="dc286-379">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="dc286-380">Esquema (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="dc286-380">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="dc286-381">Endereço IP remoto em que a solicitação foi originada.</span><span class="sxs-lookup"><span data-stu-id="dc286-381">Remote IP address where the request originated.</span></span>

<span data-ttu-id="dc286-382">O [middleware de integração do IIS](#enable-the-iisintegration-components) configura o middleware de cabeçalhos encaminhados.</span><span class="sxs-lookup"><span data-stu-id="dc286-382">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="dc286-383">Configuração adicional pode ser necessária para aplicativos hospedados atrás de servidores proxy adicionais e balanceadores de carga.</span><span class="sxs-lookup"><span data-stu-id="dc286-383">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="dc286-384">Para obter mais informações, veja [Configurar o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="dc286-384">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="dc286-385">Arquivo web.config</span><span class="sxs-lookup"><span data-stu-id="dc286-385">web.config file</span></span>

<span data-ttu-id="dc286-386">O arquivo *web.config* configura o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="dc286-386">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="dc286-387">Criando, transformar e publicar o arquivo *Web.config* é tratado por um destino do MSBuild (`_TransformWebConfig`) quando o projeto é publicado.</span><span class="sxs-lookup"><span data-stu-id="dc286-387">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="dc286-388">Este destino está presente nos destinos do SDK da Web (`Microsoft.NET.Sdk.Web`).</span><span class="sxs-lookup"><span data-stu-id="dc286-388">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="dc286-389">O SDK é definido na parte superior do arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="dc286-389">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="dc286-390">Se um arquivo *web.config* não estiver presente no projeto, ele será criado com o *processPath* e os *argumentos* corretos para configurar o Módulo do ASP.NET Core e será transferido para o [resultado publicado](xref:host-and-deploy/directory-structure).</span><span class="sxs-lookup"><span data-stu-id="dc286-390">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="dc286-391">Se um arquivo *web.config* estiver presente no projeto, ele será transformado com o *processPath* e os *argumentos* corretos para configurar o Módulo do ASP.NET Core e será movido para o resultado publicado.</span><span class="sxs-lookup"><span data-stu-id="dc286-391">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="dc286-392">A transformação não altera as definições de configuração do IIS no arquivo.</span><span class="sxs-lookup"><span data-stu-id="dc286-392">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="dc286-393">O arquivo *web.config* pode fornecer configurações adicionais do IIS que controlam módulos ativos do IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-393">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="dc286-394">Para saber mais sobre os módulos do IIS que podem processar solicitações com aplicativos do ASP.NET Core, veja o tópico [Módulos do IIS](xref:host-and-deploy/iis/modules).</span><span class="sxs-lookup"><span data-stu-id="dc286-394">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="dc286-395">Para impedir que o SDK da Web transforme o arquivo *Web. config* , use a **\<IsTransformWebConfigDisabled>** propriedade no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="dc286-395">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="dc286-396">Ao impedir que o SDK Web transforme o arquivo, o *processPath* e os *argumentos* devem ser definidos manualmente pelo desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="dc286-396">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="dc286-397">Para obter mais informações, consulte <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="dc286-397">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="dc286-398">Local do arquivo web.config</span><span class="sxs-lookup"><span data-stu-id="dc286-398">web.config file location</span></span>

<span data-ttu-id="dc286-399">Para configurar o [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) corretamente, o arquivo *Web. config* deve estar presente no caminho [raiz do conteúdo](xref:fundamentals/index#content-root) (normalmente o caminho base do aplicativo) do aplicativo implantado.</span><span class="sxs-lookup"><span data-stu-id="dc286-399">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="dc286-400">Esse é o mesmo local que o caminho físico do site fornecido ao IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-400">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="dc286-401">O arquivo *web.config* é necessário na raiz do aplicativo para habilitar a publicação de vários aplicativos usando a Implantação da Web.</span><span class="sxs-lookup"><span data-stu-id="dc286-401">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="dc286-402">Existem arquivos confidenciais no caminho físico do aplicativo, como \* \<assembly> . runtimeconfig. JSON*, \* \<assembly> . xml* (comentários de documentação XML) e \* \<assembly> . deps. JSON\*.</span><span class="sxs-lookup"><span data-stu-id="dc286-402">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="dc286-403">Quando o arquivo *web.config* estiver presente e o site for iniciado normalmente, o IIS não atenderá a esses arquivos confidenciais se eles forem solicitados.</span><span class="sxs-lookup"><span data-stu-id="dc286-403">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="dc286-404">Se o arquivo *web.config* estiver ausente, nomeado incorretamente ou se não for possível configurar o site para inicialização normal, o IIS poderá servir arquivos confidenciais publicamente.</span><span class="sxs-lookup"><span data-stu-id="dc286-404">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="dc286-405">**O arquivo *Web. config* deve estar presente na implantação o tempo todo, corretamente nomeado e ser capaz de configurar o site para inicialização normal. Nunca remova o arquivo *Web. config* de uma implantação de produção.**</span><span class="sxs-lookup"><span data-stu-id="dc286-405">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="dc286-406">Transformação do Web.config</span><span class="sxs-lookup"><span data-stu-id="dc286-406">Transform web.config</span></span>

<span data-ttu-id="dc286-407">Se você precisar transformar *Web. config* em publicar, consulte <xref:host-and-deploy/iis/transform-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="dc286-407">If you need to transform *web.config* on publish, see <xref:host-and-deploy/iis/transform-webconfig>.</span></span> <span data-ttu-id="dc286-408">Talvez seja necessário transformar o *Web. config* em publicar para definir variáveis de ambiente com base na configuração, no perfil ou no ambiente.</span><span class="sxs-lookup"><span data-stu-id="dc286-408">You might need to transform *web.config* on publish to set environment variables based on the configuration, profile, or environment.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="dc286-409">Configuração do IIS</span><span class="sxs-lookup"><span data-stu-id="dc286-409">IIS configuration</span></span>

<span data-ttu-id="dc286-410">**Sistemas operacionais do Windows Server**</span><span class="sxs-lookup"><span data-stu-id="dc286-410">**Windows Server operating systems**</span></span>

<span data-ttu-id="dc286-411">Habilite a função **Servidor Web (IIS)** e estabeleça serviços de função.</span><span class="sxs-lookup"><span data-stu-id="dc286-411">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="dc286-412">Use o assistente **Adicionar Funções e Recursos** por meio do menu **Gerenciar** ou do link no **Gerenciador do Servidor**.</span><span class="sxs-lookup"><span data-stu-id="dc286-412">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="dc286-413">Na etapa **Funções de Servidor**, marque a caixa de **Servidor Web (IIS)**.</span><span class="sxs-lookup"><span data-stu-id="dc286-413">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![A função de Servidor Web IIS é selecionada na etapa Selecionar funções de servidor.](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="dc286-415">Após a etapa **Recursos**, a etapa **Serviços de função** é carregada para o servidor Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="dc286-415">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="dc286-416">Selecione os serviços de função do IIS desejados ou aceite os serviços de função padrão fornecidos.</span><span class="sxs-lookup"><span data-stu-id="dc286-416">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![Os serviços de função padrão são selecionados na etapa Selecionar serviços de função.](index/_static/role-services-ws2016.png)

   <span data-ttu-id="dc286-418">**Autenticação do Windows (opcional)**</span><span class="sxs-lookup"><span data-stu-id="dc286-418">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="dc286-419">Para habilitar a autenticação do Windows, expanda os seguintes nós: segurança do **servidor Web**  >  **Security**.</span><span class="sxs-lookup"><span data-stu-id="dc286-419">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="dc286-420">Selecione o recurso **Autenticação do Windows**.</span><span class="sxs-lookup"><span data-stu-id="dc286-420">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="dc286-421">Para obter mais informações, consulte [autenticação \<windowsAuthentication> do Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) e [Configurar a autenticação do Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="dc286-421">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="dc286-422">**WebSockets (opcional)**</span><span class="sxs-lookup"><span data-stu-id="dc286-422">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="dc286-423">O WebSockets é compatível com o ASP.NET Core 1.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="dc286-423">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="dc286-424">Para habilitar WebSockets, expanda os seguintes nós: desenvolvimento de aplicativo de **servidor Web**  >  **Application Development**.</span><span class="sxs-lookup"><span data-stu-id="dc286-424">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="dc286-425">Selecione o recurso **Protocolo WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="dc286-425">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="dc286-426">Para obter mais informações, consulte [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="dc286-426">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="dc286-427">Continue para a etapa **Confirmação** para instalar os serviços e a função de servidor Web.</span><span class="sxs-lookup"><span data-stu-id="dc286-427">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="dc286-428">Uma reinicialização de servidor/IIS não é necessária após a instalação da função do **servidor Web (IIS)** .</span><span class="sxs-lookup"><span data-stu-id="dc286-428">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="dc286-429">**Sistemas operacionais de área de trabalho do Windows**</span><span class="sxs-lookup"><span data-stu-id="dc286-429">**Windows desktop operating systems**</span></span>

<span data-ttu-id="dc286-430">Habilite o **Console de Gerenciamento do IIS** e os **Serviços na World Wide Web**.</span><span class="sxs-lookup"><span data-stu-id="dc286-430">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="dc286-431">Navegue até **Painel de Controle** > **Programas** > **Programas e Recursos** > **Ativar ou desativar recursos do Windows** (lado esquerdo da tela).</span><span class="sxs-lookup"><span data-stu-id="dc286-431">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="dc286-432">Abra o nó **Serviços de Informações da Internet**.</span><span class="sxs-lookup"><span data-stu-id="dc286-432">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="dc286-433">Abra o nó **Ferramentas de Gerenciamento da Web**.</span><span class="sxs-lookup"><span data-stu-id="dc286-433">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="dc286-434">Marque a caixa de **Console de Gerenciamento do IIS**.</span><span class="sxs-lookup"><span data-stu-id="dc286-434">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="dc286-435">Marque a caixa de **Serviços na World Wide Web**.</span><span class="sxs-lookup"><span data-stu-id="dc286-435">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="dc286-436">Aceite os recursos padrão dos **Serviços na World Wide Web** ou personalize os recursos do IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-436">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="dc286-437">**Autenticação do Windows (opcional)**</span><span class="sxs-lookup"><span data-stu-id="dc286-437">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="dc286-438">Para habilitar a autenticação do Windows, expanda os seguintes nós: segurança **dos serviços de World Wide Web**  >  **Security**.</span><span class="sxs-lookup"><span data-stu-id="dc286-438">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="dc286-439">Selecione o recurso **Autenticação do Windows**.</span><span class="sxs-lookup"><span data-stu-id="dc286-439">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="dc286-440">Para obter mais informações, consulte [autenticação \<windowsAuthentication> do Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) e [Configurar a autenticação do Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="dc286-440">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="dc286-441">**WebSockets (opcional)**</span><span class="sxs-lookup"><span data-stu-id="dc286-441">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="dc286-442">O WebSockets é compatível com o ASP.NET Core 1.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="dc286-442">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="dc286-443">Para habilitar WebSockets, expanda os seguintes nós: recursos de desenvolvimento de aplicativos **World Wide Web Services**  >  **Application Development Features**.</span><span class="sxs-lookup"><span data-stu-id="dc286-443">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="dc286-444">Selecione o recurso **Protocolo WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="dc286-444">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="dc286-445">Para obter mais informações, consulte [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="dc286-445">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="dc286-446">Se a instalação do IIS exigir uma reinicialização, reinicie o sistema.</span><span class="sxs-lookup"><span data-stu-id="dc286-446">If the IIS installation requires a restart, restart the system.</span></span>

![O Console de Gerenciamento do IIS e os Serviços na World Wide Web estão selecionados em Recursos do Windows.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="dc286-448">Instalar o pacote de hospedagem do .NET Core</span><span class="sxs-lookup"><span data-stu-id="dc286-448">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="dc286-449">Instale o *pacote de hospedagem do .NET Core* no sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="dc286-449">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="dc286-450">O pacote instala o tempo de execução do .NET Core, a biblioteca do .NET Core e o [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="dc286-450">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="dc286-451">O módulo permite que aplicativos do ASP.NET Core sejam executados por trás do IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-451">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="dc286-452">Se o pacote de hospedagem for instalado antes do IIS, a instalação do pacote deverá ser reparada.</span><span class="sxs-lookup"><span data-stu-id="dc286-452">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="dc286-453">Execute o instalador do pacote de hospedagem novamente depois de instalar o IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-453">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="dc286-454">Se o pacote de hospedagem for instalado após a instalação da versão de 64 bits (x64) do .NET Core, os SDKs poderão parecer estar ausentes ([Nenhum SDK do .NET Core foi detectado](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="dc286-454">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="dc286-455">Para resolver o problema, consulte <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span><span class="sxs-lookup"><span data-stu-id="dc286-455">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="dc286-456">Download direto (versão atual)</span><span class="sxs-lookup"><span data-stu-id="dc286-456">Direct download (current version)</span></span>

<span data-ttu-id="dc286-457">Baixe o instalador usando o seguinte link:</span><span class="sxs-lookup"><span data-stu-id="dc286-457">Download the installer using the following link:</span></span>

[<span data-ttu-id="dc286-458">Instalador de pacote de hospedagem do .NET Core atual (download direto)</span><span class="sxs-lookup"><span data-stu-id="dc286-458">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="dc286-459">Versões anteriores do instalador</span><span class="sxs-lookup"><span data-stu-id="dc286-459">Earlier versions of the installer</span></span>

<span data-ttu-id="dc286-460">Para obter uma versão anterior do instalador:</span><span class="sxs-lookup"><span data-stu-id="dc286-460">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="dc286-461">Navegue até a página [baixar o .NET Core](https://dotnet.microsoft.com/download/dotnet-core) .</span><span class="sxs-lookup"><span data-stu-id="dc286-461">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="dc286-462">Selecione a versão do .NET Core desejada.</span><span class="sxs-lookup"><span data-stu-id="dc286-462">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="dc286-463">Na coluna **Executar aplicativos – runtime**, localize a linha da versão de runtime do .NET Core desejada.</span><span class="sxs-lookup"><span data-stu-id="dc286-463">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="dc286-464">Baixe o instalador usando o link **pacote de hospedagem** .</span><span class="sxs-lookup"><span data-stu-id="dc286-464">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="dc286-465">Alguns instaladores contêm versões de lançamento que atingiram o EOL (fim da vida útil) e não têm mais suporte da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="dc286-465">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="dc286-466">Para saber mais, confira a [política de suporte](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="dc286-466">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="dc286-467">Instalar o pacote de hospedagem</span><span class="sxs-lookup"><span data-stu-id="dc286-467">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="dc286-468">Execute o instalador no servidor.</span><span class="sxs-lookup"><span data-stu-id="dc286-468">Run the installer on the server.</span></span> <span data-ttu-id="dc286-469">Os parâmetros a seguir estão disponíveis ao executar o instalador por meio de um shell de comando do administrador:</span><span class="sxs-lookup"><span data-stu-id="dc286-469">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="dc286-470">`OPT_NO_ANCM=1`: Ignorar a instalação do módulo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dc286-470">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="dc286-471">`OPT_NO_RUNTIME=1`: Ignorar a instalação do tempo de execução do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="dc286-471">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="dc286-472">Usado quando o servidor hospeda apenas [implantações independentes (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="dc286-472">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="dc286-473">`OPT_NO_SHAREDFX=1`: Ignorar a instalação da estrutura compartilhada ASP.NET (ASP.NET Runtime).</span><span class="sxs-lookup"><span data-stu-id="dc286-473">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="dc286-474">Usado quando o servidor hospeda apenas [implantações independentes (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="dc286-474">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="dc286-475">`OPT_NO_X86=1`: Ignorar a instalação de tempos de execução x86.</span><span class="sxs-lookup"><span data-stu-id="dc286-475">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="dc286-476">Use esse parâmetro quando você souber que não hospedará aplicativos de 32 bits.</span><span class="sxs-lookup"><span data-stu-id="dc286-476">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="dc286-477">Se houver uma possibilidade de hospedar aplicativos de 32 bits e 64 bits no futuro, não use esse parâmetro e instale ambos os runtimes.</span><span class="sxs-lookup"><span data-stu-id="dc286-477">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="dc286-478">`OPT_NO_SHARED_CONFIG_CHECK=1`: Desabilite a verificação para usar uma configuração compartilhada do IIS quando a configuração compartilhada (*ApplicationHost. config*) estiver no mesmo computador que a instalação do IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-478">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="dc286-479">*Disponível somente para instaladores do ASP.NET Core 2.2 ou Hosting Bundler posterior.*</span><span class="sxs-lookup"><span data-stu-id="dc286-479">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="dc286-480">Para obter mais informações, consulte <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="dc286-480">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="dc286-481">Reinicie o sistema ou execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="dc286-481">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="dc286-482">A reinicialização do IIS identifica uma alteração no CAMINHO do sistema, que é uma variável de ambiente, realizada pelo instalador.</span><span class="sxs-lookup"><span data-stu-id="dc286-482">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="dc286-483">ASP.NET Core não adota o comportamento de roll-forward para versões de patch de pacotes de estrutura compartilhados.</span><span class="sxs-lookup"><span data-stu-id="dc286-483">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="dc286-484">Depois de atualizar a estrutura compartilhada instalando um novo pacote de hospedagem, reinicie o sistema ou execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="dc286-484">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="dc286-485">Para obter informações sobre a Configuração Compartilhada do IIS, consulte [Módulo do ASP.NET Core com a Configuração Compartilhada do IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="dc286-485">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="dc286-486">Instalar a Implantação da Web durante a publicação com o Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dc286-486">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="dc286-487">Ao implantar aplicativos para servidores com [Implantação da Web](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), instale a versão mais recente da Implantação da Web no servidor.</span><span class="sxs-lookup"><span data-stu-id="dc286-487">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="dc286-488">Para instalar a Implantação da Web, use o [WebPI (Web Platform Installer)](https://www.microsoft.com/web/downloads/platform.aspx) ou obtenha um instalador diretamente no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=43717).</span><span class="sxs-lookup"><span data-stu-id="dc286-488">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="dc286-489">O método preferencial é usar o WebPI.</span><span class="sxs-lookup"><span data-stu-id="dc286-489">The preferred method is to use WebPI.</span></span> <span data-ttu-id="dc286-490">O WebPI oferece uma instalação autônoma e uma configuração para provedores de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="dc286-490">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="dc286-491">Criar o site do IIS</span><span class="sxs-lookup"><span data-stu-id="dc286-491">Create the IIS site</span></span>

1. <span data-ttu-id="dc286-492">No sistema de hospedagem, crie uma pasta para conter arquivos e pastas publicados do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-492">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="dc286-493">Em uma etapa a seguir, o caminho da pasta é fornecido ao IIS como o caminho físico para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-493">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="dc286-494">Para obter mais informações sobre o layout de arquivo e a pasta de implantação de um aplicativo, confira <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="dc286-494">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="dc286-495">No Gerenciador do IIS, abra o nó do servidor no painel **conexões** .</span><span class="sxs-lookup"><span data-stu-id="dc286-495">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="dc286-496">Clique com botão direito do mouse na pasta **Sites**.</span><span class="sxs-lookup"><span data-stu-id="dc286-496">Right-click the **Sites** folder.</span></span> <span data-ttu-id="dc286-497">Selecione **Adicionar Site** no menu contextual.</span><span class="sxs-lookup"><span data-stu-id="dc286-497">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="dc286-498">Forneça um **Nome do site** e defina o **Caminho físico** como a pasta de implantação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-498">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="dc286-499">Forneça a configuração de **Associação** e crie o site selecionando **OK**:</span><span class="sxs-lookup"><span data-stu-id="dc286-499">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![Forneça o Nome do site, o caminho físico e o Nome do host na etapa Adicionar Site.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="dc286-501">Associações de curinga de nível superior (`http://*:80/` e `http://+:80`) **não** devem ser usadas.</span><span class="sxs-lookup"><span data-stu-id="dc286-501">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="dc286-502">Associações de curinga de nível superior podem abrir o aplicativo para vulnerabilidades de segurança.</span><span class="sxs-lookup"><span data-stu-id="dc286-502">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="dc286-503">Isso se aplica a curingas fortes e fracos.</span><span class="sxs-lookup"><span data-stu-id="dc286-503">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="dc286-504">Use nomes de host explícitos em vez de curingas.</span><span class="sxs-lookup"><span data-stu-id="dc286-504">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="dc286-505">Associações de curinga de subdomínio (por exemplo, `*.mysub.com`) não têm esse risco de segurança se você controlar o domínio pai completo (em vez de `*.com`, o qual é vulnerável).</span><span class="sxs-lookup"><span data-stu-id="dc286-505">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="dc286-506">Veja [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="dc286-506">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="dc286-507">No nó do servidor, selecione **Pools de Aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="dc286-507">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="dc286-508">Clique com o botão direito do mouse no pool de aplicativos do site e selecione **Configurações Básicas** no menu contextual.</span><span class="sxs-lookup"><span data-stu-id="dc286-508">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="dc286-509">Na janela **Editar Pool de Aplicativos**, defina a **versão do CLR do .NET** como **Sem Código Gerenciado**:</span><span class="sxs-lookup"><span data-stu-id="dc286-509">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![Defina Sem Código Gerenciado para a versão do CLR do .NET.](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="dc286-511">O ASP.NET Core é executado em um processo separado e gerencia o runtime.</span><span class="sxs-lookup"><span data-stu-id="dc286-511">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="dc286-512">ASP.NET Core não depende do carregamento do CLR da área de trabalho (.NET CLR).</span><span class="sxs-lookup"><span data-stu-id="dc286-512">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR).</span></span> <span data-ttu-id="dc286-513">O principal CoreCLR (Common Language Runtime) para .NET Core é inicializado para hospedar o aplicativo no processo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="dc286-513">The Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="dc286-514">Definir a **versão do CLR do .NET** como **Sem Código Gerenciado** é opcional, porém recomendado.</span><span class="sxs-lookup"><span data-stu-id="dc286-514">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="dc286-515">*ASP.NET Core 2.2 ou posterior*:</span><span class="sxs-lookup"><span data-stu-id="dc286-515">*ASP.NET Core 2.2 or later*:</span></span>

   * <span data-ttu-id="dc286-516">Para uma [implantação autônoma](/dotnet/core/deploying/#self-contained-deployments-scd) de 32 bits (x86) publicada com um SDK de 32 bits que usa o modelo de [hospedagem em processo](#in-process-hosting-model), habilite o Pool de aplicativos para 32 bits.</span><span class="sxs-lookup"><span data-stu-id="dc286-516">For a 32-bit (x86) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) published with a 32-bit SDK that uses the [in-process hosting model](#in-process-hosting-model), enable the Application Pool for 32-bit.</span></span> <span data-ttu-id="dc286-517">No Gerenciador do IIS, navegue até **pools de aplicativos** na barra lateral **conexões** .</span><span class="sxs-lookup"><span data-stu-id="dc286-517">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="dc286-518">Selecione o pool de aplicativos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-518">Select the app's Application Pool.</span></span> <span data-ttu-id="dc286-519">Na barra lateral **ações** , selecione **Configurações avançadas**.</span><span class="sxs-lookup"><span data-stu-id="dc286-519">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="dc286-520">Defina **habilitar aplicativos de 32 bits** como `True` .</span><span class="sxs-lookup"><span data-stu-id="dc286-520">Set **Enable 32-Bit Applications** to `True`.</span></span> 

   * <span data-ttu-id="dc286-521">para uma [implantação autocontida](/dotnet/core/deploying/#self-contained-deployments-scd) de 64 bits (x64) que usa o [modelo de hospedagem em processo](#in-process-hosting-model), desabilite o pool de aplicativos para processos de 32 bits (x86).</span><span class="sxs-lookup"><span data-stu-id="dc286-521">For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span> <span data-ttu-id="dc286-522">No Gerenciador do IIS, navegue até **pools de aplicativos** na barra lateral **conexões** .</span><span class="sxs-lookup"><span data-stu-id="dc286-522">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="dc286-523">Selecione o pool de aplicativos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-523">Select the app's Application Pool.</span></span> <span data-ttu-id="dc286-524">Na barra lateral **ações** , selecione **Configurações avançadas**.</span><span class="sxs-lookup"><span data-stu-id="dc286-524">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="dc286-525">Defina **habilitar aplicativos de 32 bits** como `False` .</span><span class="sxs-lookup"><span data-stu-id="dc286-525">Set **Enable 32-Bit Applications** to `False`.</span></span> 

1. <span data-ttu-id="dc286-526">Confirme se a identidade do modelo de processo tem as permissões apropriadas.</span><span class="sxs-lookup"><span data-stu-id="dc286-526">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="dc286-527">Se a identidade padrão do pool de aplicativos (**modelo de processo**  >  **Identity** ) for alterada de **ApplicationPoolIdentity** para outra identidade, verifique se a nova identidade tem as permissões necessárias para acessar a pasta, o banco de dados e outros recursos necessários do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-527">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="dc286-528">Por exemplo, o pool de aplicativos requer acesso de leitura e gravação às pastas nas quais o aplicativo lê e grava os arquivos.</span><span class="sxs-lookup"><span data-stu-id="dc286-528">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="dc286-529">**Configuração de Autenticação do Windows (opcional)**</span><span class="sxs-lookup"><span data-stu-id="dc286-529">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="dc286-530">Para saber mais, veja [Configurar a Autenticação do Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="dc286-530">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="dc286-531">Implantar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="dc286-531">Deploy the app</span></span>

<span data-ttu-id="dc286-532">Implante o aplicativo na pasta **caminho físico** do IIS que foi estabelecida na seção [Criar o site do IIS](#create-the-iis-site).</span><span class="sxs-lookup"><span data-stu-id="dc286-532">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="dc286-533">[Implantação da Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) é o mecanismo recomendado para implantação, mas existem várias opções para mover o aplicativo da pasta *publicar* do projeto para a pasta de implantação do sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="dc286-533">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="dc286-534">Implantação da Web com o Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dc286-534">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="dc286-535">Consulte o tópico [Perfis de publicação do Visual Studio para implantação de aplicativos ASP.NET Core](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) para saber como criar um perfil de publicação para uso com a Implantação da Web.</span><span class="sxs-lookup"><span data-stu-id="dc286-535">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="dc286-536">Se o provedor de hospedagem fornecer um Perfil de Publicação ou o suporte para a criação de um, baixe o perfil e importe-o usando a caixa de diálogo **Publicar** do Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="dc286-536">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![Página de caixa de diálogo Publicar](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="dc286-538">Implantação da Web fora do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dc286-538">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="dc286-539">Também é possível usar a [Implantação da Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) fora do Visual Studio na linha de comando.</span><span class="sxs-lookup"><span data-stu-id="dc286-539">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="dc286-540">Para obter mais informações, consulte [Ferramenta de Implantação da Web](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span><span class="sxs-lookup"><span data-stu-id="dc286-540">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="dc286-541">Alternativas à Implantação da Web</span><span class="sxs-lookup"><span data-stu-id="dc286-541">Alternatives to Web Deploy</span></span>

<span data-ttu-id="dc286-542">Use qualquer um dos vários métodos para mover o aplicativo para o sistema host, como cópia manual, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy) ou [PowerShell](/powershell/).</span><span class="sxs-lookup"><span data-stu-id="dc286-542">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="dc286-543">Para obter mais informações sobre a implantação do ASP.NET Core no IIS, consulte a seção [Recursos de implantação para administradores do IIS](#deployment-resources-for-iis-administrators).</span><span class="sxs-lookup"><span data-stu-id="dc286-543">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="dc286-544">Procurar no site</span><span class="sxs-lookup"><span data-stu-id="dc286-544">Browse the website</span></span>

<span data-ttu-id="dc286-545">Depois de implantar o aplicativo no sistema de hospedagem, faça uma solicitação para um dos pontos de extremidade públicos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-545">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="dc286-546">No exemplo a seguir, o site está associado a um **Nome do Host** IIS de `www.mysite.com` na **Porta** `80`.</span><span class="sxs-lookup"><span data-stu-id="dc286-546">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="dc286-547">É feita uma solicitação para `http://www.mysite.com`:</span><span class="sxs-lookup"><span data-stu-id="dc286-547">A request is made to `http://www.mysite.com`:</span></span>

![O navegador Microsoft Edge carregou a página de inicialização do IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="dc286-549">Arquivos de implantação bloqueados</span><span class="sxs-lookup"><span data-stu-id="dc286-549">Locked deployment files</span></span>

<span data-ttu-id="dc286-550">Os arquivos na pasta de implantação são bloqueados quando o aplicativo está em execução.</span><span class="sxs-lookup"><span data-stu-id="dc286-550">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="dc286-551">Os arquivos bloqueados não podem ser substituídos durante a implantação.</span><span class="sxs-lookup"><span data-stu-id="dc286-551">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="dc286-552">Para liberar os arquivos bloqueados em uma implantação, interrompa o pool de aplicativos usando **uma** das abordagens a seguir:</span><span class="sxs-lookup"><span data-stu-id="dc286-552">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="dc286-553">Use a Implantação da Web e referencie `Microsoft.NET.Sdk.Web` no arquivo do projeto.</span><span class="sxs-lookup"><span data-stu-id="dc286-553">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="dc286-554">Um arquivo *app_offline.htm* é colocado na raiz do diretório de aplicativo da Web.</span><span class="sxs-lookup"><span data-stu-id="dc286-554">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="dc286-555">Quando o arquivo estiver presente, o módulo do ASP.NET Core apenas desligará o aplicativo e servirá o arquivo *app_offline.htm* durante a implantação.</span><span class="sxs-lookup"><span data-stu-id="dc286-555">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="dc286-556">Para obter mais informações, consulte [Referência de configuração do módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="dc286-556">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="dc286-557">Manualmente interrompa o pool de aplicativos no Gerenciador do IIS no servidor.</span><span class="sxs-lookup"><span data-stu-id="dc286-557">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="dc286-558">Use o PowerShell para descartar *App_offline. htm* (requer o PowerShell 5 ou posterior):</span><span class="sxs-lookup"><span data-stu-id="dc286-558">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="dc286-559">Proteção de dados</span><span class="sxs-lookup"><span data-stu-id="dc286-559">Data protection</span></span>

<span data-ttu-id="dc286-560">A [pilha Proteção de Dados do ASP.NET Core](xref:security/data-protection/introduction) é usada por vários [middlewares](xref:fundamentals/middleware/index) ASP.NET Core, incluindo aqueles usados na autenticação.</span><span class="sxs-lookup"><span data-stu-id="dc286-560">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="dc286-561">Mesmo se as APIs de proteção de dados não forem chamadas pelo código do usuário, a proteção de dados deverá ser configurada com um script de implantação ou no código do usuário para criar um [repositório de chaves](xref:security/data-protection/implementation/key-management) criptográfico persistente.</span><span class="sxs-lookup"><span data-stu-id="dc286-561">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="dc286-562">Se a proteção de dados não estiver configurada, as chaves serão mantidas na memória e descartadas quando o aplicativo for reiniciado.</span><span class="sxs-lookup"><span data-stu-id="dc286-562">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="dc286-563">Se o token de autenticação for armazenado na memória quando o aplicativo for reiniciado:</span><span class="sxs-lookup"><span data-stu-id="dc286-563">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="dc286-564">Todos os tokens de autenticação baseados em cookies serão invalidados.</span><span class="sxs-lookup"><span data-stu-id="dc286-564">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="dc286-565">Os usuários precisam entrar novamente na próxima solicitação deles.</span><span class="sxs-lookup"><span data-stu-id="dc286-565">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="dc286-566">Todos os dados protegidos com o token de autenticação não poderão mais ser descriptografados.</span><span class="sxs-lookup"><span data-stu-id="dc286-566">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="dc286-567">Isso pode incluir [tokens CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) e [ASP.NET Core cookies TempData do MVC](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="dc286-567">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="dc286-568">Para configurar a proteção de dados no IIS para persistir o token de autenticação, use **uma** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="dc286-568">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="dc286-569">**Criar chaves de registro de proteção de dados**</span><span class="sxs-lookup"><span data-stu-id="dc286-569">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="dc286-570">As chaves de proteção de dados usadas pelos aplicativos ASP.NET Core são armazenadas no registro externo aos aplicativos.</span><span class="sxs-lookup"><span data-stu-id="dc286-570">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="dc286-571">Para persistir as chaves de determinado aplicativo, crie uma chave de registro para o pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="dc286-571">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="dc286-572">Para instalações autônomas do IIS não Web Farm, você pode usar o [script Provision-AutoGenKeys.ps1 de Proteção de Dados do PowerShell](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) para cada pool de aplicativos usado com um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dc286-572">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="dc286-573">Esse script cria uma chave de registro no registro HKLM que é acessível apenas para a conta do processo de trabalho do pool de aplicativos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-573">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="dc286-574">As chaves são criptografadas em repouso usando a DPAPI com uma chave para todo o computador.</span><span class="sxs-lookup"><span data-stu-id="dc286-574">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="dc286-575">Em cenários de web farm, um aplicativo pode ser configurado para usar um caminho UNC para armazenar seu token de autenticação de proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="dc286-575">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="dc286-576">Por padrão, as chaves de proteção de dados não são criptografadas.</span><span class="sxs-lookup"><span data-stu-id="dc286-576">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="dc286-577">Garanta que as permissões de arquivo de o compartilhamento de rede sejam limitadas à conta do Windows na qual o aplicativo é executado.</span><span class="sxs-lookup"><span data-stu-id="dc286-577">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="dc286-578">Um certificado X509 pode ser usado para proteger chaves em repouso.</span><span class="sxs-lookup"><span data-stu-id="dc286-578">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="dc286-579">Considere um mecanismo para permitir aos usuários carregar certificados: coloque os certificados no repositório de certificados confiáveis do usuário e certifique-se de que eles estejam disponíveis em todos os computadores nos quais o aplicativo do usuário é executado.</span><span class="sxs-lookup"><span data-stu-id="dc286-579">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="dc286-580">Veja [Configurar a proteção de dados do ASP.NET Core](xref:security/data-protection/configuration/overview) para obter detalhes.</span><span class="sxs-lookup"><span data-stu-id="dc286-580">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="dc286-581">**Configurar o pool de aplicativos do IIS para carregar o perfil do usuário**</span><span class="sxs-lookup"><span data-stu-id="dc286-581">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="dc286-582">Essa configuração está na seção **Modelo de processo** nas **Configurações avançadas** do pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="dc286-582">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="dc286-583">Defina **carregar perfil do usuário** como `True` .</span><span class="sxs-lookup"><span data-stu-id="dc286-583">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="dc286-584">Quando definido como `True`, as chaves são armazenadas no diretório do perfil do usuário e protegidas usando DPAPI com uma chave específica para a conta de usuário.</span><span class="sxs-lookup"><span data-stu-id="dc286-584">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="dc286-585">As chaves são persistidas para a pasta *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys*.</span><span class="sxs-lookup"><span data-stu-id="dc286-585">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="dc286-586">O [atributo setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) do pool de aplicativos também deve ser habilitado.</span><span class="sxs-lookup"><span data-stu-id="dc286-586">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="dc286-587">O valor padrão de `setProfileEnvironment` é `true`.</span><span class="sxs-lookup"><span data-stu-id="dc286-587">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="dc286-588">Em alguns cenários (por exemplo, um SO Windows), `setProfileEnvironment` é definido como `false`.</span><span class="sxs-lookup"><span data-stu-id="dc286-588">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="dc286-589">Se as chaves não estiverem armazenadas no diretório do perfil do usuário como esperado:</span><span class="sxs-lookup"><span data-stu-id="dc286-589">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="dc286-590">navegue até a pasta *%windir%/system32/inetsrv/config*.</span><span class="sxs-lookup"><span data-stu-id="dc286-590">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="dc286-591">Abra o arquivo *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="dc286-591">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="dc286-592">Localize o elemento `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.</span><span class="sxs-lookup"><span data-stu-id="dc286-592">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="dc286-593">Confirme se o atributo `setProfileEnvironment` não está presente, que tem como padrão o valor `true`, ou defina explicitamente o valor do atributo como `true`.</span><span class="sxs-lookup"><span data-stu-id="dc286-593">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="dc286-594">**Usar o sistema de arquivos como um repositório de tokens de autenticação**</span><span class="sxs-lookup"><span data-stu-id="dc286-594">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="dc286-595">Ajuste o código do aplicativo para [usar o sistema de arquivos como um repositório de tokens de autenticação](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="dc286-595">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="dc286-596">Use um certificado X509 para proteger o token de autenticação e verifique se ele é um certificado confiável.</span><span class="sxs-lookup"><span data-stu-id="dc286-596">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="dc286-597">Se o certificado for autoassinado, você deverá colocá-lo no repositório Raiz confiável.</span><span class="sxs-lookup"><span data-stu-id="dc286-597">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="dc286-598">Ao usar o IIS em uma web farm:</span><span class="sxs-lookup"><span data-stu-id="dc286-598">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="dc286-599">Use um compartilhamento de arquivos que pode ser acessado por todos os computadores.</span><span class="sxs-lookup"><span data-stu-id="dc286-599">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="dc286-600">Implante um certificado X509 em cada computador.</span><span class="sxs-lookup"><span data-stu-id="dc286-600">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="dc286-601">Configure a [proteção de dados no código](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="dc286-601">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="dc286-602">**Definir uma política para todo o computador para proteção de dados**</span><span class="sxs-lookup"><span data-stu-id="dc286-602">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="dc286-603">O sistema de proteção de dados tem suporte limitado para a configuração da [política de todo o computador](xref:security/data-protection/configuration/machine-wide-policy) padrão para todos os aplicativos que consomem as APIs de proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="dc286-603">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="dc286-604">Para obter mais informações, consulte <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="dc286-604">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="dc286-605">Diretórios virtuais</span><span class="sxs-lookup"><span data-stu-id="dc286-605">Virtual Directories</span></span>

<span data-ttu-id="dc286-606">[Diretórios virtuais IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) não são compatíveis com aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dc286-606">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="dc286-607">Um aplicativo pode ser hospedado como um [subaplicativo](#sub-applications).</span><span class="sxs-lookup"><span data-stu-id="dc286-607">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="dc286-608">Subaplicativos</span><span class="sxs-lookup"><span data-stu-id="dc286-608">Sub-applications</span></span>

<span data-ttu-id="dc286-609">Um aplicativo ASP.NET Core pode ser hospedado como um [subaplicativo IIS (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span><span class="sxs-lookup"><span data-stu-id="dc286-609">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="dc286-610">A parte do caminho do subaplicativo se torna parte da URL raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-610">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="dc286-611">Links de ativos estáticos dentro do subaplicativo devem usar a notação de sinal de til e barra (`~/`).</span><span class="sxs-lookup"><span data-stu-id="dc286-611">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="dc286-612">A notação de sinal de til e barra aciona um [Auxiliar de Marca](xref:mvc/views/tag-helpers/intro) para preceder a base de caminho do subaplicativo ao link relativo renderizado.</span><span class="sxs-lookup"><span data-stu-id="dc286-612">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="dc286-613">Para um subaplicativo no `/subapp_path`, uma imagem vinculada com `src="~/image.png"` é renderizada como `src="/subapp_path/image.png"`.</span><span class="sxs-lookup"><span data-stu-id="dc286-613">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="dc286-614">O Middleware de Arquivo Estático do aplicativo raiz não processa a solicitação de arquivo estático.</span><span class="sxs-lookup"><span data-stu-id="dc286-614">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="dc286-615">A solicitação é processada pelo Middleware de Arquivo Estático do subaplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-615">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="dc286-616">Se um atributo de ativo estático `src` for definido como um caminho absoluto (por exemplo, `src="/image.png"`), o link será renderizado sem a base de caminho do subaplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-616">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="dc286-617">O Middleware de Arquivos Estáticos do aplicativo raiz tenta fornecer o ativo do [webroot](xref:fundamentals/index#web-root) da raiz do aplicativo, que resulta em uma resposta *404 – Não encontrado*, a menos que o ativo estático esteja disponível no aplicativo raiz.</span><span class="sxs-lookup"><span data-stu-id="dc286-617">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="dc286-618">Para hospedar um aplicativo ASP.NET Core como um subaplicativo em outro aplicativo do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="dc286-618">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="dc286-619">Estabeleça um pool de aplicativos para o subaplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-619">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="dc286-620">Defina a **versão do CLR do .NET** como **Sem Código Gerenciado** porque o Core Common Language Runtime (CoreCLR) do .NET Core é inicializado para hospedar o aplicativo no processo de trabalho, não no CLR de Área de trabalho (CLR do .NET).</span><span class="sxs-lookup"><span data-stu-id="dc286-620">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="dc286-621">Adicione o site raiz no Gerenciador do IIS com o subaplicativo em uma pasta no site raiz.</span><span class="sxs-lookup"><span data-stu-id="dc286-621">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="dc286-622">Clique com o botão direito do mouse na pasta do subaplicativo no Gerenciador do IIS e selecione **Converter em aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="dc286-622">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="dc286-623">Na caixa de diálogo **Adicionar Aplicativo**, use o botão **Selecionar** no **Pool de Aplicativos** para atribuir o pool de aplicativos que você criou ao subaplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-623">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="dc286-624">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="dc286-624">Select **OK**.</span></span>

<span data-ttu-id="dc286-625">A atribuição de um pool de aplicativos separado para o subaplicativo é um requisito ao usar o modelo de hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="dc286-625">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="dc286-626">Para obter mais informações sobre o modelo de hospedagem em processo e como configurar o módulo ASP.NET Core, consulte <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="dc286-626">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="dc286-627">Configuração do IIS com web.config</span><span class="sxs-lookup"><span data-stu-id="dc286-627">Configuration of IIS with web.config</span></span>

<span data-ttu-id="dc286-628">A configuração do IIS é influenciada pela seção `<system.webServer>` do *web.config* para cenários do IIS que são funcionais para aplicativos ASP.NET Core com o Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dc286-628">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="dc286-629">Por exemplo, a configuração do IIS é funcional para a compactação dinâmica.</span><span class="sxs-lookup"><span data-stu-id="dc286-629">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="dc286-630">Se o IIS for configurado no nível do servidor para usar a compactação dinâmica, o elemento `<urlCompression>` no arquivo *web.config* do aplicativo pode desabilitá-la para um aplicativo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dc286-630">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="dc286-631">Para obter mais informações, consulte estes tópicos:</span><span class="sxs-lookup"><span data-stu-id="dc286-631">For more information, see the following topics:</span></span>

* [<span data-ttu-id="dc286-632">Referência de configuração para\<system.webServer></span><span class="sxs-lookup"><span data-stu-id="dc286-632">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="dc286-633">Para definir variáveis de ambiente para aplicativos individuais em execução em pools de aplicativos isolados (com suporte para o IIS 10,0 ou posterior), consulte a seção de *comando Appcmd. exe* do tópico [ \<environmentVariables> variáveis de ambiente](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) na documentação de referência do IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-633">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="dc286-634">Seções de configuração de web.config</span><span class="sxs-lookup"><span data-stu-id="dc286-634">Configuration sections of web.config</span></span>

<span data-ttu-id="dc286-635">As seções de configuração de aplicativos ASP.NET 4.x em *web.config* não são usadas por aplicativos ASP.NET Core para configuração:</span><span class="sxs-lookup"><span data-stu-id="dc286-635">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="dc286-636">Aplicativos ASP.NET Core são configurados para usar outros provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="dc286-636">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="dc286-637">Para obter mais informações, consulte [Configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="dc286-637">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="dc286-638">Pools de aplicativos</span><span class="sxs-lookup"><span data-stu-id="dc286-638">Application Pools</span></span>

<span data-ttu-id="dc286-639">O isolamento do pool de aplicativos é determinado pelo modelo de hospedagem:</span><span class="sxs-lookup"><span data-stu-id="dc286-639">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="dc286-640">Hospedagem em processo: os aplicativos devem ser executados em pools de aplicativos separados.</span><span class="sxs-lookup"><span data-stu-id="dc286-640">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="dc286-641">Hospedagem fora do processo: é recomendável isolar os aplicativos uns dos outros executando cada aplicativo em seu próprio pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="dc286-641">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="dc286-642">A caixa de diálogo **Adicionar Site** do IIS usa como padrão um único pool de aplicativos por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-642">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="dc286-643">Quando um **Nome de site** é fornecido, o texto é transferido automaticamente para a caixa de texto **Pool de aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="dc286-643">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="dc286-644">Um novo pool de aplicativos é criado usando o nome do site quando você adicionar o site.</span><span class="sxs-lookup"><span data-stu-id="dc286-644">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="dc286-645">Pool de aplicativosIdentity</span><span class="sxs-lookup"><span data-stu-id="dc286-645">Application Pool Identity</span></span>

<span data-ttu-id="dc286-646">Uma conta de identidade do pool de aplicativos permite executar um aplicativo em uma conta exclusiva sem a necessidade de criar e gerenciar domínios ou contas locais.</span><span class="sxs-lookup"><span data-stu-id="dc286-646">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="dc286-647">No IIS 8.0 ou posterior, o WAS (Processo de trabalho do administrador) do IIS cria uma conta virtual com o nome do novo pool de aplicativos e executa os processos de trabalho do pool de aplicativos nesta conta por padrão.</span><span class="sxs-lookup"><span data-stu-id="dc286-647">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="dc286-648">No console de gerenciamento do IIS, em **Configurações avançadas** para o pool de aplicativos, verifique se o **Identity** está definido para usar **ApplicationPoolIdentity**:</span><span class="sxs-lookup"><span data-stu-id="dc286-648">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![Caixa de diálogo Configurações avançadas do pool de aplicativos](index/_static/apppool-identity.png)

<span data-ttu-id="dc286-650">O processo de gerenciamento do IIS cria um identificador seguro com o nome do pool de aplicativos no Sistema de segurança do Windows.</span><span class="sxs-lookup"><span data-stu-id="dc286-650">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="dc286-651">Os recursos podem ser protegidos usando essa identidade.</span><span class="sxs-lookup"><span data-stu-id="dc286-651">Resources can be secured using this identity.</span></span> <span data-ttu-id="dc286-652">No entanto, essa identidade não é uma conta de usuário real e não será mostrada no Console de Gerenciamento de Usuários do Windows.</span><span class="sxs-lookup"><span data-stu-id="dc286-652">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="dc286-653">Se o processo de trabalho do IIS requerer acesso elevado ao aplicativo, modifique a ACL (lista de controle de acesso) do diretório que contém o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="dc286-653">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="dc286-654">Abra o Windows Explorer e navegue para o diretório.</span><span class="sxs-lookup"><span data-stu-id="dc286-654">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="dc286-655">Clique com o botão direito do mouse no diretório e selecione **Propriedades**.</span><span class="sxs-lookup"><span data-stu-id="dc286-655">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="dc286-656">Na guia **Segurança**, selecione o botão **Editar** e, em seguida, no botão **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="dc286-656">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="dc286-657">Clique no botão **Locais** e verifique se o sistema está selecionado.</span><span class="sxs-lookup"><span data-stu-id="dc286-657">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="dc286-658">Insira **IIS AppPool\\<nome_pool_aplicativos>** na área **Inserir os nomes de objeto a serem selecionados**.</span><span class="sxs-lookup"><span data-stu-id="dc286-658">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="dc286-659">Selecione o botão **Verificar Nomes**.</span><span class="sxs-lookup"><span data-stu-id="dc286-659">Select the **Check Names** button.</span></span> <span data-ttu-id="dc286-660">Para o *DefaultAppPool*, verifique os nomes usando **IIS AppPool\DefaultAppPool**.</span><span class="sxs-lookup"><span data-stu-id="dc286-660">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="dc286-661">Quando o botão **Verificar Nomes** é selecionado, um valor de **DefaultAppPool** é indicado na área de nomes de objeto.</span><span class="sxs-lookup"><span data-stu-id="dc286-661">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="dc286-662">Não é possível inserir o nome do pool de aplicativos diretamente na área de nomes de objeto.</span><span class="sxs-lookup"><span data-stu-id="dc286-662">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="dc286-663">Use o formato **IIS AppPool\\<nome_pool_aplicativos>** ao verificar o nome do objeto.</span><span class="sxs-lookup"><span data-stu-id="dc286-663">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![Selecione a caixa de diálogo de usuários ou grupos para a pasta do aplicativo: o nome do pool de aplicativos "DefaultAppPool" é anexado ao "IIS AppPool\" na área de nomes de objeto antes de selecionar"Verificar Nomes".](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="dc286-665">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="dc286-665">Select **OK**.</span></span>

   ![Selecione a caixa de diálogo de usuários ou grupos para a pasta do aplicativo: depois de selecionar "Verificar Nomes", o nome do objeto "DefaultAppPool" é mostrado na área de nomes de objeto.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="dc286-667">As permissões de leitura &amp; execução devem ser concedidas por padrão.</span><span class="sxs-lookup"><span data-stu-id="dc286-667">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="dc286-668">Forneça permissões adicionais conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="dc286-668">Provide additional permissions as needed.</span></span>

<span data-ttu-id="dc286-669">O acesso também pode ser concedido por meio de um prompt de comando usando a ferramenta **ICACLS**.</span><span class="sxs-lookup"><span data-stu-id="dc286-669">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="dc286-670">Usando o *DefaultAppPool* como exemplo, o comando a seguir é usado:</span><span class="sxs-lookup"><span data-stu-id="dc286-670">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="dc286-671">Para saber mais, veja o tópico [icacls](/windows-server/administration/windows-commands/icacls).</span><span class="sxs-lookup"><span data-stu-id="dc286-671">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="dc286-672">Suporte do HTTP/2</span><span class="sxs-lookup"><span data-stu-id="dc286-672">HTTP/2 support</span></span>

<span data-ttu-id="dc286-673">O [HTTP/2](https://httpwg.org/specs/rfc7540.html) é compatível com ASP.NET Core nos seguintes cenários de implantação de IIS:</span><span class="sxs-lookup"><span data-stu-id="dc286-673">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="dc286-674">Em processo</span><span class="sxs-lookup"><span data-stu-id="dc286-674">In-process</span></span>
  * <span data-ttu-id="dc286-675">Windows Server 2016/Windows 10 ou posterior; IIS 10 ou posterior</span><span class="sxs-lookup"><span data-stu-id="dc286-675">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="dc286-676">Conexão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="dc286-676">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="dc286-677">Fora do processo</span><span class="sxs-lookup"><span data-stu-id="dc286-677">Out-of-process</span></span>
  * <span data-ttu-id="dc286-678">Windows Server 2016/Windows 10 ou posterior; IIS 10 ou posterior</span><span class="sxs-lookup"><span data-stu-id="dc286-678">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="dc286-679">Conexões de servidor de borda voltadas para o público usam HTTP/2, mas a conexão de proxy reverso para o [servidor Kestrel](xref:fundamentals/servers/kestrel) usa HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="dc286-679">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="dc286-680">Conexão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="dc286-680">TLS 1.2 or later connection</span></span>

<span data-ttu-id="dc286-681">Para uma implantação em processo quando uma conexão HTTP/2 for estabelecida, o [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatará `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="dc286-681">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="dc286-682">Para uma implantação fora de processo quando uma conexão HTTP/2 for estabelecida, o [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatará `HTTP/1.1`.</span><span class="sxs-lookup"><span data-stu-id="dc286-682">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="dc286-683">Para saber mais sobre os modelos de hospedagem em processo e fora de processo, confira <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="dc286-683">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="dc286-684">O HTTP/2 está habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="dc286-684">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="dc286-685">As conexões retornarão para HTTP/1.1 se uma conexão HTTP/2 não for estabelecida.</span><span class="sxs-lookup"><span data-stu-id="dc286-685">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="dc286-686">Para obter mais informações sobre a configuração de HTTP/2 com implantações do IIS, consulte [HTTP/2 no IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="dc286-686">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="dc286-687">Solicitações de simulação do CORS</span><span class="sxs-lookup"><span data-stu-id="dc286-687">CORS preflight requests</span></span>

<span data-ttu-id="dc286-688">*Esta seção só se aplica a aplicativos ASP.NET Core com o .NET Framework como destino.*</span><span class="sxs-lookup"><span data-stu-id="dc286-688">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="dc286-689">Para um aplicativo ASP.NET Core com o .NET Framework como destino, as solicitações OPTIONS não são passadas para o aplicativo por padrão no IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-689">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="dc286-690">Para saber como configurar os manipuladores do IIS do aplicativo no *Web. config* para passar solicitações de opções, consulte [habilitar solicitações entre origens no ASP.NET Web API 2: como o CORS funciona](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span><span class="sxs-lookup"><span data-stu-id="dc286-690">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="dc286-691">Módulo de Inicialização de Aplicativo e Tempo Limite de Ociosidade</span><span class="sxs-lookup"><span data-stu-id="dc286-691">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="dc286-692">Quando hospedado no IIS pela versão 2 do Módulo do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="dc286-692">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="dc286-693">[Módulo de inicialização do aplicativo](#application-initialization-module): hospedado [em processo](#in-process-hosting-model) ou [fora do processo](#out-of-process-hosting-model) do aplicativo pode ser configurado para iniciar automaticamente em uma reinicialização do processo de trabalho ou reinicialização do servidor.</span><span class="sxs-lookup"><span data-stu-id="dc286-693">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="dc286-694">[Tempo limite de ociosidade](#idle-timeout): o aplicativo hospedado [no processo](#in-process-hosting-model) pode ser configurado para não atingir o tempo limite durante períodos de inatividade.</span><span class="sxs-lookup"><span data-stu-id="dc286-694">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="dc286-695">Módulo de Inicialização de Aplicativo</span><span class="sxs-lookup"><span data-stu-id="dc286-695">Application Initialization Module</span></span>

<span data-ttu-id="dc286-696">*Aplica-se a aplicativos hospedados em processo e fora de processo.*</span><span class="sxs-lookup"><span data-stu-id="dc286-696">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="dc286-697">[Inicialização de Aplicativo IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) é um recurso do IIS que envia uma solicitação HTTP para o aplicativo quando o pool de aplicativos é iniciado ou reciclado.</span><span class="sxs-lookup"><span data-stu-id="dc286-697">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="dc286-698">A solicitação dispara a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-698">The request triggers the app to start.</span></span> <span data-ttu-id="dc286-699">Por padrão, o IIS emite uma solicitação para a URL raiz do aplicativo (`/`) para inicializar o aplicativo (confira mais detalhes sobre a configuração nos [recursos adicionais](#application-initialization-module-and-idle-timeout-additional-resources)).</span><span class="sxs-lookup"><span data-stu-id="dc286-699">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="dc286-700">Confirme se o recurso da função Inicialização de Aplicativo do IIS está habilitado:</span><span class="sxs-lookup"><span data-stu-id="dc286-700">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="dc286-701">No Windows 7 ou sistemas de área de trabalho posteriores, ao usar o IIS localmente:</span><span class="sxs-lookup"><span data-stu-id="dc286-701">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="dc286-702">Navegue até **Painel de Controle** > **Programas** > **Programas e Recursos** > **Ativar ou desativar recursos do Windows** (lado esquerdo da tela).</span><span class="sxs-lookup"><span data-stu-id="dc286-702">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="dc286-703">Abra **Serviços de Informações da Internet** > **Serviços da World Wide Web** > **Recursos de Desenvolvimento de Aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="dc286-703">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="dc286-704">Marque a caixa de seleção **Inicialização de Aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="dc286-704">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="dc286-705">No Windows Server 2008 R2 ou posterior:</span><span class="sxs-lookup"><span data-stu-id="dc286-705">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="dc286-706">Abra o **Assistente de Adição de Funções e Recursos**.</span><span class="sxs-lookup"><span data-stu-id="dc286-706">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="dc286-707">No painel **Selecionar serviços de função**, abra o nó **Desenvolvimento de aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="dc286-707">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="dc286-708">Marque a caixa de seleção **Inicialização de Aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="dc286-708">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="dc286-709">Use quaisquer das abordagens a seguir para habilitar o Módulo de Inicialização do Aplicativo para o site:</span><span class="sxs-lookup"><span data-stu-id="dc286-709">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="dc286-710">Usando o Gerenciador do IIS:</span><span class="sxs-lookup"><span data-stu-id="dc286-710">Using IIS Manager:</span></span>

  1. <span data-ttu-id="dc286-711">Selecione **Pools de Aplicativos** no painel **Conexões**.</span><span class="sxs-lookup"><span data-stu-id="dc286-711">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="dc286-712">Clique com o botão direito do mouse no pool de aplicativos do aplicativo na lista e selecione **Configurações Avançadas**.</span><span class="sxs-lookup"><span data-stu-id="dc286-712">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="dc286-713">O **Modo de Inicialização** padrão é **OnDemand**.</span><span class="sxs-lookup"><span data-stu-id="dc286-713">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="dc286-714">Defina o **Modo de Inicialização** como **AlwaysRunning**.</span><span class="sxs-lookup"><span data-stu-id="dc286-714">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="dc286-715">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="dc286-715">Select **OK**.</span></span>
  1. <span data-ttu-id="dc286-716">Abra o nó **Sites** no painel **Conexões**.</span><span class="sxs-lookup"><span data-stu-id="dc286-716">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="dc286-717">Clique com o botão direito do mouse no aplicativo e selecione **Gerenciar Site** > **Configurações Avançadas**.</span><span class="sxs-lookup"><span data-stu-id="dc286-717">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="dc286-718">A configuração de **Pré-carregamento Habilitado** padrão é **Falso**.</span><span class="sxs-lookup"><span data-stu-id="dc286-718">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="dc286-719">Defina **Pré-carregamento Habilitado** como **Verdadeiro**.</span><span class="sxs-lookup"><span data-stu-id="dc286-719">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="dc286-720">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="dc286-720">Select **OK**.</span></span>

* <span data-ttu-id="dc286-721">Usando o *web.config*, adicione o elemento `<applicationInitialization>` definindo `doAppInitAfterRestart` como `true` aos elementos `<system.webServer>` no arquivo *web.config* do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="dc286-721">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="dc286-722">Tempo limite de ociosidade</span><span class="sxs-lookup"><span data-stu-id="dc286-722">Idle Timeout</span></span>

<span data-ttu-id="dc286-723">*Só se aplica a aplicativos hospedados em processo.*</span><span class="sxs-lookup"><span data-stu-id="dc286-723">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="dc286-724">Para impedir que o aplicativo fique ocioso, defina o tempo limite de ociosidade do pool de aplicativos, usando o Gerenciador do IIS:</span><span class="sxs-lookup"><span data-stu-id="dc286-724">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="dc286-725">Selecione **Pools de Aplicativos** no painel **Conexões**.</span><span class="sxs-lookup"><span data-stu-id="dc286-725">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="dc286-726">Clique com o botão direito do mouse no pool de aplicativos do aplicativo na lista e selecione **Configurações Avançadas**.</span><span class="sxs-lookup"><span data-stu-id="dc286-726">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="dc286-727">O **Tempo Limite de Ociosidade (minutos)** é de **20** minutos.</span><span class="sxs-lookup"><span data-stu-id="dc286-727">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="dc286-728">Defina o **Tempo Limite de Ociosidade (minutos)** como **0** (zero).</span><span class="sxs-lookup"><span data-stu-id="dc286-728">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="dc286-729">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="dc286-729">Select **OK**.</span></span>
1. <span data-ttu-id="dc286-730">Recicle o processo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="dc286-730">Recycle the worker process.</span></span>

<span data-ttu-id="dc286-731">Para impedir que aplicativos hospedados [fora de processo](#out-of-process-hosting-model) atinjam o tempo limite, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="dc286-731">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="dc286-732">Execute ping do aplicativo de um serviço externo para mantê-lo em execução.</span><span class="sxs-lookup"><span data-stu-id="dc286-732">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="dc286-733">Se o aplicativo hospedar apenas serviços em segundo plano, evite a hospedagem do IIS e use um [Serviço do Windows para hospedar o aplicativo ASP.NET Core](xref:host-and-deploy/windows-service).</span><span class="sxs-lookup"><span data-stu-id="dc286-733">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="dc286-734">Recursos adicionais do Módulo de Inicialização de Aplicativo e do Tempo Limite de Ociosidade</span><span class="sxs-lookup"><span data-stu-id="dc286-734">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="dc286-735">Inicialização de Aplicativo do IIS 8.0</span><span class="sxs-lookup"><span data-stu-id="dc286-735">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="dc286-736">[Inicialização \<applicationInitialization> do aplicativo ](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="dc286-736">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="dc286-737">[Configurações do modelo de processo para um \<processModel> pool de aplicativos ](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="dc286-737">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="dc286-738">Recursos de implantação para administradores do IIS</span><span class="sxs-lookup"><span data-stu-id="dc286-738">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="dc286-739">Documentação do ISS</span><span class="sxs-lookup"><span data-stu-id="dc286-739">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="dc286-740">Introdução ao Gerenciador do IIS no IIS</span><span class="sxs-lookup"><span data-stu-id="dc286-740">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="dc286-741">Implantação de aplicativo .NET Core</span><span class="sxs-lookup"><span data-stu-id="dc286-741">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="dc286-742">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="dc286-742">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="dc286-743">O site oficial da IIS da Microsoft</span><span class="sxs-lookup"><span data-stu-id="dc286-743">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="dc286-744">Biblioteca de conteúdo técnico do Windows Server</span><span class="sxs-lookup"><span data-stu-id="dc286-744">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="dc286-745">HTTP/2 no IIS</span><span class="sxs-lookup"><span data-stu-id="dc286-745">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="dc286-746">Para uma experiência de tutorial sobre como publicar um aplicativo de ASP.NET Core em um servidor IIS, confira <xref:tutorials/publish-to-iis>.</span><span class="sxs-lookup"><span data-stu-id="dc286-746">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="dc286-747">Instalar o pacote de hospedagem do .NET Core</span><span class="sxs-lookup"><span data-stu-id="dc286-747">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="dc286-748">Sistemas operacionais compatíveis</span><span class="sxs-lookup"><span data-stu-id="dc286-748">Supported operating systems</span></span>

<span data-ttu-id="dc286-749">Há suporte para os seguintes sistemas operacionais:</span><span class="sxs-lookup"><span data-stu-id="dc286-749">The following operating systems are supported:</span></span>

* <span data-ttu-id="dc286-750">Windows 7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="dc286-750">Windows 7 or later</span></span>
* <span data-ttu-id="dc286-751">Windows Server 2008 R2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="dc286-751">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="dc286-752">O [servidor HTTP.sys](xref:fundamentals/servers/httpsys) (anteriormente chamado de WebListener) não funciona em uma configuração de proxy reverso com o IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-752">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="dc286-753">Use o [servidor Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="dc286-753">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="dc286-754">Para obter mais informações sobre hospedagem no Azure, consulte <xref:host-and-deploy/azure-apps/index>.</span><span class="sxs-lookup"><span data-stu-id="dc286-754">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="dc286-755">Para obter as diretrizes de solução de problemas, consulte <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="dc286-755">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="dc286-756">Plataformas compatíveis</span><span class="sxs-lookup"><span data-stu-id="dc286-756">Supported platforms</span></span>

<span data-ttu-id="dc286-757">Aplicativos publicados para implantação de 32 bits (x86) ou 64 bits (x64) têm suporte.</span><span class="sxs-lookup"><span data-stu-id="dc286-757">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="dc286-758">Implantar um aplicativo de 32 bits com um SDK do .NET Core de 32 bits (x86), a menos que o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="dc286-758">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="dc286-759">Exija o maior espaço de endereço de memória virtual disponível para um aplicativo de 64 bits.</span><span class="sxs-lookup"><span data-stu-id="dc286-759">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="dc286-760">Exija o maior tamanho de pilha do IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-760">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="dc286-761">Tenha dependências nativas de 64 bits.</span><span class="sxs-lookup"><span data-stu-id="dc286-761">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="dc286-762">Use um SDK do .NET Core de 64 bits (x64) para publicar um aplicativo de 64 bits.</span><span class="sxs-lookup"><span data-stu-id="dc286-762">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="dc286-763">Um runtime de 64 bits deve estar presente no sistema host.</span><span class="sxs-lookup"><span data-stu-id="dc286-763">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="dc286-764">Modelos de hospedagem</span><span class="sxs-lookup"><span data-stu-id="dc286-764">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="dc286-765">Modelo de hospedagem em processo</span><span class="sxs-lookup"><span data-stu-id="dc286-765">In-process hosting model</span></span>

<span data-ttu-id="dc286-766">Usando uma hospedagem em processo, um aplicativo ASP.NET Core é executado no mesmo processo que seu processo de trabalho do IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-766">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="dc286-767">A hospedagem em processo fornece desempenho aprimorado sobre a hospedagem fora do processo porque:</span><span class="sxs-lookup"><span data-stu-id="dc286-767">In-process hosting provides improved performance over out-of-process hosting because:</span></span>

* <span data-ttu-id="dc286-768">As solicitações não são feitas por proxy no adaptador de loopback.</span><span class="sxs-lookup"><span data-stu-id="dc286-768">Requests aren't proxied over the loopback adapter.</span></span> <span data-ttu-id="dc286-769">Um adaptador de loopback é uma interface de rede que retorna o tráfego de rede de saída para o mesmo computador.</span><span class="sxs-lookup"><span data-stu-id="dc286-769">A loopback adapter is a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="dc286-770">O IIS manipula o gerenciamento de processos com o [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="dc286-770">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="dc286-771">O [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="dc286-771">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="dc286-772">Executa a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-772">Performs app initialization.</span></span>
  * <span data-ttu-id="dc286-773">Carrega o [CoreCLR](/dotnet/standard/glossary#coreclr).</span><span class="sxs-lookup"><span data-stu-id="dc286-773">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="dc286-774">Chama `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="dc286-774">Calls `Program.Main`.</span></span>
* <span data-ttu-id="dc286-775">Manipula o tempo de vida da solicitação nativa do IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-775">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="dc286-776">Não há suporte para o modelo de hospedagem em processo para aplicativos ASP.NET Core direcionados ao .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="dc286-776">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="dc286-777">O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo hospedado em processo:</span><span class="sxs-lookup"><span data-stu-id="dc286-777">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![Módulo do ASP.NET Core no cenário de hospedagem em processo](index/_static/ancm-inprocess.png)

<span data-ttu-id="dc286-779">A solicitação chega da Web para o driver do HTTP.sys no modo kernel.</span><span class="sxs-lookup"><span data-stu-id="dc286-779">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="dc286-780">O driver roteia as solicitações nativas ao IIS na porta configurada do site, normalmente, a 80 (HTTP) ou a 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="dc286-780">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="dc286-781">O módulo ASP.NET Core recebe a solicitação nativa e a passa para o servidor HTTP IIS ( `IISHttpServer` ).</span><span class="sxs-lookup"><span data-stu-id="dc286-781">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="dc286-782">O servidor HTTP do IIS é uma implementação de servidor em processo do IIS que converte a solicitação de nativa para gerenciada.</span><span class="sxs-lookup"><span data-stu-id="dc286-782">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="dc286-783">Depois que o Servidor HTTP do IIS processa a solicitação, a solicitação é enviada por push para o pipeline de middleware do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dc286-783">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="dc286-784">O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-784">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="dc286-785">A resposta do aplicativo é retornada ao IIS por meio do Servidor HTTP do IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-785">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="dc286-786">O IIS enviará a resposta ao cliente que iniciou a solicitação.</span><span class="sxs-lookup"><span data-stu-id="dc286-786">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="dc286-787">A hospedagem em processo é uma opção de aceitação para os aplicativos existentes, mas o padrão dos modelos [dotnet new](/dotnet/core/tools/dotnet-new) é o modelo de hospedagem em processo para todos os cenários do IIS e do IIS Express.</span><span class="sxs-lookup"><span data-stu-id="dc286-787">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="dc286-788">`CreateDefaultBuilder` adiciona uma instância <xref:Microsoft.AspNetCore.Hosting.Server.IServer> chamando o método <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> para inicializar o [CoreCLR](/dotnet/standard/glossary#coreclr) e hospedar o aplicativo no processo de trabalho do IIS (*w3wp.exe* ou *iisexpress.exe*).</span><span class="sxs-lookup"><span data-stu-id="dc286-788">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="dc286-789">Os testes de desempenho indicam que hospedar um aplicativo em processo do .NET Core oferece uma taxa de transferência de solicitação significativamente mais elevada em comparação a hospedar o aplicativo fora do processo e enviar por proxy as solicitações para o servidor [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="dc286-789">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="dc286-790">Modelo de hospedagem de fora do processo</span><span class="sxs-lookup"><span data-stu-id="dc286-790">Out-of-process hosting model</span></span>

<span data-ttu-id="dc286-791">Como ASP.NET Core aplicativos são executados em um processo separado do processo de trabalho do IIS, o módulo ASP.NET Core lida com o gerenciamento de processos.</span><span class="sxs-lookup"><span data-stu-id="dc286-791">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="dc286-792">O módulo inicia o processo para o aplicativo ASP.NET Core quando a primeira solicitação chega e reinicia o aplicativo se ele é desligado ou falha.</span><span class="sxs-lookup"><span data-stu-id="dc286-792">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="dc286-793">Isso é basicamente o mesmo comportamento que o dos aplicativos que são executados dentro do processo e são gerenciados pelo [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="dc286-793">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="dc286-794">O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo hospedado de fora d processo:</span><span class="sxs-lookup"><span data-stu-id="dc286-794">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Módulo do ASP.NET Core no cenário de hospedagem fora do processo](index/_static/ancm-outofprocess.png)

<span data-ttu-id="dc286-796">As solicitações chegam da Web para o driver do HTTP.sys no modo kernel.</span><span class="sxs-lookup"><span data-stu-id="dc286-796">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="dc286-797">O driver roteia as solicitações ao IIS na porta configurada do site, normalmente, a 80 (HTTP) ou a 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="dc286-797">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="dc286-798">O módulo encaminha as solicitações ao Kestrel em uma porta aleatória do aplicativo, que não seja a porta 80 ou 443.</span><span class="sxs-lookup"><span data-stu-id="dc286-798">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="dc286-799">O módulo especifica a porta por meio de uma variável de ambiente na inicialização e a extensão <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> configura o servidor para escutar em `http://localhost:{PORT}`.</span><span class="sxs-lookup"><span data-stu-id="dc286-799">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="dc286-800">Outras verificações são executadas e as solicitações que não se originam do módulo são rejeitadas.</span><span class="sxs-lookup"><span data-stu-id="dc286-800">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="dc286-801">O módulo não é compatível com encaminhamento de HTTPS, portanto, as solicitações são encaminhadas por HTTP, mesmo se recebidas pelo IIS por HTTPS.</span><span class="sxs-lookup"><span data-stu-id="dc286-801">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="dc286-802">Depois que o Kestrel coleta a solicitação do módulo, a solicitação é enviada por push ao pipeline do middleware do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dc286-802">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="dc286-803">O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-803">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="dc286-804">O middleware adicionado pela integração do IIS atualiza o esquema, o IP remoto e pathbase para encaminhar a solicitação para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="dc286-804">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="dc286-805">A resposta do aplicativo é retornada ao IIS, que a retorna por push para o cliente HTTP que iniciou a solicitação.</span><span class="sxs-lookup"><span data-stu-id="dc286-805">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="dc286-806">Para orientação sobre a configuração do Módulo do ASP.NET Core, consulte <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="dc286-806">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="dc286-807">Para saber mais sobre hospedagem, confira [Host no ASP.NET Core](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="dc286-807">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="dc286-808">Configuração de aplicativo</span><span class="sxs-lookup"><span data-stu-id="dc286-808">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="dc286-809">Habilitar os componentes de IISIntegration</span><span class="sxs-lookup"><span data-stu-id="dc286-809">Enable the IISIntegration components</span></span>

<span data-ttu-id="dc286-810">Ao criar um host no `CreateWebHostBuilder` (*Program.cs*), chame <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> para habilitar a integração do IIS:</span><span class="sxs-lookup"><span data-stu-id="dc286-810">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="dc286-811">Para obter mais informações sobre o `CreateDefaultBuilder`, consulte <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="dc286-811">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="dc286-812">Opções do IIS</span><span class="sxs-lookup"><span data-stu-id="dc286-812">IIS options</span></span>

<span data-ttu-id="dc286-813">**Modelo de hospedagem em processo**</span><span class="sxs-lookup"><span data-stu-id="dc286-813">**In-process hosting model**</span></span>

<span data-ttu-id="dc286-814">Para configurar as opções do Servidor IIS, inclua uma configuração de serviço para <xref:Microsoft.AspNetCore.Builder.IISServerOptions> em <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span><span class="sxs-lookup"><span data-stu-id="dc286-814">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="dc286-815">O exemplo a seguir desabilita AutomaticAuthentication:</span><span class="sxs-lookup"><span data-stu-id="dc286-815">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="dc286-816">Opção</span><span class="sxs-lookup"><span data-stu-id="dc286-816">Option</span></span>                         | <span data-ttu-id="dc286-817">Padrão</span><span class="sxs-lookup"><span data-stu-id="dc286-817">Default</span></span> | <span data-ttu-id="dc286-818">Configuração</span><span class="sxs-lookup"><span data-stu-id="dc286-818">Setting</span></span> |
| ---
<span data-ttu-id="dc286-819">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-819">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-820">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-820">'Blazor'</span></span>
- <span data-ttu-id="dc286-821">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-821">'Identity'</span></span>
- <span data-ttu-id="dc286-822">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-822">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-823">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-823">'Razor'</span></span>
- <span data-ttu-id="dc286-824">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-824">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-825">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-825">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-826">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-826">'Blazor'</span></span>
- <span data-ttu-id="dc286-827">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-827">'Identity'</span></span>
- <span data-ttu-id="dc286-828">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-828">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-829">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-829">'Razor'</span></span>
- <span data-ttu-id="dc286-830">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-830">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-831">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-831">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-832">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-832">'Blazor'</span></span>
- <span data-ttu-id="dc286-833">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-833">'Identity'</span></span>
- <span data-ttu-id="dc286-834">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-834">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-835">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-835">'Razor'</span></span>
- <span data-ttu-id="dc286-836">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-836">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-837">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-837">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-838">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-838">'Blazor'</span></span>
- <span data-ttu-id="dc286-839">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-839">'Identity'</span></span>
- <span data-ttu-id="dc286-840">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-840">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-841">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-841">'Razor'</span></span>
- <span data-ttu-id="dc286-842">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-842">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-843">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-844">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-844">'Blazor'</span></span>
- <span data-ttu-id="dc286-845">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-845">'Identity'</span></span>
- <span data-ttu-id="dc286-846">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-846">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-847">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-847">'Razor'</span></span>
- <span data-ttu-id="dc286-848">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-848">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-849">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-850">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-850">'Blazor'</span></span>
- <span data-ttu-id="dc286-851">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-851">'Identity'</span></span>
- <span data-ttu-id="dc286-852">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-852">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-853">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-853">'Razor'</span></span>
- <span data-ttu-id="dc286-854">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-854">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-855">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-856">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-856">'Blazor'</span></span>
- <span data-ttu-id="dc286-857">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-857">'Identity'</span></span>
- <span data-ttu-id="dc286-858">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-858">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-859">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-859">'Razor'</span></span>
- <span data-ttu-id="dc286-860">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-860">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-861">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-862">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-862">'Blazor'</span></span>
- <span data-ttu-id="dc286-863">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-863">'Identity'</span></span>
- <span data-ttu-id="dc286-864">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-864">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-865">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-865">'Razor'</span></span>
- <span data-ttu-id="dc286-866">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-866">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-867">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-868">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-868">'Blazor'</span></span>
- <span data-ttu-id="dc286-869">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-869">'Identity'</span></span>
- <span data-ttu-id="dc286-870">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-870">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-871">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-871">'Razor'</span></span>
- <span data-ttu-id="dc286-872">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-872">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-873">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-874">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-874">'Blazor'</span></span>
- <span data-ttu-id="dc286-875">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-875">'Identity'</span></span>
- <span data-ttu-id="dc286-876">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-876">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-877">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-877">'Razor'</span></span>
- <span data-ttu-id="dc286-878">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-878">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-879">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-880">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-880">'Blazor'</span></span>
- <span data-ttu-id="dc286-881">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-881">'Identity'</span></span>
- <span data-ttu-id="dc286-882">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-882">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-883">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-883">'Razor'</span></span>
- <span data-ttu-id="dc286-884">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-884">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-885">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-885">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-886">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-886">'Blazor'</span></span>
- <span data-ttu-id="dc286-887">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-887">'Identity'</span></span>
- <span data-ttu-id="dc286-888">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-888">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-889">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-889">'Razor'</span></span>
- <span data-ttu-id="dc286-890">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-890">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-891">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-892">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-892">'Blazor'</span></span>
- <span data-ttu-id="dc286-893">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-893">'Identity'</span></span>
- <span data-ttu-id="dc286-894">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-894">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-895">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-895">'Razor'</span></span>
- <span data-ttu-id="dc286-896">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-896">'SignalR' uid:</span></span> 

<span data-ttu-id="dc286-897">--------------- | :-----: | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-897">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-898">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-898">'Blazor'</span></span>
- <span data-ttu-id="dc286-899">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-899">'Identity'</span></span>
- <span data-ttu-id="dc286-900">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-900">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-901">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-901">'Razor'</span></span>
- <span data-ttu-id="dc286-902">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-902">'SignalR' uid:</span></span> 

<span data-ttu-id="dc286-903">---- | | `AutomaticAuthentication`      | `true`  | Se `true` , o servidor IIS definirá a `HttpContext.User` autenticação autenticada pelo [Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="dc286-903">---- | | `AutomaticAuthentication`      | `true`  | If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="dc286-904">Se `false`, o servidor fornecerá apenas uma identidade para `HttpContext.User` e responderá a desafios quando explicitamente solicitado pelo `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="dc286-904">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="dc286-905">A autenticação do Windows deve estar habilitada no IIS para que o `AutomaticAuthentication` funcione.</span><span class="sxs-lookup"><span data-stu-id="dc286-905">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="dc286-906">Para obter mais informações, veja [Autenticação do Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="dc286-906">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="dc286-907">| | `AuthenticationDisplayName`    | `null`  | Define o nome de exibição mostrado para os usuários nas páginas de logon.</span><span class="sxs-lookup"><span data-stu-id="dc286-907">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="dc286-908">**Modelo de hospedagem de fora do processo**</span><span class="sxs-lookup"><span data-stu-id="dc286-908">**Out-of-process hosting model**</span></span>

<span data-ttu-id="dc286-909">Para configurar opções do IIS, inclua uma configuração de serviço para <xref:Microsoft.AspNetCore.Builder.IISOptions> em <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span><span class="sxs-lookup"><span data-stu-id="dc286-909">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="dc286-910">O exemplo a seguir impede que o aplicativo preencha `HttpContext.Connection.ClientCertificate`:</span><span class="sxs-lookup"><span data-stu-id="dc286-910">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="dc286-911">Opção</span><span class="sxs-lookup"><span data-stu-id="dc286-911">Option</span></span>                         | <span data-ttu-id="dc286-912">Padrão</span><span class="sxs-lookup"><span data-stu-id="dc286-912">Default</span></span> | <span data-ttu-id="dc286-913">Configuração</span><span class="sxs-lookup"><span data-stu-id="dc286-913">Setting</span></span> |
| ---
<span data-ttu-id="dc286-914">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-914">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-915">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-915">'Blazor'</span></span>
- <span data-ttu-id="dc286-916">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-916">'Identity'</span></span>
- <span data-ttu-id="dc286-917">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-917">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-918">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-918">'Razor'</span></span>
- <span data-ttu-id="dc286-919">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-919">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-920">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-920">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-921">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-921">'Blazor'</span></span>
- <span data-ttu-id="dc286-922">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-922">'Identity'</span></span>
- <span data-ttu-id="dc286-923">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-923">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-924">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-924">'Razor'</span></span>
- <span data-ttu-id="dc286-925">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-925">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-926">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-926">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-927">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-927">'Blazor'</span></span>
- <span data-ttu-id="dc286-928">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-928">'Identity'</span></span>
- <span data-ttu-id="dc286-929">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-929">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-930">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-930">'Razor'</span></span>
- <span data-ttu-id="dc286-931">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-931">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-932">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-932">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-933">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-933">'Blazor'</span></span>
- <span data-ttu-id="dc286-934">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-934">'Identity'</span></span>
- <span data-ttu-id="dc286-935">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-935">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-936">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-936">'Razor'</span></span>
- <span data-ttu-id="dc286-937">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-937">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-938">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-938">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-939">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-939">'Blazor'</span></span>
- <span data-ttu-id="dc286-940">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-940">'Identity'</span></span>
- <span data-ttu-id="dc286-941">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-941">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-942">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-942">'Razor'</span></span>
- <span data-ttu-id="dc286-943">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-943">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-944">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-944">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-945">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-945">'Blazor'</span></span>
- <span data-ttu-id="dc286-946">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-946">'Identity'</span></span>
- <span data-ttu-id="dc286-947">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-947">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-948">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-948">'Razor'</span></span>
- <span data-ttu-id="dc286-949">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-949">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-950">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-950">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-951">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-951">'Blazor'</span></span>
- <span data-ttu-id="dc286-952">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-952">'Identity'</span></span>
- <span data-ttu-id="dc286-953">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-953">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-954">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-954">'Razor'</span></span>
- <span data-ttu-id="dc286-955">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-955">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-956">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-956">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-957">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-957">'Blazor'</span></span>
- <span data-ttu-id="dc286-958">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-958">'Identity'</span></span>
- <span data-ttu-id="dc286-959">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-959">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-960">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-960">'Razor'</span></span>
- <span data-ttu-id="dc286-961">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-961">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-962">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-962">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-963">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-963">'Blazor'</span></span>
- <span data-ttu-id="dc286-964">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-964">'Identity'</span></span>
- <span data-ttu-id="dc286-965">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-965">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-966">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-966">'Razor'</span></span>
- <span data-ttu-id="dc286-967">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-967">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-968">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-968">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-969">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-969">'Blazor'</span></span>
- <span data-ttu-id="dc286-970">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-970">'Identity'</span></span>
- <span data-ttu-id="dc286-971">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-971">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-972">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-972">'Razor'</span></span>
- <span data-ttu-id="dc286-973">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-973">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-974">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-974">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-975">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-975">'Blazor'</span></span>
- <span data-ttu-id="dc286-976">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-976">'Identity'</span></span>
- <span data-ttu-id="dc286-977">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-977">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-978">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-978">'Razor'</span></span>
- <span data-ttu-id="dc286-979">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-979">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-980">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-980">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-981">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-981">'Blazor'</span></span>
- <span data-ttu-id="dc286-982">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-982">'Identity'</span></span>
- <span data-ttu-id="dc286-983">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-983">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-984">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-984">'Razor'</span></span>
- <span data-ttu-id="dc286-985">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-985">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-986">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-986">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-987">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-987">'Blazor'</span></span>
- <span data-ttu-id="dc286-988">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-988">'Identity'</span></span>
- <span data-ttu-id="dc286-989">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-989">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-990">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-990">'Razor'</span></span>
- <span data-ttu-id="dc286-991">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-991">'SignalR' uid:</span></span> 

<span data-ttu-id="dc286-992">--------------- | :-----: | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-992">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-993">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-993">'Blazor'</span></span>
- <span data-ttu-id="dc286-994">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-994">'Identity'</span></span>
- <span data-ttu-id="dc286-995">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-995">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-996">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-996">'Razor'</span></span>
- <span data-ttu-id="dc286-997">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-997">'SignalR' uid:</span></span> 

<span data-ttu-id="dc286-998">---- | | `AutomaticAuthentication`      | `true`  | Se `true` , o [middleware de integração do IIS](#enable-the-iisintegration-components) define a `HttpContext.User` [autenticação](xref:security/authentication/windowsauth)autenticada pelo Windows.</span><span class="sxs-lookup"><span data-stu-id="dc286-998">---- | | `AutomaticAuthentication`      | `true`  | If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="dc286-999">Se `false`, o middleware fornecerá apenas uma identidade para `HttpContext.User` e responderá a desafios quando explicitamente solicitado pelo `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="dc286-999">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="dc286-1000">A autenticação do Windows deve estar habilitada no IIS para que o `AutomaticAuthentication` funcione.</span><span class="sxs-lookup"><span data-stu-id="dc286-1000">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="dc286-1001">Saiba mais no tópico [Autenticação do Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="dc286-1001">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> <span data-ttu-id="dc286-1002">| | `AuthenticationDisplayName`    | `null`  | Define o nome de exibição mostrado para os usuários nas páginas de logon.</span><span class="sxs-lookup"><span data-stu-id="dc286-1002">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="dc286-1003">| | `ForwardClientCertificate`     | `true`  | Se `true` e o `MS-ASPNETCORE-CLIENTCERT` cabeçalho da solicitação estiver presente, o `HttpContext.Connection.ClientCertificate` será preenchido.</span><span class="sxs-lookup"><span data-stu-id="dc286-1003">| | `ForwardClientCertificate`     | `true`  | If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="dc286-1004">Servidor proxy e cenários de balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="dc286-1004">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="dc286-1005">O [Middleware de integração do IIS](#enable-the-iisintegration-components), que configura o Middleware de cabeçalhos encaminhados, e o módulo do ASP.NET Core, são configurados para encaminhar o esquema (HTTP/HTTPS) e o endereço IP remoto de onde a solicitação foi originada.</span><span class="sxs-lookup"><span data-stu-id="dc286-1005">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="dc286-1006">Configuração adicional pode ser necessária para aplicativos hospedados atrás de servidores proxy adicionais e balanceadores de carga.</span><span class="sxs-lookup"><span data-stu-id="dc286-1006">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="dc286-1007">Para obter mais informações, veja [Configurar o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="dc286-1007">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="dc286-1008">Arquivo web.config</span><span class="sxs-lookup"><span data-stu-id="dc286-1008">web.config file</span></span>

<span data-ttu-id="dc286-1009">O arquivo *web.config* configura o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="dc286-1009">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="dc286-1010">Criando, transformar e publicar o arquivo *Web.config* é tratado por um destino do MSBuild (`_TransformWebConfig`) quando o projeto é publicado.</span><span class="sxs-lookup"><span data-stu-id="dc286-1010">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="dc286-1011">Este destino está presente nos destinos do SDK da Web (`Microsoft.NET.Sdk.Web`).</span><span class="sxs-lookup"><span data-stu-id="dc286-1011">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="dc286-1012">O SDK é definido na parte superior do arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="dc286-1012">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="dc286-1013">Se um arquivo *web.config* não estiver presente no projeto, ele será criado com o *processPath* e os *argumentos* corretos para configurar o Módulo do ASP.NET Core e será transferido para o [resultado publicado](xref:host-and-deploy/directory-structure).</span><span class="sxs-lookup"><span data-stu-id="dc286-1013">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="dc286-1014">Se um arquivo *web.config* estiver presente no projeto, ele será transformado com o *processPath* e os *argumentos* corretos para configurar o Módulo do ASP.NET Core e será movido para o resultado publicado.</span><span class="sxs-lookup"><span data-stu-id="dc286-1014">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="dc286-1015">A transformação não altera as definições de configuração do IIS no arquivo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1015">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="dc286-1016">O arquivo *web.config* pode fornecer configurações adicionais do IIS que controlam módulos ativos do IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-1016">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="dc286-1017">Para saber mais sobre os módulos do IIS que podem processar solicitações com aplicativos do ASP.NET Core, veja o tópico [Módulos do IIS](xref:host-and-deploy/iis/modules).</span><span class="sxs-lookup"><span data-stu-id="dc286-1017">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="dc286-1018">Para impedir que o SDK da Web transforme o arquivo *Web. config* , use a **\<IsTransformWebConfigDisabled>** propriedade no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="dc286-1018">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="dc286-1019">Ao impedir que o SDK Web transforme o arquivo, o *processPath* e os *argumentos* devem ser definidos manualmente pelo desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="dc286-1019">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="dc286-1020">Para obter mais informações, consulte <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="dc286-1020">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="dc286-1021">Local do arquivo web.config</span><span class="sxs-lookup"><span data-stu-id="dc286-1021">web.config file location</span></span>

<span data-ttu-id="dc286-1022">Para configurar o [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) corretamente, o arquivo *Web. config* deve estar presente no caminho [raiz do conteúdo](xref:fundamentals/index#content-root) (normalmente o caminho base do aplicativo) do aplicativo implantado.</span><span class="sxs-lookup"><span data-stu-id="dc286-1022">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="dc286-1023">Esse é o mesmo local que o caminho físico do site fornecido ao IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-1023">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="dc286-1024">O arquivo *web.config* é necessário na raiz do aplicativo para habilitar a publicação de vários aplicativos usando a Implantação da Web.</span><span class="sxs-lookup"><span data-stu-id="dc286-1024">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="dc286-1025">Existem arquivos confidenciais no caminho físico do aplicativo, como \* \<assembly> . runtimeconfig. JSON*, \* \<assembly> . xml* (comentários de documentação XML) e \* \<assembly> . deps. JSON\*.</span><span class="sxs-lookup"><span data-stu-id="dc286-1025">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="dc286-1026">Quando o arquivo *web.config* estiver presente e o site for iniciado normalmente, o IIS não atenderá a esses arquivos confidenciais se eles forem solicitados.</span><span class="sxs-lookup"><span data-stu-id="dc286-1026">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="dc286-1027">Se o arquivo *web.config* estiver ausente, nomeado incorretamente ou se não for possível configurar o site para inicialização normal, o IIS poderá servir arquivos confidenciais publicamente.</span><span class="sxs-lookup"><span data-stu-id="dc286-1027">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="dc286-1028">**O arquivo *Web. config* deve estar presente na implantação o tempo todo, corretamente nomeado e ser capaz de configurar o site para inicialização normal. Nunca remova o arquivo *Web. config* de uma implantação de produção.**</span><span class="sxs-lookup"><span data-stu-id="dc286-1028">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="dc286-1029">Transformação do Web.config</span><span class="sxs-lookup"><span data-stu-id="dc286-1029">Transform web.config</span></span>

<span data-ttu-id="dc286-1030">Se você precisar transformar o *Web.config* em publicação (por exemplo, definir variáveis ​​de ambiente com base na configuração, no perfil ou no ambiente), consulte <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="dc286-1030">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="dc286-1031">Configuração do IIS</span><span class="sxs-lookup"><span data-stu-id="dc286-1031">IIS configuration</span></span>

<span data-ttu-id="dc286-1032">**Sistemas operacionais do Windows Server**</span><span class="sxs-lookup"><span data-stu-id="dc286-1032">**Windows Server operating systems**</span></span>

<span data-ttu-id="dc286-1033">Habilite a função **Servidor Web (IIS)** e estabeleça serviços de função.</span><span class="sxs-lookup"><span data-stu-id="dc286-1033">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="dc286-1034">Use o assistente **Adicionar Funções e Recursos** por meio do menu **Gerenciar** ou do link no **Gerenciador do Servidor**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1034">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="dc286-1035">Na etapa **Funções de Servidor**, marque a caixa de **Servidor Web (IIS)**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1035">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![A função de Servidor Web IIS é selecionada na etapa Selecionar funções de servidor.](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="dc286-1037">Após a etapa **Recursos**, a etapa **Serviços de função** é carregada para o servidor Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="dc286-1037">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="dc286-1038">Selecione os serviços de função do IIS desejados ou aceite os serviços de função padrão fornecidos.</span><span class="sxs-lookup"><span data-stu-id="dc286-1038">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![Os serviços de função padrão são selecionados na etapa Selecionar serviços de função.](index/_static/role-services-ws2016.png)

   <span data-ttu-id="dc286-1040">**Autenticação do Windows (opcional)**</span><span class="sxs-lookup"><span data-stu-id="dc286-1040">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="dc286-1041">Para habilitar a autenticação do Windows, expanda os seguintes nós: segurança do **servidor Web**  >  **Security**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1041">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="dc286-1042">Selecione o recurso **Autenticação do Windows**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1042">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="dc286-1043">Para obter mais informações, consulte [autenticação \<windowsAuthentication> do Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) e [Configurar a autenticação do Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="dc286-1043">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="dc286-1044">**WebSockets (opcional)**</span><span class="sxs-lookup"><span data-stu-id="dc286-1044">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="dc286-1045">O WebSockets é compatível com o ASP.NET Core 1.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="dc286-1045">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="dc286-1046">Para habilitar WebSockets, expanda os seguintes nós: desenvolvimento de aplicativo de **servidor Web**  >  **Application Development**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1046">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="dc286-1047">Selecione o recurso **Protocolo WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1047">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="dc286-1048">Para obter mais informações, consulte [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="dc286-1048">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="dc286-1049">Continue para a etapa **Confirmação** para instalar os serviços e a função de servidor Web.</span><span class="sxs-lookup"><span data-stu-id="dc286-1049">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="dc286-1050">Uma reinicialização de servidor/IIS não é necessária após a instalação da função do **servidor Web (IIS)** .</span><span class="sxs-lookup"><span data-stu-id="dc286-1050">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="dc286-1051">**Sistemas operacionais de área de trabalho do Windows**</span><span class="sxs-lookup"><span data-stu-id="dc286-1051">**Windows desktop operating systems**</span></span>

<span data-ttu-id="dc286-1052">Habilite o **Console de Gerenciamento do IIS** e os **Serviços na World Wide Web**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1052">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="dc286-1053">Navegue até **Painel de Controle** > **Programas** > **Programas e Recursos** > **Ativar ou desativar recursos do Windows** (lado esquerdo da tela).</span><span class="sxs-lookup"><span data-stu-id="dc286-1053">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="dc286-1054">Abra o nó **Serviços de Informações da Internet**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1054">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="dc286-1055">Abra o nó **Ferramentas de Gerenciamento da Web**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1055">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="dc286-1056">Marque a caixa de **Console de Gerenciamento do IIS**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1056">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="dc286-1057">Marque a caixa de **Serviços na World Wide Web**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1057">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="dc286-1058">Aceite os recursos padrão dos **Serviços na World Wide Web** ou personalize os recursos do IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-1058">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="dc286-1059">**Autenticação do Windows (opcional)**</span><span class="sxs-lookup"><span data-stu-id="dc286-1059">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="dc286-1060">Para habilitar a autenticação do Windows, expanda os seguintes nós: segurança **dos serviços de World Wide Web**  >  **Security**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1060">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="dc286-1061">Selecione o recurso **Autenticação do Windows**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1061">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="dc286-1062">Para obter mais informações, consulte [autenticação \<windowsAuthentication> do Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) e [Configurar a autenticação do Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="dc286-1062">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="dc286-1063">**WebSockets (opcional)**</span><span class="sxs-lookup"><span data-stu-id="dc286-1063">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="dc286-1064">O WebSockets é compatível com o ASP.NET Core 1.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="dc286-1064">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="dc286-1065">Para habilitar WebSockets, expanda os seguintes nós: recursos de desenvolvimento de aplicativos **World Wide Web Services**  >  **Application Development Features**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1065">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="dc286-1066">Selecione o recurso **Protocolo WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1066">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="dc286-1067">Para obter mais informações, consulte [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="dc286-1067">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="dc286-1068">Se a instalação do IIS exigir uma reinicialização, reinicie o sistema.</span><span class="sxs-lookup"><span data-stu-id="dc286-1068">If the IIS installation requires a restart, restart the system.</span></span>

![O Console de Gerenciamento do IIS e os Serviços na World Wide Web estão selecionados em Recursos do Windows.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="dc286-1070">Instalar o pacote de hospedagem do .NET Core</span><span class="sxs-lookup"><span data-stu-id="dc286-1070">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="dc286-1071">Instale o *pacote de hospedagem do .NET Core* no sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="dc286-1071">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="dc286-1072">O pacote instala o tempo de execução do .NET Core, a biblioteca do .NET Core e o [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="dc286-1072">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="dc286-1073">O módulo permite que aplicativos do ASP.NET Core sejam executados por trás do IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-1073">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="dc286-1074">Se o pacote de hospedagem for instalado antes do IIS, a instalação do pacote deverá ser reparada.</span><span class="sxs-lookup"><span data-stu-id="dc286-1074">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="dc286-1075">Execute o instalador do pacote de hospedagem novamente depois de instalar o IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-1075">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="dc286-1076">Se o pacote de hospedagem for instalado após a instalação da versão de 64 bits (x64) do .NET Core, os SDKs poderão parecer estar ausentes ([Nenhum SDK do .NET Core foi detectado](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="dc286-1076">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="dc286-1077">Para resolver o problema, consulte <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span><span class="sxs-lookup"><span data-stu-id="dc286-1077">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="dc286-1078">Baixar</span><span class="sxs-lookup"><span data-stu-id="dc286-1078">Download</span></span>

1. <span data-ttu-id="dc286-1079">Navegue até a página [baixar o .NET Core](https://dotnet.microsoft.com/download/dotnet-core) .</span><span class="sxs-lookup"><span data-stu-id="dc286-1079">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="dc286-1080">Selecione a versão do .NET Core desejada.</span><span class="sxs-lookup"><span data-stu-id="dc286-1080">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="dc286-1081">Na coluna **Executar aplicativos – runtime**, localize a linha da versão de runtime do .NET Core desejada.</span><span class="sxs-lookup"><span data-stu-id="dc286-1081">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="dc286-1082">Baixe o instalador usando o link **pacote de hospedagem** .</span><span class="sxs-lookup"><span data-stu-id="dc286-1082">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="dc286-1083">Alguns instaladores contêm versões de lançamento que atingiram o EOL (fim da vida útil) e não têm mais suporte da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="dc286-1083">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="dc286-1084">Para saber mais, confira a [política de suporte](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="dc286-1084">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="dc286-1085">Instalar o pacote de hospedagem</span><span class="sxs-lookup"><span data-stu-id="dc286-1085">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="dc286-1086">Execute o instalador no servidor.</span><span class="sxs-lookup"><span data-stu-id="dc286-1086">Run the installer on the server.</span></span> <span data-ttu-id="dc286-1087">Os parâmetros a seguir estão disponíveis ao executar o instalador por meio de um shell de comando do administrador:</span><span class="sxs-lookup"><span data-stu-id="dc286-1087">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="dc286-1088">`OPT_NO_ANCM=1`: Ignorar a instalação do módulo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dc286-1088">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="dc286-1089">`OPT_NO_RUNTIME=1`: Ignorar a instalação do tempo de execução do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="dc286-1089">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="dc286-1090">Usado quando o servidor hospeda apenas [implantações independentes (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="dc286-1090">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="dc286-1091">`OPT_NO_SHAREDFX=1`: Ignorar a instalação da estrutura compartilhada ASP.NET (ASP.NET Runtime).</span><span class="sxs-lookup"><span data-stu-id="dc286-1091">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="dc286-1092">Usado quando o servidor hospeda apenas [implantações independentes (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="dc286-1092">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="dc286-1093">`OPT_NO_X86=1`: Ignorar a instalação de tempos de execução x86.</span><span class="sxs-lookup"><span data-stu-id="dc286-1093">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="dc286-1094">Use esse parâmetro quando você souber que não hospedará aplicativos de 32 bits.</span><span class="sxs-lookup"><span data-stu-id="dc286-1094">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="dc286-1095">Se houver uma possibilidade de hospedar aplicativos de 32 bits e 64 bits no futuro, não use esse parâmetro e instale ambos os runtimes.</span><span class="sxs-lookup"><span data-stu-id="dc286-1095">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="dc286-1096">`OPT_NO_SHARED_CONFIG_CHECK=1`: Desabilite a verificação para usar uma configuração compartilhada do IIS quando a configuração compartilhada (*ApplicationHost. config*) estiver no mesmo computador que a instalação do IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-1096">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="dc286-1097">*Disponível somente para instaladores do ASP.NET Core 2.2 ou Hosting Bundler posterior.*</span><span class="sxs-lookup"><span data-stu-id="dc286-1097">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="dc286-1098">Para obter mais informações, consulte <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="dc286-1098">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="dc286-1099">Reinicie o sistema ou execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="dc286-1099">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="dc286-1100">A reinicialização do IIS identifica uma alteração no CAMINHO do sistema, que é uma variável de ambiente, realizada pelo instalador.</span><span class="sxs-lookup"><span data-stu-id="dc286-1100">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="dc286-1101">Não é necessário interromper manualmente os sites individuais no IIS ao instalar o pacote de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="dc286-1101">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="dc286-1102">Aplicativos hospedados (sites do IIS) são reiniciados quando o IIS é reiniciado.</span><span class="sxs-lookup"><span data-stu-id="dc286-1102">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="dc286-1103">Os aplicativos são iniciados novamente quando recebem sua primeira solicitação, incluindo a partir do [módulo de inicialização do aplicativo](#application-initialization-module-and-idle-timeout).</span><span class="sxs-lookup"><span data-stu-id="dc286-1103">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="dc286-1104">ASP.NET Core adota o comportamento de roll-forward para versões de patch de pacotes de estrutura compartilhados.</span><span class="sxs-lookup"><span data-stu-id="dc286-1104">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="dc286-1105">Quando os aplicativos hospedados pelo IIS são reiniciados com o IIS, os aplicativos são carregados com as versões de patch mais recentes de seus pacotes referenciados quando recebem sua primeira solicitação.</span><span class="sxs-lookup"><span data-stu-id="dc286-1105">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="dc286-1106">Se o IIS não for reiniciado, os aplicativos serão reiniciados e exibirão o comportamento de roll-forward quando seus processos de trabalho forem reciclados e receberem sua primeira solicitação.</span><span class="sxs-lookup"><span data-stu-id="dc286-1106">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="dc286-1107">Para obter informações sobre a Configuração Compartilhada do IIS, consulte [Módulo do ASP.NET Core com a Configuração Compartilhada do IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="dc286-1107">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="dc286-1108">Instalar a Implantação da Web durante a publicação com o Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dc286-1108">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="dc286-1109">Ao implantar aplicativos para servidores com [Implantação da Web](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), instale a versão mais recente da Implantação da Web no servidor.</span><span class="sxs-lookup"><span data-stu-id="dc286-1109">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="dc286-1110">Para instalar a Implantação da Web, use o [WebPI (Web Platform Installer)](https://www.microsoft.com/web/downloads/platform.aspx) ou obtenha um instalador diretamente no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=43717).</span><span class="sxs-lookup"><span data-stu-id="dc286-1110">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="dc286-1111">O método preferencial é usar o WebPI.</span><span class="sxs-lookup"><span data-stu-id="dc286-1111">The preferred method is to use WebPI.</span></span> <span data-ttu-id="dc286-1112">O WebPI oferece uma instalação autônoma e uma configuração para provedores de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="dc286-1112">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="dc286-1113">Criar o site do IIS</span><span class="sxs-lookup"><span data-stu-id="dc286-1113">Create the IIS site</span></span>

1. <span data-ttu-id="dc286-1114">No sistema de hospedagem, crie uma pasta para conter arquivos e pastas publicados do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1114">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="dc286-1115">Em uma etapa a seguir, o caminho da pasta é fornecido ao IIS como o caminho físico para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1115">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="dc286-1116">Para obter mais informações sobre o layout de arquivo e a pasta de implantação de um aplicativo, confira <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="dc286-1116">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="dc286-1117">No Gerenciador do IIS, abra o nó do servidor no painel **conexões** .</span><span class="sxs-lookup"><span data-stu-id="dc286-1117">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="dc286-1118">Clique com botão direito do mouse na pasta **Sites**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1118">Right-click the **Sites** folder.</span></span> <span data-ttu-id="dc286-1119">Selecione **Adicionar Site** no menu contextual.</span><span class="sxs-lookup"><span data-stu-id="dc286-1119">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="dc286-1120">Forneça um **Nome do site** e defina o **Caminho físico** como a pasta de implantação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1120">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="dc286-1121">Forneça a configuração de **Associação** e crie o site selecionando **OK**:</span><span class="sxs-lookup"><span data-stu-id="dc286-1121">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![Forneça o Nome do site, o caminho físico e o Nome do host na etapa Adicionar Site.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="dc286-1123">Associações de curinga de nível superior (`http://*:80/` e `http://+:80`) **não** devem ser usadas.</span><span class="sxs-lookup"><span data-stu-id="dc286-1123">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="dc286-1124">Associações de curinga de nível superior podem abrir o aplicativo para vulnerabilidades de segurança.</span><span class="sxs-lookup"><span data-stu-id="dc286-1124">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="dc286-1125">Isso se aplica a curingas fortes e fracos.</span><span class="sxs-lookup"><span data-stu-id="dc286-1125">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="dc286-1126">Use nomes de host explícitos em vez de curingas.</span><span class="sxs-lookup"><span data-stu-id="dc286-1126">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="dc286-1127">Associações de curinga de subdomínio (por exemplo, `*.mysub.com`) não têm esse risco de segurança se você controlar o domínio pai completo (em vez de `*.com`, o qual é vulnerável).</span><span class="sxs-lookup"><span data-stu-id="dc286-1127">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="dc286-1128">Veja [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="dc286-1128">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="dc286-1129">No nó do servidor, selecione **Pools de Aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1129">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="dc286-1130">Clique com o botão direito do mouse no pool de aplicativos do site e selecione **Configurações Básicas** no menu contextual.</span><span class="sxs-lookup"><span data-stu-id="dc286-1130">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="dc286-1131">Na janela **Editar Pool de Aplicativos**, defina a **versão do CLR do .NET** como **Sem Código Gerenciado**:</span><span class="sxs-lookup"><span data-stu-id="dc286-1131">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![Defina Sem Código Gerenciado para a versão do CLR do .NET.](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="dc286-1133">O ASP.NET Core é executado em um processo separado e gerencia o runtime.</span><span class="sxs-lookup"><span data-stu-id="dc286-1133">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="dc286-1134">O ASP.NET Core não depende do carregamento do CLR de Área de trabalho (CLR do .NET)&mdash;o Core Common Language Runtime (CoreCLR) para o .NET Core é inicializado para hospedar o aplicativo no processo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="dc286-1134">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="dc286-1135">Definir a **versão do CLR do .NET** como **Sem Código Gerenciado** é opcional, porém recomendado.</span><span class="sxs-lookup"><span data-stu-id="dc286-1135">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="dc286-1136">*ASP.NET Core 2.2 ou posterior*: para uma [implantação autocontida](/dotnet/core/deploying/#self-contained-deployments-scd) de 64 bits (x64) que usa o [modelo de hospedagem em processo](#in-process-hosting-model), desabilite o pool de aplicativos para processos de 32 bits (x86).</span><span class="sxs-lookup"><span data-stu-id="dc286-1136">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="dc286-1137">Na barra lateral **Ações** do Gerenciador do IIS > **Pools de Aplicativos**, selecione **Definir Padrões do Pool de Aplicativos** ou **Configurações Avançadas**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1137">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="dc286-1138">Localize **Habilitar Aplicativos de 32 bits** e defina o valor como `False`.</span><span class="sxs-lookup"><span data-stu-id="dc286-1138">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="dc286-1139">Essa configuração não afeta os aplicativos implantados para a [hospedagem fora do processo](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="dc286-1139">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="dc286-1140">Confirme se a identidade do modelo de processo tem as permissões apropriadas.</span><span class="sxs-lookup"><span data-stu-id="dc286-1140">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="dc286-1141">Se a identidade padrão do pool de aplicativos (**modelo de processo**  >  **Identity** ) for alterada de **ApplicationPoolIdentity** para outra identidade, verifique se a nova identidade tem as permissões necessárias para acessar a pasta, o banco de dados e outros recursos necessários do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1141">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="dc286-1142">Por exemplo, o pool de aplicativos requer acesso de leitura e gravação às pastas nas quais o aplicativo lê e grava os arquivos.</span><span class="sxs-lookup"><span data-stu-id="dc286-1142">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="dc286-1143">**Configuração de Autenticação do Windows (opcional)**</span><span class="sxs-lookup"><span data-stu-id="dc286-1143">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="dc286-1144">Para saber mais, veja [Configurar a Autenticação do Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="dc286-1144">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="dc286-1145">Implantar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="dc286-1145">Deploy the app</span></span>

<span data-ttu-id="dc286-1146">Implante o aplicativo na pasta **caminho físico** do IIS que foi estabelecida na seção [Criar o site do IIS](#create-the-iis-site).</span><span class="sxs-lookup"><span data-stu-id="dc286-1146">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="dc286-1147">[Implantação da Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) é o mecanismo recomendado para implantação, mas existem várias opções para mover o aplicativo da pasta *publicar* do projeto para a pasta de implantação do sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="dc286-1147">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="dc286-1148">Implantação da Web com o Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dc286-1148">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="dc286-1149">Consulte o tópico [Perfis de publicação do Visual Studio para implantação de aplicativos ASP.NET Core](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) para saber como criar um perfil de publicação para uso com a Implantação da Web.</span><span class="sxs-lookup"><span data-stu-id="dc286-1149">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="dc286-1150">Se o provedor de hospedagem fornecer um Perfil de Publicação ou o suporte para a criação de um, baixe o perfil e importe-o usando a caixa de diálogo **Publicar** do Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="dc286-1150">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![Página de caixa de diálogo Publicar](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="dc286-1152">Implantação da Web fora do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dc286-1152">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="dc286-1153">Também é possível usar a [Implantação da Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) fora do Visual Studio na linha de comando.</span><span class="sxs-lookup"><span data-stu-id="dc286-1153">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="dc286-1154">Para obter mais informações, consulte [Ferramenta de Implantação da Web](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span><span class="sxs-lookup"><span data-stu-id="dc286-1154">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="dc286-1155">Alternativas à Implantação da Web</span><span class="sxs-lookup"><span data-stu-id="dc286-1155">Alternatives to Web Deploy</span></span>

<span data-ttu-id="dc286-1156">Use qualquer um dos vários métodos para mover o aplicativo para o sistema host, como cópia manual, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy) ou [PowerShell](/powershell/).</span><span class="sxs-lookup"><span data-stu-id="dc286-1156">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="dc286-1157">Para obter mais informações sobre a implantação do ASP.NET Core no IIS, consulte a seção [Recursos de implantação para administradores do IIS](#deployment-resources-for-iis-administrators).</span><span class="sxs-lookup"><span data-stu-id="dc286-1157">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="dc286-1158">Procurar no site</span><span class="sxs-lookup"><span data-stu-id="dc286-1158">Browse the website</span></span>

<span data-ttu-id="dc286-1159">Depois de implantar o aplicativo no sistema de hospedagem, faça uma solicitação para um dos pontos de extremidade públicos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1159">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="dc286-1160">No exemplo a seguir, o site está associado a um **Nome do Host** IIS de `www.mysite.com` na **Porta** `80`.</span><span class="sxs-lookup"><span data-stu-id="dc286-1160">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="dc286-1161">É feita uma solicitação para `http://www.mysite.com`:</span><span class="sxs-lookup"><span data-stu-id="dc286-1161">A request is made to `http://www.mysite.com`:</span></span>

![O navegador Microsoft Edge carregou a página de inicialização do IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="dc286-1163">Arquivos de implantação bloqueados</span><span class="sxs-lookup"><span data-stu-id="dc286-1163">Locked deployment files</span></span>

<span data-ttu-id="dc286-1164">Os arquivos na pasta de implantação são bloqueados quando o aplicativo está em execução.</span><span class="sxs-lookup"><span data-stu-id="dc286-1164">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="dc286-1165">Os arquivos bloqueados não podem ser substituídos durante a implantação.</span><span class="sxs-lookup"><span data-stu-id="dc286-1165">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="dc286-1166">Para liberar os arquivos bloqueados em uma implantação, interrompa o pool de aplicativos usando **uma** das abordagens a seguir:</span><span class="sxs-lookup"><span data-stu-id="dc286-1166">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="dc286-1167">Use a Implantação da Web e referencie `Microsoft.NET.Sdk.Web` no arquivo do projeto.</span><span class="sxs-lookup"><span data-stu-id="dc286-1167">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="dc286-1168">Um arquivo *app_offline.htm* é colocado na raiz do diretório de aplicativo da Web.</span><span class="sxs-lookup"><span data-stu-id="dc286-1168">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="dc286-1169">Quando o arquivo estiver presente, o módulo do ASP.NET Core apenas desligará o aplicativo e servirá o arquivo *app_offline.htm* durante a implantação.</span><span class="sxs-lookup"><span data-stu-id="dc286-1169">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="dc286-1170">Para obter mais informações, consulte [Referência de configuração do módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="dc286-1170">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="dc286-1171">Manualmente interrompa o pool de aplicativos no Gerenciador do IIS no servidor.</span><span class="sxs-lookup"><span data-stu-id="dc286-1171">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="dc286-1172">Use o PowerShell para descartar *App_offline. htm* (requer o PowerShell 5 ou posterior):</span><span class="sxs-lookup"><span data-stu-id="dc286-1172">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="dc286-1173">Proteção de dados</span><span class="sxs-lookup"><span data-stu-id="dc286-1173">Data protection</span></span>

<span data-ttu-id="dc286-1174">A [pilha Proteção de Dados do ASP.NET Core](xref:security/data-protection/introduction) é usada por vários [middlewares](xref:fundamentals/middleware/index) ASP.NET Core, incluindo aqueles usados na autenticação.</span><span class="sxs-lookup"><span data-stu-id="dc286-1174">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="dc286-1175">Mesmo se as APIs de proteção de dados não forem chamadas pelo código do usuário, a proteção de dados deverá ser configurada com um script de implantação ou no código do usuário para criar um [repositório de chaves](xref:security/data-protection/implementation/key-management) criptográfico persistente.</span><span class="sxs-lookup"><span data-stu-id="dc286-1175">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="dc286-1176">Se a proteção de dados não estiver configurada, as chaves serão mantidas na memória e descartadas quando o aplicativo for reiniciado.</span><span class="sxs-lookup"><span data-stu-id="dc286-1176">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="dc286-1177">Se o token de autenticação for armazenado na memória quando o aplicativo for reiniciado:</span><span class="sxs-lookup"><span data-stu-id="dc286-1177">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="dc286-1178">Todos os tokens de autenticação baseados em cookies serão invalidados.</span><span class="sxs-lookup"><span data-stu-id="dc286-1178">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="dc286-1179">Os usuários precisam entrar novamente na próxima solicitação deles.</span><span class="sxs-lookup"><span data-stu-id="dc286-1179">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="dc286-1180">Todos os dados protegidos com o token de autenticação não poderão mais ser descriptografados.</span><span class="sxs-lookup"><span data-stu-id="dc286-1180">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="dc286-1181">Isso pode incluir [tokens CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) e [ASP.NET Core cookies TempData do MVC](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="dc286-1181">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="dc286-1182">Para configurar a proteção de dados no IIS para persistir o token de autenticação, use **uma** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="dc286-1182">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="dc286-1183">**Criar chaves de registro de proteção de dados**</span><span class="sxs-lookup"><span data-stu-id="dc286-1183">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="dc286-1184">As chaves de proteção de dados usadas pelos aplicativos ASP.NET Core são armazenadas no registro externo aos aplicativos.</span><span class="sxs-lookup"><span data-stu-id="dc286-1184">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="dc286-1185">Para persistir as chaves de determinado aplicativo, crie uma chave de registro para o pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="dc286-1185">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="dc286-1186">Para instalações autônomas do IIS não Web Farm, você pode usar o [script Provision-AutoGenKeys.ps1 de Proteção de Dados do PowerShell](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) para cada pool de aplicativos usado com um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dc286-1186">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="dc286-1187">Esse script cria uma chave de registro no registro HKLM que é acessível apenas para a conta do processo de trabalho do pool de aplicativos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1187">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="dc286-1188">As chaves são criptografadas em repouso usando a DPAPI com uma chave para todo o computador.</span><span class="sxs-lookup"><span data-stu-id="dc286-1188">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="dc286-1189">Em cenários de web farm, um aplicativo pode ser configurado para usar um caminho UNC para armazenar seu token de autenticação de proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="dc286-1189">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="dc286-1190">Por padrão, as chaves de proteção de dados não são criptografadas.</span><span class="sxs-lookup"><span data-stu-id="dc286-1190">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="dc286-1191">Garanta que as permissões de arquivo de o compartilhamento de rede sejam limitadas à conta do Windows na qual o aplicativo é executado.</span><span class="sxs-lookup"><span data-stu-id="dc286-1191">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="dc286-1192">Um certificado X509 pode ser usado para proteger chaves em repouso.</span><span class="sxs-lookup"><span data-stu-id="dc286-1192">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="dc286-1193">Considere um mecanismo para permitir aos usuários carregar certificados: coloque os certificados no repositório de certificados confiáveis do usuário e certifique-se de que eles estejam disponíveis em todos os computadores nos quais o aplicativo do usuário é executado.</span><span class="sxs-lookup"><span data-stu-id="dc286-1193">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="dc286-1194">Veja [Configurar a proteção de dados do ASP.NET Core](xref:security/data-protection/configuration/overview) para obter detalhes.</span><span class="sxs-lookup"><span data-stu-id="dc286-1194">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="dc286-1195">**Configurar o pool de aplicativos do IIS para carregar o perfil do usuário**</span><span class="sxs-lookup"><span data-stu-id="dc286-1195">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="dc286-1196">Essa configuração está na seção **Modelo de processo** nas **Configurações avançadas** do pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="dc286-1196">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="dc286-1197">Defina **carregar perfil do usuário** como `True` .</span><span class="sxs-lookup"><span data-stu-id="dc286-1197">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="dc286-1198">Quando definido como `True`, as chaves são armazenadas no diretório do perfil do usuário e protegidas usando DPAPI com uma chave específica para a conta de usuário.</span><span class="sxs-lookup"><span data-stu-id="dc286-1198">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="dc286-1199">As chaves são persistidas para a pasta *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys*.</span><span class="sxs-lookup"><span data-stu-id="dc286-1199">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="dc286-1200">O [atributo setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) do pool de aplicativos também deve ser habilitado.</span><span class="sxs-lookup"><span data-stu-id="dc286-1200">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="dc286-1201">O valor padrão de `setProfileEnvironment` é `true`.</span><span class="sxs-lookup"><span data-stu-id="dc286-1201">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="dc286-1202">Em alguns cenários (por exemplo, um SO Windows), `setProfileEnvironment` é definido como `false`.</span><span class="sxs-lookup"><span data-stu-id="dc286-1202">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="dc286-1203">Se as chaves não estiverem armazenadas no diretório do perfil do usuário como esperado:</span><span class="sxs-lookup"><span data-stu-id="dc286-1203">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="dc286-1204">navegue até a pasta *%windir%/system32/inetsrv/config*.</span><span class="sxs-lookup"><span data-stu-id="dc286-1204">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="dc286-1205">Abra o arquivo *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="dc286-1205">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="dc286-1206">Localize o elemento `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.</span><span class="sxs-lookup"><span data-stu-id="dc286-1206">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="dc286-1207">Confirme se o atributo `setProfileEnvironment` não está presente, que tem como padrão o valor `true`, ou defina explicitamente o valor do atributo como `true`.</span><span class="sxs-lookup"><span data-stu-id="dc286-1207">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="dc286-1208">**Usar o sistema de arquivos como um repositório de tokens de autenticação**</span><span class="sxs-lookup"><span data-stu-id="dc286-1208">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="dc286-1209">Ajuste o código do aplicativo para [usar o sistema de arquivos como um repositório de tokens de autenticação](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="dc286-1209">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="dc286-1210">Use um certificado X509 para proteger o token de autenticação e verifique se ele é um certificado confiável.</span><span class="sxs-lookup"><span data-stu-id="dc286-1210">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="dc286-1211">Se o certificado for autoassinado, você deverá colocá-lo no repositório Raiz confiável.</span><span class="sxs-lookup"><span data-stu-id="dc286-1211">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="dc286-1212">Ao usar o IIS em uma web farm:</span><span class="sxs-lookup"><span data-stu-id="dc286-1212">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="dc286-1213">Use um compartilhamento de arquivos que pode ser acessado por todos os computadores.</span><span class="sxs-lookup"><span data-stu-id="dc286-1213">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="dc286-1214">Implante um certificado X509 em cada computador.</span><span class="sxs-lookup"><span data-stu-id="dc286-1214">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="dc286-1215">Configure a [proteção de dados no código](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="dc286-1215">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="dc286-1216">**Definir uma política para todo o computador para proteção de dados**</span><span class="sxs-lookup"><span data-stu-id="dc286-1216">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="dc286-1217">O sistema de proteção de dados tem suporte limitado para a configuração da [política de todo o computador](xref:security/data-protection/configuration/machine-wide-policy) padrão para todos os aplicativos que consomem as APIs de proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="dc286-1217">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="dc286-1218">Para obter mais informações, consulte <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="dc286-1218">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="dc286-1219">Diretórios virtuais</span><span class="sxs-lookup"><span data-stu-id="dc286-1219">Virtual Directories</span></span>

<span data-ttu-id="dc286-1220">[Diretórios virtuais IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) não são compatíveis com aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dc286-1220">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="dc286-1221">Um aplicativo pode ser hospedado como um [subaplicativo](#sub-applications).</span><span class="sxs-lookup"><span data-stu-id="dc286-1221">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="dc286-1222">Subaplicativos</span><span class="sxs-lookup"><span data-stu-id="dc286-1222">Sub-applications</span></span>

<span data-ttu-id="dc286-1223">Um aplicativo ASP.NET Core pode ser hospedado como um [subaplicativo IIS (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span><span class="sxs-lookup"><span data-stu-id="dc286-1223">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="dc286-1224">A parte do caminho do subaplicativo se torna parte da URL raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1224">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="dc286-1225">Links de ativos estáticos dentro do subaplicativo devem usar a notação de sinal de til e barra (`~/`).</span><span class="sxs-lookup"><span data-stu-id="dc286-1225">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="dc286-1226">A notação de sinal de til e barra aciona um [Auxiliar de Marca](xref:mvc/views/tag-helpers/intro) para preceder a base de caminho do subaplicativo ao link relativo renderizado.</span><span class="sxs-lookup"><span data-stu-id="dc286-1226">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="dc286-1227">Para um subaplicativo no `/subapp_path`, uma imagem vinculada com `src="~/image.png"` é renderizada como `src="/subapp_path/image.png"`.</span><span class="sxs-lookup"><span data-stu-id="dc286-1227">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="dc286-1228">O Middleware de Arquivo Estático do aplicativo raiz não processa a solicitação de arquivo estático.</span><span class="sxs-lookup"><span data-stu-id="dc286-1228">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="dc286-1229">A solicitação é processada pelo Middleware de Arquivo Estático do subaplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1229">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="dc286-1230">Se um atributo de ativo estático `src` for definido como um caminho absoluto (por exemplo, `src="/image.png"`), o link será renderizado sem a base de caminho do subaplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1230">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="dc286-1231">O Middleware de Arquivos Estáticos do aplicativo raiz tenta fornecer o ativo do [webroot](xref:fundamentals/index#web-root) da raiz do aplicativo, que resulta em uma resposta *404 – Não encontrado*, a menos que o ativo estático esteja disponível no aplicativo raiz.</span><span class="sxs-lookup"><span data-stu-id="dc286-1231">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="dc286-1232">Para hospedar um aplicativo ASP.NET Core como um subaplicativo em outro aplicativo do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="dc286-1232">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="dc286-1233">Estabeleça um pool de aplicativos para o subaplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1233">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="dc286-1234">Defina a **versão do CLR do .NET** como **Sem Código Gerenciado** porque o Core Common Language Runtime (CoreCLR) do .NET Core é inicializado para hospedar o aplicativo no processo de trabalho, não no CLR de Área de trabalho (CLR do .NET).</span><span class="sxs-lookup"><span data-stu-id="dc286-1234">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="dc286-1235">Adicione o site raiz no Gerenciador do IIS com o subaplicativo em uma pasta no site raiz.</span><span class="sxs-lookup"><span data-stu-id="dc286-1235">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="dc286-1236">Clique com o botão direito do mouse na pasta do subaplicativo no Gerenciador do IIS e selecione **Converter em aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1236">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="dc286-1237">Na caixa de diálogo **Adicionar Aplicativo**, use o botão **Selecionar** no **Pool de Aplicativos** para atribuir o pool de aplicativos que você criou ao subaplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1237">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="dc286-1238">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1238">Select **OK**.</span></span>

<span data-ttu-id="dc286-1239">A atribuição de um pool de aplicativos separado para o subaplicativo é um requisito ao usar o modelo de hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1239">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="dc286-1240">Para obter mais informações sobre o modelo de hospedagem em processo e como configurar o módulo ASP.NET Core, consulte <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="dc286-1240">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="dc286-1241">Configuração do IIS com web.config</span><span class="sxs-lookup"><span data-stu-id="dc286-1241">Configuration of IIS with web.config</span></span>

<span data-ttu-id="dc286-1242">A configuração do IIS é influenciada pela seção `<system.webServer>` do *web.config* para cenários do IIS que são funcionais para aplicativos ASP.NET Core com o Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dc286-1242">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="dc286-1243">Por exemplo, a configuração do IIS é funcional para a compactação dinâmica.</span><span class="sxs-lookup"><span data-stu-id="dc286-1243">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="dc286-1244">Se o IIS for configurado no nível do servidor para usar a compactação dinâmica, o elemento `<urlCompression>` no arquivo *web.config* do aplicativo pode desabilitá-la para um aplicativo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dc286-1244">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="dc286-1245">Para obter mais informações, consulte estes tópicos:</span><span class="sxs-lookup"><span data-stu-id="dc286-1245">For more information, see the following topics:</span></span>

* [<span data-ttu-id="dc286-1246">Referência de configuração para\<system.webServer></span><span class="sxs-lookup"><span data-stu-id="dc286-1246">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="dc286-1247">Para definir variáveis de ambiente para aplicativos individuais em execução em pools de aplicativos isolados (com suporte para o IIS 10,0 ou posterior), consulte a seção de *comando Appcmd. exe* do tópico [ \<environmentVariables> variáveis de ambiente](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) na documentação de referência do IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-1247">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="dc286-1248">Seções de configuração de web.config</span><span class="sxs-lookup"><span data-stu-id="dc286-1248">Configuration sections of web.config</span></span>

<span data-ttu-id="dc286-1249">As seções de configuração de aplicativos ASP.NET 4.x em *web.config* não são usadas por aplicativos ASP.NET Core para configuração:</span><span class="sxs-lookup"><span data-stu-id="dc286-1249">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="dc286-1250">Aplicativos ASP.NET Core são configurados para usar outros provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="dc286-1250">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="dc286-1251">Para obter mais informações, consulte [Configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="dc286-1251">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="dc286-1252">Pools de aplicativos</span><span class="sxs-lookup"><span data-stu-id="dc286-1252">Application Pools</span></span>

<span data-ttu-id="dc286-1253">O isolamento do pool de aplicativos é determinado pelo modelo de hospedagem:</span><span class="sxs-lookup"><span data-stu-id="dc286-1253">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="dc286-1254">Hospedagem em processo: os aplicativos devem ser executados em pools de aplicativos separados.</span><span class="sxs-lookup"><span data-stu-id="dc286-1254">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="dc286-1255">Hospedagem fora do processo: é recomendável isolar os aplicativos uns dos outros executando cada aplicativo em seu próprio pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="dc286-1255">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="dc286-1256">A caixa de diálogo **Adicionar Site** do IIS usa como padrão um único pool de aplicativos por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1256">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="dc286-1257">Quando um **Nome de site** é fornecido, o texto é transferido automaticamente para a caixa de texto **Pool de aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1257">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="dc286-1258">Um novo pool de aplicativos é criado usando o nome do site quando você adicionar o site.</span><span class="sxs-lookup"><span data-stu-id="dc286-1258">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="dc286-1259">Pool de aplicativosIdentity</span><span class="sxs-lookup"><span data-stu-id="dc286-1259">Application Pool Identity</span></span>

<span data-ttu-id="dc286-1260">Uma conta de identidade do pool de aplicativos permite executar um aplicativo em uma conta exclusiva sem a necessidade de criar e gerenciar domínios ou contas locais.</span><span class="sxs-lookup"><span data-stu-id="dc286-1260">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="dc286-1261">No IIS 8.0 ou posterior, o WAS (Processo de trabalho do administrador) do IIS cria uma conta virtual com o nome do novo pool de aplicativos e executa os processos de trabalho do pool de aplicativos nesta conta por padrão.</span><span class="sxs-lookup"><span data-stu-id="dc286-1261">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="dc286-1262">No console de gerenciamento do IIS, em **Configurações avançadas** para o pool de aplicativos, verifique se o **Identity** está definido para usar **ApplicationPoolIdentity**:</span><span class="sxs-lookup"><span data-stu-id="dc286-1262">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![Caixa de diálogo Configurações avançadas do pool de aplicativos](index/_static/apppool-identity.png)

<span data-ttu-id="dc286-1264">O processo de gerenciamento do IIS cria um identificador seguro com o nome do pool de aplicativos no Sistema de segurança do Windows.</span><span class="sxs-lookup"><span data-stu-id="dc286-1264">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="dc286-1265">Os recursos podem ser protegidos usando essa identidade.</span><span class="sxs-lookup"><span data-stu-id="dc286-1265">Resources can be secured using this identity.</span></span> <span data-ttu-id="dc286-1266">No entanto, essa identidade não é uma conta de usuário real e não será mostrada no Console de Gerenciamento de Usuários do Windows.</span><span class="sxs-lookup"><span data-stu-id="dc286-1266">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="dc286-1267">Se o processo de trabalho do IIS requerer acesso elevado ao aplicativo, modifique a ACL (lista de controle de acesso) do diretório que contém o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="dc286-1267">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="dc286-1268">Abra o Windows Explorer e navegue para o diretório.</span><span class="sxs-lookup"><span data-stu-id="dc286-1268">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="dc286-1269">Clique com o botão direito do mouse no diretório e selecione **Propriedades**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1269">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="dc286-1270">Na guia **Segurança**, selecione o botão **Editar** e, em seguida, no botão **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1270">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="dc286-1271">Clique no botão **Locais** e verifique se o sistema está selecionado.</span><span class="sxs-lookup"><span data-stu-id="dc286-1271">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="dc286-1272">Insira **IIS AppPool\\<nome_pool_aplicativos>** na área **Inserir os nomes de objeto a serem selecionados**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1272">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="dc286-1273">Selecione o botão **Verificar Nomes**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1273">Select the **Check Names** button.</span></span> <span data-ttu-id="dc286-1274">Para o *DefaultAppPool*, verifique os nomes usando **IIS AppPool\DefaultAppPool**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1274">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="dc286-1275">Quando o botão **Verificar Nomes** é selecionado, um valor de **DefaultAppPool** é indicado na área de nomes de objeto.</span><span class="sxs-lookup"><span data-stu-id="dc286-1275">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="dc286-1276">Não é possível inserir o nome do pool de aplicativos diretamente na área de nomes de objeto.</span><span class="sxs-lookup"><span data-stu-id="dc286-1276">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="dc286-1277">Use o formato **IIS AppPool\\<nome_pool_aplicativos>** ao verificar o nome do objeto.</span><span class="sxs-lookup"><span data-stu-id="dc286-1277">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![Selecione a caixa de diálogo de usuários ou grupos para a pasta do aplicativo: o nome do pool de aplicativos "DefaultAppPool" é anexado ao "IIS AppPool\" na área de nomes de objeto antes de selecionar"Verificar Nomes".](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="dc286-1279">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1279">Select **OK**.</span></span>

   ![Selecione a caixa de diálogo de usuários ou grupos para a pasta do aplicativo: depois de selecionar "Verificar Nomes", o nome do objeto "DefaultAppPool" é mostrado na área de nomes de objeto.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="dc286-1281">As permissões de leitura &amp; execução devem ser concedidas por padrão.</span><span class="sxs-lookup"><span data-stu-id="dc286-1281">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="dc286-1282">Forneça permissões adicionais conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="dc286-1282">Provide additional permissions as needed.</span></span>

<span data-ttu-id="dc286-1283">O acesso também pode ser concedido por meio de um prompt de comando usando a ferramenta **ICACLS**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1283">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="dc286-1284">Usando o *DefaultAppPool* como exemplo, o comando a seguir é usado:</span><span class="sxs-lookup"><span data-stu-id="dc286-1284">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="dc286-1285">Para saber mais, veja o tópico [icacls](/windows-server/administration/windows-commands/icacls).</span><span class="sxs-lookup"><span data-stu-id="dc286-1285">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="dc286-1286">Suporte do HTTP/2</span><span class="sxs-lookup"><span data-stu-id="dc286-1286">HTTP/2 support</span></span>

<span data-ttu-id="dc286-1287">O [HTTP/2](https://httpwg.org/specs/rfc7540.html) é compatível com ASP.NET Core nos seguintes cenários de implantação de IIS:</span><span class="sxs-lookup"><span data-stu-id="dc286-1287">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="dc286-1288">Em processo</span><span class="sxs-lookup"><span data-stu-id="dc286-1288">In-process</span></span>
  * <span data-ttu-id="dc286-1289">Windows Server 2016/Windows 10 ou posterior; IIS 10 ou posterior</span><span class="sxs-lookup"><span data-stu-id="dc286-1289">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="dc286-1290">Conexão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="dc286-1290">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="dc286-1291">Fora do processo</span><span class="sxs-lookup"><span data-stu-id="dc286-1291">Out-of-process</span></span>
  * <span data-ttu-id="dc286-1292">Windows Server 2016/Windows 10 ou posterior; IIS 10 ou posterior</span><span class="sxs-lookup"><span data-stu-id="dc286-1292">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="dc286-1293">Conexões de servidor de borda voltadas para o público usam HTTP/2, mas a conexão de proxy reverso para o [servidor Kestrel](xref:fundamentals/servers/kestrel) usa HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="dc286-1293">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="dc286-1294">Conexão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="dc286-1294">TLS 1.2 or later connection</span></span>

<span data-ttu-id="dc286-1295">Para uma implantação em processo quando uma conexão HTTP/2 for estabelecida, o [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatará `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="dc286-1295">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="dc286-1296">Para uma implantação fora de processo quando uma conexão HTTP/2 for estabelecida, o [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatará `HTTP/1.1`.</span><span class="sxs-lookup"><span data-stu-id="dc286-1296">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="dc286-1297">Para saber mais sobre os modelos de hospedagem em processo e fora de processo, confira <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="dc286-1297">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="dc286-1298">O HTTP/2 está habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="dc286-1298">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="dc286-1299">As conexões retornarão para HTTP/1.1 se uma conexão HTTP/2 não for estabelecida.</span><span class="sxs-lookup"><span data-stu-id="dc286-1299">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="dc286-1300">Para obter mais informações sobre a configuração de HTTP/2 com implantações do IIS, consulte [HTTP/2 no IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="dc286-1300">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="dc286-1301">Solicitações de simulação do CORS</span><span class="sxs-lookup"><span data-stu-id="dc286-1301">CORS preflight requests</span></span>

<span data-ttu-id="dc286-1302">*Esta seção só se aplica a aplicativos ASP.NET Core com o .NET Framework como destino.*</span><span class="sxs-lookup"><span data-stu-id="dc286-1302">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="dc286-1303">Para um aplicativo ASP.NET Core com o .NET Framework como destino, as solicitações OPTIONS não são passadas para o aplicativo por padrão no IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-1303">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="dc286-1304">Para saber como configurar os manipuladores do IIS do aplicativo no *Web. config* para passar solicitações de opções, consulte [habilitar solicitações entre origens no ASP.NET Web API 2: como o CORS funciona](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span><span class="sxs-lookup"><span data-stu-id="dc286-1304">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="dc286-1305">Módulo de Inicialização de Aplicativo e Tempo Limite de Ociosidade</span><span class="sxs-lookup"><span data-stu-id="dc286-1305">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="dc286-1306">Quando hospedado no IIS pela versão 2 do Módulo do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="dc286-1306">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="dc286-1307">[Módulo de inicialização do aplicativo](#application-initialization-module): hospedado [em processo](#in-process-hosting-model) ou [fora do processo](#out-of-process-hosting-model) do aplicativo pode ser configurado para iniciar automaticamente em uma reinicialização do processo de trabalho ou reinicialização do servidor.</span><span class="sxs-lookup"><span data-stu-id="dc286-1307">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="dc286-1308">[Tempo limite de ociosidade](#idle-timeout): o aplicativo hospedado [no processo](#in-process-hosting-model) pode ser configurado para não atingir o tempo limite durante períodos de inatividade.</span><span class="sxs-lookup"><span data-stu-id="dc286-1308">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="dc286-1309">Módulo de Inicialização de Aplicativo</span><span class="sxs-lookup"><span data-stu-id="dc286-1309">Application Initialization Module</span></span>

<span data-ttu-id="dc286-1310">*Aplica-se a aplicativos hospedados em processo e fora de processo.*</span><span class="sxs-lookup"><span data-stu-id="dc286-1310">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="dc286-1311">[Inicialização de Aplicativo IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) é um recurso do IIS que envia uma solicitação HTTP para o aplicativo quando o pool de aplicativos é iniciado ou reciclado.</span><span class="sxs-lookup"><span data-stu-id="dc286-1311">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="dc286-1312">A solicitação dispara a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1312">The request triggers the app to start.</span></span> <span data-ttu-id="dc286-1313">Por padrão, o IIS emite uma solicitação para a URL raiz do aplicativo (`/`) para inicializar o aplicativo (confira mais detalhes sobre a configuração nos [recursos adicionais](#application-initialization-module-and-idle-timeout-additional-resources)).</span><span class="sxs-lookup"><span data-stu-id="dc286-1313">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="dc286-1314">Confirme se o recurso da função Inicialização de Aplicativo do IIS está habilitado:</span><span class="sxs-lookup"><span data-stu-id="dc286-1314">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="dc286-1315">No Windows 7 ou sistemas de área de trabalho posteriores, ao usar o IIS localmente:</span><span class="sxs-lookup"><span data-stu-id="dc286-1315">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="dc286-1316">Navegue até **Painel de Controle** > **Programas** > **Programas e Recursos** > **Ativar ou desativar recursos do Windows** (lado esquerdo da tela).</span><span class="sxs-lookup"><span data-stu-id="dc286-1316">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="dc286-1317">Abra **Serviços de Informações da Internet** > **Serviços da World Wide Web** > **Recursos de Desenvolvimento de Aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1317">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="dc286-1318">Marque a caixa de seleção **Inicialização de Aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1318">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="dc286-1319">No Windows Server 2008 R2 ou posterior:</span><span class="sxs-lookup"><span data-stu-id="dc286-1319">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="dc286-1320">Abra o **Assistente de Adição de Funções e Recursos**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1320">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="dc286-1321">No painel **Selecionar serviços de função**, abra o nó **Desenvolvimento de aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1321">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="dc286-1322">Marque a caixa de seleção **Inicialização de Aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1322">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="dc286-1323">Use quaisquer das abordagens a seguir para habilitar o Módulo de Inicialização do Aplicativo para o site:</span><span class="sxs-lookup"><span data-stu-id="dc286-1323">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="dc286-1324">Usando o Gerenciador do IIS:</span><span class="sxs-lookup"><span data-stu-id="dc286-1324">Using IIS Manager:</span></span>

  1. <span data-ttu-id="dc286-1325">Selecione **Pools de Aplicativos** no painel **Conexões**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1325">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="dc286-1326">Clique com o botão direito do mouse no pool de aplicativos do aplicativo na lista e selecione **Configurações Avançadas**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1326">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="dc286-1327">O **Modo de Inicialização** padrão é **OnDemand**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1327">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="dc286-1328">Defina o **Modo de Inicialização** como **AlwaysRunning**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1328">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="dc286-1329">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1329">Select **OK**.</span></span>
  1. <span data-ttu-id="dc286-1330">Abra o nó **Sites** no painel **Conexões**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1330">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="dc286-1331">Clique com o botão direito do mouse no aplicativo e selecione **Gerenciar Site** > **Configurações Avançadas**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1331">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="dc286-1332">A configuração de **Pré-carregamento Habilitado** padrão é **Falso**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1332">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="dc286-1333">Defina **Pré-carregamento Habilitado** como **Verdadeiro**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1333">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="dc286-1334">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1334">Select **OK**.</span></span>

* <span data-ttu-id="dc286-1335">Usando o *web.config*, adicione o elemento `<applicationInitialization>` definindo `doAppInitAfterRestart` como `true` aos elementos `<system.webServer>` no arquivo *web.config* do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="dc286-1335">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="dc286-1336">Tempo limite de ociosidade</span><span class="sxs-lookup"><span data-stu-id="dc286-1336">Idle Timeout</span></span>

<span data-ttu-id="dc286-1337">*Só se aplica a aplicativos hospedados em processo.*</span><span class="sxs-lookup"><span data-stu-id="dc286-1337">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="dc286-1338">Para impedir que o aplicativo fique ocioso, defina o tempo limite de ociosidade do pool de aplicativos, usando o Gerenciador do IIS:</span><span class="sxs-lookup"><span data-stu-id="dc286-1338">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="dc286-1339">Selecione **Pools de Aplicativos** no painel **Conexões**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1339">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="dc286-1340">Clique com o botão direito do mouse no pool de aplicativos do aplicativo na lista e selecione **Configurações Avançadas**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1340">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="dc286-1341">O **Tempo Limite de Ociosidade (minutos)** é de **20** minutos.</span><span class="sxs-lookup"><span data-stu-id="dc286-1341">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="dc286-1342">Defina o **Tempo Limite de Ociosidade (minutos)** como **0** (zero).</span><span class="sxs-lookup"><span data-stu-id="dc286-1342">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="dc286-1343">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1343">Select **OK**.</span></span>
1. <span data-ttu-id="dc286-1344">Recicle o processo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="dc286-1344">Recycle the worker process.</span></span>

<span data-ttu-id="dc286-1345">Para impedir que aplicativos hospedados [fora de processo](#out-of-process-hosting-model) atinjam o tempo limite, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="dc286-1345">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="dc286-1346">Execute ping do aplicativo de um serviço externo para mantê-lo em execução.</span><span class="sxs-lookup"><span data-stu-id="dc286-1346">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="dc286-1347">Se o aplicativo hospedar apenas serviços em segundo plano, evite a hospedagem do IIS e use um [Serviço do Windows para hospedar o aplicativo ASP.NET Core](xref:host-and-deploy/windows-service).</span><span class="sxs-lookup"><span data-stu-id="dc286-1347">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="dc286-1348">Recursos adicionais do Módulo de Inicialização de Aplicativo e do Tempo Limite de Ociosidade</span><span class="sxs-lookup"><span data-stu-id="dc286-1348">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="dc286-1349">Inicialização de Aplicativo do IIS 8.0</span><span class="sxs-lookup"><span data-stu-id="dc286-1349">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="dc286-1350">[Inicialização \<applicationInitialization> do aplicativo ](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="dc286-1350">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="dc286-1351">[Configurações do modelo de processo para um \<processModel> pool de aplicativos ](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="dc286-1351">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="dc286-1352">Recursos de implantação para administradores do IIS</span><span class="sxs-lookup"><span data-stu-id="dc286-1352">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="dc286-1353">Documentação do ISS</span><span class="sxs-lookup"><span data-stu-id="dc286-1353">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="dc286-1354">Introdução ao Gerenciador do IIS no IIS</span><span class="sxs-lookup"><span data-stu-id="dc286-1354">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="dc286-1355">Implantação de aplicativo .NET Core</span><span class="sxs-lookup"><span data-stu-id="dc286-1355">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="dc286-1356">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="dc286-1356">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="dc286-1357">O site oficial da IIS da Microsoft</span><span class="sxs-lookup"><span data-stu-id="dc286-1357">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="dc286-1358">Biblioteca de conteúdo técnico do Windows Server</span><span class="sxs-lookup"><span data-stu-id="dc286-1358">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="dc286-1359">HTTP/2 no IIS</span><span class="sxs-lookup"><span data-stu-id="dc286-1359">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="dc286-1360">Para uma experiência de tutorial sobre como publicar um aplicativo de ASP.NET Core em um servidor IIS, confira <xref:tutorials/publish-to-iis>.</span><span class="sxs-lookup"><span data-stu-id="dc286-1360">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="dc286-1361">Instalar o pacote de hospedagem do .NET Core</span><span class="sxs-lookup"><span data-stu-id="dc286-1361">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="dc286-1362">Sistemas operacionais compatíveis</span><span class="sxs-lookup"><span data-stu-id="dc286-1362">Supported operating systems</span></span>

<span data-ttu-id="dc286-1363">Há suporte para os seguintes sistemas operacionais:</span><span class="sxs-lookup"><span data-stu-id="dc286-1363">The following operating systems are supported:</span></span>

* <span data-ttu-id="dc286-1364">Windows 7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="dc286-1364">Windows 7 or later</span></span>
* <span data-ttu-id="dc286-1365">Windows Server 2008 R2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="dc286-1365">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="dc286-1366">O [servidor HTTP.sys](xref:fundamentals/servers/httpsys) (anteriormente chamado de WebListener) não funciona em uma configuração de proxy reverso com o IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-1366">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="dc286-1367">Use o [servidor Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="dc286-1367">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="dc286-1368">Para obter mais informações sobre hospedagem no Azure, consulte <xref:host-and-deploy/azure-apps/index>.</span><span class="sxs-lookup"><span data-stu-id="dc286-1368">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="dc286-1369">Para obter as diretrizes de solução de problemas, consulte <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="dc286-1369">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="dc286-1370">Plataformas compatíveis</span><span class="sxs-lookup"><span data-stu-id="dc286-1370">Supported platforms</span></span>

<span data-ttu-id="dc286-1371">Aplicativos publicados para implantação de 32 bits (x86) ou 64 bits (x64) têm suporte.</span><span class="sxs-lookup"><span data-stu-id="dc286-1371">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="dc286-1372">Implantar um aplicativo de 32 bits com um SDK do .NET Core de 32 bits (x86), a menos que o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="dc286-1372">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="dc286-1373">Exija o maior espaço de endereço de memória virtual disponível para um aplicativo de 64 bits.</span><span class="sxs-lookup"><span data-stu-id="dc286-1373">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="dc286-1374">Exija o maior tamanho de pilha do IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-1374">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="dc286-1375">Tenha dependências nativas de 64 bits.</span><span class="sxs-lookup"><span data-stu-id="dc286-1375">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="dc286-1376">Use um SDK do .NET Core de 64 bits (x64) para publicar um aplicativo de 64 bits.</span><span class="sxs-lookup"><span data-stu-id="dc286-1376">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="dc286-1377">Um runtime de 64 bits deve estar presente no sistema host.</span><span class="sxs-lookup"><span data-stu-id="dc286-1377">A 64-bit runtime must be present on the host system.</span></span>

<span data-ttu-id="dc286-1378">O ASP.NET Core vem com o [servidor Kestrel](xref:fundamentals/servers/kestrel), um servidor HTTP padrão multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="dc286-1378">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), a default, cross-platform HTTP server.</span></span>

<span data-ttu-id="dc286-1379">Ao usar o [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) ou o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), o aplicativo é executado em um processo separado do processo de trabalho do IIS (*fora do processo*) com o [servidor Kestrel](xref:fundamentals/servers/index#kestrel).</span><span class="sxs-lookup"><span data-stu-id="dc286-1379">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](xref:fundamentals/servers/index#kestrel).</span></span>

<span data-ttu-id="dc286-1380">Como os aplicativos ASP.NET Core são executados em um processo separado do processo de trabalho do IIS, o módulo realiza o gerenciamento de processos.</span><span class="sxs-lookup"><span data-stu-id="dc286-1380">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="dc286-1381">O módulo inicia o processo para o aplicativo ASP.NET Core quando a primeira solicitação chega e reinicia o aplicativo se ele é desligado ou falha.</span><span class="sxs-lookup"><span data-stu-id="dc286-1381">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="dc286-1382">Isso é basicamente o mesmo comportamento que o dos aplicativos que são executados dentro do processo e são gerenciados pelo [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="dc286-1382">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="dc286-1383">O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo hospedado de fora d processo:</span><span class="sxs-lookup"><span data-stu-id="dc286-1383">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Módulo do ASP.NET Core](index/_static/ancm-outofprocess.png)

<span data-ttu-id="dc286-1385">As solicitações chegam da Web para o driver do HTTP.sys no modo kernel.</span><span class="sxs-lookup"><span data-stu-id="dc286-1385">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="dc286-1386">O driver roteia as solicitações ao IIS na porta configurada do site, normalmente, a 80 (HTTP) ou a 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="dc286-1386">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="dc286-1387">O módulo encaminha as solicitações ao Kestrel em uma porta aleatória do aplicativo, que não seja a porta 80 ou 443.</span><span class="sxs-lookup"><span data-stu-id="dc286-1387">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="dc286-1388">O módulo Especifica a porta por meio de uma variável de ambiente na inicialização, e o [middleware de integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configura o servidor a ser escutado `http://localhost:{port}` .</span><span class="sxs-lookup"><span data-stu-id="dc286-1388">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="dc286-1389">Outras verificações são executadas e as solicitações que não se originam do módulo são rejeitadas.</span><span class="sxs-lookup"><span data-stu-id="dc286-1389">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="dc286-1390">O módulo não é compatível com encaminhamento de HTTPS, portanto, as solicitações são encaminhadas por HTTP, mesmo se recebidas pelo IIS por HTTPS.</span><span class="sxs-lookup"><span data-stu-id="dc286-1390">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="dc286-1391">Depois que o Kestrel coleta a solicitação do módulo, a solicitação é enviada por push ao pipeline do middleware do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dc286-1391">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="dc286-1392">O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1392">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="dc286-1393">O middleware adicionado pela integração do IIS atualiza o esquema, o IP remoto e pathbase para encaminhar a solicitação para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="dc286-1393">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="dc286-1394">A resposta do aplicativo é retornada ao IIS, que a retorna por push para o cliente HTTP que iniciou a solicitação.</span><span class="sxs-lookup"><span data-stu-id="dc286-1394">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="dc286-1395">`CreateDefaultBuilder` configura o servidor [Kestrel](xref:fundamentals/servers/kestrel) como o servidor Web e habilita a integração do IIS, configurando o caminho base e a porta para o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="dc286-1395">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="dc286-1396">O Módulo do ASP.NET Core gera uma porta dinâmica a ser atribuída ao processo de back-end.</span><span class="sxs-lookup"><span data-stu-id="dc286-1396">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="dc286-1397">`CreateDefaultBuilder` chama o método <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>.</span><span class="sxs-lookup"><span data-stu-id="dc286-1397">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> method.</span></span> <span data-ttu-id="dc286-1398">`UseIISIntegration` configura o Kestrel para escutar na porta dinâmica no endereço IP do localhost (`127.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="dc286-1398">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="dc286-1399">Se a porta dinâmica for 1234, o Kestrel escutará em `127.0.0.1:1234`.</span><span class="sxs-lookup"><span data-stu-id="dc286-1399">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="dc286-1400">Essa configuração substitui outras configurações de URL fornecidas por:</span><span class="sxs-lookup"><span data-stu-id="dc286-1400">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="dc286-1401">API de escuta do Kestrel</span><span class="sxs-lookup"><span data-stu-id="dc286-1401">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="dc286-1402">[Configuração](xref:fundamentals/configuration/index) (ou [opção --urls de linha de comando](xref:fundamentals/host/web-host#override-configuration))</span><span class="sxs-lookup"><span data-stu-id="dc286-1402">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="dc286-1403">As chamadas a `UseUrls` ou à API `Listen` do Kestrel não são necessárias ao usar o módulo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1403">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="dc286-1404">Se `UseUrls` ou `Listen` for chamado, o Kestrel escutará na porta especificada somente durante a execução do aplicativo sem o IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-1404">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

<span data-ttu-id="dc286-1405">Para orientação sobre a configuração do Módulo do ASP.NET Core, consulte <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="dc286-1405">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="dc286-1406">Para saber mais sobre hospedagem, confira [Host no ASP.NET Core](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="dc286-1406">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="dc286-1407">Configuração de aplicativo</span><span class="sxs-lookup"><span data-stu-id="dc286-1407">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="dc286-1408">Habilitar os componentes de IISIntegration</span><span class="sxs-lookup"><span data-stu-id="dc286-1408">Enable the IISIntegration components</span></span>

<span data-ttu-id="dc286-1409">Ao criar um host no `CreateWebHostBuilder` (*Program.cs*), chame <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> para habilitar a integração do IIS:</span><span class="sxs-lookup"><span data-stu-id="dc286-1409">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="dc286-1410">Para obter mais informações sobre o `CreateDefaultBuilder`, consulte <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="dc286-1410">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="dc286-1411">Opções do IIS</span><span class="sxs-lookup"><span data-stu-id="dc286-1411">IIS options</span></span>

| <span data-ttu-id="dc286-1412">Opção</span><span class="sxs-lookup"><span data-stu-id="dc286-1412">Option</span></span>                         | <span data-ttu-id="dc286-1413">Padrão</span><span class="sxs-lookup"><span data-stu-id="dc286-1413">Default</span></span> | <span data-ttu-id="dc286-1414">Configuração</span><span class="sxs-lookup"><span data-stu-id="dc286-1414">Setting</span></span> |
| ---
<span data-ttu-id="dc286-1415">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-1415">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-1416">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1416">'Blazor'</span></span>
- <span data-ttu-id="dc286-1417">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-1417">'Identity'</span></span>
- <span data-ttu-id="dc286-1418">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-1418">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-1419">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1419">'Razor'</span></span>
- <span data-ttu-id="dc286-1420">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-1420">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-1421">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-1421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-1422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1422">'Blazor'</span></span>
- <span data-ttu-id="dc286-1423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-1423">'Identity'</span></span>
- <span data-ttu-id="dc286-1424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-1424">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-1425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1425">'Razor'</span></span>
- <span data-ttu-id="dc286-1426">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-1426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-1427">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-1427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-1428">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1428">'Blazor'</span></span>
- <span data-ttu-id="dc286-1429">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-1429">'Identity'</span></span>
- <span data-ttu-id="dc286-1430">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-1430">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-1431">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1431">'Razor'</span></span>
- <span data-ttu-id="dc286-1432">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-1432">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-1433">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-1433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-1434">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1434">'Blazor'</span></span>
- <span data-ttu-id="dc286-1435">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-1435">'Identity'</span></span>
- <span data-ttu-id="dc286-1436">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-1436">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-1437">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1437">'Razor'</span></span>
- <span data-ttu-id="dc286-1438">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-1438">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-1439">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-1439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-1440">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1440">'Blazor'</span></span>
- <span data-ttu-id="dc286-1441">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-1441">'Identity'</span></span>
- <span data-ttu-id="dc286-1442">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-1442">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-1443">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1443">'Razor'</span></span>
- <span data-ttu-id="dc286-1444">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-1444">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-1445">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-1445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-1446">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1446">'Blazor'</span></span>
- <span data-ttu-id="dc286-1447">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-1447">'Identity'</span></span>
- <span data-ttu-id="dc286-1448">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-1448">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-1449">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1449">'Razor'</span></span>
- <span data-ttu-id="dc286-1450">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-1450">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-1451">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-1451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-1452">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1452">'Blazor'</span></span>
- <span data-ttu-id="dc286-1453">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-1453">'Identity'</span></span>
- <span data-ttu-id="dc286-1454">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-1454">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-1455">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1455">'Razor'</span></span>
- <span data-ttu-id="dc286-1456">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-1456">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-1457">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-1457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-1458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1458">'Blazor'</span></span>
- <span data-ttu-id="dc286-1459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-1459">'Identity'</span></span>
- <span data-ttu-id="dc286-1460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-1460">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-1461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1461">'Razor'</span></span>
- <span data-ttu-id="dc286-1462">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-1462">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-1463">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-1463">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-1464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1464">'Blazor'</span></span>
- <span data-ttu-id="dc286-1465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-1465">'Identity'</span></span>
- <span data-ttu-id="dc286-1466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-1466">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-1467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1467">'Razor'</span></span>
- <span data-ttu-id="dc286-1468">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-1468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-1469">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-1469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-1470">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1470">'Blazor'</span></span>
- <span data-ttu-id="dc286-1471">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-1471">'Identity'</span></span>
- <span data-ttu-id="dc286-1472">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-1472">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-1473">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1473">'Razor'</span></span>
- <span data-ttu-id="dc286-1474">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-1474">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-1475">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-1475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-1476">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1476">'Blazor'</span></span>
- <span data-ttu-id="dc286-1477">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-1477">'Identity'</span></span>
- <span data-ttu-id="dc286-1478">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-1478">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-1479">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1479">'Razor'</span></span>
- <span data-ttu-id="dc286-1480">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-1480">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-1481">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-1481">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-1482">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1482">'Blazor'</span></span>
- <span data-ttu-id="dc286-1483">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-1483">'Identity'</span></span>
- <span data-ttu-id="dc286-1484">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-1484">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-1485">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1485">'Razor'</span></span>
- <span data-ttu-id="dc286-1486">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-1486">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-1487">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-1487">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-1488">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1488">'Blazor'</span></span>
- <span data-ttu-id="dc286-1489">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-1489">'Identity'</span></span>
- <span data-ttu-id="dc286-1490">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-1490">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-1491">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1491">'Razor'</span></span>
- <span data-ttu-id="dc286-1492">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-1492">'SignalR' uid:</span></span> 

<span data-ttu-id="dc286-1493">--------------- | :-----: | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-1493">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-1494">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1494">'Blazor'</span></span>
- <span data-ttu-id="dc286-1495">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-1495">'Identity'</span></span>
- <span data-ttu-id="dc286-1496">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-1496">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-1497">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1497">'Razor'</span></span>
- <span data-ttu-id="dc286-1498">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-1498">'SignalR' uid:</span></span> 

<span data-ttu-id="dc286-1499">---- | | `AutomaticAuthentication`      | `true`  | Se `true` , o servidor IIS definirá a `HttpContext.User` autenticação autenticada pelo [Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="dc286-1499">---- | | `AutomaticAuthentication`      | `true`  | If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="dc286-1500">Se `false`, o servidor fornecerá apenas uma identidade para `HttpContext.User` e responderá a desafios quando explicitamente solicitado pelo `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="dc286-1500">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="dc286-1501">A autenticação do Windows deve estar habilitada no IIS para que o `AutomaticAuthentication` funcione.</span><span class="sxs-lookup"><span data-stu-id="dc286-1501">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="dc286-1502">Para obter mais informações, veja [Autenticação do Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="dc286-1502">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="dc286-1503">| | `AuthenticationDisplayName`    | `null`  | Define o nome de exibição mostrado para os usuários nas páginas de logon.</span><span class="sxs-lookup"><span data-stu-id="dc286-1503">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="dc286-1504">Para configurar opções do IIS, inclua uma configuração de serviço para <xref:Microsoft.AspNetCore.Builder.IISOptions> em <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span><span class="sxs-lookup"><span data-stu-id="dc286-1504">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="dc286-1505">O exemplo a seguir impede que o aplicativo preencha `HttpContext.Connection.ClientCertificate`:</span><span class="sxs-lookup"><span data-stu-id="dc286-1505">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="dc286-1506">Opção</span><span class="sxs-lookup"><span data-stu-id="dc286-1506">Option</span></span>                         | <span data-ttu-id="dc286-1507">Padrão</span><span class="sxs-lookup"><span data-stu-id="dc286-1507">Default</span></span> | <span data-ttu-id="dc286-1508">Configuração</span><span class="sxs-lookup"><span data-stu-id="dc286-1508">Setting</span></span> |
| ---
<span data-ttu-id="dc286-1509">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-1509">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-1510">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1510">'Blazor'</span></span>
- <span data-ttu-id="dc286-1511">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-1511">'Identity'</span></span>
- <span data-ttu-id="dc286-1512">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-1512">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-1513">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1513">'Razor'</span></span>
- <span data-ttu-id="dc286-1514">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-1514">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-1515">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-1515">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-1516">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1516">'Blazor'</span></span>
- <span data-ttu-id="dc286-1517">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-1517">'Identity'</span></span>
- <span data-ttu-id="dc286-1518">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-1518">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-1519">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1519">'Razor'</span></span>
- <span data-ttu-id="dc286-1520">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-1520">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-1521">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-1521">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-1522">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1522">'Blazor'</span></span>
- <span data-ttu-id="dc286-1523">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-1523">'Identity'</span></span>
- <span data-ttu-id="dc286-1524">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-1524">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-1525">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1525">'Razor'</span></span>
- <span data-ttu-id="dc286-1526">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-1526">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-1527">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-1527">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-1528">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1528">'Blazor'</span></span>
- <span data-ttu-id="dc286-1529">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-1529">'Identity'</span></span>
- <span data-ttu-id="dc286-1530">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-1530">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-1531">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1531">'Razor'</span></span>
- <span data-ttu-id="dc286-1532">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-1532">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-1533">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-1533">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-1534">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1534">'Blazor'</span></span>
- <span data-ttu-id="dc286-1535">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-1535">'Identity'</span></span>
- <span data-ttu-id="dc286-1536">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-1536">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-1537">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1537">'Razor'</span></span>
- <span data-ttu-id="dc286-1538">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-1538">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-1539">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-1539">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-1540">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1540">'Blazor'</span></span>
- <span data-ttu-id="dc286-1541">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-1541">'Identity'</span></span>
- <span data-ttu-id="dc286-1542">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-1542">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-1543">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1543">'Razor'</span></span>
- <span data-ttu-id="dc286-1544">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-1544">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-1545">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-1545">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-1546">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1546">'Blazor'</span></span>
- <span data-ttu-id="dc286-1547">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-1547">'Identity'</span></span>
- <span data-ttu-id="dc286-1548">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-1548">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-1549">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1549">'Razor'</span></span>
- <span data-ttu-id="dc286-1550">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-1550">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-1551">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-1551">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-1552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1552">'Blazor'</span></span>
- <span data-ttu-id="dc286-1553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-1553">'Identity'</span></span>
- <span data-ttu-id="dc286-1554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-1554">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-1555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1555">'Razor'</span></span>
- <span data-ttu-id="dc286-1556">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-1556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-1557">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-1557">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-1558">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1558">'Blazor'</span></span>
- <span data-ttu-id="dc286-1559">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-1559">'Identity'</span></span>
- <span data-ttu-id="dc286-1560">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-1560">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-1561">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1561">'Razor'</span></span>
- <span data-ttu-id="dc286-1562">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-1562">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-1563">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-1563">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-1564">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1564">'Blazor'</span></span>
- <span data-ttu-id="dc286-1565">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-1565">'Identity'</span></span>
- <span data-ttu-id="dc286-1566">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-1566">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-1567">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1567">'Razor'</span></span>
- <span data-ttu-id="dc286-1568">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-1568">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-1569">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-1569">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-1570">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1570">'Blazor'</span></span>
- <span data-ttu-id="dc286-1571">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-1571">'Identity'</span></span>
- <span data-ttu-id="dc286-1572">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-1572">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-1573">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1573">'Razor'</span></span>
- <span data-ttu-id="dc286-1574">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-1574">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-1575">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-1575">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-1576">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1576">'Blazor'</span></span>
- <span data-ttu-id="dc286-1577">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-1577">'Identity'</span></span>
- <span data-ttu-id="dc286-1578">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-1578">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-1579">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1579">'Razor'</span></span>
- <span data-ttu-id="dc286-1580">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-1580">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc286-1581">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-1581">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-1582">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1582">'Blazor'</span></span>
- <span data-ttu-id="dc286-1583">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-1583">'Identity'</span></span>
- <span data-ttu-id="dc286-1584">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-1584">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-1585">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1585">'Razor'</span></span>
- <span data-ttu-id="dc286-1586">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-1586">'SignalR' uid:</span></span> 

<span data-ttu-id="dc286-1587">--------------- | :-----: | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc286-1587">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc286-1588">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1588">'Blazor'</span></span>
- <span data-ttu-id="dc286-1589">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc286-1589">'Identity'</span></span>
- <span data-ttu-id="dc286-1590">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc286-1590">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc286-1591">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc286-1591">'Razor'</span></span>
- <span data-ttu-id="dc286-1592">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dc286-1592">'SignalR' uid:</span></span> 

<span data-ttu-id="dc286-1593">---- | | `AutomaticAuthentication`      | `true`  | Se `true` , o [middleware de integração do IIS](#enable-the-iisintegration-components) define a `HttpContext.User` [autenticação](xref:security/authentication/windowsauth)autenticada pelo Windows.</span><span class="sxs-lookup"><span data-stu-id="dc286-1593">---- | | `AutomaticAuthentication`      | `true`  | If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="dc286-1594">Se `false`, o middleware fornecerá apenas uma identidade para `HttpContext.User` e responderá a desafios quando explicitamente solicitado pelo `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="dc286-1594">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="dc286-1595">A autenticação do Windows deve estar habilitada no IIS para que o `AutomaticAuthentication` funcione.</span><span class="sxs-lookup"><span data-stu-id="dc286-1595">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="dc286-1596">Saiba mais no tópico [Autenticação do Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="dc286-1596">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> <span data-ttu-id="dc286-1597">| | `AuthenticationDisplayName`    | `null`  | Define o nome de exibição mostrado para os usuários nas páginas de logon.</span><span class="sxs-lookup"><span data-stu-id="dc286-1597">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="dc286-1598">| | `ForwardClientCertificate`     | `true`  | Se `true` e o `MS-ASPNETCORE-CLIENTCERT` cabeçalho da solicitação estiver presente, o `HttpContext.Connection.ClientCertificate` será preenchido.</span><span class="sxs-lookup"><span data-stu-id="dc286-1598">| | `ForwardClientCertificate`     | `true`  | If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="dc286-1599">Servidor proxy e cenários de balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="dc286-1599">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="dc286-1600">O [Middleware de integração do IIS](#enable-the-iisintegration-components), que configura o Middleware de cabeçalhos encaminhados, e o módulo do ASP.NET Core, são configurados para encaminhar o esquema (HTTP/HTTPS) e o endereço IP remoto de onde a solicitação foi originada.</span><span class="sxs-lookup"><span data-stu-id="dc286-1600">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="dc286-1601">Configuração adicional pode ser necessária para aplicativos hospedados atrás de servidores proxy adicionais e balanceadores de carga.</span><span class="sxs-lookup"><span data-stu-id="dc286-1601">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="dc286-1602">Para obter mais informações, veja [Configurar o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="dc286-1602">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="dc286-1603">Arquivo web.config</span><span class="sxs-lookup"><span data-stu-id="dc286-1603">web.config file</span></span>

<span data-ttu-id="dc286-1604">O arquivo *web.config* configura o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="dc286-1604">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="dc286-1605">Criando, transformar e publicar o arquivo *Web.config* é tratado por um destino do MSBuild (`_TransformWebConfig`) quando o projeto é publicado.</span><span class="sxs-lookup"><span data-stu-id="dc286-1605">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="dc286-1606">Este destino está presente nos destinos do SDK da Web (`Microsoft.NET.Sdk.Web`).</span><span class="sxs-lookup"><span data-stu-id="dc286-1606">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="dc286-1607">O SDK é definido na parte superior do arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="dc286-1607">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="dc286-1608">Se um arquivo *web.config* não estiver presente no projeto, ele será criado com o *processPath* e os *argumentos* corretos para configurar o Módulo do ASP.NET Core e será transferido para o [resultado publicado](xref:host-and-deploy/directory-structure).</span><span class="sxs-lookup"><span data-stu-id="dc286-1608">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="dc286-1609">Se um arquivo *web.config* estiver presente no projeto, ele será transformado com o *processPath* e os *argumentos* corretos para configurar o Módulo do ASP.NET Core e será movido para o resultado publicado.</span><span class="sxs-lookup"><span data-stu-id="dc286-1609">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="dc286-1610">A transformação não altera as definições de configuração do IIS no arquivo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1610">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="dc286-1611">O arquivo *web.config* pode fornecer configurações adicionais do IIS que controlam módulos ativos do IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-1611">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="dc286-1612">Para saber mais sobre os módulos do IIS que podem processar solicitações com aplicativos do ASP.NET Core, veja o tópico [Módulos do IIS](xref:host-and-deploy/iis/modules).</span><span class="sxs-lookup"><span data-stu-id="dc286-1612">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="dc286-1613">Para impedir que o SDK da Web transforme o arquivo *Web. config* , use a **\<IsTransformWebConfigDisabled>** propriedade no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="dc286-1613">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="dc286-1614">Ao impedir que o SDK Web transforme o arquivo, o *processPath* e os *argumentos* devem ser definidos manualmente pelo desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="dc286-1614">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="dc286-1615">Para obter mais informações, consulte <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="dc286-1615">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="dc286-1616">Local do arquivo web.config</span><span class="sxs-lookup"><span data-stu-id="dc286-1616">web.config file location</span></span>

<span data-ttu-id="dc286-1617">Para configurar o [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) corretamente, o arquivo *Web. config* deve estar presente no caminho [raiz do conteúdo](xref:fundamentals/index#content-root) (normalmente o caminho base do aplicativo) do aplicativo implantado.</span><span class="sxs-lookup"><span data-stu-id="dc286-1617">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="dc286-1618">Esse é o mesmo local que o caminho físico do site fornecido ao IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-1618">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="dc286-1619">O arquivo *web.config* é necessário na raiz do aplicativo para habilitar a publicação de vários aplicativos usando a Implantação da Web.</span><span class="sxs-lookup"><span data-stu-id="dc286-1619">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="dc286-1620">Existem arquivos confidenciais no caminho físico do aplicativo, como \* \<assembly> . runtimeconfig. JSON*, \* \<assembly> . xml* (comentários de documentação XML) e \* \<assembly> . deps. JSON\*.</span><span class="sxs-lookup"><span data-stu-id="dc286-1620">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="dc286-1621">Quando o arquivo *web.config* estiver presente e o site for iniciado normalmente, o IIS não atenderá a esses arquivos confidenciais se eles forem solicitados.</span><span class="sxs-lookup"><span data-stu-id="dc286-1621">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="dc286-1622">Se o arquivo *web.config* estiver ausente, nomeado incorretamente ou se não for possível configurar o site para inicialização normal, o IIS poderá servir arquivos confidenciais publicamente.</span><span class="sxs-lookup"><span data-stu-id="dc286-1622">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="dc286-1623">**O arquivo *Web. config* deve estar presente na implantação o tempo todo, corretamente nomeado e ser capaz de configurar o site para inicialização normal. Nunca remova o arquivo *Web. config* de uma implantação de produção.**</span><span class="sxs-lookup"><span data-stu-id="dc286-1623">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="dc286-1624">Transformação do Web.config</span><span class="sxs-lookup"><span data-stu-id="dc286-1624">Transform web.config</span></span>

<span data-ttu-id="dc286-1625">Se você precisar transformar o *Web.config* em publicação (por exemplo, definir variáveis ​​de ambiente com base na configuração, no perfil ou no ambiente), consulte <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="dc286-1625">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="dc286-1626">Configuração do IIS</span><span class="sxs-lookup"><span data-stu-id="dc286-1626">IIS configuration</span></span>

<span data-ttu-id="dc286-1627">**Sistemas operacionais do Windows Server**</span><span class="sxs-lookup"><span data-stu-id="dc286-1627">**Windows Server operating systems**</span></span>

<span data-ttu-id="dc286-1628">Habilite a função **Servidor Web (IIS)** e estabeleça serviços de função.</span><span class="sxs-lookup"><span data-stu-id="dc286-1628">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="dc286-1629">Use o assistente **Adicionar Funções e Recursos** por meio do menu **Gerenciar** ou do link no **Gerenciador do Servidor**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1629">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="dc286-1630">Na etapa **Funções de Servidor**, marque a caixa de **Servidor Web (IIS)**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1630">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![A função de Servidor Web IIS é selecionada na etapa Selecionar funções de servidor.](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="dc286-1632">Após a etapa **Recursos**, a etapa **Serviços de função** é carregada para o servidor Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="dc286-1632">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="dc286-1633">Selecione os serviços de função do IIS desejados ou aceite os serviços de função padrão fornecidos.</span><span class="sxs-lookup"><span data-stu-id="dc286-1633">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![Os serviços de função padrão são selecionados na etapa Selecionar serviços de função.](index/_static/role-services-ws2016.png)

   <span data-ttu-id="dc286-1635">**Autenticação do Windows (opcional)**</span><span class="sxs-lookup"><span data-stu-id="dc286-1635">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="dc286-1636">Para habilitar a autenticação do Windows, expanda os seguintes nós: segurança do **servidor Web**  >  **Security**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1636">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="dc286-1637">Selecione o recurso **Autenticação do Windows**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1637">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="dc286-1638">Para obter mais informações, consulte [autenticação \<windowsAuthentication> do Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) e [Configurar a autenticação do Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="dc286-1638">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="dc286-1639">**WebSockets (opcional)**</span><span class="sxs-lookup"><span data-stu-id="dc286-1639">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="dc286-1640">O WebSockets é compatível com o ASP.NET Core 1.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="dc286-1640">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="dc286-1641">Para habilitar WebSockets, expanda os seguintes nós: desenvolvimento de aplicativo de **servidor Web**  >  **Application Development**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1641">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="dc286-1642">Selecione o recurso **Protocolo WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1642">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="dc286-1643">Para obter mais informações, consulte [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="dc286-1643">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="dc286-1644">Continue para a etapa **Confirmação** para instalar os serviços e a função de servidor Web.</span><span class="sxs-lookup"><span data-stu-id="dc286-1644">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="dc286-1645">Uma reinicialização de servidor/IIS não é necessária após a instalação da função do **servidor Web (IIS)** .</span><span class="sxs-lookup"><span data-stu-id="dc286-1645">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="dc286-1646">**Sistemas operacionais de área de trabalho do Windows**</span><span class="sxs-lookup"><span data-stu-id="dc286-1646">**Windows desktop operating systems**</span></span>

<span data-ttu-id="dc286-1647">Habilite o **Console de Gerenciamento do IIS** e os **Serviços na World Wide Web**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1647">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="dc286-1648">Navegue até **Painel de Controle** > **Programas** > **Programas e Recursos** > **Ativar ou desativar recursos do Windows** (lado esquerdo da tela).</span><span class="sxs-lookup"><span data-stu-id="dc286-1648">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="dc286-1649">Abra o nó **Serviços de Informações da Internet**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1649">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="dc286-1650">Abra o nó **Ferramentas de Gerenciamento da Web**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1650">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="dc286-1651">Marque a caixa de **Console de Gerenciamento do IIS**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1651">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="dc286-1652">Marque a caixa de **Serviços na World Wide Web**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1652">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="dc286-1653">Aceite os recursos padrão dos **Serviços na World Wide Web** ou personalize os recursos do IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-1653">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="dc286-1654">**Autenticação do Windows (opcional)**</span><span class="sxs-lookup"><span data-stu-id="dc286-1654">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="dc286-1655">Para habilitar a autenticação do Windows, expanda os seguintes nós: segurança **dos serviços de World Wide Web**  >  **Security**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1655">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="dc286-1656">Selecione o recurso **Autenticação do Windows**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1656">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="dc286-1657">Para obter mais informações, consulte [autenticação \<windowsAuthentication> do Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) e [Configurar a autenticação do Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="dc286-1657">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="dc286-1658">**WebSockets (opcional)**</span><span class="sxs-lookup"><span data-stu-id="dc286-1658">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="dc286-1659">O WebSockets é compatível com o ASP.NET Core 1.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="dc286-1659">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="dc286-1660">Para habilitar WebSockets, expanda os seguintes nós: recursos de desenvolvimento de aplicativos **World Wide Web Services**  >  **Application Development Features**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1660">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="dc286-1661">Selecione o recurso **Protocolo WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1661">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="dc286-1662">Para obter mais informações, consulte [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="dc286-1662">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="dc286-1663">Se a instalação do IIS exigir uma reinicialização, reinicie o sistema.</span><span class="sxs-lookup"><span data-stu-id="dc286-1663">If the IIS installation requires a restart, restart the system.</span></span>

![O Console de Gerenciamento do IIS e os Serviços na World Wide Web estão selecionados em Recursos do Windows.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="dc286-1665">Instalar o pacote de hospedagem do .NET Core</span><span class="sxs-lookup"><span data-stu-id="dc286-1665">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="dc286-1666">Instale o *pacote de hospedagem do .NET Core* no sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="dc286-1666">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="dc286-1667">O pacote instala o tempo de execução do .NET Core, a biblioteca do .NET Core e o [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="dc286-1667">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="dc286-1668">O módulo permite que aplicativos do ASP.NET Core sejam executados por trás do IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-1668">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="dc286-1669">Se o pacote de hospedagem for instalado antes do IIS, a instalação do pacote deverá ser reparada.</span><span class="sxs-lookup"><span data-stu-id="dc286-1669">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="dc286-1670">Execute o instalador do pacote de hospedagem novamente depois de instalar o IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-1670">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="dc286-1671">Se o pacote de hospedagem for instalado após a instalação da versão de 64 bits (x64) do .NET Core, os SDKs poderão parecer estar ausentes ([Nenhum SDK do .NET Core foi detectado](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="dc286-1671">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="dc286-1672">Para resolver o problema, consulte <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span><span class="sxs-lookup"><span data-stu-id="dc286-1672">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="dc286-1673">Baixar</span><span class="sxs-lookup"><span data-stu-id="dc286-1673">Download</span></span>

1. <span data-ttu-id="dc286-1674">Navegue até a página [baixar o .NET Core](https://dotnet.microsoft.com/download/dotnet-core) .</span><span class="sxs-lookup"><span data-stu-id="dc286-1674">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="dc286-1675">Selecione a versão do .NET Core desejada.</span><span class="sxs-lookup"><span data-stu-id="dc286-1675">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="dc286-1676">Na coluna **Executar aplicativos – runtime**, localize a linha da versão de runtime do .NET Core desejada.</span><span class="sxs-lookup"><span data-stu-id="dc286-1676">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="dc286-1677">Baixe o instalador usando o link **pacote de hospedagem** .</span><span class="sxs-lookup"><span data-stu-id="dc286-1677">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="dc286-1678">Alguns instaladores contêm versões de lançamento que atingiram o EOL (fim da vida útil) e não têm mais suporte da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="dc286-1678">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="dc286-1679">Para saber mais, confira a [política de suporte](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="dc286-1679">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="dc286-1680">Instalar o pacote de hospedagem</span><span class="sxs-lookup"><span data-stu-id="dc286-1680">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="dc286-1681">Execute o instalador no servidor.</span><span class="sxs-lookup"><span data-stu-id="dc286-1681">Run the installer on the server.</span></span> <span data-ttu-id="dc286-1682">Os parâmetros a seguir estão disponíveis ao executar o instalador por meio de um shell de comando do administrador:</span><span class="sxs-lookup"><span data-stu-id="dc286-1682">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="dc286-1683">`OPT_NO_ANCM=1`: Ignorar a instalação do módulo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dc286-1683">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="dc286-1684">`OPT_NO_RUNTIME=1`: Ignorar a instalação do tempo de execução do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="dc286-1684">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="dc286-1685">Usado quando o servidor hospeda apenas [implantações independentes (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="dc286-1685">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="dc286-1686">`OPT_NO_SHAREDFX=1`: Ignorar a instalação da estrutura compartilhada ASP.NET (ASP.NET Runtime).</span><span class="sxs-lookup"><span data-stu-id="dc286-1686">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="dc286-1687">Usado quando o servidor hospeda apenas [implantações independentes (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="dc286-1687">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="dc286-1688">`OPT_NO_X86=1`: Ignorar a instalação de tempos de execução x86.</span><span class="sxs-lookup"><span data-stu-id="dc286-1688">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="dc286-1689">Use esse parâmetro quando você souber que não hospedará aplicativos de 32 bits.</span><span class="sxs-lookup"><span data-stu-id="dc286-1689">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="dc286-1690">Se houver uma possibilidade de hospedar aplicativos de 32 bits e 64 bits no futuro, não use esse parâmetro e instale ambos os runtimes.</span><span class="sxs-lookup"><span data-stu-id="dc286-1690">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="dc286-1691">`OPT_NO_SHARED_CONFIG_CHECK=1`: Desabilite a verificação para usar uma configuração compartilhada do IIS quando a configuração compartilhada (*ApplicationHost. config*) estiver no mesmo computador que a instalação do IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-1691">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="dc286-1692">*Disponível somente para instaladores do ASP.NET Core 2.2 ou Hosting Bundler posterior.*</span><span class="sxs-lookup"><span data-stu-id="dc286-1692">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="dc286-1693">Para obter mais informações, consulte <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="dc286-1693">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="dc286-1694">Reinicie o sistema ou execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="dc286-1694">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="dc286-1695">A reinicialização do IIS identifica uma alteração no CAMINHO do sistema, que é uma variável de ambiente, realizada pelo instalador.</span><span class="sxs-lookup"><span data-stu-id="dc286-1695">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="dc286-1696">Não é necessário interromper manualmente os sites individuais no IIS ao instalar o pacote de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="dc286-1696">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="dc286-1697">Aplicativos hospedados (sites do IIS) são reiniciados quando o IIS é reiniciado.</span><span class="sxs-lookup"><span data-stu-id="dc286-1697">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="dc286-1698">Os aplicativos são iniciados novamente quando recebem sua primeira solicitação, incluindo a partir do [módulo de inicialização do aplicativo](#application-initialization-module-and-idle-timeout).</span><span class="sxs-lookup"><span data-stu-id="dc286-1698">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="dc286-1699">ASP.NET Core adota o comportamento de roll-forward para versões de patch de pacotes de estrutura compartilhados.</span><span class="sxs-lookup"><span data-stu-id="dc286-1699">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="dc286-1700">Quando os aplicativos hospedados pelo IIS são reiniciados com o IIS, os aplicativos são carregados com as versões de patch mais recentes de seus pacotes referenciados quando recebem sua primeira solicitação.</span><span class="sxs-lookup"><span data-stu-id="dc286-1700">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="dc286-1701">Se o IIS não for reiniciado, os aplicativos serão reiniciados e exibirão o comportamento de roll-forward quando seus processos de trabalho forem reciclados e receberem sua primeira solicitação.</span><span class="sxs-lookup"><span data-stu-id="dc286-1701">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="dc286-1702">Para obter informações sobre a Configuração Compartilhada do IIS, consulte [Módulo do ASP.NET Core com a Configuração Compartilhada do IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="dc286-1702">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="dc286-1703">Instalar a Implantação da Web durante a publicação com o Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dc286-1703">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="dc286-1704">Ao implantar aplicativos para servidores com [Implantação da Web](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), instale a versão mais recente da Implantação da Web no servidor.</span><span class="sxs-lookup"><span data-stu-id="dc286-1704">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="dc286-1705">Para instalar a Implantação da Web, use o [WebPI (Web Platform Installer)](https://www.microsoft.com/web/downloads/platform.aspx) ou obtenha um instalador diretamente no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=43717).</span><span class="sxs-lookup"><span data-stu-id="dc286-1705">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="dc286-1706">O método preferencial é usar o WebPI.</span><span class="sxs-lookup"><span data-stu-id="dc286-1706">The preferred method is to use WebPI.</span></span> <span data-ttu-id="dc286-1707">O WebPI oferece uma instalação autônoma e uma configuração para provedores de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="dc286-1707">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="dc286-1708">Criar o site do IIS</span><span class="sxs-lookup"><span data-stu-id="dc286-1708">Create the IIS site</span></span>

1. <span data-ttu-id="dc286-1709">No sistema de hospedagem, crie uma pasta para conter arquivos e pastas publicados do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1709">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="dc286-1710">Em uma etapa a seguir, o caminho da pasta é fornecido ao IIS como o caminho físico para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1710">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="dc286-1711">Para obter mais informações sobre o layout de arquivo e a pasta de implantação de um aplicativo, confira <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="dc286-1711">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="dc286-1712">No Gerenciador do IIS, abra o nó do servidor no painel **conexões** .</span><span class="sxs-lookup"><span data-stu-id="dc286-1712">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="dc286-1713">Clique com botão direito do mouse na pasta **Sites**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1713">Right-click the **Sites** folder.</span></span> <span data-ttu-id="dc286-1714">Selecione **Adicionar Site** no menu contextual.</span><span class="sxs-lookup"><span data-stu-id="dc286-1714">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="dc286-1715">Forneça um **Nome do site** e defina o **Caminho físico** como a pasta de implantação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1715">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="dc286-1716">Forneça a configuração de **Associação** e crie o site selecionando **OK**:</span><span class="sxs-lookup"><span data-stu-id="dc286-1716">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![Forneça o Nome do site, o caminho físico e o Nome do host na etapa Adicionar Site.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="dc286-1718">Associações de curinga de nível superior (`http://*:80/` e `http://+:80`) **não** devem ser usadas.</span><span class="sxs-lookup"><span data-stu-id="dc286-1718">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="dc286-1719">Associações de curinga de nível superior podem abrir o aplicativo para vulnerabilidades de segurança.</span><span class="sxs-lookup"><span data-stu-id="dc286-1719">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="dc286-1720">Isso se aplica a curingas fortes e fracos.</span><span class="sxs-lookup"><span data-stu-id="dc286-1720">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="dc286-1721">Use nomes de host explícitos em vez de curingas.</span><span class="sxs-lookup"><span data-stu-id="dc286-1721">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="dc286-1722">Associações de curinga de subdomínio (por exemplo, `*.mysub.com`) não têm esse risco de segurança se você controlar o domínio pai completo (em vez de `*.com`, o qual é vulnerável).</span><span class="sxs-lookup"><span data-stu-id="dc286-1722">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="dc286-1723">Veja [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="dc286-1723">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="dc286-1724">No nó do servidor, selecione **Pools de Aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1724">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="dc286-1725">Clique com o botão direito do mouse no pool de aplicativos do site e selecione **Configurações Básicas** no menu contextual.</span><span class="sxs-lookup"><span data-stu-id="dc286-1725">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="dc286-1726">Na janela **Editar Pool de Aplicativos**, defina a **versão do CLR do .NET** como **Sem Código Gerenciado**:</span><span class="sxs-lookup"><span data-stu-id="dc286-1726">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![Defina Sem Código Gerenciado para a versão do CLR do .NET.](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="dc286-1728">O ASP.NET Core é executado em um processo separado e gerencia o runtime.</span><span class="sxs-lookup"><span data-stu-id="dc286-1728">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="dc286-1729">O ASP.NET Core não depende do carregamento do CLR de Área de trabalho (CLR do .NET)&mdash;o Core Common Language Runtime (CoreCLR) para o .NET Core é inicializado para hospedar o aplicativo no processo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="dc286-1729">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="dc286-1730">Definir a **versão do CLR do .NET** como **Sem Código Gerenciado** é opcional, porém recomendado.</span><span class="sxs-lookup"><span data-stu-id="dc286-1730">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="dc286-1731">*ASP.NET Core 2.2 ou posterior*: para uma [implantação autocontida](/dotnet/core/deploying/#self-contained-deployments-scd) de 64 bits (x64) que usa o [modelo de hospedagem em processo](#in-process-hosting-model), desabilite o pool de aplicativos para processos de 32 bits (x86).</span><span class="sxs-lookup"><span data-stu-id="dc286-1731">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="dc286-1732">Na barra lateral **Ações** do Gerenciador do IIS > **Pools de Aplicativos**, selecione **Definir Padrões do Pool de Aplicativos** ou **Configurações Avançadas**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1732">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="dc286-1733">Localize **Habilitar Aplicativos de 32 bits** e defina o valor como `False`.</span><span class="sxs-lookup"><span data-stu-id="dc286-1733">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="dc286-1734">Essa configuração não afeta os aplicativos implantados para a [hospedagem fora do processo](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="dc286-1734">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="dc286-1735">Confirme se a identidade do modelo de processo tem as permissões apropriadas.</span><span class="sxs-lookup"><span data-stu-id="dc286-1735">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="dc286-1736">Se a identidade padrão do pool de aplicativos (**modelo de processo**  >  **Identity** ) for alterada de **ApplicationPoolIdentity** para outra identidade, verifique se a nova identidade tem as permissões necessárias para acessar a pasta, o banco de dados e outros recursos necessários do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1736">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="dc286-1737">Por exemplo, o pool de aplicativos requer acesso de leitura e gravação às pastas nas quais o aplicativo lê e grava os arquivos.</span><span class="sxs-lookup"><span data-stu-id="dc286-1737">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="dc286-1738">**Configuração de Autenticação do Windows (opcional)**</span><span class="sxs-lookup"><span data-stu-id="dc286-1738">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="dc286-1739">Para saber mais, veja [Configurar a Autenticação do Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="dc286-1739">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="dc286-1740">Implantar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="dc286-1740">Deploy the app</span></span>

<span data-ttu-id="dc286-1741">Implante o aplicativo na pasta **caminho físico** do IIS que foi estabelecida na seção [Criar o site do IIS](#create-the-iis-site).</span><span class="sxs-lookup"><span data-stu-id="dc286-1741">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="dc286-1742">[Implantação da Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) é o mecanismo recomendado para implantação, mas existem várias opções para mover o aplicativo da pasta *publicar* do projeto para a pasta de implantação do sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="dc286-1742">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="dc286-1743">Implantação da Web com o Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dc286-1743">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="dc286-1744">Consulte o tópico [Perfis de publicação do Visual Studio para implantação de aplicativos ASP.NET Core](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) para saber como criar um perfil de publicação para uso com a Implantação da Web.</span><span class="sxs-lookup"><span data-stu-id="dc286-1744">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="dc286-1745">Se o provedor de hospedagem fornecer um Perfil de Publicação ou o suporte para a criação de um, baixe o perfil e importe-o usando a caixa de diálogo **Publicar** do Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="dc286-1745">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![Página de caixa de diálogo Publicar](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="dc286-1747">Implantação da Web fora do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dc286-1747">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="dc286-1748">Também é possível usar a [Implantação da Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) fora do Visual Studio na linha de comando.</span><span class="sxs-lookup"><span data-stu-id="dc286-1748">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="dc286-1749">Para obter mais informações, consulte [Ferramenta de Implantação da Web](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span><span class="sxs-lookup"><span data-stu-id="dc286-1749">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="dc286-1750">Alternativas à Implantação da Web</span><span class="sxs-lookup"><span data-stu-id="dc286-1750">Alternatives to Web Deploy</span></span>

<span data-ttu-id="dc286-1751">Use qualquer um dos vários métodos para mover o aplicativo para o sistema host, como cópia manual, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy) ou [PowerShell](/powershell/).</span><span class="sxs-lookup"><span data-stu-id="dc286-1751">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="dc286-1752">Para obter mais informações sobre a implantação do ASP.NET Core no IIS, consulte a seção [Recursos de implantação para administradores do IIS](#deployment-resources-for-iis-administrators).</span><span class="sxs-lookup"><span data-stu-id="dc286-1752">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="dc286-1753">Procurar no site</span><span class="sxs-lookup"><span data-stu-id="dc286-1753">Browse the website</span></span>

<span data-ttu-id="dc286-1754">Depois de implantar o aplicativo no sistema de hospedagem, faça uma solicitação para um dos pontos de extremidade públicos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1754">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="dc286-1755">No exemplo a seguir, o site está associado a um **Nome do Host** IIS de `www.mysite.com` na **Porta** `80`.</span><span class="sxs-lookup"><span data-stu-id="dc286-1755">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="dc286-1756">É feita uma solicitação para `http://www.mysite.com`:</span><span class="sxs-lookup"><span data-stu-id="dc286-1756">A request is made to `http://www.mysite.com`:</span></span>

![O navegador Microsoft Edge carregou a página de inicialização do IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="dc286-1758">Arquivos de implantação bloqueados</span><span class="sxs-lookup"><span data-stu-id="dc286-1758">Locked deployment files</span></span>

<span data-ttu-id="dc286-1759">Os arquivos na pasta de implantação são bloqueados quando o aplicativo está em execução.</span><span class="sxs-lookup"><span data-stu-id="dc286-1759">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="dc286-1760">Os arquivos bloqueados não podem ser substituídos durante a implantação.</span><span class="sxs-lookup"><span data-stu-id="dc286-1760">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="dc286-1761">Para liberar os arquivos bloqueados em uma implantação, interrompa o pool de aplicativos usando **uma** das abordagens a seguir:</span><span class="sxs-lookup"><span data-stu-id="dc286-1761">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="dc286-1762">Use a Implantação da Web e referencie `Microsoft.NET.Sdk.Web` no arquivo do projeto.</span><span class="sxs-lookup"><span data-stu-id="dc286-1762">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="dc286-1763">Um arquivo *app_offline.htm* é colocado na raiz do diretório de aplicativo da Web.</span><span class="sxs-lookup"><span data-stu-id="dc286-1763">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="dc286-1764">Quando o arquivo estiver presente, o módulo do ASP.NET Core apenas desligará o aplicativo e servirá o arquivo *app_offline.htm* durante a implantação.</span><span class="sxs-lookup"><span data-stu-id="dc286-1764">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="dc286-1765">Para obter mais informações, consulte [Referência de configuração do módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="dc286-1765">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="dc286-1766">Manualmente interrompa o pool de aplicativos no Gerenciador do IIS no servidor.</span><span class="sxs-lookup"><span data-stu-id="dc286-1766">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="dc286-1767">Use o PowerShell para descartar *App_offline. htm* (requer o PowerShell 5 ou posterior):</span><span class="sxs-lookup"><span data-stu-id="dc286-1767">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="dc286-1768">Proteção de dados</span><span class="sxs-lookup"><span data-stu-id="dc286-1768">Data protection</span></span>

<span data-ttu-id="dc286-1769">A [pilha Proteção de Dados do ASP.NET Core](xref:security/data-protection/introduction) é usada por vários [middlewares](xref:fundamentals/middleware/index) ASP.NET Core, incluindo aqueles usados na autenticação.</span><span class="sxs-lookup"><span data-stu-id="dc286-1769">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="dc286-1770">Mesmo se as APIs de proteção de dados não forem chamadas pelo código do usuário, a proteção de dados deverá ser configurada com um script de implantação ou no código do usuário para criar um [repositório de chaves](xref:security/data-protection/implementation/key-management) criptográfico persistente.</span><span class="sxs-lookup"><span data-stu-id="dc286-1770">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="dc286-1771">Se a proteção de dados não estiver configurada, as chaves serão mantidas na memória e descartadas quando o aplicativo for reiniciado.</span><span class="sxs-lookup"><span data-stu-id="dc286-1771">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="dc286-1772">Se o token de autenticação for armazenado na memória quando o aplicativo for reiniciado:</span><span class="sxs-lookup"><span data-stu-id="dc286-1772">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="dc286-1773">Todos os tokens de autenticação baseados em cookies serão invalidados.</span><span class="sxs-lookup"><span data-stu-id="dc286-1773">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="dc286-1774">Os usuários precisam entrar novamente na próxima solicitação deles.</span><span class="sxs-lookup"><span data-stu-id="dc286-1774">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="dc286-1775">Todos os dados protegidos com o token de autenticação não poderão mais ser descriptografados.</span><span class="sxs-lookup"><span data-stu-id="dc286-1775">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="dc286-1776">Isso pode incluir [tokens CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) e [ASP.NET Core cookies TempData do MVC](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="dc286-1776">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="dc286-1777">Para configurar a proteção de dados no IIS para persistir o token de autenticação, use **uma** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="dc286-1777">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="dc286-1778">**Criar chaves de registro de proteção de dados**</span><span class="sxs-lookup"><span data-stu-id="dc286-1778">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="dc286-1779">As chaves de proteção de dados usadas pelos aplicativos ASP.NET Core são armazenadas no registro externo aos aplicativos.</span><span class="sxs-lookup"><span data-stu-id="dc286-1779">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="dc286-1780">Para persistir as chaves de determinado aplicativo, crie uma chave de registro para o pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="dc286-1780">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="dc286-1781">Para instalações autônomas do IIS não Web Farm, você pode usar o [script Provision-AutoGenKeys.ps1 de Proteção de Dados do PowerShell](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) para cada pool de aplicativos usado com um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dc286-1781">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="dc286-1782">Esse script cria uma chave de registro no registro HKLM que é acessível apenas para a conta do processo de trabalho do pool de aplicativos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1782">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="dc286-1783">As chaves são criptografadas em repouso usando a DPAPI com uma chave para todo o computador.</span><span class="sxs-lookup"><span data-stu-id="dc286-1783">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="dc286-1784">Em cenários de web farm, um aplicativo pode ser configurado para usar um caminho UNC para armazenar seu token de autenticação de proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="dc286-1784">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="dc286-1785">Por padrão, as chaves de proteção de dados não são criptografadas.</span><span class="sxs-lookup"><span data-stu-id="dc286-1785">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="dc286-1786">Garanta que as permissões de arquivo de o compartilhamento de rede sejam limitadas à conta do Windows na qual o aplicativo é executado.</span><span class="sxs-lookup"><span data-stu-id="dc286-1786">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="dc286-1787">Um certificado X509 pode ser usado para proteger chaves em repouso.</span><span class="sxs-lookup"><span data-stu-id="dc286-1787">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="dc286-1788">Considere um mecanismo para permitir aos usuários carregar certificados: coloque os certificados no repositório de certificados confiáveis do usuário e certifique-se de que eles estejam disponíveis em todos os computadores nos quais o aplicativo do usuário é executado.</span><span class="sxs-lookup"><span data-stu-id="dc286-1788">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="dc286-1789">Veja [Configurar a proteção de dados do ASP.NET Core](xref:security/data-protection/configuration/overview) para obter detalhes.</span><span class="sxs-lookup"><span data-stu-id="dc286-1789">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="dc286-1790">**Configurar o pool de aplicativos do IIS para carregar o perfil do usuário**</span><span class="sxs-lookup"><span data-stu-id="dc286-1790">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="dc286-1791">Essa configuração está na seção **Modelo de processo** nas **Configurações avançadas** do pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="dc286-1791">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="dc286-1792">Defina **carregar perfil do usuário** como `True` .</span><span class="sxs-lookup"><span data-stu-id="dc286-1792">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="dc286-1793">Quando definido como `True`, as chaves são armazenadas no diretório do perfil do usuário e protegidas usando DPAPI com uma chave específica para a conta de usuário.</span><span class="sxs-lookup"><span data-stu-id="dc286-1793">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="dc286-1794">As chaves são persistidas para a pasta *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys*.</span><span class="sxs-lookup"><span data-stu-id="dc286-1794">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="dc286-1795">O [atributo setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) do pool de aplicativos também deve ser habilitado.</span><span class="sxs-lookup"><span data-stu-id="dc286-1795">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="dc286-1796">O valor padrão de `setProfileEnvironment` é `true`.</span><span class="sxs-lookup"><span data-stu-id="dc286-1796">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="dc286-1797">Em alguns cenários (por exemplo, um SO Windows), `setProfileEnvironment` é definido como `false`.</span><span class="sxs-lookup"><span data-stu-id="dc286-1797">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="dc286-1798">Se as chaves não estiverem armazenadas no diretório do perfil do usuário como esperado:</span><span class="sxs-lookup"><span data-stu-id="dc286-1798">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="dc286-1799">navegue até a pasta *%windir%/system32/inetsrv/config*.</span><span class="sxs-lookup"><span data-stu-id="dc286-1799">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="dc286-1800">Abra o arquivo *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="dc286-1800">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="dc286-1801">Localize o elemento `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.</span><span class="sxs-lookup"><span data-stu-id="dc286-1801">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="dc286-1802">Confirme se o atributo `setProfileEnvironment` não está presente, que tem como padrão o valor `true`, ou defina explicitamente o valor do atributo como `true`.</span><span class="sxs-lookup"><span data-stu-id="dc286-1802">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="dc286-1803">**Usar o sistema de arquivos como um repositório de tokens de autenticação**</span><span class="sxs-lookup"><span data-stu-id="dc286-1803">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="dc286-1804">Ajuste o código do aplicativo para [usar o sistema de arquivos como um repositório de tokens de autenticação](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="dc286-1804">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="dc286-1805">Use um certificado X509 para proteger o token de autenticação e verifique se ele é um certificado confiável.</span><span class="sxs-lookup"><span data-stu-id="dc286-1805">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="dc286-1806">Se o certificado for autoassinado, você deverá colocá-lo no repositório Raiz confiável.</span><span class="sxs-lookup"><span data-stu-id="dc286-1806">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="dc286-1807">Ao usar o IIS em uma web farm:</span><span class="sxs-lookup"><span data-stu-id="dc286-1807">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="dc286-1808">Use um compartilhamento de arquivos que pode ser acessado por todos os computadores.</span><span class="sxs-lookup"><span data-stu-id="dc286-1808">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="dc286-1809">Implante um certificado X509 em cada computador.</span><span class="sxs-lookup"><span data-stu-id="dc286-1809">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="dc286-1810">Configure a [proteção de dados no código](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="dc286-1810">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="dc286-1811">**Definir uma política para todo o computador para proteção de dados**</span><span class="sxs-lookup"><span data-stu-id="dc286-1811">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="dc286-1812">O sistema de proteção de dados tem suporte limitado para a configuração da [política de todo o computador](xref:security/data-protection/configuration/machine-wide-policy) padrão para todos os aplicativos que consomem as APIs de proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="dc286-1812">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="dc286-1813">Para obter mais informações, consulte <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="dc286-1813">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="dc286-1814">Diretórios virtuais</span><span class="sxs-lookup"><span data-stu-id="dc286-1814">Virtual Directories</span></span>

<span data-ttu-id="dc286-1815">[Diretórios virtuais IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) não são compatíveis com aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dc286-1815">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="dc286-1816">Um aplicativo pode ser hospedado como um [subaplicativo](#sub-applications).</span><span class="sxs-lookup"><span data-stu-id="dc286-1816">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="dc286-1817">Subaplicativos</span><span class="sxs-lookup"><span data-stu-id="dc286-1817">Sub-applications</span></span>

<span data-ttu-id="dc286-1818">Um aplicativo ASP.NET Core pode ser hospedado como um [subaplicativo IIS (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span><span class="sxs-lookup"><span data-stu-id="dc286-1818">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="dc286-1819">A parte do caminho do subaplicativo se torna parte da URL raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1819">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="dc286-1820">Um subaplicativo não deve incluir o módulo do ASP.NET Core como um manipulador.</span><span class="sxs-lookup"><span data-stu-id="dc286-1820">A sub-app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="dc286-1821">Se o módulo for adicionado como um manipulador em um arquivo *Web. config* de um subaplicativo, um *erro de servidor interno 500,19* referenciando o arquivo de configuração com falha será recebido ao tentar procurar o subaplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1821">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="dc286-1822">O seguinte exemplo mostra um arquivo *web.config* publicado de um subaplicativo ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="dc286-1822">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="dc286-1823">Ao hospedar um subaplicativo não ASP.NET Core abaixo de um aplicativo ASP.NET Core, remova explicitamente o manipulador herdado no arquivo *web.config* do subaplicativo:</span><span class="sxs-lookup"><span data-stu-id="dc286-1823">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app's *web.config* file:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="dc286-1824">Links de ativos estáticos dentro do subaplicativo devem usar a notação de sinal de til e barra (`~/`).</span><span class="sxs-lookup"><span data-stu-id="dc286-1824">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="dc286-1825">A notação de sinal de til e barra aciona um [Auxiliar de Marca](xref:mvc/views/tag-helpers/intro) para preceder a base de caminho do subaplicativo ao link relativo renderizado.</span><span class="sxs-lookup"><span data-stu-id="dc286-1825">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="dc286-1826">Para um subaplicativo no `/subapp_path`, uma imagem vinculada com `src="~/image.png"` é renderizada como `src="/subapp_path/image.png"`.</span><span class="sxs-lookup"><span data-stu-id="dc286-1826">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="dc286-1827">O Middleware de Arquivo Estático do aplicativo raiz não processa a solicitação de arquivo estático.</span><span class="sxs-lookup"><span data-stu-id="dc286-1827">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="dc286-1828">A solicitação é processada pelo Middleware de Arquivo Estático do subaplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1828">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="dc286-1829">Se um atributo de ativo estático `src` for definido como um caminho absoluto (por exemplo, `src="/image.png"`), o link será renderizado sem a base de caminho do subaplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1829">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="dc286-1830">O Middleware de Arquivos Estáticos do aplicativo raiz tenta fornecer o ativo do [webroot](xref:fundamentals/index#web-root) da raiz do aplicativo, que resulta em uma resposta *404 – Não encontrado*, a menos que o ativo estático esteja disponível no aplicativo raiz.</span><span class="sxs-lookup"><span data-stu-id="dc286-1830">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="dc286-1831">Para hospedar um aplicativo ASP.NET Core como um subaplicativo em outro aplicativo do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="dc286-1831">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="dc286-1832">Estabeleça um pool de aplicativos para o subaplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1832">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="dc286-1833">Defina a **versão do CLR do .NET** como **Sem Código Gerenciado** porque o Core Common Language Runtime (CoreCLR) do .NET Core é inicializado para hospedar o aplicativo no processo de trabalho, não no CLR de Área de trabalho (CLR do .NET).</span><span class="sxs-lookup"><span data-stu-id="dc286-1833">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="dc286-1834">Adicione o site raiz no Gerenciador do IIS com o subaplicativo em uma pasta no site raiz.</span><span class="sxs-lookup"><span data-stu-id="dc286-1834">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="dc286-1835">Clique com o botão direito do mouse na pasta do subaplicativo no Gerenciador do IIS e selecione **Converter em aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1835">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="dc286-1836">Na caixa de diálogo **Adicionar Aplicativo**, use o botão **Selecionar** no **Pool de Aplicativos** para atribuir o pool de aplicativos que você criou ao subaplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1836">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="dc286-1837">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1837">Select **OK**.</span></span>

<span data-ttu-id="dc286-1838">A atribuição de um pool de aplicativos separado para o subaplicativo é um requisito ao usar o modelo de hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1838">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="dc286-1839">Para obter mais informações sobre o modelo de hospedagem em processo e como configurar o módulo ASP.NET Core, consulte <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="dc286-1839">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="dc286-1840">Configuração do IIS com web.config</span><span class="sxs-lookup"><span data-stu-id="dc286-1840">Configuration of IIS with web.config</span></span>

<span data-ttu-id="dc286-1841">A configuração do IIS é influenciada pela seção `<system.webServer>` do *web.config* para cenários do IIS que são funcionais para aplicativos ASP.NET Core com o Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dc286-1841">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="dc286-1842">Por exemplo, a configuração do IIS é funcional para a compactação dinâmica.</span><span class="sxs-lookup"><span data-stu-id="dc286-1842">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="dc286-1843">Se o IIS for configurado no nível do servidor para usar a compactação dinâmica, o elemento `<urlCompression>` no arquivo *web.config* do aplicativo pode desabilitá-la para um aplicativo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dc286-1843">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="dc286-1844">Para obter mais informações, consulte estes tópicos:</span><span class="sxs-lookup"><span data-stu-id="dc286-1844">For more information, see the following topics:</span></span>

* [<span data-ttu-id="dc286-1845">Referência de configuração para\<system.webServer></span><span class="sxs-lookup"><span data-stu-id="dc286-1845">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="dc286-1846">Para definir variáveis de ambiente para aplicativos individuais em execução em pools de aplicativos isolados (com suporte para o IIS 10,0 ou posterior), consulte a seção de *comando Appcmd. exe* do tópico [ \<environmentVariables> variáveis de ambiente](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) na documentação de referência do IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-1846">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="dc286-1847">Seções de configuração de web.config</span><span class="sxs-lookup"><span data-stu-id="dc286-1847">Configuration sections of web.config</span></span>

<span data-ttu-id="dc286-1848">As seções de configuração de aplicativos ASP.NET 4.x em *web.config* não são usadas por aplicativos ASP.NET Core para configuração:</span><span class="sxs-lookup"><span data-stu-id="dc286-1848">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="dc286-1849">Aplicativos ASP.NET Core são configurados para usar outros provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="dc286-1849">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="dc286-1850">Para obter mais informações, consulte [Configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="dc286-1850">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="dc286-1851">Pools de aplicativos</span><span class="sxs-lookup"><span data-stu-id="dc286-1851">Application Pools</span></span>

<span data-ttu-id="dc286-1852">Ao hospedar vários sites em um servidor, é recomendável isolar os aplicativos uns dos outros, executando cada aplicativo em seu próprio pool de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc286-1852">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="dc286-1853">A caixa de diálogo **Adicionar Site** do IIS usa como padrão essa configuração.</span><span class="sxs-lookup"><span data-stu-id="dc286-1853">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="dc286-1854">Quando um **Nome de site** é fornecido, o texto é transferido automaticamente para a caixa de texto **Pool de aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1854">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="dc286-1855">Um novo pool de aplicativos é criado usando o nome do site quando você adicionar o site.</span><span class="sxs-lookup"><span data-stu-id="dc286-1855">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="dc286-1856">Pool de aplicativosIdentity</span><span class="sxs-lookup"><span data-stu-id="dc286-1856">Application Pool Identity</span></span>

<span data-ttu-id="dc286-1857">Uma conta de identidade do pool de aplicativos permite executar um aplicativo em uma conta exclusiva sem a necessidade de criar e gerenciar domínios ou contas locais.</span><span class="sxs-lookup"><span data-stu-id="dc286-1857">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="dc286-1858">No IIS 8.0 ou posterior, o WAS (Processo de trabalho do administrador) do IIS cria uma conta virtual com o nome do novo pool de aplicativos e executa os processos de trabalho do pool de aplicativos nesta conta por padrão.</span><span class="sxs-lookup"><span data-stu-id="dc286-1858">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="dc286-1859">No console de gerenciamento do IIS, em **Configurações avançadas** para o pool de aplicativos, verifique se o **Identity** está definido para usar **ApplicationPoolIdentity**:</span><span class="sxs-lookup"><span data-stu-id="dc286-1859">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![Caixa de diálogo Configurações avançadas do pool de aplicativos](index/_static/apppool-identity.png)

<span data-ttu-id="dc286-1861">O processo de gerenciamento do IIS cria um identificador seguro com o nome do pool de aplicativos no Sistema de segurança do Windows.</span><span class="sxs-lookup"><span data-stu-id="dc286-1861">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="dc286-1862">Os recursos podem ser protegidos usando essa identidade.</span><span class="sxs-lookup"><span data-stu-id="dc286-1862">Resources can be secured using this identity.</span></span> <span data-ttu-id="dc286-1863">No entanto, essa identidade não é uma conta de usuário real e não será mostrada no Console de Gerenciamento de Usuários do Windows.</span><span class="sxs-lookup"><span data-stu-id="dc286-1863">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="dc286-1864">Se o processo de trabalho do IIS requerer acesso elevado ao aplicativo, modifique a ACL (lista de controle de acesso) do diretório que contém o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="dc286-1864">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="dc286-1865">Abra o Windows Explorer e navegue para o diretório.</span><span class="sxs-lookup"><span data-stu-id="dc286-1865">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="dc286-1866">Clique com o botão direito do mouse no diretório e selecione **Propriedades**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1866">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="dc286-1867">Na guia **Segurança**, selecione o botão **Editar** e, em seguida, no botão **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1867">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="dc286-1868">Clique no botão **Locais** e verifique se o sistema está selecionado.</span><span class="sxs-lookup"><span data-stu-id="dc286-1868">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="dc286-1869">Insira **IIS AppPool\\<nome_pool_aplicativos>** na área **Inserir os nomes de objeto a serem selecionados**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1869">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="dc286-1870">Selecione o botão **Verificar Nomes**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1870">Select the **Check Names** button.</span></span> <span data-ttu-id="dc286-1871">Para o *DefaultAppPool*, verifique os nomes usando **IIS AppPool\DefaultAppPool**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1871">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="dc286-1872">Quando o botão **Verificar Nomes** é selecionado, um valor de **DefaultAppPool** é indicado na área de nomes de objeto.</span><span class="sxs-lookup"><span data-stu-id="dc286-1872">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="dc286-1873">Não é possível inserir o nome do pool de aplicativos diretamente na área de nomes de objeto.</span><span class="sxs-lookup"><span data-stu-id="dc286-1873">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="dc286-1874">Use o formato **IIS AppPool\\<nome_pool_aplicativos>** ao verificar o nome do objeto.</span><span class="sxs-lookup"><span data-stu-id="dc286-1874">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![Selecione a caixa de diálogo de usuários ou grupos para a pasta do aplicativo: o nome do pool de aplicativos "DefaultAppPool" é anexado ao "IIS AppPool\" na área de nomes de objeto antes de selecionar"Verificar Nomes".](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="dc286-1876">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1876">Select **OK**.</span></span>

   ![Selecione a caixa de diálogo de usuários ou grupos para a pasta do aplicativo: depois de selecionar "Verificar Nomes", o nome do objeto "DefaultAppPool" é mostrado na área de nomes de objeto.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="dc286-1878">As permissões de leitura &amp; execução devem ser concedidas por padrão.</span><span class="sxs-lookup"><span data-stu-id="dc286-1878">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="dc286-1879">Forneça permissões adicionais conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="dc286-1879">Provide additional permissions as needed.</span></span>

<span data-ttu-id="dc286-1880">O acesso também pode ser concedido por meio de um prompt de comando usando a ferramenta **ICACLS**.</span><span class="sxs-lookup"><span data-stu-id="dc286-1880">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="dc286-1881">Usando o *DefaultAppPool* como exemplo, o comando a seguir é usado:</span><span class="sxs-lookup"><span data-stu-id="dc286-1881">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="dc286-1882">Para saber mais, veja o tópico [icacls](/windows-server/administration/windows-commands/icacls).</span><span class="sxs-lookup"><span data-stu-id="dc286-1882">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="dc286-1883">Suporte do HTTP/2</span><span class="sxs-lookup"><span data-stu-id="dc286-1883">HTTP/2 support</span></span>

<span data-ttu-id="dc286-1884">O [HTTP/2](https://httpwg.org/specs/rfc7540.html) é compatível com implantações fora de processo que cumprem os seguintes requisitos básicos:</span><span class="sxs-lookup"><span data-stu-id="dc286-1884">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="dc286-1885">Windows Server 2016/Windows 10 ou posterior; IIS 10 ou posterior</span><span class="sxs-lookup"><span data-stu-id="dc286-1885">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="dc286-1886">Conexões de servidor de borda voltadas para o público usam HTTP/2, mas a conexão de proxy reverso para o [servidor Kestrel](xref:fundamentals/servers/kestrel) usa HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="dc286-1886">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="dc286-1887">Estrutura de destino: não se aplica a implantações fora de processo, visto que a conexão HTTP/2 é manipulada inteiramente pelo IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-1887">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="dc286-1888">Conexão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="dc286-1888">TLS 1.2 or later connection</span></span>

<span data-ttu-id="dc286-1889">Se uma conexão HTTP/2 for estabelecida, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatará `HTTP/1.1`.</span><span class="sxs-lookup"><span data-stu-id="dc286-1889">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="dc286-1890">O HTTP/2 está habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="dc286-1890">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="dc286-1891">As conexões retornarão para HTTP/1.1 se uma conexão HTTP/2 não for estabelecida.</span><span class="sxs-lookup"><span data-stu-id="dc286-1891">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="dc286-1892">Para obter mais informações sobre a configuração de HTTP/2 com implantações do IIS, consulte [HTTP/2 no IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="dc286-1892">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="dc286-1893">Solicitações de simulação do CORS</span><span class="sxs-lookup"><span data-stu-id="dc286-1893">CORS preflight requests</span></span>

<span data-ttu-id="dc286-1894">*Esta seção só se aplica a aplicativos ASP.NET Core com o .NET Framework como destino.*</span><span class="sxs-lookup"><span data-stu-id="dc286-1894">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="dc286-1895">Para um aplicativo ASP.NET Core com o .NET Framework como destino, as solicitações OPTIONS não são passadas para o aplicativo por padrão no IIS.</span><span class="sxs-lookup"><span data-stu-id="dc286-1895">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="dc286-1896">Para saber como configurar os manipuladores do IIS do aplicativo no *Web. config* para passar solicitações de opções, consulte [habilitar solicitações entre origens no ASP.NET Web API 2: como o CORS funciona](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span><span class="sxs-lookup"><span data-stu-id="dc286-1896">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="dc286-1897">Recursos de implantação para administradores do IIS</span><span class="sxs-lookup"><span data-stu-id="dc286-1897">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="dc286-1898">Documentação do ISS</span><span class="sxs-lookup"><span data-stu-id="dc286-1898">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="dc286-1899">Introdução ao Gerenciador do IIS no IIS</span><span class="sxs-lookup"><span data-stu-id="dc286-1899">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="dc286-1900">Implantação de aplicativo .NET Core</span><span class="sxs-lookup"><span data-stu-id="dc286-1900">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="dc286-1901">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="dc286-1901">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="dc286-1902">O site oficial da IIS da Microsoft</span><span class="sxs-lookup"><span data-stu-id="dc286-1902">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="dc286-1903">Biblioteca de conteúdo técnico do Windows Server</span><span class="sxs-lookup"><span data-stu-id="dc286-1903">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="dc286-1904">HTTP/2 no IIS</span><span class="sxs-lookup"><span data-stu-id="dc286-1904">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end
