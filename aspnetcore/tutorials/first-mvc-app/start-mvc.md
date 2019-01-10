---
title: Introdução ao ASP.NET Core MVC
author: rick-anderson
monikerRange: '>= aspnetcore-2.2'
description: Saiba como começar a usar o ASP.NET Core MVC.
ms.author: riande
ms.date: 12/12/2018
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: cfce3b5792a5d0673bae5ddbba9e2d4d515a6279
ms.sourcegitcommit: 4e87712029de2aceb1cf2c52e9e3dda8195a5b8e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53381797"
---
# <a name="get-started-with-aspnet-core-mvc"></a>Introdução ao ASP.NET Core MVC

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [consider RP](~/includes/razor.md)]

https://docs.microsoft.com/en-us/visualstudio/ide/visual-studio-ide?view=vs-2017

Este tutorial ensina as noções básicas de criação de um aplicativo Web ASP.NET Core MVC.

O aplicativo gerencia um banco de dados de títulos de filmes. Você aprenderá como:

> [!div class="checklist"]
> * Criar um aplicativo Web.
> * Adicionar e gerar o scaffolding de um modelo.
> * Trabalhar com um banco de dados.
> * Adicionar pesquisa e validação.

No final, você terá um aplicativo que pode gerenciar e exibir dados de filmes.

[!INCLUDE[](~/includes/mvc-intro/download.md)]

> [!NOTE]
> Estamos testando a usabilidade de uma nova estrutura proposta para o sumário do ASP.NET Core.  Se você tiver alguns minutos para experimentar um exercício de localização de sete tópicos diferentes no sumário atual ou proposto, [clique aqui para participar do estudo](https://dpk4xbh5.optimalworkshop.com/treejack/aa11wn82).

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-web-app"></a>Como criar um aplicativo Web

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

No Visual Studio, selecione **Arquivo > Novo > Projeto**.

![Arquivo > Novo > Projeto](start-mvc/_static/alt_new_project.png)

Complete a caixa de diálogo **Novo Projeto**:

* No painel esquerdo, selecione **.NET Core**
* No painel central, selecione **Aplicativo Web ASP.NET Core (.NET Core)**
* Nomeie o projeto "MvcMovie" (é importante nomear o projeto "MvcMovie" para que, quando você copiar o código, o namespace corresponda).
* Selecione **OK**

![Caixa de diálogo Novo projeto, .NET Core no painel esquerdo, Web do ASP.NET Core ](start-mvc/_static/new_project2-21.png)

Faça as configurações necessárias na caixa de diálogo **Novo aplicativo Web ASP.NET Core (.NET Core) – MvcMovie**:

* Na caixa de lista suspensa do seletor de versão, selecione **ASP.NET Core 2.2**
* Selecione **Aplicativo Web (Model-View-Controller)**
* Selecione **OK**.

![Caixa de diálogo Novo projeto, .NET Core no painel esquerdo, Web do ASP.NET Core ](start-mvc/_static/new_project22-21.png)

O Visual Studio usou um modelo padrão para o projeto MVC que você acabou de criar. Para que o aplicativo comece a funcionar agora mesmo, digite um nome de projeto e selecione algumas opções. Este é um projeto inicial básico e é um bom ponto de partida.

Pressione **Ctrl+F5** para executar o aplicativo no modo sem depuração.

* O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo. Observe que a barra de endereços mostra `localhost:port#` e não algo como `example.com`. Isso ocorre porque `localhost` é o nome do host padrão do computador local. Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web. Na imagem acima, o número da porta é 5000. A URL no navegador mostra `localhost:5000`. Quando você executar o aplicativo, verá um número de porta diferente.
* Iniciar o aplicativo com **Ctrl+F5** (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código. Muitos desenvolvedores preferem usar modo de não depuração para iniciar o aplicativo e exibir alterações rapidamente.
* Você pode iniciar o aplicativo no modo de não depuração ou de depuração por meio do item de menu **Depurar**:

![Menu Depurar](start-mvc/_static/debug_menu.png)

* Você pode depurar o aplicativo selecionando o botão **IIS Express**

![IIS Express](start-mvc/_static/iis_express.png)

<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

O tutorial pressupõe que você já tenha familiaridade com o VS Code. Consulte [Introdução ao VS Code](https://code.visualstudio.com/docs) e [Ajuda do Visual Studio Code](#visual-studio-code-help) para obter mais informações.

* Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Altere os diretórios (`cd`) para uma pasta que conterá o projeto.
* Execute o seguinte comando:

   ```console
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Uma caixa de diálogo é exibida com **Os ativos necessários para build e depuração estão ausentes de 'MvcMovie'. Deseja adicioná-los?**  Selecione **Sim**

  * `dotnet new mvc -o MvcMovie`: cria um novo projeto do ASP.NET Core MVC na pasta *MvcMovie*.
  * `code -r MvcMovie`: carrega o arquivo de projeto *MvcMovie.csproj* no Visual Studio Code.

### <a name="launch-the-app"></a>Iniciar o aplicativo

* Pressione **Ctrl-F5** para execução sem o depurador.

  O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`. A barra de endereços mostra `localhost:port:5001` e não algo como `example.com`. Isso ocorre porque `localhost` é o nome do host padrão do computador local. Localhost serve somente solicitações da Web do computador local.

  Iniciar o aplicativo com **Ctrl+F5** (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código. Muitos desenvolvedores preferem usar o modo sem depuração para atualizar a página e exibir alterações.

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Selecione **Arquivo** > **Nova Solução**.

  ![Nova solução do macOS](~/tutorials/first-web-api-mac/_static/sln.png)

* Selecione **Aplicativo .NET Core** > **ASP.NET Core** > **Aplicativo Web ASP.NET Core (MVC)** > **Próximo**.

  ![Caixa de diálogo Novo projeto do macOS](~/tutorials/first-mvc-app-mac/start-mvc/1.png)

* Na caixa de diálogo **Configurar sua nova API Web do ASP.NET Core**, aceite a **Estrutura de Destino** padrão **.NET Core 2.2*.

* Nomeie o projeto **MvcMovie** e, em seguida, selecione **Criar**.

### <a name="launch-the-app"></a>Iniciar o aplicativo

Selecione **Executar** > **Iniciar Sem Depuração** para iniciar o aplicativo. O Visual Studio para Mac inicia o servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia um navegador e navega para `http://localhost:port`, em que *porta* é um número da porta escolhido aleatoriamente.

* A barra de endereços mostra `localhost:port#` e não algo como `example.com`. Isso ocorre porque `localhost` é o nome do host padrão do computador local. Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web. Quando você executar o aplicativo, verá um número da porta diferente.
* Você pode iniciar o aplicativo no modo de depuração ou sem depuração no item de menu **Executar**.

---  
<!-- End of VS tabs -->

* Selecione **Aceitar** para dar consentimento de rastreamento. Este aplicativo não acompanha informações pessoais. O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).

  ![Página Inicial ou de Índice](start-mvc/_static/privacy.png)

  A imagem a seguir mostra o aplicativo depois de aceitar o rastreamento:

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

Na próxima parte deste tutorial, você saberá mais sobre o MVC e começará a escrever um pouco de código.

> [!div class="step-by-step"]
> [Avançar](adding-controller.md)  
