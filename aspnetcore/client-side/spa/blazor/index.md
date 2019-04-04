---
title: Introdução ao Blazor
author: guardrex
description: Explore o ASP.NET Core Blazor, uma nova maneira de criar aplicativos interativos do lado do cliente com o .NET que são executados no navegador com WebAssembly.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
uid: spa/blazor/index
ms.openlocfilehash: c42363b0ce70a1a8edc19b1c428ee802a6eff3dd
ms.sourcegitcommit: 3e9e1f6d572947e15347e818f769e27dea56b648
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2019
ms.locfileid: "58750485"
---
# <a name="introduction-to-blazor"></a><span data-ttu-id="54226-103">Introdução ao Blazor</span><span class="sxs-lookup"><span data-stu-id="54226-103">Introduction to Blazor</span></span>

<span data-ttu-id="54226-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="54226-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="54226-105">O Blazor é uma estrutura de aplicativos de página única para a criação de aplicativos Web interativos do lado do cliente com o .NET.</span><span class="sxs-lookup"><span data-stu-id="54226-105">Blazor is a single-page app framework for building interactive client-side Web apps with .NET.</span></span> <span data-ttu-id="54226-106">O Blazor usa padrões abertos da Web sem plug-ins ou transpilação de código.</span><span class="sxs-lookup"><span data-stu-id="54226-106">Blazor uses open web standards without plugins or code transpilation.</span></span> <span data-ttu-id="54226-107">O Blazor funciona em todos os navegadores da Web modernos, incluindo os navegadores móveis.</span><span class="sxs-lookup"><span data-stu-id="54226-107">Blazor works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="54226-108">Usar o .NET no navegador para desenvolvimento web do lado do cliente oferece muitas vantagens:</span><span class="sxs-lookup"><span data-stu-id="54226-108">Using .NET in the browser for client-side web development offers many advantages:</span></span>

* <span data-ttu-id="54226-109">**Linguagem C#**: escreva o código em C# em vez de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="54226-109">**C# language**: Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="54226-110">**Ecossistema do .NET**: aproveite o ecossistema existente das bibliotecas do .NET.</span><span class="sxs-lookup"><span data-stu-id="54226-110">**.NET Ecosystem**: Leverage the existing ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="54226-111">**Desenvolvimento de pilha completa**: compartilhe a lógica do lado do servidor e do cliente.</span><span class="sxs-lookup"><span data-stu-id="54226-111">**Full-stack development**: Share server and client-side logic.</span></span>
* <span data-ttu-id="54226-112">**Velocidade e escalabilidade**: o .NET foi criado para desempenho, confiabilidade e segurança.</span><span class="sxs-lookup"><span data-stu-id="54226-112">**Speed and scalability**: .NET was built for performance, reliability, and security.</span></span>
* <span data-ttu-id="54226-113">**Ferramentas líderes do setor**: mantenha-se produtivo com o Visual Studio no Windows, Linux e macOS.</span><span class="sxs-lookup"><span data-stu-id="54226-113">**Industry-leading tools**: Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="54226-114">**Estabilidade e consistência**:  Desenvolva um conjunto comum de linguagens, estruturas e ferramentas que são estáveis, com recursos avançados e fáceis de usar.</span><span class="sxs-lookup"><span data-stu-id="54226-114">**Stability and consistency**:  Build on a commonset of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

