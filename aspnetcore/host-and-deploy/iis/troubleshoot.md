---
title: Solucionar problemas do ASP.NET Core no IIS
author: guardrex
description: Saiba como diagnosticar problemas com as implantações do IIS (Serviços de Informações da Internet) de aplicativos do ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/06/2019
uid: host-and-deploy/iis/troubleshoot
ms.openlocfilehash: 2f36ae2bda8537e91a3bc925505986bdd6a22a47
ms.sourcegitcommit: 34bf9fc6ea814c039401fca174642f0acb14be3c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/14/2019
ms.locfileid: "57841547"
---
# <a name="troubleshoot-aspnet-core-on-iis"></a><span data-ttu-id="9637d-103">Solucionar problemas do ASP.NET Core no IIS</span><span class="sxs-lookup"><span data-stu-id="9637d-103">Troubleshoot ASP.NET Core on IIS</span></span>

<span data-ttu-id="9637d-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9637d-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9637d-105">Este artigo fornece instruções sobre como diagnosticar um problema de inicialização do aplicativo ASP.NET Core ao hospedar com [IIS (Serviços de Informações da Internet)](/iis).</span><span class="sxs-lookup"><span data-stu-id="9637d-105">This article provides instructions on how to diagnose an ASP.NET Core app startup issue when hosting with [Internet Information Services (IIS)](/iis).</span></span> <span data-ttu-id="9637d-106">As informações neste artigo se aplicam à hospedagem em IIS no Windows Server e Windows Desktop.</span><span class="sxs-lookup"><span data-stu-id="9637d-106">The information in this article applies to hosting in IIS on Windows Server and Windows Desktop.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="9637d-107">No Visual Studio, um projeto do ASP.NET Core usa por padrão a hospedagem do [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) durante a depuração.</span><span class="sxs-lookup"><span data-stu-id="9637d-107">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="9637d-108">Uma *502.5 – falha de processo* ou uma *500.30 – falha de inicialização* que ocorre ao depurar localmente pode ser solucionada usando as recomendações presentes neste tópico.</span><span class="sxs-lookup"><span data-stu-id="9637d-108">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be troubleshooted using the advice in this topic.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="9637d-109">No Visual Studio, um projeto do ASP.NET Core usa por padrão a hospedagem do [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) durante a depuração.</span><span class="sxs-lookup"><span data-stu-id="9637d-109">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="9637d-110">Uma *502.5 – Falha de Processo* que ocorre ao depurar localmente pode ser solucionada usando as recomendações presentes neste tópico.</span><span class="sxs-lookup"><span data-stu-id="9637d-110">A *502.5 Process Failure* that occurs when debugging locally can be troubleshooted using the advice in this topic.</span></span>

::: moniker-end

<span data-ttu-id="9637d-111">Tópicos adicionais de solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="9637d-111">Additional troubleshooting topics:</span></span>

<xref:host-and-deploy/azure-apps/troubleshoot>  
<span data-ttu-id="9637d-112">Embora o Serviço de Aplicativo use o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) e o IIS para hospedar aplicativos, veja o tópico dedicado para obter instruções específicas para o Serviço de Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9637d-112">Although App Service uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and IIS to host apps, see the dedicated topic for instructions specific to App Service.</span></span>

<xref:fundamentals/error-handling>  
<span data-ttu-id="9637d-113">Descubra como tratar erros em aplicativos do ASP.NET Core durante o desenvolvimento em um sistema local.</span><span class="sxs-lookup"><span data-stu-id="9637d-113">Discover how to handle errors in ASP.NET Core apps during development on a local system.</span></span>

