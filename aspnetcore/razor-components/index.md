---
title: Introdução aos Componentes Razor
author: guardrex
description: Explore os componentes do ASP.NET Core Razor, uma maneira de criar a IU da Web interativa do lado do cliente com o .NET em um aplicativo ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2019
uid: razor-components/index
ms.openlocfilehash: 04a73d33cee0deedaf3dc97395836a936b580fbd
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56159520"
---
# <a name="introduction-to-razor-components"></a><span data-ttu-id="7ebb6-103">Introdução aos Componentes Razor</span><span class="sxs-lookup"><span data-stu-id="7ebb6-103">Introduction to Razor Components</span></span>

<span data-ttu-id="7ebb6-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7ebb6-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7ebb6-105">Os *Componentes Razor* são uma maneira de criar a IU da Web interativa do lado do cliente com o .NET:</span><span class="sxs-lookup"><span data-stu-id="7ebb6-105">*Razor Components* is a way to build interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="7ebb6-106">Crie interfaces do usuário interativas avançadas usando C# em vez de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7ebb6-106">Build rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="7ebb6-107">Compartilhe a lógica de aplicativo do lado do cliente e do servidor toda escrita com o .NET.</span><span class="sxs-lookup"><span data-stu-id="7ebb6-107">Share server-side and client-side app logic all written with .NET.</span></span>
* <span data-ttu-id="7ebb6-108">Renderize a interface do usuário, como HTML e CSS para suporte amplo de navegadores, incluindo navegadores móveis.</span><span class="sxs-lookup"><span data-stu-id="7ebb6-108">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="7ebb6-109">Os Componentes Razor são compatíveis com instalações centrais exigidas pela maioria dos aplicativos, incluindo:</span><span class="sxs-lookup"><span data-stu-id="7ebb6-109">Razor Components supports core facilities required by most apps, including:</span></span>

* <span data-ttu-id="7ebb6-110">Parâmetros</span><span class="sxs-lookup"><span data-stu-id="7ebb6-110">Parameters</span></span>
* <span data-ttu-id="7ebb6-111">Manipulação de eventos</span><span class="sxs-lookup"><span data-stu-id="7ebb6-111">Event handling</span></span>
* <span data-ttu-id="7ebb6-112">Associação de dados</span><span class="sxs-lookup"><span data-stu-id="7ebb6-112">Data binding</span></span>
* <span data-ttu-id="7ebb6-113">Roteamento</span><span class="sxs-lookup"><span data-stu-id="7ebb6-113">Routing</span></span>
* <span data-ttu-id="7ebb6-114">Injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="7ebb6-114">Dependency injection</span></span>
* <span data-ttu-id="7ebb6-115">Layouts</span><span class="sxs-lookup"><span data-stu-id="7ebb6-115">Layouts</span></span>
* <span data-ttu-id="7ebb6-116">Modelagem</span><span class="sxs-lookup"><span data-stu-id="7ebb6-116">Templating</span></span>
* <span data-ttu-id="7ebb6-117">Valores em cascata</span><span class="sxs-lookup"><span data-stu-id="7ebb6-117">Cascading values</span></span>

<span data-ttu-id="7ebb6-118">Os Componentes Razor desvinculam a lógica de renderização do componente da forma como as atualizações da IU são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="7ebb6-118">Razor Components decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="7ebb6-119">Os Componentes Razor do ASP.NET Core no .NET Core 3.0 adicionam suporte à hospedagem de Componentes Razor no servidor em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ebb6-119">ASP.NET Core Razor Components in .NET Core 3.0 adds support for hosting Razor Components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="7ebb6-120">As atualizações da interface do usuário são tratadas por uma conexão SignalR.</span><span class="sxs-lookup"><span data-stu-id="7ebb6-120">UI updates are handled over a SignalR connection.</span></span>

<span data-ttu-id="7ebb6-121">O tempo de execução:</span><span class="sxs-lookup"><span data-stu-id="7ebb6-121">The runtime:</span></span>