<span data-ttu-id="54226-115">A execução do código do .NET em navegadores da Web é possibilitada por [WebAssembly](http://webassembly.org) (abreviado como *wasm*).</span><span class="sxs-lookup"><span data-stu-id="54226-115">Running .NET code inside web browsers is made possible by [WebAssembly](http://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="54226-116">O WebAssembly é um padrão aberto da Web compatível com navegadores da Web sem plug-ins.</span><span class="sxs-lookup"><span data-stu-id="54226-116">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span> <span data-ttu-id="54226-117">O WebAssembly é um formato de código de bytes compacto, otimizado para download rápido e máxima velocidade de execução.</span><span class="sxs-lookup"><span data-stu-id="54226-117">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span>

<span data-ttu-id="54226-118">O código do WebAssembly pode acessar a funcionalidade completa do navegador por meio da interoperabilidade do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="54226-118">WebAssembly code can access the full functionality of the browser via JavaScript interop.</span></span> <span data-ttu-id="54226-119">Ao mesmo tempo, o código .NET executado pelo WebAssembly é executado na mesma área restrita confiável que o JavaScript para impedir ações mal-intencionadas no computador cliente.</span><span class="sxs-lookup"><span data-stu-id="54226-119">At the same time, .NET code executed via WebAssembly runs in the same trusted sandbox as JavaScript to prevent malicious actions on the client machine.</span></span>

![O Blazor executa o código do .NET no navegador com o WebAssembly.](index/_static/blazor.png)

<span data-ttu-id="54226-121">Quando um aplicativo Blazor é criado e executado em um navegador:</span><span class="sxs-lookup"><span data-stu-id="54226-121">When a Blazor app is built and run in a browser:</span></span>

* <span data-ttu-id="54226-122">os arquivos C# e os arquivos Razor são compilados em assemblies do .NET.</span><span class="sxs-lookup"><span data-stu-id="54226-122">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="54226-123">os assemblies e o tempo de execução do .NET são baixados no navegador.</span><span class="sxs-lookup"><span data-stu-id="54226-123">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="54226-124">O Blazor inicializa o tempo de execução do .NET e o configura para carregar os assemblies no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="54226-124">Blazor bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="54226-125">A manipulação do DOM (Modelo de Objeto do Documento) e as chamadas à API do navegador são realizadas pelo tempo de execução do Blazor por meio da interoperabilidade do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="54226-125">Document Object Model (DOM) manipulation and browser API calls are handled by the Blazor runtime via JavaScript interop.</span></span>

<span data-ttu-id="54226-126">O Blazor é compatível com instalações centrais exigidas pela maioria dos aplicativos, incluindo:</span><span class="sxs-lookup"><span data-stu-id="54226-126">Blazor supports core facilities required by most apps, including:</span></span>

* <span data-ttu-id="54226-127">Parâmetros</span><span class="sxs-lookup"><span data-stu-id="54226-127">Parameters</span></span>
* <span data-ttu-id="54226-128">Manipulação de eventos</span><span class="sxs-lookup"><span data-stu-id="54226-128">Event handling</span></span>
* <span data-ttu-id="54226-129">Associação de dados</span><span class="sxs-lookup"><span data-stu-id="54226-129">Data binding</span></span>
* <span data-ttu-id="54226-130">Roteamento</span><span class="sxs-lookup"><span data-stu-id="54226-130">Routing</span></span>
* <span data-ttu-id="54226-131">Injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="54226-131">Dependency injection</span></span>
* <span data-ttu-id="54226-132">Layouts</span><span class="sxs-lookup"><span data-stu-id="54226-132">Layouts</span></span>
* <span data-ttu-id="54226-133">Modelagem</span><span class="sxs-lookup"><span data-stu-id="54226-133">Templating</span></span>
* <span data-ttu-id="54226-134">Valores em cascata</span><span class="sxs-lookup"><span data-stu-id="54226-134">Cascading values</span></span>

<span data-ttu-id="54226-135">Para reduzir o tamanho do código não utilizado do aplicativo baixado, retirado do aplicativo quando publicado pelo [Vinculador de linguagem intermediária (IL)](xref:host-and-deploy/razor-components/configure-linker).</span><span class="sxs-lookup"><span data-stu-id="54226-135">To reduce the size of the downloaded app unused code stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/razor-components/configure-linker).</span></span>

<span data-ttu-id="54226-136">O Blazor é o modelo de hospedagem do lado do cliente para Componentes Razor.</span><span class="sxs-lookup"><span data-stu-id="54226-136">Blazor is the client-side hosting model for Razor Components.</span></span> <span data-ttu-id="54226-137">Como os Componentes Razor desvinculam a lógica de renderização de um componente da maneira de aplicar as atualizações da interface do usuário, há flexibilidade na maneira de hospedar os Componentes Razor.</span><span class="sxs-lookup"><span data-stu-id="54226-137">Because Razor Components decouple a component's rendering logic from how UI updates are applied, there's flexibility in how Razor Components can be hosted.</span></span> <span data-ttu-id="54226-138">Use os Componentes Razor do ASP.NET Core para a hospedagem dos Componentes Razor no servidor, em um aplicativo ASP.NET Core, em que as atualizações da interface do usuário são realizadas por uma conexão SignalR.</span><span class="sxs-lookup"><span data-stu-id="54226-138">Use ASP.NET Core Razor Components to host Razor Components on the server in an ASP.NET Core app where UI updates are handled over a SignalR connection.</span></span> <span data-ttu-id="54226-139">Para obter mais informações, consulte <xref:razor-components/index> e <xref:razor-components/hosting-models#server-side-hosting-model>.</span><span class="sxs-lookup"><span data-stu-id="54226-139">For more information, see <xref:razor-components/index> and <xref:razor-components/hosting-models#server-side-hosting-model>.</span></span> 

