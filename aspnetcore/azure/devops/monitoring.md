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
# <a name="monitor-and-debug"></a>Monitor e depuração

Tendo implantado o aplicativo e construído um pipeline DevOps, é importante entender como monitorar e solucionar problemas do aplicativo.

Nesta seção, você completará as seguintes tarefas:

* Encontre dados básicos de monitoramento e solução de problemas no portal Azure
* Saiba como o Azure Monitor fornece um olhar mais profundo sobre métricas em todos os serviços do Azure
* Conecte o aplicativo web com insights de aplicativos para criação de perfil de aplicativos
* Ative o registro e saiba onde baixar logs
* Stream logs em tempo real
* Saiba onde configurar alertas
* Saiba mais sobre a depuração remota de aplicativos web do Azure App Service.

## <a name="basic-monitoring-and-troubleshooting"></a>Monitoramento básico e solução de problemas

Os aplicativos web App Service são facilmente monitorados em tempo real. O portal Azure renderiza métricas em gráficos e gráficos fáceis de entender.

1. Abra o [portal Azure](https://portal.azure.com)e navegue até o *\<mywebapp unique_number\> * App Service.

1. **A guia Visão Geral** exibe informações úteis "à vista", incluindo gráficos que exibem métricas recentes.

    ![Captura de tela mostrando painel de visão geral](./media/monitoring/overview.png)

    * **Http 5xx**: Contagem de erros do lado do servidor, geralmente exceções em ASP.NET código Core.
    * **Data In**: Entrada de dados entrando em seu aplicativo web.
    * **Data Out**: Saída de dados do seu aplicativo web para clientes.
    * **Solicitações**: Contagem de solicitações HTTP.
    * **Tempo médio de resposta**: Tempo médio para que o aplicativo web responda às solicitações HTTP.

    Várias ferramentas de autoatendimento para solução de problemas e otimização também são encontradas nesta página.

    ![Captura de tela mostrando ferramentas de autoatendimento](./media/monitoring/wizards.png)

    * **Diagnosticar e resolver problemas** é um problema de autoatendimento.
    * **O Application Insights** é para o desempenho de criação de perfil e comportamento do aplicativo, e é discutido mais tarde nesta seção.
    * **O App Service Advisor** faz recomendações para ajustar a experiência do seu aplicativo.

## <a name="advanced-monitoring"></a>Monitoramento avançado

[O Azure Monitor](/azure/monitoring-and-diagnostics/) é o serviço centralizado para monitorar todas as métricas e definir alertas em todos os serviços do Azure. No Azure Monitor, os administradores podem acompanhar granularmente o desempenho e identificar tendências. Cada serviço do Azure oferece seu próprio [conjunto de métricas](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) ao Azure Monitor.

## <a name="profile-with-application-insights"></a>Perfil com Insights de Aplicativos

[O Application Insights](/azure/application-insights/app-insights-overview) é um serviço do Azure para analisar o desempenho e a estabilidade dos aplicativos web e como os usuários os usam. Os dados do Application Insights são mais amplos e profundos do que os do Azure Monitor. Os dados podem fornecer aos desenvolvedores e administradores informações-chave para melhorar os aplicativos. Os Insights do aplicativo podem ser adicionados a um recurso do Azure App Service sem alterações de código.

1. Abra o [portal Azure](https://portal.azure.com)e navegue até o *\<mywebapp unique_number\> * App Service.
1. Na **guia Visão geral,** clique no bloco **'Insights do** aplicativo'.

    ![Bloco do Application Insights](./media/monitoring/app-insights.png)

1. Selecione o **botão Criar novo recurso de** rádio. Use o nome do recurso padrão e selecione o local para o recurso Application Insights. O local não precisa coincidir com o do seu aplicativo web.

    ![Configuração do Application Insights](./media/monitoring/new-app-insights.png)

1. Em **Tempo de execução/Framework,** selecione **ASP.NET Núcleo**. Aceite as configurações padrão.
1. Selecione **OK**. Se solicitado a confirmar, selecione **Continuar**.
1. Depois que o recurso tiver sido criado, clique no nome do recurso Application Insights para navegar diretamente na página Do aplicativo Insights.

    ![Novo recurso do Application Insights está pronto](./media/monitoring/new-app-insights-done.png)

À medida que o aplicativo é usado, os dados se acumulam. Selecione **Atualizar** para recarregar a lâmina com novos dados.

![Guia de visão geral do Application Insights](./media/monitoring/app-insights-overview.png)

O Application Insights fornece informações úteis do lado do servidor sem configuração adicional. Para obter o maior valor do Application Insights, [instrumente seu aplicativo com o Application Insights SDK](/azure/application-insights/app-insights-asp-net-core). Quando configurado corretamente, o serviço fornece monitoramento de ponta a ponta em todo o servidor web e navegador, incluindo o desempenho do lado do cliente. Para obter mais informações, consulte a [documentação do Application Insights](/azure/application-insights/app-insights-overview).

## <a name="logging"></a>Registro em log

Os logs de servidor web e aplicativo são desativados por padrão no Azure App Service. Habilite os logs com as seguintes etapas:

1. Abra o [portal Azure](https://portal.azure.com)e navegue até o *\<mywebapp unique_number\> * App Service.
1. No menu à esquerda, role até a seção **Monitoramento.** Selecione **registros de diagnósticos**.

    ![Link de logs de diagnóstico](./media/monitoring/logging.png)

1. Ativar **registro de aplicativos (sistema de arquivos)**. Se solicitado, clique na caixa para instalar as extensões para permitir o registro do aplicativo no aplicativo web.
1. Defina **o registro do servidor Web** no Sistema de **Arquivos**.
1. Insira o **Período de Retenção** em dias. Por exemplo, 30.
1. Clique em **Save** (Salvar).

ASP.NET os logs do Core e do servidor web (App Service) são gerados para o aplicativo web. Eles podem ser baixados usando as informações FTP/FTPS exibidas. A senha é a mesma das credenciais de implantação criadas anteriormente neste guia. Os logs podem ser [transmitidos diretamente para sua máquina local com PowerShell ou Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download). Os logs também podem ser [visualizados no Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).

## <a name="log-streaming"></a>Streaming de log

Os logs de aplicativos e servidores web podem ser transmitidos em tempo real através do portal.

1. Abra o [portal Azure](https://portal.azure.com)e navegue até o *\<mywebapp unique_number\> * App Service.
1. No menu à esquerda, role até a seção **'Monitorar'e** selecione **'Log' ''''''''''''''''''''''**

    ![Captura de tela mostrando link de fluxo de log](./media/monitoring/log-stream.png)

Os logs também podem ser [transmitidos via Azure CLI ou Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), inclusive através do Cloud Shell.

## <a name="alerts"></a>Alertas

O Azure Monitor também fornece [alertas](/azure/monitoring-and-diagnostics/insights-alerts-portal) em tempo real com base em métricas, eventos administrativos e outros critérios.

> *Nota: Atualmente, o alerta sobre métricas de aplicativos web só está disponível no serviço Alertas (clássico).*

O [serviço Alertas (clássico)](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) pode ser encontrado no Monitor Do Azure ou na seção **Monitoramento** das configurações do Serviço de Aplicativo.

![Link de alertas (clássico)](./media/monitoring/alerts.png)

## <a name="live-debugging"></a>Depuração ao vivo

O Azure App Service pode ser [depurado remotamente com o Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) quando os logs não fornecem informações suficientes. No entanto, a depuração remota requer que o aplicativo seja compilado com símbolos de depuração. A depuração não deve ser feita na produção, exceto como último recurso.

## <a name="conclusion"></a>Conclusão

Nesta seção, você completou as seguintes tarefas:

* Encontre dados básicos de monitoramento e solução de problemas no portal Azure
* Saiba como o Azure Monitor fornece um olhar mais profundo sobre métricas em todos os serviços do Azure
* Conecte o aplicativo web com insights de aplicativos para criação de perfil de aplicativos
* Ative o registro e saiba onde baixar logs
* Stream logs em tempo real
* Saiba onde configurar alertas
* Saiba mais sobre a depuração remota de aplicativos web do Azure App Service.

## <a name="additional-reading"></a>Leitura adicional

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Monitorar o desempenho do aplicativo Web do Azure com o Application Insights](/azure/application-insights/app-insights-azure-web-apps)
* [Habilitar o registro em log de diagnóstico para aplicativos Web no Serviço de Aplicativo do Azure](/azure/app-service/web-sites-enable-diagnostic-log)
* [Solucionar problemas de um aplicativo Web no Serviço de Aplicativo do Azure usando o Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Criar alertas de métrica clássicos no Azure Monitor para serviços do Azure – Portal do Azure](/azure/monitoring-and-diagnostics/insights-alerts-portal)
