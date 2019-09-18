---
title: 'Tutorial: Introdução às Páginas do Razor no ASP.NET Core'
author: rick-anderson
description: Esta série de tutoriais mostra como usar Razor Pages no ASP.NET Core. Saiba como criar um modelo, gerar código para Razor Pages, usar o Entity Framework Core e o SQL Server para acesso a dados, adicionar funcionalidade de pesquisa, adicionar validação de entrada e usar migrações para atualizar o modelo.
ms.author: riande
ms.date: 07/25/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 0cc00cb85b6054752417b82c783cfd4c306aeda5
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082570"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a>Tutorial: Introdução às Páginas do Razor no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"
Este é o primeiro tutorial de uma série que ensina as noções básicas da criação de um aplicativo Web do Razor Pages no ASP.NET Core.

[!INCLUDE[](~/includes/advancedRP.md)]

No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

Neste tutorial, você:

> [!div class="checklist"]
> * Criar um aplicativo Web do Razor Pages.
> * Execute o aplicativo.
> * Examinar os arquivos de projeto.

No final deste tutorial, você terá um aplicativo Web em funcionamento do Razor Pages que você criará em tutoriais posteriores.

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Pré-requisitos

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-razor-pages-web-app"></a>Criar um aplicativo Web das Páginas do Razor

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.
* Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.
  ![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)
* Nomeie o projeto **RazorPagesMovie**. É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.
  ![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)

* Selecione **ASP.NET Core 3.0** na lista suspensa **Aplicativo Web** e, em seguida, selecione **Criar**.

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  O seguinte projeto inicial é criado:

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Altere para o diretório (`cd`) que contém o projeto.

* Execute os seguintes comandos:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * O comando `dotnet new` cria um projeto do Razor Pages na pasta *RazorPagesMovie*.
  * O comando `code` abre a pasta *RazorPagesMovie* na instância atual do Visual Studio Code.

* Depois que o ícone de chama do OmniSharp da barra de status ficar verde, uma caixa de diálogo perguntará se **Os ativos necessários para criar e depurar estão ausentes no "RazorPagesMovie". Deseja adicioná-los?** Selecione **Sim** na barra superior.

  Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Selecione **Arquivo** > **Nova Solução**.

![Nova solução do macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* Selecione **.NET Core** > **Aplicativo** > **Aplicativo Web** > **Avançar**.

  ![Caixa de diálogo Novo projeto do macOS](razor-pages-start/_static/webapp.png)

* Na caixa de diálogo **Configurar a nova API Web do ASP.NET Core**, defina a **Estrutura de Destino** como **.NET Core 3.0**.

  ![Seleção do .NET Core 3.0 do macOS](razor-pages-start/_static/targetframework3.png)

* Nomeie o projeto **RazorPagesMovie** e, em seguida, selecione **Criar**.

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)


## <a name="open-the-project"></a>Abrir o projeto

No Visual Studio, selecione **Arquivo > Abrir** e, em seguida, selecione o arquivo *RazorPagesMovie.csproj*.

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Executar o aplicativo

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Pressione Ctrl + F5 para execução sem o depurador.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo. A barra de endereços mostra `localhost:port#` e não algo como `example.com`. Isso ocorre porque `localhost` é o nome do host padrão do computador local. Localhost serve somente solicitações da Web do computador local. Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.
 
# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Pressione **Ctrl-F5** para execução sem o depurador.

  O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `http://localhost:5001`. A barra de endereços mostra `localhost:port#` e não algo como `example.com`. Isso ocorre porque `localhost` é o nome do host padrão do computador local. Localhost serve somente solicitações da Web do computador local.

  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Pressione **Alt-Cmd-Enter** para executar sem o depurador. Como alternativa, navegue até a barra de menus e acesse Executar > Iniciar sem depuração.

  O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`.

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a>Examinar os arquivos de projeto

Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.

### <a name="pages-folder"></a>Pasta Páginas

Contém as Razor Pages e os arquivos de suporte. Cada Razor Page é um par de arquivos:

* Um arquivo *.cshtml* que contém a marcação HTML com o código C# usando a sintaxe Razor.
* Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.

Arquivos de suporte têm nomes que começam com um sublinhado. Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas. Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página. Para obter mais informações, consulte <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Pasta wwwroot

Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS. Para obter mais informações, consulte <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Contém dados de configuração, como cadeias de conexão. Para obter mais informações, consulte <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Module.vb

Contém o ponto de entrada para o programa. Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

contém código que configura o comportamento do aplicativo. Para obter mais informações, consulte <xref:fundamentals/startup>.

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo tutorial da série:

> [!div class="step-by-step"]
> [Adicionar um modelo](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

Este é o primeiro tutorial de uma série. [A série](xref:tutorials/razor-pages/index) ensina as noções básicas de criação de um aplicativo Web do Razor Pages do ASP.NET Core.

[!INCLUDE[](~/includes/advancedRP.md)]

No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

Neste tutorial, você:

> [!div class="checklist"]
> * Criar um aplicativo Web do Razor Pages.
> * Execute o aplicativo.
> * Examinar os arquivos de projeto.

No final deste tutorial, você terá um aplicativo Web em funcionamento do Razor Pages que você criará em tutoriais posteriores.

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Pré-requisitos

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a>Criar um aplicativo Web das Páginas do Razor

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.

* Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* Nomeie o projeto **RazorPagesMovie**. É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)

* Selecione **ASP.NET Core 2.2** na lista suspensa **Aplicativo Web** e, em seguida, selecione **Criar**.

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  O seguinte projeto inicial é criado:

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Altere para o diretório (`cd`) que contém o projeto.

* Execute os seguintes comandos:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * O comando `dotnet new` cria um projeto do Razor Pages na pasta *RazorPagesMovie*.
  * O comando `code` abre a pasta *RazorPagesMovie* na instância atual do Visual Studio Code.

* Depois que o ícone de chama do OmniSharp da barra de status ficar verde, uma caixa de diálogo perguntará se **Os ativos necessários para criar e depurar estão ausentes no "RazorPagesMovie". Deseja adicioná-los?** Selecione **Sim** na barra superior.

  Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Em um terminal, execute o seguinte comando:

<!-- TODO: update these instruction once mac support 2.2 projects -->

```dotnetcli
dotnet new webapp -o RazorPagesMovie
```

Os comandos anteriores usam a [CLI do .NET Core](/dotnet/core/tools/dotnet) para criar um projeto do Razor Pages.

## <a name="open-the-project"></a>Abrir o projeto

No Visual Studio, selecione **Arquivo > Abrir** e, em seguida, selecione o arquivo *RazorPagesMovie.csproj*.

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Executar o aplicativo

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Pressione Ctrl + F5 para execução sem o depurador.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo. A barra de endereços mostra `localhost:port#` e não algo como `example.com`. Isso ocorre porque `localhost` é o nome do host padrão do computador local. Localhost serve somente solicitações da Web do computador local. Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.

* Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.

  Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Pressione **Ctrl-F5** para execução sem o depurador.

  O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `http://localhost:5001`. A barra de endereços mostra `localhost:port#` e não algo como `example.com`. Isso ocorre porque `localhost` é o nome do host padrão do computador local. Localhost serve somente solicitações da Web do computador local.

* Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.

  Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Pressione **Cmd-Opt-F5** para execução sem o depurador.

  O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`.

* Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.

  Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2_safari.png)

  A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a>Examinar os arquivos de projeto

Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.

### <a name="pages-folder"></a>Pasta Páginas

Contém as Razor Pages e os arquivos de suporte. Cada Razor Page é um par de arquivos:

* Um arquivo *.cshtml* que contém a marcação HTML com o código C# usando a sintaxe Razor.
* Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.

Arquivos de suporte têm nomes que começam com um sublinhado. Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas. Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página. Para obter mais informações, consulte <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Pasta wwwroot

Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS. Para obter mais informações, consulte <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Contém dados de configuração, como cadeias de conexão. Para obter mais informações, consulte <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Module.vb

Contém o ponto de entrada para o programa. Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Contém o código que configura o comportamento do aplicativo, como se ele requer o consentimento para cookies. Para obter mais informações, consulte <xref:fundamentals/startup>.

## <a name="additional-resources"></a>Recursos adicionais

* [Versão do YouTube deste tutorial](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo tutorial da série:

> [!div class="step-by-step"]
> [Adicionar um modelo](xref:tutorials/razor-pages/model)

::: moniker-end
