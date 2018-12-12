---
title: Ferramentas e downloads - DevOps com o ASP.NET Core e o Azure
author: CamSoper
description: Ferramentas e downloads necessários para operações de desenvolvimento com ASP.NET Core e o Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 0c64e723f1b912323103f201a66c1edaeccdcc2d
ms.sourcegitcommit: b34b25da2ab68e6495b2460ff570468f16a9bf0d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284338"
---
# <a name="tools-and-downloads"></a><span data-ttu-id="7fd62-103">Ferramentas e downloads</span><span class="sxs-lookup"><span data-stu-id="7fd62-103">Tools and downloads</span></span>

<span data-ttu-id="7fd62-104">O Azure tem várias interfaces para provisionamento e gerenciamento de recursos, como o [portal do Azure](https://portal.azure.com), [CLI do Azure](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [nuvem do Azure Shell](https://shell.azure.com/bash)e o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7fd62-104">Azure has several interfaces for provisioning and managing resources, such as the [Azure portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), and Visual Studio.</span></span> <span data-ttu-id="7fd62-105">Este guia usa uma abordagem minimalista e usa o Azure Cloud Shell sempre que possível para reduzir as etapas necessárias.</span><span class="sxs-lookup"><span data-stu-id="7fd62-105">This guide takes a minimalist approach and uses the Azure Cloud Shell whenever possible to reduce the steps required.</span></span> <span data-ttu-id="7fd62-106">No entanto, o portal do Azure deve ser usado para algumas partes.</span><span class="sxs-lookup"><span data-stu-id="7fd62-106">However, the Azure portal must be used for some portions.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7fd62-107">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="7fd62-107">Prerequisites</span></span>

<span data-ttu-id="7fd62-108">As assinaturas a seguir são necessárias:</span><span class="sxs-lookup"><span data-stu-id="7fd62-108">The following subscriptions are required:</span></span>

* <span data-ttu-id="7fd62-109">Azure &mdash; se você não tiver uma conta [Obtenha uma avaliação gratuita](https://azure.microsoft.com/free/).</span><span class="sxs-lookup"><span data-stu-id="7fd62-109">Azure &mdash; If you don't have an account, [get a free trial](https://azure.microsoft.com/free/).</span></span>
* <span data-ttu-id="7fd62-110">Os serviços do Azure DevOps &mdash; sua assinatura de DevOps do Azure e a organização é criado no capítulo 4.</span><span class="sxs-lookup"><span data-stu-id="7fd62-110">Azure DevOps Services &mdash; your Azure DevOps subscription and organization is created in Chapter 4.</span></span>
* <span data-ttu-id="7fd62-111">GitHub &mdash; se você não tiver uma conta [Inscreva-se gratuitamente](https://github.com/join).</span><span class="sxs-lookup"><span data-stu-id="7fd62-111">GitHub &mdash; If you don't have an account, [sign up for free](https://github.com/join).</span></span>

<span data-ttu-id="7fd62-112">As ferramentas a seguir são necessárias:</span><span class="sxs-lookup"><span data-stu-id="7fd62-112">The following tools are required:</span></span>

* <span data-ttu-id="7fd62-113">[Git](https://git-scm.com/downloads) &mdash; um entendimento fundamental do Git é recomendado para este guia.</span><span class="sxs-lookup"><span data-stu-id="7fd62-113">[Git](https://git-scm.com/downloads) &mdash; A fundamental understanding of Git is recommended for this guide.</span></span> <span data-ttu-id="7fd62-114">Examine os [documentação do Git](https://git-scm.com/doc), especificamente [git remoto](https://git-scm.com/docs/git-remote) e [git push](https://git-scm.com/docs/git-push).</span><span class="sxs-lookup"><span data-stu-id="7fd62-114">Review the [Git documentation](https://git-scm.com/doc), specifically [git remote](https://git-scm.com/docs/git-remote) and [git push](https://git-scm.com/docs/git-push).</span></span>
* <span data-ttu-id="7fd62-115">[SDK do .NET core](https://www.microsoft.com/net/download/) &mdash; versão 2.1.300 ou posterior é necessário para compilar e executar o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="7fd62-115">[.NET Core SDK](https://www.microsoft.com/net/download/) &mdash; Version 2.1.300 or later is required to build and run the sample app.</span></span> <span data-ttu-id="7fd62-116">Se o Visual Studio é instalado com o **desenvolvimento de plataforma cruzada do .NET Core** carga de trabalho, o SDK do .NET Core já está instalada.</span><span class="sxs-lookup"><span data-stu-id="7fd62-116">If Visual Studio is installed with the **.NET Core cross-platform development** workload, the .NET Core SDK is already installed.</span></span>

    <span data-ttu-id="7fd62-117">Verifique se a instalação do SDK do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="7fd62-117">Verify your .NET Core SDK installation.</span></span> <span data-ttu-id="7fd62-118">Abra um shell de comando e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="7fd62-118">Open a command shell, and run the following command:</span></span>

    ```console
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a><span data-ttu-id="7fd62-119">Ferramentas recomendadas (somente Windows)</span><span class="sxs-lookup"><span data-stu-id="7fd62-119">Recommended tools (Windows only)</span></span>

* <span data-ttu-id="7fd62-120">[Visual Studio](https://www.visualstudio.com/)do robustas ferramentas do Azure fornecem uma interface gráfica para a maioria das funcionalidades descritas neste guia.</span><span class="sxs-lookup"><span data-stu-id="7fd62-120">[Visual Studio](https://www.visualstudio.com/)'s robust Azure tools provide a GUI for most of the functionality described in this guide.</span></span> <span data-ttu-id="7fd62-121">Qualquer edição do Visual Studio funcionará, incluindo o Visual Studio Community Edition gratuito.</span><span class="sxs-lookup"><span data-stu-id="7fd62-121">Any edition of Visual Studio will work, including the free Visual Studio Community Edition.</span></span> <span data-ttu-id="7fd62-122">Os tutoriais são gravados para demonstrar o desenvolvimento, implantação e operações de desenvolvimento com e sem o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7fd62-122">The tutorials are written to demonstrate development, deployment, and DevOps both with and without Visual Studio.</span></span>

  <span data-ttu-id="7fd62-123">Confirme se o Visual Studio tem o seguinte [cargas de trabalho](/visualstudio/install/modify-visual-studio) instalado:</span><span class="sxs-lookup"><span data-stu-id="7fd62-123">Confirm that Visual Studio has the following [workloads](/visualstudio/install/modify-visual-studio) installed:</span></span>

  * <span data-ttu-id="7fd62-124">Desenvolvimento do ASP.NET e para a Web</span><span class="sxs-lookup"><span data-stu-id="7fd62-124">ASP.NET and web development</span></span>
  * <span data-ttu-id="7fd62-125">Desenvolvimento do Azure</span><span class="sxs-lookup"><span data-stu-id="7fd62-125">Azure development</span></span>
  * <span data-ttu-id="7fd62-126">Desenvolvimento de plataforma cruzada do .NET Core</span><span class="sxs-lookup"><span data-stu-id="7fd62-126">.NET Core cross-platform development</span></span>
