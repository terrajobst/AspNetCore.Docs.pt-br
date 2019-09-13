---
title: Referência de erros comuns para o Serviço de Aplicativo do Azure e o IIS com o ASP.NET Core
author: guardrex
description: Obtenha conselhos de solução de problemas para erros comuns ao hospedar aplicativos ASP.NET Core no Serviço de Aplicativos do Azure e no IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/11/2019
uid: host-and-deploy/azure-iis-errors-reference
ms.openlocfilehash: f6afd6491181830f4d79486fa26a64423cd4a0ac
ms.sourcegitcommit: 092061c4f6ef46ed2165fa84de6273d3786fb97e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70963669"
---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a><span data-ttu-id="f64bf-103">Referência de erros comuns para o Serviço de Aplicativo do Azure e o IIS com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f64bf-103">Common errors reference for Azure App Service and IIS with ASP.NET Core</span></span>

<span data-ttu-id="f64bf-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f64bf-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f64bf-105">Este tópico descreve os erros comuns e fornece conselhos de solução de problemas para erros específicos ao hospedar ASP.NET Core aplicativos no serviço de aplicativos do Azure e no IIS.</span><span class="sxs-lookup"><span data-stu-id="f64bf-105">This topic describes common errors and provides troubleshooting advice for specific errors when hosting ASP.NET Core apps on Azure Apps Service and IIS.</span></span>

<span data-ttu-id="f64bf-106">Para obter diretrizes gerais de solução de <xref:test/troubleshoot-azure-iis>problemas, consulte.</span><span class="sxs-lookup"><span data-stu-id="f64bf-106">For general troubleshooting guidance, see <xref:test/troubleshoot-azure-iis>.</span></span>

<span data-ttu-id="f64bf-107">Colete as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="f64bf-107">Collect the following information:</span></span>

* <span data-ttu-id="f64bf-108">Comportamento do navegador (código de status e mensagem de erro)</span><span class="sxs-lookup"><span data-stu-id="f64bf-108">Browser behavior (status code and error message)</span></span>
* <span data-ttu-id="f64bf-109">Entradas do Log de Eventos do Aplicativo</span><span class="sxs-lookup"><span data-stu-id="f64bf-109">Application Event Log entries</span></span>
  * <span data-ttu-id="f64bf-110">Serviço de Aplicativo do Azure &ndash; Confira <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="f64bf-110">Azure App Service &ndash; See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="f64bf-111">IIS</span><span class="sxs-lookup"><span data-stu-id="f64bf-111">IIS</span></span>
    1. <span data-ttu-id="f64bf-112">Selecione **Iniciar** no menu **Windows**, digite *Visualizador de Eventos* e pressione **Enter**.</span><span class="sxs-lookup"><span data-stu-id="f64bf-112">Select **Start** on the **Windows** menu, type *Event Viewer*, and press **Enter**.</span></span>
    1. <span data-ttu-id="f64bf-113">Após o **Visualizador de Eventos** ser aberto, expanda **Logs do Windows** > **Aplicativo** na barra lateral.</span><span class="sxs-lookup"><span data-stu-id="f64bf-113">After the **Event Viewer** opens, expand **Windows Logs** > **Application** in the sidebar.</span></span>
* <span data-ttu-id="f64bf-114">Entradas do log de depuração e stdout do Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f64bf-114">ASP.NET Core Module stdout and debug log entries</span></span>
  * <span data-ttu-id="f64bf-115">Serviço de Aplicativo do Azure &ndash; Confira <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="f64bf-115">Azure App Service &ndash; See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="f64bf-116">IIS &ndash; Siga as instruções nas seções [Criação de log e redirecionamento](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) e [Logs de diagnóstico avançados](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) do tópico Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f64bf-116">IIS &ndash; Follow the instructions in the [Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) and [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) sections of the ASP.NET Core Module topic.</span></span>

<span data-ttu-id="f64bf-117">Compare as informações do erro para os erros comuns a seguir.</span><span class="sxs-lookup"><span data-stu-id="f64bf-117">Compare error information to the following common errors.</span></span> <span data-ttu-id="f64bf-118">Se uma correspondência for encontrada, siga o aviso de solução de problemas.</span><span class="sxs-lookup"><span data-stu-id="f64bf-118">If a match is found, follow the troubleshooting advice.</span></span>

<span data-ttu-id="f64bf-119">A lista de erros neste tópico não é exaustiva.</span><span class="sxs-lookup"><span data-stu-id="f64bf-119">The list of errors in this topic isn't exhaustive.</span></span> <span data-ttu-id="f64bf-120">Se você encontrar um erro não listado aqui, abra um novo problema usando o botão **Comentários sobre o Conteúdo** na parte inferior deste tópico com instruções detalhadas sobre como reproduzir o erro.</span><span class="sxs-lookup"><span data-stu-id="f64bf-120">If you encounter an error not listed here, open a new issue using the **Content feedback** button at the bottom of this topic with detailed instructions on how to reproduce the error.</span></span>

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="installer-unable-to-obtain-vc-redistributable"></a><span data-ttu-id="f64bf-121">O instalador não pode obter os Pacotes Redistribuíveis do VC++</span><span class="sxs-lookup"><span data-stu-id="f64bf-121">Installer unable to obtain VC++ Redistributable</span></span>

* <span data-ttu-id="f64bf-122">**Exceção do instalador:** 0x80072efd **–OU–** 0x80072f76 – erro não especificado</span><span class="sxs-lookup"><span data-stu-id="f64bf-122">**Installer Exception:** 0x80072efd **--OR--** 0x80072f76 - Unspecified error</span></span>

* <span data-ttu-id="f64bf-123">**Exceção do log do instalador&#8224;:** Erro 0x80072efd **–OU–** 0x80072f76: Falha ao executar o pacote EXE</span><span class="sxs-lookup"><span data-stu-id="f64bf-123">**Installer Log Exception&#8224;:** Error 0x80072efd **--OR--** 0x80072f76: Failed to execute EXE package</span></span>

  <span data-ttu-id="f64bf-124">&#8224;O log está localizado em *C:\Users\{USER}\AppData\Local\Temp\dd_DotNetCoreWinSvrHosting__{TIMESTAMP}.log*.</span><span class="sxs-lookup"><span data-stu-id="f64bf-124">&#8224;The log is located at *C:\Users\{USER}\AppData\Local\Temp\dd_DotNetCoreWinSvrHosting__{TIMESTAMP}.log*.</span></span>

<span data-ttu-id="f64bf-125">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="f64bf-125">Troubleshooting:</span></span>

<span data-ttu-id="f64bf-126">Se o sistema não tiver acesso à Internet durante a [instalação do pacote de hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle), essa exceção ocorrerá quando o instalador for impedido de obter os *Pacotes Redistribuíveis do Microsoft Visual C++ 2015*.</span><span class="sxs-lookup"><span data-stu-id="f64bf-126">If the system doesn't have Internet access while [installing the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle), this exception occurs when the installer is prevented from obtaining the *Microsoft Visual C++ 2015 Redistributable*.</span></span> <span data-ttu-id="f64bf-127">Obtenha um instalador do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=53840).</span><span class="sxs-lookup"><span data-stu-id="f64bf-127">Obtain an installer from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840).</span></span> <span data-ttu-id="f64bf-128">Se o instalador falhar, o servidor poderá não receber o tempo de execução do .NET Core necessário para hospedar uma [FDD (implantação dependente de estrutura)](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="f64bf-128">If the installer fails, the server may not receive the .NET Core runtime required to host a [framework-dependent deployment (FDD)](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span> <span data-ttu-id="f64bf-129">Se estiver hospedando uma FDD, confirme se o tempo de execução está instalado em **Programas e Recursos** ou **Aplicativos e recursos**.</span><span class="sxs-lookup"><span data-stu-id="f64bf-129">If hosting an FDD, confirm that the runtime is installed in **Programs & Features** or **Apps & features**.</span></span> <span data-ttu-id="f64bf-130">Se um tempo de execução específico for necessário, baixe o tempo de execução dos [Arquivos de Download do .NET](https://dotnet.microsoft.com/download/archives) e instale-o no sistema.</span><span class="sxs-lookup"><span data-stu-id="f64bf-130">If a specific runtime is required, download the runtime from the [.NET Download Archives](https://dotnet.microsoft.com/download/archives) and install it on the system.</span></span> <span data-ttu-id="f64bf-131">Depois de instalar o tempo de execução, reinicie o sistema ou o IIS executando **net stop was /y** seguido por **net start w3svc** em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="f64bf-131">After installing the runtime, restart the system or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a><span data-ttu-id="f64bf-132">O upgrade do sistema operacional removeu o Módulo do ASP.NET Core de 32 bits</span><span class="sxs-lookup"><span data-stu-id="f64bf-132">OS upgrade removed the 32-bit ASP.NET Core Module</span></span>

