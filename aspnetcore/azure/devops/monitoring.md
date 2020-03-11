---
title: Monitorar e depurar - DevOps com o ASP.NET Core e o Azure
author: CamSoper
description: Monitorar e depurar seu código como parte de uma solução de DevOps com o ASP.NET Core e o Azure
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 07/10/2019
uid: azure/devops/monitor
ms.openlocfilehash: 1d8ed99f4387dbc99929164c558cc2ce14bd9ea0
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659498"
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

1. Abra o [portal do Azure](https://portal.azure.com)e, em seguida, navegue até o *mywebapp\<unique_number\>* serviço de aplicativo.

1. A guia **visão geral** exibe informações úteis "em breve", incluindo grafos que exibem métricas recentes.

    ![Painel de visão geral do captura de tela mostrando](./media/monitoring/overview.png)

    * **Http 5xx**: contagem de erros do lado do servidor, geralmente exceções no código ASP.NET Core.
    * **Dados em**: entrada de dados que entra em seu aplicativo Web.
    * **Saída de dados**: dados de saída de seu aplicativo Web para clientes.
    * **Solicitações**: contagem de solicitações HTTP.
    * **Tempo médio de resposta**: tempo médio para o aplicativo Web responder a solicitações HTTP.

    Várias ferramentas de autoatendimento para otimização e solução de problemas também são encontradas nesta página.

    ![Captura de tela mostrando ferramentas de autoatendimento](./media/monitoring/wizards.png)

    * **Diagnosticar e resolver problemas** é um solucionador de problemas de autoatendimento.
    * **Application insights** é para o desempenho da criação de perfil e o comportamento do aplicativo, e é discutido posteriormente nesta seção.
    * **Assistente do serviço de aplicativo** faz recomendações para ajustar sua experiência de aplicativo.

## <a name="advanced-monitoring"></a>Monitoramento avançado

[Azure monitor](/azure/monitoring-and-diagnostics/) é o serviço centralizado para monitorar todas as métricas e definir alertas nos serviços do Azure. Dentro do Azure Monitor, os administradores podem controlar o desempenho granularmente e identificar tendências. Cada serviço do Azure oferece seu próprio [conjunto de métricas](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) para Azure monitor.

## <a name="profile-with-application-insights"></a>Perfil com o Application Insights

[Application insights](/azure/application-insights/app-insights-overview) é um serviço do Azure para analisar o desempenho e a estabilidade de aplicativos Web e como os usuários os usam. Os dados do Application Insights são mais amplos e aprofundados do que o Azure Monitor. Os dados podem fornecer os desenvolvedores e administradores com informações de chave para aprimorar aplicativos. Application Insights podem ser adicionados a um recurso de serviço de aplicativo do Azure sem alterações de código.

1. Abra o [portal do Azure](https://portal.azure.com)e, em seguida, navegue até o *mywebapp\<unique_number\>* serviço de aplicativo.
1. Na guia **visão geral** , clique no bloco **Application insights** .

    ![Bloco do Application Insights](./media/monitoring/app-insights.png)

1. Selecione o botão de opção **criar novo recurso** . Use o nome de recurso padrão e selecione o local para o recurso Application Insights. O local não precisa corresponder ao que um aplicativo web.

    ![O programa de instalação do Application Insights](./media/monitoring/new-app-insights.png)

1. Para **tempo de execução/estrutura**, selecione **ASP.NET Core**. Aceite as configurações padrão.
1. Selecione **OK**. Se for solicitado a confirmar, selecione **continuar**.
1. Depois que o recurso tiver sido criado, clique no nome do recurso do Application Insights para navegar diretamente até a página do Application Insights.

    ![Novo recurso do Application Insights está pronto](./media/monitoring/new-app-insights-done.png)

Como o aplicativo é usado, os dados acumulam. Selecione **Atualizar** para recarregar a folha com novos dados.

![Guia de visão geral do Application Insights](./media/monitoring/app-insights-overview.png)

O Application Insights fornece informações úteis do lado do servidor sem nenhuma configuração adicional. Para obter o maior valor de Application Insights, [Instrumente seu aplicativo com o SDK do Application insights](/azure/application-insights/app-insights-asp-net-core). Quando configurado corretamente, o serviço fornece monitoramento de ponta a ponta entre o servidor web e o navegador, incluindo desempenho do lado do cliente. Para obter mais informações, consulte a [documentação do Application insights](/azure/application-insights/app-insights-overview).

## <a name="logging"></a>Registro em log

Logs de servidor e aplicativo da Web estão desabilitados por padrão no serviço de aplicativo do Azure. Habilite os logs com as seguintes etapas:

1. Abra o [portal do Azure](https://portal.azure.com)e navegue até o *mywebapp\<unique_number\>* serviço de aplicativo.
1. No menu à esquerda, role para baixo até a seção **monitoramento** . Selecione **logs de diagnóstico**.

    ![Link de logs de diagnóstico](./media/monitoring/logging.png)

1. Ative o **registro em log do aplicativo (Filesystem)** . Se solicitado, clique na caixa para instalar as extensões para habilitar o registro em log no aplicativo web do aplicativo.
1. Defina o **log do servidor Web** no **sistema de arquivos**.
1. Insira o **período de retenção** em dias. Por exemplo, 30.
1. Clique em **Salvar**.

Logs do servidor (serviço de aplicativo) do ASP.NET Core e da web são gerados para o aplicativo web. Eles podem ser baixados usando as informações de FTP/FTPS exibidas. A senha é o mesmo que as credenciais de implantação criadas anteriormente neste guia. Os logs podem ser [transmitidos diretamente para seu computador local com o PowerShell ou CLI do Azure](/azure/app-service/web-sites-enable-diagnostic-log#download). Os logs também podem ser [exibidos em Application insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).

## <a name="log-streaming"></a>Streaming de log

Logs do servidor web e de aplicativo podem ser transmitidos em tempo real por meio do portal.

1. Abra o [portal do Azure](https://portal.azure.com)e navegue até o *mywebapp\<unique_number\>* serviço de aplicativo.
1. No menu à esquerda, role para baixo até a seção **monitoramento** e selecione **fluxo de log**.

    ![Captura de tela mostrando o link de fluxo de log](./media/monitoring/log-stream.png)

Os logs também podem ser [transmitidos por meio de CLI do Azure ou Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), incluindo por meio do Cloud Shell.

## <a name="alerts"></a>Alertas

O Azure Monitor também fornece [alertas em tempo real](/azure/monitoring-and-diagnostics/insights-alerts-portal) com base em métricas, eventos administrativos e outros critérios.

> *Observação: atualmente, os alertas em métricas de aplicativo Web só estão disponíveis no serviço alertas (clássico).*

O [serviço alertas (clássico)](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) pode ser encontrado em Azure monitor ou na seção **monitoramento** das configurações do serviço de aplicativo.

![Link de alertas (clássico)](./media/monitoring/alerts.png)

## <a name="live-debugging"></a>Depuração ao vivo

Azure App serviço pode ser [depurado remotamente com o Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) quando os logs não fornecem informações suficientes. No entanto, a depuração remota exige o aplicativo a ser compilada com símbolos de depuração. Depuração não deve ser feita em produção, exceto como último recurso.

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

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Monitorar o desempenho do aplicativo Web do Azure com o Application Insights](/azure/application-insights/app-insights-azure-web-apps)
* [Habilitar o registro em log de diagnóstico para aplicativos Web no Serviço de Aplicativo do Azure](/azure/app-service/web-sites-enable-diagnostic-log)
* [Solucionar problemas de um aplicativo Web no Serviço de Aplicativo do Azure usando o Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Criar alertas de métrica clássicos no Azure Monitor para serviços do Azure-portal do Azure](/azure/monitoring-and-diagnostics/insights-alerts-portal)
