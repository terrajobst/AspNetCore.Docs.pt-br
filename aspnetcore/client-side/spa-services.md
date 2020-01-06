---
title: Use os serviços JavaScript para criar aplicativos de página única no ASP.NET Core
author: scottaddie
description: Saiba mais sobre os benefícios de usar os serviços JavaScript para criar um aplicativo de página única (SPA) apoiado por ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
uid: client-side/spa-services
ms.openlocfilehash: 52285999d7710cc3198836b9246596980cfc1666
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355780"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a><span data-ttu-id="b3233-103">Use os serviços JavaScript para criar aplicativos de página única no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b3233-103">Use JavaScript Services to Create Single Page Applications in ASP.NET Core</span></span>

<span data-ttu-id="b3233-104">Por [Scott Addie](https://github.com/scottaddie) e [Fiyaz Hasan](https://fiyazhasan.me/)</span><span class="sxs-lookup"><span data-stu-id="b3233-104">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](https://fiyazhasan.me/)</span></span>

<span data-ttu-id="b3233-105">Um aplicativo de página única (SPA) é um tipo popular de aplicativo web devido à sua experiência de usuário avançada inerente.</span><span class="sxs-lookup"><span data-stu-id="b3233-105">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="b3233-106">A integração de estruturas ou bibliotecas SPA do lado do cliente, como [angular](https://angular.io/) ou [reagir](https://facebook.github.io/react/), com estruturas do lado do servidor, como ASP.NET Core pode ser difícil.</span><span class="sxs-lookup"><span data-stu-id="b3233-106">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks such as ASP.NET Core can be difficult.</span></span> <span data-ttu-id="b3233-107">Os serviços JavaScript foram desenvolvidos para reduzir o conflito no processo de integração.</span><span class="sxs-lookup"><span data-stu-id="b3233-107">JavaScript Services was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="b3233-108">Ele permite que a operação contínua entre o cliente diferentes e pilhas de tecnologia do servidor.</span><span class="sxs-lookup"><span data-stu-id="b3233-108">It enables seamless operation between the different client and server technology stacks.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="b3233-109">Os recursos descritos neste artigo são obsoletos a partir do ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="b3233-109">The features described in this article are obsolete as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="b3233-110">Um mecanismo de integração de estruturas SPA mais simples está disponível no pacote NuGet [Microsoft. AspNetCore. SpaServices. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) .</span><span class="sxs-lookup"><span data-stu-id="b3233-110">A simpler SPA frameworks integration mechanism is available in the [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet package.</span></span> <span data-ttu-id="b3233-111">Para obter mais informações, consulte [[comunicado] Obsoleting Microsoft. AspNetCore. SpaServices e Microsoft. AspNetCore. nodeservices](https://github.com/aspnet/AspNetCore/issues/12890).</span><span class="sxs-lookup"><span data-stu-id="b3233-111">For more information, see [[Announcement] Obsoleting Microsoft.AspNetCore.SpaServices and Microsoft.AspNetCore.NodeServices](https://github.com/aspnet/AspNetCore/issues/12890).</span></span>

::: moniker-end

## <a name="what-is-javascript-services"></a><span data-ttu-id="b3233-112">O que são os serviços JavaScript</span><span class="sxs-lookup"><span data-stu-id="b3233-112">What is JavaScript Services</span></span>

<span data-ttu-id="b3233-113">Os serviços JavaScript são uma coleção de tecnologias do lado do cliente para ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b3233-113">JavaScript Services is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="b3233-114">Sua meta é posicionar o ASP.NET Core como plataforma de servidor preferencial dos desenvolvedores para a criação de SPAs.</span><span class="sxs-lookup"><span data-stu-id="b3233-114">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="b3233-115">Os serviços JavaScript consistem em dois pacotes do NuGet distintos:</span><span class="sxs-lookup"><span data-stu-id="b3233-115">JavaScript Services consists of two distinct NuGet packages:</span></span>

* <span data-ttu-id="b3233-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span><span class="sxs-lookup"><span data-stu-id="b3233-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="b3233-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span><span class="sxs-lookup"><span data-stu-id="b3233-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>

<span data-ttu-id="b3233-118">Esses pacotes são úteis nos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="b3233-118">These packages are useful in the following scenarios:</span></span>

* <span data-ttu-id="b3233-119">Executar o JavaScript no servidor</span><span class="sxs-lookup"><span data-stu-id="b3233-119">Run JavaScript on the server</span></span>
* <span data-ttu-id="b3233-120">Usar uma estrutura de SPA ou biblioteca</span><span class="sxs-lookup"><span data-stu-id="b3233-120">Use a SPA framework or library</span></span>
* <span data-ttu-id="b3233-121">Criar ativos do lado do cliente com o Webpack</span><span class="sxs-lookup"><span data-stu-id="b3233-121">Build client-side assets with Webpack</span></span>

<span data-ttu-id="b3233-122">O foco deste artigo é colocado sobre como usar o pacote SpaServices.</span><span class="sxs-lookup"><span data-stu-id="b3233-122">Much of the focus in this article is placed on using the SpaServices package.</span></span>

## <a name="what-is-spaservices"></a><span data-ttu-id="b3233-123">O que é SpaServices</span><span class="sxs-lookup"><span data-stu-id="b3233-123">What is SpaServices</span></span>

<span data-ttu-id="b3233-124">SpaServices foi criado para posicionar o ASP.NET Core como plataforma de servidor preferencial dos desenvolvedores para a criação de SPAs.</span><span class="sxs-lookup"><span data-stu-id="b3233-124">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="b3233-125">SpaServices não é necessário para desenvolver SPAs com ASP.NET Core e não bloqueia os desenvolvedores em uma estrutura de cliente específica.</span><span class="sxs-lookup"><span data-stu-id="b3233-125">SpaServices isn't required to develop SPAs with ASP.NET Core, and it doesn't lock developers into a particular client framework.</span></span>

<span data-ttu-id="b3233-126">SpaServices fornece infraestrutura úteis, como:</span><span class="sxs-lookup"><span data-stu-id="b3233-126">SpaServices provides useful infrastructure such as:</span></span>

* [<span data-ttu-id="b3233-127">Pré-processamento do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="b3233-127">Server-side prerendering</span></span>](#server-side-prerendering)
* [<span data-ttu-id="b3233-128">Middleware de desenvolvimento webpack</span><span class="sxs-lookup"><span data-stu-id="b3233-128">Webpack Dev Middleware</span></span>](#webpack-dev-middleware)
* [<span data-ttu-id="b3233-129">Substituição do módulo quente</span><span class="sxs-lookup"><span data-stu-id="b3233-129">Hot Module Replacement</span></span>](#hot-module-replacement)
* [<span data-ttu-id="b3233-130">Auxiliares de roteamentos</span><span class="sxs-lookup"><span data-stu-id="b3233-130">Routing helpers</span></span>](#routing-helpers)

<span data-ttu-id="b3233-131">Coletivamente, esses componentes de infraestrutura aprimoram o fluxo de trabalho de desenvolvimento e a experiência de tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="b3233-131">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="b3233-132">Os componentes podem ser adotados individualmente.</span><span class="sxs-lookup"><span data-stu-id="b3233-132">The components can be adopted individually.</span></span>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="b3233-133">Pré-requisitos para usar SpaServices</span><span class="sxs-lookup"><span data-stu-id="b3233-133">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="b3233-134">Para trabalhar com SpaServices, instale o seguinte:</span><span class="sxs-lookup"><span data-stu-id="b3233-134">To work with SpaServices, install the following:</span></span>

* <span data-ttu-id="b3233-135">[Node. js](https://nodejs.org/) (versão 6 ou posterior) com npm</span><span class="sxs-lookup"><span data-stu-id="b3233-135">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>

  * <span data-ttu-id="b3233-136">Para verificar se esses componentes estão instalados e podem ser encontrados, execute o seguinte na linha de comando:</span><span class="sxs-lookup"><span data-stu-id="b3233-136">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

  * <span data-ttu-id="b3233-137">Se estiver implantando em um site do Azure, nenhuma ação será necessária&mdash;node. js estiver instalado e disponível nos ambientes de servidor.</span><span class="sxs-lookup"><span data-stu-id="b3233-137">If deploying to an Azure web site, no action is required&mdash;Node.js is installed and available in the server environments.</span></span>

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * <span data-ttu-id="b3233-138">No Windows usando o Visual Studio 2017, o SDK é instalado selecionando a carga de trabalho de **desenvolvimento de plataforma cruzada do .NET Core** .</span><span class="sxs-lookup"><span data-stu-id="b3233-138">On Windows using Visual Studio 2017, the SDK is installed by selecting the **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="b3233-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) pacote do NuGet</span><span class="sxs-lookup"><span data-stu-id="b3233-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

## <a name="server-side-prerendering"></a><span data-ttu-id="b3233-140">Pré-processamento do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="b3233-140">Server-side prerendering</span></span>

<span data-ttu-id="b3233-141">Um aplicativo universal de (também conhecido como isomórficos) é um aplicativo de JavaScript pode ser executada tanto no servidor e cliente.</span><span class="sxs-lookup"><span data-stu-id="b3233-141">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="b3233-142">Angular, React e outras estruturas populares fornecem uma plataforma universal para esse estilo de desenvolvimento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b3233-142">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="b3233-143">A ideia é renderizado primeiro os componentes do framework no servidor por meio do Node. js e delegar ainda mais a execução para o cliente.</span><span class="sxs-lookup"><span data-stu-id="b3233-143">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="b3233-144">ASP.NET Core [auxiliares de marca](xref:mvc/views/tag-helpers/intro) fornecidos pelo SpaServices simplificar a implementação de pré-processamento do lado do servidor, chamando as funções de JavaScript no servidor.</span><span class="sxs-lookup"><span data-stu-id="b3233-144">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="server-side-prerendering-prerequisites"></a><span data-ttu-id="b3233-145">Pré-requisitos de pré-processamento do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="b3233-145">Server-side prerendering prerequisites</span></span>

<span data-ttu-id="b3233-146">Instale o pacote NPM do [ASPNET-prerenderizando](https://www.npmjs.com/package/aspnet-prerendering) :</span><span class="sxs-lookup"><span data-stu-id="b3233-146">Install the [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a><span data-ttu-id="b3233-147">Configuração de pré-processamento do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="b3233-147">Server-side prerendering configuration</span></span>

<span data-ttu-id="b3233-148">Os auxiliares de marca são feitos podem ser descobertos por meio do registro do namespace do projeto *viewimports. cshtml* arquivo:</span><span class="sxs-lookup"><span data-stu-id="b3233-148">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

<span data-ttu-id="b3233-149">Esses auxiliares de marcação abstraem as complexidades de se comunicar diretamente com as APIs de baixo nível, utilizando uma sintaxe semelhante ao HTML dentro a exibição do Razor:</span><span class="sxs-lookup"><span data-stu-id="b3233-149">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the Razor view:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a><span data-ttu-id="b3233-150">Auxiliar de marca do ASP-PreRender-Module</span><span class="sxs-lookup"><span data-stu-id="b3233-150">asp-prerender-module Tag Helper</span></span>

<span data-ttu-id="b3233-151">O `asp-prerender-module` auxiliar de marca, usado no exemplo de código anterior executa *ClientApp/dist/main-server.js* no servidor por meio do Node. js.</span><span class="sxs-lookup"><span data-stu-id="b3233-151">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="b3233-152">Para clareza, *main-Server. js* arquivo é um artefato da tarefa em que a transcompilação TypeScript e JavaScript a [Webpack](https://webpack.github.io/) processo de compilação.</span><span class="sxs-lookup"><span data-stu-id="b3233-152">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](https://webpack.github.io/) build process.</span></span> <span data-ttu-id="b3233-153">Webpack define um alias de ponto de entrada de `main-server`; e, passagem de grafo de dependência para este alias começa em de *ClientApp/inicialização-server.ts* arquivo:</span><span class="sxs-lookup"><span data-stu-id="b3233-153">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

<span data-ttu-id="b3233-154">No exemplo a seguir Angular, o *ClientApp/inicialização-server.ts* arquivo utiliza a `createServerRenderer` função e `RenderResult` tipo do `aspnet-prerendering` pacote npm para configurar a renderização do servidor por meio do Node. js.</span><span class="sxs-lookup"><span data-stu-id="b3233-154">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="b3233-155">A marcação HTML destinada a renderização do lado do servidor é passada para uma chamada de função de resolução, que é encapsulada em um JavaScript fortemente tipada `Promise` objeto.</span><span class="sxs-lookup"><span data-stu-id="b3233-155">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="b3233-156">O `Promise` significância do objeto é que ele fornece assincronamente a marcação HTML para a página para injeção no elemento de espaço reservado do DOM.</span><span class="sxs-lookup"><span data-stu-id="b3233-156">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a><span data-ttu-id="b3233-157">ASP-PreRender-auxiliar de marca de dados</span><span class="sxs-lookup"><span data-stu-id="b3233-157">asp-prerender-data Tag Helper</span></span>

<span data-ttu-id="b3233-158">Quando combinado com o `asp-prerender-module` auxiliar de marca, o `asp-prerender-data` auxiliar de marca pode ser usado para passar informações contextuais da exibição do Razor para o JavaScript do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="b3233-158">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the Razor view to the server-side JavaScript.</span></span> <span data-ttu-id="b3233-159">Por exemplo, a marcação a seguir passa os dados do usuário para o `main-server` módulo:</span><span class="sxs-lookup"><span data-stu-id="b3233-159">For example, the following markup passes user data to the `main-server` module:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

<span data-ttu-id="b3233-160">Recebido `UserName` argumento for serializado usando o serializador JSON interno e é armazenado no `params.data` objeto.</span><span class="sxs-lookup"><span data-stu-id="b3233-160">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="b3233-161">No exemplo a seguir Angular, os dados são usados para construir uma saudação personalizada dentro de um `h1` elemento:</span><span class="sxs-lookup"><span data-stu-id="b3233-161">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

<span data-ttu-id="b3233-162">Os nomes de propriedade passados em auxiliares de marca são representados com notação de **PascalCase** .</span><span class="sxs-lookup"><span data-stu-id="b3233-162">Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="b3233-163">Compare isso com JavaScript, onde os mesmos nomes de propriedade são representados com **camelCase**.</span><span class="sxs-lookup"><span data-stu-id="b3233-163">Contrast that to JavaScript, where the same property names are represented with **camelCase**.</span></span> <span data-ttu-id="b3233-164">A configuração de serialização JSON padrão é responsável por essa diferença.</span><span class="sxs-lookup"><span data-stu-id="b3233-164">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="b3233-165">Para expandir o exemplo de código anterior, dados podem ser passados do servidor para o modo de exibição por hydrating a `globals` propriedade fornecida para o `resolve` função:</span><span class="sxs-lookup"><span data-stu-id="b3233-165">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

<span data-ttu-id="b3233-166">O `postList` matriz definida dentro de `globals` objeto está anexado para o navegador global `window` objeto.</span><span class="sxs-lookup"><span data-stu-id="b3233-166">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="b3233-167">Essa elevação de variáveis em escopo global elimina a duplicação de esforço, particularmente, pois pertence ao carregar os mesmos dados, uma vez no servidor e novamente no cliente.</span><span class="sxs-lookup"><span data-stu-id="b3233-167">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![variável global de postList anexada ao objeto de janela](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a><span data-ttu-id="b3233-169">Middleware de desenvolvimento webpack</span><span class="sxs-lookup"><span data-stu-id="b3233-169">Webpack Dev Middleware</span></span>

<span data-ttu-id="b3233-170">[Middleware de desenvolvimento webpack](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduz um fluxo de trabalho de desenvolvimento simplificado no qual o Webpack cria recursos sob demanda.</span><span class="sxs-lookup"><span data-stu-id="b3233-170">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="b3233-171">O middleware automaticamente compila e atende recursos do lado do cliente quando uma página é recarregada no navegador.</span><span class="sxs-lookup"><span data-stu-id="b3233-171">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="b3233-172">A abordagem alternativa é invocar manualmente Webpack por meio do script de compilação do projeto npm quando uma dependência de terceiros ou o código personalizado é alterado.</span><span class="sxs-lookup"><span data-stu-id="b3233-172">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="b3233-173">Script de construção de um npm *Package. JSON* arquivo é mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="b3233-173">An npm build script in the *package.json* file is shown in the following example:</span></span>

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a><span data-ttu-id="b3233-174">Pré-requisitos de middleware de dev do webpack</span><span class="sxs-lookup"><span data-stu-id="b3233-174">Webpack Dev Middleware prerequisites</span></span>

<span data-ttu-id="b3233-175">Instale o pacote [ASPNET-webpack](https://www.npmjs.com/package/aspnet-webpack) NPM:</span><span class="sxs-lookup"><span data-stu-id="b3233-175">Install the [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a><span data-ttu-id="b3233-176">Configuração de middleware de dev do webpack</span><span class="sxs-lookup"><span data-stu-id="b3233-176">Webpack Dev Middleware configuration</span></span>

<span data-ttu-id="b3233-177">Webpack Dev Middleware é registrado no pipeline de solicitação HTTP por meio de código a seguir na *Startup.cs* do arquivo `Configure` método:</span><span class="sxs-lookup"><span data-stu-id="b3233-177">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

<span data-ttu-id="b3233-178">O `UseWebpackDevMiddleware` método de extensão deve ser chamado antes [Registrando a hospedagem de arquivos estáticos](xref:fundamentals/static-files) por meio de `UseStaticFiles` método de extensão.</span><span class="sxs-lookup"><span data-stu-id="b3233-178">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="b3233-179">Por motivos de segurança, registre o middleware somente quando o aplicativo é executado no modo de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="b3233-179">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="b3233-180">O *webpack.config.js* do arquivo `output.publicPath` propriedade informa o middleware para observar o `dist` pasta para que as alterações:</span><span class="sxs-lookup"><span data-stu-id="b3233-180">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a><span data-ttu-id="b3233-181">Substituição do módulo quente</span><span class="sxs-lookup"><span data-stu-id="b3233-181">Hot Module Replacement</span></span>

<span data-ttu-id="b3233-182">Pense do Webpack [módulo de substituição a quente](https://webpack.js.org/concepts/hot-module-replacement/) recurso (HMR) como uma evolução do [Webpack Dev Middleware](#webpack-dev-middleware).</span><span class="sxs-lookup"><span data-stu-id="b3233-182">Think of Webpack's [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="b3233-183">HMR apresenta todos os mesmos benefícios, mas ela simplifica ainda mais o fluxo de trabalho de desenvolvimento, atualizando automaticamente o conteúdo da página depois de compilar as alterações.</span><span class="sxs-lookup"><span data-stu-id="b3233-183">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="b3233-184">Não confunda isso com uma atualização do navegador, que poderia interferir com o estado atual de na memória e a sessão de depuração do SPA.</span><span class="sxs-lookup"><span data-stu-id="b3233-184">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="b3233-185">Há um link em tempo real entre o serviço de Middleware de desenvolvimento Webpack e o navegador, o que significa que alterações são enviadas para o navegador.</span><span class="sxs-lookup"><span data-stu-id="b3233-185">There's a live link between the Webpack Dev Middleware service and the browser, which means changes are pushed to the browser.</span></span>

### <a name="hot-module-replacement-prerequisites"></a><span data-ttu-id="b3233-186">Pré-requisitos de substituição de módulo quente</span><span class="sxs-lookup"><span data-stu-id="b3233-186">Hot Module Replacement prerequisites</span></span>

<span data-ttu-id="b3233-187">Instalar o pacote [webpack – Hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) NPM:</span><span class="sxs-lookup"><span data-stu-id="b3233-187">Install the [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a><span data-ttu-id="b3233-188">Configuração de substituição de módulo quente</span><span class="sxs-lookup"><span data-stu-id="b3233-188">Hot Module Replacement configuration</span></span>

<span data-ttu-id="b3233-189">O componente HMR deve ser registrado no pipeline de solicitação HTTP do MVC no `Configure` método:</span><span class="sxs-lookup"><span data-stu-id="b3233-189">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="b3233-190">Como ocorria com [Webpack Dev Middleware](#webpack-dev-middleware), o `UseWebpackDevMiddleware` método de extensão deve ser chamado antes do `UseStaticFiles` método de extensão.</span><span class="sxs-lookup"><span data-stu-id="b3233-190">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="b3233-191">Por motivos de segurança, registre o middleware somente quando o aplicativo é executado no modo de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="b3233-191">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="b3233-192">O *webpack.config.js* arquivo deve definir um `plugins` de matriz, mesmo se ela for deixada em branco:</span><span class="sxs-lookup"><span data-stu-id="b3233-192">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

<span data-ttu-id="b3233-193">Depois de carregar o aplicativo no navegador, a guia Console de ferramentas de desenvolvedor fornece confirmação de ativação de HMR:</span><span class="sxs-lookup"><span data-stu-id="b3233-193">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

![Hot mensagem conectada de substituição do módulo](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a><span data-ttu-id="b3233-195">Auxiliares de roteamentos</span><span class="sxs-lookup"><span data-stu-id="b3233-195">Routing helpers</span></span>

<span data-ttu-id="b3233-196">Na maioria dos SPAs baseados em ASP.NET Core, o roteamento do lado do cliente geralmente é desejado, além do roteamento do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="b3233-196">In most ASP.NET Core-based SPAs, client-side routing is often desired in addition to server-side routing.</span></span> <span data-ttu-id="b3233-197">Os sistemas de roteamento do SPA e o MVC podem trabalhar de forma independente, sem interferência.</span><span class="sxs-lookup"><span data-stu-id="b3233-197">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="b3233-198">Há, no entanto, os desafios de uma borda caso apresentando: identificando as respostas HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="b3233-198">There's, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="b3233-199">Considere o cenário em que uma rota sem extensão de `/some/page` é usado.</span><span class="sxs-lookup"><span data-stu-id="b3233-199">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="b3233-200">Suponha que a solicitação não-correspondência de padrão uma rota do lado do servidor, mas seu padrão corresponde a uma rota do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="b3233-200">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="b3233-201">Agora, considere uma solicitação de entrada para `/images/user-512.png`, que geralmente espera encontrar um arquivo de imagem no servidor.</span><span class="sxs-lookup"><span data-stu-id="b3233-201">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="b3233-202">Se esse caminho de recurso solicitado não corresponder a uma rota no lado do servidor ou a um arquivo estático, é improvável que o aplicativo do lado do cliente manipule isso&mdash;geralmente retornar um código de status HTTP 404 é desejado.</span><span class="sxs-lookup"><span data-stu-id="b3233-202">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it&mdash;generally returning a 404 HTTP status code is desired.</span></span>

### <a name="routing-helpers-prerequisites"></a><span data-ttu-id="b3233-203">Pré-requisitos de auxiliares de roteamento</span><span class="sxs-lookup"><span data-stu-id="b3233-203">Routing helpers prerequisites</span></span>

<span data-ttu-id="b3233-204">Instale o pacote NPM de roteamento do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="b3233-204">Install the client-side routing npm package.</span></span> <span data-ttu-id="b3233-205">Usando Angular como um exemplo:</span><span class="sxs-lookup"><span data-stu-id="b3233-205">Using Angular as an example:</span></span>

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a><span data-ttu-id="b3233-206">Configuração de auxiliares de roteamento</span><span class="sxs-lookup"><span data-stu-id="b3233-206">Routing helpers configuration</span></span>

<span data-ttu-id="b3233-207">Um método de extensão denominado `MapSpaFallbackRoute` é usado no `Configure` método:</span><span class="sxs-lookup"><span data-stu-id="b3233-207">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

<span data-ttu-id="b3233-208">As rotas são avaliadas na ordem em que estão configuradas.</span><span class="sxs-lookup"><span data-stu-id="b3233-208">Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="b3233-209">Consequentemente, o `default` rota no exemplo de código anterior é usada primeiro para correspondência de padrões.</span><span class="sxs-lookup"><span data-stu-id="b3233-209">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="b3233-210">Crie um novo projeto</span><span class="sxs-lookup"><span data-stu-id="b3233-210">Create a new project</span></span>

<span data-ttu-id="b3233-211">Os serviços JavaScript fornecem modelos de aplicativo pré-configurados.</span><span class="sxs-lookup"><span data-stu-id="b3233-211">JavaScript Services provide pre-configured application templates.</span></span> <span data-ttu-id="b3233-212">O SpaServices é usado nesses modelos em conjunto com estruturas e bibliotecas diferentes, como angular, reagir e Redux.</span><span class="sxs-lookup"><span data-stu-id="b3233-212">SpaServices is used in these templates in conjunction with different frameworks and libraries such as Angular, React, and Redux.</span></span>

<span data-ttu-id="b3233-213">Esses modelos podem ser instalados por meio da CLI do .NET Core, executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="b3233-213">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="b3233-214">Uma lista de modelos SPA disponíveis é exibida:</span><span class="sxs-lookup"><span data-stu-id="b3233-214">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="b3233-215">Modelos</span><span class="sxs-lookup"><span data-stu-id="b3233-215">Templates</span></span>                                 | <span data-ttu-id="b3233-216">Nome Curto</span><span class="sxs-lookup"><span data-stu-id="b3233-216">Short Name</span></span> | <span data-ttu-id="b3233-217">{1&gt;Idioma&lt;1}</span><span class="sxs-lookup"><span data-stu-id="b3233-217">Language</span></span> | <span data-ttu-id="b3233-218">Marcas</span><span class="sxs-lookup"><span data-stu-id="b3233-218">Tags</span></span>        |
| ------------------------------------------| :--------: | :------: | :---------: |
| <span data-ttu-id="b3233-219">MVC ASP.NET Core com Angular</span><span class="sxs-lookup"><span data-stu-id="b3233-219">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="b3233-220">angular</span><span class="sxs-lookup"><span data-stu-id="b3233-220">angular</span></span>    | <span data-ttu-id="b3233-221">[C#]</span><span class="sxs-lookup"><span data-stu-id="b3233-221">[C#]</span></span>     | <span data-ttu-id="b3233-222">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="b3233-222">Web/MVC/SPA</span></span> |
| <span data-ttu-id="b3233-223">MVC ASP.NET Core com React. js</span><span class="sxs-lookup"><span data-stu-id="b3233-223">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="b3233-224">react</span><span class="sxs-lookup"><span data-stu-id="b3233-224">react</span></span>      | <span data-ttu-id="b3233-225">[C#]</span><span class="sxs-lookup"><span data-stu-id="b3233-225">[C#]</span></span>     | <span data-ttu-id="b3233-226">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="b3233-226">Web/MVC/SPA</span></span> |
| <span data-ttu-id="b3233-227">MVC ASP.NET Core com React. js e Redux</span><span class="sxs-lookup"><span data-stu-id="b3233-227">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="b3233-228">reactredux</span><span class="sxs-lookup"><span data-stu-id="b3233-228">reactredux</span></span> | <span data-ttu-id="b3233-229">[C#]</span><span class="sxs-lookup"><span data-stu-id="b3233-229">[C#]</span></span>     | <span data-ttu-id="b3233-230">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="b3233-230">Web/MVC/SPA</span></span> |

<span data-ttu-id="b3233-231">Para criar um novo projeto usando um dos modelos do SPA, inclua o **nome curto** do modelo na [dotnet novo](/dotnet/core/tools/dotnet-new) comando.</span><span class="sxs-lookup"><span data-stu-id="b3233-231">To create a new project using one of the SPA templates, include the **Short Name** of the template in the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="b3233-232">O comando a seguir cria um aplicativo Angular com ASP.NET Core MVC configurado para o lado do servidor:</span><span class="sxs-lookup"><span data-stu-id="b3233-232">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="b3233-233">Definir o modo de configuração de tempo de execução</span><span class="sxs-lookup"><span data-stu-id="b3233-233">Set the runtime configuration mode</span></span>

<span data-ttu-id="b3233-234">Existem dois modos de configuração de tempo de execução principal:</span><span class="sxs-lookup"><span data-stu-id="b3233-234">Two primary runtime configuration modes exist:</span></span>

* <span data-ttu-id="b3233-235">**Desenvolvimento**:</span><span class="sxs-lookup"><span data-stu-id="b3233-235">**Development**:</span></span>
  * <span data-ttu-id="b3233-236">Inclui mapas de código-fonte para facilitar a depuração.</span><span class="sxs-lookup"><span data-stu-id="b3233-236">Includes source maps to ease debugging.</span></span>
  * <span data-ttu-id="b3233-237">Não Otimize o código do lado do cliente para o desempenho.</span><span class="sxs-lookup"><span data-stu-id="b3233-237">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="b3233-238">**Produção**:</span><span class="sxs-lookup"><span data-stu-id="b3233-238">**Production**:</span></span>
  * <span data-ttu-id="b3233-239">Exclui os mapas de origem.</span><span class="sxs-lookup"><span data-stu-id="b3233-239">Excludes source maps.</span></span>
  * <span data-ttu-id="b3233-240">Otimiza o código do lado do cliente por meio de agrupamento e minificação.</span><span class="sxs-lookup"><span data-stu-id="b3233-240">Optimizes the client-side code via bundling and minification.</span></span>

<span data-ttu-id="b3233-241">O ASP.NET Core usa uma variável de ambiente denominada `ASPNETCORE_ENVIRONMENT` para armazenar o modo de configuração.</span><span class="sxs-lookup"><span data-stu-id="b3233-241">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="b3233-242">Para obter mais informações, consulte [definir o ambiente](xref:fundamentals/environments#set-the-environment).</span><span class="sxs-lookup"><span data-stu-id="b3233-242">For more information, see [Set the environment](xref:fundamentals/environments#set-the-environment).</span></span>

### <a name="run-with-net-core-cli"></a><span data-ttu-id="b3233-243">Executar com CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="b3233-243">Run with .NET Core CLI</span></span>

<span data-ttu-id="b3233-244">Restaure os pacotes de npm e NuGet necessários, executando o seguinte comando na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="b3233-244">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```dotnetcli
dotnet restore && npm i
```

<span data-ttu-id="b3233-245">Compilar e executar o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="b3233-245">Build and run the application:</span></span>

```dotnetcli
dotnet run
```

<span data-ttu-id="b3233-246">O aplicativo é iniciado no localhost de acordo com o [modo de configuração de tempo de execução](#set-the-runtime-configuration-mode).</span><span class="sxs-lookup"><span data-stu-id="b3233-246">The application starts on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span> <span data-ttu-id="b3233-247">Navegando até `http://localhost:5000` no navegador exibe a página de aterrissagem.</span><span class="sxs-lookup"><span data-stu-id="b3233-247">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="run-with-visual-studio-2017"></a><span data-ttu-id="b3233-248">Executar com o Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="b3233-248">Run with Visual Studio 2017</span></span>

<span data-ttu-id="b3233-249">Abra o *. csproj* arquivo gerado pelo [dotnet novo](/dotnet/core/tools/dotnet-new) comando.</span><span class="sxs-lookup"><span data-stu-id="b3233-249">Open the *.csproj* file generated by the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="b3233-250">Os pacotes NuGet e npm necessários são restaurados automaticamente ao projeto aberto.</span><span class="sxs-lookup"><span data-stu-id="b3233-250">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="b3233-251">Esse processo de restauração pode demorar alguns minutos e o aplicativo está pronto para ser executado quando ele for concluído.</span><span class="sxs-lookup"><span data-stu-id="b3233-251">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="b3233-252">Clique no botão de execução verde ou pressione `Ctrl + F5`, e o navegador abre a página de aterrissagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b3233-252">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="b3233-253">O aplicativo é executado no localhost de acordo com o [modo de configuração de tempo de execução](#set-the-runtime-configuration-mode).</span><span class="sxs-lookup"><span data-stu-id="b3233-253">The application runs on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span>

## <a name="test-the-app"></a><span data-ttu-id="b3233-254">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="b3233-254">Test the app</span></span>

<span data-ttu-id="b3233-255">Modelos SpaServices são pré-configuradas para executar testes do lado do cliente usando [Karma](https://karma-runner.github.io/1.0/index.html) e [Jasmine](https://jasmine.github.io/).</span><span class="sxs-lookup"><span data-stu-id="b3233-255">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="b3233-256">Jasmine é uma estrutura de teste para JavaScript, de unidade popular enquanto Karma é um executor de teste para que esses testes.</span><span class="sxs-lookup"><span data-stu-id="b3233-256">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="b3233-257">Karma está configurado para funcionar com o [Webpack Dev Middleware](#webpack-dev-middleware) , de modo que o desenvolvedor não é necessário parar e executar o teste sempre que forem feitas alterações.</span><span class="sxs-lookup"><span data-stu-id="b3233-257">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that the developer isn't required to stop and run the test every time changes are made.</span></span> <span data-ttu-id="b3233-258">Se ele é o código em execução no caso de teste ou caso de teste em si, o teste é executado automaticamente.</span><span class="sxs-lookup"><span data-stu-id="b3233-258">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="b3233-259">Usando o aplicativo Angular como exemplo, dois casos de teste Jasmine já são fornecidos para o `CounterComponent` no *counter.component.spec.ts* arquivo:</span><span class="sxs-lookup"><span data-stu-id="b3233-259">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

<span data-ttu-id="b3233-260">Abra o prompt de comando de *ClientApp* diretório.</span><span class="sxs-lookup"><span data-stu-id="b3233-260">Open the command prompt in the *ClientApp* directory.</span></span> <span data-ttu-id="b3233-261">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="b3233-261">Run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="b3233-262">O script inicia o executor de teste Karma, que lê as configurações definidas na *karma.conf.js* arquivo.</span><span class="sxs-lookup"><span data-stu-id="b3233-262">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="b3233-263">Entre outras configurações, o *karma.conf.js* identifica os arquivos de teste para ser executada por meio do seu `files` matriz:</span><span class="sxs-lookup"><span data-stu-id="b3233-263">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a><span data-ttu-id="b3233-264">Publique o aplicativo</span><span class="sxs-lookup"><span data-stu-id="b3233-264">Publish the app</span></span>

<span data-ttu-id="b3233-265">Consulte [este problema do GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/12474) para obter mais informações sobre como publicar no Azure.</span><span class="sxs-lookup"><span data-stu-id="b3233-265">See [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/12474) for more information on publishing to Azure.</span></span>

<span data-ttu-id="b3233-266">Combinando os ativos gerados do lado do cliente e os artefatos publicados do ASP.NET Core em um pacote pronto para implantar pode ser complicado.</span><span class="sxs-lookup"><span data-stu-id="b3233-266">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="b3233-267">Felizmente, SpaServices orquestra o processo de publicação inteira com um destino MSBuild personalizado chamado `RunWebpack`:</span><span class="sxs-lookup"><span data-stu-id="b3233-267">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

<span data-ttu-id="b3233-268">O destino do MSBuild tem as seguintes responsabilidades:</span><span class="sxs-lookup"><span data-stu-id="b3233-268">The MSBuild target has the following responsibilities:</span></span>

1. <span data-ttu-id="b3233-269">Restaure os pacotes NPM.</span><span class="sxs-lookup"><span data-stu-id="b3233-269">Restore the npm packages.</span></span>
1. <span data-ttu-id="b3233-270">Crie um Build de nível de produção dos ativos de terceiros do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="b3233-270">Create a production-grade build of the third-party, client-side assets.</span></span>
1. <span data-ttu-id="b3233-271">Crie uma compilação de nível de produção dos ativos personalizados do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="b3233-271">Create a production-grade build of the custom client-side assets.</span></span>
1. <span data-ttu-id="b3233-272">Copie os ativos gerados pelo webpack para a pasta de publicação.</span><span class="sxs-lookup"><span data-stu-id="b3233-272">Copy the Webpack-generated assets to the publish folder.</span></span>

<span data-ttu-id="b3233-273">O destino do MSBuild é chamado durante a execução:</span><span class="sxs-lookup"><span data-stu-id="b3233-273">The MSBuild target is invoked when running:</span></span>

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="b3233-274">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b3233-274">Additional resources</span></span>

* [<span data-ttu-id="b3233-275">Docs angular</span><span class="sxs-lookup"><span data-stu-id="b3233-275">Angular Docs</span></span>](https://angular.io/docs)
