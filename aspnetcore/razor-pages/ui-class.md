---
title: Interface do usuário do Razor reutilizável em bibliotecas de classes com o ASP.NET Core
author: Rick-Anderson
description: Explica como criar reutilizáveis Razor da interface do usuário usando as exibições parciais em uma biblioteca de classe no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 06/28/2019
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: 77c7d4a318610fcd424da0485abd41d11e3fad6a
ms.sourcegitcommit: fbc66827e319d28bebed678ea5fd42f582fe3c34
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68493558"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a>Criar interface do usuário reutilizável usando o projeto de biblioteca de classes Razor no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Exibições do Razor, páginas, controladores, modelos de página, [componentes do Razor](xref:blazor/class-libraries), [componentes de exibição](xref:mvc/views/view-components)e modelos de dados podem ser criados em uma biblioteca de classes Razor (RCL). A RCL pode ser e empacotada e reutilizada. Os aplicativos podem incluir a RCL e substituir as exibições e as páginas que ela contém. Quando uma exibição, uma exibição parcial ou uma página Razor for encontrada no aplicativo Web e na RCL, a marcação Razor (arquivo *.cshtml*) no aplicativo Web terá precedência.

Este recurso requer [!INCLUDE[](~/includes/2.1-SDK.md)]

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-razor-ui"></a>Criar uma biblioteca de classes contendo a interface do usuário do Razor

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.
* Selecione **Aplicativo Web ASP.NET Core**.
* Dê um nome à biblioteca (por exemplo, "RazorClassLib") > **OK**. Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.
* Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.
* Selecione **Biblioteca de Classes Razor** > **OK**.

Um RCL tem o seguinte arquivo de projeto:

