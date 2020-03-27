---
title: Introdução ao ASP.NET Core Blazor
author: guardrex
description: Comece a usar o Blazor criando um aplicativo Blazor com as ferramentas de sua escolha.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 9ebeb57d2fad7e4c288d61a46911f2bf64cac2fb
ms.sourcegitcommit: f3b1bcfd108e5d53f73abc0bf2555890869d953b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80320938"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="f23cb-103">Introdução ao ASP.NET Core mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="f23cb-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="f23cb-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f23cb-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="f23cb-105">Para começar com o mais claro, siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="f23cb-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f23cb-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f23cb-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f23cb-107">Para criar aplicativos de servidor mais recentes, instale o [Visual Studio 2019 versão 16,4 ou posterior](https://visualstudio.microsoft.com/vs/preview/) com a ASP.net e a carga de trabalho de **desenvolvimento Web** .</span><span class="sxs-lookup"><span data-stu-id="f23cb-107">To create Blazor Server apps, install [Visual Studio 2019 version 16.4 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="f23cb-108">Para criar aplicativos Webassembly mais popicores e de servidor de mais recente, instale o Visual Studio 2019 16,6 Preview 2 ou posterior com a ASP.NET e a carga de trabalho de **desenvolvimento na Web** .</span><span class="sxs-lookup"><span data-stu-id="f23cb-108">To create Blazor Server and Blazor WebAssembly apps, install Visual Studio 2019 16.6 Preview 2 or later with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="f23cb-109">Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, *Webassembly* e *servidor*mais incrivelmente, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="f23cb-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="f23cb-110">Crie um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="f23cb-110">Create a new project.</span></span>

1. <span data-ttu-id="f23cb-111">Selecione **aplicativo mais incrivelmente**.</span><span class="sxs-lookup"><span data-stu-id="f23cb-111">Select **Blazor App**.</span></span> <span data-ttu-id="f23cb-112">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="f23cb-112">Select **Next**.</span></span>

1. <span data-ttu-id="f23cb-113">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="f23cb-113">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="f23cb-114">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="f23cb-114">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="f23cb-115">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="f23cb-115">Select **Create**.</span></span>

1. <span data-ttu-id="f23cb-116">Para obter uma experiência de Webassembly mais experiente (Visual Studio 16,6 Preview 2 ou posterior), escolha o modelo de **aplicativo Webassembly** mais experiente.</span><span class="sxs-lookup"><span data-stu-id="f23cb-116">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="f23cb-117">Para uma experiência de servidor mais incrivelmente (Visual Studio 16,4 ou posterior), escolha o modelo de **aplicativo de servidor** mais experiente.</span><span class="sxs-lookup"><span data-stu-id="f23cb-117">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="f23cb-118">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="f23cb-118">Select **Create**.</span></span>

