---
title: Referência de erros comuns para o Serviço de Aplicativo do Azure e o IIS com o ASP.NET Core
author: guardrex
description: Obtenha conselhos de solução de problemas para erros comuns ao hospedar aplicativos ASP.NET Core no Serviço de Aplicativos do Azure e no IIS.
ms.author: riande
ms.custom: mvc
ms.date: 02/28/2019
uid: host-and-deploy/azure-iis-errors-reference
ms.openlocfilehash: 1c8cb31b306b38ec17596af0a84f22ca0e3d911c
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57346220"
---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a><span data-ttu-id="34c7d-103">Referência de erros comuns para o Serviço de Aplicativo do Azure e o IIS com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="34c7d-103">Common errors reference for Azure App Service and IIS with ASP.NET Core</span></span>

<span data-ttu-id="34c7d-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="34c7d-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="34c7d-105">Este tópico oferece conselhos de solução de problemas para erros comuns ao hospedar aplicativos ASP.NET Core no Serviço de Aplicativos do Azure e no IIS.</span><span class="sxs-lookup"><span data-stu-id="34c7d-105">This topic offers troubleshooting advice for common errors when hosting ASP.NET Core apps on Azure Apps Service and IIS.</span></span>

<span data-ttu-id="34c7d-106">Colete as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="34c7d-106">Collect the following information:</span></span>

* <span data-ttu-id="34c7d-107">Comportamento do navegador (código de status e mensagem de erro)</span><span class="sxs-lookup"><span data-stu-id="34c7d-107">Browser behavior (status code and error message)</span></span>
* <span data-ttu-id="34c7d-108">Entradas do Log de Eventos do Aplicativo</span><span class="sxs-lookup"><span data-stu-id="34c7d-108">Application Event Log entries</span></span>
  * <span data-ttu-id="34c7d-109">Serviço de Aplicativo do Azure &ndash; Confira <xref:host-and-deploy/azure-apps/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="34c7d-109">Azure App Service &ndash; See <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>
  * <span data-ttu-id="34c7d-110">IIS</span><span class="sxs-lookup"><span data-stu-id="34c7d-110">IIS</span></span>
    1. <span data-ttu-id="34c7d-111">Selecione **Iniciar** no menu **Windows**, digite *Visualizador de Eventos* e pressione **Enter**.</span><span class="sxs-lookup"><span data-stu-id="34c7d-111">Select **Start** on the **Windows** menu, type *Event Viewer*, and press **Enter**.</span></span>
    1. <span data-ttu-id="34c7d-112">Após o **Visualizador de Eventos** ser aberto, expanda **Logs do Windows** > **Aplicativo** na barra lateral.</span><span class="sxs-lookup"><span data-stu-id="34c7d-112">After the **Event Viewer** opens, expand **Windows Logs** > **Application** in the sidebar.</span></span>
* <span data-ttu-id="34c7d-113">Entradas do log de depuração e stdout do Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="34c7d-113">ASP.NET Core Module stdout and debug log entries</span></span>
  * <span data-ttu-id="34c7d-114">Serviço de Aplicativo do Azure &ndash; Confira <xref:host-and-deploy/azure-apps/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="34c7d-114">Azure App Service &ndash; See <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>
  * <span data-ttu-id="34c7d-115">IIS &ndash; Siga as instruções nas seções [Criação de log e redirecionamento](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) e [Logs de diagnóstico avançados](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) do tópico Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="34c7d-115">IIS &ndash; Follow the instructions in the [Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) and [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) sections of the ASP.NET Core Module topic.</span></span>

<span data-ttu-id="34c7d-116">Compare as informações do erro para os erros comuns a seguir.</span><span class="sxs-lookup"><span data-stu-id="34c7d-116">Compare error information to the following common errors.</span></span> <span data-ttu-id="34c7d-117">Se uma correspondência for encontrada, siga o aviso de solução de problemas.</span><span class="sxs-lookup"><span data-stu-id="34c7d-117">If a match is found, follow the troubleshooting advice.</span></span>

<span data-ttu-id="34c7d-118">A lista de erros neste tópico não é exaustiva.</span><span class="sxs-lookup"><span data-stu-id="34c7d-118">The list of errors in this topic isn't exhaustive.</span></span> <span data-ttu-id="34c7d-119">Se você encontrar um erro não listado aqui, abra um novo problema usando o botão **Comentários sobre o Conteúdo** na parte inferior deste tópico com instruções detalhadas sobre como reproduzir o erro.</span><span class="sxs-lookup"><span data-stu-id="34c7d-119">If you encounter an error not listed here, open a new issue using the **Content feedback** button at the bottom of this topic with detailed instructions on how to reproduce the error.</span></span>

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="installer-unable-to-obtain-vc-redistributable"></a><span data-ttu-id="34c7d-120">O instalador não pode obter os Pacotes Redistribuíveis do VC++</span><span class="sxs-lookup"><span data-stu-id="34c7d-120">Installer unable to obtain VC++ Redistributable</span></span>

* <span data-ttu-id="34c7d-121">**Exceção do instalador:** 0x80072efd **–OU–** 0x80072f76 – erro não especificado</span><span class="sxs-lookup"><span data-stu-id="34c7d-121">**Installer Exception:** 0x80072efd **--OR--** 0x80072f76 - Unspecified error</span></span>

* <span data-ttu-id="34c7d-122">**Exceção do log do instalador&#8224;:** Erro 0x80072efd **–OU–** 0x80072f76: Falha ao executar o pacote EXE</span><span class="sxs-lookup"><span data-stu-id="34c7d-122">**Installer Log Exception&#8224;:** Error 0x80072efd **--OR--** 0x80072f76: Failed to execute EXE package</span></span>

  <span data-ttu-id="34c7d-123">&#8224;O log está localizado em *C:\Users\{USER}\AppData\Local\Temp\dd_DotNetCoreWinSvrHosting__{TIMESTAMP}.log*.</span><span class="sxs-lookup"><span data-stu-id="34c7d-123">&#8224;The log is located at *C:\Users\{USER}\AppData\Local\Temp\dd_DotNetCoreWinSvrHosting__{TIMESTAMP}.log*.</span></span>

<span data-ttu-id="34c7d-124">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="34c7d-124">Troubleshooting:</span></span>

<span data-ttu-id="34c7d-125">Se o sistema não tiver acesso à Internet durante a [instalação do pacote de hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle), essa exceção ocorrerá quando o instalador for impedido de obter os *Pacotes Redistribuíveis do Microsoft Visual C++ 2015*.</span><span class="sxs-lookup"><span data-stu-id="34c7d-125">If the system doesn't have Internet access while [installing the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle), this exception occurs when the installer is prevented from obtaining the *Microsoft Visual C++ 2015 Redistributable*.</span></span> <span data-ttu-id="34c7d-126">Obtenha um instalador do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=53840).</span><span class="sxs-lookup"><span data-stu-id="34c7d-126">Obtain an installer from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840).</span></span> <span data-ttu-id="34c7d-127">Se o instalador falhar, o servidor poderá não receber o tempo de execução do .NET Core necessário para hospedar uma [FDD (implantação dependente de estrutura)](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="34c7d-127">If the installer fails, the server may not receive the .NET Core runtime required to host a [framework-dependent deployment (FDD)](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span> <span data-ttu-id="34c7d-128">Se estiver hospedando uma FDD, confirme se o tempo de execução está instalado em **Programas e Recursos** ou **Aplicativos e recursos**.</span><span class="sxs-lookup"><span data-stu-id="34c7d-128">If hosting an FDD, confirm that the runtime is installed in **Programs & Features** or **Apps & features**.</span></span> <span data-ttu-id="34c7d-129">Se um tempo de execução específico for necessário, baixe o tempo de execução dos [Arquivos de Download do .NET](https://dotnet.microsoft.com/download/archives) e instale-o no sistema.</span><span class="sxs-lookup"><span data-stu-id="34c7d-129">If a specific runtime is required, download the runtime from the [.NET Download Archives](https://dotnet.microsoft.com/download/archives) and install it on the system.</span></span> <span data-ttu-id="34c7d-130">Depois de instalar o tempo de execução, reinicie o sistema ou o IIS executando **net stop was /y** seguido por **net start w3svc** em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="34c7d-130">After installing the runtime, restart the system or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a><span data-ttu-id="34c7d-131">O upgrade do sistema operacional removeu o Módulo do ASP.NET Core de 32 bits</span><span class="sxs-lookup"><span data-stu-id="34c7d-131">OS upgrade removed the 32-bit ASP.NET Core Module</span></span>