* <span data-ttu-id="7ebb6-122">Trata do envio de eventos de interface do usuário do navegador para o servidor.</span><span class="sxs-lookup"><span data-stu-id="7ebb6-122">Handles sending UI events from the browser to the server.</span></span>
* <span data-ttu-id="7ebb6-123">Aplica as atualizações da interface do usuário enviadas pelo servidor de volta para o navegador depois de executar os componentes.</span><span class="sxs-lookup"><span data-stu-id="7ebb6-123">Applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="7ebb6-124">A conexão usada pelos Componentes Razor para se comunicarem com o navegador também é usada para manusear chamadas de interoperabilidade de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7ebb6-124">The connection used by Razor Components to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![Os Componentes Razor executam o código do .NET no servidor e interagem com o Modelo de Objeto do Documento no cliente por uma conexão SignalR](index/_static/aspnet-core-razor-components.png)

<span data-ttu-id="7ebb6-126">Para obter mais informações, consulte <xref:razor-components/hosting-models#server-side-hosting-model>.</span><span class="sxs-lookup"><span data-stu-id="7ebb6-126">For more information, see <xref:razor-components/hosting-models#server-side-hosting-model>.</span></span>

<span data-ttu-id="7ebb6-127">O *Blazor* é o modelo de hospedagem experimental do lado do cliente dos Componentes Razor.</span><span class="sxs-lookup"><span data-stu-id="7ebb6-127">*Blazor* is the experimental client-side hosting model of Razor Components.</span></span> <span data-ttu-id="7ebb6-128">O Blazor é executado em .NET no navegador usando padrões abertos da Web sem plug-ins ou transpilação de código.</span><span class="sxs-lookup"><span data-stu-id="7ebb6-128">Blazor runs on .NET in the browser using open web standards without plugins or code transpilation.</span></span> <span data-ttu-id="7ebb6-129">Para obter mais informações, consulte <xref:razor-components/hosting-models#client-side-hosting-model>.</span><span class="sxs-lookup"><span data-stu-id="7ebb6-129">For more information, see <xref:razor-components/hosting-models#client-side-hosting-model>.</span></span>

## <a name="components"></a><span data-ttu-id="7ebb6-130">Componentes</span><span class="sxs-lookup"><span data-stu-id="7ebb6-130">Components</span></span>

<span data-ttu-id="7ebb6-131">Um *Componente Razor* é uma parte da interface do usuário, como uma página, uma caixa de diálogo ou um formulário de entrada de dados.</span><span class="sxs-lookup"><span data-stu-id="7ebb6-131">A *Razor Component* is a piece of UI, such as a page, dialog, or data entry form.</span></span> <span data-ttu-id="7ebb6-132">Os componentes manuseiam eventos de usuário e definem a lógica flexível de renderização de interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="7ebb6-132">Components handle user events and define flexible UI rendering logic.</span></span> <span data-ttu-id="7ebb6-133">Os componentes podem ser aninhados e reutilizados.</span><span class="sxs-lookup"><span data-stu-id="7ebb6-133">Components can be nested and reused.</span></span>

<span data-ttu-id="7ebb6-134">Os componentes são classes do .NET compiladas em assemblies do .NET que podem ser compartilhados e distribuídos como pacotes NuGet.</span><span class="sxs-lookup"><span data-stu-id="7ebb6-134">Components are .NET classes built into .NET assemblies that can be shared and distributed as NuGet packages.</span></span> <span data-ttu-id="7ebb6-135">A classe pode ser gravada na forma de uma página de marcação do Razor (*.cshtml*) ou como uma classe C# (*.cs*).</span><span class="sxs-lookup"><span data-stu-id="7ebb6-135">The class can either be written in the form of a Razor markup page (*.cshtml*) or as a C# class (*.cs*).</span></span>

