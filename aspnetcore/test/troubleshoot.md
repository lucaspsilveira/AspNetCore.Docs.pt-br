---
title: Solucionar problemas e depurar projetos ASP.NET Core
author: Rick-Anderson
description: Compreenda e solucione problemas de avisos e erros com projetos do ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/troubleshoot
ms.openlocfilehash: bd5e74569717db26496760eaaa2760122d4b49e7
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106488"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a><span data-ttu-id="ac123-103">Solucionar problemas e depurar projetos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ac123-103">Troubleshoot and debug ASP.NET Core projects</span></span>

<span data-ttu-id="ac123-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ac123-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ac123-105">Os links a seguir fornecem orientação para a solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="ac123-105">The following links provide troubleshooting guidance:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="ac123-106">NDC Conference (Londres, 2018): diagnosticar problemas em aplicativos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ac123-106">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="ac123-107">Blog do ASP.NET: solução de problemas de desempenho de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ac123-107">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="ac123-108">Avisos de SDK do .NET Core</span><span class="sxs-lookup"><span data-stu-id="ac123-108">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="ac123-109">As versões de 32 bits e 64 bits do SDK do .NET Core estão instaladas</span><span class="sxs-lookup"><span data-stu-id="ac123-109">Both the 32-bit and 64-bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="ac123-110">Na caixa de diálogo **novo projeto** para ASP.NET Core, você poderá ver o seguinte aviso:</span><span class="sxs-lookup"><span data-stu-id="ac123-110">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="ac123-111">As versões de 32 bits e 64 bits do SDK do .NET Core estão instaladas.</span><span class="sxs-lookup"><span data-stu-id="ac123-111">Both 32-bit and 64-bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="ac123-112">Somente os modelos das versões de 64 bits instaladas em ' C: \\ arquivos de programas \\ SDK do dotnet \\ \\ ' são exibidos.</span><span class="sxs-lookup"><span data-stu-id="ac123-112">Only templates from the 64-bit versions installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="ac123-113">Esse aviso aparece quando as versões de 32 bits (x86) e 64 de bits (x64) do [SDK do .NET Core](https://dotnet.microsoft.com/download/dotnet-core) são instaladas.</span><span class="sxs-lookup"><span data-stu-id="ac123-113">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) are installed.</span></span> <span data-ttu-id="ac123-114">Os motivos comuns pelos quais ambas as versões podem ser instaladas incluem:</span><span class="sxs-lookup"><span data-stu-id="ac123-114">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="ac123-115">Você baixou originalmente o instalador do SDK do .NET Core usando um computador de 32 bits, mas o copiou e o instalou em um computador de 64 bits.</span><span class="sxs-lookup"><span data-stu-id="ac123-115">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="ac123-116">O SDK do .NET Core de 32 bits foi instalado por outro aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ac123-116">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="ac123-117">A versão errada foi baixada e instalada.</span><span class="sxs-lookup"><span data-stu-id="ac123-117">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="ac123-118">Desinstale o SDK do .NET Core de 32 bits para evitar esse aviso.</span><span class="sxs-lookup"><span data-stu-id="ac123-118">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="ac123-119">Desinstalar de **Control Panel**  >  **programas e recursos**do painel de controle  >  **desinstalar ou alterar um programa**.</span><span class="sxs-lookup"><span data-stu-id="ac123-119">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="ac123-120">Se você entender por que o aviso ocorre e suas implicações, poderá ignorar o aviso.</span><span class="sxs-lookup"><span data-stu-id="ac123-120">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="ac123-121">O SDK do .NET Core é instalado em vários locais</span><span class="sxs-lookup"><span data-stu-id="ac123-121">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="ac123-122">Na caixa de diálogo **novo projeto** para ASP.NET Core, você poderá ver o seguinte aviso:</span><span class="sxs-lookup"><span data-stu-id="ac123-122">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="ac123-123">O SDK do .NET Core é instalado em vários locais.</span><span class="sxs-lookup"><span data-stu-id="ac123-123">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="ac123-124">Somente os modelos dos SDKs instalados em ' C: \\ arquivos de \\ programas \\ SDK do dotnet \\ ' são exibidos.</span><span class="sxs-lookup"><span data-stu-id="ac123-124">Only templates from the SDKs installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="ac123-125">Você verá essa mensagem quando tiver pelo menos uma instalação do SDK do .NET Core em um diretório fora de *C: Arquivos de \\ programas \\ \\ SDK \\ do dotnet*.</span><span class="sxs-lookup"><span data-stu-id="ac123-125">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="ac123-126">Geralmente isso acontece quando o SDK do .NET Core foi implantado em um computador usando copiar/colar em vez do instalador MSI.</span><span class="sxs-lookup"><span data-stu-id="ac123-126">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="ac123-127">Desinstale todos os SDKs e tempos de execução do .NET Core de 32 bits para evitar esse aviso.</span><span class="sxs-lookup"><span data-stu-id="ac123-127">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="ac123-128">Desinstalar de **Control Panel**  >  **programas e recursos**do painel de controle  >  **desinstalar ou alterar um programa**.</span><span class="sxs-lookup"><span data-stu-id="ac123-128">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="ac123-129">Se você entender por que o aviso ocorre e suas implicações, poderá ignorar o aviso.</span><span class="sxs-lookup"><span data-stu-id="ac123-129">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="ac123-130">Nenhum SDK do .NET Core foi detectado</span><span class="sxs-lookup"><span data-stu-id="ac123-130">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="ac123-131">Na caixa de diálogo **novo projeto** do Visual Studio para ASP.NET Core, você poderá ver o seguinte aviso:</span><span class="sxs-lookup"><span data-stu-id="ac123-131">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="ac123-132">Nenhum SDK do .NET Core foi detectado, verifique se eles estão incluídos na variável de ambiente `PATH` .</span><span class="sxs-lookup"><span data-stu-id="ac123-132">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="ac123-133">Ao executar um `dotnet` comando, o aviso aparece como:</span><span class="sxs-lookup"><span data-stu-id="ac123-133">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="ac123-134">Não foi possível encontrar nenhum SDKs dotnet instalado.</span><span class="sxs-lookup"><span data-stu-id="ac123-134">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="ac123-135">Esses avisos aparecem quando a variável de ambiente `PATH` não aponta para nenhum SDK do .NET Core no computador.</span><span class="sxs-lookup"><span data-stu-id="ac123-135">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="ac123-136">Para resolver esse problema:</span><span class="sxs-lookup"><span data-stu-id="ac123-136">To resolve this problem:</span></span>

* <span data-ttu-id="ac123-137">Instale o SDK do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ac123-137">Install the .NET Core SDK.</span></span> <span data-ttu-id="ac123-138">Obtenha o instalador mais recente de [downloads do .net](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="ac123-138">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="ac123-139">Verifique se a `PATH` variável de ambiente aponta para o local onde o SDK está instalado ( `C:\Program Files\dotnet\` para 64 bits/x64 ou `C:\Program Files (x86)\dotnet\` para 32 bits/x86).</span><span class="sxs-lookup"><span data-stu-id="ac123-139">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="ac123-140">O instalador do SDK normalmente define o `PATH` .</span><span class="sxs-lookup"><span data-stu-id="ac123-140">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="ac123-141">Sempre instale os mesmos SDKs de bits e tempos de execução no mesmo computador.</span><span class="sxs-lookup"><span data-stu-id="ac123-141">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="ac123-142">SDK ausente após a instalação do pacote de hospedagem do .NET Core</span><span class="sxs-lookup"><span data-stu-id="ac123-142">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="ac123-143">A instalação do [pacote de hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifica o `PATH` quando ele instala o tempo de execução do .NET Core para apontar para a versão de 32 bits (x86) do .NET Core ( `C:\Program Files (x86)\dotnet\` ).</span><span class="sxs-lookup"><span data-stu-id="ac123-143">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="ac123-144">Isso pode resultar na ausência de SDKs quando o comando .NET Core de 32 bits (x86) `dotnet` for usado ([nenhum SDK do .NET Core foi detectado](#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="ac123-144">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="ac123-145">Para resolver esse problema, mova `C:\Program Files\dotnet\` para uma posição anterior `C:\Program Files (x86)\dotnet\` no `PATH` .</span><span class="sxs-lookup"><span data-stu-id="ac123-145">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="ac123-146">Obter dados de um aplicativo</span><span class="sxs-lookup"><span data-stu-id="ac123-146">Obtain data from an app</span></span>

<span data-ttu-id="ac123-147">Se um aplicativo for capaz de responder às solicitações, você poderá obter os seguintes dados do aplicativo usando o middleware:</span><span class="sxs-lookup"><span data-stu-id="ac123-147">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="ac123-148">Solicitação: método, esquema, host, pathbase, caminho, Cadeia de caracteres de consulta, cabeçalhos</span><span class="sxs-lookup"><span data-stu-id="ac123-148">Request: Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="ac123-149">Conexão: endereço IP remoto, porta remota, endereço IP local, porta local, certificado de cliente</span><span class="sxs-lookup"><span data-stu-id="ac123-149">Connection: Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* Identity: Name, display name
* <span data-ttu-id="ac123-150">Definições de configuração</span><span class="sxs-lookup"><span data-stu-id="ac123-150">Configuration settings</span></span>
* <span data-ttu-id="ac123-151">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="ac123-151">Environment variables</span></span>

<span data-ttu-id="ac123-152">Coloque o seguinte código de [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) no início do pipeline de `Startup.Configure` processamento de solicitação do método.</span><span class="sxs-lookup"><span data-stu-id="ac123-152">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="ac123-153">O ambiente é verificado antes que o middleware seja executado para garantir que o código seja executado apenas no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="ac123-153">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="ac123-154">Para obter o ambiente, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="ac123-154">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="ac123-155">Insira o `IHostingEnvironment` no `Startup.Configure` método e verifique o ambiente com a variável local.</span><span class="sxs-lookup"><span data-stu-id="ac123-155">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="ac123-156">O código de exemplo a seguir demonstra essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="ac123-156">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="ac123-157">Atribua o ambiente a uma propriedade na `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="ac123-157">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="ac123-158">Verifique o ambiente usando a propriedade (por exemplo, `if (Environment.IsDevelopment())` ).</span><span class="sxs-lookup"><span data-stu-id="ac123-158">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

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

## <a name="debug-aspnet-core-apps"></a><span data-ttu-id="ac123-159">Depurar ASP.NET Core aplicativos</span><span class="sxs-lookup"><span data-stu-id="ac123-159">Debug ASP.NET Core apps</span></span>

<span data-ttu-id="ac123-160">Os links a seguir fornecem informações sobre como depurar aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ac123-160">The following links provide information on debugging ASP.NET Core apps.</span></span>

* [<span data-ttu-id="ac123-161">Depuração do ASP Core no Linux</span><span class="sxs-lookup"><span data-stu-id="ac123-161">Debugging ASP Core on Linux</span></span>](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [<span data-ttu-id="ac123-162">Depuração do .NET Core no UNIX por SSH</span><span class="sxs-lookup"><span data-stu-id="ac123-162">Debugging .NET Core on Unix over SSH</span></span>](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [<span data-ttu-id="ac123-163">Início Rápido: depurar o ASP.NET com o depurador do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac123-163">Quickstart: Debug ASP.NET with the Visual Studio debugger</span></span>](/visualstudio/debugger/quickstart-debug-aspnet)
* <span data-ttu-id="ac123-164">Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/2960) para obter mais informações de depuração.</span><span class="sxs-lookup"><span data-stu-id="ac123-164">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/2960) for more debugging information.</span></span>
