---
title: Introdução ao Razor componentes
author: guardrex
description: Saiba como começar com os componentes do Razor, criando e modificando um projeto de componentes do Razor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: razor-components/get-started
ms.openlocfilehash: 151e58497b0f22fa7c5a9bde1f665eeb73fd5dc3
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59515342"
---
# <a name="get-started-with-razor-components"></a><span data-ttu-id="bd5dd-103">Introdução ao Razor componentes</span><span class="sxs-lookup"><span data-stu-id="bd5dd-103">Get started with Razor Components</span></span>

<span data-ttu-id="bd5dd-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bd5dd-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

# [<a name="visual-studio"></a><span data-ttu-id="bd5dd-105">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd5dd-105">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bd5dd-106">Pré-requisitos:</span><span class="sxs-lookup"><span data-stu-id="bd5dd-106">Prerequisites:</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

<span data-ttu-id="bd5dd-107">Para criar seu primeiro projeto de componentes do Razor no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="bd5dd-107">To create your first Razor Components project in Visual Studio:</span></span>

1. <span data-ttu-id="bd5dd-108">Instale o versão mais recente [SDK de visualização do .NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0) de versão.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-108">Install the latest [.NET Core 3.0 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>
1. <span data-ttu-id="bd5dd-109">Habilite o Visual Studio para usar SDKs de visualização:</span><span class="sxs-lookup"><span data-stu-id="bd5dd-109">Enable Visual Studio to use preview SDKs:</span></span>
   1. <span data-ttu-id="bd5dd-110">Abra **ferramentas** > **opções** na barra de menus.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-110">Open **Tools** > **Options** in the menu bar.</span></span>
   1. <span data-ttu-id="bd5dd-111">Abra o **projetos e soluções** nó.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-111">Open the **Projects and Solutions** node.</span></span> <span data-ttu-id="bd5dd-112">Abra o **.NET Core** guia.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-112">Open the **.NET Core** tab.</span></span>
   1. <span data-ttu-id="bd5dd-113">Marque a caixa **Use versões prévias do SDK do .NET Core**.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-113">Check the box for **Use previews of the .NET Core SDK**.</span></span> <span data-ttu-id="bd5dd-114">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-114">Select **OK**.</span></span>
1. <span data-ttu-id="bd5dd-115">Crie um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-115">Create a new project.</span></span>
1. <span data-ttu-id="bd5dd-116">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-116">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="bd5dd-117">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-117">Select **Next**.</span></span>
1. <span data-ttu-id="bd5dd-118">Forneça um nome na **nome do projeto** campo.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-118">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="bd5dd-119">Confirme se o **local** entrada está correta ou fornecer um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-119">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="bd5dd-120">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-120">Select **Create**.</span></span>
1. <span data-ttu-id="bd5dd-121">Certifique-se **.NET Core** e **ASP.NET Core 3.0** são selecionados na parte superior.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-121">Make sure **.NET Core** and **ASP.NET Core 3.0** are selected at the top.</span></span>
1. <span data-ttu-id="bd5dd-122">Escolha o **componentes do Razor** modelo e selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-122">Choose the **Razor Components** template and select **Create**.</span></span>
1. <span data-ttu-id="bd5dd-123">Pressione **F5** para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-123">Press **F5** to run the app.</span></span>

<span data-ttu-id="bd5dd-124">Parabéns!</span><span class="sxs-lookup"><span data-stu-id="bd5dd-124">Congratulations!</span></span> <span data-ttu-id="bd5dd-125">Você acaba de executar seu primeiro aplicativo de componentes do Razor!</span><span class="sxs-lookup"><span data-stu-id="bd5dd-125">You just ran your first Razor Components app!</span></span>

<!--

# [Visual Studio Code](#tab/visual-studio-code)

Prerequisites:

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

To create your first Razor Components project in Visual Studio Code:

1. Execute the following command from a command shell:

   ```console
   dotnet new razorcomponents -o WebApplication1
   ```

1. Open the *WebApplication1* folder in Visual Studio Code.

1. Add a *.vscode* folder.

1. Add a *tasks.json* file to the *.vscode* folder with the following content:

   [!code-json[](get-started/samples_snapshot/3.x/tasks.json)]

1. Add a *launch.json* file to the *.vscode* folder with the following content:

   [!code-json[](get-started/samples_snapshot/3.x/launch.json)]

1. Execute the app using the Visual Studio Code debugger.

1. In a browser, navigate to `https://localhost:5001`.

Congratulations! You just ran your first Razor Components app!

