---
title: Introdução ao Blazor no ASP.NET Core
author: guardrex
description: Explore o Blazor no ASP.NET Core, uma maneira de criar a IU da Web interativa do lado do cliente com o .NET em um aplicativo ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: seoapril2019
ms.date: 04/18/2019
uid: blazor/index
ms.openlocfilehash: 74eeb003c249fc9ff8267ac855455f875806ccd9
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59982991"
---
# <a name="introduction-to-blazor"></a><span data-ttu-id="b3eb1-103">Introdução ao Blazor</span><span class="sxs-lookup"><span data-stu-id="b3eb1-103">Introduction to Blazor</span></span>

<span data-ttu-id="b3eb1-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b3eb1-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b3eb1-105">Bem-vindo ao Blazor!</span><span class="sxs-lookup"><span data-stu-id="b3eb1-105">Welcome to Blazor!</span></span>

<span data-ttu-id="b3eb1-106">Crie a interface do usuário da Web interativa do lado do cliente com o .NET:</span><span class="sxs-lookup"><span data-stu-id="b3eb1-106">Build interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="b3eb1-107">Crie interfaces do usuário interativas avançadas usando C# em vez de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-107">Build rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="b3eb1-108">Compartilhe a lógica de aplicativo do lado do cliente e do servidor gravada com o .NET.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-108">Share server-side and client-side app logic written with .NET.</span></span>
* <span data-ttu-id="b3eb1-109">Renderize a interface do usuário, como HTML e CSS para suporte amplo de navegadores, incluindo navegadores móveis.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="b3eb1-110">O Blazor é compatível com cenários centrais exigidos pela maioria dos aplicativos:</span><span class="sxs-lookup"><span data-stu-id="b3eb1-110">Blazor supports core scenarios required by most apps:</span></span>

* <span data-ttu-id="b3eb1-111">Parâmetros</span><span class="sxs-lookup"><span data-stu-id="b3eb1-111">Parameters</span></span>
* <span data-ttu-id="b3eb1-112">Manipulação de eventos</span><span class="sxs-lookup"><span data-stu-id="b3eb1-112">Event handling</span></span>
* <span data-ttu-id="b3eb1-113">Associação de dados</span><span class="sxs-lookup"><span data-stu-id="b3eb1-113">Data binding</span></span>
* <span data-ttu-id="b3eb1-114">Roteamento</span><span class="sxs-lookup"><span data-stu-id="b3eb1-114">Routing</span></span>
* <span data-ttu-id="b3eb1-115">Injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="b3eb1-115">Dependency injection</span></span>
* <span data-ttu-id="b3eb1-116">Layouts</span><span class="sxs-lookup"><span data-stu-id="b3eb1-116">Layouts</span></span>
* <span data-ttu-id="b3eb1-117">Modelos</span><span class="sxs-lookup"><span data-stu-id="b3eb1-117">Templates</span></span>
* <span data-ttu-id="b3eb1-118">Valores em cascata</span><span class="sxs-lookup"><span data-stu-id="b3eb1-118">Cascading values</span></span>

## <a name="components"></a><span data-ttu-id="b3eb1-119">Componentes</span><span class="sxs-lookup"><span data-stu-id="b3eb1-119">Components</span></span>

<span data-ttu-id="b3eb1-120">Um *componente* no Blazor é um elemento da interface do usuário, como uma página, uma caixa de diálogo ou um formulário de entrada de dados.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-120">A *component* in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span> <span data-ttu-id="b3eb1-121">Os componentes manuseiam eventos de usuário e definem a lógica flexível de renderização de interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-121">Components handle user events and define flexible UI rendering logic.</span></span> <span data-ttu-id="b3eb1-122">Os componentes podem ser aninhados e reutilizados.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-122">Components can be nested and reused.</span></span>