<span data-ttu-id="34c7d-132">**Log do Aplicativo:** A DLL do Módulo **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** falhou ao ser carregada.</span><span class="sxs-lookup"><span data-stu-id="34c7d-132">**Application Log:** The Module DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** failed to load.</span></span> <span data-ttu-id="34c7d-133">Os dados são o erro.</span><span class="sxs-lookup"><span data-stu-id="34c7d-133">The data is the error.</span></span>

<span data-ttu-id="34c7d-134">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="34c7d-134">Troubleshooting:</span></span>

<span data-ttu-id="34c7d-135">Arquivos que não são do sistema operacional no diretório **C:\Windows\SysWOW64\inetsrv** não são preservados durante um upgrade do sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="34c7d-135">Non-OS files in the **C:\Windows\SysWOW64\inetsrv** directory aren't preserved during an OS upgrade.</span></span> <span data-ttu-id="34c7d-136">Se o Módulo do ASP.NET Core estiver instalado antes de uma atualização do sistema operacional e, em seguida, qualquer pool de aplicativos for executado no modo de 32 bits após uma atualização do sistema operacional, esse problema será encontrado.</span><span class="sxs-lookup"><span data-stu-id="34c7d-136">If the ASP.NET Core Module is installed prior to an OS upgrade and then any app pool is run in 32-bit mode after an OS upgrade, this issue is encountered.</span></span> <span data-ttu-id="34c7d-137">Após um upgrade do sistema operacional, repare o Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="34c7d-137">After an OS upgrade, repair the ASP.NET Core Module.</span></span> <span data-ttu-id="34c7d-138">Veja [Instalar o pacote de Hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="34c7d-138">See [Install the .NET Core Hosting bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span> <span data-ttu-id="34c7d-139">Selecione **Reparar** ao executar o instalador.</span><span class="sxs-lookup"><span data-stu-id="34c7d-139">Select **Repair** when the installer is run.</span></span>

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a><span data-ttu-id="34c7d-140">Extensão de site ausente, extensões de site de 32 bits (x86) e 64 bits (x64) instaladas ou conjunto de bits incorreto do processo</span><span class="sxs-lookup"><span data-stu-id="34c7d-140">Missing site extension, 32-bit (x86) and 64-bit (x64) site extensions installed, or wrong process bitness set</span></span>

<span data-ttu-id="34c7d-141">*Aplica-se aos aplicativos hospedados pelos Serviços de Aplicativo do Azure.*</span><span class="sxs-lookup"><span data-stu-id="34c7d-141">*Applies to apps hosted by Azure App Services.*</span></span>

* <span data-ttu-id="34c7d-142">**Navegador:** Erro HTTP 500.0 – Falha de carregamento de manipulador em processo ANCM</span><span class="sxs-lookup"><span data-stu-id="34c7d-142">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span> 

* <span data-ttu-id="34c7d-143">**Log do Aplicativo:** A invocação do hostfxr para encontrar o manipulador de solicitação inprocess falha sem encontrar nenhuma dependência nativa.</span><span class="sxs-lookup"><span data-stu-id="34c7d-143">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="34c7d-144">Não foi possível localizar o manipulador de solicitação inprocess.</span><span class="sxs-lookup"><span data-stu-id="34c7d-144">Could not find inprocess request handler.</span></span> <span data-ttu-id="34c7d-145">Saída capturada da invocação do hostfxr: Não foi possível encontrar nenhuma versão de estrutura compatível.</span><span class="sxs-lookup"><span data-stu-id="34c7d-145">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="34c7d-146">A estrutura especificada 'Microsoft.AspNetCore.App', versão '{VERSION}-preview-\*' não foi encontrada.</span><span class="sxs-lookup"><span data-stu-id="34c7d-146">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span> <span data-ttu-id="34c7d-147">Falha ao iniciar o aplicativo '/LM/W3SVC/1416782824/ROOT', ErrorCode '0x8000ffff'.</span><span class="sxs-lookup"><span data-stu-id="34c7d-147">Failed to start application '/LM/W3SVC/1416782824/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="34c7d-148">**Log de stdout do Módulo do ASP.NET Core:** Não foi possível encontrar nenhuma versão de estrutura compatível.</span><span class="sxs-lookup"><span data-stu-id="34c7d-148">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="34c7d-149">A estrutura especificada 'Microsoft.AspNetCore.App', versão '{VERSION}-preview-\*' não foi encontrada.</span><span class="sxs-lookup"><span data-stu-id="34c7d-149">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="34c7d-150">**Log de depuração do módulo do ASP.NET Core:** A invocação do hostfxr para encontrar o manipulador de solicitação inprocess falha sem encontrar nenhuma dependência nativa.</span><span class="sxs-lookup"><span data-stu-id="34c7d-150">**ASP.NET Core Module Debug Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="34c7d-151">Isso provavelmente significa que o aplicativo está configurado incorretamente, verifique as versões do Microsoft.NetCore.App e Microsoft.AspNetCore.App que são afetadas pelo aplicativo e estão instaladas no computador.</span><span class="sxs-lookup"><span data-stu-id="34c7d-151">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="34c7d-152">HRESULT com falha retornou: 0x8000ffff.</span><span class="sxs-lookup"><span data-stu-id="34c7d-152">Failed HRESULT returned: 0x8000ffff.</span></span> <span data-ttu-id="34c7d-153">Não foi possível localizar o manipulador de solicitação inprocess.</span><span class="sxs-lookup"><span data-stu-id="34c7d-153">Could not find inprocess request handler.</span></span> <span data-ttu-id="34c7d-154">Não foi possível encontrar nenhuma versão de estrutura compatível.</span><span class="sxs-lookup"><span data-stu-id="34c7d-154">It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="34c7d-155">A estrutura especificada 'Microsoft.AspNetCore.App', versão '{VERSION}-preview-\*' não foi encontrada.</span><span class="sxs-lookup"><span data-stu-id="34c7d-155">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

::: moniker-end

<span data-ttu-id="34c7d-156">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="34c7d-156">Troubleshooting:</span></span>

* <span data-ttu-id="34c7d-157">Se estiver executando o aplicativo em um tempo de execução de visualização, instale a extensão de site de 32 bits (x86) **ou** de 64 bits (x64) que corresponda ao número de bit do aplicativo e à versão de tempo de execução do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="34c7d-157">If running the app on a preview runtime, install either the 32-bit (x86) **or** 64-bit (x64) site extension that matches the bitness of the app and the app's runtime version.</span></span> <span data-ttu-id="34c7d-158">**Não instale extensões ou várias versões de tempo de execução da extensão.**</span><span class="sxs-lookup"><span data-stu-id="34c7d-158">**Don't install both extensions or multiple runtime versions of the extension.**</span></span>

  * <span data-ttu-id="34c7d-159">Tempo de execução do ASP.NET Core {RUNTIME VERSION} (x86)</span><span class="sxs-lookup"><span data-stu-id="34c7d-159">ASP.NET Core {RUNTIME VERSION} (x86) Runtime</span></span>
  * <span data-ttu-id="34c7d-160">Tempo de execução do ASP.NET Core {RUNTIME VERSION} (x64)</span><span class="sxs-lookup"><span data-stu-id="34c7d-160">ASP.NET Core {RUNTIME VERSION} (x64) Runtime</span></span>

  <span data-ttu-id="34c7d-161">Reinicie o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="34c7d-161">Restart the app.</span></span> <span data-ttu-id="34c7d-162">Aguarde vários segundos até que o aplicativo seja reiniciado.</span><span class="sxs-lookup"><span data-stu-id="34c7d-162">Wait several seconds for the app to restart.</span></span> 

