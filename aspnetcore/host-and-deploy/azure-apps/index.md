---
title: Implantar aplicativos ASP.NET Core no Serviço de Aplicativo do Azure
author: bradygaster
description: Este artigo contém links para o host do Azure e para implantar recursos.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: 9ffeebbf8125ddac5d6e621e411c4e86c5bd34b1
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399303"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a><span data-ttu-id="434d0-103">Implantar aplicativos ASP.NET Core no Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="434d0-103">Deploy ASP.NET Core apps to Azure App Service</span></span>

<span data-ttu-id="434d0-104">[Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/) é um [serviço de plataforma de computação em nuvem da Microsoft](https://azure.microsoft.com/) para hospedar aplicativos Web, incluindo o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="434d0-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) is a [Microsoft cloud computing platform service](https://azure.microsoft.com/) for hosting web apps, including ASP.NET Core.</span></span>

## <a name="useful-resources"></a><span data-ttu-id="434d0-105">Recursos úteis</span><span class="sxs-lookup"><span data-stu-id="434d0-105">Useful resources</span></span>

<span data-ttu-id="434d0-106">A [Documentação de Serviço de Aplicativo](/azure/app-service/) do Azure é a página inicial para documentação de Aplicativos Azure, tutoriais, exemplos, guias de instruções e outros recursos.</span><span class="sxs-lookup"><span data-stu-id="434d0-106">[App Service Documentation](/azure/app-service/) is the home for Azure Apps documentation, tutorials, samples, how-to guides, and other resources.</span></span> <span data-ttu-id="434d0-107">Dois tutoriais importantes que pertencem à hospedagem de aplicativos ASP.NET Core são:</span><span class="sxs-lookup"><span data-stu-id="434d0-107">Two notable tutorials that pertain to hosting ASP.NET Core apps are:</span></span>

[<span data-ttu-id="434d0-108">Criar um aplicativo Web ASP.NET Core no Azure</span><span class="sxs-lookup"><span data-stu-id="434d0-108">Create an ASP.NET Core web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)  
<span data-ttu-id="434d0-109">Use o Visual Studio para criar e implantar um aplicativo Web ASP.NET Core no Serviço de Aplicativo do Azure no Windows.</span><span class="sxs-lookup"><span data-stu-id="434d0-109">Use Visual Studio to create and deploy an ASP.NET Core web app to Azure App Service on Windows.</span></span>

[<span data-ttu-id="434d0-110">Criar um aplicativo ASP.NET Core no Serviço de Aplicativo no Linux</span><span class="sxs-lookup"><span data-stu-id="434d0-110">Create an ASP.NET Core app in App Service on Linux</span></span>](/azure/app-service/containers/quickstart-dotnetcore)  
<span data-ttu-id="434d0-111">Use a linha de comando do Visual Studio para criar e implantar um aplicativo Web ASP.NET Core no Serviço de Aplicativo do Azure no Linux.</span><span class="sxs-lookup"><span data-stu-id="434d0-111">Use the command line to create and deploy an ASP.NET Core web app to Azure App Service on Linux.</span></span>

<span data-ttu-id="434d0-112">Consulte o [ASP.NET Core no painel do serviço de aplicativo](https://aspnetcoreon.azurewebsites.net/) para obter a versão do ASP.NET Core disponível no serviço Azure app.</span><span class="sxs-lookup"><span data-stu-id="434d0-112">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

<span data-ttu-id="434d0-113">Assine o repositório de [anúncios do serviço de aplicativo](https://github.com/Azure/app-service-announcements/) e monitore os problemas.</span><span class="sxs-lookup"><span data-stu-id="434d0-113">Subscribe to the [App Service Announcements](https://github.com/Azure/app-service-announcements/) repository and monitor the issues.</span></span> <span data-ttu-id="434d0-114">A equipe do serviço de aplicativo publica regularmente anúncios e cenários chegando ao serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="434d0-114">The App Service team regularly posts announcements and scenarios arriving in App Service.</span></span>

<span data-ttu-id="434d0-115">Os artigos a seguir estão disponíveis na documentação do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="434d0-115">The following articles are available in ASP.NET Core documentation:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>  
<span data-ttu-id="434d0-116">Aprenda como publicar um aplicativo ASP.NET Core no Serviço de Aplicativo do Azure usando o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="434d0-116">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
<span data-ttu-id="434d0-117">Saiba como criar um aplicativo Web ASP.NET Core usando o Visual Studio e implantá-lo no Serviço de Aplicativo do Azure, usando o Git para implantação contínua.</span><span class="sxs-lookup"><span data-stu-id="434d0-117">Learn how to create an ASP.NET Core web app using Visual Studio and deploy it to Azure App Service using Git for continuous deployment.</span></span>

[<span data-ttu-id="434d0-118">Criar seu primeiro pipeline</span><span class="sxs-lookup"><span data-stu-id="434d0-118">Create your first pipeline</span></span>](/azure/devops/pipelines/get-started-yaml)  
<span data-ttu-id="434d0-119">Configurar um build de CI para um aplicativo ASP.NET Core e, em seguida, criar uma versão de implantação contínua para o Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="434d0-119">Set up a CI build for an ASP.NET Core app, then create a continuous deployment release to Azure App Service.</span></span>

[<span data-ttu-id="434d0-120">Área restrita de aplicativo Web do Azure</span><span class="sxs-lookup"><span data-stu-id="434d0-120">Azure Web App sandbox</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
<span data-ttu-id="434d0-121">Descubra as limitações de runtime do Serviço de Aplicativo do Azure impostas pela plataforma de Aplicativos do Azure.</span><span class="sxs-lookup"><span data-stu-id="434d0-121">Discover Azure App Service runtime execution limitations enforced by the Azure Apps platform.</span></span>

<xref:test/troubleshoot>  
<span data-ttu-id="434d0-122">Compreenda e solucione problemas de avisos e erros com projetos do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="434d0-122">Understand and troubleshoot warnings and errors with ASP.NET Core projects.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="434d0-123">Configuração de aplicativo</span><span class="sxs-lookup"><span data-stu-id="434d0-123">Application configuration</span></span>

### <a name="platform"></a><span data-ttu-id="434d0-124">Plataforma</span><span class="sxs-lookup"><span data-stu-id="434d0-124">Platform</span></span>

<span data-ttu-id="434d0-125">A arquitetura da plataforma (x86/x64) de um aplicativo dos serviços de aplicativos é definida nas configurações do aplicativo no portal do Azure para aplicativos que são hospedados em uma camada de hospedagem superior (básica) ou mais alta.</span><span class="sxs-lookup"><span data-stu-id="434d0-125">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span></span> <span data-ttu-id="434d0-126">Confirme se as configurações de publicação do aplicativo (por exemplo, no perfil de publicação do Visual Studio [(. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) correspondem à configuração na configuração de serviço do aplicativo no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="434d0-126">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure Portal.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="434d0-127">Os runtimes para aplicativos de 32 bits (x86) e 64 bits (x64) estão presentes no Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="434d0-127">Runtimes for 64-bit (x64) and 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="434d0-128">O [SDK do .NET Core](/dotnet/core/sdk) disponível no Serviço de Aplicativo é de 32 bits, mas é possível implantar aplicativos de 64 bits compilados localmente usando o console do [Kudu](https://github.com/projectkudu/kudu/wiki) ou o processo de publicação do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="434d0-128">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit, but you can deploy 64-bit apps built locally using the [Kudu](https://github.com/projectkudu/kudu/wiki) console or the publish process in Visual Studio.</span></span> <span data-ttu-id="434d0-129">Para obter mais informações, consulte a seção [Publicar e implantar o aplicativo](#publish-and-deploy-the-app).</span><span class="sxs-lookup"><span data-stu-id="434d0-129">For more information, see the [Publish and deploy the app](#publish-and-deploy-the-app) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="434d0-130">Para aplicativos com dependências nativas, os runtimes para aplicativos de 32 bits (x86) estão presentes no Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="434d0-130">For apps with native dependencies, runtimes for 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="434d0-131">O [SDK do .NET Core](/dotnet/core/sdk) disponível no Serviço de Aplicativo é de 32 bits.</span><span class="sxs-lookup"><span data-stu-id="434d0-131">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit.</span></span>

::: moniker-end

<span data-ttu-id="434d0-132">Para obter mais informações sobre os componentes do .NET Core Framework e os métodos de distribuição, como informações sobre o tempo de execução do .NET Core e o SDK do .NET Core, consulte [sobre o .NET Core: composição](/dotnet/core/about#composition).</span><span class="sxs-lookup"><span data-stu-id="434d0-132">For more information on .NET Core framework components and distribution methods, such as information on the .NET Core runtime and the .NET Core SDK, see [About .NET Core: Composition](/dotnet/core/about#composition).</span></span>

### <a name="packages"></a><span data-ttu-id="434d0-133">Pacotes</span><span class="sxs-lookup"><span data-stu-id="434d0-133">Packages</span></span>

<span data-ttu-id="434d0-134">Incluem os seguintes pacotes NuGet para fornecer recursos de registro automático em log para aplicativos implantados no Serviço de Aplicativo do Azure:</span><span class="sxs-lookup"><span data-stu-id="434d0-134">Include the following NuGet packages to provide automatic logging features for apps deployed to Azure App Service:</span></span>

* <span data-ttu-id="434d0-135">O [Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) usa [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) para fornecer integração leve do ASP.NET Core com o Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="434d0-135">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) uses [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) to provide ASP.NET Core light-up integration with Azure App Service.</span></span> <span data-ttu-id="434d0-136">Os recursos de registro em log adicionais são fornecidos pelo pacote `Microsoft.AspNetCore.AzureAppServicesIntegration`.</span><span class="sxs-lookup"><span data-stu-id="434d0-136">The added logging features are provided by the `Microsoft.AspNetCore.AzureAppServicesIntegration` package.</span></span>
* <span data-ttu-id="434d0-137">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) executa [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) para adicionar provedores de log de diagnósticos do Serviço de Aplicativo do Azure no pacote `Microsoft.Extensions.Logging.AzureAppServices`.</span><span class="sxs-lookup"><span data-stu-id="434d0-137">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) executes [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) to add Azure App Service diagnostics logging providers in the `Microsoft.Extensions.Logging.AzureAppServices` package.</span></span>
* <span data-ttu-id="434d0-138">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) fornece implementações de agente para dar suporte a recursos de streaming de log e logs de diagnóstico do Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="434d0-138">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) provides logger implementations to support Azure App Service diagnostics logs and log streaming features.</span></span>

<span data-ttu-id="434d0-139">Os pacotes anteriores não estão disponíveis no [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="434d0-139">The preceding packages aren't available from the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="434d0-140">Os aplicativos que são direcionados ao .NET Framework ou fazem referência a um metapacote `Microsoft.AspNetCore.App` precisam referenciar explicitamente os pacotes individuais no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="434d0-140">Apps that target .NET Framework or reference the `Microsoft.AspNetCore.App` metapackage must explicitly reference the individual packages in the app's project file.</span></span>

## <a name="override-app-configuration-using-the-azure-portal"></a><span data-ttu-id="434d0-141">Substituir a configuração do aplicativo no Portal do Azure</span><span class="sxs-lookup"><span data-stu-id="434d0-141">Override app configuration using the Azure Portal</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="434d0-142">As configurações do aplicativo no portal do Azure permitem definir variáveis de ambiente para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="434d0-142">App settings in the Azure Portal permit you to set environment variables for the app.</span></span> <span data-ttu-id="434d0-143">As variáveis de ambiente podem ser consumidas pelo [Provedor de configuração de variáveis de ambiente](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="434d0-143">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables).</span></span>

<span data-ttu-id="434d0-144">Quando uma configuração de aplicativo é criada ou modificada no Portal do Azure e o botão **Salvar** é selecionado, o Aplicativo Azure é reiniciado.</span><span class="sxs-lookup"><span data-stu-id="434d0-144">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="434d0-145">A variável de ambiente estará disponível para o aplicativo após o serviço ser reiniciado.</span><span class="sxs-lookup"><span data-stu-id="434d0-145">The environment variable is available to the app after the service restarts.</span></span>

<span data-ttu-id="434d0-146">Quando um aplicativo usa o [host genérico](xref:fundamentals/host/generic-host), as variáveis de ambiente são carregadas na configuração do aplicativo quando <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> é chamado para compilar o host.</span><span class="sxs-lookup"><span data-stu-id="434d0-146">When an app uses the [Generic Host](xref:fundamentals/host/generic-host), environment variables are loaded into the app's configuration when <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> is called to build the host.</span></span> <span data-ttu-id="434d0-147">Para saber mais, confira <xref:fundamentals/host/generic-host> e o [Provedor de configuração de variáveis de ambiente](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="434d0-147">For more information, see <xref:fundamentals/host/generic-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables).</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="434d0-148">As configurações do aplicativo no portal do Azure permitem definir variáveis de ambiente para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="434d0-148">App settings in the Azure Portal permit you to set environment variables for the app.</span></span> <span data-ttu-id="434d0-149">As variáveis de ambiente podem ser consumidas pelo [Provedor de configuração de variáveis de ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="434d0-149">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

<span data-ttu-id="434d0-150">Quando uma configuração de aplicativo é criada ou modificada no Portal do Azure e o botão **Salvar** é selecionado, o Aplicativo Azure é reiniciado.</span><span class="sxs-lookup"><span data-stu-id="434d0-150">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="434d0-151">A variável de ambiente estará disponível para o aplicativo após o serviço ser reiniciado.</span><span class="sxs-lookup"><span data-stu-id="434d0-151">The environment variable is available to the app after the service restarts.</span></span>

<span data-ttu-id="434d0-152">Quando um aplicativo usa o [host da Web](xref:fundamentals/host/web-host), as variáveis de ambiente são carregadas na configuração do aplicativo quando <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> é chamado para compilar o host.</span><span class="sxs-lookup"><span data-stu-id="434d0-152">When an app uses the [Web Host](xref:fundamentals/host/web-host), environment variables are loaded into the app's configuration when <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> is called to build the host.</span></span> <span data-ttu-id="434d0-153">Para saber mais, confira <xref:fundamentals/host/web-host> e o [Provedor de configuração de variáveis de ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="434d0-153">For more information, see <xref:fundamentals/host/web-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="434d0-154">Servidor proxy e cenários de balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="434d0-154">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="434d0-155">O [Middleware de integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), que configura o Middleware de cabeçalhos encaminhados ao hospedar [fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model), e o módulo do ASP.NET Core são configurados para encaminhar o esquema (HTTP/HTTPS) e o endereço IP remoto de onde a solicitação foi originada.</span><span class="sxs-lookup"><span data-stu-id="434d0-155">The [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), which configures Forwarded Headers Middleware when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="434d0-156">Configuração adicional pode ser necessária para aplicativos hospedados atrás de servidores proxy adicionais e balanceadores de carga.</span><span class="sxs-lookup"><span data-stu-id="434d0-156">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="434d0-157">Para obter mais informações, veja [Configurar o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="434d0-157">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="monitoring-and-logging"></a><span data-ttu-id="434d0-158">Monitoramento e registro em log</span><span class="sxs-lookup"><span data-stu-id="434d0-158">Monitoring and logging</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="434d0-159">Os aplicativos ASP.NET Core implantados no Serviço de Aplicativo recebem automaticamente uma extensão do Serviço de Aplicativo, **Integração de Log do ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="434d0-159">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Integration**.</span></span> <span data-ttu-id="434d0-160">A extensão habilita a integração de log para aplicativos ASP.NET Core no Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="434d0-160">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="434d0-161">Os aplicativos ASP.NET Core implantados no Serviço de Aplicativo recebem automaticamente uma extensão do Serviço de Aplicativo, **Extensões de Log do ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="434d0-161">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Extensions**.</span></span> <span data-ttu-id="434d0-162">A extensão habilita a integração de log para aplicativos ASP.NET Core no Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="434d0-162">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

<span data-ttu-id="434d0-163">Para monitoramento, registro em log e informações de solução de problemas, veja os seguintes artigos:</span><span class="sxs-lookup"><span data-stu-id="434d0-163">For monitoring, logging, and troubleshooting information, see the following articles:</span></span>

[<span data-ttu-id="434d0-164">Monitorar aplicativos no Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="434d0-164">Monitor apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)  
<span data-ttu-id="434d0-165">Saiba como examinar as cotas e métricas para aplicativos e planos do Serviço de Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="434d0-165">Learn how to review quotas and metrics for apps and App Service plans.</span></span>

[<span data-ttu-id="434d0-166">Habilitar o log de diagnóstico para aplicativos no serviço Azure App</span><span class="sxs-lookup"><span data-stu-id="434d0-166">Enable diagnostics logging for apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)  
<span data-ttu-id="434d0-167">Descubra como habilitar e acessar o log de diagnósticos para os códigos de status HTTP, solicitações com falha e atividade do servidor Web.</span><span class="sxs-lookup"><span data-stu-id="434d0-167">Discover how to enable and access diagnostic logging for HTTP status codes, failed requests, and web server activity.</span></span>

<xref:fundamentals/error-handling>  
<span data-ttu-id="434d0-168">Entenda as abordagens comuns para o tratamento de erros em aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="434d0-168">Understand common approaches to handling errors in ASP.NET Core apps.</span></span>

<xref:test/troubleshoot-azure-iis>  
<span data-ttu-id="434d0-169">Saiba como diagnosticar problemas com implantações do Serviço de Aplicativo do Azure com aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="434d0-169">Learn how to diagnose issues with Azure App Service deployments with ASP.NET Core apps.</span></span>

<xref:host-and-deploy/azure-iis-errors-reference>  
<span data-ttu-id="434d0-170">Consulte os erros comuns de configuração de implantação para aplicativos hospedados pelo Serviço de Aplicativo do Azure/IIS com orientação para solução de problemas.</span><span class="sxs-lookup"><span data-stu-id="434d0-170">See the common deployment configuration errors for apps hosted by Azure App Service/IIS with troubleshooting advice.</span></span>

## <a name="data-protection-key-ring-and-deployment-slots"></a><span data-ttu-id="434d0-171">Anel de chave de proteção de dados e slots de implantação</span><span class="sxs-lookup"><span data-stu-id="434d0-171">Data Protection key ring and deployment slots</span></span>

<span data-ttu-id="434d0-172">[Chaves de proteção de dados](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) são mantidas na pasta *%HOME%\ASP.NET\DataProtection-Keys*.</span><span class="sxs-lookup"><span data-stu-id="434d0-172">[Data Protection keys](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) are persisted to the *%HOME%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="434d0-173">Essa pasta é apoiada pelo repositório de rede e é sincronizada em todos os computadores que hospedam o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="434d0-173">This folder is backed by network storage and is synchronized across all machines hosting the app.</span></span> <span data-ttu-id="434d0-174">As chaves não são protegidas em repouso.</span><span class="sxs-lookup"><span data-stu-id="434d0-174">Keys aren't protected at rest.</span></span> <span data-ttu-id="434d0-175">Essa pasta fornece o anel de chave para todas as instâncias de um aplicativo em um único slot de implantação.</span><span class="sxs-lookup"><span data-stu-id="434d0-175">This folder supplies the key ring to all instances of an app in a single deployment slot.</span></span> <span data-ttu-id="434d0-176">Slots de implantação separados, como de preparo e produção, não compartilham um anel de chave.</span><span class="sxs-lookup"><span data-stu-id="434d0-176">Separate deployment slots, such as Staging and Production, don't share a key ring.</span></span>

<span data-ttu-id="434d0-177">Quando ocorre a troca entre os slots de implantação, nenhum sistema que usa a proteção de dados consegue descriptografar dados armazenados usando o anel de chave dentro do slot anterior.</span><span class="sxs-lookup"><span data-stu-id="434d0-177">When swapping between deployment slots, any system using data protection won't be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="434d0-178">O middleware de cookie do ASP.NET usa a proteção de dados para proteger seus cookies.</span><span class="sxs-lookup"><span data-stu-id="434d0-178">ASP.NET Cookie Middleware uses data protection to protect its cookies.</span></span> <span data-ttu-id="434d0-179">Com isso, os usuários são desconectados de um aplicativo que usa o Middleware de Cookie padrão do ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="434d0-179">This leads to users being signed out of an app that uses the standard ASP.NET Cookie Middleware.</span></span> <span data-ttu-id="434d0-180">Para uma solução de anel de chave independente de slot, use um provedor de anel de chave externo, como:</span><span class="sxs-lookup"><span data-stu-id="434d0-180">For a slot-independent key ring solution, use an external key ring provider, such as:</span></span>

* <span data-ttu-id="434d0-181">Armazenamento do Blobs do Azure</span><span class="sxs-lookup"><span data-stu-id="434d0-181">Azure Blob Storage</span></span>
* <span data-ttu-id="434d0-182">Cofre de Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="434d0-182">Azure Key Vault</span></span>
* <span data-ttu-id="434d0-183">Repositório SQL</span><span class="sxs-lookup"><span data-stu-id="434d0-183">SQL store</span></span>
* <span data-ttu-id="434d0-184">Cache Redis</span><span class="sxs-lookup"><span data-stu-id="434d0-184">Redis cache</span></span>

<span data-ttu-id="434d0-185">Para obter mais informações, consulte <xref:security/data-protection/implementation/key-storage-providers>.</span><span class="sxs-lookup"><span data-stu-id="434d0-185">For more information, see <xref:security/data-protection/implementation/key-storage-providers>.</span></span>
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>

## <a name="deploy-an-aspnet-core-app-that-uses-a-net-core-preview"></a><span data-ttu-id="434d0-186">Implantar um aplicativo ASP.NET Core que usa uma versão prévia do .NET Core</span><span class="sxs-lookup"><span data-stu-id="434d0-186">Deploy an ASP.NET Core app that uses a .NET Core preview</span></span>

<span data-ttu-id="434d0-187">Para implantar um aplicativo que usa uma versão de visualização do .NET Core, consulte os recursos a seguir.</span><span class="sxs-lookup"><span data-stu-id="434d0-187">To deploy an app that uses a preview release of .NET Core, see the following resources.</span></span> <span data-ttu-id="434d0-188">Essas abordagens também são usadas quando o tempo de execução está disponível, mas o SDK não foi instalado no serviço Azure App.</span><span class="sxs-lookup"><span data-stu-id="434d0-188">These approaches are also used when the runtime is available but the SDK hasn't been installed on Azure App Service.</span></span>

* [<span data-ttu-id="434d0-189">Especifique a versão de SDK do .NET Core usando Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="434d0-189">Specify the .NET Core SDK Version using Azure Pipelines</span></span>](#specify-the-net-core-sdk-version-using-azure-pipelines)
* [<span data-ttu-id="434d0-190">Implantar um aplicativo de visualização independente</span><span class="sxs-lookup"><span data-stu-id="434d0-190">Deploy a self-contained preview app</span></span>](#deploy-a-self-contained-preview-app)
* [<span data-ttu-id="434d0-191">Usar o Docker com aplicativos Web para contêineres</span><span class="sxs-lookup"><span data-stu-id="434d0-191">Use Docker with Web Apps for containers</span></span>](#use-docker-with-web-apps-for-containers)
* [<span data-ttu-id="434d0-192">Instalar a extensão de site da versão prévia</span><span class="sxs-lookup"><span data-stu-id="434d0-192">Install the preview site extension</span></span>](#install-the-preview-site-extension)

<span data-ttu-id="434d0-193">Consulte o [ASP.NET Core no painel do serviço de aplicativo](https://aspnetcoreon.azurewebsites.net/) para obter a versão do ASP.NET Core disponível no serviço Azure app.</span><span class="sxs-lookup"><span data-stu-id="434d0-193">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

### <a name="specify-the-net-core-sdk-version-using-azure-pipelines"></a><span data-ttu-id="434d0-194">Especifique a versão de SDK do .NET Core usando Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="434d0-194">Specify the .NET Core SDK Version using Azure Pipelines</span></span>

<span data-ttu-id="434d0-195">Use [Azure app cenários de CI/CD de serviço](/azure/app-service/deploy-continuous-deployment) para configurar uma compilação de integração contínua com o Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="434d0-195">Use [Azure App Service CI/CD scenarios](/azure/app-service/deploy-continuous-deployment) to set up a continuous integration build with Azure DevOps.</span></span> <span data-ttu-id="434d0-196">Depois que a compilação DevOps do Azure for criada, configure opcionalmente a compilação para usar uma versão específica do SDK.</span><span class="sxs-lookup"><span data-stu-id="434d0-196">After the Azure DevOps build is created, optionally configure the build to use a specific SDK version.</span></span> 

#### <a name="specify-the-net-core-sdk-version"></a><span data-ttu-id="434d0-197">Especificar a versão de SDK do .NET Core</span><span class="sxs-lookup"><span data-stu-id="434d0-197">Specify the .NET Core SDK version</span></span>

<span data-ttu-id="434d0-198">Ao usar o centro de implantação do serviço de aplicativo para criar uma compilação DevOps do Azure, o pipeline de compilação padrão inclui etapas para `Restore` , `Build` , `Test` e `Publish` .</span><span class="sxs-lookup"><span data-stu-id="434d0-198">When using the App Service deployment center to create an Azure DevOps build, the default build pipeline includes steps for `Restore`, `Build`, `Test`, and `Publish`.</span></span> <span data-ttu-id="434d0-199">Para especificar a versão do SDK, selecione o botão **Adicionar (+)** na lista de trabalhos do agente para adicionar uma nova etapa.</span><span class="sxs-lookup"><span data-stu-id="434d0-199">To specify the SDK version, select the **Add (+)** button in the Agent job list to add a new step.</span></span> <span data-ttu-id="434d0-200">Pesquise **SDK do .NET Core** na barra de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="434d0-200">Search for **.NET Core SDK** in the search bar.</span></span> 

![Adicionar a etapa de SDK do .NET Core](index/add-sdk-step.png)

<span data-ttu-id="434d0-202">Mova a etapa para a primeira posição na compilação para que as etapas a seguir usem a versão especificada do SDK do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="434d0-202">Move the step into the first position in the build so that the steps following it use the specified version of the .NET Core SDK.</span></span> <span data-ttu-id="434d0-203">Especifique a versão do SDK do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="434d0-203">Specify the version of the .NET Core SDK.</span></span> <span data-ttu-id="434d0-204">Neste exemplo, o SDK é definido como `3.0.100` .</span><span class="sxs-lookup"><span data-stu-id="434d0-204">In this example, the SDK is set to `3.0.100`.</span></span>

![Etapa do SDK concluída](index/sdk-step-first-place.png)

<span data-ttu-id="434d0-206">Para publicar uma [SCD (implantação autônoma)](/dotnet/core/deploying/#self-contained-deployments-scd), configure o SCD na `Publish` etapa e forneça o [RID (identificador de tempo de execução)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="434d0-206">To publish a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd), configure SCD in the `Publish` step and provide the [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

![Publicação independente](index/self-contained.png)

### <a name="deploy-a-self-contained-preview-app"></a><span data-ttu-id="434d0-208">Implantar um aplicativo autossuficiente em versão prévia</span><span class="sxs-lookup"><span data-stu-id="434d0-208">Deploy a self-contained preview app</span></span>

<span data-ttu-id="434d0-209">Uma [SCD (implantação autocontida)](/dotnet/core/deploying/#self-contained-deployments-scd) voltada para um runtime de versão prévia transporta o runtime da versão prévia na implantação.</span><span class="sxs-lookup"><span data-stu-id="434d0-209">A [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) that targets a preview runtime carries the preview runtime in the deployment.</span></span>

<span data-ttu-id="434d0-210">Ao implantar um aplicativo autocontido:</span><span class="sxs-lookup"><span data-stu-id="434d0-210">When deploying a self-contained app:</span></span>

* <span data-ttu-id="434d0-211">O site no Serviço de Aplicativo do Azure não exige a [extensão de site da versão prévia](#install-the-preview-site-extension).</span><span class="sxs-lookup"><span data-stu-id="434d0-211">The site in Azure App Service doesn't require the [preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="434d0-212">O aplicativo precisa ser publicado após uma abordagem diferente da publicação em uma [FDD (implantação dependente de estrutura)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="434d0-212">The app must be published following a different approach than when publishing for a [framework-dependent deployment (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="434d0-213">Siga as orientações fornecidas na seção [Implantar o aplicativo autossuficiente](#deploy-the-app-self-contained).</span><span class="sxs-lookup"><span data-stu-id="434d0-213">Follow the guidance in the [Deploy the app self-contained](#deploy-the-app-self-contained) section.</span></span>

### <a name="use-docker-with-web-apps-for-containers"></a><span data-ttu-id="434d0-214">Usar o Docker com aplicativos Web para contêineres</span><span class="sxs-lookup"><span data-stu-id="434d0-214">Use Docker with Web Apps for containers</span></span>

<span data-ttu-id="434d0-215">O [Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contém as imagens de versão prévia do Docker mais recentes.</span><span class="sxs-lookup"><span data-stu-id="434d0-215">The [Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contains the latest preview Docker images.</span></span> <span data-ttu-id="434d0-216">As imagens podem ser usadas como uma imagem de base.</span><span class="sxs-lookup"><span data-stu-id="434d0-216">The images can be used as a base image.</span></span> <span data-ttu-id="434d0-217">Use a imagem e implante aplicativos Web para contêineres normalmente.</span><span class="sxs-lookup"><span data-stu-id="434d0-217">Use the image and deploy to Web Apps for Containers normally.</span></span>

### <a name="install-the-preview-site-extension"></a><span data-ttu-id="434d0-218">Instalar a extensão de site de visualização</span><span class="sxs-lookup"><span data-stu-id="434d0-218">Install the preview site extension</span></span>

<span data-ttu-id="434d0-219">Se ocorrer um problema usando a extensão de site de visualização, abra um [problema dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/issues).</span><span class="sxs-lookup"><span data-stu-id="434d0-219">If a problem occurs using the preview site extension, open an [dotnet/AspNetCore issue](https://github.com/dotnet/AspNetCore/issues).</span></span>

1. <span data-ttu-id="434d0-220">No portal do Azure, navegue até o Serviço de Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="434d0-220">From the Azure Portal, navigate to the App Service.</span></span>
1. <span data-ttu-id="434d0-221">Selecione o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="434d0-221">Select the web app.</span></span>
1. <span data-ttu-id="434d0-222">Insira "ex" na caixa de pesquisa para filtrar por "Extensões" ou role para baixo na lista de ferramentas de gerenciamento.</span><span class="sxs-lookup"><span data-stu-id="434d0-222">Type "ex" in the search box to filter for "Extensions" or scroll down the list of management tools.</span></span>
1. <span data-ttu-id="434d0-223">Selecione **extensões**.</span><span class="sxs-lookup"><span data-stu-id="434d0-223">Select **Extensions**.</span></span>
1. <span data-ttu-id="434d0-224">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="434d0-224">Select **Add**.</span></span>
1. <span data-ttu-id="434d0-225">Selecione a extensão **Runtime do ASP.NET Core {X.Y} ({x64|x86})** na lista, em que `{X.Y}` é a versão prévia do ASP.NET Core e `{x64|x86}` especifica a plataforma.</span><span class="sxs-lookup"><span data-stu-id="434d0-225">Select the **ASP.NET Core {X.Y} ({x64|x86}) Runtime** extension from the list, where `{X.Y}` is the ASP.NET Core preview version and `{x64|x86}` specifies the platform.</span></span>
1. <span data-ttu-id="434d0-226">Selecione **OK** para aceitar os termos legais.</span><span class="sxs-lookup"><span data-stu-id="434d0-226">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="434d0-227">Selecione **OK** para instalar a extensão.</span><span class="sxs-lookup"><span data-stu-id="434d0-227">Select **OK** to install the extension.</span></span>

<span data-ttu-id="434d0-228">Quando a operação for concluída, a versão prévia mais recente do .NET Core será instalada.</span><span class="sxs-lookup"><span data-stu-id="434d0-228">When the operation completes, the latest .NET Core preview is installed.</span></span> <span data-ttu-id="434d0-229">Verifique a instalação:</span><span class="sxs-lookup"><span data-stu-id="434d0-229">Verify the installation:</span></span>

1. <span data-ttu-id="434d0-230">Selecione **Ferramentas Avançadas**.</span><span class="sxs-lookup"><span data-stu-id="434d0-230">Select **Advanced Tools**.</span></span>
1. <span data-ttu-id="434d0-231">Selecione **Acessar** em **Ferramentas Avançadas**.</span><span class="sxs-lookup"><span data-stu-id="434d0-231">Select **Go** in **Advanced Tools**.</span></span>
1. <span data-ttu-id="434d0-232">Selecione o item de menu do PowerShell do **console de depuração**  >  **PowerShell** .</span><span class="sxs-lookup"><span data-stu-id="434d0-232">Select the **Debug console** > **PowerShell** menu item.</span></span>
1. <span data-ttu-id="434d0-233">No prompt do PowerShell, execute o seguinte comando.</span><span class="sxs-lookup"><span data-stu-id="434d0-233">At the PowerShell prompt, execute the following command.</span></span> <span data-ttu-id="434d0-234">Substitua a versão do runtime do ASP.NET Core por `{X.Y}` e a plataforma por `{PLATFORM}` no comando:</span><span class="sxs-lookup"><span data-stu-id="434d0-234">Substitute the ASP.NET Core runtime version for `{X.Y}` and the platform for `{PLATFORM}` in the command:</span></span>

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   <span data-ttu-id="434d0-235">O comando retornará `True` quando o runtime da versão prévia x64 estiver instalado.</span><span class="sxs-lookup"><span data-stu-id="434d0-235">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="434d0-236">A arquitetura da plataforma (x86/x64) de um aplicativo dos serviços de aplicativos é definida nas configurações do aplicativo no portal do Azure para aplicativos que são hospedados em uma camada de hospedagem superior (básica) ou mais alta.</span><span class="sxs-lookup"><span data-stu-id="434d0-236">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span></span> <span data-ttu-id="434d0-237">Confirme se as configurações de publicação do aplicativo (por exemplo, no perfil de publicação do Visual Studio [(. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) correspondem à configuração na configuração de serviço do aplicativo no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="434d0-237">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure portal.</span></span>
>
> <span data-ttu-id="434d0-238">Se o aplicativo for executado no modo em processo e a arquitetura da plataforma estiver configurada para 64 bits (x64), o Módulo do ASP.NET Core usará o runtime da versão prévia de 64 bits, se estiver presente.</span><span class="sxs-lookup"><span data-stu-id="434d0-238">If the app is run in in-process mode and the platform architecture is configured for 64-bit (x64), the ASP.NET Core Module uses the 64-bit preview runtime, if present.</span></span> <span data-ttu-id="434d0-239">Instale a extensão de **tempo de execução do ASP.NET Core {X. Y} (x64)** usando o portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="434d0-239">Install the **ASP.NET Core {X.Y} (x64) Runtime** extension using the Azure Portal.</span></span>
>
> <span data-ttu-id="434d0-240">Depois de instalar o tempo de execução x64 Preview, execute o seguinte comando na janela de comando do Azure kudu PowerShell para verificar a instalação.</span><span class="sxs-lookup"><span data-stu-id="434d0-240">After installing the x64 preview runtime, run the following command in the Azure Kudu PowerShell command window to verify the installation.</span></span> <span data-ttu-id="434d0-241">Substitua a versão de tempo de execução ASP.NET Core para `{X.Y}` no seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="434d0-241">Substitute the ASP.NET Core runtime version for `{X.Y}` in the following command:</span></span>
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> <span data-ttu-id="434d0-242">O comando retornará `True` quando o runtime da versão prévia x64 estiver instalado.</span><span class="sxs-lookup"><span data-stu-id="434d0-242">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="434d0-243">As **Extensões do ASP.NET Core** habilitam uma funcionalidade adicional para o ASP.NET Core nos Serviços de Aplicativo do Azure, como a habilitação do registro em log do Azure.</span><span class="sxs-lookup"><span data-stu-id="434d0-243">**ASP.NET Core Extensions** enables additional functionality for ASP.NET Core on Azure App Services, such as enabling Azure logging.</span></span> <span data-ttu-id="434d0-244">A extensão é instalada automaticamente durante a implantação do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="434d0-244">The extension is installed automatically when deploying from Visual Studio.</span></span> <span data-ttu-id="434d0-245">Se a extensão não estiver instalada, instale-a para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="434d0-245">If the extension isn't installed, install it for the app.</span></span>

<span data-ttu-id="434d0-246">**Usar a extensão de site de versão prévia com um modelo do ARM**</span><span class="sxs-lookup"><span data-stu-id="434d0-246">**Use the preview site extension with an ARM template**</span></span>

<span data-ttu-id="434d0-247">Se um modelo do ARM for usado para criar e implantar aplicativos, o tipo de recurso `siteextensions` poderá ser usado para adicionar a extensão de site a um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="434d0-247">If an ARM template is used to create and deploy apps, the `siteextensions` resource type can be used to add the site extension to a web app.</span></span> <span data-ttu-id="434d0-248">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="434d0-248">For example:</span></span>

[!code-json[](index/sample/arm.json?highlight=2)]

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="434d0-249">Publicar e implantar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="434d0-249">Publish and deploy the app</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="434d0-250">Para uma implantação de 64 bits:</span><span class="sxs-lookup"><span data-stu-id="434d0-250">For a 64-bit deployment:</span></span>

* <span data-ttu-id="434d0-251">Use um SDK do .NET Core de 64 bits para compilar um aplicativo de 64 bits.</span><span class="sxs-lookup"><span data-stu-id="434d0-251">Use a 64-bit .NET Core SDK to build a 64-bit app.</span></span>
* <span data-ttu-id="434d0-252">Configure a **Plataforma** como **64 bits** na seção **Configuração** > **Configurações gerais** do Serviço de Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="434d0-252">Set the **Platform** to **64 Bit** in the App Service's **Configuration** > **General settings**.</span></span> <span data-ttu-id="434d0-253">O aplicativo deve usar um plano de serviço Básico ou superior para possibilitar a escolha do número de bits da plataforma.</span><span class="sxs-lookup"><span data-stu-id="434d0-253">The app must use a Basic or higher service plan to enable the choice of platform bitness.</span></span>

::: moniker-end

### <a name="deploy-the-app-framework-dependent"></a><span data-ttu-id="434d0-254">Implantar o aplicativo dependente de estrutura de aplicativos</span><span class="sxs-lookup"><span data-stu-id="434d0-254">Deploy the app framework-dependent</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="434d0-255">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="434d0-255">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="434d0-256">Selecione **Compilar**  >  **publicar {nome do aplicativo}** na barra de ferramentas do Visual Studio ou clique com o botão direito do mouse no projeto em **Gerenciador de soluções** e selecione **publicar**.</span><span class="sxs-lookup"><span data-stu-id="434d0-256">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="434d0-257">Na caixa de diálogo **Escolher um destino de publicação**, confirme se o **Serviço de Aplicativo** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="434d0-257">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="434d0-258">Selecione **Avançado**.</span><span class="sxs-lookup"><span data-stu-id="434d0-258">Select **Advanced**.</span></span> <span data-ttu-id="434d0-259">A caixa de diálogo **Publicar** será aberta.</span><span class="sxs-lookup"><span data-stu-id="434d0-259">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="434d0-260">Na caixa de diálogo **Publicar**:</span><span class="sxs-lookup"><span data-stu-id="434d0-260">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="434d0-261">Confirme se a configuração **Versão** está selecionada.</span><span class="sxs-lookup"><span data-stu-id="434d0-261">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="434d0-262">Abra a lista suspensa **Modo de Implantação** e selecione **Dependente de Estrutura**.</span><span class="sxs-lookup"><span data-stu-id="434d0-262">Open the **Deployment Mode** drop-down list and select **Framework-Dependent**.</span></span>
   * <span data-ttu-id="434d0-263">Selecione **Portátil** como o **Runtime de Destino**.</span><span class="sxs-lookup"><span data-stu-id="434d0-263">Select **Portable** as the **Target Runtime**.</span></span>
   * <span data-ttu-id="434d0-264">Se você precisar remover arquivos adicionais após a implantação, abra as **Opções de Publicação do Arquivo** e marque a caixa de seleção para remover arquivos adicionais no destino.</span><span class="sxs-lookup"><span data-stu-id="434d0-264">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="434d0-265">Selecione **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="434d0-265">Select **Save**.</span></span>
1. <span data-ttu-id="434d0-266">Crie um novo site ou atualize um site existente seguindo as solicitações restantes do assistente de publicação.</span><span class="sxs-lookup"><span data-stu-id="434d0-266">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="434d0-267">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="434d0-267">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="434d0-268">No arquivo de projeto, não especifique um [RID (Identificador de Runtime)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="434d0-268">In the project file, don't specify a [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

1. <span data-ttu-id="434d0-269">Em um shell de comando, publique o aplicativo na Configuração de versão usando o comando [dotnet publish](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="434d0-269">From a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="434d0-270">No exemplo a seguir, o aplicativo é publicado como dependente de estrutura:</span><span class="sxs-lookup"><span data-stu-id="434d0-270">In the following example, the app is published as a framework-dependent app:</span></span>

   ```console
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="434d0-271">Mova o conteúdo do diretório *bin/Release/{TARGET FRAMEWORK}/publish* para o site no Serviço de Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="434d0-271">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="434d0-272">Se for arrastar o conteúdo da pasta *publish* de seu disco rígido local ou do compartilhamento de rede diretamente para o Serviço de Aplicativo no console do [Kudu](https://github.com/projectkudu/kudu/wiki), arraste os arquivos para a pasta `D:\home\site\wwwroot` no console do Kudu.</span><span class="sxs-lookup"><span data-stu-id="434d0-272">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the [Kudu](https://github.com/projectkudu/kudu/wiki) console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

### <a name="deploy-the-app-self-contained"></a><span data-ttu-id="434d0-273">Implantar o aplicativo autocontido</span><span class="sxs-lookup"><span data-stu-id="434d0-273">Deploy the app self-contained</span></span>

<span data-ttu-id="434d0-274">Use o Visual Studio ou o CLI do .NET Core para uma [SCD (implantação autônoma)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="434d0-274">Use Visual Studio or the .NET Core CLI for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="434d0-275">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="434d0-275">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="434d0-276">Selecione **Compilar**  >  **publicar {nome do aplicativo}** na barra de ferramentas do Visual Studio ou clique com o botão direito do mouse no projeto em **Gerenciador de soluções** e selecione **publicar**.</span><span class="sxs-lookup"><span data-stu-id="434d0-276">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="434d0-277">Na caixa de diálogo **Escolher um destino de publicação**, confirme se o **Serviço de Aplicativo** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="434d0-277">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="434d0-278">Selecione **Avançado**.</span><span class="sxs-lookup"><span data-stu-id="434d0-278">Select **Advanced**.</span></span> <span data-ttu-id="434d0-279">A caixa de diálogo **Publicar** será aberta.</span><span class="sxs-lookup"><span data-stu-id="434d0-279">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="434d0-280">Na caixa de diálogo **Publicar**:</span><span class="sxs-lookup"><span data-stu-id="434d0-280">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="434d0-281">Confirme se a configuração **Versão** está selecionada.</span><span class="sxs-lookup"><span data-stu-id="434d0-281">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="434d0-282">Abra a lista suspensa **Modo de Implantação** e selecione **Autocontido**.</span><span class="sxs-lookup"><span data-stu-id="434d0-282">Open the **Deployment Mode** drop-down list and select **Self-Contained**.</span></span>
   * <span data-ttu-id="434d0-283">Selecione o runtime de destino na lista suspensa **Runtime de Destino**.</span><span class="sxs-lookup"><span data-stu-id="434d0-283">Select the target runtime from the **Target Runtime** drop-down list.</span></span> <span data-ttu-id="434d0-284">O padrão é `win-x86`.</span><span class="sxs-lookup"><span data-stu-id="434d0-284">The default is `win-x86`.</span></span>
   * <span data-ttu-id="434d0-285">Se você precisar remover arquivos adicionais após a implantação, abra as **Opções de Publicação do Arquivo** e marque a caixa de seleção para remover arquivos adicionais no destino.</span><span class="sxs-lookup"><span data-stu-id="434d0-285">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="434d0-286">Selecione **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="434d0-286">Select **Save**.</span></span>
1. <span data-ttu-id="434d0-287">Crie um novo site ou atualize um site existente seguindo as solicitações restantes do assistente de publicação.</span><span class="sxs-lookup"><span data-stu-id="434d0-287">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="434d0-288">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="434d0-288">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="434d0-289">No arquivo de projeto, especifique um ou mais [RIDs (identificadores de runtime)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="434d0-289">In the project file, specify one or more [Runtime Identifiers (RIDs)](/dotnet/core/rid-catalog).</span></span> <span data-ttu-id="434d0-290">Use `<RuntimeIdentifier>` (singular) para um único RID ou use `<RuntimeIdentifiers>` (plural) para fornecer uma lista de RIDs delimitada por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="434d0-290">Use `<RuntimeIdentifier>` (singular) for a single RID, or use `<RuntimeIdentifiers>` (plural) to provide a semicolon-delimited list of RIDs.</span></span> <span data-ttu-id="434d0-291">No exemplo a seguir, o RID `win-x86` é especificado:</span><span class="sxs-lookup"><span data-stu-id="434d0-291">In the following example, the `win-x86` RID is specified:</span></span>

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. <span data-ttu-id="434d0-292">Em um shell de comando, publique o aplicativo na configuração de Versão do runtime do host com o comando [dotnet publish](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="434d0-292">From a command shell, publish the app in Release configuration for the host's runtime with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="434d0-293">No exemplo a seguir, o aplicativo é publicado para o RID `win-x86`.</span><span class="sxs-lookup"><span data-stu-id="434d0-293">In the following example, the app is published for the `win-x86` RID.</span></span> <span data-ttu-id="434d0-294">O RID fornecido para a opção `--runtime` precisa ser fornecida na propriedade `<RuntimeIdentifier>` (ou `<RuntimeIdentifiers>`) no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="434d0-294">The RID supplied to the `--runtime` option must be provided in the `<RuntimeIdentifier>` (or `<RuntimeIdentifiers>`) property in the project file.</span></span>

   ```console
   dotnet publish --configuration Release --runtime win-x86 --self-contained
   ```

1. <span data-ttu-id="434d0-295">Mova o conteúdo do diretório *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* para o site no Serviço de Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="434d0-295">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="434d0-296">Se for arrastar o conteúdo da pasta *publish* de seu disco rígido local ou do compartilhamento de rede diretamente para o Serviço de Aplicativo no console do Kudu, arraste os arquivos para a pasta `D:\home\site\wwwroot` no console do Kudu.</span><span class="sxs-lookup"><span data-stu-id="434d0-296">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the Kudu console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

## <a name="protocol-settings-https"></a><span data-ttu-id="434d0-297">Configurações de protocolo (HTTPS)</span><span class="sxs-lookup"><span data-stu-id="434d0-297">Protocol settings (HTTPS)</span></span>

<span data-ttu-id="434d0-298">As associações de protocolo de segurança permitem que você especifique um certificado a ser usado ao responder a solicitações em HTTPS.</span><span class="sxs-lookup"><span data-stu-id="434d0-298">Secure protocol bindings allow you specify a certificate to use when responding to requests over HTTPS.</span></span> <span data-ttu-id="434d0-299">A associação requer um certificado privado válido (*.pfx*) emitido para o nome do host específico.</span><span class="sxs-lookup"><span data-stu-id="434d0-299">Binding requires a valid private certificate (*.pfx*) issued for the specific hostname.</span></span> <span data-ttu-id="434d0-300">Para obter mais informações, consulte [tutorial: associar um certificado SSL personalizado existente ao serviço de Azure app](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="434d0-300">For more information, see [Tutorial: Bind an existing custom SSL certificate to Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

## <a name="transform-webconfig"></a><span data-ttu-id="434d0-301">Transformação do Web.config</span><span class="sxs-lookup"><span data-stu-id="434d0-301">Transform web.config</span></span>

<span data-ttu-id="434d0-302">Se você precisar transformar o *Web.config* em publicação (por exemplo, definir variáveis ​​de ambiente com base na configuração, no perfil ou no ambiente), consulte <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="434d0-302">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="434d0-303">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="434d0-303">Additional resources</span></span>

* [<span data-ttu-id="434d0-304">Visão geral do Serviço de Aplicativo</span><span class="sxs-lookup"><span data-stu-id="434d0-304">App Service overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="434d0-305">Serviço de Aplicativo do Azure: o melhor lugar para hospedar seus aplicativos .NET (vídeo de visão geral com 55 minutos)</span><span class="sxs-lookup"><span data-stu-id="434d0-305">Azure App Service: The Best Place to Host your .NET Apps (55-minute overview video)</span></span>](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [<span data-ttu-id="434d0-306">Azure Friday: experiência de diagnóstico e solução de problemas do Serviço de Aplicativo do Azure (vídeo com 12 minutos)</span><span class="sxs-lookup"><span data-stu-id="434d0-306">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [<span data-ttu-id="434d0-307">Visão geral de diagnóstico do Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="434d0-307">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

<span data-ttu-id="434d0-308">O Serviço de Aplicativo do Azure no Windows Server usa o [IIS (Serviços de Informações da Internet)](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="434d0-308">Azure App Service on Windows Server uses [Internet Information Services (IIS)](https://www.iis.net/).</span></span> <span data-ttu-id="434d0-309">Os tópicos a seguir estão relacionados com a tecnologia subjacente do IIS:</span><span class="sxs-lookup"><span data-stu-id="434d0-309">The following topics pertain to the underlying IIS technology:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [<span data-ttu-id="434d0-310">Windows Server – conteúdo do administrador de IT para as versões atuais e anteriores</span><span class="sxs-lookup"><span data-stu-id="434d0-310">Windows Server - IT administrator content for current and previous releases</span></span>](/windows-server/windows-server-versions)
