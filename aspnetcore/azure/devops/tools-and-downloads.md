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
# <a name="tools-and-downloads"></a>Ferramentas e downloads

O Azure tem várias interfaces para provisionamento e gerenciamento de recursos, como o [portal do Azure](https://portal.azure.com), [CLI do Azure](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [nuvem do Azure Shell](https://shell.azure.com/bash)e o Visual Studio. Este guia usa uma abordagem minimalista e usa o Azure Cloud Shell sempre que possível para reduzir as etapas necessárias. No entanto, o portal do Azure deve ser usado para algumas partes.

## <a name="prerequisites"></a>Pré-requisitos

As assinaturas a seguir são necessárias:

* Azure &mdash; se você não tiver uma conta [Obtenha uma avaliação gratuita](https://azure.microsoft.com/free/).
* Os serviços do Azure DevOps &mdash; sua assinatura de DevOps do Azure e a organização é criado no capítulo 4.
* GitHub &mdash; se você não tiver uma conta [Inscreva-se gratuitamente](https://github.com/join).

As ferramentas a seguir são necessárias:

* [Git](https://git-scm.com/downloads) &mdash; um entendimento fundamental do Git é recomendado para este guia. Examine os [documentação do Git](https://git-scm.com/doc), especificamente [git remoto](https://git-scm.com/docs/git-remote) e [git push](https://git-scm.com/docs/git-push).
* [SDK do .NET core](https://www.microsoft.com/net/download/) &mdash; versão 2.1.300 ou posterior é necessário para compilar e executar o aplicativo de exemplo. Se o Visual Studio é instalado com o **desenvolvimento de plataforma cruzada do .NET Core** carga de trabalho, o SDK do .NET Core já está instalada.

    Verifique se a instalação do SDK do .NET Core. Abra um shell de comando e execute o seguinte comando:

    ```console
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a>Ferramentas recomendadas (somente Windows)

* [Visual Studio](https://www.visualstudio.com/)do robustas ferramentas do Azure fornecem uma interface gráfica para a maioria das funcionalidades descritas neste guia. Qualquer edição do Visual Studio funcionará, incluindo o Visual Studio Community Edition gratuito. Os tutoriais são gravados para demonstrar o desenvolvimento, implantação e operações de desenvolvimento com e sem o Visual Studio.

  Confirme se o Visual Studio tem o seguinte [cargas de trabalho](/visualstudio/install/modify-visual-studio) instalado:

  * Desenvolvimento do ASP.NET e para a Web
  * Desenvolvimento do Azure
  * Desenvolvimento de plataforma cruzada do .NET Core
