---
title: Introdução às Páginas do Razor no ASP.NET Core
author: rick-anderson
monikerRange: '>= aspnetcore-2.2'
description: Esta série de tutoriais mostra como usar Razor Pages no ASP.NET Core. Saiba como criar um modelo, gerar código para Razor Pages, usar o Entity Framework Core e o SQL Server para acesso a dados, adicionar funcionalidade de pesquisa, adicionar validação de entrada e usar migrações para atualizar o modelo.
ms.author: riande
ms.date: 12/5/2018
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 1152ebfcee48a46ecd28c941fce32d3fc1e05c41
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52861622"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a>Tutorial: introdução ao Razor Pages no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Este tutorial ensina as noções básicas de criação de um aplicativo Web de Páginas do Razor do ASP.NET Core.

O aplicativo gerencia um banco de dados de títulos de filmes. Você aprenderá como:

> [!div class="checklist"]
> * Criar um aplicativo Web do Razor Pages.
> * Adicionar e gerar o scaffolding de um modelo.
> * Trabalhar com um banco de dados.
> * Adicionar pesquisa e validação.

No final, você terá um aplicativo que pode gerenciar e exibir itens de títulos de filmes.

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-razor-web-app"></a>Criar um aplicativo Web do Razor

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.
* Crie um novo Aplicativo Web ASP.NET Core. Nomeie o projeto **RazorPagesMovie**. É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar/colar código.
 ![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* Selecione **ASP.NET Core 2.2** na lista suspensa e, em seguida, selecione **Aplicativo Web**.

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  O seguinte projeto inicial é criado:

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

* Pressione **Ctrl-F5** para execução sem o depurador.

  O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo. A barra de endereços mostra `localhost:port#` e não algo como `example.com`. Isso ocorre porque `localhost` é o nome do host padrão do computador local. Localhost serve somente solicitações da Web do computador local. Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web. Na imagem anterior, o número da porta é 5001. Quando você executar o aplicativo, verá um número de porta diferente.

  Iniciar o aplicativo com **Ctrl+F5** (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código. Muitos desenvolvedores preferem usar o modo sem depuração para atualizar a página e exibir alterações.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Altere os diretórios (`cd`) para uma pasta que conterá o projeto.
* Execute o seguinte comando:

   ```console
   dotnet new webapp -o RazorPagesMovie
   code -r RazorPagesMovie
   ```

  * Uma caixa de diálogo é exibida com a mensagem **Os ativos necessários para build e depuração estão ausentes de 'RazorPagesMovie'. Deseja adicioná-los?**  Selecione **Sim**

  * `dotnet new webapp -o RazorPagesMovie`: cria um projeto do Razor Pages na pasta *RazorPagesMovie*.
  * `code -r RazorPagesMovie`: carrega o arquivo de projeto *RazorPagesMovie.csproj* no Visual Studio Code.

### <a name="launch-the-app"></a>Iniciar o aplicativo

* Pressione **Ctrl-F5** para execução sem o depurador.

  O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`. A barra de endereços mostra `localhost:port:5001` e não algo como `example.com`. Isso ocorre porque `localhost` é o nome do host padrão do computador local. Localhost serve somente solicitações da Web do computador local.

  Iniciar o aplicativo com **Ctrl+F5** (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código. Muitos desenvolvedores preferem usar o modo sem depuração para atualizar a página e exibir alterações.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Em um terminal, execute os seguintes comandos:

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
cd RazorPagesMovie
dotnet run
```

Os comandos anteriores usam a [CLI do .NET Core](/dotnet/core/tools/dotnet) para criar e executar um projeto de Páginas do Razor. Abra um navegador em http://localhost:5000 para exibir o aplicativo.

## <a name="open-the-project"></a>Abrir o projeto

Pressione Ctrl + C para encerrar o aplicativo.

No Visual Studio, selecione **Arquivo > Abrir** e, em seguida, selecione o arquivo *RazorPagesMovie.csproj*.

### <a name="launch-the-app"></a>Iniciar o aplicativo

Selecione **Executar > Iniciar Sem Depuração** para iniciar o aplicativo. O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`.

<!-- End of VS tabs -->

---

* Selecione **Aceitar** para dar consentimento de rastreamento. Este aplicativo não acompanha informações pessoais. O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  A imagem a seguir mostra o aplicativo depois de aceitar o rastreamento:

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="project-files-and-folders"></a>Pastas e arquivos de projeto

A tabela a seguir lista os arquivos e pastas no projeto. Neste ponto do tutorial, o arquivo *Startup.cs* é o mais importante a ser compreendido. Não é necessário examinar cada link fornecido abaixo. Os links são fornecidos como uma referência para quando você precisar de mais informações sobre um arquivo ou pasta no projeto.

| Arquivo ou pasta              | Finalidade |
| ----------------- | ------------ |
| *wwwroot* | Contém arquivos estáticos. Confira [Arquivos estáticos](xref:fundamentals/static-files). |
| *Páginas* | Pasta para [Páginas do Razor](xref:razor-pages/index). |
| *appsettings.json* | [Configuração](xref:fundamentals/configuration/index) |
| *Program.cs* | [Hospeda](xref:fundamentals/host/index) o aplicativo ASP.NET Core.|
| *Startup.cs* | Configura os serviços e o pipeline de solicitação. Consulte [Startup (Inicialização)](xref:fundamentals/startup).|

### <a name="the-pages-folder"></a>A pasta Páginas

O arquivo *_Layout.cshtml* contém elementos HTML comuns (scripts e folhas de estilo) e define o layout para o aplicativo. Por exemplo, quando você clica em **RazorPagesMovie**, **Home** ou **Privacy**, você vê os mesmos elementos. Os elementos comuns incluem o menu de navegação na parte superior e o cabeçalho na parte inferior da janela. Veja [Layout](xref:mvc/views/layout) para obter mais informações.

O arquivo *_ViewImports.cshtml* contém diretivas do Razor que são importadas para cada Página do Razor. Veja [Importando diretivas compartilhadas](xref:mvc/views/layout#importing-shared-directives) para obter mais informações.

O *_ViewStart.cshtml* define a propriedade `Layout` das Páginas do Razor para usar o arquivo *_Layout.cshtml*. Veja [Layout](xref:mvc/views/layout) para obter mais informações.

O arquivo *_ValidationScriptsPartial.cshtml* fornece uma referência a scripts de validação [jQuery](https://jquery.com/). Quando as páginas `Create` e `Edit` forem adicionadas mais adiante no tutorial, o arquivo *_ValidationScriptsPartial.cshtml* será usado.

As páginas `Index`, `Error` e `Privacy` são fornecidas para:

* `Index`: Iniciar um aplicativo.
* `Error`: Exibir informações de erro.
* `Privacy`: Especificar detalhes sobre a política de privacidade do site.

Para este tutorial, as páginas anteriores não são usadas.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="f7"></a>
### <a name="use-f7-to-toggle-between-a-razor-page-and-the-pagemodel"></a>Use F7 para alternar entre um Razor Page e o PageModel

Alternância com F7 entre um Razor Page (arquivo *\*.cshtml*) e o arquivo C# (*\*.cshtml.cs*).

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!-- TODO review  Need something in these tabs -->

Por convenção, o Razor Page (arquivo *\*.cshtml*) e o `PageModel` associado têm o mesmo nome de arquivo raiz.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Por convenção, o Razor Page (arquivo *\*.cshtml*) e o `PageModel` associado têm o mesmo nome de arquivo raiz.

---

> [!div class="step-by-step"]
> [Próximo: adicionando um modelo](xref:tutorials/razor-pages/model)