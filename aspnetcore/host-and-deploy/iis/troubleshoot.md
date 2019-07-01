---
title: Solucionar problemas do ASP.NET Core no IIS
author: guardrex
description: Saiba como diagnosticar problemas com as implantações do IIS (Serviços de Informações da Internet) de aplicativos do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/19/2019
uid: host-and-deploy/iis/troubleshoot
ms.openlocfilehash: 4df370dd9b1a5a651bcf767b8b9ace4220bdc345
ms.sourcegitcommit: 9f11685382eb1f4dd0fb694dea797adacedf9e20
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313651"
---
# <a name="troubleshoot-aspnet-core-on-iis"></a><span data-ttu-id="e6030-103">Solucionar problemas do ASP.NET Core no IIS</span><span class="sxs-lookup"><span data-stu-id="e6030-103">Troubleshoot ASP.NET Core on IIS</span></span>

<span data-ttu-id="e6030-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e6030-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e6030-105">Este artigo fornece instruções sobre como diagnosticar um problema de inicialização do aplicativo ASP.NET Core ao hospedar com [IIS (Serviços de Informações da Internet)](/iis).</span><span class="sxs-lookup"><span data-stu-id="e6030-105">This article provides instructions on how to diagnose an ASP.NET Core app startup issue when hosting with [Internet Information Services (IIS)](/iis).</span></span> <span data-ttu-id="e6030-106">As informações neste artigo se aplicam à hospedagem em IIS no Windows Server e Windows Desktop.</span><span class="sxs-lookup"><span data-stu-id="e6030-106">The information in this article applies to hosting in IIS on Windows Server and Windows Desktop.</span></span>

<span data-ttu-id="e6030-107">Tópicos adicionais de solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="e6030-107">Additional troubleshooting topics:</span></span>

* <span data-ttu-id="e6030-108">O Serviço de Aplicativo do Azure também usa o [módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) e o IIS para hospedar aplicativos.</span><span class="sxs-lookup"><span data-stu-id="e6030-108">Azure App Service also uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and IIS to host apps.</span></span> <span data-ttu-id="e6030-109">Para obter conselhos sobre solução de problemas especificamente do Serviço de Aplicativo do Azure, confira <xref:host-and-deploy/azure-apps/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="e6030-109">For troubleshooting advice that pertains specifically to Azure App Service, see <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>
* <span data-ttu-id="e6030-110"><xref:fundamentals/error-handling> aborda como tratar erros em aplicativos do ASP.NET Core durante o desenvolvimento em um sistema local.</span><span class="sxs-lookup"><span data-stu-id="e6030-110"><xref:fundamentals/error-handling> covers how to handle errors in ASP.NET Core apps during development on a local system.</span></span>
* <span data-ttu-id="e6030-111">[Aprender a depurar usando o Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger) apresenta os recursos do depurador do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e6030-111">[Learn to debug using Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger) introduces the features of the Visual Studio debugger.</span></span>
* <span data-ttu-id="e6030-112">[Depurar com o Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging) descreve o suporte de depuração interno do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="e6030-112">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging) describes the debugging support built into Visual Studio Code.</span></span>

[!INCLUDE[](~/includes/azure-iis-startup-errors.md)]

## <a name="troubleshoot-app-startup-errors"></a><span data-ttu-id="e6030-113">Solucionar problemas de inicialização do aplicativo</span><span class="sxs-lookup"><span data-stu-id="e6030-113">Troubleshoot app startup errors</span></span>

### <a name="enable-the-aspnet-core-module-debug-log"></a><span data-ttu-id="e6030-114">Habilitar o log de depuração do Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e6030-114">Enable the ASP.NET Core Module debug log</span></span>

<span data-ttu-id="e6030-115">Adicione as seguintes configurações de manipulador ao arquivo *web.config* do aplicativo para habilitar os logs de depuração do Módulo do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="e6030-115">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug logs:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="e6030-116">Confirme se o caminho especificado para o log existe e se a identidade do pool de aplicativos tem permissões de gravação no local.</span><span class="sxs-lookup"><span data-stu-id="e6030-116">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

### <a name="application-event-log"></a><span data-ttu-id="e6030-117">Log de Eventos do Aplicativo</span><span class="sxs-lookup"><span data-stu-id="e6030-117">Application Event Log</span></span>

<span data-ttu-id="e6030-118">Acesse o Log de Eventos do Aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e6030-118">Access the Application Event Log:</span></span>