* <span data-ttu-id="34c7d-163">Se a execução do aplicativo em um tempo de execução de visualização e as [extensões de site](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) de 32 bits (x86) e 64 bits (x64) estiverem instaladas, desinstale a extensão de site que não corresponde ao número de bit do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="34c7d-163">If running the app on a preview runtime and both the 32-bit (x86) and 64-bit (x64) [site extensions](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) are installed, uninstall the site extension that doesn't match the bitness of the app.</span></span> <span data-ttu-id="34c7d-164">Depois de remover a extensão de site, reinicie o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="34c7d-164">After removing the site extension, restart the app.</span></span> <span data-ttu-id="34c7d-165">Aguarde vários segundos até que o aplicativo seja reiniciado.</span><span class="sxs-lookup"><span data-stu-id="34c7d-165">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="34c7d-166">Se executar o aplicativo em um tempo de execução de visualização e o número de bit da extensão de site corresponder ao do aplicativo, confirme se a *versão do tempo de execução* da extensão de site de visualização corresponde à versão do tempo de execução do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="34c7d-166">If running the app on a preview runtime and the site extension's bitness matches that of the app, confirm that the preview site extension's *runtime version* matches the app's runtime version.</span></span>

* <span data-ttu-id="34c7d-167">Confirme se a **Plataforma** do aplicativo em **Configurações do aplicativo** corresponde ao número de bit do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="34c7d-167">Confirm that the app's **Platform** in **Application Settings** matches the bitness of the app.</span></span>

<span data-ttu-id="34c7d-168">Para obter mais informações, consulte <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.</span><span class="sxs-lookup"><span data-stu-id="34c7d-168">For more information, see <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.</span></span>

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a><span data-ttu-id="34c7d-169">Um aplicativo x86 é implantado, mas o pool de aplicativos não está habilitado para aplicativos de 32 bits</span><span class="sxs-lookup"><span data-stu-id="34c7d-169">An x86 app is deployed but the app pool isn't enabled for 32-bit apps</span></span>

* <span data-ttu-id="34c7d-170">**Navegador:** Erro HTTP 500.30 – Falha de início no processo do ANCM</span><span class="sxs-lookup"><span data-stu-id="34c7d-170">**Browser:** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="34c7d-171">**Log do Aplicativo:** Aplicativo '/LM/W3SVC/5/ROOT' com raiz física '{PATH}' atingiu uma exceção gerenciada inesperada, código de exceção = '0xe0434352'.</span><span class="sxs-lookup"><span data-stu-id="34c7d-171">**Application Log:** Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' hit unexpected managed exception, exception code = '0xe0434352'.</span></span> <span data-ttu-id="34c7d-172">Verifique os logs de stderr para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="34c7d-172">Please check the stderr logs for more information.</span></span> <span data-ttu-id="34c7d-173">Aplicativo '/LM/W3SVC/5/ROOT' com raiz física '{PATH}' falhou ao carregar o clr e o aplicativo gerenciado.</span><span class="sxs-lookup"><span data-stu-id="34c7d-173">Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' failed to load clr and managed application.</span></span> <span data-ttu-id="34c7d-174">O thread de trabalho do CLR foi encerrado prematuramente</span><span class="sxs-lookup"><span data-stu-id="34c7d-174">CLR worker thread exited prematurely</span></span>

* <span data-ttu-id="34c7d-175">**Log de stdout do Módulo do ASP.NET Core:** O arquivo de log é criado, mas vazio.</span><span class="sxs-lookup"><span data-stu-id="34c7d-175">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="34c7d-176">**Log de depuração do módulo do ASP.NET Core:** HRESULT com falha retornou: 0x8007023e</span><span class="sxs-lookup"><span data-stu-id="34c7d-176">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8007023e</span></span>

::: moniker-end

<span data-ttu-id="34c7d-177">Esse cenário é interceptado pelo SDK ao publicar um aplicativo autocontido.</span><span class="sxs-lookup"><span data-stu-id="34c7d-177">This scenario is trapped by the SDK when publishing a self-contained app.</span></span> <span data-ttu-id="34c7d-178">O SDK produzirá um erro se o RID não coincidir com o destino da plataforma (por exemplo, RID `win10-x64` com `<PlatformTarget>x86</PlatformTarget>` no arquivo de projeto).</span><span class="sxs-lookup"><span data-stu-id="34c7d-178">The SDK produces an error if the RID doesn't match the platform target (for example, `win10-x64` RID with `<PlatformTarget>x86</PlatformTarget>` in the project file).</span></span>

<span data-ttu-id="34c7d-179">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="34c7d-179">Troubleshooting:</span></span>

<span data-ttu-id="34c7d-180">Para uma implantação dependente da estrutura x86 (`<PlatformTarget>x86</PlatformTarget>`), habilite o pool de aplicativos de IIS para aplicativos de 32 bits.</span><span class="sxs-lookup"><span data-stu-id="34c7d-180">For an x86 framework-dependent deployment (`<PlatformTarget>x86</PlatformTarget>`), enable the IIS app pool for 32-bit apps.</span></span> <span data-ttu-id="34c7d-181">No Gerenciador do IIS, abra as **Configurações Avançadas** do pool de aplicativos e defina **Habilitar Aplicativos de 32 Bits** como **Verdadeiro**.</span><span class="sxs-lookup"><span data-stu-id="34c7d-181">In IIS Manager, open the app pool's **Advanced Settings** and set **Enable 32-Bit Applications** to **True**.</span></span>

## <a name="platform-conflicts-with-rid"></a><span data-ttu-id="34c7d-182">Conflitos de plataforma com o RID</span><span class="sxs-lookup"><span data-stu-id="34c7d-182">Platform conflicts with RID</span></span>

* <span data-ttu-id="34c7d-183">**Navegador:** Erro HTTP 502.5 – falha do processo</span><span class="sxs-lookup"><span data-stu-id="34c7d-183">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="34c7d-184">**Log do Aplicativo:** O aplicativo 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' com raiz física 'C:\{PATH}\' falhou ao iniciar o processo com a linha de comando '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ', ErrorCode = '0x80004005 : ff.</span><span class="sxs-lookup"><span data-stu-id="34c7d-184">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ', ErrorCode = '0x80004005 : ff.</span></span>

* <span data-ttu-id="34c7d-185">**Log de stdout do Módulo do ASP.NET Core:** Exceção sem tratamento: System.BadImageFormatException: Não foi possível carregar arquivo ou o assembly '{ASSEMBLY}.dll'.</span><span class="sxs-lookup"><span data-stu-id="34c7d-185">**ASP.NET Core Module stdout Log:** Unhandled Exception: System.BadImageFormatException: Could not load file or assembly '{ASSEMBLY}.dll'.</span></span> <span data-ttu-id="34c7d-186">Foi feita uma tentativa de carregar um programa com um formato incorreto.</span><span class="sxs-lookup"><span data-stu-id="34c7d-186">An attempt was made to load a program with an incorrect format.</span></span>

<span data-ttu-id="34c7d-187">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="34c7d-187">Troubleshooting:</span></span>

* <span data-ttu-id="34c7d-188">Confirme se o aplicativo é executado localmente no Kestrel.</span><span class="sxs-lookup"><span data-stu-id="34c7d-188">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="34c7d-189">Uma falha do processo pode ser o resultado de um problema no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="34c7d-189">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="34c7d-190">Para obter mais informações, confira [Solução de problemas (IIS)](xref:host-and-deploy/iis/troubleshoot) ou [Solução de problemas (Serviço de Aplicativo do Azure)](xref:host-and-deploy/azure-apps/troubleshoot).</span><span class="sxs-lookup"><span data-stu-id="34c7d-190">For more information, see [Troubleshoot (IIS)](xref:host-and-deploy/iis/troubleshoot) or [Troubleshoot (Azure App Service)](xref:host-and-deploy/azure-apps/troubleshoot).</span></span>

