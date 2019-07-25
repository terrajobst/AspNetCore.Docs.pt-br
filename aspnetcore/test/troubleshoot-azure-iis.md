---
title: Solucionar problemas ASP.NET Core no serviço Azure App e no IIS
author: guardrex
description: Saiba como diagnosticar problemas com implantações de serviço Azure App e Serviços de Informações da Internet (IIS) de aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/18/2019
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: deae568a6ba88c9a8365b9d7f2df629899bc64a1
ms.sourcegitcommit: 16502797ea749e2690feaa5e652a65b89c007c89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68483316"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="5d4bd-103">Solucionar problemas ASP.NET Core no serviço Azure App e no IIS</span><span class="sxs-lookup"><span data-stu-id="5d4bd-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="5d4bd-104">De [Luke Latham](https://github.com/guardrex) e [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="5d4bd-104">By [Luke Latham](https://github.com/guardrex) and [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="5d4bd-105">Este artigo fornece informações sobre erros de inicialização de aplicativo comuns e instruções sobre como diagnosticar erros quando um aplicativo é implantado no serviço de Azure App ou IIS:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="5d4bd-106">Erros de inicialização do aplicativo</span><span class="sxs-lookup"><span data-stu-id="5d4bd-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="5d4bd-107">Explica cenários de código de status HTTP de inicialização comuns.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="5d4bd-108">Solucionar problemas no serviço Azure App</span><span class="sxs-lookup"><span data-stu-id="5d4bd-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="5d4bd-109">Fornece conselhos de solução de problemas para aplicativos implantados no serviço Azure App.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="5d4bd-110">Solução de problemas no IIS</span><span class="sxs-lookup"><span data-stu-id="5d4bd-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="5d4bd-111">Fornece conselhos de solução de problemas para aplicativos implantados no IIS ou em execução no IIS Express localmente.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="5d4bd-112">A orientação se aplica às implantações do Windows Server e do Windows desktop.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="5d4bd-113">Limpar caches de pacote</span><span class="sxs-lookup"><span data-stu-id="5d4bd-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="5d4bd-114">Explica o que fazer quando pacotes incoerentes interrompem um aplicativo ao executar atualizações importantes ou alterar versões de pacotes.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="5d4bd-115">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5d4bd-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="5d4bd-116">Lista tópicos adicionais de solução de problemas.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="5d4bd-117">Erros de inicialização do aplicativo</span><span class="sxs-lookup"><span data-stu-id="5d4bd-117">App startup errors</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="5d4bd-118">No Visual Studio, um projeto do ASP.NET Core usa por padrão a hospedagem do [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) durante a depuração.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="5d4bd-119">Uma *falha de 502,5 processo* ou uma *falha de início de 500,30* que ocorre quando a depuração local pode ser diagnosticada usando o Conselho neste tópico.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="5d4bd-120">No Visual Studio, um projeto do ASP.NET Core usa por padrão a hospedagem do [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) durante a depuração.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-120">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="5d4bd-121">Uma *falha de processo 502,5* que ocorre ao depurar localmente pode ser diagnosticada usando o Conselho neste tópico.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-121">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

::: moniker-end

### <a name="40314-forbidden"></a><span data-ttu-id="5d4bd-122">403,14 proibido</span><span class="sxs-lookup"><span data-stu-id="5d4bd-122">403.14 Forbidden</span></span>

<span data-ttu-id="5d4bd-123">Falha ao iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-123">The app fails to start.</span></span> <span data-ttu-id="5d4bd-124">O seguinte erro é registrado em log:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-124">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="5d4bd-125">O erro geralmente é causado por uma implantação quebrada no sistema de hospedagem, que inclui qualquer um dos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-125">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="5d4bd-126">O aplicativo é implantado na pasta incorreta no sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-126">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="5d4bd-127">O processo de implantação não moveu todos os arquivos e pastas do aplicativo para a pasta de implantação no sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-127">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="5d4bd-128">O arquivo *Web. config* está ausente na implantação ou o conteúdo do arquivo *Web. config* está malformado.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-128">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="5d4bd-129">Execute as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-129">Perform the following steps:</span></span>

1. <span data-ttu-id="5d4bd-130">Exclua todos os arquivos e pastas da pasta de implantação no sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-130">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="5d4bd-131">Reimplante o conteúdo da pasta de *publicação* do aplicativo no sistema de hospedagem usando o método normal de implantação, como o Visual Studio, o PowerShell ou a implantação manual:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-131">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="5d4bd-132">Confirme se o arquivo *Web. config* está presente na implantação e se seu conteúdo está correto.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-132">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="5d4bd-133">Ao hospedar no serviço Azure app, confirme se o aplicativo está implantado `D:\home\site\wwwroot` na pasta.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-133">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="5d4bd-134">Quando o aplicativo é hospedado pelo IIS, confirme se o aplicativo está implantado no **caminho físico** do IIS mostrado nas **configurações básicas**do **Gerenciador do IIS**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-134">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="5d4bd-135">Confirme se todos os arquivos e pastas do aplicativo estão implantados comparando a implantação no sistema de hospedagem ao conteúdo da pasta de *publicação* do projeto.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-135">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="5d4bd-136">Para obter mais informações sobre o layout de um aplicativo ASP.NET Core publicado, <xref:host-and-deploy/directory-structure>consulte.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-136">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="5d4bd-137">Para obter mais informações sobre o arquivo *Web. config* , <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>consulte.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-137">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="5d4bd-138">500 Erro Interno do Servidor</span><span class="sxs-lookup"><span data-stu-id="5d4bd-138">500 Internal Server Error</span></span>

<span data-ttu-id="5d4bd-139">O aplicativo é iniciado, mas um erro impede o servidor de atender à solicitação.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-139">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="5d4bd-140">Esse erro ocorre no código do aplicativo durante a inicialização ou durante a criação de uma resposta.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-140">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="5d4bd-141">A resposta poderá não conter nenhum conteúdo, ou a resposta poderá ser exibida como um *500 – Erro Interno do Servidor* no navegador.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-141">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="5d4bd-142">O Log de Eventos do Aplicativo geralmente indica que o aplicativo iniciou normalmente.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-142">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="5d4bd-143">Da perspectiva do servidor, isso está correto.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-143">From the server's perspective, that's correct.</span></span> <span data-ttu-id="5d4bd-144">O aplicativo foi iniciado, mas não é capaz de gerar uma resposta válida.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-144">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="5d4bd-145">Execute o aplicativo em um prompt de comando no servidor ou habilite o log de stdout do módulo ASP.NET Core para solucionar o problema.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-145">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

::: moniker range="= aspnetcore-2.2"

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="5d4bd-146">500.0 Falha de carregamento de manipulador em processo</span><span class="sxs-lookup"><span data-stu-id="5d4bd-146">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="5d4bd-147">O processo de trabalho falha.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-147">The worker process fails.</span></span> <span data-ttu-id="5d4bd-148">O aplicativo não foi iniciado.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-148">The app doesn't start.</span></span>

<span data-ttu-id="5d4bd-149">O [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) falha ao localizar o .NET Core CLR e encontrar o manipulador de solicitação em processo (*aspnetcorev2_inprocess. dll*).</span><span class="sxs-lookup"><span data-stu-id="5d4bd-149">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="5d4bd-150">Verifique se:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-150">Check that:</span></span>

* <span data-ttu-id="5d4bd-151">O aplicativo destina-se ao pacote NuGet [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) ou ao [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="5d4bd-151">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="5d4bd-152">A versão da estrutura compartilhada do ASP.NET Core a que o aplicativo se destina está instalada no computador de destino.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-152">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="5d4bd-153">500.0 Falha de carregamento de manipulador fora de processo</span><span class="sxs-lookup"><span data-stu-id="5d4bd-153">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="5d4bd-154">O processo de trabalho falha.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-154">The worker process fails.</span></span> <span data-ttu-id="5d4bd-155">O aplicativo não foi iniciado.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-155">The app doesn't start.</span></span>

<span data-ttu-id="5d4bd-156">O [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) falha ao localizar o manipulador de solicitação de hospedagem fora do processo.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-156">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="5d4bd-157">Verifique se a *aspnetcorev2_outofprocess.dll* está presente em uma subpasta próxima a *aspnetcorev2.dll*.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-157">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="5d4bd-158">500.0 Falha de carregamento de manipulador em processo</span><span class="sxs-lookup"><span data-stu-id="5d4bd-158">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="5d4bd-159">O processo de trabalho falha.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-159">The worker process fails.</span></span> <span data-ttu-id="5d4bd-160">O aplicativo não foi iniciado.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-160">The app doesn't start.</span></span>

<span data-ttu-id="5d4bd-161">Erro desconhecido ao carregar ASP.NET Core componentes do [módulo](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="5d4bd-161">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="5d4bd-162">Execute uma das seguintes ações:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-162">Take one of the following actions:</span></span>

* <span data-ttu-id="5d4bd-163">Entre em contato com o [Suporte da Microsoft](https://support.microsoft.com/oas/default.aspx?prid=15832) (selecione **Ferramentas para Desenvolvedores** e, em seguida, **ASP.NET Core**).</span><span class="sxs-lookup"><span data-stu-id="5d4bd-163">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="5d4bd-164">Faça uma pergunta no Stack Overflow.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-164">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="5d4bd-165">Registre um problema no nosso [Repositório do GitHub](https://github.com/aspnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="5d4bd-165">File an issue on our [GitHub repository](https://github.com/aspnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="5d4bd-166">500.30 Falha de inicialização em processo</span><span class="sxs-lookup"><span data-stu-id="5d4bd-166">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="5d4bd-167">O processo de trabalho falha.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-167">The worker process fails.</span></span> <span data-ttu-id="5d4bd-168">O aplicativo não foi iniciado.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-168">The app doesn't start.</span></span>

<span data-ttu-id="5d4bd-169">O [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) tenta iniciar o .NET Core CLR em processo, mas falha ao iniciar.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-169">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="5d4bd-170">A causa de uma falha de inicialização do processo normalmente pode ser determinada das entradas no log de eventos do aplicativo e do log de stdout do módulo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-170">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="5d4bd-171">Uma condição de falha comum é o aplicativo configurado incorretamente, direcionado a uma versão da estrutura compartilhada do ASP.NET Core que não está presente.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-171">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="5d4bd-172">Verifique quais versões da estrutura compartilhada do ASP.NET Core estão instaladas no computador de destino.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-172">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="5d4bd-173">500.31 O ANCM não pôde encontrar dependências nativas</span><span class="sxs-lookup"><span data-stu-id="5d4bd-173">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="5d4bd-174">O processo de trabalho falha.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-174">The worker process fails.</span></span> <span data-ttu-id="5d4bd-175">O aplicativo não foi iniciado.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-175">The app doesn't start.</span></span>

<span data-ttu-id="5d4bd-176">O [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) tenta iniciar o tempo de execução do .NET Core em processo, mas falha ao iniciar.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-176">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="5d4bd-177">A causa mais comum dessa falha de inicialização é quando o tempo de execução `Microsoft.NETCore.App` ou `Microsoft.AspNetCore.App` não está instalado.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-177">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="5d4bd-178">Se o aplicativo for implantado no ASP.NET Core 3.0 de destino e essa versão não existir no computador, esse erro ocorrerá.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-178">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="5d4bd-179">Segue um exemplo de mensagem de erro:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-179">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="5d4bd-180">A mensagem de erro lista todas as versões instaladas do .NET Core e a versão solicitada pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-180">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="5d4bd-181">Para corrigir esse erro:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-181">To fix this error, either:</span></span>

* <span data-ttu-id="5d4bd-182">Instale a versão adequada do .NET Core no computador.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-182">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="5d4bd-183">Altere o aplicativo para uma versão do .NET Core que está presente no computador de destino.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-183">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="5d4bd-184">Publique o aplicativo como uma [implantação autossuficiente](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="5d4bd-184">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="5d4bd-185">Durante a execução no desenvolvimento (quando a variável de ambiente `ASPNETCORE_ENVIRONMENT` está definida como `Development`), o erro específico é gravado na resposta HTTP.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-185">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="5d4bd-186">A causa de uma falha de inicialização do processo também é encontrada no log de eventos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-186">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="5d4bd-187">500.32 O ANCM não pôde carregar o dll</span><span class="sxs-lookup"><span data-stu-id="5d4bd-187">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="5d4bd-188">O processo de trabalho falha.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-188">The worker process fails.</span></span> <span data-ttu-id="5d4bd-189">O aplicativo não foi iniciado.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-189">The app doesn't start.</span></span>

<span data-ttu-id="5d4bd-190">A causa mais comum para esse erro é que o aplicativo foi publicado para uma arquitetura de processador incompatível.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-190">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="5d4bd-191">Esse erro ocorrerá se o processo de trabalho estiver em execução como um aplicativo de 32 bits e o aplicativo tiver sido publicado para o destino de 64 bits.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-191">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="5d4bd-192">Para corrigir esse erro:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-192">To fix this error, either:</span></span>

* <span data-ttu-id="5d4bd-193">Republique o aplicativo para a mesma arquitetura de processador que o processo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-193">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="5d4bd-194">Publique o aplicativo como uma [implantação dependente da estrutura](/dotnet/core/deploying/#framework-dependent-executables-fde).</span><span class="sxs-lookup"><span data-stu-id="5d4bd-194">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="5d4bd-195">500.33 O ANCM não pôde carregar o manipulador de solicitação</span><span class="sxs-lookup"><span data-stu-id="5d4bd-195">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="5d4bd-196">O processo de trabalho falha.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-196">The worker process fails.</span></span> <span data-ttu-id="5d4bd-197">O aplicativo não foi iniciado.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-197">The app doesn't start.</span></span>

<span data-ttu-id="5d4bd-198">O aplicativo não referenciou a estrutura `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-198">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="5d4bd-199">Somente aplicativos direcionados `Microsoft.AspNetCore.App` à estrutura podem ser hospedados pelo [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="5d4bd-199">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="5d4bd-200">Para corrigir esse erro, confirme se o aplicativo está direcionado para a estrutura `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-200">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="5d4bd-201">Confira o `.runtimeconfig.json` para verificar a estrutura de destino do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-201">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="5d4bd-202">500.34 Não há suporte para modelos de hospedagem mistos do ANCM</span><span class="sxs-lookup"><span data-stu-id="5d4bd-202">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="5d4bd-203">O processo de trabalho não pode executar um aplicativo em processo e um aplicativo fora do processo no mesmo processo.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-203">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="5d4bd-204">Para corrigir esse erro, execute aplicativos em pools de aplicativos do IIS separados.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-204">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="5d4bd-205">500.35 Vários aplicativos do ANCM em processo no mesmo processo</span><span class="sxs-lookup"><span data-stu-id="5d4bd-205">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="5d4bd-206">O processo de trabalho não pode executar um aplicativo em processo e um aplicativo fora do processo no mesmo processo.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-206">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="5d4bd-207">Para corrigir esse erro, execute aplicativos em pools de aplicativos do IIS separados.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-207">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="5d4bd-208">500.36 Falha ao carregar o manipulador de fora do processo do ANCM</span><span class="sxs-lookup"><span data-stu-id="5d4bd-208">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="5d4bd-209">O manipulador de solicitação de fora do processo *aspnetcorev2_outofprocess.dll* não está próximo do arquivo *aspnetcorev2.dll*.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-209">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="5d4bd-210">Isso indica uma instalação corrompida do [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="5d4bd-210">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="5d4bd-211">Para corrigir esse erro, repare a instalação do [Pacote de Hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (para IIS) ou do Visual Studio (para o IIS Express).</span><span class="sxs-lookup"><span data-stu-id="5d4bd-211">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="5d4bd-212">500.37 O ANCM não pôde ser iniciado dentro do limite de tempo de inicialização</span><span class="sxs-lookup"><span data-stu-id="5d4bd-212">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="5d4bd-213">O ANCM não pôde ser iniciado dentro do limite de tempo de inicialização fornecido.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-213">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="5d4bd-214">Por padrão, o tempo limite é de 120 segundos.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-214">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="5d4bd-215">Esse erro pode ocorrer ao iniciar um grande número de aplicativos no mesmo computador.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-215">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="5d4bd-216">Verifique se há picos de uso de CPU/memória no servidor durante a inicialização.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-216">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="5d4bd-217">Talvez você precise balancear o processo de inicialização de vários aplicativos.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-217">You may need to stagger the startup process of multiple apps.</span></span>

::: moniker-end

### <a name="5025-process-failure"></a><span data-ttu-id="5d4bd-218">502.5 Falha de processo</span><span class="sxs-lookup"><span data-stu-id="5d4bd-218">502.5 Process Failure</span></span>

<span data-ttu-id="5d4bd-219">O processo de trabalho falha.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-219">The worker process fails.</span></span> <span data-ttu-id="5d4bd-220">O aplicativo não foi iniciado.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-220">The app doesn't start.</span></span>

<span data-ttu-id="5d4bd-221">O [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) tenta iniciar o processo de trabalho, mas falhar ao iniciar.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-221">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="5d4bd-222">A causa de uma falha de inicialização do processo normalmente pode ser determinada das entradas no log de eventos do aplicativo e do log de stdout do módulo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-222">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="5d4bd-223">Uma condição de falha comum é o aplicativo configurado incorretamente, direcionado a uma versão da estrutura compartilhada do ASP.NET Core que não está presente.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-223">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="5d4bd-224">Verifique quais versões da estrutura compartilhada do ASP.NET Core estão instaladas no computador de destino.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-224">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="5d4bd-225">A *estrutura compartilhada* é o conjunto de assemblies (arquivos *. dll*) instalado no computador e referenciado por um metapacote como `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-225">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="5d4bd-226">A referência do metapacote pode especificar a versão mínima necessária.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-226">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="5d4bd-227">Saiba mais em [A estrutura compartilhada](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="5d4bd-227">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="5d4bd-228">A página do erro *502.5 – Falha no Processo* é retornada quando um erro de configuração de hospedagem ou do aplicativo faz com que o processo de trabalho falhe:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-228">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="5d4bd-229">Falha ao iniciar o aplicativo (ErrorCode '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="5d4bd-229">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="5d4bd-230">O aplicativo falhou ao ser iniciado porque o assembly do aplicativo ( *.dll*) não pôde ser carregado.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-230">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="5d4bd-231">Esse erro ocorre quando há uma incompatibilidade de número de bits entre o aplicativo publicado e o processo w3wp/iisexpress.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-231">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="5d4bd-232">Confirme se a configuração de 32 bits do pool de aplicativos está correta:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-232">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="5d4bd-233">Selecione o pool de aplicativos no **Pools de Aplicativos** do Gerenciador do IIS.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-233">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="5d4bd-234">Selecione **Configurações Avançadas** em **Editar Pool de Aplicativos** no painel **Ações**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-234">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="5d4bd-235">Defina **Habilitar Aplicativos de 32 bits**:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-235">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="5d4bd-236">Se estiver implantando um aplicativo de 32 bits (x86), defina o valor como `True`.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-236">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="5d4bd-237">Se estiver implantando um aplicativo de 64 bits (x64), defina o valor como `False`.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-237">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="5d4bd-238">Confirme se não há um conflito entre uma `<Platform>` Propriedade do MSBuild no arquivo de projeto e o bit de bits publicado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-238">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="5d4bd-239">Redefinição de conexão</span><span class="sxs-lookup"><span data-stu-id="5d4bd-239">Connection reset</span></span>

<span data-ttu-id="5d4bd-240">Se um erro ocorrer após os cabeçalhos serem enviados, será tarde demais para o servidor enviar um **500 – Erro Interno do Servidor** no caso de um erro ocorrer.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-240">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="5d4bd-241">Isso geralmente acontece quando ocorre um erro durante a serialização de objetos complexos para uma resposta.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-241">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="5d4bd-242">Esse tipo de erro é exibida como um erro de *redefinição de conexão* no cliente.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-242">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="5d4bd-243">O [Log de aplicativo](xref:fundamentals/logging/index) pode ajudar a solucionar esses tipos de erros.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-243">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="5d4bd-244">Limites de inicialização padrão</span><span class="sxs-lookup"><span data-stu-id="5d4bd-244">Default startup limits</span></span>

<span data-ttu-id="5d4bd-245">O [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) é configurado com um *startupTimeLimit* padrão de 120 segundos.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-245">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="5d4bd-246">Quando deixado no valor padrão, um aplicativo pode levar até dois minutos para iniciar antes que uma falha do processo seja registrada em log pelo módulo.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-246">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="5d4bd-247">Para obter informações sobre como configurar o módulo, veja [Atributos do elemento aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="5d4bd-247">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="5d4bd-248">Solucionar problemas no serviço Azure App</span><span class="sxs-lookup"><span data-stu-id="5d4bd-248">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="5d4bd-249">Log de eventos do aplicativo (serviço Azure App)</span><span class="sxs-lookup"><span data-stu-id="5d4bd-249">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="5d4bd-250">Para acessar o Log de Eventos do Aplicativo, use a folha **Diagnosticar e solucionar problemas** no portal do Azure:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-250">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="5d4bd-251">No portal do Azure, abra o aplicativo nos **Serviços de Aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-251">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="5d4bd-252">Selecione **Diagnóstico e solução de problemas**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-252">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="5d4bd-253">Selecione o título **Ferramentas de Diagnóstico**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-253">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="5d4bd-254">Em **Ferramentas de Suporte**, selecione o botão **Eventos do Aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-254">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="5d4bd-255">Examine o erro mais recente fornecido pela entrada *IIS AspNetCoreModule* ou *IIS AspNetCoreModule V2* na coluna **Origem**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-255">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="5d4bd-256">Uma alternativa ao uso da folha **Diagnosticar e resolver problemas** é examinar o arquivo de Log de Eventos do Aplicativo diretamente usando o [Kudu](https://github.com/projectkudu/kudu/wiki):</span><span class="sxs-lookup"><span data-stu-id="5d4bd-256">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="5d4bd-257">Abra **Ferramentas Avançadas** na área **Ferramentas de Desenvolvimento**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-257">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="5d4bd-258">Selecione o botão **Ir&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="5d4bd-258">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="5d4bd-259">O console do Kudu é aberto em uma nova janela ou guia do navegador.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-259">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="5d4bd-260">Usando a barra de navegação na parte superior da página, abra **Console de depuração** e selecione **CMD**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-260">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="5d4bd-261">Abra a pasta **LogFiles**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-261">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="5d4bd-262">Selecione o ícone de lápis ao lado do arquivo *eventlog.xml*.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-262">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="5d4bd-263">Examine o log.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-263">Examine the log.</span></span> <span data-ttu-id="5d4bd-264">Role até o final do log para ver os eventos mais recentes.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-264">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="5d4bd-265">Execute o aplicativo no console do Kudu</span><span class="sxs-lookup"><span data-stu-id="5d4bd-265">Run the app in the Kudu console</span></span>

<span data-ttu-id="5d4bd-266">Muitos erros de inicialização não produzem informações úteis no Log de Eventos do Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-266">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="5d4bd-267">Você pode executar o aplicativo no Console de Execução Remota do [Kudu](https://github.com/projectkudu/kudu/wiki) para descobrir o erro:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-267">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="5d4bd-268">Abra **Ferramentas Avançadas** na área **Ferramentas de Desenvolvimento**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-268">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="5d4bd-269">Selecione o botão **Ir&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="5d4bd-269">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="5d4bd-270">O console do Kudu é aberto em uma nova janela ou guia do navegador.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-270">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="5d4bd-271">Usando a barra de navegação na parte superior da página, abra **Console de depuração** e selecione **CMD**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-271">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="5d4bd-272">Testar um aplicativo de 32 bits (x86)</span><span class="sxs-lookup"><span data-stu-id="5d4bd-272">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="5d4bd-273">**Versão atual**</span><span class="sxs-lookup"><span data-stu-id="5d4bd-273">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="5d4bd-274">Execute o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-274">Run the app:</span></span>
   * <span data-ttu-id="5d4bd-275">Se o aplicativo é uma [implantação dependente de estrutura](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="5d4bd-275">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```console
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="5d4bd-276">Se o aplicativo é uma [implantação autossuficiente](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="5d4bd-276">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="5d4bd-277">A saída do console do aplicativo, mostrando eventuais erros, é conectada ao console do Kudu.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-277">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="5d4bd-278">**Implantação dependente de estrutura em execução em uma versão de visualização**</span><span class="sxs-lookup"><span data-stu-id="5d4bd-278">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="5d4bd-279">*Requer a instalação da extensão de site de tempo de execução do ASP.NET Core {VERSION} (x86).*</span><span class="sxs-lookup"><span data-stu-id="5d4bd-279">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="5d4bd-280">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` é a versão de tempo de execução)</span><span class="sxs-lookup"><span data-stu-id="5d4bd-280">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="5d4bd-281">Execute o aplicativo: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="5d4bd-281">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="5d4bd-282">A saída do console do aplicativo, mostrando eventuais erros, é conectada ao console do Kudu.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-282">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="5d4bd-283">Testar um aplicativo de 64 bits (x64)</span><span class="sxs-lookup"><span data-stu-id="5d4bd-283">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="5d4bd-284">**Versão atual**</span><span class="sxs-lookup"><span data-stu-id="5d4bd-284">**Current release**</span></span>

* <span data-ttu-id="5d4bd-285">Se o aplicativo for uma [implantação dependente de estrutura](/dotnet/core/deploying/#framework-dependent-deployments-fdd) de 64 bits (x64):</span><span class="sxs-lookup"><span data-stu-id="5d4bd-285">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="5d4bd-286">Execute o aplicativo: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="5d4bd-286">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="5d4bd-287">Se o aplicativo é uma [implantação autossuficiente](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="5d4bd-287">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="5d4bd-288">Execute o aplicativo: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="5d4bd-288">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="5d4bd-289">A saída do console do aplicativo, mostrando eventuais erros, é conectada ao console do Kudu.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-289">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="5d4bd-290">**Implantação dependente de estrutura em execução em uma versão de visualização**</span><span class="sxs-lookup"><span data-stu-id="5d4bd-290">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="5d4bd-291">*Requer a instalação da extensão de site de tempo de execução do ASP.NET Core {VERSION} (x64).*</span><span class="sxs-lookup"><span data-stu-id="5d4bd-291">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="5d4bd-292">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` é a versão de tempo de execução)</span><span class="sxs-lookup"><span data-stu-id="5d4bd-292">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="5d4bd-293">Execute o aplicativo: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="5d4bd-293">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="5d4bd-294">A saída do console do aplicativo, mostrando eventuais erros, é conectada ao console do Kudu.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-294">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="5d4bd-295">Log de stdout do módulo ASP.NET Core (serviço Azure App)</span><span class="sxs-lookup"><span data-stu-id="5d4bd-295">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="5d4bd-296">O log de stdout do Módulo do ASP.NET Core geralmente registra mensagens de erro úteis não encontradas no Log de Eventos do Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-296">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="5d4bd-297">Para habilitar e exibir logs de stdout:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-297">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="5d4bd-298">Navegue até a folha **Diagnosticar e resolver problemas** no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-298">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="5d4bd-299">Em **SELECIONAR CATEGORIA DE PROBLEMA**, selecione o botão **Aplicativo Web Inoperante**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-299">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="5d4bd-300">Em **Soluções Sugeridas** > **Habilitar o Redirecionamento de Log de Stdout**, selecione o botão para **Abrir o Console do Kudu para editar o Web.Config**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-300">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="5d4bd-301">No **Console de Diagnóstico** do Kudu, abra as pastas no caminho **site** > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-301">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="5d4bd-302">Role para baixo para revelar o arquivo *web.config* na parte inferior da lista.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-302">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="5d4bd-303">Clique no ícone de lápis ao lado do arquivo *web.config*.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-303">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="5d4bd-304">Defina **stdoutLogEnabled** para `true` e altere o caminho **stdoutLogFile** para `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-304">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="5d4bd-305">Selecione **Salvar** para salvar o arquivo *web.config* atualizado.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-305">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="5d4bd-306">Faça uma solicitação ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-306">Make a request to the app.</span></span>
1. <span data-ttu-id="5d4bd-307">Retorne para o portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-307">Return to the Azure portal.</span></span> <span data-ttu-id="5d4bd-308">Selecione a folha **Ferramentas Avançadas** na área **FERRAMENTAS DE DESENVOLVIMENTO**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-308">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="5d4bd-309">Selecione o botão **Ir&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="5d4bd-309">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="5d4bd-310">O console do Kudu é aberto em uma nova janela ou guia do navegador.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-310">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="5d4bd-311">Usando a barra de navegação na parte superior da página, abra **Console de depuração** e selecione **CMD**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-311">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="5d4bd-312">Selecione a pasta **LogFiles**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-312">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="5d4bd-313">Inspecione a coluna **Modificado em** e selecione o ícone de lápis para editar o log de stdout com a data da última modificação.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-313">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="5d4bd-314">Quando o arquivo de log é aberto, o erro é exibido.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-314">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="5d4bd-315">Desabilite o registro em log de stdout quando a solução de problemas for concluída:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-315">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="5d4bd-316">No **Console de Diagnóstico** do Kudu, retorne para o caminho **site** > **wwwroot** para revelar o arquivo *web.config*.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-316">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="5d4bd-317">Abra o arquivo **web.config** novamente, selecionando o ícone de lápis.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-317">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="5d4bd-318">Defina **stdoutLogEnabled** para `false`.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-318">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="5d4bd-319">Selecione **Salvar** para salvar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-319">Select **Save** to save the file.</span></span>

<span data-ttu-id="5d4bd-320">Para obter mais informações, consulte <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-320">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="5d4bd-321">Falha ao desabilitar o log de stdout pode levar a falhas de aplicativo ou de servidor.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-321">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="5d4bd-322">Não há limites para o tamanho do arquivo de log ou para o número de arquivos de log criados.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-322">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="5d4bd-323">Somente use o log de stdout para solucionar problemas de inicialização de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-323">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="5d4bd-324">Para registro em log geral em um aplicativo ASP.NET Core após a inicialização, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-324">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="5d4bd-325">Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="5d4bd-325">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="5d4bd-326">Log de depuração do módulo ASP.NET Core (serviço Azure App)</span><span class="sxs-lookup"><span data-stu-id="5d4bd-326">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="5d4bd-327">O log de depuração do Módulo do ASP.NET Core fornece registro em log adicional e mais profundo do Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-327">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="5d4bd-328">Para habilitar e exibir logs de stdout:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-328">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="5d4bd-329">Para habilitar o log de diagnóstico avançado, execute um destes procedimentos:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-329">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="5d4bd-330">Siga as instruções em [Logs de diagnóstico avançados](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) para configurar o aplicativo para um log de diagnósticos avançado.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-330">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="5d4bd-331">Reimplante o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-331">Redeploy the app.</span></span>
   * <span data-ttu-id="5d4bd-332">Adicione a `<handlerSettings>` mostrada em [Logs de diagnóstico avançados](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) para o arquivo *web.config* do aplicativo ao vivo usando o console do Kudu:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-332">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="5d4bd-333">Abra **Ferramentas Avançadas** na área **Ferramentas de Desenvolvimento**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-333">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="5d4bd-334">Selecione o botão **Ir&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="5d4bd-334">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="5d4bd-335">O console do Kudu é aberto em uma nova janela ou guia do navegador.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-335">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="5d4bd-336">Usando a barra de navegação na parte superior da página, abra **Console de depuração** e selecione **CMD**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-336">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="5d4bd-337">Abra as pastas no caminho **site** > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-337">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="5d4bd-338">Edite o arquivo *web.config* selecionando o botão de lápis.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-338">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="5d4bd-339">Adicione a seção `<handlerSettings>` conforme mostrado em [Logs de diagnóstico avançados](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span><span class="sxs-lookup"><span data-stu-id="5d4bd-339">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="5d4bd-340">Selecione o botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-340">Select the **Save** button.</span></span>
1. <span data-ttu-id="5d4bd-341">Abra **Ferramentas Avançadas** na área **Ferramentas de Desenvolvimento**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-341">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="5d4bd-342">Selecione o botão **Ir&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="5d4bd-342">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="5d4bd-343">O console do Kudu é aberto em uma nova janela ou guia do navegador.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-343">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="5d4bd-344">Usando a barra de navegação na parte superior da página, abra **Console de depuração** e selecione **CMD**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-344">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="5d4bd-345">Abra as pastas no caminho **site** > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-345">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="5d4bd-346">Se você não fornecer um caminho para o arquivo *aspnetcore-debug.log*, o arquivo aparecerá na lista.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-346">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="5d4bd-347">Se você tiver fornecido um caminho, navegue até o local do arquivo de log.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-347">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="5d4bd-348">Abra o arquivo de log com o botão de lápis ao lado do nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-348">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="5d4bd-349">Desabilite o registro em log de depuração quando a solução de problemas for concluída:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-349">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="5d4bd-350">Para desabilitar o log de depuração avançado, execute um destes procedimentos:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-350">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="5d4bd-351">Remova o `<handlerSettings>` do arquivo *web.config* localmente e reimplante o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-351">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="5d4bd-352">Use o console do Kudu para editar o arquivo *web.config* e remover a seção `<handlerSettings>`.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-352">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="5d4bd-353">Salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-353">Save the file.</span></span>

<span data-ttu-id="5d4bd-354">Para obter mais informações, consulte <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-354">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="5d4bd-355">A falha ao desabilitar o log de depuração pode levar a falhas de aplicativo ou de servidor.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-355">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="5d4bd-356">Não há nenhum limite no tamanho do arquivo de log.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-356">There's no limit on log file size.</span></span> <span data-ttu-id="5d4bd-357">Somente use o log de depuração para solucionar problemas de inicialização de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-357">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="5d4bd-358">Para registro em log geral em um aplicativo ASP.NET Core após a inicialização, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-358">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="5d4bd-359">Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="5d4bd-359">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker-end

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="5d4bd-360">Aplicativo lento ou suspenso (serviço de Azure App)</span><span class="sxs-lookup"><span data-stu-id="5d4bd-360">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="5d4bd-361">Para saber mais sobre quando um aplicativo responde lentamente ou trava em uma solicitação, confira os seguintes artigos:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-361">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="5d4bd-362">Solucionar problemas de desempenho lento do aplicativo Web no Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="5d4bd-362">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="5d4bd-363">Use a Extensão de Site do Diagnosticador de Falhas para capturar o despejo para problemas de exceção intermitentes ou problemas de desempenho no aplicativo Web do Azure</span><span class="sxs-lookup"><span data-stu-id="5d4bd-363">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="5d4bd-364">Folhas de monitoramento</span><span class="sxs-lookup"><span data-stu-id="5d4bd-364">Monitoring blades</span></span>

<span data-ttu-id="5d4bd-365">As folhas de monitoramento fornecem uma experiência alternativa de solução de problemas para os métodos descritos anteriormente no tópico.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-365">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="5d4bd-366">Essas folhas podem ser usadas para diagnosticar erros da série 500.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-366">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="5d4bd-367">Verifique se as Extensões do ASP.NET Core estão instaladas.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-367">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="5d4bd-368">Se as extensões não estiverem instaladas, instale-as manualmente:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-368">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="5d4bd-369">Na seção de folha **FERRAMENTAS DE DESENVOLVIMENTO**, selecione a folha **Extensões**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-369">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="5d4bd-370">As **Extensões do ASP.NET Core** devem aparecer na lista.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-370">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="5d4bd-371">Se as extensões não estiverem instaladas, selecione o botão **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-371">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="5d4bd-372">Escolha as **Extensões do ASP.NET Core** da lista.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-372">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="5d4bd-373">Selecione **OK** para aceitar os termos legais.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-373">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="5d4bd-374">Selecione **OK** na folha **Adicionar extensão**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-374">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="5d4bd-375">Uma mensagem pop-up informativa indica quando as extensões são instaladas com êxito.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-375">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="5d4bd-376">Se o registro em log de stdout não estiver habilitado, siga estas etapas:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-376">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="5d4bd-377">No portal do Azure, selecione a folha **Ferramentas Avançadas** na área **FERRAMENTAS DE DESENVOLVIMENTO**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-377">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="5d4bd-378">Selecione o botão **Ir&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="5d4bd-378">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="5d4bd-379">O console do Kudu é aberto em uma nova janela ou guia do navegador.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-379">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="5d4bd-380">Usando a barra de navegação na parte superior da página, abra **Console de depuração** e selecione **CMD**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-380">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="5d4bd-381">Abra as pastas no caminho **site** > **wwwroot** e role para baixo para revelar o arquivo *web.config* na parte inferior da lista.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-381">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="5d4bd-382">Clique no ícone de lápis ao lado do arquivo *web.config*.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-382">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="5d4bd-383">Defina **stdoutLogEnabled** para `true` e altere o caminho **stdoutLogFile** para `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-383">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="5d4bd-384">Selecione **Salvar** para salvar o arquivo *web.config* atualizado.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-384">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="5d4bd-385">Prossiga para ativar o log de diagnóstico:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-385">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="5d4bd-386">No portal do Azure, selecione a folha **Logs de diagnóstico**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-386">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="5d4bd-387">Selecione a opção **Ligado** para **Log de Aplicativo (Sistema de arquivos)** e **Mensagens de erro detalhadas**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-387">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="5d4bd-388">Selecione o botão **Salvar** na parte superior da folha.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-388">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="5d4bd-389">Para incluir o rastreamento de solicitação com falha, também conhecido como FREB (Buffer de Evento de Solicitação com Falha), selecione a opção **Ligado** para o **Rastreamento de solicitação com falha**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-389">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="5d4bd-390">Selecione a folha **Fluxo de log**, que é listada imediatamente sob a folha **Logs de diagnóstico** no portal.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-390">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="5d4bd-391">Faça uma solicitação ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-391">Make a request to the app.</span></span>
1. <span data-ttu-id="5d4bd-392">Dentro dos dados de fluxo de log, a causa do erro é indicada.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-392">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="5d4bd-393">Sempre desabilite o registro em log de stdout após concluir a solução de problemas.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-393">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="5d4bd-394">Para exibir os logs de rastreamento de solicitação com falha (logs FREB):</span><span class="sxs-lookup"><span data-stu-id="5d4bd-394">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="5d4bd-395">Navegue até a folha **Diagnosticar e resolver problemas** no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-395">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="5d4bd-396">Selecione **Logs de Rastreamento de Solicitação com Falha** da área **FERRAMENTAS DE SUPORTE** da barra lateral.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-396">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="5d4bd-397">Confira a [seção de Rastreamentos de solicitação com falha no tópico Habilitar o log de diagnósticos para aplicativos Web no Serviço de Aplicativo do Azure](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) e as [Perguntas frequentes do desempenho do aplicativo para Aplicativos Web no Azure: como ativar o rastreamento de solicitação com falha?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-397">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="5d4bd-398">Para obter mais informações, veja [Habilitar log de diagnósticos para aplicativos Web no Serviço de Aplicativo do Azure](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="5d4bd-398">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="5d4bd-399">Falha ao desabilitar o log de stdout pode levar a falhas de aplicativo ou de servidor.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-399">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="5d4bd-400">Não há limites para o tamanho do arquivo de log ou para o número de arquivos de log criados.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-400">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="5d4bd-401">Para registro em log de rotina em um aplicativo ASP.NET Core, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-401">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="5d4bd-402">Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="5d4bd-402">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="5d4bd-403">Solucionar problemas no IIS</span><span class="sxs-lookup"><span data-stu-id="5d4bd-403">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="5d4bd-404">Log de eventos do aplicativo (IIS)</span><span class="sxs-lookup"><span data-stu-id="5d4bd-404">Application Event Log (IIS)</span></span>

<span data-ttu-id="5d4bd-405">Acesse o Log de Eventos do Aplicativo:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-405">Access the Application Event Log:</span></span>

1. <span data-ttu-id="5d4bd-406">Abra o menu Iniciar, procure **Visualizador de Eventos** e, em seguida, selecione o aplicativo **Visualizador de Eventos**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-406">Open the Start menu, search for **Event Viewer**, and then select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="5d4bd-407">No **Visualizador de Eventos**, abra o nó **Logs do Windows**.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-407">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="5d4bd-408">Selecione **Aplicativo** para abrir o Log de Eventos do Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-408">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="5d4bd-409">Procure erros associados ao aplicativo com falha.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-409">Search for errors associated with the failing app.</span></span> <span data-ttu-id="5d4bd-410">Os erros têm um valor *Módulo AspNetCore do IIS* ou *Módulo AspNetCore do IIS Express* na coluna *Origem*.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-410">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="5d4bd-411">Execute o aplicativo em um prompt de comando</span><span class="sxs-lookup"><span data-stu-id="5d4bd-411">Run the app at a command prompt</span></span>

<span data-ttu-id="5d4bd-412">Muitos erros de inicialização não produzem informações úteis no Log de Eventos do Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-412">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="5d4bd-413">Você pode encontrar a causa de alguns erros ao executar o aplicativo em um prompt de comando no sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-413">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="5d4bd-414">Implantação dependente de estrutura</span><span class="sxs-lookup"><span data-stu-id="5d4bd-414">Framework-dependent deployment</span></span>

<span data-ttu-id="5d4bd-415">Se o aplicativo é uma [implantação dependente de estrutura](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="5d4bd-415">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="5d4bd-416">Em um prompt de comando, navegue até a pasta de implantação e execute o aplicativo, executando o assembly do aplicativo com *dotnet.exe*.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-416">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="5d4bd-417">No comando a seguir, substitua o nome do assembly do aplicativo por \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-417">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="5d4bd-418">A saída do console do aplicativo, mostrando eventuais erros, é gravada na janela do console.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-418">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="5d4bd-419">Se os erros ocorrerem ao fazer uma solicitação para o aplicativo, faça uma solicitação para o host e a porta em que o Kestrel escuta.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-419">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="5d4bd-420">Usando o host e a porta padrão, faça uma solicitação para `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-420">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="5d4bd-421">Se o aplicativo responde normalmente no endereço do ponto de extremidade do Kestrel, a probabilidade de o problema estar relacionado à configuração de hospedagem é maior e, de estar relacionado ao aplicativo, menor.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-421">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="5d4bd-422">Implantação autocontida</span><span class="sxs-lookup"><span data-stu-id="5d4bd-422">Self-contained deployment</span></span>

<span data-ttu-id="5d4bd-423">Se o aplicativo é uma [implantação autossuficiente](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="5d4bd-423">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="5d4bd-424">Em um prompt de comando, navegue até a pasta de implantação e execute o arquivo executável do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-424">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="5d4bd-425">No comando a seguir, substitua o nome do assembly do aplicativo por \<assembly_name>: `<assembly_name>.exe`.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-425">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="5d4bd-426">A saída do console do aplicativo, mostrando eventuais erros, é gravada na janela do console.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-426">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="5d4bd-427">Se os erros ocorrerem ao fazer uma solicitação para o aplicativo, faça uma solicitação para o host e a porta em que o Kestrel escuta.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-427">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="5d4bd-428">Usando o host e a porta padrão, faça uma solicitação para `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-428">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="5d4bd-429">Se o aplicativo responde normalmente no endereço do ponto de extremidade do Kestrel, a probabilidade de o problema estar relacionado à configuração de hospedagem é maior e, de estar relacionado ao aplicativo, menor.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-429">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="5d4bd-430">Log de stdout do módulo ASP.NET Core (IIS)</span><span class="sxs-lookup"><span data-stu-id="5d4bd-430">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="5d4bd-431">Para habilitar e exibir logs de stdout:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-431">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="5d4bd-432">Navegue até a pasta de implantação do site no sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-432">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="5d4bd-433">Se a pasta *logs* não estiver presente, crie-a.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-433">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="5d4bd-434">Para obter instruções sobre como habilitar o MSBuild para criar a pasta *logs* na implantação automaticamente, veja o tópico [Estrutura de diretórios](xref:host-and-deploy/directory-structure).</span><span class="sxs-lookup"><span data-stu-id="5d4bd-434">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="5d4bd-435">Edite o arquivo *web.config*.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-435">Edit the *web.config* file.</span></span> <span data-ttu-id="5d4bd-436">Defina **stdoutLogEnabled** para `true` e altere o caminho **stdoutLogFile** para apontar para a pasta *logs* (por exemplo, `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="5d4bd-436">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="5d4bd-437">`stdout` no caminho é o prefixo do nome do arquivo de log.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-437">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="5d4bd-438">Uma extensão de arquivo, uma ID do processo e um carimbo de data/hora são adicionados automaticamente quando o log é criado.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-438">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="5d4bd-439">Usando `stdout` como o prefixo do nome do arquivo, um arquivo de log típico é nomeado *stdout_20180205184032_5412.log*.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-439">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="5d4bd-440">Verifique se a identidade do pool de aplicativos tem permissões de gravação para a pasta *logs*.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-440">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="5d4bd-441">Salve o arquivo *web.config* atualizado.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-441">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="5d4bd-442">Faça uma solicitação ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-442">Make a request to the app.</span></span>
1. <span data-ttu-id="5d4bd-443">Navegue até a pasta *logs*.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-443">Navigate to the *logs* folder.</span></span> <span data-ttu-id="5d4bd-444">Localize e abra o log de stdout mais recente.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-444">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="5d4bd-445">Estude o log em busca de erros.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-445">Study the log for errors.</span></span>

<span data-ttu-id="5d4bd-446">Desabilite o registro em log de stdout quando a solução de problemas for concluída:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-446">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="5d4bd-447">Edite o arquivo *web.config*.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-447">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="5d4bd-448">Defina **stdoutLogEnabled** para `false`.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-448">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="5d4bd-449">Salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-449">Save the file.</span></span>

<span data-ttu-id="5d4bd-450">Para obter mais informações, consulte <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-450">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="5d4bd-451">Falha ao desabilitar o log de stdout pode levar a falhas de aplicativo ou de servidor.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-451">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="5d4bd-452">Não há limites para o tamanho do arquivo de log ou para o número de arquivos de log criados.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-452">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="5d4bd-453">Para registro em log de rotina em um aplicativo ASP.NET Core, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-453">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="5d4bd-454">Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="5d4bd-454">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="5d4bd-455">Log de depuração do módulo ASP.NET Core (IIS)</span><span class="sxs-lookup"><span data-stu-id="5d4bd-455">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="5d4bd-456">Adicione as seguintes configurações do manipulador ao arquivo *Web. config* do aplicativo para habilitar ASP.NET Core log de depuração do módulo:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-456">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="5d4bd-457">Confirme se o caminho especificado para o log existe e se a identidade do pool de aplicativos tem permissões de gravação no local.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-457">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="5d4bd-458">Para obter mais informações, consulte <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-458">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

::: moniker-end

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="5d4bd-459">Habilitar a página de exceção do desenvolvedor</span><span class="sxs-lookup"><span data-stu-id="5d4bd-459">Enable the Developer Exception Page</span></span>

<span data-ttu-id="5d4bd-460">A [variável de ambiente `ASPNETCORE_ENVIRONMENT` pode ser adicionada ao web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) para executar o aplicativo no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-460">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="5d4bd-461">Desde que o ambiente não seja substituído na inicialização do aplicativo por `UseEnvironment` no compilador do host, definir a variável de ambiente permite que a [Página de Exceções do Desenvolvedor](xref:fundamentals/error-handling) apareça quando o aplicativo é executado.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-461">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="5d4bd-462">Configurar a variável de ambiente para `ASPNETCORE_ENVIRONMENT` só é recomendado para servidores de preparo e de teste que não estejam expostos à Internet.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-462">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="5d4bd-463">Remova a variável de ambiente do arquivo *web.config* após a solução de problemas.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-463">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="5d4bd-464">Para obter informações sobre como definir variáveis de ambiente no *web.config*, confira [Elemento filho environmentVariables de aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="5d4bd-464">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="5d4bd-465">Obter dados de um aplicativo</span><span class="sxs-lookup"><span data-stu-id="5d4bd-465">Obtain data from an app</span></span>

<span data-ttu-id="5d4bd-466">Se um aplicativo for capaz de responder às solicitações, obtenha as solicitações, conexão e dados adicionais do aplicativo que usar o middleware embutido de terminal.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-466">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="5d4bd-467">Para saber mais e obter um código de exemplo, consulte <xref:test/troubleshoot#obtain-data-from-an-app>.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-467">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="5d4bd-468">Aplicativo lento ou suspenso (IIS)</span><span class="sxs-lookup"><span data-stu-id="5d4bd-468">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="5d4bd-469">Um *despejo* é um instantâneo da memória do sistema e pode ajudar a determinar a causa de uma falha de aplicativo, de inicialização ou de um aplicativo lento.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-469">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="5d4bd-470">O aplicativo falha ou encontra uma exceção</span><span class="sxs-lookup"><span data-stu-id="5d4bd-470">App crashes or encounters an exception</span></span>

<span data-ttu-id="5d4bd-471">Obter e analisar um despejo de memória do [WER (Relatório de Erros do Windows)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="5d4bd-471">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="5d4bd-472">Crie uma pasta para armazenar os arquivos de despejo de memória em `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-472">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="5d4bd-473">O pool de aplicativos deve ter acesso para gravação à pasta.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-473">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="5d4bd-474">Execute o [script EnableDumps do PowerShell](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="5d4bd-474">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="5d4bd-475">Se o aplicativo usa o [modelo de hospedagem em processo](xref:host-and-deploy/iis/index#in-process-hosting-model), execute o script para *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-475">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="5d4bd-476">Se o aplicativo usa o [modelo de hospedagem fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model), execute o script para *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-476">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="5d4bd-477">Execute o aplicativo sob as condições que causam a falha.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-477">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="5d4bd-478">Após a falha, execute o [script DisableDumps do PowerShell](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="5d4bd-478">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="5d4bd-479">Se o aplicativo usa o [modelo de hospedagem em processo](xref:host-and-deploy/iis/index#in-process-hosting-model), execute o script para *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-479">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="5d4bd-480">Se o aplicativo usa o [modelo de hospedagem fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model), execute o script para *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-480">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="5d4bd-481">Depois que um aplicativo falhar e a coleta de despejo de memória for concluída, o aplicativo terá permissão para encerrar normalmente.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-481">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="5d4bd-482">O script do PowerShell configura o WER para coletar até cinco despejos de memória por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-482">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="5d4bd-483">Os despejos de memória podem usar uma grande quantidade de espaço em disco (até vários gigabytes cada).</span><span class="sxs-lookup"><span data-stu-id="5d4bd-483">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="5d4bd-484">O aplicativo trava, falha durante a inicialização ou executa normalmente</span><span class="sxs-lookup"><span data-stu-id="5d4bd-484">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="5d4bd-485">Quando um aplicativo *travar* (para de responder, mas não falha), falhar durante a inicialização ou executar normalmente, veja [Arquivos de despejo de memória do modo de usuário: escolher a melhor ferramenta](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) para selecionar uma ferramenta adequada para produzir o despejo de memória.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-485">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="5d4bd-486">Analisar o despejo de memória</span><span class="sxs-lookup"><span data-stu-id="5d4bd-486">Analyze the dump</span></span>

<span data-ttu-id="5d4bd-487">Um despejo de memória pode ser analisado usando várias abordagens.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-487">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="5d4bd-488">Para obter mais informações, confira [Analisando um arquivo de despejo de memória do modo de usuário](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="5d4bd-488">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="5d4bd-489">Limpar caches de pacote</span><span class="sxs-lookup"><span data-stu-id="5d4bd-489">Clear package caches</span></span>

<span data-ttu-id="5d4bd-490">Às vezes, um aplicativo funcional falha imediatamente após a atualização do SDK do .NET Core no computador de desenvolvimento ou a alteração das versões do pacote no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-490">Sometimes a functioning app fails immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="5d4bd-491">Em alguns casos, pacotes incoerentes podem interromper um aplicativo ao executar atualizações principais.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-491">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="5d4bd-492">A maioria desses problemas pode ser corrigida seguindo estas instruções:</span><span class="sxs-lookup"><span data-stu-id="5d4bd-492">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="5d4bd-493">Exclua as pastas *bin* e *obj*.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-493">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="5d4bd-494">Limpe os caches de pacote executando `dotnet nuget locals all --clear` em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-494">Clear the package caches by executing `dotnet nuget locals all --clear` from a command shell.</span></span>

   <span data-ttu-id="5d4bd-495">A limpeza de caches de pacote também pode ser realizada com a ferramenta [NuGet. exe](https://www.nuget.org/downloads) e `nuget locals all -clear`executando o comando.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-495">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="5d4bd-496">*nuget.exe* não é uma instalação fornecida com o sistema operacional Windows Desktop e devem ser obtidos separadamente do [site do NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="5d4bd-496">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="5d4bd-497">Restaure e recompile o projeto.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-497">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="5d4bd-498">Exclua todos os arquivos na pasta de implantação no servidor antes de reimplantar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5d4bd-498">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5d4bd-499">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5d4bd-499">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="5d4bd-500">Documentação do Azure</span><span class="sxs-lookup"><span data-stu-id="5d4bd-500">Azure documentation</span></span>

* [<span data-ttu-id="5d4bd-501">Application Insights para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5d4bd-501">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="5d4bd-502">Seção aplicativos Web de depuração remota de solução de problemas de um aplicativo Web no serviço Azure App usando o Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d4bd-502">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="5d4bd-503">Visão geral de diagnóstico do Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="5d4bd-503">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="5d4bd-504">Como: monitorar aplicativos no Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="5d4bd-504">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="5d4bd-505">Solucionar problemas de um aplicativo Web no Serviço de Aplicativo do Azure usando o Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d4bd-505">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="5d4bd-506">Solucionar problemas de erros HTTP de "502 – gateway incorreto" e "503 – serviço não disponível" em seus aplicativos Web do Azure</span><span class="sxs-lookup"><span data-stu-id="5d4bd-506">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="5d4bd-507">Solucionar problemas de desempenho lento do aplicativo Web no Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="5d4bd-507">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="5d4bd-508">Perguntas frequentes sobre o desempenho do aplicativo para aplicativos Web no Azure</span><span class="sxs-lookup"><span data-stu-id="5d4bd-508">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="5d4bd-509">Área restrita do aplicativo Web do Azure (limitações de execução de tempo de execução do Serviço de Aplicativo)</span><span class="sxs-lookup"><span data-stu-id="5d4bd-509">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="5d4bd-510">Azure Friday: experiência de diagnóstico e solução de problemas do Serviço de Aplicativo do Azure (vídeo com 12 minutos)</span><span class="sxs-lookup"><span data-stu-id="5d4bd-510">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="5d4bd-511">Documentação do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d4bd-511">Visual Studio documentation</span></span>

* [<span data-ttu-id="5d4bd-512">ASP.NET Core de depuração remota no IIS no Azure no Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="5d4bd-512">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="5d4bd-513">ASP.NET Core de depuração remota em um computador IIS remoto no Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="5d4bd-513">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="5d4bd-514">Aprenda a depurar usando o Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d4bd-514">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="5d4bd-515">Documentação do Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5d4bd-515">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="5d4bd-516">Depurar com o Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5d4bd-516">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)
