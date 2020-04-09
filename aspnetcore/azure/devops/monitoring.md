---
title: Monitor e depuração - DevOps com ASP.NET Core e Azure
author: CamSoper
description: Monitoramento e depuração do seu código como parte de uma solução DevOps com ASP.NET Core e Azure
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 07/10/2019
uid: azure/devops/monitor
ms.openlocfilehash: 1d8ed99f4387dbc99929164c558cc2ce14bd9ea0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659498"
---
# <a name="monitor-and-debug"></a><span data-ttu-id="befcd-103">Monitor e depuração</span><span class="sxs-lookup"><span data-stu-id="befcd-103">Monitor and debug</span></span>

<span data-ttu-id="befcd-104">Tendo implantado o aplicativo e construído um pipeline DevOps, é importante entender como monitorar e solucionar problemas do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="befcd-104">Having deployed the app and built a DevOps pipeline, it's important to understand how to monitor and troubleshoot the app.</span></span>

<span data-ttu-id="befcd-105">Nesta seção, você completará as seguintes tarefas:</span><span class="sxs-lookup"><span data-stu-id="befcd-105">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="befcd-106">Encontre dados básicos de monitoramento e solução de problemas no portal Azure</span><span class="sxs-lookup"><span data-stu-id="befcd-106">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="befcd-107">Saiba como o Azure Monitor fornece um olhar mais profundo sobre métricas em todos os serviços do Azure</span><span class="sxs-lookup"><span data-stu-id="befcd-107">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="befcd-108">Conecte o aplicativo web com insights de aplicativos para criação de perfil de aplicativos</span><span class="sxs-lookup"><span data-stu-id="befcd-108">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="befcd-109">Ative o registro e saiba onde baixar logs</span><span class="sxs-lookup"><span data-stu-id="befcd-109">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="befcd-110">Stream logs em tempo real</span><span class="sxs-lookup"><span data-stu-id="befcd-110">Stream logs in real time</span></span>
* <span data-ttu-id="befcd-111">Saiba onde configurar alertas</span><span class="sxs-lookup"><span data-stu-id="befcd-111">Learn where to set up alerts</span></span>
* <span data-ttu-id="befcd-112">Saiba mais sobre a depuração remota de aplicativos web do Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="befcd-112">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="basic-monitoring-and-troubleshooting"></a><span data-ttu-id="befcd-113">Monitoramento básico e solução de problemas</span><span class="sxs-lookup"><span data-stu-id="befcd-113">Basic monitoring and troubleshooting</span></span>

<span data-ttu-id="befcd-114">Os aplicativos web App Service são facilmente monitorados em tempo real.</span><span class="sxs-lookup"><span data-stu-id="befcd-114">App Service web apps are easily monitored in real time.</span></span> <span data-ttu-id="befcd-115">O portal Azure renderiza métricas em gráficos e gráficos fáceis de entender.</span><span class="sxs-lookup"><span data-stu-id="befcd-115">The Azure portal renders metrics in easy-to-understand charts and graphs.</span></span>

