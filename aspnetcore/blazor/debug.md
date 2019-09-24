---
title: Depurar ASP.NET Core mais incrivelmente
author: guardrex
description: Saiba como depurar aplicativos mais Incrivelmenteos.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/debug
ms.openlocfilehash: 3519479d8058f013de23cc9cfa0f5574cd158053
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71207201"
---
# <a name="debug-aspnet-core-blazor"></a><span data-ttu-id="b6bbb-103">Depurar ASP.NET Core mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="b6bbb-103">Debug ASP.NET Core Blazor</span></span>

[<span data-ttu-id="b6bbb-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="b6bbb-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="b6bbb-105">Há suporte *antecipado* para depuração de aplicativos Webassembly mais recentes em execução no Webassembly no Chrome.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-105">*Early* support exists for debugging Blazor WebAssembly apps running on WebAssembly in Chrome.</span></span>

<span data-ttu-id="b6bbb-106">Os recursos do depurador são limitados.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-106">Debugger capabilities are limited.</span></span> <span data-ttu-id="b6bbb-107">Os cenários disponíveis incluem:</span><span class="sxs-lookup"><span data-stu-id="b6bbb-107">Available scenarios include:</span></span>

* <span data-ttu-id="b6bbb-108">Definir e remover pontos de interrupção.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="b6bbb-109">Etapa única (`F10`) por meio da execução do código ou`F8`retomar ().</span><span class="sxs-lookup"><span data-stu-id="b6bbb-109">Single-step (`F10`) through the code or resume (`F8`) code execution.</span></span>
* <span data-ttu-id="b6bbb-110">Na exibição *locais* , observe os valores de qualquer variável local do tipo `int`, `string`e `bool`.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-110">In the *Locals* display, observe the values of any local variables of type `int`, `string`, and `bool`.</span></span>
* <span data-ttu-id="b6bbb-111">Consulte a pilha de chamadas, incluindo cadeias de chamada que vão do JavaScript para o .NET e do .NET para o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-111">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="b6bbb-112">Você *não pode*:</span><span class="sxs-lookup"><span data-stu-id="b6bbb-112">You *can't*:</span></span>

* <span data-ttu-id="b6bbb-113">Observe os valores de quaisquer locais que não sejam `int`um `string`, ou `bool`.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-113">Observe the values of any locals that aren't an `int`, `string`, or `bool`.</span></span>
* <span data-ttu-id="b6bbb-114">Observe os valores de quaisquer propriedades ou campos de classe.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-114">Observe the values of any class properties or fields.</span></span>
* <span data-ttu-id="b6bbb-115">Passe o mouse sobre variáveis para ver seus valores.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-115">Hover over variables to see their values.</span></span>
* <span data-ttu-id="b6bbb-116">Avalie as expressões no console.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-116">Evaluate expressions in the console.</span></span>
* <span data-ttu-id="b6bbb-117">Passe pelas chamadas assíncronas.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-117">Step across async calls.</span></span>
* <span data-ttu-id="b6bbb-118">Execute a maioria dos outros cenários de depuração comuns.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-118">Perform most other ordinary debugging scenarios.</span></span>

<span data-ttu-id="b6bbb-119">O desenvolvimento de mais cenários de depuração é um foco contínuo da equipe de engenharia.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-119">Development of further debugging scenarios is an on-going focus of the engineering team.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b6bbb-120">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="b6bbb-120">Prerequisites</span></span>

<span data-ttu-id="b6bbb-121">A depuração requer um dos seguintes navegadores:</span><span class="sxs-lookup"><span data-stu-id="b6bbb-121">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="b6bbb-122">Google Chrome (versão 70 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="b6bbb-122">Google Chrome (version 70 or later)</span></span>
* <span data-ttu-id="b6bbb-123">Visualização do Microsoft Edge ([canal de desenvolvimento de borda](https://www.microsoftedgeinsider.com))</span><span class="sxs-lookup"><span data-stu-id="b6bbb-123">Microsoft Edge preview ([Edge Dev Channel](https://www.microsoftedgeinsider.com))</span></span>

## <a name="procedure"></a><span data-ttu-id="b6bbb-124">Procedimento</span><span class="sxs-lookup"><span data-stu-id="b6bbb-124">Procedure</span></span>

1. <span data-ttu-id="b6bbb-125">Execute um aplicativo Webassembly mais incrivelmente na `Debug` configuração.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-125">Run a Blazor WebAssembly app in `Debug` configuration.</span></span> <span data-ttu-id="b6bbb-126">Passe a `--configuration Debug` opção para o comando [dotnet Run](/dotnet/core/tools/dotnet-run) : `dotnet run --configuration Debug`.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-126">Pass the `--configuration Debug` option to the [dotnet run](/dotnet/core/tools/dotnet-run) command: `dotnet run --configuration Debug`.</span></span>
1. <span data-ttu-id="b6bbb-127">Acesse o aplicativo no navegador.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-127">Access the app in the browser.</span></span>
1. <span data-ttu-id="b6bbb-128">Coloque o foco do teclado no aplicativo, não no painel Ferramentas do desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-128">Place the keyboard focus on the app, not the developer tools panel.</span></span> <span data-ttu-id="b6bbb-129">O painel Ferramentas do desenvolvedor pode ser fechado quando a depuração é iniciada.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-129">The developer tools panel can be closed when debugging is initiated.</span></span>
1. <span data-ttu-id="b6bbb-130">Selecione o seguinte atalho de teclado específico do mais alto:</span><span class="sxs-lookup"><span data-stu-id="b6bbb-130">Select the following Blazor-specific keyboard shortcut:</span></span>
   * <span data-ttu-id="b6bbb-131">`Shift+Alt+D`no Windows/Linux</span><span class="sxs-lookup"><span data-stu-id="b6bbb-131">`Shift+Alt+D` on Windows/Linux</span></span>
   * <span data-ttu-id="b6bbb-132">`Shift+Cmd+D`no macOS</span><span class="sxs-lookup"><span data-stu-id="b6bbb-132">`Shift+Cmd+D` on macOS</span></span>
1. <span data-ttu-id="b6bbb-133">Siga as etapas listadas na tela para reiniciar o navegador com a depuração remota habilitada.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-133">Follow the steps listed on the screen to restart the browser with remote debugging enabled.</span></span>
1. <span data-ttu-id="b6bbb-134">Selecione o seguinte atalho de teclado específico para o mais novo para iniciar a sessão de depuração:</span><span class="sxs-lookup"><span data-stu-id="b6bbb-134">Select the following Blazor-specific keyboard shortcut once again to start the debug session:</span></span>
   * <span data-ttu-id="b6bbb-135">`Shift+Alt+D`no Windows/Linux</span><span class="sxs-lookup"><span data-stu-id="b6bbb-135">`Shift+Alt+D` on Windows/Linux</span></span>
   * <span data-ttu-id="b6bbb-136">`Shift+Cmd+D`no macOS</span><span class="sxs-lookup"><span data-stu-id="b6bbb-136">`Shift+Cmd+D` on macOS</span></span>

## <a name="enable-remote-debugging"></a><span data-ttu-id="b6bbb-137">Habilitar depuração remota</span><span class="sxs-lookup"><span data-stu-id="b6bbb-137">Enable remote debugging</span></span>

<span data-ttu-id="b6bbb-138">Se a depuração remota estiver desabilitada, uma página de erro **não é possível localizar a guia do navegador depurável** será gerada pelo Chrome.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-138">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated by Chrome.</span></span> <span data-ttu-id="b6bbb-139">A página de erro contém instruções para executar o Chrome com a porta de depuração aberta para que o proxy de depuração mais incrivelmente possa se conectar ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-139">The error page contains instructions for running Chrome with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="b6bbb-140">*Feche todas as instâncias do Chrome* e reinicie o Chrome conforme instruído.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-140">*Close all Chrome instances* and restart Chrome as instructed.</span></span>

## <a name="debug-the-app"></a><span data-ttu-id="b6bbb-141">Depurar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="b6bbb-141">Debug the app</span></span>

<span data-ttu-id="b6bbb-142">Depois que o Chrome estiver em execução com a depuração remota habilitada, o atalho de teclado de depuração abrirá uma nova guia do depurador. Após alguns instantes, a guia **fontes** mostra uma lista dos assemblies .net no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-142">Once Chrome is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="b6bbb-143">Expanda cada assembly e localize os arquivos de origem *. cs*/ *. Razor* disponíveis para depuração.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-143">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="b6bbb-144">Defina pontos de interrupção, alterne de volta para a guia do aplicativo e os pontos de interrupção serão atingidos quando o código for executado.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-144">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="b6bbb-145">Depois que um ponto de interrupção é atingido, etapa`F10`única () por meio da execução`F8`do código ou retomar () normalmente.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-145">After a breakpoint is hit, single-step (`F10`) through the code or resume (`F8`) code execution normally.</span></span>

<span data-ttu-id="b6bbb-146">O mais incrivelmente fornece um proxy de depuração que implementa o [protocolo Chrome devtools](https://chromedevtools.github.io/devtools-protocol/) e aumenta o protocolo com o. Informações específicas de rede.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-146">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="b6bbb-147">Quando o atalho de teclado de depuração é pressionado, o mais um é apontado para o Chrome DevTools no proxy.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-147">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="b6bbb-148">O proxy se conecta à janela do navegador que você está procurando depurar (portanto, a necessidade de habilitar a depuração remota).</span><span class="sxs-lookup"><span data-stu-id="b6bbb-148">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="b6bbb-149">Mapas de origem do navegador</span><span class="sxs-lookup"><span data-stu-id="b6bbb-149">Browser source maps</span></span>

<span data-ttu-id="b6bbb-150">Os mapas de origem do navegador permitem que o navegador mapeie arquivos compilados de volta para seus arquivos de origem originais e normalmente são usados para depuração do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-150">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="b6bbb-151">No entanto, o mais incrivelmente C# não mapeia diretamente para JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-151">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="b6bbb-152">Em vez disso, o mais importante é a interpretação de IL no navegador, portanto, os mapas de origem não são relevantes.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-152">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshooting-tip"></a><span data-ttu-id="b6bbb-153">Dica de solução de problemas</span><span class="sxs-lookup"><span data-stu-id="b6bbb-153">Troubleshooting tip</span></span>

<span data-ttu-id="b6bbb-154">Se você estiver executando erros, a dica a seguir pode ajudar:</span><span class="sxs-lookup"><span data-stu-id="b6bbb-154">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="b6bbb-155">Na guia **depurador** , abra as ferramentas de desenvolvedor em seu navegador.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-155">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="b6bbb-156">No console do, execute `localStorage.clear()` para remover qualquer ponto de interrupção.</span><span class="sxs-lookup"><span data-stu-id="b6bbb-156">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