1. <span data-ttu-id="e6030-119">Abra o menu Iniciar, procure **Visualizador de Eventos** e, em seguida, selecione o aplicativo **Visualizador de Eventos**.</span><span class="sxs-lookup"><span data-stu-id="e6030-119">Open the Start menu, search for **Event Viewer**, and then select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="e6030-120">No **Visualizador de Eventos**, abra o nó **Logs do Windows**.</span><span class="sxs-lookup"><span data-stu-id="e6030-120">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="e6030-121">Selecione **Aplicativo** para abrir o Log de Eventos do Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6030-121">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="e6030-122">Procure erros associados ao aplicativo com falha.</span><span class="sxs-lookup"><span data-stu-id="e6030-122">Search for errors associated with the failing app.</span></span> <span data-ttu-id="e6030-123">Os erros têm um valor *Módulo AspNetCore do IIS* ou *Módulo AspNetCore do IIS Express* na coluna *Origem*.</span><span class="sxs-lookup"><span data-stu-id="e6030-123">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="e6030-124">Execute o aplicativo em um prompt de comando</span><span class="sxs-lookup"><span data-stu-id="e6030-124">Run the app at a command prompt</span></span>

<span data-ttu-id="e6030-125">Muitos erros de inicialização não produzem informações úteis no Log de Eventos do Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6030-125">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="e6030-126">Você pode encontrar a causa de alguns erros ao executar o aplicativo em um prompt de comando no sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="e6030-126">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="e6030-127">Implantação dependente de estrutura</span><span class="sxs-lookup"><span data-stu-id="e6030-127">Framework-dependent deployment</span></span>

