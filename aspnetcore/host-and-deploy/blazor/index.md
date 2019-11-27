---
title: Hospedar e implantar ASP.NET Core Blazor
author: guardrex
description: Descubra como hospedar e implantar aplicativos Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/23/2019
no-loc:
- Blazor
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 5c37c3d9f424f4c4b814e1955880623fd95179f2
ms.sourcegitcommit: 918d7000b48a2892750264b852bad9e96a1165a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74550368"
---
# <a name="host-and-deploy-aspnet-core-opno-locblazor"></a><span data-ttu-id="25d33-103">Hospedar e implantar ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="25d33-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="25d33-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="25d33-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a><span data-ttu-id="25d33-105">Publique o aplicativo</span><span class="sxs-lookup"><span data-stu-id="25d33-105">Publish the app</span></span>

<span data-ttu-id="25d33-106">Os aplicativos são publicados para implantação na configuração de versão.</span><span class="sxs-lookup"><span data-stu-id="25d33-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="25d33-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="25d33-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="25d33-108">Selecione **Versão** > **Publicar {APLICATIVO}** na barra de navegação.</span><span class="sxs-lookup"><span data-stu-id="25d33-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="25d33-109">Selecione o botão *destino de publicação*.</span><span class="sxs-lookup"><span data-stu-id="25d33-109">Select the *publish target*.</span></span> <span data-ttu-id="25d33-110">Para publicar localmente, selecione **Pasta**.</span><span class="sxs-lookup"><span data-stu-id="25d33-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="25d33-111">Aceite o local padrão no campo **Escolher uma pasta** ou especifique um local diferente.</span><span class="sxs-lookup"><span data-stu-id="25d33-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="25d33-112">Selecione o botão **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="25d33-112">Select the **Publish** button.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="25d33-113">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="25d33-113">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="25d33-114">Use o comando [dotnet publish](/dotnet/core/tools/dotnet-publish) para publicar o aplicativo com uma configuração de versão:</span><span class="sxs-lookup"><span data-stu-id="25d33-114">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="25d33-115">Publicar o aplicativo dispara uma [restauração](/dotnet/core/tools/dotnet-restore) das dependências do projeto e [compila](/dotnet/core/tools/dotnet-build) o projeto antes de criar os ativos para implantação.</span><span class="sxs-lookup"><span data-stu-id="25d33-115">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="25d33-116">Como parte do processo de build, os assemblies e métodos não usados são removidos para reduzir o tamanho de download do aplicativo e os tempos de carregamento.</span><span class="sxs-lookup"><span data-stu-id="25d33-116">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="25d33-117">Um aplicativo Webassembly Blazor é publicado na pasta */bin/Release/{Target Framework}/Publish/{Assembly Name}/dist* .</span><span class="sxs-lookup"><span data-stu-id="25d33-117">A Blazor WebAssembly app is published to the */bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* folder.</span></span> <span data-ttu-id="25d33-118">Um aplicativo do Blazor Server é publicado na pasta */Publish do/bin/Release/{Target Framework}* .</span><span class="sxs-lookup"><span data-stu-id="25d33-118">A Blazor Server app is published to the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>

<span data-ttu-id="25d33-119">Os ativos na pasta são implantados no servidor Web.</span><span class="sxs-lookup"><span data-stu-id="25d33-119">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="25d33-120">A implantação pode ser um processo manual ou automatizado, dependendo das ferramentas de desenvolvimento em uso.</span><span class="sxs-lookup"><span data-stu-id="25d33-120">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="25d33-121">Caminho base do aplicativo</span><span class="sxs-lookup"><span data-stu-id="25d33-121">App base path</span></span>

<span data-ttu-id="25d33-122">O *caminho base do aplicativo* é o caminho da URL raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="25d33-122">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="25d33-123">Considere o seguinte aplicativo ASP.NET Core e Blazor subaplicativo:</span><span class="sxs-lookup"><span data-stu-id="25d33-123">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="25d33-124">O aplicativo ASP.NET Core é nomeado `MyApp`:</span><span class="sxs-lookup"><span data-stu-id="25d33-124">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="25d33-125">O aplicativo reside fisicamente em *d:/MyApp*.</span><span class="sxs-lookup"><span data-stu-id="25d33-125">The app physically resides at *d:/MyApp*.</span></span>
  * <span data-ttu-id="25d33-126">As solicitações são recebidas em `https://www.contoso.com/{MYAPP RESOURCE}`.</span><span class="sxs-lookup"><span data-stu-id="25d33-126">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="25d33-127">Um aplicativo Blazor chamado `CoolApp` é um subaplicativo do `MyApp`:</span><span class="sxs-lookup"><span data-stu-id="25d33-127">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="25d33-128">O subaplicativo reside fisicamente em *d:/MyApp/CoolApp*.</span><span class="sxs-lookup"><span data-stu-id="25d33-128">The sub-app physically resides at *d:/MyApp/CoolApp*.</span></span>
  * <span data-ttu-id="25d33-129">As solicitações são recebidas em `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span><span class="sxs-lookup"><span data-stu-id="25d33-129">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="25d33-130">Sem especificar configuração adicional para `CoolApp`, o subaplicativo nesse cenário não tem conhecimento de onde ele reside no servidor.</span><span class="sxs-lookup"><span data-stu-id="25d33-130">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="25d33-131">Por exemplo, o aplicativo não pode construir URLs relativas corretas para seus recursos sem saber que ela reside no caminho de URL relativo `/CoolApp/`.</span><span class="sxs-lookup"><span data-stu-id="25d33-131">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="25d33-132">Para fornecer a configuração para o caminho base do aplicativo Blazor do `https://www.contoso.com/CoolApp/`, o atributo `href` da marca `<base>` é definido como o caminho raiz relativo no arquivo *pages/_Host. cshtml* (Blazor Server) ou no arquivo *wwwroot/index.html* (Blazor Webassembly):</span><span class="sxs-lookup"><span data-stu-id="25d33-132">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly):</span></span>

