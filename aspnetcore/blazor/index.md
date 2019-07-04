---
title: Introdução ao Blazor no ASP.NET Core
author: guardrex
description: Explore o Blazor no ASP.NET Core, uma maneira de criar a IU da Web interativa do lado do cliente com o .NET em um aplicativo ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 07/01/2019
uid: blazor/index
ms.openlocfilehash: dbfadf9481cf16279e2a491bc04f51058e1d8a9c
ms.sourcegitcommit: eb3e51d58dd713eefc242148f45bd9486be3a78a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67500425"
---
# <a name="introduction-to-blazor"></a><span data-ttu-id="593ab-103">Introdução ao Blazor</span><span class="sxs-lookup"><span data-stu-id="593ab-103">Introduction to Blazor</span></span>

<span data-ttu-id="593ab-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="593ab-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="593ab-105">*Bem-vindo ao Blazor!*</span><span class="sxs-lookup"><span data-stu-id="593ab-105">*Welcome to Blazor!*</span></span>

<span data-ttu-id="593ab-106">O Blazor é uma estrutura para criar uma interface do usuário web interativa do lado do cliente com o .NET:</span><span class="sxs-lookup"><span data-stu-id="593ab-106">Blazor is a framework for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="593ab-107">Crie interfaces do usuário interativas avançadas usando C# em vez de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="593ab-107">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="593ab-108">Compartilhe a lógica de aplicativo do lado do cliente e do servidor gravada no .NET.</span><span class="sxs-lookup"><span data-stu-id="593ab-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="593ab-109">Renderize a interface do usuário, como HTML e CSS para suporte amplo de navegadores, incluindo navegadores móveis.</span><span class="sxs-lookup"><span data-stu-id="593ab-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="593ab-110">Usar o .NET para desenvolvimento web do lado do cliente oferece as seguintes vantagens:</span><span class="sxs-lookup"><span data-stu-id="593ab-110">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="593ab-111">escreva o código em C# em vez de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="593ab-111">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="593ab-112">Aproveite o ecossistema .NET existente das bibliotecas .NET.</span><span class="sxs-lookup"><span data-stu-id="593ab-112">Leverage the existing .NET ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="593ab-113">Compartilhe a lógica de aplicativo entre o servidor e o cliente.</span><span class="sxs-lookup"><span data-stu-id="593ab-113">Share app logic across server and client.</span></span>
* <span data-ttu-id="593ab-114">Beneficie-se com o desempenho, confiabilidade e segurança do .NET.</span><span class="sxs-lookup"><span data-stu-id="593ab-114">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="593ab-115">mantenha-se produtivo com o Visual Studio no Windows, Linux e macOS.</span><span class="sxs-lookup"><span data-stu-id="593ab-115">Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="593ab-116">Crie um conjunto comum de linguagens, estruturas e ferramentas que são estáveis, com recursos avançados e fáceis de usar.</span><span class="sxs-lookup"><span data-stu-id="593ab-116">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="593ab-117">Componentes</span><span class="sxs-lookup"><span data-stu-id="593ab-117">Components</span></span>

<span data-ttu-id="593ab-118">Os aplicativos Blazor se baseiam em *componentes*.</span><span class="sxs-lookup"><span data-stu-id="593ab-118">Blazor apps are based on *components*.</span></span> <span data-ttu-id="593ab-119">Um componente no Blazor é um elemento da interface do usuário, como uma página, uma caixa de diálogo ou um formulário de entrada de dados.</span><span class="sxs-lookup"><span data-stu-id="593ab-119">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="593ab-120">Os componentes são classes do .NET incorporadas a assemblies .NET que:</span><span class="sxs-lookup"><span data-stu-id="593ab-120">Components are .NET classes built into .NET assemblies that:</span></span>