1. <span data-ttu-id="f23cb-119">Pressione <kbd>Ctrl</kbd>+<kbd>F5</kbd> para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f23cb-119">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f23cb-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f23cb-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="f23cb-121">Instale o [SDK do .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="f23cb-121">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="f23cb-122">Opcionalmente, instale o modelo de visualização do [Webassembly mais incrivelmente](xref:blazor/hosting-models#blazor-webassembly) executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="f23cb-122">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
   ```

   > [!NOTE]
   > <span data-ttu-id="f23cb-123">O [SDK do .NET Core versão 3.1.201 ou posterior](https://dotnet.microsoft.com/download/dotnet-core/3.1) é **necessário** para usar o modelo Webassembly do 3,2 Preview 3 mais recente.</span><span class="sxs-lookup"><span data-stu-id="f23cb-123">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 3 Blazor WebAssembly template.</span></span> <span data-ttu-id="f23cb-124">Confirme a versão do SDK do .NET Core instalada executando `dotnet --version` em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="f23cb-124">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="f23cb-125">Instale o [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="f23cb-125">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="f23cb-126">Instale o mais recente [ C# para a extensão de Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e a extensão do [depurador do JavaScript (noturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) com `debug.javascript.usePreview` definido como `true`.</span><span class="sxs-lookup"><span data-stu-id="f23cb-126">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="f23cb-127">Para uma experiência de servidor mais incrivelmente, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="f23cb-127">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="f23cb-128">Para uma experiência de Webassembly mais experiente, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="f23cb-128">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="f23cb-129">Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, um *servidor mais incrivelmente* e um *Webassembly*de mais ou mais, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="f23cb-129">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="f23cb-130">Abra a pasta *WebApplication1* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="f23cb-130">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="f23cb-131">O IDE solicita que você adicione ativos para compilar e depurar o projeto.</span><span class="sxs-lookup"><span data-stu-id="f23cb-131">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="f23cb-132">Selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="f23cb-132">Select **Yes**.</span></span>

1. <span data-ttu-id="f23cb-133">Execute o aplicativo usando o depurador de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="f23cb-133">Run the app using the Visual Studio Code debugger.</span></span>

1. <span data-ttu-id="f23cb-134">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="f23cb-134">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f23cb-135">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f23cb-135">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f23cb-136">Há suporte para o servidor mais incrivelmente no Visual Studio para Mac.</span><span class="sxs-lookup"><span data-stu-id="f23cb-136">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="f23cb-137">Não há suporte para Webassembly mais incrivelmente no momento.</span><span class="sxs-lookup"><span data-stu-id="f23cb-137">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="f23cb-138">Para criar aplicativos Webassembly mais elaborados no macOS, siga as orientações na guia **CLI do .NET Core** .</span><span class="sxs-lookup"><span data-stu-id="f23cb-138">To build Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab.</span></span>

1. <span data-ttu-id="f23cb-139">Instale o [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="f23cb-139">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="f23cb-140">Selecione **arquivo** > **nova solução** ou crie um **novo projeto**.</span><span class="sxs-lookup"><span data-stu-id="f23cb-140">Select **File** > **New Solution** or create a **New Project**.</span></span>

1. <span data-ttu-id="f23cb-141">Na barra lateral, selecione **.NET Core** > **aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="f23cb-141">In the sidebar, select **.NET Core** > **App**.</span></span>

1. <span data-ttu-id="f23cb-142">Selecione o modelo de **aplicativo de servidor mais incrivelmente** .</span><span class="sxs-lookup"><span data-stu-id="f23cb-142">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="f23cb-143">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="f23cb-143">Select **Create**.</span></span>

   <span data-ttu-id="f23cb-144">Para obter informações sobre o modelo de Hospedagem de servidor mais incrivelmente, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="f23cb-144">For information on the Blazor Server hosting model, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="f23cb-145">Defina a **estrutura de destino** como **.NET Core 3,1** e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="f23cb-145">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

1. <span data-ttu-id="f23cb-146">No campo **nome do projeto** , nomeie o aplicativo `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="f23cb-146">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="f23cb-147">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="f23cb-147">Select **Create**.</span></span>

1. <span data-ttu-id="f23cb-148">Selecione **executar** > **executar sem depuração** para executar o aplicativo *sem o depurador*.</span><span class="sxs-lookup"><span data-stu-id="f23cb-148">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="f23cb-149">Execute o aplicativo com **Iniciar Depuração** para executar o aplicativo *com o depurador*.</span><span class="sxs-lookup"><span data-stu-id="f23cb-149">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