<span data-ttu-id="f64bf-133">**Log do Aplicativo:** A DLL do Módulo **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** falhou ao ser carregada.</span><span class="sxs-lookup"><span data-stu-id="f64bf-133">**Application Log:** The Module DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** failed to load.</span></span> <span data-ttu-id="f64bf-134">Os dados são o erro.</span><span class="sxs-lookup"><span data-stu-id="f64bf-134">The data is the error.</span></span>

<span data-ttu-id="f64bf-135">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="f64bf-135">Troubleshooting:</span></span>

<span data-ttu-id="f64bf-136">Arquivos que não são do sistema operacional no diretório **C:\Windows\SysWOW64\inetsrv** não são preservados durante um upgrade do sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="f64bf-136">Non-OS files in the **C:\Windows\SysWOW64\inetsrv** directory aren't preserved during an OS upgrade.</span></span> <span data-ttu-id="f64bf-137">Se o Módulo do ASP.NET Core estiver instalado antes de uma atualização do sistema operacional e, em seguida, qualquer pool de aplicativos for executado no modo de 32 bits após uma atualização do sistema operacional, esse problema será encontrado.</span><span class="sxs-lookup"><span data-stu-id="f64bf-137">If the ASP.NET Core Module is installed prior to an OS upgrade and then any app pool is run in 32-bit mode after an OS upgrade, this issue is encountered.</span></span> <span data-ttu-id="f64bf-138">Após um upgrade do sistema operacional, repare o Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f64bf-138">After an OS upgrade, repair the ASP.NET Core Module.</span></span> <span data-ttu-id="f64bf-139">Veja [Instalar o pacote de Hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="f64bf-139">See [Install the .NET Core Hosting bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span> <span data-ttu-id="f64bf-140">Selecione **Reparar** ao executar o instalador.</span><span class="sxs-lookup"><span data-stu-id="f64bf-140">Select **Repair** when the installer is run.</span></span>

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a><span data-ttu-id="f64bf-141">Extensão de site ausente, extensões de site de 32 bits (x86) e 64 bits (x64) instaladas ou conjunto de bits incorreto do processo</span><span class="sxs-lookup"><span data-stu-id="f64bf-141">Missing site extension, 32-bit (x86) and 64-bit (x64) site extensions installed, or wrong process bitness set</span></span>

<span data-ttu-id="f64bf-142">*Aplica-se aos aplicativos hospedados pelos Serviços de Aplicativo do Azure.*</span><span class="sxs-lookup"><span data-stu-id="f64bf-142">*Applies to apps hosted by Azure App Services.*</span></span>

* <span data-ttu-id="f64bf-143">**Navegador:** Erro HTTP 500.0 – Falha de carregamento de manipulador em processo ANCM</span><span class="sxs-lookup"><span data-stu-id="f64bf-143">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="f64bf-144">**Log do Aplicativo:** A invocação do hostfxr para encontrar o manipulador de solicitação inprocess falha sem encontrar nenhuma dependência nativa.</span><span class="sxs-lookup"><span data-stu-id="f64bf-144">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="f64bf-145">Não foi possível localizar o manipulador de solicitação inprocess.</span><span class="sxs-lookup"><span data-stu-id="f64bf-145">Could not find inprocess request handler.</span></span> <span data-ttu-id="f64bf-146">Saída capturada da invocação do hostfxr: Não foi possível encontrar nenhuma versão de estrutura compatível.</span><span class="sxs-lookup"><span data-stu-id="f64bf-146">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="f64bf-147">A estrutura especificada 'Microsoft.AspNetCore.App', versão '{VERSION}-preview-\*' não foi encontrada.</span><span class="sxs-lookup"><span data-stu-id="f64bf-147">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span> <span data-ttu-id="f64bf-148">Falha ao iniciar o aplicativo '/LM/W3SVC/1416782824/ROOT', ErrorCode '0x8000ffff'.</span><span class="sxs-lookup"><span data-stu-id="f64bf-148">Failed to start application '/LM/W3SVC/1416782824/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="f64bf-149">**Log de stdout do Módulo do ASP.NET Core:** Não foi possível encontrar nenhuma versão de estrutura compatível.</span><span class="sxs-lookup"><span data-stu-id="f64bf-149">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="f64bf-150">A estrutura especificada 'Microsoft.AspNetCore.App', versão '{VERSION}-preview-\*' não foi encontrada.</span><span class="sxs-lookup"><span data-stu-id="f64bf-150">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="f64bf-151">**Log de depuração do módulo do ASP.NET Core:** A invocação do hostfxr para encontrar o manipulador de solicitação inprocess falha sem encontrar nenhuma dependência nativa.</span><span class="sxs-lookup"><span data-stu-id="f64bf-151">**ASP.NET Core Module Debug Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="f64bf-152">Isso provavelmente significa que o aplicativo está configurado incorretamente, verifique as versões do Microsoft.NetCore.App e Microsoft.AspNetCore.App que são afetadas pelo aplicativo e estão instaladas no computador.</span><span class="sxs-lookup"><span data-stu-id="f64bf-152">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="f64bf-153">HRESULT com falha retornou: 0x8000ffff.</span><span class="sxs-lookup"><span data-stu-id="f64bf-153">Failed HRESULT returned: 0x8000ffff.</span></span> <span data-ttu-id="f64bf-154">Não foi possível localizar o manipulador de solicitação inprocess.</span><span class="sxs-lookup"><span data-stu-id="f64bf-154">Could not find inprocess request handler.</span></span> <span data-ttu-id="f64bf-155">Não foi possível encontrar nenhuma versão de estrutura compatível.</span><span class="sxs-lookup"><span data-stu-id="f64bf-155">It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="f64bf-156">A estrutura especificada 'Microsoft.AspNetCore.App', versão '{VERSION}-preview-\*' não foi encontrada.</span><span class="sxs-lookup"><span data-stu-id="f64bf-156">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

::: moniker-end

<span data-ttu-id="f64bf-157">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="f64bf-157">Troubleshooting:</span></span>

