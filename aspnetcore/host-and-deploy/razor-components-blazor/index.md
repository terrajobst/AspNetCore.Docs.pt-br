---
title: Hospedar e implantar Componentes Razor e o Blazor
author: guardrex
description: Descubra como hospedar e implantar Componentes Razor e aplicativos Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/28/2019
uid: host-and-deploy/razor-components-blazor/index
ms.openlocfilehash: eeaa27bef584523b77d8b47000b310fe0cac7341
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59070303"
---
# <a name="host-and-deploy-razor-components-and-blazor"></a><span data-ttu-id="c9bac-103">Hospedar e implantar Componentes Razor e o Blazor</span><span class="sxs-lookup"><span data-stu-id="c9bac-103">Host and deploy Razor Components and Blazor</span></span>

<span data-ttu-id="c9bac-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="c9bac-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="c9bac-105">Publique o aplicativo</span><span class="sxs-lookup"><span data-stu-id="c9bac-105">Publish the app</span></span>

<span data-ttu-id="c9bac-106">Os aplicativos são publicados para implantação na configuração de versão com o comando [dotnet publish](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="c9bac-106">Apps are published for deployment in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="c9bac-107">Um IDE (ambiente de desenvolvimento integrado) pode manipular a execução do comando `dotnet publish` automaticamente usando recursos de publicação internos, para que não seja necessário executar o comando manualmente usando um prompt de comando, dependendo das ferramentas de desenvolvimento em uso.</span><span class="sxs-lookup"><span data-stu-id="c9bac-107">An integrated development environment (IDE) may handle executing the `dotnet publish` command automatically using its built-in publishing features, so it might not be necessary to manually execute the command from a command prompt depending on the development tools in use.</span></span>

```console
dotnet publish -c Release
```

`dotnet publish` <span data-ttu-id="c9bac-108">dispara uma [restauração](/dotnet/core/tools/dotnet-restore) das dependências do projeto e [compila](/dotnet/core/tools/dotnet-build) o projeto antes de criar os ativos para implantação.</span><span class="sxs-lookup"><span data-stu-id="c9bac-108">triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="c9bac-109">Como parte do processo de build, os assemblies e métodos não usados são removidos para reduzir o tamanho de download do aplicativo e os tempos de carregamento.</span><span class="sxs-lookup"><span data-stu-id="c9bac-109">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span> <span data-ttu-id="c9bac-110">A implantação é criada na pasta */bin/Release/{TARGET FRAMEWORK}/publish*.</span><span class="sxs-lookup"><span data-stu-id="c9bac-110">The deployment is created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>

<span data-ttu-id="c9bac-111">Os ativos na pasta *publish* são implantados no servidor Web.</span><span class="sxs-lookup"><span data-stu-id="c9bac-111">The assets in the *publish* folder are deployed to the web server.</span></span> <span data-ttu-id="c9bac-112">A implantação pode ser um processo manual ou automatizado, dependendo das ferramentas de desenvolvimento em uso.</span><span class="sxs-lookup"><span data-stu-id="c9bac-112">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="deployment"></a><span data-ttu-id="c9bac-113">Implantação</span><span class="sxs-lookup"><span data-stu-id="c9bac-113">Deployment</span></span>

<span data-ttu-id="c9bac-114">Confira orientações de implantação nos tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="c9bac-114">For deployment guidance, see the following topics:</span></span>

* [<span data-ttu-id="c9bac-115">Blazor no lado do cliente</span><span class="sxs-lookup"><span data-stu-id="c9bac-115">Client-side Blazor</span></span>](xref:host-and-deploy/razor-components-blazor/blazor)
* [<span data-ttu-id="c9bac-116">Componentes Razor ASP.NET Core no lado do servidor</span><span class="sxs-lookup"><span data-stu-id="c9bac-116">Server-side ASP.NET Core Razor Components</span></span>](xref:host-and-deploy/razor-components-blazor/razor-components)