* <span data-ttu-id="34c7d-191">Se essa exceção ocorrer para uma implantação dos Aplicativos do Azure ao fazer upgrade de um aplicativo e implantar assemblies mais recentes, exclua manualmente todos os arquivos da implantação anterior.</span><span class="sxs-lookup"><span data-stu-id="34c7d-191">If this exception occurs for an Azure Apps deployment when upgrading an app and deploying newer assemblies, manually delete all files from the prior deployment.</span></span> <span data-ttu-id="34c7d-192">Assemblies incompatíveis remanescentes podem resultar em uma exceção `System.BadImageFormatException` durante a implantação de um aplicativo atualizado.</span><span class="sxs-lookup"><span data-stu-id="34c7d-192">Lingering incompatible assemblies can result in a `System.BadImageFormatException` exception when deploying an upgraded app.</span></span>

## <a name="uri-endpoint-wrong-or-stopped-website"></a><span data-ttu-id="34c7d-193">Ponto de extremidade de URI incorreto ou site interrompido</span><span class="sxs-lookup"><span data-stu-id="34c7d-193">URI endpoint wrong or stopped website</span></span>

* <span data-ttu-id="34c7d-194">**Navegador:** ERR_CONNECTION_REFUSED **–OU–** Não é possível estabelecer conexão</span><span class="sxs-lookup"><span data-stu-id="34c7d-194">**Browser:** ERR_CONNECTION_REFUSED **--OR--** Unable to connect</span></span>

* <span data-ttu-id="34c7d-195">**Log do Aplicativo:** Nenhuma entrada</span><span class="sxs-lookup"><span data-stu-id="34c7d-195">**Application Log:** No entry</span></span>

* <span data-ttu-id="34c7d-196">**Log de stdout do Módulo do ASP.NET Core:** O arquivo de log não é criado.</span><span class="sxs-lookup"><span data-stu-id="34c7d-196">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="34c7d-197">**Log de depuração do módulo do ASP.NET Core:** O arquivo de log não é criado.</span><span class="sxs-lookup"><span data-stu-id="34c7d-197">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="34c7d-198">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="34c7d-198">Troubleshooting:</span></span>

* <span data-ttu-id="34c7d-199">Confirme se o ponto de extremidade do URI correto para o aplicativo está sendo usado.</span><span class="sxs-lookup"><span data-stu-id="34c7d-199">Confirm the correct URI endpoint for the app is in use.</span></span> <span data-ttu-id="34c7d-200">Verifique as associações.</span><span class="sxs-lookup"><span data-stu-id="34c7d-200">Check the bindings.</span></span>

* <span data-ttu-id="34c7d-201">Confirme que o site do IIS não está no estado *Parado*.</span><span class="sxs-lookup"><span data-stu-id="34c7d-201">Confirm that the IIS website isn't in the *Stopped* state.</span></span>

## <a name="corewebengine-or-w3svc-server-features-disabled"></a><span data-ttu-id="34c7d-202">Recursos do servidor CoreWebEngine ou W3SVC desabilitados</span><span class="sxs-lookup"><span data-stu-id="34c7d-202">CoreWebEngine or W3SVC server features disabled</span></span>

<span data-ttu-id="34c7d-203">**Exceção do Sistema Operacional:** Os recursos CoreWebEngine e W3SVC do IIS 7.0 devem ser instalados para usar o Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="34c7d-203">**OS Exception:** The IIS 7.0 CoreWebEngine and W3SVC features must be installed to use the ASP.NET Core Module.</span></span>

<span data-ttu-id="34c7d-204">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="34c7d-204">Troubleshooting:</span></span>