* <span data-ttu-id="f64bf-158">Se estiver executando o aplicativo em um tempo de execução de visualização, instale a extensão de site de 32 bits (x86) **ou** de 64 bits (x64) que corresponda ao número de bit do aplicativo e à versão de tempo de execução do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f64bf-158">If running the app on a preview runtime, install either the 32-bit (x86) **or** 64-bit (x64) site extension that matches the bitness of the app and the app's runtime version.</span></span> <span data-ttu-id="f64bf-159">**Não instale extensões ou várias versões de tempo de execução da extensão.**</span><span class="sxs-lookup"><span data-stu-id="f64bf-159">**Don't install both extensions or multiple runtime versions of the extension.**</span></span>

  * <span data-ttu-id="f64bf-160">Tempo de execução do ASP.NET Core {RUNTIME VERSION} (x86)</span><span class="sxs-lookup"><span data-stu-id="f64bf-160">ASP.NET Core {RUNTIME VERSION} (x86) Runtime</span></span>
  * <span data-ttu-id="f64bf-161">Tempo de execução do ASP.NET Core {RUNTIME VERSION} (x64)</span><span class="sxs-lookup"><span data-stu-id="f64bf-161">ASP.NET Core {RUNTIME VERSION} (x64) Runtime</span></span>

  <span data-ttu-id="f64bf-162">Reinicie o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f64bf-162">Restart the app.</span></span> <span data-ttu-id="f64bf-163">Aguarde vários segundos até que o aplicativo seja reiniciado.</span><span class="sxs-lookup"><span data-stu-id="f64bf-163">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="f64bf-164">Se a execução do aplicativo em um tempo de execução de visualização e as [extensões de site](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) de 32 bits (x86) e 64 bits (x64) estiverem instaladas, desinstale a extensão de site que não corresponde ao número de bit do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f64bf-164">If running the app on a preview runtime and both the 32-bit (x86) and 64-bit (x64) [site extensions](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) are installed, uninstall the site extension that doesn't match the bitness of the app.</span></span> <span data-ttu-id="f64bf-165">Depois de remover a extensão de site, reinicie o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f64bf-165">After removing the site extension, restart the app.</span></span> <span data-ttu-id="f64bf-166">Aguarde vários segundos até que o aplicativo seja reiniciado.</span><span class="sxs-lookup"><span data-stu-id="f64bf-166">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="f64bf-167">Se executar o aplicativo em um tempo de execução de visualização e o número de bit da extensão de site corresponder ao do aplicativo, confirme se a *versão do tempo de execução* da extensão de site de visualização corresponde à versão do tempo de execução do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f64bf-167">If running the app on a preview runtime and the site extension's bitness matches that of the app, confirm that the preview site extension's *runtime version* matches the app's runtime version.</span></span>

* <span data-ttu-id="f64bf-168">Confirme se a **Plataforma** do aplicativo em **Configurações do aplicativo** corresponde ao número de bit do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f64bf-168">Confirm that the app's **Platform** in **Application Settings** matches the bitness of the app.</span></span>

<span data-ttu-id="f64bf-169">Para obter mais informações, consulte <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.</span><span class="sxs-lookup"><span data-stu-id="f64bf-169">For more information, see <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.</span></span>

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a><span data-ttu-id="f64bf-170">Um aplicativo x86 é implantado, mas o pool de aplicativos não está habilitado para aplicativos de 32 bits</span><span class="sxs-lookup"><span data-stu-id="f64bf-170">An x86 app is deployed but the app pool isn't enabled for 32-bit apps</span></span>

* <span data-ttu-id="f64bf-171">**Navegador:** Erro HTTP 500.30 – Falha de início no processo do ANCM</span><span class="sxs-lookup"><span data-stu-id="f64bf-171">**Browser:** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="f64bf-172">**Log do Aplicativo:** Aplicativo '/LM/W3SVC/5/ROOT' com raiz física '{PATH}' atingiu uma exceção gerenciada inesperada, código de exceção = '0xe0434352'.</span><span class="sxs-lookup"><span data-stu-id="f64bf-172">**Application Log:** Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' hit unexpected managed exception, exception code = '0xe0434352'.</span></span> <span data-ttu-id="f64bf-173">Verifique os logs de stderr para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="f64bf-173">Please check the stderr logs for more information.</span></span> <span data-ttu-id="f64bf-174">Aplicativo '/LM/W3SVC/5/ROOT' com raiz física '{PATH}' falhou ao carregar o clr e o aplicativo gerenciado.</span><span class="sxs-lookup"><span data-stu-id="f64bf-174">Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' failed to load clr and managed application.</span></span> <span data-ttu-id="f64bf-175">O thread de trabalho do CLR foi encerrado prematuramente</span><span class="sxs-lookup"><span data-stu-id="f64bf-175">CLR worker thread exited prematurely</span></span>

* <span data-ttu-id="f64bf-176">**Log de stdout do Módulo do ASP.NET Core:** O arquivo de log é criado, mas vazio.</span><span class="sxs-lookup"><span data-stu-id="f64bf-176">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="f64bf-177">**Log de depuração do módulo do ASP.NET Core:** HRESULT com falha retornou: 0x8007023e</span><span class="sxs-lookup"><span data-stu-id="f64bf-177">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8007023e</span></span>

::: moniker-end

<span data-ttu-id="f64bf-178">Esse cenário é interceptado pelo SDK ao publicar um aplicativo autocontido.</span><span class="sxs-lookup"><span data-stu-id="f64bf-178">This scenario is trapped by the SDK when publishing a self-contained app.</span></span> <span data-ttu-id="f64bf-179">O SDK produzirá um erro se o RID não coincidir com o destino da plataforma (por exemplo, RID `win10-x64` com `<PlatformTarget>x86</PlatformTarget>` no arquivo de projeto).</span><span class="sxs-lookup"><span data-stu-id="f64bf-179">The SDK produces an error if the RID doesn't match the platform target (for example, `win10-x64` RID with `<PlatformTarget>x86</PlatformTarget>` in the project file).</span></span>

<span data-ttu-id="f64bf-180">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="f64bf-180">Troubleshooting:</span></span>

<span data-ttu-id="f64bf-181">Para uma implantação dependente da estrutura x86 (`<PlatformTarget>x86</PlatformTarget>`), habilite o pool de aplicativos de IIS para aplicativos de 32 bits.</span><span class="sxs-lookup"><span data-stu-id="f64bf-181">For an x86 framework-dependent deployment (`<PlatformTarget>x86</PlatformTarget>`), enable the IIS app pool for 32-bit apps.</span></span> <span data-ttu-id="f64bf-182">No Gerenciador do IIS, abra as **Configurações Avançadas** do pool de aplicativos e defina **Habilitar Aplicativos de 32 Bits** como **Verdadeiro**.</span><span class="sxs-lookup"><span data-stu-id="f64bf-182">In IIS Manager, open the app pool's **Advanced Settings** and set **Enable 32-Bit Applications** to **True**.</span></span>

## <a name="platform-conflicts-with-rid"></a><span data-ttu-id="f64bf-183">Conflitos de plataforma com o RID</span><span class="sxs-lookup"><span data-stu-id="f64bf-183">Platform conflicts with RID</span></span>

* <span data-ttu-id="f64bf-184">**Navegador:** Erro HTTP 502.5 – falha do processo</span><span class="sxs-lookup"><span data-stu-id="f64bf-184">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="f64bf-185">**Log do Aplicativo:** O aplicativo 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' com raiz física 'C:\{PATH}\' falhou ao iniciar o processo com a linha de comando '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ', ErrorCode = '0x80004005 : ff.</span><span class="sxs-lookup"><span data-stu-id="f64bf-185">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ', ErrorCode = '0x80004005 : ff.</span></span>

* <span data-ttu-id="f64bf-186">**Log de stdout do Módulo do ASP.NET Core:** Exceção sem tratamento: System.BadImageFormatException: Não foi possível carregar arquivo ou o assembly '{ASSEMBLY}.dll'.</span><span class="sxs-lookup"><span data-stu-id="f64bf-186">**ASP.NET Core Module stdout Log:** Unhandled Exception: System.BadImageFormatException: Could not load file or assembly '{ASSEMBLY}.dll'.</span></span> <span data-ttu-id="f64bf-187">Foi feita uma tentativa de carregar um programa com um formato incorreto.</span><span class="sxs-lookup"><span data-stu-id="f64bf-187">An attempt was made to load a program with an incorrect format.</span></span>

<span data-ttu-id="f64bf-188">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="f64bf-188">Troubleshooting:</span></span>

