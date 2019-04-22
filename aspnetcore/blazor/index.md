---
title: Introdução ao Blazor no ASP.NET Core
author: guardrex
description: Explore o Blazor no ASP.NET Core, uma maneira de criar a IU da Web interativa do lado do cliente com o .NET em um aplicativo ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: seoapril2019
ms.date: 04/15/2019
uid: blazor/index
ms.openlocfilehash: a5b12a5c5c10a74ab192d0d67a2ba9a5617232c7
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614551"
---
# <a name="introduction-to-blazor"></a><span data-ttu-id="0adde-103">Introdução ao Blazor</span><span class="sxs-lookup"><span data-stu-id="0adde-103">Introduction to Blazor</span></span>

<span data-ttu-id="0adde-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0adde-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="razor-components"></a><span data-ttu-id="0adde-105">Componentes Razor</span><span class="sxs-lookup"><span data-stu-id="0adde-105">Razor Components</span></span>

<span data-ttu-id="0adde-106">O modelo de hospedagem no lado do cliente do Blazor, os *Componentes Razor*, é uma maneira de criar a IU da Web interativa do lado do cliente com o .NET:</span><span class="sxs-lookup"><span data-stu-id="0adde-106">The server-side hosting model of Blazor, *Razor Components*, is a way to build interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="0adde-107">Crie interfaces do usuário interativas avançadas usando C# em vez de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0adde-107">Build rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="0adde-108">Compartilhe a lógica de aplicativo do lado do cliente e do servidor toda escrita com o .NET.</span><span class="sxs-lookup"><span data-stu-id="0adde-108">Share server-side and client-side app logic all written with .NET.</span></span>
* <span data-ttu-id="0adde-109">Renderize a interface do usuário, como HTML e CSS para suporte amplo de navegadores, incluindo navegadores móveis.</span><span class="sxs-lookup"><span data-stu-id="0adde-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="0adde-110">Os Componentes Razor são compatíveis com instalações centrais exigidas pela maioria dos aplicativos, incluindo:</span><span class="sxs-lookup"><span data-stu-id="0adde-110">Razor Components supports core facilities required by most apps, including:</span></span>

* <span data-ttu-id="0adde-111">Parâmetros</span><span class="sxs-lookup"><span data-stu-id="0adde-111">Parameters</span></span>
* <span data-ttu-id="0adde-112">Manipulação de eventos</span><span class="sxs-lookup"><span data-stu-id="0adde-112">Event handling</span></span>
* <span data-ttu-id="0adde-113">Associação de dados</span><span class="sxs-lookup"><span data-stu-id="0adde-113">Data binding</span></span>
* <span data-ttu-id="0adde-114">Roteamento</span><span class="sxs-lookup"><span data-stu-id="0adde-114">Routing</span></span>
* <span data-ttu-id="0adde-115">Injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="0adde-115">Dependency injection</span></span>
* <span data-ttu-id="0adde-116">Layouts</span><span class="sxs-lookup"><span data-stu-id="0adde-116">Layouts</span></span>
* <span data-ttu-id="0adde-117">Modelagem</span><span class="sxs-lookup"><span data-stu-id="0adde-117">Templating</span></span>
* <span data-ttu-id="0adde-118">Valores em cascata</span><span class="sxs-lookup"><span data-stu-id="0adde-118">Cascading values</span></span>

<span data-ttu-id="0adde-119">Os Componentes Razor desvinculam a lógica de renderização do componente da forma como as atualizações da IU são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="0adde-119">Razor Components decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="0adde-120">Os Componentes Razor do ASP.NET Core no .NET Core 3.0 adicionam suporte à hospedagem de Componentes Razor no servidor em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0adde-120">ASP.NET Core Razor Components in .NET Core 3.0 adds support for hosting Razor Components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="0adde-121">As atualizações da interface do usuário são tratadas por uma conexão SignalR.</span><span class="sxs-lookup"><span data-stu-id="0adde-121">UI updates are handled over a SignalR connection.</span></span>

<span data-ttu-id="0adde-122">O tempo de execução:</span><span class="sxs-lookup"><span data-stu-id="0adde-122">The runtime:</span></span>

