---
title: Ferramentas e downloads - DevOps com o ASP.NET Core e o Azure
author: CamSoper
description: Ferramentas e downloads necessários para operações de desenvolvimento com ASP.NET Core e o Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 9c1042dd48b9167209b46e97a09e011b80e2609c
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511139"
---
# <a name="tools-and-downloads"></a>Ferramentas e downloads

O Azure tem várias interfaces para provisionar e gerenciar recursos, como o [portal do Azure](https://portal.azure.com), [CLI do Azure](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure cloud Shell](https://shell.azure.com/bash)e Visual Studio. Este guia usa uma abordagem minimalista e usa o Azure Cloud Shell sempre que possível para reduzir as etapas necessárias. No entanto, o portal do Azure deve ser usado para algumas partes.

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

As assinaturas a seguir são necessárias:

* &mdash; do Azure se você não tiver uma conta, [obtenha uma avaliação gratuita](https://azure.microsoft.com/free/).
* Azure DevOps Services &mdash; sua assinatura e organização do DevOps do Azure é criada no capítulo 4.
* O GitHub &mdash; se você não tiver uma conta, [Inscreva-se gratuitamente](https://github.com/join).

As ferramentas a seguir são necessárias:

* O [git](https://git-scm.com/downloads) &mdash; um entendimento fundamental do git é recomendado para este guia. Examine a [documentação do git](https://git-scm.com/doc), especificamente [git remoto](https://git-scm.com/docs/git-remote) e [git Push](https://git-scm.com/docs/git-push).
* [SDK do .NET Core](https://dotnet.microsoft.com/download/) &mdash; versão 2.1.300 ou posterior é necessária para compilar e executar o aplicativo de exemplo. Se o Visual Studio estiver instalado com a carga de trabalho de **desenvolvimento de plataforma cruzada do .NET Core** , o SDK do .NET Core já estará instalado.

    Verifique se a instalação do SDK do .NET Core. Abra um shell de comando e execute o seguinte comando:

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a>Ferramentas recomendadas (somente Windows)

* As ferramentas robustas do Azure do [Visual Studio](https://visualstudio.microsoft.com)fornecem uma GUI para a maioria das funcionalidades descritas neste guia. Qualquer edição do Visual Studio funcionará, incluindo o Visual Studio Community Edition gratuito. Os tutoriais são gravados para demonstrar o desenvolvimento, implantação e operações de desenvolvimento com e sem o Visual Studio.

  Confirme se o Visual Studio tem as seguintes [cargas de trabalho](/visualstudio/install/modify-visual-studio) instaladas:

  * Desenvolvimento do ASP.NET e para a Web
  * Desenvolvimento do Azure
  * Desenvolvimento de plataforma cruzada do .NET Core
