---
title: Introdução ao Blazor
author: guardrex
description: Saiba como começar com Blazor criando e modificando um projeto Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2019
uid: spa/blazor/get-started
ms.openlocfilehash: 8c984bab8a13b4fc2d87fd1a7e0b285dfa25ba09
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56159566"
---
# <a name="get-started-with-blazor"></a><span data-ttu-id="82564-103">Introdução ao Blazor</span><span class="sxs-lookup"><span data-stu-id="82564-103">Get started with Blazor</span></span>

<span data-ttu-id="82564-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="82564-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="82564-105">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="82564-105">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="82564-106">Pré-requisitos:</span><span class="sxs-lookup"><span data-stu-id="82564-106">Prerequisites:</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

<span data-ttu-id="82564-107">Para criar seu primeiro projeto Blazor no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="82564-107">To create your first Blazor project in Visual Studio:</span></span>

1. <span data-ttu-id="82564-108">Instale o versão mais recente [extensão de serviços de linguagem Blazor](https://go.microsoft.com/fwlink/?linkid=870389) do Visual Studio Marketplace.</span><span class="sxs-lookup"><span data-stu-id="82564-108">Install the latest [Blazor Language Services extension](https://go.microsoft.com/fwlink/?linkid=870389) from the Visual Studio Marketplace.</span></span> <span data-ttu-id="82564-109">Esta etapa disponibiliza Blazor modelos para o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="82564-109">This step makes Blazor templates available to Visual Studio.</span></span>
1. <span data-ttu-id="82564-110">Verifique os modelos de Blazor disponíveis para uso com a CLI do .NET Core, executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="82564-110">Make the Blazor templates available for use with the .NET Core CLI by running the following command in a command shell:</span></span>

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates
   ```

1. <span data-ttu-id="82564-111">Selecione **arquivo** > **novo projeto** > **Web** > **aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="82564-111">Select **File** > **New Project** > **Web** > **ASP.NET Core Web Application**.</span></span>
1. <span data-ttu-id="82564-112">Certifique-se **.NET Core** e **ASP.NET Core 3.0** são selecionados na parte superior.</span><span class="sxs-lookup"><span data-stu-id="82564-112">Make sure **.NET Core** and **ASP.NET Core 3.0** are selected at the top.</span></span>
1. <span data-ttu-id="82564-113">Escolha o modelo **Blazor** e selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="82564-113">Choose the **Blazor** template and select **OK**.</span></span>
1. <span data-ttu-id="82564-114">Pressione **F5** para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="82564-114">Press **F5** to run the app.</span></span>

<span data-ttu-id="82564-115">Parabéns!</span><span class="sxs-lookup"><span data-stu-id="82564-115">Congratulations!</span></span> <span data-ttu-id="82564-116">Você acaba de executar seu primeiro aplicativo Blazor!</span><span class="sxs-lookup"><span data-stu-id="82564-116">You just ran your first Blazor app!</span></span>

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

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="82564-117">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="82564-117">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="82564-118">Pré-requisitos:</span><span class="sxs-lookup"><span data-stu-id="82564-118">Prerequisites:</span></span>

* [<span data-ttu-id="82564-119">.NET core SDK 3.0 versão prévia</span><span class="sxs-lookup"><span data-stu-id="82564-119">.NET Core SDK 3.0 Preview</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

1. <span data-ttu-id="82564-120">Adicione os modelos de Blazor, executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="82564-120">Add the Blazor templates by running the following command in a command shell:</span></span>

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates
   ```

1. <span data-ttu-id="82564-121">Crie seu primeiro projeto Blazor em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="82564-121">Create your first Blazor project in a command shell:</span></span>

   ```console
   dotnet new blazor -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. <span data-ttu-id="82564-122">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="82564-122">In a browser, navigate to `https://localhost:5001`.</span></span>

<span data-ttu-id="82564-123">Parabéns!</span><span class="sxs-lookup"><span data-stu-id="82564-123">Congratulations!</span></span> <span data-ttu-id="82564-124">Você acaba de executar seu primeiro aplicativo Blazor!</span><span class="sxs-lookup"><span data-stu-id="82564-124">You just ran your first Blazor app!</span></span>

---

## <a name="blazor-project"></a><span data-ttu-id="82564-125">Projeto Blazor</span><span class="sxs-lookup"><span data-stu-id="82564-125">Blazor project</span></span>

<span data-ttu-id="82564-126">Quando o aplicativo é executado, várias páginas estão disponíveis nas guias na barra lateral:</span><span class="sxs-lookup"><span data-stu-id="82564-126">When the app is run, multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="82564-127">Home</span><span class="sxs-lookup"><span data-stu-id="82564-127">Home</span></span>
* <span data-ttu-id="82564-128">Contador</span><span class="sxs-lookup"><span data-stu-id="82564-128">Counter</span></span>
* <span data-ttu-id="82564-129">Buscar dados</span><span class="sxs-lookup"><span data-stu-id="82564-129">Fetch data</span></span>

<span data-ttu-id="82564-130">Na página de contador, selecione a **Click me** botão incrementar o contador sem uma atualização de página.</span><span class="sxs-lookup"><span data-stu-id="82564-130">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="82564-131">Incrementar um contador em uma página da Web normalmente requer a gravação do JavaScript, mas Blazor fornece uma abordagem melhor usando C#.</span><span class="sxs-lookup"><span data-stu-id="82564-131">Incrementing a counter in a webpage normally requires writing JavaScript, but Blazor provides a better approach using C#.</span></span>

<span data-ttu-id="82564-132">*Pages/Counter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="82564-132">*Pages/Counter.cshtml*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.cshtml)]

