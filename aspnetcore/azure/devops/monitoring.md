---
title: Monitorar e depurar-DevOps com o ASP.NET Core e o Azure
author: CamSoper
description: Monitorando e Depurando seu código como parte de uma solução DevOps com ASP.NET Core e o Azure
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 07/10/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/monitor
ms.openlocfilehash: a94b1e0b5ce2a24cf22eb665c9bcd03c25ffa67f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400369"
---
# <a name="monitor-and-debug"></a>Monitorar e depurar

Depois de implantar o aplicativo e criar um pipeline do DevOps, é importante entender como monitorar e solucionar problemas do aplicativo.

Nesta seção, você concluirá as seguintes tarefas:

* Encontre monitoramento básico e dados de solução de problemas no portal do Azure
* Saiba como Azure Monitor fornece uma análise mais profunda das métricas em todos os serviços do Azure
* Conectar o aplicativo Web com Application Insights para a criação de perfil de aplicativo
* Ativar o registro em log e saber onde baixar logs
* Transmitir logs em tempo real
* Saiba onde configurar alertas
* Saiba mais sobre a depuração remota Azure App aplicativos Web do serviço.

## <a name="basic-monitoring-and-troubleshooting"></a>Monitoramento e solução de problemas básicos

Os aplicativos Web do serviço de aplicativo são facilmente monitorados em tempo real. O portal do Azure renderiza métricas em gráficos fáceis de entender e grafos.