<span data-ttu-id="b3eb1-123">Os componentes são classes do .NET compiladas em assemblies do .NET que podem ser compartilhados e distribuídos como pacotes NuGet.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-123">Components are .NET classes built into .NET assemblies that can be shared and distributed as NuGet packages.</span></span> <span data-ttu-id="b3eb1-124">A classe do componente geralmente é gravada na forma de uma página de marcação do Razor com uma extensão de arquivo *.razor*.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-124">The component class is usually written in the form of a Razor markup page with a *.razor* file extension.</span></span> <span data-ttu-id="b3eb1-125">Componentes no Blazor às vezes são chamados de componentes do Razor.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-125">Components in Blazor are sometimes referred to as Razor components.</span></span>

<span data-ttu-id="b3eb1-126">O [Razor](xref:mvc/views/razor) é uma sintaxe para a combinação da marcação HTML com o código C#.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-126">[Razor](xref:mvc/views/razor) is a syntax for combining HTML markup with C# code.</span></span> <span data-ttu-id="b3eb1-127">O Razor foi projetado visando a produtividade do desenvolvedor, permitindo que ele mude entre a marcação e o C# no mesmo arquivo com o suporte do [IntelliSense](/visualstudio/ide/using-intellisense).</span><span class="sxs-lookup"><span data-stu-id="b3eb1-127">Razor is designed for developer productivity, allowing the developer to switch between markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="b3eb1-128">As Razor Pages e as exibições MVC também usam o Razor.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-128">Razor Pages and MVC views also use Razor.</span></span> <span data-ttu-id="b3eb1-129">Ao contrário das Razor Pages e das exibições MVC, que são criadas ao redor de um modelo de solicitação/resposta, os componentes são usados especificamente para manusear a composição da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-129">Unlike Razor Pages and MVC views, which are built around a request/response model, components are used specifically for handling UI composition.</span></span> <span data-ttu-id="b3eb1-130">Os componentes Razor podem ser usados especificamente para composição e lógica de interface do usuário do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-130">Razor components can be used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="b3eb1-131">A marcação a seguir é um exemplo de um componente de caixa de diálogo personalizada:</span><span class="sxs-lookup"><span data-stu-id="b3eb1-131">The following markup is an example of a custom dialog component:</span></span>

```cshtml
<div>
    <h2>@Title</h2>
    @BodyContent
    <button onclick="@OnOK">OK</button>
</div>

@functions {
    public string Title { get; set; }
    public RenderFragment BodyContent { get; set; }
    public Action OnOK { get; set; }
}
```