* <span data-ttu-id="593ab-121">Definem a lógica de renderização da interface de usuário flexível.</span><span class="sxs-lookup"><span data-stu-id="593ab-121">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="593ab-122">Tratam eventos do usuário.</span><span class="sxs-lookup"><span data-stu-id="593ab-122">Handle user events.</span></span>
* <span data-ttu-id="593ab-123">Podem ser aninhados e reutilizados.</span><span class="sxs-lookup"><span data-stu-id="593ab-123">Can be nested and reused.</span></span>
* <span data-ttu-id="593ab-124">Podem ser compartilhados e distribuído como [bibliotecas de classes Razor](xref:razor-pages/ui-class) ou [pacotes do NuGet](/nuget/what-is-nuget).</span><span class="sxs-lookup"><span data-stu-id="593ab-124">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="593ab-125">A classe do componente geralmente é gravada na forma de uma página de marcação do [Razor](xref:mvc/views/razor) com uma extensão de arquivo *.razor*.</span><span class="sxs-lookup"><span data-stu-id="593ab-125">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a *.razor* file extension.</span></span> <span data-ttu-id="593ab-126">Os componentes no Blazor são formalmente chamados de *componentes do Razor*.</span><span class="sxs-lookup"><span data-stu-id="593ab-126">Components in Blazor are formally referred to as *Razor components*.</span></span> <span data-ttu-id="593ab-127">O Razor é uma sintaxe para a combinação de marcação HTML com o código C# projetada para melhorar a produtividade do desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="593ab-127">Razor is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="593ab-128">O Razor permite que você alterne entre a marcação HTML e C# no mesmo arquivo com suporte [IntelliSense](/visualstudio/ide/using-intellisense).</span><span class="sxs-lookup"><span data-stu-id="593ab-128">Razor allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="593ab-129">As Razor Pages e MVC também usam o Razor.</span><span class="sxs-lookup"><span data-stu-id="593ab-129">Razor Pages and MVC also use Razor.</span></span> <span data-ttu-id="593ab-130">Ao contrário das Razor Pages e das MVC, que são criadas ao redor de um modelo de solicitação/resposta, os componentes são usados especificamente para composição e lógica da interface do usuário do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="593ab-130">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="593ab-131">A seguinte marcação Razor demonstra um componente (*Dialog.razor*), que pode ser aninhado dentro de outro componente:</span><span class="sxs-lookup"><span data-stu-id="593ab-131">The following Razor markup demonstrates a component (*Dialog.razor*), which can be nested within another component:</span></span>

```cshtml
<div>
    <h1>@Title</h1>

    @ChildContent

    <button @onclick="@OnYes">Yes!</button>
</div>

@code {
    [Parameter]
    private string Title { get; set; }

    [Parameter]
    private RenderFragment ChildContent { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button was selected.");
    }
}
```

<span data-ttu-id="593ab-132">O conteúdo do corpo da caixa de diálogo (`ChildContent`) e o título (`Title`) são fornecidos pelo componente que usa esse componente em sua interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="593ab-132">The dialog's body content (`ChildContent`) and title (`Title`) are provided by the component that uses this component in its UI.</span></span> <span data-ttu-id="593ab-133">`OnYes` é um método C# disparado pelo evento `onclick` do botão.</span><span class="sxs-lookup"><span data-stu-id="593ab-133">`OnYes` is a C# method triggered by the button's `onclick` event.</span></span>

<span data-ttu-id="593ab-134">O Blazor usa marcações HTML naturais para composição de interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="593ab-134">Blazor uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="593ab-135">Os elementos HTML especificam componentes e os atributos da marcação transmitem valores para as propriedades de um componente.</span><span class="sxs-lookup"><span data-stu-id="593ab-135">HTML elements specify components, and a tag's attributes pass values to a component's properties.</span></span>

<span data-ttu-id="593ab-136">No exemplo a seguir, o componente `Index` usa o componente `Dialog`.</span><span class="sxs-lookup"><span data-stu-id="593ab-136">In the following example, the `Index` component uses the `Dialog` component.</span></span> <span data-ttu-id="593ab-137">`ChildContent` e `Title` são definidos pelos atributos e pelo conteúdo do elemento `<Dialog>`.</span><span class="sxs-lookup"><span data-stu-id="593ab-137">`ChildContent` and `Title` are set by the attributes and content of the `<Dialog>` element.</span></span>

<span data-ttu-id="593ab-138">*Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="593ab-138">*Index.razor*:</span></span>

