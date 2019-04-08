---
title: Introdução ao Blazor
author: guardrex
description: Saiba como começar com Blazor criando e modificando um projeto Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: spa/blazor/get-started
ms.openlocfilehash: b3928c2812be6f34cdf2f17295a1251106f651e5
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068229"
---
# <a name="get-started-with-blazor"></a><span data-ttu-id="627ec-103">Introdução ao Blazor</span><span class="sxs-lookup"><span data-stu-id="627ec-103">Get started with Blazor</span></span>

<span data-ttu-id="627ec-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="627ec-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

# [<a name="visual-studio"></a><span data-ttu-id="627ec-105">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="627ec-105">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="627ec-106">Pré-requisitos:</span><span class="sxs-lookup"><span data-stu-id="627ec-106">Prerequisites:</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

<span data-ttu-id="627ec-107">Para criar seu primeiro projeto Blazor no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="627ec-107">To create your first Blazor project in Visual Studio:</span></span>

1. <span data-ttu-id="627ec-108">Instale o versão mais recente [SDK de visualização do .NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0) de versão.</span><span class="sxs-lookup"><span data-stu-id="627ec-108">Install the latest [.NET Core 3.0 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>
1. <span data-ttu-id="627ec-109">Habilite o Visual Studio para usar SDKs de visualização:</span><span class="sxs-lookup"><span data-stu-id="627ec-109">Enable Visual Studio to use preview SDKs:</span></span>
   1. <span data-ttu-id="627ec-110">Abra **ferramentas** > **opções** na barra de menus.</span><span class="sxs-lookup"><span data-stu-id="627ec-110">Open **Tools** > **Options** in the menu bar.</span></span>
   1. <span data-ttu-id="627ec-111">Abra o **projetos e soluções** nó.</span><span class="sxs-lookup"><span data-stu-id="627ec-111">Open the **Projects and Solutions** node.</span></span> <span data-ttu-id="627ec-112">Abra o **.NET Core** guia.</span><span class="sxs-lookup"><span data-stu-id="627ec-112">Open the **.NET Core** tab.</span></span>
   1. <span data-ttu-id="627ec-113">Marque a caixa **Use versões prévias do SDK do .NET Core**.</span><span class="sxs-lookup"><span data-stu-id="627ec-113">Check the box for **Use previews of the .NET Core SDK**.</span></span> <span data-ttu-id="627ec-114">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="627ec-114">Select **OK**.</span></span>
1. <span data-ttu-id="627ec-115">Instale o versão mais recente [Blazor extensão](https://go.microsoft.com/fwlink/?linkid=870389) do Visual Studio Marketplace.</span><span class="sxs-lookup"><span data-stu-id="627ec-115">Install the latest [Blazor extension](https://go.microsoft.com/fwlink/?linkid=870389) from the Visual Studio Marketplace.</span></span> <span data-ttu-id="627ec-116">Esta etapa disponibiliza Blazor modelos para o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="627ec-116">This step makes Blazor templates available to Visual Studio.</span></span>
1. <span data-ttu-id="627ec-117">Verifique os modelos de Blazor disponíveis para uso com a CLI do .NET Core, executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="627ec-117">Make the Blazor templates available for use with the .NET Core CLI by running the following command in a command shell:</span></span>

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::0.9.0-preview3-19154-02
   ```
1. <span data-ttu-id="627ec-118">Crie um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="627ec-118">Create a new project.</span></span>
1. <span data-ttu-id="627ec-119">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="627ec-119">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="627ec-120">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="627ec-120">Select **Next**.</span></span>
1. <span data-ttu-id="627ec-121">Forneça um nome na **nome do projeto** campo.</span><span class="sxs-lookup"><span data-stu-id="627ec-121">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="627ec-122">Confirme se o **local** entrada está correta ou fornecer um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="627ec-122">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="627ec-123">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="627ec-123">Select **Create**.</span></span>
1. <span data-ttu-id="627ec-124">Certifique-se **.NET Core** e **ASP.NET Core 3.0** são selecionados na parte superior.</span><span class="sxs-lookup"><span data-stu-id="627ec-124">Make sure **.NET Core** and **ASP.NET Core 3.0** are selected at the top.</span></span>
1. <span data-ttu-id="627ec-125">Selecione o **Blazor** modelo e selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="627ec-125">Select the **Blazor** template and select **Create**.</span></span>
1. <span data-ttu-id="627ec-126">Pressione **F5** para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="627ec-126">Press **F5** to run the app.</span></span>

<span data-ttu-id="627ec-127">Parabéns!</span><span class="sxs-lookup"><span data-stu-id="627ec-127">Congratulations!</span></span> <span data-ttu-id="627ec-128">Você acaba de executar seu primeiro aplicativo Blazor!</span><span class="sxs-lookup"><span data-stu-id="627ec-128">You just ran your first Blazor app!</span></span>

<!--

# [Visual Studio Code](#tab/visual-studio-code)

Prerequisites:

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

To create your first Blazor project in Visual Studio Code:

1. Execute the following command in a command shell:

   ```console
   dotnet new blazor -o WebApplication1
   ```

1. Open the *WebApplication1* folder in Visual Studio Code.

1. Visual Studio code offers to create assets to build and debug the app, which includes the *tasks.json* and *launch.json* files. Select **Yes** to add the assets.

1. Execute the app using the Visual Studio Code debugger.

1. In a browser, navigate to `https://localhost:5001`.

Congratulations! You just ran your first Blazor app!

# [Visual Studio for Mac](#tab/visual-studio-mac)

.NET Core 3.0 will be supported with Visual Studio for Mac version 8.0 or later. Visual Studio for Mac version 8.0 Preview isn't available at this time.

Use the [.NET Core CLI version of this topic](xref:razor-components/get-started?tabs=netcore-cli) on macOS.

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

To create your first project Blazor project in Visual Studio for Mac:

1. Select **File** > **New Solution** or **New Project**.
1. In the sidebar, select **.NET Core** > **App**.
1. Select **Blazor** and select **Next**.
1. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.
1. In the **Project Name** field, enter `WebApplication1`. Select **Create**.
1. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

Congratulations! You just ran your first Blazor app!
-->

# [<a name="net-core-cli"></a><span data-ttu-id="627ec-129">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="627ec-129">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="627ec-130">Pré-requisitos:</span><span class="sxs-lookup"><span data-stu-id="627ec-130">Prerequisites:</span></span>

* [<span data-ttu-id="627ec-131">.NET core SDK 3.0 versão prévia</span><span class="sxs-lookup"><span data-stu-id="627ec-131">.NET Core SDK 3.0 Preview</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

1. <span data-ttu-id="627ec-132">Adicione os modelos de Blazor, executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="627ec-132">Add the Blazor templates by running the following command in a command shell:</span></span>

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::0.9.0-preview3-19154-02
   ```

1. <span data-ttu-id="627ec-133">Crie seu primeiro projeto Blazor em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="627ec-133">Create your first Blazor project in a command shell:</span></span>

   ```console
   dotnet new blazor -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. <span data-ttu-id="627ec-134">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="627ec-134">In a browser, navigate to `https://localhost:5001`.</span></span>

<span data-ttu-id="627ec-135">Parabéns!</span><span class="sxs-lookup"><span data-stu-id="627ec-135">Congratulations!</span></span> <span data-ttu-id="627ec-136">Você acaba de executar seu primeiro aplicativo Blazor!</span><span class="sxs-lookup"><span data-stu-id="627ec-136">You just ran your first Blazor app!</span></span>

---

## <a name="blazor-project"></a><span data-ttu-id="627ec-137">Projeto Blazor</span><span class="sxs-lookup"><span data-stu-id="627ec-137">Blazor project</span></span>

<span data-ttu-id="627ec-138">Quando o aplicativo é executado, várias páginas estão disponíveis nas guias na barra lateral:</span><span class="sxs-lookup"><span data-stu-id="627ec-138">When the app is run, multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="627ec-139">Home</span><span class="sxs-lookup"><span data-stu-id="627ec-139">Home</span></span>
* <span data-ttu-id="627ec-140">Contador</span><span class="sxs-lookup"><span data-stu-id="627ec-140">Counter</span></span>
* <span data-ttu-id="627ec-141">Buscar dados</span><span class="sxs-lookup"><span data-stu-id="627ec-141">Fetch data</span></span>

<span data-ttu-id="627ec-142">Na página Contador, selecione o botão **Clique aqui** para incrementar o contador sem uma atualização de página.</span><span class="sxs-lookup"><span data-stu-id="627ec-142">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="627ec-143">Incrementar um contador em uma página da Web normalmente requer a gravação do JavaScript, mas Blazor fornece uma abordagem melhor usando C#.</span><span class="sxs-lookup"><span data-stu-id="627ec-143">Incrementing a counter in a webpage normally requires writing JavaScript, but Blazor provides a better approach using C#.</span></span>

<span data-ttu-id="627ec-144">*Pages/Counter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="627ec-144">*Pages/Counter.cshtml*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.cshtml)]

<span data-ttu-id="627ec-145">Uma solicitação para `/counter` no navegador, conforme especificado pelo `@page` diretiva na parte superior, faz com que o componente de contador processar seu conteúdo.</span><span class="sxs-lookup"><span data-stu-id="627ec-145">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the Counter component to render its content.</span></span> <span data-ttu-id="627ec-146">Componentes processam em uma representação na memória da árvore de renderização que pode ser usado para atualizar a interface do usuário de uma maneira flexível e eficiente.</span><span class="sxs-lookup"><span data-stu-id="627ec-146">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="627ec-147">Cada vez que o **Click me** botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="627ec-147">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="627ec-148">O `onclick` evento é disparado.</span><span class="sxs-lookup"><span data-stu-id="627ec-148">The `onclick` event is fired.</span></span>
* <span data-ttu-id="627ec-149">O método `IncrementCount` é chamado.</span><span class="sxs-lookup"><span data-stu-id="627ec-149">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="627ec-150">O `currentCount` é incrementado.</span><span class="sxs-lookup"><span data-stu-id="627ec-150">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="627ec-151">O componente é renderizado novamente.</span><span class="sxs-lookup"><span data-stu-id="627ec-151">The component is rendered again.</span></span>

<span data-ttu-id="627ec-152">O tempo de execução compara o novo conteúdo para o conteúdo anterior e só se aplica o conteúdo alterado para modelo de objeto de documento (DOM).</span><span class="sxs-lookup"><span data-stu-id="627ec-152">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="627ec-153">Adicione um componente para outro componente usando uma sintaxe semelhante ao HTML.</span><span class="sxs-lookup"><span data-stu-id="627ec-153">Add a component to another component using an HTML-like syntax.</span></span> <span data-ttu-id="627ec-154">Componente são especificados usando atributos ou conteúdo filho.</span><span class="sxs-lookup"><span data-stu-id="627ec-154">Component parameters are specified using attributes or child content.</span></span> <span data-ttu-id="627ec-155">Por exemplo, um componente de contador pode ser adicionado a homepage do aplicativo adicionando um `<Counter />` elemento para o componente do índice.</span><span class="sxs-lookup"><span data-stu-id="627ec-155">For example, a Counter component can be added to the app's homepage by adding a `<Counter />` element to the Index component.</span></span>

<span data-ttu-id="627ec-156">Na *Pages*, substitua o componente de solicitação de pesquisa com um componente de contador:</span><span class="sxs-lookup"><span data-stu-id="627ec-156">In *Pages/Index.cshtml*, replace the Survey Prompt component with a Counter component:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.cshtml?highlight=7)]

