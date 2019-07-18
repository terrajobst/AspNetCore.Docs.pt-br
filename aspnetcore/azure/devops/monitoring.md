---
title: Monitorar e depurar - DevOps com o ASP.NET Core e o Azure
author: CamSoper
description: Monitorar e depurar seu código como parte de uma solução de DevOps com o ASP.NET Core e o Azure
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 07/10/2019
uid: azure/devops/monitor
ms.openlocfilehash: 1d8ed99f4387dbc99929164c558cc2ce14bd9ea0
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68307952"
---
# <a name="monitor-and-debug"></a><span data-ttu-id="8255f-103">Monitorar e depurar</span><span class="sxs-lookup"><span data-stu-id="8255f-103">Monitor and debug</span></span>

<span data-ttu-id="8255f-104">Tendo implantado o aplicativo e criado um pipeline de DevOps, é importante entender como monitorar e solucionar problemas com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8255f-104">Having deployed the app and built a DevOps pipeline, it's important to understand how to monitor and troubleshoot the app.</span></span>

<span data-ttu-id="8255f-105">Nesta seção, você concluirá as seguintes tarefas:</span><span class="sxs-lookup"><span data-stu-id="8255f-105">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="8255f-106">Localizar básicos de monitoramento e solução de problemas de dados no portal do Azure</span><span class="sxs-lookup"><span data-stu-id="8255f-106">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="8255f-107">Saiba como o Azure Monitor fornece uma análise mais profunda das métricas entre todos os serviços do Azure</span><span class="sxs-lookup"><span data-stu-id="8255f-107">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="8255f-108">Conectar o aplicativo web com o Application Insights para a criação de perfil de aplicativo</span><span class="sxs-lookup"><span data-stu-id="8255f-108">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="8255f-109">Ativar o registro em log e saiba onde baixar logs</span><span class="sxs-lookup"><span data-stu-id="8255f-109">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="8255f-110">Stream logs em tempo real</span><span class="sxs-lookup"><span data-stu-id="8255f-110">Stream logs in real time</span></span>
* <span data-ttu-id="8255f-111">Saiba onde configurar alertas</span><span class="sxs-lookup"><span data-stu-id="8255f-111">Learn where to set up alerts</span></span>
* <span data-ttu-id="8255f-112">Saiba mais sobre aplicativos de web do serviço de aplicativo do Azure depuração remotos.</span><span class="sxs-lookup"><span data-stu-id="8255f-112">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="basic-monitoring-and-troubleshooting"></a><span data-ttu-id="8255f-113">Solução de problemas e monitoramento básico</span><span class="sxs-lookup"><span data-stu-id="8255f-113">Basic monitoring and troubleshooting</span></span>

<span data-ttu-id="8255f-114">Com facilidade, aplicativos web do serviço de aplicativo são monitorados em tempo real.</span><span class="sxs-lookup"><span data-stu-id="8255f-114">App Service web apps are easily monitored in real time.</span></span> <span data-ttu-id="8255f-115">O portal do Azure processa as métricas em gráficos fáceis de entender e.</span><span class="sxs-lookup"><span data-stu-id="8255f-115">The Azure portal renders metrics in easy-to-understand charts and graphs.</span></span>

