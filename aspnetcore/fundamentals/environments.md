---
title: Usar vários ambientes no ASP.NET Core
author: rick-anderson
description: Aprenda a controlar o comportamento do aplicativo em vários ambientes em aplicativos do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/17/2019
uid: fundamentals/environments
ms.openlocfilehash: b0218b2c77c283c0849dca9491046534b88c5a77
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656215"
---
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="ea360-103">Usar vários ambientes no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ea360-103">Use multiple environments in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ea360-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ea360-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ea360-105">O ASP.NET Core configura o comportamento do aplicativo com base no ambiente de runtime usando uma variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="ea360-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="ea360-106">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ea360-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="ea360-107">Ambientes</span><span class="sxs-lookup"><span data-stu-id="ea360-107">Environments</span></span>

<span data-ttu-id="ea360-108">ASP.NET Core lê a `ASPNETCORE_ENVIRONMENT` variável ambiente na inicialização do aplicativo e armazena o valor no [IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName).</span><span class="sxs-lookup"><span data-stu-id="ea360-108">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName).</span></span> <span data-ttu-id="ea360-109">`ASPNETCORE_ENVIRONMENT`pode ser definido como qualquer valor, mas três valores são fornecidos pelo framework:</span><span class="sxs-lookup"><span data-stu-id="ea360-109">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.Extensions.Hosting.Environments.Development>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <span data-ttu-id="ea360-110"><xref:Microsoft.Extensions.Hosting.Environments.Production> (padrão)</span><span class="sxs-lookup"><span data-stu-id="ea360-110"><xref:Microsoft.Extensions.Hosting.Environments.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="ea360-111">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="ea360-111">The preceding code:</span></span>

* <span data-ttu-id="ea360-112">Chama [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) quando `ASPNETCORE_ENVIRONMENT` é definido como `Development`.</span><span class="sxs-lookup"><span data-stu-id="ea360-112">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="ea360-113">Chama [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) quando o valor de `ASPNETCORE_ENVIRONMENT` é definido com um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="ea360-113">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="ea360-114">O [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) `IHostingEnvironment.EnvironmentName` usa o valor de incluir ou excluir marcação no elemento:</span><span class="sxs-lookup"><span data-stu-id="ea360-114">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="ea360-115">No Windows e no macOS, valores e variáveis de ambiente não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ea360-115">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="ea360-116">Valores e variáveis de ambiente do Linux **diferenciam maiúsculas de minúsculas** por padrão.</span><span class="sxs-lookup"><span data-stu-id="ea360-116">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="ea360-117">Desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="ea360-117">Development</span></span>

