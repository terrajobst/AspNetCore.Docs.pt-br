---
title: Depurar ASP.NET Core Blazor Webassembly
author: guardrex
description: Saiba como depurar Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 5dbc900ab68682068a7f9e3ffdaabef89a0c7798
ms.sourcegitcommit: 6ffb583991d6689326605a24565130083a28ef85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80306486"
---
# <a name="debug-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="5e8f4-103">Depurar ASP.NET Core Blazor Webassembly</span><span class="sxs-lookup"><span data-stu-id="5e8f4-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="5e8f4-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="5e8f4-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="5e8f4-105"> aplicativos Webassembly podem ser depurados usando as ferramentas de desenvolvimento do navegador em navegadores baseados em Chromium (borda/Chrome).</span><span class="sxs-lookup"><span data-stu-id="5e8f4-105"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span>  <span data-ttu-id="5e8f4-106">Como alternativa, você pode depurar seu aplicativo usando o Visual Studio ou Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-106">Alternatively you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="5e8f4-107">Os cenários disponíveis incluem:</span><span class="sxs-lookup"><span data-stu-id="5e8f4-107">Available scenarios include:</span></span>

* <span data-ttu-id="5e8f4-108">Definir e remover pontos de interrupção.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="5e8f4-109">Execute o aplicativo com suporte para depuração no Visual Studio e Visual Studio Code (suporte a<kbd>F5</kbd> ).</span><span class="sxs-lookup"><span data-stu-id="5e8f4-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="5e8f4-110">Etapa única (<kbd>F10</kbd>) por meio do código.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="5e8f4-111">Retome a execução de código com <kbd>F8</kbd> em um navegador ou <kbd>F5</kbd> no Visual Studio ou Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="5e8f4-112">Na exibição *locais* , observe os valores de variáveis locais.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="5e8f4-113">Consulte a pilha de chamadas, incluindo cadeias de chamada que vão do JavaScript para o .NET e do .NET para o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="5e8f4-114">Por enquanto, você *não pode*:</span><span class="sxs-lookup"><span data-stu-id="5e8f4-114">For now, you *can't*:</span></span>

* <span data-ttu-id="5e8f4-115">Inspecione as matrizes.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-115">Inspect arrays.</span></span>
* <span data-ttu-id="5e8f4-116">Focalize para inspecionar Membros.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-116">Hover to inspect members.</span></span>
* <span data-ttu-id="5e8f4-117">Depuração de etapa dentro ou fora de código gerenciado.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-117">Step debug into or out of managed code.</span></span>
* <span data-ttu-id="5e8f4-118">Ter suporte completo para inspecionar tipos de valor.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-118">Have full support for inspecting value types.</span></span>
* <span data-ttu-id="5e8f4-119">Interromper em exceções sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-119">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="5e8f4-120">Pontos de interrupção de acesso durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-120">Hit breakpoints during app startup.</span></span>
* <span data-ttu-id="5e8f4-121">Depurar um aplicativo com um trabalho de serviço.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-121">Debug an app with a service worker.</span></span>

<span data-ttu-id="5e8f4-122">Continuaremos a melhorar a experiência de depuração em versões futuras.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5e8f4-123">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="5e8f4-123">Prerequisites</span></span>

