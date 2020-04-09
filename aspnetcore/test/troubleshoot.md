---
title: Solução de problemas e depuração ASP.NET projetos core
author: Rick-Anderson
description: Compreenda e solucione problemas de avisos e erros com projetos do ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: test/troubleshoot
ms.openlocfilehash: 345967f08cf99ef5f18d0c9bcd59ab29c74454f1
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511503"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a><span data-ttu-id="d3852-103">Solução de problemas e depuração ASP.NET projetos core</span><span class="sxs-lookup"><span data-stu-id="d3852-103">Troubleshoot and debug ASP.NET Core projects</span></span>

<span data-ttu-id="d3852-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d3852-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d3852-105">Os links a seguir fornecem orientação para solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="d3852-105">The following links provide troubleshooting guidance:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="d3852-106">Conferência NDC (Londres, 2018): Questões de diagnóstico em ASP.NET principais aplicações</span><span class="sxs-lookup"><span data-stu-id="d3852-106">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="d3852-107">ASP.NET Blog: Solução de problemas ASP.NET problemas de desempenho principal</span><span class="sxs-lookup"><span data-stu-id="d3852-107">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="d3852-108">Avisos .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="d3852-108">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="d3852-109">As versões de 32 bits e 64 bits do .NET Core SDK estão instaladas</span><span class="sxs-lookup"><span data-stu-id="d3852-109">Both the 32-bit and 64-bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="d3852-110">Na caixa de diálogo **Novo Projeto** para ASP.NET Core, você pode ver o seguinte aviso:</span><span class="sxs-lookup"><span data-stu-id="d3852-110">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="d3852-111">Ambas as versões de 32 bits e 64 bits do .NET Core SDK estão instaladas.</span><span class="sxs-lookup"><span data-stu-id="d3852-111">Both 32-bit and 64-bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="d3852-112">Somente modelos das versões de 64\\bits\\instaladas\\em\\'C: Arquivos de programa dotnet sdk ' são exibidos.</span><span class="sxs-lookup"><span data-stu-id="d3852-112">Only templates from the 64-bit versions installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="d3852-113">Este aviso aparece quando as versões de 32 bits (x86) e 64 bits (x64) do [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) são instaladas.</span><span class="sxs-lookup"><span data-stu-id="d3852-113">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) are installed.</span></span> <span data-ttu-id="d3852-114">As razões comuns pelas quais ambas as versões podem ser instaladas incluem:</span><span class="sxs-lookup"><span data-stu-id="d3852-114">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="d3852-115">Você originalmente baixou o instalador .NET Core SDK usando uma máquina de 32 bits, mas depois copiou-o e instalou-o em uma máquina de 64 bits.</span><span class="sxs-lookup"><span data-stu-id="d3852-115">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="d3852-116">O SDK .NET Core de 32 bits foi instalado por outro aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3852-116">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="d3852-117">A versão errada foi baixada e instalada.</span><span class="sxs-lookup"><span data-stu-id="d3852-117">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="d3852-118">Desinstale o SDK do Núcleo .NET de 32 bits para evitar esse aviso.</span><span class="sxs-lookup"><span data-stu-id="d3852-118">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="d3852-119">Desinstale a partir de programas e recursos do **painel** > de > controle**Desinstale ou altere um programa**.**Programs and Features**</span><span class="sxs-lookup"><span data-stu-id="d3852-119">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="d3852-120">Se você entender por que o aviso ocorre e suas implicações, você pode ignorar o aviso.</span><span class="sxs-lookup"><span data-stu-id="d3852-120">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="d3852-121">O .NET Core SDK está instalado em vários locais</span><span class="sxs-lookup"><span data-stu-id="d3852-121">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="d3852-122">Na caixa de diálogo **Novo Projeto** para ASP.NET Core, você pode ver o seguinte aviso:</span><span class="sxs-lookup"><span data-stu-id="d3852-122">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="d3852-123">O .NET Core SDK está instalado em vários locais.</span><span class="sxs-lookup"><span data-stu-id="d3852-123">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="d3852-124">Somente modelos dos SDKs instalados\\em\\'C:\\Arquivos\\do programa dotnet sdk ' são exibidos.</span><span class="sxs-lookup"><span data-stu-id="d3852-124">Only templates from the SDKs installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="d3852-125">Você vê esta mensagem quando você tem pelo menos uma instalação do .NET Core SDK em um diretório fora de *C:\\\\Arquivos do programa dotnet\\sdk\\*.</span><span class="sxs-lookup"><span data-stu-id="d3852-125">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="d3852-126">Normalmente isso acontece quando o .NET Core SDK foi implantado em uma máquina usando cópia/colar em vez do instalador MSI.</span><span class="sxs-lookup"><span data-stu-id="d3852-126">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="d3852-127">Desinstale todos os SDKs do Núcleo .NET de 32 bits e tempos de execução para evitar esse aviso.</span><span class="sxs-lookup"><span data-stu-id="d3852-127">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="d3852-128">Desinstale a partir de programas e recursos do **painel** > de > controle**Desinstale ou altere um programa**.**Programs and Features**</span><span class="sxs-lookup"><span data-stu-id="d3852-128">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="d3852-129">Se você entender por que o aviso ocorre e suas implicações, você pode ignorar o aviso.</span><span class="sxs-lookup"><span data-stu-id="d3852-129">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="d3852-130">Não foram detectados SDKs do núcleo .NET</span><span class="sxs-lookup"><span data-stu-id="d3852-130">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="d3852-131">Na caixa de diálogo Visual Studio **New Project** para ASP.NET Core, você pode ver o seguinte aviso:</span><span class="sxs-lookup"><span data-stu-id="d3852-131">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="d3852-132">Não foram detectados SDKs de núcleo .NET, `PATH`certifique-se de que estão incluídos na variável ambiente .</span><span class="sxs-lookup"><span data-stu-id="d3852-132">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="d3852-133">Ao executar `dotnet` um comando, o aviso aparece como:</span><span class="sxs-lookup"><span data-stu-id="d3852-133">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="d3852-134">Não foi possível encontrar nenhum SDK dotnet instalado.</span><span class="sxs-lookup"><span data-stu-id="d3852-134">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="d3852-135">Esses avisos aparecem quando `PATH` a variável ambiente não aponta para nenhum SDK do Núcleo .NET na máquina.</span><span class="sxs-lookup"><span data-stu-id="d3852-135">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="d3852-136">Para resolver esse problema:</span><span class="sxs-lookup"><span data-stu-id="d3852-136">To resolve this problem:</span></span>

