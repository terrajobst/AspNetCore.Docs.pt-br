---
title: Ferramentas e downloads - DevOps com o ASP.NET Core e o Azure
author: CamSoper
description: Ferramentas e downloads necessários para operações de desenvolvimento com ASP.NET Core e o Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 25ce311373b0aaddfa3bc2728c39e503acbca69d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659484"
---
# <a name="tools-and-downloads"></a><span data-ttu-id="ef421-103">Ferramentas e downloads</span><span class="sxs-lookup"><span data-stu-id="ef421-103">Tools and downloads</span></span>

<span data-ttu-id="ef421-104">O Azure tem várias interfaces para provisionar e gerenciar recursos, como o [portal do Azure](https://portal.azure.com), [CLI do Azure](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure cloud Shell](https://shell.azure.com/bash)e Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ef421-104">Azure has several interfaces for provisioning and managing resources, such as the [Azure portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), and Visual Studio.</span></span> <span data-ttu-id="ef421-105">Este guia usa uma abordagem minimalista e usa o Azure Cloud Shell sempre que possível para reduzir as etapas necessárias.</span><span class="sxs-lookup"><span data-stu-id="ef421-105">This guide takes a minimalist approach and uses the Azure Cloud Shell whenever possible to reduce the steps required.</span></span> <span data-ttu-id="ef421-106">No entanto, o portal do Azure deve ser usado para algumas partes.</span><span class="sxs-lookup"><span data-stu-id="ef421-106">However, the Azure portal must be used for some portions.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ef421-107">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="ef421-107">Prerequisites</span></span>

<span data-ttu-id="ef421-108">As assinaturas a seguir são necessárias:</span><span class="sxs-lookup"><span data-stu-id="ef421-108">The following subscriptions are required:</span></span>

* <span data-ttu-id="ef421-109">&mdash; do Azure se você não tiver uma conta, [obtenha uma avaliação gratuita](https://azure.microsoft.com/free/).</span><span class="sxs-lookup"><span data-stu-id="ef421-109">Azure &mdash; If you don't have an account, [get a free trial](https://azure.microsoft.com/free/).</span></span>
* <span data-ttu-id="ef421-110">Azure DevOps Services &mdash; sua assinatura e organização do DevOps do Azure é criada no capítulo 4.</span><span class="sxs-lookup"><span data-stu-id="ef421-110">Azure DevOps Services &mdash; your Azure DevOps subscription and organization is created in Chapter 4.</span></span>
* <span data-ttu-id="ef421-111">O GitHub &mdash; se você não tiver uma conta, [Inscreva-se gratuitamente](https://github.com/join).</span><span class="sxs-lookup"><span data-stu-id="ef421-111">GitHub &mdash; If you don't have an account, [sign up for free](https://github.com/join).</span></span>

<span data-ttu-id="ef421-112">As ferramentas a seguir são necessárias:</span><span class="sxs-lookup"><span data-stu-id="ef421-112">The following tools are required:</span></span>

* <span data-ttu-id="ef421-113">O [git](https://git-scm.com/downloads) &mdash; um entendimento fundamental do git é recomendado para este guia.</span><span class="sxs-lookup"><span data-stu-id="ef421-113">[Git](https://git-scm.com/downloads) &mdash; A fundamental understanding of Git is recommended for this guide.</span></span> <span data-ttu-id="ef421-114">Examine a [documentação do git](https://git-scm.com/doc), especificamente [git remoto](https://git-scm.com/docs/git-remote) e [git Push](https://git-scm.com/docs/git-push).</span><span class="sxs-lookup"><span data-stu-id="ef421-114">Review the [Git documentation](https://git-scm.com/doc), specifically [git remote](https://git-scm.com/docs/git-remote) and [git push](https://git-scm.com/docs/git-push).</span></span>
* <span data-ttu-id="ef421-115">[SDK do .NET Core](https://www.microsoft.com/net/download/) &mdash; versão 2.1.300 ou posterior é necessária para compilar e executar o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="ef421-115">[.NET Core SDK](https://www.microsoft.com/net/download/) &mdash; Version 2.1.300 or later is required to build and run the sample app.</span></span> <span data-ttu-id="ef421-116">Se o Visual Studio estiver instalado com a carga de trabalho de **desenvolvimento de plataforma cruzada do .NET Core** , o SDK do .NET Core já estará instalado.</span><span class="sxs-lookup"><span data-stu-id="ef421-116">If Visual Studio is installed with the **.NET Core cross-platform development** workload, the .NET Core SDK is already installed.</span></span>

    <span data-ttu-id="ef421-117">Verifique se a instalação do SDK do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ef421-117">Verify your .NET Core SDK installation.</span></span> <span data-ttu-id="ef421-118">Abra um shell de comando e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="ef421-118">Open a command shell, and run the following command:</span></span>

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a><span data-ttu-id="ef421-119">Ferramentas recomendadas (somente Windows)</span><span class="sxs-lookup"><span data-stu-id="ef421-119">Recommended tools (Windows only)</span></span>

* <span data-ttu-id="ef421-120">As ferramentas robustas do Azure do [Visual Studio](https://visualstudio.microsoft.com)fornecem uma GUI para a maioria das funcionalidades descritas neste guia.</span><span class="sxs-lookup"><span data-stu-id="ef421-120">[Visual Studio](https://visualstudio.microsoft.com)'s robust Azure tools provide a GUI for most of the functionality described in this guide.</span></span> <span data-ttu-id="ef421-121">Qualquer edição do Visual Studio funcionará, incluindo o Visual Studio Community Edition gratuito.</span><span class="sxs-lookup"><span data-stu-id="ef421-121">Any edition of Visual Studio will work, including the free Visual Studio Community Edition.</span></span> <span data-ttu-id="ef421-122">Os tutoriais são gravados para demonstrar o desenvolvimento, implantação e operações de desenvolvimento com e sem o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ef421-122">The tutorials are written to demonstrate development, deployment, and DevOps both with and without Visual Studio.</span></span>

  <span data-ttu-id="ef421-123">Confirme se o Visual Studio tem as seguintes [cargas de trabalho](/visualstudio/install/modify-visual-studio) instaladas:</span><span class="sxs-lookup"><span data-stu-id="ef421-123">Confirm that Visual Studio has the following [workloads](/visualstudio/install/modify-visual-studio) installed:</span></span>

  * <span data-ttu-id="ef421-124">Desenvolvimento Web e ASP.NET</span><span class="sxs-lookup"><span data-stu-id="ef421-124">ASP.NET and web development</span></span>
  * <span data-ttu-id="ef421-125">Desenvolvimento do Azure</span><span class="sxs-lookup"><span data-stu-id="ef421-125">Azure development</span></span>
  * <span data-ttu-id="ef421-126">Desenvolvimento multiplataforma com o .NET Core</span><span class="sxs-lookup"><span data-stu-id="ef421-126">.NET Core cross-platform development</span></span>