<span data-ttu-id="5e8f4-124">A depuração requer um dos seguintes navegadores:</span><span class="sxs-lookup"><span data-stu-id="5e8f4-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="5e8f4-125">Microsoft Edge (versão 80 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="5e8f4-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="5e8f4-126">Google Chrome (versão 70 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="5e8f4-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="5e8f4-127">Habilitar depuração para Visual Studio e Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5e8f4-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="5e8f4-128">A depuração é habilitada automaticamente para novos projetos que são criados usando o modelo de projeto do ASP.NET Core 3,2 Preview 3 ou posterior Blazor Webassembly.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-128">Debugging is enabled automatically for new projects that are created using the ASP.NET Core 3.2 Preview 3 or later Blazor WebAssembly project template.</span></span>

<span data-ttu-id="5e8f4-129">Para habilitar a depuração para um aplicativo Webassembly Blazor existente, atualize o arquivo *launchSettings. JSON* no projeto de inicialização para incluir a seguinte Propriedade `inspectUri` em cada perfil de inicialização:</span><span class="sxs-lookup"><span data-stu-id="5e8f4-129">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="5e8f4-130">Depois de atualizado, o arquivo *launchSettings. JSON* deve ser semelhante ao exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="5e8f4-130">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="5e8f4-131">A propriedade `inspectUri`:</span><span class="sxs-lookup"><span data-stu-id="5e8f4-131">The `inspectUri` property:</span></span>

* <span data-ttu-id="5e8f4-132">Permite que o IDE detecte que o aplicativo é um aplicativo Webassembly Blazor.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-132">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="5e8f4-133">Instrui a infraestrutura de depuração de script para se conectar ao navegador por meio do proxy de depuração do Blazor.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-133">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="5e8f4-134">{1&gt;Visual Studio&lt;1}</span><span class="sxs-lookup"><span data-stu-id="5e8f4-134">Visual Studio</span></span>

<span data-ttu-id="5e8f4-135">Para depurar um aplicativo Webassembly Blazor no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="5e8f4-135">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="5e8f4-136">Verifique se você [instalou a versão de visualização mais recente do Visual Studio 2019 16,6](https://visualstudio.com/preview) (visualização 2 ou posterior).</span><span class="sxs-lookup"><span data-stu-id="5e8f4-136">Ensure you have [installed the latest preview release of Visual Studio 2019 16.6](https://visualstudio.com/preview) (Preview 2 or later).</span></span>
1. <span data-ttu-id="5e8f4-137">Crie um novo aplicativo ASP.NET Core hospedado Blazor Webassembly.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-137">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="5e8f4-138">Pressione <kbd>F5</kbd> para executar o aplicativo no depurador.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-138">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="5e8f4-139">Defina um ponto de interrupção no *Counter. Razor* no método `IncrementCount`.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-139">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="5e8f4-140">Navegue até a guia **contador** e selecione o botão para atingir o ponto de interrupção:</span><span class="sxs-lookup"><span data-stu-id="5e8f4-140">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Contador de depuração](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="5e8f4-142">Confira o valor do campo `currentCount` na janela locais:</span><span class="sxs-lookup"><span data-stu-id="5e8f4-142">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Exibir locais](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="5e8f4-144">Pressione <kbd>F5</kbd> para continuar a execução.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-144">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="5e8f4-145">Ao depurar seu aplicativo Webassembly Blazor, você também pode depurar o código do servidor:</span><span class="sxs-lookup"><span data-stu-id="5e8f4-145">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="5e8f4-146">Defina um ponto de interrupção na página *FetchData. Razor* em `OnInitializedAsync`.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-146">Set a breakpoint in the *FetchData.razor* page in `OnInitializedAsync`.</span></span>
1. <span data-ttu-id="5e8f4-147">Defina um ponto de interrupção no `WeatherForecastController` no método de ação `Get`.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-147">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="5e8f4-148">Navegue até a guia **buscar dados** para atingir o primeiro ponto de interrupção no componente `FetchData` pouco antes de emitir uma solicitação HTTP para o servidor:</span><span class="sxs-lookup"><span data-stu-id="5e8f4-148">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Depurar dados de busca](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="5e8f4-150">Pressione <kbd>F5</kbd> para continuar a execução e, em seguida, pressione o ponto de interrupção no servidor no `WeatherForecastController`:</span><span class="sxs-lookup"><span data-stu-id="5e8f4-150">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Servidor de depuração](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="5e8f4-152">Pressione <kbd>F5</kbd> novamente para permitir que a execução continue e veja a tabela previsão do tempo processada.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-152">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

## <a name="visual-studio-code"></a><span data-ttu-id="5e8f4-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5e8f4-153">Visual Studio Code</span></span>

<span data-ttu-id="5e8f4-154">Para depurar um aplicativo Webassembly Blazor no Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="5e8f4-154">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="5e8f4-155">Instale a [ C# extensão](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e a extensão do [depurador do JavaScript (noturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) com `debug.javascript.usePreview` definido como `true`.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-155">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![Extensões](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![Depurador de visualização JS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="5e8f4-158">Abra um aplicativo Webassembly Blazor existente com a depuração habilitada.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-158">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="5e8f4-159">Se você receber a notificação a seguir que a configuração adicional é necessária para habilitar a depuração, confirme se você tem as extensões corretas instaladas e a depuração de visualização do JavaScript habilitada e recarregue a janela:</span><span class="sxs-lookup"><span data-stu-id="5e8f4-159">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![Configuração adicional necessária](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="5e8f4-161">Uma notificação oferece para adicionar os ativos necessários ao aplicativo para criação e depuração.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-161">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="5e8f4-162">Selecione **Sim**:</span><span class="sxs-lookup"><span data-stu-id="5e8f4-162">Select **Yes**:</span></span>

     ![Adicionar ativos necessários](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="5e8f4-164">Iniciar o aplicativo no depurador é um processo de duas etapas:</span><span class="sxs-lookup"><span data-stu-id="5e8f4-164">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="5e8f4-165">1 \.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-165">1\.</span></span> <span data-ttu-id="5e8f4-166">**Primeiro**, inicie o aplicativo usando a configuração de inicialização do **.net Core (Blazor autônomo)** .</span><span class="sxs-lookup"><span data-stu-id="5e8f4-166">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="5e8f4-167">2 \.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-167">2\.</span></span> <span data-ttu-id="5e8f4-168">**Depois que o aplicativo for iniciado**, inicie o navegador usando o **assembly da Web do Blazor de depuração do .NET Core na** configuração de inicialização do Chrome (requer o Chrome).</span><span class="sxs-lookup"><span data-stu-id="5e8f4-168">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="5e8f4-169">Para usar o Edge em vez do Chrome, altere o `type` da configuração de inicialização em *. vscode/Launch. JSON* de `pwa-chrome` para `pwa-edge`.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-169">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-edge`.</span></span>

1. <span data-ttu-id="5e8f4-170">Defina um ponto de interrupção no método `IncrementCount` no componente `Counter` e, em seguida, selecione o botão para atingir o ponto de interrupção:</span><span class="sxs-lookup"><span data-stu-id="5e8f4-170">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Contador de depuração no VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="5e8f4-172">Depurar no navegador</span><span class="sxs-lookup"><span data-stu-id="5e8f4-172">Debug in the browser</span></span>

1. <span data-ttu-id="5e8f4-173">Execute uma compilação de depuração do aplicativo no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-173">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="5e8f4-174">Pressione <kbd>Shift</kbd>+<kbd>ALT</kbd>+<kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-174">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="5e8f4-175">O navegador deve ser executado com a depuração remota habilitada.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-175">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="5e8f4-176">Se a depuração remota estiver desabilitada, uma página de erro **não é possível localizar a guia do navegador depurável** será gerada.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-176">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="5e8f4-177">A página de erro contém instruções para executar o navegador com a porta de depuração aberta para que o proxy de depuração de Blazor possa se conectar ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-177">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="5e8f4-178">*Feche todas as instâncias do navegador* e reinicie o navegador conforme instruído.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-178">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="5e8f4-179">Depois que o navegador estiver em execução com a depuração remota habilitada, o atalho de teclado de depuração abrirá uma nova guia do depurador. Após alguns instantes, a guia **fontes** mostra uma lista dos assemblies .net no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-179">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="5e8f4-180">Expanda cada assembly e localize os arquivos de origem *. cs*/ *. Razor* disponíveis para depuração.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-180">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="5e8f4-181">Defina pontos de interrupção, alterne de volta para a guia do aplicativo e os pontos de interrupção serão atingidos quando o código for executado.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-181">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="5e8f4-182">Depois que um ponto de interrupção é atingido, a etapa única (<kbd>F10</kbd>) por meio da execução do código ou retomar (<kbd>F8</kbd>) normalmente.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-182">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="5e8f4-183"> fornece um proxy de depuração que implementa o [protocolo Chrome devtools](https://chromedevtools.github.io/devtools-protocol/) e aumenta o protocolo com o. Informações específicas de rede.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-183"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="5e8f4-184">Quando o atalho de teclado de depuração é pressionado, Blazor aponta para o Chrome DevTools no proxy.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-184">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="5e8f4-185">O proxy se conecta à janela do navegador que você está procurando depurar (portanto, a necessidade de habilitar a depuração remota).</span><span class="sxs-lookup"><span data-stu-id="5e8f4-185">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="5e8f4-186">Mapas de origem do navegador</span><span class="sxs-lookup"><span data-stu-id="5e8f4-186">Browser source maps</span></span>

<span data-ttu-id="5e8f4-187">Os mapas de origem do navegador permitem que o navegador mapeie arquivos compilados de volta para seus arquivos de origem originais e normalmente são usados para depuração do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-187">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="5e8f4-188">No entanto, Blazor atualmente C# não mapeia diretamente para JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-188">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="5e8f4-189">Em vez disso, Blazor a interpretação de IL no navegador, portanto, os mapas de origem não são relevantes.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-189">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="5e8f4-190">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="5e8f4-190">Troubleshoot</span></span>

<span data-ttu-id="5e8f4-191">Se você estiver executando erros, a dica a seguir pode ajudar:</span><span class="sxs-lookup"><span data-stu-id="5e8f4-191">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="5e8f4-192">Na guia **depurador** , abra as ferramentas de desenvolvedor em seu navegador.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-192">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="5e8f4-193">No console do, execute `localStorage.clear()` para remover qualquer ponto de interrupção.</span><span class="sxs-lookup"><span data-stu-id="5e8f4-193">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