* <span data-ttu-id="0adde-123">Trata do envio de eventos de interface do usuário do navegador para o servidor.</span><span class="sxs-lookup"><span data-stu-id="0adde-123">Handles sending UI events from the browser to the server.</span></span>
* <span data-ttu-id="0adde-124">Aplica as atualizações da interface do usuário enviadas pelo servidor de volta para o navegador depois de executar os componentes.</span><span class="sxs-lookup"><span data-stu-id="0adde-124">Applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="0adde-125">A conexão usada pelos Componentes Razor para se comunicarem com o navegador também é usada para manusear chamadas de interoperabilidade de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0adde-125">The connection used by Razor Components to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![Os Componentes Razor executam o código do .NET no servidor e interagem com o Modelo de Objeto do Documento no cliente por uma conexão SignalR](index/_static/aspnet-core-razor-components.png)

<span data-ttu-id="0adde-127">Para obter mais informações, consulte <xref:blazor/hosting-models#server-side-hosting-model>.</span><span class="sxs-lookup"><span data-stu-id="0adde-127">For more information, see <xref:blazor/hosting-models#server-side-hosting-model>.</span></span>

## <a name="components"></a><span data-ttu-id="0adde-128">Componentes</span><span class="sxs-lookup"><span data-stu-id="0adde-128">Components</span></span>

<span data-ttu-id="0adde-129">Um *Componente Razor* é uma parte da interface do usuário, como uma página, uma caixa de diálogo ou um formulário de entrada de dados.</span><span class="sxs-lookup"><span data-stu-id="0adde-129">A *Razor Component* is a piece of UI, such as a page, dialog, or data entry form.</span></span> <span data-ttu-id="0adde-130">Os componentes manuseiam eventos de usuário e definem a lógica flexível de renderização de interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="0adde-130">Components handle user events and define flexible UI rendering logic.</span></span> <span data-ttu-id="0adde-131">Os componentes podem ser aninhados e reutilizados.</span><span class="sxs-lookup"><span data-stu-id="0adde-131">Components can be nested and reused.</span></span>

<span data-ttu-id="0adde-132">Os componentes são classes do .NET compiladas em assemblies do .NET que podem ser compartilhados e distribuídos como pacotes NuGet.</span><span class="sxs-lookup"><span data-stu-id="0adde-132">Components are .NET classes built into .NET assemblies that can be shared and distributed as NuGet packages.</span></span> <span data-ttu-id="0adde-133">A classe é normalmente gravada na forma de uma página de marcação do Razor com uma extensão de arquivo *.razor* (componentes do Razor) ou uma página de marcação do Razor com uma extensão de arquivo *. cshtml* (Blazor).</span><span class="sxs-lookup"><span data-stu-id="0adde-133">The class is normally written in the form of a Razor markup page with a *.razor* file extension (Razor Components) or Razor markup page with a *.cshtml* file extension (Blazor).</span></span>

<span data-ttu-id="0adde-134">O [Razor](xref:mvc/views/razor) é uma sintaxe para a combinação da marcação HTML com o código C#.</span><span class="sxs-lookup"><span data-stu-id="0adde-134">[Razor](xref:mvc/views/razor) is a syntax for combining HTML markup with C# code.</span></span> <span data-ttu-id="0adde-135">O Razor foi projetado visando a produtividade do desenvolvedor, permitindo que ele mude entre a marcação e o C# no mesmo arquivo com o suporte do [IntelliSense](/visualstudio/ide/using-intellisense).</span><span class="sxs-lookup"><span data-stu-id="0adde-135">Razor is designed for developer productivity, allowing the developer to switch between markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="0adde-136">As Razor Pages e as exibições MVC também usam o Razor.</span><span class="sxs-lookup"><span data-stu-id="0adde-136">Razor Pages and MVC views also use Razor.</span></span> <span data-ttu-id="0adde-137">Ao contrário das Razor Pages e das exibições MVC, que são criadas ao redor de um modelo de solicitação/resposta, os componentes são usados especificamente para manusear a composição da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="0adde-137">Unlike Razor Pages and MVC views, which are built around a request/response model, components are used specifically for handling UI composition.</span></span> <span data-ttu-id="0adde-138">Os Componentes Razor podem ser usados especificamente para composição e lógica de interface do usuário do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="0adde-138">Razor Components can be used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="0adde-139">A marcação a seguir é um exemplo de um componente de caixa de diálogo personalizada:</span><span class="sxs-lookup"><span data-stu-id="0adde-139">The following markup is an example of a custom dialog component:</span></span>

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