* <span data-ttu-id="f64bf-189">Confirme se o aplicativo é executado localmente no Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f64bf-189">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="f64bf-190">Uma falha do processo pode ser o resultado de um problema no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f64bf-190">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="f64bf-191">Para obter mais informações, consulte <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="f64bf-191">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="f64bf-192">Se essa exceção ocorrer para uma implantação dos Aplicativos do Azure ao fazer upgrade de um aplicativo e implantar assemblies mais recentes, exclua manualmente todos os arquivos da implantação anterior.</span><span class="sxs-lookup"><span data-stu-id="f64bf-192">If this exception occurs for an Azure Apps deployment when upgrading an app and deploying newer assemblies, manually delete all files from the prior deployment.</span></span> <span data-ttu-id="f64bf-193">Assemblies incompatíveis remanescentes podem resultar em uma exceção `System.BadImageFormatException` durante a implantação de um aplicativo atualizado.</span><span class="sxs-lookup"><span data-stu-id="f64bf-193">Lingering incompatible assemblies can result in a `System.BadImageFormatException` exception when deploying an upgraded app.</span></span>

## <a name="uri-endpoint-wrong-or-stopped-website"></a><span data-ttu-id="f64bf-194">Ponto de extremidade de URI incorreto ou site interrompido</span><span class="sxs-lookup"><span data-stu-id="f64bf-194">URI endpoint wrong or stopped website</span></span>

* <span data-ttu-id="f64bf-195">**Navegador:** ERR_CONNECTION_REFUSED **–OU–** Não é possível estabelecer conexão</span><span class="sxs-lookup"><span data-stu-id="f64bf-195">**Browser:** ERR_CONNECTION_REFUSED **--OR--** Unable to connect</span></span>

* <span data-ttu-id="f64bf-196">**Log do Aplicativo:** Nenhuma entrada</span><span class="sxs-lookup"><span data-stu-id="f64bf-196">**Application Log:** No entry</span></span>

* <span data-ttu-id="f64bf-197">**Log de stdout do Módulo do ASP.NET Core:** O arquivo de log não é criado.</span><span class="sxs-lookup"><span data-stu-id="f64bf-197">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="f64bf-198">**Log de depuração do módulo do ASP.NET Core:** O arquivo de log não é criado.</span><span class="sxs-lookup"><span data-stu-id="f64bf-198">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="f64bf-199">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="f64bf-199">Troubleshooting:</span></span>

* <span data-ttu-id="f64bf-200">Confirme se o ponto de extremidade do URI correto para o aplicativo está sendo usado.</span><span class="sxs-lookup"><span data-stu-id="f64bf-200">Confirm the correct URI endpoint for the app is in use.</span></span> <span data-ttu-id="f64bf-201">Verifique as associações.</span><span class="sxs-lookup"><span data-stu-id="f64bf-201">Check the bindings.</span></span>

* <span data-ttu-id="f64bf-202">Confirme que o site do IIS não está no estado *Parado*.</span><span class="sxs-lookup"><span data-stu-id="f64bf-202">Confirm that the IIS website isn't in the *Stopped* state.</span></span>

## <a name="corewebengine-or-w3svc-server-features-disabled"></a><span data-ttu-id="f64bf-203">Recursos do servidor CoreWebEngine ou W3SVC desabilitados</span><span class="sxs-lookup"><span data-stu-id="f64bf-203">CoreWebEngine or W3SVC server features disabled</span></span>

<span data-ttu-id="f64bf-204">**Exceção do Sistema Operacional:** Os recursos CoreWebEngine e W3SVC do IIS 7.0 devem ser instalados para usar o Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f64bf-204">**OS Exception:** The IIS 7.0 CoreWebEngine and W3SVC features must be installed to use the ASP.NET Core Module.</span></span>

<span data-ttu-id="f64bf-205">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="f64bf-205">Troubleshooting:</span></span>