1. <span data-ttu-id="befcd-116">Abra o [portal Azure](https://portal.azure.com)e navegue até o \*\<mywebapp unique_number\> \* App Service.</span><span class="sxs-lookup"><span data-stu-id="befcd-116">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>

1. <span data-ttu-id="befcd-117">**A guia Visão Geral** exibe informações úteis "à vista", incluindo gráficos que exibem métricas recentes.</span><span class="sxs-lookup"><span data-stu-id="befcd-117">The **Overview** tab displays useful "at-a-glance" information, including graphs displaying recent metrics.</span></span>

    ![Captura de tela mostrando painel de visão geral](./media/monitoring/overview.png)

    * <span data-ttu-id="befcd-119">**Http 5xx**: Contagem de erros do lado do servidor, geralmente exceções em ASP.NET código Core.</span><span class="sxs-lookup"><span data-stu-id="befcd-119">**Http 5xx**: Count of server-side errors, usually exceptions in ASP.NET Core code.</span></span>
    * <span data-ttu-id="befcd-120">**Data In**: Entrada de dados entrando em seu aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="befcd-120">**Data In**: Data ingress coming into your web app.</span></span>
    * <span data-ttu-id="befcd-121">**Data Out**: Saída de dados do seu aplicativo web para clientes.</span><span class="sxs-lookup"><span data-stu-id="befcd-121">**Data Out**: Data egress from your web app to clients.</span></span>
    * <span data-ttu-id="befcd-122">**Solicitações**: Contagem de solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="befcd-122">**Requests**: Count of HTTP requests.</span></span>
    * <span data-ttu-id="befcd-123">**Tempo médio de resposta**: Tempo médio para que o aplicativo web responda às solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="befcd-123">**Average Response Time**: Average time for the web app to respond to HTTP requests.</span></span>

    <span data-ttu-id="befcd-124">Várias ferramentas de autoatendimento para solução de problemas e otimização também são encontradas nesta página.</span><span class="sxs-lookup"><span data-stu-id="befcd-124">Several self-service tools for troubleshooting and optimization are also found on this page.</span></span>

    ![Captura de tela mostrando ferramentas de autoatendimento](./media/monitoring/wizards.png)

    * <span data-ttu-id="befcd-126">**Diagnosticar e resolver problemas** é um problema de autoatendimento.</span><span class="sxs-lookup"><span data-stu-id="befcd-126">**Diagnose and solve problems** is a self-service troubleshooter.</span></span>
    * <span data-ttu-id="befcd-127">**O Application Insights** é para o desempenho de criação de perfil e comportamento do aplicativo, e é discutido mais tarde nesta seção.</span><span class="sxs-lookup"><span data-stu-id="befcd-127">**Application Insights** is for profiling performance and app behavior, and is discussed later in this section.</span></span>
    * <span data-ttu-id="befcd-128">**O App Service Advisor** faz recomendações para ajustar a experiência do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="befcd-128">**App Service Advisor** makes recommendations to tune your app experience.</span></span>

## <a name="advanced-monitoring"></a><span data-ttu-id="befcd-129">Monitoramento avançado</span><span class="sxs-lookup"><span data-stu-id="befcd-129">Advanced monitoring</span></span>

<span data-ttu-id="befcd-130">[O Azure Monitor](/azure/monitoring-and-diagnostics/) é o serviço centralizado para monitorar todas as métricas e definir alertas em todos os serviços do Azure.</span><span class="sxs-lookup"><span data-stu-id="befcd-130">[Azure Monitor](/azure/monitoring-and-diagnostics/) is the centralized service for monitoring all metrics and setting alerts across Azure services.</span></span> <span data-ttu-id="befcd-131">No Azure Monitor, os administradores podem acompanhar granularmente o desempenho e identificar tendências.</span><span class="sxs-lookup"><span data-stu-id="befcd-131">Within Azure Monitor, administrators can granularly track performance and identify trends.</span></span> <span data-ttu-id="befcd-132">Cada serviço do Azure oferece seu próprio [conjunto de métricas](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) ao Azure Monitor.</span><span class="sxs-lookup"><span data-stu-id="befcd-132">Each Azure service offers its own [set of metrics](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) to Azure Monitor.</span></span>

## <a name="profile-with-application-insights"></a><span data-ttu-id="befcd-133">Perfil com Insights de Aplicativos</span><span class="sxs-lookup"><span data-stu-id="befcd-133">Profile with Application Insights</span></span>

<span data-ttu-id="befcd-134">[O Application Insights](/azure/application-insights/app-insights-overview) é um serviço do Azure para analisar o desempenho e a estabilidade dos aplicativos web e como os usuários os usam.</span><span class="sxs-lookup"><span data-stu-id="befcd-134">[Application Insights](/azure/application-insights/app-insights-overview) is an Azure service for analyzing the performance and stability of web apps and how users use them.</span></span> <span data-ttu-id="befcd-135">Os dados do Application Insights são mais amplos e profundos do que os do Azure Monitor.</span><span class="sxs-lookup"><span data-stu-id="befcd-135">The data from Application Insights is broader and deeper than that of Azure Monitor.</span></span> <span data-ttu-id="befcd-136">Os dados podem fornecer aos desenvolvedores e administradores informações-chave para melhorar os aplicativos.</span><span class="sxs-lookup"><span data-stu-id="befcd-136">The data can provide developers and administrators with key information for improving apps.</span></span> <span data-ttu-id="befcd-137">Os Insights do aplicativo podem ser adicionados a um recurso do Azure App Service sem alterações de código.</span><span class="sxs-lookup"><span data-stu-id="befcd-137">Application Insights can be added to an Azure App Service resource without code changes.</span></span>

1. <span data-ttu-id="befcd-138">Abra o [portal Azure](https://portal.azure.com)e navegue até o \*\<mywebapp unique_number\> \* App Service.</span><span class="sxs-lookup"><span data-stu-id="befcd-138">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="befcd-139">Na **guia Visão geral,** clique no bloco **'Insights do** aplicativo'.</span><span class="sxs-lookup"><span data-stu-id="befcd-139">From the **Overview** tab, click the **Application Insights** tile.</span></span>

    ![Bloco do Application Insights](./media/monitoring/app-insights.png)

1. <span data-ttu-id="befcd-141">Selecione o **botão Criar novo recurso de** rádio.</span><span class="sxs-lookup"><span data-stu-id="befcd-141">Select the **Create new resource** radio button.</span></span> <span data-ttu-id="befcd-142">Use o nome do recurso padrão e selecione o local para o recurso Application Insights.</span><span class="sxs-lookup"><span data-stu-id="befcd-142">Use the default resource name, and select the location for the Application Insights resource.</span></span> <span data-ttu-id="befcd-143">O local não precisa coincidir com o do seu aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="befcd-143">The location doesn't need to match that of your web app.</span></span>

    ![Configuração do Application Insights](./media/monitoring/new-app-insights.png)

1. <span data-ttu-id="befcd-145">Em **Tempo de execução/Framework,** selecione **ASP.NET Núcleo**.</span><span class="sxs-lookup"><span data-stu-id="befcd-145">For **Runtime/Framework**, select **ASP.NET Core**.</span></span> <span data-ttu-id="befcd-146">Aceite as configurações padrão.</span><span class="sxs-lookup"><span data-stu-id="befcd-146">Accept the default settings.</span></span>
1. <span data-ttu-id="befcd-147">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="befcd-147">Select **OK**.</span></span> <span data-ttu-id="befcd-148">Se solicitado a confirmar, selecione **Continuar**.</span><span class="sxs-lookup"><span data-stu-id="befcd-148">If prompted to confirm, select **Continue**.</span></span>
1. <span data-ttu-id="befcd-149">Depois que o recurso tiver sido criado, clique no nome do recurso Application Insights para navegar diretamente na página Do aplicativo Insights.</span><span class="sxs-lookup"><span data-stu-id="befcd-149">After the resource has been created, click the name of Application Insights resource to navigate directly to the Application Insights page.</span></span>

    ![Novo recurso do Application Insights está pronto](./media/monitoring/new-app-insights-done.png)

<span data-ttu-id="befcd-151">À medida que o aplicativo é usado, os dados se acumulam.</span><span class="sxs-lookup"><span data-stu-id="befcd-151">As the app is used, data accumulates.</span></span> <span data-ttu-id="befcd-152">Selecione **Atualizar** para recarregar a lâmina com novos dados.</span><span class="sxs-lookup"><span data-stu-id="befcd-152">Select **Refresh** to reload the blade with new data.</span></span>

![Guia de visão geral do Application Insights](./media/monitoring/app-insights-overview.png)

<span data-ttu-id="befcd-154">O Application Insights fornece informações úteis do lado do servidor sem configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="befcd-154">Application Insights provides useful server-side information with no additional configuration.</span></span> <span data-ttu-id="befcd-155">Para obter o maior valor do Application Insights, [instrumente seu aplicativo com o Application Insights SDK](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="befcd-155">To get the most value from Application Insights, [instrument your app with the Application Insights SDK](/azure/application-insights/app-insights-asp-net-core).</span></span> <span data-ttu-id="befcd-156">Quando configurado corretamente, o serviço fornece monitoramento de ponta a ponta em todo o servidor web e navegador, incluindo o desempenho do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="befcd-156">When properly configured, the service provides end-to-end monitoring across the web server and browser, including client-side performance.</span></span> <span data-ttu-id="befcd-157">Para obter mais informações, consulte a [documentação do Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="befcd-157">For more information, see the [Application Insights documentation](/azure/application-insights/app-insights-overview).</span></span>

## <a name="logging"></a><span data-ttu-id="befcd-158">Registro em log</span><span class="sxs-lookup"><span data-stu-id="befcd-158">Logging</span></span>

<span data-ttu-id="befcd-159">Os logs de servidor web e aplicativo são desativados por padrão no Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="befcd-159">Web server and app logs are disabled by default in Azure App Service.</span></span> <span data-ttu-id="befcd-160">Habilite os logs com as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="befcd-160">Enable the logs with the following steps:</span></span>

1. <span data-ttu-id="befcd-161">Abra o [portal Azure](https://portal.azure.com)e navegue até o \*\<mywebapp unique_number\> \* App Service.</span><span class="sxs-lookup"><span data-stu-id="befcd-161">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="befcd-162">No menu à esquerda, role até a seção **Monitoramento.**</span><span class="sxs-lookup"><span data-stu-id="befcd-162">In the menu to the left, scroll down to the **Monitoring** section.</span></span> <span data-ttu-id="befcd-163">Selecione **registros de diagnósticos**.</span><span class="sxs-lookup"><span data-stu-id="befcd-163">Select **Diagnostics logs**.</span></span>

    ![Link de logs de diagnóstico](./media/monitoring/logging.png)

1. <span data-ttu-id="befcd-165">Ativar **registro de aplicativos (sistema de arquivos)**.</span><span class="sxs-lookup"><span data-stu-id="befcd-165">Turn on **Application Logging (Filesystem)**.</span></span> <span data-ttu-id="befcd-166">Se solicitado, clique na caixa para instalar as extensões para permitir o registro do aplicativo no aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="befcd-166">If prompted, click the box to install the extensions to enable app logging in the web app.</span></span>
1. <span data-ttu-id="befcd-167">Defina **o registro do servidor Web** no Sistema de **Arquivos**.</span><span class="sxs-lookup"><span data-stu-id="befcd-167">Set **Web server logging** to **File System**.</span></span>
1. <span data-ttu-id="befcd-168">Insira o **Período de Retenção** em dias.</span><span class="sxs-lookup"><span data-stu-id="befcd-168">Enter the **Retention Period** in days.</span></span> <span data-ttu-id="befcd-169">Por exemplo, 30.</span><span class="sxs-lookup"><span data-stu-id="befcd-169">For example, 30.</span></span>
1. <span data-ttu-id="befcd-170">Clique em **Save** (Salvar).</span><span class="sxs-lookup"><span data-stu-id="befcd-170">Click **Save**.</span></span>

<span data-ttu-id="befcd-171">ASP.NET os logs do Core e do servidor web (App Service) são gerados para o aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="befcd-171">ASP.NET Core and web server (App Service) logs are generated for the web app.</span></span> <span data-ttu-id="befcd-172">Eles podem ser baixados usando as informações FTP/FTPS exibidas.</span><span class="sxs-lookup"><span data-stu-id="befcd-172">They can be downloaded using the FTP/FTPS information displayed.</span></span> <span data-ttu-id="befcd-173">A senha é a mesma das credenciais de implantação criadas anteriormente neste guia.</span><span class="sxs-lookup"><span data-stu-id="befcd-173">The password is the same as the deployment credentials created earlier in this guide.</span></span> <span data-ttu-id="befcd-174">Os logs podem ser [transmitidos diretamente para sua máquina local com PowerShell ou Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download).</span><span class="sxs-lookup"><span data-stu-id="befcd-174">The logs can be [streamed directly to your local machine with PowerShell or Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download).</span></span> <span data-ttu-id="befcd-175">Os logs também podem ser [visualizados no Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span><span class="sxs-lookup"><span data-stu-id="befcd-175">Logs can also be [viewed in Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span></span>

## <a name="log-streaming"></a><span data-ttu-id="befcd-176">Streaming de log</span><span class="sxs-lookup"><span data-stu-id="befcd-176">Log streaming</span></span>

<span data-ttu-id="befcd-177">Os logs de aplicativos e servidores web podem ser transmitidos em tempo real através do portal.</span><span class="sxs-lookup"><span data-stu-id="befcd-177">App and web server logs can be streamed in real time through the portal.</span></span>

1. <span data-ttu-id="befcd-178">Abra o [portal Azure](https://portal.azure.com)e navegue até o \*\<mywebapp unique_number\> \* App Service.</span><span class="sxs-lookup"><span data-stu-id="befcd-178">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="befcd-179">No menu à esquerda, role até a seção **'Monitorar'e** selecione **'Log' ''''''''''''''''''''''**</span><span class="sxs-lookup"><span data-stu-id="befcd-179">In the menu to the left, scroll down to the **Monitoring** section and select **Log stream**.</span></span>

    ![Captura de tela mostrando link de fluxo de log](./media/monitoring/log-stream.png)

<span data-ttu-id="befcd-181">Os logs também podem ser [transmitidos via Azure CLI ou Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), inclusive através do Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="befcd-181">Logs can also be [streamed via Azure CLI or Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), including through the Cloud Shell.</span></span>

## <a name="alerts"></a><span data-ttu-id="befcd-182">Alertas</span><span class="sxs-lookup"><span data-stu-id="befcd-182">Alerts</span></span>

<span data-ttu-id="befcd-183">O Azure Monitor também fornece [alertas](/azure/monitoring-and-diagnostics/insights-alerts-portal) em tempo real com base em métricas, eventos administrativos e outros critérios.</span><span class="sxs-lookup"><span data-stu-id="befcd-183">Azure Monitor also provides [real time alerts](/azure/monitoring-and-diagnostics/insights-alerts-portal) based on metrics, administrative events, and other criteria.</span></span>

> <span data-ttu-id="befcd-184">*Nota: Atualmente, o alerta sobre métricas de aplicativos web só está disponível no serviço Alertas (clássico).*</span><span class="sxs-lookup"><span data-stu-id="befcd-184">*Note: Currently alerting on web app metrics is only available in the Alerts (classic) service.*</span></span>

<span data-ttu-id="befcd-185">O [serviço Alertas (clássico)](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) pode ser encontrado no Monitor Do Azure ou na seção **Monitoramento** das configurações do Serviço de Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="befcd-185">The [Alerts (classic) service](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) can be found in Azure Monitor or under the **Monitoring** section of the App Service settings.</span></span>

![Link de alertas (clássico)](./media/monitoring/alerts.png)

## <a name="live-debugging"></a><span data-ttu-id="befcd-187">Depuração ao vivo</span><span class="sxs-lookup"><span data-stu-id="befcd-187">Live debugging</span></span>

<span data-ttu-id="befcd-188">O Azure App Service pode ser [depurado remotamente com o Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) quando os logs não fornecem informações suficientes.</span><span class="sxs-lookup"><span data-stu-id="befcd-188">Azure App Service can be [debugged remotely with Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) when logs don't provide enough information.</span></span> <span data-ttu-id="befcd-189">No entanto, a depuração remota requer que o aplicativo seja compilado com símbolos de depuração.</span><span class="sxs-lookup"><span data-stu-id="befcd-189">However, remote debugging requires the app to be compiled with debug symbols.</span></span> <span data-ttu-id="befcd-190">A depuração não deve ser feita na produção, exceto como último recurso.</span><span class="sxs-lookup"><span data-stu-id="befcd-190">Debugging shouldn't be done in production, except as a last resort.</span></span>

## <a name="conclusion"></a><span data-ttu-id="befcd-191">Conclusão</span><span class="sxs-lookup"><span data-stu-id="befcd-191">Conclusion</span></span>

<span data-ttu-id="befcd-192">Nesta seção, você completou as seguintes tarefas:</span><span class="sxs-lookup"><span data-stu-id="befcd-192">In this section, you completed the following tasks:</span></span>

* <span data-ttu-id="befcd-193">Encontre dados básicos de monitoramento e solução de problemas no portal Azure</span><span class="sxs-lookup"><span data-stu-id="befcd-193">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="befcd-194">Saiba como o Azure Monitor fornece um olhar mais profundo sobre métricas em todos os serviços do Azure</span><span class="sxs-lookup"><span data-stu-id="befcd-194">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="befcd-195">Conecte o aplicativo web com insights de aplicativos para criação de perfil de aplicativos</span><span class="sxs-lookup"><span data-stu-id="befcd-195">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="befcd-196">Ative o registro e saiba onde baixar logs</span><span class="sxs-lookup"><span data-stu-id="befcd-196">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="befcd-197">Stream logs em tempo real</span><span class="sxs-lookup"><span data-stu-id="befcd-197">Stream logs in real time</span></span>
* <span data-ttu-id="befcd-198">Saiba onde configurar alertas</span><span class="sxs-lookup"><span data-stu-id="befcd-198">Learn where to set up alerts</span></span>
* <span data-ttu-id="befcd-199">Saiba mais sobre a depuração remota de aplicativos web do Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="befcd-199">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="befcd-200">Leitura adicional</span><span class="sxs-lookup"><span data-stu-id="befcd-200">Additional reading</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="befcd-201">Monitorar o desempenho do aplicativo Web do Azure com o Application Insights</span><span class="sxs-lookup"><span data-stu-id="befcd-201">Monitor Azure web app performance with Application Insights</span></span>](/azure/application-insights/app-insights-azure-web-apps)
* [<span data-ttu-id="befcd-202">Habilitar o registro em log de diagnóstico para aplicativos Web no Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="befcd-202">Enable diagnostics logging for web apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)
* [<span data-ttu-id="befcd-203">Solucionar problemas de um aplicativo Web no Serviço de Aplicativo do Azure usando o Visual Studio</span><span class="sxs-lookup"><span data-stu-id="befcd-203">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="befcd-204">Criar alertas de métrica clássicos no Azure Monitor para serviços do Azure – Portal do Azure</span><span class="sxs-lookup"><span data-stu-id="befcd-204">Create classic metric alerts in Azure Monitor for Azure services - Azure portal</span></span>](/azure/monitoring-and-diagnostics/insights-alerts-portal)
