---
title: Depurar ASP.NET Core Blazor
author: guardrex
description: Saiba como depurar Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 1b0035af48b82807a6ae14835a41a1ecbef06bb6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78661703"
---
# <a name="debug-aspnet-core-blazor"></a><span data-ttu-id="551ee-103">Depurar ASP.NET Core mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="551ee-103">Debug ASP.NET Core Blazor</span></span>

[<span data-ttu-id="551ee-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="551ee-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="551ee-105">Há suporte *antecipado* para depuração de Webassembly mais recente usando as ferramentas de desenvolvimento do navegador em navegadores baseados em Chromium (Chrome/Edge).</span><span class="sxs-lookup"><span data-stu-id="551ee-105">*Early* support exists for debugging Blazor WebAssembly using the browser dev tools in Chromium-based browsers (Chrome/Edge).</span></span> <span data-ttu-id="551ee-106">O trabalho está em andamento para:</span><span class="sxs-lookup"><span data-stu-id="551ee-106">Work is in progress to:</span></span>

* <span data-ttu-id="551ee-107">Habilite totalmente a depuração no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="551ee-107">Fully enable debugging in Visual Studio.</span></span>
* <span data-ttu-id="551ee-108">Habilite a depuração no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="551ee-108">Enable debugging in Visual Studio Code.</span></span>

<span data-ttu-id="551ee-109">Os recursos do depurador são limitados.</span><span class="sxs-lookup"><span data-stu-id="551ee-109">Debugger capabilities are limited.</span></span> <span data-ttu-id="551ee-110">Os cenários disponíveis incluem:</span><span class="sxs-lookup"><span data-stu-id="551ee-110">Available scenarios include:</span></span>

* <span data-ttu-id="551ee-111">Definir e remover pontos de interrupção.</span><span class="sxs-lookup"><span data-stu-id="551ee-111">Set and remove breakpoints.</span></span>
* <span data-ttu-id="551ee-112">Etapa única (`F10`) por meio da execução do código ou de retomada (`F8`).</span><span class="sxs-lookup"><span data-stu-id="551ee-112">Single-step (`F10`) through the code or resume (`F8`) code execution.</span></span>
* <span data-ttu-id="551ee-113">Na exibição *locais* , observe os valores de quaisquer variáveis locais do tipo `int`, `string`e `bool`.</span><span class="sxs-lookup"><span data-stu-id="551ee-113">In the *Locals* display, observe the values of any local variables of type `int`, `string`, and `bool`.</span></span>
* <span data-ttu-id="551ee-114">Consulte a pilha de chamadas, incluindo cadeias de chamada que vão do JavaScript para o .NET e do .NET para o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="551ee-114">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="551ee-115">Você *não pode*:</span><span class="sxs-lookup"><span data-stu-id="551ee-115">You *can't*:</span></span>

* <span data-ttu-id="551ee-116">Observe os valores de qualquer local que não seja um `int`, `string`ou `bool`.</span><span class="sxs-lookup"><span data-stu-id="551ee-116">Observe the values of any locals that aren't an `int`, `string`, or `bool`.</span></span>
* <span data-ttu-id="551ee-117">Observe os valores de quaisquer propriedades ou campos de classe.</span><span class="sxs-lookup"><span data-stu-id="551ee-117">Observe the values of any class properties or fields.</span></span>
* <span data-ttu-id="551ee-118">Passe o mouse sobre variáveis para ver seus valores.</span><span class="sxs-lookup"><span data-stu-id="551ee-118">Hover over variables to see their values.</span></span>
* <span data-ttu-id="551ee-119">Avalie as expressões no console.</span><span class="sxs-lookup"><span data-stu-id="551ee-119">Evaluate expressions in the console.</span></span>
* <span data-ttu-id="551ee-120">Passe pelas chamadas assíncronas.</span><span class="sxs-lookup"><span data-stu-id="551ee-120">Step across async calls.</span></span>
* <span data-ttu-id="551ee-121">Execute a maioria dos outros cenários de depuração comuns.</span><span class="sxs-lookup"><span data-stu-id="551ee-121">Perform most other ordinary debugging scenarios.</span></span>

<span data-ttu-id="551ee-122">O desenvolvimento de mais cenários de depuração é um foco contínuo da equipe de engenharia.</span><span class="sxs-lookup"><span data-stu-id="551ee-122">Development of further debugging scenarios is an on-going focus of the engineering team.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="551ee-123">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="551ee-123">Prerequisites</span></span>

<span data-ttu-id="551ee-124">A depuração requer um dos seguintes navegadores:</span><span class="sxs-lookup"><span data-stu-id="551ee-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="551ee-125">Google Chrome (versão 70 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="551ee-125">Google Chrome (version 70 or later)</span></span>
* <span data-ttu-id="551ee-126">Visualização do Microsoft Edge ([canal de desenvolvimento de borda](https://www.microsoftedgeinsider.com))</span><span class="sxs-lookup"><span data-stu-id="551ee-126">Microsoft Edge preview ([Edge Dev Channel](https://www.microsoftedgeinsider.com))</span></span>

## <a name="procedure"></a><span data-ttu-id="551ee-127">Procedimento</span><span class="sxs-lookup"><span data-stu-id="551ee-127">Procedure</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="551ee-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="551ee-128">Visual Studio</span></span>](#tab/visual-studio)

> [!WARNING]
> <span data-ttu-id="551ee-129">O suporte à depuração no Visual Studio está em um estágio inicial do desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="551ee-129">Debugging support in Visual Studio is at an early stage of development.</span></span> <span data-ttu-id="551ee-130">Não há suporte para a depuração **F5** no momento.</span><span class="sxs-lookup"><span data-stu-id="551ee-130">**F5** debugging isn't currently supported.</span></span>

1. <span data-ttu-id="551ee-131">Execute um aplicativo Webassembly mais claro em `Debug` configuração sem depuração (**Ctrl**+**F5** em vez de **F5**).</span><span class="sxs-lookup"><span data-stu-id="551ee-131">Run a Blazor WebAssembly app in `Debug` configuration without debugging (**Ctrl**+**F5** instead of **F5**).</span></span>
1. <span data-ttu-id="551ee-132">Abra as propriedades de depuração do aplicativo (última entrada no menu **depurar** ) e copie a URL do **aplicativo**http.</span><span class="sxs-lookup"><span data-stu-id="551ee-132">Open the Debug properties of the app (last entry in the **Debug** menu) and copy the HTTP **App URL**.</span></span> <span data-ttu-id="551ee-133">Navegue até o endereço HTTP (não o endereço HTTPS) do aplicativo usando um navegador baseado em Chromium (Edge beta ou Chrome).</span><span class="sxs-lookup"><span data-stu-id="551ee-133">Browse to the HTTP address (not the HTTPS address) of the app using a Chromium-based browser (Edge Beta or Chrome).</span></span>
1. <span data-ttu-id="551ee-134">Coloque o foco do teclado no aplicativo na janela do navegador, não no painel Ferramentas do desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="551ee-134">Place the keyboard focus on the app in the browser window, not the developer tools panel.</span></span> <span data-ttu-id="551ee-135">É melhor manter o painel Ferramentas do desenvolvedor fechado para este procedimento.</span><span class="sxs-lookup"><span data-stu-id="551ee-135">It's best to keep the developer tools panel closed for this procedure.</span></span> <span data-ttu-id="551ee-136">Depois que a depuração for iniciada, você poderá abrir novamente o painel Ferramentas de desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="551ee-136">After debugging has started, you can re-open the developer tools panel.</span></span>
1. <span data-ttu-id="551ee-137">Selecione o seguinte atalho de teclado específico do mais alto:</span><span class="sxs-lookup"><span data-stu-id="551ee-137">Select the following Blazor-specific keyboard shortcut:</span></span>

   * <span data-ttu-id="551ee-138">`Shift+Alt+D` no Windows</span><span class="sxs-lookup"><span data-stu-id="551ee-138">`Shift+Alt+D` on Windows</span></span>
   * <span data-ttu-id="551ee-139">`Shift+Cmd+D` no macOS</span><span class="sxs-lookup"><span data-stu-id="551ee-139">`Shift+Cmd+D` on macOS</span></span>

   <span data-ttu-id="551ee-140">Se você receber a **guia não é possível localizar o navegador depurável**, consulte [Habilitar depuração remota](#enable-remote-debugging).</span><span class="sxs-lookup"><span data-stu-id="551ee-140">If you receive the **Unable to find debuggable browser tab**, see [Enable remote debugging](#enable-remote-debugging).</span></span>
   
   <span data-ttu-id="551ee-141">Depois de habilitar a depuração remota:</span><span class="sxs-lookup"><span data-stu-id="551ee-141">After enabling remote debugging:</span></span>
   
   <span data-ttu-id="551ee-142">1 \.</span><span class="sxs-lookup"><span data-stu-id="551ee-142">1\.</span></span> <span data-ttu-id="551ee-143">Uma nova janela do navegador abre.</span><span class="sxs-lookup"><span data-stu-id="551ee-143">A new browser window opens.</span></span> <span data-ttu-id="551ee-144">Feche a janela anterior.</span><span class="sxs-lookup"><span data-stu-id="551ee-144">Close the prior window.</span></span>

   <span data-ttu-id="551ee-145">2 \.</span><span class="sxs-lookup"><span data-stu-id="551ee-145">2\.</span></span> <span data-ttu-id="551ee-146">Coloque o foco do teclado no aplicativo na janela do navegador.</span><span class="sxs-lookup"><span data-stu-id="551ee-146">Place the keyboard focus on the app in the browser window.</span></span>

   <span data-ttu-id="551ee-147">3 \.</span><span class="sxs-lookup"><span data-stu-id="551ee-147">3\.</span></span> <span data-ttu-id="551ee-148">Selecione o atalho de teclado específico do mais novo navegador na janela nova: `Shift+Alt+D` no Windows ou `Shift+Cmd+D` no macOS.</span><span class="sxs-lookup"><span data-stu-id="551ee-148">Select the Blazor-specific keyboard shortcut in the new browser window: `Shift+Alt+D` on Windows or `Shift+Cmd+D` on macOS.</span></span>

   <span data-ttu-id="551ee-149">4 \.</span><span class="sxs-lookup"><span data-stu-id="551ee-149">4\.</span></span> <span data-ttu-id="551ee-150">A guia **devtools** é aberta no navegador.</span><span class="sxs-lookup"><span data-stu-id="551ee-150">The **DevTools** tab opens in the browser.</span></span> <span data-ttu-id="551ee-151">**Selecione novamente a guia do aplicativo na janela do navegador.**</span><span class="sxs-lookup"><span data-stu-id="551ee-151">**Reselect the app's tab in the browser window.**</span></span>

   <span data-ttu-id="551ee-152">Para anexar o aplicativo ao Visual Studio, consulte a seção [anexar ao processo no Visual Studio](#attach-to-process-in-visual-studio) .</span><span class="sxs-lookup"><span data-stu-id="551ee-152">To attach the app to Visual Studio, see the [Attach to process in Visual Studio](#attach-to-process-in-visual-studio) section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="551ee-153">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="551ee-153">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="551ee-154">Execute um aplicativo Webassembly mais incrivelmente na configuração do `Debug` passando a opção `--configuration Debug` para o comando [dotnet execute](/dotnet/core/tools/dotnet-run) : `dotnet run --configuration Debug`.</span><span class="sxs-lookup"><span data-stu-id="551ee-154">Run a Blazor WebAssembly app in `Debug` configuration by passing the `--configuration Debug` option to the [dotnet run](/dotnet/core/tools/dotnet-run) command: `dotnet run --configuration Debug`.</span></span>
1. <span data-ttu-id="551ee-155">Navegue até o aplicativo na URL HTTP mostrada na janela do Shell.</span><span class="sxs-lookup"><span data-stu-id="551ee-155">Navigate to the app at the HTTP URL shown in the shell's window.</span></span>
1. <span data-ttu-id="551ee-156">Coloque o foco do teclado no aplicativo, não no painel Ferramentas do desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="551ee-156">Place the keyboard focus on the app, not the developer tools panel.</span></span> <span data-ttu-id="551ee-157">É melhor manter o painel Ferramentas do desenvolvedor fechado para este procedimento.</span><span class="sxs-lookup"><span data-stu-id="551ee-157">It's best to keep the developer tools panel closed for this procedure.</span></span> <span data-ttu-id="551ee-158">Depois que a depuração for iniciada, você poderá abrir novamente o painel Ferramentas de desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="551ee-158">After debugging has started, you can re-open the developer tools panel.</span></span>
1. <span data-ttu-id="551ee-159">Selecione o seguinte atalho de teclado específico do mais alto:</span><span class="sxs-lookup"><span data-stu-id="551ee-159">Select the following Blazor-specific keyboard shortcut:</span></span>

   * <span data-ttu-id="551ee-160">`Shift+Alt+D` no Windows</span><span class="sxs-lookup"><span data-stu-id="551ee-160">`Shift+Alt+D` on Windows</span></span>
   * <span data-ttu-id="551ee-161">`Shift+Cmd+D` no macOS</span><span class="sxs-lookup"><span data-stu-id="551ee-161">`Shift+Cmd+D` on macOS</span></span>

   <span data-ttu-id="551ee-162">Se você receber a **guia não é possível localizar o navegador depurável**, consulte [Habilitar depuração remota](#enable-remote-debugging).</span><span class="sxs-lookup"><span data-stu-id="551ee-162">If you receive the **Unable to find debuggable browser tab**, see [Enable remote debugging](#enable-remote-debugging).</span></span>
   
   <span data-ttu-id="551ee-163">Depois de habilitar a depuração remota:</span><span class="sxs-lookup"><span data-stu-id="551ee-163">After enabling remote debugging:</span></span>
   
   <span data-ttu-id="551ee-164">1 \.</span><span class="sxs-lookup"><span data-stu-id="551ee-164">1\.</span></span> <span data-ttu-id="551ee-165">Uma nova janela do navegador abre.</span><span class="sxs-lookup"><span data-stu-id="551ee-165">A new browser window opens.</span></span> <span data-ttu-id="551ee-166">Feche a janela anterior.</span><span class="sxs-lookup"><span data-stu-id="551ee-166">Close the prior window.</span></span>

   <span data-ttu-id="551ee-167">2 \.</span><span class="sxs-lookup"><span data-stu-id="551ee-167">2\.</span></span> <span data-ttu-id="551ee-168">Coloque o foco do teclado no aplicativo na janela do navegador, não no painel Ferramentas do desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="551ee-168">Place the keyboard focus on the app in the browser window, not the developer tools panel.</span></span>

   <span data-ttu-id="551ee-169">3 \.</span><span class="sxs-lookup"><span data-stu-id="551ee-169">3\.</span></span> <span data-ttu-id="551ee-170">Selecione o atalho de teclado específico do mais novo navegador na janela nova: `Shift+Alt+D` no Windows ou `Shift+Cmd+D` no macOS.</span><span class="sxs-lookup"><span data-stu-id="551ee-170">Select the Blazor-specific keyboard shortcut in the new browser window: `Shift+Alt+D` on Windows or `Shift+Cmd+D` on macOS.</span></span>

---

## <a name="enable-remote-debugging"></a><span data-ttu-id="551ee-171">Habilitar depuração remota</span><span class="sxs-lookup"><span data-stu-id="551ee-171">Enable remote debugging</span></span>

<span data-ttu-id="551ee-172">Se a depuração remota estiver desabilitada, uma página de erro **não é possível localizar a guia do navegador depurável** será gerada pelo Chrome.</span><span class="sxs-lookup"><span data-stu-id="551ee-172">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated by Chrome.</span></span> <span data-ttu-id="551ee-173">A página de erro contém instruções para executar o Chrome com a porta de depuração aberta para que o proxy de depuração de Blazor possa se conectar ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="551ee-173">The error page contains instructions for running Chrome with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="551ee-174">*Feche todas as instâncias do Chrome* e reinicie o Chrome conforme instruído.</span><span class="sxs-lookup"><span data-stu-id="551ee-174">*Close all Chrome instances* and restart Chrome as instructed.</span></span>

## <a name="debug-the-app"></a><span data-ttu-id="551ee-175">Depurar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="551ee-175">Debug the app</span></span>

<span data-ttu-id="551ee-176">Depois que o Chrome estiver em execução com a depuração remota habilitada, o atalho de teclado de depuração abrirá uma nova guia do depurador. Após alguns instantes, a guia **fontes** mostra uma lista dos assemblies .net no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="551ee-176">Once Chrome is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="551ee-177">Expanda cada assembly e localize os arquivos de origem *. cs*/ *. Razor* disponíveis para depuração.</span><span class="sxs-lookup"><span data-stu-id="551ee-177">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="551ee-178">Defina pontos de interrupção, alterne de volta para a guia do aplicativo e os pontos de interrupção serão atingidos quando o código for executado.</span><span class="sxs-lookup"><span data-stu-id="551ee-178">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="551ee-179">Depois que um ponto de interrupção é atingido, a etapa única (`F10`) pela execução do código ou retomar (`F8`) normalmente.</span><span class="sxs-lookup"><span data-stu-id="551ee-179">After a breakpoint is hit, single-step (`F10`) through the code or resume (`F8`) code execution normally.</span></span>

Blazor<span data-ttu-id="551ee-180"> fornece um proxy de depuração que implementa o [protocolo Chrome devtools](https://chromedevtools.github.io/devtools-protocol/) e aumenta o protocolo com o. Informações específicas de rede.</span><span class="sxs-lookup"><span data-stu-id="551ee-180"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="551ee-181">Quando o atalho de teclado de depuração é pressionado, Blazor aponta para o Chrome DevTools no proxy.</span><span class="sxs-lookup"><span data-stu-id="551ee-181">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="551ee-182">O proxy se conecta à janela do navegador que você está procurando depurar (portanto, a necessidade de habilitar a depuração remota).</span><span class="sxs-lookup"><span data-stu-id="551ee-182">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="attach-to-process-in-visual-studio"></a><span data-ttu-id="551ee-183">Anexar ao processo no Visual Studio</span><span class="sxs-lookup"><span data-stu-id="551ee-183">Attach to process in Visual Studio</span></span>

<span data-ttu-id="551ee-184">Anexar ao processo do aplicativo no Visual Studio é um cenário de depuração *temporário* para Blazor Webassembly enquanto a depuração **F5** está em desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="551ee-184">Attaching to the app's process in Visual Studio is a *temporary* debugging scenario for Blazor WebAssembly while **F5** debugging is in development.</span></span>

<span data-ttu-id="551ee-185">Para anexar o processo do aplicativo em execução ao Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="551ee-185">To attach the running app's process to Visual Studio:</span></span>

1. <span data-ttu-id="551ee-186">No Visual Studio, selecione **depurar** > **anexar ao processo**.</span><span class="sxs-lookup"><span data-stu-id="551ee-186">In Visual Studio, select **Debug** > **Attach to Process**.</span></span>
1. <span data-ttu-id="551ee-187">Para o **tipo de conexão**, selecione **WebSocket do protocolo Chrome devtools (sem autenticação)** .</span><span class="sxs-lookup"><span data-stu-id="551ee-187">For the **Connection type**, select **Chrome devtools protocol websocket (no authentication)**.</span></span>
1. <span data-ttu-id="551ee-188">Para o **destino de conexão**, Cole o endereço http (não o endereço https) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="551ee-188">For the **Connection target**, paste in the HTTP address (not the HTTPS address) of the app.</span></span>
1. <span data-ttu-id="551ee-189">Selecione **Atualizar** para atualizar as entradas em **processos disponíveis**.</span><span class="sxs-lookup"><span data-stu-id="551ee-189">Select **Refresh** to refresh the entries under **Available processes**.</span></span>
1. <span data-ttu-id="551ee-190">Selecione o processo do navegador a ser depurado e selecione **anexar**.</span><span class="sxs-lookup"><span data-stu-id="551ee-190">Select the browser process to debug and select **Attach**.</span></span>
1. <span data-ttu-id="551ee-191">Na caixa de diálogo **Selecionar tipo de código** , selecione o tipo de código para o navegador específico ao qual você está anexando (Edge ou Chrome) e, em seguida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="551ee-191">In the **Select Code Type** dialog, select the code type for the specific browser you're attaching to (Edge or Chrome) and then select **OK**.</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="551ee-192">Mapas de origem do navegador</span><span class="sxs-lookup"><span data-stu-id="551ee-192">Browser source maps</span></span>

<span data-ttu-id="551ee-193">Os mapas de origem do navegador permitem que o navegador mapeie arquivos compilados de volta para seus arquivos de origem originais e normalmente são usados para depuração do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="551ee-193">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="551ee-194">No entanto, Blazor atualmente C# não mapeia diretamente para JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="551ee-194">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="551ee-195">Em vez disso, Blazor a interpretação de IL no navegador, portanto, os mapas de origem não são relevantes.</span><span class="sxs-lookup"><span data-stu-id="551ee-195">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="551ee-196">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="551ee-196">Troubleshoot</span></span>

<span data-ttu-id="551ee-197">Se você estiver executando erros, a dica a seguir pode ajudar:</span><span class="sxs-lookup"><span data-stu-id="551ee-197">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="551ee-198">Na guia **depurador** , abra as ferramentas de desenvolvedor em seu navegador.</span><span class="sxs-lookup"><span data-stu-id="551ee-198">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="551ee-199">No console do, execute `localStorage.clear()` para remover qualquer ponto de interrupção.</span><span class="sxs-lookup"><span data-stu-id="551ee-199">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
