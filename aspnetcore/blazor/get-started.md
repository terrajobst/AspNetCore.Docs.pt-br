---
title: Introdução ao ASP.NET Core Blazor
author: guardrex
description: Comece a usar o Blazor criando um aplicativo Blazor com as ferramentas de sua escolha.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/10/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 89c7529d2b8ec97db731f7c7268e19937c398115
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083243"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="2b2a4-103">Introdução ao ASP.NET Core mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="2b2a4-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="2b2a4-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2b2a4-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="2b2a4-105">Introdução ao mais incrivelmente:</span><span class="sxs-lookup"><span data-stu-id="2b2a4-105">Get started with Blazor:</span></span>

1. <span data-ttu-id="2b2a4-106">Instale o [SDK do .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="2b2a4-106">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="2b2a4-107">Opcionalmente, instale o modelo [Webassembly mais incrivelmente](xref:blazor/hosting-models#blazor-webassembly) :</span><span class="sxs-lookup"><span data-stu-id="2b2a4-107">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="2b2a4-108">Instale o [SDK do .NET Core 3.1.102 ou posterior (versão prévia)](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="2b2a4-108">Install the [.NET Core 3.1.102 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="2b2a4-109">Execute o comando a seguir em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-109">Run the following command in a command shell.</span></span> <span data-ttu-id="2b2a4-110">O pacote [Microsoft. AspNetCore. Components. Webassembly. templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) tem uma versão de visualização enquanto o Webassembly de mais de baixo está em visualização.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-110">The [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview2.20160.5
   ```

   > [!NOTE]
   > <span data-ttu-id="2b2a4-111">SDK do .NET Core versão 3.1.102 ou posterior é **necessária** para usar o modelo Webassembly do 3,2 Preview 2 mais recente.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-111">.NET Core SDK version 3.1.102 or later is **required** to use the 3.2 Preview 2 Blazor WebAssembly template.</span></span> <span data-ttu-id="2b2a4-112">Confirme a versão do SDK do .NET Core instalada executando `dotnet --version` em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-112">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="2b2a4-113">Siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="2b2a4-113">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studio"></a>[<span data-ttu-id="2b2a4-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2b2a4-114">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="2b2a4-115">1 \.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-115">1\.</span></span> <span data-ttu-id="2b2a4-116">Instale o [Visual Studio 2019 versão 16,4 ou posterior](https://visualstudio.microsoft.com/vs/preview/) com a ASP.net e a carga de trabalho de **desenvolvimento Web** .</span><span class="sxs-lookup"><span data-stu-id="2b2a4-116">Install [Visual Studio 2019 version 16.4 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="2b2a4-117">2 \.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-117">2\.</span></span> <span data-ttu-id="2b2a4-118">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-118">Create a new project.</span></span>

   <span data-ttu-id="2b2a4-119">3 \.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-119">3\.</span></span> <span data-ttu-id="2b2a4-120">Selecione **aplicativo mais incrivelmente**.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-120">Select **Blazor App**.</span></span> <span data-ttu-id="2b2a4-121">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-121">Select **Next**.</span></span>

   <span data-ttu-id="2b2a4-122">4 \.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-122">4\.</span></span> <span data-ttu-id="2b2a4-123">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-123">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="2b2a4-124">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-124">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="2b2a4-125">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-125">Select **Create**.</span></span>

   <span data-ttu-id="2b2a4-126">5 \.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-126">5\.</span></span> <span data-ttu-id="2b2a4-127">Para obter uma experiência de Webassembly mais experiente, escolha o modelo de **aplicativo Webassembly mais incrivelmente** .</span><span class="sxs-lookup"><span data-stu-id="2b2a4-127">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="2b2a4-128">Para uma experiência de servidor mais incrivelmente, escolha o modelo de **aplicativo de servidor** mais experiente.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-128">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="2b2a4-129">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-129">Select **Create**.</span></span> <span data-ttu-id="2b2a4-130">Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, um *servidor mais incrivelmente* e um *Webassembly*de mais ou mais, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-130">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span> <span data-ttu-id="2b2a4-131">Se o modelo Webassembly mais antigo não estiver presente, retorne à etapa anterior e reinstale o modelo.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-131">If the Blazor WebAssembly template isn't present, return to the previous step and reinstall the template.</span></span>

   <span data-ttu-id="2b2a4-132">6 \.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-132">6\.</span></span> <span data-ttu-id="2b2a4-133">Pressione **Ctrl**+**F5** para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-133">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="2b2a4-134">Se você instalou a extensão do Visual Studio para uma versão prévia anterior do ASP.NET Core mais recente (visualização 6 ou anterior), você pode desinstalar a extensão.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-134">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="2b2a4-135">Instalar os modelos mais bem em um shell de comando agora é suficiente para trazer os modelos no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-135">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-code"></a>[<span data-ttu-id="2b2a4-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2b2a4-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="2b2a4-137">1 \.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-137">1\.</span></span> <span data-ttu-id="2b2a4-138">Instale o [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="2b2a4-138">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="2b2a4-139">2 \.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-139">2\.</span></span> <span data-ttu-id="2b2a4-140">Instale o mais recente [ C# para a extensão de Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span><span class="sxs-lookup"><span data-stu-id="2b2a4-140">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

   <span data-ttu-id="2b2a4-141">3 \.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-141">3\.</span></span> <span data-ttu-id="2b2a4-142">Para uma experiência de Webassembly mais experiente, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="2b2a4-142">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="2b2a4-143">Para uma experiência de servidor mais incrivelmente, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="2b2a4-143">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="2b2a4-144">Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, um *servidor mais incrivelmente* e um *Webassembly*de mais ou mais, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-144">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="2b2a4-145">4 \.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-145">4\.</span></span> <span data-ttu-id="2b2a4-146">Abra a pasta *WebApplication1* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-146">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="2b2a4-147">5 \.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-147">5\.</span></span> <span data-ttu-id="2b2a4-148">Para um projeto de servidor mais incrivelmente, o IDE solicita que você adicione ativos para compilar e depurar o projeto.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-148">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="2b2a4-149">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-149">Select **Yes**.</span></span>

   <span data-ttu-id="2b2a4-150">6 \.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-150">6\.</span></span> <span data-ttu-id="2b2a4-151">Se estiver usando um aplicativo de servidor mais incrivelmente, execute o aplicativo usando o depurador de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-151">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="2b2a4-152">Se estiver usando um aplicativo Webassembly mais incrivelmente, execute `dotnet run` na pasta do projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-152">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="2b2a4-153">7 \.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-153">7\.</span></span> <span data-ttu-id="2b2a4-154">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-154">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2b2a4-155">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="2b2a4-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="2b2a4-156">1 \.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-156">1\.</span></span> <span data-ttu-id="2b2a4-157">Instale o [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="2b2a4-157">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

   <span data-ttu-id="2b2a4-158">2 \.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-158">2\.</span></span> <span data-ttu-id="2b2a4-159">Selecione **arquivo** > **nova solução** ou crie um **novo projeto**.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-159">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="2b2a4-160">3 \.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-160">3\.</span></span> <span data-ttu-id="2b2a4-161">Na barra lateral, selecione **.NET Core** > **aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-161">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="2b2a4-162">4 \.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-162">4\.</span></span> <span data-ttu-id="2b2a4-163">Selecione o modelo de **aplicativo de servidor mais incrivelmente** .</span><span class="sxs-lookup"><span data-stu-id="2b2a4-163">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="2b2a4-164">Somente o modelo de servidor mais útil está disponível no Visual Studio para Mac no momento.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-164">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="2b2a4-165">Para obter uma experiência de Webassembly mais experiente, siga as instruções na guia **CLI do .NET Core** . Depois de selecionar o modelo de servidor mais novo, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-165">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="2b2a4-166">Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, um *servidor mais incrivelmente* e um *Webassembly*de mais ou mais, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-166">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="2b2a4-167">5 \.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-167">5\.</span></span> <span data-ttu-id="2b2a4-168">Defina a **estrutura de destino** como **.NET Core 3,1** e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-168">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

   <span data-ttu-id="2b2a4-169">6 \.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-169">6\.</span></span> <span data-ttu-id="2b2a4-170">No campo **nome do projeto** , nomeie o aplicativo `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-170">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="2b2a4-171">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-171">Select **Create**.</span></span>

   <span data-ttu-id="2b2a4-172">7 \.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-172">7\.</span></span> <span data-ttu-id="2b2a4-173">Selecione **executar** > **executar sem depuração** para executar o aplicativo *sem o depurador*.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-173">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="2b2a4-174">Execute o aplicativo com **Iniciar Depuração** para executar o aplicativo *com o depurador*.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-174">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

   <span data-ttu-id="2b2a4-175">Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-175">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

   # <a name="net-core-cli"></a>[<span data-ttu-id="2b2a4-176">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="2b2a4-176">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="2b2a4-177">Para uma experiência de Webassembly mais experiente, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="2b2a4-177">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="2b2a4-178">Para uma experiência de servidor mais incrivelmente, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="2b2a4-178">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="2b2a4-179">Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, um *servidor mais incrivelmente* e um *Webassembly*de mais ou mais, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-179">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="2b2a4-180">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-180">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

<span data-ttu-id="2b2a4-181">Várias páginas estão disponíveis em guias na barra lateral:</span><span class="sxs-lookup"><span data-stu-id="2b2a4-181">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="2b2a4-182">Home</span><span class="sxs-lookup"><span data-stu-id="2b2a4-182">Home</span></span>
* <span data-ttu-id="2b2a4-183">Contador</span><span class="sxs-lookup"><span data-stu-id="2b2a4-183">Counter</span></span>
* <span data-ttu-id="2b2a4-184">Buscar dados</span><span class="sxs-lookup"><span data-stu-id="2b2a4-184">Fetch data</span></span>

<span data-ttu-id="2b2a4-185">Na página Contador, selecione o botão **Clique aqui** para incrementar o contador sem uma atualização de página.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-185">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="2b2a4-186">Incrementar um contador em uma página da Web normalmente requer a escrita de JavaScript, mas com Blazor C#você pode usar.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-186">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="2b2a4-187">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="2b2a4-187">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="2b2a4-188">Uma solicitação de `/counter` no navegador, conforme especificado pela diretiva `@page` na parte superior, faz com que o componente `Counter` processe seu conteúdo.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-188">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="2b2a4-189">Os componentes são renderizados em uma representação na memória da árvore de renderização que pode ser usada para atualizar a interface do usuário de maneira flexível e eficiente.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-189">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="2b2a4-190">Sempre que o botão **clicar em mim** estiver selecionado:</span><span class="sxs-lookup"><span data-stu-id="2b2a4-190">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="2b2a4-191">O evento `onclick` é acionado.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-191">The `onclick` event is fired.</span></span>
* <span data-ttu-id="2b2a4-192">O método `IncrementCount` é chamado.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-192">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="2b2a4-193">O `currentCount` é incrementado.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-193">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="2b2a4-194">O componente é renderizado novamente.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-194">The component is rendered again.</span></span>

<span data-ttu-id="2b2a4-195">O tempo de execução compara o novo conteúdo com o conteúdo anterior e aplica apenas o conteúdo alterado para o Modelo de Objeto do Documento (DOM).</span><span class="sxs-lookup"><span data-stu-id="2b2a4-195">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="2b2a4-196">Adicione um componente a outro componente usando a sintaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-196">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="2b2a4-197">Por exemplo, adicione o componente `Counter` à Home Page do aplicativo adicionando um elemento `<Counter />` ao componente `Index`.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-197">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="2b2a4-198">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="2b2a4-198">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="2b2a4-199">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-199">Run the app.</span></span> <span data-ttu-id="2b2a4-200">A Home Page tem seu próprio contador fornecido pelo componente `Counter`.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-200">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="2b2a4-201">Os parâmetros de componente são especificados usando atributos ou [conteúdo filho](xref:blazor/components#child-content), que permitem definir propriedades no componente filho.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-201">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="2b2a4-202">Para adicionar um parâmetro ao componente `Counter`, atualize o bloco de `@code` do componente:</span><span class="sxs-lookup"><span data-stu-id="2b2a4-202">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="2b2a4-203">Adicione uma propriedade pública para `IncrementAmount` com um atributo `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-203">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="2b2a4-204">Altere o método `IncrementCount` para usar o `IncrementAmount` ao aumentar o valor de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-204">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="2b2a4-205">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="2b2a4-205">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="2b2a4-206">Especifique o `IncrementAmount` no elemento `<Counter>` do componente de `Index` usando um atributo.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-206">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="2b2a4-207">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="2b2a4-207">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="2b2a4-208">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-208">Run the app.</span></span> <span data-ttu-id="2b2a4-209">O componente `Index` tem seu próprio contador que é incrementado em dez cada vez que o botão **Click me** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-209">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="2b2a4-210">O componente `Counter` (*Counter. Razor*) em `/counter` continua a incrementar um.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-210">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2b2a4-211">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="2b2a4-211">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="2b2a4-212">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="2b2a4-212">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