# [Visual Studio for Mac](#tab/visual-studio-mac)

.NET Core 3.0 will be supported with Visual Studio for Mac version 8.0 or later. Visual Studio for Mac version 8.0 Preview isn't available at this time.

Use the [.NET Core CLI version of this topic](xref:razor-components/get-started?tabs=netcore-cli) on macOS.

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

To create your first project Razor Components project in Visual Studio for Mac:

1. Select **File** > **New Solution** or **New Project**.
1. In the sidebar, select **.NET Core** > **App**.
1. Select **ASP.NET Core Razor Components** and select **Next**.
1. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.
1. In the **Project Name** field, enter `WebApplication1`. Select **Create**.
1. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

Congratulations! You just ran your first Razor Components app!
-->

# [<a name="net-core-cli"></a><span data-ttu-id="bd5dd-126">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="bd5dd-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="bd5dd-127">Pré-requisitos:</span><span class="sxs-lookup"><span data-stu-id="bd5dd-127">Prerequisites:</span></span>

* [<span data-ttu-id="bd5dd-128">.NET core SDK 3.0 versão prévia</span><span class="sxs-lookup"><span data-stu-id="bd5dd-128">.NET Core SDK 3.0 Preview</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

1. <span data-ttu-id="bd5dd-129">Para criar seu primeiro projeto de componentes do Razor de um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="bd5dd-129">To create your first Razor Components project from a command shell:</span></span>

   ```console
   dotnet new razorcomponents -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. <span data-ttu-id="bd5dd-130">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-130">In a browser, navigate to `https://localhost:5001`.</span></span>

<span data-ttu-id="bd5dd-131">Parabéns!</span><span class="sxs-lookup"><span data-stu-id="bd5dd-131">Congratulations!</span></span> <span data-ttu-id="bd5dd-132">Você acaba de executar seu primeiro aplicativo de componentes do Razor!</span><span class="sxs-lookup"><span data-stu-id="bd5dd-132">You just ran your first Razor Components app!</span></span>

---

## <a name="razor-components-project"></a><span data-ttu-id="bd5dd-133">Projeto de componentes do Razor</span><span class="sxs-lookup"><span data-stu-id="bd5dd-133">Razor Components project</span></span>

<span data-ttu-id="bd5dd-134">Componentes do Razor são criados usando a sintaxe do Razor, mas são compilados de maneira diferente de exibições de páginas do Razor e MVC.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-134">Razor Components are authored using Razor syntax but are compiled differently than Razor Pages and MVC views.</span></span> <span data-ttu-id="bd5dd-135">O *.razor* extensão de arquivo é usada para especificar um componente do Razor.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-135">The *.razor* file extension is used to specify a Razor Component.</span></span> <span data-ttu-id="bd5dd-136">Páginas do Razor e MVC exibições continuam a usar o *. cshtml* extensão de arquivo.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-136">Razor Pages and MVC views continue to use the *.cshtml* file extension.</span></span>

> [!NOTE]
> <span data-ttu-id="bd5dd-137">Componentes do Razor podem ser criados usando o *. cshtml* extensão de arquivo, desde que esses arquivos são identificados como arquivos de componente do Razor usando o `_RazorComponentInclude` propriedade do MSBuild.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-137">Razor Components can be authored using the *.cshtml* file extension as long as those files are identified as Razor Component files using the `_RazorComponentInclude` MSBuild property.</span></span> <span data-ttu-id="bd5dd-138">Por exemplo, um aplicativo criado usando o modelo de componente do Razor Especifica que todos os *. cshtml* arquivos sob o *componentes* pasta deve ser tratada como componentes do Razor:</span><span class="sxs-lookup"><span data-stu-id="bd5dd-138">For example, an app created using the Razor Component template specifies that all *.cshtml* files under the *Components* folder should be treated as Razor Components:</span></span>
>
> ```xml
> <_RazorComponentInclude>Components\**\*.cshtml</_RazorComponentInclude>
> ```

<span data-ttu-id="bd5dd-139">Quando o aplicativo é executado, várias páginas estão disponíveis nas guias na barra lateral:</span><span class="sxs-lookup"><span data-stu-id="bd5dd-139">When the app is run, multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="bd5dd-140">Home</span><span class="sxs-lookup"><span data-stu-id="bd5dd-140">Home</span></span>
* <span data-ttu-id="bd5dd-141">Contador</span><span class="sxs-lookup"><span data-stu-id="bd5dd-141">Counter</span></span>
* <span data-ttu-id="bd5dd-142">Buscar dados</span><span class="sxs-lookup"><span data-stu-id="bd5dd-142">Fetch data</span></span>

<span data-ttu-id="bd5dd-143">Na página Contador, selecione o botão **Clique aqui** para incrementar o contador sem uma atualização de página.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-143">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="bd5dd-144">A incrementação de um contador em uma página da Web normalmente exige JavaScript, mas o Razor Components fornece uma abordagem melhor usando C#.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-144">Incrementing a counter in a webpage normally requires writing JavaScript, but Razor Components provides a better approach using C#.</span></span>

<span data-ttu-id="bd5dd-145">*WebApplication1/Components/Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="bd5dd-145">*WebApplication1/Components/Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor)]

