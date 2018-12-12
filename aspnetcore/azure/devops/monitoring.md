---
title: Monitorar e depurar - DevOps com o ASP.NET Core e o Azure
author: CamSoper
description: Monitorar e depurar seu código como parte de uma solução de DevOps com o ASP.NET Core e o Azure
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/monitor
ms.openlocfilehash: 00489bd92dfff8fd80bd24c2e60193d32031d7c4
ms.sourcegitcommit: b34b25da2ab68e6495b2460ff570468f16a9bf0d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284364"
---
# <a name="monitor-and-debug"></a>Monitorar e depurar

Tendo implantado o aplicativo e criado um pipeline de DevOps, é importante entender como monitorar e solucionar problemas com o aplicativo.

Nesta seção, você concluirá as seguintes tarefas:

* Localizar básicos de monitoramento e solução de problemas de dados no portal do Azure
* Saiba como o Azure Monitor fornece uma análise mais profunda das métricas entre todos os serviços do Azure
* Conectar o aplicativo web com o Application Insights para a criação de perfil de aplicativo
* Ativar o registro em log e saiba onde baixar logs
* Stream logs em tempo real
* Saiba onde configurar alertas
* Saiba mais sobre aplicativos de web do serviço de aplicativo do Azure depuração remotos.

## <a name="basic-monitoring-and-troubleshooting"></a>Solução de problemas e monitoramento básico

Com facilidade, aplicativos web do serviço de aplicativo são monitorados em tempo real. O portal do Azure processa as métricas em gráficos fáceis de entender e.