<span data-ttu-id="f23cb-150">Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue.</span><span class="sxs-lookup"><span data-stu-id="f23cb-150">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f23cb-151">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="f23cb-151">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="f23cb-152">Instale o [SDK do .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="f23cb-152">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="f23cb-153">Opcionalmente, instale o modelo de visualização do [Webassembly mais incrivelmente](xref:blazor/hosting-models#blazor-webassembly) executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="f23cb-153">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
   ```

   > [!NOTE]
   > <span data-ttu-id="f23cb-154">O [SDK do .NET Core versão 3.1.201 ou posterior](https://dotnet.microsoft.com/download/dotnet-core/3.1) é **necessário** para usar o modelo Webassembly do 3,2 Preview 3 mais recente.</span><span class="sxs-lookup"><span data-stu-id="f23cb-154">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 3 Blazor WebAssembly template.</span></span> <span data-ttu-id="f23cb-155">Confirme a versão do SDK do .NET Core instalada executando `dotnet --version` em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="f23cb-155">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="f23cb-156">Para uma experiência de servidor mais incrivelmente, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="f23cb-156">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="f23cb-157">Para uma experiência de Webassembly mais experiente, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="f23cb-157">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="f23cb-158">Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, um *servidor mais incrivelmente* e um *Webassembly*de mais ou mais, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="f23cb-158">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="f23cb-159">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="f23cb-159">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="f23cb-160">Várias páginas estão disponíveis em guias na barra lateral:</span><span class="sxs-lookup"><span data-stu-id="f23cb-160">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="f23cb-161">Home</span><span class="sxs-lookup"><span data-stu-id="f23cb-161">Home</span></span>
* <span data-ttu-id="f23cb-162">Contador</span><span class="sxs-lookup"><span data-stu-id="f23cb-162">Counter</span></span>
* <span data-ttu-id="f23cb-163">Buscar dados</span><span class="sxs-lookup"><span data-stu-id="f23cb-163">Fetch data</span></span>

<span data-ttu-id="f23cb-164">Na página Contador, selecione o botão **Clique aqui** para incrementar o contador sem uma atualização de página.</span><span class="sxs-lookup"><span data-stu-id="f23cb-164">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="f23cb-165">Incrementar um contador em uma página da Web normalmente requer a escrita de JavaScript, mas com Blazor C#você pode usar.</span><span class="sxs-lookup"><span data-stu-id="f23cb-165">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="f23cb-166">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="f23cb-166">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="f23cb-167">Uma solicitação de `/counter` no navegador, conforme especificado pela diretiva `@page` na parte superior, faz com que o componente `Counter` processe seu conteúdo.</span><span class="sxs-lookup"><span data-stu-id="f23cb-167">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="f23cb-168">Os componentes são renderizados em uma representação na memória da árvore de renderização que pode ser usada para atualizar a interface do usuário de maneira flexível e eficiente.</span><span class="sxs-lookup"><span data-stu-id="f23cb-168">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="f23cb-169">Sempre que o botão **clicar em mim** estiver selecionado:</span><span class="sxs-lookup"><span data-stu-id="f23cb-169">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="f23cb-170">O evento `onclick` é acionado.</span><span class="sxs-lookup"><span data-stu-id="f23cb-170">The `onclick` event is fired.</span></span>
* <span data-ttu-id="f23cb-171">O método `IncrementCount` é chamado.</span><span class="sxs-lookup"><span data-stu-id="f23cb-171">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="f23cb-172">O `currentCount` é incrementado.</span><span class="sxs-lookup"><span data-stu-id="f23cb-172">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="f23cb-173">O componente é renderizado novamente.</span><span class="sxs-lookup"><span data-stu-id="f23cb-173">The component is rendered again.</span></span>

<span data-ttu-id="f23cb-174">O tempo de execução compara o novo conteúdo com o conteúdo anterior e aplica apenas o conteúdo alterado para o Modelo de Objeto do Documento (DOM).</span><span class="sxs-lookup"><span data-stu-id="f23cb-174">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="f23cb-175">Adicione um componente a outro componente usando a sintaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="f23cb-175">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="f23cb-176">Por exemplo, adicione o componente `Counter` à Home Page do aplicativo adicionando um elemento `<Counter />` ao componente `Index`.</span><span class="sxs-lookup"><span data-stu-id="f23cb-176">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="f23cb-177">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="f23cb-177">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="f23cb-178">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f23cb-178">Run the app.</span></span> <span data-ttu-id="f23cb-179">A Home Page tem seu próprio contador fornecido pelo componente `Counter`.</span><span class="sxs-lookup"><span data-stu-id="f23cb-179">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="f23cb-180">Os parâmetros de componente são especificados usando atributos ou [conteúdo filho](xref:blazor/components#child-content), que permitem definir propriedades no componente filho.</span><span class="sxs-lookup"><span data-stu-id="f23cb-180">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="f23cb-181">Para adicionar um parâmetro ao componente `Counter`, atualize o bloco de `@code` do componente:</span><span class="sxs-lookup"><span data-stu-id="f23cb-181">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="f23cb-182">Adicione uma propriedade pública para `IncrementAmount` com um atributo `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="f23cb-182">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="f23cb-183">Altere o método `IncrementCount` para usar o `IncrementAmount` ao aumentar o valor de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="f23cb-183">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="f23cb-184">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="f23cb-184">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="f23cb-185">Especifique o `IncrementAmount` no elemento `<Counter>` do componente de `Index` usando um atributo.</span><span class="sxs-lookup"><span data-stu-id="f23cb-185">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="f23cb-186">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="f23cb-186">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="f23cb-187">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f23cb-187">Run the app.</span></span> <span data-ttu-id="f23cb-188">O componente `Index` tem seu próprio contador que é incrementado em dez cada vez que o botão **Click me** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="f23cb-188">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="f23cb-189">O componente `Counter` (*Counter. Razor*) em `/counter` continua a incrementar um.</span><span class="sxs-lookup"><span data-stu-id="f23cb-189">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f23cb-190">{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="f23cb-190">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="f23cb-191">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f23cb-191">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
