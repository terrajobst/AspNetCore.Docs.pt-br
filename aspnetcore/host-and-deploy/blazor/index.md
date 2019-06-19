---
title: Hospedar e implantar o ASP.NET Core Blazor
author: guardrex
description: Descubra como hospedar e implantar aplicativos Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/14/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 8a5ac5c58e7ceab07e55da8b61ebb01f7ac984bc
ms.sourcegitcommit: 4ef0362ef8b6e5426fc5af18f22734158fe587e1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67153204"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="34be6-103">Hospedar e implantar o ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="34be6-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="34be6-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="34be6-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="34be6-105">Publique o aplicativo</span><span class="sxs-lookup"><span data-stu-id="34be6-105">Publish the app</span></span>

<span data-ttu-id="34be6-106">Os aplicativos são publicados para implantação na configuração de versão.</span><span class="sxs-lookup"><span data-stu-id="34be6-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="34be6-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="34be6-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="34be6-108">Selecione **Versão** > **Publicar {APLICATIVO}** na barra de navegação.</span><span class="sxs-lookup"><span data-stu-id="34be6-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="34be6-109">Selecione o botão *destino de publicação*.</span><span class="sxs-lookup"><span data-stu-id="34be6-109">Select the *publish target*.</span></span> <span data-ttu-id="34be6-110">Para publicar localmente, selecione **Pasta**.</span><span class="sxs-lookup"><span data-stu-id="34be6-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="34be6-111">Aceite o local padrão no campo **Escolher uma pasta** ou especifique um local diferente.</span><span class="sxs-lookup"><span data-stu-id="34be6-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="34be6-112">Selecione o botão **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="34be6-112">Select the **Publish** button.</span></span>

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[<span data-ttu-id="34be6-113">Visual Studio Code/CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="34be6-113">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="34be6-114">Use o comando [dotnet publish](/dotnet/core/tools/dotnet-publish) para publicar o aplicativo com uma configuração de versão:</span><span class="sxs-lookup"><span data-stu-id="34be6-114">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```console
dotnet publish -c Release
```

---

<span data-ttu-id="34be6-115">Publicar o aplicativo dispara uma [restauração](/dotnet/core/tools/dotnet-restore) das dependências do projeto e [compila](/dotnet/core/tools/dotnet-build) o projeto antes de criar os ativos para implantação.</span><span class="sxs-lookup"><span data-stu-id="34be6-115">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="34be6-116">Como parte do processo de build, os assemblies e métodos não usados são removidos para reduzir o tamanho de download do aplicativo e os tempos de carregamento.</span><span class="sxs-lookup"><span data-stu-id="34be6-116">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="34be6-117">Um aplicativo Blazor do lado do cliente é publicado na pasta */bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist*.</span><span class="sxs-lookup"><span data-stu-id="34be6-117">A Blazor client-side app is published to the */bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* folder.</span></span> <span data-ttu-id="34be6-118">Um aplicativo Blazor do lado do servidor é publicado na pasta */bin/Release/{ESTRUTURA DE DESTINO}/publish*.</span><span class="sxs-lookup"><span data-stu-id="34be6-118">A Blazor server-side app is published to the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>

<span data-ttu-id="34be6-119">Os ativos na pasta são implantados no servidor Web.</span><span class="sxs-lookup"><span data-stu-id="34be6-119">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="34be6-120">A implantação pode ser um processo manual ou automatizado, dependendo das ferramentas de desenvolvimento em uso.</span><span class="sxs-lookup"><span data-stu-id="34be6-120">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="deployment"></a><span data-ttu-id="34be6-121">Implantação</span><span class="sxs-lookup"><span data-stu-id="34be6-121">Deployment</span></span>

<span data-ttu-id="34be6-122">Confira orientações de implantação nos tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="34be6-122">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/client-side>
* <xref:host-and-deploy/blazor/server-side>

## <a name="blazor-serverless-hosting-with-azure-storage"></a><span data-ttu-id="34be6-123">Hospedagem sem servidor do Blazor com o Armazenamento do Azure</span><span class="sxs-lookup"><span data-stu-id="34be6-123">Blazor serverless hosting with Azure Storage</span></span>

<span data-ttu-id="34be6-124">Aplicativos do lado do cliente do Blazor podem ser fornecidos do [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) como conteúdo estático diretamente de um contêiner de armazenamento.</span><span class="sxs-lookup"><span data-stu-id="34be6-124">Blazor client-side apps can be served from [Azure Storage](https://azure.microsoft.com/services/storage/) as static content directly from a storage container.</span></span>

<span data-ttu-id="34be6-125">Para saber mais, confira [Hospedar e implantar o ASP.NET Core Blazor no lado do cliente (implantação autônoma): Armazenamento do Azure](xref:host-and-deploy/blazor/client-side#azure-storage).</span><span class="sxs-lookup"><span data-stu-id="34be6-125">For more information, see [Host and deploy ASP.NET Core Blazor client-side (Standalone deployment): Azure Storage](xref:host-and-deploy/blazor/client-side#azure-storage).</span></span>