[!code-xml[Main](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Da linha de comando, execute `dotnet new razorclasslib`. Por exemplo:

```console
dotnet new razorclasslib -o RazorUIClassLib
```

Para obter mais informações, confira [dotnet new](/dotnet/core/tools/dotnet-new). Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.

---

Adicione arquivos Razor na RCL.

Os modelos do ASP.NET Core, suponha que o conteúdo da RCL está no *áreas* pasta. Consulte [layout de páginas RCL](#afs) para criar um RCL que expõe o `~/Pages` conteúdo em `~/Areas/Pages`em vez de.

## <a name="referencing-rcl-content"></a>Fazendo referência ao conteúdo do RCL

A RCL pode ser referenciada por:

* Pacote do NuGet. Confira [Criando pacotes do NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) e [Criar e publicar um pacote do NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName}.csproj*. Confira [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a>Passo a passo: Criar um projeto RCL e usar de um projeto Razor Pages

Você pode baixar o [projeto completo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) e testá-lo em vez de criá-lo. O download de exemplo contém um código adicional e links que facilitam o teste do projeto. Você pode deixar comentários [nesse problema do GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/6098) com suas opiniões sobre os exemplos de download em relação às instruções passo a passo.

### <a name="test-the-download-app"></a>Testar o aplicativo de download

Se você não tiver baixado o aplicativo concluído e preferir criar o projeto do passo a passo, vá para a [próxima seção](#create-an-rcl).

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Abra o arquivo *.sln* no Visual Studio. Execute o aplicativo.

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Em um prompt de comando no diretório *cli*, crie a RCL e o aplicativo Web.

```console
dotnet build
```

Acesse o diretório *WebApp1* e execute o aplicativo:

```console
dotnet run
```

---

Siga as instruções em [Testar WebApp1](#test)

## <a name="create-an-rcl"></a>Criar um RCL

Nesta seção, um RCL é criado. Arquivos Razor são adicionados à RCL.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Crie o projeto da RCL:

* No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.
* Selecione **Aplicativo Web ASP.NET Core**.
* Nomeie o aplicativo **RazorUIClassLib** > **Okey**.
* Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.
* Selecione **Biblioteca de Classes Razor** > **OK**.
* Adicione um arquivo Razor de exibição parcial chamado *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Na linha de comando, execute o seguinte:

```console
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

Os comandos anteriores:

* Cria o `RazorUIClassLib` RCL.
* Criam uma página Razor _Message e a adicionam à RCL. O parâmetro `-np` cria a página sem um `PageModel`.
* Cria uma [viewstart](xref:mvc/views/layout#running-code-before-each-view) de arquivo e o adiciona à RCL.

O *viewstart* arquivo é necessário para usar o layout do projeto páginas Razor (que é adicionado na próxima seção).

---

### <a name="add-razor-files-and-folders-to-the-project"></a>Adicionar pastas e arquivos Razor ao projeto

* Substitua a marcação em *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* pelo código a seguir:

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* Substitua a marcação em *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* pelo código a seguir:

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` é necessário para usar a exibição parcial (`<partial name="_Message" />`). Em vez de incluir a diretiva `@addTagHelper`, você pode adicionar um arquivo *_ViewImports.cshtml*. Por exemplo:

```console
dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
```

Para obter mais informações sobre *viewimports. cshtml*, consulte [importando diretivas compartilhadas](xref:mvc/views/layout#importing-shared-directives)

* Crie a biblioteca de classes para verificar se não há nenhum erro de compilador:

```console
dotnet build RazorUIClassLib
```

A saída do build contém *RazorUIClassLib.dll* e *RazorUIClassLib.Views.dll*. *RazorUIClassLib.Views.dll* contém o conteúdo Razor compilado.

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a>Use a biblioteca da interface do usuário do Razor de um projeto Páginas Razor

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Crie aplicativo Web Páginas Razor:

* No **Gerenciador de Soluções**, clique com o botão direito do mouse na solução > **Adicionar** >  **Novo Projeto**.
* Selecione **Aplicativo Web ASP.NET Core**.
* Nomeie o aplicativo como **WebApp1**.
* Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.
* Selecione **Aplicativo Web** > **OK**.

* No **Gerenciador de Soluções**, clique com o botão direito do mouse em **WebApp1** e selecione **Definir como projeto de inicialização**.
* No **Gerenciador de Soluções**, clique com o botão direito do mouse em **WebApp1** e selecione **Dependências de Build** > **Dependências do Projeto**.
* Marque **RazorUIClassLib** como uma dependência de **WebApp1**.
* No **Gerenciador de Soluções**, clique com o botão direito do mouse em **WebApp1** e selecione **Adicionar** > **Referência**.
* Na caixa de diálogo **Gerenciador de Referências**, marque **RazorUIClassLib** > **OK**.

Execute o aplicativo.

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Crie um aplicativo Web Razor Pages e um arquivo de solução que contenha o aplicativo Razor Pages e o RCL:

```console
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

Crie e execute o aplicativo Web:

```console
cd WebApp1
dotnet run
```

---

<a name="test"></a>

### <a name="test-webapp1"></a>Testar o WebApp1

Verifique se a biblioteca de classes da interface do usuário do Razor está em uso:

* Navegue para `/MyFeature/Page1`.

## <a name="override-views-partial-views-and-pages"></a>Substituir exibições, exibições parciais e páginas

Quando uma exibição, uma exibição parcial ou uma página Razor for encontrada no aplicativo Web e na RCL, a marcação Razor (arquivo *.cshtml*) no aplicativo Web terá precedência. Por exemplo, adicione *WebApp1/areas/MyFeature/Pages/página1. cshtml* a WebApp1, e página1 no WebApp1 terá precedência sobre PÁGINA1 no RCL.

No download de exemplo, renomeie *WebApp1/Areas/MyFeature2* como *WebApp1/Areas/MyFeature* para testar a precedência.

Copie a exibição parcial *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* para *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*. Atualize a marcação para indicar o novo local. Crie e execute o aplicativo para verificar se a versão da parcial do aplicativo está sendo usada.

<a name="afs"></a>

### <a name="rcl-pages-layout"></a>Layout de páginas RCL

A RCL como se fosse parte do aplicativo web de conteúdo de referência *páginas* pasta, crie o projeto da RCL com a seguinte estrutura de arquivo:

* *RazorUIClassLib/páginas*
* *RazorUIClassLib/páginas/Shared*

Suponha *RazorUIClassLib/páginas/Shared* contém dois arquivos parciais: *_Header.cshtml* e *_Footer.cshtml*. O `<partial>` marcas pode ser adicionadas ao *layout. cshtml* arquivo:

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker range=">= aspnetcore-3.0"

## <a name="create-an-rcl-with-static-assets"></a>Criar um RCL com ativos estáticos

Um RCL pode exigir ativos estáticos complementares que podem ser referenciados pelo aplicativo de consumo do RCL. ASP.NET Core permite criar RCLs que incluem ativos estáticos disponíveis para um aplicativo de consumo.

Para incluir ativos complementares como parte de um RCL, crie uma pasta *wwwroot* na biblioteca de classes e inclua todos os arquivos necessários nessa pasta.

Ao empacotar um RCL, todos os ativos complementares na pasta *wwwroot* são incluídos automaticamente no pacote e disponibilizados para aplicativos que fazem referência ao pacote.

### <a name="consume-content-from-a-referenced-rcl"></a>Consumir conteúdo de um RCL referenciado

Os arquivos incluídos na pasta *wwwroot* do RCL são expostos ao aplicativo de consumo sob o prefixo `_content/{LIBRARY NAME}/`. Por exemplo, uma biblioteca chamada *Razor. class. lib* resulta em um caminho para o conteúdo estático `_content/Razor.Class.Lib/`em.

O aplicativo de consumo faz referência a ativos estáticos fornecidos `<script>`pela `<style>`biblioteca `<img>`com,, e outras marcas HTML. O aplicativo de consumo deve ter o [suporte de arquivo estático](xref:fundamentals/static-files) habilitado.

### <a name="multi-project-development-flow"></a>Fluxo de desenvolvimento de vários projetos

Quando o aplicativo de consumo for executado:

* Os ativos no RCL permanecem em suas pastas originais. Os ativos não são movidos para o aplicativo de consumo.
* Qualquer alteração na pasta *wwwroot* do RCL é refletida no aplicativo de consumo depois que o RCL é recriado e sem recriar o aplicativo de consumo.

Quando o RCL é criado, é produzido um manifesto que descreve os locais estáticos de ativos da Web. O aplicativo de consumo lê o manifesto em tempo de execução para consumir os ativos de projetos e pacotes referenciados. Quando um novo ativo é adicionado a um RCL, o RCL deve ser recriado para atualizar seu manifesto antes que um aplicativo de consumo possa acessar o novo ativo.

### <a name="publish"></a>Publicar

Quando o aplicativo é publicado, os ativos complementares de todos os projetos e pacotes referenciados são copiados para a pasta *wwwroot* do `_content/{LIBRARY NAME}/`aplicativo publicado em.

::: moniker-end