## <a name="components"></a><span data-ttu-id="54226-140">Componentes</span><span class="sxs-lookup"><span data-stu-id="54226-140">Components</span></span>

<span data-ttu-id="54226-141">Um *Componente Razor* é uma parte da interface do usuário, como uma página, uma caixa de diálogo ou um formulário de entrada de dados.</span><span class="sxs-lookup"><span data-stu-id="54226-141">A *Razor Component* is a piece of UI, such as a page, dialog, or data entry form.</span></span> <span data-ttu-id="54226-142">Os componentes manuseiam eventos de usuário e definem a lógica flexível de renderização de interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="54226-142">Components handle user events and define flexible UI rendering logic.</span></span> <span data-ttu-id="54226-143">Os componentes podem ser aninhados e reutilizados.</span><span class="sxs-lookup"><span data-stu-id="54226-143">Components can be nested and reused.</span></span>

<span data-ttu-id="54226-144">Os componentes são classes do .NET compiladas em assemblies do .NET que podem ser compartilhados e distribuídos como pacotes NuGet.</span><span class="sxs-lookup"><span data-stu-id="54226-144">Components are .NET classes built into .NET assemblies that can be shared and distributed as NuGet packages.</span></span> <span data-ttu-id="54226-145">A classe pode ser escrita na forma de uma página de marcação do Razor (*.cshtml*) ou como uma classe C# (*.cs*).</span><span class="sxs-lookup"><span data-stu-id="54226-145">The class can either be written in the form of a Razor markup page (*.cshtml*) or as a C# class (*.cs*).</span></span>

<span data-ttu-id="54226-146">O [Razor](xref:mvc/views/razor) é uma sintaxe para a combinação da marcação HTML com o código C#.</span><span class="sxs-lookup"><span data-stu-id="54226-146">[Razor](xref:mvc/views/razor) is a syntax for combining HTML markup with C# code.</span></span> <span data-ttu-id="54226-147">O Razor foi projetado visando a produtividade do desenvolvedor, permitindo que ele mude entre a marcação e o C# no mesmo arquivo com o suporte do [IntelliSense](/visualstudio/ide/using-intellisense).</span><span class="sxs-lookup"><span data-stu-id="54226-147">Razor is designed for developer productivity, allowing the developer to switch between markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="54226-148">As Razor Pages e as exibições MVC também usam o Razor.</span><span class="sxs-lookup"><span data-stu-id="54226-148">Razor Pages and MVC views also use Razor.</span></span> <span data-ttu-id="54226-149">Ao contrário das Razor Pages e das exibições MVC, que são criadas ao redor de um modelo de solicitação/resposta, os componentes são usados especificamente para manusear a composição da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="54226-149">Unlike Razor Pages and MVC views, which are built around a request/response model, components are used specifically for handling UI composition.</span></span> <span data-ttu-id="54226-150">Os Componentes Razor podem ser usados especificamente para composição e lógica de interface do usuário do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="54226-150">Razor Components can be used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="54226-151">A marcação a seguir é um exemplo de um componente de caixa de diálogo personalizada em um arquivo Razor (*DialogComponent.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="54226-151">The following markup is an example of a custom dialog component in a Razor file (*DialogComponent.cshtml*):</span></span>

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

<span data-ttu-id="54226-152">Quando esse componente é usado em outro lugar no aplicativo, o IntelliSense acelera o desenvolvimento com o preenchimento de sintaxe e de parâmetro.</span><span class="sxs-lookup"><span data-stu-id="54226-152">When this component is used elsewhere in the app, IntelliSense speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="54226-153">Os componentes são renderizados em uma representação na memória do DOM do navegador chamada *árvore de renderização*, que pode ser usada para atualizar a interface do usuário de maneira flexível e eficiente.</span><span class="sxs-lookup"><span data-stu-id="54226-153">Components render into an in-memory representation of the browser DOM called a *render tree* that can then be used to update the UI in a flexible and efficient way.</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="54226-154">Interoperabilidade do JavaScript</span><span class="sxs-lookup"><span data-stu-id="54226-154">JavaScript interop</span></span>