<span data-ttu-id="bd5dd-146">Uma solicitação para `/counter` no navegador, conforme especificado pelo `@page` diretiva na parte superior, faz com que o componente de contador processar seu conteúdo.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-146">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the Counter component to render its content.</span></span> <span data-ttu-id="bd5dd-147">Componentes processam em uma representação na memória da árvore de renderização que pode ser usado para atualizar a interface do usuário de uma maneira flexível e eficiente.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-147">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="bd5dd-148">Cada vez que o **Click me** botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="bd5dd-148">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="bd5dd-149">O `onclick` evento é disparado.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-149">The `onclick` event is fired.</span></span>
* <span data-ttu-id="bd5dd-150">O método `IncrementCount` é chamado.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-150">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="bd5dd-151">O `currentCount` é incrementado.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-151">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="bd5dd-152">O componente é renderizado novamente.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-152">The component is rendered again.</span></span>

<span data-ttu-id="bd5dd-153">O tempo de execução compara o novo conteúdo para o conteúdo anterior e só se aplica o conteúdo alterado para modelo de objeto de documento (DOM).</span><span class="sxs-lookup"><span data-stu-id="bd5dd-153">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="bd5dd-154">Adicione um componente para outro componente usando uma sintaxe semelhante ao HTML.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-154">Add a component to another component using an HTML-like syntax.</span></span> <span data-ttu-id="bd5dd-155">Componente são especificados usando atributos ou conteúdo filho.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-155">Component parameters are specified using attributes or child content.</span></span> <span data-ttu-id="bd5dd-156">Por exemplo, um componente de contador pode ser adicionado a homepage do aplicativo adicionando um `<Counter />` elemento para o componente do índice.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-156">For example, a Counter component can be added to the app's homepage by adding a `<Counter />` element to the Index component.</span></span>

<span data-ttu-id="bd5dd-157">*WebApplication1/Components/Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="bd5dd-157">*WebApplication1/Components/Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="bd5dd-158">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-158">Run the app.</span></span> <span data-ttu-id="bd5dd-159">A home page tem seu próprio contador.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-159">The homepage has its own counter.</span></span>

<span data-ttu-id="bd5dd-160">Para adicionar um parâmetro para o componente de contador, atualize o componente `@functions` bloco:</span><span class="sxs-lookup"><span data-stu-id="bd5dd-160">To add a parameter to the Counter component, update the component's `@functions` block:</span></span>

* <span data-ttu-id="bd5dd-161">Adicionar uma propriedade para `IncrementAmount` decorada com o `[Parameter]` atributo.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-161">Add a property for `IncrementAmount` decorated with the `[Parameter]` attribute.</span></span>
* <span data-ttu-id="bd5dd-162">Altere o método `IncrementCount` para usar o `IncrementAmount` ao aumentar o valor de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-162">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="bd5dd-163">*WebApplication1/Components/Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="bd5dd-163">*WebApplication1/Components/Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=4,8)]

<span data-ttu-id="bd5dd-164">Especifique um parâmetro `IncrementAmount` no elemento `<Counter>` do componente Home usando um atributo.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-164">Specify an `IncrementAmount` parameter in the Home component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="bd5dd-165">*WebApplication1/Components/Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="bd5dd-165">*WebApplication1/Components/Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor)]

<span data-ttu-id="bd5dd-166">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-166">Run the app.</span></span> <span data-ttu-id="bd5dd-167">A home page tem seu próprio contador que incrementa dez cada vez que o **Click me** botão é selecionado.</span><span class="sxs-lookup"><span data-stu-id="bd5dd-167">The homepage has its own counter that increments by ten each time the **Click me** button is selected.</span></span>

## <a name="next-steps"></a><span data-ttu-id="bd5dd-168">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="bd5dd-168">Next steps</span></span>

<xref:tutorials/first-razor-components-app>