<span data-ttu-id="b3eb1-132">Quando esse componente é usado em outro lugar no aplicativo, o IntelliSense no [Visual Studio](https://visualstudio.microsoft.com/vs/) acelera o desenvolvimento com o preenchimento de sintaxe e de parâmetro.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-132">When this component is used elsewhere in the app, IntelliSense in [Visual Studio](https://visualstudio.microsoft.com/vs/) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="b3eb1-133">Os componentes são renderizados em uma representação na memória do DOM do navegador chamada *árvore de renderização*, que pode ser usada para atualizar a interface do usuário de maneira flexível e eficiente.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-133">Components render into an in-memory representation of the browser DOM called a *render tree* that can then be used to update the UI in a flexible and efficient way.</span></span>

## <a name="blazor-server-side"></a><span data-ttu-id="b3eb1-134">Blazor no lado do servidor</span><span class="sxs-lookup"><span data-stu-id="b3eb1-134">Blazor server-side</span></span>

<span data-ttu-id="b3eb1-135">Os componentes Blazor desvinculam a lógica de renderização do componente da forma como as atualizações da interface do usuário são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-135">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="b3eb1-136">O Blazor no lado do servidor dá suporte à hospedagem de componentes Razor no servidor em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-136">Blazor server-side provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="b3eb1-137">As atualizações da interface do usuário são tratadas por uma conexão SignalR.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-137">UI updates are handled over a SignalR connection.</span></span>

<span data-ttu-id="b3eb1-138">O tempo de execução:</span><span class="sxs-lookup"><span data-stu-id="b3eb1-138">The runtime:</span></span>

* <span data-ttu-id="b3eb1-139">Trata do envio de eventos de interface do usuário do navegador para o servidor.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-139">Handles sending UI events from the browser to the server.</span></span>
* <span data-ttu-id="b3eb1-140">Aplica as atualizações da interface do usuário enviadas pelo servidor de volta para o navegador depois de executar os componentes.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-140">Applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="b3eb1-141">A conexão usada pelo Blazor no lado do servidor para se comunicar com o navegador também é usada para manusear chamadas de interoperabilidade de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-141">The connection used by Blazor server-side to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![O Blazor no lado do servidor executa o código do .NET no servidor e interage com o Modelo de Objeto do Documento no cliente por uma conexão SignalR](index/_static/blazor-server-side.png)

<span data-ttu-id="b3eb1-143">Para obter mais informações, consulte <xref:blazor/hosting-models#server-side>.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-143">For more information, see <xref:blazor/hosting-models#server-side>.</span></span>

## <a name="blazor-client-side"></a><span data-ttu-id="b3eb1-144">Blazor no lado do cliente</span><span class="sxs-lookup"><span data-stu-id="b3eb1-144">Blazor client-side</span></span>

<span data-ttu-id="b3eb1-145">O Blazor no lado do cliente é uma estrutura de aplicativos de página única para a criação de aplicativos Web interativos do lado do cliente com o .NET.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-145">Blazor client-side is a single-page app framework for building interactive client-side Web apps with .NET.</span></span> <span data-ttu-id="b3eb1-146">O Blazor no lado do cliente usa padrões abertos da Web sem plug-ins ou transpilação de código.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-146">Blazor client-side uses open web standards without plugins or code transpilation.</span></span> <span data-ttu-id="b3eb1-147">O Blazor no lado do cliente funciona em todos os navegadores da Web modernos, incluindo os navegadores móveis.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-147">Blazor client-side works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="b3eb1-148">Usar o .NET no navegador para desenvolvimento web do lado do cliente oferece muitas vantagens:</span><span class="sxs-lookup"><span data-stu-id="b3eb1-148">Using .NET in the browser for client-side web development offers many advantages:</span></span>

* <span data-ttu-id="b3eb1-149">**Linguagem C#**: escreva o código em C# em vez de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-149">**C# language**: Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="b3eb1-150">**Ecossistema do .NET**: aproveite o ecossistema existente das bibliotecas do .NET.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-150">**.NET Ecosystem**: Leverage the existing ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="b3eb1-151">**Desenvolvimento de pilha completa**: compartilhe a lógica do lado do servidor e do cliente.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-151">**Full-stack development**: Share server and client-side logic.</span></span>
* <span data-ttu-id="b3eb1-152">**Velocidade e escalabilidade**: o .NET foi criado para desempenho, confiabilidade e segurança.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-152">**Speed and scalability**: .NET was built for performance, reliability, and security.</span></span>
* <span data-ttu-id="b3eb1-153">**Ferramentas líderes do setor**: mantenha-se produtivo com o Visual Studio no Windows, Linux e macOS.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-153">**Industry-leading tools**: Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="b3eb1-154">**Estabilidade e consistência**:  Crie um conjunto comum de linguagens, estruturas e ferramentas que são estáveis, com recursos avançados e fáceis de usar.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-154">**Stability and consistency**:  Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

<span data-ttu-id="b3eb1-155">A execução do código do .NET em navegadores da Web é possibilitada por [WebAssembly](http://webassembly.org) (abreviado como *wasm*).</span><span class="sxs-lookup"><span data-stu-id="b3eb1-155">Running .NET code inside web browsers is made possible by [WebAssembly](http://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="b3eb1-156">O WebAssembly é um padrão aberto da Web compatível com navegadores da Web sem plug-ins.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-156">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span> <span data-ttu-id="b3eb1-157">O WebAssembly é um formato de código de bytes compacto, otimizado para download rápido e máxima velocidade de execução.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-157">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span>

<span data-ttu-id="b3eb1-158">O código do WebAssembly pode acessar a funcionalidade completa do navegador por meio da interoperabilidade do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-158">WebAssembly code can access the full functionality of the browser via JavaScript interop.</span></span> <span data-ttu-id="b3eb1-159">Ao mesmo tempo, o código .NET executado pelo WebAssembly é executado na mesma área restrita confiável que o JavaScript para impedir ações mal-intencionadas no computador cliente.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-159">At the same time, .NET code executed via WebAssembly runs in the same trusted sandbox as JavaScript to prevent malicious actions on the client machine.</span></span>

![O Blazor do lado do cliente executa o código do .NET no navegador com o WebAssembly.](index/_static/blazor-client-side.png)

<span data-ttu-id="b3eb1-161">Quando um aplicativo Blazor no lado do cliente é criado e executado em um navegador:</span><span class="sxs-lookup"><span data-stu-id="b3eb1-161">When a Blazor client-side app is built and run in a browser:</span></span>

* <span data-ttu-id="b3eb1-162">os arquivos C# e os arquivos Razor são compilados em assemblies do .NET.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-162">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="b3eb1-163">os assemblies e o tempo de execução do .NET são baixados no navegador.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-163">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="b3eb1-164">O Blazor no lado do cliente inicializa o tempo de execução do .NET e o configura para carregar os assemblies no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-164">Blazor client-side bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="b3eb1-165">A manipulação do DOM (Modelo de Objeto do Documento) e as chamadas à API do navegador são realizadas pelo tempo de execução do Blazor no lado do cliente por meio da interoperabilidade do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-165">Document Object Model (DOM) manipulation and browser API calls are handled by the Blazor client-side runtime via JavaScript interop.</span></span>

<span data-ttu-id="b3eb1-166">Para reduzir o tamanho do aplicativo baixado, o código não utilizado é retirado do aplicativo quando publicado pelo [Vinculador de linguagem intermediária (IL)](xref:host-and-deploy/blazor/configure-linker).</span><span class="sxs-lookup"><span data-stu-id="b3eb1-166">To reduce the size of the downloaded app, unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/blazor/configure-linker).</span></span>

<span data-ttu-id="b3eb1-167">O lado do cliente Blazor é um modelo de hospedagem do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-167">Blazor client-side is a client-side hosting model.</span></span> <span data-ttu-id="b3eb1-168">Como o Blazor desvincula uma lógica de renderização de componente da maneira de aplicar as atualizações da interface do usuário, há flexibilidade na maneira de hospedar o Blazor.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-168">Because Blazor decouples a component's rendering logic from how UI updates are applied, there's flexibility in how Blazor can be hosted.</span></span> <span data-ttu-id="b3eb1-169">Use o [Blazor no lado do servidor](#blazor-server-side) para hospedar o Blazor no servidor em um aplicativo ASP.NET Core, em que as atualizações da interface do usuário são realizadas por uma conexão SignalR.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-169">Use [Blazor server-side](#blazor-server-side) to host Blazor on the server in an ASP.NET Core app where UI updates are handled over a SignalR connection.</span></span> <span data-ttu-id="b3eb1-170">Para obter mais informações, consulte <xref:blazor/hosting-models#server-side>.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-170">For more information, see <xref:blazor/hosting-models#server-side>.</span></span> 

<span data-ttu-id="b3eb1-171">O tamanho do conteúdo é um fator de desempenho crítico para a usabilidade do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-171">Payload size is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="b3eb1-172">O Blazor no lado do cliente otimiza o tamanho do conteúdo para reduzir o tempo de download:</span><span class="sxs-lookup"><span data-stu-id="b3eb1-172">Blazor client-side optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="b3eb1-173">As partes não usadas de assemblies do .NET são removidas durante o processo de compilação.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-173">Unused parts of .NET assemblies are removed during the build process.</span></span>
* <span data-ttu-id="b3eb1-174">As respostas HTTP são compactadas.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-174">HTTP responses are compressed.</span></span>
* <span data-ttu-id="b3eb1-175">O tempo de execução do .NET e os assemblies são armazenados em cache no navegador.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-175">The .NET runtime and assemblies are cached in the browser.</span></span>

<span data-ttu-id="b3eb1-176">O [Blazor do lado do servidor](#blazor-server-side) fornece um tamanho de conteúdo menor do que o Blazor no lado do cliente, mantendo os assemblies do .NET, o assembly do aplicativo e o lado do servidor do tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-176">[Blazor server-side](#blazor-server-side) provides a smaller payload size than Blazor client-side by maintaining .NET assemblies, the app's assembly, and the runtime server-side.</span></span> <span data-ttu-id="b3eb1-177">Os aplicativos do Blazor no lado do servidor só oferecem arquivos de marcação e ativos estáticos para os clientes.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-177">Blazor server-side apps only serve markup files and static assets to clients.</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="b3eb1-178">Interoperabilidade do JavaScript</span><span class="sxs-lookup"><span data-stu-id="b3eb1-178">JavaScript interop</span></span>

<span data-ttu-id="b3eb1-179">Para aplicativos que exigem bibliotecas JavaScript e APIs do navegador de terceiros, os componentes interoperam com o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-179">For apps that require third-party JavaScript libraries and browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="b3eb1-180">Os componentes são capazes de usar qualquer biblioteca ou API que o JavaScript possa usar.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-180">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="b3eb1-181">O código C# pode chamar o código JavaScript, e o código JavaScript pode chamar o código C#.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-181">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="b3eb1-182">Para obter mais informações, confira [Interoperabilidade do JavaScript](xref:blazor/javascript-interop).</span><span class="sxs-lookup"><span data-stu-id="b3eb1-182">For more information, see [JavaScript interop](xref:blazor/javascript-interop).</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="b3eb1-183">Compartilhamento de código e o .NET Standard</span><span class="sxs-lookup"><span data-stu-id="b3eb1-183">Code sharing and .NET Standard</span></span>

<span data-ttu-id="b3eb1-184">Os aplicativos podem referenciar e usar as bibliotecas [.NET Standard](/dotnet/standard/net-standard) existentes.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-184">Apps can reference and use existing [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="b3eb1-185">O .NET Standard é uma especificação formal das APIs do .NET que são comuns entre as implementações do .NET.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-185">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="b3eb1-186">O .Blazor implementa o .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-186">Blazor implements .NET Standard 2.0.</span></span> <span data-ttu-id="b3eb1-187">As APIs que não são aplicáveis em um navegador da Web (por exemplo, para acessar o sistema de arquivos, abrir um soquete, threading e outros recursos) geram a <xref:System.PlatformNotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-187">APIs that aren't applicable inside a web browser (for example, accessing the file system, opening a socket, threading, and other features) throw <xref:System.PlatformNotSupportedException>.</span></span> <span data-ttu-id="b3eb1-188">As bibliotecas de classe do .NET Standard podem ser compartilhadas entre diferentes plataformas do .NET, como Blazor, .NET Framework, .NET Core, Xamarin, Mono e Unity.</span><span class="sxs-lookup"><span data-stu-id="b3eb1-188">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b3eb1-189">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b3eb1-189">Additional resources</span></span>

* [<span data-ttu-id="b3eb1-190">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="b3eb1-190">WebAssembly</span></span>](http://webassembly.org/)
* [<span data-ttu-id="b3eb1-191">Guia do C#</span><span class="sxs-lookup"><span data-stu-id="b3eb1-191">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="b3eb1-192">HTML</span><span class="sxs-lookup"><span data-stu-id="b3eb1-192">HTML</span></span>](https://www.w3.org/html/)
