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
# <a name="host-and-deploy-aspnet-core-blazor"></a>Hospedar e implantar o ASP.NET Core Blazor

Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)

## <a name="publish-the-app"></a>Publique o aplicativo

Os aplicativos são publicados para implantação na configuração de versão.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Selecione **Versão** > **Publicar {APLICATIVO}** na barra de navegação.
1. Selecione o botão *destino de publicação*. Para publicar localmente, selecione **Pasta**.
1. Aceite o local padrão no campo **Escolher uma pasta** ou especifique um local diferente. Selecione o botão **Publicar**.

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[Visual Studio Code/CLI do .NET Core](#tab/visual-studio-code+netcore-cli)

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