* <span data-ttu-id="d3852-137">Instale o SDK do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d3852-137">Install the .NET Core SDK.</span></span> <span data-ttu-id="d3852-138">Obtenha o instalador mais recente a partir de [.NET Downloads](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="d3852-138">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="d3852-139">Verifique se `PATH` a variável ambiente aponta para o local`C:\Program Files\dotnet\` onde o SDK está `C:\Program Files (x86)\dotnet\` instalado ( para 64 bits/x64 ou para 32 bits/x86).</span><span class="sxs-lookup"><span data-stu-id="d3852-139">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="d3852-140">O instalador SDK `PATH`normalmente define o .</span><span class="sxs-lookup"><span data-stu-id="d3852-140">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="d3852-141">Instale sempre os mesmos SDKs de bitness e tempos de execução na mesma máquina.</span><span class="sxs-lookup"><span data-stu-id="d3852-141">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="d3852-142">Faltando SDK depois de instalar o Pacote de hospedagem .NET Core</span><span class="sxs-lookup"><span data-stu-id="d3852-142">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="d3852-143">A instalação do .NET Core `PATH` Hosting Bundle modifica o tempo de [execução](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) do .NET Core para apontar para a`C:\Program Files (x86)\dotnet\`versão de 32 bits (x86) do .NET Core ( ).</span><span class="sxs-lookup"><span data-stu-id="d3852-143">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="d3852-144">Isso pode resultar em SDKs ausentes quando o comando .86 do .NET Core `dotnet` de 32 bits é usado ( Não foram[detectados SDKs de núcleo .NET](#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="d3852-144">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="d3852-145">Para resolver este `C:\Program Files\dotnet\` problema, mova-se para uma posição antes `C:\Program Files (x86)\dotnet\` no `PATH`.</span><span class="sxs-lookup"><span data-stu-id="d3852-145">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="d3852-146">Obter dados de um aplicativo</span><span class="sxs-lookup"><span data-stu-id="d3852-146">Obtain data from an app</span></span>

