---
title: 'Tutorial: Introdução às Páginas do Razor no ASP.NET Core'
author: rick-anderson
description: Esta série de tutoriais mostra como usar Razor Pages no ASP.NET Core. Saiba como criar um modelo, gerar código para Razor Pages, usar o Entity Framework Core e o SQL Server para acesso a dados, adicionar funcionalidade de pesquisa, adicionar validação de entrada e usar migrações para atualizar o modelo.
ms.author: riande
ms.date: 12/5/2018
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 1d264ca4a605d8291e273a8f054c92e7eefa5548
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2019
ms.locfileid: "59468841"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a>Tutorial: Introdução às Páginas do Razor no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

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

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-razor-pages-web-app"></a>Criar um aplicativo Web das Páginas do Razor

# [<a name="visual-studio"></a>Visual Studio](#tab/visual-studio)

* No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.

* Crie um novo Aplicativo Web ASP.NET Core. Nomeie o projeto **RazorPagesMovie**. É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* Selecione **ASP.NET Core 2.2** na lista suspensa e, em seguida, selecione **Aplicativo Web**.

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  O seguinte projeto inicial é criado:

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# [<a name="visual-studio-code"></a>Visual Studio Code](#tab/visual-studio-code)

* Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Altere os diretórios (`cd`) para uma pasta que conterá o projeto.

* Execute os seguintes comandos:

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * O comando `dotnet new` cria um projeto do Razor Pages na pasta *RazorPagesMovie*.
  * O comando `code` abre a pasta *RazorPagesMovie* em uma nova instância do Visual Studio Code.

  Uma caixa de diálogo é exibida com a mensagem **Os ativos necessários para build e depuração estão ausentes de 'RazorPagesMovie'. Deseja adicioná-los?**

* Selecione **Sim**

# [<a name="visual-studio-for-mac"></a>Visual Studio para Mac](#tab/visual-studio-mac)

Em um terminal, execute o seguinte comando:

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

Os comandos anteriores usam a [CLI do .NET Core](/dotnet/core/tools/dotnet) para criar um projeto do Razor Pages.

## <a name="open-the-project"></a>Abrir o projeto

No Visual Studio, selecione **Arquivo > Abrir** e, em seguida, selecione o arquivo *RazorPagesMovie.csproj*.

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Executar o aplicativo

# [<a name="visual-studio"></a>Visual Studio](#tab/visual-studio)

* Pressione Ctrl + F5 para execução sem o depurador.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo. A barra de endereços mostra `localhost:port#` e não algo como `example.com`. Isso ocorre porque `localhost` é o nome do host padrão do computador local. Localhost serve somente solicitações da Web do computador local. Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.

* Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.

  Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# [<a name="visual-studio-code"></a>Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Pressione **Ctrl-F5** para execução sem o depurador.

  O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `http://localhost:5001`. A barra de endereços mostra `localhost:port#` e não algo como `example.com`. Isso ocorre porque `localhost` é o nome do host padrão do computador local. Localhost serve somente solicitações da Web do computador local.

* Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.

  Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# [<a name="visual-studio-for-mac"></a>Visual Studio para Mac](#tab/visual-studio-mac)

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

Contém o ponto de entrada para o programa. Para obter mais informações, consulte <xref:fundamentals/host/web-host>.

### <a name="startupcs"></a>Startup.cs

Contém o código que configura o comportamento do aplicativo, como se ele requer o consentimento para cookies. Para obter mais informações, consulte <xref:fundamentals/startup>.

## <a name="additional-resources"></a>Recursos adicionais

* [Versão deste tutorial no YouTube](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Criou um aplicativo Web do Razor Pages.
> * Executou o aplicativo.
> * Examinou os arquivos de projeto.

Vá para o próximo tutorial da série:

> [!div class="step-by-step"]
> [Adicionar um modelo](xref:tutorials/razor-pages/model)