<span data-ttu-id="82564-133">Uma solicitação para `/counter` no navegador, conforme especificado pelo `@page` diretiva na parte superior, faz com que o componente de contador processar seu conteúdo.</span><span class="sxs-lookup"><span data-stu-id="82564-133">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the Counter component to render its content.</span></span> <span data-ttu-id="82564-134">Componentes processam em uma representação na memória da árvore de renderização que pode ser usado para atualizar a interface do usuário de uma maneira flexível e eficiente.</span><span class="sxs-lookup"><span data-stu-id="82564-134">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="82564-135">Cada vez que o **Click me** botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="82564-135">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="82564-136">O `onclick` evento é disparado.</span><span class="sxs-lookup"><span data-stu-id="82564-136">The `onclick` event is fired.</span></span>
* <span data-ttu-id="82564-137">O método `IncrementCount` é chamado.</span><span class="sxs-lookup"><span data-stu-id="82564-137">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="82564-138">O `currentCount` é incrementado.</span><span class="sxs-lookup"><span data-stu-id="82564-138">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="82564-139">O componente é renderizado novamente.</span><span class="sxs-lookup"><span data-stu-id="82564-139">The component is rendered again.</span></span>

<span data-ttu-id="82564-140">O tempo de execução compara o novo conteúdo para o conteúdo anterior e só se aplica o conteúdo alterado para modelo de objeto de documento (DOM).</span><span class="sxs-lookup"><span data-stu-id="82564-140">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="82564-141">Adicione um componente para outro componente usando uma sintaxe semelhante ao HTML.</span><span class="sxs-lookup"><span data-stu-id="82564-141">Add a component to another component using an HTML-like syntax.</span></span> <span data-ttu-id="82564-142">Componente são especificados usando atributos ou conteúdo filho.</span><span class="sxs-lookup"><span data-stu-id="82564-142">Component parameters are specified using attributes or child content.</span></span> <span data-ttu-id="82564-143">Por exemplo, um componente de contador pode ser adicionado a homepage do aplicativo adicionando um `<Counter />` elemento para o componente do índice.</span><span class="sxs-lookup"><span data-stu-id="82564-143">For example, a Counter component can be added to the app's homepage by adding a `<Counter />` element to the Index component.</span></span>

<span data-ttu-id="82564-144">Na *Pages*, substitua o componente de solicitação de pesquisa com um componente de contador:</span><span class="sxs-lookup"><span data-stu-id="82564-144">In *Pages/Index.cshtml*, replace the Survey Prompt component with a Counter component:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.cshtml?highlight=7)]

<span data-ttu-id="82564-145">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="82564-145">Run the app.</span></span> <span data-ttu-id="82564-146">A home page tem seu próprio contador.</span><span class="sxs-lookup"><span data-stu-id="82564-146">The homepage has its own counter.</span></span>

<span data-ttu-id="82564-147">Para adicionar um parâmetro para o componente de contador, atualize o componente `@functions` bloco:</span><span class="sxs-lookup"><span data-stu-id="82564-147">To add a parameter to the Counter component, update the component's `@functions` block:</span></span>

* <span data-ttu-id="82564-148">Adicionar uma propriedade para `IncrementAmount` decorada com o `[Parameter]` atributo.</span><span class="sxs-lookup"><span data-stu-id="82564-148">Add a property for `IncrementAmount` decorated with the `[Parameter]` attribute.</span></span>
* <span data-ttu-id="82564-149">Alterar o `IncrementCount` método a ser usado o `IncrementAmount` ao aumentar o valor de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="82564-149">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="82564-150">*Pages/Counter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="82564-150">*Pages/Counter.cshtml*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.cshtml?highlight=4,8)]

<span data-ttu-id="82564-151">Especifique um `IncrementAmount` parâmetro no componente de Home `<Counter>` usando um atributo do elemento.</span><span class="sxs-lookup"><span data-stu-id="82564-151">Specify an `IncrementAmount` parameter in the Home component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="82564-152">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="82564-152">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.cshtml)]

<span data-ttu-id="82564-153">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="82564-153">Run the app.</span></span> <span data-ttu-id="82564-154">A home page tem seu próprio contador que incrementa dez cada vez que o **Click me** botão é selecionado.</span><span class="sxs-lookup"><span data-stu-id="82564-154">The homepage has its own counter that increments by ten each time the **Click me** button is selected.</span></span>

## <a name="next-steps"></a><span data-ttu-id="82564-155">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="82564-155">Next steps</span></span>

<xref:tutorials/first-razor-components-app>