<span data-ttu-id="0adde-140">Quando esse componente é usado em outro lugar no aplicativo, o IntelliSense acelera o desenvolvimento com o preenchimento de sintaxe e de parâmetro.</span><span class="sxs-lookup"><span data-stu-id="0adde-140">When this component is used elsewhere in the app, IntelliSense speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="0adde-141">Os componentes são renderizados em uma representação na memória do DOM do navegador chamada *árvore de renderização*, que pode ser usada para atualizar a interface do usuário de maneira flexível e eficiente.</span><span class="sxs-lookup"><span data-stu-id="0adde-141">Components render into an in-memory representation of the browser DOM called a *render tree* that can then be used to update the UI in a flexible and efficient way.</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="0adde-142">Interoperabilidade do JavaScript</span><span class="sxs-lookup"><span data-stu-id="0adde-142">JavaScript interop</span></span>

<span data-ttu-id="0adde-143">Para aplicativos que exigem bibliotecas JavaScript e APIs do navegador de terceiros, os componentes interoperam com o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0adde-143">For apps that require third-party JavaScript libraries and browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="0adde-144">Os componentes são capazes de usar qualquer biblioteca ou API que o JavaScript possa usar.</span><span class="sxs-lookup"><span data-stu-id="0adde-144">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="0adde-145">O código C# pode chamar o código JavaScript, e o código JavaScript pode chamar o código C#.</span><span class="sxs-lookup"><span data-stu-id="0adde-145">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="0adde-146">Para obter mais informações, confira [Interoperabilidade do JavaScript](xref:blazor/javascript-interop).</span><span class="sxs-lookup"><span data-stu-id="0adde-146">For more information, see [JavaScript interop](xref:blazor/javascript-interop).</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="0adde-147">Compartilhamento de código e o .NET Standard</span><span class="sxs-lookup"><span data-stu-id="0adde-147">Code sharing and .NET Standard</span></span>