<span data-ttu-id="d3852-147">Se um aplicativo é capaz de responder às solicitações, você pode obter os seguintes dados do aplicativo usando middleware:</span><span class="sxs-lookup"><span data-stu-id="d3852-147">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="d3852-148">Método &ndash; de solicitação, esquema, host, base de caminho, caminho, seqüência de consulta, cabeçalhos</span><span class="sxs-lookup"><span data-stu-id="d3852-148">Request &ndash; Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="d3852-149">Conexão &ndash; Endereço IP remoto, porta remota, endereço IP local, porta local, certificado do cliente</span><span class="sxs-lookup"><span data-stu-id="d3852-149">Connection &ndash; Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* <span data-ttu-id="d3852-150">Nome &ndash; de identidade, nome de exibição</span><span class="sxs-lookup"><span data-stu-id="d3852-150">Identity &ndash; Name, display name</span></span>
* <span data-ttu-id="d3852-151">Definições de configuração</span><span class="sxs-lookup"><span data-stu-id="d3852-151">Configuration settings</span></span>
* <span data-ttu-id="d3852-152">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="d3852-152">Environment variables</span></span>

<span data-ttu-id="d3852-153">Coloque o seguinte código [de middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) no início do `Startup.Configure` pipeline de processamento de solicitação do método.</span><span class="sxs-lookup"><span data-stu-id="d3852-153">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="d3852-154">O ambiente é verificado antes que o middleware seja executado para garantir que o código seja executado apenas no ambiente Desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="d3852-154">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="d3852-155">Para obter o ambiente, use qualquer uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="d3852-155">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="d3852-156">Injete-o `IHostingEnvironment` no `Startup.Configure` método e verifique o ambiente com a variável local.</span><span class="sxs-lookup"><span data-stu-id="d3852-156">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="d3852-157">O seguinte código de amostra demonstra essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="d3852-157">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="d3852-158">Atribuir o ambiente a uma `Startup` propriedade da classe.</span><span class="sxs-lookup"><span data-stu-id="d3852-158">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="d3852-159">Verifique o ambiente usando a `if (Environment.IsDevelopment())`propriedade (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="d3852-159">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

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

## <a name="debug-aspnet-core-apps"></a><span data-ttu-id="d3852-160">Depurar aplicativos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d3852-160">Debug ASP.NET Core apps</span></span>

<span data-ttu-id="d3852-161">Os links a seguir fornecem informações sobre depuração ASP.NET aplicativos Core.</span><span class="sxs-lookup"><span data-stu-id="d3852-161">The following links provide information on debugging ASP.NET Core apps.</span></span>

* [<span data-ttu-id="d3852-162">Depuração do ASP Core no Linux</span><span class="sxs-lookup"><span data-stu-id="d3852-162">Debugging ASP Core on Linux</span></span>](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [<span data-ttu-id="d3852-163">Depuração do Núcleo .NET no Unix sobre SSH</span><span class="sxs-lookup"><span data-stu-id="d3852-163">Debugging .NET Core on Unix over SSH</span></span>](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [<span data-ttu-id="d3852-164">Início Rápido: depurar o ASP.NET com o depurador do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d3852-164">Quickstart: Debug ASP.NET with the Visual Studio debugger</span></span>](/visualstudio/debugger/quickstart-debug-aspnet)
* <span data-ttu-id="d3852-165">Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/2960) para obter mais informações de depuração.</span><span class="sxs-lookup"><span data-stu-id="d3852-165">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/2960) for more debugging information.</span></span>
