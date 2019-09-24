---
title: Introdução ao ASP.NET Core mais incrivelmente
author: guardrex
description: Comece com o mais elaborado criando um aplicativo mais incrivelmente com as ferramentas de sua escolha.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/get-started
ms.openlocfilehash: 4c2a8f62b7f6a60815d131756d1e551904d918ad
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71207215"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="2cd16-103">Introdução ao ASP.NET Core mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="2cd16-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="2cd16-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2cd16-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="2cd16-105">Introdução ao mais incrivelmente:</span><span class="sxs-lookup"><span data-stu-id="2cd16-105">Get started with Blazor:</span></span>

1. <span data-ttu-id="2cd16-106">Instale a versão mais recente do [SDK do .NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0) .</span><span class="sxs-lookup"><span data-stu-id="2cd16-106">Install the latest [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>

1. <span data-ttu-id="2cd16-107">Instale os modelos mais poficados executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="2cd16-107">Install the Blazor templates by running the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.0.0-preview9.19465.2
   ```

1. <span data-ttu-id="2cd16-108">Siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="2cd16-108">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2cd16-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2cd16-109">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="2cd16-110">1 \.</span><span class="sxs-lookup"><span data-stu-id="2cd16-110">1\.</span></span> <span data-ttu-id="2cd16-111">Instale o [Visual Studio](https://visualstudio.com/vs/) mais recente com a ASP.net e a carga de trabalho de **desenvolvimento Web** .</span><span class="sxs-lookup"><span data-stu-id="2cd16-111">Install the latest [Visual Studio](https://visualstudio.com/vs/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="2cd16-112">2 \.</span><span class="sxs-lookup"><span data-stu-id="2cd16-112">2\.</span></span> <span data-ttu-id="2cd16-113">Crie um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="2cd16-113">Create a new project.</span></span>

   <span data-ttu-id="2cd16-114">3 \.</span><span class="sxs-lookup"><span data-stu-id="2cd16-114">3\.</span></span> <span data-ttu-id="2cd16-115">Selecione **aplicativo mais incrivelmente**.</span><span class="sxs-lookup"><span data-stu-id="2cd16-115">Select **Blazor App**.</span></span> <span data-ttu-id="2cd16-116">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="2cd16-116">Select **Next**.</span></span>

   <span data-ttu-id="2cd16-117">4 \.</span><span class="sxs-lookup"><span data-stu-id="2cd16-117">4\.</span></span> <span data-ttu-id="2cd16-118">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="2cd16-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="2cd16-119">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="2cd16-119">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="2cd16-120">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="2cd16-120">Select **Create**.</span></span>

   <span data-ttu-id="2cd16-121">5 \.</span><span class="sxs-lookup"><span data-stu-id="2cd16-121">5\.</span></span> <span data-ttu-id="2cd16-122">Para obter uma experiência de Webassembly mais experiente, escolha o modelo de **aplicativo Webassembly mais incrivelmente** .</span><span class="sxs-lookup"><span data-stu-id="2cd16-122">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="2cd16-123">Para uma experiência de servidor mais incrivelmente, escolha o modelo de **aplicativo de servidor** mais experiente.</span><span class="sxs-lookup"><span data-stu-id="2cd16-123">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="2cd16-124">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="2cd16-124">Select **Create**.</span></span> <span data-ttu-id="2cd16-125">Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, um *servidor mais incrivelmente* e um *Webassembly*de mais ou mais, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="2cd16-125">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="2cd16-126">6 \.</span><span class="sxs-lookup"><span data-stu-id="2cd16-126">6\.</span></span> <span data-ttu-id="2cd16-127">Pressione **F5** para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2cd16-127">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="2cd16-128">Se você instalou a extensão do Visual Studio para uma versão prévia anterior do ASP.NET Core mais recente (visualização 6 ou anterior), você pode desinstalar a extensão.</span><span class="sxs-lookup"><span data-stu-id="2cd16-128">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="2cd16-129">Instalar os modelos mais bem em um shell de comando agora é suficiente para trazer os modelos no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2cd16-129">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2cd16-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2cd16-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="2cd16-131">1 \.</span><span class="sxs-lookup"><span data-stu-id="2cd16-131">1\.</span></span> <span data-ttu-id="2cd16-132">Instalar o [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="2cd16-132">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="2cd16-133">2 \.</span><span class="sxs-lookup"><span data-stu-id="2cd16-133">2\.</span></span> <span data-ttu-id="2cd16-134">Instale o mais recente [ C# para a extensão de Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="2cd16-134">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="2cd16-135">3 \.</span><span class="sxs-lookup"><span data-stu-id="2cd16-135">3\.</span></span> <span data-ttu-id="2cd16-136">Para uma experiência de Webassembly mais experiente, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="2cd16-136">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="2cd16-137">Para uma experiência de servidor mais incrivelmente, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="2cd16-137">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="2cd16-138">Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, um *servidor mais incrivelmente* e um *Webassembly*de mais ou mais, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="2cd16-138">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="2cd16-139">4 \.</span><span class="sxs-lookup"><span data-stu-id="2cd16-139">4\.</span></span> <span data-ttu-id="2cd16-140">Abra a pasta *WebApplication1* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="2cd16-140">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="2cd16-141">5 \.</span><span class="sxs-lookup"><span data-stu-id="2cd16-141">5\.</span></span> <span data-ttu-id="2cd16-142">Para um projeto de servidor mais incrivelmente, o IDE solicita que você adicione ativos para compilar e depurar o projeto.</span><span class="sxs-lookup"><span data-stu-id="2cd16-142">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="2cd16-143">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="2cd16-143">Select **Yes**.</span></span>

   <span data-ttu-id="2cd16-144">6 \.</span><span class="sxs-lookup"><span data-stu-id="2cd16-144">6\.</span></span> <span data-ttu-id="2cd16-145">Se estiver usando um aplicativo de servidor mais incrivelmente, execute o aplicativo usando o depurador de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="2cd16-145">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="2cd16-146">Se estiver usando um aplicativo Webassembly mais incrivelmente, `dotnet run` execute na pasta do projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2cd16-146">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="2cd16-147">7 \.</span><span class="sxs-lookup"><span data-stu-id="2cd16-147">7\.</span></span> <span data-ttu-id="2cd16-148">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="2cd16-148">In a browser, navigate to `https://localhost:5001`.</span></span>

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor Server experience, select the **Blazor Server App** template. For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="2cd16-149">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="2cd16-149">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="2cd16-150">Para uma experiência de Webassembly mais experiente, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="2cd16-150">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="2cd16-151">Para uma experiência de servidor mais incrivelmente, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="2cd16-151">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="2cd16-152">Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, um *servidor mais incrivelmente* e um *Webassembly*de mais ou mais, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="2cd16-152">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="2cd16-153">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="2cd16-153">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

<span data-ttu-id="2cd16-154">Várias páginas estão disponíveis em guias na barra lateral:</span><span class="sxs-lookup"><span data-stu-id="2cd16-154">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="2cd16-155">Home</span><span class="sxs-lookup"><span data-stu-id="2cd16-155">Home</span></span>
* <span data-ttu-id="2cd16-156">Contador</span><span class="sxs-lookup"><span data-stu-id="2cd16-156">Counter</span></span>
* <span data-ttu-id="2cd16-157">Buscar dados</span><span class="sxs-lookup"><span data-stu-id="2cd16-157">Fetch data</span></span>

<span data-ttu-id="2cd16-158">Na página Contador, selecione o botão **Clique aqui** para incrementar o contador sem uma atualização de página.</span><span class="sxs-lookup"><span data-stu-id="2cd16-158">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="2cd16-159">Incrementar um contador em uma página da Web normalmente requer a escrita de JavaScript, mas os componentes do Razor C#fornecem uma abordagem melhor usando o.</span><span class="sxs-lookup"><span data-stu-id="2cd16-159">Incrementing a counter in a webpage normally requires writing JavaScript, but Razor components provide a better approach using C#.</span></span>

<span data-ttu-id="2cd16-160">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="2cd16-160">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="2cd16-161">Uma solicitação para `/counter` no navegador, conforme especificado `@page` pela diretiva na parte superior, faz com que o `Counter` componente processe seu conteúdo.</span><span class="sxs-lookup"><span data-stu-id="2cd16-161">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="2cd16-162">Os componentes são renderizados em uma representação na memória da árvore de renderização que pode ser usada para atualizar a interface do usuário de maneira flexível e eficiente.</span><span class="sxs-lookup"><span data-stu-id="2cd16-162">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="2cd16-163">Sempre que o botão **clicar em mim** estiver selecionado:</span><span class="sxs-lookup"><span data-stu-id="2cd16-163">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="2cd16-164">O `onclick` evento é acionado.</span><span class="sxs-lookup"><span data-stu-id="2cd16-164">The `onclick` event is fired.</span></span>
* <span data-ttu-id="2cd16-165">O método `IncrementCount` é chamado.</span><span class="sxs-lookup"><span data-stu-id="2cd16-165">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="2cd16-166">O `currentCount` é incrementado.</span><span class="sxs-lookup"><span data-stu-id="2cd16-166">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="2cd16-167">O componente é renderizado novamente.</span><span class="sxs-lookup"><span data-stu-id="2cd16-167">The component is rendered again.</span></span>

<span data-ttu-id="2cd16-168">O tempo de execução compara o novo conteúdo com o conteúdo anterior e aplica apenas o conteúdo alterado para o Modelo de Objeto do Documento (DOM).</span><span class="sxs-lookup"><span data-stu-id="2cd16-168">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="2cd16-169">Adicione um componente a outro componente usando a sintaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="2cd16-169">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="2cd16-170">Por exemplo, adicione o `Counter` componente à página inicial do aplicativo adicionando um `<Counter />` elemento ao `Index` componente.</span><span class="sxs-lookup"><span data-stu-id="2cd16-170">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="2cd16-171">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="2cd16-171">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="2cd16-172">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2cd16-172">Run the app.</span></span> <span data-ttu-id="2cd16-173">A Home Page tem seu próprio contador fornecido pelo `Counter` componente.</span><span class="sxs-lookup"><span data-stu-id="2cd16-173">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="2cd16-174">Os parâmetros de componente são especificados usando atributos ou [conteúdo filho](xref:blazor/components#child-content), que permitem definir propriedades no componente filho.</span><span class="sxs-lookup"><span data-stu-id="2cd16-174">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="2cd16-175">Para adicionar um parâmetro ao `Counter` componente, atualize o bloco do `@code` componente:</span><span class="sxs-lookup"><span data-stu-id="2cd16-175">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="2cd16-176">Adicione uma propriedade pública para `IncrementAmount` com um `[Parameter]` atributo.</span><span class="sxs-lookup"><span data-stu-id="2cd16-176">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="2cd16-177">Altere o método `IncrementCount` para usar o `IncrementAmount` ao aumentar o valor de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="2cd16-177">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="2cd16-178">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="2cd16-178">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="2cd16-179">Especifique o `IncrementAmount` noelemento`<Counter>` do componenteusandoumatributo.`Index`</span><span class="sxs-lookup"><span data-stu-id="2cd16-179">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="2cd16-180">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="2cd16-180">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="2cd16-181">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2cd16-181">Run the app.</span></span> <span data-ttu-id="2cd16-182">O `Index` componente tem seu próprio contador que é incrementado em dez cada vez que o botão **Click me** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="2cd16-182">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="2cd16-183">O `Counter` componente (*Counter. Razor*) em `/counter` continua a incrementar um.</span><span class="sxs-lookup"><span data-stu-id="2cd16-183">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2cd16-184">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="2cd16-184">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="2cd16-185">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="2cd16-185">Additional resources</span></span>

* <xref:signalr/introduction>