1. <span data-ttu-id="8255f-116">Abra o [portal do Azure](https://portal.azure.com)e, em seguida, navegue até a *mywebapp\<unique_number\>*  o serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8255f-116">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>

1. <span data-ttu-id="8255f-117">O **visão geral** guia exibe informações úteis de "instantâneo", incluindo gráficos que exibem as métricas recentes.</span><span class="sxs-lookup"><span data-stu-id="8255f-117">The **Overview** tab displays useful "at-a-glance" information, including graphs displaying recent metrics.</span></span>

    ![Painel de visão geral do captura de tela mostrando](./media/monitoring/overview.png)

    * <span data-ttu-id="8255f-119">**Http 5xx**: Contagem de erros do lado do servidor, geralmente exceções no código ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8255f-119">**Http 5xx**: Count of server-side errors, usually exceptions in ASP.NET Core code.</span></span>
    * <span data-ttu-id="8255f-120">**Dados em**: Entrada de dados que entra em seu aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="8255f-120">**Data In**: Data ingress coming into your web app.</span></span>
    * <span data-ttu-id="8255f-121">**Saída de dados**: Saída de dados de seu aplicativo Web para clientes.</span><span class="sxs-lookup"><span data-stu-id="8255f-121">**Data Out**: Data egress from your web app to clients.</span></span>
    * <span data-ttu-id="8255f-122">**Solicitações**: Contagem de solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="8255f-122">**Requests**: Count of HTTP requests.</span></span>
    * <span data-ttu-id="8255f-123">**Tempo médio de resposta**: Tempo médio para o aplicativo Web responder às solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="8255f-123">**Average Response Time**: Average time for the web app to respond to HTTP requests.</span></span>

    <span data-ttu-id="8255f-124">Várias ferramentas de autoatendimento para otimização e solução de problemas também são encontradas nesta página.</span><span class="sxs-lookup"><span data-stu-id="8255f-124">Several self-service tools for troubleshooting and optimization are also found on this page.</span></span>

    ![Captura de tela mostrando ferramentas de autoatendimento](./media/monitoring/wizards.png)

    * <span data-ttu-id="8255f-126">**Diagnosticar e resolver problemas** é uma solução de problemas de autoatendimento.</span><span class="sxs-lookup"><span data-stu-id="8255f-126">**Diagnose and solve problems** is a self-service troubleshooter.</span></span>
    * <span data-ttu-id="8255f-127">**Application Insights** é para criação de perfil de desempenho e o comportamento do aplicativo e será discutido posteriormente nesta seção.</span><span class="sxs-lookup"><span data-stu-id="8255f-127">**Application Insights** is for profiling performance and app behavior, and is discussed later in this section.</span></span>
    * <span data-ttu-id="8255f-128">**Assistente do serviço de aplicativo** faz recomendações para ajustar sua experiência de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8255f-128">**App Service Advisor** makes recommendations to tune your app experience.</span></span>

## <a name="advanced-monitoring"></a><span data-ttu-id="8255f-129">Monitoramento avançado</span><span class="sxs-lookup"><span data-stu-id="8255f-129">Advanced monitoring</span></span>

<span data-ttu-id="8255f-130">[O Azure Monitor](/azure/monitoring-and-diagnostics/) é o serviço centralizado para todas as métricas de monitoramento e configuração de alertas em todos os serviços do Azure.</span><span class="sxs-lookup"><span data-stu-id="8255f-130">[Azure Monitor](/azure/monitoring-and-diagnostics/) is the centralized service for monitoring all metrics and setting alerts across Azure services.</span></span> <span data-ttu-id="8255f-131">Dentro do Azure Monitor, os administradores podem controlar o desempenho granularmente e identificar tendências.</span><span class="sxs-lookup"><span data-stu-id="8255f-131">Within Azure Monitor, administrators can granularly track performance and identify trends.</span></span> <span data-ttu-id="8255f-132">Cada serviço do Azure oferece seu próprio [conjunto de métricas](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) para o Azure Monitor.</span><span class="sxs-lookup"><span data-stu-id="8255f-132">Each Azure service offers its own [set of metrics](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) to Azure Monitor.</span></span>

## <a name="profile-with-application-insights"></a><span data-ttu-id="8255f-133">Perfil com o Application Insights</span><span class="sxs-lookup"><span data-stu-id="8255f-133">Profile with Application Insights</span></span>

<span data-ttu-id="8255f-134">[Application Insights](/azure/application-insights/app-insights-overview) é um serviço do Azure para analisar o desempenho e estabilidade de aplicativos web e como os usuários usá-los.</span><span class="sxs-lookup"><span data-stu-id="8255f-134">[Application Insights](/azure/application-insights/app-insights-overview) is an Azure service for analyzing the performance and stability of web apps and how users use them.</span></span> <span data-ttu-id="8255f-135">Os dados do Application Insights são mais amplos e aprofundados do que o Azure Monitor.</span><span class="sxs-lookup"><span data-stu-id="8255f-135">The data from Application Insights is broader and deeper than that of Azure Monitor.</span></span> <span data-ttu-id="8255f-136">Os dados podem fornecer os desenvolvedores e administradores com informações de chave para aprimorar aplicativos.</span><span class="sxs-lookup"><span data-stu-id="8255f-136">The data can provide developers and administrators with key information for improving apps.</span></span> <span data-ttu-id="8255f-137">Application Insights podem ser adicionados a um recurso de serviço de aplicativo do Azure sem alterações de código.</span><span class="sxs-lookup"><span data-stu-id="8255f-137">Application Insights can be added to an Azure App Service resource without code changes.</span></span>

1. <span data-ttu-id="8255f-138">Abra o [portal do Azure](https://portal.azure.com)e, em seguida, navegue até a *mywebapp\<unique_number\>*  o serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8255f-138">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="8255f-139">Dos **visão geral** , clique o **Application Insights** lado a lado.</span><span class="sxs-lookup"><span data-stu-id="8255f-139">From the **Overview** tab, click the **Application Insights** tile.</span></span>

    ![Bloco do Application Insights](./media/monitoring/app-insights.png)

1. <span data-ttu-id="8255f-141">Selecione o **criar novo recurso** botão de opção.</span><span class="sxs-lookup"><span data-stu-id="8255f-141">Select the **Create new resource** radio button.</span></span> <span data-ttu-id="8255f-142">Use o nome de recurso padrão e selecione o local para o recurso Application Insights.</span><span class="sxs-lookup"><span data-stu-id="8255f-142">Use the default resource name, and select the location for the Application Insights resource.</span></span> <span data-ttu-id="8255f-143">O local não precisa corresponder ao que um aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="8255f-143">The location doesn't need to match that of your web app.</span></span>

    ![O programa de instalação do Application Insights](./media/monitoring/new-app-insights.png)

1. <span data-ttu-id="8255f-145">Para **tempo de execução/estrutura**, selecione **ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="8255f-145">For **Runtime/Framework**, select **ASP.NET Core**.</span></span> <span data-ttu-id="8255f-146">Aceite as configurações padrão.</span><span class="sxs-lookup"><span data-stu-id="8255f-146">Accept the default settings.</span></span>
1. <span data-ttu-id="8255f-147">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="8255f-147">Select **OK**.</span></span> <span data-ttu-id="8255f-148">Se solicitado a confirmar, selecione **continuar**.</span><span class="sxs-lookup"><span data-stu-id="8255f-148">If prompted to confirm, select **Continue**.</span></span>
1. <span data-ttu-id="8255f-149">Depois que o recurso tiver sido criado, clique no nome do recurso do Application Insights para navegar diretamente até a página do Application Insights.</span><span class="sxs-lookup"><span data-stu-id="8255f-149">After the resource has been created, click the name of Application Insights resource to navigate directly to the Application Insights page.</span></span>

    ![Novo recurso do Application Insights está pronto](./media/monitoring/new-app-insights-done.png)

<span data-ttu-id="8255f-151">Como o aplicativo é usado, os dados acumulam.</span><span class="sxs-lookup"><span data-stu-id="8255f-151">As the app is used, data accumulates.</span></span> <span data-ttu-id="8255f-152">Selecione **Refresh** para recarregar a folha com novos dados.</span><span class="sxs-lookup"><span data-stu-id="8255f-152">Select **Refresh** to reload the blade with new data.</span></span>

![Guia de visão geral do Application Insights](./media/monitoring/app-insights-overview.png)

<span data-ttu-id="8255f-154">O Application Insights fornece informações úteis do lado do servidor sem nenhuma configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="8255f-154">Application Insights provides useful server-side information with no additional configuration.</span></span> <span data-ttu-id="8255f-155">Para obter o máximo valor do Application Insights [instrumentar seu aplicativo com o SDK do Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="8255f-155">To get the most value from Application Insights, [instrument your app with the Application Insights SDK](/azure/application-insights/app-insights-asp-net-core).</span></span> <span data-ttu-id="8255f-156">Quando configurado corretamente, o serviço fornece monitoramento de ponta a ponta entre o servidor web e o navegador, incluindo desempenho do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="8255f-156">When properly configured, the service provides end-to-end monitoring across the web server and browser, including client-side performance.</span></span> <span data-ttu-id="8255f-157">Para obter mais informações, consulte o [documentação do Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="8255f-157">For more information, see the [Application Insights documentation](/azure/application-insights/app-insights-overview).</span></span>

## <a name="logging"></a><span data-ttu-id="8255f-158">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="8255f-158">Logging</span></span>

<span data-ttu-id="8255f-159">Logs de servidor e aplicativo da Web estão desabilitados por padrão no serviço de aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="8255f-159">Web server and app logs are disabled by default in Azure App Service.</span></span> <span data-ttu-id="8255f-160">Habilite os logs com as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="8255f-160">Enable the logs with the following steps:</span></span>

1. <span data-ttu-id="8255f-161">Abra o [portal do Azure](https://portal.azure.com)e navegue até a *mywebapp\<unique_number\>*  o serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8255f-161">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="8255f-162">No menu à esquerda, role para baixo até a **monitoramento** seção.</span><span class="sxs-lookup"><span data-stu-id="8255f-162">In the menu to the left, scroll down to the **Monitoring** section.</span></span> <span data-ttu-id="8255f-163">Selecione **logs de diagnóstico**.</span><span class="sxs-lookup"><span data-stu-id="8255f-163">Select **Diagnostics logs**.</span></span>

    ![Link de logs de diagnóstico](./media/monitoring/logging.png)

1. <span data-ttu-id="8255f-165">Ative **log de aplicativo (Filesystem)** .</span><span class="sxs-lookup"><span data-stu-id="8255f-165">Turn on **Application Logging (Filesystem)**.</span></span> <span data-ttu-id="8255f-166">Se solicitado, clique na caixa para instalar as extensões para habilitar o registro em log no aplicativo web do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8255f-166">If prompted, click the box to install the extensions to enable app logging in the web app.</span></span>
1. <span data-ttu-id="8255f-167">Definir **log do servidor Web** à **sistema de arquivos**.</span><span class="sxs-lookup"><span data-stu-id="8255f-167">Set **Web server logging** to **File System**.</span></span>
1. <span data-ttu-id="8255f-168">Insira o **período de retenção** em dias.</span><span class="sxs-lookup"><span data-stu-id="8255f-168">Enter the **Retention Period** in days.</span></span> <span data-ttu-id="8255f-169">Por exemplo, 30.</span><span class="sxs-lookup"><span data-stu-id="8255f-169">For example, 30.</span></span>
1. <span data-ttu-id="8255f-170">Clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="8255f-170">Click **Save**.</span></span>

<span data-ttu-id="8255f-171">Logs do servidor (serviço de aplicativo) do ASP.NET Core e da web são gerados para o aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="8255f-171">ASP.NET Core and web server (App Service) logs are generated for the web app.</span></span> <span data-ttu-id="8255f-172">Eles podem ser baixados usando as informações de FTP/FTPS exibidas.</span><span class="sxs-lookup"><span data-stu-id="8255f-172">They can be downloaded using the FTP/FTPS information displayed.</span></span> <span data-ttu-id="8255f-173">A senha é o mesmo que as credenciais de implantação criadas anteriormente neste guia.</span><span class="sxs-lookup"><span data-stu-id="8255f-173">The password is the same as the deployment credentials created earlier in this guide.</span></span> <span data-ttu-id="8255f-174">Os logs podem ser [transmitido diretamente ao seu computador local com o PowerShell ou CLI do Azure](/azure/app-service/web-sites-enable-diagnostic-log#download).</span><span class="sxs-lookup"><span data-stu-id="8255f-174">The logs can be [streamed directly to your local machine with PowerShell or Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download).</span></span> <span data-ttu-id="8255f-175">Os logs também podem ser [exibidos no Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span><span class="sxs-lookup"><span data-stu-id="8255f-175">Logs can also be [viewed in Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span></span>

## <a name="log-streaming"></a><span data-ttu-id="8255f-176">Streaming de log</span><span class="sxs-lookup"><span data-stu-id="8255f-176">Log streaming</span></span>

<span data-ttu-id="8255f-177">Logs do servidor web e de aplicativo podem ser transmitidos em tempo real por meio do portal.</span><span class="sxs-lookup"><span data-stu-id="8255f-177">App and web server logs can be streamed in real time through the portal.</span></span>

1. <span data-ttu-id="8255f-178">Abra o [portal do Azure](https://portal.azure.com)e navegue até a *mywebapp\<unique_number\>*  o serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8255f-178">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="8255f-179">No menu à esquerda, role para baixo até a **Monitoring** seção e selecione **fluxo de Log**.</span><span class="sxs-lookup"><span data-stu-id="8255f-179">In the menu to the left, scroll down to the **Monitoring** section and select **Log stream**.</span></span>

    ![Captura de tela mostrando o link de fluxo de log](./media/monitoring/log-stream.png)

<span data-ttu-id="8255f-181">Os logs também podem ser [transmitido por meio da CLI do Azure ou o Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), incluindo por meio do Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="8255f-181">Logs can also be [streamed via Azure CLI or Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), including through the Cloud Shell.</span></span>

## <a name="alerts"></a><span data-ttu-id="8255f-182">Alertas</span><span class="sxs-lookup"><span data-stu-id="8255f-182">Alerts</span></span>

<span data-ttu-id="8255f-183">O Azure Monitor também fornece [alertas em tempo real](/azure/monitoring-and-diagnostics/insights-alerts-portal) com base em métricas, eventos administrativos e outros critérios.</span><span class="sxs-lookup"><span data-stu-id="8255f-183">Azure Monitor also provides [real time alerts](/azure/monitoring-and-diagnostics/insights-alerts-portal) based on metrics, administrative events, and other criteria.</span></span>

> <span data-ttu-id="8255f-184">*Observação: Atualmente, os alertas em métricas de aplicativo Web só estão disponíveis no serviço alertas (clássico).*</span><span class="sxs-lookup"><span data-stu-id="8255f-184">*Note: Currently alerting on web app metrics is only available in the Alerts (classic) service.*</span></span>

<span data-ttu-id="8255f-185">O [alertas de serviço (clássico)](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) pode ser encontrada no Azure Monitor ou sob o **monitoramento** seção das configurações de serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8255f-185">The [Alerts (classic) service](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) can be found in Azure Monitor or under the **Monitoring** section of the App Service settings.</span></span>

![Link de alertas (clássico)](./media/monitoring/alerts.png)

## <a name="live-debugging"></a><span data-ttu-id="8255f-187">Depuração ao vivo</span><span class="sxs-lookup"><span data-stu-id="8255f-187">Live debugging</span></span>

<span data-ttu-id="8255f-188">O serviço de aplicativo do Azure pode ser [depurado remotamente com o Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) quando os logs não fornecer informações suficientes.</span><span class="sxs-lookup"><span data-stu-id="8255f-188">Azure App Service can be [debugged remotely with Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) when logs don't provide enough information.</span></span> <span data-ttu-id="8255f-189">No entanto, a depuração remota exige o aplicativo a ser compilada com símbolos de depuração.</span><span class="sxs-lookup"><span data-stu-id="8255f-189">However, remote debugging requires the app to be compiled with debug symbols.</span></span> <span data-ttu-id="8255f-190">Depuração não deve ser feita em produção, exceto como último recurso.</span><span class="sxs-lookup"><span data-stu-id="8255f-190">Debugging shouldn't be done in production, except as a last resort.</span></span>

## <a name="conclusion"></a><span data-ttu-id="8255f-191">Conclusão</span><span class="sxs-lookup"><span data-stu-id="8255f-191">Conclusion</span></span>

<span data-ttu-id="8255f-192">Nesta seção, você concluiu as seguintes tarefas:</span><span class="sxs-lookup"><span data-stu-id="8255f-192">In this section, you completed the following tasks:</span></span>

* <span data-ttu-id="8255f-193">Localizar básicos de monitoramento e solução de problemas de dados no portal do Azure</span><span class="sxs-lookup"><span data-stu-id="8255f-193">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="8255f-194">Saiba como o Azure Monitor fornece uma análise mais profunda das métricas entre todos os serviços do Azure</span><span class="sxs-lookup"><span data-stu-id="8255f-194">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="8255f-195">Conectar o aplicativo web com o Application Insights para a criação de perfil de aplicativo</span><span class="sxs-lookup"><span data-stu-id="8255f-195">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="8255f-196">Ativar o registro em log e saiba onde baixar logs</span><span class="sxs-lookup"><span data-stu-id="8255f-196">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="8255f-197">Stream logs em tempo real</span><span class="sxs-lookup"><span data-stu-id="8255f-197">Stream logs in real time</span></span>
* <span data-ttu-id="8255f-198">Saiba onde configurar alertas</span><span class="sxs-lookup"><span data-stu-id="8255f-198">Learn where to set up alerts</span></span>
* <span data-ttu-id="8255f-199">Saiba mais sobre aplicativos de web do serviço de aplicativo do Azure depuração remotos.</span><span class="sxs-lookup"><span data-stu-id="8255f-199">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="8255f-200">Leitura adicional</span><span class="sxs-lookup"><span data-stu-id="8255f-200">Additional reading</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="8255f-201">Monitorar o desempenho do aplicativo web do Azure com o Application Insights</span><span class="sxs-lookup"><span data-stu-id="8255f-201">Monitor Azure web app performance with Application Insights</span></span>](/azure/application-insights/app-insights-azure-web-apps)
* [<span data-ttu-id="8255f-202">Habilitar log de diagnósticos para aplicativos Web no Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="8255f-202">Enable diagnostics logging for web apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)
* [<span data-ttu-id="8255f-203">Solucionar problemas de um aplicativo Web no Serviço de Aplicativo do Azure usando o Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8255f-203">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="8255f-204">Criar alertas de métrica clássicos no Azure Monitor para serviços do Azure - portal do Azure</span><span class="sxs-lookup"><span data-stu-id="8255f-204">Create classic metric alerts in Azure Monitor for Azure services - Azure portal</span></span>](/azure/monitoring-and-diagnostics/insights-alerts-portal)