<span data-ttu-id="627ec-157">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="627ec-157">Run the app.</span></span> <span data-ttu-id="627ec-158">A home page tem seu próprio contador.</span><span class="sxs-lookup"><span data-stu-id="627ec-158">The homepage has its own counter.</span></span>

<span data-ttu-id="627ec-159">Para adicionar um parâmetro para o componente de contador, atualize o componente `@functions` bloco:</span><span class="sxs-lookup"><span data-stu-id="627ec-159">To add a parameter to the Counter component, update the component's `@functions` block:</span></span>

* <span data-ttu-id="627ec-160">Adicionar uma propriedade para `IncrementAmount` decorada com o `[Parameter]` atributo.</span><span class="sxs-lookup"><span data-stu-id="627ec-160">Add a property for `IncrementAmount` decorated with the `[Parameter]` attribute.</span></span>
* <span data-ttu-id="627ec-161">Altere o método `IncrementCount` para usar o `IncrementAmount` ao aumentar o valor de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="627ec-161">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="627ec-162">*Pages/Counter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="627ec-162">*Pages/Counter.cshtml*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.cshtml?highlight=4,8)]

<span data-ttu-id="627ec-163">Especifique um parâmetro `IncrementAmount` no elemento `<Counter>` do componente Home usando um atributo.</span><span class="sxs-lookup"><span data-stu-id="627ec-163">Specify an `IncrementAmount` parameter in the Home component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="627ec-164">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="627ec-164">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.cshtml)]

<span data-ttu-id="627ec-165">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="627ec-165">Run the app.</span></span> <span data-ttu-id="627ec-166">A home page tem seu próprio contador que incrementa dez cada vez que o **Click me** botão é selecionado.</span><span class="sxs-lookup"><span data-stu-id="627ec-166">The homepage has its own counter that increments by ten each time the **Click me** button is selected.</span></span>

## <a name="next-steps"></a><span data-ttu-id="627ec-167">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="627ec-167">Next steps</span></span>

<xref:tutorials/first-razor-components-app>