```cshtml
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="593ab-139">A caixa de diálogo é renderizada quando o pai (*Index.razor*) é acessado em um navegador:</span><span class="sxs-lookup"><span data-stu-id="593ab-139">The dialog is rendered when the parent (*Index.razor*) is accessed in a browser:</span></span>

![Componente da caixa de diálogo renderizada no navegador](index/_static/dialog.png)

<span data-ttu-id="593ab-141">Quando esse componente é usado no aplicativo, o IntelliSense no [Visual Studio](/visualstudio/ide/using-intellisense) e no [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) acelera o desenvolvimento com o preenchimento de sintaxe e de parâmetro.</span><span class="sxs-lookup"><span data-stu-id="593ab-141">When this component is used in the app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) and [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="593ab-142">Os componentes são renderizados em uma representação na memória do Modelo de Objeto do Documento (DOM) do navegador chamada *árvore de renderização*, que é usada para atualizar a interface do usuário de maneira flexível e eficiente.</span><span class="sxs-lookup"><span data-stu-id="593ab-142">Components render into an in-memory representation of the browser's Document Object Model (DOM) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## <a name="blazor-client-side"></a><span data-ttu-id="593ab-143">Blazor no lado do cliente</span><span class="sxs-lookup"><span data-stu-id="593ab-143">Blazor client-side</span></span>

<span data-ttu-id="593ab-144">O Blazor no lado do cliente é uma estrutura de aplicativos de página única para a criação de aplicativos Web interativos do lado do cliente com o .NET.</span><span class="sxs-lookup"><span data-stu-id="593ab-144">Blazor client-side is a single-page app framework for building interactive client-side web apps with .NET.</span></span> <span data-ttu-id="593ab-145">O Blazor do lado do cliente usa padrões Web abertos sem plugins ou transpilação de código e funciona em todos os navegadores modernos, inclusive os móveis.</span><span class="sxs-lookup"><span data-stu-id="593ab-145">Blazor client-side uses open web standards without plugins or code transpilation and works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="593ab-146">A execução do código do .NET em navegadores da Web é possibilitada por [WebAssembly](http://webassembly.org) (abreviado como *wasm*).</span><span class="sxs-lookup"><span data-stu-id="593ab-146">Running .NET code inside web browsers is made possible by [WebAssembly](http://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="593ab-147">O WebAssembly é um formato de código de bytes compacto, otimizado para download rápido e máxima velocidade de execução.</span><span class="sxs-lookup"><span data-stu-id="593ab-147">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="593ab-148">O WebAssembly é um padrão aberto da Web compatível com navegadores da Web sem plug-ins.</span><span class="sxs-lookup"><span data-stu-id="593ab-148">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="593ab-149">O código WebAssembly pode acessar a funcionalidade completa do navegador por meio de JavaScript, chamado *Interoperabilidade do JavaScript* (ou *Interop do JavaScript*).</span><span class="sxs-lookup"><span data-stu-id="593ab-149">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* (or *JavaScript interop*).</span></span> <span data-ttu-id="593ab-150">O código .NET executado por meio da WebAssembly no navegador é executado na área restrita do JavaScript do navegador com as proteções que a área restrita oferece contra ações mal intencionadas no computador cliente.</span><span class="sxs-lookup"><span data-stu-id="593ab-150">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

![O Blazor do lado do cliente executa o código do .NET no navegador com o WebAssembly.](index/_static/blazor-client-side.png)

<span data-ttu-id="593ab-152">Quando um aplicativo Blazor no lado do cliente é criado e executado em um navegador:</span><span class="sxs-lookup"><span data-stu-id="593ab-152">When a Blazor client-side app is built and run in a browser:</span></span>

* <span data-ttu-id="593ab-153">os arquivos C# e os arquivos Razor são compilados em assemblies do .NET.</span><span class="sxs-lookup"><span data-stu-id="593ab-153">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="593ab-154">os assemblies e o tempo de execução do .NET são baixados no navegador.</span><span class="sxs-lookup"><span data-stu-id="593ab-154">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="593ab-155">O Blazor no lado do cliente inicializa o tempo de execução do .NET e o configura para carregar os assemblies no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="593ab-155">Blazor client-side bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="593ab-156">O tempo de execução do Blazor do lado do cliente usa a interoperabilidade de JavaScript para manipular as chamadas de API do navegador e as manipulações DOM.</span><span class="sxs-lookup"><span data-stu-id="593ab-156">The Blazor client-side runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="593ab-157">O tamanho do aplicativo publicado, seu *tamanho de payload*, é um fator de desempenho crítico para a utilidade do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="593ab-157">The size of the published app, its *payload size*, is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="593ab-158">Um aplicativo grande leva um tempo relativamente longo para baixar para um navegador, o que afeta a experiência do usuário.</span><span class="sxs-lookup"><span data-stu-id="593ab-158">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> <span data-ttu-id="593ab-159">O Blazor no lado do cliente otimiza o tamanho do conteúdo para reduzir o tempo de download:</span><span class="sxs-lookup"><span data-stu-id="593ab-159">Blazor client-side optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="593ab-160">O código não utilizado é retirado do aplicativo quando publicado pelo [Vinculador de linguagem intermediária (IL)](xref:host-and-deploy/blazor/configure-linker).</span><span class="sxs-lookup"><span data-stu-id="593ab-160">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/blazor/configure-linker).</span></span>
* <span data-ttu-id="593ab-161">As respostas HTTP são compactadas.</span><span class="sxs-lookup"><span data-stu-id="593ab-161">HTTP responses are compressed.</span></span>
* <span data-ttu-id="593ab-162">O tempo de execução do .NET e os assemblies são armazenados em cache no navegador.</span><span class="sxs-lookup"><span data-stu-id="593ab-162">The .NET runtime and assemblies are cached in the browser.</span></span>

## <a name="blazor-server-side"></a><span data-ttu-id="593ab-163">Blazor no lado do servidor</span><span class="sxs-lookup"><span data-stu-id="593ab-163">Blazor server-side</span></span>

<span data-ttu-id="593ab-164">Os componentes Blazor desvinculam a lógica de renderização do componente da forma como as atualizações da interface do usuário são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="593ab-164">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="593ab-165">O Blazor no lado do servidor dá suporte à hospedagem de componentes Razor no servidor em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="593ab-165">Blazor server-side provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="593ab-166">As atualizações da interface do usuário são tratadas por uma conexão [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="593ab-166">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="593ab-167">O tempo de execução realiza o envio de eventos da interface do usuário do navegador para o servidor e executar os componentes, aplica as atualizações na interface do usuário retornadas pelo servidor ao navegador.</span><span class="sxs-lookup"><span data-stu-id="593ab-167">The runtime handles sending UI events from the browser to the server and applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="593ab-168">A conexão usada pelo Blazor no lado do servidor para se comunicar com o navegador também é usada para manusear chamadas de interoperabilidade de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="593ab-168">The connection used by Blazor server-side to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![O Blazor no lado do servidor executa o código do .NET no servidor e interage com o Modelo de Objeto do Documento no cliente por uma conexão SignalR](index/_static/blazor-server-side.png)

## <a name="javascript-interop"></a><span data-ttu-id="593ab-170">Interoperabilidade do JavaScript</span><span class="sxs-lookup"><span data-stu-id="593ab-170">JavaScript interop</span></span>

<span data-ttu-id="593ab-171">Para aplicativos que exigem bibliotecas JavaScript e acesso a APIs do navegador de terceiros, os componentes interoperam com o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="593ab-171">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="593ab-172">Os componentes são capazes de usar qualquer biblioteca ou API que o JavaScript possa usar.</span><span class="sxs-lookup"><span data-stu-id="593ab-172">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="593ab-173">O código C# pode chamar o código JavaScript, e o código JavaScript pode chamar o código C#.</span><span class="sxs-lookup"><span data-stu-id="593ab-173">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="593ab-174">Para obter mais informações, consulte <xref:blazor/javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="593ab-174">For more information, see <xref:blazor/javascript-interop>.</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="593ab-175">Compartilhamento de código e o .NET Standard</span><span class="sxs-lookup"><span data-stu-id="593ab-175">Code sharing and .NET Standard</span></span>

<span data-ttu-id="593ab-176">O Blazor implementa o [.NET Standard 2.0](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="593ab-176">Blazor implements [.NET Standard 2.0](/dotnet/standard/net-standard).</span></span> <span data-ttu-id="593ab-177">O .NET Standard é uma especificação formal das APIs do .NET que são comuns entre as implementações do .NET.</span><span class="sxs-lookup"><span data-stu-id="593ab-177">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="593ab-178">As bibliotecas de classe do .NET Standard podem ser compartilhadas entre diferentes plataformas do .NET, como Blazor, .NET Framework, .NET Core, Xamarin, Mono e Unity.</span><span class="sxs-lookup"><span data-stu-id="593ab-178">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="593ab-179">As APIs que não são aplicáveis em um navegador da Web (por exemplo, para acessar o sistema de arquivos, abrir um soquete e threading) geram a <xref:System.PlatformNotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="593ab-179">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="593ab-180">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="593ab-180">Additional resources</span></span>

* [<span data-ttu-id="593ab-181">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="593ab-181">WebAssembly</span></span>](http://webassembly.org/)
* <xref:blazor/hosting-models>
* [<span data-ttu-id="593ab-182">Guia do C#</span><span class="sxs-lookup"><span data-stu-id="593ab-182">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="593ab-183">HTML</span><span class="sxs-lookup"><span data-stu-id="593ab-183">HTML</span></span>](https://www.w3.org/html/)