<span data-ttu-id="54226-155">Para aplicativos que exigem bibliotecas JavaScript e APIs do navegador de terceiros, o Blazor interopera com o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="54226-155">For apps that require third-party JavaScript libraries and browser APIs, Blazor interoperates with JavaScript.</span></span> <span data-ttu-id="54226-156">Os componentes são capazes de usar qualquer biblioteca ou API que o JavaScript possa usar.</span><span class="sxs-lookup"><span data-stu-id="54226-156">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="54226-157">O código C# pode chamar o código JavaScript, e o código JavaScript pode chamar o código C#.</span><span class="sxs-lookup"><span data-stu-id="54226-157">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="54226-158">Para obter mais informações, confira [Interoperabilidade do JavaScript](xref:razor-components/javascript-interop).</span><span class="sxs-lookup"><span data-stu-id="54226-158">For more information, see [JavaScript interop](xref:razor-components/javascript-interop).</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="54226-159">Compartilhamento de código e o .NET Standard</span><span class="sxs-lookup"><span data-stu-id="54226-159">Code sharing and .NET Standard</span></span>

<span data-ttu-id="54226-160">Os aplicativos podem referenciar e usar as bibliotecas [.NET Standard](/dotnet/standard/net-standard) existentes.</span><span class="sxs-lookup"><span data-stu-id="54226-160">Apps can reference and use existing [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="54226-161">O .NET Standard é uma especificação formal das APIs do .NET que são comuns entre as implementações do .NET.</span><span class="sxs-lookup"><span data-stu-id="54226-161">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="54226-162">Há suporte para o .NET standard 2.0 ou superior.</span><span class="sxs-lookup"><span data-stu-id="54226-162">.NET Standard 2.0 or higher is supported.</span></span> <span data-ttu-id="54226-163">As APIs que não são aplicáveis em um navegador da Web (por exemplo, para acessar o sistema de arquivos, abrir um soquete, threading e outros recursos) geram a <xref:System.PlatformNotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="54226-163">APIs that aren't applicable inside a web browser (for example, accessing the file system, opening a socket, threading, and other features) throw <xref:System.PlatformNotSupportedException>.</span></span> <span data-ttu-id="54226-164">As bibliotecas de classes do .NET Standard podem ser compartilhadas entre o código do servidor e em aplicativos baseados em navegador.</span><span class="sxs-lookup"><span data-stu-id="54226-164">.NET Standard class libraries can be shared across server code and in browser-based apps.</span></span>

## <a name="optimization"></a><span data-ttu-id="54226-165">Otimização</span><span class="sxs-lookup"><span data-stu-id="54226-165">Optimization</span></span>

<span data-ttu-id="54226-166">O tamanho do conteúdo é um fator de desempenho crítico para a usabilidade do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="54226-166">Payload size is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="54226-167">O Blazor otimiza o tamanho do conteúdo para reduzir os tempos de download:</span><span class="sxs-lookup"><span data-stu-id="54226-167">Blazor optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="54226-168">As partes não usadas de assemblies do .NET são removidas durante o processo de compilação.</span><span class="sxs-lookup"><span data-stu-id="54226-168">Unused parts of .NET assemblies are removed during the build process.</span></span>
* <span data-ttu-id="54226-169">As respostas HTTP são compactadas.</span><span class="sxs-lookup"><span data-stu-id="54226-169">HTTP responses are compressed.</span></span>
* <span data-ttu-id="54226-170">O tempo de execução do .NET e os assemblies são armazenados em cache no navegador.</span><span class="sxs-lookup"><span data-stu-id="54226-170">The .NET runtime and assemblies are cached in the browser.</span></span>

<span data-ttu-id="54226-171">Os [Componentes Razor do lado servidor](xref:razor-components/index) fornecem um tamanho de conteúdo ainda menor do que o Blazor mantendo os assemblies do .NET, o assembly do aplicativo e o lado do servidor do tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="54226-171">[Server-side Razor Components](xref:razor-components/index) provides an even smaller payload size than Blazor by maintaining .NET assemblies, the app's assembly, and the runtime server-side.</span></span> <span data-ttu-id="54226-172">Os aplicativos de Componentes Razor só oferecem arquivos de marcação e ativos estáticos para os clientes.</span><span class="sxs-lookup"><span data-stu-id="54226-172">Razor Components apps only serve markup files and static assets to clients.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="54226-173">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="54226-173">Additional resources</span></span>

* <xref:razor-components/index>
* [<span data-ttu-id="54226-174">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="54226-174">WebAssembly</span></span>](http://webassembly.org/)
* [<span data-ttu-id="54226-175">Guia do C#</span><span class="sxs-lookup"><span data-stu-id="54226-175">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="54226-176">HTML</span><span class="sxs-lookup"><span data-stu-id="54226-176">HTML</span></span>](https://www.w3.org/html/)
