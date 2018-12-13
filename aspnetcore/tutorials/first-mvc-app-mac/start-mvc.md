---
title: Introdução ao ASP.NET Core MVC e ao Visual Studio para Mac
author: rick-anderson
description: Saiba como começar a usar o ASP.NET Core MVC e o Visual Studio
ms.author: riande
ms.custom: mvc
ms.date: 12/01/2018
uid: tutorials/first-mvc-app-mac/start-mvc
ms.openlocfilehash: 1e3cc91003ac946ac2e5efcd79e20d9215ded902
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52862220"
---
# <a name="get-started-with-aspnet-core-mvc-and-visual-studio-for-mac"></a>Introdução ao ASP.NET Core MVC e ao Visual Studio para Mac

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Este tutorial ensina os conceitos básicos da criação de um aplicativo Web ASP.NET Core MVC usando o [Visual Studio para Mac](https://www.visualstudio.com/vs/visual-studio-mac/).

[!INCLUDE [consider RP](../../includes/razor.md)]

Há três versões deste tutorial:

* macOS: [Compilar um aplicativo ASP.NET Core MVC com o Visual Studio para Mac](xref:tutorials/first-mvc-app-mac/start-mvc)
* Windows: [Compilar um aplicativo ASP.NET Core MVC com o Visual Studio](xref:tutorials/first-mvc-app/start-mvc)
* Linux, macOS e Windows: [Compilar um aplicativo ASP.NET Core MVC com o Visual Studio Code](xref:tutorials/first-mvc-app-xplat/start-mvc)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [](~/includes/net-core-prereqs-macos.md)]

## <a name="create-a-web-app"></a>Como criar um aplicativo Web

No Visual Studio, selecione **Arquivo** > **Nova Solução**.

![Nova solução do macOS](../first-web-api-mac/_static/sln.png)

Selecione **Aplicativo .NET Core** > **ASP.NET Core** > **Aplicativo Web ASP.NET Core (MVC)** > **Próximo**.

![Caixa de diálogo Novo projeto do macOS](start-mvc/1.png)

Nomeie o projeto **MvcMovie** e, em seguida, selecione **Criar**.

![Caixa de diálogo Novo projeto do macOS](start-mvc/2.png)

### <a name="launch-the-app"></a>Iniciar o aplicativo

No Visual Studio, selecione **Executar** > **Iniciar Sem Depuração** para iniciar o aplicativo. O Visual Studio inicia o servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia um navegador e navega para `http://localhost:port`, em que *porta* é um número de porta escolhido aleatoriamente.

![Navegador com novo projeto](start-mvc/b1.png)

* A barra de endereços mostra `localhost:port#` e não algo como `example.com`. Isso ocorre porque `localhost` é o nome do host padrão do computador local. Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web. Quando você executar o aplicativo, verá um número da porta diferente.
* Você pode iniciar o aplicativo no modo de depuração ou sem depuração no item de menu **Executar**.

O modelo padrão fornece os links **Início**, **Sobre** e **Contato**. A imagem do navegador anterior não mostra esses links. Dependendo do tamanho do navegador, talvez você precise clicar no ícone de navegação para mostrá-los.

![Navegador com Novo projeto](start-mvc/b2.png)

Na próxima parte deste tutorial, você saberá mais sobre o MVC e começará a escrever um pouco de código.

> [!div class="step-by-step"]
> [Avançar](adding-controller.md)  
