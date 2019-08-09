---
title: Hospedar e implantar o ASP.NET Core Blazor
author: guardrex
description: Descubra como hospedar e implantar aplicativos Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/14/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: d18abbf33c71dca5130bfc6b503b46c1d5bce537
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68913934"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a>Hospedar e implantar o ASP.NET Core Blazor

Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)

## <a name="publish-the-app"></a>Publique o aplicativo

Os aplicativos são publicados para implantação na configuração de versão.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Selecione **Versão** > **Publicar {APLICATIVO}** na barra de navegação.
1. Selecione o botão *destino de publicação*. Para publicar localmente, selecione **Pasta**.
1. Aceite o local padrão no campo **Escolher uma pasta** ou especifique um local diferente. Selecione o botão **Publicar**.

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Use o comando [dotnet publish](/dotnet/core/tools/dotnet-publish) para publicar o aplicativo com uma configuração de versão:

```console
dotnet publish -c Release
```

---

Publicar o aplicativo dispara uma [restauração](/dotnet/core/tools/dotnet-restore) das dependências do projeto e [compila](/dotnet/core/tools/dotnet-build) o projeto antes de criar os ativos para implantação. Como parte do processo de build, os assemblies e métodos não usados são removidos para reduzir o tamanho de download do aplicativo e os tempos de carregamento.

Um aplicativo Blazor do lado do cliente é publicado na pasta */bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist*. Um aplicativo Blazor do lado do servidor é publicado na pasta */bin/Release/{ESTRUTURA DE DESTINO}/publish*.

Os ativos na pasta são implantados no servidor Web. A implantação pode ser um processo manual ou automatizado, dependendo das ferramentas de desenvolvimento em uso.

## <a name="deployment"></a>Implantação

Confira orientações de implantação nos tópicos a seguir:

* <xref:host-and-deploy/blazor/client-side>
* <xref:host-and-deploy/blazor/server-side>

## <a name="blazor-serverless-hosting-with-azure-storage"></a>Hospedagem sem servidor do Blazor com o Armazenamento do Azure

Aplicativos do lado do cliente do Blazor podem ser fornecidos do [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) como conteúdo estático diretamente de um contêiner de armazenamento.

Para saber mais, confira [Hospedar e implantar o ASP.NET Core Blazor no lado do cliente (implantação autônoma): Armazenamento do Azure](xref:host-and-deploy/blazor/client-side#azure-storage).