<span data-ttu-id="0adde-148">Os aplicativos podem referenciar e usar as bibliotecas [.NET Standard](/dotnet/standard/net-standard) existentes.</span><span class="sxs-lookup"><span data-stu-id="0adde-148">Apps can reference and use existing [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="0adde-149">O .NET Standard é uma especificação formal das APIs do .NET que são comuns entre as implementações do .NET.</span><span class="sxs-lookup"><span data-stu-id="0adde-149">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="0adde-150">O .Blazor implementa o .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="0adde-150">Blazor implements .NET Standard 2.0.</span></span> <span data-ttu-id="0adde-151">As APIs que não são aplicáveis em um navegador da Web (por exemplo, para acessar o sistema de arquivos, abrir um soquete, threading e outros recursos) geram a <xref:System.PlatformNotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="0adde-151">APIs that aren't applicable inside a web browser (for example, accessing the file system, opening a socket, threading, and other features) throw <xref:System.PlatformNotSupportedException>.</span></span> <span data-ttu-id="0adde-152">As bibliotecas de classe do .NET Standard podem ser compartilhadas entre diferentes plataformas do .NET, como Blazor, .NET Framework, .NET Core, Xamarin, Mono e Unity.</span><span class="sxs-lookup"><span data-stu-id="0adde-152">.NET Standard class libraries can be shared across different .NET platforms, like Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

## <a name="blazor"></a><span data-ttu-id="0adde-153">Blazor</span><span class="sxs-lookup"><span data-stu-id="0adde-153">Blazor</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="0adde-154">O Blazor é uma estrutura de aplicativos de página única para a criação de aplicativos Web interativos do lado do cliente com o .NET.</span><span class="sxs-lookup"><span data-stu-id="0adde-154">Blazor is a single-page app framework for building interactive client-side Web apps with .NET.</span></span> <span data-ttu-id="0adde-155">O Blazor usa padrões abertos da Web sem plug-ins ou transpilação de código.</span><span class="sxs-lookup"><span data-stu-id="0adde-155">Blazor uses open web standards without plugins or code transpilation.</span></span> <span data-ttu-id="0adde-156">O Blazor funciona em todos os navegadores da Web modernos, incluindo os navegadores móveis.</span><span class="sxs-lookup"><span data-stu-id="0adde-156">Blazor works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="0adde-157">Usar o .NET no navegador para desenvolvimento web do lado do cliente oferece muitas vantagens:</span><span class="sxs-lookup"><span data-stu-id="0adde-157">Using .NET in the browser for client-side web development offers many advantages:</span></span>

* <span data-ttu-id="0adde-158">**Linguagem C#**: escreva o código em C# em vez de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0adde-158">**C# language**: Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="0adde-159">**Ecossistema do .NET**: aproveite o ecossistema existente das bibliotecas do .NET.</span><span class="sxs-lookup"><span data-stu-id="0adde-159">**.NET Ecosystem**: Leverage the existing ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="0adde-160">**Desenvolvimento de pilha completa**: compartilhe a lógica do lado do servidor e do cliente.</span><span class="sxs-lookup"><span data-stu-id="0adde-160">**Full-stack development**: Share server and client-side logic.</span></span>
* <span data-ttu-id="0adde-161">**Velocidade e escalabilidade**: o .NET foi criado para desempenho, confiabilidade e segurança.</span><span class="sxs-lookup"><span data-stu-id="0adde-161">**Speed and scalability**: .NET was built for performance, reliability, and security.</span></span>
* <span data-ttu-id="0adde-162">**Ferramentas líderes do setor**: mantenha-se produtivo com o Visual Studio no Windows, Linux e macOS.</span><span class="sxs-lookup"><span data-stu-id="0adde-162">**Industry-leading tools**: Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="0adde-163">**Estabilidade e consistência**:  Desenvolva um conjunto comum de linguagens, estruturas e ferramentas que são estáveis, com recursos avançados e fáceis de usar.</span><span class="sxs-lookup"><span data-stu-id="0adde-163">**Stability and consistency**:  Build on a commonset of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

<span data-ttu-id="0adde-164">A execução do código do .NET em navegadores da Web é possibilitada por [WebAssembly](http://webassembly.org) (abreviado como *wasm*).</span><span class="sxs-lookup"><span data-stu-id="0adde-164">Running .NET code inside web browsers is made possible by [WebAssembly](http://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="0adde-165">O WebAssembly é um padrão aberto da Web compatível com navegadores da Web sem plug-ins.</span><span class="sxs-lookup"><span data-stu-id="0adde-165">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span> <span data-ttu-id="0adde-166">O WebAssembly é um formato de código de bytes compacto, otimizado para download rápido e máxima velocidade de execução.</span><span class="sxs-lookup"><span data-stu-id="0adde-166">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span>

<span data-ttu-id="0adde-167">O código do WebAssembly pode acessar a funcionalidade completa do navegador por meio da interoperabilidade do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0adde-167">WebAssembly code can access the full functionality of the browser via JavaScript interop.</span></span> <span data-ttu-id="0adde-168">Ao mesmo tempo, o código .NET executado pelo WebAssembly é executado na mesma área restrita confiável que o JavaScript para impedir ações mal-intencionadas no computador cliente.</span><span class="sxs-lookup"><span data-stu-id="0adde-168">At the same time, .NET code executed via WebAssembly runs in the same trusted sandbox as JavaScript to prevent malicious actions on the client machine.</span></span>

![O Blazor executa o código do .NET no navegador com o WebAssembly.](index/_static/blazor.png)

<span data-ttu-id="0adde-170">Quando um aplicativo Blazor é criado e executado em um navegador:</span><span class="sxs-lookup"><span data-stu-id="0adde-170">When a Blazor app is built and run in a browser:</span></span>

* <span data-ttu-id="0adde-171">os arquivos C# e os arquivos Razor são compilados em assemblies do .NET.</span><span class="sxs-lookup"><span data-stu-id="0adde-171">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="0adde-172">os assemblies e o tempo de execução do .NET são baixados no navegador.</span><span class="sxs-lookup"><span data-stu-id="0adde-172">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="0adde-173">O Blazor inicializa o tempo de execução do .NET e o configura para carregar os assemblies no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0adde-173">Blazor bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="0adde-174">A manipulação do DOM (Modelo de Objeto do Documento) e as chamadas à API do navegador são realizadas pelo tempo de execução do Blazor por meio da interoperabilidade do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0adde-174">Document Object Model (DOM) manipulation and browser API calls are handled by the Blazor runtime via JavaScript interop.</span></span>

<span data-ttu-id="0adde-175">O Blazor é compatível com instalações centrais exigidas pela maioria dos aplicativos, incluindo:</span><span class="sxs-lookup"><span data-stu-id="0adde-175">Blazor supports core facilities required by most apps, including:</span></span>

* <span data-ttu-id="0adde-176">Parâmetros</span><span class="sxs-lookup"><span data-stu-id="0adde-176">Parameters</span></span>
* <span data-ttu-id="0adde-177">Manipulação de eventos</span><span class="sxs-lookup"><span data-stu-id="0adde-177">Event handling</span></span>
* <span data-ttu-id="0adde-178">Associação de dados</span><span class="sxs-lookup"><span data-stu-id="0adde-178">Data binding</span></span>
* <span data-ttu-id="0adde-179">Roteamento</span><span class="sxs-lookup"><span data-stu-id="0adde-179">Routing</span></span>
* <span data-ttu-id="0adde-180">Injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="0adde-180">Dependency injection</span></span>
* <span data-ttu-id="0adde-181">Layouts</span><span class="sxs-lookup"><span data-stu-id="0adde-181">Layouts</span></span>
* <span data-ttu-id="0adde-182">Modelagem</span><span class="sxs-lookup"><span data-stu-id="0adde-182">Templating</span></span>
* <span data-ttu-id="0adde-183">Valores em cascata</span><span class="sxs-lookup"><span data-stu-id="0adde-183">Cascading values</span></span>

<span data-ttu-id="0adde-184">Para reduzir o tamanho do aplicativo baixado, o código não utilizado é retirado do aplicativo quando publicado pelo [Vinculador de linguagem intermediária (IL)](xref:host-and-deploy/blazor/configure-linker).</span><span class="sxs-lookup"><span data-stu-id="0adde-184">To reduce the size of the downloaded app, unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/blazor/configure-linker).</span></span>

<span data-ttu-id="0adde-185">O lado do cliente Blazor é um modelo de hospedagem do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="0adde-185">Blazor client-side is a client-side hosting model.</span></span> <span data-ttu-id="0adde-186">Como o Blazor desvincula uma lógica de renderização de componente da maneira de aplicar as atualizações da interface do usuário, há flexibilidade na maneira de hospedar o Blazor.</span><span class="sxs-lookup"><span data-stu-id="0adde-186">Because Blazor decouples a component's rendering logic from how UI updates are applied, there's flexibility in how Blazor can be hosted.</span></span> <span data-ttu-id="0adde-187">Use os [Componentes Razor](#razor-components) do ASP.NET Core para a hospedagem dos Componentes Razor no servidor, em um aplicativo ASP.NET Core, em que as atualizações da interface do usuário são realizadas por uma conexão SignalR.</span><span class="sxs-lookup"><span data-stu-id="0adde-187">Use ASP.NET Core [Razor Components](#razor-components) to host Razor Components on the server in an ASP.NET Core app where UI updates are handled over a SignalR connection.</span></span> <span data-ttu-id="0adde-188">Para obter mais informações, consulte <xref:blazor/hosting-models#server-side-hosting-model>.</span><span class="sxs-lookup"><span data-stu-id="0adde-188">For more information, see <xref:blazor/hosting-models#server-side-hosting-model>.</span></span> 

<span data-ttu-id="0adde-189">O tamanho do conteúdo é um fator de desempenho crítico para a usabilidade do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0adde-189">Payload size is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="0adde-190">O Blazor otimiza o tamanho do conteúdo para reduzir os tempos de download:</span><span class="sxs-lookup"><span data-stu-id="0adde-190">Blazor optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="0adde-191">As partes não usadas de assemblies do .NET são removidas durante o processo de compilação.</span><span class="sxs-lookup"><span data-stu-id="0adde-191">Unused parts of .NET assemblies are removed during the build process.</span></span>
* <span data-ttu-id="0adde-192">As respostas HTTP são compactadas.</span><span class="sxs-lookup"><span data-stu-id="0adde-192">HTTP responses are compressed.</span></span>
* <span data-ttu-id="0adde-193">O tempo de execução do .NET e os assemblies são armazenados em cache no navegador.</span><span class="sxs-lookup"><span data-stu-id="0adde-193">The .NET runtime and assemblies are cached in the browser.</span></span>

<span data-ttu-id="0adde-194">Os [Componentes Razor](#razor-components) fornecem um tamanho de conteúdo menor do que o Blazor mantendo os assemblies do .NET, o assembly do aplicativo e o lado do servidor do tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="0adde-194">[Razor Components](#razor-components) provides a smaller payload size than Blazor by maintaining .NET assemblies, the app's assembly, and the runtime server-side.</span></span> <span data-ttu-id="0adde-195">Os aplicativos de Componentes Razor só oferecem arquivos de marcação e ativos estáticos para os clientes.</span><span class="sxs-lookup"><span data-stu-id="0adde-195">Razor Components apps only serve markup files and static assets to clients.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0adde-196">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0adde-196">Additional resources</span></span>

* [<span data-ttu-id="0adde-197">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="0adde-197">WebAssembly</span></span>](http://webassembly.org/)
* [<span data-ttu-id="0adde-198">Guia do C#</span><span class="sxs-lookup"><span data-stu-id="0adde-198">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="0adde-199">HTML</span><span class="sxs-lookup"><span data-stu-id="0adde-199">HTML</span></span>](https://www.w3.org/html/)