<span data-ttu-id="e6030-128">Se o aplicativo é uma [implantação dependente de estrutura](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="e6030-128">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="e6030-129">Em um prompt de comando, navegue até a pasta de implantação e execute o aplicativo, executando o assembly do aplicativo com *dotnet.exe*.</span><span class="sxs-lookup"><span data-stu-id="e6030-129">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="e6030-130">No comando a seguir, substitua o nome do assembly do aplicativo por \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span><span class="sxs-lookup"><span data-stu-id="e6030-130">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="e6030-131">A saída do console do aplicativo, mostrando eventuais erros, é gravada na janela do console.</span><span class="sxs-lookup"><span data-stu-id="e6030-131">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="e6030-132">Se os erros ocorrerem ao fazer uma solicitação para o aplicativo, faça uma solicitação para o host e a porta em que o Kestrel escuta.</span><span class="sxs-lookup"><span data-stu-id="e6030-132">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="e6030-133">Usando o host e a porta padrão, faça uma solicitação para `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="e6030-133">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="e6030-134">Se o aplicativo responde normalmente no endereço do ponto de extremidade do Kestrel, a probabilidade de o problema estar relacionado à configuração de hospedagem é maior e, de estar relacionado ao aplicativo, menor.</span><span class="sxs-lookup"><span data-stu-id="e6030-134">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="e6030-135">Implantação autocontida</span><span class="sxs-lookup"><span data-stu-id="e6030-135">Self-contained deployment</span></span>

<span data-ttu-id="e6030-136">Se o aplicativo é uma [implantação autossuficiente](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="e6030-136">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="e6030-137">Em um prompt de comando, navegue até a pasta de implantação e execute o arquivo executável do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6030-137">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="e6030-138">No comando a seguir, substitua o nome do assembly do aplicativo por \<assembly_name>: `<assembly_name>.exe`.</span><span class="sxs-lookup"><span data-stu-id="e6030-138">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="e6030-139">A saída do console do aplicativo, mostrando eventuais erros, é gravada na janela do console.</span><span class="sxs-lookup"><span data-stu-id="e6030-139">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="e6030-140">Se os erros ocorrerem ao fazer uma solicitação para o aplicativo, faça uma solicitação para o host e a porta em que o Kestrel escuta.</span><span class="sxs-lookup"><span data-stu-id="e6030-140">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="e6030-141">Usando o host e a porta padrão, faça uma solicitação para `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="e6030-141">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="e6030-142">Se o aplicativo responde normalmente no endereço do ponto de extremidade do Kestrel, a probabilidade de o problema estar relacionado à configuração de hospedagem é maior e, de estar relacionado ao aplicativo, menor.</span><span class="sxs-lookup"><span data-stu-id="e6030-142">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log"></a><span data-ttu-id="e6030-143">Log de stdout do Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e6030-143">ASP.NET Core Module stdout log</span></span>

<span data-ttu-id="e6030-144">Para habilitar e exibir logs de stdout:</span><span class="sxs-lookup"><span data-stu-id="e6030-144">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="e6030-145">Navegue até a pasta de implantação do site no sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="e6030-145">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="e6030-146">Se a pasta *logs* não estiver presente, crie-a.</span><span class="sxs-lookup"><span data-stu-id="e6030-146">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="e6030-147">Para obter instruções sobre como habilitar o MSBuild para criar a pasta *logs* na implantação automaticamente, veja o tópico [Estrutura de diretórios](xref:host-and-deploy/directory-structure).</span><span class="sxs-lookup"><span data-stu-id="e6030-147">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="e6030-148">Edite o arquivo *web.config*.</span><span class="sxs-lookup"><span data-stu-id="e6030-148">Edit the *web.config* file.</span></span> <span data-ttu-id="e6030-149">Defina **stdoutLogEnabled** para `true` e altere o caminho **stdoutLogFile** para apontar para a pasta *logs* (por exemplo, `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="e6030-149">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="e6030-150">`stdout` no caminho é o prefixo do nome do arquivo de log.</span><span class="sxs-lookup"><span data-stu-id="e6030-150">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="e6030-151">Uma extensão de arquivo, uma ID do processo e um carimbo de data/hora são adicionados automaticamente quando o log é criado.</span><span class="sxs-lookup"><span data-stu-id="e6030-151">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="e6030-152">Usando `stdout` como o prefixo do nome do arquivo, um arquivo de log típico é nomeado *stdout_20180205184032_5412.log*.</span><span class="sxs-lookup"><span data-stu-id="e6030-152">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="e6030-153">Verifique se a identidade do pool de aplicativos tem permissões de gravação para a pasta *logs*.</span><span class="sxs-lookup"><span data-stu-id="e6030-153">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="e6030-154">Salve o arquivo *web.config* atualizado.</span><span class="sxs-lookup"><span data-stu-id="e6030-154">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="e6030-155">Faça uma solicitação ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6030-155">Make a request to the app.</span></span>
1. <span data-ttu-id="e6030-156">Navegue até a pasta *logs*.</span><span class="sxs-lookup"><span data-stu-id="e6030-156">Navigate to the *logs* folder.</span></span> <span data-ttu-id="e6030-157">Localize e abra o log de stdout mais recente.</span><span class="sxs-lookup"><span data-stu-id="e6030-157">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="e6030-158">Estude o log em busca de erros.</span><span class="sxs-lookup"><span data-stu-id="e6030-158">Study the log for errors.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e6030-159">Desabilite o registro em log de stdout quando a solução de problemas for concluída.</span><span class="sxs-lookup"><span data-stu-id="e6030-159">Disable stdout logging when troubleshooting is complete.</span></span>

1. <span data-ttu-id="e6030-160">Edite o arquivo *web.config*.</span><span class="sxs-lookup"><span data-stu-id="e6030-160">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="e6030-161">Defina **stdoutLogEnabled** para `false`.</span><span class="sxs-lookup"><span data-stu-id="e6030-161">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="e6030-162">Salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="e6030-162">Save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="e6030-163">Falha ao desabilitar o log de stdout pode levar a falhas de aplicativo ou de servidor.</span><span class="sxs-lookup"><span data-stu-id="e6030-163">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="e6030-164">Não há limites para o tamanho do arquivo de log ou para o número de arquivos de log criados.</span><span class="sxs-lookup"><span data-stu-id="e6030-164">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="e6030-165">Para registro em log de rotina em um aplicativo ASP.NET Core, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs.</span><span class="sxs-lookup"><span data-stu-id="e6030-165">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="e6030-166">Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="e6030-166">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="enable-the-developer-exception-page"></a><span data-ttu-id="e6030-167">Habilitar a página de exceção do desenvolvedor</span><span class="sxs-lookup"><span data-stu-id="e6030-167">Enable the Developer Exception Page</span></span>

<span data-ttu-id="e6030-168">A [variável de ambiente `ASPNETCORE_ENVIRONMENT` pode ser adicionada ao web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) para executar o aplicativo no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="e6030-168">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="e6030-169">Desde que o ambiente não seja substituído na inicialização do aplicativo por `UseEnvironment` no compilador do host, definir a variável de ambiente permite que a [Página de Exceções do Desenvolvedor](xref:fundamentals/error-handling) apareça quando o aplicativo é executado.</span><span class="sxs-lookup"><span data-stu-id="e6030-169">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="e6030-170">Configurar a variável de ambiente para `ASPNETCORE_ENVIRONMENT` só é recomendado para servidores de preparo e de teste que não estejam expostos à Internet.</span><span class="sxs-lookup"><span data-stu-id="e6030-170">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="e6030-171">Remova a variável de ambiente do arquivo *web.config* após a solução de problemas.</span><span class="sxs-lookup"><span data-stu-id="e6030-171">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="e6030-172">Para obter informações sobre como definir variáveis de ambiente no *web.config*, confira [Elemento filho environmentVariables de aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="e6030-172">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

## <a name="common-startup-errors"></a><span data-ttu-id="e6030-173">Erros de inicialização comuns</span><span class="sxs-lookup"><span data-stu-id="e6030-173">Common startup errors</span></span>

<span data-ttu-id="e6030-174">Consulte <xref:host-and-deploy/azure-iis-errors-reference>.</span><span class="sxs-lookup"><span data-stu-id="e6030-174">See <xref:host-and-deploy/azure-iis-errors-reference>.</span></span> <span data-ttu-id="e6030-175">A maioria dos problemas comuns que impedem a inicialização do aplicativo é abordada no tópico de referência.</span><span class="sxs-lookup"><span data-stu-id="e6030-175">Most of the common problems that prevent app startup are covered in the reference topic.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="e6030-176">Obter dados de um aplicativo</span><span class="sxs-lookup"><span data-stu-id="e6030-176">Obtain data from an app</span></span>

<span data-ttu-id="e6030-177">Se um aplicativo for capaz de responder às solicitações, obtenha as solicitações, conexão e dados adicionais do aplicativo que usar o middleware embutido de terminal.</span><span class="sxs-lookup"><span data-stu-id="e6030-177">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="e6030-178">Para saber mais e obter um código de exemplo, consulte <xref:test/troubleshoot#obtain-data-from-an-app>.</span><span class="sxs-lookup"><span data-stu-id="e6030-178">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

## <a name="create-a-dump"></a><span data-ttu-id="e6030-179">Criar um despejo de memória</span><span class="sxs-lookup"><span data-stu-id="e6030-179">Create a dump</span></span>

<span data-ttu-id="e6030-180">Um *despejo de memória* é um instantâneo da memória do sistema e pode ajudar a determinar a causa de uma falha de aplicativo, falha de inicialização ou lentidão de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6030-180">A *dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="e6030-181">O aplicativo falha ou encontra uma exceção</span><span class="sxs-lookup"><span data-stu-id="e6030-181">App crashes or encounters an exception</span></span>

<span data-ttu-id="e6030-182">Obter e analisar um despejo de memória do [WER (Relatório de Erros do Windows)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="e6030-182">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="e6030-183">Crie uma pasta para armazenar os arquivos de despejo de memória em `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="e6030-183">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="e6030-184">O pool de aplicativos deve ter acesso para gravação à pasta.</span><span class="sxs-lookup"><span data-stu-id="e6030-184">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="e6030-185">Execute o [script EnableDumps do PowerShell](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="e6030-185">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="e6030-186">Se o aplicativo usa o [modelo de hospedagem em processo](xref:host-and-deploy/iis/index#in-process-hosting-model), execute o script para *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="e6030-186">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="e6030-187">Se o aplicativo usa o [modelo de hospedagem fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model), execute o script para *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="e6030-187">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="e6030-188">Execute o aplicativo sob as condições que causam a falha.</span><span class="sxs-lookup"><span data-stu-id="e6030-188">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="e6030-189">Após a falha, execute o [script DisableDumps do PowerShell](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="e6030-189">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="e6030-190">Se o aplicativo usa o [modelo de hospedagem em processo](xref:host-and-deploy/iis/index#in-process-hosting-model), execute o script para *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="e6030-190">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="e6030-191">Se o aplicativo usa o [modelo de hospedagem fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model), execute o script para *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="e6030-191">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="e6030-192">Depois que um aplicativo falhar e a coleta de despejo de memória for concluída, o aplicativo terá permissão para encerrar normalmente.</span><span class="sxs-lookup"><span data-stu-id="e6030-192">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="e6030-193">O script do PowerShell configura o WER para coletar até cinco despejos de memória por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6030-193">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="e6030-194">Os despejos de memória podem usar uma grande quantidade de espaço em disco (até vários gigabytes cada).</span><span class="sxs-lookup"><span data-stu-id="e6030-194">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="e6030-195">O aplicativo trava, falha durante a inicialização ou executa normalmente</span><span class="sxs-lookup"><span data-stu-id="e6030-195">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="e6030-196">Quando um aplicativo *travar* (para de responder, mas não falha), falhar durante a inicialização ou executar normalmente, veja [Arquivos de despejo de memória do modo de usuário: escolher a melhor ferramenta](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) para selecionar uma ferramenta adequada para produzir o despejo de memória.</span><span class="sxs-lookup"><span data-stu-id="e6030-196">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

### <a name="analyze-the-dump"></a><span data-ttu-id="e6030-197">Analisar o despejo de memória</span><span class="sxs-lookup"><span data-stu-id="e6030-197">Analyze the dump</span></span>

<span data-ttu-id="e6030-198">Um despejo de memória pode ser analisado usando várias abordagens.</span><span class="sxs-lookup"><span data-stu-id="e6030-198">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="e6030-199">Para obter mais informações, confira [Analisando um arquivo de despejo de memória do modo de usuário](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="e6030-199">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="remote-debugging"></a><span data-ttu-id="e6030-200">Depuração remota</span><span class="sxs-lookup"><span data-stu-id="e6030-200">Remote debugging</span></span>

<span data-ttu-id="e6030-201">Veja [Depuração remota do ASP.NET Core em um computador de IIS remoto no Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) na documentação do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e6030-201">See [Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) in the Visual Studio documentation.</span></span>

## <a name="application-insights"></a><span data-ttu-id="e6030-202">Informações do aplicativo</span><span class="sxs-lookup"><span data-stu-id="e6030-202">Application Insights</span></span>

<span data-ttu-id="e6030-203">O [Application Insights](/azure/application-insights/) fornece telemetria de aplicativos hospedados pelo IIS, incluindo recursos de relatório e de registro de erros em log.</span><span class="sxs-lookup"><span data-stu-id="e6030-203">[Application Insights](/azure/application-insights/) provides telemetry from apps hosted by IIS, including error logging and reporting features.</span></span> <span data-ttu-id="e6030-204">O Application Insights só pode relatar erros ocorridos depois que o aplicativo é iniciado quando os recursos de registro em log do aplicativo se tornam disponíveis.</span><span class="sxs-lookup"><span data-stu-id="e6030-204">Application Insights can only report on errors that occur after the app starts when the app's logging features become available.</span></span> <span data-ttu-id="e6030-205">Para obter mais informações, veja [Application Insights para ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="e6030-205">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="additional-advice"></a><span data-ttu-id="e6030-206">Orientações adicionais</span><span class="sxs-lookup"><span data-stu-id="e6030-206">Additional advice</span></span>

<span data-ttu-id="e6030-207">Algumas vezes um aplicativo em funcionamento falha imediatamente após atualizar o SDK do .NET Core nas versões de pacote ou de computador de desenvolvimento no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6030-207">Sometimes a functioning app fails immediately after upgrading either the .NET Core SDK on the development machine or package versions within the app.</span></span> <span data-ttu-id="e6030-208">Em alguns casos, pacotes incoerentes podem interromper um aplicativo ao executar atualizações principais.</span><span class="sxs-lookup"><span data-stu-id="e6030-208">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="e6030-209">A maioria desses problemas pode ser corrigida seguindo estas instruções:</span><span class="sxs-lookup"><span data-stu-id="e6030-209">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="e6030-210">Exclua as pastas *bin* e *obj*.</span><span class="sxs-lookup"><span data-stu-id="e6030-210">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="e6030-211">Limpe os caches de pacote em *%UserProfile%\\.nuget\\packages* e *%LocalAppData%\\Nuget\\v3-cache*.</span><span class="sxs-lookup"><span data-stu-id="e6030-211">Clear the package caches at *%UserProfile%\\.nuget\\packages* and *%LocalAppData%\\Nuget\\v3-cache*.</span></span>
1. <span data-ttu-id="e6030-212">Restaure e recompile o projeto.</span><span class="sxs-lookup"><span data-stu-id="e6030-212">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="e6030-213">Confirme que a implantação anterior no servidor foi completamente excluída antes de reimplantar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6030-213">Confirm that the prior deployment on the server has been completely deleted prior to redeploying the app.</span></span>

> [!TIP]
> <span data-ttu-id="e6030-214">Uma maneira prática de se limpar caches do pacote é executar `dotnet nuget locals all --clear` de um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="e6030-214">A convenient way to clear package caches is to execute `dotnet nuget locals all --clear` from a command prompt.</span></span>
>
> <span data-ttu-id="e6030-215">Também é possível limpar os caches de pacote usando a ferramenta [nuget.exe](https://www.nuget.org/downloads) e executando o comando `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="e6030-215">Clearing package caches can also be accomplished by using the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="e6030-216">*nuget.exe* não é uma instalação fornecida com o sistema operacional Windows Desktop e devem ser obtidos separadamente do [site do NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="e6030-216">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e6030-217">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e6030-217">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/azure-apps/troubleshoot>