<span data-ttu-id="7ebb6-136">O [Razor](xref:mvc/views/razor) é uma sintaxe para a combinação da marcação HTML com o código C#.</span><span class="sxs-lookup"><span data-stu-id="7ebb6-136">[Razor](xref:mvc/views/razor) is a syntax for combining HTML markup with C# code.</span></span> <span data-ttu-id="7ebb6-137">O Razor foi projetado visando a produtividade do desenvolvedor, permitindo que ele mude entre a marcação e o C# no mesmo arquivo com o suporte do [IntelliSense](/visualstudio/ide/using-intellisense).</span><span class="sxs-lookup"><span data-stu-id="7ebb6-137">Razor is designed for developer productivity, allowing the developer to switch between markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="7ebb6-138">As Razor Pages e as exibições MVC também usam o Razor.</span><span class="sxs-lookup"><span data-stu-id="7ebb6-138">Razor Pages and MVC views also use Razor.</span></span> <span data-ttu-id="7ebb6-139">Ao contrário das Razor Pages e das exibições MVC, que são criadas ao redor de um modelo de solicitação/resposta, os componentes são usados especificamente para manusear a composição da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="7ebb6-139">Unlike Razor Pages and MVC views, which are built around a request/response model, components are used specifically for handling UI composition.</span></span> <span data-ttu-id="7ebb6-140">Os Componentes Razor podem ser usados especificamente para composição e lógica de interface do usuário do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="7ebb6-140">Razor Components can be used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="7ebb6-141">A marcação a seguir é um exemplo de um componente de caixa de diálogo personalizada em um arquivo Razor (*DialogComponent.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="7ebb6-141">The following markup is an example of a custom dialog component in a Razor file (*DialogComponent.cshtml*):</span></span>

```cshtml
<div>
    <h2>@Title</h2>
    @BodyContent
    <button onclick=@OnOK>OK</button>
</div>

@functions {
    public string Title { get; set; }
    public RenderFragment BodyContent { get; set; }
    public Action OnOK { get; set; }
}
```

<span data-ttu-id="7ebb6-142">Quando esse componente é usado em outro lugar no aplicativo, o IntelliSense acelera o desenvolvimento com o preenchimento de sintaxe e de parâmetro.</span><span class="sxs-lookup"><span data-stu-id="7ebb6-142">When this component is used elsewhere in the app, IntelliSense speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="7ebb6-143">Os componentes são renderizados em uma representação na memória do DOM do navegador chamada *árvore de renderização*, que pode ser usada para atualizar a interface do usuário de maneira flexível e eficiente.</span><span class="sxs-lookup"><span data-stu-id="7ebb6-143">Components render into an in-memory representation of the browser DOM called a *render tree* that can then be used to update the UI in a flexible and efficient way.</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="7ebb6-144">Interoperabilidade do JavaScript</span><span class="sxs-lookup"><span data-stu-id="7ebb6-144">JavaScript interop</span></span>

<span data-ttu-id="7ebb6-145">Para aplicativos que exigem bibliotecas JavaScript e APIs do navegador de terceiros, os componentes interoperam com o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7ebb6-145">For apps that require third-party JavaScript libraries and browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="7ebb6-146">Os componentes são capazes de usar qualquer biblioteca ou API que o JavaScript possa usar.</span><span class="sxs-lookup"><span data-stu-id="7ebb6-146">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="7ebb6-147">O código C# pode chamar o código JavaScript, e o código JavaScript pode chamar o código C#.</span><span class="sxs-lookup"><span data-stu-id="7ebb6-147">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="7ebb6-148">Para obter mais informações, confira [Interoperabilidade do JavaScript](xref:razor-components/javascript-interop).</span><span class="sxs-lookup"><span data-stu-id="7ebb6-148">For more information, see [JavaScript interop](xref:razor-components/javascript-interop).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7ebb6-149">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="7ebb6-149">Additional resources</span></span>

* <xref:spa/blazor/index>
* [<span data-ttu-id="7ebb6-150">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7ebb6-150">WebAssembly</span></span>](http://webassembly.org/)
* [<span data-ttu-id="7ebb6-151">Guia do C#</span><span class="sxs-lookup"><span data-stu-id="7ebb6-151">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="7ebb6-152">HTML</span><span class="sxs-lookup"><span data-stu-id="7ebb6-152">HTML</span></span>](https://www.w3.org/html/)
