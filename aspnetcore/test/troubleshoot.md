---
<span data-ttu-id="202a6-101">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="202a6-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="202a6-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="202a6-102">'Blazor'</span></span>
- <span data-ttu-id="202a6-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="202a6-103">'Identity'</span></span>
- <span data-ttu-id="202a6-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="202a6-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="202a6-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="202a6-105">'Razor'</span></span>
- <span data-ttu-id="202a6-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="202a6-106">'SignalR' uid:</span></span> 

---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a><span data-ttu-id="202a6-107">Solucionar problemas e depurar projetos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="202a6-107">Troubleshoot and debug ASP.NET Core projects</span></span>

<span data-ttu-id="202a6-108">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="202a6-108">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="202a6-109">Os links a seguir fornecem orientação para a solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="202a6-109">The following links provide troubleshooting guidance:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="202a6-110">NDC Conference (Londres, 2018): diagnosticar problemas em aplicativos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="202a6-110">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="202a6-111">Blog do ASP.NET: solução de problemas de desempenho de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="202a6-111">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="202a6-112">Avisos de SDK do .NET Core</span><span class="sxs-lookup"><span data-stu-id="202a6-112">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="202a6-113">As versões de 32 bits e 64 bits do SDK do .NET Core estão instaladas</span><span class="sxs-lookup"><span data-stu-id="202a6-113">Both the 32-bit and 64-bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="202a6-114">Na caixa de diálogo **novo projeto** para ASP.NET Core, você poderá ver o seguinte aviso:</span><span class="sxs-lookup"><span data-stu-id="202a6-114">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="202a6-115">As versões de 32 bits e 64 bits do SDK do .NET Core estão instaladas.</span><span class="sxs-lookup"><span data-stu-id="202a6-115">Both 32-bit and 64-bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="202a6-116">Somente os modelos das versões de 64 bits instaladas em ' C: \\ arquivos de programas \\ SDK do dotnet \\ \\ ' são exibidos.</span><span class="sxs-lookup"><span data-stu-id="202a6-116">Only templates from the 64-bit versions installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="202a6-117">Esse aviso aparece quando as versões de 32 bits (x86) e 64 de bits (x64) do [SDK do .NET Core](https://dotnet.microsoft.com/download/dotnet-core) são instaladas.</span><span class="sxs-lookup"><span data-stu-id="202a6-117">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) are installed.</span></span> <span data-ttu-id="202a6-118">Os motivos comuns pelos quais ambas as versões podem ser instaladas incluem:</span><span class="sxs-lookup"><span data-stu-id="202a6-118">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="202a6-119">Você baixou originalmente o instalador do SDK do .NET Core usando um computador de 32 bits, mas o copiou e o instalou em um computador de 64 bits.</span><span class="sxs-lookup"><span data-stu-id="202a6-119">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="202a6-120">O SDK do .NET Core de 32 bits foi instalado por outro aplicativo.</span><span class="sxs-lookup"><span data-stu-id="202a6-120">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="202a6-121">A versão errada foi baixada e instalada.</span><span class="sxs-lookup"><span data-stu-id="202a6-121">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="202a6-122">Desinstale o SDK do .NET Core de 32 bits para evitar esse aviso.</span><span class="sxs-lookup"><span data-stu-id="202a6-122">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="202a6-123">Desinstalar de **Control Panel**  >  **programas e recursos**do painel de controle  >  **desinstalar ou alterar um programa**.</span><span class="sxs-lookup"><span data-stu-id="202a6-123">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="202a6-124">Se você entender por que o aviso ocorre e suas implicações, poderá ignorar o aviso.</span><span class="sxs-lookup"><span data-stu-id="202a6-124">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="202a6-125">O SDK do .NET Core é instalado em vários locais</span><span class="sxs-lookup"><span data-stu-id="202a6-125">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="202a6-126">Na caixa de diálogo **novo projeto** para ASP.NET Core, você poderá ver o seguinte aviso:</span><span class="sxs-lookup"><span data-stu-id="202a6-126">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="202a6-127">O SDK do .NET Core é instalado em vários locais.</span><span class="sxs-lookup"><span data-stu-id="202a6-127">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="202a6-128">Somente os modelos dos SDKs instalados em ' C: \\ arquivos de \\ programas \\ SDK do dotnet \\ ' são exibidos.</span><span class="sxs-lookup"><span data-stu-id="202a6-128">Only templates from the SDKs installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="202a6-129">Você verá essa mensagem quando tiver pelo menos uma instalação do SDK do .NET Core em um diretório fora de *C: Arquivos de \\ programas \\ \\ SDK \\ do dotnet*.</span><span class="sxs-lookup"><span data-stu-id="202a6-129">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="202a6-130">Geralmente isso acontece quando o SDK do .NET Core foi implantado em um computador usando copiar/colar em vez do instalador MSI.</span><span class="sxs-lookup"><span data-stu-id="202a6-130">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="202a6-131">Desinstale todos os SDKs e tempos de execução do .NET Core de 32 bits para evitar esse aviso.</span><span class="sxs-lookup"><span data-stu-id="202a6-131">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="202a6-132">Desinstalar de **Control Panel**  >  **programas e recursos**do painel de controle  >  **desinstalar ou alterar um programa**.</span><span class="sxs-lookup"><span data-stu-id="202a6-132">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="202a6-133">Se você entender por que o aviso ocorre e suas implicações, poderá ignorar o aviso.</span><span class="sxs-lookup"><span data-stu-id="202a6-133">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="202a6-134">Nenhum SDK do .NET Core foi detectado</span><span class="sxs-lookup"><span data-stu-id="202a6-134">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="202a6-135">Na caixa de diálogo **novo projeto** do Visual Studio para ASP.NET Core, você poderá ver o seguinte aviso:</span><span class="sxs-lookup"><span data-stu-id="202a6-135">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="202a6-136">Nenhum SDK do .NET Core foi detectado, verifique se eles estão incluídos na variável de ambiente `PATH` .</span><span class="sxs-lookup"><span data-stu-id="202a6-136">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="202a6-137">Ao executar um `dotnet` comando, o aviso aparece como:</span><span class="sxs-lookup"><span data-stu-id="202a6-137">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="202a6-138">Não foi possível encontrar nenhum SDKs dotnet instalado.</span><span class="sxs-lookup"><span data-stu-id="202a6-138">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="202a6-139">Esses avisos aparecem quando a variável de ambiente `PATH` não aponta para nenhum SDK do .NET Core no computador.</span><span class="sxs-lookup"><span data-stu-id="202a6-139">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="202a6-140">Para resolver esse problema:</span><span class="sxs-lookup"><span data-stu-id="202a6-140">To resolve this problem:</span></span>

* <span data-ttu-id="202a6-141">Instale o SDK do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="202a6-141">Install the .NET Core SDK.</span></span> <span data-ttu-id="202a6-142">Obtenha o instalador mais recente de [downloads do .net](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="202a6-142">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="202a6-143">Verifique se a `PATH` variável de ambiente aponta para o local onde o SDK está instalado ( `C:\Program Files\dotnet\` para 64 bits/x64 ou `C:\Program Files (x86)\dotnet\` para 32 bits/x86).</span><span class="sxs-lookup"><span data-stu-id="202a6-143">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="202a6-144">O instalador do SDK normalmente define o `PATH` .</span><span class="sxs-lookup"><span data-stu-id="202a6-144">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="202a6-145">Sempre instale os mesmos SDKs de bits e tempos de execução no mesmo computador.</span><span class="sxs-lookup"><span data-stu-id="202a6-145">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="202a6-146">SDK ausente após a instalação do pacote de hospedagem do .NET Core</span><span class="sxs-lookup"><span data-stu-id="202a6-146">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="202a6-147">A instalação do [pacote de hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifica o `PATH` quando ele instala o tempo de execução do .NET Core para apontar para a versão de 32 bits (x86) do .NET Core ( `C:\Program Files (x86)\dotnet\` ).</span><span class="sxs-lookup"><span data-stu-id="202a6-147">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="202a6-148">Isso pode resultar na ausência de SDKs quando o comando .NET Core de 32 bits (x86) `dotnet` for usado ([nenhum SDK do .NET Core foi detectado](#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="202a6-148">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="202a6-149">Para resolver esse problema, mova `C:\Program Files\dotnet\` para uma posição anterior `C:\Program Files (x86)\dotnet\` no `PATH` .</span><span class="sxs-lookup"><span data-stu-id="202a6-149">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="202a6-150">Obter dados de um aplicativo</span><span class="sxs-lookup"><span data-stu-id="202a6-150">Obtain data from an app</span></span>

<span data-ttu-id="202a6-151">Se um aplicativo for capaz de responder às solicitações, você poderá obter os seguintes dados do aplicativo usando o middleware:</span><span class="sxs-lookup"><span data-stu-id="202a6-151">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="202a6-152">Solicitação: método, esquema, host, pathbase, caminho, Cadeia de caracteres de consulta, cabeçalhos</span><span class="sxs-lookup"><span data-stu-id="202a6-152">Request: Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="202a6-153">Conexão: endereço IP remoto, porta remota, endereço IP local, porta local, certificado de cliente</span><span class="sxs-lookup"><span data-stu-id="202a6-153">Connection: Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* Identity: Name, display name
* <span data-ttu-id="202a6-154">Definições de configuração</span><span class="sxs-lookup"><span data-stu-id="202a6-154">Configuration settings</span></span>
* <span data-ttu-id="202a6-155">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="202a6-155">Environment variables</span></span>

<span data-ttu-id="202a6-156">Coloque o seguinte código de [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) no início do pipeline de `Startup.Configure` processamento de solicitação do método.</span><span class="sxs-lookup"><span data-stu-id="202a6-156">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="202a6-157">O ambiente é verificado antes que o middleware seja executado para garantir que o código seja executado apenas no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="202a6-157">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="202a6-158">Para obter o ambiente, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="202a6-158">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="202a6-159">Insira o `IHostingEnvironment` no `Startup.Configure` método e verifique o ambiente com a variável local.</span><span class="sxs-lookup"><span data-stu-id="202a6-159">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="202a6-160">O código de exemplo a seguir demonstra essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="202a6-160">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="202a6-161">Atribua o ambiente a uma propriedade na `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="202a6-161">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="202a6-162">Verifique o ambiente usando a propriedade (por exemplo, `if (Environment.IsDevelopment())` ).</span><span class="sxs-lookup"><span data-stu-id="202a6-162">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, 
    IConfiguration config)
{
    if (env.IsDevelopment())
    {
        app.Run(async (context) =>
        {
            var sb = new StringBuilder();
            var nl = System.Environment.NewLine;
            var rule = string.Concat(nl, new string('-', 40), nl);
            var authSchemeProvider = app.ApplicationServices
                .GetRequiredService<IAuthenticationSchemeProvider>();

            sb.Append($"Request{rule}");
            sb.Append($"{DateTimeOffset.Now}{nl}");
            sb.Append($"{context.Request.Method} {context.Request.Path}{nl}");
            sb.Append($"Scheme: {context.Request.Scheme}{nl}");
            sb.Append($"Host: {context.Request.Headers["Host"]}{nl}");
            sb.Append($"PathBase: {context.Request.PathBase.Value}{nl}");
            sb.Append($"Path: {context.Request.Path.Value}{nl}");
            sb.Append($"Query: {context.Request.QueryString.Value}{nl}{nl}");

            sb.Append($"Connection{rule}");
            sb.Append($"RemoteIp: {context.Connection.RemoteIpAddress}{nl}");
            sb.Append($"RemotePort: {context.Connection.RemotePort}{nl}");
            sb.Append($"LocalIp: {context.Connection.LocalIpAddress}{nl}");
            sb.Append($"LocalPort: {context.Connection.LocalPort}{nl}");
            sb.Append($"ClientCert: {context.Connection.ClientCertificate}{nl}{nl}");

            sb.Append($"Identity{rule}");
            sb.Append($"User: {context.User.Identity.Name}{nl}");
            var scheme = await authSchemeProvider
                .GetSchemeAsync(IISDefaults.AuthenticationScheme);
            sb.Append($"DisplayName: {scheme?.DisplayName}{nl}{nl}");

            sb.Append($"Headers{rule}");
            foreach (var header in context.Request.Headers)
            {
                sb.Append($"{header.Key}: {header.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Websockets{rule}");
            if (context.Features.Get<IHttpUpgradeFeature>() != null)
            {
                sb.Append($"Status: Enabled{nl}{nl}");
            }
            else
            {
                sb.Append($"Status: Disabled{nl}{nl}");
            }

            sb.Append($"Configuration{rule}");
            foreach (var pair in config.AsEnumerable())
            {
                sb.Append($"{pair.Path}: {pair.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Environment Variables{rule}");
            var vars = System.Environment.GetEnvironmentVariables();
            foreach (var key in vars.Keys.Cast<string>().OrderBy(key => key, 
                StringComparer.OrdinalIgnoreCase))
            {
                var value = vars[key];
                sb.Append($"{key}: {value}{nl}");
            }

            context.Response.ContentType = "text/plain";
            await context.Response.WriteAsync(sb.ToString());
        });
    }
}
```

## <a name="debug-aspnet-core-apps"></a><span data-ttu-id="202a6-163">Depurar ASP.NET Core aplicativos</span><span class="sxs-lookup"><span data-stu-id="202a6-163">Debug ASP.NET Core apps</span></span>

<span data-ttu-id="202a6-164">Os links a seguir fornecem informações sobre como depurar aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="202a6-164">The following links provide information on debugging ASP.NET Core apps.</span></span>

* [<span data-ttu-id="202a6-165">Depuração do ASP Core no Linux</span><span class="sxs-lookup"><span data-stu-id="202a6-165">Debugging ASP Core on Linux</span></span>](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [<span data-ttu-id="202a6-166">Depuração do .NET Core no UNIX por SSH</span><span class="sxs-lookup"><span data-stu-id="202a6-166">Debugging .NET Core on Unix over SSH</span></span>](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [<span data-ttu-id="202a6-167">Início Rápido: depurar o ASP.NET com o depurador do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="202a6-167">Quickstart: Debug ASP.NET with the Visual Studio debugger</span></span>](/visualstudio/debugger/quickstart-debug-aspnet)
* <span data-ttu-id="202a6-168">Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/2960) para obter mais informações de depuração.</span><span class="sxs-lookup"><span data-stu-id="202a6-168">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/2960) for more debugging information.</span></span>