[<span data-ttu-id="9637d-114">Aprenda a depurar usando o Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9637d-114">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)  
<span data-ttu-id="9637d-115">Este tópico apresenta os recursos do depurador do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9637d-115">This topic introduces the features of the Visual Studio debugger.</span></span>

[<span data-ttu-id="9637d-116">Depurar com o Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9637d-116">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)  
<span data-ttu-id="9637d-117">Saiba mais sobre o suporte de depuração interno do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="9637d-117">Learn about the debugging support built into Visual Studio Code.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="9637d-118">Erros de inicialização do aplicativo</span><span class="sxs-lookup"><span data-stu-id="9637d-118">App startup errors</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="9637d-119">502.5 Falha de processo</span><span class="sxs-lookup"><span data-stu-id="9637d-119">502.5 Process Failure</span></span>

<span data-ttu-id="9637d-120">O processo de trabalho falha.</span><span class="sxs-lookup"><span data-stu-id="9637d-120">The worker process fails.</span></span> <span data-ttu-id="9637d-121">O aplicativo não foi iniciado.</span><span class="sxs-lookup"><span data-stu-id="9637d-121">The app doesn't start.</span></span>

<span data-ttu-id="9637d-122">O Módulo do ASP.NET Core tenta iniciar o processo dotnet de back-end, mas falha ao iniciar.</span><span class="sxs-lookup"><span data-stu-id="9637d-122">The ASP.NET Core Module attempts to start the backend dotnet process but it fails to start.</span></span> <span data-ttu-id="9637d-123">A causa de uma falha de inicialização do processo geralmente pode ser determinada com base em entradas no [Log de Eventos do Aplicativo](#application-event-log) e no [log de stdout do Módulo do ASP.NET Core](#aspnet-core-module-stdout-log).</span><span class="sxs-lookup"><span data-stu-id="9637d-123">The cause of a process startup failure can usually be determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span> 

<span data-ttu-id="9637d-124">Uma condição de falha comum é o aplicativo configurado incorretamente, direcionado a uma versão da estrutura compartilhada do ASP.NET Core que não está presente.</span><span class="sxs-lookup"><span data-stu-id="9637d-124">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="9637d-125">Verifique quais versões da estrutura compartilhada do ASP.NET Core estão instaladas no computador de destino.</span><span class="sxs-lookup"><span data-stu-id="9637d-125">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>

<span data-ttu-id="9637d-126">A página do erro *502.5 – Falha no Processo* é retornada quando um erro de configuração de hospedagem ou do aplicativo faz com que o processo de trabalho falhe:</span><span class="sxs-lookup"><span data-stu-id="9637d-126">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

![Janela do navegador mostrando a página 502.5 – Falha no Processo](troubleshoot/_static/process-failure-page.png)

::: moniker range=">= aspnetcore-2.2"

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="9637d-128">500.30 Falha de inicialização em processo</span><span class="sxs-lookup"><span data-stu-id="9637d-128">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="9637d-129">O processo de trabalho falha.</span><span class="sxs-lookup"><span data-stu-id="9637d-129">The worker process fails.</span></span> <span data-ttu-id="9637d-130">O aplicativo não foi iniciado.</span><span class="sxs-lookup"><span data-stu-id="9637d-130">The app doesn't start.</span></span>

<span data-ttu-id="9637d-131">O Módulo do ASP.NET Core tenta iniciar o CLR do .NET Core em processo, mas falha ao iniciar.</span><span class="sxs-lookup"><span data-stu-id="9637d-131">The ASP.NET Core Module attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="9637d-132">A causa de uma falha de inicialização do processo geralmente pode ser determinada com base em entradas no [Log de Eventos do Aplicativo](#application-event-log) e no [log de stdout do Módulo do ASP.NET Core](#aspnet-core-module-stdout-log).</span><span class="sxs-lookup"><span data-stu-id="9637d-132">The cause of a process startup failure can usually be determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span> 

<span data-ttu-id="9637d-133">Uma condição de falha comum é o aplicativo configurado incorretamente, direcionado a uma versão da estrutura compartilhada do ASP.NET Core que não está presente.</span><span class="sxs-lookup"><span data-stu-id="9637d-133">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="9637d-134">Verifique quais versões da estrutura compartilhada do ASP.NET Core estão instaladas no computador de destino.</span><span class="sxs-lookup"><span data-stu-id="9637d-134">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="9637d-135">500.0 Falha de carregamento de manipulador em processo</span><span class="sxs-lookup"><span data-stu-id="9637d-135">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="9637d-136">O processo de trabalho falha.</span><span class="sxs-lookup"><span data-stu-id="9637d-136">The worker process fails.</span></span> <span data-ttu-id="9637d-137">O aplicativo não foi iniciado.</span><span class="sxs-lookup"><span data-stu-id="9637d-137">The app doesn't start.</span></span>

<span data-ttu-id="9637d-138">O Módulo do ASP.NET Core falha ao encontrar o CLR do .NET Core e o manipulador de solicitação em processo (*aspnetcorev2_inprocess.dll*).</span><span class="sxs-lookup"><span data-stu-id="9637d-138">The ASP.NET Core Module fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="9637d-139">Verifique se:</span><span class="sxs-lookup"><span data-stu-id="9637d-139">Check that:</span></span>

* <span data-ttu-id="9637d-140">O aplicativo destina-se ao pacote NuGet [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) ou ao [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="9637d-140">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="9637d-141">A versão da estrutura compartilhada do ASP.NET Core a que o aplicativo se destina está instalada no computador de destino.</span><span class="sxs-lookup"><span data-stu-id="9637d-141">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="9637d-142">500.0 Falha de carregamento de manipulador fora de processo</span><span class="sxs-lookup"><span data-stu-id="9637d-142">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="9637d-143">O processo de trabalho falha.</span><span class="sxs-lookup"><span data-stu-id="9637d-143">The worker process fails.</span></span> <span data-ttu-id="9637d-144">O aplicativo não foi iniciado.</span><span class="sxs-lookup"><span data-stu-id="9637d-144">The app doesn't start.</span></span>

<span data-ttu-id="9637d-145">O Módulo do ASP.NET Core falha ao encontrar o manipulador de solicitações de hospedagem de fora do processo.</span><span class="sxs-lookup"><span data-stu-id="9637d-145">The ASP.NET Core Module fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="9637d-146">Verifique se a *aspnetcorev2_outofprocess.dll* está presente em uma subpasta próxima a *aspnetcorev2.dll*.</span><span class="sxs-lookup"><span data-stu-id="9637d-146">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span> 

::: moniker-end

### <a name="500-internal-server-error"></a><span data-ttu-id="9637d-147">500 Erro Interno do Servidor</span><span class="sxs-lookup"><span data-stu-id="9637d-147">500 Internal Server Error</span></span>

<span data-ttu-id="9637d-148">O aplicativo é iniciado, mas um erro impede o servidor de atender à solicitação.</span><span class="sxs-lookup"><span data-stu-id="9637d-148">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="9637d-149">Esse erro ocorre no código do aplicativo durante a inicialização ou durante a criação de uma resposta.</span><span class="sxs-lookup"><span data-stu-id="9637d-149">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="9637d-150">A resposta poderá não conter nenhum conteúdo, ou a resposta poderá ser exibida como um *500 – Erro Interno do Servidor* no navegador.</span><span class="sxs-lookup"><span data-stu-id="9637d-150">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="9637d-151">O Log de Eventos do Aplicativo geralmente indica que o aplicativo iniciou normalmente.</span><span class="sxs-lookup"><span data-stu-id="9637d-151">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="9637d-152">Da perspectiva do servidor, isso está correto.</span><span class="sxs-lookup"><span data-stu-id="9637d-152">From the server's perspective, that's correct.</span></span> <span data-ttu-id="9637d-153">O aplicativo foi iniciado, mas não é capaz de gerar uma resposta válida.</span><span class="sxs-lookup"><span data-stu-id="9637d-153">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="9637d-154">[Execute o aplicativo em um prompt de comando](#run-the-app-at-a-command-prompt) no servidor ou [habilite o log de stdout do Módulo do ASP.NET Core](#aspnet-core-module-stdout-log) para solucionar o problema.</span><span class="sxs-lookup"><span data-stu-id="9637d-154">[Run the app at a command prompt](#run-the-app-at-a-command-prompt) on the server or [enable the ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log) to troubleshoot the problem.</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="9637d-155">Falha ao iniciar o aplicativo (ErrorCode '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="9637d-155">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="9637d-156">O aplicativo falhou ao ser iniciado porque o assembly do aplicativo (*.dll*) não pôde ser carregado.</span><span class="sxs-lookup"><span data-stu-id="9637d-156">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="9637d-157">Esse erro ocorre quando há uma incompatibilidade de número de bits entre o aplicativo publicado e o processo w3wp/iisexpress.</span><span class="sxs-lookup"><span data-stu-id="9637d-157">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="9637d-158">Confirme se a configuração de 32 bits do pool de aplicativos está correta:</span><span class="sxs-lookup"><span data-stu-id="9637d-158">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="9637d-159">Selecione o pool de aplicativos no **Pools de Aplicativos** do Gerenciador do IIS.</span><span class="sxs-lookup"><span data-stu-id="9637d-159">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="9637d-160">Selecione **Configurações Avançadas** em **Editar Pool de Aplicativos** no painel **Ações**.</span><span class="sxs-lookup"><span data-stu-id="9637d-160">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="9637d-161">Defina **Habilitar Aplicativos de 32 bits**:</span><span class="sxs-lookup"><span data-stu-id="9637d-161">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="9637d-162">Se estiver implantando um aplicativo de 32 bits (x86), defina o valor como `True`.</span><span class="sxs-lookup"><span data-stu-id="9637d-162">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="9637d-163">Se estiver implantando um aplicativo de 64 bits (x64), defina o valor como `False`.</span><span class="sxs-lookup"><span data-stu-id="9637d-163">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="9637d-164">Redefinição de conexão</span><span class="sxs-lookup"><span data-stu-id="9637d-164">Connection reset</span></span>

<span data-ttu-id="9637d-165">Se um erro ocorrer após os cabeçalhos serem enviados, será tarde demais para o servidor enviar um **500 – Erro Interno do Servidor** no caso de um erro ocorrer.</span><span class="sxs-lookup"><span data-stu-id="9637d-165">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="9637d-166">Isso geralmente acontece quando ocorre um erro durante a serialização de objetos complexos para uma resposta.</span><span class="sxs-lookup"><span data-stu-id="9637d-166">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="9637d-167">Esse tipo de erro é exibida como um erro de *redefinição de conexão* no cliente.</span><span class="sxs-lookup"><span data-stu-id="9637d-167">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="9637d-168">O [Log de aplicativo](xref:fundamentals/logging/index) pode ajudar a solucionar esses tipos de erros.</span><span class="sxs-lookup"><span data-stu-id="9637d-168">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

## <a name="default-startup-limits"></a><span data-ttu-id="9637d-169">Limites de inicialização padrão</span><span class="sxs-lookup"><span data-stu-id="9637d-169">Default startup limits</span></span>

<span data-ttu-id="9637d-170">O Módulo do ASP.NET Core está configurado com um *startupTimeLimit* padrão de 120 segundos.</span><span class="sxs-lookup"><span data-stu-id="9637d-170">The ASP.NET Core Module is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="9637d-171">Quando deixado no valor padrão, um aplicativo pode levar até dois minutos para iniciar antes que uma falha do processo seja registrada em log pelo módulo.</span><span class="sxs-lookup"><span data-stu-id="9637d-171">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="9637d-172">Para obter informações sobre como configurar o módulo, veja [Atributos do elemento aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="9637d-172">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-app-startup-errors"></a><span data-ttu-id="9637d-173">Solucionar problemas de inicialização do aplicativo</span><span class="sxs-lookup"><span data-stu-id="9637d-173">Troubleshoot app startup errors</span></span>

### <a name="enable-the-aspnet-core-module-debug-log"></a><span data-ttu-id="9637d-174">Habilitar o log de depuração do Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9637d-174">Enable the ASP.NET Core Module debug log</span></span>

<span data-ttu-id="9637d-175">Adicione as seguintes configurações de manipulador ao arquivo *web.config* do aplicativo para habilitar os logs de depuração do Módulo do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="9637d-175">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug logs:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="9637d-176">Confirme se o caminho especificado para o log existe e se a identidade do pool de aplicativos tem permissões de gravação no local.</span><span class="sxs-lookup"><span data-stu-id="9637d-176">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

### <a name="application-event-log"></a><span data-ttu-id="9637d-177">Log de Eventos do Aplicativo</span><span class="sxs-lookup"><span data-stu-id="9637d-177">Application Event Log</span></span>

<span data-ttu-id="9637d-178">Acesse o Log de Eventos do Aplicativo:</span><span class="sxs-lookup"><span data-stu-id="9637d-178">Access the Application Event Log:</span></span>

1. <span data-ttu-id="9637d-179">Abra o menu Iniciar, procure **Visualizador de Eventos** e, em seguida, selecione o aplicativo **Visualizador de Eventos**.</span><span class="sxs-lookup"><span data-stu-id="9637d-179">Open the Start menu, search for **Event Viewer**, and then select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="9637d-180">No **Visualizador de Eventos**, abra o nó **Logs do Windows**.</span><span class="sxs-lookup"><span data-stu-id="9637d-180">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="9637d-181">Selecione **Aplicativo** para abrir o Log de Eventos do Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9637d-181">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="9637d-182">Procure erros associados ao aplicativo com falha.</span><span class="sxs-lookup"><span data-stu-id="9637d-182">Search for errors associated with the failing app.</span></span> <span data-ttu-id="9637d-183">Os erros têm um valor *Módulo AspNetCore do IIS* ou *Módulo AspNetCore do IIS Express* na coluna *Origem*.</span><span class="sxs-lookup"><span data-stu-id="9637d-183">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="9637d-184">Execute o aplicativo em um prompt de comando</span><span class="sxs-lookup"><span data-stu-id="9637d-184">Run the app at a command prompt</span></span>

<span data-ttu-id="9637d-185">Muitos erros de inicialização não produzem informações úteis no Log de Eventos do Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9637d-185">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="9637d-186">Você pode encontrar a causa de alguns erros ao executar o aplicativo em um prompt de comando no sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="9637d-186">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="9637d-187">Implantação dependente de estrutura</span><span class="sxs-lookup"><span data-stu-id="9637d-187">Framework-dependent deployment</span></span>

<span data-ttu-id="9637d-188">Se o aplicativo é uma [implantação dependente de estrutura](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="9637d-188">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="9637d-189">Em um prompt de comando, navegue até a pasta de implantação e execute o aplicativo, executando o assembly do aplicativo com *dotnet.exe*.</span><span class="sxs-lookup"><span data-stu-id="9637d-189">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="9637d-190">No comando a seguir, substitua o nome do assembly do aplicativo por \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span><span class="sxs-lookup"><span data-stu-id="9637d-190">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="9637d-191">A saída do console do aplicativo, mostrando eventuais erros, é gravada na janela do console.</span><span class="sxs-lookup"><span data-stu-id="9637d-191">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="9637d-192">Se os erros ocorrerem ao fazer uma solicitação para o aplicativo, faça uma solicitação para o host e a porta em que o Kestrel escuta.</span><span class="sxs-lookup"><span data-stu-id="9637d-192">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="9637d-193">Usando o host e a porta padrão, faça uma solicitação para `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="9637d-193">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="9637d-194">Se o aplicativo responde normalmente no endereço do ponto de extremidade do Kestrel, a probabilidade de o problema estar relacionado à configuração de hospedagem é maior e, de estar relacionado ao aplicativo, menor.</span><span class="sxs-lookup"><span data-stu-id="9637d-194">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="9637d-195">Implantação autocontida</span><span class="sxs-lookup"><span data-stu-id="9637d-195">Self-contained deployment</span></span>

<span data-ttu-id="9637d-196">Se o aplicativo é uma [implantação autossuficiente](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="9637d-196">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="9637d-197">Em um prompt de comando, navegue até a pasta de implantação e execute o arquivo executável do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9637d-197">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="9637d-198">No comando a seguir, substitua o nome do assembly do aplicativo por \<assembly_name>: `<assembly_name>.exe`.</span><span class="sxs-lookup"><span data-stu-id="9637d-198">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="9637d-199">A saída do console do aplicativo, mostrando eventuais erros, é gravada na janela do console.</span><span class="sxs-lookup"><span data-stu-id="9637d-199">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="9637d-200">Se os erros ocorrerem ao fazer uma solicitação para o aplicativo, faça uma solicitação para o host e a porta em que o Kestrel escuta.</span><span class="sxs-lookup"><span data-stu-id="9637d-200">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="9637d-201">Usando o host e a porta padrão, faça uma solicitação para `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="9637d-201">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="9637d-202">Se o aplicativo responde normalmente no endereço do ponto de extremidade do Kestrel, a probabilidade de o problema estar relacionado à configuração de hospedagem é maior e, de estar relacionado ao aplicativo, menor.</span><span class="sxs-lookup"><span data-stu-id="9637d-202">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log"></a><span data-ttu-id="9637d-203">Log de stdout do Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9637d-203">ASP.NET Core Module stdout log</span></span>

<span data-ttu-id="9637d-204">Para habilitar e exibir logs de stdout:</span><span class="sxs-lookup"><span data-stu-id="9637d-204">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="9637d-205">Navegue até a pasta de implantação do site no sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="9637d-205">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="9637d-206">Se a pasta *logs* não estiver presente, crie-a.</span><span class="sxs-lookup"><span data-stu-id="9637d-206">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="9637d-207">Para obter instruções sobre como habilitar o MSBuild para criar a pasta *logs* na implantação automaticamente, veja o tópico [Estrutura de diretórios](xref:host-and-deploy/directory-structure).</span><span class="sxs-lookup"><span data-stu-id="9637d-207">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="9637d-208">Edite o arquivo *web.config*.</span><span class="sxs-lookup"><span data-stu-id="9637d-208">Edit the *web.config* file.</span></span> <span data-ttu-id="9637d-209">Defina **stdoutLogEnabled** para `true` e altere o caminho **stdoutLogFile** para apontar para a pasta *logs* (por exemplo, `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="9637d-209">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="9637d-210">`stdout` no caminho é o prefixo do nome do arquivo de log.</span><span class="sxs-lookup"><span data-stu-id="9637d-210">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="9637d-211">Uma extensão de arquivo, uma ID do processo e um carimbo de data/hora são adicionados automaticamente quando o log é criado.</span><span class="sxs-lookup"><span data-stu-id="9637d-211">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="9637d-212">Usando `stdout` como o prefixo do nome do arquivo, um arquivo de log típico é nomeado *stdout_20180205184032_5412.log*.</span><span class="sxs-lookup"><span data-stu-id="9637d-212">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span> 
1. <span data-ttu-id="9637d-213">Verifique se a identidade do pool de aplicativos tem permissões de gravação para a pasta *logs*.</span><span class="sxs-lookup"><span data-stu-id="9637d-213">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="9637d-214">Salve o arquivo *web.config* atualizado.</span><span class="sxs-lookup"><span data-stu-id="9637d-214">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="9637d-215">Faça uma solicitação ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9637d-215">Make a request to the app.</span></span>
1. <span data-ttu-id="9637d-216">Navegue até a pasta *logs*.</span><span class="sxs-lookup"><span data-stu-id="9637d-216">Navigate to the *logs* folder.</span></span> <span data-ttu-id="9637d-217">Localize e abra o log de stdout mais recente.</span><span class="sxs-lookup"><span data-stu-id="9637d-217">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="9637d-218">Estude o log em busca de erros.</span><span class="sxs-lookup"><span data-stu-id="9637d-218">Study the log for errors.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="9637d-219">Desabilite o registro em log de stdout quando a solução de problemas for concluída.</span><span class="sxs-lookup"><span data-stu-id="9637d-219">Disable stdout logging when troubleshooting is complete.</span></span>

1. <span data-ttu-id="9637d-220">Edite o arquivo *web.config*.</span><span class="sxs-lookup"><span data-stu-id="9637d-220">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="9637d-221">Defina **stdoutLogEnabled** para `false`.</span><span class="sxs-lookup"><span data-stu-id="9637d-221">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="9637d-222">Salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="9637d-222">Save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="9637d-223">Falha ao desabilitar o log de stdout pode levar a falhas de aplicativo ou de servidor.</span><span class="sxs-lookup"><span data-stu-id="9637d-223">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="9637d-224">Não há limites para o tamanho do arquivo de log ou para o número de arquivos de log criados.</span><span class="sxs-lookup"><span data-stu-id="9637d-224">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="9637d-225">Para registro em log de rotina em um aplicativo ASP.NET Core, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs.</span><span class="sxs-lookup"><span data-stu-id="9637d-225">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="9637d-226">Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="9637d-226">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="enable-the-developer-exception-page"></a><span data-ttu-id="9637d-227">Habilitar a página de exceção do desenvolvedor</span><span class="sxs-lookup"><span data-stu-id="9637d-227">Enable the Developer Exception Page</span></span>

<span data-ttu-id="9637d-228">A [variável de ambiente `ASPNETCORE_ENVIRONMENT` pode ser adicionada ao web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) para executar o aplicativo no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="9637d-228">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="9637d-229">Desde que o ambiente não seja substituído na inicialização do aplicativo por `UseEnvironment` no compilador do host, definir a variável de ambiente permite que a [Página de Exceções do Desenvolvedor](xref:fundamentals/error-handling) apareça quando o aplicativo é executado.</span><span class="sxs-lookup"><span data-stu-id="9637d-229">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

<span data-ttu-id="9637d-230">Configurar a variável de ambiente para `ASPNETCORE_ENVIRONMENT` só é recomendado para servidores de preparo e de teste que não estejam expostos à Internet.</span><span class="sxs-lookup"><span data-stu-id="9637d-230">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="9637d-231">Remova a variável de ambiente do arquivo *web.config* após a solução de problemas.</span><span class="sxs-lookup"><span data-stu-id="9637d-231">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="9637d-232">Para obter informações sobre como definir variáveis de ambiente no *web.config*, confira [Elemento filho environmentVariables de aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="9637d-232">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

## <a name="common-startup-errors"></a><span data-ttu-id="9637d-233">Erros de inicialização comuns</span><span class="sxs-lookup"><span data-stu-id="9637d-233">Common startup errors</span></span>

<span data-ttu-id="9637d-234">Consulte <xref:host-and-deploy/azure-iis-errors-reference>.</span><span class="sxs-lookup"><span data-stu-id="9637d-234">See <xref:host-and-deploy/azure-iis-errors-reference>.</span></span> <span data-ttu-id="9637d-235">A maioria dos problemas comuns que impedem a inicialização do aplicativo é abordada no tópico de referência.</span><span class="sxs-lookup"><span data-stu-id="9637d-235">Most of the common problems that prevent app startup are covered in the reference topic.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="9637d-236">Obter dados de um aplicativo</span><span class="sxs-lookup"><span data-stu-id="9637d-236">Obtain data from an app</span></span>

<span data-ttu-id="9637d-237">Se um aplicativo for capaz de responder às solicitações, obtenha as solicitações, conexão e dados adicionais do aplicativo que usar o middleware embutido de terminal.</span><span class="sxs-lookup"><span data-stu-id="9637d-237">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="9637d-238">Para saber mais e obter um código de exemplo, consulte <xref:test/troubleshoot#obtain-data-from-an-app>.</span><span class="sxs-lookup"><span data-stu-id="9637d-238">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

## <a name="create-a-dump"></a><span data-ttu-id="9637d-239">Criar um despejo de memória</span><span class="sxs-lookup"><span data-stu-id="9637d-239">Create a dump</span></span>

<span data-ttu-id="9637d-240">Um *despejo de memória* é um instantâneo da memória do sistema e pode ajudar a determinar a causa de uma falha de aplicativo, falha de inicialização ou lentidão de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9637d-240">A *dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="9637d-241">O aplicativo falha ou encontra uma exceção</span><span class="sxs-lookup"><span data-stu-id="9637d-241">App crashes or encounters an exception</span></span>

<span data-ttu-id="9637d-242">Obter e analisar um despejo de memória do [WER (Relatório de Erros do Windows)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="9637d-242">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="9637d-243">Crie uma pasta para armazenar os arquivos de despejo de memória em `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="9637d-243">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="9637d-244">O pool de aplicativos deve ter acesso para gravação à pasta.</span><span class="sxs-lookup"><span data-stu-id="9637d-244">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="9637d-245">Execute o [script EnableDumps do PowerShell](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/troubleshoot/scripts/EnableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="9637d-245">Run the [EnableDumps PowerShell script](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/troubleshoot/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="9637d-246">Se o aplicativo usa o [modelo de hospedagem em processo](xref:fundamentals/servers/index#in-process-hosting-model), execute o script para *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="9637d-246">If the app uses the [in-process hosting model](xref:fundamentals/servers/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```
   * <span data-ttu-id="9637d-247">Se o aplicativo usa o [modelo de hospedagem fora do processo](xref:fundamentals/servers/index#out-of-process-hosting-model), execute o script para *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="9637d-247">If the app uses the [out-of-process hosting model](xref:fundamentals/servers/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```
1. <span data-ttu-id="9637d-248">Execute o aplicativo sob as condições que causam a falha.</span><span class="sxs-lookup"><span data-stu-id="9637d-248">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="9637d-249">Após a falha, execute o [script DisableDumps do PowerShell](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/troubleshoot/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="9637d-249">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/troubleshoot/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="9637d-250">Se o aplicativo usa o [modelo de hospedagem em processo](xref:fundamentals/servers/index#in-process-hosting-model), execute o script para *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="9637d-250">If the app uses the [in-process hosting model](xref:fundamentals/servers/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```
   * <span data-ttu-id="9637d-251">Se o aplicativo usa o [modelo de hospedagem fora do processo](xref:fundamentals/servers/index#out-of-process-hosting-model), execute o script para *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="9637d-251">If the app uses the [out-of-process hosting model](xref:fundamentals/servers/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="9637d-252">Depois que um aplicativo falhar e a coleta de despejo de memória for concluída, o aplicativo terá permissão para encerrar normalmente.</span><span class="sxs-lookup"><span data-stu-id="9637d-252">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="9637d-253">O script do PowerShell configura o WER para coletar até cinco despejos de memória por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9637d-253">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="9637d-254">Os despejos de memória podem usar uma grande quantidade de espaço em disco (até vários gigabytes cada).</span><span class="sxs-lookup"><span data-stu-id="9637d-254">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="9637d-255">O aplicativo trava, falha durante a inicialização ou executa normalmente</span><span class="sxs-lookup"><span data-stu-id="9637d-255">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="9637d-256">Quando um aplicativo *travar* (para de responder, mas não falha), falhar durante a inicialização ou executar normalmente, veja [Arquivos de despejo de memória do modo de usuário: escolher a melhor ferramenta](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) para selecionar uma ferramenta adequada para produzir o despejo de memória.</span><span class="sxs-lookup"><span data-stu-id="9637d-256">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

### <a name="analyze-the-dump"></a><span data-ttu-id="9637d-257">Analisar o despejo de memória</span><span class="sxs-lookup"><span data-stu-id="9637d-257">Analyze the dump</span></span>

<span data-ttu-id="9637d-258">Um despejo de memória pode ser analisado usando várias abordagens.</span><span class="sxs-lookup"><span data-stu-id="9637d-258">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="9637d-259">Para obter mais informações, confira [Analisando um arquivo de despejo de memória do modo de usuário](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="9637d-259">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="remote-debugging"></a><span data-ttu-id="9637d-260">Depuração remota</span><span class="sxs-lookup"><span data-stu-id="9637d-260">Remote debugging</span></span>

<span data-ttu-id="9637d-261">Veja [Depuração remota do ASP.NET Core em um computador de IIS remoto no Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) na documentação do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9637d-261">See [Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) in the Visual Studio documentation.</span></span>

## <a name="application-insights"></a><span data-ttu-id="9637d-262">Informações do aplicativo</span><span class="sxs-lookup"><span data-stu-id="9637d-262">Application Insights</span></span>

<span data-ttu-id="9637d-263">O [Application Insights](/azure/application-insights/) fornece telemetria de aplicativos hospedados pelo IIS, incluindo recursos de relatório e de registro de erros em log.</span><span class="sxs-lookup"><span data-stu-id="9637d-263">[Application Insights](/azure/application-insights/) provides telemetry from apps hosted by IIS, including error logging and reporting features.</span></span> <span data-ttu-id="9637d-264">O Application Insights só pode relatar erros ocorridos depois que o aplicativo é iniciado quando os recursos de registro em log do aplicativo se tornam disponíveis.</span><span class="sxs-lookup"><span data-stu-id="9637d-264">Application Insights can only report on errors that occur after the app starts when the app's logging features become available.</span></span> <span data-ttu-id="9637d-265">Para obter mais informações, veja [Application Insights para ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="9637d-265">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="additional-advice"></a><span data-ttu-id="9637d-266">Orientações adicionais</span><span class="sxs-lookup"><span data-stu-id="9637d-266">Additional advice</span></span>

<span data-ttu-id="9637d-267">Algumas vezes um aplicativo em funcionamento falha imediatamente após atualizar o SDK do .NET Core nas versões de pacote ou de computador de desenvolvimento no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9637d-267">Sometimes a functioning app fails immediately after upgrading either the .NET Core SDK on the development machine or package versions within the app.</span></span> <span data-ttu-id="9637d-268">Em alguns casos, pacotes incoerentes podem interromper um aplicativo ao executar atualizações principais.</span><span class="sxs-lookup"><span data-stu-id="9637d-268">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="9637d-269">A maioria desses problemas pode ser corrigida seguindo estas instruções:</span><span class="sxs-lookup"><span data-stu-id="9637d-269">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="9637d-270">Exclua as pastas *bin* e *obj*.</span><span class="sxs-lookup"><span data-stu-id="9637d-270">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="9637d-271">Limpe os caches de pacote em *%UserProfile%\\.nuget\\packages* e *%LocalAppData%\\Nuget\\v3-cache*.</span><span class="sxs-lookup"><span data-stu-id="9637d-271">Clear the package caches at *%UserProfile%\\.nuget\\packages* and *%LocalAppData%\\Nuget\\v3-cache*.</span></span>
1. <span data-ttu-id="9637d-272">Restaure e recompile o projeto.</span><span class="sxs-lookup"><span data-stu-id="9637d-272">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="9637d-273">Confirme que a implantação anterior no servidor foi completamente excluída antes de reimplantar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9637d-273">Confirm that the prior deployment on the server has been completely deleted prior to redeploying the app.</span></span>

> [!TIP]
> <span data-ttu-id="9637d-274">Uma maneira prática de se limpar caches do pacote é executar `dotnet nuget locals all --clear` de um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="9637d-274">A convenient way to clear package caches is to execute `dotnet nuget locals all --clear` from a command prompt.</span></span>
>
> <span data-ttu-id="9637d-275">Também é possível limpar os caches de pacote usando a ferramenta [nuget.exe](https://www.nuget.org/downloads) e executando o comando `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="9637d-275">Clearing package caches can also be accomplished by using the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="9637d-276">*nuget.exe* não é uma instalação fornecida com o sistema operacional Windows Desktop e devem ser obtidos separadamente do [site do NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="9637d-276">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9637d-277">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="9637d-277">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/azure-apps/troubleshoot>