```html
<base href="/CoolApp/">
```

<span data-ttu-id="25d33-133">os aplicativos do Blazor Server também definem o caminho base do servidor chamando <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> no pipeline de solicitação do aplicativo de `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="25d33-133">Blazor Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="25d33-134">Ao fornecer o caminho de URL relativo, um componente que não está no diretório raiz pode construir URLs relativas ao caminho raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="25d33-134">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="25d33-135">Os componentes em diferentes níveis da estrutura de diretório podem criar links para outros recursos em locais em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="25d33-135">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="25d33-136">O caminho base do aplicativo também é usado para interceptar hiperlinks selecionados onde o destino `href` do link está dentro do espaço URI de caminho base do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="25d33-136">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="25d33-137">O roteador de Blazor lida com a navegação interna.</span><span class="sxs-lookup"><span data-stu-id="25d33-137">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="25d33-138">Em muitos cenários de hospedagem, o caminho de URL relativo para o aplicativo é a raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="25d33-138">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="25d33-139">Nesses casos, o caminho base da URL relativa do aplicativo é uma barra (`<base href="/" />`), que é a configuração padrão para um aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="25d33-139">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="25d33-140">Em outros cenários de hospedagem, como páginas do GitHub e subaplicativos do IIS, o caminho base do aplicativo deve ser definido como o caminho de URL relativo do servidor do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="25d33-140">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="25d33-141">Para definir o caminho base do aplicativo, atualize a marca de `<base>` dentro dos elementos de marca `<head>` do arquivo *pages/_Host. cshtml* (Blazor Server) ou *wwwroot/index.html* File (Blazor Webassembly).</span><span class="sxs-lookup"><span data-stu-id="25d33-141">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly).</span></span> <span data-ttu-id="25d33-142">Defina o valor do atributo `href` como `/{RELATIVE URL PATH}/` (a barra à direita é necessária), em que `{RELATIVE URL PATH}` é o caminho de URL relativo completo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="25d33-142">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="25d33-143">Para um aplicativo Webassembly Blazor com um caminho de URL não raiz relativo (por exemplo, `<base href="/CoolApp/">`), o aplicativo não consegue localizar seus recursos *quando executado localmente*.</span><span class="sxs-lookup"><span data-stu-id="25d33-143">For an Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="25d33-144">Para superar esse problema durante o desenvolvimento e os testes locais, você pode fornecer um argumento *base de caminho* que corresponde ao valor de `href` da tag `<base>` no runtime.</span><span class="sxs-lookup"><span data-stu-id="25d33-144">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="25d33-145">Não inclua uma barra à direita.</span><span class="sxs-lookup"><span data-stu-id="25d33-145">Don't include a trailing slash.</span></span> <span data-ttu-id="25d33-146">Para passar o argumento de base Path ao executar o aplicativo localmente, execute o comando `dotnet run` no diretório do aplicativo com a opção `--pathbase`:</span><span class="sxs-lookup"><span data-stu-id="25d33-146">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="25d33-147">Para um aplicativo Webassembly Blazor com um caminho de URL relativo de `/CoolApp/` (`<base href="/CoolApp/">`), o comando é:</span><span class="sxs-lookup"><span data-stu-id="25d33-147">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="25d33-148">O aplicativo Webassembly Blazor responde localmente em `http://localhost:port/CoolApp`.</span><span class="sxs-lookup"><span data-stu-id="25d33-148">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

## <a name="deployment"></a><span data-ttu-id="25d33-149">Implantação</span><span class="sxs-lookup"><span data-stu-id="25d33-149">Deployment</span></span>

<span data-ttu-id="25d33-150">Confira orientações de implantação nos tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="25d33-150">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
