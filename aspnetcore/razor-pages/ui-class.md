---
title: Interface do usuário do Razor reutilizável em bibliotecas de classes com o ASP.NET Core
author: Rick-Anderson
description: Explica como criar reutilizáveis Razor da interface do usuário usando as exibições parciais em uma biblioteca de classe no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 06/24/2019
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: 96ef8fc055a6b92cd0808d02031d917b8446f305
ms.sourcegitcommit: 763af2cbdab0da62d1f1cfef4bcf787f251dfb5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2019
ms.locfileid: "67394755"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a>Criar a interface do usuário reutilizável usando o projeto de biblioteca de classes Razor no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Exibições do Razor, páginas, controladores, modelos de página [componentes do Razor](xref:blazor/class-libraries), [componentes de exibição](xref:mvc/views/view-components), e modelos de dados podem ser criados em uma biblioteca de classes Razor (RCL). A RCL pode ser e empacotada e reutilizada. Os aplicativos podem incluir a RCL e substituir as exibições e as páginas que ela contém. Quando uma exibição, uma exibição parcial ou uma página Razor for encontrada no aplicativo Web e na RCL, a marcação Razor (arquivo *.cshtml*) no aplicativo Web terá precedência.

Este recurso requer [!INCLUDE[](~/includes/2.1-SDK.md)]

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-razor-ui"></a>Criar uma biblioteca de classes contendo a interface do usuário do Razor

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.
* Selecione **Aplicativo Web ASP.NET Core**.
* Dê um nome à biblioteca (por exemplo, "RazorClassLib") > **OK**. Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.
* Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.
* Selecione **Biblioteca de Classes Razor** > **OK**.

Uma RCL tem o seguinte arquivo de projeto:

[!code-xml[Main](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Da linha de comando, execute `dotnet new razorclasslib`. Por exemplo:

```console
dotnet new razorclasslib -o RazorUIClassLib
```

Para obter mais informações, confira [dotnet new](/dotnet/core/tools/dotnet-new). Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.

---

Adicione arquivos Razor na RCL.

Os modelos do ASP.NET Core, suponha que o conteúdo da RCL está no *áreas* pasta. Ver [layout de páginas RCL](#afs) para criar uma RCL que expõe conteúdo em `~/Pages` em vez de `~/Areas/Pages`.

## <a name="referencing-rcl-content"></a>Fazendo referência a conteúdo da RCL

A RCL pode ser referenciada por:

* Pacote do NuGet. Confira [Criando pacotes do NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) e [Criar e publicar um pacote do NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName}.csproj*. Confira [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a>Passo a passo: Criar um projeto da RCL e usar a partir de um projeto de páginas do Razor

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

## <a name="create-an-rcl"></a>Criar uma RCL

Nesta seção, uma RCL é criado. Arquivos Razor são adicionados à RCL.

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

Crie um aplicativo web páginas Razor e um arquivo de solução que contém o aplicativo páginas Razor e na RCL:

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

Verifique se que a biblioteca de classes Razor da interface do usuário está em uso:

* Navegue para `/MyFeature/Page1`.

## <a name="override-views-partial-views-and-pages"></a>Substituir exibições, exibições parciais e páginas

Quando uma exibição, uma exibição parcial ou uma página Razor for encontrada no aplicativo Web e na RCL, a marcação Razor (arquivo *.cshtml*) no aplicativo Web terá precedência. Por exemplo, adicione *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* ao WebApp1, e a Page1 ao WebApp1 terá precedência sobre a Page1 na RCL.

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

## <a name="create-an-rcl-with-static-assets"></a>Criar uma RCL com ativos estáticos

Uma RCL pode exigir ativos estáticos de complementar que podem ser referenciados pelo aplicativo de consumo da RCL. ASP.NET Core permite a criação de RCLs que incluem ativos estáticos que estão disponíveis para um aplicativo de consumo.

Para incluir ativos complementar como parte de uma RCL, crie uma *wwwroot* pasta na biblioteca de classes e incluir quaisquer arquivos necessários nessa pasta.

Ao empacotar uma RCL, todos os ativos de complementar a *wwwroot* pasta são incluídos automaticamente no pacote e são disponibilizados para referenciar o pacote de aplicativos.

### <a name="consume-content-from-a-referenced-rcl"></a>Consumir conteúdo de uma RCL referenciado

Os arquivos incluídos na *wwwroot* pasta da RCL são expostos para o aplicativo de consumo sob o prefixo `_content/{LIBRARY NAME}/`. `{LIBRARY NAME}` o nome do projeto de biblioteca que é convertido em letras minúsculas com períodos (`.`) removido. Por exemplo, uma biblioteca denominada *Razor.Class.Lib* resulta em um caminho para o conteúdo estático em `_content/razorclasslib/`.

O aplicativo de consumo referencia ativos estáticos fornecidos pela biblioteca com `<script>`, `<style>`, `<img>`e outras marcas HTML. O aplicativo de consumo deve ter [suporte de arquivo estático](xref:fundamentals/static-files) habilitado.

### <a name="multi-project-development-flow"></a>Fluxo de vários projetos de desenvolvimento

Quando o aplicativo de consumo é executado:

* Os ativos na RCL mantenha-se em suas pastas originais. Os ativos não são movidos para o aplicativo de consumo.
* Qualquer alteração dentro a RCL *wwwroot* pasta é refletida no aplicativo de consumo depois que a RCL é reconstruída e sem recompilar o aplicativo de consumo.

Quando a RCL é compilada, um manifesto é produzido que descreve os locais de ativos estáticos web. O aplicativo de consumo lê o manifesto em tempo de execução para consumir os ativos de pacotes e projetos referenciados. Quando um novo ativo for adicionado a uma RCL, a RCL deve ser recriada para atualizar seu manifesto antes que um aplicativo de consumo possa acessar o novo ativo.

### <a name="publish"></a>Publicar

Quando o aplicativo é publicado, os ativos de todos os projetos e pacotes complementares são copiados para o *wwwroot* pasta do aplicativo publicado em `_content/{LIBRARY NAME}/`.