1. Abra o [portal do Azure](https://portal.azure.com)e, em seguida, navegue até o serviço de aplicativo *myWebApp \<unique_number\> * .

1. A guia **visão geral** exibe informações úteis "em breve", incluindo grafos que exibem métricas recentes.

    ![Captura de tela mostrando o painel Visão geral](./media/monitoring/overview.png)

    * **Http 5xx**: contagem de erros do lado do servidor, geralmente exceções no código ASP.NET Core.
    * **Dados em**: entrada de dados que entra em seu aplicativo Web.
    * **Saída de dados**: dados de saída de seu aplicativo Web para clientes.
    * **Solicitações**: contagem de solicitações HTTP.
    * **Tempo médio de resposta**: tempo médio para o aplicativo Web responder a solicitações HTTP.

    Várias ferramentas de autoatendimento para solução de problemas e otimização também são encontradas nesta página.

    ![Captura de tela mostrando ferramentas de autoatendimento](./media/monitoring/wizards.png)

    * **Diagnosticar e resolver problemas** é um solucionador de problemas de autoatendimento.
    * **Application insights** é para o desempenho da criação de perfil e o comportamento do aplicativo, e é discutido posteriormente nesta seção.
    * **Assistente do serviço de aplicativo** faz recomendações para ajustar sua experiência de aplicativo.

## <a name="advanced-monitoring"></a>Monitoramento avançado

[Azure monitor](/azure/monitoring-and-diagnostics/) é o serviço centralizado para monitorar todas as métricas e definir alertas nos serviços do Azure. No Azure Monitor, os administradores podem controlar de maneira granular o desempenho e identificar as tendências. Cada serviço do Azure oferece seu próprio [conjunto de métricas](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) para Azure monitor.

## <a name="profile-with-application-insights"></a>Perfil com Application Insights

[Application insights](/azure/application-insights/app-insights-overview) é um serviço do Azure para analisar o desempenho e a estabilidade de aplicativos Web e como os usuários os usam. Os dados de Application Insights são mais amplos e mais detalhados do que o de Azure Monitor. Os dados podem fornecer aos desenvolvedores e aos administradores informações importantes para melhorar os aplicativos. Application Insights pode ser adicionado a um recurso de serviço de Azure App sem alterações de código.

1. Abra o [portal do Azure](https://portal.azure.com)e, em seguida, navegue até o serviço de aplicativo *myWebApp \<unique_number\> * .
1. Na guia **visão geral** , clique no bloco **Application insights** .

    ![Bloco do Application Insights](./media/monitoring/app-insights.png)

1. Selecione o botão de opção **criar novo recurso** . Use o nome do recurso padrão e selecione o local para o recurso de Application Insights. O local não precisa corresponder ao do seu aplicativo Web.

    ![Application Insights configuração](./media/monitoring/new-app-insights.png)

1. Para **tempo de execução/estrutura**, selecione **ASP.NET Core**. Aceite as configurações padrão.
1. Selecione **OK**. Se for solicitado a confirmar, selecione **continuar**.
1. Depois que o recurso tiver sido criado, clique no nome do Application Insights recurso para navegar diretamente para a página Application Insights.

    ![Novo recurso de Application Insights está pronto](./media/monitoring/new-app-insights-done.png)

Conforme o aplicativo é usado, os dados são acumulados. Selecione **Atualizar** para recarregar a folha com novos dados.

![Guia de visão geral do Application Insights](./media/monitoring/app-insights-overview.png)

Application Insights fornece informações úteis do lado do servidor sem nenhuma configuração adicional. Para obter o maior valor de Application Insights, [Instrumente seu aplicativo com o SDK do Application insights](/azure/application-insights/app-insights-asp-net-core). Quando configurado corretamente, o serviço fornece monitoramento de ponta a ponta no servidor Web e no navegador, incluindo o desempenho do lado do cliente. Para obter mais informações, consulte a [documentação do Application insights](/azure/application-insights/app-insights-overview).

## <a name="logging"></a>Registrando em log

Os logs do servidor Web e do aplicativo são desabilitados por padrão no serviço Azure App. Habilite os logs com as seguintes etapas:

1. Abra o [portal do Azure](https://portal.azure.com)e navegue até o serviço de aplicativo *myWebApp \<unique_number\> * .
1. No menu à esquerda, role para baixo até a seção **monitoramento** . Selecione **logs de diagnóstico**.

    ![Link de logs de diagnóstico](./media/monitoring/logging.png)

1. Ative o **registro em log do aplicativo (Filesystem)**. Se solicitado, clique na caixa para instalar as extensões para habilitar o log de aplicativo no aplicativo Web.
1. Defina o **log do servidor Web** no **sistema de arquivos**.
1. Insira o **período de retenção** em dias. Por exemplo, 30.
1. Clique em **Salvar**.

Os logs do ASP.NET Core e do servidor Web (serviço de aplicativo) são gerados para o aplicativo Web. Eles podem ser baixados usando as informações de FTP/FTPS exibidas. A senha é a mesma que as credenciais de implantação criadas anteriormente neste guia. Os logs podem ser [transmitidos diretamente para seu computador local com o PowerShell ou CLI do Azure](/azure/app-service/web-sites-enable-diagnostic-log#download). Os logs também podem ser [exibidos em Application insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).

## <a name="log-streaming"></a>Streaming de log

Os logs do aplicativo e do servidor Web podem ser transmitidos em tempo real por meio do Portal.

1. Abra o [portal do Azure](https://portal.azure.com)e navegue até o serviço de aplicativo *myWebApp \<unique_number\> * .
1. No menu à esquerda, role para baixo até a seção **monitoramento** e selecione **fluxo de log**.

    ![Captura de tela mostrando link de fluxo de log](./media/monitoring/log-stream.png)

Os logs também podem ser [transmitidos por meio de CLI do Azure ou Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), incluindo por meio do Cloud Shell.

## <a name="alerts"></a>Alertas

O Azure Monitor também fornece [alertas em tempo real](/azure/monitoring-and-diagnostics/insights-alerts-portal) com base em métricas, eventos administrativos e outros critérios.

> *Observação: atualmente, os alertas em métricas de aplicativo Web só estão disponíveis no serviço alertas (clássico).*

O [serviço alertas (clássico)](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) pode ser encontrado em Azure monitor ou na seção **monitoramento** das configurações do serviço de aplicativo.

![Link de alertas (clássico)](./media/monitoring/alerts.png)

## <a name="live-debugging"></a>Depuração dinâmica

Azure App serviço pode ser [depurado remotamente com o Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) quando os logs não fornecem informações suficientes. No entanto, a depuração remota exige que o aplicativo seja compilado com símbolos de depuração. A depuração não deve ser feita em produção, exceto como último recurso.

## <a name="conclusion"></a>Conclusão

Nesta seção, você concluiu as seguintes tarefas:

* Encontre monitoramento básico e dados de solução de problemas no portal do Azure
* Saiba como Azure Monitor fornece uma análise mais profunda das métricas em todos os serviços do Azure
* Conectar o aplicativo Web com Application Insights para a criação de perfil de aplicativo
* Ativar o registro em log e saber onde baixar logs
* Transmitir logs em tempo real
* Saiba onde configurar alertas
* Saiba mais sobre a depuração remota Azure App aplicativos Web do serviço.

## <a name="additional-reading"></a>Leitura adicional

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Monitorar o desempenho do aplicativo Web do Azure com o Application Insights](/azure/application-insights/app-insights-azure-web-apps)
* [Habilitar o registro em log de diagnóstico para aplicativos Web no Serviço de Aplicativo do Azure](/azure/app-service/web-sites-enable-diagnostic-log)
* [Solucionar problemas de um aplicativo Web no Serviço de Aplicativo do Azure usando o Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Criar alertas de métrica clássicos no Azure Monitor para serviços do Azure – Portal do Azure](/azure/monitoring-and-diagnostics/insights-alerts-portal)