<span data-ttu-id="f64bf-206">Confirme que a função e os recursos apropriados estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="f64bf-206">Confirm that the proper role and features are enabled.</span></span> <span data-ttu-id="f64bf-207">Consulte [Configuração do IIS](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="f64bf-207">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

## <a name="incorrect-website-physical-path-or-app-missing"></a><span data-ttu-id="f64bf-208">Caminho físico do site incorreto ou aplicativo ausente</span><span class="sxs-lookup"><span data-stu-id="f64bf-208">Incorrect website physical path or app missing</span></span>

* <span data-ttu-id="f64bf-209">**Navegador:** 403 Proibido – acesso negado **–OU–** 403.14 Proibido – o servidor Web está configurado para não listar o conteúdo deste diretório.</span><span class="sxs-lookup"><span data-stu-id="f64bf-209">**Browser:** 403 Forbidden - Access is denied **--OR--** 403.14 Forbidden - The Web server is configured to not list the contents of this directory.</span></span>

* <span data-ttu-id="f64bf-210">**Log do Aplicativo:** Nenhuma entrada</span><span class="sxs-lookup"><span data-stu-id="f64bf-210">**Application Log:** No entry</span></span>

* <span data-ttu-id="f64bf-211">**Log de stdout do Módulo do ASP.NET Core:** O arquivo de log não é criado.</span><span class="sxs-lookup"><span data-stu-id="f64bf-211">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="f64bf-212">**Log de depuração do módulo do ASP.NET Core:** O arquivo de log não é criado.</span><span class="sxs-lookup"><span data-stu-id="f64bf-212">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="f64bf-213">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="f64bf-213">Troubleshooting:</span></span>

<span data-ttu-id="f64bf-214">Confira as **Configurações Básicas** no site do IIS e a pasta do aplicativo físico.</span><span class="sxs-lookup"><span data-stu-id="f64bf-214">Check the IIS website **Basic Settings** and the physical app folder.</span></span> <span data-ttu-id="f64bf-215">Confirme que o aplicativo está na pasta no **Caminho físico** do site do IIS.</span><span class="sxs-lookup"><span data-stu-id="f64bf-215">Confirm that the app is in the folder at the IIS website **Physical path**.</span></span>

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a><span data-ttu-id="f64bf-216">Função incorreta, Módulo do ASP.NET Core Não Instalado ou permissões incorretas</span><span class="sxs-lookup"><span data-stu-id="f64bf-216">Incorrect role, ASP.NET Core Module not installed, or incorrect permissions</span></span>

* <span data-ttu-id="f64bf-217">**Navegador:** 500.19 Erro interno do servidor – a página solicitada não pode ser acessada porque os dados de configuração relacionados da página são inválidos.</span><span class="sxs-lookup"><span data-stu-id="f64bf-217">**Browser:** 500.19 Internal Server Error - The requested page cannot be accessed because the related configuration data for the page is invalid.</span></span> <span data-ttu-id="f64bf-218">**–OU–** Esta página não pode ser exibida</span><span class="sxs-lookup"><span data-stu-id="f64bf-218">**--OR--** This page can't be displayed</span></span>

* <span data-ttu-id="f64bf-219">**Log do Aplicativo:** Nenhuma entrada</span><span class="sxs-lookup"><span data-stu-id="f64bf-219">**Application Log:** No entry</span></span>

* <span data-ttu-id="f64bf-220">**Log de stdout do Módulo do ASP.NET Core:** O arquivo de log não é criado.</span><span class="sxs-lookup"><span data-stu-id="f64bf-220">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="f64bf-221">**Log de depuração do módulo do ASP.NET Core:** O arquivo de log não é criado.</span><span class="sxs-lookup"><span data-stu-id="f64bf-221">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="f64bf-222">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="f64bf-222">Troubleshooting:</span></span>

* <span data-ttu-id="f64bf-223">Confirme que você habilitou a função apropriada.</span><span class="sxs-lookup"><span data-stu-id="f64bf-223">Confirm that the proper role is enabled.</span></span> <span data-ttu-id="f64bf-224">Consulte [Configuração do IIS](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="f64bf-224">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

* <span data-ttu-id="f64bf-225">Abra **Programas e Recursos** ou **Aplicativos e Recursos** e confirme se a **Hospedagem do Windows Server** está instalada.</span><span class="sxs-lookup"><span data-stu-id="f64bf-225">Open **Programs & Features** or **Apps & features** and confirm that **Windows Server Hosting** is installed.</span></span> <span data-ttu-id="f64bf-226">Se a **Hospedagem do Windows Server** não estiver presente na lista de programas instalados, baixe e instale o Pacote de Hospedagem do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="f64bf-226">If **Windows Server Hosting** isn't present in the list of installed programs, download and install the .NET Core Hosting Bundle.</span></span>

  [<span data-ttu-id="f64bf-227">Instalador de pacote de hospedagem do .NET Core atual (download direto)</span><span class="sxs-lookup"><span data-stu-id="f64bf-227">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="f64bf-228">Para obter mais informações, confira [Instalar o pacote de hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="f64bf-228">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

* <span data-ttu-id="f64bf-229">Verifique se o **Pool de aplicativos** > **Modelo de processo** > **Identidade** está definido como **ApplicationPoolIdentity** ou se a identidade personalizada tem as permissões corretas para acessar a pasta de implantação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f64bf-229">Make sure that the **Application Pool** > **Process Model** > **Identity** is set to **ApplicationPoolIdentity** or the custom identity has the correct permissions to access the app's deployment folder.</span></span>

* <span data-ttu-id="f64bf-230">Se você desinstalou o Pacote de Hospedagem do ASP.NET Core e instalou uma versão anterior do pacote de hospedagem, o arquivo *applicationHost.config* não inclui uma seção para o Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f64bf-230">If you uninstalled the ASP.NET Core Hosting Bundle and installed an earlier version of the hosting bundle, the *applicationHost.config* file doesn't include a section for the ASP.NET Core Module.</span></span> <span data-ttu-id="f64bf-231">Abra *applicationHost.config* em *%windir%/System32/inetsrv/config* e encontre o grupo de seção `<configuration><configSections><sectionGroup name="system.webServer">`.</span><span class="sxs-lookup"><span data-stu-id="f64bf-231">Open *applicationHost.config* at *%windir%/System32/inetsrv/config* and find the `<configuration><configSections><sectionGroup name="system.webServer">` section group.</span></span> <span data-ttu-id="f64bf-232">Se estiver faltando a seção do Módulo do ASP.NET Core no grupo de seções, adicione o elemento da seção:</span><span class="sxs-lookup"><span data-stu-id="f64bf-232">If the section for the ASP.NET Core Module is missing from the section group, add the section element:</span></span>

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  <span data-ttu-id="f64bf-233">Como alternativa, instale a versão mais recente do Pacote de Hospedagem do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f64bf-233">Alternatively, install the latest version of the ASP.NET Core Hosting Bundle.</span></span> <span data-ttu-id="f64bf-234">A versão mais recente é compatível com versões anteriores dos aplicativos do ASP.NET Core com suporte.</span><span class="sxs-lookup"><span data-stu-id="f64bf-234">The latest version is backwards-compatible with supported ASP.NET Core apps.</span></span>

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a><span data-ttu-id="f64bf-235">processPath incorreto, variável de PATH ausente, pacote de hospedagem não instalado, sistema/IIS não reiniciado, Pacotes Redistribuíveis do VC++ não instalados ou violação de acesso de dotnet.exe</span><span class="sxs-lookup"><span data-stu-id="f64bf-235">Incorrect processPath, missing PATH variable, Hosting Bundle not installed, system/IIS not restarted, VC++ Redistributable not installed, or dotnet.exe access violation</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="f64bf-236">**Navegador:** Erro HTTP 500.0 – Falha de carregamento de manipulador em processo ANCM</span><span class="sxs-lookup"><span data-stu-id="f64bf-236">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="f64bf-237">**Log do Aplicativo:** Aplicativo 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' com raiz física 'C:\{PATH}\' falhou ao iniciar o processo com a linha de comando '"{...}"</span><span class="sxs-lookup"><span data-stu-id="f64bf-237">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="f64bf-238">', ErrorCode = '0x80070002 : 0.</span><span class="sxs-lookup"><span data-stu-id="f64bf-238">', ErrorCode = '0x80070002 : 0.</span></span> <span data-ttu-id="f64bf-239">Não foi possível iniciar o aplicativo '{PATH}'.</span><span class="sxs-lookup"><span data-stu-id="f64bf-239">Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="f64bf-240">O executável não foi encontrado em '{PATH}'.</span><span class="sxs-lookup"><span data-stu-id="f64bf-240">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="f64bf-241">Falha ao iniciar o aplicativo '/LM/W3SVC/2/ROOT', ErrorCode '0x8007023e'.</span><span class="sxs-lookup"><span data-stu-id="f64bf-241">Failed to start application '/LM/W3SVC/2/ROOT', ErrorCode '0x8007023e'.</span></span>

* <span data-ttu-id="f64bf-242">**Log de stdout do Módulo do ASP.NET Core:** O arquivo de log não é criado.</span><span class="sxs-lookup"><span data-stu-id="f64bf-242">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="f64bf-243">**Log de depuração do módulo do ASP.NET Core:** Log de eventos: Não foi possível iniciar o aplicativo '{PATH}'.</span><span class="sxs-lookup"><span data-stu-id="f64bf-243">**ASP.NET Core Module Debug Log:** Event Log: 'Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="f64bf-244">O executável não foi encontrado em '{PATH}'.</span><span class="sxs-lookup"><span data-stu-id="f64bf-244">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="f64bf-245">HRESULT com falha retornou: 0x8007023e</span><span class="sxs-lookup"><span data-stu-id="f64bf-245">Failed HRESULT returned: 0x8007023e</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="f64bf-246">**Navegador:** Erro HTTP 502.5 – falha do processo</span><span class="sxs-lookup"><span data-stu-id="f64bf-246">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="f64bf-247">**Log do Aplicativo:** Aplicativo 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' com raiz física 'C:\{PATH}\' falhou ao iniciar o processo com a linha de comando '"{...}"</span><span class="sxs-lookup"><span data-stu-id="f64bf-247">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="f64bf-248">', ErrorCode = '0x80070002 : 0.</span><span class="sxs-lookup"><span data-stu-id="f64bf-248">', ErrorCode = '0x80070002 : 0.</span></span>

* <span data-ttu-id="f64bf-249">**Log de stdout do Módulo do ASP.NET Core:** O arquivo de log é criado, mas vazio.</span><span class="sxs-lookup"><span data-stu-id="f64bf-249">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker-end

<span data-ttu-id="f64bf-250">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="f64bf-250">Troubleshooting:</span></span>

* <span data-ttu-id="f64bf-251">Confirme se o aplicativo é executado localmente no Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f64bf-251">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="f64bf-252">Uma falha do processo pode ser o resultado de um problema no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f64bf-252">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="f64bf-253">Para obter mais informações, consulte <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="f64bf-253">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="f64bf-254">Verifique o atributo *processPath* no elemento `<aspNetCore>` em *web.config* para confirmar se ele é `dotnet` para uma FDD (implantação dependente de estrutura) ou `.\{ASSEMBLY}.exe` para uma [SCD (implantação autossuficiente)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="f64bf-254">Check the *processPath* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's `dotnet` for a framework-dependent deployment (FDD) or `.\{ASSEMBLY}.exe` for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

* <span data-ttu-id="f64bf-255">Para uma FDD, o *dotnet.exe* pode não estar acessível por meio das configurações de PATH.</span><span class="sxs-lookup"><span data-stu-id="f64bf-255">For an FDD, *dotnet.exe* might not be accessible via the PATH settings.</span></span> <span data-ttu-id="f64bf-256">Confirme se *C:\Arquivos de Programas\dotnet\\* existe nas configurações de PATH do Sistema.</span><span class="sxs-lookup"><span data-stu-id="f64bf-256">Confirm that *C:\Program Files\dotnet\\* exists in the System PATH settings.</span></span>

* <span data-ttu-id="f64bf-257">Para uma FDD, o *dotnet.exe* pode não estar acessível para a identidade do usuário do pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="f64bf-257">For an FDD, *dotnet.exe* might not be accessible for the user identity of the app pool.</span></span> <span data-ttu-id="f64bf-258">Confirme se a identidade do usuário do pool de aplicativos tem acesso ao diretório *C:\Arquivos de Programas\dotnet*.</span><span class="sxs-lookup"><span data-stu-id="f64bf-258">Confirm that the app pool user identity has access to the *C:\Program Files\dotnet* directory.</span></span> <span data-ttu-id="f64bf-259">Confirme se não há nenhuma regra de negação configurada para a identidade do usuário do pool de aplicativos no *C:\Arquivos de Programas\dotnet* e nos diretórios do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f64bf-259">Confirm that there are no deny rules configured for the app pool user identity on the *C:\Program Files\dotnet* and app directories.</span></span>

* <span data-ttu-id="f64bf-260">Talvez você tenha implantado uma FDD e instalado o .NET Core sem reiniciar o IIS.</span><span class="sxs-lookup"><span data-stu-id="f64bf-260">An FDD may have been deployed and .NET Core installed without restarting IIS.</span></span> <span data-ttu-id="f64bf-261">Reinicie o servidor ou o IIS executando **net stop was /y** seguido por **net start w3svc** em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="f64bf-261">Either restart the server or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="f64bf-262">Você pode ter implantado uma FDD sem instalar o tempo de execução do .NET Core no sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="f64bf-262">An FDD may have been deployed without installing the .NET Core runtime on the hosting system.</span></span> <span data-ttu-id="f64bf-263">Se o tempo de execução do .NET Core ainda não foi instalado, execute o **Instalador do Pacote de Hospedagem do .NET Core** no sistema.</span><span class="sxs-lookup"><span data-stu-id="f64bf-263">If the .NET Core runtime hasn't been installed, run the **.NET Core Hosting Bundle installer** on the system.</span></span>

  [<span data-ttu-id="f64bf-264">Instalador de pacote de hospedagem do .NET Core atual (download direto)</span><span class="sxs-lookup"><span data-stu-id="f64bf-264">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="f64bf-265">Para obter mais informações, confira [Instalar o pacote de hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="f64bf-265">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

  <span data-ttu-id="f64bf-266">Se um tempo de execução específico for necessário, baixe o tempo de execução dos [Arquivos de Download do .NET](https://dotnet.microsoft.com/download/archives) e instale-o no sistema.</span><span class="sxs-lookup"><span data-stu-id="f64bf-266">If a specific runtime is required, download the runtime from the [.NET Download Archives](https://dotnet.microsoft.com/download/archives) and install it on the system.</span></span> <span data-ttu-id="f64bf-267">Conclua a instalação reiniciando o sistema ou o IIS executando **net stop was /y** seguido por **net start w3svc** em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="f64bf-267">Complete the installation by restarting the system or restarting IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="f64bf-268">Talvez você tenha implantado uma FDD e os *Pacotes redistribuíveis do Microsoft Visual C++ 2015 (x64)* não estejam instalados no sistema.</span><span class="sxs-lookup"><span data-stu-id="f64bf-268">An FDD may have been deployed and the *Microsoft Visual C++ 2015 Redistributable (x64)* isn't installed on the system.</span></span> <span data-ttu-id="f64bf-269">Obtenha um instalador do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=53840).</span><span class="sxs-lookup"><span data-stu-id="f64bf-269">Obtain an installer from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840).</span></span>

## <a name="incorrect-arguments-of-aspnetcore-element"></a><span data-ttu-id="f64bf-270">Argumentos incorretos do elemento \<aspNetCore></span><span class="sxs-lookup"><span data-stu-id="f64bf-270">Incorrect arguments of \<aspNetCore> element</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="f64bf-271">**Navegador:** Erro HTTP 500.0 – Falha de carregamento de manipulador em processo ANCM</span><span class="sxs-lookup"><span data-stu-id="f64bf-271">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="f64bf-272">**Log do Aplicativo:** A invocação do hostfxr para encontrar o manipulador de solicitação inprocess falha sem encontrar nenhuma dependência nativa.</span><span class="sxs-lookup"><span data-stu-id="f64bf-272">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="f64bf-273">Isso provavelmente significa que o aplicativo está configurado incorretamente, verifique as versões do Microsoft.NetCore.App e Microsoft.AspNetCore.App que são afetadas pelo aplicativo e estão instaladas no computador.</span><span class="sxs-lookup"><span data-stu-id="f64bf-273">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="f64bf-274">Não foi possível localizar o manipulador de solicitação inprocess.</span><span class="sxs-lookup"><span data-stu-id="f64bf-274">Could not find inprocess request handler.</span></span> <span data-ttu-id="f64bf-275">Saída capturada da invocação do hostfxr: Você quis dizer executar comandos do SDK do dotnet?</span><span class="sxs-lookup"><span data-stu-id="f64bf-275">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="f64bf-276">Instale o SDK do dotnet de: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Falha ao iniciar o aplicativo '/LM/W3SVC/3/ROOT', ErrorCode '0x8000ffff'.</span><span class="sxs-lookup"><span data-stu-id="f64bf-276">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed to start application '/LM/W3SVC/3/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="f64bf-277">**Log de stdout do Módulo do ASP.NET Core:** Você quis dizer executar comandos do SDK do dotnet?</span><span class="sxs-lookup"><span data-stu-id="f64bf-277">**ASP.NET Core Module stdout Log:** Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="f64bf-278">Instale o SDK do dotnet de: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409</span><span class="sxs-lookup"><span data-stu-id="f64bf-278">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409</span></span>

* <span data-ttu-id="f64bf-279">**Log de depuração do módulo do ASP.NET Core:** A invocação do hostfxr para encontrar o manipulador de solicitação inprocess falha sem encontrar nenhuma dependência nativa.</span><span class="sxs-lookup"><span data-stu-id="f64bf-279">**ASP.NET Core Module Debug Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="f64bf-280">Isso provavelmente significa que o aplicativo está configurado incorretamente, verifique as versões do Microsoft.NetCore.App e Microsoft.AspNetCore.App que são afetadas pelo aplicativo e estão instaladas no computador.</span><span class="sxs-lookup"><span data-stu-id="f64bf-280">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="f64bf-281">HRESULT com falha retornou: 0x8000ffff Não foi possível localizar o manipulador de solicitação inprocess.</span><span class="sxs-lookup"><span data-stu-id="f64bf-281">Failed HRESULT returned: 0x8000ffff Could not find inprocess request handler.</span></span> <span data-ttu-id="f64bf-282">Saída capturada da invocação do hostfxr: Você quis dizer executar comandos do SDK do dotnet?</span><span class="sxs-lookup"><span data-stu-id="f64bf-282">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="f64bf-283">Instale o SDK do dotnet de: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 HRESULT com falha retornou: 0x8000ffff</span><span class="sxs-lookup"><span data-stu-id="f64bf-283">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed HRESULT returned: 0x8000ffff</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="f64bf-284">**Navegador:** Erro HTTP 502.5 – falha do processo</span><span class="sxs-lookup"><span data-stu-id="f64bf-284">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="f64bf-285">**Log do Aplicativo:** Aplicativo 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' com a raiz física 'C:\{PATH}\' falha ao iniciar o processo com a linha de comando '"dotnet" .\{ASSEMBLY}.dll', ErrorCode = '0x80004005: 80008081.</span><span class="sxs-lookup"><span data-stu-id="f64bf-285">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"dotnet" .\{ASSEMBLY}.dll', ErrorCode = '0x80004005 : 80008081.</span></span>

* <span data-ttu-id="f64bf-286">**Log de stdout do Módulo do ASP.NET Core:** O aplicativo a ser executado não existe: 'PATH\{ASSEMBLY}.dll'</span><span class="sxs-lookup"><span data-stu-id="f64bf-286">**ASP.NET Core Module stdout Log:** The application to execute does not exist: 'PATH\{ASSEMBLY}.dll'</span></span>

::: moniker-end

<span data-ttu-id="f64bf-287">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="f64bf-287">Troubleshooting:</span></span>

* <span data-ttu-id="f64bf-288">Confirme se o aplicativo é executado localmente no Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f64bf-288">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="f64bf-289">Uma falha do processo pode ser o resultado de um problema no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f64bf-289">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="f64bf-290">Para obter mais informações, consulte <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="f64bf-290">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="f64bf-291">Examine o atributo *arguments* no elemento `<aspNetCore>` no *web.config* para confirmar se ele: (a) é `.\{ASSEMBLY}.dll` de uma FDD (implantação dependente de estrutura); ou (b) não está presente, é uma cadeia de caracteres vazia (`arguments=""`) ou uma lista de argumentos do aplicativo (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) para uma SCD (implantação autossuficiente).</span><span class="sxs-lookup"><span data-stu-id="f64bf-291">Examine the *arguments* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's either (a) `.\{ASSEMBLY}.dll` for a framework-dependent deployment (FDD); or (b) not present, an empty string (`arguments=""`), or a list of the app's arguments (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) for a self-contained deployment (SCD).</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="missing-net-core-shared-framework"></a><span data-ttu-id="f64bf-292">Estrutura compartilhada do .NET Core ausente</span><span class="sxs-lookup"><span data-stu-id="f64bf-292">Missing .NET Core shared framework</span></span>

* <span data-ttu-id="f64bf-293">**Navegador:** Erro HTTP 500.0 – Falha de carregamento de manipulador em processo ANCM</span><span class="sxs-lookup"><span data-stu-id="f64bf-293">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="f64bf-294">**Log do Aplicativo:** A invocação do hostfxr para encontrar o manipulador de solicitação inprocess falha sem encontrar nenhuma dependência nativa.</span><span class="sxs-lookup"><span data-stu-id="f64bf-294">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="f64bf-295">Isso provavelmente significa que o aplicativo está configurado incorretamente, verifique as versões do Microsoft.NetCore.App e Microsoft.AspNetCore.App que são afetadas pelo aplicativo e estão instaladas no computador.</span><span class="sxs-lookup"><span data-stu-id="f64bf-295">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="f64bf-296">Não foi possível localizar o manipulador de solicitação inprocess.</span><span class="sxs-lookup"><span data-stu-id="f64bf-296">Could not find inprocess request handler.</span></span> <span data-ttu-id="f64bf-297">Saída capturada da invocação do hostfxr: Não foi possível encontrar nenhuma versão de estrutura compatível.</span><span class="sxs-lookup"><span data-stu-id="f64bf-297">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="f64bf-298">A estrutura especificada 'Microsoft.AspNetCore.App', versão '{VERSION}', não foi encontrada.</span><span class="sxs-lookup"><span data-stu-id="f64bf-298">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

<span data-ttu-id="f64bf-299">Falha ao iniciar o aplicativo '/LM/W3SVC/5/ROOT', ErrorCode '0x8000ffff'.</span><span class="sxs-lookup"><span data-stu-id="f64bf-299">Failed to start application '/LM/W3SVC/5/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="f64bf-300">**Log de stdout do Módulo do ASP.NET Core:** Não foi possível encontrar nenhuma versão de estrutura compatível.</span><span class="sxs-lookup"><span data-stu-id="f64bf-300">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="f64bf-301">A estrutura especificada 'Microsoft.AspNetCore.App', versão '{VERSION}', não foi encontrada.</span><span class="sxs-lookup"><span data-stu-id="f64bf-301">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

* <span data-ttu-id="f64bf-302">**Log de depuração do módulo do ASP.NET Core:** HRESULT com falha retornou: 0x8000ffff</span><span class="sxs-lookup"><span data-stu-id="f64bf-302">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8000ffff</span></span>

::: moniker-end

<span data-ttu-id="f64bf-303">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="f64bf-303">Troubleshooting:</span></span>

<span data-ttu-id="f64bf-304">Para uma FDD (implantação dependente de estrutura), confirme se você tem o tempo de execução correto instalado no sistema.</span><span class="sxs-lookup"><span data-stu-id="f64bf-304">For a framework-dependent deployment (FDD), confirm that the correct runtime installed on the system.</span></span>

## <a name="stopped-application-pool"></a><span data-ttu-id="f64bf-305">Pool de aplicativos interrompido</span><span class="sxs-lookup"><span data-stu-id="f64bf-305">Stopped Application Pool</span></span>

* <span data-ttu-id="f64bf-306">**Navegador:** 503 Serviço Não Disponível</span><span class="sxs-lookup"><span data-stu-id="f64bf-306">**Browser:** 503 Service Unavailable</span></span>

* <span data-ttu-id="f64bf-307">**Log do Aplicativo:** Nenhuma entrada</span><span class="sxs-lookup"><span data-stu-id="f64bf-307">**Application Log:** No entry</span></span>

* <span data-ttu-id="f64bf-308">**Log de stdout do Módulo do ASP.NET Core:** O arquivo de log não é criado.</span><span class="sxs-lookup"><span data-stu-id="f64bf-308">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="f64bf-309">**Log de depuração do módulo do ASP.NET Core:** O arquivo de log não é criado.</span><span class="sxs-lookup"><span data-stu-id="f64bf-309">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="f64bf-310">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="f64bf-310">Troubleshooting:</span></span>

<span data-ttu-id="f64bf-311">Confirme que o Pool de Aplicativos não está no estado *Parado*.</span><span class="sxs-lookup"><span data-stu-id="f64bf-311">Confirm that the Application Pool isn't in the *Stopped* state.</span></span>

## <a name="sub-application-includes-a-handlers-section"></a><span data-ttu-id="f64bf-312">O subaplicativo inclui uma seção \<manipuladores></span><span class="sxs-lookup"><span data-stu-id="f64bf-312">Sub-application includes a \<handlers> section</span></span>

* <span data-ttu-id="f64bf-313">**Navegador:** Erro HTTP 500.19 – Erro Interno do Servidor</span><span class="sxs-lookup"><span data-stu-id="f64bf-313">**Browser:** HTTP Error 500.19 - Internal Server Error</span></span>

* <span data-ttu-id="f64bf-314">**Log do Aplicativo:** Nenhuma entrada</span><span class="sxs-lookup"><span data-stu-id="f64bf-314">**Application Log:** No entry</span></span>

* <span data-ttu-id="f64bf-315">**Log de stdout do Módulo do ASP.NET Core:** O arquivo de log do aplicativo raiz é criado e mostra uma operação normal.</span><span class="sxs-lookup"><span data-stu-id="f64bf-315">**ASP.NET Core Module stdout Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="f64bf-316">O arquivo de log do subaplicativo não é criado.</span><span class="sxs-lookup"><span data-stu-id="f64bf-316">The sub-app's log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="f64bf-317">**Log de depuração do módulo do ASP.NET Core:** O arquivo de log do aplicativo raiz é criado e mostra uma operação normal.</span><span class="sxs-lookup"><span data-stu-id="f64bf-317">**ASP.NET Core Module Debug Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="f64bf-318">O arquivo de log do subaplicativo não é criado.</span><span class="sxs-lookup"><span data-stu-id="f64bf-318">The sub-app's log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="f64bf-319">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="f64bf-319">Troubleshooting:</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f64bf-320">Confirme se o arquivo *web.config* do subaplicativo não inclui uma seção `<handlers>` ou que o subaplicativo não herda os manipuladores do aplicativo pai.</span><span class="sxs-lookup"><span data-stu-id="f64bf-320">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section or that the sub-app doesn't inherit the parent app's handlers.</span></span>

<span data-ttu-id="f64bf-321">A seção `<system.webServer>` do aplicativo pai de *web.config* é colocada dentro de um elemento `<location>`.</span><span class="sxs-lookup"><span data-stu-id="f64bf-321">The parent app's `<system.webServer>` section of *web.config* is placed inside of a `<location>` element.</span></span> <span data-ttu-id="f64bf-322">A propriedade <xref:System.Configuration.SectionInformation.InheritInChildApplications*> é definida como `false` para indicar que as configurações especificadas no elemento [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) não são herdadas por aplicativos que residem em um subdiretório do aplicativo pai.</span><span class="sxs-lookup"><span data-stu-id="f64bf-322">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the parent app.</span></span> <span data-ttu-id="f64bf-323">Para obter mais informações, consulte <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="f64bf-323">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f64bf-324">Confirme se o arquivo *web.config* do subaplicativo não inclui uma seção `<handlers>`.</span><span class="sxs-lookup"><span data-stu-id="f64bf-324">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section.</span></span>

::: moniker-end

## <a name="stdout-log-path-incorrect"></a><span data-ttu-id="f64bf-325">caminho do log de stdout incorreto</span><span class="sxs-lookup"><span data-stu-id="f64bf-325">stdout log path incorrect</span></span>

* <span data-ttu-id="f64bf-326">**Navegador:** O aplicativo responde normalmente.</span><span class="sxs-lookup"><span data-stu-id="f64bf-326">**Browser:** The app responds normally.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="f64bf-327">**Log do Aplicativo:** Não foi possível iniciar o redirecionamento de stdout em C:\Arquivos de Programas\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span><span class="sxs-lookup"><span data-stu-id="f64bf-327">**Application Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="f64bf-328">Mensagem de exceção: HRESULT 0x80070005 retornado em {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span><span class="sxs-lookup"><span data-stu-id="f64bf-328">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="f64bf-329">Não foi possível parar o redirecionamento de stdout em C:\Arquivos de Programas\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span><span class="sxs-lookup"><span data-stu-id="f64bf-329">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="f64bf-330">Mensagem de exceção: HRESULT 0x80070002 retornado em {PATH}.</span><span class="sxs-lookup"><span data-stu-id="f64bf-330">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="f64bf-331">Não foi possível iniciar o redirecionamento de stdout em {PATH}\aspnetcorev2_inprocess.dll.</span><span class="sxs-lookup"><span data-stu-id="f64bf-331">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

* <span data-ttu-id="f64bf-332">**Log de stdout do Módulo do ASP.NET Core:** O arquivo de log não é criado.</span><span class="sxs-lookup"><span data-stu-id="f64bf-332">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="f64bf-333">**Log de depuração do módulo do ASP.NET Core:** Não foi possível iniciar o redirecionamento de stdout em C:\Arquivos de Programas\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span><span class="sxs-lookup"><span data-stu-id="f64bf-333">**ASP.NET Core Module debug Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="f64bf-334">Mensagem de exceção: HRESULT 0x80070005 retornado em {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span><span class="sxs-lookup"><span data-stu-id="f64bf-334">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="f64bf-335">Não foi possível parar o redirecionamento de stdout em C:\Arquivos de Programas\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span><span class="sxs-lookup"><span data-stu-id="f64bf-335">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="f64bf-336">Mensagem de exceção: HRESULT 0x80070002 retornado em {PATH}.</span><span class="sxs-lookup"><span data-stu-id="f64bf-336">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="f64bf-337">Não foi possível iniciar o redirecionamento de stdout em {PATH}\aspnetcorev2_inprocess.dll.</span><span class="sxs-lookup"><span data-stu-id="f64bf-337">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="f64bf-338">**Log do Aplicativo:** Aviso: Não foi possível criar stdoutLogFile \\?\{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log, ErrorCode = -2147024893.</span><span class="sxs-lookup"><span data-stu-id="f64bf-338">**Application Log:** Warning: Could not create stdoutLogFile \\?\{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log, ErrorCode = -2147024893.</span></span>

* <span data-ttu-id="f64bf-339">**Log de stdout do Módulo do ASP.NET Core:** O arquivo de log não é criado.</span><span class="sxs-lookup"><span data-stu-id="f64bf-339">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="f64bf-340">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="f64bf-340">Troubleshooting:</span></span>

* <span data-ttu-id="f64bf-341">O caminho `stdoutLogFile` especificado no elemento `<aspNetCore>` de *web.config* não existe.</span><span class="sxs-lookup"><span data-stu-id="f64bf-341">The `stdoutLogFile` path specified in the `<aspNetCore>` element of *web.config* doesn't exist.</span></span> <span data-ttu-id="f64bf-342">Para obter mais informações, confira [Módulo ASP.NET Core: criação de log e redirecionamento](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span><span class="sxs-lookup"><span data-stu-id="f64bf-342">For more information, see [ASP.NET Core Module: Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span></span>

* <span data-ttu-id="f64bf-343">O usuário do pool de aplicativos não tem acesso de gravação para o caminho do log de stdout.</span><span class="sxs-lookup"><span data-stu-id="f64bf-343">The app pool user doesn't have write access to the stdout log path.</span></span>

## <a name="application-configuration-general-issue"></a><span data-ttu-id="f64bf-344">Problema geral de configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="f64bf-344">Application configuration general issue</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="f64bf-345">**Navegador:** Erro HTTP 500.0 – Falha de carregamento de manipulador em processo ANCM **–OU–** Erro HTTP 500.30 – Falha de início no processo do ANCM</span><span class="sxs-lookup"><span data-stu-id="f64bf-345">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure **--OR--** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="f64bf-346">**Log do Aplicativo:** Variável</span><span class="sxs-lookup"><span data-stu-id="f64bf-346">**Application Log:** Variable</span></span>

* <span data-ttu-id="f64bf-347">**Log de stdout do Módulo do ASP.NET Core:** O arquivo de log é criado, mas vazio, ou criado com entradas normais até o ponto da falha do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f64bf-347">**ASP.NET Core Module stdout Log:** The log file is created but empty or created with normal entries until the point of the app failing.</span></span>

* <span data-ttu-id="f64bf-348">**Log de depuração do módulo do ASP.NET Core:** Variável</span><span class="sxs-lookup"><span data-stu-id="f64bf-348">**ASP.NET Core Module Debug Log:** Variable</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="f64bf-349">**Navegador:** Erro HTTP 502.5 – falha do processo</span><span class="sxs-lookup"><span data-stu-id="f64bf-349">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="f64bf-350">**Log do Aplicativo:** Aplicativo 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' com raiz física 'C:\{PATH}\' criada no processo com a linha de comando '"C:\{PATH}\{ASSEMBLY}.{exe|dll}" ', mas falhou, não respondeu ou não escutou na porta '{PORT}' fornecida, ErrorCode = '{ERROR CODE}'</span><span class="sxs-lookup"><span data-stu-id="f64bf-350">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' created process with commandline '"C:\{PATH}\{ASSEMBLY}.{exe|dll}" ' but either crashed or did not respond or did not listen on the given port '{PORT}', ErrorCode = '{ERROR CODE}'</span></span>

* <span data-ttu-id="f64bf-351">**Log de stdout do Módulo do ASP.NET Core:** O arquivo de log é criado, mas vazio.</span><span class="sxs-lookup"><span data-stu-id="f64bf-351">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker-end

<span data-ttu-id="f64bf-352">Solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="f64bf-352">Troubleshooting:</span></span>

<span data-ttu-id="f64bf-353">O processo não pôde ser iniciado, provavelmente, devido a um problema de programação ou configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f64bf-353">The process failed to start, most likely due to an app configuration or programming issue.</span></span>

<span data-ttu-id="f64bf-354">Para mais informações, consulte os seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="f64bf-354">For more information, see the following topics:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>
