---
title: Introdução às Páginas do Razor no ASP.NET Core
author: rick-anderson
description: Esta série de tutoriais mostra como usar Razor Pages no ASP.NET Core. Saiba como criar um modelo, gerar código para Razor Pages, usar o Entity Framework Core e o SQL Server para acesso a dados, adicionar funcionalidade de pesquisa, adicionar validação de entrada e usar migrações para atualizar o modelo.
ms.author: riande
ms.date: 05/30/2018
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: ca5b134aaa0a9218bc3b0466c3448bd41e8cef8b
ms.sourcegitcommit: e9b99854b0a8021dafabee0db5e1338067f250a9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52450730"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a>Tutorial: introdução ao Razor Pages no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range="= aspnetcore-2.0"

Recomendamos que você siga a versão ASP.NET Core 2.1 deste tutorial. É **muito** mais fácil de seguir e cobre muito mais recursos. Selecione **ASP.NET Core 2.1** no seletor de versão.

![Seletor de versão no sumário](razor-pages-start/_static/v21.png)

::: moniker-end

Este tutorial ensina as noções básicas de criação de um aplicativo Web de Páginas do Razor do ASP.NET Core. As Páginas do Razor são a maneira recomendada para criar a interface do usuário para aplicativos Web no ASP.NET Core.

Há três versões deste tutorial:

* Windows: este tutorial
* macOS: [Introdução a Razor Pages com o Visual Studio para Mac](xref:tutorials/razor-pages-mac/razor-pages-start)
* macOS, Linux e Windows: [Introdução a Páginas Razor no ASP.NET Core no Visual Studio Code](xref:tutorials/razor-pages-vsc/razor-pages-start)

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample) ([como baixar](xref:index#how-to-download-a-sample))

> [!NOTE]
> Estamos testando a usabilidade de uma nova estrutura proposta para o sumário do ASP.NET Core.  Se você tiver alguns minutos para experimentar um exercício de localização de sete tópicos diferentes no sumário atual ou proposto, [clique aqui para participar do estudo](https://dpk4xbh5.optimalworkshop.com/treejack/rps16hd5).

::: moniker range=">= aspnetcore-2.1"

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Prerequisites](~/includes/net-core-prereqs-windows.md)]

## <a name="create-a-razor-web-app"></a>Criar um aplicativo Web do Razor

* No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.
* Crie um novo Aplicativo Web ASP.NET Core. Nomeie o projeto **RazorPagesMovie**. É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar/colar código.
 ![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)
* Selecione **ASP.NET Core 2.1** na lista suspensa e selecione **Aplicativo Web**.

 ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2_2.1.png)

O modelo do Visual Studio cria um projeto inicial:

![Gerenciador de Soluções](razor-pages-start/_static/se2.1.png)

Pressione **F5** para executar o aplicativo no modo de depuração ou **Ctrl-F5** para executar sem anexar o depurador. Selecione **Aceitar** para dar consentimento ao acompanhamento. Este aplicativo não acompanha informações pessoais. O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).

![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR.png)

A imagem a seguir mostra o aplicativo depois de aceitar o rastreamento:

![Página Inicial ou de Índice](razor-pages-start/_static/home2.1.png)

* O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo. A barra de endereços mostra `localhost:port#` e não algo como `example.com`. Isso ocorre porque `localhost` é o nome do host padrão do computador local. Localhost serve somente solicitações da Web do computador local. Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web. Na imagem anterior, o número da porta é 5001. Quando você executar o aplicativo, verá um número de porta diferente.
* Iniciar o aplicativo com **Ctrl+F5** (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código. Muitos desenvolvedores preferem usar o modo de não depuração para iniciar o aplicativo rapidamente e exibir alterações.

[!INCLUDE [razor-pages-start](~/includes/RP/2.1/razor-pages-start.md)]

[!INCLUDE [F7](~/includes/RP/F7.md)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Prerequisites](~/includes/net-core-prereqs-windows.md)]

## <a name="create-a-razor-web-app"></a>Criar um aplicativo Web do Razor

* No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.
* Crie um novo Aplicativo Web ASP.NET Core. Nomeie o projeto **RazorPagesMovie**. É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar/colar código.
  ![novo aplicativo Web ASP.NET Core](../../razor-pages/index/_static/np.png)
* Selecione **ASP.NET Core 2.0** na lista suspensa e selecione **Aplicativo Web**.

  [!INCLUDE [install 2.0](~/includes/dotnetcore-on-dotnetfx-vs.md)]

O modelo do Visual Studio cria um projeto inicial:

![Gerenciador de Soluções](razor-pages-start/_static/se.png)

Pressione **F5** para executar o aplicativo no modo de depuração ou **Ctrl-F5** para executar sem anexar o depurador

![Página Inicial ou de Índice](razor-pages-start/_static/home.png)

* O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo. A barra de endereços mostra `localhost:port#` e não algo como `example.com`. Isso ocorre porque `localhost` é o nome do host padrão do computador local. Localhost serve somente solicitações da Web do computador local. Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web. Na imagem anterior, o número da porta é 5000. Quando você executar o aplicativo, verá um número de porta diferente.
* Iniciar o aplicativo com **Ctrl+F5** (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código. Muitos desenvolvedores preferem usar o modo de não depuração para iniciar o aplicativo rapidamente e exibir alterações.

[!INCLUDE [razor-pages-start](~/includes/RP/razor-pages-start.md)]

::: moniker-end

> [!div class="step-by-step"]
> [Próximo: adicionando um modelo](xref:tutorials/razor-pages/model)