<span data-ttu-id="34c7d-205">Confirme que a função e os recursos apropriados estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="34c7d-205">Confirm that the proper role and features are enabled.</span></span> <span data-ttu-id="34c7d-206">Consulte [Configuração do IIS](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="34c7d-206">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

## <a name="incorrect-website-physical-path-or-app-missing"></a><span data-ttu-id="34c7d-207">Caminho físico do site incorreto ou aplicativo ausente</span><span class="sxs-lookup"><span data-stu-id="34c7d-207">Incorrect website physical path or app missing</span></span>

* <span data-ttu-id="34c7d-208">**Navegador:** 403 Proibido – acesso negado **–OU–** 403.14 Proibido – o servidor Web está configurado para não listar o conteúdo deste diretório.</span><span class="sxs-lookup"><span data-stu-id="34c7d-208">**Browser:** 403 Forbidden - Access is denied **--OR--** 403.14 Forbidden - The Web server is configured to not list the contents of this directory.</span></span>

* <span data-ttu-id="34c7d-209">**Log do Aplicativo:** Nenhuma entrada</span><span class="sxs-lookup"><span data-stu-id="34c7d-209">**Application Log:** No entry</span></span>

* <span data-ttu-id="34c7d-210">**Log de stdout do Módulo do ASP.NET Core:** O arquivo de log não é criado.</span><span class="sxs-lookup"><span data-stu-id="34c7d-210">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="34c7d-211">**Log de depuração do módulo do ASP.NET Core:** O arquivo de log não é criado.</span><span class="sxs-lookup"><span data-stu-id="34c7d-211">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="34c7d-212">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="34c7d-212">Troubleshooting:</span></span>

<span data-ttu-id="34c7d-213">Confira as **Configurações Básicas** no site do IIS e a pasta do aplicativo físico.</span><span class="sxs-lookup"><span data-stu-id="34c7d-213">Check the IIS website **Basic Settings** and the physical app folder.</span></span> <span data-ttu-id="34c7d-214">Confirme que o aplicativo está na pasta no **Caminho físico** do site do IIS.</span><span class="sxs-lookup"><span data-stu-id="34c7d-214">Confirm that the app is in the folder at the IIS website **Physical path**.</span></span>

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a><span data-ttu-id="34c7d-215">Função incorreta, Módulo do ASP.NET Core Não Instalado ou permissões incorretas</span><span class="sxs-lookup"><span data-stu-id="34c7d-215">Incorrect role, ASP.NET Core Module not installed, or incorrect permissions</span></span>

* <span data-ttu-id="34c7d-216">**Navegador:** 500.19 Erro interno do servidor – a página solicitada não pode ser acessada porque os dados de configuração relacionados da página são inválidos.</span><span class="sxs-lookup"><span data-stu-id="34c7d-216">**Browser:** 500.19 Internal Server Error - The requested page cannot be accessed because the related configuration data for the page is invalid.</span></span> <span data-ttu-id="34c7d-217">**–OU–** Esta página não pode ser exibida</span><span class="sxs-lookup"><span data-stu-id="34c7d-217">**--OR--** This page can't be displayed</span></span>

* <span data-ttu-id="34c7d-218">**Log do Aplicativo:** Nenhuma entrada</span><span class="sxs-lookup"><span data-stu-id="34c7d-218">**Application Log:** No entry</span></span>

* <span data-ttu-id="34c7d-219">**Log de stdout do Módulo do ASP.NET Core:** O arquivo de log não é criado.</span><span class="sxs-lookup"><span data-stu-id="34c7d-219">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="34c7d-220">**Log de depuração do módulo do ASP.NET Core:** O arquivo de log não é criado.</span><span class="sxs-lookup"><span data-stu-id="34c7d-220">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="34c7d-221">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="34c7d-221">Troubleshooting:</span></span>

* <span data-ttu-id="34c7d-222">Confirme que você habilitou a função apropriada.</span><span class="sxs-lookup"><span data-stu-id="34c7d-222">Confirm that the proper role is enabled.</span></span> <span data-ttu-id="34c7d-223">Consulte [Configuração do IIS](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="34c7d-223">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

* <span data-ttu-id="34c7d-224">Abra **Programas e Recursos** ou **Aplicativos e Recursos** e confirme se a **Hospedagem do Windows Server** está instalada.</span><span class="sxs-lookup"><span data-stu-id="34c7d-224">Open **Programs & Features** or **Apps & features** and confirm that **Windows Server Hosting** is installed.</span></span> <span data-ttu-id="34c7d-225">Se a **Hospedagem do Windows Server** não estiver presente na lista de programas instalados, baixe e instale o Pacote de Hospedagem do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="34c7d-225">If **Windows Server Hosting** isn't present in the list of installed programs, download and install the .NET Core Hosting Bundle.</span></span>

  [<span data-ttu-id="34c7d-226">Instalador de pacote de hospedagem do .NET Core atual (download direto)</span><span class="sxs-lookup"><span data-stu-id="34c7d-226">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="34c7d-227">Para obter mais informações, confira [Instalar o pacote de hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="34c7d-227">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

* <span data-ttu-id="34c7d-228">Verifique se o **Pool de aplicativos** > **Modelo de processo** > **Identidade** está definido como **ApplicationPoolIdentity** ou se a identidade personalizada tem as permissões corretas para acessar a pasta de implantação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="34c7d-228">Make sure that the **Application Pool** > **Process Model** > **Identity** is set to **ApplicationPoolIdentity** or the custom identity has the correct permissions to access the app's deployment folder.</span></span>

* <span data-ttu-id="34c7d-229">Se você desinstalou o Pacote de Hospedagem do ASP.NET Core e instalou uma versão anterior do pacote de hospedagem, o arquivo *applicationHost.config* não inclui uma seção para o Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="34c7d-229">If you uninstalled the ASP.NET Core Hosting Bundle and installed an earlier version of the hosting bundle, the *applicationHost.config* file doesn't include a section for the ASP.NET Core Module.</span></span> <span data-ttu-id="34c7d-230">Abra *applicationHost.config* em *%windir%/System32/inetsrv/config* e encontre o grupo de seção `<configuration><configSections><sectionGroup name="system.webServer">`.</span><span class="sxs-lookup"><span data-stu-id="34c7d-230">Open *applicationHost.config* at *%windir%/System32/inetsrv/config* and find the `<configuration><configSections><sectionGroup name="system.webServer">` section group.</span></span> <span data-ttu-id="34c7d-231">Se estiver faltando a seção do Módulo do ASP.NET Core no grupo de seções, adicione o elemento da seção:</span><span class="sxs-lookup"><span data-stu-id="34c7d-231">If the section for the ASP.NET Core Module is missing from the section group, add the section element:</span></span>

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```
  
  <span data-ttu-id="34c7d-232">Como alternativa, instale a versão mais recente do Pacote de Hospedagem do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="34c7d-232">Alternatively, install the lastest version of the ASP.NET Core Hosting Bundle.</span></span> <span data-ttu-id="34c7d-233">A versão mais recente é compatível com versões anteriores dos aplicativos do ASP.NET Core com suporte.</span><span class="sxs-lookup"><span data-stu-id="34c7d-233">The latest version is backwards-compatible with supported ASP.NET Core apps.</span></span>

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a><span data-ttu-id="34c7d-234">processPath incorreto, variável de PATH ausente, pacote de hospedagem não instalado, sistema/IIS não reiniciado, Pacotes Redistribuíveis do VC++ não instalados ou violação de acesso de dotnet.exe</span><span class="sxs-lookup"><span data-stu-id="34c7d-234">Incorrect processPath, missing PATH variable, Hosting Bundle not installed, system/IIS not restarted, VC++ Redistributable not installed, or dotnet.exe access violation</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="34c7d-235">**Navegador:** Erro HTTP 500.0 – Falha de carregamento de manipulador em processo ANCM</span><span class="sxs-lookup"><span data-stu-id="34c7d-235">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="34c7d-236">**Log do Aplicativo:** Aplicativo 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' com raiz física 'C:\{PATH}\' falhou ao iniciar o processo com a linha de comando '"{...}"</span><span class="sxs-lookup"><span data-stu-id="34c7d-236">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="34c7d-237">', ErrorCode = '0x80070002 : 0.</span><span class="sxs-lookup"><span data-stu-id="34c7d-237">', ErrorCode = '0x80070002 : 0.</span></span> <span data-ttu-id="34c7d-238">Não foi possível iniciar o aplicativo '{PATH}'.</span><span class="sxs-lookup"><span data-stu-id="34c7d-238">Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="34c7d-239">O executável não foi encontrado em '{PATH}'.</span><span class="sxs-lookup"><span data-stu-id="34c7d-239">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="34c7d-240">Falha ao iniciar o aplicativo '/LM/W3SVC/2/ROOT', ErrorCode '0x8007023e'.</span><span class="sxs-lookup"><span data-stu-id="34c7d-240">Failed to start application '/LM/W3SVC/2/ROOT', ErrorCode '0x8007023e'.</span></span>

* <span data-ttu-id="34c7d-241">**Log de stdout do Módulo do ASP.NET Core:** O arquivo de log não é criado.</span><span class="sxs-lookup"><span data-stu-id="34c7d-241">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="34c7d-242">**Log de depuração do módulo do ASP.NET Core:** Log de eventos: Não foi possível iniciar o aplicativo '{PATH}'.</span><span class="sxs-lookup"><span data-stu-id="34c7d-242">**ASP.NET Core Module Debug Log:** Event Log: 'Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="34c7d-243">O executável não foi encontrado em '{PATH}'.</span><span class="sxs-lookup"><span data-stu-id="34c7d-243">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="34c7d-244">HRESULT com falha retornou: 0x8007023e</span><span class="sxs-lookup"><span data-stu-id="34c7d-244">Failed HRESULT returned: 0x8007023e</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="34c7d-245">**Navegador:** Erro HTTP 502.5 – falha do processo</span><span class="sxs-lookup"><span data-stu-id="34c7d-245">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="34c7d-246">**Log do Aplicativo:** Aplicativo 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' com raiz física 'C:\{PATH}\' falhou ao iniciar o processo com a linha de comando '"{...}"</span><span class="sxs-lookup"><span data-stu-id="34c7d-246">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="34c7d-247">', ErrorCode = '0x80070002 : 0.</span><span class="sxs-lookup"><span data-stu-id="34c7d-247">', ErrorCode = '0x80070002 : 0.</span></span>

* <span data-ttu-id="34c7d-248">**Log de stdout do Módulo do ASP.NET Core:** O arquivo de log é criado, mas vazio.</span><span class="sxs-lookup"><span data-stu-id="34c7d-248">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker-end

<span data-ttu-id="34c7d-249">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="34c7d-249">Troubleshooting:</span></span>

* <span data-ttu-id="34c7d-250">Confirme se o aplicativo é executado localmente no Kestrel.</span><span class="sxs-lookup"><span data-stu-id="34c7d-250">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="34c7d-251">Uma falha do processo pode ser o resultado de um problema no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="34c7d-251">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="34c7d-252">Para obter mais informações, confira [Solução de problemas (IIS)](xref:host-and-deploy/iis/troubleshoot) ou [Solução de problemas (Serviço de Aplicativo do Azure)](xref:host-and-deploy/azure-apps/troubleshoot).</span><span class="sxs-lookup"><span data-stu-id="34c7d-252">For more information, see [Troubleshoot (IIS)](xref:host-and-deploy/iis/troubleshoot) or [Troubleshoot (Azure App Service)](xref:host-and-deploy/azure-apps/troubleshoot).</span></span>

* <span data-ttu-id="34c7d-253">Verifique o atributo *processPath* no elemento `<aspNetCore>` em *web.config* para confirmar se ele é `dotnet` para uma FDD (implantação dependente de estrutura) ou `.\{ASSEMBLY}.exe` para uma [SCD (implantação autossuficiente)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="34c7d-253">Check the *processPath* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's `dotnet` for a framework-dependent deployment (FDD) or `.\{ASSEMBLY}.exe` for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

* <span data-ttu-id="34c7d-254">Para uma FDD, o *dotnet.exe* pode não estar acessível por meio das configurações de PATH.</span><span class="sxs-lookup"><span data-stu-id="34c7d-254">For an FDD, *dotnet.exe* might not be accessible via the PATH settings.</span></span> <span data-ttu-id="34c7d-255">Confirme se *C:\Arquivos de Programas\dotnet\\* existe nas configurações de PATH do Sistema.</span><span class="sxs-lookup"><span data-stu-id="34c7d-255">Confirm that *C:\Program Files\dotnet\\* exists in the System PATH settings.</span></span>

* <span data-ttu-id="34c7d-256">Para uma FDD, o *dotnet.exe* pode não estar acessível para a identidade do usuário do pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="34c7d-256">For an FDD, *dotnet.exe* might not be accessible for the user identity of the app pool.</span></span> <span data-ttu-id="34c7d-257">Confirme se a identidade do usuário do pool de aplicativos tem acesso ao diretório *C:\Arquivos de Programas\dotnet*.</span><span class="sxs-lookup"><span data-stu-id="34c7d-257">Confirm that the app pool user identity has access to the *C:\Program Files\dotnet* directory.</span></span> <span data-ttu-id="34c7d-258">Confirme se não há nenhuma regra de negação configurada para a identidade do usuário do pool de aplicativos no *C:\Arquivos de Programas\dotnet* e nos diretórios do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="34c7d-258">Confirm that there are no deny rules configured for the app pool user identity on the *C:\Program Files\dotnet* and app directories.</span></span>

* <span data-ttu-id="34c7d-259">Talvez você tenha implantado uma FDD e instalado o .NET Core sem reiniciar o IIS.</span><span class="sxs-lookup"><span data-stu-id="34c7d-259">An FDD may have been deployed and .NET Core installed without restarting IIS.</span></span> <span data-ttu-id="34c7d-260">Reinicie o servidor ou o IIS executando **net stop was /y** seguido por **net start w3svc** em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="34c7d-260">Either restart the server or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="34c7d-261">Você pode ter implantado uma FDD sem instalar o tempo de execução do .NET Core no sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="34c7d-261">An FDD may have been deployed without installing the .NET Core runtime on the hosting system.</span></span> <span data-ttu-id="34c7d-262">Se o tempo de execução do .NET Core ainda não foi instalado, execute o **Instalador do Pacote de Hospedagem do .NET Core** no sistema.</span><span class="sxs-lookup"><span data-stu-id="34c7d-262">If the .NET Core runtime hasn't been installed, run the **.NET Core Hosting Bundle installer** on the system.</span></span>

  [<span data-ttu-id="34c7d-263">Instalador de pacote de hospedagem do .NET Core atual (download direto)</span><span class="sxs-lookup"><span data-stu-id="34c7d-263">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="34c7d-264">Para obter mais informações, confira [Instalar o pacote de hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="34c7d-264">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

  <span data-ttu-id="34c7d-265">Se um tempo de execução específico for necessário, baixe o tempo de execução dos [Arquivos de Download do .NET](https://dotnet.microsoft.com/download/archives) e instale-o no sistema.</span><span class="sxs-lookup"><span data-stu-id="34c7d-265">If a specific runtime is required, download the runtime from the [.NET Download Archives](https://dotnet.microsoft.com/download/archives) and install it on the system.</span></span> <span data-ttu-id="34c7d-266">Conclua a instalação reiniciando o sistema ou o IIS executando **net stop was /y** seguido por **net start w3svc** em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="34c7d-266">Complete the installation by restarting the system or restarting IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="34c7d-267">Talvez você tenha implantado uma FDD e os *Pacotes redistribuíveis do Microsoft Visual C++ 2015 (x64)* não estejam instalados no sistema.</span><span class="sxs-lookup"><span data-stu-id="34c7d-267">An FDD may have been deployed and the *Microsoft Visual C++ 2015 Redistributable (x64)* isn't installed on the system.</span></span> <span data-ttu-id="34c7d-268">Obtenha um instalador do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=53840).</span><span class="sxs-lookup"><span data-stu-id="34c7d-268">Obtain an installer from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840).</span></span>

## <a name="incorrect-arguments-of-aspnetcore-element"></a><span data-ttu-id="34c7d-269">Argumentos incorretos do elemento \<aspNetCore></span><span class="sxs-lookup"><span data-stu-id="34c7d-269">Incorrect arguments of \<aspNetCore> element</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="34c7d-270">**Navegador:** Erro HTTP 500.0 – Falha de carregamento de manipulador em processo ANCM</span><span class="sxs-lookup"><span data-stu-id="34c7d-270">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="34c7d-271">**Log do Aplicativo:** A invocação do hostfxr para encontrar o manipulador de solicitação inprocess falha sem encontrar nenhuma dependência nativa.</span><span class="sxs-lookup"><span data-stu-id="34c7d-271">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="34c7d-272">Isso provavelmente significa que o aplicativo está configurado incorretamente, verifique as versões do Microsoft.NetCore.App e Microsoft.AspNetCore.App que são afetadas pelo aplicativo e estão instaladas no computador.</span><span class="sxs-lookup"><span data-stu-id="34c7d-272">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="34c7d-273">Não foi possível localizar o manipulador de solicitação inprocess.</span><span class="sxs-lookup"><span data-stu-id="34c7d-273">Could not find inprocess request handler.</span></span> <span data-ttu-id="34c7d-274">Saída capturada da invocação do hostfxr: Você quis dizer executar comandos do SDK do dotnet?</span><span class="sxs-lookup"><span data-stu-id="34c7d-274">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="34c7d-275">Instale o SDK do dotnet de: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Falha ao iniciar o aplicativo '/LM/W3SVC/3/ROOT', ErrorCode '0x8000ffff'.</span><span class="sxs-lookup"><span data-stu-id="34c7d-275">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed to start application '/LM/W3SVC/3/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="34c7d-276">**Log de stdout do Módulo do ASP.NET Core:** Você quis dizer executar comandos do SDK do dotnet?</span><span class="sxs-lookup"><span data-stu-id="34c7d-276">**ASP.NET Core Module stdout Log:** Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="34c7d-277">Instale o SDK do dotnet de: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409</span><span class="sxs-lookup"><span data-stu-id="34c7d-277">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409</span></span>

* <span data-ttu-id="34c7d-278">**Log de depuração do módulo do ASP.NET Core:** A invocação do hostfxr para encontrar o manipulador de solicitação inprocess falha sem encontrar nenhuma dependência nativa.</span><span class="sxs-lookup"><span data-stu-id="34c7d-278">**ASP.NET Core Module Debug Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="34c7d-279">Isso provavelmente significa que o aplicativo está configurado incorretamente, verifique as versões do Microsoft.NetCore.App e Microsoft.AspNetCore.App que são afetadas pelo aplicativo e estão instaladas no computador.</span><span class="sxs-lookup"><span data-stu-id="34c7d-279">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="34c7d-280">HRESULT com falha retornou: 0x8000ffff Não foi possível localizar o manipulador de solicitação inprocess.</span><span class="sxs-lookup"><span data-stu-id="34c7d-280">Failed HRESULT returned: 0x8000ffff Could not find inprocess request handler.</span></span> <span data-ttu-id="34c7d-281">Saída capturada da invocação do hostfxr: Você quis dizer executar comandos do SDK do dotnet?</span><span class="sxs-lookup"><span data-stu-id="34c7d-281">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="34c7d-282">Instale o SDK do dotnet de: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 HRESULT com falha retornou: 0x8000ffff</span><span class="sxs-lookup"><span data-stu-id="34c7d-282">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed HRESULT returned: 0x8000ffff</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="34c7d-283">**Navegador:** Erro HTTP 502.5 – falha do processo</span><span class="sxs-lookup"><span data-stu-id="34c7d-283">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="34c7d-284">**Log do Aplicativo:** Aplicativo 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' com a raiz física 'C:\{PATH}\' falha ao iniciar o processo com a linha de comando '"dotnet" .\{ASSEMBLY}.dll', ErrorCode = '0x80004005: 80008081.</span><span class="sxs-lookup"><span data-stu-id="34c7d-284">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"dotnet" .\{ASSEMBLY}.dll', ErrorCode = '0x80004005 : 80008081.</span></span>

* <span data-ttu-id="34c7d-285">**Log de stdout do Módulo do ASP.NET Core:** O aplicativo a ser executado não existe: 'PATH\{ASSEMBLY}.dll'</span><span class="sxs-lookup"><span data-stu-id="34c7d-285">**ASP.NET Core Module stdout Log:** The application to execute does not exist: 'PATH\{ASSEMBLY}.dll'</span></span>

::: moniker-end

<span data-ttu-id="34c7d-286">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="34c7d-286">Troubleshooting:</span></span>

* <span data-ttu-id="34c7d-287">Confirme se o aplicativo é executado localmente no Kestrel.</span><span class="sxs-lookup"><span data-stu-id="34c7d-287">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="34c7d-288">Uma falha do processo pode ser o resultado de um problema no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="34c7d-288">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="34c7d-289">Para obter mais informações, confira [Solução de problemas (IIS)](xref:host-and-deploy/iis/troubleshoot) ou [Solução de problemas (Serviço de Aplicativo do Azure)](xref:host-and-deploy/azure-apps/troubleshoot).</span><span class="sxs-lookup"><span data-stu-id="34c7d-289">For more information, see [Troubleshoot (IIS)](xref:host-and-deploy/iis/troubleshoot) or [Troubleshoot (Azure App Service)](xref:host-and-deploy/azure-apps/troubleshoot).</span></span>

* <span data-ttu-id="34c7d-290">Examine o atributo *arguments* no elemento `<aspNetCore>` no *web.config* para confirmar se ele: (a) é `.\{ASSEMBLY}.dll` de uma FDD (implantação dependente de estrutura); ou (b) não está presente, é uma cadeia de caracteres vazia (`arguments=""`) ou uma lista de argumentos do aplicativo (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) para uma SCD (implantação autossuficiente).</span><span class="sxs-lookup"><span data-stu-id="34c7d-290">Examine the *arguments* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's either (a) `.\{ASSEMBLY}.dll` for a framework-dependent deployment (FDD); or (b) not present, an empty string (`arguments=""`), or a list of the app's arguments (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) for a self-contained deployment (SCD).</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="missing-net-core-shared-framework"></a><span data-ttu-id="34c7d-291">Estrutura compartilhada do .NET Core ausente</span><span class="sxs-lookup"><span data-stu-id="34c7d-291">Missing .NET Core shared framework</span></span>

* <span data-ttu-id="34c7d-292">**Navegador:** Erro HTTP 500.0 – Falha de carregamento de manipulador em processo ANCM</span><span class="sxs-lookup"><span data-stu-id="34c7d-292">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="34c7d-293">**Log do Aplicativo:** A invocação do hostfxr para encontrar o manipulador de solicitação inprocess falha sem encontrar nenhuma dependência nativa.</span><span class="sxs-lookup"><span data-stu-id="34c7d-293">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="34c7d-294">Isso provavelmente significa que o aplicativo está configurado incorretamente, verifique as versões do Microsoft.NetCore.App e Microsoft.AspNetCore.App que são afetadas pelo aplicativo e estão instaladas no computador.</span><span class="sxs-lookup"><span data-stu-id="34c7d-294">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="34c7d-295">Não foi possível localizar o manipulador de solicitação inprocess.</span><span class="sxs-lookup"><span data-stu-id="34c7d-295">Could not find inprocess request handler.</span></span> <span data-ttu-id="34c7d-296">Saída capturada da invocação do hostfxr: Não foi possível encontrar nenhuma versão de estrutura compatível.</span><span class="sxs-lookup"><span data-stu-id="34c7d-296">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="34c7d-297">A estrutura especificada 'Microsoft.AspNetCore.App', versão '{VERSION}', não foi encontrada.</span><span class="sxs-lookup"><span data-stu-id="34c7d-297">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

<span data-ttu-id="34c7d-298">Falha ao iniciar o aplicativo '/LM/W3SVC/5/ROOT', ErrorCode '0x8000ffff'.</span><span class="sxs-lookup"><span data-stu-id="34c7d-298">Failed to start application '/LM/W3SVC/5/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="34c7d-299">**Log de stdout do Módulo do ASP.NET Core:** Não foi possível encontrar nenhuma versão de estrutura compatível.</span><span class="sxs-lookup"><span data-stu-id="34c7d-299">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="34c7d-300">A estrutura especificada 'Microsoft.AspNetCore.App', versão '{VERSION}', não foi encontrada.</span><span class="sxs-lookup"><span data-stu-id="34c7d-300">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

* <span data-ttu-id="34c7d-301">**Log de depuração do módulo do ASP.NET Core:** HRESULT com falha retornou: 0x8000ffff</span><span class="sxs-lookup"><span data-stu-id="34c7d-301">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8000ffff</span></span>

::: moniker-end

<span data-ttu-id="34c7d-302">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="34c7d-302">Troubleshooting:</span></span>

<span data-ttu-id="34c7d-303">Para uma FDD (implantação dependente de estrutura), confirme se você tem o tempo de execução correto instalado no sistema.</span><span class="sxs-lookup"><span data-stu-id="34c7d-303">For a framework-dependent deployment (FDD), confirm that the correct runtime installed on the system.</span></span>

## <a name="stopped-application-pool"></a><span data-ttu-id="34c7d-304">Pool de aplicativos interrompido</span><span class="sxs-lookup"><span data-stu-id="34c7d-304">Stopped Application Pool</span></span>

* <span data-ttu-id="34c7d-305">**Navegador:** 503 Serviço Não Disponível</span><span class="sxs-lookup"><span data-stu-id="34c7d-305">**Browser:** 503 Service Unavailable</span></span>

* <span data-ttu-id="34c7d-306">**Log do Aplicativo:** Nenhuma entrada</span><span class="sxs-lookup"><span data-stu-id="34c7d-306">**Application Log:** No entry</span></span>

* <span data-ttu-id="34c7d-307">**Log de stdout do Módulo do ASP.NET Core:** O arquivo de log não é criado.</span><span class="sxs-lookup"><span data-stu-id="34c7d-307">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="34c7d-308">**Log de depuração do módulo do ASP.NET Core:** O arquivo de log não é criado.</span><span class="sxs-lookup"><span data-stu-id="34c7d-308">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="34c7d-309">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="34c7d-309">Troubleshooting:</span></span>

<span data-ttu-id="34c7d-310">Confirme que o Pool de Aplicativos não está no estado *Parado*.</span><span class="sxs-lookup"><span data-stu-id="34c7d-310">Confirm that the Application Pool isn't in the *Stopped* state.</span></span>

## <a name="sub-application-includes-a-handlers-section"></a><span data-ttu-id="34c7d-311">O subaplicativo inclui uma seção \<manipuladores></span><span class="sxs-lookup"><span data-stu-id="34c7d-311">Sub-application includes a \<handlers> section</span></span>

* <span data-ttu-id="34c7d-312">**Navegador:** Erro HTTP 500.19 – Erro Interno do Servidor</span><span class="sxs-lookup"><span data-stu-id="34c7d-312">**Browser:** HTTP Error 500.19 - Internal Server Error</span></span>

* <span data-ttu-id="34c7d-313">**Log do Aplicativo:** Nenhuma entrada</span><span class="sxs-lookup"><span data-stu-id="34c7d-313">**Application Log:** No entry</span></span>

* <span data-ttu-id="34c7d-314">**Log de stdout do Módulo do ASP.NET Core:** O arquivo de log do aplicativo raiz é criado e mostra uma operação normal.</span><span class="sxs-lookup"><span data-stu-id="34c7d-314">**ASP.NET Core Module stdout Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="34c7d-315">O arquivo de log do subaplicativo não é criado.</span><span class="sxs-lookup"><span data-stu-id="34c7d-315">The sub-app's log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="34c7d-316">**Log de depuração do módulo do ASP.NET Core:** O arquivo de log do aplicativo raiz é criado e mostra uma operação normal.</span><span class="sxs-lookup"><span data-stu-id="34c7d-316">**ASP.NET Core Module Debug Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="34c7d-317">O arquivo de log do subaplicativo não é criado.</span><span class="sxs-lookup"><span data-stu-id="34c7d-317">The sub-app's log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="34c7d-318">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="34c7d-318">Troubleshooting:</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="34c7d-319">Confirme se o arquivo *web.config* do subaplicativo não inclui uma seção `<handlers>` ou que o subaplicativo não herda os manipuladores do aplicativo pai.</span><span class="sxs-lookup"><span data-stu-id="34c7d-319">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section or that the sub-app doesn't inherit the parent app's handlers.</span></span>

<span data-ttu-id="34c7d-320">A seção `<system.webServer>` do aplicativo pai de *web.config* é colocada dentro de um elemento `<location>`.</span><span class="sxs-lookup"><span data-stu-id="34c7d-320">The parent app's `<system.webServer>` section of *web.config* is placed inside of a `<location>` element.</span></span> <span data-ttu-id="34c7d-321">A propriedade <xref:System.Configuration.SectionInformation.InheritInChildApplications*> é definida como `false` para indicar que as configurações especificadas no elemento [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) não são herdadas por aplicativos que residem em um subdiretório do aplicativo pai.</span><span class="sxs-lookup"><span data-stu-id="34c7d-321">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the parent app.</span></span> <span data-ttu-id="34c7d-322">Para obter mais informações, consulte <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="34c7d-322">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="34c7d-323">Confirme se o arquivo *web.config* do subaplicativo não inclui uma seção `<handlers>`.</span><span class="sxs-lookup"><span data-stu-id="34c7d-323">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section.</span></span>

::: moniker-end

## <a name="stdout-log-path-incorrect"></a><span data-ttu-id="34c7d-324">caminho do log de stdout incorreto</span><span class="sxs-lookup"><span data-stu-id="34c7d-324">stdout log path incorrect</span></span>

* <span data-ttu-id="34c7d-325">**Navegador:** O aplicativo responde normalmente.</span><span class="sxs-lookup"><span data-stu-id="34c7d-325">**Browser:** The app responds normally.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="34c7d-326">**Log do Aplicativo:** Não foi possível iniciar o redirecionamento de stdout em C:\Arquivos de Programas\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span><span class="sxs-lookup"><span data-stu-id="34c7d-326">**Application Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="34c7d-327">Mensagem de exceção: HRESULT 0x80070005 retornado em {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span><span class="sxs-lookup"><span data-stu-id="34c7d-327">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="34c7d-328">Não foi possível parar o redirecionamento de stdout em C:\Arquivos de Programas\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span><span class="sxs-lookup"><span data-stu-id="34c7d-328">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="34c7d-329">Mensagem de exceção: HRESULT 0x80070002 retornado em {PATH}.</span><span class="sxs-lookup"><span data-stu-id="34c7d-329">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="34c7d-330">Não foi possível iniciar o redirecionamento de stdout em {PATH}\aspnetcorev2_inprocess.dll.</span><span class="sxs-lookup"><span data-stu-id="34c7d-330">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

* <span data-ttu-id="34c7d-331">**Log de stdout do Módulo do ASP.NET Core:** O arquivo de log não é criado.</span><span class="sxs-lookup"><span data-stu-id="34c7d-331">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="34c7d-332">**Log de depuração do módulo do ASP.NET Core:** Não foi possível iniciar o redirecionamento de stdout em C:\Arquivos de Programas\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span><span class="sxs-lookup"><span data-stu-id="34c7d-332">**ASP.NET Core Module debug Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="34c7d-333">Mensagem de exceção: HRESULT 0x80070005 retornado em {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span><span class="sxs-lookup"><span data-stu-id="34c7d-333">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="34c7d-334">Não foi possível parar o redirecionamento de stdout em C:\Arquivos de Programas\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span><span class="sxs-lookup"><span data-stu-id="34c7d-334">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="34c7d-335">Mensagem de exceção: HRESULT 0x80070002 retornado em {PATH}.</span><span class="sxs-lookup"><span data-stu-id="34c7d-335">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="34c7d-336">Não foi possível iniciar o redirecionamento de stdout em {PATH}\aspnetcorev2_inprocess.dll.</span><span class="sxs-lookup"><span data-stu-id="34c7d-336">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="34c7d-337">**Log do Aplicativo:** Aviso: Não foi possível criar stdoutLogFile \\?\{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log, ErrorCode = -2147024893.</span><span class="sxs-lookup"><span data-stu-id="34c7d-337">**Application Log:** Warning: Could not create stdoutLogFile \\?\{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log, ErrorCode = -2147024893.</span></span>

* <span data-ttu-id="34c7d-338">**Log de stdout do Módulo do ASP.NET Core:** O arquivo de log não é criado.</span><span class="sxs-lookup"><span data-stu-id="34c7d-338">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="34c7d-339">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="34c7d-339">Troubleshooting:</span></span>

* <span data-ttu-id="34c7d-340">O caminho `stdoutLogFile` especificado no elemento `<aspNetCore>` de *web.config* não existe.</span><span class="sxs-lookup"><span data-stu-id="34c7d-340">The `stdoutLogFile` path specified in the `<aspNetCore>` element of *web.config* doesn't exist.</span></span> <span data-ttu-id="34c7d-341">Para obter mais informações, confira [Módulo ASP.NET Core: criação de log e redirecionamento](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span><span class="sxs-lookup"><span data-stu-id="34c7d-341">For more information, see [ASP.NET Core Module: Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span></span>

* <span data-ttu-id="34c7d-342">O usuário do pool de aplicativos não tem acesso de gravação para o caminho do log de stdout.</span><span class="sxs-lookup"><span data-stu-id="34c7d-342">The app pool user doesn't have write access to the stdout log path.</span></span>

## <a name="application-configuration-general-issue"></a><span data-ttu-id="34c7d-343">Problema geral de configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="34c7d-343">Application configuration general issue</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="34c7d-344">**Navegador:** Erro HTTP 500.0 – Falha de carregamento de manipulador em processo ANCM **–OU–** Erro HTTP 500.30 – Falha de início no processo do ANCM</span><span class="sxs-lookup"><span data-stu-id="34c7d-344">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure **--OR--** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="34c7d-345">**Log do Aplicativo:** Variável</span><span class="sxs-lookup"><span data-stu-id="34c7d-345">**Application Log:** Variable</span></span>

* <span data-ttu-id="34c7d-346">**Log de stdout do Módulo do ASP.NET Core:** O arquivo de log é criado, mas vazio, ou criado com entradas normais até o ponto da falha do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="34c7d-346">**ASP.NET Core Module stdout Log:** The log file is created but empty or created with normal entries until the point of the app failing.</span></span>

* <span data-ttu-id="34c7d-347">**Log de depuração do módulo do ASP.NET Core:** Variável</span><span class="sxs-lookup"><span data-stu-id="34c7d-347">**ASP.NET Core Module Debug Log:** Variable</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="34c7d-348">**Navegador:** Erro HTTP 502.5 – falha do processo</span><span class="sxs-lookup"><span data-stu-id="34c7d-348">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="34c7d-349">**Log do Aplicativo:** Aplicativo 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' com raiz física 'C:\{PATH}\' criada no processo com a linha de comando '"C:\{PATH}\{ASSEMBLY}.{exe|dll}" ', mas falhou, não respondeu ou não escutou na porta '{PORT}' fornecida, ErrorCode = '{ERROR CODE}'</span><span class="sxs-lookup"><span data-stu-id="34c7d-349">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' created process with commandline '"C:\{PATH}\{ASSEMBLY}.{exe|dll}" ' but either crashed or did not respond or did not listen on the given port '{PORT}', ErrorCode = '{ERROR CODE}'</span></span>

* <span data-ttu-id="34c7d-350">**Log de stdout do Módulo do ASP.NET Core:** O arquivo de log é criado, mas vazio.</span><span class="sxs-lookup"><span data-stu-id="34c7d-350">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker-end

<span data-ttu-id="34c7d-351">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="34c7d-351">Troubleshooting:</span></span>

<span data-ttu-id="34c7d-352">O processo não pôde ser iniciado, provavelmente, devido a um problema de programação ou configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="34c7d-352">The process failed to start, most likely due to an app configuration or programming issue.</span></span>

<span data-ttu-id="34c7d-353">Para mais informações, consulte os seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="34c7d-353">For more information, see the following topics:</span></span>

* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-apps/troubleshoot>
* <xref:test/troubleshoot>