1. Abra o [portal do Azure](https://portal.azure.com)e, em seguida, navegue até a *mywebapp\<unique_number\>*  o serviço de aplicativo.

1. O **visão geral** guia exibe informações úteis de "instantâneo", incluindo gráficos que exibem as métricas recentes.

    ![Painel de visão geral do captura de tela mostrando](./media/monitoring/overview.png)

    * **Http 5xx**: Contagem de erros do lado do servidor, geralmente exceções no código do ASP.NET Core.
    * **Dados em**: Entrada de dados que chegam ao seu aplicativo web.
    * **Saída de dados**: Saída de dados do seu aplicativo web aos clientes.
    * **Solicitações**: Contagem de solicitações HTTP.
    * **Tempo médio de resposta**: Tempo médio para o aplicativo web responder às solicitações HTTP.

    Várias ferramentas de autoatendimento para otimização e solução de problemas também são encontradas nesta página.

    ![Captura de tela mostrando ferramentas de autoatendimento](./media/monitoring/wizards.png)

    * **Diagnosticar e resolver problemas** é uma solução de problemas de autoatendimento.
    * **Application Insights** é para criação de perfil de desempenho e o comportamento do aplicativo e será discutido posteriormente nesta seção.
    * **Assistente do serviço de aplicativo** faz recomendações para ajustar sua experiência de aplicativo.

## <a name="advanced-monitoring"></a>Monitoramento avançado

[O Azure Monitor](/azure/monitoring-and-diagnostics/) é o serviço centralizado para todas as métricas de monitoramento e configuração de alertas em todos os serviços do Azure. Dentro do Azure Monitor, os administradores podem controlar o desempenho granularmente e identificar tendências. Cada serviço do Azure oferece seu próprio [conjunto de métricas](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) para o Azure Monitor.

## <a name="profile-with-application-insights"></a>Perfil com o Application Insights

[Application Insights](/azure/application-insights/app-insights-overview) é um serviço do Azure para analisar o desempenho e estabilidade de aplicativos web e como os usuários usá-los. Os dados do Application Insights são mais amplos e aprofundados do que o Azure Monitor. Os dados podem fornecer os desenvolvedores e administradores com informações de chave para aprimorar aplicativos. Application Insights podem ser adicionados a um recurso de serviço de aplicativo do Azure sem alterações de código.

1. Abra o [portal do Azure](https://portal.azure.com)e, em seguida, navegue até a *mywebapp\<unique_number\>*  o serviço de aplicativo.
1. Dos **visão geral** , clique o **Application Insights** lado a lado.

    ![Bloco do Application Insights](./media/monitoring/app-insights.png)

1. Selecione o **criar novo recurso** botão de opção. Use o nome de recurso padrão e selecione o local para o recurso Application Insights. O local não precisa corresponder ao que um aplicativo web.

    ![O programa de instalação do Application Insights](./media/monitoring/new-app-insights.png)

1. Para **tempo de execução/estrutura**, selecione **ASP.NET Core**. Aceite as configurações padrão.
1. Selecione **OK**. Se solicitado a confirmar, selecione **continuar**.
1. Depois que o recurso tiver sido criado, clique no nome do recurso do Application Insights para navegar diretamente até a página do Application Insights.

    ![Novo recurso do Application Insights está pronto](./media/monitoring/new-app-insights-done.png)

Como o aplicativo é usado, os dados acumulam. Selecione **Refresh** para recarregar a folha com novos dados.

![Guia de visão geral do Application Insights](./media/monitoring/app-insights-overview.png)

O Application Insights fornece informações úteis do lado do servidor sem nenhuma configuração adicional. Para obter o máximo valor do Application Insights [instrumentar seu aplicativo com o SDK do Application Insights](/azure/application-insights/app-insights-asp-net-core). Quando configurado corretamente, o serviço fornece monitoramento de ponta a ponta entre o servidor web e o navegador, incluindo desempenho do lado do cliente. Para obter mais informações, consulte o [documentação do Application Insights](/azure/application-insights/app-insights-overview).

## <a name="logging"></a>Registrando em log

Logs de servidor e aplicativo da Web estão desabilitados por padrão no serviço de aplicativo do Azure. Habilite os logs com as seguintes etapas:

1. Abra o [portal do Azure](https://portal.azure.com)e navegue até a *mywebapp\<unique_number\>*  o serviço de aplicativo.
1. No menu à esquerda, role para baixo até a **monitoramento** seção. Selecione **logs de diagnóstico**.

    ![Link de logs de diagnóstico](./media/monitoring/logging.png)

1. Ative **log de aplicativo (Filesystem)**. Se solicitado, clique na caixa para instalar as extensões para habilitar o registro em log no aplicativo web do aplicativo.
1. Definir **log do servidor Web** à **sistema de arquivos**.
1. Insira o **período de retenção** em dias. Por exemplo, 30.
1. Clique em **Salvar**.

Logs do servidor (serviço de aplicativo) do ASP.NET Core e da web são gerados para o aplicativo web. Eles podem ser baixados usando as informações de FTP/FTPS exibidas. A senha é o mesmo que as credenciais de implantação criadas anteriormente neste guia. Os logs podem ser [transmitido diretamente ao seu computador local com o PowerShell ou CLI do Azure](/azure/app-service/web-sites-enable-diagnostic-log#download). Os logs também podem ser [exibidos no Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).

## <a name="log-streaming"></a>Streaming de log

Logs do servidor web e de aplicativo podem ser transmitidos em tempo real por meio do portal.

1. Abra o [portal do Azure](https://portal.azure.com)e navegue até a *mywebapp\<unique_number\>*  o serviço de aplicativo.
1. No menu à esquerda, role para baixo até a **Monitoring** seção e selecione **fluxo de Log**.

    ![Captura de tela mostrando o link de fluxo de log](./media/monitoring/log-stream.png)

Os logs também podem ser [transmitido por meio da CLI do Azure ou o Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), incluindo por meio do Cloud Shell.

## <a name="alerts"></a>Alertas

O Azure Monitor também fornece [alertas em tempo real](/azure/monitoring-and-diagnostics/insights-alerts-portal) com base em métricas, eventos administrativos e outros critérios.

> *Observação: No momento, alertas em métricas do aplicativo web só está disponível no serviço alertas (clássico).*

O [alertas de serviço (clássico)](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) pode ser encontrada no Azure Monitor ou sob o **monitoramento** seção das configurações de serviço de aplicativo.

![Link de alertas (clássico)](./media/monitoring/alerts.png)

## <a name="live-debugging"></a>Depuração ao vivo

O serviço de aplicativo do Azure pode ser [depurado remotamente com o Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) quando os logs não fornecer informações suficientes. No entanto, a depuração remota exige o aplicativo a ser compilada com símbolos de depuração. Depuração não deve ser feita em produção, exceto como último recurso.

## <a name="conclusion"></a>Conclusão

Nesta seção, você concluiu as seguintes tarefas:

* Localizar básicos de monitoramento e solução de problemas de dados no portal do Azure
* Saiba como o Azure Monitor fornece uma análise mais profunda das métricas entre todos os serviços do Azure
* Conectar o aplicativo web com o Application Insights para a criação de perfil de aplicativo
* Ativar o registro em log e saiba onde baixar logs
* Stream logs em tempo real
* Saiba onde configurar alertas
* Saiba mais sobre aplicativos de web do serviço de aplicativo do Azure depuração remotos.

## <a name="additional-reading"></a>Leitura adicional

* <xref:host-and-deploy/azure-apps/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Monitorar o desempenho do aplicativo web do Azure com o Application Insights](/azure/application-insights/app-insights-azure-web-apps)
* [Habilitar log de diagnósticos para aplicativos Web no Serviço de Aplicativo do Azure](/azure/app-service/web-sites-enable-diagnostic-log)
* [Solucionar problemas de um aplicativo Web no Serviço de Aplicativo do Azure usando o Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Criar alertas de métrica clássicos no Azure Monitor para serviços do Azure - portal do Azure](/azure/monitoring-and-diagnostics/insights-alerts-portal)