<span data-ttu-id="ea360-118">O ambiente de desenvolvimento pode habilitar recursos que não devem ser expostos em produção.</span><span class="sxs-lookup"><span data-stu-id="ea360-118">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="ea360-119">Por exemplo, os modelos do ASP.NET Core habilitam a [Página de exceção do desenvolvedor](xref:fundamentals/error-handling#developer-exception-page) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="ea360-119">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="ea360-120">O ambiente de desenvolvimento do computador local pode ser definido no arquivo *Properties\launchSettings.json* do projeto.</span><span class="sxs-lookup"><span data-stu-id="ea360-120">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="ea360-121">Os valores de ambiente definidos em *launchSettings.json* substituem os valores definidos no ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="ea360-121">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="ea360-122">O seguinte JSON mostra três perfis de um arquivo *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="ea360-122">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> <span data-ttu-id="ea360-123">A propriedade `applicationUrl` no *launchSettings.json* pode especificar uma lista de URLs de servidores.</span><span class="sxs-lookup"><span data-stu-id="ea360-123">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="ea360-124">Use um ponto e vírgula entre as URLs na lista:</span><span class="sxs-lookup"><span data-stu-id="ea360-124">Use a semicolon between the URLs in the list:</span></span>
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

<span data-ttu-id="ea360-125">Quando o aplicativo é inicializado com [dotnet run](/dotnet/core/tools/dotnet-run), o primeiro perfil com `"commandName": "Project"` é usado.</span><span class="sxs-lookup"><span data-stu-id="ea360-125">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="ea360-126">O valor de `commandName` especifica o servidor Web a ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="ea360-126">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="ea360-127">`commandName` pode ser qualquer um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="ea360-127">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="ea360-128">`Project` (que inicia o Kestrel)</span><span class="sxs-lookup"><span data-stu-id="ea360-128">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="ea360-129">Quando um aplicativo for iniciado com [dotnet run](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="ea360-129">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="ea360-130">*launchSettings.json* é lido, se está disponível.</span><span class="sxs-lookup"><span data-stu-id="ea360-130">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="ea360-131">As configurações de `environmentVariables` em *launchSettings.json* substituem as variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="ea360-131">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="ea360-132">O ambiente de hospedagem é exibido.</span><span class="sxs-lookup"><span data-stu-id="ea360-132">The hosting environment is displayed.</span></span>

<span data-ttu-id="ea360-133">A saída a seguir mostra um aplicativo iniciado com [dotnet run](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="ea360-133">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="ea360-134">A guia **Depurar** das propriedades do projeto do Visual Studio fornece uma GUI para editar o arquivo *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="ea360-134">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Configurando variáveis de ambiente das propriedades do projeto](environments/_static/project-properties-debug.png)

<span data-ttu-id="ea360-136">As alterações feitas nos perfis do projeto poderão não ter efeito até que o servidor Web seja reiniciado.</span><span class="sxs-lookup"><span data-stu-id="ea360-136">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="ea360-137">O Kestrel precisa ser reiniciado antes de detectar as alterações feitas ao seu ambiente.</span><span class="sxs-lookup"><span data-stu-id="ea360-137">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="ea360-138">*launchSettings.json* não deve armazenar segredos.</span><span class="sxs-lookup"><span data-stu-id="ea360-138">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="ea360-139">A [ferramenta Secret Manager](xref:security/app-secrets) pode ser usado para armazenar segredos de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="ea360-139">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="ea360-140">Ao usar [Visual Studio Code](https://code.visualstudio.com/), variáveis de ambiente podem ser definidas no arquivo *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="ea360-140">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="ea360-141">O exemplo a seguir define o ambiente como `Development`:</span><span class="sxs-lookup"><span data-stu-id="ea360-141">The following example sets the environment to `Development`:</span></span>

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

<span data-ttu-id="ea360-142">Um arquivo *.vscode/launch.json* do projeto não é lido ao iniciar o aplicativo com `dotnet run` da mesma maneira que *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ea360-142">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="ea360-143">Ao inicializar um aplicativo em desenvolvimento que não tem um arquivo *launchSettings.json*, defina o ambiente com uma variável de ambiente ou um argumento de linha de comando para o comando `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="ea360-143">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="ea360-144">Produção</span><span class="sxs-lookup"><span data-stu-id="ea360-144">Production</span></span>

<span data-ttu-id="ea360-145">O ambiente de produção deve ser configurado para maximizar a segurança, o desempenho e a robustez do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ea360-145">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="ea360-146">Algumas configurações comuns que são diferentes do desenvolvimento incluem:</span><span class="sxs-lookup"><span data-stu-id="ea360-146">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="ea360-147">Armazenamento em cache.</span><span class="sxs-lookup"><span data-stu-id="ea360-147">Caching.</span></span>
* <span data-ttu-id="ea360-148">Recursos do lado do cliente são agrupados, minimizados e potencialmente atendidos por meio de uma CDN.</span><span class="sxs-lookup"><span data-stu-id="ea360-148">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="ea360-149">Páginas de erro de diagnóstico desabilitadas.</span><span class="sxs-lookup"><span data-stu-id="ea360-149">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="ea360-150">Páginas de erro amigáveis habilitadas.</span><span class="sxs-lookup"><span data-stu-id="ea360-150">Friendly error pages enabled.</span></span>
* <span data-ttu-id="ea360-151">Log de produção e monitoramento habilitados.</span><span class="sxs-lookup"><span data-stu-id="ea360-151">Production logging and monitoring enabled.</span></span> <span data-ttu-id="ea360-152">Por exemplo, [Insights de aplicativos](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="ea360-152">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="ea360-153">Definir o ambiente</span><span class="sxs-lookup"><span data-stu-id="ea360-153">Set the environment</span></span>

<span data-ttu-id="ea360-154">Muitas vezes é útil definir um ambiente específico para testes com uma variável de ambiente ou configuração de plataforma.</span><span class="sxs-lookup"><span data-stu-id="ea360-154">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="ea360-155">Se o ambiente não for definido, ele usará `Production` como padrão, o que desabilitará a maioria dos recursos de depuração.</span><span class="sxs-lookup"><span data-stu-id="ea360-155">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="ea360-156">O método para configurar o ambiente depende do sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="ea360-156">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="ea360-157">Quando o host é construído, a última configuração do ambiente lida pelo aplicativo determina o ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ea360-157">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="ea360-158">O ambiente do aplicativo não pode ser alterado enquanto o aplicativo estiver em execução.</span><span class="sxs-lookup"><span data-stu-id="ea360-158">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="ea360-159">Configuração de ambiente ou plataforma</span><span class="sxs-lookup"><span data-stu-id="ea360-159">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="ea360-160">Serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="ea360-160">Azure App Service</span></span>

<span data-ttu-id="ea360-161">Para definir o ambiente no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), execute as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="ea360-161">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="ea360-162">Selecione o aplicativo na folha **Serviços de Aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="ea360-162">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="ea360-163">No grupo **Configurações,** selecione a **lâmina Configuração.**</span><span class="sxs-lookup"><span data-stu-id="ea360-163">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="ea360-164">Na guia **Configurações** do aplicativo, selecione **Nova configuração do aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="ea360-164">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="ea360-165">Na janela **de configuração de aplicativo Adicionar/Editar,** forneça `ASPNETCORE_ENVIRONMENT` o **Nome**.</span><span class="sxs-lookup"><span data-stu-id="ea360-165">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="ea360-166">Para **valor,** forneça o ambiente `Staging`(por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="ea360-166">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="ea360-167">Selecione a caixa **de sote de configuração de implantação** se desejar que a configuração do ambiente permaneça com o slot atual quando os slots de implantação forem trocados.</span><span class="sxs-lookup"><span data-stu-id="ea360-167">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="ea360-168">Para obter mais informações, consulte [Configurar ambientes de preparação no Azure App Service](/azure/app-service/web-sites-staged-publishing) na documentação do Azure.</span><span class="sxs-lookup"><span data-stu-id="ea360-168">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="ea360-169">Selecione **OK** para fechar a janela **de configuração do aplicativo Adicionar/Editar.**</span><span class="sxs-lookup"><span data-stu-id="ea360-169">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="ea360-170">Selecione **Salvar** na parte superior da **lâmina de configuração.**</span><span class="sxs-lookup"><span data-stu-id="ea360-170">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="ea360-171">O Serviço de Aplicativo do Azure reinicia automaticamente o aplicativo após uma configuração de aplicativo (variável de ambiente) ser adicionada, alterada ou excluída no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="ea360-171">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="ea360-172">Windows</span><span class="sxs-lookup"><span data-stu-id="ea360-172">Windows</span></span>

<span data-ttu-id="ea360-173">Para definir o `ASPNETCORE_ENVIRONMENT` para a sessão atual quando o aplicativo for iniciado usando [dotnet run](/dotnet/core/tools/dotnet-run), os comandos a seguir serão usados:</span><span class="sxs-lookup"><span data-stu-id="ea360-173">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="ea360-174">**Prompt de comando**</span><span class="sxs-lookup"><span data-stu-id="ea360-174">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="ea360-175">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="ea360-175">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="ea360-176">Esses comandos somente têm efeito para a janela atual.</span><span class="sxs-lookup"><span data-stu-id="ea360-176">These commands only take effect for the current window.</span></span> <span data-ttu-id="ea360-177">Quando a janela é fechada, a configuração `ASPNETCORE_ENVIRONMENT` é revertida para a configuração padrão ou o valor de computador.</span><span class="sxs-lookup"><span data-stu-id="ea360-177">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="ea360-178">Para definir o valor globalmente no Windows, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="ea360-178">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="ea360-179">Abra o **Painel de Controle** > **Sistema** > **Configurações avançadas do sistema** e adicione ou edite o valor `ASPNETCORE_ENVIRONMENT`:</span><span class="sxs-lookup"><span data-stu-id="ea360-179">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Propriedades avançadas do sistema](environments/_static/systemsetting_environment.png)

  ![Variável de ambiente do ASP.NET Core](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="ea360-182">Abra um prompt de comando administrativo e use o comando `setx` ou abra um prompt de comando administrativo do PowerShell e use `[Environment]::SetEnvironmentVariable`:</span><span class="sxs-lookup"><span data-stu-id="ea360-182">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="ea360-183">**Prompt de comando**</span><span class="sxs-lookup"><span data-stu-id="ea360-183">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="ea360-184">O comutador `/M` indica para definir a variável de ambiente no nível do sistema.</span><span class="sxs-lookup"><span data-stu-id="ea360-184">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="ea360-185">Se o comutador `/M` não for usado, a variável de ambiente será definida para a conta de usuário.</span><span class="sxs-lookup"><span data-stu-id="ea360-185">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="ea360-186">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="ea360-186">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="ea360-187">O valor da opção `Machine` indica para definir a variável de ambiente no nível do sistema.</span><span class="sxs-lookup"><span data-stu-id="ea360-187">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="ea360-188">Se o valor da opção for alterado para `User`, a variável de ambiente será definida para a conta de usuário.</span><span class="sxs-lookup"><span data-stu-id="ea360-188">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="ea360-189">Quando a variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida globalmente, ela entra em vigor para `dotnet run` em qualquer janela de comando aberta depois que o valor é definido.</span><span class="sxs-lookup"><span data-stu-id="ea360-189">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="ea360-190">**web.config**</span><span class="sxs-lookup"><span data-stu-id="ea360-190">**web.config**</span></span>

<span data-ttu-id="ea360-191">Para definir a variável de ambiente `ASPNETCORE_ENVIRONMENT` com *web.config*, consulte a seção *Definindo variáveis de ambiente* de <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="ea360-191">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="ea360-192">**Arquivo de projeto ou perfil de publicação**</span><span class="sxs-lookup"><span data-stu-id="ea360-192">**Project file or publish profile**</span></span>

<span data-ttu-id="ea360-193">**Para implantações do Windows IIS:** Inclua `<EnvironmentName>` a propriedade no [perfil de publicação (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) ou arquivo do projeto.</span><span class="sxs-lookup"><span data-stu-id="ea360-193">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="ea360-194">Esta abordagem define o ambiente no arquivo *web.config* quando o projeto é publicado:</span><span class="sxs-lookup"><span data-stu-id="ea360-194">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="ea360-195">**Por pool de aplicativos do IIS**</span><span class="sxs-lookup"><span data-stu-id="ea360-195">**Per IIS Application Pool**</span></span>

<span data-ttu-id="ea360-196">Para definir a variável de ambiente `ASPNETCORE_ENVIRONMENT` para um aplicativo em execução em um Pool de aplicativos isolado (compatível com IIS 10.0 ou posterior), consulte a seção *Comando AppCmd.exe* do tópico [Variáveis de ambiente &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe).</span><span class="sxs-lookup"><span data-stu-id="ea360-196">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="ea360-197">Quando a variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida para um pool de aplicativos, seu valor substitui uma configuração no nível do sistema.</span><span class="sxs-lookup"><span data-stu-id="ea360-197">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ea360-198">Ao hospedar um aplicativo no IIS e adicionar ou alterar a variável de ambiente `ASPNETCORE_ENVIRONMENT`, use qualquer uma das abordagens a seguir para que o novo valor seja escolhido por aplicativos:</span><span class="sxs-lookup"><span data-stu-id="ea360-198">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="ea360-199">Execute `net stop was /y` seguido por `net start w3svc` em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="ea360-199">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="ea360-200">Reinicie o servidor.</span><span class="sxs-lookup"><span data-stu-id="ea360-200">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="ea360-201">macOS</span><span class="sxs-lookup"><span data-stu-id="ea360-201">macOS</span></span>

<span data-ttu-id="ea360-202">A configuração do ambiente atual para macOS pode ser feita em linha ao executar o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="ea360-202">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="ea360-203">Como alternativa, defina o ambiente com `export` antes de executar o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="ea360-203">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="ea360-204">As variáveis de ambiente no nível do computador são definidas no arquivo *.bashrc* ou *.bash_profile*.</span><span class="sxs-lookup"><span data-stu-id="ea360-204">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="ea360-205">Edite o arquivo usando qualquer editor de texto.</span><span class="sxs-lookup"><span data-stu-id="ea360-205">Edit the file using any text editor.</span></span> <span data-ttu-id="ea360-206">Adicione a seguinte instrução:</span><span class="sxs-lookup"><span data-stu-id="ea360-206">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="ea360-207">Linux</span><span class="sxs-lookup"><span data-stu-id="ea360-207">Linux</span></span>

<span data-ttu-id="ea360-208">Para distribuições Linux, use o comando `export` no prompt de comando para as configurações de variável baseadas na sessão e o arquivo *bash_profile* para as configurações de ambiente no nível do computador.</span><span class="sxs-lookup"><span data-stu-id="ea360-208">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="ea360-209">Defina o ambiente em código</span><span class="sxs-lookup"><span data-stu-id="ea360-209">Set the environment in code</span></span>

<span data-ttu-id="ea360-210">Ligue <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> quando construir o hospedeiro.</span><span class="sxs-lookup"><span data-stu-id="ea360-210">Call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="ea360-211">Consulte <xref:fundamentals/host/generic-host#environmentname>.</span><span class="sxs-lookup"><span data-stu-id="ea360-211">See <xref:fundamentals/host/generic-host#environmentname>.</span></span>


### <a name="configuration-by-environment"></a><span data-ttu-id="ea360-212">Configuração por ambiente</span><span class="sxs-lookup"><span data-stu-id="ea360-212">Configuration by environment</span></span>

<span data-ttu-id="ea360-213">Para carregar a configuração por ambiente, recomendamos:</span><span class="sxs-lookup"><span data-stu-id="ea360-213">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="ea360-214">*appsettings* arquivos *(appsettings.{ Ambiente}.json*).</span><span class="sxs-lookup"><span data-stu-id="ea360-214">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="ea360-215">Consulte <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="ea360-215">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="ea360-216">Variáveis de ambiente (definidas em cada sistema onde o aplicativo está hospedado).</span><span class="sxs-lookup"><span data-stu-id="ea360-216">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="ea360-217">Veja <xref:fundamentals/host/generic-host#environmentname> e <xref:security/app-secrets#environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="ea360-217">See <xref:fundamentals/host/generic-host#environmentname> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="ea360-218">Gerenciador de Segredo (somente no ambiente de desenvolvimento).</span><span class="sxs-lookup"><span data-stu-id="ea360-218">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="ea360-219">Consulte <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="ea360-219">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="ea360-220">Métodos e classe Startup baseados no ambiente</span><span class="sxs-lookup"><span data-stu-id="ea360-220">Environment-based Startup class and methods</span></span>

### <a name="inject-iwebhostenvironment-into-startupconfigure"></a><span data-ttu-id="ea360-221">Injete o IWebHostEnvironment em Startup.Configure</span><span class="sxs-lookup"><span data-stu-id="ea360-221">Inject IWebHostEnvironment into Startup.Configure</span></span>

<span data-ttu-id="ea360-222">Injete <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="ea360-222">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="ea360-223">Essa abordagem é útil quando `Startup.Configure` o aplicativo requer apenas ajustes para alguns ambientes com diferenças mínimas de código por ambiente.</span><span class="sxs-lookup"><span data-stu-id="ea360-223">This approach is useful when the app only requires adjusting `Startup.Configure` for a few environments with minimal code differences per environment.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a><span data-ttu-id="ea360-224">Injete iWebHostEnvironment na classe Startup</span><span class="sxs-lookup"><span data-stu-id="ea360-224">Inject IWebHostEnvironment into the Startup class</span></span>

<span data-ttu-id="ea360-225"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> Injete `Startup` na construtora.</span><span class="sxs-lookup"><span data-stu-id="ea360-225">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into the `Startup` constructor.</span></span> <span data-ttu-id="ea360-226">Essa abordagem é útil quando `Startup` o aplicativo requer configuração para apenas alguns ambientes com diferenças mínimas de código por ambiente.</span><span class="sxs-lookup"><span data-stu-id="ea360-226">This approach is useful when the app requires configuring `Startup` for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="ea360-227">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="ea360-227">In the following example:</span></span>

* <span data-ttu-id="ea360-228">O ambiente é `_env` mantido no campo.</span><span class="sxs-lookup"><span data-stu-id="ea360-228">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="ea360-229">`_env`é usado `ConfigureServices` `Configure` e para aplicar a configuração de inicialização com base no ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ea360-229">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

```csharp
public class Startup
{
    private readonly IWebHostEnvironment _env;

    public Startup(IWebHostEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```
### <a name="startup-class-conventions"></a><span data-ttu-id="ea360-230">Convenções da classe Startup</span><span class="sxs-lookup"><span data-stu-id="ea360-230">Startup class conventions</span></span>

<span data-ttu-id="ea360-231">Quando um aplicativo ASP.NET Core é iniciado, a [classe Startup](xref:fundamentals/startup) inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ea360-231">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="ea360-232">O aplicativo pode `Startup` definir classes separadas para `StartupDevelopment`diferentes ambientes (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="ea360-232">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="ea360-233">A `Startup` classe apropriada é selecionada em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="ea360-233">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="ea360-234">A classe cujo sufixo do nome corresponde ao ambiente atual é priorizada.</span><span class="sxs-lookup"><span data-stu-id="ea360-234">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="ea360-235">Se uma classe `Startup{EnvironmentName}` correspondente não for encontrada, a classe `Startup` será usada.</span><span class="sxs-lookup"><span data-stu-id="ea360-235">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="ea360-236">Essa abordagem é útil quando o aplicativo requer a configuração da inicialização para vários ambientes com muitas diferenças de código por ambiente.</span><span class="sxs-lookup"><span data-stu-id="ea360-236">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="ea360-237">Para implementar classes `Startup` com base em ambiente, crie uma classe `Startup{EnvironmentName}` para cada ambiente no uso e classe `Startup` de fallback:</span><span class="sxs-lookup"><span data-stu-id="ea360-237">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}
```

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="ea360-238">Use a sobrecarga [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) que aceita um nome de assembly:</span><span class="sxs-lookup"><span data-stu-id="ea360-238">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a><span data-ttu-id="ea360-239">Convenções do método Startup</span><span class="sxs-lookup"><span data-stu-id="ea360-239">Startup method conventions</span></span>

<span data-ttu-id="ea360-240">[Configurar](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) e [ConfigurarServiços](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) suportam versões `Configure<EnvironmentName>` `Configure<EnvironmentName>Services`específicas do ambiente do formulário e .</span><span class="sxs-lookup"><span data-stu-id="ea360-240">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="ea360-241">Essa abordagem é útil quando o aplicativo requer a configuração da inicialização para vários ambientes com muitas diferenças de código por ambiente.</span><span class="sxs-lookup"><span data-stu-id="ea360-241">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="ea360-242">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ea360-242">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ea360-243">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ea360-243">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ea360-244">O ASP.NET Core configura o comportamento do aplicativo com base no ambiente de runtime usando uma variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="ea360-244">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="ea360-245">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ea360-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="ea360-246">Ambientes</span><span class="sxs-lookup"><span data-stu-id="ea360-246">Environments</span></span>

<span data-ttu-id="ea360-247">O ASP.NET Core lê a variável de ambiente `ASPNETCORE_ENVIRONMENT` na inicialização do aplicativo e armazena o valor em [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span><span class="sxs-lookup"><span data-stu-id="ea360-247">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span></span> <span data-ttu-id="ea360-248">`ASPNETCORE_ENVIRONMENT`pode ser definido como qualquer valor, mas três valores são fornecidos pelo framework:</span><span class="sxs-lookup"><span data-stu-id="ea360-248">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <span data-ttu-id="ea360-249"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (padrão)</span><span class="sxs-lookup"><span data-stu-id="ea360-249"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="ea360-250">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="ea360-250">The preceding code:</span></span>

* <span data-ttu-id="ea360-251">Chama [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) quando `ASPNETCORE_ENVIRONMENT` é definido como `Development`.</span><span class="sxs-lookup"><span data-stu-id="ea360-251">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="ea360-252">Chama [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) quando o valor de `ASPNETCORE_ENVIRONMENT` é definido com um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="ea360-252">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="ea360-253">O [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) `IHostingEnvironment.EnvironmentName` usa o valor de incluir ou excluir marcação no elemento:</span><span class="sxs-lookup"><span data-stu-id="ea360-253">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="ea360-254">No Windows e no macOS, valores e variáveis de ambiente não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ea360-254">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="ea360-255">Valores e variáveis de ambiente do Linux **diferenciam maiúsculas de minúsculas** por padrão.</span><span class="sxs-lookup"><span data-stu-id="ea360-255">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="ea360-256">Desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="ea360-256">Development</span></span>

<span data-ttu-id="ea360-257">O ambiente de desenvolvimento pode habilitar recursos que não devem ser expostos em produção.</span><span class="sxs-lookup"><span data-stu-id="ea360-257">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="ea360-258">Por exemplo, os modelos do ASP.NET Core habilitam a [Página de exceção do desenvolvedor](xref:fundamentals/error-handling#developer-exception-page) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="ea360-258">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="ea360-259">O ambiente de desenvolvimento do computador local pode ser definido no arquivo *Properties\launchSettings.json* do projeto.</span><span class="sxs-lookup"><span data-stu-id="ea360-259">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="ea360-260">Os valores de ambiente definidos em *launchSettings.json* substituem os valores definidos no ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="ea360-260">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="ea360-261">O seguinte JSON mostra três perfis de um arquivo *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="ea360-261">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> <span data-ttu-id="ea360-262">A propriedade `applicationUrl` no *launchSettings.json* pode especificar uma lista de URLs de servidores.</span><span class="sxs-lookup"><span data-stu-id="ea360-262">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="ea360-263">Use um ponto e vírgula entre as URLs na lista:</span><span class="sxs-lookup"><span data-stu-id="ea360-263">Use a semicolon between the URLs in the list:</span></span>
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

<span data-ttu-id="ea360-264">Quando o aplicativo é inicializado com [dotnet run](/dotnet/core/tools/dotnet-run), o primeiro perfil com `"commandName": "Project"` é usado.</span><span class="sxs-lookup"><span data-stu-id="ea360-264">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="ea360-265">O valor de `commandName` especifica o servidor Web a ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="ea360-265">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="ea360-266">`commandName` pode ser qualquer um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="ea360-266">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="ea360-267">`Project` (que inicia o Kestrel)</span><span class="sxs-lookup"><span data-stu-id="ea360-267">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="ea360-268">Quando um aplicativo for iniciado com [dotnet run](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="ea360-268">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="ea360-269">*launchSettings.json* é lido, se está disponível.</span><span class="sxs-lookup"><span data-stu-id="ea360-269">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="ea360-270">As configurações de `environmentVariables` em *launchSettings.json* substituem as variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="ea360-270">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="ea360-271">O ambiente de hospedagem é exibido.</span><span class="sxs-lookup"><span data-stu-id="ea360-271">The hosting environment is displayed.</span></span>

<span data-ttu-id="ea360-272">A saída a seguir mostra um aplicativo iniciado com [dotnet run](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="ea360-272">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="ea360-273">A guia **Depurar** das propriedades do projeto do Visual Studio fornece uma GUI para editar o arquivo *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="ea360-273">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Configurando variáveis de ambiente das propriedades do projeto](environments/_static/project-properties-debug.png)

<span data-ttu-id="ea360-275">As alterações feitas nos perfis do projeto poderão não ter efeito até que o servidor Web seja reiniciado.</span><span class="sxs-lookup"><span data-stu-id="ea360-275">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="ea360-276">O Kestrel precisa ser reiniciado antes de detectar as alterações feitas ao seu ambiente.</span><span class="sxs-lookup"><span data-stu-id="ea360-276">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="ea360-277">*launchSettings.json* não deve armazenar segredos.</span><span class="sxs-lookup"><span data-stu-id="ea360-277">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="ea360-278">A [ferramenta Secret Manager](xref:security/app-secrets) pode ser usado para armazenar segredos de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="ea360-278">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="ea360-279">Ao usar [Visual Studio Code](https://code.visualstudio.com/), variáveis de ambiente podem ser definidas no arquivo *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="ea360-279">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="ea360-280">O exemplo a seguir define o ambiente como `Development`:</span><span class="sxs-lookup"><span data-stu-id="ea360-280">The following example sets the environment to `Development`:</span></span>

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

<span data-ttu-id="ea360-281">Um arquivo *.vscode/launch.json* do projeto não é lido ao iniciar o aplicativo com `dotnet run` da mesma maneira que *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ea360-281">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="ea360-282">Ao inicializar um aplicativo em desenvolvimento que não tem um arquivo *launchSettings.json*, defina o ambiente com uma variável de ambiente ou um argumento de linha de comando para o comando `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="ea360-282">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="ea360-283">Produção</span><span class="sxs-lookup"><span data-stu-id="ea360-283">Production</span></span>

<span data-ttu-id="ea360-284">O ambiente de produção deve ser configurado para maximizar a segurança, o desempenho e a robustez do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ea360-284">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="ea360-285">Algumas configurações comuns que são diferentes do desenvolvimento incluem:</span><span class="sxs-lookup"><span data-stu-id="ea360-285">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="ea360-286">Armazenamento em cache.</span><span class="sxs-lookup"><span data-stu-id="ea360-286">Caching.</span></span>
* <span data-ttu-id="ea360-287">Recursos do lado do cliente são agrupados, minimizados e potencialmente atendidos por meio de uma CDN.</span><span class="sxs-lookup"><span data-stu-id="ea360-287">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="ea360-288">Páginas de erro de diagnóstico desabilitadas.</span><span class="sxs-lookup"><span data-stu-id="ea360-288">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="ea360-289">Páginas de erro amigáveis habilitadas.</span><span class="sxs-lookup"><span data-stu-id="ea360-289">Friendly error pages enabled.</span></span>
* <span data-ttu-id="ea360-290">Log de produção e monitoramento habilitados.</span><span class="sxs-lookup"><span data-stu-id="ea360-290">Production logging and monitoring enabled.</span></span> <span data-ttu-id="ea360-291">Por exemplo, [Insights de aplicativos](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="ea360-291">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="ea360-292">Definir o ambiente</span><span class="sxs-lookup"><span data-stu-id="ea360-292">Set the environment</span></span>

<span data-ttu-id="ea360-293">Muitas vezes é útil definir um ambiente específico para testes com uma variável de ambiente ou configuração de plataforma.</span><span class="sxs-lookup"><span data-stu-id="ea360-293">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="ea360-294">Se o ambiente não for definido, ele usará `Production` como padrão, o que desabilitará a maioria dos recursos de depuração.</span><span class="sxs-lookup"><span data-stu-id="ea360-294">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="ea360-295">O método para configurar o ambiente depende do sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="ea360-295">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="ea360-296">Quando o host é construído, a última configuração do ambiente lida pelo aplicativo determina o ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ea360-296">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="ea360-297">O ambiente do aplicativo não pode ser alterado enquanto o aplicativo estiver em execução.</span><span class="sxs-lookup"><span data-stu-id="ea360-297">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="ea360-298">Configuração de ambiente ou plataforma</span><span class="sxs-lookup"><span data-stu-id="ea360-298">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="ea360-299">Serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="ea360-299">Azure App Service</span></span>

<span data-ttu-id="ea360-300">Para definir o ambiente no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), execute as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="ea360-300">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="ea360-301">Selecione o aplicativo na folha **Serviços de Aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="ea360-301">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="ea360-302">No grupo **Configurações,** selecione a **lâmina Configuração.**</span><span class="sxs-lookup"><span data-stu-id="ea360-302">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="ea360-303">Na guia **Configurações** do aplicativo, selecione **Nova configuração do aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="ea360-303">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="ea360-304">Na janela **de configuração de aplicativo Adicionar/Editar,** forneça `ASPNETCORE_ENVIRONMENT` o **Nome**.</span><span class="sxs-lookup"><span data-stu-id="ea360-304">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="ea360-305">Para **valor,** forneça o ambiente `Staging`(por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="ea360-305">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="ea360-306">Selecione a caixa **de sote de configuração de implantação** se desejar que a configuração do ambiente permaneça com o slot atual quando os slots de implantação forem trocados.</span><span class="sxs-lookup"><span data-stu-id="ea360-306">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="ea360-307">Para obter mais informações, consulte [Configurar ambientes de preparação no Azure App Service](/azure/app-service/web-sites-staged-publishing) na documentação do Azure.</span><span class="sxs-lookup"><span data-stu-id="ea360-307">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="ea360-308">Selecione **OK** para fechar a janela **de configuração do aplicativo Adicionar/Editar.**</span><span class="sxs-lookup"><span data-stu-id="ea360-308">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="ea360-309">Selecione **Salvar** na parte superior da **lâmina de configuração.**</span><span class="sxs-lookup"><span data-stu-id="ea360-309">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="ea360-310">O Serviço de Aplicativo do Azure reinicia automaticamente o aplicativo após uma configuração de aplicativo (variável de ambiente) ser adicionada, alterada ou excluída no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="ea360-310">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="ea360-311">Windows</span><span class="sxs-lookup"><span data-stu-id="ea360-311">Windows</span></span>

<span data-ttu-id="ea360-312">Para definir o `ASPNETCORE_ENVIRONMENT` para a sessão atual quando o aplicativo for iniciado usando [dotnet run](/dotnet/core/tools/dotnet-run), os comandos a seguir serão usados:</span><span class="sxs-lookup"><span data-stu-id="ea360-312">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="ea360-313">**Prompt de comando**</span><span class="sxs-lookup"><span data-stu-id="ea360-313">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="ea360-314">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="ea360-314">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="ea360-315">Esses comandos somente têm efeito para a janela atual.</span><span class="sxs-lookup"><span data-stu-id="ea360-315">These commands only take effect for the current window.</span></span> <span data-ttu-id="ea360-316">Quando a janela é fechada, a configuração `ASPNETCORE_ENVIRONMENT` é revertida para a configuração padrão ou o valor de computador.</span><span class="sxs-lookup"><span data-stu-id="ea360-316">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="ea360-317">Para definir o valor globalmente no Windows, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="ea360-317">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="ea360-318">Abra o **Painel de Controle** > **Sistema** > **Configurações avançadas do sistema** e adicione ou edite o valor `ASPNETCORE_ENVIRONMENT`:</span><span class="sxs-lookup"><span data-stu-id="ea360-318">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Propriedades avançadas do sistema](environments/_static/systemsetting_environment.png)

  ![Variável de ambiente do ASP.NET Core](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="ea360-321">Abra um prompt de comando administrativo e use o comando `setx` ou abra um prompt de comando administrativo do PowerShell e use `[Environment]::SetEnvironmentVariable`:</span><span class="sxs-lookup"><span data-stu-id="ea360-321">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="ea360-322">**Prompt de comando**</span><span class="sxs-lookup"><span data-stu-id="ea360-322">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="ea360-323">O comutador `/M` indica para definir a variável de ambiente no nível do sistema.</span><span class="sxs-lookup"><span data-stu-id="ea360-323">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="ea360-324">Se o comutador `/M` não for usado, a variável de ambiente será definida para a conta de usuário.</span><span class="sxs-lookup"><span data-stu-id="ea360-324">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="ea360-325">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="ea360-325">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="ea360-326">O valor da opção `Machine` indica para definir a variável de ambiente no nível do sistema.</span><span class="sxs-lookup"><span data-stu-id="ea360-326">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="ea360-327">Se o valor da opção for alterado para `User`, a variável de ambiente será definida para a conta de usuário.</span><span class="sxs-lookup"><span data-stu-id="ea360-327">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="ea360-328">Quando a variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida globalmente, ela entra em vigor para `dotnet run` em qualquer janela de comando aberta depois que o valor é definido.</span><span class="sxs-lookup"><span data-stu-id="ea360-328">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="ea360-329">**web.config**</span><span class="sxs-lookup"><span data-stu-id="ea360-329">**web.config**</span></span>

<span data-ttu-id="ea360-330">Para definir a variável de ambiente `ASPNETCORE_ENVIRONMENT` com *web.config*, consulte a seção *Definindo variáveis de ambiente* de <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="ea360-330">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="ea360-331">**Arquivo de projeto ou perfil de publicação**</span><span class="sxs-lookup"><span data-stu-id="ea360-331">**Project file or publish profile**</span></span>

<span data-ttu-id="ea360-332">**Para implantações do Windows IIS:** Inclua `<EnvironmentName>` a propriedade no [perfil de publicação (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) ou arquivo do projeto.</span><span class="sxs-lookup"><span data-stu-id="ea360-332">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="ea360-333">Esta abordagem define o ambiente no arquivo *web.config* quando o projeto é publicado:</span><span class="sxs-lookup"><span data-stu-id="ea360-333">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="ea360-334">**Por pool de aplicativos do IIS**</span><span class="sxs-lookup"><span data-stu-id="ea360-334">**Per IIS Application Pool**</span></span>

<span data-ttu-id="ea360-335">Para definir a variável de ambiente `ASPNETCORE_ENVIRONMENT` para um aplicativo em execução em um Pool de aplicativos isolado (compatível com IIS 10.0 ou posterior), consulte a seção *Comando AppCmd.exe* do tópico [Variáveis de ambiente &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe).</span><span class="sxs-lookup"><span data-stu-id="ea360-335">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="ea360-336">Quando a variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida para um pool de aplicativos, seu valor substitui uma configuração no nível do sistema.</span><span class="sxs-lookup"><span data-stu-id="ea360-336">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ea360-337">Ao hospedar um aplicativo no IIS e adicionar ou alterar a variável de ambiente `ASPNETCORE_ENVIRONMENT`, use qualquer uma das abordagens a seguir para que o novo valor seja escolhido por aplicativos:</span><span class="sxs-lookup"><span data-stu-id="ea360-337">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="ea360-338">Execute `net stop was /y` seguido por `net start w3svc` em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="ea360-338">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="ea360-339">Reinicie o servidor.</span><span class="sxs-lookup"><span data-stu-id="ea360-339">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="ea360-340">macOS</span><span class="sxs-lookup"><span data-stu-id="ea360-340">macOS</span></span>

<span data-ttu-id="ea360-341">A configuração do ambiente atual para macOS pode ser feita em linha ao executar o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="ea360-341">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="ea360-342">Como alternativa, defina o ambiente com `export` antes de executar o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="ea360-342">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="ea360-343">As variáveis de ambiente no nível do computador são definidas no arquivo *.bashrc* ou *.bash_profile*.</span><span class="sxs-lookup"><span data-stu-id="ea360-343">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="ea360-344">Edite o arquivo usando qualquer editor de texto.</span><span class="sxs-lookup"><span data-stu-id="ea360-344">Edit the file using any text editor.</span></span> <span data-ttu-id="ea360-345">Adicione a seguinte instrução:</span><span class="sxs-lookup"><span data-stu-id="ea360-345">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="ea360-346">Linux</span><span class="sxs-lookup"><span data-stu-id="ea360-346">Linux</span></span>

<span data-ttu-id="ea360-347">Para distribuições Linux, use o comando `export` no prompt de comando para as configurações de variável baseadas na sessão e o arquivo *bash_profile* para as configurações de ambiente no nível do computador.</span><span class="sxs-lookup"><span data-stu-id="ea360-347">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="ea360-348">Defina o ambiente em código</span><span class="sxs-lookup"><span data-stu-id="ea360-348">Set the environment in code</span></span>

<span data-ttu-id="ea360-349">Ligue <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> quando construir o hospedeiro.</span><span class="sxs-lookup"><span data-stu-id="ea360-349">Call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="ea360-350">Consulte <xref:fundamentals/host/web-host#environment>.</span><span class="sxs-lookup"><span data-stu-id="ea360-350">See <xref:fundamentals/host/web-host#environment>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="ea360-351">Configuração por ambiente</span><span class="sxs-lookup"><span data-stu-id="ea360-351">Configuration by environment</span></span>

<span data-ttu-id="ea360-352">Para carregar a configuração por ambiente, recomendamos:</span><span class="sxs-lookup"><span data-stu-id="ea360-352">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="ea360-353">*appsettings* arquivos *(appsettings.{ Ambiente}.json*).</span><span class="sxs-lookup"><span data-stu-id="ea360-353">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="ea360-354">Consulte <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="ea360-354">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="ea360-355">Variáveis de ambiente (definidas em cada sistema onde o aplicativo está hospedado).</span><span class="sxs-lookup"><span data-stu-id="ea360-355">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="ea360-356">Veja <xref:fundamentals/host/web-host#environment> e <xref:security/app-secrets#environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="ea360-356">See <xref:fundamentals/host/web-host#environment> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="ea360-357">Gerenciador de Segredo (somente no ambiente de desenvolvimento).</span><span class="sxs-lookup"><span data-stu-id="ea360-357">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="ea360-358">Consulte <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="ea360-358">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="ea360-359">Métodos e classe Startup baseados no ambiente</span><span class="sxs-lookup"><span data-stu-id="ea360-359">Environment-based Startup class and methods</span></span>

### <a name="inject-ihostingenvironment-into-startupconfigure"></a><span data-ttu-id="ea360-360">Injete o IHostingEnvironment na inicialização.Configure</span><span class="sxs-lookup"><span data-stu-id="ea360-360">Inject IHostingEnvironment into Startup.Configure</span></span>

<span data-ttu-id="ea360-361">Injete <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="ea360-361">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="ea360-362">Essa abordagem é útil quando `Startup.Configure` o aplicativo requer apenas a configuração para apenas alguns ambientes com diferenças mínimas de código por ambiente.</span><span class="sxs-lookup"><span data-stu-id="ea360-362">This approach is useful when the app only requires configuring `Startup.Configure` for only a few environments with minimal code differences per environment.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-ihostingenvironment-into-the-startup-class"></a><span data-ttu-id="ea360-363">Injete iHostingEnvironment na classe Startup</span><span class="sxs-lookup"><span data-stu-id="ea360-363">Inject IHostingEnvironment into the Startup class</span></span>

<span data-ttu-id="ea360-364"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> Injete `Startup` no construtor e atribua o serviço `Startup` a um campo para uso em toda a classe.</span><span class="sxs-lookup"><span data-stu-id="ea360-364">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into the `Startup` constructor and assign the service to a field for use throughout the `Startup` class.</span></span> <span data-ttu-id="ea360-365">Essa abordagem é útil quando o aplicativo requer a configuração da inicialização para apenas alguns ambientes com diferenças mínimas de código por ambiente.</span><span class="sxs-lookup"><span data-stu-id="ea360-365">This approach is useful when the app requires configuring startup for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="ea360-366">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="ea360-366">In the following example:</span></span>

* <span data-ttu-id="ea360-367">O ambiente é `_env` mantido no campo.</span><span class="sxs-lookup"><span data-stu-id="ea360-367">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="ea360-368">`_env`é usado `ConfigureServices` `Configure` e para aplicar a configuração de inicialização com base no ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ea360-368">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

```csharp
public class Startup
{
    private readonly IHostingEnvironment _env;

    public Startup(IHostingEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```

### <a name="startup-class-conventions"></a><span data-ttu-id="ea360-369">Convenções da classe Startup</span><span class="sxs-lookup"><span data-stu-id="ea360-369">Startup class conventions</span></span>

<span data-ttu-id="ea360-370">Quando um aplicativo ASP.NET Core é iniciado, a [classe Startup](xref:fundamentals/startup) inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ea360-370">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="ea360-371">O aplicativo pode `Startup` definir classes separadas para `StartupDevelopment`diferentes ambientes (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="ea360-371">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="ea360-372">A `Startup` classe apropriada é selecionada em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="ea360-372">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="ea360-373">A classe cujo sufixo do nome corresponde ao ambiente atual é priorizada.</span><span class="sxs-lookup"><span data-stu-id="ea360-373">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="ea360-374">Se uma classe `Startup{EnvironmentName}` correspondente não for encontrada, a classe `Startup` será usada.</span><span class="sxs-lookup"><span data-stu-id="ea360-374">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="ea360-375">Essa abordagem é útil quando o aplicativo requer a configuração da inicialização para vários ambientes com muitas diferenças de código por ambiente.</span><span class="sxs-lookup"><span data-stu-id="ea360-375">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="ea360-376">Para implementar classes `Startup` com base em ambiente, crie uma classe `Startup{EnvironmentName}` para cada ambiente no uso e classe `Startup` de fallback:</span><span class="sxs-lookup"><span data-stu-id="ea360-376">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}
```

<span data-ttu-id="ea360-377">Use a sobrecarga [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) que aceita um nome de assembly:</span><span class="sxs-lookup"><span data-stu-id="ea360-377">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a><span data-ttu-id="ea360-378">Convenções do método Startup</span><span class="sxs-lookup"><span data-stu-id="ea360-378">Startup method conventions</span></span>

<span data-ttu-id="ea360-379">[Configurar](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) e [ConfigurarServiços](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) suportam versões `Configure<EnvironmentName>` `Configure<EnvironmentName>Services`específicas do ambiente do formulário e .</span><span class="sxs-lookup"><span data-stu-id="ea360-379">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="ea360-380">Essa abordagem é útil quando o aplicativo requer a configuração da inicialização para vários ambientes com muitas diferenças de código por ambiente.</span><span class="sxs-lookup"><span data-stu-id="ea360-380">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="ea360-381">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ea360-381">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
